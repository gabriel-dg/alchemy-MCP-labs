# The demo clip

The README has a placeholder for a short screen recording, right under the sample output:

```html
<!-- demo clip goes here: ![Lab 3 running](docs/assets/lab-03.gif) - recording script in docs/demo-clip.md -->
```

Record it, drop the file at `docs/assets/lab-03.gif`, and swap the comment for the image line. Twenty seconds of a real run does more for this repo than another paragraph.

## What to record

Lab 3, scenario B, the same run the README opens with. It is the best clip in the repo: one line of input, five chains, dollars at the end, and nothing to explain. It also briefs an address nobody owns, so no one's wallet ends up in a clip they did not agree to.

## Before you hit record

- A **fresh terminal**, in the repo folder, nothing else on screen
- Font large enough to read at 480px wide. Two steps up from your normal size
- An Alchemy app already selected, or a single app on the account, so the run does not stop to ask you which one. That question is correct behaviour but it is not the clip
- Prompt cleared, no scrollback
- Terminal about 100 columns by 30 rows. Taller than that and the report scrolls out of frame

## Shot list, about 20 seconds

| Time | On screen | Note |
|------|-----------|------|
| 0:00-0:02 | Empty prompt, `claude` already running | Hold two beats so the viewer sees a normal terminal |
| 0:02-0:05 | Type `/multichain-brief 0x1111111111111111111111111111111111111111` and press enter | Type it live. Pasting reads as staged |
| 0:05-0:14 | Tool calls scroll past: `ethGetCode`, `getTokensByAddress`, `getHistoricalTokenPrices` | Do not cut this. Watching named tools go by *is* the pitch: the viewer sees exactly what was asked |
| 0:14-0:19 | The report lands. Scroll so the native balance table and the **Total** line are both in frame | Hold on the total for a full three seconds |
| 0:19-0:20 | Freeze on the total | Last frame is the one that gets screenshotted |

If the run takes longer than about ten seconds, speed the middle section to roughly 2x rather than cutting frames. The tool names should stay readable.

## Encoding

Aim for under 3MB so GitHub renders it inline without a click.

```bash
# from a screen recording
ffmpeg -i lab-03.mov -vf "fps=12,scale=900:-1:flags=lanczos,split[a][b];[a]palettegen[p];[b][p]paletteuse" -loop 0 docs/assets/lab-03.gif
```

`fps=12` and a 900px width are usually enough. If it comes out over 3MB, drop to `fps=10` before you drop the width: unreadable text defeats the purpose.

## What not to do

- No title card, no intro, no music. The clip starts at the prompt
- Do not blur the tool calls to "clean it up". They are the product
- No real wallet, yours or anyone else's. `0x1111...1111` has no owner, which is the whole reason it is the one on the front page
- Do not add a voiceover. The clip has to work muted in a timeline
