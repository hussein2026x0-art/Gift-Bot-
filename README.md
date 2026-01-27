<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mina Gift App - سوق الهدايا</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://unpkg.com/@tonconnect/ui@latest/dist/tonconnect-ui.min.js"></script>
    
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--tg-theme-bg-color, #ffffff);
            color: var(--tg-theme-text-color, #000000);
            margin: 0;
            padding: 15px;
        }
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }
        #ton-connect-button {
            float: left;
        }
        .gift-card {
            background-color: var(--tg-theme-secondary-bg-color, #f0f0f0);
            border-radius: 12px;
            padding: 15px;
            margin-bottom: 15px;
            border: 1px solid #ddd;
        }
        .gift-image {
            width: 100%;
            height: 150px;
            background: #ccc;
            border-radius: 8px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 50px;
        }
        .price-tag {
            color: #0088cc;
            font-weight: bold;
            font-size: 1.2em;
        }
        .buy-btn {
            background-color: var(--tg-theme-button-color, #248bcf);
            color: var(--tg-theme-button-text-color, #ffffff);
            border: none;
            padding: 10px 20px;
            border-radius: 8px;
            width: 100%;
            cursor: pointer;
            font-weight: bold;
            margin-top: 10px;
        }
        .tabs {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
        }
        .tab {
            flex: 1;
            text-align: center;
            padding: 8px;
            background: #eee;
            border-radius: 20px;
            cursor: pointer;
            font-size: 14px;
        }
        .tab.active {
            background: #0088cc;
            color: white;
        }
    </style>
</head>
<body>

    <div class="header">
        <h3>Mina Market 🎁</h3>
        <div id="ton-connect-button"></div>
    </div>

    <div class="tabs">
        <div class="tab active" onclick="showSection('market')">السوق العام</div>
        <div class="tab" onclick="showSection('my-gifts')">هداياي</div>
    </div>

    <div id="market-section">
        <div class="gift-card">
            <div class="gift-image">💎</div>
            <h4>هدية رقمية مطورة #102</h4>
            <p>السعر: <span class="price-tag">5 TON</span></p>
            <button class="buy-btn" onclick="buyGift(5)">شراء الآن</button>
        </div>
    </div>

    <div id="my-gifts-section" style="display:none;">
        <p style="text-align:center;">لا توجد هدايا في مخزنك حالياً.</p>
    </div>

    <script>
        // تهيئة تطبيق تلجرام
        const tg = window.Telegram.WebApp;
        tg.expand(); // توسيع التطبيق ليأخذ الشاشة كاملة

        // تهيئة ربط المحفظة
        const tonConnectUI = new TON_CONNECT_UI.TonConnectUI({
            manifestUrl: 'https://your-domain.com/manifest.json', // تأكد من وضع رابط المانيفست الصحيح
            buttonRootId: 'ton-connect-button'
        });

        // التنقل بين الأقسام
        function showSection(section) {
            document.getElementById('market-section').style.display = section === 'market' ? 'block' : 'none';
            document.getElementById('my-gifts-section').style.display = section === 'my-gifts' ? 'block' : 'none';
            
            // تغيير حالة الأزرار (Tabs)
            const tabs = document.querySelectorAll('.tab');
            tabs[0].classList.toggle('active', section === 'market');
            tabs[1].classList.toggle('active', section === 'my-gifts');
        }

        // دالة الشراء (تحتاج ربط مع الباك اند لاحقاً)
        async function buyGift(amount) {
            const wallet = tonConnectUI.account?.address;
            if (!wallet) {
                tg.showAlert("يرجى ربط محفظة TON أولاً!");
                return;
            }

            const transaction = {
                validUntil: Math.floor(Date.now() / 1000) + 60,
                messages: [
                    {
                        address: "UQC.........................", // محفظة البوت لاستلام المبلغ والعمولة
                        amount: (amount * 1000000000).toString(), // القيمة بالنانو تون
                    }
                ]
            };

            try {
                await tonConnectUI.sendTransaction(transaction);
                tg.showConfirm("تم إرسال الطلب، سيتم نقل الهدية بعد تأكيد الشبكة.");
            } catch (e) {
                console.error(e);
            }
        }
    </script>
</body>
</html>
