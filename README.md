<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Adrian Super App | Pro</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        :root { --main-color: #0088cc; --bg-dark: #0b0e11; --card-bg: rgba(255, 255, 255, 0.05); }
        body { font-family: 'Cairo', sans-serif; background-color: var(--bg-dark); color: #fff; overflow-x: hidden; user-select: none; }
        
        .glass { background: var(--card-bg); backdrop-filter: blur(15px); border: 1px solid rgba(255, 255, 255, 0.1); transition: 0.3s; }
        .glass:active { transform: scale(0.98); background: rgba(255, 255, 255, 0.08); }
        
        .tab-content { display: none; animation: fadeIn 0.4s ease; }
        .tab-content.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* عجلة الحظ المطورة */
        #wheel-container { position: relative; width: 300px; height: 300px; margin: 20px auto; filter: drop-shadow(0 0 20px rgba(0,0,0,0.5)); }
        canvas { width: 100%; height: 100%; border-radius: 50%; border: 8px solid #1e2329; transition: transform 4s cubic-bezier(0.1, 0, 0.1, 1); }
        #wheel-pointer { position: absolute; top: -15px; left: 50%; transform: translateX(-50%); width: 0; height: 0; border-left: 18px solid transparent; border-right: 18px solid transparent; border-top: 35px solid #f0b90b; z-index: 10; filter: drop-shadow(0 4px 4px rgba(0,0,0,0.5)); }

        /* مفاتيح التحكم Toggle */
        .switch { position: relative; display: inline-block; width: 44px; height: 24px; }
        .switch input { opacity: 0; width: 0; height: 0; }
        .slider { position: absolute; cursor: pointer; top: 0; left: 0; right: 0; bottom: 0; background-color: #333; transition: .4s; border-radius: 24px; }
        .slider:before { position: absolute; content: ""; height: 18px; width: 18px; left: 3px; bottom: 3px; background-color: white; transition: .4s; border-radius: 50%; }
        input:checked + .slider { background-color: var(--main-color); }
        input:checked + .slider:before { transform: translateX(20px); }

        .reaction { position: fixed; bottom: 100px; pointer-events: none; animation: flyUp 2s ease-out forwards; font-size: 2.5rem; z-index: 1000; text-shadow: 0 10px 20px rgba(0,0,0,0.5); }
        @keyframes flyUp { 0% { transform: translateY(0) scale(0.5); opacity: 0; } 20% { opacity: 1; transform: translateY(-20px) scale(1.2); } 100% { transform: translateY(-400px) scale(1); opacity: 0; } }
        
        .badge-glow { box-shadow: 0 0 15px var(--main-color); }
    </style>
</head>
<body class="p-4 pb-32">

    <header class="glass p-5 rounded-[2rem] mb-6 flex justify-between items-center relative overflow-hidden">
        <div class="absolute top-0 right-0 w-24 h-24 bg-blue-500/10 blur-3xl rounded-full"></div>
        <div class="flex items-center gap-4 z-10">
            <div id="user-photo-container" class="relative">
                <div id="user-photo" class="w-14 h-14 rounded-2xl bg-gradient-to-tr from-blue-600 to-purple-600 flex items-center justify-center font-black text-2xl border-2 border-white/10 shadow-lg overflow-hidden">👤</div>
                <div class="absolute -bottom-1 -right-1 w-5 h-5 bg-green-500 border-4 border-[#0b0e11] rounded-full"></div>
            </div>
            <div>
                <h2 id="user-name" class="font-black text-lg leading-tight">المستخدم...</h2>
                <div class="flex items-center gap-2">
                    <span class="text-[9px] font-bold bg-white/10 text-white px-2 py-0.5 rounded-md uppercase tracking-wider">ID: <span id="user-id">0000</span></span>
                    <span id="user-badge" class="text-[9px] font-bold bg-yellow-500 text-black px-2 py-0.5 rounded-md">PRO VIP</span>
                </div>
            </div>
        </div>
        <div class="text-left z-10">
            <div class="text-[10px] text-gray-400 font-bold mb-1">XP PROGRESS</div>
            <div class="w-24 h-2 bg-white/5 rounded-full overflow-hidden border border-white/5">
                <div id="xp-bar" class="w-2/3 h-full bg-gradient-to-r from-blue-500 to-cyan-400 shadow-[0_0_10px_#0088cc]"></div>
            </div>
            <p class="text-[10px] mt-1 font-bold text-blue-400 text-right">LVL 18</p>
        </div>
    </header>

    <main>
        <section id="game-tab" class="tab-content active">
            <div class="grid grid-cols-2 gap-4 mb-8">
                <div class="glass p-4 rounded-3xl flex items-center gap-3">
                    <div class="w-10 h-10 rounded-xl bg-blue-500/20 flex items-center justify-center text-xl">🛡️</div>
                    <div><p class="text-[10px] text-gray-400">الفوز</p><p class="font-black text-lg">128</p></div>
                </div>
                <div class="glass p-4 rounded-3xl flex items-center gap-3">
                    <div class="w-10 h-10 rounded-xl bg-red-500/20 flex items-center justify-center text-xl">⚖️</div>
                    <div><p class="text-[10px] text-gray-400">أحكام</p><p class="font-black text-lg">54</p></div>
                </div>
            </div>

            <div id="wheel-container">
                <div id="wheel-pointer"></div>
                <canvas id="wheelCanvas" width="500" height="500"></canvas>
            </div>
            
            <div class="mt-8 flex flex-col gap-3 items-center">
                <button onclick="spinWheel()" id="spin-btn" class="w-full max-w-[280px] bg-blue-600 hover:bg-blue-500 text-white font-black py-4 rounded-2xl shadow-xl transition-all active:scale-95 text-lg">
                    دوران العجلة 🔥
                </button>
                <p class="text-[10px] text-gray-500">سيتم اختيار ضحية عشوائية من القائمة</p>
            </div>
        </section>

        <section id="arcade-tab" class="tab-content">
            <h3 class="text-xl font-black mb-5 flex items-center gap-2"><span>🕹️</span> ركن الألعاب السريعة</h3>
            
            <div class="space-y-4">
                <div class="glass p-5 rounded-[2rem] flex justify-between items-center">
                    <div>
                        <h4 class="font-black text-blue-400">تحدي النقر (10 ثوانٍ)</h4>
                        <p id="click-score" class="text-xs text-gray-400 font-bold">أفضل نتيجة: 0</p>
                    </div>
                    <button onclick="startClickGame()" id="click-game-btn" class="bg-blue-600 px-6 py-3 rounded-2xl font-black text-xs">ابدأ</button>
                </div>

                <div class="glass p-5 rounded-[2rem]">
                    <h4 class="font-black text-green-400 mb-3">حجر ورقة مقص ضد البوت</h4>
                    <div class="flex justify-around">
                        <button onclick="playRPS('✊')" class="text-3xl glass p-4 rounded-2xl">✊</button>
                        <button onclick="playRPS('✋')" class="text-3xl glass p-4 rounded-2xl">✋</button>
                        <button onclick="playRPS('✌️')" class="text-3xl glass p-4 rounded-2xl">✌️</button>
                    </div>
                    <p id="rps-result" class="text-center mt-3 text-xs font-bold text-gray-400">اختر سلاحك!</p>
                </div>
            </div>
        </section>

        <section id="chat-tab" class="tab-content">
            <div class="glass p-6 rounded-[2.5rem] border border-white/10 shadow-2xl mb-6">
                <div class="flex items-center gap-2 mb-4">
                    <span class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
                    <h3 class="font-black text-lg">صارح <span class="text-blue-400">أدريان</span></h3>
                </div>
                <textarea id="sarahni-text" class="w-full bg-white/5 border border-white/10 rounded-2xl p-4 text-sm focus:outline-none focus:border-blue-500 transition-all" rows="4" placeholder="اكتب رسالتك السرية هنا..."></textarea>
                <button onclick="sendSarahni()" class="w-full bg-white text-black font-black py-4 rounded-2xl mt-4 shadow-lg active:scale-95 transition-all">إرسال كـ مجهول 🚀</button>
            </div>

            <div class="text-center mb-4 text-[10px] font-bold text-gray-500 uppercase tracking-widest">تفاعلات مباشرة</div>
            <div id="reactions-btns" class="flex justify-between gap-2 p-2 glass rounded-3xl">
                <button onclick="sendReaction('🔥')" class="hover:bg-white/10 p-4 rounded-2xl transition-all">🔥</button>
                <button onclick="sendReaction('😂')" class="hover:bg-white/10 p-4 rounded-2xl transition-all">😂</button>
                <button onclick="sendReaction('❤️')" class="hover:bg-white/10 p-4 rounded-2xl transition-all">❤️</button>
                <button onclick="sendReaction('💩')" class="hover:bg-white/10 p-4 rounded-2xl transition-all">💩</button>
                <button onclick="sendReaction('⚡')" class="hover:bg-white/10 p-4 rounded-2xl transition-all">⚡</button>
            </div>
        </section>

        <section id="settings-tab" class="tab-content">
            <div class="glass p-6 rounded-[2rem] mb-6 border-b-4 border-blue-500 shadow-xl">
                <div class="flex items-center gap-4">
                    <div id="settings-user-photo" class="w-16 h-16 rounded-2xl bg-blue-600"></div>
                    <div>
                        <h3 id="settings-user-name" class="font-black text-xl">أدريان محمد</h3>
                        <p class="text-xs text-gray-400">عضو بريميوم منذ 2026</p>
                    </div>
                </div>
            </div>

            <div class="space-y-3">
                <div class="glass p-5 rounded-3xl flex justify-between items-center">
                    <div class="flex items-center gap-3">
                        <span>🌙</span><span class="font-bold text-sm">الوضع الليلي المتطور</span>
                    </div>
                    <label class="switch"><input type="checkbox" checked onchange="toggleDark()"><span class="slider"></span></label>
                </div>
                <div class="glass p-5 rounded-3xl flex justify-between items-center">
                    <div class="flex items-center gap-3">
                        <span>🔔</span><span class="font-bold text-sm">إشعارات النظام</span>
                    </div>
                    <label class="switch"><input type="checkbox" checked><span class="slider"></span></label>
                </div>
                <div class="glass p-5 rounded-3xl flex justify-between items-center">
                    <div class="flex items-center gap-3">
                        <span>📳</span><span class="font-bold text-sm">الاهتزاز (Haptic)</span>
                    </div>
                    <label class="switch"><input id="haptic-toggle" type="checkbox" checked><span class="slider"></span></label>
                </div>
                <div class="glass p-5 rounded-3xl">
                    <p class="text-[10px] text-gray-400 mb-3 font-bold uppercase">لون الهوية المخصص</p>
                    <div class="flex justify-around bg-black/20 p-3 rounded-2xl">
                        <div onclick="setTheme('#0088cc')" class="w-8 h-8 rounded-full bg-blue-500 border-2 border-white/20 cursor-pointer hover:scale-110 transition-all"></div>
                        <div onclick="setTheme('#d946ef')" class="w-8 h-8 rounded-full bg-pink-500 border-2 border-white/20 cursor-pointer hover:scale-110 transition-all"></div>
                        <div onclick="setTheme('#10b981')" class="w-8 h-8 rounded-full bg-emerald-500 border-2 border-white/20 cursor-pointer hover:scale-110 transition-all"></div>
                        <div onclick="setTheme('#f59e0b')" class="w-8 h-8 rounded-full bg-amber-500 border-2 border-white/20 cursor-pointer hover:scale-110 transition-all"></div>
                    </div>
                </div>
            </div>
        </section>
    </main>

    <div class="fixed bottom-20 left-0 right-0 glass h-10 flex items-center overflow-hidden z-40 border-y border-white/5">
        <div class="whitespace-nowrap animate-marquee flex gap-12 items-center px-4 font-bold">
            <span class="text-[11px] flex items-center gap-1"><i class="w-2 h-2 bg-blue-500 rounded-full"></i> TON: <span id="ton-price" class="text-blue-400">...</span></span>
            <span class="text-[11px] flex items-center gap-1"><i class="w-2 h-2 bg-green-500 rounded-full"></i> BTC: <span id="btc-price" class="text-orange-400">...</span></span>
            <span class="text-[11px] flex items-center gap-1"><i class="w-2 h-2 bg-red-500 rounded-full"></i> أدريان شوت: الريال يتأهل للمطفي!</span>
        </div>
    </div>

    <nav class="fixed bottom-0 left-0 right-0 glass h-20 flex justify-around items-center rounded-t-[2.5rem] border-t border-white/10 z-50 px-6">
        <button onclick="showTab('game-tab')" class="flex flex-col items-center opacity-40 transition-all active-tab scale-110"><span class="text-2xl">🎡</span><span class="text-[9px] font-black mt-1 uppercase">روليت</span></button>
        <button onclick="showTab('arcade-tab')" class="flex flex-col items-center opacity-40 transition-all"><span class="text-2xl">🕹️</span><span class="text-[9px] font-black mt-1 uppercase">آركيد</span></button>
        <button onclick="showTab('chat-tab')" class="flex flex-col items-center opacity-40 transition-all"><span class="text-2xl">💬</span><span class="text-[9px] font-black mt-1 uppercase">تفاعل</span></button>
        <button onclick="showTab('settings-tab')" class="flex flex-col items-center opacity-40 transition-all"><span class="text-2xl">⚙️</span><span class="text-[9px] font-black mt-1 uppercase">حسابي</span></button>
    </nav>

    <script>
        const tg = window.Telegram.WebApp;
        tg.expand();
        tg.enableClosingConfirmation();

        // 1. جلب بيانات المستخدم الحقيقية
        const user = tg.initDataUnsafe.user || { first_name: "أدريان", id: "000000", photo_url: "" };
        document.getElementById('user-name').innerText = user.first_name;
        document.getElementById('settings-user-name').innerText = user.first_name + " " + (user.last_name || "");
        document.getElementById('user-id').innerText = user.id;

        if(user.photo_url) {
            const img = `<img src="${user.photo_url}" class="w-full h-full object-cover">`;
            document.getElementById('user-photo').innerHTML = img;
            document.getElementById('settings-user-photo').innerHTML = img;
            document.getElementById('settings-user-photo').style.overflow = "hidden";
        }

        // 2. نظام التبويب
        function showTab(tabId) {
            document.querySelectorAll('.tab-content').forEach(t => t.classList.remove('active'));
            document.getElementById(tabId).classList.add('active');
            document.querySelectorAll('nav button').forEach(b => b.classList.remove('active-tab', 'scale-110'));
            event.currentTarget.classList.add('active-tab', 'scale-110');
            tg.HapticFeedback.impactOccurred('medium');
        }

        // 3. نظام العجلة
        const canvas = document.getElementById('wheelCanvas');
        const ctx = canvas.getContext('2d');
        const players = ["أدريان", "فهد", "سارة", "علي", "مريم", "ياسين", "نور", "صالح"];
        const colors = ["#1e2329", "#30363d", "#0088cc", "#f0b90b", "#ff3b30", "#00c853", "#673ab7", "#e91e63"];

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
                ctx.translate(250 + Math.cos(i * arc + arc/2) * 170, 250 + Math.sin(i * arc + arc/2) * 170);
                ctx.rotate(i * arc + arc/2 + Math.PI/2);
                ctx.font = "bold 22px Cairo";
                ctx.fillText(p, -ctx.measureText(p).width/2, 0);
                ctx.restore();
            });
        }
        drawWheel();

        let spinning = false;
        function spinWheel() {
            if(spinning) return;
            spinning = true;
            tg.HapticFeedback.impactOccurred('heavy');
            const randDeg = Math.floor(5000 + Math.random() * 5000);
            canvas.style.transform = `rotate(${randDeg}deg)`;
            
            setTimeout(() => {
                spinning = false;
                tg.HapticFeedback.notificationOccurred('success');
                const winner = players[Math.floor(Math.random() * players.length)];
                tg.showAlert(`🔥 وقع الحكم على: ${winner}`);
            }, 4000);
        }

        // 4. ألعاب الآركيد (Logic)
        let clickCount = 0;
        let bestScore = 0;
        function startClickGame() {
            clickCount = 0;
            const btn = document.getElementById('click-game-btn');
            btn.innerText = "انقر!!!";
            btn.onclick = () => {
                clickCount++;
                tg.HapticFeedback.impactOccurred('light');
            };
            setTimeout(() => {
                btn.innerText = "انتهى!";
                btn.onclick = startClickGame;
                if(clickCount > bestScore) bestScore = clickCount;
                document.getElementById('click-score').innerText = `أفضل نتيجة: ${bestScore} | الحالية: ${clickCount}`;
                tg.showPopup({ message: `انتهى الوقت! عدد نقراتك: ${clickCount}` });
            }, 10000);
        }

        function playRPS(userChoice) {
            const choices = ['✊', '✋', '✌️'];
            const botChoice = choices[Math.floor(Math.random() * 3)];
            let res = "";
            if(userChoice === botChoice) res = "تعادل! 🤝";
            else if((userChoice==='✊'&&botChoice==='✌️') || (userChoice==='✋'&&botChoice==='✊') || (userChoice==='✌️'&&botChoice==='✋')) res = "فزت أنت! 🎉";
            else res = "خسرت! 💀";
            document.getElementById('rps-result').innerText = `البوت اختار ${botChoice} - ${res}`;
            tg.HapticFeedback.impactOccurred('medium');
        }

        // 5. التفاعلات والصارحني
        function sendReaction(emoji) {
            const el = document.createElement('div');
            el.className = 'reaction';
            el.innerText = emoji;
            el.style.left = (Math.random() * 80 + 10) + '%';
            document.body.appendChild(el);
            setTimeout(() => el.remove(), 2000);
            if(document.getElementById('haptic-toggle').checked) tg.HapticFeedback.impactOccurred('light');
        }

        function sendSarahni() {
            const text = document.getElementById('sarahni-text').value;
            if(!text) return tg.showAlert("اكتب شيئاً أولاً!");
            tg.showConfirm("هل أنت متأكد من إرسال الرسالة كشخص مجهول؟", (ok) => {
                if(ok) {
                    tg.showPopup({ message: "✅ تم إرسال رسالتك بنجاح وبسرية تامة!" });
                    document.getElementById('sarahni-text').value = "";
                }
            });
        }
        
        // 6. أسعار العملات
        async function updateMarket() {
            try {
                const res = await fetch('https://api.binance.com/api/v3/ticker/price?symbols=["TONUSDT","BTCUSDT"]');
                const data = await res.json();
                document.getElementById('ton-price').innerText = "$" + parseFloat(data[0].price).toFixed(2);
                document.getElementById('btc-price').innerText = "$" + parseFloat(data[1].price).toLocaleString();
            } catch(e) {}
        }
        setInterval(updateMarket, 10000);
        updateMarket();

        // 7. الإعدادات والثيم
        function setTheme(color) {
            document.documentElement.style.setProperty('--main-color', color);
            document.getElementById('spin-btn').style.backgroundColor = color;
            document.getElementById('xp-bar').style.boxShadow = `0 0 15px ${color}`;
            tg.setHeaderColor(color);
        }

        function toggleDark() {
            const body = document.body;
            if(body.style.backgroundColor === 'white') {
                body.style.backgroundColor = '#0b0e11';
                body.style.color = 'white';
            } else {
                body.style.backgroundColor = 'white';
                body.style.color = 'black';
            }
        }
    </script>
</body>
</html>
