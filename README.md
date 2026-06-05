<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Study Monitor — Kalpit</title>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;600;700&family=Bebas+Neue&family=Outfit:wght@300;400;600;700&display=swap" rel="stylesheet">
<style>
:root {
  --bg:#080810; --surf:#0f0f1a; --surf2:#14141f; --border:#1f1f35;
  --accent:#00e5ff; --green:#00ff88; --red:#ff2d55; --yellow:#ffd60a;
  --purple:#bf5af2; --text:#e2e2f0; --muted:#4a4a6a;
  --glow-c:0 0 24px rgba(0,229,255,0.35);
  --glow-g:0 0 24px rgba(0,255,136,0.35);
  --glow-r:0 0 30px rgba(255,45,85,0.45);
}
*{margin:0;padding:0;box-sizing:border-box;}
html,body{height:100%;}
body{background:var(--bg);color:var(--text);font-family:'Outfit',sans-serif;min-height:100vh;overflow-x:hidden;}
body::before{content:'';position:fixed;inset:0;background:radial-gradient(ellipse 60% 40% at 20% 20%,rgba(0,229,255,0.04) 0%,transparent 60%),radial-gradient(ellipse 50% 40% at 80% 80%,rgba(191,90,242,0.04) 0%,transparent 60%);pointer-events:none;z-index:0;}
body::after{content:'';position:fixed;inset:0;background-image:linear-gradient(rgba(0,229,255,0.025) 1px,transparent 1px),linear-gradient(90deg,rgba(0,229,255,0.025) 1px,transparent 1px);background-size:48px 48px;pointer-events:none;z-index:0;}

/* ── SETUP ── */
#setupScreen{position:fixed;inset:0;z-index:100;display:flex;align-items:center;justify-content:center;background:var(--bg);padding:20px;}
.setup-box{background:var(--surf);border:1px solid var(--border);border-radius:20px;padding:40px 36px;max-width:540px;width:100%;position:relative;overflow:hidden;}
.setup-box::before{content:'';position:absolute;top:0;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,var(--accent),var(--purple),transparent);}
.setup-title{font-family:'Bebas Neue',sans-serif;font-size:2.6rem;letter-spacing:2px;color:var(--accent);margin-bottom:6px;line-height:1;}
.setup-sub{font-size:.82rem;color:var(--muted);margin-bottom:28px;font-family:'JetBrains Mono',monospace;}
.setup-label{font-size:.68rem;letter-spacing:2px;text-transform:uppercase;color:var(--muted);margin-bottom:12px;font-family:'JetBrains Mono',monospace;}
.cam-options{display:grid;grid-template-columns:1fr 1fr;gap:14px;margin-bottom:24px;}
.cam-option{border:2px solid var(--border);border-radius:14px;padding:20px 16px;cursor:pointer;transition:all .25s;text-align:center;position:relative;background:var(--surf2);}
.cam-option:hover{border-color:var(--accent);background:rgba(0,229,255,0.04);}
.cam-option.selected{border-color:var(--accent);background:rgba(0,229,255,0.07);box-shadow:var(--glow-c);}
.badge{position:absolute;top:10px;right:10px;background:var(--green);color:#000;font-size:.52rem;font-weight:700;letter-spacing:1px;padding:2px 7px;border-radius:99px;font-family:'JetBrains Mono',monospace;}
.cam-icon{font-size:2.2rem;margin-bottom:10px;}
.cam-name{font-weight:700;font-size:.95rem;margin-bottom:4px;}
.cam-desc{font-size:.7rem;color:var(--muted);line-height:1.5;}
.name-row{display:flex;gap:12px;align-items:center;margin-bottom:24px;}
.name-row label{font-size:.72rem;color:var(--muted);white-space:nowrap;font-family:'JetBrains Mono',monospace;}
.name-input{flex:1;background:var(--surf2);border:1px solid var(--border);color:var(--text);font-family:'JetBrains Mono',monospace;font-size:.85rem;padding:8px 14px;border-radius:8px;outline:none;transition:border-color .2s;}
.name-input:focus{border-color:var(--accent);}

/* Free badge strip */
.free-strip{background:rgba(0,255,136,0.08);border:1px solid rgba(0,255,136,0.2);border-radius:8px;padding:10px 14px;margin-bottom:22px;font-size:.72rem;color:var(--green);font-family:'JetBrains Mono',monospace;display:flex;align-items:center;gap:8px;}

.btn-launch{width:100%;padding:14px;background:var(--accent);color:#000;font-family:'Bebas Neue',sans-serif;font-size:1.3rem;letter-spacing:3px;border:none;border-radius:10px;cursor:pointer;box-shadow:var(--glow-c);transition:all .2s;}
.btn-launch:hover{filter:brightness(1.1);transform:translateY(-2px);}

/* Loading overlay */
#loadingOverlay{position:fixed;inset:0;z-index:200;background:var(--bg);display:none;flex-direction:column;align-items:center;justify-content:center;gap:20px;}
.load-title{font-family:'Bebas Neue',sans-serif;font-size:2rem;letter-spacing:3px;color:var(--accent);}
.load-bar-wrap{width:280px;height:4px;background:var(--border);border-radius:99px;overflow:hidden;}
.load-bar{height:100%;background:linear-gradient(90deg,var(--accent),var(--green));border-radius:99px;width:0%;transition:width .4s;}
.load-msg{font-size:.75rem;color:var(--muted);font-family:'JetBrains Mono',monospace;text-align:center;}

/* ── MAIN APP ── */
#mainApp{display:none;position:relative;z-index:1;}
.app{max-width:1100px;margin:0 auto;padding:20px 18px 40px;}
header{display:flex;align-items:center;justify-content:space-between;margin-bottom:20px;padding-bottom:14px;border-bottom:1px solid var(--border);}
.logo{font-family:'Bebas Neue',sans-serif;font-size:1.6rem;letter-spacing:3px;color:var(--accent);}
.logo em{color:var(--muted);font-style:normal;font-size:.9rem;letter-spacing:1px;margin-left:10px;}
.hdr-right{display:flex;align-items:center;gap:12px;}
.mode-pill{display:flex;align-items:center;gap:6px;background:var(--surf2);border:1px solid var(--border);border-radius:99px;padding:4px 12px;font-size:.65rem;letter-spacing:1.5px;text-transform:uppercase;font-family:'JetBrains Mono',monospace;color:var(--muted);}
.dot{width:7px;height:7px;border-radius:50%;background:var(--muted);transition:all .3s;}
.dot.on{background:var(--green);box-shadow:var(--glow-g);animation:blink 1.5s infinite;}
.dot.red{background:var(--red);box-shadow:var(--glow-r);animation:blink .5s infinite;}
@keyframes blink{0%,100%{opacity:1;}50%{opacity:.4;}}

.grid{display:grid;grid-template-columns:1fr 300px;gap:16px;}
@media(max-width:750px){.grid{grid-template-columns:1fr;}}
.card{background:var(--surf);border:1px solid var(--border);border-radius:14px;padding:18px;position:relative;overflow:hidden;}
.card-shine{position:absolute;top:0;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,var(--accent),transparent);opacity:0;transition:opacity .4s;}
.card.active .card-shine{opacity:1;}

/* Camera */
.cam-wrap{position:relative;width:100%;aspect-ratio:16/9;background:#04040c;border-radius:10px;overflow:hidden;margin-bottom:14px;}
#video{width:100%;height:100%;object-fit:cover;display:block;border-radius:10px;}
#video.mirror{transform:scaleX(-1);}
#canvas{position:absolute;inset:0;width:100%;height:100%;pointer-events:none;}
#canvas.mirror{transform:scaleX(-1);}
.cam-overlay{position:absolute;inset:0;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:12px;background:rgba(4,4,12,0.92);border-radius:10px;font-family:'JetBrains Mono',monospace;font-size:.78rem;color:var(--muted);letter-spacing:1px;}
.cam-overlay-icon{font-size:2.8rem;}

/* Detection box */
.det-box{position:absolute;bottom:10px;left:10px;right:10px;background:rgba(8,8,16,0.88);border:1px solid var(--border);border-radius:8px;padding:9px 13px;font-family:'JetBrains Mono',monospace;font-size:.68rem;display:none;backdrop-filter:blur(8px);}
.det-box.show{display:block;}
.det-label{color:var(--accent);letter-spacing:1px;font-size:.6rem;margin-bottom:4px;text-transform:uppercase;}
.det-items{display:flex;flex-wrap:wrap;gap:5px;margin-top:4px;}
.det-tag{padding:2px 8px;border-radius:99px;font-size:.6rem;border:1px solid;}
.det-tag.good{background:rgba(0,255,136,0.1);border-color:var(--green);color:var(--green);}
.det-tag.bad {background:rgba(255,45,85,0.1); border-color:var(--red);  color:var(--red);}
.det-tag.warn{background:rgba(255,214,10,0.1);border-color:var(--yellow);color:var(--yellow);}
.det-tag.neu {background:rgba(0,229,255,0.08);border-color:var(--accent);color:var(--accent);}

/* Scan line */
.scan-line{position:absolute;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,var(--accent),transparent);pointer-events:none;animation:scanMove 2.5s linear infinite;}
@keyframes scanMove{0%{top:0;opacity:0;}10%{opacity:.9;}90%{opacity:.9;}100%{top:100%;opacity:0;}}

/* State */
.state-display{display:flex;align-items:center;gap:14px;padding:12px 16px;border-radius:10px;border:1px solid var(--border);background:rgba(0,0,0,0.3);transition:all .4s;margin-bottom:14px;}
.state-display.studying {border-color:var(--green); background:rgba(0,255,136,0.06); box-shadow:var(--glow-g);}
.state-display.bad      {border-color:var(--red);   background:rgba(255,45,85,0.07); box-shadow:var(--glow-r); animation:shk .35s;}
.state-display.warning  {border-color:var(--yellow);background:rgba(255,214,10,0.06);box-shadow:0 0 20px rgba(255,214,10,0.3);}
@keyframes shk{0%,100%{transform:translateX(0);}25%{transform:translateX(-5px);}75%{transform:translateX(5px);}}
.state-icon{font-size:1.8rem;flex-shrink:0;}
.state-name{font-family:'Bebas Neue',sans-serif;font-size:1.25rem;letter-spacing:2px;}
.state-name.ok  {color:var(--green);}
.state-name.bad {color:var(--red);}
.state-name.warn{color:var(--yellow);}
.state-name.idle{color:var(--muted);}
.state-sub{font-size:.67rem;color:var(--muted);margin-top:2px;font-family:'JetBrains Mono',monospace;}

.controls{display:flex;gap:10px;flex-wrap:wrap;}
.btn{display:inline-flex;align-items:center;gap:7px;padding:9px 18px;border-radius:8px;font-family:'JetBrains Mono',monospace;font-size:.72rem;font-weight:600;letter-spacing:.5px;cursor:pointer;border:1px solid;transition:all .2s;}
.btn-go  {background:var(--green);color:#000;border-color:var(--green);box-shadow:var(--glow-g);}
.btn-go:hover{filter:brightness(1.1);transform:translateY(-1px);}
.btn-stop{background:transparent;color:var(--red);border-color:var(--red);}
.btn-stop:hover{background:rgba(255,45,85,0.1);}
.btn-ghost{background:transparent;color:var(--muted);border-color:var(--border);font-size:.68rem;}
.btn-ghost:hover{color:var(--text);border-color:var(--muted);}
.btn-sm{padding:6px 12px;font-size:.65rem;}

.sidebar{display:flex;flex-direction:column;gap:14px;}
.card-title{font-size:.62rem;letter-spacing:2.5px;text-transform:uppercase;color:var(--muted);margin-bottom:14px;font-family:'JetBrains Mono',monospace;}
.stat-row{display:flex;justify-content:space-between;align-items:center;padding:7px 0;border-bottom:1px solid var(--border);}
.stat-row:last-child{border-bottom:none;}
.sl{font-size:.68rem;color:var(--muted);font-family:'JetBrains Mono',monospace;}
.sv{font-size:.88rem;font-weight:700;font-family:'JetBrains Mono',monospace;}
.sv.g{color:var(--green);}.sv.r{color:var(--red);}.sv.y{color:var(--yellow);}.sv.c{color:var(--accent);}
.prog-wrap{margin-top:12px;}
.prog-lbl{display:flex;justify-content:space-between;font-size:.63rem;color:var(--muted);margin-bottom:5px;font-family:'JetBrains Mono',monospace;}
.prog-bar{height:5px;background:var(--border);border-radius:99px;overflow:hidden;}
.prog-fill{height:100%;background:linear-gradient(90deg,var(--green),var(--accent));border-radius:99px;transition:width 1s ease;width:0%;}
.srow{display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid var(--border);font-size:.72rem;}
.srow:last-child{border-bottom:none;}
.slbl{color:var(--muted);font-family:'JetBrains Mono',monospace;font-size:.67rem;}
input[type=range]{-webkit-appearance:none;width:75px;height:3px;background:var(--border);border-radius:2px;outline:none;}
input[type=range]::-webkit-slider-thumb{-webkit-appearance:none;width:13px;height:13px;background:var(--accent);border-radius:50%;cursor:pointer;}
select{background:var(--bg);border:1px solid var(--border);color:var(--text);font-family:'JetBrains Mono',monospace;font-size:.65rem;padding:3px 6px;border-radius:4px;outline:none;max-width:120px;}
select:focus{border-color:var(--accent);}
.log-list{list-style:none;max-height:160px;overflow-y:auto;display:flex;flex-direction:column;gap:5px;}
.log-list::-webkit-scrollbar{width:2px;}
.log-list::-webkit-scrollbar-thumb{background:var(--border);}
.log-item{font-size:.63rem;display:flex;gap:8px;padding:5px 8px;background:rgba(0,0,0,0.3);border-radius:5px;border-left:2px solid var(--red);color:var(--muted);animation:fadeup .3s;font-family:'JetBrains Mono',monospace;}
.log-item.ph{border-left-color:var(--yellow);}
@keyframes fadeup{from{opacity:0;transform:translateY(-4px);}to{opacity:1;transform:none;}}
.lt{color:var(--red);flex-shrink:0;}.log-item.ph .lt{color:var(--yellow);}

/* Warning banner */
.warn-banner{display:none;position:fixed;top:0;left:0;right:0;padding:13px;text-align:center;z-index:999;font-family:'Bebas Neue',sans-serif;font-size:1.3rem;letter-spacing:3px;animation:slidein .3s;}
.warn-banner.study{background:var(--red);color:#fff;}
.warn-banner.phone{background:var(--yellow);color:#000;}
@keyframes slidein{from{transform:translateY(-100%);}to{transform:none;}}

.mode-chip{display:inline-flex;align-items:center;gap:6px;background:rgba(0,229,255,0.07);border:1px solid rgba(0,229,255,0.18);border-radius:6px;padding:5px 10px;font-size:.63rem;letter-spacing:1px;color:var(--accent);font-family:'JetBrains Mono',monospace;margin-bottom:14px;}
</style>
</head>
<body>

<!-- Loading overlay -->
<div id="loadingOverlay">
  <div class="load-title">LOADING AI MODEL</div>
  <div class="load-bar-wrap"><div class="load-bar" id="loadBar"></div></div>
  <div class="load-msg" id="loadMsg">Downloading object detection model...</div>
</div>

<!-- Setup screen -->
<div id="setupScreen">
  <div class="setup-box">
    <div class="setup-title">STUDY.MONITOR</div>
    <div class="setup-sub">// 100% free · no API key · runs in browser</div>

    <div class="free-strip">✅ 100% FREE — Uses TensorFlow.js locally. No internet API. No key needed.</div>

    <div class="setup-label">Choose your webcam setup</div>
    <div class="cam-options">
      <div class="cam-option" id="optFront" onclick="selectMode('front')">
        <div class="cam-icon">🖥️</div>
        <div class="cam-name">Front Camera</div>
        <div class="cam-desc">Fixed above monitor. Tracks face position and gaze direction.</div>
      </div>
      <div class="cam-option selected" id="optSide" onclick="selectMode('side')">
        <div class="badge">PREFERRED</div>
        <div class="cam-icon">📷</div>
        <div class="cam-name">Side / Portable Cam</div>
        <div class="cam-desc">Place beside you. Detects phone, books, laptop on your desk using AI object detection.</div>
      </div>
    </div>

    <div class="name-row">
      <label>YOUR NAME</label>
      <input class="name-input" id="setupName" type="text" value="Kalpit" maxlength="20">
    </div>

    <button class="btn-launch" onclick="launchApp()">LAUNCH MONITOR →</button>
  </div>
</div>

<!-- Main App -->
<div id="mainApp">
  <div class="warn-banner" id="warnBanner"></div>
  <div class="app">
    <header>
      <div class="logo">STUDY.MONITOR <em id="hdrMode">// SIDE CAM</em></div>
      <div class="hdr-right">
        <div class="mode-pill"><div class="dot" id="liveDot"></div><span id="liveLabel">OFFLINE</span></div>
        <button class="btn btn-ghost btn-sm" onclick="goBack()">⟵ Setup</button>
      </div>
    </header>

    <div class="grid">
      <!-- Camera card -->
      <div class="card" id="mainCard">
        <div class="card-shine"></div>
        <div class="mode-chip" id="modeChip">📷 SIDE CAM — OBJECT DETECTION</div>
        <p class="card-title">📹 Live Feed</p>
        <div class="cam-wrap">
          <video id="video" autoplay muted playsinline></video>
          <canvas id="canvas"></canvas>
          <div class="cam-overlay" id="camOverlay">
            <div class="cam-overlay-icon">📷</div>
            <span>CAMERA OFFLINE</span>
          </div>
          <div class="scan-line" id="scanLine" style="display:none"></div>
          <div class="det-box" id="detBox">
            <div class="det-label">🔍 Detected Objects</div>
            <div class="det-items" id="detItems">—</div>
          </div>
        </div>

        <div class="state-display" id="stateDisplay">
          <div class="state-icon" id="stateIcon">😴</div>
          <div style="flex:1">
            <div class="state-name idle" id="stateName">Waiting...</div>
            <div class="state-sub" id="stateSub">Start monitoring to begin</div>
          </div>
        </div>

        <div class="controls">
          <button class="btn btn-go"   id="startBtn" onclick="startMonitor()">▶ Start</button>
          <button class="btn btn-stop" id="stopBtn"  onclick="stopMonitor()" style="display:none">■ Stop</button>
          <button class="btn btn-ghost" onclick="triggerAlert('study',true)">🔊 Test Alert</button>
          <button class="btn btn-ghost" onclick="triggerAlert('phone',true)">📵 Test Phone</button>
        </div>
      </div>

      <!-- Sidebar -->
      <div class="sidebar">
        <div class="card">
          <p class="card-title">📊 Session Stats</p>
          <div class="stat-row"><span class="sl">Session</span> <span class="sv c" id="sesTime">00:00:00</span></div>
          <div class="stat-row"><span class="sl">Focused</span> <span class="sv g" id="focTime">00:00:00</span></div>
          <div class="stat-row"><span class="sl">Distracted</span><span class="sv r" id="disTime">00:00:00</span></div>
          <div class="stat-row"><span class="sl">Phone alerts</span><span class="sv y" id="phoneCount">0</span></div>
          <div class="stat-row"><span class="sl">Study alerts</span><span class="sv r" id="studyCount">0</span></div>
          <div class="prog-wrap">
            <div class="prog-lbl"><span>Focus Score</span><span id="focPct">—</span></div>
            <div class="prog-bar"><div class="prog-fill" id="progFill"></div></div>
          </div>
        </div>

        <div class="card">
          <p class="card-title">⚙️ Settings</p>
          <div class="srow">
            <span class="slbl">Name</span>
            <input type="text" id="nameInput" value="Kalpit" maxlength="20"
              style="background:var(--bg);border:1px solid var(--border);color:var(--text);font-family:'JetBrains Mono',monospace;font-size:.7rem;padding:4px 8px;border-radius:4px;outline:none;max-width:110px;">
          </div>
          <div class="srow">
            <span class="slbl">Alert delay (s)</span>
            <div style="display:flex;align-items:center;gap:6px">
              <input type="range" id="delaySlider" min="3" max="20" value="6" oninput="updateDelay(this.value)">
              <span id="delayVal" style="font-size:.7rem;color:var(--accent);font-family:'JetBrains Mono',monospace;">6s</span>
            </div>
          </div>
          <div class="srow" id="confRow">
            <span class="slbl">Detection confidence</span>
            <div style="display:flex;align-items:center;gap:6px">
              <input type="range" id="confSlider" min="30" max="85" value="55" oninput="updateConf(this.value)">
              <span id="confVal" style="font-size:.7rem;color:var(--accent);font-family:'JetBrains Mono',monospace;">55%</span>
            </div>
          </div>
          <div class="srow" id="gazeRow">
            <span class="slbl">Gaze detection</span>
            <input type="checkbox" id="gazeCheck" checked style="accent-color:var(--accent);width:15px;height:15px;cursor:pointer">
          </div>
          <div class="srow">
            <span class="slbl">Voice</span>
            <select id="voiceSelect"><option value="-1">Default</option></select>
          </div>
          <div class="srow">
            <span class="slbl">Volume</span>
            <div style="display:flex;align-items:center;gap:6px">
              <input type="range" id="volSlider" min="0.1" max="1" step="0.1" value="1" oninput="updateVol(this.value)">
              <span id="volVal" style="font-size:.7rem;color:var(--accent);font-family:'JetBrains Mono',monospace;">100%</span>
            </div>
          </div>
          <div class="srow">
            <span class="slbl">Voice status</span>
            <span style="font-size:.63rem;color:var(--muted);font-family:'JetBrains Mono',monospace;" id="voiceStatus">Loading...</span>
          </div>
        </div>

        <div class="card">
          <p class="card-title">🔔 Alert Log</p>
          <ul class="log-list" id="logList">
            <li style="font-size:.63rem;color:var(--muted);padding:4px 8px;font-family:'JetBrains Mono',monospace;">No alerts yet</li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- TensorFlow.js + COCO-SSD -->
<script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@4.15.0/dist/tf.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@tensorflow-models/coco-ssd@2.2.3/dist/coco-ssd.min.js"></script>
<!-- MediaPipe (front cam) -->
<script src="https://cdn.jsdelivr.net/npm/@mediapipe/camera_utils/camera_utils.js" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/@mediapipe/face_mesh/face_mesh.js" crossorigin="anonymous"></script>

<script>
// ═══════════════════════════════════════════
// GLOBALS
// ═══════════════════════════════════════════
let camMode      = 'side';
let userName     = 'Kalpit';
let isRunning    = false;
let cocoModel    = null;
let mpCamera     = null;
let faceMesh     = null;
let alertDelay   = 6;
let confThresh   = 0.55;
let volume       = 1.0;
let voices       = [];
let voicesReady  = false;

let sessionStart = null;
let focusSec = 0, disSec = 0;
let statsTimer   = null;
let phoneAlerts  = 0, studyAlerts = 0;

let lastFaceTime    = Date.now();
let lastLookTime    = Date.now();
let currentState    = 'idle';
let lastAlertTime   = 0;
const COOLDOWN      = 9000;

// Side cam detection state
let detectionLoop   = null;
let phoneSeenSince  = null;  // timestamp when phone first detected
let noPersonSince   = null;
let distractedSince = null;

// ═══════════════════════════════════════════
// SETUP
// ═══════════════════════════════════════════
function selectMode(m) {
  camMode = m;
  document.getElementById('optFront').classList.toggle('selected', m==='front');
  document.getElementById('optSide').classList.toggle('selected',  m==='side');
}

async function launchApp() {
  userName = document.getElementById('setupName').value.trim() || 'Kalpit';
  document.getElementById('nameInput').value = userName;
  document.getElementById('setupScreen').style.display = 'none';

  // Show loading, preload COCO-SSD
  const lo = document.getElementById('loadingOverlay');
  lo.style.display = 'flex';
  setLoadProgress(10, 'Loading TensorFlow.js...');

  try {
    await tf.ready();
    setLoadProgress(35, 'Downloading COCO-SSD object detection model...');
    cocoModel = await cocoSsd.load({ base: 'lite_mobilenet_v2' });
    setLoadProgress(100, 'Model ready!');
    await new Promise(r => setTimeout(r, 600));
  } catch(e) {
    console.warn('COCO-SSD load failed:', e);
    setLoadProgress(100, 'Model load failed — front cam only will work');
    await new Promise(r => setTimeout(r, 1000));
  }

  lo.style.display = 'none';
  document.getElementById('mainApp').style.display = 'block';

  // Configure UI for mode
  if (camMode === 'front') {
    document.getElementById('hdrMode').textContent  = '// FRONT CAM';
    document.getElementById('modeChip').textContent = '🖥️  FRONT CAM — FACE & GAZE TRACKING';
    document.getElementById('confRow').style.display = 'none';
    document.getElementById('gazeRow').style.display = 'flex';
    document.getElementById('video').classList.add('mirror');
    document.getElementById('canvas').classList.add('mirror');
  } else {
    document.getElementById('hdrMode').textContent  = '// SIDE CAM';
    document.getElementById('modeChip').textContent = '📷 SIDE CAM — OBJECT DETECTION (FREE)';
    document.getElementById('confRow').style.display = 'flex';
    document.getElementById('gazeRow').style.display = 'none';
  }
}

function setLoadProgress(pct, msg) {
  document.getElementById('loadBar').style.width = pct+'%';
  document.getElementById('loadMsg').textContent  = msg;
}

function goBack() {
  stopMonitor();
  document.getElementById('mainApp').style.display    = 'none';
  document.getElementById('setupScreen').style.display = 'flex';
}

// ═══════════════════════════════════════════
// VOICES
// ═══════════════════════════════════════════
function loadVoices() {
  const list = speechSynthesis.getVoices();
  if (!list || list.length === 0) return false;
  voices = list;
  const sel = document.getElementById('voiceSelect');
  sel.innerHTML = '<option value="-1">Default (system)</option>';
  let pref = -1;
  list.forEach((v,i) => {
    const o = document.createElement('option');
    o.value = i; o.textContent = v.name.substring(0,24);
    sel.appendChild(o);
    if (pref===-1 && v.lang.startsWith('en') && v.name.toLowerCase().includes('google')) pref=i;
  });
  if (pref===-1) pref = list.findIndex(v=>v.lang.startsWith('en-'));
  if (pref>=0) sel.value = pref;
  voicesReady = true;
  document.getElementById('voiceStatus').textContent = list.length+' voices ✓';
  document.getElementById('voiceStatus').style.color = 'var(--green)';
  return true;
}
window.speechSynthesis.onvoiceschanged = loadVoices;
let vt=0; const vtimer=setInterval(()=>{ if(loadVoices()||vt++>30){ clearInterval(vtimer); if(!voicesReady){document.getElementById('voiceStatus').textContent='System default';document.getElementById('voiceStatus').style.color='var(--accent)';}}},250);

function updateDelay(v){ alertDelay=parseInt(v); document.getElementById('delayVal').textContent=v+'s'; }
function updateConf(v) { confThresh=parseInt(v)/100; document.getElementById('confVal').textContent=v+'%'; }
function updateVol(v)  { volume=parseFloat(v); document.getElementById('volVal').textContent=Math.round(v*100)+'%'; }

// ═══════════════════════════════════════════
// ALERTS
// ═══════════════════════════════════════════
function triggerAlert(type='study', test=false) {
  const now = Date.now();
  if (!test && now-lastAlertTime < COOLDOWN) return;
  lastAlertTime = now;
  const n = document.getElementById('nameInput').value.trim() || userName;

  const msgs = type==='phone' ? [
    `${n}, stop using your smartphone! Study!`,
    `Put the phone down, ${n}! Focus!`,
    `${n}! No phone during study time!`,
    `${n}, that phone can wait! Study now!`,
  ] : [
    `${n}, Study!`,
    `Hey ${n}! Focus on studying!`,
    `${n}, stop getting distracted! Study now!`,
    `Come on ${n}, back to your books!`,
    `${n}! Eyes on your work!`,
  ];
  const msg = msgs[Math.floor(Math.random()*msgs.length)];

  try { speechSynthesis.cancel(); } catch(e){}
  const u = new SpeechSynthesisUtterance(msg);
  const idx = parseInt(document.getElementById('voiceSelect').value);
  if (idx>=0 && voices[idx]) u.voice = voices[idx];
  u.volume=volume; u.rate=0.88; u.pitch=1.2;
  setTimeout(()=>{ try{speechSynthesis.speak(u);}catch(e){} },50);

  const banner = document.getElementById('warnBanner');
  banner.textContent = type==='phone' ? `📵 ${msg.toUpperCase()}` : `⚠️ ${msg.toUpperCase()} ⚠️`;
  banner.className   = `warn-banner ${type==='phone'?'phone':'study'}`;
  banner.style.display='block';
  setTimeout(()=>banner.style.display='none',4000);

  if (!test) {
    if(type==='phone'){phoneAlerts++;document.getElementById('phoneCount').textContent=phoneAlerts;}
    else             {studyAlerts++;document.getElementById('studyCount').textContent=studyAlerts;}
    const t=new Date().toTimeString().slice(0,8);
    const li=document.createElement('li');
    li.className=`log-item${type==='phone'?' ph':''}`;
    li.innerHTML=`<span class="lt">${t}</span><span>${msg}</span>`;
    const ll=document.getElementById('logList');
    if(!ll.querySelector('.log-item'))ll.innerHTML='';
    ll.prepend(li);
    while(ll.children.length>25)ll.removeChild(ll.lastChild);
  }
}

// ═══════════════════════════════════════════
// STATE
// ═══════════════════════════════════════════
function setState(s, sub) {
  currentState = s;
  const sd=document.getElementById('stateDisplay');
  const sn=document.getElementById('stateName');
  sd.className='state-display '+(s==='studying'?'studying':s==='distracted'||s==='phone'||s==='no-face'?'bad':s==='warning'?'warning':'');
  sn.className='state-name '+(s==='studying'?'ok':s==='distracted'||s==='phone'||s==='no-face'?'bad':s==='warning'?'warn':'idle');
  const cfg={
    studying:  {icon:'📖',name:'STUDYING',        sub:'Focused on work'},
    phone:     {icon:'📵',name:'PHONE DETECTED!', sub:'Put the phone down!'},
    distracted:{icon:'😵',name:'DISTRACTED!',     sub:'Not studying'},
    warning:   {icon:'⚠️',name:'WARNING',         sub:'Possible distraction'},
    'no-face': {icon:'👻',name:'AWAY FROM DESK',  sub:'No person detected'},
    idle:      {icon:'😴',name:'Waiting...',      sub:'Start monitoring to begin'},
  }[s]||{icon:'😴',name:'Waiting...',sub:''};
  document.getElementById('stateIcon').textContent=cfg.icon;
  sn.textContent=cfg.name;
  document.getElementById('stateSub').textContent=sub||cfg.sub;
}

// ═══════════════════════════════════════════
// SIDE CAM — COCO-SSD OBJECT DETECTION
// ═══════════════════════════════════════════
// What COCO-SSD detects that matters for us:
// GOOD (studying): book, laptop, keyboard, mouse, scissors, pen(not labeled but person+book=study)
// BAD  (phone):    cell phone
// BAD  (tablet):   remote_control (tablets sometimes), book (if held up like tablet)
// NEUTRAL:         person, chair, cup, backpack

const STUDY_OBJECTS  = new Set(['book','laptop','keyboard','mouse','scissors','cup']);
const PHONE_OBJECTS  = new Set(['cell phone']);
const TABLET_OBJECTS = new Set(['remote']);  // tablets often trigger 'remote' in COCO

function renderDetections(predictions, cv) {
  const c = cv.getContext('2d');
  predictions.forEach(p => {
    const [x,y,w,h] = p.bbox;
    const isPhone  = PHONE_OBJECTS.has(p.class);
    const isStudy  = STUDY_OBJECTS.has(p.class);
    const isTbl    = TABLET_OBJECTS.has(p.class);
    c.strokeStyle = isPhone ? '#ff2d55' : isTbl ? '#ffd60a' : isStudy ? '#00ff88' : 'rgba(0,229,255,0.5)';
    c.lineWidth   = isPhone||isTbl ? 3 : 2;
    c.strokeRect(x,y,w,h);
    c.fillStyle = isPhone ? 'rgba(255,45,85,0.15)' : isTbl ? 'rgba(255,214,10,0.1)' : isStudy ? 'rgba(0,255,136,0.1)' : 'transparent';
    c.fillRect(x,y,w,h);
    // Label
    const label = `${p.class} ${Math.round(p.score*100)}%`;
    c.fillStyle = isPhone ? '#ff2d55' : isTbl ? '#ffd60a' : isStudy ? '#00ff88' : 'var(--accent,#00e5ff)';
    c.font = 'bold 13px JetBrains Mono, monospace';
    c.fillText(label, x+4, y>18?y-6:y+16);
  });
}

function updateDetBox(predictions) {
  const box   = document.getElementById('detBox');
  const items = document.getElementById('detItems');
  box.classList.add('show');
  if (!predictions.length) {
    items.innerHTML = '<span style="color:var(--muted);font-size:.62rem">Nothing detected</span>';
    return;
  }
  items.innerHTML = predictions.map(p => {
    const cls = PHONE_OBJECTS.has(p.class)?'bad':STUDY_OBJECTS.has(p.class)?'good':TABLET_OBJECTS.has(p.class)?'warn':'neu';
    return `<span class="det-tag ${cls}">${p.class} ${Math.round(p.score*100)}%</span>`;
  }).join('');
}

async function runSideDetection() {
  if (!isRunning || !cocoModel) return;
  const vd = document.getElementById('video');
  const cv = document.getElementById('canvas');
  cv.width  = vd.videoWidth  || 640;
  cv.height = vd.videoHeight || 480;
  const c   = cv.getContext('2d');
  c.clearRect(0,0,cv.width,cv.height);

  let predictions = [];
  try {
    predictions = await cocoModel.detect(vd, undefined, confThresh);
  } catch(e) { return; }

  renderDetections(predictions, cv);
  updateDetBox(predictions);

  const now        = Date.now();
  const hasPerson  = predictions.some(p=>p.class==='person');
  const hasPhone   = predictions.some(p=>PHONE_OBJECTS.has(p.class));
  const hasTablPC  = predictions.some(p=>TABLET_OBJECTS.has(p.class));
  const hasStudy   = predictions.some(p=>STUDY_OBJECTS.has(p.class));

  // ── Decision logic ──────────────────────────────
  if (hasPhone) {
    // Phone detected → immediate warning state, alert after delay
    if (!phoneSeenSince) phoneSeenSince = now;
    noPersonSince   = null;
    distractedSince = null;
    const dur = (now-phoneSeenSince)/1000;
    if (dur >= alertDelay) {
      setState('phone', `Phone detected for ${dur.toFixed(0)}s`);
      triggerAlert('phone');
    } else {
      setState('warning', `📵 Phone seen · alert in ${(alertDelay-dur).toFixed(0)}s`);
    }
  } else if (hasTablPC && !hasStudy) {
    // Tablet with no study material
    if (!phoneSeenSince) phoneSeenSince = now;
    noPersonSince   = null;
    distractedSince = null;
    const dur = (now-phoneSeenSince)/1000;
    if (dur >= alertDelay) {
      setState('phone', `Tablet/device detected for ${dur.toFixed(0)}s`);
      triggerAlert('phone');
    } else {
      setState('warning', `📱 Device detected · alert in ${(alertDelay-dur).toFixed(0)}s`);
    }
  } else if (!hasPerson) {
    // No person at desk
    phoneSeenSince  = null;
    distractedSince = null;
    if (!noPersonSince) noPersonSince = now;
    const dur = (now-noPersonSince)/1000;
    if (dur >= alertDelay) {
      setState('no-face', `Away from desk ${dur.toFixed(0)}s`);
      triggerAlert('study');
    } else if (dur > 2) {
      setState('no-face', `No one at desk · alert in ${(alertDelay-dur).toFixed(0)}s`);
    }
  } else if (hasStudy || (hasPerson && !hasPhone && !hasTablPC)) {
    // Person present + study objects = studying
    // OR person present with no bad objects = benefit of doubt = studying
    phoneSeenSince  = null;
    noPersonSince   = null;
    distractedSince = null;
    const detail = hasStudy
      ? `Detected: ${predictions.filter(p=>STUDY_OBJECTS.has(p.class)).map(p=>p.class).join(', ')}`
      : 'Person at desk, no distractions';
    setState('studying', detail);
  } else {
    phoneSeenSince = null;
    noPersonSince  = null;
    setState('studying', 'Monitoring desk...');
  }
}

// ═══════════════════════════════════════════
// FRONT CAM — MediaPipe Face Mesh + Gaze
// ═══════════════════════════════════════════
function isLookingAtScreen(lms) {
  if(!lms||!lms.length) return false;
  if(!document.getElementById('gazeCheck').checked) return true;
  const nose=lms[1],chin=lms[152],fore=lms[10],L=lms[33],R=lms[263];
  if(!nose||!chin||!fore) return true;
  const fW=Math.abs(R.x-L.x)+0.001, cX=(L.x+R.x)/2;
  const devX=Math.abs(nose.x-cX)/fW;
  const eY=(L.y+R.y)/2, fH=Math.abs(fore.y-chin.y)+0.001;
  const devY=(nose.y-eY)/fH;
  return devX<=0.28 && devY<=0.58;
}

function onFrontResults(results) {
  const vd=document.getElementById('video');
  const cv=document.getElementById('canvas');
  cv.width=vd.videoWidth||640; cv.height=vd.videoHeight||480;
  const c=cv.getContext('2d');
  c.clearRect(0,0,cv.width,cv.height);
  const now=Date.now();
  const hasFace=results.multiFaceLandmarks&&results.multiFaceLandmarks.length>0;
  if(hasFace){
    lastFaceTime=now;
    const lms=results.multiFaceLandmarks[0];
    c.fillStyle='rgba(0,229,255,0.25)';
    for(let i=0;i<lms.length;i+=3){c.beginPath();c.arc(lms[i].x*cv.width,lms[i].y*cv.height,1.1,0,Math.PI*2);c.fill();}
    c.fillStyle='rgba(0,255,136,0.7)';
    [33,160,158,133,153,144,263,387,385,362,380,373].forEach(i=>{if(lms[i]){c.beginPath();c.arc(lms[i].x*cv.width,lms[i].y*cv.height,2.3,0,Math.PI*2);c.fill();}});
    const looking=isLookingAtScreen(lms);
    if(looking){lastLookTime=now;setState('studying');}
    else{
      const aw=(now-lastLookTime)/1000;
      if(aw>=alertDelay){setState('distracted',`Looking away ${aw.toFixed(0)}s`);triggerAlert('study');}
      else setState('warning',`Possible distraction · alert in ${(alertDelay-aw).toFixed(0)}s`);
    }
  } else {
    const aw=(now-lastFaceTime)/1000;
    if(aw>=alertDelay){setState('no-face',`Away ${aw.toFixed(0)}s`);triggerAlert('study');}
    else if(aw>1)setState('no-face',`No face · alert in ${(alertDelay-aw).toFixed(0)}s`);
  }
}

// ═══════════════════════════════════════════
// START / STOP
// ═══════════════════════════════════════════
async function startMonitor() {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({video:{width:1280,height:720,facingMode:'user'}});
    const vd = document.getElementById('video');
    vd.srcObject = stream;
    await new Promise(r=>{vd.onloadedmetadata=r;});

    isRunning=true;
    phoneSeenSince=null; noPersonSince=null; distractedSince=null;
    document.getElementById('camOverlay').style.display='none';
    document.getElementById('startBtn').style.display='none';
    document.getElementById('stopBtn').style.display='inline-flex';
    document.getElementById('liveDot').className='dot on';
    document.getElementById('liveLabel').textContent='LIVE';
    document.getElementById('mainCard').classList.add('active');

    sessionStart=Date.now(); focusSec=0; disSec=0; phoneAlerts=0; studyAlerts=0;
    lastFaceTime=lastLookTime=Date.now();
    document.getElementById('phoneCount').textContent='0';
    document.getElementById('studyCount').textContent='0';
    document.getElementById('logList').innerHTML='<li style="font-size:.63rem;color:var(--muted);padding:4px 8px;font-family:\'JetBrains Mono\',monospace">No alerts yet</li>';
    clearInterval(statsTimer);
    statsTimer=setInterval(updateStats,1000);

    if(camMode==='front'){
      // Front cam: MediaPipe
      faceMesh=new FaceMesh({locateFile:f=>`https://cdn.jsdelivr.net/npm/@mediapipe/face_mesh/${f}`});
      faceMesh.setOptions({maxNumFaces:1,refineLandmarks:true,minDetectionConfidence:0.6,minTrackingConfidence:0.6});
      faceMesh.onResults(onFrontResults);
      mpCamera=new Camera(vd,{onFrame:async()=>{if(faceMesh)await faceMesh.send({image:vd});},width:640,height:480});
      await mpCamera.start();
    } else {
      // Side cam: COCO-SSD loop
      document.getElementById('scanLine').style.display='block';
      document.getElementById('detBox').classList.add('show');
      setState('studying','Starting object detection...');
      // Run detection every 800ms (fast enough, not too heavy)
      detectionLoop = setInterval(runSideDetection, 800);
    }

  } catch(err){
    alert('Camera access denied or not available.\n\n'+err.message);
  }
}

function stopMonitor(){
  isRunning=false;
  if(detectionLoop){clearInterval(detectionLoop);detectionLoop=null;}
  if(mpCamera){try{mpCamera.stop();}catch(e){}mpCamera=null;}
  if(faceMesh){try{faceMesh.close();}catch(e){}faceMesh=null;}
  const vd=document.getElementById('video');
  if(vd.srcObject){vd.srcObject.getTracks().forEach(t=>t.stop());vd.srcObject=null;}
  clearInterval(statsTimer);
  try{speechSynthesis.cancel();}catch(e){}
  document.getElementById('warnBanner').style.display='none';
  document.getElementById('camOverlay').style.display='flex';
  document.getElementById('startBtn').style.display='inline-flex';
  document.getElementById('stopBtn').style.display='none';
  document.getElementById('liveDot').className='dot';
  document.getElementById('liveLabel').textContent='OFFLINE';
  document.getElementById('mainCard').classList.remove('active');
  document.getElementById('scanLine').style.display='none';
  document.getElementById('detBox').classList.remove('show');
  setState('idle');
  const cv=document.getElementById('canvas');
  cv.getContext('2d').clearRect(0,0,cv.width,cv.height);
}

// ═══════════════════════════════════════════
// STATS
// ═══════════════════════════════════════════
function updateStats(){
  if(!sessionStart)return;
  const el=Math.floor((Date.now()-sessionStart)/1000);
  if(currentState==='studying')focusSec++;
  else if(currentState==='distracted'||currentState==='phone'||currentState==='no-face')disSec++;
  document.getElementById('sesTime').textContent=fmt(el);
  document.getElementById('focTime').textContent=fmt(focusSec);
  document.getElementById('disTime').textContent=fmt(disSec);
  const pct=el>0?Math.round(focusSec/el*100):0;
  document.getElementById('focPct').textContent=pct+'%';
  document.getElementById('progFill').style.width=pct+'%';
}
function fmt(s){return[Math.floor(s/3600),Math.floor((s%3600)/60),s%60].map(x=>String(x).padStart(2,'0')).join(':');}
</script>
</body>
</html>
