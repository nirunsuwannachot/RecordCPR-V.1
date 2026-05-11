# RecordCPR-V.1
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>ACLS Pro 2025 - Fixed Footer</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        :root { --app-height: 100dvh; }
        
        html, body {
            height: var(--app-height);
            margin: 0;
            overflow: hidden; /* ล็อคหน้าจอหลักไม่ให้เลื่อน */
            background-color: #0f172a;
        }

        /* แบ่งหน้าจอเป็น 3 ส่วนชัดเจน */
        .app-grid {
            display: grid;
            grid-template-rows: auto 1fr auto; /* บน-กลาง-ล่าง */
            height: var(--app-height);
            width: 100%;
        }

        /* ส่วนกลาง (Action Log) ที่เลื่อนได้ */
        .scrollable-log {
            overflow-y: auto;
            -webkit-overflow-scrolling: touch;
            background-color: #f8fafc;
        }

        .drug-btn {
            display: flex; align-items: center; justify-content: center;
            height: 44px; font-size: 10px; font-weight: 800;
            border-radius: 8px; transition: all 0.1s;
            text-transform: uppercase;
        }
        .drug-btn:active { transform: scale(0.95); }
        .no-scrollbar::-webkit-scrollbar { display: none; }
    </style>
</head>
<body>

    <div class="app-grid">
        
        <div class="p-4 bg-slate-900 text-white shadow-xl z-10">
            <div class="flex justify-between items-center mb-3">
                <div>
                    <h1 class="text-xl font-black text-red-500 italic leading-none">ACLS 2025</h1>
                    <p class="text-[9px] text-slate-500 font-bold mt-1">NIRUN SUWANNACHOT</p>
                </div>
                <div class="text-right">
                    <p id="total-timer" class="text-2xl font-mono font-bold text-emerald-400 leading-none">00:00</p>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-2 mb-3">
                <div class="bg-slate-800 p-2 rounded-xl border-2 border-blue-500 text-center">
                    <p class="text-[9px] text-blue-400 font-bold uppercase">Rhythm Check</p>
                    <p id="cpr-timer" class="text-3xl font-mono font-black">02:00</p>
                </div>
                <div class="bg-slate-800 p-2 rounded-xl border border-slate-700 text-[10px] flex flex-col justify-center">
                    <p class="text-yellow-500 font-black uppercase">Algorithm</p>
                    <div id="guidance-text" class="text-slate-400 italic">Waiting...</div>
                </div>
            </div>

            <div class="grid grid-cols-3 gap-2 mb-3">
                <button onclick="toggleStart()" id="btn-start" class="bg-emerald-600 py-3 rounded-lg font-black text-xs uppercase shadow-md">Start</button>
                <button onclick="setRhythm('Shockable')" class="bg-rose-700 py-3 rounded-lg font-black text-xs uppercase">Shockable</button>
                <button onclick="setRhythm('Non-Shockable')" class="bg-blue-700 py-3 rounded-lg font-black text-[9px] uppercase">Non-Shock</button>
            </div>

            <div class="bg-white p-2 rounded-xl border border-slate-200">
                <div class="grid grid-cols-3 gap-2 mb-2">
                    <button onclick="recordAction('💓 START CPR')" class="drug-btn border-2 border-emerald-500 text-emerald-700">START CPR</button>
                    <button onclick="recordAction('💉 IV/IO')" class="drug-btn border-2 border-sky-600 text-sky-700">IV/IO</button>
                    <button onclick="recordAction('💧 FLUID')" class="drug-btn border-2 border-blue-400 text-blue-700 uppercase">Fluid</button>
                </div>
                <div class="grid grid-cols-3 gap-2">
                    <button onclick="recordDefib()" class="drug-btn border-2 border-rose-500 text-rose-600 italic">DEFIB 200J</button>
                    <button onclick="recordEpi()" class="drug-btn bg-blue-600 text-white shadow-md">EPINEPHRINE</button>
                    <button onclick="recordAmio()" class="drug-btn border-2 border-purple-500 text-purple-600">AMIO 300</button>
                </div>
            </div>
        </div>

        <div class="scrollable-log no-scrollbar">
            <table class="w-full text-left border-separate border-spacing-y-1 p-3">
                <thead class="sticky top-0 bg-slate-50 z-10 shadow-sm">
                    <tr class="text-[9px] text-slate-400 font-black uppercase">
                        <th class="py-2 w-16 text-center">Time</th>
                        <th class="py-2 px-2">Action Log</th>
                    </tr>
                </thead>
                <tbody id="log-body" class="text-xs font-bold text-slate-700"></tbody>
            </table>
        </div>

        <div class="p-4 bg-white border-t border-slate-200 shadow-[0_-4px_15px_rgba(0,0,0,0.05)] z-10">
            <div class="flex items-center gap-2">
                <button onclick="recordAction('🏁 ROSC ACHIEVED')" class="flex-[2.5] bg-emerald-500 text-white py-4 rounded-xl font-black text-xs uppercase shadow-md active:scale-95 transition-transform">
                    ROSC ACHIEVED
                </button>

                <div class="flex gap-1.5 flex-[1.5]">
                    <div class="bg-rose-600 text-white flex-1 py-1 rounded-lg shadow-sm text-center">
                        <p class="text-[7px] font-black leading-none uppercase">Shock</p>
                        <p id="dash-shock" class="text-lg font-black leading-none mt-0.5">0</p>
                    </div>
                    <div class="bg-blue-600 text-white flex-1 py-1 rounded-lg shadow-sm text-center">
                        <p class="text-[7px] font-black leading-none uppercase">Epi</p>
                        <p id="dash-epi" class="text-lg font-black leading-none mt-0.5">0</p>
                    </div>
                </div>

                <button onclick="closeCase()" class="flex-1 bg-slate-800 text-white py-4 rounded-xl font-black text-[10px] uppercase shadow-md active:bg-black">
                    CLOSE
                </button>
            </div>
        </div>

    </div>

    <script>
        let totalSec = 0, cprSec = 120, isRunning = false, mainInterval = null;
        let defibCount = 0, epiCount = 0;

        function formatTime(s) {
            const m = Math.floor(s / 60).toString().padStart(2, '0');
            const sec = (s % 60).toString().padStart(2, '0');
            return `${m}:${sec}`;
        }

        function toggleStart() {
            const btn = document.getElementById('btn-start');
            if (!isRunning) {
                isRunning = true; btn.innerText = 'PAUSE'; btn.classList.replace('bg-emerald-600', 'bg-amber-600');
                if (totalSec === 0) recordAction('🚀 Code Blue Activated');
                mainInterval = setInterval(() => {
                    totalSec++; cprSec--;
                    document.getElementById('total-timer').innerText = formatTime(totalSec);
                    document.getElementById('cpr-timer').innerText = formatTime(cprSec);
                    if (cprSec <= 0) { cprSec = 120; recordAction('⚠️ Check Rhythm!'); }
                }, 1000);
            } else {
                isRunning = false; btn.innerText = 'RESUME'; btn.classList.replace('bg-amber-600', 'bg-emerald-600');
                clearInterval(mainInterval);
            }
        }

        function setRhythm(type) {
            document.getElementById('guidance-text').innerHTML = type === 'Shockable' ? '<b class="text-rose-500 uppercase">⚡ Shockable</b>' : '<b class="text-blue-500 uppercase">💉 Non-Shock</b>';
            recordAction(`🔍 Rhythm: ${type}`);
            cprSec = 120;
        }

        function recordDefib() {
            defibCount++; document.getElementById('dash-shock').innerText = defibCount;
            recordAction(`⚡ Defib #${defibCount} (200J)`);
            cprSec = 120;
        }

        function recordEpi() {
            epiCount++; document.getElementById('dash-epi').innerText = epiCount;
            recordAction(`💉 Epinephrine #${epiCount}`);
        }

        function recordAction(msg) {
            const body = document.getElementById('log-body');
            const row = document.createElement('tr');
            row.className = "bg-white border-l-4 border-slate-300 shadow-sm";
            row.innerHTML = `<td class="py-2.5 text-center bg-slate-50 font-mono text-[10px] font-black">${formatTime(totalSec)}</td><td class="py-2.5 px-3 uppercase tracking-tighter">${msg}</td>`;
            body.insertBefore(row, body.firstChild);
            document.querySelector('.scrollable-log').scrollTop = 0;
        }

        function closeCase() {
            if(confirm("Confirm Close Case?")) {
                isRunning = false; clearInterval(mainInterval);
                alert("Summary:\nTime: " + formatTime(totalSec) + "\nShock: " + defibCount + "\nEpi: " + epiCount);
            }
        }
    </script>
</body>
</html>
