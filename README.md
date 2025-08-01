<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Jogo da Memória</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>Jogo da Memória</h1>
  <div class="game-board" id="board"></div>
  <div class="status" id="status"></div>
  <button onclick="reiniciarJogo()">Reiniciar</button>

  <audio id="flipSound" src="https://cdn.pixabay.com/audio/2021/08/04/audio_1f4b8f624c.mp3"></audio>
  <audio id="matchSound" src="https://cdn.pixabay.com/audio/2022/03/15/audio_c0b7f8aa5b.mp3"></audio>

  <script src="script.js"></script>
</body>
</html>
* {
  box-sizing: border-box;
  font-family: 'Segoe UI', sans-serif;
}
body {
  background-color: #121212;
  color: #f0f0f0;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 2rem;
}
h1 {
  margin-bottom: 1rem;
  color: #00e5ff;
}
.game-board {
  display: grid;
  grid-template-columns: repeat(4, 100px);
  gap: 15px;
  justify-content: center;
}
.card {
  width: 100px;
  height: 100px;
  background-color: #1e1e1e;
  border-radius: 12px;
  cursor: pointer;
  position: relative;
  transition: transform 0.3s;
  transform-style: preserve-3d;
}
.card.flip {
  transform: rotateY(180deg);
}
.card .front,
.card .back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2rem;
}
.card .front {
  background-color: #00e5ff;
  color: #121212;
  transform: rotateY(180deg);
}
.card .back {
  background-color: #2c2c2c;
}
.status {
  margin-top: 2rem;
  font-size: 1.1rem;
  color: #ccc;
}
button {
  margin-top: 1.5rem;
  padding: 0.5rem 1rem;
  background: #00e5ff;
  border: none;
  color: #121212;
  border-radius: 8px;
  font-weight: bold;
  cursor: pointer;
}
button:hover {
  background: #00bcd4;
}
const emojis = ['🐱', '🎮', '🍕', '🚀', '🎧', '📚', '🌈', '🧠'];
let cards = [];
let flippedCards = [];
let lock = false;
let matches = 0;

const board = document.getElementById('board');
const status = document.getElementById('status');
const flipSound = document.getElementById('flipSound');
const matchSound = document.getElementById('matchSound');

function embaralhar(array) {
  return array.concat(array).sort(() => Math.random() - 0.5);
}

function criarTabuleiro() {
  cards = embaralhar(emojis);
  board.innerHTML = '';
  cards.forEach((emoji) => {
    const card = document.createElement('div');
    card.classList.add('card');
    card.dataset.emoji = emoji;
    const front = document.createElement('div');
    front.classList.add('front');
    front.textContent = emoji;
    const back = document.createElement('div');
    back.classList.add('back');
    back.textContent = '?';
    card.appendChild(front);
    card.appendChild(back);
    card.addEventListener('click', () => virarCarta(card));
    board.appendChild(card);
  });
  matches = 0;
  status.textContent = '';
}
function virarCarta(card) {
  if (lock || card.classList.contains('flip')) return;

  card.classList.add('flip');
  flipSound.play();
  flippedCards.push(card);

  if (flippedCards.length === 2) {
    lock = true;
    const [card1, card2] = flippedCards;
    if (card1.dataset.emoji === card2.dataset.emoji) {
      matchSound.play();
      matches++;
      flippedCards = [];
      lock = false;
      if (matches === emojis.length) {
        status.textContent = 'Parabéns! Você venceu! 🏆';
      }
    } else {
      setTimeout(() => {
        card1.classList.remove('flip');
        card2.classList.remove('flip');
        flippedCards = [];
        lock = false;
      }, 1000);
    }
  }
}
function reiniciarJogo() {
  criarTabuleiro();
}
criarTabuleiro();
