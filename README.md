[index.html.html](https://github.com/user-attachments/files/28378857/index.html)
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Study Monitor — Kalpit</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@700;800&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0a0f;
    --surface: #111118;
    --border: #1e1e2e;
    --accent: #00ff88;
    --accent2: #ff3366;
    --warn: #ffaa00;
    --text: #e8e8f0;
    --muted: #555570;
    --glow: 0 0 20px rgba(0,255,136,0.3);
    --glow-red: 0 0 30px rgba(255,51,102,0.5);
  }
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Space Mono', monospace;
    min-height: 100vh;
    overflow-x: hidden;
  }
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(0,255,136,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0,255,136,0.03) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
    z-index: 0;
  }
  .app {
    position: relative;
    z-index: 1;
    max-width: 980px;
    margin: 0 auto;
    padding: 24px 20px 40px;
  }
  header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 28px;
    padding-bottom: 16px;
    border-bottom: 1px solid var(--border);
  }
  .logo {
    font-family: 'Syne', sans-serif;
    font-size: 1.4rem;
    font-weight: 800;
    letter-spacing: -0.5px;
  }
  .logo span { color: var(--accent); }
  .status-badge {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 0.7rem;
    letter-spacing: 1.5px;
    text-transform: uppercase;
    color: var(--muted);
  }
  .dot {
    width: 8px; height: 8px;
    border-radius: 50%;
    background: var(--muted);
    transition: background 0.3s, box-shadow 0.3s;
  }
  .dot.on  { background: var(--accent);  box-shadow: var(--glow);     animation: pulse 1.5s infinite; }
  .dot.warn{ background: var(--accent2); box-shadow: var(--glow-red); animation: pulse 0.5s infinite; }
  @keyframes pulse {
    0%,100%{ opacity:1; transform:scale(1);   }
    50%    { opacity:.6; transform:scale(1.3); }
  }
  .grid {
    display: grid;
    grid-template-columns: 1fr 320px;
    gap: 16px;
  }
  @media(max-width:720px){ .grid{ grid-template-columns:1fr; } }
  .card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 20px;
    position: relative;
    overflow: hidden;
  }
  .card::before {
    content:'';
    position:absolute; top:0; left:0; right:0; height:1px;
    background:linear-gradient(90deg,transparent,var(--accent),transparent);
    opacity:0; transition:opacity .5s;
  }
  .card.active::before{ opacity:1; }
  .cam-wrap {
    position: relative;
    width: 100%;
    aspect-ratio: 4/3;
    background: #050508;
    border-radius: 8px;
    overflow: hidden;
    margin-bottom: 14px;
  }
  #video {
    width:100%; height:100%;
    object-fit:cover; display:block;
    transform:scaleX(-1);
    border-radius:8px;
  }
  #canvas {
    position:absolute; inset:0;
    width:100%; height:100%;
    transform:scaleX(-1);
    pointer-events:none;
  }
  .cam-overlay {
    position:absolute; inset:0;
    display:flex; align-items:center; justify-content:center;
    background:rgba(10,10,15,0.9);
    border-radius:8px;
    flex-direction:column; gap:12px;
    font-size:.8rem; color:var(--muted); letter-spacing:1px;
  }
  .cam-overlay .icon{ font-size:2.5rem; }
  .state-display {
    display:flex; align-items:center; gap:12px;
    padding:12px 16px; border-radius:8px;
    border:1px solid var(--border);
    background:rgba(0,0,0,0.3);
    transition:all .4s;
  }
  .state-display.studying  { border-color:var(--accent);  background:rgba(0,255,136,0.06); box-shadow:var(--glow); }
  .state-display.distracted{ border-color:var(--accent2); background:rgba(255,51,102,0.08); box-shadow:var(--glow-red); animation:shake .4s ease-in-out; }
  @keyframes shake{
    0%,100%{transform:translateX(0);}
    25%{transform:translateX(-4px);}
    75%{transform:translateX(4px);}
  }
  .state-icon{ font-size:1.6rem; }
  .state-label{ font-family:'Syne',sans-serif; font-size:1.1rem; font-weight:700; }
  .state-label.studying  { color:var(--accent);  }
  .state-label.distracted{ color:var(--accent2); }
  .state-label.idle      { color:var(--muted);   }
  .sidebar{ display:flex; flex-direction:column; gap:14px; }
  .card-title{ font-size:.65rem; letter-spacing:2px; text-transform:uppercase; color:var(--muted); margin-bottom:14px; }
  .stat-row{ display:flex; justify-content:space-between; align-items:baseline; padding:8px 0; border-bottom:1px solid var(--border); }
  .stat-row:last-child{ border-bottom:none; }
  .stat-label{ font-size:.72rem; color:var(--muted); }
  .stat-value{ font-size:1rem; font-weight:700; color:var(--text); }
  .stat-value.green { color:var(--accent);  }
  .stat-value.red   { color:var(--accent2); }
  .stat-value.yellow{ color:var(--warn);    }
  .progress-wrap{ margin-top:12px; }
  .progress-label{ display:flex; justify-content:space-between; font-size:.68rem; color:var(--muted); margin-bottom:6px; }
  .progress-bar{ height:6px; background:var(--border); border-radius:99px; overflow:hidden; }
  .progress-fill{ height:100%; background:linear-gradient(90deg,var(--accent),#00ccff); border-radius:99px; transition:width 1s ease; width:0%; }
  .setting-row{ display:flex; justify-content:space-between; align-items:center; padding:8px 0; border-bottom:1px solid var(--border); font-size:.75rem; }
  .setting-row:last-child{ border-bottom:none; }
  .setting-label{ color:var(--muted); }
  input[type=range]{ -webkit-appearance:none; width:80px; height:4px; background:var(--border); border-radius:2px; outline:none; }
  input[type=range]::-webkit-slider-thumb{ -webkit-appearance:none; width:14px; height:14px; background:var(--accent); border-radius:50%; cursor:pointer; box-shadow:var(--glow); }
  select,input[type=text]{
    background:var(--bg); border:1px solid var(--border); color:var(--text);
    font-family:'Space Mono',monospace; font-size:.72rem;
    padding:4px 8px; border-radius:4px; outline:none; max-width:140px;
  }
  select:focus,input[type=text]:focus{ border-color:var(--accent); }
  .btn{
    display:inline-flex; align-items:center; gap:8px;
    padding:10px 20px; border-radius:8px;
    font-family:'Space Mono',monospace; font-size:.78rem; font-weight:700;
    letter-spacing:1px; cursor:pointer; border:1px solid;
    transition:all .2s; text-transform:uppercase;
  }
  .btn-primary{ background:var(--accent); color:#000; border-color:var(--accent); box-shadow:var(--glow); }
  .btn-primary:hover{ filter:brightness(1.15); transform:translateY(-1px); }
  .btn-danger{ background:transparent; color:var(--accent2); border-color:var(--accent2); }
  .btn-danger:hover{ background:rgba(255,51,102,0.1); }
  .btn-ghost{ background:transparent; color:var(--muted); border-color:var(--border); font-size:.7rem; }
  .btn-ghost:hover{ color:var(--text); border-color:var(--muted); }
  .controls{ display:flex; gap:10px; margin-top:14px; flex-wrap:wrap; }
  .log-list{ list-style:none; max-height:140px; overflow-y:auto; display:flex; flex-direction:column; gap:6px; }
  .log-list::-webkit-scrollbar{ width:3px; }
  .log-list::-webkit-scrollbar-thumb{ background:var(--border); border-radius:2px; }
  .log-item{
    font-size:.68rem; display:flex; gap:8px; padding:5px 8px;
    background:rgba(0,0,0,0.3); border-radius:4px;
    border-left:2px solid var(--accent2); color:var(--muted);
    animation:fadeIn .3s;
  }
  @keyframes fadeIn{ from{opacity:0;transform:translateY(-4px);} to{opacity:1;transform:none;} }
  .log-time{ color:var(--accent2); flex-shrink:0; }
  .warning-banner{
    display:none; position:fixed; top:0; left:0; right:0;
    background:var(--accent2); color:#fff; text-align:center;
    padding:12px; font-family:'Syne',sans-serif; font-weight:800;
    font-size:1.2rem; letter-spacing:2px; z-index:999;
    animation:slidein .3s;
  }
  @keyframes slidein{ from{transform:translateY(-100%);} to{transform:none;} }
  .voice-status{
    font-size:.65rem; color:var(--muted); margin-top:4px;
    letter-spacing:.5px;
  }
  .voice-status.ready{ color:var(--accent); }
  .voice-status.error{ color:var(--accent2); }
</style>
</head>
<body>

<div class="warning-banner" id="warningBanner">⚠️ KALPIT, STUDY! ⚠️</div>

<div class="app">
  <header>
    <div class="logo">STUDY<span>.</span>MONITOR</div>
    <div class="status-badge">
      <div class="dot" id="liveDot"></div>
      <span id="liveLabel">OFFLINE</span>
    </div>
  </header>

  <div class="grid">
    <!-- Main camera card -->
    <div class="card" id="mainCard">
      <p class="card-title">📷 Live Feed</p>
      <div class="cam-wrap">
        <video id="video" autoplay muted playsinline></video>
        <canvas id="canvas"></canvas>
        <div class="cam-overlay" id="camOverlay">
          <div class="icon">📷</div>
          <span>CAMERA OFFLINE</span>
        </div>
      </div>
      <div class="state-display" id="stateDisplay">
        <div class="state-icon" id="stateIcon">😴</div>
        <div>
          <div class="state-label idle" id="stateLabel">Waiting...</div>
          <div style="font-size:.68rem;color:var(--muted);margin-top:2px" id="stateDetail">Start monitoring to begin</div>
        </div>
      </div>
      <div class="controls">
        <button class="btn btn-primary" id="startBtn" onclick="startMonitor()">▶ Start Monitoring</button>
        <button class="btn btn-danger"  id="stopBtn"  onclick="stopMonitor()" style="display:none">■ Stop</button>
        <button class="btn btn-ghost"   onclick="triggerAlert(true)">🔊 Test Alert</button>
      </div>
    </div>

    <!-- Sidebar -->
    <div class="sidebar">

      <!-- Stats -->
      <div class="card">
        <p class="card-title">📊 Session Stats</p>
        <div class="stat-row">
          <span class="stat-label">Session Time</span>
          <span class="stat-value"        id="sessionTime">00:00:00</span>
        </div>
        <div class="stat-row">
          <span class="stat-label">Focused</span>
          <span class="stat-value green"  id="focusTime">00:00:00</span>
        </div>
        <div class="stat-row">
          <span class="stat-label">Distracted</span>
          <span class="stat-value red"    id="distTime">00:00:00</span>
        </div>
        <div class="stat-row">
          <span class="stat-label">Alerts Fired</span>
          <span class="stat-value yellow" id="alertCount">0</span>
        </div>
        <div class="progress-wrap">
          <div class="progress-label">
            <span>Focus Score</span>
            <span id="focusPct">—</span>
          </div>
          <div class="progress-bar">
            <div class="progress-fill" id="progressFill"></div>
          </div>
        </div>
      </div>

      <!-- Settings -->
      <div class="card">
        <p class="card-title">⚙️ Settings</p>
        <div class="setting-row">
          <span class="setting-label">Your Name</span>
          <input type="text" id="nameInput" value="Kalpit" placeholder="Name" maxlength="20">
        </div>
        <div class="setting-row">
          <span class="setting-label">Alert delay (sec)</span>
          <div style="display:flex;align-items:center;gap:6px">
            <input type="range" id="delaySlider" min="2" max="15" value="5" oninput="updateDelay(this.value)">
            <span id="delayVal" style="font-size:.72rem;color:var(--accent)">5s</span>
          </div>
        </div>
        <div class="setting-row">
          <span class="setting-label">Voice</span>
          <select id="voiceSelect"><option value="-1">Default (system)</option></select>
        </div>
        <div class="setting-row" style="flex-direction:column;align-items:flex-start;gap:2px">
          <div style="display:flex;justify-content:space-between;width:100%">
            <span class="setting-label">Voice Status</span>
            <span class="voice-status" id="voiceStatus">Loading...</span>
          </div>
        </div>
        <div class="setting-row">
          <span class="setting-label">Volume</span>
          <div style="display:flex;align-items:center;gap:6px">
            <input type="range" id="volSlider" min="0.1" max="1" step="0.1" value="1" oninput="updateVol(this.value)">
            <span id="volVal" style="font-size:.72rem;color:var(--accent)">100%</span>
          </div>
        </div>
        <div class="setting-row">
          <span class="setting-label">Detect looking away</span>
          <input type="checkbox" id="awayCheck" checked style="accent-color:var(--accent);width:16px;height:16px;cursor:pointer">
        </div>
      </div>

      <!-- Alert Log -->
      <div class="card">
        <p class="card-title">🔔 Alert Log</p>
        <ul class="log-list" id="logList">
          <li style="font-size:.68rem;color:var(--muted);padding:4px 8px">No alerts yet</li>
        </ul>
      </div>

    </div>
  </div>
</div>

<!-- MediaPipe -->
<script src="https://cdn.jsdelivr.net/npm/@mediapipe/camera_utils/camera_utils.js" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/@mediapipe/face_mesh/face_mesh.js" crossorigin="anonymous"></script>

<script>
// ── State ────────────────────────────────────────────────────────────────────
let isRunning    = false;
let mpCamera     = null;
let faceMesh     = null;
let alertDelay   = 5;
let volume       = 1.0;
let voices       = [];
let voicesReady  = false;

let sessionStart  = null;
let focusSec = 0, distSec = 0;
let statsInterval = null;
let alertCount    = 0;

let lastFaceTime    = Date.now();
let lastLookingTime = Date.now();
let currentState    = 'idle';
let lastAlertTime   = 0;
const ALERT_COOLDOWN = 8000;

// ── DOM ───────────────────────────────────────────────────────────────────────
const video         = document.getElementById('video');
const canvas        = document.getElementById('canvas');
const ctx           = canvas.getContext('2d');
const camOverlay    = document.getElementById('camOverlay');
const stateDisplay  = document.getElementById('stateDisplay');
const stateIcon     = document.getElementById('stateIcon');
const stateLabel    = document.getElementById('stateLabel');
const stateDetail   = document.getElementById('stateDetail');
const liveDot       = document.getElementById('liveDot');
const liveLabel     = document.getElementById('liveLabel');
const startBtn      = document.getElementById('startBtn');
const stopBtn       = document.getElementById('stopBtn');
const logList       = document.getElementById('logList');
const warningBanner = document.getElementById('warningBanner');
const voiceStatus   = document.getElementById('voiceStatus');

// ── Voice Loading (robust retry — works in GAS sandboxed iframes) ─────────────
function loadVoices() {
  const list = speechSynthesis.getVoices();
  if (!list || list.length === 0) return false;

  voices = list;
  const sel = document.getElementById('voiceSelect');
  sel.innerHTML = '<option value="-1">Default (system)</option>';

  let preferredIdx = -1;
  list.forEach((v, i) => {
    const opt = document.createElement('option');
    opt.value = i;
    opt.textContent = v.name.substring(0, 26);
    sel.appendChild(opt);
    // prefer a Google/natural English voice
    if (preferredIdx === -1 && v.lang.startsWith('en') && v.name.toLowerCase().includes('google')) preferredIdx = i;
  });
  // fallback: any English voice
  if (preferredIdx === -1) {
    preferredIdx = list.findIndex(v => v.lang.startsWith('en-'));
  }
  if (preferredIdx >= 0) sel.value = preferredIdx;

  voicesReady = true;
  voiceStatus.textContent = list.length + ' voices loaded ✓';
  voiceStatus.className = 'voice-status ready';
  return true;
}

// Retry loop — fires every 250ms up to 30 tries (~7.5s)
// Handles GAS iframe delay and browsers that load voices async
window.speechSynthesis.onvoiceschanged = loadVoices;
let voiceTries = 0;
const voiceTimer = setInterval(() => {
  if (loadVoices() || voiceTries++ > 30) {
    clearInterval(voiceTimer);
    if (!voicesReady) {
      voiceStatus.textContent = 'Using system default';
      voiceStatus.className = 'voice-status ready';
    }
  }
}, 250);

// ── Settings helpers ──────────────────────────────────────────────────────────
function updateDelay(v) {
  alertDelay = parseInt(v);
  document.getElementById('delayVal').textContent = v + 's';
}
function updateVol(v) {
  volume = parseFloat(v);
  document.getElementById('volVal').textContent = Math.round(v * 100) + '%';
}

// ── Alert ─────────────────────────────────────────────────────────────────────
function triggerAlert(test = false) {
  const now = Date.now();
  if (!test && now - lastAlertTime < ALERT_COOLDOWN) return;
  lastAlertTime = now;

  const name = document.getElementById('nameInput').value.trim() || 'Kalpit';
  const msgs = [
    `${name}, Study!`,
    `Hey ${name}! Focus on studying!`,
    `${name}, stop getting distracted! Study now!`,
    `Come on ${name}, back to your books!`,
    `${name}! Eyes on your work!`,
    `${name}, put the phone down and study!`,
  ];
  const msg = msgs[Math.floor(Math.random() * msgs.length)];

  // Cancel any ongoing speech first
  try { speechSynthesis.cancel(); } catch(e) {}

  const utter = new SpeechSynthesisUtterance(msg);
  utter.volume = volume;
  utter.rate   = 0.9;
  utter.pitch  = 1.15;

  // Set voice only if loaded; otherwise browser uses system default (still works!)
  const idx = parseInt(document.getElementById('voiceSelect').value);
  if (idx >= 0 && voices[idx]) utter.voice = voices[idx];

  // GAS iframe fix: wrap in tiny timeout so speech isn't blocked
  setTimeout(() => {
    try { speechSynthesis.speak(utter); } catch(e) { console.warn('Speech error:', e); }
  }, 50);

  // Banner
  warningBanner.textContent = `⚠️ ${msg.toUpperCase()} ⚠️`;
  warningBanner.style.display = 'block';
  setTimeout(() => warningBanner.style.display = 'none', 3500);

  // Log
  if (!test) {
    alertCount++;
    document.getElementById('alertCount').textContent = alertCount;
    const t = new Date().toTimeString().slice(0, 8);
    const li = document.createElement('li');
    li.className = 'log-item';
    li.innerHTML = `<span class="log-time">${t}</span><span>${msg}</span>`;
    if (!logList.querySelector('.log-item')) logList.innerHTML = '';
    logList.prepend(li);
    while (logList.children.length > 20) logList.removeChild(logList.lastChild);
  }
}

// ── State Machine ─────────────────────────────────────────────────────────────
function setState(newState, detail) {
  currentState = newState;
  stateDisplay.className = 'state-display ' +
    (newState === 'studying' ? 'studying' :
    (newState === 'distracted' || newState === 'no-face') ? 'distracted' : '');
  stateLabel.className = 'state-label ' +
    (newState === 'studying' ? 'studying' :
    (newState === 'distracted' || newState === 'no-face') ? 'distracted' : 'idle');

  const cfg = {
    studying:   { icon: '📖', label: 'STUDYING',       detail: 'Face detected & focused' },
    distracted: { icon: '😵', label: 'DISTRACTED!',    detail: 'Not looking at screen'   },
    'no-face':  { icon: '👻', label: 'AWAY FROM DESK', detail: 'No face detected'         },
    idle:       { icon: '😴', label: 'Waiting...',     detail: 'Start monitoring to begin'},
  }[newState] || { icon: '😴', label: 'Waiting...', detail: '' };

  stateIcon.textContent  = cfg.icon;
  stateLabel.textContent = cfg.label;
  stateDetail.textContent = detail || cfg.detail;
}

// ── Gaze / Attention Detection ────────────────────────────────────────────────
function isLookingAtScreen(lms) {
  if (!lms || lms.length === 0) return false;
  if (!document.getElementById('awayCheck').checked) return true;

  const nose     = lms[1];
  const chin     = lms[152];
  const forehead = lms[10];
  const leftEye  = lms[33];
  const rightEye = lms[263];
  if (!nose || !chin || !forehead) return true;

  const faceW    = Math.abs(rightEye.x - leftEye.x) + 0.001;
  const centerX  = (leftEye.x + rightEye.x) / 2;
  const noseDevX = Math.abs(nose.x - centerX) / faceW;   // side turn

  const eyeCY    = (leftEye.y + rightEye.y) / 2;
  const faceH    = Math.abs(forehead.y - chin.y) + 0.001;
  const noseDevY = (nose.y - eyeCY) / faceH;             // down tilt

  return noseDevX <= 0.28 && noseDevY <= 0.58;
}

// ── MediaPipe Results ─────────────────────────────────────────────────────────
function onResults(results) {
  canvas.width  = video.videoWidth  || 640;
  canvas.height = video.videoHeight || 480;
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  const now     = Date.now();
  const hasFace = results.multiFaceLandmarks && results.multiFaceLandmarks.length > 0;

  if (hasFace) {
    lastFaceTime = now;
    const lms = results.multiFaceLandmarks[0];

    // Draw mesh
    ctx.fillStyle = 'rgba(0,255,136,0.3)';
    for (let i = 0; i < lms.length; i += 3) {
      ctx.beginPath();
      ctx.arc(lms[i].x * canvas.width, lms[i].y * canvas.height, 1.2, 0, Math.PI * 2);
      ctx.fill();
    }
    // Highlight eyes
    ctx.fillStyle = 'rgba(0,200,255,0.7)';
    [33,160,158,133,153,144,263,387,385,362,380,373].forEach(i => {
      if (lms[i]) {
        ctx.beginPath();
        ctx.arc(lms[i].x * canvas.width, lms[i].y * canvas.height, 2.5, 0, Math.PI * 2);
        ctx.fill();
      }
    });

    const looking = isLookingAtScreen(lms);
    if (looking) {
      lastLookingTime = now;
      setState('studying');
    } else {
      const awayFor = (now - lastLookingTime) / 1000;
      if (awayFor >= alertDelay) {
        setState('distracted', `Looking away for ${awayFor.toFixed(0)}s`);
        triggerAlert();
      } else {
        setState('studying', `Possible distraction · alert in ${(alertDelay - awayFor).toFixed(0)}s`);
      }
    }
  } else {
    const awayFor = (now - lastFaceTime) / 1000;
    if (awayFor >= alertDelay) {
      setState('no-face', `Away for ${awayFor.toFixed(0)}s`);
      triggerAlert();
    } else if (awayFor > 1) {
      setState('no-face', `No face · alert in ${(alertDelay - awayFor).toFixed(0)}s`);
    }
  }
}

// ── Start Monitor ─────────────────────────────────────────────────────────────
async function startMonitor() {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({
      video: { width: 640, height: 480, facingMode: 'user' }
    });
    video.srcObject = stream;
    await new Promise(r => { video.onloadedmetadata = r; });

    faceMesh = new FaceMesh({
      locateFile: f => `https://cdn.jsdelivr.net/npm/@mediapipe/face_mesh/${f}`
    });
    faceMesh.setOptions({
      maxNumFaces: 1,
      refineLandmarks: true,
      minDetectionConfidence: 0.6,
      minTrackingConfidence: 0.6
    });
    faceMesh.onResults(onResults);

    mpCamera = new Camera(video, {
      onFrame: async () => { if (faceMesh) await faceMesh.send({ image: video }); },
      width: 640, height: 480
    });
    await mpCamera.start();

    isRunning = true;
    camOverlay.style.display = 'none';
    startBtn.style.display   = 'none';
    stopBtn.style.display    = 'inline-flex';
    liveDot.className        = 'dot on';
    liveLabel.textContent    = 'LIVE';
    document.getElementById('mainCard').classList.add('active');

    sessionStart = Date.now();
    focusSec = 0; distSec = 0; alertCount = 0;
    lastFaceTime = lastLookingTime = Date.now();
    document.getElementById('alertCount').textContent = '0';
    logList.innerHTML = '<li style="font-size:.68rem;color:var(--muted);padding:4px 8px">No alerts yet</li>';
    clearInterval(statsInterval);
    statsInterval = setInterval(updateStats, 1000);

  } catch(err) {
    alert(
      'Camera access denied or unavailable.\n\n' +
      'Please allow camera permission in your browser and try again.\n\n' +
      err.message
    );
  }
}

// ── Stop Monitor ──────────────────────────────────────────────────────────────
function stopMonitor() {
  isRunning = false;
  if (mpCamera)        { try { mpCamera.stop();    } catch(e){} mpCamera = null; }
  if (video.srcObject) { video.srcObject.getTracks().forEach(t => t.stop()); video.srcObject = null; }
  if (faceMesh)        { try { faceMesh.close();   } catch(e){} faceMesh = null; }
  clearInterval(statsInterval);
  try { speechSynthesis.cancel(); } catch(e) {}
  warningBanner.style.display = 'none';

  camOverlay.style.display = 'flex';
  startBtn.style.display   = 'inline-flex';
  stopBtn.style.display    = 'none';
  liveDot.className        = 'dot';
  liveLabel.textContent    = 'OFFLINE';
  document.getElementById('mainCard').classList.remove('active');
  setState('idle');
  ctx.clearRect(0, 0, canvas.width, canvas.height);
}

// ── Stats ─────────────────────────────────────────────────────────────────────
function updateStats() {
  if (!sessionStart) return;
  const elapsed = Math.floor((Date.now() - sessionStart) / 1000);
  if (currentState === 'studying')                              focusSec++;
  else if (currentState === 'distracted' || currentState === 'no-face') distSec++;
  document.getElementById('sessionTime').textContent = fmt(elapsed);
  document.getElementById('focusTime').textContent   = fmt(focusSec);
  document.getElementById('distTime').textContent    = fmt(distSec);
  const pct = elapsed > 0 ? Math.round(focusSec / elapsed * 100) : 0;
  document.getElementById('focusPct').textContent    = pct + '%';
  document.getElementById('progressFill').style.width = pct + '%';
}

function fmt(s) {
  return [Math.floor(s/3600), Math.floor((s%3600)/60), s%60]
    .map(x => String(x).padStart(2,'0')).join(':');
}
</script>
</body>
</html>
