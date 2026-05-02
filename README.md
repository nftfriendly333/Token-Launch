<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>$PvE // 2+1 ETH Configuration Visualizer</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;700;800&family=Orbitron:wght@500;700;900&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #05070d;
    --panel: #0d1320;
    --line: #1a2238;
    --line-2: #243153;
    --txt: #c8d3e8;
    --dim: #6b7794;
    --accent: #00ffa3;
    --accent-2: #00d9ff;
    --warn: #ffb547;
    --danger: #ff4773;
    --gold: #ffd166;
    --inject: #b066ff;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  html, body {
    background: var(--bg);
    color: var(--txt);
    font-family: 'JetBrains Mono', monospace;
    font-size: 13px;
    line-height: 1.5;
    min-height: 100vh;
  }
  body::before {
    content: '';
    position: fixed; inset: 0;
    background:
      radial-gradient(ellipse at 20% 0%, rgba(0,217,255,0.06), transparent 50%),
      radial-gradient(ellipse at 80% 100%, rgba(176,102,255,0.05), transparent 50%);
    pointer-events: none; z-index: 0;
  }
  body::after {
    content: '';
    position: fixed; inset: 0;
    background-image:
      linear-gradient(rgba(255,255,255,0.012) 1px, transparent 1px),
      linear-gradient(90deg, rgba(255,255,255,0.012) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none; z-index: 0;
  }
  .wrap {
    position: relative; z-index: 1;
    max-width: 1280px;
    margin: 0 auto;
    padding: 20px 14px 60px;
  }
  .hdr {
    border: 1px solid var(--line);
    background: linear-gradient(180deg, var(--panel), transparent);
    padding: 18px 20px;
    margin-bottom: 18px;
    position: relative;
  }
  .hdr::before, .hdr::after { content: ''; position: absolute; width: 12px; height: 12px; }
  .hdr::before { top: -1px; left: -1px; border-top: 2px solid var(--accent-2); border-left: 2px solid var(--accent-2); }
  .hdr::after { bottom: -1px; right: -1px; border-bottom: 2px solid var(--inject); border-right: 2px solid var(--inject); }
  .tag { font-size: 10px; color: var(--accent-2); letter-spacing: 0.3em; margin-bottom: 6px; }
  h1 {
    font-family: 'Orbitron', sans-serif;
    font-weight: 900;
    font-size: clamp(20px, 4vw, 28px);
    letter-spacing: 0.04em;
    color: #fff;
    margin-bottom: 6px;
  }
  h1 .pve { color: var(--accent-2); }
  .sub { font-size: 12px; color: var(--dim); }

  .meta-row {
    display: flex; flex-wrap: wrap; gap: 14px;
    margin-top: 12px; font-size: 11px; color: var(--dim);
  }
  .meta-row span b { color: var(--accent-2); font-weight: 700; }

  .stats {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
    gap: 8px;
    margin-bottom: 16px;
  }
  .stat-card {
    border: 1px solid var(--line);
    background: var(--panel);
    padding: 10px 12px;
  }
  .stat-card .k {
    font-size: 9px;
    letter-spacing: 0.2em;
    color: var(--dim);
  }
  .stat-card .v {
    font-family: 'Orbitron', sans-serif;
    font-size: 16px;
    margin-top: 4px;
    letter-spacing: 0.03em;
  }
  .v.gold { color: var(--gold); }
  .v.green { color: var(--accent); }
  .v.red { color: var(--danger); }
  .v.cyan { color: var(--accent-2); }
  .v.purple { color: var(--inject); }

  .chart-wrap {
    border: 1px solid var(--line);
    background: var(--panel);
    padding: 16px;
    margin-bottom: 16px;
  }
  .chart-hdr {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 12px;
    flex-wrap: wrap;
    gap: 8px;
  }
  .chart-hdr h3 {
    font-family: 'Orbitron', sans-serif;
    font-size: 13px;
    color: #fff;
    letter-spacing: 0.1em;
  }
  .controls {
    display: flex;
    gap: 8px;
    flex-wrap: wrap;
  }
  button.btn {
    background: var(--accent);
    color: var(--bg);
    border: none;
    padding: 8px 18px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    font-weight: 700;
    letter-spacing: 0.2em;
    cursor: pointer;
  }
  button.btn:hover { box-shadow: 0 0 16px var(--accent); }
  button.btn.alt {
    background: transparent;
    color: var(--dim);
    border: 1px solid var(--line-2);
  }
  button.btn.alt:hover { color: var(--txt); border-color: var(--accent); }

  svg { width: 100%; height: auto; display: block; }

  .legend {
    display: flex;
    gap: 14px;
    font-size: 10px;
    flex-wrap: wrap;
    margin-top: 8px;
    color: var(--dim);
  }
  .legend span { display: flex; align-items: center; gap: 6px; }
  .legend i { width: 12px; height: 2px; display: inline-block; }
  .legend i.box { width: 10px; height: 10px; }

  .events {
    border: 1px solid var(--line);
    background: var(--panel);
    padding: 14px;
    margin-bottom: 16px;
  }
  .events h3 {
    font-family: 'Orbitron', sans-serif;
    font-size: 12px;
    color: var(--accent-2);
    letter-spacing: 0.15em;
    margin-bottom: 10px;
  }
  .event-list {
    max-height: 220px;
    overflow-y: auto;
    font-size: 11px;
  }
  .event-list::-webkit-scrollbar { width: 4px; }
  .event-list::-webkit-scrollbar-track { background: var(--bg); }
  .event-list::-webkit-scrollbar-thumb { background: var(--line-2); }
  .event-row {
    display: flex;
    gap: 10px;
    padding: 5px 0;
    border-bottom: 1px dashed var(--line);
    align-items: baseline;
  }
  .event-row .t { color: var(--dim); font-size: 10px; min-width: 50px; }
  .event-row.inject { background: rgba(176,102,255,0.08); margin: 0 -8px; padding: 6px 8px; border-bottom: 1px solid var(--inject); }
  .event-row.inject .e { color: var(--inject); font-weight: 700; }
  .event-row.whale .e { color: var(--gold); font-weight: 700; }
  .event-row.dump .e { color: var(--danger); font-weight: 700; }
  .event-row.lp .e { color: var(--accent); font-weight: 700; }

  .takeaway {
    border: 1px solid var(--inject);
    background: linear-gradient(135deg, rgba(0,217,255,0.04), rgba(176,102,255,0.04));
    padding: 16px;
    position: relative;
  }
  .takeaway::before {
    content: '▶ 2+1 CONFIG ANALYSIS';
    position: absolute;
    top: -8px;
    left: 16px;
    background: var(--bg);
    padding: 0 8px;
    font-size: 9px;
    letter-spacing: 0.3em;
    color: var(--inject);
  }
  .takeaway p { font-size: 12px; line-height: 1.7; margin-bottom: 8px; }
  .takeaway b { color: var(--accent); }

  .param-panel {
    border: 1px solid var(--line);
    background: var(--panel);
    padding: 16px;
    margin-bottom: 16px;
    position: relative;
  }
  .param-panel::before {
    content: '';
    position: absolute; top: -1px; left: 30px;
    width: 8px; height: 8px;
    border-top: 2px solid var(--inject);
    border-left: 2px solid var(--inject);
  }
  .param-panel h3 {
    font-family: 'Orbitron', sans-serif;
    font-size: 12px;
    color: var(--inject);
    letter-spacing: 0.15em;
    margin-bottom: 14px;
  }
  .param-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
    gap: 14px;
    margin-bottom: 14px;
  }
  .param-group {
    border: 1px solid var(--line);
    background: rgba(0,0,0,0.25);
    padding: 10px 12px;
  }
  .param-label {
    font-size: 9px;
    color: var(--dim);
    letter-spacing: 0.18em;
    margin-bottom: 8px;
  }
  .param-group input[type=range] {
    -webkit-appearance: none;
    width: 100%;
    height: 4px;
    background: var(--line-2);
    outline: none;
    margin: 6px 0;
  }
  .param-group input[type=range]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 14px; height: 14px;
    background: var(--inject);
    cursor: pointer;
    box-shadow: 0 0 8px var(--inject);
    border: 2px solid var(--bg);
  }
  .param-group input[type=range]::-moz-range-thumb {
    width: 14px; height: 14px;
    background: var(--inject);
    cursor: pointer;
    border: 2px solid var(--bg);
  }
  .param-readout {
    font-family: 'Orbitron', sans-serif;
    font-size: 12px;
    color: var(--accent);
    font-weight: 700;
    margin-top: 4px;
  }
  .param-actions {
    display: flex;
    gap: 8px;
    flex-wrap: wrap;
    padding-top: 10px;
    border-top: 1px dashed var(--line);
  }
</style>
</head>
<body>
<div class="wrap">

  <header class="hdr">
    <div class="tag">// CONFIG A · 2 ETH SEED + 1 ETH SINGLE-SIDE</div>
    <h1><span class="pve">$PvE</span> :: 2+1 LIVE CHART</h1>
    <div class="sub">Defense-first allocation. Tweak parameters below before running.</div>
    <div class="meta-row">
      <span>T+0 SEED: <b>2.0 ETH + 80M tokens</b></span>
      <span>T+6 INJECT: <b>+1.0 ETH single-side</b></span>
      <span>START MC: <b>$58K</b></span>
      <span>POST-INJECT MC: <b>~$87K</b></span>
      <span>500K CLIMB: <b>5.7x</b></span>
    </div>
  </header>

  <section class="param-panel">
    <h3>// PARAMETER CONTROLS</h3>
    <div class="param-grid">
      <div class="param-group">
        <div class="param-label">BUY PRESSURE · EARLY (T+0 to T+5)</div>
        <input type="range" id="earlyBuy" min="40" max="80" step="1" value="63">
        <div class="param-readout"><span id="earlyBuyVal">63</span>% buy bias</div>
      </div>
      <div class="param-group">
        <div class="param-label">BUY PRESSURE · STEADY (T+5 onward)</div>
        <input type="range" id="steadyBuy" min="40" max="75" step="1" value="58">
        <div class="param-readout"><span id="steadyBuyVal">58</span>% buy bias</div>
      </div>
      <div class="param-group">
        <div class="param-label">VOLUME MULTIPLIER</div>
        <input type="range" id="volMult" min="50" max="300" step="10" value="150">
        <div class="param-readout"><span id="volMultVal">1.5</span>x baseline</div>
      </div>
      <div class="param-group">
        <div class="param-label">WHALE FREQUENCY</div>
        <input type="range" id="whaleProb" min="0" max="15" step="1" value="5">
        <div class="param-readout"><span id="whaleProbVal">5</span>% of trades</div>
      </div>
      <div class="param-group">
        <div class="param-label">BURST INTENSITY</div>
        <input type="range" id="burstMult" min="50" max="300" step="10" value="150">
        <div class="param-readout"><span id="burstMultVal">1.5</span>x</div>
      </div>
      <div class="param-group">
        <div class="param-label"># OF MICRO DUMPS</div>
        <input type="range" id="dumpCount" min="0" max="6" step="1" value="4">
        <div class="param-readout"><span id="dumpCountVal">4</span> dumps scheduled</div>
      </div>
      <div class="param-group">
        <div class="param-label"># OF COMMUNITY LPs</div>
        <input type="range" id="lpCount" min="0" max="6" step="1" value="3">
        <div class="param-readout"><span id="lpCountVal">3</span> incentivized adds</div>
      </div>
      <div class="param-group">
        <div class="param-label">INITIAL ETH SEED</div>
        <input type="range" id="initialEth" min="10" max="40" step="5" value="20">
        <div class="param-readout"><span id="initialEthVal">2.0</span> ETH paired</div>
      </div>
      <div class="param-group">
        <div class="param-label">SINGLE-SIDE INJECTION</div>
        <input type="range" id="injectEth" min="0" max="60" step="1" value="20">
        <div class="param-readout"><span id="injectEthVal">1.00</span> ETH at T+6</div>
      </div>
      <div class="param-group">
        <div class="param-label">INJECTION TIME (MIN)</div>
        <input type="range" id="injectTime" min="3" max="15" step="1" value="6">
        <div class="param-readout">T+<span id="injectTimeVal">6</span>:00</div>
      </div>
    </div>
    <div class="param-actions">
      <button class="btn alt" id="resetParamsBtn">⟲ RESET TO DEFAULTS</button>
      <button class="btn alt" id="presetQuiet">PRESET: QUIET</button>
      <button class="btn alt" id="presetEngaged">PRESET: ENGAGED</button>
      <button class="btn alt" id="presetViral">PRESET: VIRAL</button>
    </div>
  </section>

  <section class="stats">
    <div class="stat-card">
      <div class="k">CURRENT PRICE</div>
      <div class="v cyan" id="curPrice">--</div>
    </div>
    <div class="stat-card">
      <div class="k">CURRENT MC</div>
      <div class="v green" id="curMC">--</div>
    </div>
    <div class="stat-card">
      <div class="k">PEAK MC</div>
      <div class="v gold" id="peakMC">--</div>
    </div>
    <div class="stat-card">
      <div class="k">ETH IN POOL</div>
      <div class="v purple" id="poolEth">2.00</div>
    </div>
    <div class="stat-card">
      <div class="k">VOLUME</div>
      <div class="v" id="totVol">$0</div>
    </div>
    <div class="stat-card">
      <div class="k">BUY PRESSURE</div>
      <div class="v green" id="buyPressure">63%</div>
    </div>
    <div class="stat-card">
      <div class="k">TIME</div>
      <div class="v" id="simTime">T+0:00</div>
    </div>
  </section>

  <section class="chart-wrap">
    <div class="chart-hdr">
      <h3>// LIVE PRICE ACTION · 60 MIN WINDOW · CONFIG 2+1</h3>
      <div class="controls">
        <button class="btn" id="runBtn">▶ RUN</button>
        <button class="btn alt" id="resetBtn">RESET</button>
        <button class="btn alt" id="speedBtn">SPEED: 4x</button>
      </div>
    </div>
    <svg id="priceChart" viewBox="0 0 800 380" preserveAspectRatio="none"></svg>
    <div class="legend">
      <span><i class="box" style="background:#00ffa3;"></i> Bullish candle</span>
      <span><i class="box" style="background:#ff4773;"></i> Bearish candle</span>
      <span><i style="background:#b066ff; height:1px; border-top:2px dashed #b066ff;"></i> Team T+6 inject</span>
      <span><i style="background:#00ffa3; height:1px; border-top:2px dashed #00ffa3;"></i> 🤝 Community LP</span>
      <span><i style="background:#ff1f5e; height:1px; border-top:2px dashed #ff1f5e;"></i> 🐋 Micro Dump</span>
      <span><i style="background:#ffd166; height:1px; border-top:2px dashed #ffd166;"></i> Peak</span>
      <span>1 candle = 30 sec</span>
    </div>
  </section>

  <section class="events">
    <h3>// EVENT LOG</h3>
    <div class="event-list" id="events"></div>
  </section>

  <section class="takeaway">
    <p id="takeawayText">
      Press <b>▶ RUN</b> to visualize the 2+1 configuration. Strategy: launch with <b>2.0 ETH paired against 80M tokens</b> (~$58K MC) — already deeper than 1.5 ETH alternatives. At <b>T+6min</b>, single-side <b>+1.0 ETH</b> pumps price ~33% (smaller pump than 2 ETH inject because pool is already deeper). Final pool: 3 ETH + community LP additions. Watch how the deeper initial pool absorbs the T+3 micro whale dump much better than thinner configs would.
    </p>
  </section>

</div>

<script>
const ETH_PRICE = 2320;
const TOTAL_SUPPLY = 1_000_000_000;
const TOKENS_IN_LP = TOTAL_SUPPLY * 0.08;
const SIM_DURATION_MIN = 60;
const TICK_MS_BASE = 100;
let speedMultiplier = 4;

// Live parameters - read from sliders before each run
const params = {
  earlyBuy: 0.63,
  steadyBuy: 0.58,
  volMult: 1.5,
  whaleProb: 0.05,
  burstMult: 1.5,
  dumpCount: 4,
  lpCount: 3,
  initialEth: 2.0,
  injectEth: 1.0,
  injectTime: 360
};

// Available micro dumps - subset will be used based on dumpCount
const ALL_MICRO_DUMPS = [
  { atSec: 180, tokens: 3_000_000, label: 'Micro whale #1' },
  { atSec: 420, tokens: 1_200_000, label: 'Micro whale #2' },
  { atSec: 660, tokens: 900_000, label: 'Micro whale #3' },
  { atSec: 960, tokens: 750_000, label: 'Micro whale #4' },
  { atSec: 1500, tokens: 600_000, label: 'Micro whale #5' },
  { atSec: 2100, tokens: 500_000, label: 'Micro whale #6' }
];

function getActiveMicroDumps() {
  return ALL_MICRO_DUMPS.slice(0, params.dumpCount);
}

function generateCommunityLPs() {
  const lps = [
    { atSec: 240 + Math.floor(Math.random() * 120), eth: 0.05 + Math.random() * 0.10, handle: 'fibtech_degen' },
    { atSec: 720 + Math.floor(Math.random() * 240), eth: 0.05 + Math.random() * 0.10, handle: 'pve_chad' },
    { atSec: 1320 + Math.floor(Math.random() * 360), eth: 0.05 + Math.random() * 0.10, handle: 'iron_arena_op' },
    { atSec: 1800 + Math.floor(Math.random() * 240), eth: 0.05 + Math.random() * 0.10, handle: 'op_native_lp' },
    { atSec: 2400 + Math.floor(Math.random() * 300), eth: 0.05 + Math.random() * 0.10, handle: 'velo_voter' },
    { atSec: 2880 + Math.floor(Math.random() * 240), eth: 0.05 + Math.random() * 0.10, handle: 'pve_holder42' }
  ];
  return lps.slice(0, params.lpCount);
}

let state = null;
let simTimer = null;

function fmt$(n) {
  if (n >= 1_000_000) return '$' + (n/1_000_000).toFixed(2) + 'M';
  if (n >= 1_000) return '$' + (n/1_000).toFixed(1) + 'K';
  if (n >= 1) return '$' + n.toFixed(2);
  if (n >= 0.0001) return '$' + n.toFixed(6);
  return '$' + n.toExponential(2);
}

function fmtTime(sec) {
  const m = Math.floor(sec / 60);
  const s = Math.floor(sec % 60);
  return `T+${m}:${s.toString().padStart(2,'0')}`;
}

function initState() {
  return {
    ethRes: params.initialEth,
    tokenRes: TOKENS_IN_LP,
    elapsedSec: 0,
    candles: [],
    currentCandle: null,
    depthHistory: [],
    injectionsApplied: [],
    events: [],
    totalVolume: 0,
    peakMC: 0,
    communityLPs: generateCommunityLPs(),
    communityLPsApplied: new Set(),
    dumpsFired: new Set(),
    activeMicroDumps: getActiveMicroDumps(),
    burstUntil: 0,
    burstIntensity: 0
  };
}

function priceOf(s) { return (s.ethRes * ETH_PRICE) / s.tokenRes; }
function mcOf(s) { return priceOf(s) * TOTAL_SUPPLY; }

function executeRandomTrade(s) {
  const buyChance = s.elapsedSec < 300 ? params.earlyBuy : params.steadyBuy;
  const isBuy = Math.random() < buyChance;
  const isWhale = Math.random() < params.whaleProb;
  let usdSize;
  if (isWhale) usdSize = 1000 + Math.random() * 1200;
  else usdSize = 20 + Math.pow(Math.random(), 2.4) * 550;

  const k = s.ethRes * s.tokenRes;
  const priceBefore = priceOf(s);

  if (isBuy) {
    const ethIn = (usdSize / ETH_PRICE) * 0.997;
    const newEth = s.ethRes + ethIn;
    const newTokens = k / newEth;
    s.ethRes = newEth;
    s.tokenRes = newTokens;
  } else {
    const tokensToSell = (usdSize / priceBefore) * 0.997;
    const maxSell = s.tokenRes * 0.12;
    const actual = Math.min(tokensToSell, maxSell);
    const newTokens = s.tokenRes + actual;
    const newEth = k / newTokens;
    s.ethRes = newEth;
    s.tokenRes = newTokens;
  }
  s.totalVolume += usdSize;

  if (isWhale) {
    s.events.push({
      time: s.elapsedSec,
      type: 'whale',
      msg: `🐋 ${isBuy ? 'BUY' : 'SELL'} ${fmt$(usdSize)} · MC ${fmt$(mcOf(s))}`
    });
  }
}

function applyInjection(s) {
  const oldPrice = priceOf(s);
  s.ethRes += params.injectEth;
  const newPrice = priceOf(s);
  const pumpPct = ((newPrice / oldPrice) - 1) * 100;

  s.injectionsApplied.push({ time: s.elapsedSec, eth: params.injectEth, priceAfter: newPrice });
  s.events.push({
    time: s.elapsedSec,
    type: 'inject',
    msg: `💉 SINGLE-SIDE +${params.injectEth} ETH (0 tokens) · price +${pumpPct.toFixed(1)}% · MC ${fmt$(mcOf(s))}`
  });
}

function applyMicroDump(s, dump) {
  const k = s.ethRes * s.tokenRes;
  const priceBefore = priceOf(s);
  const tokensWithFee = dump.tokens * 0.997;
  const newTokens = s.tokenRes + tokensWithFee;
  const newEth = k / newTokens;
  const ethReceived = s.ethRes - newEth;
  const usdReceived = ethReceived * ETH_PRICE;
  s.ethRes = newEth;
  s.tokenRes = newTokens;
  s.totalVolume += usdReceived;
  const priceAfter = priceOf(s);
  const dumpPct = ((priceBefore / priceAfter) - 1) * 100;

  s.events.push({
    time: s.elapsedSec,
    type: 'dump',
    msg: `🐋⚠️ ${dump.label} dumped ${(dump.tokens/1e6).toFixed(1)}M tokens · received ${fmt$(usdReceived)} · price -${dumpPct.toFixed(1)}% · MC ${fmt$(mcOf(s))}`,
    isDump: true
  });

  if (!s.dumpMarkers) s.dumpMarkers = [];
  s.dumpMarkers.push({ time: s.elapsedSec, tokens: dump.tokens, label: dump.label });
}

function applyCommunityLP(s, lp) {
  const currentPrice = priceOf(s);
  const ratio = s.tokenRes / s.ethRes;
  const tokensToAdd = lp.eth * ratio;
  s.ethRes += lp.eth;
  s.tokenRes += tokensToAdd;

  s.events.push({
    time: s.elapsedSec,
    type: 'lp',
    msg: `🤝 @${lp.handle} added LP: ${lp.eth.toFixed(3)} ETH + ${(tokensToAdd/1e6).toFixed(2)}M $PvE @ ${fmt$(currentPrice)}`
  });

  if (!s.lpMarkers) s.lpMarkers = [];
  s.lpMarkers.push({ time: s.elapsedSec, eth: lp.eth, handle: lp.handle });
}

function tick() {
  const s = state;
  s.elapsedSec += 1;

  // Team injection (uses dynamic params.injectTime, skip if injectEth = 0)
  if (params.injectEth > 0 && s.elapsedSec >= params.injectTime && s.injectionsApplied.length === 0) {
    applyInjection(s);
  }

  // Micro dumps (active subset based on params.dumpCount)
  for (const dump of s.activeMicroDumps) {
    if (s.elapsedSec >= dump.atSec && !s.dumpsFired.has(dump.atSec)) {
      s.dumpsFired.add(dump.atSec);
      applyMicroDump(s, dump);
    }
  }

  // Community LPs
  for (const lp of s.communityLPs) {
    if (s.elapsedSec >= lp.atSec && !s.communityLPsApplied.has(lp.atSec)) {
      s.communityLPsApplied.add(lp.atSec);
      applyCommunityLP(s, lp);
    }
  }

  // Trade frequency curve (front-loaded with bursts)
  let tradeProb;
  const t = s.elapsedSec;
  if (t < 180) tradeProb = 0.10;
  else if (t < 600) {
    const progress = (t - 180) / 420;
    tradeProb = 0.08 - (progress * 0.05);
  } else if (t < 1800) {
    tradeProb = 0.025;
    if (t > s.burstUntil) {
      if (Math.random() < 0.008 * params.burstMult) {
        s.burstUntil = t + 30 + Math.random() * 30;
        s.burstIntensity = (0.07 + Math.random() * 0.04) * params.burstMult;
      }
    }
    if (t < s.burstUntil) tradeProb = s.burstIntensity;
  } else {
    tradeProb = 0.015;
    if (t > s.burstUntil) {
      if (Math.random() < 0.004 * params.burstMult) {
        s.burstUntil = t + 20 + Math.random() * 40;
        s.burstIntensity = (0.05 + Math.random() * 0.03) * params.burstMult;
      }
    }
    if (t < s.burstUntil) tradeProb = s.burstIntensity;
  }
  // Apply dynamic volume multiplier
  tradeProb *= params.volMult;
  if (tradeProb > 0.95) tradeProb = 0.95;

  if (Math.random() < tradeProb) executeRandomTrade(s);

  // Build candles
  const candleIdx = Math.floor(s.elapsedSec / 30);
  const currentPrice = priceOf(s);
  if (!s.currentCandle || s.currentCandle.idx !== candleIdx) {
    if (s.currentCandle) s.candles.push(s.currentCandle);
    s.currentCandle = {
      idx: candleIdx,
      open: currentPrice,
      close: currentPrice,
      high: currentPrice,
      low: currentPrice,
      time: candleIdx * 30
    };
  } else {
    s.currentCandle.close = currentPrice;
    if (currentPrice > s.currentCandle.high) s.currentCandle.high = currentPrice;
    if (currentPrice < s.currentCandle.low) s.currentCandle.low = currentPrice;
  }

  const mc = mcOf(s);
  if (mc > s.peakMC) s.peakMC = mc;

  // Update UI
  document.getElementById('curPrice').textContent = fmt$(currentPrice);
  document.getElementById('curMC').textContent = fmt$(mc);
  document.getElementById('peakMC').textContent = fmt$(s.peakMC);
  document.getElementById('poolEth').textContent = s.ethRes.toFixed(2);
  document.getElementById('totVol').textContent = fmt$(s.totalVolume);
  document.getElementById('simTime').textContent = fmtTime(s.elapsedSec);

  const bpEl = document.getElementById('buyPressure');
  const currentBP = s.elapsedSec < 300 ? Math.round(params.earlyBuy * 100) : Math.round(params.steadyBuy * 100);
  bpEl.textContent = currentBP + '%';
  bpEl.className = 'v ' + (currentBP >= 60 ? 'green' : currentBP >= 50 ? 'cyan' : 'red');

  drawPriceChart(s);
  renderEvents(s);

  if (s.elapsedSec >= SIM_DURATION_MIN * 60) {
    if (s.currentCandle) s.candles.push(s.currentCandle);
    clearInterval(simTimer);
    simTimer = null;
    finalizeAnalysis(s);
    document.getElementById('runBtn').textContent = '▶ RUN AGAIN';
  }
}

function drawPriceChart(s) {
  const svg = document.getElementById('priceChart');
  const W = 800, H = 380;
  const padL = 70, padR = 20, padT = 20, padB = 50;
  const cw = W - padL - padR;
  const ch = H - padT - padB;

  const allCandles = [...s.candles];
  if (s.currentCandle) allCandles.push(s.currentCandle);
  if (allCandles.length === 0) { svg.innerHTML = ''; return; }

  let minP = Infinity, maxP = -Infinity;
  for (const c of allCandles) {
    if (c.low < minP) minP = c.low;
    if (c.high > maxP) maxP = c.high;
  }
  const range = maxP - minP || maxP * 0.1;
  minP -= range * 0.08;
  maxP += range * 0.15;
  if (minP < 0) minP = 0;

  const totalSlots = 120;
  const slotW = cw / totalSlots;
  const candleW = Math.max(2, slotW * 0.7);

  const xPos = (i) => padL + slotW * i + slotW / 2;
  const yPos = (price) => padT + ch - ((price - minP) / (maxP - minP)) * ch;
  const xFromTime = (t) => padL + (t / (SIM_DURATION_MIN * 60)) * cw;

  let html = '';

  // Grid lines (price)
  for (let i = 0; i <= 5; i++) {
    const p = minP + ((maxP - minP) / 5) * i;
    const y = yPos(p);
    const mc = p * TOTAL_SUPPLY;
    html += `<line x1="${padL}" y1="${y}" x2="${W - padR}" y2="${y}" stroke="#1a2238" stroke-width="1" stroke-dasharray="2,4"/>`;
    html += `<text x="${padL - 6}" y="${y + 3}" fill="#6b7794" font-size="9" text-anchor="end" font-family="JetBrains Mono">${fmt$(mc)}</text>`;
  }

  // Time grid (every 5 min)
  for (let m = 0; m <= 60; m += 5) {
    const x = xFromTime(m * 60);
    html += `<line x1="${x}" y1="${padT}" x2="${x}" y2="${padT + ch}" stroke="#1a2238" stroke-width="1" stroke-dasharray="2,4" opacity="0.5"/>`;
    html += `<text x="${x}" y="${padT + ch + 14}" fill="#6b7794" font-size="9" text-anchor="middle" font-family="JetBrains Mono">${m}m</text>`;
  }

  // Team injection marker (purple at dynamic time)
  if (params.injectEth > 0) {
    const injX = xFromTime(params.injectTime);
    html += `<line x1="${injX}" y1="${padT}" x2="${injX}" y2="${padT + ch}" stroke="#b066ff" stroke-width="1.5" stroke-dasharray="6,4" opacity="0.85"/>`;
    if (s.elapsedSec >= params.injectTime) {
      html += `<text x="${injX + 4}" y="${padT + 14}" fill="#b066ff" font-size="9" font-family="JetBrains Mono" font-weight="700">+${params.injectEth}Ξ SINGLE</text>`;
    }
  }

  // Community LP markers (green)
  if (s.lpMarkers) {
    for (const lp of s.lpMarkers) {
      const x = xFromTime(lp.time);
      html += `<line x1="${x}" y1="${padT}" x2="${x}" y2="${padT + ch}" stroke="#00ffa3" stroke-width="1" stroke-dasharray="3,3" opacity="0.55"/>`;
      html += `<text x="${x}" y="${padT + ch - 18}" fill="#00ffa3" font-size="10" font-family="JetBrains Mono" text-anchor="middle" font-weight="700">🤝</text>`;
      html += `<text x="${x + 3}" y="${padT + 56}" fill="#00ffa3" font-size="8" font-family="JetBrains Mono" opacity="0.85">+${lp.eth.toFixed(2)}Ξ</text>`;
    }
  }

  // Micro dump markers (red)
  if (s.dumpMarkers) {
    for (const md of s.dumpMarkers) {
      const x = xFromTime(md.time);
      html += `<line x1="${x}" y1="${padT}" x2="${x}" y2="${padT + ch}" stroke="#ff1f5e" stroke-width="2" stroke-dasharray="4,2" opacity="0.8"/>`;
      html += `<text x="${x}" y="${padT + ch - 4}" fill="#ff1f5e" font-size="13" font-family="JetBrains Mono" text-anchor="middle" font-weight="700">🐋</text>`;
      html += `<text x="${x + 3}" y="${padT + 42}" fill="#ff1f5e" font-size="8" font-family="JetBrains Mono" opacity="0.85">${(md.tokens/1e6).toFixed(1)}M!</text>`;
    }
  }

  // Peak line
  if (s.peakMC > 0) {
    const peakPrice = s.peakMC / TOTAL_SUPPLY;
    if (peakPrice >= minP && peakPrice <= maxP) {
      const py = yPos(peakPrice);
      html += `<line x1="${padL}" y1="${py}" x2="${W - padR}" y2="${py}" stroke="#ffd166" stroke-width="1" stroke-dasharray="3,3" opacity="0.5"/>`;
      html += `<text x="${W - padR - 4}" y="${py - 4}" fill="#ffd166" font-size="9" text-anchor="end" font-family="JetBrains Mono">PEAK ${fmt$(s.peakMC)}</text>`;
    }
  }

  // Candles
  allCandles.forEach((c) => {
    const slotIdx = Math.floor(c.time / 30);
    const x = xPos(slotIdx);
    const isUp = c.close >= c.open;
    const color = isUp ? '#00ffa3' : '#ff4773';
    const yH = yPos(c.high);
    const yL = yPos(c.low);
    const yO = yPos(c.open);
    const yC = yPos(c.close);
    const bodyTop = Math.min(yO, yC);
    const bodyH = Math.max(1.5, Math.abs(yC - yO));
    html += `<line x1="${x}" y1="${yH}" x2="${x}" y2="${yL}" stroke="${color}" stroke-width="1" opacity="0.85"/>`;
    html += `<rect x="${x - candleW/2}" y="${bodyTop}" width="${candleW}" height="${bodyH}" fill="${color}" opacity="0.95"/>`;
  });

  // Axis labels
  html += `<line x1="${padL}" y1="${padT + ch}" x2="${W - padR}" y2="${padT + ch}" stroke="#243153"/>`;
  html += `<text x="${W/2}" y="${H - 6}" fill="#6b7794" font-size="10" text-anchor="middle" font-family="JetBrains Mono" letter-spacing="2">TIME (MINUTES)</text>`;
  html += `<text x="14" y="${H/2}" fill="#6b7794" font-size="10" text-anchor="middle" font-family="JetBrains Mono" transform="rotate(-90, 14, ${H/2})" letter-spacing="2">MARKET CAP</text>`;

  svg.innerHTML = html;
}

function renderEvents(s) {
  const el = document.getElementById('events');
  const recent = s.events.slice(-30).reverse();
  el.innerHTML = recent.map(e =>
    `<div class="event-row ${e.type}"><span class="t">${fmtTime(e.time)}</span><span class="e">${e.msg}</span></div>`
  ).join('');
}

function finalizeAnalysis(s) {
  const finalMC = mcOf(s);
  const startMC = (params.initialEth * ETH_PRICE / TOKENS_IN_LP) * TOTAL_SUPPLY;
  const totalReturn = ((finalMC / startMC) - 1) * 100;
  const drawdown = ((s.peakMC - finalMC) / s.peakMC) * 100;
  const finalLpUsd = s.ethRes * ETH_PRICE;

  const k = s.ethRes * s.tokenRes;
  const ethIn = (1000 / ETH_PRICE) * 0.997;
  const newEth = s.ethRes + ethIn;
  const newTokens = k / newEth;
  const tokensOut = s.tokenRes - newTokens;
  const effPrice = 1000 / tokensOut;
  const slippage1k = ((effPrice - priceOf(s)) / priceOf(s)) * 100;

  let txt = `<b>Simulation complete.</b> Started at <b>${fmt$(startMC)}</b> MC with ${params.initialEth.toFixed(2)} ETH paired against 80M tokens. Peak hit <span style="color:var(--gold);">${fmt$(s.peakMC)}</span> · final settled at <span class="green">${fmt$(finalMC)}</span> (<b>${totalReturn > 0 ? '+' : ''}${totalReturn.toFixed(0)}%</b> from launch). `;
  txt += `Final pool: <b>${s.ethRes.toFixed(2)} ETH (${fmt$(finalLpUsd)})</b> with $1K slippage at ${slippage1k.toFixed(1)}%. `;
  txt += `<br><br>`;
  txt += `<b>Run parameters:</b> ${Math.round(params.earlyBuy*100)}%/${Math.round(params.steadyBuy*100)}% buy pressure · ${params.volMult.toFixed(1)}x volume · ${(params.whaleProb*100).toFixed(0)}% whale rate · ${params.dumpCount} micro dumps · ${params.lpCount} community LPs<br>`;
  txt += `<br>`;
  txt += `<b>Key observations:</b><br>`;
  txt += `• <b>Drawdown:</b> ${drawdown.toFixed(0)}% from peak ${drawdown < 30 ? '— solid stability' : drawdown < 50 ? '— manageable volatility' : '— rough run, RNG didn\'t cooperate'}<br>`;
  txt += `• <b>Final pool depth:</b> ${s.ethRes.toFixed(2)} ETH means $1K trades slip ${slippage1k.toFixed(1)}% — ${slippage1k < 5 ? 'healthy' : slippage1k < 12 ? 'workable' : 'still thin'}<br>`;
  txt += `• <b>Total volume processed:</b> ${fmt$(s.totalVolume)}`;

  document.getElementById('takeawayText').innerHTML = txt;
}

function startSim() {
  if (simTimer) clearInterval(simTimer);
  state = initState();
  const startMC = (params.initialEth * ETH_PRICE / TOKENS_IN_LP) * TOTAL_SUPPLY;
  state.events.push({
    time: 0,
    type: 'inject',
    msg: `🚀 LAUNCH · ${params.initialEth.toFixed(2)} ETH + 80M tokens · MC ${fmt$(startMC)}`
  });
  document.getElementById('runBtn').textContent = '⏸ RUNNING...';
  simTimer = setInterval(tick, TICK_MS_BASE / speedMultiplier);
}

function resetSim() {
  if (simTimer) { clearInterval(simTimer); simTimer = null; }
  state = initState();
  document.getElementById('priceChart').innerHTML = '';
  document.getElementById('events').innerHTML = '';
  document.getElementById('curPrice').textContent = '--';
  document.getElementById('curMC').textContent = '--';
  document.getElementById('peakMC').textContent = '--';
  document.getElementById('poolEth').textContent = params.initialEth.toFixed(2);
  document.getElementById('totVol').textContent = '$0';
  document.getElementById('simTime').textContent = 'T+0:00';
  document.getElementById('buyPressure').textContent = Math.round(params.earlyBuy * 100) + '%';
  document.getElementById('runBtn').textContent = '▶ RUN';
  const startMC = (params.initialEth * ETH_PRICE / TOKENS_IN_LP) * TOTAL_SUPPLY;
  document.getElementById('takeawayText').innerHTML = `Press <b>▶ RUN</b> to visualize. Current config: <b>${params.initialEth.toFixed(2)} ETH initial seed</b> + <b>${params.injectEth.toFixed(2)} ETH single-side injection at T+${Math.floor(params.injectTime/60)}min</b>. Starting MC: <b>${fmt$(startMC)}</b>. Buy pressure: ${Math.round(params.earlyBuy*100)}%/${Math.round(params.steadyBuy*100)}%. ${params.dumpCount} micro dumps · ${params.lpCount} community LPs · ${params.volMult.toFixed(1)}x volume.`;
}

document.getElementById('runBtn').addEventListener('click', startSim);
document.getElementById('resetBtn').addEventListener('click', resetSim);
document.getElementById('speedBtn').addEventListener('click', () => {
  const speeds = [1, 2, 4, 8];
  const idx = speeds.indexOf(speedMultiplier);
  speedMultiplier = speeds[(idx + 1) % speeds.length];
  document.getElementById('speedBtn').textContent = `SPEED: ${speedMultiplier}x`;
  if (simTimer) {
    clearInterval(simTimer);
    simTimer = setInterval(tick, TICK_MS_BASE / speedMultiplier);
  }
});

// ============ PARAMETER CONTROLS ============
const sliders = {
  earlyBuy: { el: 'earlyBuy', val: 'earlyBuyVal', transform: v => v / 100, display: v => v },
  steadyBuy: { el: 'steadyBuy', val: 'steadyBuyVal', transform: v => v / 100, display: v => v },
  volMult: { el: 'volMult', val: 'volMultVal', transform: v => v / 100, display: v => (v / 100).toFixed(1) },
  whaleProb: { el: 'whaleProb', val: 'whaleProbVal', transform: v => v / 100, display: v => v },
  burstMult: { el: 'burstMult', val: 'burstMultVal', transform: v => v / 100, display: v => (v / 100).toFixed(1) },
  dumpCount: { el: 'dumpCount', val: 'dumpCountVal', transform: v => parseInt(v), display: v => v },
  lpCount: { el: 'lpCount', val: 'lpCountVal', transform: v => parseInt(v), display: v => v },
  initialEth: { el: 'initialEth', val: 'initialEthVal', transform: v => v / 10, display: v => (v / 10).toFixed(1) },
  injectEth: { el: 'injectEth', val: 'injectEthVal', transform: v => v / 20, display: v => (v / 20).toFixed(2) },
  injectTime: { el: 'injectTime', val: 'injectTimeVal', transform: v => parseInt(v) * 60, display: v => v }
};

function syncSliderToParam(key) {
  const cfg = sliders[key];
  const slider = document.getElementById(cfg.el);
  const valEl = document.getElementById(cfg.val);
  const updateFromSlider = () => {
    params[key] = cfg.transform(slider.value);
    valEl.textContent = cfg.display(slider.value);
  };
  slider.addEventListener('input', updateFromSlider);
  updateFromSlider();
}

Object.keys(sliders).forEach(syncSliderToParam);

function setSliderValues(values) {
  Object.entries(values).forEach(([key, sliderVal]) => {
    const cfg = sliders[key];
    const slider = document.getElementById(cfg.el);
    slider.value = sliderVal;
    slider.dispatchEvent(new Event('input'));
  });
}

document.getElementById('resetParamsBtn').addEventListener('click', () => {
  setSliderValues({
    earlyBuy: 63, steadyBuy: 58, volMult: 150, whaleProb: 5,
    burstMult: 150, dumpCount: 4, lpCount: 3,
    initialEth: 20, injectEth: 20, injectTime: 6
  });
});
document.getElementById('presetQuiet').addEventListener('click', () => {
  setSliderValues({
    earlyBuy: 58, steadyBuy: 53, volMult: 100, whaleProb: 3,
    burstMult: 100, dumpCount: 4, lpCount: 2,
    initialEth: 20, injectEth: 20, injectTime: 6
  });
});
document.getElementById('presetEngaged').addEventListener('click', () => {
  setSliderValues({
    earlyBuy: 63, steadyBuy: 58, volMult: 150, whaleProb: 5,
    burstMult: 150, dumpCount: 4, lpCount: 3,
    initialEth: 20, injectEth: 20, injectTime: 6
  });
});
document.getElementById('presetViral').addEventListener('click', () => {
  setSliderValues({
    earlyBuy: 70, steadyBuy: 63, volMult: 250, whaleProb: 8,
    burstMult: 250, dumpCount: 4, lpCount: 5,
    initialEth: 20, injectEth: 20, injectTime: 6
  });
});

setTimeout(startSim, 500);
</script>
</body>
</html>
