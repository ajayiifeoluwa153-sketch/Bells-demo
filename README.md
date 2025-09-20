<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Demo — Student Portal Dashboard (Mock)</title>
  <style>
    /* Basic reset */
    *{box-sizing:border-box;margin:0;padding:0}
    body{font-family:Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
         background:#f4f6f8;color:#111;line-height:1.4}
    .container{display:flex;min-height:100vh}
    /* Sidebar */
    .sidebar{width:260px;background:#0b2545;color:#fff;padding:24px;display:flex;flex-direction:column;gap:18px}
    .brand{font-weight:700;font-size:18px;letter-spacing:0.6px}
    .nav{display:flex;flex-direction:column;gap:8px}
    .nav a{color:rgba(255,255,255,0.9);text-decoration:none;padding:8px;border-radius:6px}
    .nav a.active, .nav a:hover{background:rgba(255,255,255,0.06)}
    /* Main */
    .main{flex:1;padding:28px}
    header.top{display:flex;justify-content:space-between;align-items:center;margin-bottom:20px}
    .search{display:flex;gap:8px;align-items:center}
    .search input{padding:8px 12px;border-radius:8px;border:1px solid #d6dbe0}
    .cards{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:16px;margin-bottom:18px}
    .card{background:#fff;padding:16px;border-radius:10px;box-shadow:0 6px 18px rgba(10,20,40,0.06)}
    h2{font-size:18px;margin-bottom:8px}
    p.sub{color:#58606b;font-size:13px}
    /* Fees area */
    .fees-row{display:flex;gap:16px;flex-wrap:wrap}
    .fees-card{flex:1;min-width:320px}
    label{display:block;font-weight:600;margin-bottom:6px}
    .input-inline{display:flex;gap:8px;align-items:center}
    input[type="number"]{padding:10px 12px;border-radius:8px;border:1px solid #d6dbe0;width:180px}
    button{padding:10px 12px;border-radius:8px;border:0;background:#0b2545;color:#fff;cursor:pointer}
    button.secondary{background:#2e6aa3}
    small.hint{display:block;margin-top:8px;color:#6b7280}
    footer{margin-top:28px;color:#6b7280;font-size:13px}
    /* Responsive */
    @media (max-width:800px){
      .sidebar{display:none}
      .container{flex-direction:column}
      .main{padding:16px}
    }
  </style>
</head>
<body>
  <div class="container">
    <aside class="sidebar" aria-label="Sidebar">
      <div class="brand">BELLS — Demo Portal</div>
      <div class="sub">Student Dashboard (mock)</div>
      <nav class="nav" aria-label="Main navigation">
        <a href="#" class="active">Dashboard</a>
        <a href="#">Fees</a>
        <a href="#">Courses</a>
        <a href="#">Payments</a>
        <a href="#">Profile</a>
      </nav>
      <div style="margin-top:auto">
        <small>Demo only — not connected to the real portal</small>
      </div>
    </aside>

    <main class="main">
      <header class="top">
        <div>
          <h1 style="font-size:20px">Welcome, <span id="studentName">Jane Student</span></h1>
          <p class="sub" id="matric">Matric No: <strong>BM/2024/001</strong></p>
        </div>
        <div class="search">
          <input id="searchBox" placeholder="Quick search (mock)" />
          <div style="padding:8px 10px;border-radius:8px;background:#fff;box-shadow:0 2px 6px rgba(10,20,40,0.04)">⚙️</div>
        </div>
      </header>

      <section class="cards" aria-label="Overview cards">
        <article class="card">
          <h2>Account Balance</h2>
          <p class="sub">Amount due: <strong id="balanceDisplay">₦0.00</strong></p>
        </article>

        <article class="card">
          <h2>Current Level</h2>
          <p class="sub">100 Level &middot; Department of Engineering</p>
        </article>

        <article class="card">
          <h2>Notifications</h2>
          <p class="sub" id="notif">No new notifications</p>
        </article>
      </section>

      <section class="card fees-card" aria-labelledby="feesHeading">
        <h2 id="feesHeading">School Fees (mock)</h2>
        <p class="sub">This is a local demo. Changes are saved only in your browser for testing.</p>

        <div class="fees-row" style="margin-top:12px">
          <div style="flex:1;min-width:220px">
            <label for="feeAmount">Fee amount (₦)</label>
            <div class="input-inline">
              <input id="feeAmount" type="number" min="0" step="100" />
              <button id="saveFee">Save</button>
              <button class="secondary" id="resetFee">Reset</button>
            </div>
            <small class="hint">Enter whole amounts. Click Save to store locally (no server).</small>
          </div>

          <div style="min-width:220px;align-self:center">
            <p>Current stored fee:</p>
            <h2 id="currentFee">₦—</h2>
          </div>
        </div>
      </section>

      <footer>
        Demo created for learning. Do not use for actual payments.
      </footer>
    </main>
  </div>

  <script>
    // Demo: manage a "fee amount" stored locally in localStorage
    const FEE_KEY = 'demo_portal_fee_v1';
    const feeInput = document.getElementById('feeAmount');
    const saveBtn = document.getElementById('saveFee');
    const resetBtn = document.getElementById('resetFee');
    const currentFeeEl = document.getElementById('currentFee');
    const balanceDisplay = document.getElementById('balanceDisplay');

    function formatNaira(n){
      if (n === null || n === undefined || isNaN(n)) return '₦—';
      return '₦' + Number(n).toLocaleString('en-NG', {maximumFractionDigits:0});
    }

    function loadFee(){
      const stored = localStorage.getItem(FEE_KEY);
      if(!stored) {
        currentFeeEl.textContent = formatNaira(null);
        balanceDisplay.textContent = formatNaira(0);
        return;
      }
      const val = Number(stored);
      currentFeeEl.textContent = formatNaira(val);
      balanceDisplay.textContent = formatNaira(val);
      feeInput.value = val;
    }

    saveBtn.addEventListener('click', () => {
      const v = Number(feeInput.value);
      if(isNaN(v) || v < 0){
        alert('Enter a valid non-negative number');
        return;
      }
      localStorage.setItem(FEE_KEY, String(Math.round(v)));
      loadFee();
      // small UX cue
      saveBtn.textContent = 'Saved ✓';
      setTimeout(()=> saveBtn.textContent = 'Save', 900);
    });

    resetBtn.addEventListener('click', () => {
      if(confirm('Reset stored fee to nothing? This only affects this demo in your browser.')) {
        localStorage.removeItem(FEE_KEY);
        feeInput.value = '';
        loadFee();
      }
    });

    // initialize
    loadFee();
  </script>
</body>
</html>
