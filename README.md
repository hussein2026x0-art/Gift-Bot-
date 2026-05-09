<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TON Dashboard | Adrian</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Cairo', sans-serif; background-color: #0b0e11; color: #eaecef; overflow-x: hidden; }
        .price-card { background: linear-gradient(145deg, #1e2329, #14171a); border: 1px solid #30363d; border-radius: 24px; }
        #chart-container { height: 300px; border-radius: 16px; overflow: hidden; margin-top: 20px; border: 1px solid #30363d; }
    </style>
</head>
<body class="p-4">

    <div class="flex justify-between items-center mb-6">
        <div>
            <h1 class="text-2xl font-black text-white">TON / USDT</h1>
            <p class="text-xs text-gray-500">سعر الصرف اللحظي</p>
        </div>
        <div class="bg-blue-600 p-2 rounded-lg shadow-lg shadow-blue-900/20">
            <svg width="24" height="24" fill="white" viewBox="0 0 24 24"><path d="M12 2L4.5 20.29L5.21 21L12 18L18.79 21L19.5 20.29L12 2Z"/></svg>
        </div>
    </div>

    <div class="price-card p-6 mb-4 text-center shadow-2xl">
        <div id="price-loader" class="animate-pulse text-gray-600">جاري جلب البيانات...</div>
        <div id="price-content" class="hidden">
            <span id="main-price" class="text-5xl font-black text-white tracking-tighter">0.00</span>
            <div id="price-change" class="text-sm font-bold mt-2">0.00%</div>
        </div>
    </div>

    <div class="grid grid-cols-2 gap-3 mb-4">
        <div class="price-card p-4">
            <p class="text-[10px] text-gray-400 mb-1">صرف الدينار (1410)</p>
            <p id="iqd-price" class="text-lg font-bold text-yellow-500">0 IQD</p>
        </div>
        <div class="price-card p-4">
            <p class="text-[10px] text-gray-400 mb-1">صرف آسيا (1610)</p>
            <p id="asia-price" class="text-lg font-bold text-blue-400">0 ASIA</p>
        </div>
    </div>

    <div id="chart-container">
        <div class="tradingview-widget-container" style="height:100%;width:100%">
            <div id="tradingview_ton" style="height:100%;width:100%"></div>
            <script type="text/javascript" src="https://s3.tradingview.com/tv.js"></script>
            <script type="text/javascript">
            new TradingView.widget({
                "autosize": true,
                "symbol": "OKX:TONUSDT",
                "interval": "60",
                "timezone": "Etc/UTC",
                "theme": "dark",
                "style": "1",
                "locale": "ar",
                "toolbar_bg": "#f1f3f6",
                "enable_publishing": false,
                "hide_top_toolbar": true,
                "save_image": false,
                "container_id": "tradingview_ton"
            });
            </script>
        </div>
    </div>

    <footer class="mt-8 text-center pb-6">
        <p class="text-xs text-gray-600 font-bold uppercase tracking-widest">Developed by Adrian Mohammed • 2026</p>
    </footer>

    <script>
        async function updateData() {
            try {
                const response = await fetch('https://api.binance.com/api/v3/ticker/24hr?symbol=TONUSDT');
                const data = await response.json();
                
                const price = parseFloat(data.lastPrice);
                const change = parseFloat(data.priceChangePercent);
                
                document.getElementById('price-loader').classList.add('hidden');
                document.getElementById('price-content').classList.remove('hidden');
                
                document.getElementById('main-price').innerText = price.toFixed(3);
                
                const changeEl = document.getElementById('price-change');
                changeEl.innerText = (change >= 0 ? "▲ +" : "▼ ") + change.toFixed(2) + "%";
                changeEl.style.color = change >= 0 ? "#00ffad" : "#ff3b30";
                
                document.getElementById('iqd-price').innerText = Math.floor(price * 1410).toLocaleString() + " د.ع";
                document.getElementById('asia-price').innerText = Math.floor(price * 1610).toLocaleString() + " رصيد";
                
            } catch (e) { console.error("Error updating price"); }
        }
        setInterval(updateData, 5000);
        updateData();
    </script>
</body>
</html>
