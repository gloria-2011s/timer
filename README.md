<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>2학년 1학기 수행평가 타이머</title>
    <style>
        body { 
            background-color: #f1f2f6; 
            font-family: 'Pretendard', -apple-system, sans-serif; 
            display: flex; 
            flex-direction: column; 
            align-items: center; 
            justify-content: center; 
            height: 100vh; 
            margin: 0; 
            color: #2f3542;
            overflow: hidden; 
        }
        
        h1 { font-size: 2.2rem; margin-bottom: 5px; color: #1e272e; }

        /* 타이머 크기 최적화 */
        #timer { 
            font-size: 16rem; 
            font-weight: 900; 
            margin: 5px 0; 
            line-height: 1;
            font-variant-numeric: tabular-nums;
            text-shadow: 4px 4px 0px rgba(0,0,0,0.05);
        }

        /* 안내 사항 박스 */
        .info-box {
            background: white;
            padding: 25px 40px;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
            margin-top: 15px;
            border-top: 10px solid #2f3542;
            text-align: center;
            width: 85%;
            max-width: 850px;
        }
        .info-item { 
            font-size: 2rem; 
            margin: 8px 0; 
            font-weight: 700; 
            line-height: 1.3;
        }
        .highlight { color: #eb4d4b; }

        /* 버튼 영역 */
        .controls { margin-top: 25px; display: flex; gap: 15px; }
        button { 
            padding: 12px 35px; 
            font-size: 1.2rem; 
            cursor: pointer; 
            border: none; 
            border-radius: 12px; 
            font-weight: 800;
            transition: all 0.2s;
        }
        .start { background: #3742fa; color: white; }
        .reset { background: #747d8c; color: white; }
        button:hover { transform: scale(1.05); opacity: 0.9; }
    </style>
</head>
<body>

    <h1>2학년 1학기 1차 수행평가</h1>

    <div id="timer">30:00</div>

    <div class="info-box">
        <div class="info-item">📌 준비물 : <b>아이북</b></div>
        <div class="info-item">📊 데이터량 <b>충분한 갯수</b>로 분석</div>
        <div class="info-item highlight">⚠️ 수행평가지 부정행위 사항 절대 유의</div>
    </div>
    
    <div class="controls">
        <button class="start" onclick="toggleTimer()" id="startBtn">시험 시작</button>
        <button class="reset" onclick="resetTimer()">초기화</button>
    </div>

    <script>
        // 시간을 30분(1800초)으로 설정
        let timeLeft = 30 * 60; 
        let timerInterval = null;
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();

        function playStrongAlarm() {
            let count = 0;
            const interval = setInterval(() => {
                const oscillator = audioCtx.createOscillator();
                const gainNode = audioCtx.createGain();
                oscillator.connect(gainNode);
                gainNode.connect(audioCtx.destination);
                
                oscillator.type = 'square'; 
                oscillator.frequency.setValueAtTime(880, audioCtx.currentTime); 
                gainNode.gain.setValueAtTime(0.3, audioCtx.currentTime);
                
                oscillator.start();
                oscillator.stop(audioCtx.currentTime + 0.15);
                
                count++;
                if (count >= 6) clearInterval(interval); 
            }, 250);
        }

        function updateDisplay() {
            let mins = Math.floor(timeLeft / 60);
            let secs = timeLeft % 60;
            document.getElementById('timer').textContent = 
                `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
            
            // 1분 남았을 때부터 빨간색으로 변경하여 경고
            if (timeLeft <= 60) {
                document.getElementById('timer').style.color = "#ff4757";
            } else {
                document.getElementById('timer').style.color = "#2f3542";
            }
        }

        function toggleTimer() {
            if (audioCtx.state === 'suspended') audioCtx.resume();

            if (timerInterval) {
                clearInterval(timerInterval);
                timerInterval = null;
                document.getElementById('startBtn').textContent = "다시 시작";
            } else {
                document.getElementById('startBtn').textContent = "일시 정지";
                timerInterval = setInterval(() => {
                    if (timeLeft > 0) {
                        timeLeft--;
                        updateDisplay();
                    } else {
                        clearInterval(timerInterval);
                        playStrongAlarm(); 
                        setTimeout(() => alert("수행평가 시간이 종료되었습니다!"), 1500);
                    }
                }, 1000);
            }
        }

        function resetTimer() {
            clearInterval(timerInterval);
            timerInterval = null;
            timeLeft = 30 * 60; 
            document.getElementById('startBtn').textContent = "시험 시작";
            document.getElementById('timer').style.color = "#2f3542";
            updateDisplay();
        }
    </script>
</body>
</html>
