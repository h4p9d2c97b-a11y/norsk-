<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Norsk A1 — Інтерактивний тренажер слів</title>
    <style>
        :root {
            --primary: #00205B;
            --secondary: #BA0C2F;
            --bg: #F4F6F9;
            --card-bg: #FFFFFF;
            --text: #2D3748;
            --success: #28A745;
            --danger: #DC3545;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        header {
            text-align: center;
            margin-bottom: 25px;
        }

        h1 {
            color: var(--primary);
            margin-bottom: 5px;
        }

        p.subtitle {
            color: #666;
            margin-top: 0;
        }

        .nav-tabs {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
        }

        .tab-btn {
            padding: 10px 20px;
            border: none;
            background-color: #E2E8F0;
            color: var(--text);
            font-weight: bold;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.2s;
        }

        .tab-btn.active {
            background-color: var(--primary);
            color: white;
        }

        .section {
            display: none;
            width: 100%;
            max-width: 600px;
        }

        .section.active {
            display: block;
        }

        /* --- FLASHCARDS --- */
        .card-container {
            perspective: 1000px;
            width: 100%;
            height: 220px;
            cursor: pointer;
        }

        .card {
            width: 100%;
            height: 100%;
            position: relative;
            transform-style: preserve-3d;
            transition: transform 0.6s;
            border-radius: 15px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
        }

        .card.flipped {
            transform: rotateY(180deg);
        }

        .card-front, .card-back {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            border-radius: 15px;
            background: var(--card-bg);
            padding: 20px;
            box-sizing: border-box;
        }

        .card-front {
            border-top: 6px solid var(--primary);
        }

        .card-back {
            background-color: #F0F4FF;
            transform: rotateY(180deg);
            border-top: 6px solid var(--secondary);
        }

        .word {
            font-size: 2rem;
            font-weight: bold;
            color: var(--primary);
            margin-bottom: 10px;
        }

        .translation {
            font-size: 1.5rem;
            color: var(--secondary);
        }

        .hint {
            font-size: 0.85rem;
            color: #888;
            position: absolute;
            bottom: 15px;
        }

        .controls {
            display: flex;
            justify-content: space-between;
            margin-top: 15px;
        }

        .btn {
            padding: 10px 20px;
            border: none;
            background: var(--primary);
            color: white;
            border-radius: 8px;
            cursor: pointer;
            font-size: 1rem;
        }

        /* --- QUIZ --- */
        .quiz-card {
            background: white;
            padding: 20px;
            border-radius: 12px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.05);
        }

        .options-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
            margin-top: 15px;
        }

        .option-btn {
            padding: 12px;
            border: 2px solid #E2E8F0;
            background: white;
            border-radius: 8px;
            cursor: pointer;
            font-size: 1rem;
            transition: all 0.2s;
        }

        .option-btn.correct {
            background-color: var(--success);
            color: white;
            border-color: var(--success);
        }

        .option-btn.wrong {
            background-color: var(--danger);
            color: white;
            border-color: var(--danger);
        }

        /* --- DICTIONARY --- */
        .search-box {
            width: 100%;
            padding: 12px;
            border: 2px solid #CBD5E0;
            border-radius: 8px;
            font-size: 1rem;
            margin-bottom: 15px;
            box-sizing: border-box;
        }

        .dict-list {
            background: white;
            border-radius: 12px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.05);
            max-height: 400px;
            overflow-y: auto;
        }

        .dict-item {
            display: flex;
            justify-content: space-between;
            padding: 12px 20px;
            border-bottom: 1px solid #E2E8F0;
        }

        .dict-item:last-child {
            border-bottom: none;
        }

        .norwegian-word {
            font-weight: bold;
            color: var(--primary);
        }
    </style>
</head>
<body>

    <header>
        <h1>Norsk A1 Leksjoner</h1>
        <p class="subtitle">Інтерактивний тренажер словникового запасу</p>
    </header>

    <div class="nav-tabs">
        <button class="tab-btn active" onclick="switchTab('cards')">🎴 Картки</button>
        <button class="tab-btn" onclick="switchTab('quiz')">✍️ Тест</button>
        <button class="tab-btn" onclick="switchTab('dict')">📖 Словник</button>
    </div>

    <!-- FLASHCARDS SECTION -->
    <div id="cards" class="section active">
        <div class="card-container" onclick="flipCard()">
            <div class="card" id="flashcard">
                <div class="card-front">
                    <div class="word" id="card-no">Завантаження...</div>
                    <div class="hint">Натисніть, щоб перевернути</div>
                </div>
                <div class="card-back">
                    <div class="translation" id="card-ru">Переклад</div>
                    <div class="hint">Натисніть, щоб повернути</div>
                </div>
            </div>
        </div>
        <div class="controls">
            <button class="btn" onclick="prevCard()">← Назад</button>
            <span id="card-counter" style="align-self: center; font-weight: bold;">1 / 20</span>
            <button class="btn" onclick="nextCard()">Вперед →</button>
        </div>
    </div>

    <!-- QUIZ SECTION -->
    <div id="quiz" class="section">
        <div class="quiz-card">
            <h3 id="quiz-question" style="margin-top: 0;">Питання...</h3>
            <div class="options-grid" id="quiz-options"></div>
            <div style="margin-top: 15px; text-align: right;">
                <button class="btn" onclick="nextQuizQuestion()">Наступне →</button>
            </div>
        </div>
    </div>

    <!-- DICTIONARY SECTION -->
    <div id="dict" class="section">
        <input type="text" class="search-box" id="search" placeholder="Пошук слова (норвезькою або російською)..." oninput="filterDict()">
        <div class="dict-list" id="dict-container"></div>
    </div>

    <script>
        const words = [
            { no: "leksjon", ru: "урок" },
            { no: "Hei!", ru: "Привет!" },
            { no: "Hva heter du?", ru: "Как тебя зовут?" },
            { no: "du", ru: "ты" },
            { no: "hva", ru: "что / как" },
            { no: "Jeg heter...", ru: "Меня зовут..." },
            { no: "jeg", ru: "я" },
            { no: "er", ru: "есть / являюсь" },
            { no: "bor", ru: "живу" },
            { no: "i", ru: "в" },
            { no: "Jeg kommer fra...", ru: "Я приехал из..." },
            { no: "fra", ru: "из / с" },
            { no: "snakker", ru: "говорю" },
            { no: "og", ru: "и" },
            { no: "litt", ru: "немного" },
            { no: "norsk", ru: "норвежский" },
            { no: "går på", ru: "хожу на" },
            { no: "norskkurs", ru: "курс норвежского языка" },
            { no: "men", ru: "но" },
            { no: "kler", ru: "одежда" },
            { no: "kler (på)", ru: "одевать" },
            { no: "ligge", ru: "лежать" },
            { no: "legge", ru: "класть / положить" },
            { no: "morsmålet", ru: "родной язык" },
            { no: "lærer", ru: "учитель" },
            { no: "står opp", ru: "встаёт" },
            { no: "klokka", ru: "время / часы" },
            { no: "dusjer", ru: "принимает душ" },
            { no: "frokost", ru: "завтрак" },
            { no: "spiser", ru: "ест" },
            { no: "begynner", ru: "начинается" },
            { no: "mye", ru: "много" },
            { no: "Hvordan går det?", ru: "Как дела?" },
            { no: "Det går bra", ru: "Всё в порядке" }
        ];

        // --- FLASHCARDS LOGIC ---
        let currentCardIndex = 0;

        function updateCard() {
            document.getElementById('card-no').innerText = words[currentCardIndex].no;
            document.getElementById('card-ru').innerText = words[currentCardIndex].ru;
            document.getElementById('card-counter').innerText = `${currentCardIndex + 1} / ${words.length}`;
            document.getElementById('flashcard').classList.remove('flipped');
        }

        function flipCard() {
            document.getElementById('flashcard').classList.toggle('flipped');
        }

        function nextCard() {
            currentCardIndex = (currentCardIndex + 1) % words.length;
            updateCard();
        }

        function prevCard() {
            currentCardIndex = (currentCardIndex - 1 + words.length) % words.length;
            updateCard();
        }

        // --- QUIZ LOGIC ---
        let currentQuizWord = {};

        function nextQuizQuestion() {
            const optionsContainer = document.getElementById('quiz-options');
            optionsContainer.innerHTML = '';

            currentQuizWord = words[Math.floor(Math.random() * words.length)];
            document.getElementById('quiz-question').innerText = `Як перекладається: "${currentQuizWord.no}"?`;

            let choices = [currentQuizWord.ru];
            while (choices.length < 4) {
                let randomWord = words[Math.floor(Math.random() * words.length)].ru;
                if (!choices.includes(randomWord)) choices.push(randomWord);
            }

            choices.sort(() => Math.random() - 0.5);

            choices.forEach(option => {
                const btn = document.createElement('button');
                btn.className = 'option-btn';
                btn.innerText = option;
                btn.onclick = () => checkAnswer(btn, option);
                optionsContainer.appendChild(btn);
            });
        }

        function checkAnswer(btn, selected) {
            const buttons = document.querySelectorAll('.option-btn');
            buttons.forEach(b => b.disabled = true);

            if (selected === currentQuizWord.ru) {
                btn.classList.add('correct');
            } else {
                btn.classList.add('wrong');
                buttons.forEach(b => {
                    if (b.innerText === currentQuizWord.ru) b.classList.add('correct');
                });
            }
        }

        // --- DICTIONARY LOGIC ---
        function renderDict(filter = '') {
            const container = document.getElementById('dict-container');
            container.innerHTML = '';
            
            words
                .filter(w => w.no.toLowerCase().includes(filter.toLowerCase()) || w.ru.toLowerCase().includes(filter.toLowerCase()))
                .forEach(w => {
                    const item = document.createElement('div');
                    item.className = 'dict-item';
                    item.innerHTML = `<span class="norwegian-word">${w.no}</span><span>${w.ru}</span>`;
                    container.appendChild(item);
                });
        }

        function filterDict() {
            const val = document.getElementById('search').value;
            renderDict(val);
        }

        // --- NAVIGATION LOGIC ---
        function switchTab(tabId) {
            document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
            document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));

            document.getElementById(tabId).classList.add('active');
            event.target.classList.add('active');

            if (tabId === 'quiz') nextQuizQuestion();
        }

        // Init
        updateCard();
        renderDict();
    </script>
</body>
</html>
