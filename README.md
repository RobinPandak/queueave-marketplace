# QueueAve marketplace

A [Claude Code plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces) for [QueueAve](https://badminton.queueave.com), the badminton queue management app.

## Add the marketplace

```
/plugin marketplace add RobinPandak/queueave-marketplace
```

## Plugins

| Plugin | Install | What it does |
| --- | --- | --- |
| **queueave** | `/plugin install queueave@queueave` | Connects Claude to the QueueAve MCP server and ships operate/match/queue/assess skills, so you can run live sessions with matchmaking, assessment, walk-ins, payments, and full court management from Claude. |

After installing `queueave`, add the connector URL `https://badminton.queueave.com/api/mcp` (Claude Desktop / web) or run `claude mcp add --transport http queueave https://badminton.queueave.com/api/mcp` (CLI), then sign in with Google on first use. See [`plugins/queueave/README.md`](./plugins/queueave/README.md) for full setup.

## Repository layout

```
.
├── .claude-plugin/
│   └── marketplace.json        # marketplace catalog
└── plugins/
    └── queueave/
        ├── .claude-plugin/
        │   └── plugin.json     # plugin manifest
        ├── .mcp.json           # QueueAve MCP server (HTTP, OAuth)
        ├── reference/
        │   ├── leveling.md     # level ladder and ELO reference
        │   └── matchmaking.md  # matchmaking process reference
        ├── skills/
        │   ├── operate/
        │   │   └── SKILL.md    # operating manual (/queueave:operate)
        │   ├── match/
        │   │   └── SKILL.md    # court match creation (/queueave:match)
        │   ├── queue/
        │   │   └── SKILL.md    # on-deck queuing (/queueave:queue)
        │   └── assess/
        │       └── SKILL.md    # player assessment (/queueave:assess)
        └── README.md
```

## Develop / test locally

```
git clone https://github.com/RobinPandak/queueave-marketplace
/plugin marketplace add ./queueave-marketplace
/plugin install queueave@queueave
```

Or load the plugin directly without installing:

```
claude --plugin-dir ./queueave-marketplace/plugins/queueave
```
