<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TON Dashboard | Adrian</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;900&display=swap" rel="stylesheet">
    <style>
        :root { --bg-color: #080a0c; --card-bg: #111418; --accent: #0088cc; }
        body { font-family: 'Cairo', sans-serif; background-color: var(--bg-color); color: #ffffff; overflow-x: hidden; }
        
        .section-card { background: var(--card-bg); border: 1px solid #1f2328; border-radius: 20px; margin-bottom: 16px; padding: 20px; }
        .label-text { font-size: 10px; color: #848e9c; font-weight: 700; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 8px; display: block; }
        
        .input-group { background: #080a0c; border: 1.5px solid #1f2328; border-radius: 14px; padding: 12px 16px; transition: 0.3s; }
        .input-group:focus-within { border-color: var(--accent); box-shadow: 0 0 10px rgba(0, 136, 204, 0.2); }
        input { background: transparent; border: none; outline: none; width: 100%; font-size: 18px; font-weight: 900; color: #fff; text-align: left; }
        
        @keyframes pulse-up { 0% { box-shadow: inset 0 0 20px rgba(0, 255, 173, 0.1); } 100% { box-shadow: none; } }
        @keyframes pulse-down { 0% { box-shadow: inset 0 0 20px rgba(255, 59, 48, 0.1); } 100% { box-shadow: none; } }
        .up { animation: pulse-up 1s ease-out; }
        .down { animation: pulse-down 1s ease-out; }
    </style>
</head>
<body class="p-4">

    <header class="flex justify-between items-center mb-6 px-1">
        <div>
            <h1 class="text-xl font-black tracking-tight">ADRIAN <span class="text-[#0088cc]">TON</span></h1>
            <div class="flex items-center gap-1.5 mt-0.5">
                <span class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
                <span class="text-[10px] text-gray-500 font-bold uppercase tracking-wider">السوق مفتوح مباشر</span>
            </div>
        </div>
        <button onclick="requestNotify()" class="bg-blue-600/10 p-2 rounded-lg border border-blue-600/20">
            <svg width="20" height="20" fill="#0088cc" viewBox="0 0 24 24"><path d="M12 22c1.1 0 2-.9 2-2h-4c0 1.1.9 2 2 2zm6-6v-5c0-3.07-1.63-5.64-4.5-6.32V4c0-.83-.67-1.5-1.5-1.5s-1.5.67-1.5 1.5v.68C7.64 5.36 6 7.92 6 11v5l-2 2v1h16v-1l-2-2z"/></svg>
        </button>
    </header>

    <section id="price-card" class="section-card text-center py-10 transition-all duration-500">
        <span class="label-text">سعر التداول الحالي (USDT)</span>
        <div id="price-loader" class="text-gray-700 font-bold animate-pulse">جاري الاتصال...</div>
        <div id="price-content" class="hidden">
            <div class="flex items-baseline justify-center gap-1">
                <span id="main-price" class="text-6xl font-black tracking-tighter">0.00</span>
                <span class="text-2xl font-bold text-[#0088cc]">$</span>
            </div>
            <div id="price-change" class="mt-4 px-4 py-1.5 rounded-full text-[11px] font-black inline-block">0.00%</div>
        </div>
    </section>

    <section class="section-card">
        <span class="label-text">محول العملات الذكي</span>
        <div class="input-group flex items-center gap-3">
            <input type="number" id="ton-input" value="1" placeholder="0">
            <span class="font-black text-[#0088cc] text-sm">TON</span>
        </div>
        <div class="grid grid-cols-2 gap-3 mt-4">
            <div class="bg-[#080a0c] p-4 rounded-xl border border-[#1f2328]">
                <span class="text-[9px] text-gray-500 font-bold block mb-1 uppercase text-right">بالدينار العراقي</span>
                <span id="res-iqd" class="text-md font-black text-yellow-500 block text-right font-mono">0</span>
            </div>
            <div class="bg-[#080a0c] p-4 rounded-xl border border-[#1f2328]">
                <span class="text-[9px] text-gray-500 font-bold block mb-1 uppercase text-right">برصيد آسيا</span>
                <span id="res-asia" class="text-md font-black text-blue-400 block text-right font-mono">0</span>
            </div>
        </div>
    </section>

    <section class="section-card">
        <span class="label-text">مخطط أهدافك الاستثمارية 🎯</span>
        <div class="flex gap-2 mb-4">
            <div class="input-group flex-1">
                <span class="text-[9px] text-gray-500 block mb-1">سعر الهدف $</span>
                <input type="number" id="target-price" value="10" class="!text-sm">
            </div>
            <div class="input-group flex-1">
                <span class="text-[9px] text-gray-500 block mb-1">كميتك حالياً</span>
                <input type="number" id="my-hold" value="100" class="!text-sm">
            </div>
        </div>
        <div class="bg-blue-600/10 p-4 rounded-xl border border-blue-600/20 text-center">
            <p class="text-[10px] text-gray-400 uppercase font-bold mb-1">قيمة محفظتك عند الهدف</p>
            <p id="target-res" class="text-2xl font-black text-blue-400">0 $</p>
        </div>
    </section>

    <section class="section-card !p-0 overflow-hidden" style="height: 300px;">
        <div class="tradingview-widget-container" style="height:100%;width:100%">
            <div id="tradingview_ton" style="height:100%;width:100%"></div>
            <script type="text/javascript" src="https://s3.tradingview.com/tv.js"></script>
            <script type="text/javascript">
            new TradingView.widget({
                "autosize": true, "symbol": "OKX:TONUSDT", "interval": "15",
                "timezone": "Etc/UTC", "theme": "dark", "style": "3", "locale": "ar",
                "hide_top_toolbar": true, "save_image": false, "container_id": "tradingview_ton",
                "backgroundColor": "#111418", "gridColor": "rgba(255,255,255,0.02)"
            });
            </script>
        </div>
    </section>

    <nav class="grid grid-cols-2 gap-3 mb-8">
        <a href="https://fragment.com" class="bg-[#111418] border border-[#1f2328] p-4 rounded-2xl text-center text-[10px] font-bold uppercase active:scale-95 transition">شراء يوزرات</a>
        <a href="https://tonviewer.com" class="bg-[#111418] border border-[#1f2328] p-4 rounded-2xl text-center text-[10px] font-bold uppercase active:scale-95 transition">المستكشف</a>
    </nav>

    <footer class="text-center pb-8">
        <p class="text-[9px] text-gray-600 font-bold uppercase tracking-[0.3em]">Adrian Engine Pro • 2026</p>
    </footer>

    <script>
        let lastPrice = 0;
        let currentPrice = 0;
        let targetReached = false;

        // طلب إذن التنبيهات
        function requestNotify() {
            if (Notification.permission !== "granted") {
                Notification.requestPermission();
            }
        }

        async function updateData() {
            try {
                const response = await fetch('https://api.binance.com/api/v3/ticker/24hr?symbol=TONUSDT');
                const data = await response.json();
                currentPrice = parseFloat(data.lastPrice);
                const change = parseFloat(data.priceChangePercent);
                
                // تحديث الوميض
                const card = document.getElementById('price-card');
                if (lastPrice > 0) {
                    card.classList.remove('up', 'down');
                    void card.offsetWidth;
                    card.classList.add(currentPrice > lastPrice ? 'up' : 'down');
                }
                lastPrice = currentPrice;

                document.getElementById('price-loader').classList.add('hidden');
                document.getElementById('price-content').classList.remove('hidden');
                document.getElementById('main-price').innerText = currentPrice.toFixed(3);
                
                const changeEl = document.getElementById('price-change');
                changeEl.innerText = (change >= 0 ? "▲ +" : "▼ ") + change.toFixed(2) + "%";
                changeEl.className = `mt-4 px-4 py-1.5 rounded-full text-[11px] font-black inline-block ${change >= 0 ? 'bg-green-500/10 text-green-500' : 'bg-red-500/10 text-red-500'}`;
                
                calculate();
                updateTargets();
                checkPriceAlert();
            } catch (e) { console.error("API Error"); }
        }

        function calculate() {
            const amount = document.getElementById('ton-input').value || 0;
            document.getElementById('res-iqd').innerText = Math.floor(amount * currentPrice * 1410).toLocaleString() + " د.ع";
            document.getElementById('res-asia').innerText = Math.floor(amount * currentPrice * 1610).toLocaleString() + " رصيد";
        }

        function updateTargets() {
            const target = document.getElementById('target-price').value || 0;
            const hold = document.getElementById('my-hold').value || 0;
            const result = target * hold;
            document.getElementById('target-res').innerText = result.toLocaleString() + " $";
        }

        // نظام التنبيهات الحقيقي
        function checkPriceAlert() {
            const target = parseFloat(document.getElementById('target-price').value);
            if (currentPrice >= target && !targetReached) {
                if (Notification.permission === "granted") {
                    new Notification("🚀 Adrian Alert: تم الوصول للهدف!", {
                        body: `سعر TON الآن هو ${currentPrice}$, هدفك كان ${target}$`,
                        icon: "https://cryptologos.cc/logos/toncoin-ton-logo.png"
                    });
                }
                targetReached = true; // منع تكرار التنبيه لنفس الهدف
            } else if (currentPrice < target) {
                targetReached = false; 
            }
        }

        document.getElementById('ton-input').addEventListener('input', calculate);
        document.getElementById('target-price').addEventListener('input', updateTargets);
        document.getElementById('my-hold').addEventListener('input', updateTargets);

        setInterval(updateData, 4000);
        updateData();
    </script>
</body>
</html>
