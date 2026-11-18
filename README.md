# Uno Official Plugin

Start and follow engineering work in Uno from clients that support [Agent Plugins](https://agent-plugins.org/compatible-clients). The plugin bundles Uno's hosted MCP server and the `uno-thread` skill, which can upload screenshots and attach them to the first message of a new thread.

## Install in Cursor

Clone [Uno-official-plugin](https://github.com/unohq/Uno-official-plugin) and import its root from **Customize → Import from Disk** in Cursor. You can also copy the plugin files to `~/.cursor/plugins/local/uno-official-plugin` and reload Cursor. Enterprise administrators may need to enable local plugin imports. See the [Cursor plugin documentation](https://cursor.com/docs/plugins#test-plugins-locally).

In **Customize**, enable the Uno MCP server and use its sign-in action. Choose **One factory** for a workspace-scoped connection or **Personal — your factories** to use your current permissions across your factories. The plugin uses OAuth at `https://api.run.uno/mcp`. You can revoke access under [Uno MCP connections](https://app.run.uno/auth/mcp).

## Start a thread

In Cursor chat, invoke `/uno-thread` or ask the agent to hand a task to Uno. Name the target factory and repository if they are ambiguous.

> Ask Uno to investigate how follow-ups could queue while a turn is running. Attach the original screenshot to the first message and send back the Uno thread link.

For an image handoff, the agent needs access to the actual file and the ability to make an HTTPS upload. Give it a readable PNG, JPEG, or WebP file. Uno supports up to five images, with an 8 MiB combined attachment limit for Worker coding turns. The skill checks the first persisted message before reporting that the image was attached. Starting work can consume Uno credits.

## Use with Grok Bot

Add a **Remote HTTPS** custom connector in **Connect apps** using `https://api.run.uno/mcp` and complete Uno OAuth. Use [the skill instructions](skills/uno-thread/SKILL.md) as a private skill in Grok Bot, or paste them into a request. The same upload works when the Bot can read the original file bytes and make the signed HTTPS PUT. See [Grok Bot custom connectors](https://docs.x.ai/grok-bot/computer-and-apps) and [private skills](https://docs.x.ai/grok-bot/skills-routines-and-automations#save-a-skill).

## License

The plugin configuration and skill are [MIT licensed](LICENSE). Uno retains rights to its name and [logo](assets/uno-logo.png).
