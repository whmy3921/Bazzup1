# Bazzup1
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>لعبة XO احترافية</title>
    <style>
        :root {
            --primary-color: #4a6fa5;
            --secondary-color: #6b98d1;
            --accent-color: #ff6b6b;
            --background-color: #f8f9fa;
            --text-color: #333;
            --cell-size: 100px;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--background-color);
            color: var(--text-color);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        .container {
            text-align: center;
            max-width: 500px;
            width: 100%;
            background: white;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
            padding: 30px;
            transition: transform 0.3s ease;
        }

        .container:hover {
            transform: translateY(-5px);
        }

        h1 {
            color: var(--primary-color);
            margin-bottom: 20px;
            font-size: 2.5rem;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
        }

        .status {
            font-size: 1.2rem;
            margin: 20px 0;
            padding: 10px;
            border-radius: 10px;
            background-color: #f0f0f0;
            transition: all 0.3s ease;
        }

        .board {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            grid-gap: 10px;
            margin: 20px auto;
            max-width: calc(var(--cell-size) * 3 + 20px);
        }

        .cell {
            width: var(--cell-size);
            height: var(--cell-size);
            background-color: #fff;
            border: 2px solid var(--primary-color);
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2.5rem;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.2s ease;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }

        .cell:hover {
            background-color: #f0f5ff;
            transform: scale(1.05);
        }

        .cell.x {
            color: var(--primary-color);
        }

        .cell.o {
            color: var(--accent-color);
        }

        .reset-btn {
            background-color: var(--primary-color);
            color: white;
            border: none;
            padding: 12px 25px;
            font-size: 1rem;
            border-radius: 50px;
            cursor: pointer;
            margin-top: 20px;
            transition: all 0.3s ease;
            box-shadow: 0 4px 10px rgba(74, 111, 165, 0.3);
        }

        .reset-btn:hover {
            background-color: var(--secondary-color);
            transform: translateY(-2px);
            box-shadow: 0 6px 15px rgba(74, 111, 165, 0.4);
        }

        .score-board {
            display: flex;
            justify-content: space-around;
            margin-top: 20px;
            background: linear-gradient(135deg, #f0f5ff, #e6eeff);
            padding: 15px;
            border-radius: 15px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
        }

        .score {
            text-align: center;
        }

        .score span {
            display: block;
            font-size: 1.8rem;
            font-weight: bold;
            color: var(--primary-color);
        }

        .win {
            background-color: #e6ffea;
            color: #2e7d32;
            animation: pulse 0.5s ease;
        }

        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }

        @media (max-width: 500px) {
            :root {
                --cell-size: 80px;
            }
            
            .container {
                padding: 15px;
            }
            
            h1 {
                font-size: 2rem;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>لعبة XO</h1>
        
        <div class="status" id="status">دور لاعب X</div>
        
        <div class="board" id="board">
            <div class="cell" data-cell-index="0"></div>
            <div class="cell" data-cell-index="1"></div>
            <div class="cell" data-cell-index="2"></div>
            <div class="cell" data-cell-index="3"></div>
            <div class="cell" data-cell-index="4"></div>
            <div class="cell" data-cell-index="5"></div>
            <div class="cell" data-cell-index="6"></div>
            <div class="cell" data-cell-index="7"></div>
            <div class="cell" data-cell-index="8"></div>
        </div>
        
        <button class="reset-btn" id="resetBtn">لعب مرة أخرى</button>
        
        <div class="score-board">
            <div class="score">
                <span id="xScore">0</span>
                <div>لاعب X</div>
            </div>
            <div class="score">
                <span id="oScore">0</span>
                <div>لاعب O</div>
            </div>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            // عناصر DOM
            const statusDisplay = document.getElementById('status');
            const cells = document.querySelectorAll('.cell');
            const resetButton = document.getElementById('resetBtn');
            const xScoreDisplay = document.getElementById('xScore');
            const oScoreDisplay = document.getElementById('oScore');
            
            // متغيرات اللعبة
            let gameActive = true;
            let currentPlayer = 'X';
            let gameState = ['', '', '', '', '', '', '', '', ''];
            let scores = { X: 0, O: 0 };
            
            // رسائل الحالة
            const winningMessage = () => `فاز لاعب ${currentPlayer}!`;
            const drawMessage = () => `تعادل!`;
            const currentPlayerTurn = () => `دور لاعب ${currentPlayer}`;
            
            // تهيئة الرسالة الأولى
            statusDisplay.innerHTML = currentPlayerTurn();
            
            // أنماط الفوز الممكنة
            const winningConditions = [
                [0, 1, 2], [3, 4, 5], [6, 7, 8], // صفوف
                [0, 3, 6], [1, 4, 7], [2, 5, 8], // أعمدة
                [0, 4, 8], [2, 4, 6]             // أقطار
            ];
            
            // معالجة نقرات الخلايا
            function handleCellClick(clickedCellEvent) {
                const clickedCell = clickedCellEvent.target;
                const clickedCellIndex = parseInt(clickedCell.getAttribute('data-cell-index'));
                
                // التحقق إذا كانت الخلية مشغولة أو اللعبة غير نشطة
                if (gameState[clickedCellIndex] !== '' || !gameActive) return;
                
                // تحديث حالة اللعبة والواجهة
                gameState[clickedCellIndex] = currentPlayer;
                clickedCell.innerHTML = currentPlayer;
                clickedCell.classList.add(currentPlayer.toLowerCase());
                
                // التحقق من الفوز أو التعادل
                checkResult();
            }
            
            // التحقق من النتيجة
            function checkResult() {
                let roundWon = false;
                let winningLine = [];
                
                // التحقق من أنماط الفوز
                for (let i = 0; i < winningConditions.length; i++) {
                    const [a, b, c] = winningConditions[i];
                    if (gameState[a] === '' || gameState[b] === '' || gameState[c] === '') continue;
                    
                    if (gameState[a] === gameState[b] && gameState[b] === gameState[c]) {
                        roundWon = true;
                        winningLine = [a, b, c];
                        break;
                    }
                }
                
                if (roundWon) {
                    // حالة الفوز
                    statusDisplay.innerHTML = winningMessage();
                    statusDisplay.classList.add('win');
                    gameActive = false;
                    
                    // تحديث النتيجة
                    scores[currentPlayer]++;
                    updateScores();
                    
                    // تلوين خلايا الفوز
                    winningLine.forEach(index => {
                        document.querySelector(`[data-cell-index="${index}"]`).style.backgroundColor = '#e6ffea';
                    });
                    
                    return;
                }
                
                // حالة التعادل
                const roundDraw = !gameState.includes('');
                if (roundDraw) {
                    statusDisplay.innerHTML = drawMessage();
                    gameActive = false;
                    return;
                }
                
                // تغيير اللاعب
                currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
                statusDisplay.innerHTML = currentPlayerTurn();
            }
            
            // تحديث عرض النتائج
            function updateScores() {
                xScoreDisplay.textContent = scores.X;
                oScoreDisplay.textContent = scores.O;
            }
            
            // إعادة تعيين اللعبة
            function handleRestartGame() {
                gameActive = true;
                currentPlayer = 'X';
                gameState = ['', '', '', '', '', '', '', '', ''];
                statusDisplay.innerHTML = currentPlayerTurn();
                statusDisplay.classList.remove('win');
                
                cells.forEach(cell => {
                    cell.innerHTML = '';
                    cell.classList.remove('x', 'o');
                    cell.style.backgroundColor = '';
                });
            }
            
            // إضافة مستمعي الأحداث
            cells.forEach(cell => cell.addEventListener('click', handleCellClick));
            resetButton.addEventListener('click', handleRestartGame);
        });
    </script>
</body>
</html>
