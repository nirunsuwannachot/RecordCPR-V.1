# RecordCPR-V.1
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>ACLS Pro Recorder</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* ล็อคความสูงหน้าจอให้เต็มพอดีกับ Browser ทุกค่าย */
        :root {
            --app-height: 100dvh;
        }
        html, body {
            height: var(--app-height);
            margin: 0;
            padding: 0;
            overflow: hidden; /* ห้ามเลื่อนทั้งหน้าจอ */
            background-color: #0f172a;
        }

        /* Container หลักที่แบ่งสัดส่วนหน้าจอ */
        .main-wrapper {
            display: flex;
            flex-direction: column;
            height: var(--app-height);
            width: 100%;
        }

        /* ส่วนที่เลื่อนได้ (Action Log) - บังคับความสูงให้พอดีพื้นที่ที่เหลือ */
        .log-section {
            flex-grow: 1; 
            overflow-y: auto;
            -webkit-overflow-scrolling: touch;
            background-color: #f1f5f9;
        }

        /* ปุ่มต่างๆ ห้ามโดนบีบขนาด */
        .no-shrink {
            flex-shrink: 0;
        }

        .drug-btn {
            display: flex; align-items: center; justify-content: center;
            height: 48px; font-size: 10px; font-weight: 900;
            border-radius: 8px; transition: all 0.1s;
            text-transform: uppercase;
        }
        .drug-btn:active { transform: scale(0.95); opacity: 0.8; }
        
        /* ปรับแต่ง Scrollbar ของ Log ให้ดูสะอาด */
        .log-section::-webkit-scrollbar { width: 4px; }
        .log-section::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
    </style>
</head>
<body>

    <div class="main-wrapper">
        
        <div class="no-shrink p-4 bg-slate-900 text-white shadow-xl">
            <div class="flex justify-between items-center mb-3">
                <div>
                    <h1 class="text-xl font-black text-red-500 italic leading-none">ACLS 2025</h1>
                    <p class="text-[9px] text-slate-500 font-bold mt-1">Nirun Suwannachot</p>
                </div>
                <div class="text-right">
                    <p class="text-[9px] text-slate-500 font-bold">TOTAL TIME</p>
                    <p id="total-timer" class="text-2xl font-mono font-bold text-emerald-400">00:00</p>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-2 mb-3">
                <div id="cpr-card" class="bg-slate-800 p-2 rounded-xl border-2 border-blue-500 text-center">
                    <p class="text-[9px] text-blue-400 font-bold uppercase">Rhythm Check</p>
                    <p id="cpr-timer" class="text-3xl font-mono font-black">02:00</p>
                </div>
                <div class="bg-slate-800 p-2 rounded-xl border border-slate-700 text-[10px] flex flex-col justify-center">
                    <p class="text-yellow-500 font-black uppercase">Guide</p>
                    <div id="guidance-text" class="text-slate-400 italic">Waiting...</div>
                </div>
            </div>

            <div class="grid grid-cols-3 gap-2">
                <button onclick="toggleStart()" id="btn-start" class="bg-emerald-600 py-3 rounded-lg font-black text-xs uppercase">Start</button>
                <button onclick="setRhythm('Shockable')" class="bg-rose-700 py-3 rounded-lg font-black text-xs uppercase">Shockable</button>
                <button onclick="setRhythm('Non-Shockable')" class="bg-blue-700 py-3 rounded-lg font-black text-[9px] uppercase">Non-Shock</button>
            </div>
        </div>

        <div class="no-shrink p-3 bg-white border-b border-slate-200">
            <div class="grid grid-cols-3 gap-2 mb-2">
                <button onclick="recordAction('💓 Start CPR')" class="drug-btn border-2 border-emerald-500 text-emerald-700 bg-emerald-50">START CPR</button>
                <button onclick="recordAction('💉 Access IV/IO')" class="drug-btn border-2 border-sky-600 text-sky-700 bg-sky-50">IV/IO</button>
                <button onclick="recordAction('💧 IV Fluid')" class="drug-btn border-2 border-blue-400 text-blue-700 bg-blue-50">FLUID</button>
            </div>
            <div class="grid grid-cols-3 gap-2 mb-2">
                <button onclick="recordDefib()" class="drug-btn border-2 border-rose-500 text-rose-600 bg-rose-50 italic">DEFIB 200J</button>
                <button onclick="recordEpi()" class="drug-btn bg-blue-600 text-white shadow-md">EPINEPHRINE</button>
                <button onclick="recordAmio()" class="drug-btn border-2 border-purple-500 text-purple-600 bg-white">AMIO 300</button>
            </div>
            <div class="grid grid-cols-2 gap-2">
                <button onclick="recordAction('💊 Amio 150mg')" class="drug-btn bg-slate-100 border border-slate-300 text-slate-700">AMIO 150</button>
                <button onclick="recordAction('🌬️ Adv. Airway')" class="drug-btn bg-orange-50 border border-orange-300 text-orange-700 italic">ADV. AIRWAY</button>
            </div>
        </div>

        <div class="log-section">
            <table class="w-full text-left border-separate border-spacing-y-1 p-3">
                <thead class="sticky top-0 bg-slate-50 z-10 shadow-sm">
                    <tr class="text-[9px] text-slate-400 font-black uppercase">
                        <th class="py-2 w-16 text-center">Time</th>
                        <th class="py-2 px-2">Action Log</th>
                    </tr>
                </thead>
                <tbody id="log-body" class="text-xs font-bold text-slate-700">
                    </tbody>
            </table>
        </div>

        <div class="no-shrink p-4 bg-white border-t flex items-center justify-between gap-3">
            <button onclick="recordAction('🏁 ROSC Achieved')" class="flex-1 bg-emerald-500 text-white py-4 rounded-2xl font-black text-xs uppercase shadow-lg">ROSC Achieved</button>
            <div class="flex gap-1.5">
                <div class="bg-rose-600 text-white px-3 py-1 rounded-xl shadow-md text-center">
                    <p class="text-[8px] font-black leading-none">SHOCK</p>
                    <p id="dash-shock" class="text-lg font-black leading-none">0</p>
                </div>
                <div class="bg-blue-600 text-white px-3 py-1 rounded-xl shadow-md text-center">
                    <p class="text-[8px] font-black leading-none">EPI</p>
                    <p id="dash-epi" class="text-lg font-black leading-none">0</p>
                </div>
            </div>
            <button onclick="closeCase()" class="bg-slate-800 text-white px-4 py-4 rounded-2xl font-black text-[10px] uppercase">Close</button>
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
                isRunning = true;
                btn.innerText = 'PAUSE'; btn.classList.replace('bg-emerald-600', 'bg-amber-600');
                if (totalSec === 0) recordAction('🚀 Code Blue Activated');
                mainInterval = setInterval(() => {
                    totalSec++; cprSec--;
                    document.getElementById('total-timer').innerText = formatTime(totalSec);
                    document.getElementById('cpr-timer').innerText = formatTime(cprSec);
                    if (cprSec <= 0) { cprSec = 120; recordAction('⚠️ Check Rhythm!'); }
                }, 1000);
            } else {
                isRunning = false;
                btn.innerText = 'RESUME'; btn.classList.replace('bg-amber-600', 'bg-emerald-600');
                clearInterval(mainInterval);
            }
        }

        function setRhythm(type) {
            document.getElementById('guidance-text').innerHTML = type === 'Shockable' ? '<b>⚡ Shockable:</b> Shock 200J -> CPR' : '<b>💉 Non-Shock:</b> Epi ASAP -> CPR';
            recordAction(`🔍 Rhythm: ${type}`);
            cprSec = 120;
        }

        function recordDefib() {
            defibCount++; document.getElementById('dash-shock').innerText = defibCount;
            recordAction(`⚡ Defibrillation #${defibCount} (200J)`);
            cprSec = 120;
        }

        function recordEpi() {
            epiCount++; document.getElementById('dash-epi').innerText = epiCount;
            recordAction(`💉 Epinephrine #${epiCount} (1 mg)`);
        }

        function recordAction(msg) {
            const body = document.getElementById('log-body');
            const row = document.createElement('tr');
            row.className = "bg-white border-l-4 border-slate-300 shadow-sm";
            row.innerHTML = `<td class="py-2.5 text-center bg-slate-50 font-mono text-[10px] font-black">${formatTime(totalSec)}</td><td class="py-2.5 px-3">${msg}</td>`;
            body.insertBefore(row, body.firstChild);
            // เลื่อน Log กลับไปบนสุดอัตโนมัติเมื่อมีการบันทึกใหม่
            document.querySelector('.log-section').scrollTop = 0;
        }

        function closeCase() {
            isRunning = false; clearInterval(mainInterval);
            alert("Case Closed at " + formatTime(totalSec));
        }
    </script>
</body>
</html>
