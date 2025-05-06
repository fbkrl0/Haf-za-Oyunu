<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>İklim Hafıza Oyunu</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Fredoka+One&display=swap');

    body {
      font-family: 'Fredoka One', cursive;
      background: linear-gradient(to right, #f9d423, #ff4e50);
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      margin: 0;
    }

    h1 {
      color: white;
      font-size: 48px;
      text-shadow: 2px 2px 4px #00000060;
      margin-bottom: 10px;
    }

    .scores {
      display: flex;
      justify-content: space-between;
      width: 300px;
      font-size: 20px;
      color: white;
      margin-bottom: 20px;
      text-shadow: 1px 1px 2px #00000060;
    }

    .game-board {
      display: grid;
      grid-template-columns: repeat(4, 100px);
      grid-gap: 10px;
    }

    .card {
      width: 100px;
      height: 100px;
      background-color: #ff6600;
      border-radius: 12px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 14px;
      color: black;
      cursor: pointer;
      position: relative;
      transition: transform 0.3s ease;
      box-shadow: 2px 2px 5px #00000040;
      transform-style: preserve-3d;
      perspective: 1000px;
    }

    .card.flipped {
      transform: rotateY(180deg);
    }

    .card-inner {
      position: absolute;
      width: 100%;
      height: 100%;
      display: flex;
      align-items: center;
      justify-content: center;
      backface-visibility: hidden;
      border-radius: 12px;
    }

    .card .front {
      background-color: white;
      transform: rotateY(180deg);
    }

    .card .back {
      background-color: #ff6600;
      color: transparent;
    }

    .card.matched {
      animation: fadeOut 0.5s forwards;
    }

    @keyframes fadeOut {
      to {
        opacity: 0;
        transform: scale(0.5);
      }
    }

    #restart {
      margin-top: 20px;
      padding: 10px 20px;
      font-size: 16px;
      border: none;
      border-radius: 5px;
      background-color: #333;
      color: white;
      cursor: pointer;
    }

    #winner {
      font-size: 24px;
      color: white;
      margin-top: 20px;
      text-shadow: 1px 1px 2px #00000060;
    }
  </style>
</head>
<body>
  <h1>İklim Hafıza Oyunu</h1>
  <div class="scores">
    <div>Oyuncu 1: <span id="score1">0</span></div>
    <div>Oyuncu 2: <span id="score2">0</span></div>
  </div>
  <div class="game-board" id="gameBoard"></div>
  <div id="winner"></div>
  <button id="restart" onclick="startGame()">Tekrar Oyna</button>

  <script>
    const pairs = [
      ['Akdeniz İklimi', 'Çalı Biyomu'],
      ['Karadeniz İklimi', 'Ilıman Yaprak Döken Orman Biyomu'],
      ['Karasal İklim', 'Step Biyomu'],
      ['Marmara İklimi', 'Ayçiçeği'],
      ['Manisa', 'Üzüm'],
      ['Konya', 'Ereğli Elması'],
      ['Ordu', 'Fındık'],
      ['Bursa', 'Şeftali']
    ];

    let cards = [];
    let flippedCards = [];
    let matchedCount = 0;
    let currentPlayer = 1;
    let score1 = 0;
    let score2 = 0;

    function shuffle(array) {
      return array.flat().sort(() => Math.random() - 0.5);
    }

    function startGame() {
      cards = shuffle(pairs);
      flippedCards = [];
      matchedCount = 0;
      score1 = 0;
      score2 = 0;
      currentPlayer = 1;
      document.getElementById('score1').textContent = score1;
      document.getElementById('score2').textContent = score2;
      document.getElementById('winner').textContent = '';
      const board = document.getElementById('gameBoard');
      board.innerHTML = '';
      cards.forEach((text, index) => {
        const card = document.createElement('div');
        card.className = 'card';
        card.dataset.text = text;
        card.innerHTML = `
          <div class="card-inner back"></div>
          <div class="card-inner front">${text}</div>
        `;
        card.addEventListener('click', () => flipCard(card));
        board.appendChild(card);
      });
    }

    function flipCard(card) {
      if (flippedCards.length === 2 || card.classList.contains('flipped') || card.classList.contains('matched')) return;
      card.classList.add('flipped');
      flippedCards.push(card);
      if (flippedCards.length === 2) {
        const [first, second] = flippedCards;
        if (isMatch(first.dataset.text, second.dataset.text)) {
          first.classList.add('matched');
          second.classList.add('matched');
          if (currentPlayer === 1) {
            score1++;
            document.getElementById('score1').textContent = score1;
          } else {
            score2++;
            document.getElementById('score2').textContent = score2;
          }
          matchedCount++;
          if (matchedCount === pairs.length) showWinner();
          flippedCards = [];
        } else {
          setTimeout(() => {
            first.classList.remove('flipped');
            second.classList.remove('flipped');
            flippedCards = [];
            currentPlayer = currentPlayer === 1 ? 2 : 1;
          }, 1000);
        }
      }
    }

    function isMatch(text1, text2) {
      return pairs.some(pair => pair.includes(text1) && pair.includes(text2) && text1 !== text2);
    }

    function showWinner() {
      let message = '';
      if (score1 > score2) message = '🎉 Oyuncu 1 Kazandı!';
      else if (score2 > score1) message = '🎉 Oyuncu 2 Kazandı!';
      else message = '🤝 Berabere!';
      document.getElementById('winner').textContent = message;
    }

    startGame();
  </script>
</body>
</html>
