# Contributing

Two kinds of additions: a **lab** (a walkthrough for humans) and a **skill** (a playbook for agents). Most labs use one skill. Lab 0 uses none.

## Adding a lab

Create `labs/NN-<name>/README.md` with these sections, in this order:

1. **Goal**: two or three sentences. What the user will have at the end.
2. **Before you start**: prerequisites, usually "SETUP.md done, app selected".
3. **Run it**: one prompt per scenario in a fenced block. Each prompt must be self-contained: it names the skill file to follow and includes the input.
4. **What you should see**: the shape of the output, plus real values observed on a stated date.
5. **Reading the output**: explain every heading or field the user will see and what to do about it.
6. **Try your own**: how to swap in the user's inputs, other networks, paid-tier options.
7. **Troubleshooting**: lab-specific failures only. General ones live in `SETUP.md`.

Add a row to the Labs table in the root `README.md`.

## Adding a skill

Create `skills/<name>/` with:

- `SKILL.md` in agentskills format. Frontmatter `name` must equal the folder name. The `description` is what the agent uses to decide when the skill applies, so write it as trigger phrases.
- `PROMPTS.md` with one copy-paste block per scenario. No placeholders that look like inputs. Ship real, harmless inputs.
- `examples/` with a short `README.md` and at least one reference run.
- `.claude/skills/<name>/SKILL.md`: a thin pointer with the same frontmatter whose body says "read and follow `skills/<name>/SKILL.md`".

### Skill rules

- List only tool names that exist on the Alchemy MCP server. Check with the server's tool list, not from memory.
- No send, sign, or broadcast steps. No `create_app` or other account-mutating admin tools.
- No API keys, `.env` files, or runtime glue code in skill docs.
- Design for the Free tier by default. Name the paid tools explicitly and say how to degrade.
- Do not duplicate `SETUP.md`. Link to it when MCP is missing.
- If the skill needs a hash, encoding, or other computation the model cannot do reliably, route it through a tool (`web3Sha3` for keccak256) and say so.

### Reference runs

Files in `examples/` are cold runs: give a fresh agent only the prompt from `PROMPTS.md`, let it follow `SKILL.md`, and paste its report plus its raw call log. Ask the agent to list what was unclear in the skill and fix the skill before shipping. Start each file with a header line that states the run date. Chain state changes; readers need to know what to expect to differ.

## Checklist before opening a PR

- [ ] Every tool name in the new files exists on the server
- [ ] Prompts run as pasted, with the repo open in the agent
- [ ] Reference run dated and labelled
- [ ] Root `README.md` table updated
- [ ] `CLAUDE.md` updated if the agent needs a new rule
