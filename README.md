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
        input { background: transparent; border: none; outline: none; width: 100%; font-size: 18px; font-weight: 700; color: #fff; text-align: left; }

        .coin-row { border-bottom: 1px solid #1a1e23; padding: 14px 8px; display: flex; align-items: center; justify-content: space-between; transition: 0.2s; cursor: pointer; }
        .coin-row:last-child { border-bottom: none; }
        .coin-row:active { background: rgba(255,255,255,0.03); }

        .price-up { color: #02c076 !important; }
        .price-down { color: #f84960 !important; }
        
        .nav-bar { position: fixed; bottom: 0; left: 0; right: 0; background: rgba(18, 21, 26, 0.95); backdrop-filter: blur(15px); border-top: 1px solid #1f2328; display: flex; justify-content: space-around; padding: 12px; z-index: 100; }
        .nav-item { display: flex; flex-direction: column; align-items: center; color: var(--text-dim); font-size: 10px; font-weight: 700; }
        .nav-item.active { color: var(--accent); }

        .coin-icon { width: 32px; height: 32px; border-radius: 50%; background: #1a1e23; padding: 4px; object-fit: contain; }
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
        <button onclick="requestNotify()" class="bg-blue-600/10 p-2.5 rounded-xl border border-blue-600/20">
            <svg width="20" height="20" fill="#0088cc" viewBox="0 0 24 24"><path d="M12 22c1.1 0 2-.9 2-2h-4c0 1.1.9 2 2 2zm6-6v-5c0-3.07-1.63-5.64-4.5-6.32V4c0-.83-.67-1.5-1.5-1.5s-1.5.67-1.5 1.5v.68C7.64 5.36 6 7.92 6 11v5l-2 2v1h16v-1l-2-2z"/></svg>
        </button>
    </header>

    <section id="price-card" class="glass-card p-6 mb-4 text-center shadow-2xl shadow-blue-900/10">
        <span class="label-text">Toncoin (TON/USDT)</span>
        <div id="price-content">
            <div class="flex items-center justify-center gap-1 my-2">
                <span id="main-price" class="text-5xl font-black tracking-tighter">0.00</span>
                <span class="text-xl font-bold text-[#0088cc]">$</span>
            </div>
            <div id="price-change" class="text-[11px] font-black px-3 py-1 rounded-full inline-block">0.00%</div>
        </div>
    </section>

    <section class="glass-card p-5 mb-4 overflow-hidden">
        <div class="flex justify-between items-center mb-4">
            <span class="label-text">أداء العملات الرقمية</span>
            <span class="text-[9px] bg-white/5 px-2 py-1 rounded text-gray-500 font-bold">LIVE</span>
        </div>
        <div id="market-list">
            <div class="animate-pulse flex flex-col gap-4">
                <div class="h-10 bg-gray-800/50 rounded-xl"></div>
                <div class="h-10 bg-gray-800/50 rounded-xl"></div>
            </div>
        </div>
    </section>

    <section class="glass-card p-5 mb-4">
        <span class="label-text">أهداف محفظتك 🎯</span>
        <div class="flex gap-2 mt-3">
            <div class="input-group flex-1">
                <span class="text-[9px] text-gray-500 block mb-1 uppercase">Target $</span>
                <input type="number" id="target-price" value="10">
            </div>
            <div class="input-group flex-1">
                <span class="text-[9px] text-gray-500 block mb-1 uppercase">Amount</span>
                <input type="number" id="my-hold" value="100">
            </div>
        </div>
        <div class="mt-4 p-4 bg-blue-600/5 rounded-2xl border border-blue-600/10 text-center">
            <span class="text-[10px] font-bold text-gray-500 block mb-1 uppercase">Estimated Balance</span>
            <span id="target-res" class="text-2xl font-black text-blue-400">0 $</span>
        </div>
    </section>

    <nav class="grid grid-cols-2 gap-3 mb-6">
        <a href="https://fragment.com" class="glass-card p-4 text-center text-[11px] font-black uppercase">FRAGMENT</a>
        <a href="https://tonviewer.com" class="glass-card p-4 text-center text-[11px] font-black uppercase">EXPLORER</a>
    </nav>

    <footer class="text-center pb-4">
        <p class="text-[9px] text-gray-600 font-bold uppercase tracking-[0.4em]">Adrian Pro Engine • 2026</p>
    </footer>

    <div class="nav-bar">
        <div class="nav-item active">
            <svg width="22" height="22" fill="currentColor" viewBox="0 0 24 24"><path d="M10 20v-6h4v6h5v-8h3L12 3 2 12h3v8z"/></svg>
            <span>السوق</span>
        </div>
        <div class="nav-item opacity-40">
            <svg width="22" height="22" fill="currentColor" viewBox="0 0 24 24"><path d="M21 18v1c0 1.1-.9 2-2 2H5c-1.11 0-2-.9-2-2V5c0-1.1.89-2 2-2h14c1.1 0 2 .9 2 2v1h-9c-1.11 0-2 .9-2 2v8c0 1.1.89 2 2 2h9zm-9-2h10V8H12v8zm4-2.5c-.83 0-1.5-.67-1.5-1.5s.67-1.5 1.5-1.5 1.5.67 1.5 1.5-.67 1.5-1.5 1.5z"/></svg>
            <span>المحفظة</span>
        </div>
    </div>

    <script>
        const symbols = ['TONUSDT', 'BTCUSDT', 'ETHUSDT', 'SOLUSDT', 'BNBUSDT', 'XRPUSDT', 'ADAUSDT', 'DOGEUSDT', 'TRXUSDT', 'AVAXUSDT'];
        
        // روابط الصور الفعلية للعملات
        const coinAssets = {
            'TONUSDT': { name: 'Toncoin', icon: 'https://cryptologos.cc/logos/toncoin-ton-logo.png' },
            'BTCUSDT': { name: 'Bitcoin', icon: 'https://cryptologos.cc/logos/bitcoin-btc-logo.png' },
            'ETHUSDT': { name: 'Ethereum', icon: 'https://cryptologos.cc/logos/ethereum-eth-logo.png' },
            'SOLUSDT': { name: 'Solana', icon: 'https://cryptologos.cc/logos/solana-sol-logo.png' },
            'BNBUSDT': { name: 'Binance Coin', icon: 'https://cryptologos.cc/logos/bnb-bnb-logo.png' },
            'XRPUSDT': { name: 'Ripple', icon: 'https://cryptologos.cc/logos/xrp-xrp-logo.png' },
            'ADAUSDT': { name: 'Cardano', icon: 'https://cryptologos.cc/logos/cardano-ada-logo.png' },
            'DOGEUSDT': { name: 'Dogecoin', icon: 'https://cryptologos.cc/logos/dogecoin-doge-logo.png' },
            'TRXUSDT': { name: 'TRON', icon: 'https://cryptologos.cc/logos/tron-trx-logo.png' },
            'AVAXUSDT': { name: 'Avalanche', icon: 'https://cryptologos.cc/logos/avalanche-avax-logo.png' }
        };
        
        let lastTonPrice = 0;

        async function fetchMarketData() {
            try {
                const response = await fetch('https://api.binance.com/api/v3/ticker/24hr');
                const data = await response.json();
                const filtered = data.filter(item => symbols.includes(item.symbol));
                
                const listContainer = document.getElementById('market-list');
                listContainer.innerHTML = '';

                filtered.sort((a, b) => symbols.indexOf(a.symbol) - symbols.indexOf(b.symbol));

                filtered.forEach(coin => {
                    const price = parseFloat(coin.lastPrice);
                    const change = parseFloat(coin.priceChangePercent);
                    const asset = coinAssets[coin.symbol];
                    const colorClass = change >= 0 ? 'price-up' : 'price-down';
                    
                    if(coin.symbol === 'TONUSDT') {
                        lastTonPrice = price;
                        updatePrimary(price, change);
                    }

                    listContainer.innerHTML += `
                        <div class="coin-row">
                            <div class="flex items-center gap-3">
                                <img src="${asset.icon}" class="coin-icon" alt="${asset.name}">
                                <div>
                                    <p class="text-[13px] font-bold">${asset.name}</p>
                                    <p class="text-[9px] text-gray-500 uppercase tracking-tighter">${coin.symbol.replace('USDT','')}</p>
                                </div>
                            </div>
                            <div class="text-left">
                                <p class="text-sm font-black font-mono">${price < 1 ? price.toFixed(4) : price.toLocaleString()}$</p>
                                <p class="text-[10px] font-bold ${colorClass}">${change >= 0 ? '+' : ''}${change.toFixed(2)}%</p>
                            </div>
                        </div>
                    `;
                });
                updateWallet();
            } catch (e) { console.error("Update Error"); }
        }

        function updatePrimary(price, change) {
            document.getElementById('main-price').innerText = price.toFixed(3);
            const el = document.getElementById('price-change');
            el.innerText = (change >= 0 ? '▲ +' : '▼ ') + change.toFixed(2) + '%';
            el.className = `text-[11px] font-black px-3 py-1 rounded-full inline-block ${change >= 0 ? 'bg-green-500/10 text-green-500' : 'bg-red-500/10 text-red-500'}`;
        }

        function updateWallet() {
            const target = document.getElementById('target-price').value || 0;
            const hold = document.getElementById('my-hold').value || 0;
            const result = target * hold;
            document.getElementById('target-res').innerText = result.toLocaleString() + " $";
            
            if (lastTonPrice >= target && target > 0) {
                if (Notification.permission === "granted") {
                    new Notification("🚀 ADRIAN PRO: تم بلوغ الهدف!", { body: `سعر TON الآن ${lastTonPrice}$` });
                }
            }
        }

        function requestNotify() {
            Notification.requestPermission().then(p => { if(p === 'granted') alert("التنبيهات مفعلة ✅"); });
        }

        document.getElementById('target-price').addEventListener('input', updateWallet);
        document.getElementById('my-hold').addEventListener('input', updateWallet);

        setInterval(fetchMarketData, 5000);
        fetchMarketData();
    </script>
</body>
</html>
