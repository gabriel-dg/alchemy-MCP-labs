# Checklist: new skill folder (skill #2+)

Use this when adding the next MCP skill to the lab.

## Folder and files

- [ ] Folder name under `skills/` equals the `name` field in `SKILL.md` frontmatter
- [ ] Include `SKILL.md` (agentskills format)
- [ ] Include `PROMPT.md` (one copy-paste block)
- [ ] Include `examples/` with a short `README.md` and at least one sample report
- [ ] Every example starts with: `SAMPLE OUTPUT — not live`

## Repo hygiene

- [ ] Update the skill index table in root `README.md`
- [ ] Do not duplicate `SETUP.md` content inside the skill
- [ ] Link to `SETUP.md` when MCP is missing

## Allowed-tools discipline

- [ ] List only real Alchemy MCP tools the skill may call
- [ ] Do not invent tool names
- [ ] No send / sign / broadcast steps
- [ ] No API keys, `.env`, or runtime glue code in the skill docs
