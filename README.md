<style>
    /* ตั้งค่าให้หน้าจอพอดีกับ Device เสมอ */
    :root { --app-height: 100dvh; }
    body {
        height: var(--app-height);
        margin: 0;
        display: flex;
        flex-direction: column;
        overflow: hidden; /* ห้ามเลื่อนทั้งหน้าจอเด็ดขาด */
        background-color: #0f172a;
    }

    /* ส่วนที่ต้องอยู่กับที่ (Fixed) */
    .fixed-top { flex-shrink: 0; }
    .fixed-bottom { flex-shrink: 0; }

    /* ส่วน Action Log: ล็อคความสูงให้แสดงประมาณ 5 แถว */
    .log-container {
        flex-grow: 1; /* กินพื้นที่ที่เหลือระหว่างปุ่มบนกับปุ่มล่าง */
        overflow-y: auto; /* เลื่อนได้เฉพาะส่วนนี้ */
        background-color: #f1f5f9;
        border-top: 2px solid #e2e8f0;
        border-bottom: 2px solid #e2e8f0;
        /* กำหนดความสูงเพื่อให้เห็นประมาณ 5 รายการ (ปรับตัวเลขนี้ได้) */
        max-height: 220px; 
        min-height: 220px;
    }

    /* ตกแต่งตารางให้ดูง่ายขึ้น */
    #log-body tr {
        height: 40px; /* ฟิกความสูงแถวเพื่อให้คำนวณพื้นที่ง่าย */
    }

    .no-scrollbar::-webkit-scrollbar { display: none; }
</style>

<body>
    <div class="fixed-top p-4 bg-slate-900 text-white">
        </div>

    <div class="fixed-top p-3 bg-white border-b">
        </div>

    <div class="log-container no-scrollbar">
        <table class="w-full text-left border-separate border-spacing-y-1 p-3">
            <thead class="sticky top-0 bg-slate-50 z-10">
                <tr class="text-[9px] text-slate-400 font-black uppercase">
                    <th class="py-1 w-16 text-center">Time</th>
                    <th class="py-1 px-2">Action Log</th>
                </tr>
            </thead>
            <tbody id="log-body" class="text-xs font-bold text-slate-700">
                </tbody>
        </table>
    </div>

    <div class="fixed-bottom bg-white border-t p-3 flex items-center gap-3">
        </div>
</body>
