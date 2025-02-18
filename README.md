<!DOCTYPE html>
<html lang="he">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>חישוב גודל לוט</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            direction: rtl;
            background-color: #f0f0f0;
            padding: 20px;
            text-align: center;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
            background-color: #fff;
            padding: 30px;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        select, input, button {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            font-size: 16px;
            border-radius: 5px;
            border: 1px solid #ccc;
        }
        .result {
            background-color: #e8f7ff;
            padding: 20px;
            border-radius: 8px;
            margin-top: 20px;
            font-size: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>חישוב גודל לוט</h1>
        <h2>חישוב פורקס וקריפטו</h2>

        <label for="assetType">בחר סוג נכס:</label>
        <select id="assetType" onchange="toggleInputFields()">
            <option value="forex">פורקס</option>
            <option value="crypto">קריפטו</option>
        </select>

        <div id="forexInputs">
            <label for="forexPair">בחר נכס פורקס:</label>
            <select id="forexPair">
                <option value="USDJPY">USDJPY</option>
                <option value="GBPJPY">GBPJPY</option>
                <option value="EURUSD">EURUSD</option>
                <option value="GBPUSD">GBPUSD</option>
                <option value="AUDUSD">AUDUSD</option>
                <option value="NZDUSD">NZDUSD</option>
                <option value="US100">US100</option>
                <option value="XAUUSD">XAUUSD</option>
            </select>

            <label for="entryForex">מחיר כניסה:</label>
            <input type="number" id="entryForex" placeholder="הכנס מחיר כניסה" required>

            <label for="stopForex">סטופ לוס:</label>
            <input type="number" id="stopForex" placeholder="הכנס מחיר יציאה (סטופ לוס)" required>

            <label for="riskForex">סיכון ($):</label>
            <input type="number" id="riskForex" placeholder="כמה דולר אתה רוצה לסכן" required>

            <button onclick="calculateLotSize()">חשב גודל לוט פורקס</button>
        </div>

        <div id="cryptoInputs" style="display: none;">
            <label for="entryCrypto">מחיר כניסה (בשקלים):</label>
            <input type="number" id="entryCrypto" placeholder="הכנס מחיר כניסה בשקלים" required>

            <label for="stopCrypto">סטופ לוס (בשקלים):</label>
            <input type="number" id="stopCrypto" placeholder="הכנס מחיר יציאה (סטופ לוס) בשקלים" required>

            <label for="riskCrypto">סיכון ($):</label>
            <input type="number" id="riskCrypto" placeholder="כמה דולר אתה רוצה לסכן" required>

            <button onclick="calculateLotSize()">חשב גודל לוט קריפטו</button>
        </div>

        <div class="result" id="result" style="display: none;">
            <p><strong>תוצאה:</strong></p>
            <p id="lotResult"></p>
        </div>
    </div>

    <script>
        const forexData = {
            "EURUSD": { "pip_value": 10, "pip_divisor": 0.0001, "exchange_rate": 1 },
            "GBPUSD": { "pip_value": 10, "pip_divisor": 0.0001, "exchange_rate": 1 },
            "AUDUSD": { "pip_value": 10, "pip_divisor": 0.0001, "exchange_rate": 1 },
            "NZDUSD": { "pip_value": 10, "pip_divisor": 0.0001, "exchange_rate": 1 },
            "USDJPY": { "pip_value": 1000, "pip_divisor": 0.01, "exchange_rate": 110 },
            "GBPJPY": { "pip_value": 1000, "pip_divisor": 0.01, "exchange_rate": 150 },
            "US100": { "pip_value": 1, "pip_divisor": 1, "exchange_rate": 1 },
            "XAUUSD": { "pip_value": 10, "pip_divisor": 0.01, "exchange_rate": 1 }
        };

        const cryptoData = {
            "BTCUSDT": { "pip_value": 0.01, "pip_divisor": 0.01, "exchange_rate": 1 },
            "ETHUSDT": { "pip_value": 0.01, "pip_divisor": 0.01, "exchange_rate": 1 },
            "BNBUSDT": { "pip_value": 0.01, "pip_divisor": 0.01, "exchange_rate": 1 },
            "SOLUSDT": { "pip_value": 0.01, "pip_divisor": 0.01, "exchange_rate": 1 },
            "ADAUSDT": { "pip_value": 0.01, "pip_divisor": 0.01, "exchange_rate": 1 }
        };

        function toggleInputFields() {
            const assetType = document.getElementById("assetType").value;
            if (assetType === "forex") {
                document.getElementById("forexInputs").style.display = "block";
                document.getElementById("cryptoInputs").style.display = "none";
            } else {
                document.getElementById("forexInputs").style.display = "none";
                document.getElementById("cryptoInputs").style.display = "block";
            }
        }

        function calculateLotSize() {
            const assetType = document.getElementById("assetType").value;

            let entry, stop, risk, settings;

            if (assetType === "forex") {
                const pair = document.getElementById("forexPair").value;
                entry = parseFloat(document.getElementById("entryForex").value);
                stop = parseFloat(document.getElementById("stopForex").value);
                risk = parseFloat(document.getElementById("riskForex").value);
                settings = forexData[pair];
            } else {
                entry = parseFloat(document.getElementById("entryCrypto").value);
                stop = parseFloat(document.getElementById("stopCrypto").value);
                risk = parseFloat(document.getElementById("riskCrypto").value);
                settings = cryptoData["BTCUSDT"];  // כל הקריפטו אותו חישוב
            }

            if (!entry || !stop || !risk) {
                alert("אנא מלא את כל השדות בצורה תקינה.");
                return;
            }

            let distance = Math.abs(entry - stop);
            const pips = distance / settings.pip_divisor;
            const lotSize = (risk * settings.exchange_rate) / (pips * settings.pip_value);

            document.getElementById("lotResult").textContent = `נכס: ${assetType === 'forex' ? document.getElementById("forexPair").value : 'קריפטו'} | מחיר כניסה: ${entry} | סטופ לוס: ${stop} | סיכון: ${risk}$ | גודל הלוט המומלץ: ${lotSize.toFixed(2)} לוט`;
            document.getElementById("result").style.display = "block";
        }
    </script>
</body>
</html>
