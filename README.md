<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TON Pro Dashboard | Adrian</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Cairo', sans-serif; background-color: #0b0e11; color: #eaecef; overflow-x: hidden; }
        .glass-card { background: rgba(30, 35, 41, 0.6); backdrop-filter: blur(12px); border: 1px solid rgba(255,255,255,0.05); border-radius: 24px; }
        .input-field { background: rgba(0,0,0,0.3); border: 1px solid #30363d; border-radius: 16px; transition: 0.3s; }
        .input-field:focus { border-color: #2563eb; box-shadow: 0 0 15px rgba(37, 99, 235, 0.2); outline: none; }
        #chart-container { height: 280px; border-radius: 24px; overflow: hidden; border: 1px solid #30363d; }
        .price-flash-up { animation: flash-up 0.8s; }
        .price-flash-down { animation: flash-down 0.8s; }
        @keyframes flash-up { 0% { background: rgba(34, 197, 94, 0.2); } 100% { background: transparent; } }
        @keyframes flash-down { 0% { background: rgba(239, 68, 68, 0.2); } 100% { background: transparent; } }
    </style>
</head>
<body class="p-4 pb-12">

    <div class="flex justify-between items-center mb-6">
        <div>
            <h1 class="text-xl font-black text-white tracking-tighter uppercase">ADRIAN <span class="text-blue-500">TON</span></h1>
            <div class="flex items-center gap-2">
                <span class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
                <p class="text-[9px] text-gray-500 font-bold uppercase tracking-widest">Network Status: Online</p>
            </div>
        </div>
        <div class="bg-blue-600/20 p-2.5 rounded-2xl border border-blue-600/30 shadow-lg shadow-blue-900/20">
            <img src="https://cryptologos.cc/logos/toncoin-ton-logo.png" class="w-6 h-6" alt="TON">
        </div>
    </div>

    <div id="main-card" class="glass-card p-8 mb-4 text-center transition-all duration-500">
        <p class="text-gray-500 text-[10px] font-bold uppercase mb-2 tracking-widest">Global Market Price</p>
        <div id="price-loader" class="animate-pulse text-gray-700 font-black">CONNECTING...</div>
        <div id="price-content" class="hidden">
            <div class="flex items-center justify-center gap-1">
                <span id="main-price" class="text-6xl font-black text-white tracking-tighter">0.00</span>
                <span class="text-2xl text-blue-500 font-black">$</span>
            </div>
            <div id="price-change" class="text-[11px] font-black mt-3 py-1.5 px-4 rounded-full inline-block">0.00%</div>
        </div>
    </div>

    <div class="grid grid-cols-1 gap-4 mb-4">
        <div class="glass-card p-5">
            <h3 class="text-[11px] font-bold mb-4 text-gray-400 uppercase tracking-widest">حاسبة التداول السريعة</h3>
            <div class="relative">
                <input type="number" id="ton-input" class="input-field w-full p-4 text-left font-black text-xl text-white pr-20" value="1">
                <span class="absolute right-5 top-4 font-black text-blue-500">TON</span>
            </div>
            <div class="grid grid-cols-2 gap-3 mt-4">
                <div class="bg-black/40 p-3.5 rounded-2xl border border-white/5">
                    <p class="text-[9px] text-gray-500 font-bold mb-1">السعر بالدينار</p>
                    <p id="res-iqd" class="text-sm font-black text-yellow-500">0 د.ع</p>
                </div>
                <div class="bg-black/40 p-3.5 rounded-2xl border border-white/5">
                    <p class="text-[9px] text-gray-500 font-bold mb-1">تحويل آسيا</p>
                    <p id="res-asia" class="text-sm font-black text-blue-400">0 رصيد</p>
                </div>
            </div>
        </div>

        <div class="glass-card p-4 flex justify-around items-center">
            <div class="text-center">
                <p class="text-[8px] text-gray-500 font-bold uppercase">Avg Fee</p>
                <p class="text-xs font-bold text-white">~0.005 TON</p>
            </div>
            <div class="w-px h-8 bg-white/5"></div>
            <div class="text-center">
                <p class="text-[8px] text-gray-500 font-bold uppercase">Block Time</p>
                <p class="text-xs font-bold text-white">~5.1s</p>
            </div>
            <div class="w-px h-8 bg-white/5"></div>
            <div class="text-center">
                <p class="text-[8px] text-gray-500 font-bold uppercase">TPS</p>
                <p class="text-xs font-bold text-green-500">100,000+</p>
            </div>
        </div>
    </div>

    <div id="chart-container" class="mb-4 shadow-inner">
        <div class="tradingview-widget-container" style="height:100%;width:100%">
            <div id="tradingview_ton" style="height:100%;width:100%"></div>
            <script type="text/javascript" src="https://s3.tradingview.com/tv.js"></script>
            <script type="text/javascript">
            new TradingView.widget({
                "autosize": true, "symbol": "OKX:TONUSDT", "interval": "15",
                "timezone": "Etc/UTC", "theme": "dark", "style": "3",
                "locale": "ar", "hide_top_toolbar": true, "save_image": false,
                "container_id": "tradingview_ton", "backgroundColor": "#0b0e11", "gridColor": "rgba(255,255,255,0.02)"
            });
            </script>
        </div>
    </div>

    <div class="grid grid-cols-2 gap-3">
        <a href="https://fragment.com" class="glass-card p-4 text-center text-[10px] font-black uppercase tracking-tighter hover:bg-blue-600 transition-all active:scale-95">Buy Usernames</a>
        <a href="https://tonviewer.com" class="glass-card p-4 text-center text-[10px] font-black uppercase tracking-tighter hover:bg-blue-600 transition-all active:scale-95">TON Explorer</a>
    </div>

    <footer class="mt-10 text-center pb-4">
        <p class="text-[9px] text-gray-600 font-bold uppercase tracking-[0.3em]">System Engine: Adrian Pro v2.0 • 2026</p>
    </footer>

    <script>
        let lastPrice = 0;
        let currentPrice = 0;

        async function updateData() {
            try {
                const response = await fetch('https://api.binance.com/api/v3/ticker/24hr?symbol=TONUSDT');
                const data = await response.json();
                
                currentPrice = parseFloat(data.lastPrice);
                const change = parseFloat(data.priceChangePercent);
                
                // Price Flash Effect
                const card = document.getElementById('main-card');
                if (lastPrice > 0 && currentPrice > lastPrice) {
                    card.classList.add('price-flash-up');
                    setTimeout(() => card.classList.remove('price-flash-up'), 800);
                } else if (lastPrice > 0 && currentPrice < lastPrice) {
                    card.classList.add('price-flash-down');
                    setTimeout(() => card.classList.remove('price-flash-down'), 800);
                }
                
                lastPrice = currentPrice;

                document.getElementById('price-loader').classList.add('hidden');
                document.getElementById('price-content').classList.remove('hidden');
                document.getElementById('main-price').innerText = currentPrice.toFixed(3);
                
                const changeEl = document.getElementById('price-change');
                changeEl.innerText = (change >= 0 ? "▲ +" : "▼ ") + change.toFixed(2) + "%";
                changeEl.className = `text-[10px] font-black mt-3 py-1 px-4 rounded-full inline-block ${change >= 0 ? 'bg-green-500/10 text-green-500 border border-green-500/20' : 'bg-red-500/10 text-red-500 border border-red-500/20'}`;
                
                calculate();
            } catch (e) { console.error("API Connection Error"); }
        }

        function calculate() {
            const amount = document.getElementById('ton-input').value || 0;
            const iqd = (amount * currentPrice * 1410);
            const asia = (amount * currentPrice * 1610);
            
            document.getElementById('res-iqd').innerText = Math.floor(iqd).toLocaleString() + " د.ع";
            document.getElementById('res-asia').innerText = Math.floor(asia).toLocaleString() + " رصيد";
        }

        document.getElementById('ton-input').addEventListener('input', calculate);
        setInterval(updateData, 4000);
        updateData();
    </script>
</body>
</html>
