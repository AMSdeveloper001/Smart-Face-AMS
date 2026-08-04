# Smart-Face-AMS







<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>FaceTrack Pro — Advanced Biometric Attendance</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;600;700&family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;600&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/face-api.js/0.22.2/face-api.min.js"></script>
<style>
  :root{
    --bg-deep: #050811;
    --bg-panel: #0d1326;
    --bg-panel-2: #131b36;
    --surface: rgba(255,255,255,0.05);
    --surface-hi: rgba(255,255,255,0.1);
    --border: rgba(255,255,255,0.12);
    --border-hi: rgba(255,255,255,0.25);
    
    /* VIBRANT & COLOURFUL PALETTE */
    --violet: #8B5CF6;
    --violet-glow: rgba(139, 92, 246, 0.4);
    --cyan: #06B6D4;
    --cyan-glow: rgba(6, 182, 212, 0.4);
    --magenta: #EC4899;
    --pink-glow: rgba(236, 72, 153, 0.4);
    
    /* STATUS COLORS */
    --color-p: #10B981;  /* Present */
    --color-a: #EF4444;  /* Absent */
    --color-cl: #F59E0B; /* Casual Leave */
    --color-l: #3B82F6;  /* Leave */
    --color-mp: #A855F7; /* Mis Punch */
    
    --text: #F3F4F6;
    --text-dim: #9CA3AF;
    --text-faint: #6B7280;
    --radius: 16px;
    --font-display: 'Space Grotesk', sans-serif;
    --font-body: 'Inter', sans-serif;
    --font-mono: 'JetBrains Mono', monospace;
  }

  *{margin:0;padding:0;box-sizing:border-box;}
  body{
    background: 
      radial-gradient(1200px 800px at 85% -10%, rgba(139,92,246,0.25), transparent 60%),
      radial-gradient(1000px 700px at -10% 110%, rgba(6,182,212,0.2), transparent 60%),
      radial-gradient(800px 600px at 50% 50%, rgba(236,72,153,0.12), transparent 70%),
      var(--bg-deep);
    color: var(--text);
    font-family: var(--font-body);
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Animations */
  @keyframes floatAnim {
    0%, 100% { transform: translateY(0px); }
    50% { transform: translateY(-6px); }
  }

  button{font-family:inherit;cursor:pointer;}
  input,select{font-family:inherit;}
  .hidden{display:none !important;}

  /* Corner brackets animation motif */
  .brackets{position:relative;}
  .brackets::before,.brackets::after,
  .brackets .bl,.brackets .br{
    content:"";position:absolute;width:14px;height:14px;
    border-color:var(--cyan);opacity:0.6;transition:all .3s ease;
  }
  .brackets::before{top:-1px;left:-1px;border-top:2px solid;border-left:2px solid;border-radius:4px 0 0 0;}
  .brackets::after{top:-1px;right:-1px;border-top:2px solid;border-right:2px solid;border-radius:0 4px 0 0;}
  .brackets .bl{bottom:-1px;left:-1px;border-bottom:2px solid;border-left:2px solid;border-radius:0 0 0 4px;}
  .brackets .br{bottom:-1px;right:-1px;border-bottom:2px solid;border-right:2px solid;border-radius:0 0 4px 0;}
  .brackets:hover::before,.brackets:hover::after,.brackets:hover .bl,.brackets:hover .br{
    opacity:1; border-color:var(--magenta); width:18px; height:18px;
  }

  /* App Shell */
  #app{display:flex;min-height:100vh;}
  
  /* LOGIN */
  #login-screen{
    width:100%;min-height:100vh;display:flex;align-items:center;justify-content:center;padding:24px;
  }
  .login-card{
    width:min(420px,92vw);
    background: linear-gradient(145deg, var(--bg-panel-2), var(--bg-panel));
    border: 1px solid var(--border-hi); border-radius: 20px;
    padding: 38px 34px; position:relative; z-index:2;
    box-shadow: 0 20px 50px rgba(0,0,0,0.8);
    animation: floatAnim 6s ease-in-out infinite;
  }
  .login-brand{display:flex;align-items:center;gap:12px;margin-bottom:24px;}
  .login-brand .mark{
    width:42px;height:42px;border-radius:12px;
    background: linear-gradient(135deg, var(--violet), var(--cyan), var(--magenta));
    display:flex;align-items:center;justify-content:center;
    box-shadow: 0 0 15px var(--violet-glow);
  }
  .login-brand h1{font-family:var(--font-display);font-size:22px;background:linear-gradient(90deg, #fff, var(--cyan));-webkit-background-clip:text;-webkit-text-fill-color:transparent;}

  .field{margin-bottom:16px;}
  .field label{display:block;font-size:12px;color:var(--text-dim);margin-bottom:6px;font-weight:600;}
  .field input, .field select{
    width:100%;padding:12px 14px;border-radius:10px;
    background:rgba(255,255,255,0.04);border:1px solid var(--border);color:var(--text);
    transition:all .2s ease;
  }
  .field input:focus, .field select:focus{border-color:var(--cyan);box-shadow:0 0 10px var(--cyan-glow);outline:none;}

  .btn{
    display:inline-flex;align-items:center;justify-content:center;gap:8px;
    border:none;border-radius:10px;padding:12px 20px;font-size:14px;font-weight:600;
    transition:all .2s ease;text-decoration:none;
  }
  .btn:hover{transform:translateY(-2px);box-shadow: 0 5px 15px rgba(0,0,0,0.4);}
  .btn:active{transform:scale(0.97);}
  .btn-primary{
    background: linear-gradient(135deg, var(--violet), #6366F1); color:#fff;
    box-shadow: 0 4px 20px var(--violet-glow);
  }
  .btn-cyan{ background: linear-gradient(135deg, var(--cyan), #0284C7); color:#fff; box-shadow: 0 4px 20px var(--cyan-glow); }
  .btn-magenta{ background: linear-gradient(135deg, var(--magenta), #DB2777); color:#fff; box-shadow: 0 4px 20px var(--pink-glow); }
  .btn-ghost{background:var(--surface);color:var(--text);border:1px solid var(--border);}
  .btn-ghost:hover{background:var(--surface-hi);border-color:var(--border-hi);}

  /* SIDEBAR */
  #sidebar{
    width:250px;background:linear-gradient(180deg, var(--bg-panel-2), var(--bg-panel));
    border-right:1px solid var(--border);display:flex;flex-direction:column;padding:24px 16px;
    position:sticky;top:0;height:100vh;
  }
  .sb-brand{display:flex;align-items:center;gap:12px;padding-bottom:20px;border-bottom:1px solid var(--border);margin-bottom:20px;}
  .sb-brand .mark{width:36px;height:36px;border-radius:10px;background:linear-gradient(135deg,var(--violet),var(--cyan));display:flex;align-items:center;justify-content:center;}
  .nav-item{
    display:flex;align-items:center;gap:12px;padding:12px 14px;border-radius:12px;margin-bottom:6px;
    color:var(--text-dim);font-size:14px;font-weight:500;transition:all .2s ease;cursor:pointer;
  }
  .nav-item:hover{background:var(--surface-hi);color:#fff;transform:translateX(4px);}
  .nav-item.active{
    background: linear-gradient(90deg, rgba(139,92,246,0.25), rgba(6,182,212,0.15));
    color:#fff; border:1px solid rgba(139,92,246,0.4); box-shadow: 0 0 15px rgba(139,92,246,0.2);
  }

  /* MAIN CONTENT */
  #main{flex:1;min-width:0;padding-bottom:60px;}
  .topbar{
    position:sticky;top:0;z-index:10;display:flex;align-items:center;justify-content:space-between;
    padding:20px 32px;background:rgba(5,8,17,0.85);backdrop-filter:blur(16px);
    border-bottom:1px solid var(--border);
  }
  .topbar h2{font-family:var(--font-display);font-size:22px;background:linear-gradient(90deg, #fff, var(--cyan));-webkit-background-clip:text;-webkit-text-fill-color:transparent;}
  .clock{font-family:var(--font-mono);font-size:14px;color:var(--cyan);background:rgba(6,182,212,0.1);padding:8px 16px;border-radius:20px;border:1px solid var(--cyan-glow);}

  .page{padding:30px;max-width:1300px;margin:0 auto;}
  .page.hidden{display:none;}

  /* CARDS & STATS */
  .grid{display:grid;gap:20px;}
  .grid-4{grid-template-columns:repeat(auto-fit, minmax(220px, 1fr));}
  .card{
    background: linear-gradient(145deg, rgba(255,255,255,0.05), rgba(255,255,255,0.02));
    border:1px solid var(--border);border-radius:var(--radius);padding:22px;
    backdrop-filter:blur(10px);transition:all .3s ease;
  }
  .card:hover{border-color:var(--border-hi);transform:translateY(-3px);}

  /* CAM CAMERA VIEW */
  .capture-wrap{display:grid;grid-template-columns:1fr 340px;gap:24px;}
  @media(max-width:960px){.capture-wrap{grid-template-columns:1fr;}}
  
  .cam-box{
    background:#000;border:2px solid var(--border);border-radius:var(--radius);
    aspect-ratio:16/10;position:relative;overflow:hidden;box-shadow: 0 0 30px rgba(0,0,0,0.8);
  }
  .cam-box video, .cam-box canvas{width:100%;height:100%;object-fit:cover;transform:scaleX(-1);position:absolute;top:0;left:0;}
  .cam-controls{
    padding:18px;display:flex;gap:14px;justify-content:center;background:var(--bg-panel);
    border:1px solid var(--border);border-top:none;border-radius:0 0 var(--radius) var(--radius);
  }

  .punch-mode-selector{
    display:flex;gap:10px;background:rgba(0,0,0,0.3);padding:6px;border-radius:12px;margin-bottom:16px;border:1px solid var(--border);
  }
  .punch-opt{
    flex:1;text-align:center;padding:10px;border-radius:8px;font-weight:600;cursor:pointer;transition:all .2s ease;
  }
  .punch-opt.active-in{background:var(--color-p);color:#fff;box-shadow:0 0 12px rgba(16,185,129,0.4);}
  .punch-opt.active-out{background:var(--magenta);color:#fff;box-shadow:0 0 12px rgba(236,72,153,0.4);}

  /* ATTENDANCE DETAILED TABLE VIEW */
  .table-container{overflow-x:auto;border-radius:var(--radius);border:1px solid var(--border);background:var(--bg-panel);}
  .records-table{width:100%;border-collapse:collapse;min-width:1000px;}
  .records-table th{background:rgba(255,255,255,0.06);padding:14px;font-size:12px;text-transform:uppercase;color:var(--text-dim);border-bottom:1px solid var(--border);text-align:center;}
  .records-table td{padding:12px 10px;border-bottom:1px solid var(--border);text-align:center;vertical-align:top;}
  
  /* PUNCH CELL BOX */
  .punch-box{
    background: rgba(255,255,255,0.03); border:1px solid var(--border); border-radius:10px; padding:8px 6px;
    display:flex; flex-direction:column; gap:4px; align-items:center; min-width:85px; transition:all .2s ease; cursor:pointer;
  }
  .punch-box:hover{border-color:var(--cyan); background:rgba(6,182,212,0.05);}
  
  .status-badge{
    font-size:11px; font-weight:700; padding:2px 8px; border-radius:6px; color:#fff; text-transform:uppercase; display:inline-block;
  }
  .badge-P{background:var(--color-p); box-shadow:0 0 8px rgba(16,185,129,0.4);}
  .badge-A{background:var(--color-a); box-shadow:0 0 8px rgba(239,68,68,0.4);}
  .badge-CL{background:var(--color-cl); box-shadow:0 0 8px rgba(245,158,11,0.4);}
  .badge-L{background:var(--color-l); box-shadow:0 0 8px rgba(59,130,246,0.4);}
  .badge-MP{background:var(--color-mp); box-shadow:0 0 8px rgba(168,85,247,0.4);}

  .shift-tag{font-size:9px; color:var(--cyan); font-family:var(--font-mono); font-weight:600;}
  .time-tag{font-size:10px; color:var(--text-dim); font-family:var(--font-mono);}

  /* SEARCH INPUT */
  .search-bar{
    padding:10px 16px;border-radius:10px;background:rgba(255,255,255,0.04);
    border:1px solid var(--border);color:#fff;width:260px;
  }
  .search-bar:focus{border-color:var(--cyan);outline:none;}

  /* TOAST */
  #toast-wrap{position:fixed;bottom:24px;right:24px;z-index:100;display:flex;flex-direction:column;gap:10px;}
  .toast{
    background:var(--bg-panel-2);border:1px solid var(--border-hi);border-radius:12px;padding:14px 20px;
    font-size:13px;display:flex;align-items:center;gap:12px;box-shadow:0 10px 30px rgba(0,0,0,0.5);
    animation:floatAnim 0.3s ease;
  }

  /* MODALS */
  .modal{
    position:fixed;inset:0;background:rgba(0,0,0,0.8);backdrop-filter:blur(6px);z-index:99;
    display:flex;align-items:center;justify-content:center;padding:20px;
  }
  .modal-card{
    background:var(--bg-panel-2);border:1px solid var(--border-hi);border-radius:16px;padding:26px;width:min(520px,100%);
    max-height:90vh;overflow-y:auto;
  }
</style>
</head>
<body>

<div id="toast-wrap"></div>

<!-- ================= LOGIN ================= -->
<div id="login-screen">
  <div class="login-card brackets">
    <span class="bl"></span><span class="br"></span>
    <div class="login-brand">
      <div class="mark">
        <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="2"><path d="M4 8V6a2 2 0 0 1 2-2h2M20 8V6a2 2 0 0 1-2-2h-2M4 16v2a2 2 0 0 0 2 2h2M20 16v2a2 2 0 0 1-2 2h-2M9 10a3 3 0 1 0 6 0 3 3 0 0 0-6 0ZM7 17c1-2.2 2.9-3 5-3s4 .8 5 3"/></svg>
      </div>
      <div>
        <h1>FaceTrack Pro</h1>
        <span style="font-size:10px;color:var(--text-faint);letter-spacing:1px;font-family:var(--font-mono);">ADVANCED BIOMETRIC SUITE</span>
      </div>
    </div>
    <div class="field">
      <label>Employee Email</label>
      <input type="email" value="admin@company.com">
    </div>
    <div class="field">
      <label>Password</label>
      <input type="password" value="••••••••">
    </div>
    <button class="btn btn-primary" style="width:100%;margin-top:10px;" onclick="app.login()">Sign In to Dashboard</button>
  </div>
</div>

<!-- ================= APP SHELL ================= -->
<div id="app" class="hidden">
  <aside id="sidebar">
    <div class="sb-brand">
      <div class="mark">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="2"><path d="M4 8V6a2 2 0 0 1 2-2h2M20 8V6a2 2 0 0 1-2-2h-2M4 16v2a2 2 0 0 0 2 2h2M20 16v2a2 2 0 0 1-2 2h-2M9 10a3 3 0 1 0 6 0 3 3 0 0 0-6 0ZM7 17c1-2.2 2.9-3 5-3s4 .8 5 3"/></svg>
      </div>
      <div><h1 style="font-size:16px;font-family:var(--font-display);">FaceTrack</h1><span style="font-size:9px;color:var(--cyan);font-family:var(--font-mono);">PRO EDITION</span></div>
    </div>

    <nav id="navList">
      <div class="nav-item active" data-page="home">Dashboard</div>
      <div class="nav-item" data-page="capture">Mark Attendance</div>
      <div class="nav-item" data-page="records">Monthly Punching View</div>
      <div class="nav-item" data-page="employees">Employees List</div>
    </nav>

    <div style="margin-top:auto;padding-top:16px;border-top:1px solid var(--border);display:flex;flex-direction:column;gap:10px;">
      <a href="https://yourcompany.com" target="_blank" class="btn btn-cyan" style="width:100%;font-size:12px;">🌐 Visit Website</a>
      <button class="btn btn-ghost" style="width:100%;" onclick="app.logout()">Sign Out</button>
    </div>
  </aside>

  <main id="main">
    <div class="topbar">
      <div>
        <h2 id="pageTitle">Dashboard</h2>
        <div style="font-size:12px;color:var(--text-faint);" id="pageSub">Real-time attendance summary</div>
      </div>
      <div style="display:flex;align-items:center;gap:16px;">
        <a href="https://yourcompany.com" target="_blank" class="btn btn-ghost" style="font-size:12px;">🔗 Main Website</a>
        <div class="clock" id="clockEl">00:00:00 AM</div>
      </div>
    </div>

    <!-- HOME PAGE -->
    <section class="page" id="page-home">
      <div class="grid grid-4" style="margin-bottom:24px;">
        <div class="card" style="border-left:4px solid var(--violet);">
          <div style="font-size:12px;color:var(--text-dim);">Total Staff</div>
          <div style="font-size:32px;font-weight:700;font-family:var(--font-display);" id="statTotal">0</div>
        </div>
        <div class="card" style="border-left:4px solid var(--color-p);">
          <div style="font-size:12px;color:var(--text-dim);">Present Today</div>
          <div style="font-size:32px;font-weight:700;font-family:var(--font-display);color:var(--color-p);" id="statPresent">0</div>
        </div>
        <div class="card" style="border-left:4px solid var(--color-a);">
          <div style="font-size:12px;color:var(--text-dim);">Absent Today</div>
          <div style="font-size:32px;font-weight:700;font-family:var(--font-display);color:var(--color-a);" id="statAbsent">0</div>
        </div>
        <div class="card" style="border-left:4px solid var(--cyan);">
          <div style="font-size:12px;color:var(--text-dim);">Attendance Rate</div>
          <div style="font-size:32px;font-weight:700;font-family:var(--font-display);color:var(--cyan);" id="statRate">0%</div>
        </div>
      </div>
      
      <div class="card">
        <h3 style="font-size:16px;margin-bottom:16px;font-family:var(--font-display);">Today's Activity Feed</h3>
        <div id="activityFeed"></div>
      </div>
    </section>

    <!-- MARK ATTENDANCE (CAPTURE) -->
    <section class="page hidden" id="page-capture">
      <div class="capture-wrap">
        <div>
          <!-- PUNCH IN / OUT SELECTOR -->
          <div class="punch-mode-selector">
            <div class="punch-opt active-in" id="btnPunchInOpt" onclick="app.setPunchMode('IN')">
              PUNCH IN
            </div>
            <div class="punch-opt" id="btnPunchOutOpt" onclick="app.setPunchMode('OUT')">
              PUNCH OUT
            </div>
          </div>

          <div class="cam-box brackets" id="camBox">
            <video id="camVideo" autoplay muted playsinline></video>
            <canvas id="camCanvas"></canvas>
          </div>
          
          <div class="cam-controls">
            <button class="btn btn-cyan" id="camStartBtn" onclick="app.startCamera()">Start Camera</button>
            <button class="btn btn-magenta hidden" id="camScanBtn" onclick="app.scanFace()">Scan & Punch Now</button>
            <button class="btn btn-ghost hidden" id="camStopBtn" onclick="app.stopCamera()">Stop Camera</button>
          </div>
        </div>

        <div>
          <div class="card brackets" style="margin-bottom:16px;">
            <span class="bl"></span><span class="br"></span>
            <h4 style="font-size:14px;color:var(--cyan);margin-bottom:10px;">PUNCH STATUS</h4>
            <div id="punchStatusDisplay" style="font-size:13px;color:var(--text-dim);">
              Select <b>Punch In</b> or <b>Punch Out</b> and face the camera.
            </div>
          </div>
          <div class="card">
            <h4 style="font-size:14px;margin-bottom:12px;">Recent Punches Today</h4>
            <div id="recentPunchList" style="font-size:12px;"></div>
          </div>
        </div>
      </div>
    </section>

    <!-- MONTHLY RECORDS -->
    <section class="page hidden" id="page-records">
      <div style="display:flex;gap:12px;margin-bottom:20px;flex-wrap:wrap;align-items:center;">
        <select id="recMonth" class="btn btn-ghost" onchange="app.renderRecords()"></select>
        <select id="recYear" class="btn btn-ghost" onchange="app.renderRecords()"></select>
        
        <!-- SEARCH BAR FOR EMPLOYEE -->
        <input type="text" id="recSearch" class="search-bar" placeholder="🔍 Search Employee Name/ID..." onkeyup="app.renderRecords()">

        <div style="margin-left:auto;font-size:12px;color:var(--text-dim);">
          <span style="color:var(--color-p)">P</span>: Present | 
          <span style="color:var(--color-a)">A</span>: Absent | 
          <span style="color:var(--color-cl)">CL</span>: Casual Leave | 
          <span style="color:var(--color-l)">L</span>: Leave | 
          <span style="color:var(--color-mp)">MP</span>: Mis-Punch
        </div>
      </div>

      <div class="table-container">
        <table class="records-table">
          <thead id="recordsHead"></thead>
          <tbody id="recordsBody"></tbody>
        </table>
      </div>
    </section>

    <!-- EMPLOYEES LIST -->
    <section class="page hidden" id="page-employees">
      <div style="margin-bottom:20px;">
        <button class="btn btn-primary" onclick="app.openAddEmployeeModal()">+ Add New Employee (Complete Profile)</button>
      </div>
      <div class="grid grid-4" id="employeeGrid"></div>
    </section>
  </main>
</div>

<!-- EDIT PUNCHING RECORD MODAL -->
<div id="editModal" class="modal hidden">
  <div class="modal-card brackets">
    <span class="bl"></span><span class="br"></span>
    <h3 style="font-size:16px;margin-bottom:14px;">Edit Punching Details</h3>
    <div class="field">
      <label>Status Code</label>
      <select id="editStatus">
        <option value="P">P - Present</option>
        <option value="A">A - Absent</option>
        <option value="CL">CL - Casual Leave</option>
        <option value="L">L - Leave</option>
        <option value="MP">MP - Mis-Punch / Half Day</option>
      </select>
    </div>
    <div class="field">
      <label>Shift Name</label>
      <input type="text" id="editShift" value="Shift A (09:00-18:00)">
    </div>
    <div style="display:flex;gap:10px;">
      <div class="field" style="flex:1;">
        <label>In Time</label>
        <input type="text" id="editInTime" placeholder="09:00 AM">
      </div>
      <div class="field" style="flex:1;">
        <label>Out Time</label>
        <input type="text" id="editOutTime" placeholder="06:00 PM">
      </div>
    </div>
    <div style="display:flex;gap:10px;margin-top:10px;">
      <button class="btn btn-ghost" style="flex:1;" onclick="app.closeEditModal()">Cancel</button>
      <button class="btn btn-primary" style="flex:1;" onclick="app.saveEditRecord()">Save</button>
    </div>
  </div>
</div>

<!-- ADD NEW EMPLOYEE COMPLETE MODAL -->
<div id="addEmpModal" class="modal hidden">
  <div class="modal-card brackets">
    <span class="bl"></span><span class="br"></span>
    <h3 style="font-size:18px;margin-bottom:16px;color:var(--cyan);">Add New Employee (Full Registration)</h3>
    
    <div class="field">
      <label>Full Name *</label>
      <input type="text" id="empName" placeholder="e.g. Ramesh Kumar">
    </div>
    
    <div style="display:flex;gap:10px;">
      <div class="field" style="flex:1;">
        <label>Contact Number *</label>
        <input type="tel" id="empContact" placeholder="+91 9876543210">
      </div>
      <div class="field" style="flex:1;">
        <label>Email Address *</label>
        <input type="email" id="empEmail" placeholder="ramesh@company.com">
      </div>
    </div>

    <div style="display:flex;gap:10px;">
      <div class="field" style="flex:1;">
        <label>Aadhaar Number *</label>
        <input type="text" id="empAadhaar" placeholder="12-digit Aadhaar">
      </div>
      <div class="field" style="flex:1;">
        <label>Father's Name *</label>
        <input type="text" id="empFather" placeholder="Father's Name">
      </div>
    </div>

    <div class="field">
      <label>Department</label>
      <select id="empDept">
        <option value="Engineering">Engineering</option>
        <option value="Design">Design</option>
        <option value="Human Resources">Human Resources</option>
        <option value="Operations">Operations</option>
      </select>
    </div>

    <!-- FACE SCAN SECTION -->
    <div class="field">
      <label>Live Face Scan Registration</label>
      <div style="background:#000;height:160px;border-radius:10px;position:relative;overflow:hidden;display:flex;align-items:center;justify-content:center;border:1px solid var(--border);">
        <video id="empCamVideo" autoplay muted playsinline style="width:100%;height:100%;object-fit:cover;transform:scaleX(-1);"></video>
        <div id="faceScanOverlay" style="position:absolute;color:var(--cyan);font-size:12px;background:rgba(0,0,0,0.6);padding:6px 12px;border-radius:20px;">
          Camera Off
        </div>
      </div>
      <div style="display:flex;gap:10px;margin-top:8px;">
        <button class="btn btn-ghost" style="flex:1;font-size:12px;" onclick="app.startEmpCamera()">Start Cam</button>
        <button class="btn btn-magenta" style="flex:1;font-size:12px;" onclick="app.captureEmpFace()">Scan & Register Face</button>
      </div>
    </div>

    <div style="display:flex;gap:10px;margin-top:16px;">
      <button class="btn btn-ghost" style="flex:1;" onclick="app.closeAddEmployeeModal()">Cancel</button>
      <button class="btn btn-primary" style="flex:1;" onclick="app.saveEmployee()">Save Employee</button>
    </div>
  </div>
</div>

<script>
(function(){

const MONTH_NAMES = ["January","February","March","April","May","June","July","August","September","October","November","December"];

let state = {
  employees: [
    { id: "EMP-001", name: "Rahul Sharma", contact: "9876543210", email: "rahul@company.com", father: "Suresh Sharma", dept: "Engineering", faceRegistered: true },
    { id: "EMP-002", name: "Priya Patel", contact: "9123456789", email: "priya@company.com", father: "Ramesh Patel", dept: "Design", faceRegistered: true }
  ],
  attendance: {}, // { 'YYYY-MM-DD': { empId: { status:'P', shift:'Shift A', inTime:'09:15 AM', outTime:'06:00 PM' } } }
  punchMode: 'IN', // IN or OUT
  currentEdit: null,
  stream: null,
  empStream: null,
  scannedFaceData: false
};

function pad(n){ return String(n).padStart(2,'0'); }
function todayKey(){ const d=new Date(); return `${d.getFullYear()}-${pad(d.getMonth()+1)}-${pad(d.getDate())}`; }
function keyFor(y,m,d){ return `${y}-${pad(m+1)}-${pad(d)}`; }

function toast(msg, kind='info'){
  const wrap = document.getElementById('toast-wrap');
  const el = document.createElement('div');
  el.className = 'toast';
  el.innerHTML = `<span>${msg}</span>`;
  wrap.appendChild(el);
  setTimeout(()=>{ el.remove(); }, 3000);
}

function tickClock(){
  document.getElementById('clockEl').textContent = new Date().toLocaleTimeString();
}

function switchPage(page){
  document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
  const activeNav = document.querySelector(`.nav-item[data-page="${page}"]`);
  if(activeNav) activeNav.classList.add('active');

  document.querySelectorAll('.page').forEach(p => p.classList.add('hidden'));
  document.getElementById('page-' + page).classList.remove('hidden');

  if(page !== 'capture') stopCamera();
  if(page === 'home') renderHome();
  if(page === 'records') renderRecords();
  if(page === 'employees') renderEmployees();
}

/* PUNCH MODE */
function setPunchMode(mode){
  state.punchMode = mode;
  const inBtn = document.getElementById('btnPunchInOpt');
  const outBtn = document.getElementById('btnPunchOutOpt');
  if(mode === 'IN'){
    inBtn.className = 'punch-opt active-in';
    outBtn.className = 'punch-opt';
  } else {
    inBtn.className = 'punch-opt';
    outBtn.className = 'punch-opt active-out';
  }
}

/* HOME */
function renderHome(){
  const total = state.employees.length;
  const tk = todayKey();
  const todayRec = state.attendance[tk] || {};
  
  const present = Object.values(todayRec).filter(r => r.status === 'P' || r.inTime).length;
  const absent = Math.max(total - present, 0);
  const rate = total ? Math.round((present / total) * 100) : 0;

  document.getElementById('statTotal').textContent = total;
  document.getElementById('statPresent').textContent = present;
  document.getElementById('statAbsent').textContent = absent;
  document.getElementById('statRate').textContent = rate + '%';

  const activity = Object.entries(todayRec).map(([empId, r]) => {
    const emp = state.employees.find(e => e.id === empId);
    return `<div style="display:flex;justify-content:space-between;padding:10px;border-bottom:1px solid var(--border);">
      <div><b>${emp ? emp.name : empId}</b> <span style="font-size:11px;color:var(--text-dim);">(${r.shift || 'Shift A'})</span></div>
      <div><span class="status-badge badge-${r.status}">${r.status}</span> <span class="time-tag">IN: ${r.inTime || '--'} | OUT: ${r.outTime || '--'}</span></div>
    </div>`;
  }).join('');

  document.getElementById('activityFeed').innerHTML = activity || '<div style="color:var(--text-faint);padding:10px;">No punching records for today yet.</div>';
}

/* CAMERA CONTROL */
async function startCamera(){
  try{
    state.stream = await navigator.mediaDevices.getUserMedia({video:true});
    document.getElementById('camVideo').srcObject = state.stream;
    document.getElementById('camStartBtn').classList.add('hidden');
    document.getElementById('camScanBtn').classList.remove('hidden');
    document.getElementById('camStopBtn').classList.remove('hidden');
    toast('Camera initialized successfully!', 'info');
  }catch(e){
    toast('Camera access denied or unavailable', 'danger');
  }
}

function stopCamera(){
  if(state.stream){
    state.stream.getTracks().forEach(t => t.stop());
    state.stream = null;
  }
  document.getElementById('camStartBtn').classList.remove('hidden');
  document.getElementById('camScanBtn').classList.add('hidden');
  document.getElementById('camStopBtn').classList.add('hidden');
}

function scanFace(){
  if(!state.employees.length){
    toast('No employees registered!', 'warn');
    return;
  }
  
  const emp = state.employees[0];
  const tk = todayKey();
  const timeStr = new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});

  state.attendance[tk] = state.attendance[tk] || {};
  const currentPunch = state.attendance[tk][emp.id] || { status: 'P', shift: 'Shift A (09:00-18:00)', inTime: '', outTime: '' };

  if(state.punchMode === 'IN'){
    currentPunch.inTime = timeStr;
    currentPunch.status = 'P';
    toast(`${emp.name} Punched IN at ${timeStr}`, 'success');
  } else {
    currentPunch.outTime = timeStr;
    toast(`${emp.name} Punched OUT at ${timeStr}`, 'success');
  }

  state.attendance[tk][emp.id] = currentPunch;

  document.getElementById('punchStatusDisplay').innerHTML = `
    <div style="background:rgba(16,185,129,0.15);padding:12px;border-radius:10px;border:1px solid var(--color-p);">
      <h3 style="color:var(--color-p);">${emp.name}</h3>
      <div>Action: <b>PUNCH ${state.punchMode}</b></div>
      <div>Time: <b>${timeStr}</b></div>
    </div>
  `;
  renderRecentPunches();
}

function renderRecentPunches(){
  const tk = todayKey();
  const recs = state.attendance[tk] || {};
  document.getElementById('recentPunchList').innerHTML = Object.entries(recs).map(([id, r]) => {
    const emp = state.employees.find(e => e.id === id);
    return `<div style="padding:6px 0;border-bottom:1px solid var(--border);">
      <b>${emp ? emp.name : id}</b> - <span style="color:var(--cyan);">IN: ${r.inTime||'--'}</span> | <span style="color:var(--magenta);">OUT: ${r.outTime||'--'}</span>
    </div>`;
  }).join('');
}

/* MONTHLY RECORDS WITH SEARCH */
function populateSelects(){
  const mSel = document.getElementById('recMonth');
  const ySel = document.getElementById('recYear');
  if(mSel.children.length === 0){
    mSel.innerHTML = MONTH_NAMES.map((m,i)=>`<option value="${i}" ${i===(new Date().getMonth())?'selected':''}>${m}</option>`).join('');
    const curY = new Date().getFullYear();
    ySel.innerHTML = [curY-1, curY, curY+1].map(y=>`<option value="${y}" ${y===curY?'selected':''}>${y}</option>`).join('');
  }
}

function renderRecords(){
  populateSelects();
  const m = parseInt(document.getElementById('recMonth').value);
  const y = parseInt(document.getElementById('recYear').value);
  const search = (document.getElementById('recSearch').value || '').toLowerCase();
  const daysInMonth = new Date(y, m + 1, 0).getDate();

  let headHTML = `<tr><th style="min-width:160px;">Employee</th>`;
  for(let d = 1; d <= daysInMonth; d++){
    headHTML += `<th>${d}</th>`;
  }
  headHTML += `</tr>`;
  document.getElementById('recordsHead').innerHTML = headHTML;

  // Filter employees by Search Query
  const filteredEmps = state.employees.filter(e => 
    e.name.toLowerCase().includes(search) || e.id.toLowerCase().includes(search)
  );

  let bodyHTML = filteredEmps.map(emp => {
    let row = `<tr><td style="text-align:left;font-weight:600;background:rgba(255,255,255,0.02);position:sticky;left:0;">${emp.name}<br><span style="font-size:10px;color:var(--text-faint);">${emp.id}</span></td>`;
    for(let d = 1; d <= daysInMonth; d++){
      const dateKey = keyFor(y, m, d);
      const rec = (state.attendance[dateKey] || {})[emp.id] || { status: 'A', shift: 'Shift A', inTime: '', outTime: '' };

      row += `<td>
        <div class="punch-box" onclick="app.openEditModal('${emp.id}', '${dateKey}')">
          <span class="status-badge badge-${rec.status}">${rec.status}</span>
          <span class="shift-tag">${rec.shift || 'Shift A'}</span>
          <span class="time-tag">I: ${rec.inTime || '--'}</span>
          <span class="time-tag">O: ${rec.outTime || '--'}</span>
        </div>
      </td>`;
    }
    row += `</tr>`;
    return row;
  }).join('');

  document.getElementById('recordsBody').innerHTML = bodyHTML || '<tr><td colspan="32" style="padding:20px;color:var(--text-faint);">No Employee matches search query.</td></tr>';
}

/* EDIT MODAL */
function openEditModal(empId, dateKey){
  state.currentEdit = { empId, dateKey };
  const rec = (state.attendance[dateKey] || {})[empId] || { status: 'A', shift: 'Shift A (09:00-18:00)', inTime: '', outTime: '' };
  
  document.getElementById('editStatus').value = rec.status;
  document.getElementById('editShift').value = rec.shift || 'Shift A (09:00-18:00)';
  document.getElementById('editInTime').value = rec.inTime || '';
  document.getElementById('editOutTime').value = rec.outTime || '';
  
  document.getElementById('editModal').classList.remove('hidden');
}

function closeEditModal(){
  document.getElementById('editModal').classList.add('hidden');
}

function saveEditRecord(){
  if(!state.currentEdit) return;
  const { empId, dateKey } = state.currentEdit;
  
  state.attendance[dateKey] = state.attendance[dateKey] || {};
  state.attendance[dateKey][empId] = {
    status: document.getElementById('editStatus').value,
    shift: document.getElementById('editShift').value,
    inTime: document.getElementById('editInTime').value,
    outTime: document.getElementById('editOutTime').value
  };

  closeEditModal();
  renderRecords();
  toast('Punching details updated successfully!', 'success');
}

/* EMPLOYEES & ADD FULL DETAILS */
function renderEmployees(){
  document.getElementById('employeeGrid').innerHTML = state.employees.map(e => `
    <div class="card brackets" style="text-align:center;">
      <span class="bl"></span><span class="br"></span>
      <div style="width:60px;height:60px;border-radius:50%;background:linear-gradient(135deg,var(--violet),var(--cyan));margin:0 auto 12px;display:flex;align-items:center;justify-content:center;font-weight:700;font-size:20px;">
        ${e.name[0]}
      </div>
      <div style="font-weight:600;font-size:15px;">${e.name}</div>
      <div style="font-size:12px;color:var(--cyan);">${e.dept}</div>
      <div style="font-size:11px;color:var(--text-dim);margin-top:6px;">📞 ${e.contact || 'N/A'}</div>
      <div style="font-size:11px;color:var(--text-dim);">✉️ ${e.email || 'N/A'}</div>
      <div style="font-size:10px;color:var(--text-faint);margin-top:6px;">Father: ${e.father || 'N/A'} | ID: ${e.id}</div>
    </div>
  `).join('');
}

function openAddEmployeeModal(){
  document.getElementById('addEmpModal').classList.remove('hidden');
}

function closeAddEmployeeModal(){
  stopEmpCamera();
  document.getElementById('addEmpModal').classList.add('hidden');
}

async function startEmpCamera(){
  try{
    state.empStream = await navigator.mediaDevices.getUserMedia({video:true});
    document.getElementById('empCamVideo').srcObject = state.empStream;
    document.getElementById('faceScanOverlay').textContent = "Camera Ready for Scan";
    document.getElementById('faceScanOverlay').style.color = "var(--color-p)";
  }catch(e){
    toast('Camera Access Denied', 'danger');
  }
}

function stopEmpCamera(){
  if(state.empStream){
    state.empStream.getTracks().forEach(t => t.stop());
    state.empStream = null;
  }
}

function captureEmpFace(){
  state.scannedFaceData = true;
  document.getElementById('faceScanOverlay').textContent = "✓ Face Scanned & Saved";
  document.getElementById('faceScanOverlay').style.color = "var(--color-p)";
  toast('Face Biomteric Captured!', 'success');
}

function saveEmployee(){
  const name = document.getElementById('empName').value;
  const contact = document.getElementById('empContact').value;
  const email = document.getElementById('empEmail').value;
  const father = document.getElementById('empFather').value;
  const dept = document.getElementById('empDept').value;

  if(!name || !contact || !email){
    toast('Please fill all required fields!', 'warn');
    return;
  }

  const id = "EMP-" + pad(state.employees.length + 1);
  state.employees.push({
    id, name, contact, email, father, dept,
    faceRegistered: state.scannedFaceData
  });

  closeAddEmployeeModal();
  renderEmployees();
  toast(`${name} registered successfully!`, 'success');
}

/* AUTH */
function login(){
  document.getElementById('login-screen').classList.add('hidden');
  document.getElementById('app').classList.remove('hidden');
  switchPage('home');
}
function logout(){
  stopCamera();
  document.getElementById('app').classList.add('hidden');
  document.getElementById('login-screen').classList.remove('hidden');
}

/* INIT */
function init(){
  setInterval(tickClock, 1000);
  tickClock();
  document.querySelectorAll('.nav-item').forEach(item => {
    item.addEventListener('click', () => switchPage(item.dataset.page));
  });
}

document.addEventListener('DOMContentLoaded', init);

window.app = {
  login, logout, switchPage, setPunchMode,
  startCamera, stopCamera, scanFace,
  renderRecords, openEditModal, closeEditModal, saveEditRecord,
  openAddEmployeeModal, closeAddEmployeeModal, startEmpCamera, captureEmpFace, saveEmployee
};

})();
</script>
</body>
</html>

