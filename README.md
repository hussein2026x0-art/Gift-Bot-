<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Adrian Super App</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        :root { --main-color: #0088cc; }
        body { font-family: 'Cairo', sans-serif; transition: 0.3s; background-color: #0b0e11; color: #fff; }
        .glass { background: rgba(255, 255, 255, 0.05); backdrop-filter: blur(10px); border: 1px solid rgba(255, 255, 255, 0.1); }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        
        /* عجلة الحظ */
        #wheel-container { position: relative; width: 280px; height: 280px; margin: 0 auto; }
        canvas { width: 100%; height: 100%; border-radius: 50%; border: 5px solid #30363d; transition: transform 4s cubic-bezier(0.1, 0, 0.1, 1); }
        #wheel-pointer { position: absolute; top: -10px; left: 50%; transform: translateX(-50%); width: 0; height: 0; border-left: 15px solid transparent; border-right: 15px solid transparent; border-top: 30px solid #f0b90b; z-index: 10; }

        /* الألعاب والأنيميشن */
        .reaction { position: fixed; bottom: 80px; pointer-events: none; animation: flyUp 2s forwards; font-size: 2rem; z-index: 100; }
        @keyframes flyUp { 0% { transform: translateY(0) opacity(1); } 100% { transform: translateY(-300px) opacity(0); } }
    </style>
</head>
<body class="p-4 pb-24">

    <header class="glass p-4 rounded-3xl mb-6 flex justify-between items-center">
        <div class="flex items-center gap-3">
            <div id="user-photo" class="w-12 h-12 rounded-full bg-blue-600 flex items-center justify-center font-bold text-xl">👤</div>
            <div>
                <h2 id="user-name" class="font-bold text-sm">المستخدم</h2>
                <span class="text-[10px] bg-yellow-500/20 text-yellow-500 px-2 py-0.5 rounded-full">🏆 ناجي محترف</span>
            </div>
        </div>
        <div class="text-left">
            <p class="text-[10px] text-gray-400">المستوى 12</p>
            <div class="w-20 h-1.5 bg-gray-700 rounded-full mt-1"><div class="w-3/4 h-full bg-blue-500 rounded-full"></div></div>
        </div>
    </header>

    <main>
        <section id="game-tab" class="tab-content active text-center">
            <div class="grid grid-cols-3 gap-3 mb-6">
                <div class="glass p-3 rounded-2xl text-center">
                    <p class="text-[10px] text-gray-400 text-nowrap">فوز</p>
                    <p class="font-black text-blue-400">24</p>
                </div>
                <div class="glass p-3 rounded-2xl text-center">
                    <p class="text-[10px] text-gray-400 text-nowrap">أحكام</p>
                    <p class="font-black text-red-400">12</p>
                </div>
                <div class="glass p-3 rounded-2xl text-center">
                    <p class="text-[10px] text-gray-400 text-nowrap">نقاط XP</p>
                    <p class="font-black text-green-400">1.2k</p>
                </div>
            </div>

            <div id="wheel-container" class="mb-6">
                <div id="wheel-pointer"></div>
                <canvas id="wheelCanvas" width="500" height="500"></canvas>
            </div>
            <button onclick="spinWheel()" id="spin-btn" class="bg-blue-600 hover:bg-blue-700 text-white font-black py-4 px-12 rounded-2xl shadow-lg shadow-blue-900/20 active:scale-95 transition-transform">
                دوران العجلة 🔥
            </button>
        </section>

        <section id="arcade-tab" class="tab-content">
            <h3 class="text-xl font-black mb-4">ركن الألعاب 🕹️</h3>
            <div class="grid grid-cols-1 gap-4">
                <div class="glass p-4 rounded-2xl flex justify-between items-center">
                    <div>
                        <h4 class="font-bold">حجر ورقة مقص</h4>
                        <p class="text-xs text-gray-400">تحدى البوت واربح نقاط</p>
                    </div>
                    <button class="bg-blue-600 px-4 py-2 rounded-xl text-xs">لعب</button>
                </div>
                <div class="glass p-4 rounded-2xl flex justify-between items-center">
                    <div>
                        <h4 class="font-bold">تحدي النقر السريع</h4>
                        <p class="text-xs text-gray-400">أسرع 10 نقرات في العالم</p>
                    </div>
                    <button class="bg-green-600 px-4 py-2 rounded-xl text-xs">بدء</button>
                </div>
                <div class="glass p-4 rounded-2xl flex justify-between items-center">
                    <div>
                        <h4 class="font-bold">توقعات TON</h4>
                        <p class="text-xs text-gray-400">صعود أم هبوط؟</p>
                    </div>
                    <button class="bg-yellow-600 px-4 py-2 rounded-xl text-xs">توقع</button>
                </div>
            </div>
        </section>

        <section id="chat-tab" class="tab-content">
            <div class="glass p-6 rounded-3xl mb-4">
                <h3 class="font-black mb-2">صندوق صارحني ✉️</h3>
                <textarea class="w-full bg-black/30 border border-white/10 rounded-xl p-3 text-sm" rows="3" placeholder="أرسل رسالة مجهولة لأدريان..."></textarea>
                <button class="w-full bg-white text-black font-bold py-2 rounded-xl mt-2">إرسال مجهول</button>
            </div>
            <div id="reactions-btns" class="flex justify-around gap-2 overflow-x-auto">
                <button onclick="sendReaction('🔥')" class="glass p-3 rounded-full">🔥</button>
                <button onclick="sendReaction('😂')" class="glass p-3 rounded-full">😂</button>
                <button onclick="sendReaction('❤️')" class="glass p-3 rounded-full">❤️</button>
                <button onclick="sendReaction('👏')" class="glass p-3 rounded-full">👏</button>
            </div>
        </section>

        <section id="settings-tab" class="tab-content">
            <h3 class="text-xl font-black mb-4">الإعدادات</h3>
            <div class="glass rounded-2xl overflow-hidden text-sm">
                <div class="p-4 border-b border-white/5 flex justify-between items-center">
                    <span>الوضع الليلي</span>
                    <input type="checkbox" checked class="w-5 h-5">
                </div>
                <div class="p-4 border-b border-white/5 flex justify-between items-center">
                    <span>لون الهوية</span>
                    <div class="flex gap-2">
                        <div onclick="setTheme('#0088cc')" class="w-6 h-6 rounded-full bg-blue-500 cursor-pointer"></div>
                        <div onclick="setTheme('#00c853')" class="w-6 h-6 rounded-full bg-green-500 cursor-pointer"></div>
                        <div onclick="setTheme('#ff3d00')" class="w-6 h-6 rounded-full bg-red-500 cursor-pointer"></div>
                    </div>
                </div>
            </div>
        </section>
    </main>

    <div class="fixed bottom-[70px] left-0 right-0 glass h-8 flex items-center overflow-hidden z-40">
        <div class="whitespace-nowrap animate-marquee flex gap-10 items-center px-4">
            <span class="text-[10px]">💎 TON: <span id="ton-price" class="text-blue-400">...</span></span>
            <span class="text-[10px]">⚽ ريال مدريد 2 - 0 برشلونة (انتهت)</span>
            <span class="text-[10px]">₿ BTC: <span class="text-orange-400">$64,230</span></span>
        </div>
    </div>

    <nav class="fixed bottom-0 left-0 right-0 glass h-16 flex justify-around items-center rounded-t-3xl border-t border-white/10 z-50">
        <button onclick="showTab('game-tab')" class="flex flex-col items-center opacity-50 active-tab"><span class="text-xl">🎡</span><span class="text-[10px]">اللعب</span></button>
        <button onclick="showTab('arcade-tab')" class="flex flex-col items-center opacity-50"><span class="text-xl">🕹️</span><span class="text-[10px]">آركيد</span></button>
        <button onclick="showTab('chat-tab')" class="flex flex-col items-center opacity-50"><span class="text-xl">💬</span><span class="text-[10px]">الدردشة</span></button>
        <button onclick="showTab('settings-tab')" class="flex flex-col items-center opacity-50"><span class="text-xl">⚙️</span><span class="text-[10px]">حسابي</span></button>
    </nav>

    <style>
        .animate-marquee { display: inline-flex; animation: marquee 15s linear infinite; }
        @keyframes marquee { 0% { transform: translateX(-100%); } 100% { transform: translateX(100%); } }
        .active-tab { opacity: 1 !important; color: var(--main-color); }
    </style>

    <script>
        const tg = window.Telegram.WebApp;
        tg.expand();

        // بيانات المستخدم
        if(tg.initDataUnsafe.user) {
            document.getElementById('user-name').innerText = tg.initDataUnsafe.user.first_name;
            if(tg.initDataUnsafe.user.photo_url) {
                document.getElementById('user-photo').innerHTML = `<img src="${tg.initDataUnsafe.user.photo_url}" class="rounded-full">`;
            }
        }

        // تبديل الألسنة
        function showTab(tabId) {
            document.querySelectorAll('.tab-content').forEach(t => t.classList.remove('active'));
            document.getElementById(tabId).classList.add('active');
            document.querySelectorAll('nav button').forEach(b => b.classList.remove('active-tab'));
            event.currentTarget.classList.add('active-tab');
        }

        // نظام العجلة
        const canvas = document.getElementById('wheelCanvas');
        const ctx = canvas.getContext('2d');
        const players = ["أدريان", "محمد", "حسين", "علي", "أحمد", "يوسف"];
        const colors = ["#1e2329", "#30363d", "#0088cc", "#f0b90b", "#ff3b30", "#00c853"];

        function drawWheel() {
            const arc = (Math.PI * 2) / players.length;
            players.forEach((p, i) => {
                ctx.beginPath();
                ctx.fillStyle = colors[i % colors.length];
                ctx.moveTo(250, 250);
                ctx.arc(250, 250, 250, i * arc, (i + 1) * arc);
                ctx.fill();
                ctx.save();
                ctx.fillStyle = "#fff";
                ctx.translate(250 + Math.cos(i * arc + arc/2) * 150, 250 + Math.sin(i * arc + arc/2) * 150);
                ctx.rotate(i * arc + arc/2 + Math.PI/2);
                ctx.font = "bold 20px Cairo";
                ctx.fillText(p, -ctx.measureText(p).width/2, 0);
                ctx.restore();
            });
        }
        drawWheel();

        let spinning = false;
        function spinWheel() {
            if(spinning) return;
            spinning = true;
            const randDeg = Math.floor(5000 + Math.random() * 5000);
            canvas.style.transform = `rotate(${randDeg}deg)`;
            setTimeout(() => {
                spinning = false;
                tg.HapticFeedback.notificationOccurred('success');
                alert("وقع الاختيار على الضحية! 🔥");
            }, 4000);
        }

        // سعر TON
        async function getPrices() {
            try {
                const res = await fetch('https://api.binance.com/api/v3/ticker/price?symbol=TONUSDT');
                const data = await res.json();
                document.getElementById('ton-price').innerText = "$" + parseFloat(data.price).toFixed(2);
            } catch(e) {}
        }
        setInterval(getPrices, 5000);
        getPrices();

        // تفاعلات الحية
        function sendReaction(emoji) {
            const el = document.createElement('div');
            el.className = 'reaction';
            el.innerText = emoji;
            el.style.left = (Math.random() * 80 + 10) + '%';
            document.body.appendChild(el);
            setTimeout(() => el.remove(), 2000);
            tg.HapticFeedback.impactOccurred('light');
        }

        // تغيير الثيم
        function setTheme(color) {
            document.documentElement.style.setProperty('--main-color', color);
            document.getElementById('spin-btn').style.backgroundColor = color;
            tg.MainButton.setParams({color: color});
        }
    </script>
</body>
</html>
