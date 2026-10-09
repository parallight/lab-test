# OpenLabs — Claude Code plugin

> **TEST ring** — Marvin's dev ring, points at the test backend (`https://test.lab-agent.parallight.ai`, served by the `staging` branch). Not for learners.

Learn to build AI agents by **directing** them, guided by a resident master craftsman — inside Claude Code. Draw what you want to learn as a teachboard board. Zero API keys (the LLM runs through Parallight's backend).

## Install (Claude Code)

```
/plugin marketplace add parallight/lab-test
/plugin install openlabs-test@parallight-test
```

Then fully restart Claude Code and type `/openlabs-test` (menu: learn / lab / board / connect), or go straight to `/openlabs-test connect` / `/lab-login`.

More: <https://parallight.ai>

---

This repo is the private test Claude Code marketplace. The MCP server (`plugins/openlabs-test/bundle/`) talks to the Parallight backend; it holds no secrets.
