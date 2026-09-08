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

Files in `examples/` are cold runs: give a fresh agent only the prompt from `PROMPTS.md`, let it follow `SKILL.md`, and paste its report plus its raw call log. Ask the agent to list what was unclear in the skill and fix the skill before shipping. Start each file with a header line that states the run date and the skill version it ran against, for example `Cold run, 2026-09-07, on skill v0.3.0`. Without the version, a reader comparing a current run against an older example cannot tell a regression from a shortcut the skill has since gained. Chain state changes; readers need to know what to expect to differ.

## Recording a clip

The root `README.md` has a placeholder for a short screen recording at `docs/assets/lab-03.gif`. If you record one, swap the HTML comment for the image line.

Record Lab 3 scenario B, the run the README opens with: one line of input, five chains, a dollar total, nothing to explain. Roughly twenty seconds, in a fresh terminal at about 100x30, font two sizes up from normal, with an app already selected so the run does not stop to ask which one.

Hold two beats on an empty prompt, type the command live rather than pasting, let the tool calls scroll past unedited, and finish holding three full seconds on the total. Those tool names going by are the pitch: do not cut or blur them. No title card, no music, no voiceover, and no real wallet, yours or anyone else's. `0x1111...1111` has no owner, which is why it is the one on the front page.

Keep it under 3MB so GitHub renders it inline:

```bash
ffmpeg -i lab-03.mov -vf "fps=12,scale=900:-1:flags=lanczos,split[a][b];[a]palettegen[p];[b][p]paletteuse" -loop 0 docs/assets/lab-03.gif
```

If it comes out too big, drop to `fps=10` before you drop the width. Unreadable tool names defeat the purpose.

## Checklist before opening a PR

- [ ] Every tool name in the new files exists on the server
- [ ] Prompts run as pasted, with the repo open in the agent
- [ ] Reference run dated and labelled
- [ ] Root `README.md` table updated
- [ ] `CLAUDE.md` updated if the agent needs a new rule
