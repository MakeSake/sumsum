// ----- Chrono Empires: Idle Clicker -----

// Era data
const eras = [
  { name: "Stone Age", color: "var(--stone)", icon: "assets/era_stone.png", req: 0 },
  { name: "Bronze Age", color: "var(--bronze)", icon: "assets/era_bronze.png", req: 100 },
  { name: "Medieval", color: "var(--medieval)", icon: "assets/era_medieval.png", req: 500 },
  { name: "Industrial", color: "var(--industrial)", icon: "assets/era_industrial.png", req: 2000 },
  { name: "Futuristic", color: "var(--future)", icon: "assets/era_future.png", req: 10000 }
];

// Game state
let state = {
  shards: 0,
  shardsPerClick: 1,
  workers: 0,
  buildings: 0,
  shardsPerSecond: 0,
  workerCost: 10,
  buildingCost: 100,
  era: 0,
  prestigeMult: 1,
  prestigeCount: 0
};

// Upgrade scaling
const workerBase = 10, workerGrowth = 1.15;
const buildingBase = 100, buildingGrowth = 1.25;
const workerIncome = 1; // per second
const buildingIncome = 8; // per second

// Prestige
const prestigeReq = 1000;
const prestigeMultInc = 0.5;

// DOM
const el = {
  shards: document.getElementById('shard-count'),
  shardRate: document.getElementById('shard-rate'),
  workers: document.getElementById('worker-count'),
  buildings: document.getElementById('building-count'),
  era: document.getElementById('current-era'),
  eraIcon: document.getElementById('era-icon'),
  prestige: document.getElementById('prestige-mult'),
  clickBtn: document.getElementById('click-btn'),
  buyWorkerBtn: document.getElementById('buy-worker-btn'),
  buyBuildingBtn: document.getElementById('buy-building-btn'),
  workerCost: document.getElementById('worker-cost'),
  buildingCost: document.getElementById('building-cost'),
  prestigeBtn: document.getElementById('prestige-btn'),
  prestigeInfo: document.getElementById('prestige-info'),
  saveBtn: document.getElementById('save-btn'),
  loadBtn: document.getElementById('load-btn'),
  saveInfo: document.getElementById('save-info'),
  clickSound: document.getElementById('click-sound'),
  upgradeSound: document.getElementById('upgrade-sound'),
  prestigeSound: document.getElementById('prestige-sound')
};

// Simple animation
function animateButton(btn) {
  btn.style.transform = "scale(1.08)";
  setTimeout(() => btn.style.transform = "scale(1)", 120);
}

// Update UI
function updateUI() {
  // Era
  let eraIdx = eras.length - 1;
  for (let i = 0; i < eras.length; i++) {
    if (state.shards < eras[i].req) {
      eraIdx = i - 1;
      break;
    }
  }
  if (eraIdx < 0) eraIdx = 0;
  state.era = eraIdx;
  el.era.textContent = eras[eraIdx].name;
  el.eraIcon.src = eras[eraIdx].icon;
  el.eraIcon.alt = eras[eraIdx].name + " Icon";
  el.era.parentElement.style.background = `linear-gradient(90deg, ${eras[eraIdx].color}, var(--card-bg))`;

  // Stats
  el.shards.textContent = Math.floor(state.shards);
  el.shardRate.textContent = (state.shardsPerSecond * state.prestigeMult).toFixed(1);
  el.workers.textContent = state.workers;
  el.buildings.textContent = state.buildings;
  el.prestige.textContent = state.prestigeMult.toFixed(1) + "x";

  // Costs
  el.workerCost.textContent = `(${Math.floor(state.workerCost)})`;
  el.buildingCost.textContent = `(${Math.floor(state.buildingCost)})`;

  // Prestige button
  if (state.shards >= prestigeReq) {
    el.prestigeBtn.disabled = false;
    el.prestigeInfo.textContent = "Ready!";
  } else {
    el.prestigeBtn.disabled = true;
    el.prestigeInfo.textContent = `Reach ${prestigeReq} shards`;
  }
}

// Calculate income
function recalcIncome() {
  state.shardsPerSecond =
    (state.workers * workerIncome + state.buildings * buildingIncome);
}

// Click handler
el.clickBtn.onclick = () => {
  state.shards += state.shardsPerClick * state.prestigeMult;
  animateButton(el.clickBtn);
  if (el.clickSound) el.clickSound.play();
  updateUI();
  maybeEraChange();
};

// Buy worker
el.buyWorkerBtn.onclick = () => {
  if (state.shards >= state.workerCost) {
    state.shards -= state.workerCost;
    state.workers++;
    state.workerCost = workerBase * Math.pow(workerGrowth, state.workers);
    recalcIncome();
    animateButton(el.buyWorkerBtn);
    if (el.upgradeSound) el.upgradeSound.play();
    updateUI();
    maybeEraChange();
  }
};

// Buy building
el.buyBuildingBtn.onclick = () => {
  if (state.shards >= state.buildingCost) {
    state.shards -= state.buildingCost;
    state.buildings++;
    state.buildingCost = buildingBase * Math.pow(buildingGrowth, state.buildings);
    recalcIncome();
    animateButton(el.buyBuildingBtn);
    if (el.upgradeSound) el.upgradeSound.play();
    updateUI();
    maybeEraChange();
  }
};

// Prestige
el.prestigeBtn.onclick = () => {
  if (state.shards >= prestigeReq) {
    state.prestigeMult += prestigeMultInc;
    state.prestigeCount++;
    state.shards = 0;
    state.workers = 0;
    state.buildings = 0;
    state.workerCost = workerBase;
    state.buildingCost = buildingBase;
    recalcIncome();
    animateButton(el.prestigeBtn);
    if (el.prestigeSound) el.prestigeSound.play();
    updateUI();
    maybeEraChange();
  }
};

// Era visual change
function maybeEraChange() {
  // Called after any action to update visuals if era changes
  updateUI();
}

// Save
el.saveBtn.onclick = () => {
  localStorage.setItem('chronoEmpiresSave', JSON.stringify(state));
  el.saveInfo.textContent = "Game Saved!";
  setTimeout(() => (el.saveInfo.textContent = ""), 1200);
};

// Load
el.loadBtn.onclick = () => {
  let save = localStorage.getItem('chronoEmpiresSave');
  if (save) {
    state = JSON.parse(save);
    recalcIncome();
    updateUI();
    el.saveInfo.textContent = "Loaded!";
    setTimeout(() => (el.saveInfo.textContent = ""), 1200);
  } else {
    el.saveInfo.textContent = "No save found";
    setTimeout(() => (el.saveInfo.textContent = ""), 1200);
  }
};

// Main loop: automatic income
setInterval(() => {
  state.shards += state.shardsPerSecond * state.prestigeMult / 10;
  updateUI();
  maybeEraChange();
}, 100);

// First UI render
recalcIncome();
updateUI();

// Load on start
window.onload = () => {
  let save = localStorage.getItem('chronoEmpiresSave');
  if (save) {
    state = JSON.parse(save);
    recalcIncome();
    updateUI();
  }
};
