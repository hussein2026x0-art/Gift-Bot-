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
        :root { --bg: #050708; --card: #12151a; --accent: #0088cc; --border: #1f2328; }
        body { font-family: 'Cairo', sans-serif; background: var(--bg); color: #fff; overflow-x: hidden; padding-bottom: 110px; transition: 0.3s; }
        
        /* Light Mode Classes */
        body.light-mode { --bg: #f3f4f6; --card: #ffffff; --border: #e5e7eb; color: #111827; }
        body.light-mode .glass-card { background: #ffffff; border-color: #e5e7eb; box-shadow: 0 4px 6px -1px rgba(0,0,0,0.1); }
        body.light-mode .coin-row { border-color: #f3f4f6; }
        body.light-mode .nav-bar { background: rgba(255, 255, 255, 0.9); border-color: #e5e7eb; }
        body.light-mode .detail-sheet, body.light-mode .settings-sheet { background: #ffffff; border-color: #e5e7eb; }

        .glass-card { background: var(--card); border: 1px solid var(--border); border-radius: 24px; transition: 0.3s; }
        .coin-row { padding: 18px; border-bottom: 1px solid #1a1e23; cursor: pointer; display: flex; align-items: center; justify-content: space-between; }
        .coin-row:last-child { border-bottom: none; }
        
        .detail-sheet, .settings-sheet { position: fixed; bottom: -100%; left: 0; right: 0; height: 90vh; background: #0b0e11; border-top: 1px solid var(--border); z-index: 1000; transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1); border-radius: 32px 32px 0 0; padding: 24px; overflow-y: auto; }
        .detail-sheet.active, .settings-sheet.active { bottom: 0; }
        
        .overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.8); display: none; z-index: 999; backdrop-filter: blur(4px); }
        .nav-bar { position: fixed; bottom: 0; left: 0; right: 0; background: rgba(18, 21, 26, 0.95); backdrop-filter: blur(12px); border-top: 1px solid var(--border); height: 90px; display: flex; align-items: center; justify-content: space-around; z-index: 500; }
        
        .setting-item { display: flex; align-items: center; justify-content: space-between; padding: 14px 0; border-bottom: 1px solid var(--border); }
        .section-title { font-size: 12px; font-weight: 900; color: var(--accent); text-transform: uppercase; margin-top: 20px; display: block; letter-spacing: 1px; }
        
        input, select { background: rgba(255,255,255,0.05); border: 1px solid var(--border); border-radius: 12px; padding: 8px 12px; color: white; font-size: 14px; outline: none; }
        body.light-mode input, body.light-mode select { background: #f9fafb; color: #111827; }
        
        .price-up { color: #02c076; } .price-down { color: #f84960; }
    </style>
</head>
<body>

    <header class="p-6 flex justify-between items-center">
        <div>
            <h1 class="text-2xl font-black italic uppercase tracking-tighter">VETO <span class="text-blue-500">MARKET</span></h1>
            <p class="text-[10px] text-gray-500 font-bold tracking-widest mt-1 uppercase">Official Adrian Engine v4.0</p>
        </div>
        <div id="connection-status" class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></div>
    </header>

    <section class="mx-4 glass-card overflow-hidden shadow-2xl">
        <div id="market-list">
            <div class="p-10 text-center animate-pulse text-gray-600">جاري الاتصال بالسوق...</div>
        </div>
    </section>

    <div class="overlay" id="overlay" onclick="closeAllSheets()"></div>

    <div class="detail-sheet" id="detail-sheet">
        <div class="w-12 h-1 bg-gray-700 rounded-full mx-auto mb-6"></div>
        <div class="flex justify-between items-start mb-6">
            <div class="flex items-center gap-3">
                <img id="modal-icon" src="" class="w-12 h-12">
                <div>
                    <h2 id="modal-name" class="text-2xl font-black">---</h2>
                    <p id="modal-symbol" class="text-gray-500 text-xs uppercase">---</p>
                </div>
            </div>
            <div class="text-left">
                <p id="modal-price" class="text-2xl font-black font-mono">0.00$</p>
                <p id="modal-converted" class="text-sm font-bold text-yellow-500">0 IQD</p>
            </div>
        </div>
        <div id="chart-box" class="w-full h-64 glass-card overflow-hidden mb-6"></div>
        <div class="grid grid-cols-2 gap-3">
            <div class="bg-white/5 p-4 rounded-2xl border border-white/5 text-center">
                <span class="text-[10px] text-gray-500 block mb-1 uppercase font-bold tracking-widest">24h High</span>
                <span id="stat-high" class="text-green-500 font-black font-mono">0.00$</span>
            </div>
            <div class="bg-white/5 p-4 rounded-2xl border border-white/5 text-center">
                <span class="text-[10px] text-gray-500 block mb-1 uppercase font-bold tracking-widest">24h Low</span>
                <span id="stat-low" class="text-red-500 font-black font-mono">0.00$</span>
            </div>
        </div>
    </div>

    <div class="settings-sheet" id="settings-sheet">
        <div class="w-12 h-1 bg-gray-700 rounded-full mx-auto mb-6"></div>
        <h2 class="text-xl font-black mb-4 flex items-center gap-2">
            <svg width="24" height="24" fill="currentColor" viewBox="0 0 24 24"><path d="M19.14 12.94c.04-.3.06-.61.06-.94 0-.32-.02-.64-.07-.94l2.03-1.58c.18-.14.23-.41.12-.61l-1.92-3.32c-.12-.22-.37-.29-.59-.22l-2.39.96c-.5-.38-1.03-.7-1.62-.94l-.36-2.54c-.04-.24-.24-.41-.48-.41h-3.84c-.24 0-.43.17-.47.41l-.36 2.54c-.59.24-1.13.57-1.62.94l-2.39-.96c-.22-.08-.47 0-.59.22L2.74 8.87c-.12.21-.08.47.12.61l2.03 1.58c-.05.3-.09.63-.09.94s.02.64.07.94l-2.03 1.58c-.18.14-.23.41-.12.61l1.92 3.32c.12.22.37.29.59.22l2.39-.96c.5.38 1.03.7 1.62.94l.36 2.54c.05.24.24.41.48.41h3.84c.24 0 .44-.17.47-.41l.36-2.54c.59-.24 1.13-.56 1.62-.94l2.39.96c.22.08.47 0 .59-.22l1.92-3.32c.12-.22.07-.47-.12-.61l-2.01-1.58zM12 15.6c-1.98 0-3.6-1.62-3.6-3.6s1.62-3.6 3.6-3.6 3.6 1.62 3.6 3.6-1.62 3.6-3.6 3.6z"/></svg>
            الإعدادات
        </h2>

        <span class="section-title">الإعدادات المالية</span>
        <div class="setting-item">
            <span>عملة العرض</span>
            <select id="currency-select">
                <option value="USD">USD ($)</option>
                <option value="IQD">IQD (د.ع)</option>
                <option value="EUR">EUR (€)</option>
            </select>
        </div>
        <div class="setting-item">
            <span>سعر صرف $ يدوي</span>
            <input type="number" id="iqd-rate-input" class="w-24 text-center" value="1460">
        </div>
        <div class="setting-item flex-col items-start gap-2">
            <span>رصيد المحفظة الافتراضي</span>
            <input type="text" placeholder="مثال: 500 TON" class="w-full">
        </div>

        <span class="section-title">التنبيهات الذكية</span>
        <div class="setting-item">
            <span>تنبيهات الأسعار</span>
            <button onclick="tg.showAlert('سيتم إطلاق ميزة التنبيهات في التحديث القادم')" class="text-blue-500 font-bold">ضبط +</button>
        </div>
        <div class="setting-item">
            <span>تحركات 5% (ساعة)</span>
            <input type="checkbox" class="w-5 h-5">
        </div>

        <span class="section-title">التخصيص والمظهر</span>
        <div class="setting-item">
            <span>الوضع الليلي</span>
            <button onclick="toggleTheme()" class="bg-gray-800 px-3 py-1 rounded-lg text-xs">تبديل</button>
        </div>
        <div class="setting-item">
            <span>نوع الشارت</span>
            <select id="chart-type">
                <option value="1">شموع (Candles)</option>
                <option value="3">خطي (Area)</option>
            </select>
        </div>
        <div class="setting-item">
            <span>اللغة</span>
            <select>
                <option>العربية</option>
                <option>English</option>
            </select>
        </div>

        <span class="section-title">الربط والحساب</span>
        <div class="p-4 bg-white/5 rounded-2xl border border-white/5 mt-2 flex items-center gap-3">
            <img id="set-user-avatar" src="" class="w-10 h-10 rounded-full">
            <div>
                <p id="set-user-name" class="font-bold text-sm">مستخدم تليجرام</p>
                <p class="text-[10px] text-gray-500">الحساب متصل بنجاح</p>
            </div>
            <button onclick="tg.close()" class="mr-auto text-red-500 text-xs font-bold uppercase">خروج</button>
        </div>

        <span class="section-title">الدعم والمطور</span>
        <div class="setting-item" onclick="window.open('https://t.me/vipadrian')">
            <span>تواصل مع المطور</span>
            <span class="text-blue-400 font-bold">@vipadrian</span>
        </div>
        <div class="setting-item">
            <span>إصدار التطبيق</span>
            <span class="text-gray-500">v4.0 Pro</span>
        </div>
        <p class="text-[9px] text-center text-gray-600 mt-6 font-bold uppercase tracking-widest">© 2026 Adrian Pro • Veto Market</p>
    </div>

    <div class="nav-bar">
        <div class="flex flex-col items-center gap-1 text-gray-500" onclick="openSettings()">
            <svg width="24" height="24" fill="currentColor" viewBox="0 0 24 24"><path d="M19.14 12.94c.04-.3.06-.61.06-.94 0-.32-.02-.64-.07-.94l2.03-1.58c.18-.14.23-.41.12-.61l-1.92-3.32c-.12-.22-.37-.29-.59-.22l-2.39.96c-.5-.38-1.03-.7-1.62-.94l-.36-2.54c-.04-.24-.24-.41-.48-.41h-3.84c-.24 0-.43.17-.47.41l-.36 2.54c-.59.24-1.13.57-1.62.94l-2.39-.96c-.22-.08-.47 0-.59.22L2.74 8.87c-.12.21-.08.47.12.61l2.03 1.58c-.05.3-.09.63-.09.94s.02.64.07.94l-2.03 1.58c-.18.14-.23.41-.12.61l1.92 3.32c.12.22.37.29.59.22l2.39-.96c.5.38 1.03.7 1.62.94l.36 2.54c.05.24.24.41.48.41h3.84c.24 0 .44-.17.47-.41l.36-2.54c.59-.24 1.13-.56 1.62-.94l2.39.96c.22.08.47 0 .59-.22l1.92-3.32c.12-.22.07-.47-.12-.61l-2.01-1.58zM12 15.6c-1.98 0-3.6-1.62-3.6-3.6s1.62-3.6 3.6-3.6 3.6 1.62 3.6 3.6-1.62 3.6-3.6 3.6z"/></svg>
            <span class="text-[10px] font-bold">الإعدادات</span>
        </div>
        
        <div class="flex flex-col items-center -mt-12" onclick="closeAllSheets()">
            <div class="w-16 h-16 rounded-full border-4 border-[#050708] bg-[#1a1e23] flex items-center justify-center overflow-hidden shadow-2xl relative">
                <img src="https://via.placeholder.com/100?text=👤" id="user-avatar">
            </div>
            <span id="user-name" class="text-[10px] font-black text-white mt-2 uppercase tracking-tighter">Adrian</span>
        </div>

        <div class="flex flex-col items-center gap-1 text-blue-500" onclick="tg.showAlert('منصة التداول ستتوفر في النسخة القادمة')">
            <svg width="24" height="24" fill="currentColor" viewBox="0 0 24 24"><path d="M3.5 18.49l6-6.01 4 4L22 6.92l-1.41-1.41-7.09 7.97-4-4L2 16.99z"/></svg>
            <span class="text-[10px] font-bold">التداول</span>
        </div>
    </div>

    <script type="text/javascript" src="https://s3.tradingview.com/tv.js"></script>
    <script>
        const tg = window.Telegram.WebApp;
        tg.expand();

        let iqdRate = 1460;
        let selectedCurrency = "USD";
        let chartType = "1";

        // Initial Data Loading
        function initApp() {
            const user = tg.initDataUnsafe?.user;
            if (user) {
                const name = user.first_name + (user.last_name ? " " + user.last_name : "");
                document.getElementById('user-name').innerText = name;
                document.getElementById('set-user-name').innerText = name;
                if (user.photo_url) {
                    document.getElementById('user-avatar').src = user.photo_url;
                    document.getElementById('set-user-avatar').src = user.photo_url;
                }
            }
        }
        initApp();

        const symbols = ['TONUSDT', 'BTCUSDT', 'ETHUSDT', 'SOLUSDT', 'BNBUSDT', 'PAXGUSDT'];
        const coinNames = { 
            'TONUSDT': {name: 'Toncoin', icon: 'https://cryptologos.cc/logos/toncoin-ton-logo.png'},
            'BTCUSDT': {name: 'Bitcoin', icon: 'https://cryptologos.cc/logos/bitcoin-btc-logo.png'},
            'ETHUSDT': {name: 'Ethereum', icon: 'https://cryptologos.cc/logos/ethereum-eth-logo.png'},
            'SOLUSDT': {name: 'Solana', icon: 'https://cryptologos.cc/logos/solana-sol-logo.png'},
            'BNBUSDT': {name: 'BNB', icon: 'https://cryptologos.cc/logos/bnb-bnb-logo.png'},
            'PAXGUSDT': {name: 'Gold (Oz)', icon: 'https://cdn-icons-png.flaticon.com/512/2855/2855172.png'}
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
                                <img src="${asset.icon}" class="w-10 h-10 rounded-full shadow-lg">
                                <div>
                                    <p class="text-sm font-black">${asset.name}</p>
                                    <p class="text-[10px] text-gray-500 uppercase font-bold">${coin.symbol.replace('USDT','')}</p>
                                </div>
                            </div>
                            <div class="text-left">
                                <p class="text-md font-black font-mono tracking-tighter">${formatPrice(price)}</p>
                                <p class="text-[10px] font-black ${change >= 0 ? 'price-up' : 'price-down'}">${change >= 0 ? '+' : ''}${change.toFixed(2)}%</p>
                            </div>
                        </div>
                    `;
                });
            } catch (e) { console.error("Market error"); }
        }

        function formatPrice(price) {
            if(selectedCurrency === "IQD") return (price * iqdRate).toLocaleString() + " د.ع";
            if(selectedCurrency === "EUR") return (price * 0.92).toLocaleString() + " €";
            return price.toLocaleString() + " $";
        }

        function showDetails(symbol, price, change, high, low) {
            const asset = coinNames[symbol];
            document.getElementById('modal-icon').src = asset.icon;
            document.getElementById('modal-name').innerText = asset.name;
            document.getElementById('modal-symbol').innerText = symbol;
            document.getElementById('modal-price').innerText = parseFloat(price).toLocaleString() + "$";
            document.getElementById('modal-converted').innerText = (parseFloat(price) * iqdRate).toLocaleString() + " د.ع";
            document.getElementById('stat-high').innerText = parseFloat(high).toLocaleString() + "$";
            document.getElementById('stat-low').innerText = parseFloat(low).toLocaleString() + "$";

            document.getElementById('chart-box').innerHTML = `<div id="tv_chart" style="height:100%"></div>`;
            new TradingView.widget({
                "autosize": true, "symbol": `BINANCE:${symbol}`, "interval": "60",
                "timezone": "Etc/UTC", "theme": document.body.classList.contains('light-mode') ? "light" : "dark",
                "style": chartType, "locale": "ar", "hide_top_toolbar": true, "container_id": "tv_chart"
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

        function toggleTheme() {
            document.body.classList.toggle('light-mode');
        }

        // Settings Listeners
        document.getElementById('currency-select').addEventListener('change', (e) => {
            selectedCurrency = e.target.value;
            fetchMarket();
        });
        
        document.getElementById('iqd-rate-input').addEventListener('input', (e) => {
            iqdRate = e.target.value || 1;
            fetchMarket();
        });

        document.getElementById('chart-type').addEventListener('change', (e) => {
            chartType = e.target.value;
        });

        setInterval(fetchMarket, 5000);
        fetchMarket();
    </script>
</body>
</html>
