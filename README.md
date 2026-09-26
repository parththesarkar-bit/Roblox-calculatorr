index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Roblox Multi-Game Trade Calculator</title>
    <style>
        body { 
            font-family: 'Segoe UI', Arial, sans-serif; 
            max-width: 480px; 
            margin: 20px auto; 
            padding: 25px; 
            background-color: #1a1c24; 
            color: #ffffff;
            border-radius: 12px; 
            box-shadow: 0 8px 24px rgba(0,0,0,0.4); 
            border: 1px solid #2d313f;
        }
        h2 { 
            text-align: center; 
            color: #ffb703; 
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 5px;
        }
        .last-updated {
            text-align: center;
            font-size: 12px;
            color: #707590;
            margin-bottom: 25px;
        }
        label { 
            font-weight: 600; 
            display: block; 
            margin-top: 18px; 
            color: #bfa3ff;
        }
        input, select, button { 
            width: 100%; 
            padding: 12px; 
            margin-top: 6px; 
            border-radius: 6px; 
            border: 1px solid #3d4357; 
            font-size: 16px; 
            background-color: #252836;
            color: #ffffff;
            outline: none;
            box-sizing: border-box;
        }
        input:focus, select:focus {
            border-color: #ffb703;
        }
        input::placeholder {
            color: #707590;
        }
        button { 
            background-color: #ffb703; 
            color: #1a1c24; 
            border: none; 
            font-weight: bold; 
            margin-top: 25px; 
            cursor: pointer; 
            transition: transform 0.1s;
        }
        button:active {
            transform: scale(0.98);
        }
        #result { 
            margin-top: 25px; 
            padding: 15px; 
            font-weight: bold; 
            text-align: center; 
            border-radius: 6px; 
            display: none; 
            font-size: 17px;
            line-height: 1.5;
        }
        .value-breakdown {
            font-size: 14px;
            margin-top: 8px;
            font-weight: normal;
            opacity: 0.9;
        }
    </style>
</head>
<body>

    <h2>Roblox Trade Calculator</h2>
    <!-- Change this text manually whenever you change numbers inside the script below -->
    <div class="last-updated">Values Database Updated: September 2026</div>

    <label for="gameSelect">1. Choose Roblox Game:</label>
    <select id="gameSelect" onchange="updateItems()">
        <option value="bloxfruits">Blox Fruits</option>
        <option value="adoptme">Adopt Me!</option>
    </select>

    <label for="searchBar">🔍 Filter Items by Name:</label>
    <input type="text" id="searchBar" placeholder="Type to filter fruits..." oninput="updateItems()">

    <label for="yourItem">2. Your Item:</label>
    <select id="yourItem"></select>

    <label for="theirItem">3. Their Item (What they offer):</label>
    <select id="theirItem"></select>

    <button onclick="calculateTrade()">Check Trade Fairness</button>

    <div id="result"></div>

    <script>
        // ==========================================
        // 🛠️ EASY UPDATE CORNER
        // Edit these numbers below every 1-2 months to keep your values fresh!
        // ==========================================
        const gameData = {
            bloxfruits: {
                "Perm Dragon (Mythical)": 7500,
                "Perm Kitsune (Mythical)": 7000,
                "Perm Leopard (Mythical)": 5500,
                "Perm Dough (Mythical)": 4800,
                "Perm T-Rex (Mythical)": 4200,
                "Perm Spirit (Mythical)": 4000,
                "Perm Venom (Mythical)": 3800,
                "Perm Control (Mythical)": 3500,
                "Perm Shadow (Mythical)": 3200,
                "Perm Mammoth (Mythical)": 3200,
                "Perm Gravity (Mythical)": 2500,
                "Perm Portal (Legendary)": 3500,
                "Perm Buddha (Legendary)": 3200,
                "Perm Blizzard (Legendary)": 2800,
                "Perm Rumble (Legendary)": 2800,
                "Perm Sound (Legendary)": 2400,
                "Perm Phoenix (Legendary)": 2200,
                "Perm Pain (Legendary)": 1800,
                "Perm Magma (Rare)": 1500,
                "Perm Light (Rare)": 1300,
                "Perm Ice (Uncommon)": 950,
                "Perm Dark (Uncommon)": 850,
                "Physical Dragon Fruit": 120,
                "Physical Kitsune Fruit": 115,
                "Physical Leopard Fruit": 45,
                "Physical Dough Fruit": 25,
                "Physical T-Rex Fruit": 20,
                "Physical Spirit Fruit": 10,
                "Physical Mammoth Fruit": 10,
                "Physical Venom Fruit": 9,
                "Physical Control Fruit": 8,
                "Physical Shadow Fruit": 6,
                "Physical Gravity Fruit": 2,
                "Physical Buddha Fruit": 7,
                "Physical Portal Fruit": 6,
                "Physical Rumble Fruit": 5,
                "Physical Blizzard Fruit": 5,
                "Physical Sound Fruit": 4,
                "Physical Phoenix Fruit": 3,
                "Physical Pain Fruit": 1,
                "Physical Magma Fruit": 2,
                "Physical Light Fruit": 1.5,
                "Physical Ghost Fruit": 1,
                "Physical Barrier Fruit": 0.5,
                "Physical Rubber Fruit": 0.5,
                "Physical Diamond Fruit": 0.2,
                "Physical Ice Fruit": 0.5,
                "Physical Dark Fruit": 0.2,
                "Physical Sand Fruit": 0.1,
                "Physical Falcon Fruit": 0.1,
                "Physical Flame Fruit": 0.1,
                "Physical Spike Fruit": 0.05,
                "Physical Smoke Fruit": 0.05,
                "Physical Spring Fruit": 0.05,
                "Physical Bomb Fruit": 0.02,
                "Physical Chop Fruit": 0.02,
                "Physical Spin Fruit": 0.01,
                "Physical Rocket Fruit": 0.01
            },
            adoptme: {
                "Shadow Dragon (FR)": 2200,
                "Bat Dragon (FR)": 2000,
                "Giraffe (FR)": 1400,
                "Frost Dragon (FR)": 1000,
                "Owl (FR)": 750,
                "Neon Unicorn (NFR)": 30,
                "Common Dog": 1
            }
        };

        function updateItems() {
            const selectedGame = document.getElementById("gameSelect").value;
            const filterText = document.getElementById("searchBar").value.toLowerCase();
            const allItems = Object.keys(gameData[selectedGame]);
            
            const filteredItems = allItems.filter(item => item.toLowerCase().includes(filterText));
            
            const yourItemDropdown = document.getElementById("yourItem");
            const theirItemDropdown = document.getElementById("theirItem");

            yourItemDropdown.innerHTML = "";
            theirItemDropdown.innerHTML = "";

            if(filteredItems.length === 0) {
                const optA = new Option("No items found", "");
                const optB = new Option("No items found", "");
                yourItemDropdown.add(optA);
                theirItemDropdown.add(optB);
                return;
            }

            filteredItems.forEach(item => {
                yourItemDropdown.options[yourItemDropdown.options.length] = new Option(item, item);
                theirItemDropdown.options[theirItemDropdown.options.length] = new Option(item, item);
            });
        }

        function calculateTrade() {
            const game = document.getElementById("gameSelect").value;
            const itemA = document.getElementById("yourItem").value;
            const itemB = document.getElementById("theirItem").value;

            if(!itemA || !itemB) return;

            const myValue = gameData[game][itemA];
            const theirValue = gameData[game][itemB];

            const resultDiv = document.getElementById("result");
            resultDiv.style.display = "block";

            // Calculating precise evaluation difference
            const difference = Math.abs(theirValue - myValue);

            if (theirValue > myValue) {
                resultDiv.innerHTML = `🎉 BIG WIN! You should take this trade.<br>
                <div class="value-breakdown">You are gaining <strong>+${difference}</strong> points in market value!</div>`;
                resultDiv.style.backgroundColor = "#1b4d22";
                resultDiv.style.color = "#4af26e";
            } else if (theirValue === myValue) {
                resultDiv.innerHTML = `🤝 FAIR TRADE. Equal value on both sides.<br>
                <div class="value-breakdown">Net Value Difference: <strong>0</strong> points.</div>`;
                resultDiv.style.backgroundColor = "#4d3d1b";
                resultDiv.style.color = "#ffd166";
            } else {
                resultDiv.innerHTML = `❌ LOSS! Reject this trade.<br>
                <div class="value-breakdown">Warning: You are losing <strong>-${difference}</strong> points in valuation!</div>`;
                resultDiv.style.backgroundColor = "#4d1b1b";
                resultDiv.style.color = "#ff6b6b";
            }
        }

        updateItems();
    </script>
</body>
</html>
