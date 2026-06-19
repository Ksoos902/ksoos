<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
<meta name="theme-color" content="#0f0f0f">
<meta name="mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<title>Creatine Tracker</title>
<link rel="manifest" href="manifest.json">
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
<style>
:root {
  --bg: #0f0f0f;
  --surface: #1a1a1a;
  --surface2: #242424;
  --border: rgba(255,255,255,0.08);
  --accent: #7c5cfc;
  --accent-soft: rgba(124,92,252,0.15);
  --text: #f0f0f0;
  --text2: #888;
  --text3: #555;
  --green: #4ade80;
  --amber: #fbbf24;
  --red: #f87171;
  --radius: 12px;
  --radius-sm: 8px;
  --font: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  --safe-bottom: env(safe-area-inset-bottom, 0px);
}
* { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
html, body { height: 100%; background: var(--bg); color: var(--text); font-family: var(--font); font-size: 15px; overscroll-behavior: none; }
body { display: flex; flex-direction: column; }

/* Nav */
.nav { display: flex; border-bottom: 0.5px solid var(--border); background: var(--bg); position: sticky; top: 0; z-index: 100; }
.nav-btn { flex: 1; padding: 14px 4px 10px; font-size: 11px; color: var(--text3); background: none; border: none; cursor: pointer; display: flex; flex-direction: column; align-items: center; gap: 4px; transition: color 0.15s; }
.nav-btn svg { width: 22px; height: 22px; stroke: currentColor; fill: none; stroke-width: 1.8; stroke-linecap: round; stroke-linejoin: round; }
.nav-btn.active { color: var(--accent); }
.nav-btn span { font-size: 10px; letter-spacing: 0.02em; }

/* Pages */
.page { display: none; flex: 1; overflow-y: auto; padding-bottom: calc(60px + var(--safe-bottom)); }
.page.active { display: block; }

/* Header */
.page-header { padding: 20px 16px 12px; border-bottom: 0.5px solid var(--border); }
.page-header h1 { font-size: 22px; font-weight: 600; letter-spacing: -0.5px; }
.page-header p { font-size: 13px; color: var(--text2); margin-top: 3px; }

/* Form sections */
.form-section { padding: 16px 16px 0; }
.section-label { font-size: 11px; font-weight: 600; color: var(--text3); text-transform: uppercase; letter-spacing: 0.08em; margin-bottom: 10px; }
.field { margin-bottom: 14px; }
.field > label { display: block; font-size: 13px; color: var(--text2); margin-bottom: 5px; }
.field input[type=text],
.field input[type=number],
.field input[type=date],
.field input[type=time],
.field textarea {
  width: 100%; padding: 10px 12px; font-size: 15px; font-family: var(--font);
  background: var(--surface); border: 0.5px solid var(--border); border-radius: var(--radius-sm);
  color: var(--text); appearance: none; -webkit-appearance: none;
}
.field input:focus, .field textarea:focus { outline: none; border-color: var(--accent); }
.field textarea { height: 72px; resize: none; }
.field input[type=date]::-webkit-calendar-picker-indicator { filter: invert(1); opacity: 0.5; }

/* Toggle buttons */
.toggle-row { display: flex; gap: 6px; flex-wrap: wrap; }
.tog { flex: 1; min-width: 0; padding: 9px 6px; font-size: 13px; background: var(--surface); border: 0.5px solid var(--border); border-radius: var(--radius-sm); cursor: pointer; color: var(--text2); text-align: center; white-space: nowrap; transition: all 0.12s; }
.tog.on { background: var(--accent-soft); border-color: var(--accent); color: var(--accent); font-weight: 500; }

/* Sliders */
.slider-wrap { display: flex; align-items: center; gap: 10px; }
.slider-wrap input[type=range] {
  flex: 1; height: 4px; appearance: none; -webkit-appearance: none;
  background: var(--surface2); border-radius: 2px; outline: none;
}
.slider-wrap input[type=range]::-webkit-slider-thumb {
  appearance: none; -webkit-appearance: none; width: 20px; height: 20px;
  border-radius: 50%; background: var(--accent); cursor: pointer;
}
.slider-val { font-size: 15px; font-weight: 600; min-width: 28px; text-align: right; color: var(--text); }
.slider-labels { display: flex; justify-content: space-between; margin-top: 2px; }
.slider-labels span { font-size: 10px; color: var(--text3); }

/* Save button */
.save-btn {
  display: block; width: calc(100% - 32px); margin: 20px 16px;
  padding: 15px; font-size: 16px; font-weight: 600; font-family: var(--font);
  background: var(--accent); color: white; border: none;
  border-radius: var(--radius); cursor: pointer; letter-spacing: -0.2px;
  transition: opacity 0.12s, transform 0.08s;
}
.save-btn:active { opacity: 0.85; transform: scale(0.98); }

/* History */
.history-list { padding: 12px 16px; }
.entry-card {
  background: var(--surface); border: 0.5px solid var(--border);
  border-radius: var(--radius); padding: 14px; margin-bottom: 10px;
}
.entry-top { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 10px; }
.entry-date { font-size: 14px; font-weight: 600; }
.entry-phase { font-size: 11px; color: var(--accent); background: var(--accent-soft); padding: 3px 8px; border-radius: 99px; }
.entry-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 6px 12px; }
.entry-stat { font-size: 12px; color: var(--text2); }
.entry-stat strong { color: var(--text); font-weight: 500; }
.entry-note { font-size: 12px; color: var(--text2); margin-top: 8px; border-top: 0.5px solid var(--border); padding-top: 8px; line-height: 1.5; }
.del-btn { background: none; border: none; cursor: pointer; color: var(--text3); padding: 4px; font-size: 18px; line-height: 1; }
.export-row { padding: 0 16px 16px; }
.export-btn { width: 100%; padding: 12px; font-size: 14px; font-family: var(--font); background: var(--surface); border: 0.5px solid var(--border); border-radius: var(--radius-sm); color: var(--text2); cursor: pointer; }
.no-entries { text-align: center; padding: 60px 20px; color: var(--text3); font-size: 14px; line-height: 1.7; }

/* Metrics */
.metrics-grid { padding: 12px 16px; display: flex; flex-direction: column; gap: 14px; }
.chart-card { background: var(--surface); border: 0.5px solid var(--border); border-radius: var(--radius); padding: 14px; }
.chart-title { font-size: 13px; font-weight: 600; margin-bottom: 4px; }
.chart-sub { font-size: 11px; color: var(--text2); margin-bottom: 12px; line-height: 1.4; }
.chart-wrap { position: relative; height: 200px; }
.no-data { height: 100px; display: flex; align-items: center; justify-content: center; font-size: 12px; color: var(--text3); }

/* Settings */
.settings-section { padding: 16px; border-bottom: 0.5px solid var(--border); }
.settings-section h2 { font-size: 13px; font-weight: 600; color: var(--text3); text-transform: uppercase; letter-spacing: 0.06em; margin-bottom: 12px; }
.setting-row { display: flex; justify-content: space-between; align-items: center; padding: 10px 0; border-bottom: 0.5px solid var(--border); }
.setting-row:last-child { border-bottom: none; }
.setting-label { font-size: 14px; }
.setting-desc { font-size: 12px; color: var(--text2); margin-top: 2px; }
.setting-val { font-size: 13px; color: var(--accent); font-weight: 500; }
.notif-btn { padding: 9px 16px; font-size: 13px; font-family: var(--font); background: var(--accent); color: white; border: none; border-radius: var(--radius-sm); cursor: pointer; font-weight: 500; }
.notif-btn.off { background: var(--surface2); color: var(--text2); border: 0.5px solid var(--border); }
.notif-status { font-size: 12px; margin-top: 8px; }
.notif-status.ok { color: var(--green); }
.notif-status.err { color: var(--red); }

/* Toast */
.toast {
  position: fixed; bottom: calc(72px + var(--safe-bottom) + 10px); left: 50%; transform: translateX(-50%);
  background: var(--text); color: var(--bg); padding: 10px 20px;
  border-radius: 99px; font-size: 13px; font-weight: 500; z-index: 999;
  opacity: 0; transition: opacity 0.2s; pointer-events: none; white-space: nowrap;
}
.toast.show { opacity: 1; }

/* Scrollbar */
::-webkit-scrollbar { width: 3px; }
::-webkit-scrollbar-track { background: transparent; }
::-webkit-scrollbar-thumb { background: var(--surface2); border-radius: 2px; }
</style>
</head>
<body>

<!-- Bottom nav -->
<nav class="nav">
  <button class="nav-btn active" onclick="showPage('log')" id="nav-log">
    <svg viewBox="0 0 24 24"><path d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5"/><path d="M11 13l9-9"/><path d="M15 5h4v4"/></svg>
    <span>Log</span>
  </button>
  <button class="nav-btn" onclick="showPage('history')" id="nav-history">
    <svg viewBox="0 0 24 24"><rect x="3" y="4" width="18" height="18" rx="2"/><path d="M16 2v4M8 2v4M3 10h18"/></svg>
    <span>History</span>
  </button>
  <button class="nav-btn" onclick="showPage('metrics')" id="nav-metrics">
    <svg viewBox="0 0 24 24"><path d="M3 3v18h18"/><path d="M18 9l-5 5-4-4-3 3"/></svg>
    <span>Insights</span>
  </button>
  <button class="nav-btn" onclick="showPage('settings')" id="nav-settings">
    <svg viewBox="0 0 24 24"><circle cx="12" cy="12" r="3"/><path d="M12 1v4M12 19v4M4.22 4.22l2.83 2.83M16.95 16.95l2.83 2.83M1 12h4M19 12h4M4.22 19.78l2.83-2.83M16.95 7.05l2.83-2.83"/></svg>
    <span>Settings</span>
  </button>
</nav>

<!-- LOG PAGE -->
<div class="page active" id="page-log">
  <div class="page-header">
    <h1>Today's entry</h1>
    <p id="hdr-date"></p>
  </div>

  <div class="form-section">
    <div class="section-label">Date & cycle</div>
    <div class="field"><label>Date</label><input type="date" id="f-date"></div>
    <div class="field"><label>Same as previous entry?</label>
      <div class="toggle-row">
        <div class="tog" onclick="tog(this,'same','Y')">Yes</div>
        <div class="tog" onclick="tog(this,'same','N')">No</div>
      </div>
    </div>
    <div class="field"><label>Cycle day</label><input type="number" id="f-cycle" min="1" max="40" placeholder="e.g. 14"></div>
    <div class="field"><label>Cycle phase</label>
      <div class="toggle-row">
        <div class="tog" onclick="tog(this,'phase','Follicular')">Follicular</div>
        <div class="tog" onclick="tog(this,'phase','Luteal')">Luteal</div>
      </div>
    </div>
  </div>

  <div class="form-section">
    <div class="section-label">Sleep & energy</div>
    <div class="field"><label>Sleep quality</label>
      <div class="toggle-row">
        <div class="tog" onclick="tog(this,'sleep','Low')">Low</div>
        <div class="tog" onclick="tog(this,'sleep','Medium')">Medium</div>
        <div class="tog" onclick="tog(this,'sleep','High')">High</div>
      </div>
    </div>
    <div class="field"><label>Exhaustion</label>
      <div class="slider-wrap">
        <input type="range" min="0" max="10" step="1" value="5" id="f-exhaustion" oninput="setVal('sv-ex',this.value)">
        <span class="slider-val" id="sv-ex">5</span>
      </div>
      <div class="slider-labels"><span>None</span><span>Extreme</span></div>
    </div>
  </div>

  <div class="form-section">
    <div class="section-label">Medication & creatine</div>
    <div class="field"><label>Meds (mg)</label><input type="number" id="f-meds" step="0.5" placeholder="e.g. 5"></div>
    <div class="field"><label>Creatine dose (g)</label><input type="number" id="f-creatine" step="0.5" placeholder="e.g. 1.5"></div>
    <div class="field"><label>Time taken</label><input type="time" id="f-time"></div>
  </div>

  <div class="form-section">
    <div class="section-label">Body & symptoms</div>
    <div class="field"><label>Body weight (kg)</label><input type="number" id="f-weight" step="0.1" placeholder="e.g. 57.1"></div>
    <div class="field"><label>Bloating</label>
      <div class="slider-wrap">
        <input type="range" min="0" max="10" step="1" value="0" id="f-bloating" oninput="setVal('sv-bl',this.value)">
        <span class="slider-val" id="sv-bl">0</span>
      </div>
      <div class="slider-labels"><span>None</span><span>Severe</span></div>
    </div>
    <div class="field"><label>Pre-bloated?</label>
      <div class="toggle-row">
        <div class="tog" onclick="tog(this,'prebloat','Y')">Yes</div>
        <div class="tog" onclick="tog(this,'prebloat','N')">No</div>
      </div>
    </div>
  </div>

  <div class="form-section">
    <div class="section-label">Digestion & appetite</div>
    <div class="field"><label>Appetite</label>
      <div class="slider-wrap">
        <input type="range" min="0" max="10" step="1" value="5" id="f-appetite" oninput="setVal('sv-ap',this.value)">
        <span class="slider-val" id="sv-ap">5</span>
      </div>
      <div class="slider-labels"><span>None</span><span>High</span></div>
    </div>
    <div class="field"><label>Bowel comfort</label>
      <div class="slider-wrap">
        <input type="range" min="0" max="10" step="1" value="5" id="f-bowel" oninput="setVal('sv-bw',this.value)">
        <span class="slider-val" id="sv-bw">5</span>
      </div>
      <div class="slider-labels"><span>Uncomfortable</span><span>Comfortable</span></div>
    </div>
    <div class="field"><label>Bowel movement today?</label>
      <div class="toggle-row">
        <div class="tog" onclick="tog(this,'bm','Y')">Yes</div>
        <div class="tog" onclick="tog(this,'bm','N')">No</div>
      </div>
    </div>
  </div>

  <div class="form-section">
    <div class="section-label">Training</div>
    <div class="field"><label>Training day?</label>
      <div class="toggle-row">
        <div class="tog" onclick="tog(this,'training','Y');showTrainType(true)">Yes</div>
        <div class="tog" onclick="tog(this,'training','N');showTrainType(false)">No</div>
      </div>
    </div>
    <div class="field" id="train-type-field" style="display:none"><label>Type of training</label>
      <div class="toggle-row">
        <div class="tog" onclick="tog(this,'traintype','Cardio')">Cardio</div>
        <div class="tog" onclick="tog(this,'traintype','Resistance')">Resistance</div>
        <div class="tog" onclick="tog(this,'traintype','Mixed')">Mixed</div>
      </div>
    </div>
    <div class="field"><label>Step count</label><input type="number" id="f-steps" placeholder="e.g. 8500"></div>
  </div>

  <div class="form-section">
    <div class="section-label">Notes & expectations</div>
    <div class="field"><label>Expectations</label><textarea id="f-expect" placeholder="What do you expect today?"></textarea></div>
    <div class="field"><label>Notes</label><textarea id="f-notes" placeholder="Anything else worth noting..."></textarea></div>
  </div>

  <button class="save-btn" onclick="saveEntry()">Save entry</button>
</div>

<!-- HISTORY PAGE -->
<div class="page" id="page-history">
  <div class="page-header">
    <h1>History</h1>
    <p id="entry-count">0 entries</p>
  </div>
  <div class="history-list" id="history-list"></div>
  <div class="export-row">
    <button class="export-btn" onclick="exportCSV()">↓ Export all data as CSV</button>
  </div>
</div>

<!-- METRICS PAGE -->
<div class="page" id="page-metrics">
  <div class="page-header">
    <h1>Insights</h1>
    <p>Patterns from your logged data</p>
  </div>
  <div class="metrics-grid" id="metrics-grid"></div>
</div>

<!-- SETTINGS PAGE -->
<div class="page" id="page-settings">
  <div class="page-header">
    <h1>Settings</h1>
    <p>Reminders & preferences</p>
  </div>

  <div class="settings-section">
    <h2>Reminders</h2>
    <div style="margin-bottom:12px">
      <div style="font-size:14px;margin-bottom:4px">Creatine reminder</div>
      <div style="font-size:12px;color:var(--text2);margin-bottom:10px">Daily at 12:30 pm — time to take your creatine</div>
      <button class="notif-btn off" id="btn-creatine-notif" onclick="toggleNotif('creatine')">Enable reminder</button>
    </div>
    <div>
      <div style="font-size:14px;margin-bottom:4px">Log reminder</div>
      <div style="font-size:12px;color:var(--text2);margin-bottom:10px">Daily at 11:00 pm — log your day's entry</div>
      <button class="notif-btn off" id="btn-log-notif" onclick="toggleNotif('log')">Enable reminder</button>
    </div>
    <div class="notif-status" id="notif-status"></div>
  </div>

  <div class="settings-section">
    <h2>Data</h2>
    <div class="setting-row">
      <div>
        <div class="setting-label">Total entries</div>
      </div>
      <div class="setting-val" id="stat-entries">0</div>
    </div>
    <div class="setting-row">
      <div>
        <div class="setting-label">Days tracked</div>
      </div>
      <div class="setting-val" id="stat-days">0</div>
    </div>
    <div style="margin-top:12px">
      <button class="export-btn" onclick="exportCSV()">↓ Export as CSV</button>
    </div>
  </div>

  <div class="settings-section">
    <h2>About</h2>
    <div style="font-size:13px;color:var(--text2);line-height:1.6">
      Creatine Tracker v1.0<br>
      Data stored locally on your device.<br>
      Add to home screen for the best experience and to enable notifications.
    </div>
  </div>
</div>

<div class="toast" id="toast"></div>

<script>
// ─── State ───────────────────────────────────────────────────────────────────
const S = {}; // toggle state
let entries = JSON.parse(localStorage.getItem('ct-entries') || '[]');
let notifSettings = JSON.parse(localStorage.getItem('ct-notif') || '{"creatine":false,"log":false}');
let charts = {};

// ─── Init ─────────────────────────────────────────────────────────────────────
document.getElementById('hdr-date').textContent = new Date().toLocaleDateString('en-GB',{weekday:'long',day:'numeric',month:'long',year:'numeric'});
document.getElementById('f-date').value = todayISO();

if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('./sw.js').then(reg => {
    console.log('SW registered');
    setupNotifUI();
    scheduleNotifications();
  });
}

function todayISO() {
  return new Date().toISOString().split('T')[0];
}

// ─── Navigation ───────────────────────────────────────────────────────────────
function showPage(id) {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
  document.getElementById('page-'+id).classList.add('active');
  document.getElementById('nav-'+id).classList.add('active');
  if (id === 'history') renderHistory();
  if (id === 'metrics') renderMetrics();
  if (id === 'settings') renderSettings();
}

// ─── Toggle buttons ───────────────────────────────────────────────────────────
function tog(el, key, val) {
  el.closest('.toggle-row').querySelectorAll('.tog').forEach(b => b.classList.remove('on'));
  el.classList.add('on');
  S[key] = val;
}

function setVal(id, val) { document.getElementById(id).textContent = val; }
function showTrainType(show) { document.getElementById('train-type-field').style.display = show ? '' : 'none'; }

// ─── Save entry ───────────────────────────────────────────────────────────────
function saveEntry() {
  const entry = {
    date: document.getElementById('f-date').value,
    same: S.same||'',
    cycleDay: +document.getElementById('f-cycle').value||null,
    phase: S.phase||'',
    sleep: S.sleep||'',
    exhaustion: +document.getElementById('f-exhaustion').value,
    meds: +document.getElementById('f-meds').value||null,
    creatine: +document.getElementById('f-creatine').value||null,
    timeTaken: document.getElementById('f-time').value,
    weight: +document.getElementById('f-weight').value||null,
    bloating: +document.getElementById('f-bloating').value,
    prebloat: S.prebloat||'',
    appetite: +document.getElementById('f-appetite').value,
    bowelComfort: +document.getElementById('f-bowel').value,
    bm: S.bm||'',
    training: S.training||'',
    trainType: S.traintype||'',
    steps: +document.getElementById('f-steps').value||null,
    expectations: document.getElementById('f-expect').value.trim(),
    notes: document.getElementById('f-notes').value.trim(),
    ts: Date.now()
  };

  // Replace if same date, else prepend
  const idx = entries.findIndex(e => e.date === entry.date);
  if (idx >= 0) entries[idx] = entry; else entries.unshift(entry);
  entries.sort((a,b) => b.date.localeCompare(a.date));
  save();
  showToast('Entry saved ✓');
}

function save() {
  localStorage.setItem('ct-entries', JSON.stringify(entries));
}

// ─── History ──────────────────────────────────────────────────────────────────
function renderHistory() {
  document.getElementById('entry-count').textContent = entries.length + ' entr' + (entries.length===1?'y':'ies');
  const el = document.getElementById('history-list');
  if (!entries.length) {
    el.innerHTML = '<div class="no-entries">No entries yet.<br>Start logging to see your history here.</div>';
    return;
  }
  el.innerHTML = entries.map((e,i) => `
    <div class="entry-card">
      <div class="entry-top">
        <div class="entry-date">${fmtDate(e.date)}</div>
        <div style="display:flex;gap:8px;align-items:center">
          ${e.phase ? `<span class="entry-phase">${e.phase}</span>` : ''}
          <button class="del-btn" onclick="delEntry(${i})" aria-label="Delete">×</button>
        </div>
      </div>
      <div class="entry-grid">
        ${e.creatine!=null ? `<div class="entry-stat">Creatine <strong>${e.creatine}g</strong></div>` : ''}
        ${e.weight!=null ? `<div class="entry-stat">Weight <strong>${e.weight}kg</strong></div>` : ''}
        ${e.sleep ? `<div class="entry-stat">Sleep <strong>${e.sleep}</strong></div>` : ''}
        ${e.exhaustion!=null ? `<div class="entry-stat">Exhaustion <strong>${e.exhaustion}/10</strong></div>` : ''}
        ${e.bloating!=null ? `<div class="entry-stat">Bloating <strong>${e.bloating}/10</strong></div>` : ''}
        ${e.appetite!=null ? `<div class="entry-stat">Appetite <strong>${e.appetite}/10</strong></div>` : ''}
        ${e.training ? `<div class="entry-stat">Training <strong>${e.training==='Y'?(e.trainType||'Yes'):'Rest day'}</strong></div>` : ''}
        ${e.steps ? `<div class="entry-stat">Steps <strong>${e.steps.toLocaleString()}</strong></div>` : ''}
      </div>
      ${e.notes ? `<div class="entry-note">${e.notes}</div>` : ''}
    </div>`).join('');
}

function delEntry(i) {
  if (!confirm('Delete this entry?')) return;
  entries.splice(i,1);
  save();
  renderHistory();
}

function fmtDate(iso) {
  if (!iso) return '—';
  return new Date(iso+'T12:00:00').toLocaleDateString('en-GB',{weekday:'short',day:'numeric',month:'short',year:'numeric'});
}

// ─── Export ───────────────────────────────────────────────────────────────────
function exportCSV() {
  const headers = ['Date','Same as prev','Cycle day','Phase','Sleep','Exhaustion','Meds (mg)','Creatine (g)','Time taken','Weight (kg)','Bloating','Pre-bloated','Appetite','Bowel comfort','Bowel movement','Training','Train type','Steps','Expectations','Notes'];
  const rows = entries.map(e => [
    e.date, e.same, e.cycleDay, e.phase, e.sleep, e.exhaustion,
    e.meds, e.creatine, e.timeTaken, e.weight, e.bloating, e.prebloat,
    e.appetite, e.bowelComfort, e.bm, e.training, e.trainType, e.steps,
    `"${(e.expectations||'').replace(/"/g,'""')}"`,
    `"${(e.notes||'').replace(/"/g,'""')}"`
  ]);
  const csv = [headers, ...rows].map(r => r.join(',')).join('\n');
  const a = document.createElement('a');
  a.href = 'data:text/csv;charset=utf-8,' + encodeURIComponent(csv);
  a.download = `creatine-tracker-${todayISO()}.csv`;
  a.click();
}

// ─── Metrics ──────────────────────────────────────────────────────────────────
const ACCENT = '#7c5cfc';
const ACCENT2 = '#4ade80';
const ACCENT3 = '#fbbf24';
const ACCENT4 = '#f87171';

const chartDefaults = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: { legend: { display: false }, tooltip: { backgroundColor: '#1a1a1a', borderColor: '#333', borderWidth: 1, titleColor: '#f0f0f0', bodyColor: '#888', padding: 10 } },
  scales: {
    x: { grid: { color: 'rgba(255,255,255,0.04)' }, ticks: { color: '#555', font: { size: 11 } } },
    y: { grid: { color: 'rgba(255,255,255,0.04)' }, ticks: { color: '#555', font: { size: 11 } }, beginAtZero: true }
  }
};

function avg(arr) { return arr.length ? arr.reduce((a,b)=>a+b,0)/arr.length : 0; }
function round1(n) { return Math.round(n*10)/10; }

function renderMetrics() {
  // Destroy old charts
  Object.values(charts).forEach(c => { try { c.destroy(); } catch(e){} });
  charts = {};

  const grid = document.getElementById('metrics-grid');
  grid.innerHTML = '';

  if (entries.length < 2) {
    grid.innerHTML = '<div style="text-align:center;padding:60px 20px;color:var(--text3);font-size:14px;line-height:1.7">Log at least 2 entries<br>to see insights here.</div>';
    return;
  }

  const chartDefs = [
    { id: 'c1', title: 'Symptoms vs cycle phase', sub: 'Average bloating & exhaustion in each phase', fn: chartSymptomsVsPhase },
    { id: 'c2', title: 'Physical symptoms over time', sub: 'Bloating and exhaustion day by day', fn: chartSymptomsOverTime },
    { id: 'c3', title: 'Symptoms by creatine dose', sub: 'Average bloating at each dose level', fn: chartSymptomsByDose },
    { id: 'c4', title: 'Symptoms vs training days', sub: 'Average bloating on rest vs training days', fn: chartSymptomsVsTraining },
    { id: 'c5', title: 'Sleep quality vs symptoms', sub: 'Average bloating by sleep level', fn: chartSleepVsSymptoms },
    { id: 'c6', title: 'Cycle day vs symptoms', sub: 'Average bloating by cycle day', fn: chartCycleDayVsSymptoms },
    { id: 'c7', title: 'Cycle phase vs sleep quality', sub: 'Sleep score distribution by phase', fn: chartPhaseVsSleep },
    { id: 'c8', title: 'Cycle day vs average weight', sub: 'Weight trend across your cycle', fn: chartCycleDayVsWeight },
    { id: 'c9', title: 'Creatine dose vs weight', sub: 'Average weight at each dose level', fn: chartDoseVsWeight },
    { id: 'c10', title: 'Creatine dose vs appetite', sub: 'Average appetite at each dose level', fn: chartDoseVsAppetite },
    { id: 'c11', title: 'Creatine dose vs sleep quality', sub: 'Sleep score by dose level', fn: chartDoseVsSleep },
  ];

  chartDefs.forEach(def => {
    const card = document.createElement('div');
    card.className = 'chart-card';
    card.innerHTML = `<div class="chart-title">${def.title}</div><div class="chart-sub">${def.sub}</div><div class="chart-wrap"><canvas id="${def.id}"></canvas></div>`;
    grid.appendChild(card);
  });

  // Render after DOM is ready
  requestAnimationFrame(() => {
    chartDefs.forEach(def => {
      try { def.fn(def.id); } catch(e) { console.error(def.id, e); }
    });
  });
}

function mkChart(id, type, data, opts={}) {
  const ctx = document.getElementById(id);
  if (!ctx) return;
  charts[id] = new Chart(ctx, {
    type,
    data,
    options: deepMerge(JSON.parse(JSON.stringify(chartDefaults)), opts)
  });
}

function deepMerge(target, source) {
  for (const key of Object.keys(source)) {
    if (source[key] && typeof source[key] === 'object' && !Array.isArray(source[key])) {
      if (!target[key]) target[key] = {};
      deepMerge(target[key], source[key]);
    } else { target[key] = source[key]; }
  }
  return target;
}

// 1. Symptoms vs cycle phase
function chartSymptomsVsPhase(id) {
  const phases = ['Follicular','Luteal'];
  const bloat = phases.map(p => {
    const r = entries.filter(e => e.phase===p && e.bloating!=null);
    return r.length ? round1(avg(r.map(e=>e.bloating))) : 0;
  });
  const exhaust = phases.map(p => {
    const r = entries.filter(e => e.phase===p && e.exhaustion!=null);
    return r.length ? round1(avg(r.map(e=>e.exhaustion))) : 0;
  });
  mkChart(id, 'bar', {
    labels: phases,
    datasets: [
      { label: 'Bloating', data: bloat, backgroundColor: ACCENT, borderRadius: 6 },
      { label: 'Exhaustion', data: exhaust, backgroundColor: ACCENT3, borderRadius: 6 }
    ]
  }, { plugins: { legend: { display: true, labels: { color: '#888', font: { size: 11 }, boxWidth: 12 } } }, scales: { y: { max: 10 } } });
}

// 2. Symptoms over time
function chartSymptomsOverTime(id) {
  const sorted = [...entries].sort((a,b)=>a.date.localeCompare(b.date)).slice(-30);
  const labels = sorted.map(e => e.date.slice(5)); // MM-DD
  mkChart(id, 'line', {
    labels,
    datasets: [
      { label: 'Bloating', data: sorted.map(e=>e.bloating), borderColor: ACCENT, backgroundColor: 'rgba(124,92,252,0.1)', tension: 0.3, pointRadius: 3, fill: true },
      { label: 'Exhaustion', data: sorted.map(e=>e.exhaustion), borderColor: ACCENT3, backgroundColor: 'rgba(251,191,36,0.05)', tension: 0.3, pointRadius: 3 }
    ]
  }, { plugins: { legend: { display: true, labels: { color: '#888', font: { size: 11 }, boxWidth: 12 } } }, scales: { y: { max: 10 } } });
}

// 3. Symptoms by creatine dose
function chartSymptomsByDose(id) {
  const doses = [...new Set(entries.filter(e=>e.creatine!=null).map(e=>e.creatine))].sort((a,b)=>a-b);
  const bloat = doses.map(d => {
    const r = entries.filter(e=>e.creatine===d && e.bloating!=null);
    return round1(avg(r.map(e=>e.bloating)));
  });
  mkChart(id, 'bar', {
    labels: doses.map(d=>d+'g'),
    datasets: [{ label: 'Avg bloating', data: bloat, backgroundColor: ACCENT, borderRadius: 6 }]
  }, { scales: { y: { max: 10 } } });
}

// 4. Symptoms vs training
function chartSymptomsVsTraining(id) {
  const groups = ['Rest day','Training day'];
  const bloat = [
    round1(avg(entries.filter(e=>e.training==='N' && e.bloating!=null).map(e=>e.bloating))),
    round1(avg(entries.filter(e=>e.training==='Y' && e.bloating!=null).map(e=>e.bloating)))
  ];
  mkChart(id, 'bar', {
    labels: groups,
    datasets: [{ label: 'Avg bloating', data: bloat, backgroundColor: [ACCENT2, ACCENT], borderRadius: 6 }]
  }, { scales: { y: { max: 10 } } });
}

// 5. Sleep vs symptoms
function chartSleepVsSymptoms(id) {
  const levels = ['Low','Medium','High'];
  const bloat = levels.map(s => {
    const r = entries.filter(e=>e.sleep===s && e.bloating!=null);
    return r.length ? round1(avg(r.map(e=>e.bloating))) : 0;
  });
  mkChart(id, 'bar', {
    labels: levels,
    datasets: [{ label: 'Avg bloating', data: bloat, backgroundColor: [ACCENT4, ACCENT3, ACCENT2], borderRadius: 6 }]
  }, { scales: { y: { max: 10 } } });
}

// 6. Cycle day vs symptoms
function chartCycleDayVsSymptoms(id) {
  const days = {};
  entries.filter(e=>e.cycleDay && e.bloating!=null).forEach(e=>{
    if (!days[e.cycleDay]) days[e.cycleDay]=[];
    days[e.cycleDay].push(e.bloating);
  });
  const sorted = Object.keys(days).map(Number).sort((a,b)=>a-b);
  mkChart(id, 'line', {
    labels: sorted.map(d=>'Day '+d),
    datasets: [{ label: 'Avg bloating', data: sorted.map(d=>round1(avg(days[d]))), borderColor: ACCENT, backgroundColor: 'rgba(124,92,252,0.1)', tension: 0.3, pointRadius: 4, fill: true }]
  }, { scales: { y: { max: 10 } } });
}

// 7. Cycle phase vs sleep quality
function chartPhaseVsSleep(id) {
  const sleepScore = { Low: 1, Medium: 2, High: 3 };
  const phases = ['Follicular','Luteal'];
  const scores = phases.map(p => {
    const r = entries.filter(e=>e.phase===p && e.sleep);
    return r.length ? round1(avg(r.map(e=>sleepScore[e.sleep]||0))) : 0;
  });
  mkChart(id, 'bar', {
    labels: phases,
    datasets: [{ label: 'Sleep score (1=Low, 3=High)', data: scores, backgroundColor: [ACCENT, ACCENT2], borderRadius: 6 }]
  }, { scales: { y: { max: 3, ticks: { callback: v => ['','Low','Med','High'][v]||v } } } });
}

// 8. Cycle day vs weight
function chartCycleDayVsWeight(id) {
  const days = {};
  entries.filter(e=>e.cycleDay && e.weight!=null).forEach(e=>{
    if (!days[e.cycleDay]) days[e.cycleDay]=[];
    days[e.cycleDay].push(e.weight);
  });
  const sorted = Object.keys(days).map(Number).sort((a,b)=>a-b);
  if (!sorted.length) { noData(id); return; }
  mkChart(id, 'line', {
    labels: sorted.map(d=>'Day '+d),
    datasets: [{ label: 'Avg weight (kg)', data: sorted.map(d=>round1(avg(days[d]))), borderColor: ACCENT3, backgroundColor: 'rgba(251,191,36,0.1)', tension: 0.3, pointRadius: 4, fill: true }]
  }, { scales: { y: { beginAtZero: false } } });
}

// 9. Creatine dose vs weight
function chartDoseVsWeight(id) {
  const doses = [...new Set(entries.filter(e=>e.creatine!=null && e.weight!=null).map(e=>e.creatine))].sort((a,b)=>a-b);
  if (!doses.length) { noData(id); return; }
  const weights = doses.map(d => round1(avg(entries.filter(e=>e.creatine===d && e.weight!=null).map(e=>e.weight))));
  mkChart(id, 'line', {
    labels: doses.map(d=>d+'g'),
    datasets: [{ label: 'Avg weight (kg)', data: weights, borderColor: ACCENT3, backgroundColor: 'rgba(251,191,36,0.1)', tension: 0.3, pointRadius: 5, fill: true }]
  }, { scales: { y: { beginAtZero: false } } });
}

// 10. Creatine dose vs appetite
function chartDoseVsAppetite(id) {
  const doses = [...new Set(entries.filter(e=>e.creatine!=null).map(e=>e.creatine))].sort((a,b)=>a-b);
  const appetite = doses.map(d => {
    const r = entries.filter(e=>e.creatine===d && e.appetite!=null);
    return r.length ? round1(avg(r.map(e=>e.appetite))) : 0;
  });
  mkChart(id, 'bar', {
    labels: doses.map(d=>d+'g'),
    datasets: [{ label: 'Avg appetite', data: appetite, backgroundColor: ACCENT2, borderRadius: 6 }]
  }, { scales: { y: { max: 10 } } });
}

// 11. Creatine dose vs sleep quality
function chartDoseVsSleep(id) {
  const sleepScore = { Low: 1, Medium: 2, High: 3 };
  const doses = [...new Set(entries.filter(e=>e.creatine!=null && e.sleep).map(e=>e.creatine))].sort((a,b)=>a-b);
  if (!doses.length) { noData(id); return; }
  const scores = doses.map(d => {
    const r = entries.filter(e=>e.creatine===d && e.sleep);
    return r.length ? round1(avg(r.map(e=>sleepScore[e.sleep]||0))) : 0;
  });
  mkChart(id, 'bar', {
    labels: doses.map(d=>d+'g'),
    datasets: [{ label: 'Sleep score', data: scores, backgroundColor: ACCENT4, borderRadius: 6 }]
  }, { scales: { y: { max: 3, ticks: { callback: v => ['','Low','Med','High'][v]||v } } } });
}

function noData(id) {
  const wrap = document.getElementById(id).parentElement;
  wrap.innerHTML = '<div class="no-data">Not enough data yet</div>';
}

// ─── Settings & notifications ─────────────────────────────────────────────────
function renderSettings() {
  document.getElementById('stat-entries').textContent = entries.length;
  const days = new Set(entries.map(e=>e.date)).size;
  document.getElementById('stat-days').textContent = days;
  setupNotifUI();
}

function setupNotifUI() {
  const creatineBtn = document.getElementById('btn-creatine-notif');
  const logBtn = document.getElementById('btn-log-notif');
  if (!creatineBtn) return;

  if (!('Notification' in window) || !('serviceWorker' in navigator)) {
    creatineBtn.textContent = 'Not supported';
    creatineBtn.disabled = true;
    logBtn.textContent = 'Not supported';
    logBtn.disabled = true;
    return;
  }

  const perm = Notification.permission;
  updateNotifButtons();
}

function updateNotifButtons() {
  const creatineBtn = document.getElementById('btn-creatine-notif');
  const logBtn = document.getElementById('btn-log-notif');
  const statusEl = document.getElementById('notif-status');
  if (!creatineBtn) return;

  const perm = Notification.permission;

  if (perm === 'denied') {
    statusEl.textContent = 'Notifications blocked. Enable in your browser settings.';
    statusEl.className = 'notif-status err';
    [creatineBtn, logBtn].forEach(b => { b.textContent = 'Blocked'; b.disabled = true; b.className = 'notif-btn off'; });
    return;
  }

  creatineBtn.className = 'notif-btn' + (notifSettings.creatine ? '' : ' off');
  creatineBtn.textContent = notifSettings.creatine ? 'Reminder on ✓' : 'Enable reminder';
  logBtn.className = 'notif-btn' + (notifSettings.log ? '' : ' off');
  logBtn.textContent = notifSettings.log ? 'Reminder on ✓' : 'Enable reminder';

  if (notifSettings.creatine || notifSettings.log) {
    statusEl.textContent = 'Reminders active — keep this app added to your home screen.';
    statusEl.className = 'notif-status ok';
  } else {
    statusEl.textContent = '';
  }
}

async function toggleNotif(type) {
  if (!('Notification' in window)) { alert('Notifications not supported on this browser.'); return; }

  if (Notification.permission === 'default') {
    const perm = await Notification.requestPermission();
    if (perm !== 'granted') {
      document.getElementById('notif-status').textContent = 'Permission denied. Enable notifications in your device settings.';
      document.getElementById('notif-status').className = 'notif-status err';
      return;
    }
  }

  if (Notification.permission !== 'granted') {
    document.getElementById('notif-status').textContent = 'Notifications blocked in device settings.';
    document.getElementById('notif-status').className = 'notif-status err';
    return;
  }

  notifSettings[type] = !notifSettings[type];
  localStorage.setItem('ct-notif', JSON.stringify(notifSettings));
  updateNotifButtons();
  scheduleNotifications();

  if (notifSettings[type]) {
    // Send a test notification
    const msg = type === 'creatine' ? 'Creatine reminder set for 12:30 pm daily' : 'Log reminder set for 11:00 pm daily';
    showToast(msg);
  }
}

// ─── Notification scheduling ──────────────────────────────────────────────────
function scheduleNotifications() {
  if (!('Notification' in window) || Notification.permission !== 'granted') return;

  // Clear existing interval
  if (window._notifInterval) clearInterval(window._notifInterval);

  // Check every minute if a notification should fire
  window._notifInterval = setInterval(checkAndNotify, 60 * 1000);
  checkAndNotify(); // run immediately too
}

function checkAndNotify() {
  const now = new Date();
  const h = now.getHours();
  const m = now.getMinutes();
  const key = `ct-notif-sent-${now.toDateString()}`;
  const sent = JSON.parse(localStorage.getItem(key) || '{}');

  if (notifSettings.creatine && h === 12 && m === 30 && !sent.creatine) {
    fireNotif('Time to take your creatine!', 'Daily 12:30 pm reminder — log your dose after.', 'creatine');
    sent.creatine = true;
    localStorage.setItem(key, JSON.stringify(sent));
  }

  if (notifSettings.log && h === 23 && m === 0 && !sent.log) {
    fireNotif('Log your day', 'Don\'t forget to fill in today\'s creatine tracker entry.', 'log');
    sent.log = true;
    localStorage.setItem(key, JSON.stringify(sent));
  }
}

function fireNotif(title, body, tag) {
  navigator.serviceWorker.ready.then(reg => {
    reg.showNotification(title, {
      body,
      tag,
      icon: './icon-192.png',
      badge: './icon-192.png',
      vibrate: [200, 100, 200],
      requireInteraction: false
    });
  });
}

// ─── Toast ────────────────────────────────────────────────────────────────────
let toastTimer;
function showToast(msg) {
  const el = document.getElementById('toast');
  el.textContent = msg;
  el.classList.add('show');
  clearTimeout(toastTimer);
  toastTimer = setTimeout(() => el.classList.remove('show'), 2500);
}
</script>
</body>
</html>
