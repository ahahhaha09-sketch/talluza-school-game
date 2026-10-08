<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>مهمة إنقاذ الطائر - مدرسة طلوزة</title>
  <link rel="manifest" href="manifest.json">
  <style>
    :root {
      --primary: #4CAF50;
      --primary-dark: #388E3C;
      --accent: #FFC107;
      --danger: #FF5252;
      --sky: #E0F7FA;
      --card-bg: #FFFFFF;
      --text: #263238;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      user-select: none;
    }

    body {
      background: linear-gradient(180deg, #81D4FA 0%, #E8F5E9 100%);
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 15px;
      color: var(--text);
    }

    .game-card {
      background: var(--card-bg);
      width: 100%;
      max-width: 500px;
      border-radius: 24px;
      box-shadow: 0 12px 32px rgba(0,0,0,0.15);
      padding: 24px;
      text-align: center;
      position: relative;
      overflow: hidden;
      border: 4px solid #FFF;
    }

    /* Header & Progress */
    .header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 20px;
      background: #F1F8E9;
      padding: 10px 18px;
      border-radius: 50px;
    }

    .badge {
      font-size: 1.1rem;
      font-weight: bold;
      color: var(--primary-dark);
      display: flex;
      align-items: center;
      gap: 6px;
    }

    .progress-bar-bg {
      width: 100%;
      height: 14px;
      background: #E0E0E0;
      border-radius: 10px;
      margin: 15px 0;
      overflow: hidden;
    }

    .progress-bar-fill {
      height: 100%;
      width: 0%;
      background: linear-gradient(90deg, #FFB74D, #4CAF50);
      transition: width 0.4s ease;
      border-radius: 10px;
    }

    /* Stage Area & Animated Bird */
    .stage-area {
      position: relative;
      height: 180px;
      background: linear-gradient(180deg, #B2EBF2 0%, #E8F8F5 100%);
      border-radius: 18px;
      margin-bottom: 20px;
      display: flex;
      align-items: center;
      justify-content: center;
      overflow: hidden;
    }

    .cage-overlay {
      position: absolute;
      width: 100px;
      height: 120px;
      border: 5px solid #78909C;
      border-bottom: none;
      border-radius: 50px 50px 0 0;
      transition: all 0.6s ease;
    }

    .cage-bars {
      position: absolute;
      width: 100%;
      height: 100%;
      display: flex;
      justify-content: space-around;
    }

    .bar {
      width: 4px;
      background: #78909C;
      height: 100%;
    }

    .bird-svg {
      width: 90px;
      height: 90px;
      z-index: 2;
      transition: transform 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    }

    .bird-happy {
      animation: bounce 0.8s infinite alternate;
    }

    @keyframes bounce {
      from { transform: translateY(0); }
      to { transform: translateY(-15px) rotate(5deg); }
    }

    /* Questions & Options */
    .question-title {
      font-size: 1.35rem;
      font-weight: 700;
      color: #1B5E20;
      margin-bottom: 20px;
      line-height: 1.4;
    }

    .options-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 12px;
    }

    .btn-option {
      background: #F5F5F5;
      border: 3px solid #E0E0E0;
      border-radius: 16px;
      padding: 14px 10px;
      font-size: 1.1rem;
      font-weight: 700;
      color: #37474F;
      cursor: pointer;
      transition: all 0.2s ease;
      outline: none;
    }

    .btn-option:active {
      transform: scale(0.96);
    }

    .btn-option.correct {
      background: #E8F5E9;
      border-color: #4CAF50;
      color: #2E7D32;
    }

    .btn-option.wrong {
      background: #FFEBEE;
      border-color: #FF5252;
      color: #C62828;
    }

    /* Screen Overlay / Win Screen */
    .win-screen {
      display: none;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 20px;
    }

    .win-title {
      font-size: 2rem;
      color: #2E7D32;
      margin: 15px 0 10px 0;
    }

    .star-rating {
      font-size: 2.5rem;
      color: #FFC107;
      margin-bottom: 15px;
    }

    .btn-reset {
      background: var(--primary);
      color: white;
      border: none;
      padding: 14px 32px;
      font-size: 1.2rem;
      font-weight: bold;
      border-radius: 50px;
      cursor: pointer;
      box-shadow: 0 6px 16px rgba(76, 175, 80, 0.4);
      margin-top: 15px;
    }

    /* Confetti Particles */
    .confetti {
      position: absolute;
      width: 10px;
      height: 10px;
      border-radius: 3px;
      animation: fall 2s linear infinite;
    }

    @keyframes fall {
      to { transform: translateY(350px) rotate(360deg); opacity: 0; }
    }
  </style>
</head>
<body>

  <div class="game-card" id="gameCard">
    <!-- Header Info -->
    <div class="header">
      <div class="badge">🏆 النقاط: <span id="scoreText">0</span></div>
      <div class="badge">🐥 مرحلة: <span id="levelText">1</span>/5</div>
    </div>

    <!-- Progress Bar -->
    <div class="progress-bar-bg">
      <div class="progress-bar-fill" id="progressBar"></div>
    </div>

    <!-- Main Game Play Area -->
    <div id="playArea">
      <!-- Stage Visual (Bird & Cage) -->
      <div class="stage-area">
        <div class="cage-overlay" id="cage">
          <div class="cage-bars">
            <div class="bar"></div>
            <div class="bar"></div>
            <div class="bar"></div>
          </div>
        </div>
        <!-- SVG Bird -->
        <svg class="bird-svg" id="bird" viewBox="0 0 100 100">
          <circle cx="50" cy="50" r="35" fill="#FFCA28"/>
          <circle cx="65" cy="42" r="6" fill="#000"/>
          <polygon points="75,48 92,52 75,58" fill="#FF5722"/>
          <path d="M 30,55 Q 10,40 25,30 Q 35,45 30,55 Z" fill="#FFA000"/>
          <path d="M 40,65 Q 50,80 60,65" fill="none" stroke="#FF5722" stroke-width="4" stroke-linecap="round"/>
        </svg>
      </div>

      <!-- Question Text -->
      <div class="question-title" id="questionText">جاري تحميل السؤال...</div>

      <!-- Options -->
      <div class="options-grid" id="optionsGrid"></div>
    </div>

    <!-- Victory Screen -->
    <div class="win-screen" id="winScreen">
      <svg class="bird-svg bird-happy" viewBox="0 0 100 100" style="width:120px; height:120px;">
        <circle cx="50" cy="50" r="35" fill="#FFCA28"/>
        <circle cx="65" cy="42" r="6" fill="#000"/>
        <polygon points="75,48 92,52 75,58" fill="#FF5722"/>
        <path d="M 30,55 Q 10,40 25,30 Q 35,45 30,55 Z" fill="#FFA000"/>
      </svg>
      <h2 class="win-title">بطل إنقاذ الطائر! 🎉</h2>
      <p style="font-size:1.1rem; color:#555;">أحسنت يا بطل مدرسة طلوزة، لقد حررت الطائر بنجاح!</p>
      <div class="star-rating">⭐⭐⭐</div>
      <button class="btn-reset" onclick="restartGame()">إعادة اللعب 🔄</button>
    </div>
  </div>

  <script>
    // Audio Synthesizer (Zero External Audio Files - Works 100% Offline)
    const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    
    function playSound(type) {
      if (audioCtx.state === 'suspended') {
        audioCtx.resume();
      }
      const osc = audioCtx.createOscillator();
      const gain = audioCtx.createGain();
      osc.connect(gain);
      gain.connect(audioCtx.destination);

      if (type === 'correct') {
        osc.frequency.setValueAtTime(523.25, audioCtx.currentTime); // C5
        osc.frequency.exponentialRampToValueAtTime(880, audioCtx.currentTime + 0.3); // A5
        gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.3);
        osc.start();
        osc.stop(audioCtx.currentTime + 0.3);
      } else if (type === 'wrong') {
        osc.type = 'sawtooth';
        osc.frequency.setValueAtTime(220, audioCtx.currentTime); // A3
        osc.frequency.setValueAtTime(140, audioCtx.currentTime + 0.15);
        gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.3);
        osc.start();
        osc.stop(audioCtx.currentTime + 0.3);
      } else if (type === 'win') {
        osc.frequency.setValueAtTime(440, audioCtx.currentTime);
        osc.frequency.exponentialRampToValueAtTime(1046.50, audioCtx.currentTime + 0.6);
        gain.gain.setValueAtTime(0.4, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.6);
        osc.start();
        osc.stop(audioCtx.currentTime + 0.6);
      }
    }

    // Questions Data (Grade 2 Curriculum & Nature Awareness)
    const questions = [
      {
        question: "ما هو الحرف الأول في كلمة (طـائـر)؟",
        options: ["حرف ( ط )", "حرف ( ب )", "حرف ( س )", "حرف ( م )"],
        answer: 0
      },
      {
        question: "كم ناتج جمع: ٥ + ٤ = ؟",
        options: ["٧", "٨", "٩", "١٠"],
        answer: 2
      },
      {
        question: "أيّ من هذه الكائنات يغطي جسمه الريش؟",
        options: ["القطة", "العصفور", "السمكة", "الأرنب"],
        answer: 1
      },
      {
        question: "ما هو مضاد كلمة (صغير)؟",
        options: ["جميل", "كبير", "سريع", "قريب"],
        answer: 1
      },
      {
        question: "ماذا يأكل الطائر الصغير ليقوى ويكبر؟",
        options: ["الحبوب والديدان", "الحجارة", "الأوراق الجافة", "الحديد"],
        answer: 0
      }
    ];

    let currentQuestion = 0;
    let score = 0;
    let isClickable = true;

    function loadQuestion() {
      isClickable = true;
      const q = questions[currentQuestion];
      document.getElementById('questionText').innerText = q.question;
      document.getElementById('levelText').innerText = currentQuestion + 1;
      document.getElementById('scoreText').innerText = score;
      
      const progressPercent = (currentQuestion / questions.length) * 100;
      document.getElementById('progressBar').style.width = progressPercent + '%';

      // Update Cage Animation
      const cage = document.getElementById('cage');
      cage.style.transform = `translateY(-${currentQuestion * 25}px)`;
      cage.style.opacity = 1 - (currentQuestion * 0.2);

      const grid = document.getElementById('optionsGrid');
      grid.innerHTML = '';

      q.options.forEach((opt, index) => {
        const btn = document.createElement('button');
        btn.className = 'btn-option';
        btn.innerText = opt;
        btn.onclick = () => checkAnswer(index, btn);
        grid.appendChild(btn);
      });
    }

    function checkAnswer(selectedIndex, btn) {
      if (!isClickable) return;
      isClickable = false;

      const q = questions[currentQuestion];
      const bird = document.getElementById('bird');

      if (selectedIndex === q.answer) {
        btn.classList.add('correct');
        playSound('correct');
        score += 20;
        document.getElementById('scoreText').innerText = score;
        bird.classList.add('bird-happy');

        setTimeout(() => {
          bird.classList.remove('bird-happy');
          currentQuestion++;
          if (currentQuestion < questions.length) {
            loadQuestion();
          } else {
            showWinScreen();
          }
        }, 1200);

      } else {
        btn.classList.add('wrong');
        playSound('wrong');
        bird.style.transform = 'shake(10px)';

        setTimeout(() => {
          btn.classList.remove('wrong');
          isClickable = true;
        }, 800);
      }
    }

    function showWinScreen() {
      playSound('win');
      document.getElementById('playArea').style.display = 'none';
      document.getElementById('winScreen').style.display = 'flex';
      document.getElementById('progressBar').style.width = '100%';
      document.getElementById('cage').style.display = 'none';
      createConfetti();
    }

    function createConfetti() {
      const card = document.getElementById('gameCard');
      const colors = ['#FFC107', '#2196F3', '#4CAF50', '#E91E63', '#9C27B0'];
      for (let i = 0; i < 30; i++) {
        const p = document.createElement('div');
        p.className = 'confetti';
        p.style.left = Math.random() * 100 + '%';
        p.style.top = '0px';
        p.style.backgroundColor = colors[Math.floor(Math.random() * colors.length)];
        p.style.animationDelay = Math.random() * 2 + 's';
        card.appendChild(p);
      }
    }

    function restartGame() {
      currentQuestion = 0;
      score = 0;
      document.getElementById('playArea').style.display = 'block';
      document.getElementById('winScreen').style.display = 'none';
      document.getElementById('cage').style.style = 'block';
      loadQuestion();
    }

    // Register Service Worker for Offline Functionality
    if ('serviceWorker' in navigator) {
      navigator.serviceWorker.register('sw.js');
    }

    // Start Game
    loadQuestion();
  </script>
</body>
</html>
```eof

---

### كيف تقوم بتحديث اللعبة في GitHub بسهولة من الهاتف؟

1. افتح صفحة المستودع الخاص بك على GitHub:
   `[https://github.com/ahahhaha09-sketch/talluza-school-game](https://github.com/ahahhaha09-sketch/talluza-school-game)`
2. اضغط على ملف **`index.html`** للفتحه.
3. اضغط على أيقونة **القلم (Edit this file)** لتعديله.
4. حدد الكود القديم بالكامل، واحيذفْه، ثم **الصق الكود الجديد أعلاه**.
5. انزل لأسفل واضغط على الزر الأخضر **Commit changes**.

خلال دقيقة واحدة، قم بفتح اللعبة على هاتفك وستلاحظ الفرق الكبير في التصميم، والأصوات، والألوان والتحريك!
