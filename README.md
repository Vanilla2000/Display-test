# Display-test
Display evo test

<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Тест: Эволюция дисплеев</title>
    <style>
        :root {
            --bg: #0f172a;
            --card: #1e293b;
            --text: #f8fafc;
            --accent: #38bdf8;
            --correct: #22c55e;
            --wrong: #ef4444;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
        }

        #quiz-container {
            background-color: var(--card);
            padding: 2rem;
            border-radius: 1rem;
            box-shadow: 0 10px 25px rgba(0,0,0,0.3);
            width: 100%;
            max-width: 600px;
            text-align: center;
        }

        h2 { color: var(--accent); margin-bottom: 1.5rem; }

        .options-grid {
            display: grid;
            gap: 10px;
            margin-top: 1rem;
        }

        button {
            background: #334155;
            color: white;
            border: 2px solid transparent;
            padding: 12px 20px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 1rem;
            transition: 0.2s;
        }

        button:hover { background: #475569; border-color: var(--accent); }

        .correct { background-color: var(--correct) !important; }
        .wrong { background-color: var(--wrong) !important; }

        #next-btn {
            margin-top: 1.5rem;
            background-color: var(--accent);
            color: var(--bg);
            font-weight: bold;
            display: none;
            width: 100%;
        }

        #result { font-size: 1.4rem; font-weight: bold; margin-bottom: 1rem; }
        #feedback { font-style: italic; color: #94a3b8; margin-bottom: 1.5rem; }
    </style>
</head>
<body>

<div id="quiz-container">
    <div id="quiz">
        <h2 id="question">Загрузка вопроса...</h2>
        <div id="options" class="options-grid"></div>
        <button id="next-btn">Следующий вопрос</button>
    </div>
    <div id="result-container" style="display:none;">
        <h2>Тест завершен!</h2>
        <p id="result"></p>
        <p id="feedback"></p>
        <button onclick="location.reload()">Пройти еще раз</button>
    </div>
</div>

<script>
    // --- БЛОК ИЗ 10 ВОПРОСОВ ---
    const questions = [
        {
            q: "Какая технология самая старая и использует вакуумную трубку?",
            a: ["LCD", "CRT (ЭЛТ)", "OLED", "Plasma"],
            correct: 1
        },
        {
            q: "Что нужно LCD-дисплею для работы, чего не требуется OLED?",
            a: ["Жидкие кристаллы", "Провода", "Отдельный слой подсветки", "Стеклянный корпус"],
            correct: 2
        },
        {
            q: "Какая технология позволяет создавать гибкие и сворачивающиеся экраны?",
            a: ["OLED", "CRT", "MicroLED", "LCD"],
            correct: 0
        },
        {
            q: "Что означает буква 'Q' в названии технологии QLED?",
            a: ["Quick (Быстрый)", "Quality (Качество)", "Quantum (Квантовые точки)", "Quartz (Кварц)"],
            correct: 2
        },
        {
            q: "Главное преимущество MicroLED перед OLED?",
            a: ["Низкая цена", "Отсутствие риска выгорания", "Большая толщина", "Меньшая яркость"],
            correct: 1
        },
        {
            q: "Как называется минимальный элемент изображения (красный + зеленый + синий)?",
            a: ["Бит", "Транзистор", "Пиксель", "Люминофор"],
            correct: 2
        },
        {
            q: "Какая технология дает 'бесконечный' черный цвет?",
            a: ["LCD", "CRT", "OLED / MicroLED", "TFT"],
            correct: 2
        },
        {
            q: "Почему старые ЭЛТ (CRT) мониторы мерцали?",
            a: ["Из-за перегрева", "Из-за построчного сканирования лучом", "Из-за пыли", "Из-за жидких кристаллов"],
            correct: 1
        },
        {
            q: "Что такое PPI в характеристиках дисплея?",
            a: ["Плотность пикселей на дюйм", "Скорость обновления", "Тип матрицы", "Энергопотребление"],
            correct: 0
        },
        {
            q: "Правильный порядок эволюции (от старого к новому):",
            a: ["LCD -> CRT -> OLED", "CRT -> LCD -> OLED -> MicroLED", "OLED -> LCD -> CRT", "MicroLED -> OLED -> CRT"],
            correct: 1
        }
    ];

    let currentQ = 0;
    let score = 0;

    const qElem = document.getElementById("question");
    const optElem = document.getElementById("options");
    const nextBtn = document.getElementById("next-btn");

    function loadQuestion() {
        const data = questions[currentQ];
        qElem.innerText = data.q;
        optElem.innerHTML = "";
        nextBtn.style.display = "none";

        data.a.forEach((text, index) => {
            const btn = document.createElement("button");
            btn.innerText = text;
            btn.onclick = () => checkAnswer(index, btn);
            optElem.appendChild(btn);
        });
    }

    function checkAnswer(idx, btn) {
        const allBtns = optElem.querySelectorAll("button");
        allBtns.forEach(b => b.disabled = true);

        if (idx === questions[currentQ].correct) {
            btn.classList.add("correct");
            score++;
        } else {
            btn.classList.add("wrong");
            allBtns[questions[currentQ].correct].classList.add("correct");
        }
        nextBtn.style.display = "block";
    }

    nextBtn.onclick = () => {
        currentQ++;
        if (currentQ < questions.length) {
            loadQuestion();
        } else {
            showResults();
        }
    };

    function showResults() {
        document.getElementById("quiz").style.display = "none";
        document.getElementById("result-container").style.display = "block";
        
        const resultText = document.getElementById("result");
        const feedbackText = document.getElementById("feedback");
        
        resultText.innerText = `Ваш результат: ${score} из ${questions.length}`;
        
        if (score === 10) feedbackText.innerText = "Великолепно! Вы эксперт в дисплеях.";
        else if (score >= 7) feedbackText.innerText = "Отличный результат! Почти идеально.";
        else if (score >= 4) feedbackText.innerText = "Неплохо, но стоит повторить некоторые темы.";
        else feedbackText.innerText = "Попробуйте еще раз!";
    }

    loadQuestion();
</script>

</body>
</html>
