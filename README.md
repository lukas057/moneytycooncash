<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8">
  <title>Free Money Clicker</title>
  <style>
    body {
      font-family: sans-serif;
      background-color: #f0f0f0;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      margin: 0;
      position: relative;
    }

    #total {
      font-size: 24px;
      margin-bottom: 20px;
      font-weight: bold;
      color: #444;
    }

    #moneyButton {
      width: 150px;
      height: 150px;
      border-radius: 50%;
      background-color: white;
      border: 2px solid #000;
      font-size: 20px;
      cursor: pointer;
      box-shadow: 2px 2px 10px rgba(0,0,0,0.2);
      transition: transform 0.2s;
      position: relative;
    }

    #moneyButton:hover {
      transform: scale(1.05);
    }

    #result {
      margin-top: 30px;
      font-size: 30px;
      color: #2e7d32;
    }

    #shop {
      position: absolute;
      bottom: 20px;
      left: 20px;
      display: flex;
      flex-direction: column;
      gap: 10px;
    }

    #shop button {
      padding: 10px 20px;
      font-size: 16px;
      background-color: #ffffff;
      border: 2px solid #000;
      cursor: pointer;
      box-shadow: 1px 1px 5px rgba(0,0,0,0.2);
    }

    #upgradeStatus {
      font-size: 16px;
      color: #444;
    }
  </style>
</head>
<body>

  <div id="total">Gesammelt: 0,00 €</div>
  <button id="moneyButton">free money</button>
  <div id="result"></div>

  <div id="shop">
    <button id="upgradeButton">Upgrade kaufen</button>
    <div id="upgradeStatus">Upgrade-Stufe: 0 / 50</div>
    <button id="resetButton">Zurücksetzen</button>
  </div>

  <audio id="cashSound" src="cash.mp3" preload="auto"></audio>

  <script>
    const maxUpgrades = 50;
    const baseMin = 0.01;
    const baseMax = 1.00;
    const upgradeBaseCost = 10;
    const upgradeCostIncrease = 5;

    let totalAmount = parseFloat(localStorage.getItem("totalAmount")) || 0;
    let upgradeLevel = parseInt(localStorage.getItem("upgradeLevel")) || 0;

    const cashSound = document.getElementById("cashSound");
    const totalDisplay = document.getElementById("total");
    const resultDisplay = document.getElementById("result");
    const upgradeButton = document.getElementById("upgradeButton");
    const upgradeStatus = document.getElementById("upgradeStatus");
    const resetButton = document.getElementById("resetButton");

    function getUpgradeCost() {
      return upgradeBaseCost + upgradeLevel * upgradeCostIncrease;
    }

    function getRandomAmount() {
      const max = baseMax + upgradeLevel * 2;
      const min = baseMin + upgradeLevel * 0.1;
      const value = Math.random() * (max - min) + min;
      return parseFloat(value.toFixed(2));
    }

    function updateDisplay() {
      totalDisplay.innerText = `Gesammelt: ${totalAmount.toFixed(2)} €`;
      upgradeStatus.innerText = upgradeLevel < maxUpgrades
        ? `Upgrade-Stufe: ${upgradeLevel} / ${maxUpgrades}`
        : `Upgrade-Stufe: ${maxUpgrades} (MAX)`;

      if (upgradeLevel < maxUpgrades) {
        const cost = getUpgradeCost();
        upgradeButton.innerText = `Upgrade kaufen (Kosten: ${cost.toFixed(2)} €)`;
        upgradeButton.disabled = totalAmount < cost;
      } else {
        upgradeButton.innerText = `Upgrade (MAX)`;
        upgradeButton.disabled = true;
      }

      // Speichern
      localStorage.setItem("totalAmount", totalAmount.toFixed(2));
      localStorage.setItem("upgradeLevel", upgradeLevel.toString());
    }

    document.getElementById("moneyButton").addEventListener("click", () => {
      const amount = getRandomAmount();
      totalAmount += amount;

      resultDisplay.innerText = `+ ${amount.toFixed(2)} €`;
      updateDisplay();

      cashSound.currentTime = 0;
      cashSound.play();
    });

    upgradeButton.addEventListener("click", () => {
      const cost = getUpgradeCost();
      if (totalAmount >= cost && upgradeLevel < maxUpgrades) {
        totalAmount -= cost;
        upgradeLevel++;
        updateDisplay();
      }
    });

    resetButton.addEventListener("click", () => {
      if (confirm("Möchtest du deinen Fortschritt wirklich zurücksetzen?")) {
        totalAmount = 0;
        upgradeLevel = 0;
        localStorage.removeItem("totalAmount");
        localStorage.removeItem("upgradeLevel");
        updateDisplay();
      }
    });

    updateDisplay();
  </script>

</body>
</html>
