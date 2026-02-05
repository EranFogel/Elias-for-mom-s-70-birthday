# Elias-for-mom-s-70-birthday
A family game
```html
<!DOCTYPE html>
<html lang="he" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>משחק אליאס</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Arial', sans-serif;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            padding: 20px;
        }
        
        .screen {
            display: none;
            width: 100%;
            max-width: 600px;
        }
        
        .screen.active {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
        }
        
        .home-screen {
            gap: 30px;
        }
        
        .deck-button {
            width: 100%;
            max-width: 400px;
            padding: 40px;
            font-size: 28px;
            font-weight: bold;
            border: none;
            border-radius: 15px;
            cursor: pointer;
            color: white;
            transition: transform 0.2s, box-shadow 0.2s;
            box-shadow: 0 8px 20px rgba(0,0,0,0.3);
        }
        
        .deck-button:hover {
            transform: translateY(-3px);
            box-shadow: 0 12px 25px rgba(0,0,0,0.4);
        }
        
        .deck-button:active {
            transform: translateY(0);
        }
        
        .blue-deck {
            background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
        }
        
        .green-deck {
            background: linear-gradient(135deg, #43e97b 0%, #38f9d7 100%);
        }
        
        .game-screen {
            background: white;
            border-radius: 20px;
            padding: 30px;
            box-shadow: 0 15px 35px rgba(0,0,0,0.3);
        }
        
        .game-screen.blue-theme {
            border: 5px solid #4facfe;
        }
        
        .game-screen.green-theme {
            border: 5px solid #43e97b;
        }
        
        .deck-title {
            font-size: 24px;
            font-weight: bold;
            text-align: center;
            margin-bottom: 20px;
        }
        
        .blue-theme .deck-title {
            color: #4facfe;
        }
        
        .green-theme .deck-title {
            color: #43e97b;
        }
        
        .word-display {
            font-size: 56px;
            font-weight: bold;
            text-align: center;
            min-height: 120px;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 30px;
            border-radius: 10px;
            margin: 20px 0;
        }
        
        .blue-theme .word-display {
            background: linear-gradient(135deg, #e0f4ff 0%, #b3e5fc 100%);
            color: #0277bd;
        }
        
        .green-theme .word-display {
            background: linear-gradient(135deg, #e8f5e9 0%, #c8e6c9 100%);
            color: #2e7d32;
        }
        
        .timer {
            font-size: 48px;
            font-weight: bold;
            text-align: center;
            margin: 20px 0;
            padding: 20px;
            border-radius: 10px;
            background: #f5f5f5;
            color: #333;
        }
        
        .timer.warning {
            color: #d32f2f;
            animation: pulse 1s infinite;
        }
        
        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }
        
        .button-group {
            display: flex;
            flex-direction: column;
            gap: 15px;
            width: 100%;
            margin-top: 20px;
        }
        
        .game-button {
            padding: 18px 30px;
            font-size: 20px;
            font-weight: bold;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.2s;
            color: white;
        }
        
        .game-button:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }
        
        .game-button:active {
            transform: translateY(0);
        }
        
        .game-button:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }
        
        .blue-theme .primary-button {
            background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
        }
        
        .green-theme .primary-button {
            background: linear-gradient(135deg, #43e97b 0%, #38f9d7 100%);
        }
        
        .secondary-button {
            background: linear-gradient(135deg, #a8a8a8 0%, #7a7a7a 100%);
        }
        
        .message {
            font-size: 24px;
            font-weight: bold;
            text-align: center;
            color: #d32f2f;
            margin: 10px 0;
            min-height: 30px;
        }
    </style>
</head>
<body>
    <div class="screen home-screen active" id="homeScreen">
        <button class="deck-button blue-deck" onclick="startGame('blue')">חבילת קלפים כחולה</button>
        <button class="deck-button green-deck" onclick="startGame('green')">חבילת קלפים ירוקה</button>
    </div>
    
    <div class="screen game-screen" id="gameScreen">
        <div class="deck-title" id="deckTitle"></div>
        <div class="word-display" id="wordDisplay">לחץ על מילה הבאה</div>
        <div class="timer" id="timer">60</div>
        <div class="message" id="message"></div>
        <div class="button-group">
            <button class="game-button primary-button" id="nextWordBtn" onclick="nextWord()">מילה הבאה</button>
            <button class="game-button secondary-button" onclick="toggleTimer()">התחל / אפס טיימר</button>
            <button class="game-button secondary-button" onclick="goHome()">חזרה למסך הראשי</button>
        </div>
    </div>
    
    <script>
        const blueWords = ['חמד', 'פסוליה', 'במיה', 'שעועית', 'שנקין', 'איינשטיין', 'אוניברסיטת ת״א', 'אליאנס', 'בל״ל', 'בנק לאומי', 'טלרים', 'מנהל תשלומים', 'ספרד', 'מנחם', 'חמסה', 'חומסה', 'נונו', 'הנרייט', 'בנין', 'לולי', 'דולי', 'מימדיון', 'דואה', 'שתלים', 'דנטליים', 'שברץ 18', 'אלקבץ', 'ציפורה', 'יעקב', 'מלוחיה', 'ללא סוכר', 'פיפ', 'עָדֶן', 'פורט סעיד', 'קהיר', 'אלכסנדריה', 'לונדון', 'היפים והאמיצים', 'רידג׳', 'בית ספר ניצנים', 'נגריה', 'דודה בלה', 'זקו', 'באר שבע', 'מעברה'];
        
        const greenWords = ['תל אביב', 'חמור', 'נונה', 'רנה', 'כבש', 'נכדים', 'פוגל', 'ענת', 'ערן', 'מיה', 'שני', 'גלעד', 'גבי', 'מעיין', 'יאיר', 'כרמל', 'אורי', 'נעם', 'אלה', 'איתן', 'ארי', 'עוז', 'דני', 'פרעה', 'משה רבנו'];
        
        let currentDeck = [];
        let usedWords = [];
        let activeDeck = '';
        let timerInterval = null;
        let timeLeft = 60;
        
        function startGame(deck) {
            activeDeck = deck;
            usedWords = [];
            
            if (deck === 'blue') {
                currentDeck = [...blueWords];
                document.getElementById('deckTitle').textContent = 'חבילה כחולה';
                document.getElementById('gameScreen').className = 'screen game-screen active blue-theme';
            } else {
                currentDeck = [...greenWords];
                document.getElementById('deckTitle').textContent = 'חבילה ירוקה';
                document.getElementById('gameScreen').className = 'screen game-screen active green-theme';
            }
            
            document.getElementById('homeScreen').classList.remove('active');
            document.getElementById('wordDisplay').textContent = 'לחץ על מילה הבאה';
            document.getElementById('message').textContent = '';
            document.getElementById('nextWordBtn').disabled = false;
            resetTimer();
        }
        
        function nextWord() {
            const availableWords = currentDeck.filter(word => !usedWords.includes(word));
            
            if (availableWords.length === 0) {
                document.getElementById('message').textContent = 'אין יותר מילים בחבילה הזו';
                document.getElementById('nextWordBtn').disabled = true;
                return;
            }
            
            const randomIndex = Math.floor(Math.random() * availableWords.length);
            const selectedWord = availableWords[randomIndex];
            
            usedWords.push(selectedWord);
            document.getElementById('wordDisplay').textContent = selectedWord;
            document.getElementById('message').textContent = '';
        }
        
        function toggleTimer() {
            if (timerInterval) {
                stopTimer();
                resetTimer();
            } else {
                startTimer();
            }
        }
        
        function startTimer() {
            stopTimer();
            timeLeft = 60;
            updateTimerDisplay();
            
            timerInterval = setInterval(() => {
                timeLeft--;
                updateTimerDisplay();
                
                if (timeLeft <= 0) {
                    stopTimer();
                    document.getElementById('message').textContent = 'הזמן נגמר';
                }
            }, 1000);
        }
        
        function stopTimer() {
            if (timerInterval) {
                clearInterval(timerInterval);
                timerInterval = null;
            }
        }
        
        function resetTimer() {
            stopTimer();
            timeLeft = 60;
            updateTimerDisplay();
        }
        
        function updateTimerDisplay() {
            const timerElement = document.getElementById('timer');
            timerElement.textContent = timeLeft;
            
            if (timeLeft <= 10) {
                timerElement.classList.add('warning');
            } else {
                timerElement.classList.remove('warning');
            }
        }
        
        function goHome() {
            stopTimer();
            document.getElementById('gameScreen').classList.remove('active');
            document.getElementById('homeScreen').classList.add('active');
        }
    </script>
</body>
</html>
```
