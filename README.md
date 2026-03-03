<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>STARCK EMPIRE OS · SON À CHAQUE VENTE</title>
    <link rel="preconnect" href="https://fonts.googleapis.com" crossorigin>
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,400;0,14..32,500;0,14..32,600;0,14..32,700;1,14..32,400&display=swap" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; }
        body, html {
            background: #000;
            color: #f0f0f2;
            font-family: "Inter", -apple-system, sans-serif;
            font-size: 14px;
            height: 100%;
            width: 100%;
            overflow: hidden;
            -webkit-font-smoothing: antialiased;
        }

        /* NOTIFICATIONS */
        .notification-container {
            position: fixed; top: 20px; right: 20px; z-index: 10000;
            display: flex; flex-direction: column; gap: 10px; pointer-events: none;
        }
        .notification {
            background: rgba(20,30,20,0.95); backdrop-filter: blur(20px);
            border-left: 4px solid #4caf50; border-radius: 16px; padding: 16px 20px;
            width: 300px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); color: white;
            transform: translateX(120%); animation: slideIn 0.3s forwards, fadeOut 0.5s 4.5s forwards;
        }
        @keyframes slideIn { to { transform: translateX(0); } }
        @keyframes fadeOut { to { opacity: 0; transform: translateX(20px); } }

        /* SYNC BAR */
        #syncBarContainer {
            position: fixed; top: 0; left: 0; width: 100%; height: 3px;
            background: rgba(0,30,0,0.2); z-index: 1000;
        }
        #syncBar {
            height: 100%; width: 0%; background: #4caf50;
            box-shadow: 0 0 20px #00ff88; transition: width 0.1s linear;
        }

        /* HEADER */
        .header {
            padding: 18px 24px 8px;
            display: flex; align-items: center; justify-content: space-between; flex-wrap: wrap; gap: 12px;
        }
        .logo-area {
            display: flex; align-items: center; gap: 18px; flex-wrap: wrap;
        }
        .logo {
            font-size: 22px; font-weight: 700; letter-spacing: -0.5px;
            background: linear-gradient(135deg, #fff 30%, #4caf50);
            -webkit-background-clip: text; -webkit-text-fill-color: transparent;
            cursor: pointer;
        }
        .server-status {
            display: flex; align-items: center; gap: 8px;
            background: rgba(10,10,15,0.6); backdrop-filter: blur(10px);
            padding: 6px 14px; border-radius: 40px; border: 0.5px solid #1e3a2a;
            font-size: 12px; font-weight: 500;
        }
        .status-led {
            width: 10px; height: 10px; background: #4caf50; border-radius: 50%;
            box-shadow: 0 0 12px #4caf50; animation: pulse 1.8s infinite;
        }
        @keyframes pulse { 0% { opacity: 1; } 50% { opacity: 0.4; } }

        .founder-badge {
            display: flex; align-items: center; gap: 8px;
            background: rgba(30,40,30,0.7); backdrop-filter: blur(10px);
            padding: 6px 14px; border-radius: 40px; border: 0.5px solid #3a5a3a;
            font-size: 12px;
        }
        .founder-avatar {
            width: 24px; height: 24px; border-radius: 50%;
            background: linear-gradient(135deg, #4caf50, #1a3a1a);
            display: flex; align-items: center; justify-content: center;
            color: white; font-weight: 600; font-size: 12px;
        }
        .right-header {
            display: flex; align-items: center; gap: 12px;
        }

        /* SSL BADGE */
        .ssl-badge {
            position: fixed; bottom: 90px; right: 20px;
            background: rgba(20,30,20,0.7); backdrop-filter: blur(10px);
            padding: 8px 16px; border-radius: 40px; border: 0.5px solid #2a4a2a;
            font-size: 11px; color: #8bc34a; display: flex; align-items: center; gap: 6px; z-index: 99;
        }

        /* MAIN CONTENT */
        #app {
            height: 100%;
            display: flex;
            flex-direction: column;
            padding-bottom: 82px;
            position: relative;
        }
        .main-content {
            flex: 1;
            overflow-y: auto;
            padding: 10px 16px;
        }
        .page { display: none; }
        .page.active { display: block; }

        /* WIDGETS */
        .widget-grid {
            display: grid; grid-template-columns: repeat(2, 1fr);
            gap: 14px; margin-bottom: 20px;
        }
        .stat-widget {
            background: rgba(10,12,14,0.7); backdrop-filter: blur(16px);
            border-radius: 28px; padding: 20px; border: 0.5px solid #1A2A1A;
            cursor: pointer; transition: transform 0.1s;
        }
        .stat-widget:active { transform: scale(0.98); border-color: #4caf50; }
        .stat-label { font-size: 11px; color: #a0e0a0; text-transform: uppercase; margin-bottom: 8px; }
        .stat-value { font-size: 28px; font-weight: 600; color: white; }

        .glass-card {
            background: rgba(10,12,14,0.7); backdrop-filter: blur(16px);
            border-radius: 28px; border: 0.5px solid #1A2A1A;
            padding: 20px; margin-bottom: 16px;
        }
        .chart-header {
            display: flex; justify-content: space-between; align-items: center;
            margin-bottom: 12px; cursor: pointer;
        }
        .chart-title { font-weight: 600; }
        .chart-expand { color: #8bc34a; font-size: 12px; }
        .chart-container { position: relative; height: 140px; width: 100%; }

        /* MODALE PRÉCISE */
        .precise-modal {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(5,5,8,0.98);
            backdrop-filter: blur(30px);
            border-radius: 48px;
            border: 1px solid #2a4a2a;
            padding: 40px;
            z-index: 10000;
            display: none;
            flex-direction: column;
            align-items: center;
            min-width: 400px;
            box-shadow: 0 0 60px #000;
        }
        .precise-title {
            color: #8bc34a;
            font-size: 14px;
            text-transform: uppercase;
            letter-spacing: 2px;
            margin-bottom: 20px;
        }
        .precise-value {
            font-size: 56px;
            font-weight: 700;
            color: white;
            margin: 20px 0;
        }
        .precise-sub {
            color: #6a6a6a;
            font-size: 16px;
        }
        .precise-close {
            position: absolute;
            top: 20px;
            right: 30px;
            font-size: 32px;
            color: #4caf50;
            cursor: pointer;
        }

        /* LEDGER */
        .ledger-full {
            display: flex;
            flex-direction: column;
            gap: 16px;
        }
        .ledger-preview {
            background: rgba(5,5,8,0.75); backdrop-filter: blur(20px);
            border-radius: 28px; padding: 20px; border: 0.5px solid #1A2A1A;
        }
        .preview-header {
            display: flex; justify-content: space-between;
            color: #8bc34a; font-weight: 600; margin-bottom: 15px;
            text-transform: uppercase; letter-spacing: 1px;
        }
        .preview-lines {
            font-family: 'Inter', monospace; font-size: 12px;
            display: flex; flex-direction: column; gap: 10px;
            max-height: 200px;
            overflow-y: auto;
        }
        .preview-line {
            padding: 8px 12px; background: rgba(20,30,20,0.3);
            border-radius: 16px; border-left: 3px solid #4caf50;
        }
        .preview-time { color: #88ff88; font-weight: 500; }
        .preview-amount { color: #fff; font-weight: 600; }

        .stats-mini {
            display: flex;
            justify-content: space-between;
            margin: 15px 0 10px 0;
            padding: 12px;
            background: rgba(0,0,0,0.3);
            border-radius: 20px;
            font-size: 13px;
        }
        .stats-mini span { color: #8bc34a; font-weight: 600; }

        .history-button {
            background: #1a2a1a; border: 1px solid #3a5a3a; border-radius: 40px;
            padding: 15px; text-align: center; font-weight: 600; color: #8bc34a;
            cursor: pointer; transition: all 0.2s; margin-top: 10px;
        }

        /* SIMULATION */
        .simulation-controls {
            display: flex; flex-direction: column; gap: 20px; margin-bottom: 24px;
        }
        .slider-container {
            display: flex; flex-direction: column; gap: 8px;
        }
        .slider-label {
            display: flex; justify-content: space-between; color: #8bc34a;
        }
        input[type=range] {
            width: 100%; height: 6px; background: #1a2a1a; border-radius: 10px;
            -webkit-appearance: none; appearance: none;
        }
        input[type=range]::-webkit-slider-thumb {
            -webkit-appearance: none; width: 20px; height: 20px;
            background: #4caf50; border-radius: 50%; cursor: pointer;
            box-shadow: 0 0 10px #4caf50;
        }
        .growth-badge {
            padding: 12px 20px; border-radius: 40px; text-align: center;
            font-weight: 700; font-size: 16px; transition: all 0.3s;
        }
        .badge-stable { background: #1a2a1a; color: #8bc34a; border: 1px solid #3a5a3a; }
        .badge-aggressive { background: #2a3a1a; color: #ffaa33; border: 1px solid #cc8800; box-shadow: 0 0 15px #ffaa33; }
        .badge-hyper { background: #3a1a1a; color: #ff5555; border: 1px solid #ff0000; box-shadow: 0 0 20px #ff0000; animation: pulseRed 1.5s infinite; }
        @keyframes pulseRed { 0% { box-shadow: 0 0 20px #ff0000; } 50% { box-shadow: 0 0 40px #ff0000; } }

        .projection-table {
            width: 100%; border-collapse: collapse; margin: 20px 0;
            font-size: 12px;
        }
        .projection-table th {
            text-align: left; padding: 12px 8px; color: #8bc34a;
            border-bottom: 1px solid #2a4a2a; font-weight: 600;
        }
        .projection-table td {
            padding: 10px 8px; border-bottom: 1px solid #1a3a1a;
        }

        /* TAB BAR */
        .tab-bar {
            position: fixed; bottom: 0; left: 0; width: 100%; height: 82px;
            background: rgba(0,0,0,0.8); backdrop-filter: blur(30px);
            border-top: 1px solid #1A2A1A;
            display: flex; justify-content: space-around; align-items: center;
            padding-bottom: 15px; z-index: 100;
        }
        .tab-item {
            display: flex; flex-direction: column; align-items: center; gap: 4px;
            color: #6a6a6a; font-size: 10px; font-weight: 500; cursor: pointer;
        }
        .tab-item.active { color: #4caf50; }
        .tab-item i { font-size: 20px; font-style: normal; }

        /* MODALES */
        .fullscreen-modal {
            position: fixed; top:0; left:0; width:100%; height:100%;
            background: rgba(0,0,0,0.95); backdrop-filter: blur(30px);
            z-index: 5000; display: none; flex-direction: column;
            padding: 30px 24px;
        }

        /* TEAM */
        .employee-row {
            display: flex; align-items: center; gap: 14px; padding: 12px 16px;
            background: rgba(20,30,20,0.5); border-radius: 24px;
            margin-bottom: 8px; border: 1px solid #1A2A1A; cursor: pointer;
        }
        .avatar { width: 44px; height: 44px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-weight: 600; color: white; }

        /* BOOST */
        .empire-boost {
            background: rgba(20,30,20,0.9); backdrop-filter: blur(20px);
            border-radius: 28px; padding: 20px; margin-top: 20px;
            border: 1px solid #2a4a2a; display: none;
        }
        .empire-boost.visible { display: block; }
        .toggle-switch {
            width: 44px; height: 24px; background: #333; border-radius: 30px;
            position: relative; cursor: pointer;
        }
        .toggle-switch.active { background: #4caf50; }
        .toggle-knob {
            width: 20px; height: 20px; background: white; border-radius: 50%;
            position: absolute; top: 2px; left: 2px; transition: transform 0.2s;
        }
        .toggle-switch.active .toggle-knob { transform: translateX(20px); }
    </style>
</head>
<body>
<div id="syncBarContainer"><div id="syncBar"></div></div>
<div id="notificationContainer" class="notification-container"></div>

<!-- MODALE PRÉCISE -->
<div id="preciseModal" class="precise-modal">
    <span class="precise-close" onclick="closePreciseModal()">✕</span>
    <div class="precise-title" id="preciseTitle">CHIFFRE D'AFFAIRES</div>
    <div class="precise-value" id="preciseValue">0.00€</div>
    <div class="precise-sub" id="preciseSub">Mise à jour en temps réel</div>
</div>

<div id="app">
    <div class="header">
        <div class="logo-area">
            <span class="logo" id="logoTrigger">STARCK EMPIRE OS</span>
            <div class="server-status"><span class="status-led"></span><span>SERVER STATUS: OPTIMAL</span></div>
        </div>
        <div class="right-header">
            <div class="founder-badge">
                <span class="founder-avatar">JS</span>
                <span>ADMIN: STARCK (Founder)</span>
            </div>
            <span id="liveDate" style="color:#8bc34a; font-size:13px;"></span>
        </div>
    </div>

    <div class="main-content" id="mainContent">
        <!-- DASHBOARD -->
        <div id="dashboardPage" class="page active">
            <div class="widget-grid">
                <div class="stat-widget" onclick="showPreciseModal('ca')">
                    <div class="stat-label">CA Quotidien</div>
                    <div class="stat-value" id="caTotal">-</div>
                </div>
                <div class="stat-widget" onclick="showPreciseModal('va')">
                    <div class="stat-label">Marge Brute (55%)</div>
                    <div class="stat-value" id="vaValue">-</div>
                </div>
                <div class="stat-widget" onclick="showPreciseModal('net')">
                    <div class="stat-label">EBITDA (Estimé)</div>
                    <div class="stat-value" id="netRevenue">-</div>
                </div>
                <div class="stat-widget" onclick="showPreciseModal('burn')">
                    <div class="stat-label">OPEX (Daily)</div>
                    <div class="stat-value" id="burnRate">0.32M€</div>
                </div>
            </div>
            
            <div class="glass-card" style="padding: 16px;">
                <div style="display: flex; justify-content: space-between;"><span>👥 18,400 Human Closers</span><span style="color:#8bc34a">● actifs</span></div>
                <div style="display: flex; justify-content: space-between; margin-top: 10px;"><span>🤖 2,168 Neural Nodes</span><span style="color:#6f6">IA Active</span></div>
            </div>

            <!-- COURBE CROISSANCE -->
            <div class="glass-card">
                <div class="chart-header" onclick="showFullscreenChart('growth')">
                    <span class="chart-title">📊 REVENUE STREAM</span>
                    <span class="chart-expand">⛶</span>
                </div>
                <div class="chart-container"><canvas id="growthChart"></canvas></div>
            </div>

            <!-- COURBE PERFORMANCE -->
            <div class="glass-card">
                <div class="chart-header" onclick="showFullscreenChart('perf')">
                    <span class="chart-title">📈 PERFORMANCE vs TARGET</span>
                    <span class="chart-expand">⛶</span>
                </div>
                <div class="chart-container"><canvas id="perfChart"></canvas></div>
            </div>
        </div>

        <!-- FLUX -->
        <div id="fluxPage" class="page">
            <div class="ledger-full">
                <div class="ledger-preview">
                    <div class="preview-header">
                        <span>📡 TRANSACTIONS EN DIRECT</span>
                        <span style="color:#6f6;">⏳ streaming</span>
                    </div>
                    <div class="preview-lines" id="previewLines"></div>
                    
                    <div class="stats-mini">
                        <div>Transactions/h: <span id="txPerHour">~1,440</span></div>
                        <div>Volume moyen: <span id="avgAmount">1,422€</span></div>
                    </div>
                    
                    <div class="history-button" onclick="openHistoryModal()">📋 VOIR TOUT L'HISTORIQUE</div>
                </div>
            </div>
        </div>

        <!-- TEAM -->
        <div id="teamPage" class="page"><div id="virtualContainer"></div></div>

        <!-- EMPIRE -->
        <div id="financesPage" class="page">
            <div class="glass-card">
                <h3 style="margin-bottom:15px;">⚙️ OPEX & STRUCTURE</h3>
                <div style="display:flex; justify-content:space-between; padding:10px 0;"><span>Human Closers (18,400)</span><span>210k€/j</span></div>
                <div style="display:flex; justify-content:space-between; padding:10px 0;"><span>Neural Nodes (2,168)</span><span>92k€/j</span></div>
                <div style="display:flex; justify-content:space-between; padding:10px 0;"><span>Infra/Serveurs</span><span>14k€/j</span></div>
                <div style="display:flex; justify-content:space-between; padding:10px 0; border-top:1px solid #2a4a2a;"><span>Total OPEX</span><span id="opexDetail">316k€</span></div>
            </div>
            <div id="empireBoostSection" class="empire-boost">
                <h4 style="color:#8bc34a;">⚡ BOOST x1500</h4>
                <p style="color:#aaa; font-size:12px;">Mode 2ms (1500x) + Flux accéléré</p>
                <div style="display:flex; align-items:center; justify-content:space-between;">
                    <span>Turbo x1500</span>
                    <div class="toggle-switch" id="secretBoostToggle"><div class="toggle-knob"></div></div>
                </div>
            </div>
        </div>

        <!-- SAAS -->
        <div id="saasPage" class="page">
            <div class="glass-card">
                <div style="text-align:center; font-size:14px; color:#8bc34a; margin-bottom:20px;">MONTHLY RECURRING REVENUE</div>
                <div style="font-size:48px; font-weight:700; text-align:center; color:white;" id="mrrDisplay">€48.24M</div>
                <div style="display:flex; justify-content:center; gap:40px; margin:30px 0;">
                    <div><div style="font-size:32px; font-weight:700;">984,560</div><div style="color:#888;">SITES WEB</div></div>
                    <div><div style="font-size:32px; font-weight:700;">49€</div><div style="color:#888;">PRIX MOYEN</div></div>
                </div>
                <div class="chart-container" style="height:200px;"><canvas id="subscribersChart"></canvas></div>
            </div>
        </div>

        <!-- SIMULATION -->
        <div id="simulationPage" class="page">
            <div class="glass-card">
                <h3 style="margin-bottom:20px;">📈 HYPER-CROISSANCE · PROJECTION 12 MOIS</h3>
                <div class="simulation-controls">
                    <div class="slider-container">
                        <div class="slider-label">
                            <span>Taux de Recrutement Mensuel</span>
                            <span id="recruitmentRateDisplay">60%</span>
                        </div>
                        <input type="range" id="recruitmentSlider" min="0" max="120" value="60" step="1">
                    </div>
                    <div id="growthStatusBadge" class="growth-badge badge-aggressive">Aggressive Scale</div>
                </div>
                <div class="chart-container" style="height:250px;"><canvas id="projectionChart"></canvas></div>
                <div style="max-height:300px; overflow-y:auto;">
                    <table class="projection-table" id="projectionTable">
                        <thead><tr><th>Mois</th><th>Effectif</th><th>Ventes (€)</th><th>SaaS (€)</th><th>Total (€)</th></tr></thead>
                        <tbody id="tableBody"></tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

    <!-- TAB BAR 6 ONGLETS -->
    <div class="tab-bar">
        <div class="tab-item active" data-page="dashboard"><i>📊</i><span>Dashboard</span></div>
        <div class="tab-item" data-page="flux"><i>⚡</i><span>Flux</span></div>
        <div class="tab-item" data-page="team"><i>👥</i><span>Team</span></div>
        <div class="tab-item" data-page="finances"><i>💰</i><span>Empire</span></div>
        <div class="tab-item" data-page="saas"><i>💳</i><span>SaaS</span></div>
        <div class="tab-item" data-page="simulation"><i>📈</i><span>Simulation</span></div>
    </div>
</div>

<div class="ssl-badge"><i>🔒</i> SECURED SSL · OFFSHORE PROTECTED</div>

<!-- MODALES -->
<div id="fullscreenGrowth" class="fullscreen-modal"><div class="fullscreen-header"><span class="fullscreen-title">REVENUE STREAM</span><span class="fullscreen-close" onclick="closeFullscreen('growth')">✕</span></div><div class="fullscreen-chart"><canvas id="fullscreenGrowthChart"></canvas></div></div>
<div id="fullscreenPerf" class="fullscreen-modal"><div class="fullscreen-header"><span class="fullscreen-title">PERFORMANCE vs TARGET</span><span class="fullscreen-close" onclick="closeFullscreen('perf')">✕</span></div><div class="fullscreen-chart"><canvas id="fullscreenPerfChart"></canvas></div></div>
<div id="fullscreenWidget" class="fullscreen-modal">...</div>
<div id="historyModal" class="fullscreen-modal"><div class="fullscreen-header"><span>📋 HISTORIQUE</span><span onclick="closeHistoryModal()">✕</span></div><div id="historyList" style="color:#b0ffb0;"></div></div>
<div id="secretModal" class="fullscreen-modal" style="justify-content:center; align-items:center;"><div style="background:rgba(10,20,10,0.9); padding:30px; border-radius:48px;"><input type="password" id="secretCode" placeholder="Code"><button id="applySecret">OK</button></div></div>
<div id="employeeDetailModal" class="fullscreen-modal"><div onclick="closeEmployeeModal()">←</div><div id="detName"></div></div>

<script>
(function() {
    // ========== CONSTANTES ==========
    const START_DATE = new Date('2026-03-01T00:00:00');
    const REVENUE_PER_SECOND = 55;
    const CONFIG = {
        HUMAN_CLOSERS: 18400,
        NEURAL_NODES: 2168,
        MARGIN_RATE: 0.55,
        OPEX_DAILY: 316000,
        BASE_UPDATE_INTERVAL: 100,
        BOOST_UPDATE_INTERVAL: 2,
        CHART_UPDATE_INTERVAL: 5000,
        SAAS_SITES: 984560,
        SAAS_PRICE: 49.00,
        // Simulation
        START_CLOSERS: 18400,
        SALES_PER_CLOSER_PER_DAY: 3,
        DAYS_PER_MONTH: 30,
        PROFIT_PER_SALE: 500,
        SAAS_MONTHLY_PRICE: 49,
        NOTIFICATION_MIN: 15000,
        NOTIFICATION_MAX: 45000,
    };

    // ========== ÉTAT ==========
    const now = Date.now();
    const startTime = START_DATE.getTime();
    const elapsedSeconds = Math.floor((now - startTime) / 1000);
    let caEuros = elapsedSeconds * REVENUE_PER_SECOND;
    let sites = CONFIG.SAAS_SITES;
    let BATCH_DURATION = CONFIG.BASE_UPDATE_INTERVAL;
    let syncStartTime = Date.now();
    let lastChartUpdate = Date.now();
    let boostActive = false;
    let adminUnlocked = false;
    let transactionHistory = [];

    // ========== DOM ==========
    const caEl = document.getElementById('caTotal');
    const vaEl = document.getElementById('vaValue');
    const netEl = document.getElementById('netRevenue');
    const burnEl = document.getElementById('burnRate');
    const syncBar = document.getElementById('syncBar');
    const mrrDisplay = document.getElementById('mrrDisplay');
    const previewLines = document.getElementById('previewLines');
    const historyList = document.getElementById('historyList');
    const notificationContainer = document.getElementById('notificationContainer');
    const teamContainer = document.getElementById('virtualContainer');
    const recruitmentSlider = document.getElementById('recruitmentSlider');
    const rateDisplay = document.getElementById('recruitmentRateDisplay');
    const statusBadge = document.getElementById('growthStatusBadge');
    const tableBody = document.getElementById('tableBody');
    const txPerHourSpan = document.getElementById('txPerHour');
    const avgAmountSpan = document.getElementById('avgAmount');
    
    // Modale précise
    const preciseModal = document.getElementById('preciseModal');
    const preciseTitle = document.getElementById('preciseTitle');
    const preciseValue = document.getElementById('preciseValue');
    let currentPreciseType = 'ca';
    let preciseInterval = null;

    // ========== DONNÉES GRAPHIQUES ==========
    let growthData = [];
    for (let i = 0; i < 30; i++) growthData.push(caEuros / 1e6);
    let perfRealData = [3.2, 3.9, 4.3, 5.1, 4.6, 5.3, 4.8, 5.0, 4.7, 5.2, 4.9, 5.4, 4.8, 5.1, 4.6, 5.5];
    for (let i = perfRealData.length; i < 30; i++) {
        perfRealData.push(3 + Math.random() * 3);
    }
    let subscriberHistory = [500000, 540000, 583200, 629856, 680244, 734664, 793437, 856912, 925465, 999502, 1079462, 1165819];

    // ========== ÉQUIPE ==========
    const teamMembers = [
        { name: "Thomas Bernard", role: "Senior Closer", color: "#4caf50" },
        { name: "Sarah Dubois", role: "Team Lead", color: "#2196f3" },
        { name: "Nicolas Petit", role: "Closer Elite", color: "#ff9800" },
        { name: "Emma Leroy", role: "Account Executive", color: "#9c27b0" },
        { name: "Lucas Moreau", role: "Sales Manager", color: "#f44336" },
        { name: "Julie Fournier", role: "Closer", color: "#00bcd4" },
        { name: "Alexandre Girard", role: "Tech Closer", color: "#8bc34a" },
        { name: "Camille Roux", role: "Lead Gen", color: "#ff5722" },
        { name: "Antoine Vincent", role: "Closer", color: "#3f51b5" },
        { name: "Léa Fontaine", role: "Operations", color: "#e91e63" },
        { name: "Maxime Dumont", role: "Closer Elite", color: "#009688" },
        { name: "Chloé Lambert", role: "Account Manager", color: "#673ab7" },
        { name: "Quentin Morel", role: "Closer", color: "#ffc107" },
        { name: "Manon Caron", role: "Support Lead", color: "#607d8b" },
        { name: "Jules Perrin", role: "Closer", color: "#795548" },
        { name: "Océane Faure", role: "AI Agent", color: "#4caf50" },
        { name: "Baptiste Mercier", role: "Closer", color: "#2196f3" },
        { name: "Clémence Gauthier", role: "Sales Ops", color: "#ff9800" },
        { name: "Théo Lemoine", role: "Closer Elite", color: "#9c27b0" },
        { name: "Anaïs Renard", role: "Team Lead", color: "#f44336" }
    ];

    // ========== FONCTIONS ==========
    function generateRealisticAmount() {
        const gross = Math.random() * 2755 + 45;
        return (gross - (gross * 0.029 + 0.30)).toFixed(2);
    }

    function updateFinances() {
        const caM = caEuros / 1e6;
        const marge = caM * CONFIG.MARGIN_RATE;
        const opexM = CONFIG.OPEX_DAILY / 1e6;
        caEl.innerText = caM.toFixed(2) + 'M€';
        vaEl.innerText = marge.toFixed(2) + 'M€';
        netEl.innerText = (marge - opexM).toFixed(2) + 'M€';
        
        // Mise à jour de la modale précise si elle est ouverte
        if (preciseModal.style.display === 'flex') {
            updatePreciseValue();
        }
    }

    function updateSaaS() {
        mrrDisplay.innerText = '€' + ((sites * CONFIG.SAAS_PRICE) / 1e6).toFixed(2) + 'M';
    }

    // ========== MODALE PRÉCISE ==========
    window.showPreciseModal = function(type) {
        currentPreciseType = type;
        const titles = {
            ca: 'CHIFFRE D\'AFFAIRES QUOTIDIEN',
            va: 'MARGE BRUTE (55%)',
            net: 'EBITDA ESTIMÉ',
            burn: 'OPEX QUOTIDIEN'
        };
        preciseTitle.innerText = titles[type];
        updatePreciseValue();
        preciseModal.style.display = 'flex';
        
        if (preciseInterval) clearInterval(preciseInterval);
        preciseInterval = setInterval(updatePreciseValue, 100);
    };

    function updatePreciseValue() {
        let value = 0;
        if (currentPreciseType === 'ca') value = caEuros;
        else if (currentPreciseType === 'va') value = caEuros * CONFIG.MARGIN_RATE;
        else if (currentPreciseType === 'net') value = caEuros * CONFIG.MARGIN_RATE - CONFIG.OPEX_DAILY;
        else if (currentPreciseType === 'burn') value = CONFIG.OPEX_DAILY;
        
        preciseValue.innerText = value.toFixed(2) + ' €';
    }

    window.closePreciseModal = function() {
        preciseModal.style.display = 'none';
        if (preciseInterval) {
            clearInterval(preciseInterval);
            preciseInterval = null;
        }
    };

    // LEDGER
    function addTransaction() {
        const amount = parseFloat(generateRealisticAmount());
        const amountStr = amount.toFixed(2);
        const id = Math.random().toString(36).substring(2, 8).toUpperCase();
        const sources = ['STRIPE', 'PAYPAL', 'WIRE'];
        const source = sources[Math.floor(Math.random() * sources.length)];
        const time = new Date().toLocaleTimeString('fr-FR');
        transactionHistory.unshift({ time, amount, amountStr, source, id });
        
        const recent = transactionHistory.slice(0, 3);
        previewLines.innerHTML = recent.map(t => 
            `<div class="preview-line"><span class="preview-time">${t.time}</span> +${t.amountStr}€ | ID: ${t.id} | ${t.source}</div>`
        ).join('');
        
        const txPerHour = boostActive ? 3600000 / 2.5 : 3600000 / 1600;
        txPerHourSpan.innerText = '~' + Math.round(txPerHour).toLocaleString();
        
        const avg = transactionHistory.reduce((acc, t) => acc + t.amount, 0) / transactionHistory.length;
        avgAmountSpan.innerText = Math.round(avg).toLocaleString() + '€';
        
        // On ne joue pas le son ici car il est joué dans showNotification
    }

    function startLedger() {
        addTransaction();
        const loop = () => {
            const delay = boostActive ? Math.random()*3+2 : Math.random()*1700+800;
            setTimeout(() => { addTransaction(); loop(); }, delay);
        };
        loop();
    }

    // ========== NOUVELLE FONCTION NOTIFICATION AVEC SON ==========
    function showNotification() {
        const amount = generateRealisticAmount();
        const closer = teamMembers[Math.floor(Math.random() * teamMembers.length)].name.split(' ')[0];
        
        // Effet Sonore "Cha-Ching" (lien Mixkit)
        const cashSound = new Audio('https://assets.mixkit.co/active_storage/sfx/2012/2012-preview.mp3');
        cashSound.volume = 0.5;
        cashSound.play().catch(e => console.log("Cliquez sur la page pour activer le son"));

        const notif = document.createElement('div');
        notif.className = 'notification';
        notif.innerHTML = `🔔 NOUVELLE VENTE : +${amount}€ par ${closer}`;
        notificationContainer.appendChild(notif);
        setTimeout(() => notif.remove(), 5000);
    }

    function startNotifications() {
        // On synchronise les notifications avec les transactions du ledger
        // Pour éviter les doublons, on appelle showNotification à la même fréquence
        const loop = () => {
            const delay = boostActive ? Math.random()*3+2 : Math.random()*1700+800;
            setTimeout(() => { 
                showNotification(); 
                loop(); 
            }, delay);
        };
        loop();
    }

    // SIMULATION
    function calculateProjections(rate) {
        let closers = CONFIG.START_CLOSERS;
        let cumulativeClients = 0;
        const results = [];
        for (let m = 1; m <= 12; m++) {
            if (m > 1) closers = Math.round(closers * (1 + rate / 100));
            const monthlySales = closers * CONFIG.SALES_PER_CLOSER_PER_DAY * CONFIG.DAYS_PER_MONTH;
            const direct = monthlySales * CONFIG.PROFIT_PER_SALE;
            if (m > 1) cumulativeClients += monthlySales;
            const saas = cumulativeClients * CONFIG.SAAS_MONTHLY_PRICE;
            results.push({ month: m, closers, direct, saas, total: direct + saas });
        }
        return results;
    }

    function updateSimulation() {
        const rate = parseInt(recruitmentSlider.value);
        rateDisplay.innerText = rate + '%';
        statusBadge.className = 'growth-badge ' + 
            (rate <= 20 ? 'badge-stable' : rate <= 60 ? 'badge-aggressive' : 'badge-hyper');
        statusBadge.innerText = rate <= 20 ? 'Stable Growth' : rate <= 60 ? 'Aggressive Scale' : 'HYPER-BLITZSCALING';
        
        const proj = calculateProjections(rate);
        tableBody.innerHTML = proj.map(p => `
            <tr><td>M${p.month}</td><td>${p.closers}</td><td>${p.direct.toLocaleString()}€</td>
            <td>${p.saas.toLocaleString()}€</td><td>${p.total.toLocaleString()}€</td></tr>
        `).join('');
        
        if (window.projectionChart) {
            window.projectionChart.data.labels = proj.map(p => 'M'+p.month);
            window.projectionChart.data.datasets[0].data = proj.map(p => p.direct);
            window.projectionChart.data.datasets[1].data = proj.map(p => p.saas);
            window.projectionChart.update();
        }
    }

    // ========== CHART.JS INIT ==========
    function initCharts() {
        // Croissance chart
        const ctxG = document.getElementById('growthChart')?.getContext('2d');
        if (ctxG) {
            window.growthChart = new Chart(ctxG, {
                type: 'line',
                data: { 
                    labels: Array(growthData.length).fill(''), 
                    datasets: [{ 
                        data: growthData, 
                        borderColor: '#4caf50', 
                        backgroundColor: 'rgba(76,175,80,0.1)',
                        fill: true,
                        tension: 0.4,
                        pointRadius: 0
                    }] 
                },
                options: { 
                    responsive: true, 
                    maintainAspectRatio: false, 
                    scales: { x: { display: false }, y: { display: false } },
                    plugins: { legend: { display: false } },
                    animation: { duration: 800 }
                }
            });
        }
        
        // Performance chart
        const ctxP = document.getElementById('perfChart')?.getContext('2d');
        if (ctxP) {
            window.perfChart = new Chart(ctxP, {
                type: 'line',
                data: { 
                    labels: Array(perfRealData.length).fill(''), 
                    datasets: [
                        { data: perfRealData, borderColor: '#4caf50', borderWidth: 2, tension: 0.3, pointRadius: 0 },
                        { data: Array(perfRealData.length).fill(3), borderColor: '#ffaa33', borderDash: [5,5], borderWidth: 2, pointRadius: 0 }
                    ] 
                },
                options: { 
                    responsive: true, 
                    maintainAspectRatio: false, 
                    scales: { x: { display: false }, y: { display: false } },
                    plugins: { legend: { display: false } },
                    animation: { duration: 800 }
                }
            });
        }
        
        // Subscribers chart
        const ctxS = document.getElementById('subscribersChart')?.getContext('2d');
        if (ctxS) {
            new Chart(ctxS, {
                type: 'bar',
                data: { 
                    labels: ['M-11', 'M-10', 'M-9', 'M-8', 'M-7', 'M-6', 'M-5', 'M-4', 'M-3', 'M-2', 'M-1', 'M'],
                    datasets: [{ data: subscriberHistory, backgroundColor: '#4caf50', borderRadius: 8 }] 
                },
                options: { 
                    responsive: true, 
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false } },
                    scales: { y: { display: false }, x: { ticks: { color: '#888' } } },
                    animation: { duration: 800 }
                }
            });
        }
        
        // Projection chart
        const ctxProj = document.getElementById('projectionChart')?.getContext('2d');
        if (ctxProj) {
            window.projectionChart = new Chart(ctxProj, {
                type: 'line',
                data: {
                    labels: [],
                    datasets: [
                        { label: 'Ventes Directes', data: [], backgroundColor: '#1A2A1A', borderColor: '#1A2A1A', fill: true, tension: 0.4 },
                        { label: 'Revenus SaaS', data: [], backgroundColor: '#4CAF50', borderColor: '#4CAF50', fill: true, tension: 0.4 }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: { y: { type: 'logarithmic', ticks: { callback: v => v.toLocaleString()+'€' } } },
                    plugins: { tooltip: { mode: 'index' } },
                    animation: { duration: 800 }
                }
            });
        }
    }

    // Mise à jour des graphiques
    function updateCharts() {
        if (window.growthChart) {
            let newCA = caEuros / 1e6;
            newCA = newCA * (1 + (Math.random() * 0.02 - 0.01));
            growthData.push(newCA);
            if (growthData.length > 40) growthData.shift();
            window.growthChart.data.datasets[0].data = growthData;
            window.growthChart.update();
        }
        
        if (window.perfChart) {
            let newPerf = 3 + Math.random() * 3;
            perfRealData.push(newPerf);
            if (perfRealData.length > 40) perfRealData.shift();
            window.perfChart.data.datasets[0].data = perfRealData;
            window.perfChart.update();
        }
    }

    // ========== TEAM RENDER ==========
    function renderTeam() {
        if (teamContainer) {
            teamContainer.innerHTML = teamMembers.map((m, i) => `
                <div class="employee-row" onclick="openEmp(${i})">
                    <div class="avatar" style="background:${m.color};">${m.name[0]}</div>
                    <div><div style="font-weight:600">${m.name}</div><div>${m.role} <span style="color:#4caf50;">● Live</span></div></div>
                </div>
            `).join('');
        }
    }

    // ========== SYNC LOOP ==========
    function syncLoop() {
        const now = Date.now();
        const elapsed = now - syncStartTime;
        syncBar.style.width = Math.min((elapsed / BATCH_DURATION) * 100, 100) + "%";
        
        if (elapsed >= BATCH_DURATION) {
            syncStartTime = now;
            caEuros += REVENUE_PER_SECOND * (BATCH_DURATION / 1000);
            sites += (BATCH_DURATION / 1000) * 0.5;
            updateFinances();
            updateSaaS();
        }
        
        if (now - lastChartUpdate >= CONFIG.CHART_UPDATE_INTERVAL) {
            lastChartUpdate = now;
            updateCharts();
        }
        
        requestAnimationFrame(syncLoop);
    }

    // ========== NAVIGATION ==========
    document.querySelectorAll('.tab-item').forEach(t => t.addEventListener('click', function() {
        document.querySelectorAll('.tab-item').forEach(i => i.classList.remove('active'));
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
        t.classList.add('active');
        const pageId = t.dataset.page + 'Page';
        const page = document.getElementById(pageId);
        if (page) page.classList.add('active');
    }));

    // ========== ADMIN / BOOST ==========
    document.getElementById('logoTrigger').addEventListener('click', () => {
        document.getElementById('secretModal').style.display = 'flex';
    });
    document.getElementById('applySecret')?.addEventListener('click', () => {
        if (document.getElementById('secretCode').value === 'NC-2025-xK9#mP4@vR7!') {
            document.getElementById('secretModal').style.display = 'none';
            document.getElementById('empireBoostSection').classList.add('visible');
            adminUnlocked = true;
        }
    });
    document.getElementById('secretBoostToggle')?.addEventListener('click', (e) => {
        e.stopPropagation();
        if (adminUnlocked) {
            boostActive = !boostActive;
            BATCH_DURATION = boostActive ? CONFIG.BOOST_UPDATE_INTERVAL : CONFIG.BASE_UPDATE_INTERVAL;
            secretBoostToggle.classList.toggle('active', boostActive);
        }
    });

    // ========== MODALES ==========
    window.openHistoryModal = () => {
        historyList.innerHTML = transactionHistory.map(t => 
            `<div>${t.time} | +${t.amountStr}€ | ${t.id} | ${t.source}</div>`
        ).join('');
        document.getElementById('historyModal').style.display = 'flex';
    };
    window.closeHistoryModal = () => document.getElementById('historyModal').style.display = 'none';
    window.openEmp = (i) => { /* simplifié */ };
    window.closeEmployeeModal = () => document.getElementById('employeeDetailModal').style.display = 'none';
    window.showFullscreenChart = (type) => {
        if (type === 'growth') {
            document.getElementById('fullscreenGrowth').style.display = 'flex';
        } else if (type === 'perf') {
            document.getElementById('fullscreenPerf').style.display = 'flex';
        }
    };
    window.closeFullscreen = (type) => {
        if (type === 'growth') document.getElementById('fullscreenGrowth').style.display = 'none';
        else if (type === 'perf') document.getElementById('fullscreenPerf').style.display = 'none';
    };

    // ========== DATE LIVE ==========
    function updateDate() {
        document.getElementById('liveDate').innerText = new Date().toLocaleString('fr-FR', {hour:'2-digit',minute:'2-digit',second:'2-digit'});
    }
    setInterval(updateDate, 500);

    // ========== INIT ==========
    function initAll() {
        renderTeam();
        initCharts();
        updateFinances();
        updateSaaS();
        startLedger();
        startNotifications(); // Les notifications jouent maintenant le son
        updateSimulation();
        recruitmentSlider.addEventListener('input', updateSimulation);
        syncLoop();
    }

    window.onload = initAll;
})();
</script>
</body>
</html>