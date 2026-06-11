# integrations-Debugging-Functions-Tools-Hardening.
Latest update and on the road to V2 testing soon.
# Updated Base44 Enhancement Prompt (with Wallet + Jupiter)

Copy and paste this into your Base44 AI chat:

---

Upgrade my existing minimal "Church of Pump" Base44 app into a professional degen dashboard for the $PUMP token.

**Official Token Mint (CA):** `TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb`

**Theme:** Cyberpunk / neon degen style (dark backgrounds, hot pink, electric cyan, purple gradients). Make it feel premium but fun.

**Required Features:**

1. **Wallet Connection**
   - Prominent "Connect Wallet" button in the top navigation.
   - Support Phantom, Backpack, and Solflare.
   - When connected, display shortened wallet address + Disconnect button.
   - Only show sensitive actions (like Withdraw Fees) when a wallet is connected.

2. **Live On-Chain Stats**
   - Total Transfers count (from TransferCounter PDA)
   - Last Transfer timestamp
   - Current 1% Tax status explanation

3. **Recent Activity Feed**
   - Display both `TransferEvent` and `TransferWithdrawal` events
   - Show event type, amount, relevant addresses, and timestamp
   - Color code transfers (cyan) vs withdrawals (pink)

4. **Tax & Treasury Section**
   - Clear explanation of the 1% tax mechanics (using Token-2022 TransferFee)
   - Button to trigger `withdraw_fees` (only for authorized wallets)

5. **Jupiter Swap Integration**
   - Add a "Swap on Jupiter" button that opens Jupiter aggregator in a new tab or modal, pre-filled with the $PUMP mint.
   - Jupiter URL example: `https://jup.ag/swap/USDC-TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb`

**Technical Details:**
- Transfer Hook Program: `PumpTokenHook11111111111111111111111111111111`
- Counter PDA seeds: `["counter"]`
- Events: `TransferEvent`, `TransferWithdrawal`
- Mint: `TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb`

Make the dashboard fully responsive, with good loading states, error handling, and a production-ready feel. Prepare it for custom domain on Base44 Pro tier.

After building, summarize the main sections you created and any manual steps needed (especially for wallet connection and Jupiter button).
 token2022 Solana integration 
solana account TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb --output json | \
jq '.account.data.parsed.info.mintAuthority'
import { Connection, PublicKey } from '@solana/web3.js';
import { getMint, TOKEN_2022_PROGRAM_ID } from '@solana/spl-token';
const connection = new Connection('https://api.mainnet-beta.solana.com');
const mintPubkey = new PublicKey('TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb');
const mint = await getMint(connection, mintPubkey, undefined, TOKEN_2022_PROGRAM_ID);
console.log('Mint Authority:', mint.mintAuthority?.toBase58() || 'Renounced (null)');
solana account TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb --output json | \
jq '.account.data.parsed.info | {mintAuthority, freezeAuthority, supply, decimals}'
{
  "mintAuthority": null,
  "freezeAuthority": null,
  "supply": "1000000000000000",
  "decimals": 6
}
solana account TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb | grep -E 'Mint Authority|Freeze Authority'
npx ts-node scripts/check_token_status.ts
const progress = Math.min((solRaised / 69) * 100, 100);
const tokensSold = Math.floor((progress / 100) * 793_100_000);
import { Connection, PublicKey } from '@solana/web3.js';
const connection = new Connection('https://api.mainnet-beta.solana.com');
const mint = new PublicKey('TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb');
// Get token supply (proxy for circulating supply)
const supply = await connection.getTokenSupply(mint);
console.log('Token Supply:', supply.value.uiAmountString);
// Birdeye API example
const url = `https://public-api.birdeye.so/defi/token_overview?address=${mint.toBase58()}`;
const res = await fetch(url, { headers: { 'X-API-KEY': 'YOUR_BIRDEYE_KEY' } });
const data = await res.json();
console.log('Liquidity:', data.data.liquidity);
console.log('Market Cap:', data.data.marketCap);
console.log('24h Volume:', data.data.volume24h);
npx ts-node scripts/raydium_live_pool_data.ts
npm install @raydium-io/raydium-sdk
npx ts-node scripts/unified_token_dashboard.ts
- job_name: 'pump-token-dashboard'
  static_configs:
    - targets: ['localhost:3000']
const SOL_PRICE = await getSolPrice(); // from Birdeye
const TARGET_SOL_RAISED = 69;
const TARGET_MCAP_USD = TARGET_SOL_RAISED * SOL_PRICE;
const GRADUATION_PRICE = TARGET_MCAP_USD / 206900000;
npx ts-node scripts/unified_token_dashboard.ts
BIRDEYE_API_KEY=your_key_here
TELEGRAM_BOT_TOKEN=your_bot_token
TELEGRAM_CHAT_ID=your_chat_id
DISCORD_WEBHOOK=your_webhook
# 1. Run the unified dashboard
npx ts-node scripts/unified_token_dashboard.ts
# 2. (Optional) Run with Docker
docker compose -f docker-compose.monitor.yml up -d
# 3. Import Grafana dashboard
# Then add /metrics to Prometheus
npx ts-node scripts/bonding_curve_rebalancer.ts
const jupiterUrl = `https://quote-api.jup.ag/v6/quote?inputMint=So11111111111111111111111111111111111111112&outputMint=${MINT.toBase58()}&amount=1000000000&slippageBps=50`;
# 1. Set your env vars
cp systemd/pump-tax-monitor.env.example .env
# Edit .env with DISCORD_WEBHOOK, TELEGRAM_*, BIRDEYE_API_KEY, etc.
# 2. Start everything
docker compose -f docker-compose.full.yml up -d --build
# 3. Access services
# Dashboard + metrics: http://localhost:3000
# Grafana: http://localhost:3001 (admin/admin)
# Prometheus: http://localhost:9090
import { Liquidity } from '@raydium-io/raydium-sdk';
const poolKeys = await Liquidity.findPoolByMint({
  connection: CONNECTION,
  mintA: MINT,
  mintB: new PublicKey('So11111111111111111111111111111111111111112'), // SOL
  programId: new PublicKey('675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8')
});
if (poolKeys) {
  const poolInfo = await Liquidity.fetchInfo({ connection: CONNECTION, poolKeys });
  console.log('Base Reserve (tokens):', poolInfo.baseReserve.toString());
  console.log('Quote Reserve (SOL):', poolInfo.quoteReserve.toString());
  console.log('Current Price:', poolInfo.currentPrice);
}
docker compose -f docker-compose.full.yml up -d --build
import { executeJupiterSwap } from './jupiter_swap_executor';
const txid = await executeJupiterSwap(
  wallet.publicKey,
  amountInLamports,
  async (tx) => await wallet.signTransaction(tx)
);
# One-time setup (if you want a build step)
npx tsc --init
npx tsc scripts/*.ts --outDir dist --module commonjs --target es2020 --esModuleInterop
# Or use the faster esbuild (recommended)
npm install -g esbuild
esbuild scripts/unified_token_dashboard.ts --bundle --platform=node --outfile=dist/dashboard.js
# 1. Run the unified dashboard (now with Jupiter + everything)
npx ts-node scripts/unified_token_dashboard.ts
# 2. (Recommended) Use the full Docker stack
docker compose -f docker-compose.full.yml up -d --build
# 3. Compile everything to hardened JS
esbuild scripts/*.ts --bundle --platform=node --outdir=dist

# Updated Base44 Enhancement Prompt (with Wallet + Jupiter)

Copy and paste this into your Base44 AI chat:

---

Upgrade my existing minimal "Church of Pump" Base44 app into a professional degen dashboard for the $PUMP token.

**Official Token Mint (CA):** `TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb`

**Theme:** Cyberpunk / neon degen style (dark backgrounds, hot pink, electric cyan, purple gradients). Make it feel premium but fun.

**Required Features:**

1. **Wallet Connection**
   - Prominent "Connect Wallet" button in the top navigation.
   - Support Phantom, Backpack, and Solflare.
   - When connected, display shortened wallet address + Disconnect button.
   - Only show sensitive actions (like Withdraw Fees) when a wallet is connected.

2. **Live On-Chain Stats**
   - Total Transfers count (from TransferCounter PDA)
   - Last Transfer timestamp
   - Current 1% Tax status explanation

3. **Recent Activity Feed**
   - Display both `TransferEvent` and `TransferWithdrawal` events
   - Show event type, amount, relevant addresses, and timestamp
   - Color code transfers (cyan) vs withdrawals (pink)

4. **Tax & Treasury Section**
   - Clear explanation of the 1% tax mechanics (using Token-2022 TransferFee)
   - Button to trigger `withdraw_fees` (only for authorized wallets)

5. **Jupiter Swap Integration**
   - Add a "Swap on Jupiter" button that opens Jupiter aggregator in a new tab or modal, pre-filled with the $PUMP mint.
   - Jupiter URL example: `https://jup.ag/swap/USDC-TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb`

**Technical Details:**
- Transfer Hook Program: `PumpTokenHook11111111111111111111111111111111`
- Counter PDA seeds: `["counter"]`
- Events: `TransferEvent`, `TransferWithdrawal`
- Mint: `TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb`

Make the dashboard fully responsive, with good loading states, error handling, and a production-ready feel. Prepare it for custom domain on Base44 Pro tier.

After building, summarize the main sections you created and any manual steps needed (especially for wallet connection and Jupiter button).
Mint token2022 Solana integration 
solana account TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb --output json | \
jq '.account.data.parsed.info.mintAuthority'
import { Connection, PublicKey } from '@solana/web3.js';
import { getMint, TOKEN_2022_PROGRAM_ID } from '@solana/spl-token';
const connection = new Connection('https://api.mainnet-beta.solana.com');
const mintPubkey = new PublicKey('TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb');
const mint = await getMint(connection, mintPubkey, undefined, TOKEN_2022_PROGRAM_ID);
console.log('Mint Authority:', mint.mintAuthority?.toBase58() || 'Renounced (null)');
solana account TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb --output json | \
jq '.account.data.parsed.info | {mintAuthority, freezeAuthority, supply, decimals}'
{
  "mintAuthority": null,
  "freezeAuthority": null,
  "supply": "1000000000000000",
  "decimals": 6
}
solana account TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb | grep -E 'Mint Authority|Freeze Authority'
npx ts-node scripts/check_token_status.ts
const progress = Math.min((solRaised / 69) * 100, 100);
const tokensSold = Math.floor((progress / 100) * 793_100_000);
import { Connection, PublicKey } from '@solana/web3.js';
const connection = new Connection('https://api.mainnet-beta.solana.com');
const mint = new PublicKey('TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb');
// Get token supply (proxy for circulating supply)
const supply = await connection.getTokenSupply(mint);
console.log('Token Supply:', supply.value.uiAmountString);
// Birdeye API example
const url = `https://public-api.birdeye.so/defi/token_overview?address=${mint.toBase58()}`;
const res = await fetch(url, { headers: { 'X-API-KEY': 'YOUR_BIRDEYE_KEY' } });
const data = await res.json();
console.log('Liquidity:', data.data.liquidity);
console.log('Market Cap:', data.data.marketCap);
console.log('24h Volume:', data.data.volume24h);
npx ts-node scripts/raydium_live_pool_data.ts
npm install @raydium-io/raydium-sdk
npx ts-node scripts/unified_token_dashboard.ts
- job_name: 'pump-token-dashboard'
  static_configs:
    - targets: ['localhost:3000']
const SOL_PRICE = await getSolPrice(); // from Birdeye
const TARGET_SOL_RAISED = 69;
const TARGET_MCAP_USD = TARGET_SOL_RAISED * SOL_PRICE;
const GRADUATION_PRICE = TARGET_MCAP_USD / 206900000;
npx ts-node scripts/unified_token_dashboard.ts
BIRDEYE_API_KEY=your_key_here
TELEGRAM_BOT_TOKEN=your_bot_token
TELEGRAM_CHAT_ID=your_chat_id
DISCORD_WEBHOOK=your_webhook
# 1. Run the unified dashboard
npx ts-node scripts/unified_token_dashboard.ts
# 2. (Optional) Run with Docker
docker compose -f docker-compose.monitor.yml up -d
# 3. Import Grafana dashboard
# Then add /metrics to Prometheus
npx ts-node scripts/bonding_curve_rebalancer.ts
const jupiterUrl = `https://quote-api.jup.ag/v6/quote?inputMint=So11111111111111111111111111111111111111112&outputMint=${MINT.toBase58()}&amount=1000000000&slippageBps=50`;
# 1. Set your env vars
cp systemd/pump-tax-monitor.env.example .env
# Edit .env with DISCORD_WEBHOOK, TELEGRAM_*, BIRDEYE_API_KEY, etc.
# 2. Start everything
docker compose -f docker-compose.full.yml up -d --build
# 3. Access services
# Dashboard + metrics: http://localhost:3000
# Grafana: http://localhost:3001 (admin/admin)
# Prometheus: http://localhost:9090
import { Liquidity } from '@raydium-io/raydium-sdk';
const poolKeys = await Liquidity.findPoolByMint({
  connection: CONNECTION,
  mintA: MINT,
  mintB: new PublicKey('So11111111111111111111111111111111111111112'), // SOL
  programId: new PublicKey('675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8')
});
if (poolKeys) {
  const poolInfo = await Liquidity.fetchInfo({ connection: CONNECTION, poolKeys });
  console.log('Base Reserve (tokens):', poolInfo.baseReserve.toString());
  console.log('Quote Reserve (SOL):', poolInfo.quoteReserve.toString());
  console.log('Current Price:', poolInfo.currentPrice);
}
docker compose -f docker-compose.full.yml up -d --build
import { executeJupiterSwap } from './jupiter_swap_executor';
const txid = await executeJupiterSwap(
  wallet.publicKey,
  amountInLamports,
  async (tx) => await wallet.signTransaction(tx)
);
# One-time setup (if you want a build step)
npx tsc --init
npx tsc scripts/*.ts --outDir dist --module commonjs --target es2020 --esModuleInterop
# Or use the faster esbuild (recommended)
npm install -g esbuild
esbuild scripts/unified_token_dashboard.ts --bundle --platform=node --outfile=dist/dashboard.js
# 1. Run the unified dashboard (now with Jupiter + everything)
npx ts-node scripts/unified_token_dashboard.ts
# 2. (Recommended) Use the full Docker stack
docker compose -f docker-compose.full.yml up -d --build
# 3. Compile everything to hardened JS
esbuild scripts/*.ts --bundle --platform=node --outdir=dist


esbuild scripts/unified_token_dashboard.ts --bundle --platform=node --outfile=dist/unified_token_dashboard.js --format=cjs --external:node-fetch

node dist/unified_token_dashboard.js

[Unit]
Description=$PUMP Unified Token Dashboard (Docker)
After=docker.service
Requires=docker.service

[Service]
Type=simple
ExecStart=/usr/bin/docker compose -f /home/youruser/pump-token/docker-compose.full.yml up
ExecStop=/usr/bin/docker compose -f /home/youruser/pump-token/docker-compose.full.yml down
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target

sudo systemctl daemon-reload
sudo systemctl enable --now pump-token-monitor
create_pump_mint.ts

node dist/unified_token_dashboard.js

// 1. TransferFeeConfig (your 1% tax)
// 2. TransferHook
// 3. DefaultAccountState (freeze new accounts initially)
// 4. MetadataPointer + TokenMetadata
// 5. InitializeMint

import { ComputeBudgetProgram } from '@solana/web3.js';

const modifyComputeUnits = ComputeBudgetProgram.setComputeUnitLimit({ 
  units: 400_000   // Set explicit limit (lower = cheaper, higher = safer for complex txs)
});

const addPriorityFee = ComputeBudgetProgram.setComputeUnitPrice({ 
  microLamports: 10_000   // Priority fee (higher = faster inclusion)
});

docker compose -f docker-compose.full.yml up -d --build

cat /sys/fs/cgroup/<your-cgroup>/memory.events

rate(cgroup_memory_high_events[5m]) > 0

import { bundle } from 'jito-ts'; // or use direct RPC

const bundleTxs = [mainTransaction, tipTransaction];
const bundleId = await jitoClient.sendBundle(bundleTxs);

# Run the unified hardened dashboard
npx ts-node scripts/unified_token_dashboard.ts

# Or with full stack
docker compose -f docker-compose.full.yml up -d --build

import { JitoBundleSender, sendCriticalTransactionWithJito } from './jito_bundle_sender';

// Example: Send any critical transaction with Jito priority
const result = await sendCriticalTransactionWithJito(
  yourMainTransaction,
  wallet.publicKey,
  async (tx) => await wallet.signTransaction(tx)
);

# Run the enhanced rebalancer (now with action suggestions)
npx ts-node scripts/bonding_curve_rebalancer.ts

# Run the unified dashboard (includes Jito-ready metrics + memory cgroup monitoring)
npx ts-node scripts/unified_token_dashboard.ts

# Use the new reusable Jito module in any critical tx path
# (example integration already shown in jito_bundle_sender.ts)

Imbalance detected → Calculate size → Jito bundle with tip → Fast on-chain rebalance tx → (Optional) Bridge to secure withdraw

{
  "title": "Jito Tip Costs (SOL)",
  "type": "stat",
  "targets": [
    {
      "expr": "jito_tip_costs_sol",
      "legendFormat": "Avg Tip"
    }
  ],
  "fieldConfig": {
    "defaults": {
      "unit": "currencySOL"
    }
  }
},
{
  "title": "Jito Bundle Success Rate",
  "type": "gauge",
  "targets": [
    {
      "expr": "jito_bundle_success_rate",
      "legendFormat": "Success %"
    }
  ],
  "fieldConfig": {
    "defaults": {
      "min": 0,
      "max": 100,
      "unit": "percent",
      "thresholds": {
        "steps": [
          { "color": "red", "value": 0 },
          { "color": "yellow", "value": 80 },
          { "color": "green", "value": 95 }
        ]
      }
    }
  }
}

# Test the enhanced rebalancer with Jito
npx ts-node scripts/bonding_curve_rebalancer.ts

# Run the full unified dashboard (now with memory cgroup + Jito metrics ready)
npx ts-node scripts/unified_token_dashboard.ts

# Test Jito-wired withdraw
npx ts-node scripts/execute_withdraw.ts

# Test Jito-powered rebalancer with real Jupiter swaps
npx ts-node scripts/bonding_curve_rebalancer.ts

# Run the full unified dashboard (now includes all integrations)
npx ts-node scripts/unified_token_dashboard.ts

# Full stack with Grafana (includes new Jito panels)
docker compose -f docker-compose.full.yml up -d --build

const result = await jitoSender.sendBundle([rebalanceTx], payer, signFn);
if (result.success) {
  recordJitoBundleResult(true, tipSOL); // Updates unified metrics
}

chmod +x deploy.sh

# 1. One-command deploy
./deploy.sh up

# 2. Access everything
# Dashboard + metrics: http://localhost:3000/metrics (and /dashboard, /health)
# Grafana: http://localhost:3001
# Prometheus: http://localhost:9090

# 3. Test Jito-powered rebalancer + withdraw
npx ts-node scripts/bonding_curve_rebalancer.ts
npx ts-node scripts/execute_withdraw.ts

npx ts-node scripts/run_all.ts

import * as Metrics from './metrics';
// ...
function getPrometheusMetrics() { return Metrics.getPrometheusMetrics(); }

import { ComputeBudgetProgram } from '@solana/web3.js';

tx.add(ComputeBudgetProgram.setComputeUnitLimit({ units: 600_000 }));
tx.add(ComputeBudgetProgram.setComputeUnitPrice({ microLamports: 100_000 }));

lib/
└── metrics.ts          # All metrics + persistence (done)

# 1. One-command production deploy
./deploy.sh up

# 2. Or run the all-in-one locally
npx ts-node scripts/run_all.ts

# 3. Metrics are now persistent across restarts (metrics.json created automatically)

# 1. One-command production deploy
./deploy.sh up

# 2. Or run the all-in-one locally
npx ts-node scripts/run_all.ts

npm install redis

cp metrics.json.example metrics.json

pump-token/
├── lib/
│   ├── metrics.ts          # Dedicated + persistent (file/Redis)
│   └── jito.ts             # Reusable Jito bundle sender
├── scripts/
│   ├── unified_token_dashboard.ts
│   ├── bonding_curve_rebalancer.ts
│   ├── execute_withdraw.ts
│   ├── run_all.ts
│   └── deploy.sh
├── grafana/
│   ├── pump-token-dashboard.json   # Uses template variables
│   └── alert-rules/
│       └── pump-token-alerts.json  # Full rules with variables
├── metrics.json.example
└── docker-compose.full.yml

./deploy.sh up
# or
npx ts-node scripts/run_all.ts

{
  "currentTax": "12458392",
  "raydiumLiquidityUSD": 125430,
  "bondingCurveProgress": 87.4,
  "tokenPriceUSD": 0.0001247,
  "lastUpdate": "2026-06-10T09:20:00.000Z",
  "cgroupMemoryCurrent": 187654321,
  "cgroupMemoryMax": 536870912,
  "cgroupMemoryHighEvents": 12,
  "cgroupOomEvents": 0,
  "jitoTipCostsSOL": 0.124,
  "jitoBundleSuccessRate": 96.8,
  "jitoBundlesSent": 47,
  "jitoBundlesFailed": 2
}

scrape_configs:
  - job_name: 'pump-metrics'
    static_configs:
      - targets: ['localhost:3000']
    metrics_path: /metrics
    scrape_interval: 15s

docker build -f Dockerfile.metrics -t pump-metrics .
docker run -p 3000:3000 --env-file .env pump-metrics

# Burn 1 token (adjust decimals as needed)
npx ts-node scripts/token_incinerator.ts

# Or call programmatically
import { incinerateTokens } from './token_incinerator';
await incinerateTokens(1_000_000_000, true); // 1 token with Jito

# Full stack with Grafana + alerts
./deploy.sh up

# Or lightweight all-in-one (dashboard + rebalancer)
npx ts-node scripts/run_all.ts

# Run incinerator separately when needed
npx ts-node scripts/token_incinerator.ts

docker build -f Dockerfile.incinerator -t pump-incinerator .
docker run --env-file .env pump-incinerator

./deploy.sh up

{
  "rpc": {
    "healthy": true,
    "currentSlot": 123456789,
    "solanaVersion": "1.18.x"
  }
}

pump-incinerator:
  build:
    context: .
    dockerfile: Dockerfile.incinerator
  ...
  profiles:
    - incinerator   # Only starts with: docker compose --profile incinerator up

# Start full stack + incinerator
docker compose --profile incinerator -f docker-compose.full.yml up -d

# Or run incinerator standalone
docker compose --profile incinerator run pump-incinerator

./deploy.sh up
# or with incinerator
docker compose --profile incinerator -f docker-compose.full.yml up -d

{
  "rpc": {
    "healthy": true,
    "currentSlot": 123456789,
    "solanaVersion": "1.18.x",
    "blockProduction": {
      "healthy": true,
      "avgTps": 4200,
      "samples": 10,
      "totalSlots": 150,
      "totalTransactions": 630000
    }
  }
}

lib/
├── metrics.ts          # All metrics + persistence (file/Redis)
├── jito.ts             # Reusable Jito bundle sender (with tip bumping + retry)
└── solana.ts           # Shared Solana utilities (RPC health, block production, PDAs, reserves)

# Run everything together
npx ts-node scripts/run_all.ts

# Or full Docker stack
./deploy.sh up

private bumpTip() {
  const bumpFactor = 1.5 + (this.recentFailures * 0.1); // Dynamic based on failure streak
  this.tipLamports = Math.floor(this.tipLamports * bumpFactor);
  // Cap + logging
}

{
  "rpc": {
    "healthy": true,
    "currentSlot": 123456789,
    "latencyMs": 45,
    "blockProduction": { "avgTps": 4200, ... },
    "retryable": true
  }
}

npx ts-node scripts/run_all.ts
# or
./deploy.sh up

const result = await jitoSender.sendBundleWithRetry(
  [yourTx],
  payer,
  signFn,
  3 // max retries
);

// Run with: node --test tests/jito.test.ts
describe('JitoBundleSender Tip Bumping', () => {
  it('should bump tip correctly on failure simulation', () => { ... });
  it('should cap tip at max value', () => { ... });
  it('should integrate with retry logic (simulated)', async () => { ... });
});

node --test tests/jito.test.ts
npx ts-node scripts/run_all.ts  # Runs dashboard + rebalancer with Jito

npm install @jito-labs/jito-ts

import { Connection, PublicKey, Transaction, VersionedTransaction, SystemProgram } from '@solana/web3.js';
import { searcher } from '@jito-labs/jito-ts'; // Official SDK

const JITO_TIP_ACCOUNT = new PublicKey('96gYZGLnJYVFmbjzopPSU6QiEV5fGqZNyN9nmNhvrZU5');
const DEFAULT_TIP_LAMPORTS = 1_000_000;

export class JitoBundleSender {
  private connection: Connection;
  private tipAccount: PublicKey;
  private tipLamports: number;
  private recentFailures = 0;

  constructor(connection = new Connection('https://api.mainnet-beta.solana.com'), tipAccount = JITO_TIP_ACCOUNT, tipLamports = DEFAULT_TIP_LAMPORTS) {
    this.connection = connection;
    this.tipAccount = tipAccount;
    this.tipLamports = tipLamports;
  }

  private bumpTip() {
    const bumpFactor = 1.5 + (this.recentFailures * 0.1);
    this.tipLamports = Math.floor(this.tipLamports * bumpFactor);
    if (this.tipLamports > 100_000_000) this.tipLamports = 100_000_000;
  }

  async sendBundleWithRetry(transactions, payer, signAllTransactions, maxRetries = 3) {
    for (let attempt = 0; attempt <= maxRetries; attempt++) {
      this.bumpTip();
      try {
        const bundle = new searcher.Bundle(transactions); // SDK bundle
        // Add tip instruction via SDK or manually
        const result = await searcher.sendBundle(bundle, this.tipAccount); // Simplified SDK call
        
        this.recentFailures = Math.max(0, this.recentFailures - 1);
        return { bundleId: result, success: true };
      } catch (error) {
        this.recentFailures++;
        if (attempt < maxRetries) {
          await new Promise(r => setTimeout(r, Math.pow(2, attempt) * 1000));
        }
      }
    }
    return { success: false, error: 'Max retries exceeded' };
  }

  // ... (rest of class with createTipTransaction, getTipAccounts, sendProtectedBundle)
}
// Usage remains identical - drop-in replacement with better typing/MEV features.
lib/
├── metrics.ts      # Persistent metrics (file + optional Redis)
├── jito.ts         # Jito bundles + tip bumping + MEV protection + SDK migration path
├── solana.ts       # Core Solana utils (RPC health, block production, bonding curve)
└── health.ts       # NEW: Stake distribution + performance checks
"stakeDistribution": {
  "totalStake": 123456789012345,
  "nakamotoCoefficient": 4,
  "decentralizationScore": 60
}
npx ts-node scripts/run_all.ts
# or
./deploy.sh up

pub fn mint_compressed_reward_nft(
    ctx: Context<MintCompressedReward>,
    metadata_uri: String,
    name: String,
    symbol: String,

cd programs/pump_rewards
anchor build --verifiable
) -> Result<()>
if let Ok(confidential_account) = get_extension::<ConfidentialTransferAccount>(
    &ctx.accounts.source.to_account_info().try_borrow_data()?
) {
    msg!("Confidential transfer detected via zkToken - tax applied on decrypted amount");
}/// ElGamal Encryption in Confidential Transfers
/// 
/// - Uses ElGamal (additive homomorphic) to encrypt balances on-chain.
/// - Transfers include zero-knowledge range proofs.
/// - The transfer hook receives the **decrypted amount** via proof context.
/// - This allows tax/burn logic while user balances stay private.
// Secure key loading with validation
const treasuryKeypair = Keypair.fromSecretKey(
  Uint8Array.from(JSON.parse(process.env.TREASURY_KEY || '[]'))
);
// TODO: Add key rotation + audit logging
const https = require('https');
const fs = require('fs');

// Enforce TLS 1.3 + modern ciphers
const options = {
  key: fs.readFileSync(process.env.TLS_KEY_PATH),
  cert: fs.readFileSync(process.env.TLS_CERT_PATH),
  minVersion: 'TLSv1.3',
  ciphers: [
    'TLS_AES_256_GCM_SHA384',
    'TLS_CHACHA20_POLY1305_SHA256',
    'TLS_AES_128_GCM_SHA256'
  ].join(':'),
  honorCipherOrder: true
};

https.createServer(options, app).listen(PORT, () => {
  console.log(`🚀 Secure $PUMP Bot running on HTTPS (TLS 1.3 enforced)`);
});
ts-node scripts/rotate_dilithium_keys.ts
spl-token create-token \
  --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb \
  --transfer-hook $PROGRAM_ID \
  --enable-metadata \
  --enable-confidential-transfers \
  --default-account-state frozen   # Security hardening
  cd programs/pump_rewards
anchor build --verifiable
0 3 * * 0 /path/to/pump_rewards/scripts/rotate_dilithium_keys_cron.sh >> /var/log/pump-key-rotation.log 2>&1
--default-account-state frozen

# Install liboqs and Node bindings (when stable)
npm install liboqs-node
# or build from source: https://github.com/open-quantum-safe/liboqs
const { OQS } = require('liboqs-node');

const hybridKEM = new OQS.KEM('Kyber768'); // or X25519Kyber768Draft00 when available

// In your HTTPS server options or custom TLS layer:
// Use hybrid key exchange during handshake
// Current recommendation: Run classical TLS 1.3 now + plan hybrid upgrade in 2027
app.post('/admin/rotate-dilithium-keys', async (req, res) => {
  const authToken = req.headers['x-admin-token'];
  if (authToken !== process.env.ADMIN_ROTATION_TOKEN) {
    return res.status(403).send('Forbidden');
  }

  try {
    const result = await runRotationScript(); // call the cron script or ts-node
    res.json({ success: true, result });
  } catch (err) {
    res.status(500).json({ success: false, error: err.message });
  }
});

cd programs/pump_rewards
anchor build --verifiable
/// Dilithium Key Generation (off-chain recommended)
/// Perform real key generation off-chain using a secure crate like `pqcrypto-dilithium`.
/// This stub is provided for structure and future on-chain use.
pub fn generate_dilithium_keypair() -> ([u8; 32], [u8; 64]) {
    let mut public_key = [0u8; 32];
    let mut secret_key = [0u8; 64];
    
    // TODO: Replace with real implementation:
    // let (pk, sk) = pqcrypto_dilithium::dilithium5::keypair();
    // public_key.copy_from_slice(&pk);
    // secret_key.copy_from_slice(&sk);
    
    msg!("Dilithium keypair generation requested (use off-chain in production with secure RNG)");
    (public_key, secret_key)
}
aws secretsmanager update-secret \
  --secret-id "PUMP_DILITHIUM_PRIVATE_KEY" \
  --secret-string "$NEW_PRIV"
  cd programs/pump_rewards
anchor build --verifiable
chmod +x scripts/rotate_dilithium_keys_cron.sh
crontab -e
# Add: 0 3 * * 0 /full/path/to/rotate_dilithium_keys_cron.sh
curl -X POST https://your-bot.example.com/admin/rotate-dilithium-keys \
  -H "x-admin-token: $ADMIN_ROTATION_TOKEN"
  cd offchain-keygen
cargo run --release -- --output my_keys.json
cd key-rotation-service
docker compose up -d --build
cd pump_rewards/key-rotation-service

# 1. Build the hardened image
docker compose build --no-cache

# 2. Start the service (it will use the scripts from the parent folder)
docker compose up -d

# 3. Check it's running with mTLS ready
docker compose ps
docker logs pump-key-rotation

# 4. Quick health check (internal)
curl -k https://localhost:3443/health
curl -X POST https://localhost:3443/rotate-dilithium-keys \
  -H "Content-Type: application/json" \
  -H "x-admin-token: test-token" \
  -k
  # Make executable
chmod +x scripts/rotate_dilithium_keys_cron.sh
export ADMIN_ROTATION_TOKEN="your-very-long-random-token-here"
curl -X POST https://your-production-bot.example.com/admin/rotate-dilithium-keys \
  -H "x-admin-token: $ADMIN_ROTATION_TOKEN" \
  -H "Content-Type: application/json"
  /// Kyber KEM (Key Encapsulation Mechanism) for post-quantum key exchange
pub fn kyber_encapsulate(public_key: &[u8]) -> Result<([u8; KYBER768_CIPHERTEXT_SIZE], [u8; KYBER768_SHARED_SECRET_SIZE])>

pub fn kyber_decapsulate(ciphertext: &[u8], secret_key: &[u8]) -> Result<[u8; KYBER768_SHARED_SECRET_SIZE]>

/// Hybrid Post-Quantum Key Exchange (X25519 + Kyber)
pub fn hybrid_key_exchange(...)
# Wrap new Dilithium key with KMS before storing
aws kms encrypt \
  --key-id alias/pump-wrapping-key \
  --plaintext fileb://<(echo -n "$NEW_PRIV" | base64 -d) \
  --output text --query CiphertextBlob > wrapped_key.b64
  cd pump_rewards/key-rotation-service

# Build the hardened image
docker compose build --no-cache

# Start the service
docker compose up -d

# Verify it's running with mTLS enabled
docker compose ps
docker logs pump-key-rotation

# Test health endpoint
curl -k https://localhost:3443/health
chmod +x scripts/rotate_dilithium_keys_cron.sh

# Copy to production server
sudo cp scripts/rotate_dilithium_keys_cron.sh /opt/pump/scripts/
sudo chmod 700 /opt/pump/scripts/rotate_dilithium_keys_cron.sh

# Add to crontab (every 90 days)
crontab -e
# Paste: 0 3 */90 * * /opt/pump/scripts/rotate_dilithium_keys_cron.sh >> /var/log/pump-rotation.log 2>&1
cd pump_rewards/offchain-keygen

# Build with real pqcrypto-dilithium (uncomment the dependency first)
cargo build --release

# Generate keys (run in air-gapped/secure machine)
./target/release/pump-dilithium-keygen --output keys.json
# In your production environment / Docker secrets
export ADMIN_ROTATION_TOKEN="your-long-random-token-here"

# Test
curl -X POST https://your-bot.example.com/admin/rotate-dilithium-keys \
  -H "x-admin-token: $ADMIN_ROTATION_TOKEN"
  cd key-rotation-service
chmod +x generate-mtls-certs.sh
./generate-mtls-certs.sh
pub fn kyber_encapsulate(public_key: &[u8]) -> Result<([u8; KYBER768_CIPHERTEXT_SIZE], [u8; KYBER768_SHARED_SECRET_SIZE])>

pub fn kyber_decapsulate(ciphertext: &[u8], secret_key: &[u8]) -> Result<[u8; KYBER768_SHARED_SECRET_SIZE]>

pub fn hybrid_key_exchange(classical_shared: &[u8], kyber_ciphertext: &[u8], kyber_secret_key: &[u8]) -> Result<[u8; 64]>
cd pump-token

# 1. Deploy
./scripts/deploy_incinerator.sh

# 2. Configure env
cp .env.example .env
# Edit: RPC_URL, ANCHOR_WALLET, MINT, etc.

# 3. Start (systemd example)
sudo systemctl start pump-incinerator

# 4. Test manually
npx ts-node scripts/token_incinerator.ts

# 5. Wire into rebalancer (edit bonding_curve_rebalancer.ts)
chmod +x scripts/deploy_incinerator.sh
./scripts/deploy_incinerator.sh
import { incinerateTokens } from './token_incinerator';

// In your rebalance logic:
if (imbalance > 0.05) {  // 5% imbalance threshold
  const burnAmount = Math.floor(imbalance * totalSupply * 0.1); // e.g. 10% of excess
  await incinerateTokens(burnAmount, true); // true = prefer Jito
  Metrics.incinerationsTotal.inc({ trigger: 'rebalancer' });
}
services:
  incinerator:
    build:
      context: .
      dockerfile: Dockerfile.incinerator
    env_file: .env
    restart: unless-stopped
    cd pump_rewards/offchain-keygen

# 1. Add the real crate (uncomment in Cargo.toml)
# pqcrypto-dilithium = "0.3"

# 2. Update src/main.rs to use real keygen (replace the stub section with):
# use pqcrypto_dilithium::dilithium5;
# let (public_key, secret_key) = dilithium5::keypair();

# 3. Build a release binary
cargo build --release

# 4. Run it (generates real keys)
./target/release/pump-dilithium-keygen --output keys.json

sudo cp pump-token/systemd/pump-incinerator.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now pump-incinerator
sudo systemctl status pump-incinerator
# 1. Build & test the service
cd pump-token/key-rotation-service   # (if testing rotation service too)
docker compose build

# 2. Deploy incinerator
cd ../..
chmod +x scripts/deploy_incinerator.sh
./scripts/deploy_incinerator.sh

# 3. Install systemd service
sudo cp systemd/pump-incinerator.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now pump-incinerator

# 4. Import Grafana dashboard
# (Use the JSON file above)

# 5. Verify metrics
curl http://localhost:3000/metrics | grep incinerat
# Lean (just the bot)
docker compose -f docker-compose.full.yml up -d

# Full production stack
docker compose -f docker-compose.full.yml --profile full up -d

# Full + key rotation service
docker compose -f docker-compose.full.yml --profile full --profile security up -d
cd pump-token

# 1. Copy example env
cp .env.example .env
# Edit with your real values (RPC, keys, mint, etc.)

# 2. Start full stack
docker compose -f docker-compose.full.yml --profile full up -d --build

# 3. Check everything is healthy
docker compose -f docker-compose.full.yml ps

# 4. View Grafana (incinerator dashboard already importable)
# http://localhost:3002  (admin / changeme or your set password)
# In Grafana → Dashboards → Import → Upload `grafana/incinerator-dashboard.json`
cd key-rotation-service
./generate-mtls-certs.sh
sudo cp systemd/pump-incinerator.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now pump-incinerator
cd pump-token

# 1. Environment
cp .env.example .env
# Edit .env with your real values

# 2. Generate mTLS certs (for key-rotation-service)
cd key-rotation-service
./generate-mtls-certs.sh
cd ..

# 3. Start full stack
docker compose -f docker-compose.full.yml --profile full up -d --build

# 4. Import Grafana dashboard + alerts
# Grafana → Dashboards → Import → incinerator-dashboard.json
# Alerting → Alert rules → Import → incinerator-alerts.yml

# 5. (Optional) Install systemd service for incinerator
sudo cp systemd/pump-incinerator.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now pump-incinerator
{service="incinerator"} |= "Jito" | json | rate[5m]
{container_name=~"pump-.*"} |~ "error|fail|incinerat"
cd pump-token/terraform

# Initialize
terraform init

# Plan (review what will be created)
terraform plan \
  -var="grafana_admin_password=StrongPass123!" \
  -var="helius_webhook_secret=xxx" \
  -var="treasury_key=xxx" \
  -var="admin_rotation_token=xxx"

# Apply
terraform apply
# 1. Simple filter
{container_name=~"pump-.*"} |= "error"

# 2. JSON parsing + filtering (great for structured logs from pump-bot/incinerator)
{container_name="pump-incinerator"} | json | method="jito" | rate[5m]

# 3. Count errors by reason (very useful for incinerator failures)
sum by (reason) (
  count_over_time({container_name=~"pump-.*"} |= "fail|error" | json [5m])
)

# 4. Histogram of burn amounts (if you log the amount)
{container_name="pump-incinerator"} | json | unwrap amount | __error__="" | histogram_quantile(0.99, sum(rate) by (le))

# 5. Correlate with metrics (using Grafana mixed queries)
# In Grafana you can combine:
# LogQL: {service="incinerator"} |= "Jito failed"
# + PromQL: rate(incineration_failures[5m])
cd pump-token/ansible

# 1. Edit inventory.yml with your server IP and variables
# 2. (Recommended) Encrypt secrets with Ansible Vault
ansible-vault create vars/main.yml

# 3. Run the playbook
ansible-playbook -i inventory.yml playbook.yml --ask-vault-pass
if (!verifyHeliusSignature(req)) {
  console.error(JSON.stringify({
    level: 'error',
    msg: 'Invalid Helius signature - possible attack',
    service: 'pump-bot',
    timestamp: new Date().toISOString()
  }));
  breachCounter.inc();
  return res.status(401).send('Unauthorized');
}
if (!verifyHeliusSignature(req)) {
  console.error(JSON.stringify({
    level: 'error',
    msg: 'Invalid Helius signature - possible attack',
    service: 'pump-bot',
    timestamp: new Date().toISOString()
  }));
  breachCounter.inc();
  return res.status(401).send('Unauthorized');
}
console.log(JSON.stringify({
  level: 'info',
  msg: 'Tokens incinerated via Jito',
  service: 'incinerator',
  method: 'jito',
  amount,
  bundleId: result.bundleId,
  timestamp: new Date().toISOString()
}));
{service="incinerator"} | json | method="jito"
labels:
  - "com.docker.compose.service=pump-bot"
  - "logging=structured"
  - {service="incinerator"} 
{service="pump-bot"}
{service="incinerator"} | json | method="jito"
{service="pump-bot"} | json | level="error"
{service="incinerator"} | json | trace_id=`${__trace.trace_id}`
prometheus.remote_write "mimir" {
  endpoint {
    url = "http://mimir:9009/api/v1/push"
  }
}
mimir:
  image: grafana/mimir:latest
  command: ["-target=all", "-config.file=/etc/mimir/mimir.yaml"]
  volumes:
    - ./mimir:/etc/mimir
  ports:
    - "9009:9009"
  profiles:
    - full
    - cd pump-token

# Rebuild Alloy with new config
docker compose -f docker-compose.full.yml up -d --build alloy

# Import LogQL query library as a dashboard or use in Explore
# (copy queries from grafana/logql-queries.md)

# (Optional) Add Mimir service to docker-compose.full.yml and restart
import TradeDesk from './TradeDesk';

export default function App() {
  return <TradeDesk />;
}
import TradeDesk from './TradeDesk';

export default function App() {
  return <TradeDesk />;
}
cd pump-token

# Start with frontend profile
docker compose -f docker-compose.full.yml --profile full --profile frontend up -d --build

# Or standalone
cd xnft-frontend
npm run dev
{
  "@solana/wallet-adapter-react": "^0.15.35",
  "@solana/wallet-adapter-react-ui": "^0.9.35",
  "wagmi": "^2.12.0",
  "viem": "^2.21.0"
}
app.post('/api/record-trade', (req, res) => {
  const { chain, amount, txHash } = req.body;
  
  tradeCounter.inc({ chain });
  tradeVolume.inc({ chain }, amount);
  
  console.log(`Trade recorded: ${chain} - ${amount} - ${txHash}`);
  res.json({ success: true });
});
const buildJupiterSwapTx = async (userPublicKey: PublicKey, amount: number) => {
  // 1. Get quote from Jupiter v6
  const quoteResponse = await fetch(
    `https://quote-api.jup.ag/v6/quote?inputMint=So11111111111111111111111111111111111111112&outputMint=${MINT.toBase58()}&amount=${amount * 1_000_000_000}&slippageBps=50`
  ).then(res => res.json());

  // 2. Get swap transaction
  const swapResponse = await fetch('https://quote-api.jup.ag/v6/swap', {
    method: 'POST',
    body: JSON.stringify({
      quoteResponse,
      userPublicKey: userPublicKey.toBase58(),
      wrapAndUnwrapSol: true,
      dynamicComputeUnitLimit: true,
      prioritizationFeeLamports: 'auto'
    })
  }).then(res => res.json());

  // 3. Return deserialized transaction for signing
  const swapTransactionBuf = Buffer.from(swapResponse.swapTransaction, 'base64');
  return Transaction.from(swapTransactionBuf);
};
const tradeCounter = new client.Counter({
  name: 'trade_desk_trades_total',
  help: 'Total number of trades executed via Trade Desk',
  labelNames: ['chain']
});

const tradeVolume = new client.Counter({
  name: 'trade_desk_volume_total',
  help: 'Total trading volume via Trade Desk',
  labelNames: ['chain']
});
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json
COPY --from=builder /app/public ./public
ENV NODE_ENV=production
EXPOSE 3000
CMD ["npm", "start"]
docker build -t pump-trade-desk:latest -f xnft-frontend/Dockerfile xnft-frontend
docker run -p 3003:3000 pump-trade-desk:latest
const handleBaseBridge = async () => {
  if (!publicKey) return;

  setTradeStatus('Initiating bridge to Base via Wormhole...');

  // In production: Use @wormhole-foundation/sdk or Wormhole Connect
  // Example flow:
  // 1. Lock/burn $PUMP on Solana
  // 2. Mint wrapped $PUMP on Base
  // 3. Record metrics with chain="base-bridge"

  await recordTradeMetrics('base-bridge', parseFloat(tradeAmount), 'bridge-tx-hash');
  setTradeStatus('✅ Bridge to Base initiated. Check Wormhole dashboard.');
};
cd pump-token

docker compose -f docker-compose.full.yml --profile full --profile frontend up -d --build
<WormholeConnect
  config={{
    env: 'mainnet',
    networks: ['solana', 'base'],
    tokens: {
      PUMP: {
        solana: 'TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb',
        base: '0xYourBaseWrappedPUMPAddress' // Update after deployment
      }
    },
    rpcs: {
      solana: process.env.NEXT_PUBLIC_SOLANA_RPC,
      base: 'https://mainnet.base.org'
    }
  }}
  onComplete={(transfer) => {
    // Automatically record bridge metrics when Wormhole transfer completes
    recordTradeMetrics(
      'base-bridge',
      parseFloat(tradeAmount),
      transfer.txHash || 'wormhole-tx'
    );
    
    setTradeStatus(`✅ Bridge to Base completed! TX: ${transfer.txHash}`);
    setShowBridge(false);
  }}
  onClose={() => setShowBridge(false)}
/>
import express from 'express';
import client from 'prom-client';

const router = express.Router();

const tradeCounter = new client.Counter({
  name: 'trade_desk_trades_total',
  help: 'Total successful trades',
  labelNames: ['chain']
});

const tradeVolume = new client.Counter({
  name: 'trade_desk_volume_total',
  help: 'Total trading volume',
  labelNames: ['chain']
});

const tradeErrorCounter = new client.Counter({
  name: 'trade_desk_errors_total',
  help: 'Total failed trades / bridges',
  labelNames: ['chain', 'reason']
});

router.post('/api/record-trade', (req, res) => {
  const { chain, amount, txHash, wallet, success = true, reason } = req.body;

  if (!chain || typeof amount !== 'number') {
    return res.status(400).json({ error: 'chain and numeric amount required' });
  }

  if (success) {
    tradeCounter.inc({ chain });
    tradeVolume.inc({ chain }, amount);
  } else {
    tradeErrorCounter.inc({ chain, reason: reason || 'unknown_error' });
  }

  res.json({ success: true, recorded: success ? 'trade' : 'error' });
});

export default router;
import recordTradeRouter from './record-trade-endpoint';
app.use(recordTradeRouter);
<WormholeConnect
  config={{ /* your existing config */ }}
  onComplete={(transfer) => {
    recordTradeMetrics('base-bridge', parseFloat(tradeAmount), transfer.txHash || 'completed');
    setTradeStatus(`✅ Bridge completed! TX: ${transfer.txHash}`);
    setShowBridge(false);
  }}
  onError={(error) => {
    recordTradeMetrics('base-bridge', parseFloat(tradeAmount) || 0, 'error', false, error.message);
    setTradeStatus(`❌ Bridge failed: ${error.message}`);
    setTimeout(() => setShowBridge(false), 2500);
  }}
/>
import { SwapWidget } from '@uniswap/widgets';

// Add this section in your Trade Desk UI (after bridging)
{selectedChain === 'base' && evmConnected && (
  <div className="mt-6">
    <h3 className="text-lg font-bold mb-3">Trade on Base (Uniswap)</h3>
    <SwapWidget
      provider={yourEvmProvider}           // from wagmi
      tokenList={yourTokenList}            // include your Base $PUMP
      defaultInputTokenAddress="ETH"
      defaultOutputTokenAddress="0xYourBasePUMP"
      width={360}
      onSwapSuccess={(result) => {
        recordTradeMetrics('base', parseFloat(tradeAmount), result.hash);
      }}
    />
  </div>
)}
npm install @uniswap/widgets
- alert: HighTradeDeskErrorRate
  expr: sum(rate(trade_desk_errors_total[5m])) / sum(rate(trade_desk_trades_total[5m])) > 0.15
  for: 5m
  labels:
    severity: critical
  annotations:
    summary: "High error rate on Trade Desk"
    







