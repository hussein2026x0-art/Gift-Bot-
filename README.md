<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Adrian Crypto Pro</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;900&display=swap" rel="stylesheet">
    <style>
        :root { --bg: #050708; --card: #12151a; --accent: #0088cc; }
        body { font-family: 'Cairo', sans-serif; background: var(--bg); color: #fff; overflow-x: hidden; padding-bottom: 100px; }
        .glass-card { background: var(--card); border: 1px solid #1f2328; border-radius: 20px; transition: 0.3s; }
        .coin-row { padding: 16px; border-bottom: 1px solid #1a1e23; cursor: pointer; display: flex; align-items: center; justify-content: space-between; }
        .coin-row:last-child { border-bottom: none; }
        .coin-row:active { background: rgba(255,255,255,0.05); }
        .detail-sheet { position: fixed; bottom: -100%; left: 0; right: 0; height: 85vh; background: #0b0e11; border-top: 1px solid #1f2328; z-index: 1000; transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1); border-radius: 30px 30px 0 0; padding: 20px; overflow-y: auto; }
        .detail-sheet.active { bottom: 0; }
        .overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.7); display: none; z-index: 999; }
        .nav-bar { position: fixed; bottom: 0; left: 0; right: 0; background: rgba(18, 21, 26, 0.95); backdrop-filter: blur(10px); border-top: 1px solid #1f2328; height: 85px; display: flex; align-items: center; justify-content: space-around; z-index: 500; }
        .price-up { color: #02c076; } .price-down { color: #f84960; }
        #user-avatar { object-fit: cover; width: 100%; height: 100%; }
    </style>
</head>
<body>

    <header class="p-6">
        <h1 class="text-2xl font-black italic uppercase">ADRIAN <span class="text-blue-500">PRO</span></h1>
        <p class="text-[10px] text-gray-500 font-bold tracking-widest mt-1 uppercase">Official Market Engine v4.0</p>
    </header>

    <section class="mx-4 glass-card overflow-hidden shadow-2xl">
        <div id="market-list">
            <div class="p-10 text-center animate-pulse text-gray-600">جاري الاتصال بالسوق...</div>
        </div>
    </section>

    <div class="overlay" id="overlay" onclick="closeDetails()"></div>
    <div class="detail-sheet" id="detail-sheet">
        <div class="w-12 h-1 bg-gray-700 rounded-full mx-auto mb-6"></div>
        <div class="flex justify-between items-start mb-6">
            <div>
                <div class="flex items-center gap-3">
                    <img id="modal-icon" src="" class="w-10 h-10">
                    <h2 id="modal-name" class="text-2xl font-black">---</h2>
                </div>
                <p id="modal-symbol" class="text-gray-500 text-xs mt-1 uppercase">---</p>
            </div>
            <div class="text-left">
                <p id="modal-price" class="text-2xl font-black font-mono">0.00$</p>
                <p id="modal-iqd" class="text-xs font-bold text-yellow-500">0 د.ع</p>
            </div>
        </div>
        <div id="chart-box" class="w-full h-64 glass-card overflow-hidden mb-6"></div>
        <div class="grid grid-cols-2 gap-3">
            <div class="bg-white/5 p-4 rounded-2xl border border-white/5 text-center">
                <span class="text-[10px] text-gray-500 block mb-1 uppercase font-bold">24h High</span>
                <span id="stat-high" class="text-green-500 font-black font-mono">0.00$</span>
            </div>
            <div class="bg-white/5 p-4 rounded-2xl border border-white/5 text-center">
                <span class="text-[10px] text-gray-500 block mb-1 uppercase font-bold">24h Low</span>
                <span id="stat-low" class="text-red-500 font-black font-mono">0.00$</span>
            </div>
        </div>
    </div>

    <div class="nav-bar">
        <div class="flex flex-col items-center gap-1 text-gray-500" onclick="alert('قريباً')">
            <svg width="22" height="22" fill="currentColor" viewBox="0 0 24 24"><path d="M19.14 12.94c.04-.3.06-.61.06-.94 0-.32-.02-.64-.07-.94l2.03-1.58c.18-.14.23-.41.12-.61l-1.92-3.32c-.12-.22-.37-.29-.59-.22l-2.39.96c-.5-.38-1.03-.7-1.62-.94l-.36-2.54c-.04-.24-.24-.41-.48-.41h-3.84c-.24 0-.43.17-.47.41l-.36 2.54c-.59.24-1.13.57-1.62.94l-2.39-.96c-.22-.08-.47 0-.59.22L2.74 8.87c-.12.21-.08.47.12.61l2.03 1.58c-.05.3-.09.63-.09.94s.02.64.07.94l-2.03 1.58c-.18.14-.23.41-.12.61l1.92 3.32c.12.22.37.29.59.22l2.39-.96c.5.38 1.03.7 1.62.94l.36 2.54c.05.24.24.41.48.41h3.84c.24 0 .44-.17.47-.41l.36-2.54c.59-.24 1.13-.56 1.62-.94l2.39.96c.22.08.47 0 .59-.22l1.92-3.32c.12-.22.07-.47-.12-.61l-2.01-1.58zM12 15.6c-1.98 0-3.6-1.62-3.6-3.6s1.62-3.6 3.6-3.6 3.6 1.62 3.6 3.6-1.62 3.6-3.6 3.6z"/></svg>
            <span class="text-[9px] font-bold">الإعدادات</span>
        </div>
        
        <div class="flex flex-col items-center -mt-10">
            <div class="w-16 h-16 rounded-full border-4 border-[#050708] bg-[#1a1e23] flex items-center justify-center overflow-hidden shadow-xl">
                <img src="https://via.placeholder.com/100?text=👤" id="user-avatar" alt="User Avatar">
            </div>
            <span id="user-name" class="text-[10px] font-black text-white mt-1 uppercase tracking-wider">جاري التحميل...</span>
        </div>

        <div class="flex flex-col items-center gap-1 text-blue-500">
            <svg width="22" height="22" fill="currentColor" viewBox="0 0 24 24"><path d="M3.5 18.49l6-6.01 4 4L22 6.92l-1.41-1.41-7.09 7.97-4-4L2 16.99z"/></svg>
            <span class="text-[9px] font-bold">التداول</span>
        </div>
    </div>

    <script type="text/javascript" src="https://s3.tradingview.com/tv.js"></script>
    <script>
        // 1. تفعيل بيانات تليجرام الحقيقية
        const tg = window.Telegram.WebApp;
        tg.expand(); // فتح التطبيق بالكامل

        function loadUserData() {
            const user = tg.initDataUnsafe?.user;
            const userNameEl = document.getElementById('user-name');
            const userAvatarEl = document.getElementById('user-avatar');

            if (user) {
                // عرض اسم المستخدم الفعلي
                userNameEl.innerText = user.first_name + (user.last_name ? " " + user.last_name : "");
                // عرض الصورة الشخصية إذا كانت متوفرة
                if (user.photo_url) {
                    userAvatarEl.src = user.photo_url;
                }
            } else {
                userNameEl.innerText = "ADRIAN"; // اسم افتراضي في حال التشغيل خارج تليجرام
            }
        }
        loadUserData();

        // 2. إدارة بيانات السوق
        const symbols = ['TONUSDT', 'BTCUSDT', 'ETHUSDT', 'SOLUSDT', 'BNBUSDT', 'PAXGUSDT'];
        const coinNames = { 
            'TONUSDT': {name: 'Toncoin', icon: 'https://cryptologos.cc/logos/toncoin-ton-logo.png'},
            'BTCUSDT': {name: 'Bitcoin', icon: 'https://cryptologos.cc/logos/bitcoin-btc-logo.png'},
            'ETHUSDT': {name: 'Ethereum', icon: 'https://cryptologos.cc/logos/ethereum-eth-logo.png'},
            'SOLUSDT': {name: 'Solana', icon: 'https://cryptologos.cc/logos/solana-sol-logo.png'},
            'BNBUSDT': {name: 'BNB', icon: 'https://cryptologos.cc/logos/bnb-bnb-logo.png'},
            'PAXGUSDT': {name: 'Gold (Oz)', icon: 'https://cdn-icons-png.flaticon.com/512/2855/2855172.png'}
        };
        
        let iqdRate = 1460;

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
                            <div class="flex items-center gap-3">
                                <img src="${asset.icon}" class="w-8 h-8 rounded-full">
                                <div>
                                    <p class="text-sm font-black">${asset.name}</p>
                                    <p class="text-[9px] text-gray-500 uppercase">${coin.symbol.replace('USDT','')}</p>
                                </div>
                            </div>
                            <div class="text-left">
                                <p class="text-sm font-black font-mono">${price.toLocaleString()}$</p>
                                <p class="text-[10px] font-bold ${change >= 0 ? 'price-up' : 'price-down'}">${change >= 0 ? '+' : ''}${change.toFixed(2)}%</p>
                            </div>
                        </div>
                    `;
                });
            } catch (e) { console.error("Error fetching market data"); }
        }

        function showDetails(symbol, price, change, high, low) {
            const asset = coinNames[symbol];
            document.getElementById('modal-icon').src = asset.icon;
            document.getElementById('modal-name').innerText = asset.name;
            document.getElementById('modal-symbol').innerText = symbol;
            document.getElementById('modal-price').innerText = parseFloat(price).toLocaleString() + "$";
            document.getElementById('modal-iqd').innerText = (parseFloat(price) * iqdRate).toLocaleString() + " د.ع";
            document.getElementById('stat-high').innerText = parseFloat(high).toLocaleString() + "$";
            document.getElementById('stat-low').innerText = parseFloat(low).toLocaleString() + "$";

            document.getElementById('chart-box').innerHTML = `<div id="tv_chart" style="height:100%"></div>`;
            new TradingView.widget({
                "autosize": true, "symbol": `BINANCE:${symbol}`, "interval": "60",
                "timezone": "Etc/UTC", "theme": "dark", "style": "1", "locale": "ar",
                "hide_top_toolbar": true, "container_id": "tv_chart", "backgroundColor": "#12151a"
            });

            document.getElementById('overlay').style.display = 'block';
            document.getElementById('detail-sheet').classList.add('active');
        }

        function closeDetails() {
            document.getElementById('detail-sheet').classList.remove('active');
            document.getElementById('overlay').style.display = 'none';
        }

        setInterval(fetchMarket, 5000);
        fetchMarket();
    </script>
</body>
</html>
