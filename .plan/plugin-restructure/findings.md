# Findings

## Plugin structure (from anthropics/knowledge-work-plugins)

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          ← { name, version, description, author }
├── commands/                ← flat .md files, invoked as /command-name
│   └── command-name.md      ← frontmatter: description, argument-hint
├── skills/                  ← directories with SKILL.md, passive context
│   └── skill-name/
│       └── SKILL.md         ← frontmatter: name, description
├── .mcp.json               ← optional MCP connections
└── README.md
```

Key difference from current ze-skills:
- Commands are flat `.md` files, not directories with SKILL.md
- Commands frontmatter uses `description` + `argument-hint` (no `name`, no `disable-model-invocation`)
- Skills are passive context Claude draws on automatically (not invoked as slash commands)
