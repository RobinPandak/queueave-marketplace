# QueueAve marketplace

A [Claude Code plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces) for [QueueAve](https://badminton.queueave.com), the badminton queue management app.

## Add the marketplace

```
/plugin marketplace add RobinPandak/queueave-marketplace
```

## Plugins

| Plugin | Install | What it does |
| --- | --- | --- |
| **queueave** | `/plugin install queueave@queueave` | Connects Claude to the QueueAve MCP server and ships an operating-manual skill, so you can run live sessions, matches, players, and courts from Claude. |

After installing `queueave`, generate a personal token at `https://badminton.queueave.com/dashboard/mcp`, set it as `QUEUEAVE_MCP_TOKEN`, and restart Claude Code. See [`plugins/queueave/README.md`](./plugins/queueave/README.md) for full setup.

## Repository layout

```
.
├── .claude-plugin/
│   └── marketplace.json        # marketplace catalog
└── plugins/
    └── queueave/
        ├── .claude-plugin/
        │   └── plugin.json     # plugin manifest
        ├── .mcp.json           # QueueAve MCP server (HTTP, token-gated)
        ├── skills/
        │   └── operate/
        │       └── SKILL.md    # operating manual skill (/queueave:operate)
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
