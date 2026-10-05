<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dashboard Administrasi Muta'allim MTQ</title>

  <!-- External Libraries: Chart.js & Ionicons -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script type="module" src="https://unpkg.com/ionicons@5.5.2/dist/ionicons/ionicons.esm.js"></script>
  <script nomodule src="https://unpkg.com/ionicons@5.5.2/dist/ionicons/ionicons.js"></script>

  <style>
    :root {
      --bg-body: #0a0f1d;
      --card-bg: #131c31;
      --card-border: rgba(59, 130, 246, 0.15);
      --accent-blue: #3b82f6;
      --accent-glow: #00f0ff;
      --accent-green: #10b981;
      --accent-purple: #8b5cf6;
      --text-main: #f8fafc;
      --text-muted: #94a3b8;
      --radius-lg: 18px;
      --radius-sm: 12px;
      --excel-header-bg: #0369a1;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'Segoe UI', -apple-system, BlinkMacSystemFont, Roboto, sans-serif;
    }

    body {
      background-color: var(--bg-body);
      color: var(--text-main);
      display: flex;
      min-height: 100vh;
      overflow-x: hidden;
    }

    /* Sidebar Styling */
    .sidebar {
      width: 250px;
      background: #070a14;
      padding: 20px 12px;
      display: flex;
      flex-direction: column;
      border-right: 1px solid rgba(59, 130, 246, 0.2);
      transition: width 0.35s cubic-bezier(0.4, 0, 0.2, 1);
      position: relative;
      z-index: 20;
    }

    .sidebar.collapsed {
      width: 80px;
    }

    .brand {
      display: flex;
      align-items: center;
      gap: 14px;
      font-weight: 700;
      font-size: 1.1rem;
      margin-bottom: 32px;
      color: var(--accent-glow);
      padding-left: 10px;
      white-space: nowrap;
      overflow: hidden;
      text-shadow: 0 0 10px rgba(0, 240, 255, 0.5);
    }

    .toggle-btn {
      position: absolute;
      top: 20px;
      right: -14px;
      background: var(--accent-blue);
      color: #fff;
      border: none;
      border-radius: 50%;
      width: 28px;
      height: 28px;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      box-shadow: 0 0 12px var(--accent-blue);
      z-index: 30;
      transition: transform 0.3s ease;
    }

    .toggle-btn:hover {
      transform: scale(1.15);
    }

    .navigation {
      list-style: none;
      display: flex;
      flex-direction: column;
      gap: 12px;
    }

    .navigation li {
      position: relative;
      width: 100%;
    }

    .navigation li a {
      display: flex;
      align-items: center;
      gap: 16px;
      padding: 12px 16px;
      color: var(--text-muted);
      text-decoration: none;
      font-weight: 500;
      border-radius: var(--radius-sm);
      transition: all 0.3s ease;
      white-space: nowrap;
    }

    .navigation li a .icon {
      font-size: 1.4rem;
      min-width: 28px;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .navigation li.active a {
      color: #fff;
      background: rgba(59, 130, 246, 0.15);
    }

    /* Neon Glow Indicator */
    .navigation li.active::before {
      content: '';
      position: absolute;
      left: -6px;
      top: 50%;
      transform: translateY(-50%);
      width: 6px;
      height: 65%;
      background: var(--accent-glow);
      border-radius: 10px;
      box-shadow: 
        0 0 6px var(--accent-glow), 
        0 0 12px var(--accent-glow), 
        0 0 24px var(--accent-glow);
    }

    .sidebar.collapsed .text {
      opacity: 0;
      pointer-events: none;
    }

    .main-content {
      flex: 1;
      padding: 28px;
      overflow-y: auto;
      transition: all 0.3s ease;
    }

    .header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 24px;
    }

    .header h1 {
      font-size: 1.6rem;
      font-weight: 700;
      letter-spacing: 0.5px;
      background: linear-gradient(to right, #fff, var(--accent-glow));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }

    /* Dynamic Grid Layout */
    .dashboard-grid {
      display: grid;
      grid-template-columns: 2.2fr 1fr;
      gap: 24px;
      transition: all 0.4s ease;
    }

    .dashboard-grid.expanded {
      grid-template-columns: 1fr;
    }

    .left-section, .right-section {
      display: flex;
      flex-direction: column;
      gap: 24px;
    }

    .dashboard-grid.expanded .right-section {
      display: grid;
      grid-template-columns: 1fr 1fr;
      align-items: stretch;
    }

    .stats-cards {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
      gap: 16px;
    }

    .card {
      background: var(--card-bg);
      border-radius: var(--radius-lg);
      padding: 22px;
      border: 1px solid var(--card-border);
      position: relative;
      transition: all 0.35s cubic-bezier(0.175, 0.885, 0.32, 1.275);
      box-shadow: 0 8px 20px rgba(0, 0, 0, 0.3);
      display: flex;
      flex-direction: column;
    }

    .card:hover {
      transform: translateY(-5px);
      border-color: var(--accent-glow);
      box-shadow: 
        0 0 15px rgba(0, 240, 255, 0.4),
        0 0 30px rgba(59, 130, 246, 0.25),
        inset 0 0 15px rgba(0, 240, 255, 0.1);
    }

    .card-highlight {
      background: linear-gradient(135deg, rgba(37, 99, 235, 0.8), rgba(29, 78, 216, 0.9));
      border: 1px solid rgba(0, 240, 255, 0.4);
    }

    .card-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 12px;
      color: var(--text-muted);
      font-size: 0.9rem;
    }

    .card-value {
      font-size: 1.8rem;
      font-weight: 700;
      color: #fff;
    }

    .chart-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 16px;
      flex-wrap: wrap;
      gap: 10px;
    }

    .btn-action {
      background: rgba(0, 240, 255, 0.08);
      color: var(--accent-glow);
      border: 1px solid rgba(0, 240, 255, 0.4);
      padding: 8px 16px;
      border-radius: var(--radius-sm);
      cursor: pointer;
      display: inline-flex;
      align-items: center;
      gap: 8px;
      font-size: 0.85rem;
      font-weight: 600;
      transition: all 0.3s ease;
      box-shadow: 0 0 8px rgba(0, 240, 255, 0.1);
    }

    .btn-action:hover {
      background: var(--accent-blue);
      color: #fff;
      border-color: var(--accent-blue);
      box-shadow: 0 0 15px rgba(59, 130, 246, 0.6);
      transform: translateY(-2px);
    }

    /* Chart Containers */
    .chart-wrapper {
      position: relative;
      width: 100%;
      height: 380px;
      overflow-x: auto;
      transition: all 0.4s ease;
    }

    .chart-wrapper canvas {
      min-width: 700px;
      width: 100% !important;
      height: 100% !important;
    }

    /* Responsive & Compact Doughnut Canvas Wrapper */
    .doughnut-wrapper {
      position: relative;
      width: 100%;
      height: 190px;
      display: flex;
      align-items: center;
      justify-content: center;
      margin: 0 auto;
      flex: 1;
    }

    .doughnut-wrapper canvas {
      max-height: 100% !important;
      max-width: 100% !important;
    }

    /* Santri Baru Item */
    .santri-list {
      display: flex;
      flex-direction: column;
      gap: 8px;
      justify-content: space-around;
      flex: 1;
    }

    .santri-item {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 10px 14px;
      background: rgba(255, 255, 255, 0.02);
      border: 1px solid rgba(255, 255, 255, 0.05);
      border-radius: var(--radius-sm);
      font-size: 0.88rem;
      transition: all 0.2s ease;
    }

    .santri-item:hover {
      background: rgba(59, 130, 246, 0.1);
      border-color: rgba(0, 240, 255, 0.3);
      box-shadow: 0 0 10px rgba(0, 240, 255, 0.2);
    }

    .santri-badge {
      background: rgba(0, 240, 255, 0.15);
      color: var(--accent-glow);
      padding: 3px 10px;
      border-radius: 20px;
      font-weight: 700;
      border: 1px solid rgba(0, 240, 255, 0.3);
      font-size: 0.82rem;
    }

    /* Excel Modal Styling */
    .modal-overlay {
      position: fixed;
      top: 0; left: 0; width: 100%; height: 100%;
      background: rgba(5, 10, 20, 0.88);
      backdrop-filter: blur(10px);
      display: flex;
      align-items: center;
      justify-content: center;
      z-index: 100;
      opacity: 0;
      pointer-events: none;
      transition: all 0.35s cubic-bezier(0.4, 0, 0.2, 1);
    }

    .modal-overlay.active {
      opacity: 1;
      pointer-events: auto;
    }

    .modal-content {
      background: #0f172a;
      border-radius: var(--radius-lg);
      padding: 24px;
      width: 95%;
      max-width: 1350px;
      max-height: 90vh;
      display: flex;
      flex-direction: column;
      border: 1px solid var(--accent-glow);
      box-shadow: 
        0 0 30px rgba(0, 240, 255, 0.25),
        0 0 60px rgba(59, 130, 246, 0.2);
      transform: scale(0.9) translateY(20px);
      transition: all 0.35s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    }

    .modal-overlay.active .modal-content {
      transform: scale(1) translateY(0);
    }

    .modal-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 20px;
      padding-bottom: 12px;
      border-bottom: 1px solid rgba(255,255,255,0.1);
    }

    .modal-title {
      font-size: 1.3rem;
      font-weight: 700;
      color: var(--accent-glow);
      display: flex;
      align-items: center;
      gap: 10px;
    }

    .modal-close {
      background: rgba(255, 255, 255, 0.1);
      border: none;
      color: #fff;
      width: 36px;
      height: 36px;
      border-radius: 50%;
      font-size: 1.4rem;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: all 0.2s ease;
    }

    .modal-close:hover {
      background: #ef4444;
      box-shadow: 0 0 12px #ef4444;
    }

    .excel-table-container {
      overflow: auto;
      max-height: 70vh;
      border-radius: var(--radius-sm);
      border: 1px solid #334155;
    }

    .excel-table {
      width: 100%;
      border-collapse: collapse;
      font-size: 0.82rem;
      text-align: center;
      white-space: nowrap;
    }

    .excel-table th {
      background: var(--excel-header-bg);
      color: #fff;
      padding: 8px 10px;
      font-weight: 700;
      border: 1px solid #0284c7;
      position: sticky;
      top: 0;
      z-index: 10;
    }

    .excel-table td {
      padding: 7px 8px;
      border: 1px solid #1e293b;
      background: #0f172a;
      color: #e2e8f0;
      transition: all 0.2s ease;
    }

    .excel-table tbody tr:hover td {
      background: rgba(59, 130, 246, 0.12);
    }

    .excel-table td.total-cell, .excel-table tr.total-row td {
      font-weight: 700;
      color: var(--accent-glow);
      background: rgba(0, 240, 255, 0.06);
    }

    .excel-table td:hover {
      box-shadow: 0 0 10px var(--accent-glow), inset 0 0 8px var(--accent-glow);
      background: rgba(0, 240, 255, 0.2) !important;
      color: #fff;
    }

    .excel-table tr.total-row {
      position: sticky;
      bottom: 0;
      background: #0369a1;
      font-weight: 800;
      font-size: 0.9rem;
      z-index: 5;
    }

    .excel-table tr.total-row td {
      background: #0284c7;
      color: #fff;
      border-top: 2px solid var(--accent-glow);
    }
  </style>
</head>
<body>

  <aside class="sidebar" id="sidebar">
    <button class="toggle-btn" onclick="toggleSidebar()" title="Toggle Sidebar">
      <ion-icon name="chevron-back-outline" id="toggleIcon"></ion-icon>
    </button>
    <div class="brand">
      <ion-icon name="grid-outline"></ion-icon>
      <span class="text">Muta'allim MTQ</span>
    </div>

    <ul class="navigation">
      <li class="active">
        <a href="#">
          <span class="icon"><ion-icon name="home-outline"></ion-icon></span>
          <span class="text">Dashboard</span>
        </a>
      </li>
      <li>
        <a href="#">
          <span class="icon"><ion-icon name="people-outline"></ion-icon></span>
          <span class="text">Data Muta'allim</span>
        </a>
      </li>
      <li>
        <a href="#">
          <span class="icon"><ion-icon name="document-text-outline"></ion-icon></span>
          <span class="text">Data Jalsah</span>
        </a>
      </li>
      <li>
        <a href="#">
          <span class="icon"><ion-icon name="person-remove-outline"></ion-icon></span>
          <span class="text">No Base</span>
        </a>
      </li>
    </ul>
  </aside>

  <main class="main-content">
    <header class="header">
      <div>
        <h1>Dashboard Administrasi</h1>
        <div style="color: var(--text-muted); font-size: 0.9rem; margin-top: 4px;">Ringkasan Data Muta'allim & Jalsah Real-time</div>
      </div>
    </header>

    <div class="dashboard-grid" id="dashboardGrid">
      <!-- LEFT SECTION -->
      <div class="left-section">
        <!-- 4 Stat Cards dengan Glow Hover Effect -->
        <div class="stats-cards">
          <div class="card card-highlight">
            <div class="card-header">
              <span>1. Total Data Utama</span>
              <ion-icon name="server-outline" style="font-size: 1.4rem; color: var(--accent-glow);"></ion-icon>
            </div>
            <div class="card-value">8.353</div>
          </div>
          <div class="card">
            <div class="card-header">
              <span>2. Total Data Muta'allim</span>
              <ion-icon name="person-outline" style="font-size: 1.4rem; color: var(--accent-blue);"></ion-icon>
            </div>
            <div class="card-value">8.351</div>
          </div>
          <div class="card">
            <div class="card-header">
              <span>3. Total Data Jalsah</span>
              <ion-icon name="layers-outline" style="font-size: 1.4rem; color: var(--accent-green);"></ion-icon>
            </div>
            <div class="card-value">22 Jalsah</div>
          </div>
          <div class="card">
            <div class="card-header">
              <span>4. Total Anak No Base</span>
              <ion-icon name="alert-circle-outline" style="font-size: 1.4rem; color: #ef4444;"></ion-icon>
            </div>
            <div class="card-value">42</div>
          </div>
        </div>

        <!-- MAIN GLOWING LINE CHART CARD -->
        <div class="card">
          <div class="chart-header">
            <div>
              <h3 style="font-size: 1.15rem; color: #fff;">Grafik Muta'allim Per Daerah</h3>
              <span style="font-size: 0.8rem; color: var(--text-muted);">Tren Distribusi Santri berdasarkan Domisili Daerah (A - RUMAH ORANG TUA)</span>
            </div>
            <div style="display: flex; gap: 8px;">
              <button class="btn-action" onclick="toggleExpandChart()" id="btnExpand">
                <ion-icon name="expand-outline"></ion-icon>
                <span id="btnExpandText">Perlebar Grafik</span>
              </button>
              <button class="btn-action" onclick="openModal()">
                <ion-icon name="open-outline"></ion-icon>
                View Detail Excel
              </button>
            </div>
          </div>
          
          <div class="chart-wrapper">
            <canvas id="lineChartDaerah"></canvas>
          </div>
        </div>
      </div>

      <!-- RIGHT SECTION -->
      <div class="right-section">
        <!-- Doughnut Chart Jalsah (Ukuran Ringkas & Responsive) -->
        <div class="card" id="cardDoughnut">
          <div class="chart-header">
            <h3 style="font-size: 1.1rem; color: #fff;">Total Muta'allim Per Jalsah</h3>
          </div>
          <div class="doughnut-wrapper">
            <canvas id="doughnutChartJalsah"></canvas>
          </div>
        </div>

        <!-- Santri Baru Per Marhalah -->
        <div class="card" id="cardSantriBaru">
          <div class="chart-header">
            <h3 style="font-size: 1.1rem; color: #fff;">Total Santri Baru</h3>
          </div>
          <div class="santri-list">
            <div class="santri-item"><span>Marhalah 1</span><span class="santri-badge">265 Anak</span></div>
            <div class="santri-item"><span>Marhalah 2</span><span class="santri-badge">567 Anak</span></div>
            <div class="santri-item"><span>Marhalah 3</span><span class="santri-badge">1.116 Anak</span></div>
            <div class="santri-item"><span>Marhalah 4</span><span class="santri-badge">1.230 Anak</span></div>
            <div class="santri-item"><span>Marhalah 5</span><span class="santri-badge">1.019 Anak</span></div>
            <div class="santri-item"><span>Marhalah 6</span><span class="santri-badge">1.164 Anak</span></div>
          </div>
        </div>
      </div>
    </div>
  </main>

  <div class="modal-overlay" id="modalDetail">
    <div class="modal-content">
      <div class="modal-header">
        <div class="modal-title">
          <ion-icon name="stats-chart-outline" style="font-size: 1.5rem;"></ion-icon>
          <span>TOTAL MUTA'ALLIM PER DAERAH - SPREADSHEET DETAIL</span>
        </div>
        <button class="modal-close" onclick="closeModal()" title="Tutup">&times;</button>
      </div>

      <div class="excel-table-container">
        <table class="excel-table">
          <thead>
            <tr>
              <th>DOM</th>
              <th>1</th>
              <th>2</th>
              <th>3</th>
              <th>4</th>
              <th>5</th>
              <th>6</th>
              <th>INT 1</th>
              <th>INT 2</th>
              <th>INT 3</th>
              <th>INT 4</th>
              <th>INT 5</th>
              <th>INT 6</th>
              <th>PASCA</th>
              <th>INT GT</th>
              <th>PASCA 1</th>
              <th>PASCA 2</th>
              <th>PASCA 3</th>
              <th>PASCA 4</th>
              <th>PASCA 5</th>
              <th>SAB'AH</th>
              <th>TOTAL MUTA'ALLIM</th>
            </tr>
          </thead>
          <tbody id="excelTableBody">
            <!-- Dynamically populated -->
          </tbody>
          <tfoot>
            <tr class="total-row" id="excelTableFoot">
              <!-- Dynamically populated total row -->
            </tr>
          </tfoot>
        </table>
      </div>
    </div>
  </div>

  <script>
    // Excel Dataset
    const excelData = [
      { dom: 'A', values: [0, 0, 2, 4, 34, 61, 0, 0, 0, 1, 3, 6, 29, 2, 81, 31, 0, 1, 0, 8], total: 263 },
      { dom: 'B', values: [0, 1, 3, 5, 37, 87, 0, 0, 0, 5, 1, 10, 35, 2, 93, 52, 2, 3, 0, 13], total: 349 },
      { dom: 'C', values: [0, 0, 2, 0, 9, 27, 0, 2, 3, 4, 10, 15, 20, 11, 70, 58, 0, 0, 0, 7], total: 238 },
      { dom: 'D', values: [1, 2, 46, 42, 27, 8, 0, 0, 0, 0, 0, 2, 0, 0, 2, 15, 0, 0, 0, 3], total: 148 },
      { dom: 'E', values: [0, 0, 0, 1, 9, 14, 0, 0, 4, 9, 11, 12, 14, 5, 41, 43, 0, 0, 0, 4], total: 167 },
      { dom: 'F', values: [0, 0, 7, 4, 11, 19, 0, 0, 2, 10, 5, 26, 13, 21, 52, 65, 1, 1, 1, 18], total: 256 },
      { dom: 'G', values: [1, 11, 144, 151, 96, 79, 0, 0, 1, 0, 0, 1, 0, 5, 2, 16, 11, 12, 10, 3], total: 543 },
      { dom: 'H', values: [0, 0, 4, 6, 35, 61, 0, 0, 3, 12, 21, 32, 37, 17, 58, 59, 0, 2, 0, 21], total: 368 },
      { dom: 'I', values: [0, 1, 3, 12, 24, 22, 0, 0, 4, 9, 6, 26, 15, 11, 67, 74, 2, 2, 1, 10], total: 289 },
      { dom: 'J', values: [1, 11, 109, 154, 186, 150, 0, 0, 0, 0, 0, 0, 0, 7, 1, 37, 15, 18, 15, 8], total: 712 },
      { dom: 'K', values: [0, 1, 1, 8, 33, 88, 0, 2, 2, 4, 1, 6, 38, 0, 88, 56, 0, 1, 1, 27], total: 357 },
      { dom: 'L', values: [81, 175, 45, 20, 1, 0, 0, 0, 0, 1, 0, 0, 0, 2, 2, 5, 0, 0, 0, 1], total: 333 },
      { dom: 'M', values: [90, 170, 36, 22, 2, 1, 0, 0, 0, 0, 0, 1, 2, 5, 7, 2, 0, 0, 0, 0], total: 338 },
      { dom: 'N', values: [50, 79, 87, 55, 28, 69, 0, 0, 0, 0, 0, 0, 2, 1, 1, 4, 10, 0, 11, 7], total: 404 },
      { dom: 'O', values: [0, 1, 5, 25, 86, 123, 0, 0, 0, 6, 6, 8, 43, 3, 74, 50, 5, 2, 3, 15], total: 455 },
      { dom: 'P', values: [0, 0, 2, 1, 42, 62, 0, 0, 8, 27, 9, 40, 64, 5, 125, 90, 0, 0, 0, 11], total: 486 },
      { dom: 'Q', values: [0, 2, 100, 173, 181, 141, 0, 0, 0, 0, 0, 1, 2, 16, 3, 21, 0, 1, 0, 4], total: 645 },
      { dom: 'R', values: [30, 68, 319, 241, 23, 9, 0, 0, 0, 2, 0, 1, 1, 1, 3, 7, 0, 1, 0, 3], total: 709 },
      { dom: 'S', values: [9, 39, 163, 253, 38, 19, 0, 0, 0, 0, 0, 1, 1, 2, 3, 11, 0, 0, 0, 1], total: 540 },
      { dom: 'T', values: [1, 3, 32, 43, 98, 93, 0, 0, 6, 17, 7, 13, 32, 12, 110, 111, 4, 5, 9, 22], total: 618 },
      { dom: 'Z', values: [0, 0, 3, 10, 16, 29, 0, 0, 0, 1, 0, 1, 1, 2, 1, 4, 0, 1, 0, 4], total: 73 },
      { dom: 'DALEM', values: [0, 0, 1, 0, 1, 0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0], total: 4 },
      { dom: 'LAINNYA', values: [0, 2, 1, 0, 1, 2, 0, 0, 1, 2, 2, 1, 1, 0, 9, 16, 0, 0, 0, 0], total: 38 },
      { dom: 'RUMAH ORANG TUA', values: [1, 1, 2, 0, 2, 0, 0, 0, 1, 1, 2, 1, 0, 0, 2, 7, 0, 0, 0, 0], total: 20 }
    ];

    // Populate Spreadsheet Table
    const tableBody = document.getElementById('excelTableBody');
    const tableFoot = document.getElementById('excelTableFoot');

    let columnTotals = new Array(20).fill(0);
    let grandTotal = 0;

    excelData.forEach(row => {
      const tr = document.createElement('tr');
      let rowHtml = `<td style="font-weight:700; color:var(--accent-glow);">${row.dom}</td>`;
      
      row.values.forEach((val, i) => {
        columnTotals[i] += val;
        rowHtml += `<td>${val}</td>`;
      });

      grandTotal += row.total;
      rowHtml += `<td class="total-cell">${row.total}</td>`;
      tr.innerHTML = rowHtml;
      tableBody.appendChild(tr);
    });

    let footHtml = `<td>Total</td>`;
    columnTotals.forEach(tot => {
      footHtml += `<td>${tot}</td>`;
    });
    footHtml += `<td style="font-size:1rem; color:var(--accent-glow);">${grandTotal}</td>`;
    tableFoot.innerHTML = footHtml;

    // Sidebar Toggle Function
    function toggleSidebar() {
      const sidebar = document.getElementById('sidebar');
      const icon = document.getElementById('toggleIcon');
      sidebar.classList.toggle('collapsed');
      
      if (sidebar.classList.contains('collapsed')) {
        icon.setAttribute('name', 'chevron-forward-outline');
      } else {
        icon.setAttribute('name', 'chevron-back-outline');
      }
    }

    // Modal Control Functions
    function openModal() {
      document.getElementById('modalDetail').classList.add('active');
    }

    function closeModal() {
      document.getElementById('modalDetail').classList.remove('active');
    }

    document.getElementById('modalDetail').addEventListener('click', (e) => {
      if (e.target.classList.contains('modal-overlay')) {
        closeModal();
      }
    });

    // 1. Line Chart Setup (Muta'allim Per Daerah)
    const daerahLabels = excelData.map(d => d.dom);
    const daerahTotals = excelData.map(d => d.total);
    const ctxLine = document.getElementById('lineChartDaerah').getContext('2d');

    const gradientGlow = ctxLine.createLinearGradient(0, 0, 0, 350);
    gradientGlow.addColorStop(0, 'rgba(0, 240, 255, 0.45)');
    gradientGlow.addColorStop(0.5, 'rgba(59, 130, 246, 0.15)');
    gradientGlow.addColorStop(1, 'rgba(15, 23, 42, 0)');

    const lineChart = new Chart(ctxLine, {
      type: 'line',
      data: {
        labels: daerahLabels,
        datasets: [{
          label: 'Total Muta\'allim',
          data: daerahTotals,
          fill: true,
          backgroundColor: gradientGlow,
          borderColor: '#00f0ff',
          borderWidth: 3,
          pointBackgroundColor: '#00f0ff',
          pointBorderColor: '#fff',
          pointBorderWidth: 2,
          pointRadius: 5,
          pointHoverRadius: 8,
          pointHoverBackgroundColor: '#fff',
          pointHoverBorderColor: '#00f0ff',
          tension: 0.38
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: { display: false },
          tooltip: {
            backgroundColor: '#0f172a',
            titleColor: '#00f0ff',
            bodyColor: '#fff',
            borderColor: '#00f0ff',
            borderWidth: 1,
            padding: 12,
            displayColors: false,
            callbacks: {
              label: function(context) {
                return `Total Muta'allim: ${context.parsed.y} Santri`;
              }
            }
          }
        },
        scales: {
          x: {
            grid: { color: 'rgba(51, 65, 85, 0.4)', drawBorder: false },
            ticks: { 
              color: '#94a3b8', 
              font: { weight: '600', size: 11 },
              maxRotation: 45,
              minRotation: 0
            }
          },
          y: {
            grid: { color: 'rgba(51, 65, 85, 0.4)', drawBorder: false },
            ticks: { color: '#94a3b8' },
            beginAtZero: true
          }
        }
      }
    });

    // 2. Doughnut Chart Setup (Total Muta'allim Per Jalsah)
    const ctxDoughnut = document.getElementById('doughnutChartJalsah').getContext('2d');
    const doughnutChart = new Chart(ctxDoughnut, {
      type: 'doughnut',
      data: {
        labels: ['Marhalah 1-6', 'INT 1-6', 'PASCA 1-3', 'SAB\'AH'],
        datasets: [{
          data: [5361, 439, 2370, 190],
          backgroundColor: ['#00f0ff', '#3b82f6', '#10b981', '#f59e0b'],
          borderWidth: 0,
          hoverOffset: 8
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        radius: '70%',       // Diameter ringkas agar tidak kebesaran
        cutout: '75%',       // Ketebalan lingkaran elegan
        plugins: {
          legend: {
            position: 'bottom',
            labels: { 
              color: '#94a3b8', 
              boxWidth: 10, 
              padding: 10,
              font: { size: 11 }
            }
          }
        }
      }
    });

    // Dynamic Expansion Logic: Toggle & Resize Both Charts Smoothly
    function toggleExpandChart() {
      const grid = document.getElementById('dashboardGrid');
      const btnText = document.getElementById('btnExpandText');
      
      grid.classList.toggle('expanded');

      if (grid.classList.contains('expanded')) {
        btnText.innerText = 'Kecilkan Grafik';
      } else {
        btnText.innerText = 'Perlebar Grafik';
      }

      // Animasi resize chart agar halus saat merespons perubahan grid
      setTimeout(() => {
        lineChart.resize();
        doughnutChart.resize();
      }, 300);
    }
  </script>
</body>
</html>
