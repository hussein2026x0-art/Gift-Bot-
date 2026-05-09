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
        
        .detail-sheet, .settings-sheet { position: fixed; bottom: -100%; left: 0; right: 0; height: 92vh; background: #050708; border-top: 1px solid var(--border); z-index: 1000; transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1); border-radius: 30px 30px 0 0; padding: 20px; overflow-y: auto; }
        .detail-sheet.active, .settings-sheet.active { bottom: 0; }
        
        .overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.85); display: none; z-index: 999; backdrop-filter: blur(8px); }
        .nav-bar { position: fixed; bottom: 0; left: 0; right: 0; background: rgba(10, 12, 15, 0.9); backdrop-filter: blur(15px); border-top: 1px solid var(--border); height: 85px; display: flex; align-items: center; justify-content: space-around; z-index: 500; }
        
        .stat-card { background: rgba(255,255,255,0.03); border-radius: 18px; padding: 15px; border: 1px solid var(--border); }
        .community-btn { display: flex; align-items: center; justify-content: space-between; padding: 14px; background: rgba(255,255,255,0.02); border-radius: 15px; margin-bottom: 8px; border: 1px solid var(--border); transition: 0.2s; }

        input, select { background: #1a1e23; border: 1px solid #2d3748; border-radius: 10px; padding: 6px 10px; color: white; font-size: 13px; outline: none; }
    </style>
</head>
<body>

    <header class="p-6 flex justify-between items-center">
        <div>
            <h1 class="text-2xl font-black italic uppercase tracking-tighter">VETO <span class="text-blue-500">MARKET</span></h1>
            <p class="text-[10px] text-gray-500 font-bold tracking-widest mt-1 uppercase" id="app-version">Adrian Engine v5.0 Pro</p>
        </div>
        <div class="flex items-center gap-2 bg-green-500/10 px-3 py-1 rounded-full border border-green-500/20">
            <div class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></div>
            <span class="text-[9px] font-bold text-green-500 uppercase">Live</span>
        </div>
    </header>

    <section class="mx-4 glass-card overflow-hidden" id="main-market">
        <div id="market-list">
            <div class="p-10 text-center animate-pulse text-gray-600 font-bold">جاري الاتصال بالسوق...</div>
        </div>
    </section>

    <div class="overlay" id="overlay" onclick="closeAllSheets()"></div>

    <div class="settings-sheet" id="settings-sheet">
        <div class="flex items-center justify-between mb-8">
            <button onclick="closeAllSheets()" class="w-10 h-10 bg-white/5 rounded-full flex items-center justify-center">
                <svg width="20" height="20" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path d="M15 19l-7-7 7-7"></path></svg>
            </button>
            <h2 class="text-lg font-black uppercase italic tracking-widest" data-lang="settings_title">Settings</h2>
            <div class="w-10"></div>
        </div>

        <div class="mb-8">
            <p class="text-[10px] text-gray-500 font-black mb-4 uppercase tracking-widest" data-lang="community">COMMUNITY</p>
            <div class="community-btn" onclick="window.open('https://t.me/bytweet')">
                <span class="text-sm font-bold text-blue-400" data-lang="channel">Channel</span>
                <span class="text-[10px] text-gray-500">@bytweet</span>
            </div>
            <div class="community-btn" onclick="window.open('https://t.me/vipadrian')">
                <span class="text-sm font-bold text-green-400" data-lang="support">Support (Adrian)</span>
                <span class="text-[10px] text-gray-500">@vipadrian</span>
            </div>
        </div>

        <div class="mb-8">
            <p class="text-[10px] text-gray-500 font-black mb-4 uppercase tracking-widest" data-lang="global_stats">Global Stats</p>
            <div class="grid grid-cols-2 gap-3">
                <div class="stat-card">
                    <p class="text-[9px] text-gray-500 font-bold uppercase" data-lang="total_users">Total Users</p>
                    <p class="text-xl font-black italic" id="total-users-val">0</p>
                </div>
                <div class="stat-card">
                    <p class="text-[9px] text-gray-500 font-bold uppercase" data-lang="active_now">Active Now</p>
                    <p class="text-xl font-black italic text-green-400" id="active-users-val">0</p>
                </div>
            </div>
        </div>

        <div class="glass-card p-5 mb-10">
            <p class="text-[10px] text-blue-500 font-black mb-4 uppercase" data-lang="app_pref">Preferences</p>
            
            <div class="flex justify-between items-center py-3 border-b border-white/5">
                <span class="text-sm font-bold" data-lang="language">Language</span>
                <select id="lang-select" onchange="changeLanguage(this.value)">
                    <option value="ar">العربية</option>
                    <option value="en">English</option>
                </select>
            </div>

            <div class="flex justify-between items-center py-3 border-b border-white/5">
                <span class="text-sm font-bold" data-lang="currency">Currency</span>
                <select id="currency-select">
                    <option value="USD">USD ($)</option>
                    <option value="IQD">IQD (د.ع)</option>
                </select>
            </div>

            <div class="flex justify-between items-center py-3 border-b border-white/5">
                <span class="text-sm font-bold" data-lang="exchange_rate">Exchange Rate</span>
                <input type="number" id="iqd-rate-input" value="1520" class="w-16 bg-transparent text-right font-black">
            </div>
        </div>
        
        <p class="text-center text-[10px] text-gray-600 font-black uppercase mb-10">System Owner: Adrian • 2026</p>
    </div>

    <div class="detail-sheet" id="detail-sheet">
        <div class="flex items-center justify-between mb-6">
            <button onclick="closeAllSheets()" class="w-10 h-10 bg-white/5 rounded-full flex items-center justify-center">
                <svg width="20" height="20" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path d="M15 19l-7-7 7-7"></path></svg>
            </button>
            <div class="flex items-center gap-2">
                <img id="modal-icon" src="" class="w-6 h-6">
                <span id="modal-name" class="font-black text-sm uppercase italic">--</span>
            </div>
            <div class="w-10"></div>
        </div>
        
        <div class="text-center mb-6">
            <p id="modal-price" class="text-4xl font-black tracking-tighter">0.00$</p>
            <p id="modal-converted" class="text-xs font-bold text-yellow-500 mt-1 italic">0 د.ع</p>
        </div>

        <div id="chart-box" class="w-full h-64 glass-card overflow-hidden mb-6 bg-black"></div>

        <div class="grid grid-cols-2 gap-4">
            <div class="stat-card text-center">
                <span class="text-[9px] text-gray-500 block uppercase font-black">24h High</span>
                <span id="stat-high" class="text-green-500 font-black">0.00$</span>
            </div>
            <div class="stat-card text-center">
                <span class="text-[9px] text-gray-500 block uppercase font-black">24h Low</span>
                <span id="stat-low" class="text-red-500 font-black">0.00$</span>
            </div>
        </div>
    </div>

    <div class="nav-bar">
        <div class="flex flex-col items-center gap-1 text-gray-500" onclick="tg.showAlert('Coming Soon')">
            <svg width="22" height="22" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"></path></svg>
            <span class="text-[9px] font-black uppercase" data-lang="nav_chart">Chart</span>
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
        const tg = window.Telegram.WebApp;
        tg.expand();
        tg.headerColor = "#050708";

        let iqdRate = 1520;
        let currentLang = 'ar';
        let selectedCurrency = "USD";

        const translations = {
            ar: {
                settings_title: "الإعدادات",
                community: "المجتمع",
                channel: "القناة الرسمية",
                support: "الدعم الفني (أدريان)",
                global_stats: "إحصائيات المستخدمين",
                total_users: "إجمالي المستخدمين",
                active_now: "نشط الآن",
                app_pref: "تفضيلات التطبيق",
                language: "اللغة",
                currency: "عملة العرض",
                exchange_rate: "سعر الصرف (دينار)",
                nav_chart: "منصة التداول",
                nav_market: "السوق",
                nav_settings: "الإعدادات"
            },
            en: {
                settings_title: "Settings",
                community: "COMMUNITY",
                channel: "Official Channel",
                support: "Technical Support (Adrian)",
                global_stats: "Global Statistics",
                total_users: "Total Users",
                active_now: "Active Now",
                app_pref: "Preferences",
                language: "Language",
                currency: "Currency",
                exchange_rate: "Exchange Rate (IQD)",
                nav_chart: "Chart",
                nav_market: "Market",
                nav_settings: "Settings"
            }
        };

        function changeLanguage(lang) {
            currentLang = lang;
            document.documentElement.lang = lang;
            document.documentElement.dir = lang === 'ar' ? 'rtl' : 'ltr';
            document.querySelectorAll('[data-lang]').forEach(el => {
                const key = el.getAttribute('data-lang');
                el.innerText = translations[lang][key];
            });
            fetchMarket(); // Update UI
        }

        function updateLiveUsers() {
            // محاكاة أرقام مستخدمين واقعية
            const baseUsers = 2000;
            const activeNow = Math.floor(Math.random() * (210 - 150 + 1)) + 150;
            document.getElementById('total-users-val').innerText = baseUsers.toLocaleString();
            document.getElementById('active-users-val').innerText = activeNow;
        }

        const symbols = ['TONUSDT', 'BTCUSDT', 'ETHUSDT', 'SOLUSDT', 'BNBUSDT', 'XRPUSDT', 'TRXUSDT'];
        const coinNames = { 
            'TONUSDT': {name: 'Toncoin', icon: 'https://cryptologos.cc/logos/toncoin-ton-logo.png'},
            'BTCUSDT': {name: 'Bitcoin', icon: 'https://cryptologos.cc/logos/bitcoin-btc-logo.png'},
            'ETHUSDT': {name: 'Ethereum', icon: 'https://cryptologos.cc/logos/ethereum-eth-logo.png'},
            'SOLUSDT': {name: 'Solana', icon: 'https://cryptologos.cc/logos/solana-sol-logo.png'},
            'BNBUSDT': {name: 'BNB', icon: 'https://cryptologos.cc/logos/bnb-bnb-logo.png'},
            'XRPUSDT': {name: 'XRP', icon: 'https://cryptologos.cc/logos/xrp-xrp-logo.png'},
            'TRXUSDT': {name: 'TRON', icon: 'https://cryptologos.cc/logos/tron-trx-logo.png'}
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
                                <img src="${asset.icon}" class="w-9 h-9">
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
            } catch (e) { console.error("Market error"); }
        }

        function formatPrice(price) {
            if(selectedCurrency === "IQD") return (price * iqdRate).toLocaleString() + (currentLang === 'ar' ? ' د.ع' : ' IQD');
            return price.toLocaleString() + " $";
        }

        function showDetails(symbol, price, change, high, low) {
            const asset = coinNames[symbol];
            document.getElementById('modal-icon').src = asset.icon;
            document.getElementById('modal-name').innerText = asset.name;
            document.getElementById('modal-price').innerText = parseFloat(price).toLocaleString() + "$";
            document.getElementById('modal-converted').innerText = (parseFloat(price) * iqdRate).toLocaleString() + (currentLang === 'ar' ? " دينار عراقي" : " IQD");
            document.getElementById('stat-high').innerText = parseFloat(high).toLocaleString() + "$";
            document.getElementById('stat-low').innerText = parseFloat(low).toLocaleString() + "$";

            document.getElementById('chart-box').innerHTML = `<div id="tv_chart" style="height:100%"></div>`;
            new TradingView.widget({
                "autosize": true, "symbol": `BINANCE:${symbol}`, "interval": "60",
                "theme": "dark", "style": "3", "locale": currentLang, "container_id": "tv_chart",
                "backgroundColor": "#050708", "hide_top_toolbar": true
            });

            document.getElementById('overlay').style.display = 'block';
            document.getElementById('detail-sheet').classList.add('active');
        }

        function openSettings() {
            updateLiveUsers();
            document.getElementById('overlay').style.display = 'block';
            document.getElementById('settings-sheet').classList.add('active');
        }

        function closeAllSheets() {
            document.getElementById('detail-sheet').classList.remove('active');
            document.getElementById('settings-sheet').classList.remove('active');
            document.getElementById('overlay').style.display = 'none';
        }

        document.getElementById('currency-select').addEventListener('change', (e) => {
            selectedCurrency = e.target.value;
            fetchMarket();
        });
        
        document.getElementById('iqd-rate-input').addEventListener('input', (e) => {
            iqdRate = e.target.value || 1;
            fetchMarket();
        });

        // تشغيل البوت
        setInterval(fetchMarket, 5000);
        setInterval(updateLiveUsers, 10000);
        fetchMarket();
        updateLiveUsers();
        changeLanguage('ar');
    </script>
</body>
</html>
