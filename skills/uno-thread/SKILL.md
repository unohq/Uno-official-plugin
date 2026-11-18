---
name: uno-thread
description: Start an engineering thread in Uno, attach original screenshots or images to its first message, and verify the message and turn. Use when the user asks Uno to take on work, including requests with image attachments.
---

# Start an Uno thread

Use the connected Uno MCP server. Ask the user to sign in through the client if the server requires OAuth. Discover the target factory and repositories with `get_workspaces` and `uno_get_workspace` as applicable to the connection scope. Use the user's requested factory and repositories. Ask for a choice if multiple matches remain. Starting work can consume Uno credits.

Put the full task, constraints, and requested output in the initial message. For a text-only request, use `uno_create_session` with the objective, selected repository IDs, and a stable `requestId`. Check the returned `next_action` before reporting execution status. The first message is already submitted by this tool.

## With images

1. Resolve each user-selected image to the original bytes through a file handle, a readable local path, or an authorized private file source. A vision preview cannot supply those bytes. If the original is unavailable, ask the user for an accessible file or for permission to continue with text alone. Pause thread creation while the requested image is missing.
2. Check the MIME type and exact byte length and compute the lowercase SHA-256 hex digest over the bytes that will be sent. Uno accepts up to five PNG, JPEG, or WebP images. Worker coding turns have an 8 MiB combined attachment limit.
3. Generate one draft UUID for the whole first message. For each image, call `post_workspaces_session_image_assets_upload_url` with the top-level `workspaceId` and `body: { draftId, filename, mediaType, sizeBytes }`. Use the returned `data.id`, `data.uploadUrl`, and `data.requiredHeaders`.
4. With a binary-safe HTTP client, send an HTTPS `PUT` of the raw file bytes to the exact signed URL returned by Uno, using the supplied headers. Keep the signed URL out of chat and logs and use it without forwarding the Uno OAuth token. After a successful PUT, call `post_workspaces_session_image_assets_complete` with top-level `workspaceId` and `assetId`, and `body: { draftId, sha256 }`. Confirm each asset reports `ready`. Use the same connected Uno identity, workspace, and draft for the entire flow.
5. Call `post_workspaces_sessions` with top-level `workspaceId` and a stable `idempotencyKey`. Set `body.objective`, `body.repositories` to the selected repository objects (each with a `repositoryId`), `body.draftId` to the upload draft, and `body.initialConversation` to an object containing `message: { text: "<full task>" }` and `imageAssetIds` with all ready asset IDs. Use this single call to attach the images and queue the first turn. If a response is uncertain, retry with the same key and identical input.

## Verify and report

Open the primary conversation via the returned `data.primary_conversation_id`. Use `get_conversations_messages` to verify the first user message has the expected IDs in its `attachments` array, then `get_conversations_status` to check the turn. Report the thread link and observed status. A queued turn has been accepted. It is running once Uno reports `running`.

If an upload fails, report the failed step before starting a text-only thread. If creation is accepted but attachment verification is pending, report it as pending and avoid starting a duplicate thread.
