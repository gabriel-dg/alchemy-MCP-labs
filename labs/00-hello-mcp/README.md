# Lab 0: hello MCP

## Goal

Prove that your agent can reach the Alchemy MCP server, select an app, and read live data. Five tool calls, two minutes. If this works, every other lab will.

## Before you start

- [SETUP.md](../../SETUP.md) done: server connected, one Alchemy app exists
- This repo open in your agent

## Run it

Paste this into your agent:

```text
Use the Alchemy MCP server for these steps, one at a time, and show me the raw result of each:

1. Call ping.
2. Call list_apps. If there is exactly one app, select it with select_app. If there are several, show me the list and ask which one to select.
3. Call list_chains and show me the first 5 networks with their ids.
4. Call ethBlockNumber on eth-mainnet and convert the hex to decimal.
5. Call ethGetBalance on eth-mainnet for 0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045 and convert the wei value to ETH.

Finish with a table: step, tool name, result in one line.
```

## What you should see

The agent may ask you to approve each tool call the first time. Say yes. If it asks you to pick an app, pick the one with Ethereum Mainnet enabled.

Values observed on 2026-09-07:

| Step | Tool | Result |
|------|------|--------|
| 1 | `ping` | a short success reply |
| 2 | `list_apps`, `select_app` | your app id and name, then "API key cached" |
| 3 | `list_chains` | 70+ networks, ids like `eth-mainnet`, `base-mainnet`, `solana-mainnet` |
| 4 | `ethBlockNumber` | `0x18b9993`, which is block 25,926,035. Yours will be higher |
| 5 | `ethGetBalance` | `0x5d2659027b0b8043` wei, about 6.71 ETH. Yours will differ |

The address in step 5 is `vitalik.eth`. Lab 1 shows how the agent resolves the name itself.

## Reading the output

- **Hex everywhere.** JSON-RPC returns numbers as hex strings. Block numbers are plain integers. Balances are in wei, where 1 ETH is 10^18 wei. The agent converts them when asked.
- **`select_app` is per session.** If you start a new session you select again. The skills in this repo do it automatically.
- **Tool calls are visible.** Your agent shows each call and its JSON. That transparency is the point: you can always see what was asked and what came back.

## Try your own

- **Your wallet**: replace the address in step 5 with yours.
- **Another network**: change `eth-mainnet` to `base-mainnet` or `arb-mainnet` in steps 4 and 5. If the call fails with a message about the app not supporting the network, enable it in the dashboard for your app.
- **A token balance**: add a step 6, "Call getTokenBalancesByAddress on eth-mainnet for the same address and show the first 5 entries." Notice the first page is mostly spam tokens with vanity addresses. Lab 1 explains why.
- **Solana**: add "Call solana_getBalance on solana-mainnet for `<any Solana address>`." Your app needs Solana enabled.
- **What did that cost?** Add "Call get_usage_summary and tell me how many compute units this month has used so far." Every call costs compute units, and the Free tier has a monthly allowance shown in the dashboard. For scale: building and cold-testing this whole repo on 2026-09-07, including ten agent runs, used 61,408 compute units at $0.00.

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| Agent says it has no Alchemy tools | Server not connected. Redo [SETUP.md](../../SETUP.md) step 2 and restart the agent. |
| `ping` works, step 4 fails | No app selected. Say "select an Alchemy app". |
| Step 4 or 5 fails on another network | The app does not have that network enabled. Dashboard, Apps, your app, Networks. |
