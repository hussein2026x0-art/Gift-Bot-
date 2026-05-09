<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Veto Market | Adrian</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;900&display=swap" rel="stylesheet">
    <style>
        :root { --bg: #050708; --card: #0e1116; --accent: #0088cc; --border: #1a1e23; }
        body { font-family: 'Cairo', sans-serif; background: var(--bg); color: #fff; overflow-x: hidden; padding-bottom: 100px; transition: 0.3s; }
        
        .glass-card { background: var(--card); border: 1px solid var(--border); border-radius: 20px; transition: 0.3s; }
        .coin-row { padding: 18px; border-bottom: 1px solid var(--border); cursor: pointer; display: flex; align-items: center; justify-content: space-between; }
        
        .detail-sheet, .settings-sheet, .wallet-sheet { position: fixed; bottom: -100%; left: 0; right: 0; height: 92vh; background: #050708; border-top: 1px solid var(--border); z-index: 1000; transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1); border-radius: 30px 30px 0 0; padding: 20px; overflow-y: auto; }
        .active { bottom: 0 !important; }
        
        .overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.85); display: none; z-index: 999; backdrop-filter: blur(8px); }
        .nav-bar { position: fixed; bottom: 0; left: 0; right: 0; background: rgba(10, 12, 15, 0.9); backdrop-filter: blur(15px); border-top: 1px solid var(--border); height: 85px; display: flex; align-items: center; justify-content: space-around; z-index: 500; }
        
        .stat-card { background: rgba(255,255,255,0.03); border-radius: 18px; padding: 15px; border: 1px solid var(--border); }
        .btn-buy { background: #02c076; color: white; font-weight: 900; border-radius: 12px; padding: 12px; width: 100%; transition: 0.2s; }
        .btn-sell { background: #f84960; color: white; font-weight: 900; border-radius: 12px; padding: 12px; width: 100%; transition: 0.2s; }
        .btn-buy:active, .btn-sell:active { transform: scale(0.95); opacity: 0.8; }

        input, select { background: #1a1e23; border: 1px solid #2d3748; border-radius: 10px; padding: 8px; color: white; font-size: 14px; outline: none; width: 100%; }
        .history-item { border-left: 3px solid var(--accent); background: rgba(255,255,255,0.02); padding: 10px; margin-bottom: 8px; border-radius: 8px; }
    </style>
</head>
<body>

    <header class="p-6 flex justify-between items-center">
        <div>
            <h1 class="text-2xl font-black italic uppercase tracking-tighter">VETO <span class="text-blue-500">MARKET</span></h1>
            <p class="text-[10px] text-gray-500 font-bold tracking-widest mt-1 uppercase">Adrian Trading Engine v6.0</p>
        </div>
        <div class="text-right">
            <p class="text-[9px] text-gray-500 font-bold uppercase" data-lang="balance">Your Balance</p>
            <p id="main-balance" class="text-lg font-black text-green-400 font-mono">0.00$</p>
        </div>
    </header>

    <section class="mx-4 glass-card overflow-hidden">
        <div class="p-4 border-b border-white/5 flex justify-between items-center">
            <h3 class="text-xs font-black uppercase tracking-widest text-gray-400" data-lang="live_market">Live Market</h3>
            <span class="text-[9px] bg-blue-500/20 text-blue-400 px-2 py-1 rounded">BINANCE FEED</span>
        </div>
        <div id="market-list">
            <div class="p-10 text-center animate-pulse text-gray-600 font-bold">Connecting to Exchange...</div>
        </div>
    </section>

    <div class="overlay" id="overlay" onclick="closeAllSheets()"></div>

    <div class="wallet-sheet" id="wallet-sheet">
        <div class="flex items-center justify-between mb-8">
            <button onclick="closeAllSheets()" class="w-10 h-10 bg-white/5 rounded-full flex items-center justify-center">
                <svg width="20" height="20" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path d="M15 19l-7-7 7-7"></path></svg>
            </button>
            <h2 class="text-lg font-black uppercase italic tracking-widest" data-lang="wallet_history">Portfolio</h2>
            <div class="w-10"></div>
        </div>

        <div class="stat-card mb-6 border-blue-500/30 bg-blue-500/5">
            <p class="text-xs font-bold text-blue-400 uppercase mb-1" data-lang="total_assets">Total Assets (USD)</p>
            <p id="wallet-usd" class="text-3xl font-black font-mono">0.00$</p>
        </div>

        <h3 class="text-sm font-black mb-4 uppercase text-gray-500" data-lang="my_assets">My Assets</h3>
        <div id="my-assets-list" class="space-y-3 mb-8">
            </div>

        <h3 class="text-sm font-black mb-4 uppercase text-gray-500" data-lang="trade_history">Trade History</h3>
        <div id="trade-history" class="space-y-2">
            <p class="text-center text-gray-600 py-4 text-xs">No trades yet</p>
        </div>
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

        <div id="chart-box" class="w-full h-48 glass-card overflow-hidden mb-6 bg-black"></div>

        <div class="glass-card p-4 mb-6">
            <div class="flex justify-between mb-4">
                <span class="text-xs font-bold text-gray-400 uppercase" data-lang="amount">Order Amount ($)</span>
                <span class="text-xs font-bold text-blue-500" onclick="setMax()">MAX</span>
            </div>
            <input type="number" id="trade-amount" placeholder="0.00" class="mb-4 text-lg font-black font-mono">
            
            <div class="grid grid-cols-2 gap-4">
                <button class="btn-buy" onclick="processTrade('buy')" data-lang="buy">BUY</button>
                <button class="btn-sell" onclick="processTrade('sell')" data-lang="sell">SELL</button>
            </div>
        </div>
    </div>

    <div class="settings-sheet" id="settings-sheet">
        <div class="flex items-center justify-between mb-8">
            <button onclick="closeAllSheets()" class="w-10 h-10 bg-white/5 rounded-full flex items-center justify-center">
                <svg width="20" height="20" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path d="M15 19l-7-7 7-7"></path></svg>
            </button>
            <h2 class="text-lg font-black uppercase italic" data-lang="settings_title">Settings</h2>
            <div class="w-10"></div>
        </div>

        <div class="stat-card flex items-center gap-4 border-l-4 border-orange-500 mb-6">
            <div class="p-3 bg-orange-500/10 rounded-xl text-orange-500">
                <svg width="24" height="24" fill="currentColor" viewBox="0 0 24 24"><path d="M16 11c1.66 0 2.99-1.34 2.99-3S17.66 5 16 5s-3 1.34-3 3 1.34 3 3 3zm-8 0c1.66 0 2.99-1.34 2.99-3S9.66 5 8 5 5 6.34 5 8s1.34 3 3 3zm0 2c-2.33 0-7 1.17-7 3.5V19h14v-2.5c0-2.33-4.67-3.5-7-3.5zm8 0c-.29 0-.62.02-.97.05 1.16.84 1.97 1.97 1.97 3.45V19h6v-2.5c0-2.33-4.67-3.5-7-3.5z"/></svg>
            </div>
            <div>
                <p class="text-[10px] text-gray-500 font-bold uppercase" data-lang="active_now">Active Traders Now</p>
                <p id="active-users" class="text-2xl font-black italic">1,248</p>
            </div>
        </div>

        <div class="glass-card p-5 space-y-4">
            <div class="flex justify-between items-center">
                <span class="text-sm font-bold" data-lang="language">Language</span>
                <select id="lang-select" onchange="changeLanguage(this.value)" class="w-24">
                    <option value="ar">العربية</option>
                    <option value="en">English</option>
                </select>
            </div>
            <div class="flex justify-between items-center">
                <span class="text-sm font-bold" data-lang="exchange_rate">IQD Rate</span>
                <input type="number" id="iqd-rate-input" value="1520" class="w-20 text-right font-bold">
            </div>
            <div class="pt-4 border-t border-white/5">
                <button onclick="window.open('https://t.me/vipadrian')" class="w-full py-3 bg-blue-600 rounded-xl font-black text-xs uppercase tracking-widest">Contact Adrian (Support)</button>
            </div>
        </div>
    </div>

    <div class="nav-bar">
        <div class="flex flex-col items-center gap-1 text-gray-500" onclick="openWallet()">
            <svg width="22" height="22" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M3 10h18M7 15h1m4 0h1m-7 4h12a3 3 0 003-3V8a3 3 0 00-3-3H6a3 3 0 00-3 3v8a3 3 0 003 3z"></path></svg>
            <span class="text-[9px] font-black uppercase" data-lang="nav_wallet">Wallet</span>
        </div>
        <div class="flex flex-col items-center gap-1 text-blue-500" onclick="closeAllSheets()">
            <div class="p-3 bg-blue-500/10 rounded-2xl border border-blue-500/20">
                <svg width="24" height="24" fill="currentColor" viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-2 14.5v-9l6 4.5-6 4.5z"/></svg>
            </div>
            <span class="text-[9px] font-black uppercase" data-lang="nav_market">Market</span>
        </div>
        <div class="flex flex-col items-center gap-1 text-gray-500" onclick="openSettings()">
            <svg width="22" height="22" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z"></path><circle cx="12" cy="12" r="3"></circle></svg>
            <span class="text-[9px] font-black uppercase" data-lang="nav_settings">Settings</span>
        </div>
    </div>

    <script type="text/javascript" src="https://s3.tradingview.com/tv.js"></script>
    <script>
        // --- 1. CONFIG & STATE ---
        const tg = window.Telegram.WebApp;
        let iqdRate = 1520;
        let currentSymbol = "";
        let currentPrice = 0;
        
        // المحفظة (مخزنة في ذاكرة المتصفح لمحاكاة الواقع)
        let wallet = JSON.parse(localStorage.getItem('adrian_wallet')) || {
            balance: 5000.00, // رصيد وهمي للبدء
            assets: {},
            history: []
        };

        const symbols = ['TONUSDT', 'BTCUSDT', 'ETHUSDT', 'SOLUSDT', 'BNBUSDT', 'TRXUSDT'];
        const coinNames = { 
            'TONUSDT': {name: 'Toncoin', icon: 'https://cryptologos.cc/logos/toncoin-ton-logo.png'},
            'BTCUSDT': {name: 'Bitcoin', icon: 'https://cryptologos.cc/logos/bitcoin-btc-logo.png'},
            'ETHUSDT': {name: 'Ethereum', icon: 'https://cryptologos.cc/logos/ethereum-eth-logo.png'},
            'SOLUSDT': {name: 'Solana', icon: 'https://cryptologos.cc/logos/solana-sol-logo.png'},
            'BNBUSDT': {name: 'BNB', icon: 'https://cryptologos.cc/logos/bnb-bnb-logo.png'},
            'TRXUSDT': {name: 'TRON', icon: 'https://cryptologos.cc/logos/tron-trx-logo.png'}
        };

        const translations = {
            ar: {
                balance: "رصيدك المتاح", live_market: "السوق المباشر", wallet_history: "المحفظة والسجل",
                total_assets: "إجمالي الأصول (دولار)", my_assets: "عملاتي", trade_history: "سجل التداول",
                amount: "مبلغ الطلب ($)", buy: "شراء", sell: "بيع", settings_title: "الإعدادات",
                active_now: "المتداولون النشطون الآن", language: "اللغة", exchange_rate: "سعر صرف الدينار",
                nav_wallet: "المحفظة", nav_market: "السوق", nav_settings: "الإعدادات"
            },
            en: {
                balance: "Your Balance", live_market: "Live Market", wallet_history: "Portfolio",
                total_assets: "Total Assets (USD)", my_assets: "My Assets", trade_history: "History",
                amount: "Order Amount ($)", buy: "BUY", sell: "SELL", settings_title: "Settings",
                active_now: "Active Traders", language: "Language", exchange_rate: "IQD Rate",
                nav_wallet: "Wallet", nav_market: "Market", nav_settings: "Settings"
            }
        };

        // --- 2. MARKET LOGIC ---
        async function fetchMarket() {
            try {
                const res = await fetch('https://api.binance.com/api/v3/ticker/24hr');
                const data = await res.json();
                const filtered = data.filter(item => symbols.includes(item.symbol));
                const list = document.getElementById('market-list');
                list.innerHTML = '';

                filtered.sort((a,b) => symbols.indexOf(a.symbol) - symbols.indexOf(b.symbol)).forEach(coin => {
                    const price = parseFloat(coin.lastPrice);
                    const change = parseFloat(coin.priceChangePercent);
                    const asset = coinNames[coin.symbol];
                    
                    list.innerHTML += `
                        <div class="coin-row" onclick="showDetails('${coin.symbol}', ${price}, '${coin.highPrice}', '${coin.lowPrice}')">
                            <div class="flex items-center gap-4">
                                <img src="${asset.icon}" class="w-9 h-9">
                                <div>
                                    <p class="text-sm font-black italic uppercase">${asset.name}</p>
                                    <p class="text-[10px] text-gray-500 font-bold tracking-widest">${coin.symbol.replace('USDT','')}</p>
                                </div>
                            </div>
                            <div class="text-left">
                                <p class="text-md font-black font-mono tracking-tighter">${price.toLocaleString()}$</p>
                                <p class="text-[10px] font-black ${change >= 0 ? 'text-green-500' : 'text-red-500'} italic">
                                    ${change >= 0 ? '▲' : '▼'} ${Math.abs(change).toFixed(2)}%
                                </p>
                            </div>
                        </div>
                    `;
                });
                updateUI();
            } catch (e) { console.error("API Error"); }
        }

        // --- 3. TRADING LOGIC ---
        function processTrade(type) {
            const amountUSD = parseFloat(document.getElementById('trade-amount').value);
            if (!amountUSD || amountUSD <= 0) return tg.showAlert("Please enter a valid amount");

            if (type === 'buy') {
                if (wallet.balance < amountUSD) return tg.showAlert("Insufficient Balance!");
                
                const coinAmount = amountUSD / currentPrice;
                wallet.balance -= amountUSD;
                wallet.assets[currentSymbol] = (wallet.assets[currentSymbol] || 0) + coinAmount;
                addHistory('BUY', currentSymbol, amountUSD, coinAmount);
            } else {
                const userCoinBalance = wallet.assets[currentSymbol] || 0;
                const coinsToSell = amountUSD / currentPrice;
                
                if (userCoinBalance < coinsToSell) return tg.showAlert("Insufficient Coins!");
                
                wallet.balance += amountUSD;
                wallet.assets[currentSymbol] -= coinsToSell;
                addHistory('SELL', currentSymbol, amountUSD, coinsToSell);
            }

            saveWallet();
            updateUI();
            tg.showScanQrPopup({text: "Processing Order..."});
            setTimeout(() => { tg.closeScanQrPopup(); closeAllSheets(); }, 1000);
        }

        function addHistory(type, symbol, usd, coins) {
            wallet.history.unshift({
                type, symbol, usd: usd.toFixed(2), coins: coins.toFixed(6), date: new Date().toLocaleTimeString()
            });
        }

        function saveWallet() {
            localStorage.setItem('adrian_wallet', JSON.stringify(wallet));
        }

        // --- 4. UI LOGIC ---
        function updateUI() {
            document.getElementById('main-balance').innerText = wallet.balance.toLocaleString(undefined, {minimumFractionDigits: 2}) + "$";
            document.getElementById('wallet-usd').innerText = wallet.balance.toLocaleString(undefined, {minimumFractionDigits: 2}) + "$";
            
            // تحديث قائمة العملات المملوكة
            const assetList = document.getElementById('my-assets-list');
            assetList.innerHTML = '';
            for (const [sym, amt] of Object.entries(wallet.assets)) {
                if (amt > 0) {
                    assetList.innerHTML += `
                        <div class="stat-card flex justify-between items-center">
                            <span class="font-black text-xs">${sym.replace('USDT','')}</span>
                            <span class="font-mono font-bold text-blue-400">${amt.toFixed(6)}</span>
                        </div>
                    `;
                }
            }

            // تحديث السجل
            const historyList = document.getElementById('trade-history');
            if (wallet.history.length > 0) {
                historyList.innerHTML = wallet.history.map(h => `
                    <div class="history-item flex justify-between items-center">
                        <div>
                            <p class="text-[10px] font-black ${h.type === 'BUY' ? 'text-green-500' : 'text-red-500'}">${h.type} ${h.symbol}</p>
                            <p class="text-[8px] text-gray-500">${h.date}</p>
                        </div>
                        <p class="text-xs font-black font-mono">${h.usd}$</p>
                    </div>
                `).join('');
            }
        }

        function showDetails(symbol, price, high, low) {
            currentSymbol = symbol;
            currentPrice = price;
            const asset = coinNames[symbol];
            
            document.getElementById('modal-icon').src = asset.icon;
            document.getElementById('modal-name').innerText = asset.name;
            document.getElementById('modal-price').innerText = price.toLocaleString() + "$";
            document.getElementById('modal-converted').innerText = (price * iqdRate).toLocaleString() + " IQD";
            
            document.getElementById('chart-box').innerHTML = `<div id="tv_chart" style="height:100%"></div>`;
            new TradingView.widget({
                "autosize": true, "symbol": `BINANCE:${symbol}`, "interval": "60",
                "theme": "dark", "style": "3", "container_id": "tv_chart",
                "backgroundColor": "#050708", "hide_top_toolbar": true
            });

                        document.getElementById('overlay').style.display = 'block';
            document.getElementById('detail-sheet').classList.add('active');
        }

        function changeLanguage(lang) {
            document.documentElement.dir = lang === 'ar' ? 'rtl' : 'ltr';
            document.querySelectorAll('[data-lang]').forEach(el => {
                el.innerText = translations[lang][el.getAttribute('data-lang')];
            });
        }

        function openWallet() { document.getElementById('overlay').style.display = 'block'; document.getElementById('wallet-sheet').classList.add('active'); }
        function openSettings() { document.getElementById('overlay').style.display = 'block'; document.getElementById('settings-sheet').classList.add('active'); }
        function closeAllSheets() { 
            document.querySelectorAll('.detail-sheet, .settings-sheet, .wallet-sheet').forEach(s => s.classList.remove('active'));
            document.getElementById('overlay').style.display = 'none';
        }
        function setMax() { document.getElementById('trade-amount').value = wallet.balance.toFixed(2); }

        // Start
        setInterval(fetchMarket, 5000);
        fetchMarket();
        changeLanguage('ar');
        tg.expand();
    </script>
</body>
</html>
