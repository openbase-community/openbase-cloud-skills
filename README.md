# Openbase Cloud Skills

Public [agent skills](https://code.claude.com/docs/en/skills) for working with
**[Openbase Cloud](https://openbase.cloud)** — the Heroku-style platform (PaaS).

These skills give an AI agent (Claude Code, Openbase Coder, or any
skills-compatible agent) the context to help you deploy and operate apps on
Openbase Cloud.

## Skills

| Skill | Use it when |
| --- | --- |
| [`openbase-deploy`](skills/openbase-deploy/SKILL.md) | Deploying an app to Openbase Cloud — connecting a GitHub repo, push-to-deploy, config vars and secrets, hostnames, and inspecting apps/logs/releases/usage with the `openbase` CLI or dashboard. |

## Install

Point your agent's skills directory at this repo, or copy the skill folder into
your skills location. With the Openbase skill installer:

```bash
# from a skills.sh-compatible installer
skills add openbase-community/openbase-cloud-skills
```

Or symlink an individual skill into your agent's skills directory (e.g.
`~/.claude/skills/` for Claude Code):

```bash
ln -s "$(pwd)/skills/openbase-deploy" ~/.claude/skills/openbase-deploy
```

## Related

- The `openbase` CLI: <https://github.com/openbase-community/openbase-cloud-cli>
- Web dashboard: <https://app.openbase.cloud>

## License

MIT — see [LICENSE](LICENSE).
