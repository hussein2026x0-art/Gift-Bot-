<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Veto Market</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;900&display=swap" rel="stylesheet">
    <style>
        :root { --bg: #050708; --card: #0e1116; --accent: #0088cc; --border: #1a1e23; }
        body { font-family: 'Cairo', sans-serif; background: var(--bg); color: #fff; overflow-x: hidden; padding-bottom: 100px; transition: 0.3s; }
        
        body.light-mode { --bg: #f8fafc; --card: #ffffff; --border: #e2e8f0; color: #1e293b; }
        
        .glass-card { background: var(--card); border: 1px solid var(--border); border-radius: 20px; transition: 0.3s; }
        .coin-row { padding: 18px; border-bottom: 1px solid var(--border); cursor: pointer; display: flex; align-items: center; justify-content: space-between; }
        
        .detail-sheet, .settings-sheet { position: fixed; bottom: -100%; left: 0; right: 0; height: 92vh; background: #050708; border-top: 1px solid var(--border); z-index: 1000; transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1); border-radius: 30px 30px 0 0; padding: 20px; overflow-y: auto; }
        body.light-mode .settings-sheet { background: #f8fafc; }
        .detail-sheet.active, .settings-sheet.active { bottom: 0; }
        
        .overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.85); display: none; z-index: 999; backdrop-filter: blur(8px); }
        .nav-bar { position: fixed; bottom: 0; left: 0; right: 0; background: rgba(10, 12, 15, 0.9); backdrop-filter: blur(15px); border-top: 1px solid var(--border); height: 85px; display: flex; align-items: center; justify-content: space-around; z-index: 500; }
        
        .stat-card { background: rgba(255,255,255,0.03); border-radius: 18px; padding: 15px; border: 1px solid var(--border); }
        .community-btn { display: flex; align-items: center; justify-content: space-between; padding: 14px; background: rgba(255,255,255,0.02); border-radius: 15px; margin-bottom: 8px; border: 1px solid var(--border); transition: 0.2s; }
        .community-btn:active { transform: scale(0.98); background: rgba(255,255,255,0.05); }

        .price-up { color: #02c076; } .price-down { color: #f84960; }
        input, select { background: #1a1e23; border: 1px solid #2d3748; border-radius: 10px; padding: 6px 10px; color: white; font-size: 13px; }
    </style>
</head>
<body>

    <header class="p-6 flex justify-between items-center">
        <div>
            <h1 class="text-2xl font-black italic uppercase tracking-tighter">VETO <span class="text-blue-500">MARKET</span></h1>
            <p class="text-[10px] text-gray-500 font-bold tracking-widest mt-1 uppercase">Official Adrian Engine v4.5</p>
        </div>
        <div id="connection-status" class="flex items-center gap-2 bg-green-500/10 px-3 py-1 rounded-full border border-green-500/20">
            <div class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></div>
            <span class="text-[9px] font-bold text-green-500">LIVE</span>
        </div>
    </header>

    <section class="mx-4 glass-card overflow-hidden">
        <div id="market-list">
            <div class="p-10 text-center animate-pulse text-gray-600 font-bold">جاري تحديث الأسعار...</div>
        </div>
    </section>

    <div class="overlay" id="overlay" onclick="closeAllSheets()"></div>

    <div class="settings-sheet" id="settings-sheet">
        <div class="flex items-center justify-between mb-8">
            <button onclick="closeAllSheets()" class="w-10 h-10 bg-white/5 rounded-full flex items-center justify-center">
                <svg width="20" height="20" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path d="M15 19l-7-7 7-7"></path></svg>
            </button>
            <h2 class="text-lg font-black uppercase italic tracking-widest">Settings</h2>
            <div class="w-10"></div>
        </div>

        <div class="mb-8">
            <p class="text-[10px] text-gray-500 font-black mb-4 flex items-center gap-2">
                <svg width="14" height="14" fill="currentColor" viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-1 17.93c-3.95-.49-7-3.85-7-7.93 0-.62.08-1.21.21-1.79L9 15v1c0 1.1.9 2 2 2v1.93zm6.9-2.54c-.26-.81-1-1.39-1.9-1.39h-1v-3c0-.55-.45-1-1-1H8v-2h2c.55 0 1-.45 1-1V7h2c1.1 0 2-.9 2-2v-.41c2.93 1.19 5 4.06 5 7.41 0 2.08-.8 3.97-2.1 5.39z"/></svg>
                COMMUNITY
            </p>
            <div class="community-btn" onclick="window.open('https://t.me/bytweet')">
                <span class="text-sm font-bold text-blue-400">Channel</span>
                <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14"></path></svg>
            </div>
            <div class="community-btn" onclick="window.open('https://t.me/vipadrian')">
                <span class="text-sm font-bold text-green-400">Support</span>
                <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14"></path></svg>
            </div>
        </div>

        <div class="mb-8">
            <p class="text-[10px] text-gray-500 font-black mb-4 flex items-center gap-2 uppercase tracking-widest">
                <svg width="14" height="14" fill="currentColor" viewBox="0 0 24 24"><path d="M16 6l2.29 2.29-4.88 4.88-4-4L2 16.59 3.41 18l6-6 4 4 6.3-6.29L22 12V6z"/></svg>
                Global Stats
            </p>
            <div class="grid grid-cols-1 gap-3">
                <div class="stat-card flex items-center gap-4 border-l-4 border-orange-500">
                    <div class="p-3 bg-orange-500/10 rounded-xl text-orange-500">
                        <svg width="24" height="24" fill="currentColor" viewBox="0 0 24 24"><path d="M16 11c1.66 0 2.99-1.34 2.99-3S17.66 5 16 5s-3 1.34-3 3 1.34 3 3 3zm-8 0c1.66 0 2.99-1.34 2.99-3S9.66 5 8 5 5 6.34 5 8s1.34 3 3 3zm0 2c-2.33 0-7 1.17-7 3.5V19h14v-2.5c0-2.33-4.67-3.5-7-3.5zm8 0c-.29 0-.62.02-.97.05 1.16.84 1.97 1.97 1.97 3.45V19h6v-2.5c0-2.33-4.67-3.5-7-3.5z"/></svg>
                    </div>
                    <div>
                        <p class="text-[10px] text-gray-500 font-bold uppercase">Total Users</p>
                        <p class="text-2xl font-black italic">1,248</p>
                    </div>
                </div>
                <div class="stat-card flex items-center gap-4 border-l-4 border-green-500">
                    <div class="p-3 bg-green-500/10 rounded-xl text-green-500">
                        <svg width="24" height="24" fill="currentColor" viewBox="0 0 24 24"><path d="M2 21h19v-3H2v3zM20 12h-7V5h7v7zM11 21H2v-7h9v7zM11 12H2V5h9v7z"/></svg>
                    </div>
                    <div>
                        <p class="text-[10px] text-gray-500 font-bold uppercase">Active Today</p>
                        <p class="text-2xl font-black italic text-green-400">182</p>
                    </div>
                </div>
            </div>
        </div>

        <div class="glass-card p-5 mb-10">
            <p class="text-[10px] text-blue-500 font-black mb-4 uppercase">Finance & App</p>
            <div class="flex justify-between items-center py-3 border-b border-white/5">
                <span class="text-sm font-bold">عملة العرض</span>
                <select id="currency-select" class="bg-black/40 border-0 text-xs font-bold">
                    <option value="USD">USD ($)</option>
                    <option value="IQD">IQD (د.ع)</option>
                </select>
            </div>
            <div class="flex justify-between items-center py-3 border-b border-white/5">
                <span class="text-sm font-bold">سعر الصرف (دينار)</span>
                <input type="number" id="iqd-rate-input" value="1500" class="w-16 bg-transparent text-right font-black">
            </div>
            <div class="flex justify-between items-center py-3 border-b border-white/5">
                <span class="text-sm font-bold">الوضع الليلي</span>
                <button onclick="toggleTheme()" class="text-xs bg-white/10 px-3 py-1 rounded-full">تبديل</button>
            </div>
            <div class="flex justify-between items-center py-3 border-b border-white/5" onclick="tg.close()">
                <span class="text-sm font-bold text-red-500">تسجيل الخروج</span>
                <svg width="18" height="18" fill="none" stroke="currentColor" class="text-red-500" viewBox="0 0 24 24"><path d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1"></path></svg>
            </div>
        </div>
        
        <p class="text-center text-[10px] text-gray-600 font-black uppercase mb-10">Developed by @vipadrian • v4.5</p>
    </div>

    <div class="detail-sheet" id="detail-sheet">
        <div class="flex items-center justify-between mb-6">
            <button onclick="closeAllSheets()" class="w-10 h-10 bg-white/5 rounded-full flex items-center justify-center">
                <svg width="20" height="20" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path d="M15 19l-7-7 7-7"></path></svg>
            </button>
            <div class="flex items-center gap-2">
                <img id="modal-icon" src="" class="w-6 h-6">
                <span id="modal-name" class="font-black text-sm uppercase">--</span>
            </div>
            <div class="w-10"></div>
        </div>
        
        <div class="text-center mb-6">
            <p id="modal-price" class="text-4xl font-black tracking-tighter">0.00$</p>
            <p id="modal-converted" class="text-xs font-bold text-yellow-500 mt-1 italic">0 د.ع</p>
        </div>

        <div id="chart-box" class="w-full h-72 glass-card overflow-hidden mb-6 bg-black"></div>

        <div class="grid grid-cols-2 gap-4">
            <div class="stat-card text-center">
                <span class="text-[9px] text-gray-500 block uppercase font-black">24h High</span>
                <span id="stat-high" class="text-green-500 font-black font-mono">0.00$</span>
            </div>
            <div class="stat-card text-center">
                <span class="text-[9px] text-gray-500 block uppercase font-black">24h Low</span>
                <span id="stat-low" class="text-red-500 font-black font-mono">0.00$</span>
            </div>
        </div>
    </div>

    <div class="nav-bar">
        <div class="flex flex-col items-center gap-1 text-gray-500" onclick="alert('قريباً')">
            <svg width="22" height="22" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"></path></svg>
            <span class="text-[9px] font-black uppercase">Chart</span>
        </div>
        <div class="flex flex-col items-center gap-1 text-blue-500">
            <div class="p-3 bg-blue-500/10 rounded-2xl shadow-lg border border-blue-500/20">
                <svg width="24" height="24" fill="currentColor" viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-2 14.5v-9l6 4.5-6 4.5z"/></svg>
            </div>
            <span class="text-[9px] font-black uppercase">Market</span>
        </div>
        <div class="flex flex-col items-center gap-1 text-gray-500" onclick="openSettings()">
            <svg width="22" height="22" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z"></path><circle cx="12" cy="12" r="3"></circle></svg>
            <span class="text-[9px] font-black uppercase">Settings</span>
        </div>
    </div>

    <script type="text/javascript" src="https://s3.tradingview.com/tv.js"></script>
    <script>
        const tg = window.Telegram.WebApp;
        tg.expand();
        tg.headerColor = "#050708";

        let iqdRate = 1500;
        let selectedCurrency = "USD";
        const symbols = ['TONUSDT', 'BTCUSDT', 'ETHUSDT', 'SOLUSDT', 'BNBUSDT', 'PAXGUSDT', 'TRXUSDT', 'XRPUSDT'];
        const coinNames = { 
            'TONUSDT': {name: 'Toncoin', icon: 'https://cryptologos.cc/logos/toncoin-ton-logo.png'},
            'BTCUSDT': {name: 'Bitcoin', icon: 'https://cryptologos.cc/logos/bitcoin-btc-logo.png'},
            'ETHUSDT': {name: 'Ethereum', icon: 'https://cryptologos.cc/logos/ethereum-eth-logo.png'},
            'SOLUSDT': {name: 'Solana', icon: 'https://cryptologos.cc/logos/solana-sol-logo.png'},
            'BNBUSDT': {name: 'BNB', icon: 'https://cryptologos.cc/logos/bnb-bnb-logo.png'},
            'TRXUSDT': {name: 'TRON', icon: 'https://cryptologos.cc/logos/tron-trx-logo.png'},
            'XRPUSDT': {name: 'XRP', icon: 'https://cryptologos.cc/logos/xrp-xrp-logo.png'},
            'PAXGUSDT': {name: 'Gold', icon: 'https://cdn-icons-png.flaticon.com/512/2855/2855172.png'}
        };

        async function fetchMarket() {
            try {
                const response = await fetch('https://api.binance.com/api/v3/ticker/24hr');
                const data = await response.json();
                const filtered = data.filter(item => symbols.includes(item.symbol));
                const list = document.getElementById('market-list');
                list.innerHTML = '';

                filtered.sort((a,b) => symbols.indexOf(a.symbol) - symbols.indexOf(b.symbol)).forEach(coin => {
                    const price = parseFloat(coin.lastPrice);
                    const change = parseFloat(coin.priceChangePercent);
                    const asset = coinNames[coin.symbol];
                    
                    list.innerHTML += `
                        <div class="coin-row" onclick="showDetails('${coin.symbol}', '${price}', '${change}', '${coin.highPrice}', '${coin.lowPrice}')">
                            <div class="flex items-center gap-4">
                                <img src="${asset.icon}" class="w-10 h-10">
                                <div>
                                    <p class="text-sm font-black italic uppercase">${asset.name}</p>
                                    <p class="text-[10px] text-gray-500 font-bold tracking-widest">${coin.symbol.replace('USDT','')}</p>
                                </div>
                            </div>
                            <div class="text-left">
                                <p class="text-md font-black font-mono tracking-tighter">${formatPrice(price)}</p>
                                <p class="text-[10px] font-black ${change >= 0 ? 'text-green-500' : 'text-red-500'} italic">
                                    ${change >= 0 ? '▲' : '▼'} ${Math.abs(change).toFixed(2)}%
                                </p>
                            </div>
                        </div>
                    `;
                });
            } catch (e) { console.error("Update Error"); }
        }

        function formatPrice(price) {
            if(selectedCurrency === "IQD") return (price * iqdRate).toLocaleString() + " د.ع";
            return price.toLocaleString() + " $";
        }

        function showDetails(symbol, price, change, high, low) {
            const asset = coinNames[symbol];
            document.getElementById('modal-icon').src = asset.icon;
            document.getElementById('modal-name').innerText = asset.name;
            document.getElementById('modal-price').innerText = parseFloat(price).toLocaleString() + "$";
            document.getElementById('modal-converted').innerText = (parseFloat(price) * iqdRate).toLocaleString() + " دينار عراقي";
            document.getElementById('stat-high').innerText = parseFloat(high).toLocaleString() + "$";
            document.getElementById('stat-low').innerText = parseFloat(low).toLocaleString() + "$";

            document.getElementById('chart-box').innerHTML = `<div id="tv_chart" style="height:100%"></div>`;
            new TradingView.widget({
                "autosize": true, "symbol": `BINANCE:${symbol}`, "interval": "60",
                "theme": "dark", "style": "3", "locale": "ar", "container_id": "tv_chart",
                "backgroundColor": "#050708", "hide_top_toolbar": true, "hide_legend": true
            });

            document.getElementById('overlay').style.display = 'block';
            document.getElementById('detail-sheet').classList.add('active');
        }

        function openSettings() {
            document.getElementById('overlay').style.display = 'block';
            document.getElementById('settings-sheet').classList.add('active');
        }

        function closeAllSheets() {
            document.getElementById('detail-sheet').classList.remove('active');
            document.getElementById('settings-sheet').classList.remove('active');
            document.getElementById('overlay').style.display = 'none';
        }

        function toggleTheme() { document.body.classList.toggle('light-mode'); }

        document.getElementById('currency-select').addEventListener('change', (e) => {
            selectedCurrency = e.target.value;
            fetchMarket();
        });
        
        document.getElementById('iqd-rate-input').addEventListener('input', (e) => {
            iqdRate = e.target.value || 1;
            fetchMarket();
        });

        setInterval(fetchMarket, 5000);
        fetchMarket();
    </script>
</body>
</html>
