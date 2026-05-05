[index.html.html](https://github.com/user-attachments/files/27383694/index.html.html)
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="robots" content="noindex, nofollow">
<title>Solé · Dashboard</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Fraunces:ital,opsz,wght@0,9..144,300..900;1,9..144,300..900&family=JetBrains+Mono:wght@400;500;700&family=Inter+Tight:wght@400;500;600;700&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0"></script>
<style>
  /* ============================================
     RESET & TOKENS
     ============================================ */
  * { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    /* paleta — verde profundo + crema cálida + acentos */
    --ink: #0f1611;
    --ink-soft: #1a2420;
    --ink-line: #243029;
    --paper: #f5efe4;
    --paper-warm: #ebe2d2;
    --paper-dark: #d4c8b3;
    --moss: #2d4a3a;
    --moss-bright: #4a7a5e;
    --citrus: #d4ff5a;
    --rust: #c45a3d;
    --plum: #6b3a5c;
    --sky: #7fa8c9;

    --text: var(--paper);
    --text-mut: rgba(245, 239, 228, 0.62);
    --text-faint: rgba(245, 239, 228, 0.38);

    --display: 'Fraunces', Georgia, serif;
    --body: 'Inter Tight', -apple-system, sans-serif;
    --mono: 'JetBrains Mono', monospace;

    --r-sm: 4px;
    --r-md: 10px;
    --r-lg: 18px;
  }

  html, body {
    background: var(--ink);
    color: var(--text);
    font-family: var(--body);
    font-size: 14px;
    line-height: 1.5;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    min-height: 100vh;
  }

  body::before {
    content: '';
    position: fixed; inset: 0;
    background:
      radial-gradient(ellipse 90% 60% at 8% 0%, rgba(45, 74, 58, 0.4), transparent 60%),
      radial-gradient(ellipse 70% 50% at 100% 100%, rgba(107, 58, 92, 0.18), transparent 60%);
    pointer-events: none;
    z-index: 0;
  }

  body::after {
    content: '';
    position: fixed; inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='3'/%3E%3CfeColorMatrix values='0 0 0 0 0.96 0 0 0 0 0.94 0 0 0 0 0.89 0 0 0 0.04 0'/%3E%3C/filter%3E%3Crect width='200' height='200' filter='url(%23n)'/%3E%3C/svg%3E");
    pointer-events: none;
    z-index: 1;
    mix-blend-mode: overlay;
  }

  .app {
    position: relative;
    z-index: 2;
    max-width: 1400px;
    margin: 0 auto;
    padding: 32px 32px 80px;
  }

  /* ============================================
     HEADER
     ============================================ */
  .header {
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    padding-bottom: 28px;
    margin-bottom: 32px;
    border-bottom: 1px solid var(--ink-line);
    position: relative;
  }

  .header::after {
    content: '';
    position: absolute;
    left: 0; bottom: -1px;
    width: 80px; height: 1px;
    background: var(--citrus);
  }

  .brand h1 {
    font-family: var(--display);
    font-weight: 300;
    font-size: clamp(40px, 5vw, 64px);
    letter-spacing: -0.03em;
    line-height: 0.95;
    color: var(--paper);
  }

  .brand h1 em {
    font-style: italic;
    font-weight: 400;
    color: var(--citrus);
  }

  .brand .sub {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--text-mut);
    letter-spacing: 0.18em;
    text-transform: uppercase;
    margin-top: 8px;
  }

  .header-meta {
    text-align: right;
    font-family: var(--mono);
    font-size: 11px;
    color: var(--text-mut);
    line-height: 1.7;
  }

  .header-meta .live {
    display: inline-flex;
    align-items: center;
    gap: 6px;
  }

  .header-meta .dot {
    display: inline-block;
    width: 6px; height: 6px;
    border-radius: 50%;
    background: var(--citrus);
    box-shadow: 0 0 12px var(--citrus);
    animation: pulse 1.6s infinite;
  }

  @keyframes pulse {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.3; }
  }

  /* ============================================
     KPI CARDS
     ============================================ */
  .kpi-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;
    margin-bottom: 40px;
  }

  .kpi {
    background: var(--ink-soft);
    border: 1px solid var(--ink-line);
    border-radius: var(--r-lg);
    padding: 24px;
    position: relative;
    overflow: hidden;
    transition: transform 0.3s, border-color 0.3s;
  }

  .kpi:hover {
    transform: translateY(-2px);
    border-color: var(--moss-bright);
  }

  .kpi::before {
    content: '';
    position: absolute;
    top: 0; left: 0;
    width: 3px; height: 100%;
    background: var(--moss-bright);
  }

  .kpi.accent::before { background: var(--citrus); }
  .kpi.warm::before { background: var(--rust); }
  .kpi.cool::before { background: var(--sky); }

  .kpi-label {
    font-family: var(--mono);
    font-size: 10px;
    text-transform: uppercase;
    letter-spacing: 0.16em;
    color: var(--text-mut);
    margin-bottom: 16px;
  }

  .kpi-value {
    font-family: var(--display);
    font-weight: 300;
    font-size: 56px;
    line-height: 1;
    letter-spacing: -0.04em;
    color: var(--paper);
    margin-bottom: 8px;
  }

  .kpi-value sup {
    font-size: 14px;
    font-weight: 400;
    color: var(--text-mut);
    margin-left: 4px;
    vertical-align: super;
  }

  .kpi-detail {
    font-size: 12px;
    color: var(--text-mut);
  }

  .kpi-detail strong {
    color: var(--paper);
    font-weight: 600;
  }

  /* ============================================
     LAYOUT
     ============================================ */
  .grid-main {
    display: grid;
    grid-template-columns: 1fr 1.2fr;
    gap: 24px;
    margin-bottom: 32px;
  }

  .panel {
    background: var(--ink-soft);
    border: 1px solid var(--ink-line);
    border-radius: var(--r-lg);
    padding: 28px;
  }

  .panel-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 24px;
    padding-bottom: 16px;
    border-bottom: 1px solid var(--ink-line);
  }

  .panel-title {
    font-family: var(--display);
    font-weight: 400;
    font-size: 22px;
    letter-spacing: -0.01em;
  }

  .panel-meta {
    font-family: var(--mono);
    font-size: 10px;
    color: var(--text-mut);
    text-transform: uppercase;
    letter-spacing: 0.14em;
  }

  /* ============================================
     CHART
     ============================================ */
  .chart-wrap {
    height: 280px;
    position: relative;
  }

  /* ============================================
     LISTA CONVERSACIONES
     ============================================ */
  .convos-list {
    max-height: 480px;
    overflow-y: auto;
    padding-right: 8px;
  }

  .convos-list::-webkit-scrollbar { width: 6px; }
  .convos-list::-webkit-scrollbar-track { background: transparent; }
  .convos-list::-webkit-scrollbar-thumb {
    background: var(--ink-line);
    border-radius: 3px;
  }

  .convo-item {
    padding: 16px 0;
    border-bottom: 1px solid var(--ink-line);
    cursor: pointer;
    transition: padding 0.2s, background 0.2s;
    display: grid;
    grid-template-columns: 1fr auto;
    gap: 12px;
    align-items: start;
  }

  .convo-item:hover {
    padding-left: 12px;
    background: rgba(74, 122, 94, 0.06);
  }

  .convo-item:last-child { border-bottom: none; }

  .convo-numero {
    font-family: var(--mono);
    font-size: 12px;
    color: var(--paper);
    font-weight: 500;
    margin-bottom: 6px;
  }

  .convo-preview {
    font-size: 13px;
    color: var(--text-mut);
    overflow: hidden;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    line-height: 1.45;
  }

  .convo-stats {
    text-align: right;
    font-family: var(--mono);
    font-size: 10px;
    color: var(--text-mut);
    line-height: 1.6;
    text-transform: uppercase;
    letter-spacing: 0.06em;
  }

  .convo-stats .turns {
    color: var(--citrus);
    font-weight: 600;
  }

  .badge-deriv {
    display: inline-block;
    background: rgba(196, 90, 61, 0.16);
    color: var(--rust);
    font-family: var(--mono);
    font-size: 9px;
    padding: 2px 6px;
    border-radius: 3px;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    margin-top: 4px;
  }

  /* ============================================
     SEARCH BAR
     ============================================ */
  .filters {
    display: flex;
    gap: 12px;
    margin-bottom: 20px;
  }

  .search-input,
  .filter-select {
    background: var(--ink);
    border: 1px solid var(--ink-line);
    border-radius: var(--r-md);
    color: var(--paper);
    font-family: var(--body);
    font-size: 13px;
    padding: 10px 14px;
    outline: none;
    transition: border-color 0.2s;
  }

  .search-input { flex: 1; }

  .search-input:focus,
  .filter-select:focus {
    border-color: var(--moss-bright);
  }

  .search-input::placeholder {
    color: var(--text-faint);
  }

  /* ============================================
     CONVERSATION FULL TABLE
     ============================================ */
  .full-table {
    background: var(--ink-soft);
    border: 1px solid var(--ink-line);
    border-radius: var(--r-lg);
    overflow: hidden;
  }

  .full-table-header {
    padding: 24px 28px;
    border-bottom: 1px solid var(--ink-line);
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .full-list {
    max-height: 600px;
    overflow-y: auto;
  }

  /* ============================================
     MODAL: CONVERSATION VIEW
     ============================================ */
  .modal {
    position: fixed;
    inset: 0;
    background: rgba(15, 22, 17, 0.85);
    backdrop-filter: blur(8px);
    z-index: 100;
    display: none;
    align-items: center;
    justify-content: center;
    padding: 32px;
  }

  .modal.open {
    display: flex;
    animation: modalFade 0.3s ease-out;
  }

  @keyframes modalFade {
    from { opacity: 0; }
    to { opacity: 1; }
  }

  .modal-card {
    background: var(--paper);
    color: var(--ink);
    width: 100%;
    max-width: 540px;
    height: 90vh;
    border-radius: var(--r-lg);
    overflow: hidden;
    display: flex;
    flex-direction: column;
    box-shadow: 0 30px 80px rgba(0, 0, 0, 0.5);
    animation: modalSlide 0.4s cubic-bezier(0.2, 0.8, 0.2, 1);
  }

  @keyframes modalSlide {
    from { transform: translateY(20px) scale(0.97); opacity: 0; }
    to { transform: translateY(0) scale(1); opacity: 1; }
  }

  .modal-header {
    padding: 16px 20px;
    background: var(--moss);
    color: var(--paper);
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .modal-header .who {
    font-family: var(--mono);
    font-size: 12px;
    font-weight: 500;
  }

  .modal-header .when {
    font-family: var(--mono);
    font-size: 10px;
    opacity: 0.7;
    margin-top: 2px;
  }

  .modal-actions {
    display: flex;
    gap: 8px;
  }

  .icon-btn {
    background: rgba(245, 239, 228, 0.1);
    border: none;
    color: var(--paper);
    width: 32px; height: 32px;
    border-radius: 50%;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 16px;
    transition: background 0.2s;
  }

  .icon-btn:hover { background: rgba(245, 239, 228, 0.2); }

  .modal-body {
    flex: 1;
    overflow-y: auto;
    padding: 20px 16px;
    background:
      linear-gradient(rgba(212, 200, 179, 0.4), rgba(212, 200, 179, 0.4)),
      url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 200 200'%3E%3Cdefs%3E%3Cpattern id='p' width='40' height='40' patternUnits='userSpaceOnUse'%3E%3Cpath d='M0 20 L40 20 M20 0 L20 40' stroke='%23000' stroke-width='0.3' opacity='0.06'/%3E%3C/pattern%3E%3C/defs%3E%3Crect width='200' height='200' fill='url(%23p)'/%3E%3C/svg%3E");
  }

  /* WhatsApp-style bubbles */
  .bubble-row {
    display: flex;
    margin-bottom: 8px;
    animation: bubbleIn 0.3s ease-out;
  }

  @keyframes bubbleIn {
    from { opacity: 0; transform: translateY(8px); }
    to { opacity: 1; transform: translateY(0); }
  }

  .bubble-row.user { justify-content: flex-end; }
  .bubble-row.bot { justify-content: flex-start; }

  .bubble {
    max-width: 75%;
    padding: 8px 12px;
    border-radius: 8px;
    font-size: 14px;
    line-height: 1.4;
    position: relative;
    word-wrap: break-word;
    white-space: pre-wrap;
  }

  .bubble.user {
    background: #d4f4cc;
    color: var(--ink);
    border-bottom-right-radius: 2px;
  }

  .bubble.bot {
    background: #ffffff;
    color: var(--ink);
    border-bottom-left-radius: 2px;
    box-shadow: 0 1px 1px rgba(0,0,0,0.05);
  }

  .bubble .stamp {
    font-family: var(--mono);
    font-size: 9px;
    color: rgba(15, 22, 17, 0.45);
    text-align: right;
    margin-top: 2px;
    user-select: none;
  }

  .bubble.deriv {
    border-left: 3px solid var(--rust);
  }

  /* ============================================
     LOADING & EMPTY STATES
     ============================================ */
  .loading {
    text-align: center;
    padding: 40px;
    color: var(--text-mut);
    font-family: var(--mono);
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 0.15em;
  }

  .loading::after {
    content: '...';
    display: inline-block;
    animation: dots 1.4s infinite;
  }

  @keyframes dots {
    0%, 20% { content: '.'; }
    40% { content: '..'; }
    60%, 100% { content: '...'; }
  }

  .empty {
    text-align: center;
    padding: 60px 20px;
    color: var(--text-mut);
    font-family: var(--display);
    font-style: italic;
    font-size: 18px;
  }

  /* ============================================
     SECCIÓN TÍTULOS
     ============================================ */
  .section-title {
    font-family: var(--display);
    font-weight: 300;
    font-size: 32px;
    letter-spacing: -0.02em;
    margin: 56px 0 20px;
    display: flex;
    align-items: baseline;
    gap: 12px;
  }

  .section-title .num {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--citrus);
    letter-spacing: 0.15em;
  }

  /* ============================================
     SECONDARY ROW (heatmap + breakdown)
     ============================================ */
  .stats-row {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 16px;
    margin-bottom: 32px;
  }

  .stat-mini {
    background: var(--ink-soft);
    border: 1px solid var(--ink-line);
    border-radius: var(--r-md);
    padding: 18px 20px;
  }

  .stat-mini-label {
    font-family: var(--mono);
    font-size: 9px;
    text-transform: uppercase;
    letter-spacing: 0.15em;
    color: var(--text-mut);
    margin-bottom: 10px;
  }

  .stat-mini-value {
    font-family: var(--display);
    font-weight: 400;
    font-size: 28px;
    letter-spacing: -0.02em;
  }

  .stat-mini-bar {
    height: 4px;
    background: var(--ink-line);
    border-radius: 2px;
    margin-top: 10px;
    overflow: hidden;
  }

  .stat-mini-bar > div {
    height: 100%;
    background: var(--moss-bright);
    border-radius: 2px;
    transition: width 0.6s ease-out;
  }

  /* ============================================
     RESPONSIVE
     ============================================ */
  @media (max-width: 900px) {
    .kpi-grid { grid-template-columns: repeat(2, 1fr); }
    .grid-main { grid-template-columns: 1fr; }
    .stats-row { grid-template-columns: 1fr; }
    .app { padding: 20px; }
  }

  @media (max-width: 480px) {
    .kpi-grid { grid-template-columns: 1fr; }
    .header { flex-direction: column; align-items: flex-start; gap: 16px; }
    .header-meta { text-align: left; }
  }
</style>
</head>
<body>

<div class="app">

  <!-- HEADER -->
  <header class="header">
    <div class="brand">
      <h1>Solé <em>·</em> dashboard</h1>
      <div class="sub">Espacio Solé · Bot WhatsApp · Fluxx IA</div>
    </div>
    <div class="header-meta">
      <div class="live"><span class="dot"></span> en vivo · <span id="last-update">cargando</span></div>
      <div id="datetime"></div>
    </div>
  </header>

  <!-- KPIs -->
  <section class="kpi-grid" id="kpi-grid">
    <div class="kpi accent">
      <div class="kpi-label">conversaciones · hoy</div>
      <div class="kpi-value" id="kpi-today">—</div>
      <div class="kpi-detail">de <strong id="kpi-week-detail">—</strong> esta semana</div>
    </div>
    <div class="kpi">
      <div class="kpi-label">total mensajes</div>
      <div class="kpi-value" id="kpi-total-msgs">—</div>
      <div class="kpi-detail"><strong id="kpi-user-msgs">—</strong> usuario · <strong id="kpi-bot-msgs">—</strong> bot</div>
    </div>
    <div class="kpi cool">
      <div class="kpi-label">números únicos</div>
      <div class="kpi-value" id="kpi-unique">—</div>
      <div class="kpi-detail"><strong id="kpi-avg-msgs">—</strong> mensajes promedio por persona</div>
    </div>
    <div class="kpi warm">
      <div class="kpi-label">derivaciones a recepción</div>
      <div class="kpi-value" id="kpi-deriv">—</div>
      <div class="kpi-detail"><strong id="kpi-deriv-pct">—</strong>% del total de respuestas</div>
    </div>
  </section>

  <!-- BREAKDOWN -->
  <section class="stats-row" id="stats-row">
    <div class="stat-mini">
      <div class="stat-mini-label">turno mañana · 6h–14h</div>
      <div class="stat-mini-value" id="stat-morning">—</div>
      <div class="stat-mini-bar"><div id="bar-morning" style="width:0%"></div></div>
    </div>
    <div class="stat-mini">
      <div class="stat-mini-label">turno tarde · 14h–20h</div>
      <div class="stat-mini-value" id="stat-afternoon">—</div>
      <div class="stat-mini-bar"><div id="bar-afternoon" style="width:0%"></div></div>
    </div>
    <div class="stat-mini">
      <div class="stat-mini-label">turno noche · 20h–6h</div>
      <div class="stat-mini-value" id="stat-night">—</div>
      <div class="stat-mini-bar"><div id="bar-night" style="width:0%"></div></div>
    </div>
  </section>

  <!-- CHART + RECENT -->
  <section class="grid-main">
    <div class="panel">
      <div class="panel-header">
        <div class="panel-title">Actividad · últimos 14 días</div>
        <div class="panel-meta">mensajes / día</div>
      </div>
      <div class="chart-wrap">
        <canvas id="chartActivity"></canvas>
      </div>
    </div>

    <div class="panel">
      <div class="panel-header">
        <div class="panel-title">Conversaciones recientes</div>
        <div class="panel-meta" id="recent-count">—</div>
      </div>
      <div class="convos-list" id="recent-list">
        <div class="loading">conectando con supabase</div>
      </div>
    </div>
  </section>

  <!-- FULL LIST -->
  <h2 class="section-title">
    <span class="num">02</span>
    Todas las conversaciones
  </h2>

  <div class="filters">
    <input type="text" class="search-input" id="search-input"
           placeholder="Buscar por número o por contenido del mensaje…">
    <select class="filter-select" id="filter-period">
      <option value="all">Todo el tiempo</option>
      <option value="today">Hoy</option>
      <option value="week">Esta semana</option>
      <option value="month">Este mes</option>
    </select>
    <select class="filter-select" id="filter-deriv">
      <option value="all">Todas</option>
      <option value="deriv">Solo con derivación</option>
      <option value="no-deriv">Sin derivación</option>
    </select>
  </div>

  <div class="full-table">
    <div class="full-list" id="full-list">
      <div class="loading">cargando</div>
    </div>
  </div>
</div>

<!-- MODAL: CONVERSACIÓN COMPLETA -->
<div class="modal" id="modal">
  <div class="modal-card">
    <div class="modal-header">
      <div>
        <div class="who" id="modal-numero">—</div>
        <div class="when" id="modal-info">—</div>
      </div>
      <div class="modal-actions">
        <button class="icon-btn" id="btn-copy" title="Copiar conversación">⎘</button>
        <button class="icon-btn" id="btn-close" title="Cerrar">✕</button>
      </div>
    </div>
    <div class="modal-body" id="modal-body"></div>
  </div>
</div>

<script>
// ============================================
// CONFIG
// ============================================
const SUPABASE_URL = 'https://dskwidhrcrumqqdzncen.supabase.co';
const SUPABASE_KEY = 'sb_publishable_CaeM1Wf39mlukguZi14R3g_h79RB1Vs';

const supa = supabase.createClient(SUPABASE_URL, SUPABASE_KEY);

// Frase exacta de derivación (desde el system prompt)
const FRASE_DERIVACION = 'te paso con recepción';

// Estado global
let allMessages = [];
let allConvos = [];
let filtered = [];

// ============================================
// INIT
// ============================================
async function init() {
  updateDateTime();
  setInterval(updateDateTime, 1000);

  await loadData();
  renderAll();

  // refresh cada 60s
  setInterval(async () => {
    await loadData();
    renderAll();
  }, 60000);
}

function updateDateTime() {
  const d = new Date();
  const opts = { weekday: 'long', day: 'numeric', month: 'long', hour: '2-digit', minute: '2-digit' };
  document.getElementById('datetime').textContent = d.toLocaleString('es-AR', opts);
  document.getElementById('last-update').textContent = d.toLocaleTimeString('es-AR', { hour: '2-digit', minute: '2-digit', second: '2-digit' });
}

// ============================================
// CARGA DE DATOS
// ============================================
async function loadData() {
  const { data, error } = await supa
    .from('conversaciones')
    .select('*')
    .order('created_at', { ascending: true });

  if (error) {
    console.error('Error:', error);
    document.getElementById('recent-list').innerHTML = `<div class="empty">Error: ${error.message}</div>`;
    return;
  }

  allMessages = data || [];
  allConvos = groupByConversations(allMessages);
}

// Agrupa mensajes en conversaciones (por número, separadas por gaps de 1h+)
function groupByConversations(msgs) {
  const byNumero = {};
  msgs.forEach(m => {
    if (!byNumero[m.numero]) byNumero[m.numero] = [];
    byNumero[m.numero].push(m);
  });

  const convos = [];
  Object.entries(byNumero).forEach(([numero, items]) => {
    items.sort((a, b) => new Date(a.created_at) - new Date(b.created_at));
    let current = null;
    items.forEach(m => {
      const t = new Date(m.created_at);
      if (!current || (t - new Date(current.last)) > 60 * 60 * 1000) {
        if (current) convos.push(current);
        current = {
          numero,
          start: m.created_at,
          last: m.created_at,
          messages: [m],
          turns_user: m.role === 'user' ? 1 : 0,
          turns_bot: m.role === 'assistant' ? 1 : 0,
          has_deriv: m.role === 'assistant' && m.content.toLowerCase().includes(FRASE_DERIVACION)
        };
      } else {
        current.messages.push(m);
        current.last = m.created_at;
        if (m.role === 'user') current.turns_user++;
        else current.turns_bot++;
        if (m.role === 'assistant' && m.content.toLowerCase().includes(FRASE_DERIVACION)) {
          current.has_deriv = true;
        }
      }
    });
    if (current) convos.push(current);
  });

  // ordenar por más reciente primero
  return convos.sort((a, b) => new Date(b.last) - new Date(a.last));
}

// ============================================
// RENDER
// ============================================
function renderAll() {
  renderKPIs();
  renderTimeStats();
  renderChart();
  renderRecent();
  applyFiltersAndRender();
}

function renderKPIs() {
  const now = new Date();
  const todayStr = now.toISOString().slice(0, 10);
  const weekAgo = new Date(now.getTime() - 7 * 24 * 3600 * 1000);

  // Hoy
  const todayConvos = allConvos.filter(c => c.start.slice(0, 10) === todayStr);
  const weekConvos = allConvos.filter(c => new Date(c.start) >= weekAgo);

  document.getElementById('kpi-today').textContent = todayConvos.length;
  document.getElementById('kpi-week-detail').textContent = weekConvos.length;

  // Total mensajes
  const userMsgs = allMessages.filter(m => m.role === 'user').length;
  const botMsgs = allMessages.filter(m => m.role === 'assistant').length;
  document.getElementById('kpi-total-msgs').textContent = (userMsgs + botMsgs).toLocaleString('es-AR');
  document.getElementById('kpi-user-msgs').textContent = userMsgs;
  document.getElementById('kpi-bot-msgs').textContent = botMsgs;

  // Únicos
  const numeros = new Set(allMessages.map(m => m.numero));
  document.getElementById('kpi-unique').textContent = numeros.size;
  const avg = numeros.size ? (allMessages.length / numeros.size).toFixed(1) : '0';
  document.getElementById('kpi-avg-msgs').textContent = avg;

  // Derivaciones
  const derivs = allMessages.filter(m =>
    m.role === 'assistant' && m.content.toLowerCase().includes(FRASE_DERIVACION)
  ).length;
  document.getElementById('kpi-deriv').textContent = derivs;
  const pct = botMsgs ? ((derivs / botMsgs) * 100).toFixed(1) : '0';
  document.getElementById('kpi-deriv-pct').textContent = pct;
}

function renderTimeStats() {
  const buckets = { morning: 0, afternoon: 0, night: 0 };
  allMessages.forEach(m => {
    const h = new Date(m.created_at).getHours();
    if (h >= 6 && h < 14) buckets.morning++;
    else if (h >= 14 && h < 20) buckets.afternoon++;
    else buckets.night++;
  });

  const total = buckets.morning + buckets.afternoon + buckets.night || 1;

  document.getElementById('stat-morning').textContent = buckets.morning;
  document.getElementById('stat-afternoon').textContent = buckets.afternoon;
  document.getElementById('stat-night').textContent = buckets.night;

  document.getElementById('bar-morning').style.width = `${(buckets.morning / total) * 100}%`;
  document.getElementById('bar-afternoon').style.width = `${(buckets.afternoon / total) * 100}%`;
  document.getElementById('bar-night').style.width = `${(buckets.night / total) * 100}%`;
}

let chartInstance = null;
function renderChart() {
  const ctx = document.getElementById('chartActivity').getContext('2d');
  const days = 14;
  const now = new Date();
  now.setHours(0,0,0,0);

  const labels = [];
  const dataUser = [];
  const dataBot = [];

  for (let i = days - 1; i >= 0; i--) {
    const d = new Date(now.getTime() - i * 24 * 3600 * 1000);
    const ds = d.toISOString().slice(0,10);
    labels.push(d.toLocaleDateString('es-AR', { day: 'numeric', month: 'short' }));
    dataUser.push(allMessages.filter(m => m.created_at.slice(0,10) === ds && m.role === 'user').length);
    dataBot.push(allMessages.filter(m => m.created_at.slice(0,10) === ds && m.role === 'assistant').length);
  }

  if (chartInstance) chartInstance.destroy();

  chartInstance = new Chart(ctx, {
    type: 'bar',
    data: {
      labels,
      datasets: [
        { label: 'Usuario', data: dataUser, backgroundColor: 'rgba(212, 255, 90, 0.85)', borderRadius: 4 },
        { label: 'Bot', data: dataBot, backgroundColor: 'rgba(74, 122, 94, 0.85)', borderRadius: 4 }
      ]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      plugins: {
        legend: {
          labels: {
            color: '#f5efe4',
            font: { family: 'JetBrains Mono', size: 10 },
            usePointStyle: true,
            pointStyle: 'rect'
          }
        }
      },
      scales: {
        x: {
          grid: { display: false },
          ticks: { color: 'rgba(245,239,228,0.5)', font: { family: 'JetBrains Mono', size: 9 } }
        },
        y: {
          grid: { color: 'rgba(245,239,228,0.06)' },
          ticks: { color: 'rgba(245,239,228,0.5)', font: { family: 'JetBrains Mono', size: 9 }, precision: 0 }
        }
      }
    }
  });
}

function renderRecent() {
  const list = document.getElementById('recent-list');
  const recent = allConvos.slice(0, 12);

  document.getElementById('recent-count').textContent = `${recent.length} de ${allConvos.length}`;

  if (recent.length === 0) {
    list.innerHTML = '<div class="empty">No hay conversaciones aún</div>';
    return;
  }

  list.innerHTML = recent.map((c, idx) => renderConvoItem(c, idx)).join('');
  bindConvoClicks(list);
}

function renderConvoItem(c, idx) {
  const numeroFmt = formatNumero(c.numero);
  const lastMsg = c.messages[c.messages.length - 1];
  const preview = lastMsg.content.replace(/\n/g, ' ').slice(0, 90);
  const start = new Date(c.start);
  const timeStr = start.toLocaleString('es-AR', {
    day: '2-digit', month: '2-digit', hour: '2-digit', minute: '2-digit'
  });
  const totalTurns = c.turns_user;

  return `
    <div class="convo-item" data-idx="${idx}" data-key="${c.numero}|${c.start}">
      <div>
        <div class="convo-numero">${numeroFmt}</div>
        <div class="convo-preview">${escapeHtml(preview)}${preview.length === 90 ? '…' : ''}</div>
        ${c.has_deriv ? '<span class="badge-deriv">↗ derivación</span>' : ''}
      </div>
      <div class="convo-stats">
        <div><span class="turns">${totalTurns}</span> turnos</div>
        <div>${timeStr}</div>
      </div>
    </div>
  `;
}

function bindConvoClicks(container) {
  container.querySelectorAll('.convo-item').forEach(el => {
    el.addEventListener('click', () => {
      const key = el.dataset.key;
      const c = filtered.find(x => `${x.numero}|${x.start}` === key) ||
                allConvos.find(x => `${x.numero}|${x.start}` === key);
      if (c) openModal(c);
    });
  });
}

// ============================================
// FILTROS Y FULL LIST
// ============================================
function applyFiltersAndRender() {
  const q = (document.getElementById('search-input').value || '').toLowerCase().trim();
  const period = document.getElementById('filter-period').value;
  const derivF = document.getElementById('filter-deriv').value;

  const now = new Date();
  let from = null;
  if (period === 'today') {
    from = new Date(now); from.setHours(0,0,0,0);
  } else if (period === 'week') {
    from = new Date(now.getTime() - 7 * 24 * 3600 * 1000);
  } else if (period === 'month') {
    from = new Date(now.getTime() - 30 * 24 * 3600 * 1000);
  }

  filtered = allConvos.filter(c => {
    if (from && new Date(c.start) < from) return false;
    if (derivF === 'deriv' && !c.has_deriv) return false;
    if (derivF === 'no-deriv' && c.has_deriv) return false;
    if (q) {
      const inNumero = c.numero.includes(q);
      const inMsgs = c.messages.some(m => m.content.toLowerCase().includes(q));
      if (!inNumero && !inMsgs) return false;
    }
    return true;
  });

  const list = document.getElementById('full-list');
  if (filtered.length === 0) {
    list.innerHTML = '<div class="empty">No se encontraron conversaciones</div>';
    return;
  }

  list.innerHTML = filtered.map((c, idx) => renderConvoItem(c, idx)).join('');
  bindConvoClicks(list);
}

// ============================================
// MODAL
// ============================================
function openModal(convo) {
  const modal = document.getElementById('modal');
  document.getElementById('modal-numero').textContent = formatNumero(convo.numero);
  const start = new Date(convo.start);
  const end = new Date(convo.last);
  const duration = Math.round((end - start) / 60000);
  document.getElementById('modal-info').textContent =
    `${start.toLocaleString('es-AR', { day: '2-digit', month: 'short', hour: '2-digit', minute: '2-digit' })} · ${convo.messages.length} mensajes · ${duration} min`;

  const body = document.getElementById('modal-body');
  body.innerHTML = convo.messages.map(m => {
    const role = m.role === 'user' ? 'user' : 'bot';
    const t = new Date(m.created_at);
    const stamp = t.toLocaleTimeString('es-AR', { hour: '2-digit', minute: '2-digit' });
    const isDeriv = m.role === 'assistant' && m.content.toLowerCase().includes(FRASE_DERIVACION);
    return `
      <div class="bubble-row ${role}">
        <div class="bubble ${role} ${isDeriv ? 'deriv' : ''}">${escapeHtml(m.content)}<div class="stamp">${stamp}</div></div>
      </div>
    `;
  }).join('');

  body.scrollTop = 0;
  modal.classList.add('open');

  // copy button
  document.getElementById('btn-copy').onclick = () => {
    const text = convo.messages.map(m => {
      const who = m.role === 'user' ? 'Cliente' : 'Solé';
      const t = new Date(m.created_at).toLocaleTimeString('es-AR', { hour: '2-digit', minute: '2-digit' });
      return `[${t}] ${who}: ${m.content}`;
    }).join('\n');
    navigator.clipboard.writeText(text);
    const btn = document.getElementById('btn-copy');
    const orig = btn.textContent;
    btn.textContent = '✓';
    setTimeout(() => { btn.textContent = orig; }, 1200);
  };
}

document.getElementById('btn-close').onclick = () => {
  document.getElementById('modal').classList.remove('open');
};

document.getElementById('modal').onclick = (e) => {
  if (e.target.id === 'modal') {
    document.getElementById('modal').classList.remove('open');
  }
};

document.addEventListener('keydown', (e) => {
  if (e.key === 'Escape') document.getElementById('modal').classList.remove('open');
});

// ============================================
// FILTROS BIND
// ============================================
['search-input', 'filter-period', 'filter-deriv'].forEach(id => {
  document.getElementById(id).addEventListener('input', applyFiltersAndRender);
  document.getElementById(id).addEventListener('change', applyFiltersAndRender);
});

// ============================================
// HELPERS
// ============================================
function formatNumero(n) {
  // 5492213036756 → +54 9 221 303-6756
  if (!n) return '';
  const s = n.toString();
  if (s.length === 13 && s.startsWith('549')) {
    return `+54 9 ${s.slice(3,6)} ${s.slice(6,9)}-${s.slice(9)}`;
  }
  return n;
}

function escapeHtml(s) {
  if (!s) return '';
  return s.replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;').replace(/"/g, '&quot;');
}

// ============================================
// GO
// ============================================
init();
</script>

</body>
</html>
