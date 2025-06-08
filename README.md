<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Quiz Game</title>
  <style>
    body {
      background-color: #f5f5dc;
      color: #333;
      font-family: 'Segoe UI', sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 20px;
    }
    .screen {
      display: none;
      width: 100%;
      max-width: 800px;
      border: 1px solid #ccc;
      border-radius: 15px;
      background-color: white;
      padding: 30px;
      box-shadow: 0 0 20px rgba(0,0,0,0.1);
    }
    .active {
      display: block;
    }
    button {
      padding: 10px 20px;
      margin: 10px 5px;
      border: none;
      border-radius: 5px;
      background-color: #dcdcdc;
      cursor: pointer;
      transition: background-color 0.3s;
    }
    button:hover {
      background-color: #b0b0b0;
    }
    .catalog button {
      width: 100%;
      margin: 10px 0;
      font-size: 1.1em;
    }
    .profile {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 10px;
    }
    .color-option {
      padding: 5px 10px;
      margin: 5px;
      cursor: pointer;
      border-radius: 5px;
      color: white;
      display: inline-block;
    }
    img {
      max-width: 100%;
      border-radius: 10px;
      margin: 10px 0;
    }
  </style>
</head>
<body>
  <div class="screen" id="loginScreen">
    <h2>Enter Your Name</h2>
    <input type="text" id="playerName" placeholder="Your name" />
    <button onclick="startGame()">Start Playing</button>
  </div>

  <div class="screen" id="catalogScreen">
    <div class="profile">
      <span id="displayName">Player</span> | Coins: <span id="coinCount">0</span>
      <button onclick="showCustomize()">Customize</button>
    </div>
    <h3>Game Catalog</h3>
    <div class="catalog" id="quizList"></div>
  </div>

  <div class="screen" id="quizScreen">
    <h3 id="quizTitle"></h3>
    <img id="quizImage" src="" alt="" style="display:none;">
    <p id="questionText"></p>
    <div id="options"></div>
  </div>

  <div class="screen" id="customizeScreen">
    <h3>Customize Name Color</h3>
    <div id="colorOptions"></div>
    <button onclick="goToCatalog()">Back</button>
  </div>

  <script>
    let playerName = "";
    let coins = 0;
    let currentQuiz = [];
    let currentQuestionIndex = 0;

    const nameColors = [
      { color: 'lightblue', cost: 10 },
      { color: 'blue', cost: 15 },
      { color: 'skyblue', cost: 20 },
      { color: 'limegreen', cost: 25 },
      { color: 'green', cost: 30 },
      { color: 'lightgreen', cost: 35 },
      { color: 'yellow', cost: 40 },
      { color: 'orange', cost: 45 },
      { color: 'orangered', cost: 50 },
      { color: 'red', cost: 60 },
      { color: 'deeppink', cost: 65 },
      { color: 'pink', cost: 70 },
      { color: 'purple', cost: 75 },
      { color: 'indigo', cost: 80 },
      { color: 'violet', cost: 85 }
    ];

    const quizzes = {
      math: [
        { q: '12 + 8 = ?', options: ['18', '20', '22'], answer: '20', image: '' },
        { q: '7 x 6 = ?', options: ['42', '36', '48'], answer: '42', image: '' }
      ],
      art: [
        { q: 'Who painted the Mona Lisa?', options: ['Van Gogh', 'Da Vinci', 'Picasso'], answer: 'Da Vinci', image: 'https://upload.wikimedia.org/wikipedia/commons/6/6a/Mona_Lisa.jpg' }
      ],
      music: [
        { q: 'Who is known as the King of Pop?', options: ['Elvis Presley', 'Michael Jackson', 'Prince'], answer: 'Michael Jackson', image: '' }
      ],
      introvert: [
        { q: 'Do you enjoy being in large groups?', options: ['Yes', 'Sometimes', 'No'], answer: 'No', image: '' },
        { q: 'Do you prefer deep conversations over small talk?', options: ['No', 'Sometimes', 'Yes'], answer: 'Yes', image: '' }
      ]
    };

    function switchScreen(id) {
      document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
      document.getElementById(id).classList.add('active');
    }

    function startGame() {
      playerName = document.getElementById('playerName').value || 'Player';
      document.getElementById('displayName').textContent = playerName;
      updateCoins();
      populateQuizCatalog();
      switchScreen('catalogScreen');
    }

    function updateCoins() {
      document.getElementById('coinCount').textContent = coins;
    }

    function populateQuizCatalog() {
      const quizList = document.getElementById('quizList');
      quizList.innerHTML = '';
      Object.keys(quizzes).forEach(type => {
        const btn = document.createElement('button');
        btn.textContent = type.charAt(0).toUpperCase() + type.slice(1) + ' Quiz';
        btn.onclick = () => startQuiz(type);
        quizList.appendChild(btn);
      });
    }

    function startQuiz(type) {
      currentQuiz = quizzes[type];
      currentQuestionIndex = 0;
      document.getElementById('quizTitle').textContent = type.charAt(0).toUpperCase() + type.slice(1) + ' Quiz';
      showQuestion();
      switchScreen('quizScreen');
    }

    function showQuestion() {
      const q = currentQuiz[currentQuestionIndex];
      document.getElementById('questionText').textContent = q.q;
      const quizImage = document.getElementById('quizImage');
      if (q.image) {
        quizImage.src = q.image;
        quizImage.style.display = 'block';
      } else {
        quizImage.style.display = 'none';
      }
      const optionsDiv = document.getElementById('options');
      optionsDiv.innerHTML = '';
      q.options.forEach(opt => {
        const btn = document.createElement('button');
        btn.textContent = opt;
        btn.onclick = () => checkAnswer(opt);
        optionsDiv.appendChild(btn);
      });
    }

    function checkAnswer(selected) {
      const correct = currentQuiz[currentQuestionIndex].answer;
      if (selected === correct) coins += 10;
      updateCoins();
      currentQuestionIndex++;
      if (currentQuestionIndex < currentQuiz.length) {
        showQuestion();
      } else {
        alert("Quiz complete! You've earned coins.");
        switchScreen('catalogScreen');
      }
    }

    function showCustomize() {
      const container = document.getElementById('colorOptions');
      container.innerHTML = '';
      nameColors.forEach(item => {
        const div = document.createElement('div');
        div.className = 'color-option';
        div.style.backgroundColor = item.color;
        div.textContent = `${item.color} - ${item.cost} coins`;
        div.onclick = () => buyColor(item);
        container.appendChild(div);
      });
      switchScreen('customizeScreen');
    }

    function buyColor(item) {
      if (coins >= item.cost) {
        coins -= item.cost;
        updateCoins();
        document.getElementById('displayName').style.color = item.color;
        alert(`Name color changed to ${item.color}`);
        goToCatalog();
      } else {
        alert('Not enough coins!');
      }
    }

    function goToCatalog() {
      switchScreen('catalogScreen');
    }

    switchScreen('loginScreen');
  </script>
</body>
</html>
