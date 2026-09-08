# How it works

## The flow

```
 you                agent                 Alchemy MCP server            blockchains
 ───                ─────                 ──────────────────            ───────────
 paste a prompt ──► reads SKILL.md
                    picks a tool ───────► https://mcp.alchemy.com/mcp
                                          (OAuth, your selected app) ──► Ethereum, Base,
                                                                         Polygon, Solana...
                    gets JSON back ◄──────────────────────────────────── 
                    writes the report
 read the report ◄──
```

You never write code. The skill tells the agent which tools to call and in what order. The agent calls them over MCP, gets JSON back, and writes a report you can read.

## Concepts

**MCP (Model Context Protocol).** An open standard that lets an AI agent discover a server's tools and call them. Claude Code, Cursor, VS Code, Codex and Claude Desktop all speak it.

**Alchemy MCP server.** Alchemy's hosted MCP endpoint. It wraps Alchemy's JSON-RPC, token, NFT, simulation, and Solana APIs as MCP tools. Authentication is OAuth with your Alchemy account.

**App.** A project in the Alchemy dashboard. Each app has an API key and a set of enabled networks. The MCP server routes requests through the app you select with `select_app`. You select once per session.

**Network id.** A string like `eth-mainnet`, `base-mainnet`, `solana-mainnet`. Most tools take one as a parameter. `list_chains` returns the full list.

**Compute units (CU).** Alchemy's usage metric. Every call costs some CU. Free apps have a monthly allowance and a rate limit. The labs here use a few hundred CU per run.

**Free vs PAYG.** Some tools and parameters are paid: NFT spam filters, the Trace API, the Debug API, and event-log queries wider than 10 blocks. They return a 400 that mentions payg, upgrade, or billing, or an error that names the allowed range. The skills skip them and note it under Gaps.

**Proxies.** Many contracts are a thin proxy that forwards every call to an implementation contract stored in a known storage slot. Lab 2 reads those slots with `ethGetStorageAt`. The logic you are trusting lives at the implementation, and it can be upgraded.

**Verified source.** Alchemy decodes simulated calls using Etherscan's ABI when the contract's source is verified there. Lab 2 uses that as a Free-tier "is the source public" signal: probe a common function with `simulateExecution` and see whether the response carries a decoded block.

**Pagination and "page 1 only".** Token, NFT, and transfer lists are paginated and often sorted by address, not by value. The first page of a famous wallet is usually airdropped spam tokens with vanity addresses. The skill labels these results "page 1 only" and never concludes that a token is absent because it did not appear on page 1.

**Multi-chain tools.** Most tools take one `network`. The token balance tools take a `networks` list instead and return one flat array with a `network` field per row, natives first. Lab 3 uses that to read five chains in one call. Two quirks: a network the app lacks is dropped from the response without an error, and Polygon answers as `matic-mainnet` whatever id you sent.

**Native vs ERC-20.** ETH on Ethereum, Base, Arbitrum and OP Mainnet is the chain's own currency, with no contract address; the token tools show it as a row with `tokenAddress` `null`. POL on Polygon is the same, but Polygon also exposes it through a precompile at `0x…1010`, so it shows up twice. WETH and the stablecoins are ERC-20 contracts and have an address per chain; the same symbol at a different address on another chain is a different contract.

**Simulation vs trace.** `simulateAssetChanges` and `simulateExecution` run a transaction against current state without sending it and return the balance changes and events it would produce. They work on Free and are the core of Lab 1. Traces (`traceTransaction`, `debugTraceTransaction`) replay a mined transaction step by step and are paid.

**ENS and namehash.** `vitalik.eth` is an ENS name. Resolving it means calling the ENS registry contract with a 32-byte `namehash` of the name. The hash is keccak256, which language models cannot compute reliably, so the skill computes it with the server's `web3Sha3` tool and then makes two `ethCall`s. The full recipe is in the skill.

**EIP-7702.** Since the Pectra upgrade an externally owned account can delegate its code to a contract. `ethGetCode` on such an address returns 23 bytes starting with `0xef0100` followed by the delegate address. Signatures from that account are then interpreted by the delegate. The skill flags this as REVIEW so you look at who the delegate is. Vitalik's own address is delegated at the time of writing, which makes it a useful first example.

## Tool map

The server exposes 173 tools across 160+ networks, checked on 2026-09-08 from a Free-tier connection. Alchemy keeps adding networks, so call `list_chains` for today's list rather than relying on a number written down here. Grouped by family, with the ones the labs use in bold:

| Family | Examples | Notes |
|--------|----------|-------|
| Admin | **`ping`**, **`list_apps`**, **`select_app`**, **`list_chains`**, `get_app`, `get_usage_summary` | `select_app` first. Do not use `create_app` or webhook tools in labs |
| JSON-RPC reads | **`ethBlockNumber`**, **`ethGetBalance`**, **`ethGetCode`**, **`ethGetStorageAt`**, **`ethGetTransactionCount`**, **`ethCall`**, **`ethGetTransactionByHash`**, **`ethGetTransactionReceipt`**, **`ethGetLogs`**, `ethGasPrice`, **`web3Sha3`** | Standard Ethereum RPC on any EVM network. Logs are capped at a 10-block range on Free |
| Transfers | **`getAssetTransfers`** | History of ETH, ERC-20, ERC-721, ERC-1155 movements for an address or a token contract |
| Tokens | **`getTokenBalancesByAddress`**, **`getTokensByAddress`**, **`getTokenMetadata`**, **`getTokenAllowance`** | Both balance tools take a `networks` list and answer for every chain in one request. `getTokensByAddress` adds metadata and prices per row |
| Prices | **`getTokenPricesByAddress`**, **`getTokenPricesBySymbol`**, **`getHistoricalTokenPrices`** | USD from Alchemy's feed. History at 5-minute, hourly or daily intervals, up to a year of daily points. Prices may not exist for every token |
| NFTs | **`getNFTsForOwner`**, **`getContractMetadata`**, `getNFTMetadata`, `getOwnersForContract`, **`isSpamContract`**, `getFloorPrice` | Spam filters are paid |
| Simulation | **`simulateAssetChanges`**, **`simulateExecution`**, `simulateAssetChangesBundle` | Free. Read-only preview of an unsigned transaction |
| Trace / debug | `traceTransaction`, `traceCall`, `debugTraceTransaction`, `debugTraceCall` | Paid |
| Account abstraction | `estimateUserOperationGas`, `getUserOperationReceipt`, `requestGasAndPaymasterAndData` | For ERC-4337 flows, planned lab |
| Solana | `solana_getBalance`, `solana_getAssetsByOwner`, `solana_searchAssets`, `solana_getTransaction` | RPC plus DAS, planned lab |

The server also publishes MCP resources at `alchemy://networks`, `alchemy://networks/evm`, and `alchemy://networks/solana` with the same data as `list_chains`.

## Glossary

- **Calldata**: the hex payload of a transaction. The first 4 bytes select the function, the rest are its arguments.
- **Approve / allowance**: an ERC-20 permission letting a spender move your tokens. "Unlimited" means the maximum uint256.
- **Mined transaction**: one already included in a block. It has a hash and a receipt. You can inspect it but not change it.
- **Unsigned call**: a transaction you have not signed yet. It can be simulated. This is where a preflight is useful.
- **Receipt**: the result of a mined transaction: success or failure, gas used, and the event logs it emitted.
- **Gaps**: the section of the report that lists what the skill could not verify, and why.
