<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Adrian Pro Dashboard</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;900&display=swap" rel="stylesheet">
    <style>
        :root { --bg-color: #050708; --card-bg: #12151a; --accent: #0088cc; --text-dim: #848e9c; }
        body { font-family: 'Cairo', sans-serif; background-color: var(--bg-color); color: #ffffff; overflow-x: hidden; padding-bottom: 80px; }
        
        .glass-card { background: var(--card-bg); border: 1px solid #1f2328; border-radius: 24px; transition: 0.3s; }
        .label-text { font-size: 11px; color: var(--text-dim); font-weight: 700; text-transform: uppercase; letter-spacing: 0.5px; }
        
        .input-group { background: #080a0c; border: 1px solid #2a2f36; border-radius: 16px; padding: 12px 16px; }
        .input-group:focus-within { border-color: var(--accent); }
        input { background: transparent; border: none; outline: none; width: 100%; font-size: 18px; font-weight: 700; color: #fff; }

        .coin-row { border-bottom: 1px solid #1a1e23; padding: 12px 4px; display: flex; align-items: center; justify-content: space-between; transition: 0.2s; }
        .coin-row:last-child { border-bottom: none; }
        .coin-row:active { background: rgba(255,255,255,0.03); }

        .price-up { color: #02c076 !important; }
        .price-down { color: #f84960 !important; }
        
        .nav-bar { position: fixed; bottom: 0; left: 0; right: 0; background: rgba(18, 21, 26, 0.95); backdrop-filter: blur(10px); border-top: 1px solid #1f2328; display: flex; justify-content: space-around; padding: 12px; z-index: 100; }
        .nav-item { display: flex; flex-direction: column; align-items: center; color: var(--text-dim); font-size: 10px; font-weight: 700; }
        .nav-item.active { color: var(--accent); }
    </style>
</head>
<body class="p-4">

    <header class="flex justify-between items-center mb-6 px-1">
        <div>
            <h1 class="text-xl font-black tracking-tight">ADRIAN <span class="text-[#0088cc]">PRO</span></h1>
            <p class="text-[10px] text-gray-500 font-bold uppercase tracking-widest flex items-center gap-1">
                <span class="w-1.5 h-1.5 bg-green-500 rounded-full animate-pulse"></span> Live Market Engine
            </p>
        </div>
        <div class="flex gap-2">
            <button onclick="requestNotify()" class="bg-blue-600/10 p-2.5 rounded-xl border border-blue-600/20 shadow-lg shadow-blue-900/10">
                <svg width="20" height="20" fill="#0088cc" viewBox="0 0 24 24"><path d="M12 22c1.1 0 2-.9 2-2h-4c0 1.1.9 2 2 2zm6-6v-5c0-3.07-1.63-5.64-4.5-6.32V4c0-.83-.67-1.5-1.5-1.5s-1.5.67-1.5 1.5v.68C7.64 5.36 6 7.92 6 11v5l-2 2v1h16v-1l-2-2z"/></svg>
            </button>
        </div>
    </header>

    <section id="price-card" class="glass-card p-6 mb-4 text-center">
        <span class="label-text">Toncoin Price (Primary)</span>
        <div id="price-content">
            <div class="flex items-center justify-center gap-1 my-2">
                <span id="main-price" class="text-5xl font-black tracking-tighter">0.00</span>
                <span class="text-xl font-bold text-[#0088cc]">$</span>
            </div>
            <div id="price-change" class="text-[11px] font-black px-3 py-1 rounded-full inline-block">0.00%</div>
        </div>
    </section>

    <section class="glass-card p-5 mb-4">
        <div class="flex justify-between items-center mb-4">
            <span class="label-text">مراقب السوق المباشر</span>
            <span class="text-[9px] bg-white/5 px-2 py-1 rounded uppercase font-bold text-gray-500">Top 10 Assets</span>
        </div>
        <div id="market-list">
            <div class="animate-pulse flex flex-col gap-4">
                <div class="h-8 bg-gray-800/50 rounded w-full"></div>
                <div class="h-8 bg-gray-800/50 rounded w-full"></div>
            </div>
        </div>
    </section>

    <div class="grid grid-cols-1 gap-4 mb-4">
        <section class="glass-card p-5">
            <span class="label-text">المحفظة الافتراضية 🎯</span>
            <div class="flex gap-2 mt-3">
                <div class="input-group flex-1">
                    <span class="text-[9px] text-gray-500 block mb-1">السعر المستهدف</span>
                    <input type="number" id="target-price" value="10">
                </div>
                <div class="input-group flex-1">
                    <span class="text-[9px] text-gray-500 block mb-1">كمية TON</span>
                    <input type="number" id="my-hold" value="100">
                </div>
            </div>
            <div class="mt-4 flex justify-between items-center p-4 bg-blue-600/5 rounded-2xl border border-blue-600/10">
                <span class="text-xs font-bold text-gray-400">القيمة الإجمالية</span>
                <span id="target-res" class="text-xl font-black text-blue-400">0 $</span>
            </div>
        </section>
    </div>

    <nav class="grid grid-cols-2 gap-3 mb-6">
        <a href="https://fragment.com" class="glass-card p-4 text-center text-[10px] font-black uppercase hover:bg-white/5 active:scale-95 transition">Fragment</a>
        <a href="https://tonviewer.com" class="glass-card p-4 text-center text-[10px] font-black uppercase hover:bg-white/5 active:scale-95 transition">Explorer</a>
    </nav>

    <footer class="text-center">
        <p class="text-[9px] text-gray-600 font-bold uppercase tracking-[0.4em]">Adrian Engine Pro v4.0 • 2026</p>
    </footer>

    <div class="nav-bar">
        <div class="nav-item active">
            <svg width="20" height="20" fill="currentColor" viewBox="0 0 24 24"><path d="M10 20v-6h4v6h5v-8h3L12 3 2 12h3v8z"/></svg>
            <span>الرئيسية</span>
        </div>
        <div class="nav-item" onclick="alert('قريباً')">
            <svg width="20" height="20" fill="currentColor" viewBox="0 0 24 24"><path d="M3.5 18.49l6-6.01 4 4L22 6.92l-1.41-1.41-7.09 7.97-4-4L2 16.99z"/></svg>
            <span>التداول</span>
        </div>
        <div class="nav-item" onclick="alert('قريباً')">
            <svg width="20" height="20" fill="currentColor" viewBox="0 0 24 24"><path d="M19.14 12.94c.04-.3.06-.61.06-.94 0-.32-.02-.64-.07-.94l2.03-1.58c.18-.14.23-.41.12-.61l-1.92-3.32c-.12-.22-.37-.29-.59-.22l-2.39.96c-.5-.38-1.03-.7-1.62-.94l-.36-2.54c-.04-.24-.24-.41-.48-.41h-3.84c-.24 0-.43.17-.47.41l-.36 2.54c-.59.24-1.13.57-1.62.94l-2.39-.96c-.22-.08-.47 0-.59.22L2.74 8.87c-.12.21-.08.47.12.61l2.03 1.58c-.05.3-.09.63-.09.94s.02.64.07.94l-2.03 1.58c-.18.14-.23.41-.12.61l1.92 3.32c.12.22.37.29.59.22l2.39-.96c.5.38 1.03.7 1.62.94l.36 2.54c.05.24.24.41.48.41h3.84c.24 0 .44-.17.47-.41l.36-2.54c.59-.24 1.13-.56 1.62-.94l2.39.96c.22.08.47 0 .59-.22l1.92-3.32c.12-.22.07-.47-.12-.61l-2.01-1.58zM12 15.6c-1.98 0-3.6-1.62-3.6-3.6s1.62-3.6 3.6-3.6 3.6 1.62 3.6 3.6-1.62 3.6-3.6 3.6z"/></svg>
            <span>الإعدادات</span>
        </div>
    </div>

    <script>
        const symbols = ['TONUSDT', 'BTCUSDT', 'ETHUSDT', 'SOLUSDT', 'BNBUSDT', 'XRPUSDT', 'ADAUSDT', 'DOGEUSDT', 'TRXUSDT', 'AVAXUSDT'];
        const coinNames = { 'TONUSDT': 'Toncoin', 'BTCUSDT': 'Bitcoin', 'ETHUSDT': 'Ethereum', 'SOLUSDT': 'Solana', 'BNBUSDT': 'Binance Coin', 'XRPUSDT': 'Ripple', 'ADAUSDT': 'Cardano', 'DOGEUSDT': 'Dogecoin', 'TRXUSDT': 'Tron', 'AVAXUSDT': 'Avalanche' };
        
        let prices = {};

        async function fetchMarketData() {
            try {
                const response = await fetch('https://api.binance.com/api/v3/ticker/24hr');
                const data = await response.json();
                const filtered = data.filter(item => symbols.includes(item.symbol));
                
                const listContainer = document.getElementById('market-list');
                listContainer.innerHTML = '';

                filtered.forEach(coin => {
                    const price = parseFloat(coin.lastPrice);
                    const change = parseFloat(coin.priceChangePercent);
                    const colorClass = change >= 0 ? 'price-up' : 'price-down';
                    
                    if(coin.symbol === 'TONUSDT') updatePrimary(price, change);

                    listContainer.innerHTML += `
                        <div class="coin-row">
                            <div class="flex items-center gap-3">
                                <div class="w-8 h-8 bg-white/5 rounded-full flex items-center justify-center font-bold text-[10px]">${coin.symbol.replace('USDT','')}</div>
                                <div>
                                    <p class="text-xs font-bold">${coinNames[coin.symbol]}</p>
                                    <p class="text-[9px] text-gray-500 uppercase">${coin.symbol}</p>
                                </div>
                            </div>
                            <div class="text-left">
                                <p class="text-sm font-black font-mono">${price < 1 ? price.toFixed(4) : price.toLocaleString()}$</p>
                                <p class="text-[10px] font-bold ${colorClass}">${change >= 0 ? '+' : ''}${change.toFixed(2)}%</p>
                            </div>
                        </div>
                    `;
                });
                updateTargets(prices['TONUSDT']);
            } catch (e) { console.error("Market API Error"); }
        }

        function updatePrimary(price, change) {
            prices['TONUSDT'] = price;
            document.getElementById('main-price').innerText = price.toFixed(3);
            const el = document.getElementById('price-change');
            el.innerText = (change >= 0 ? '▲ +' : '▼ ') + change.toFixed(2) + '%';
            el.className = `text-[11px] font-black px-3 py-1 rounded-full inline-block ${change >= 0 ? 'bg-green-500/10 text-green-500' : 'bg-red-500/10 text-red-500'}`;
        }

        function updateTargets(currentTonPrice) {
            const target = document.getElementById('target-price').value || 0;
            const hold = document.getElementById('my-hold').value || 0;
            const result = target * hold;
            document.getElementById('target-res').innerText = result.toLocaleString() + " $";
            
            // Notification Logic
            if (currentTonPrice >= target && target > 0) {
                sendNotification(currentTonPrice);
            }
        }

        function requestNotify() {
            if (Notification.permission !== "granted") Notification.requestPermission();
            else alert("التنبيهات مفعلة مسبقاً ✅");
        }

        function sendNotification(price) {
            if (Notification.permission === "granted") {
                new Notification("🚀 Adrian Pro: Target Hit!", {
                    body: `TON وصل لسعرك المستهدف: ${price}$`,
                    icon: "https://cryptologos.cc/logos/toncoin-ton-logo.png"
                });
            }
        }

        setInterval(fetchMarketData, 5000);
        fetchMarketData();
    </script>
</body>
</html>
