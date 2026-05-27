<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🔥 宇宙無敵殘酷/幸運抽籤機 🔥</title>
    <!-- 引入 Tailwind CSS 方便排版 -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- 引入 Canvas Confetti 煙火特效套件 -->
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <!-- 引入好玩的字體 -->
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@700;900&display=swap" rel="stylesheet">
    
    <style>
        body {
            font-family: 'Noto Sans TC', sans-serif;
            /* 幽默的動態漸層背景 */
            background: linear-gradient(135deg, #ff9a9e 0%, #fecfef 99%, #fecfef 100%);
            background-size: 400% 400%;
            animation: gradientBG 10s ease infinite;
            overflow: hidden; /* 防止煙火造成捲軸 */
        }

        @keyframes gradientBG {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        .name-box {
            box-shadow: 0 10px 25px rgba(0,0,0,0.2), inset 0 0 20px rgba(255,255,255,0.5);
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
            transition: all 0.1s;
        }

        /* 抽籤時的震動特效 */
        .shake {
            animation: shake 0.5s cubic-bezier(.36,.07,.19,.97) both infinite;
            transform: translate3d(0, 0, 0);
            backface-visibility: hidden;
            perspective: 1000px;
        }

        @keyframes shake {
            10%, 90% { transform: translate3d(-2px, 0, 0) rotate(-1deg); }
            20%, 80% { transform: translate3d(4px, 0, 0) rotate(2deg); }
            30%, 50%, 70% { transform: translate3d(-8px, 0, 0) rotate(-2deg); }
            40%, 60% { transform: translate3d(8px, 0, 0) rotate(2deg); }
        }

        /* 放大彈跳特效 (公布結果時) */
        .pop {
            animation: pop 0.6s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
        }

        @keyframes pop {
            0% { transform: scale(0.5); opacity: 0; }
            100% { transform: scale(1); opacity: 1; }
        }

        /* 按鈕特效 */
        .btn-magic {
            background-size: 200% auto;
            background-image: linear-gradient(to right, #ff512f 0%, #dd2476 51%, #ff512f 100%);
            transition: 0.5s;
        }
        .btn-magic:hover {
            background-position: right center;
            transform: scale(1.05);
        }
        .btn-magic:active {
            transform: scale(0.95);
        }
        
        /* 隱藏捲軸 */
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="h-screen w-screen flex flex-col items-center justify-center text-gray-800 p-4">

    <!-- 幽默標題 -->
    <div class="text-center mb-8">
        <h1 class="text-4xl md:text-6xl font-black text-transparent bg-clip-text bg-gradient-to-r from-purple-600 to-pink-600 drop-shadow-md mb-2">
            🎯 命運的俄羅斯輪盤 🎯
        </h1>
        <p id="subtitle" class="text-xl md:text-2xl font-bold text-gray-700 mt-4">
            準備好迎接你的命運了嗎？
        </p>
    </div>

    <!-- 顯示名字的區域 -->
    <div id="nameDisplayContainer" class="w-full max-w-2xl bg-white rounded-3xl p-8 md:p-16 mb-10 name-box flex items-center justify-center min-h-[250px] border-4 border-yellow-400 relative overflow-hidden">
        <!-- 裝飾用背景文字 -->
        <div class="absolute inset-0 opacity-5 flex items-center justify-center text-9xl font-black select-none pointer-events-none">
            ?
        </div>
        
        <h2 id="nameDisplay" class="text-6xl md:text-8xl font-black text-red-600 tracking-widest text-center z-10">
            誰是祭品
        </h2>
    </div>

    <!-- 控制按鈕 -->
    <button id="drawBtn" onclick="startDraw()" class="btn-magic text-white font-black text-2xl md:text-4xl py-4 px-12 rounded-full shadow-[0_10px_20px_rgba(221,36,118,0.5)] border-4 border-white cursor-pointer select-none">
        👇 按下去，決定命運！
    </button>

    <!-- 幽默註腳 -->
    <p class="mt-8 text-sm md:text-base text-gray-600 font-bold opacity-70">
        免責聲明：本機器保證絕對(不)公平，抽中請自行認命，不要打工程師。
    </p>

    <script>
        // 您提供的名單
        const names = [
            "林佑穎", "嚴培心", "邱語真", "吳廷蔚", "李書驊", "馮焱玲", "郭芊妤", "許華珍", 
            "黃翊庭", "洪藝書", "陳清如", "巫品佳", "林祐亘", "溫芸玄", "劉峻羽", "楊于萱", 
            "鄧伊婷", "林雨蒨", "潘芊妤", "王俐捷", "陳是寧", "莊捷涵", "陳楷薇", "陳玟心", 
            "林品亨", "劉家槥", "邱凡純", "陳綮曜", "李銘杰", "黃阡瑜", "林沛涵", "蘇佑丞", 
            "蘇佑葳", "黃芷嫻", "李依倫", "葉芊惠", "謝秀慧", "何詠貽", "陳韋仲", "楊弘運", 
            "陳映", "吳柔葳", "林文婷"
        ];

        // 幽默的台詞庫 (抽中時隨機顯示)
        const funnyQuotes = [
            "天啊！怎麼會是你！😂",
            "命中注定躲不掉，認命吧！🎯",
            "這就是傳說中的天選之子嗎？✨",
            "大家都在笑你，你看到了嗎？🤣",
            "快去買樂透！運氣全用在這了！🎰",
            "神明說：『就是你了！』🙏",
            "恭喜老爺！賀喜夫人！🎉",
            "不要懷疑，你的螢幕沒壞，真的是你！👀",
            "我就知道是你！(其實我不知道) 🤫"
        ];

        let drawInterval;
        let isDrawing = false;
        
        const nameDisplay = document.getElementById('nameDisplay');
        const nameDisplayContainer = document.getElementById('nameDisplayContainer');
        const drawBtn = document.getElementById('drawBtn');
        const subtitle = document.getElementById('subtitle');

        function startDraw() {
            if (isDrawing) return; // 正在抽籤就不能再按
            
            isDrawing = true;
            drawBtn.innerText = "😵 命運轉盤狂飆中... 😵";
            drawBtn.classList.add("opacity-50", "cursor-not-allowed");
            subtitle.innerText = "神明正在為您擲筊...";
            
            // 移除可能存在的結果特效
            nameDisplay.classList.remove('pop');
            nameDisplay.classList.remove('text-purple-600');
            nameDisplay.classList.add('text-red-600');
            
            // 加上震動特效
            nameDisplayContainer.classList.add('shake');

            // 瘋狂洗牌特效
            let counter = 0;
            drawInterval = setInterval(() => {
                const randomName = names[Math.floor(Math.random() * names.length)];
                nameDisplay.innerText = randomName;
                
                // 隨機變換顏色增加刺激感
                const colors = ['#EF4444', '#3B82F6', '#10B981', '#F59E0B', '#8B5CF6'];
                nameDisplay.style.color = colors[Math.floor(Math.random() * colors.length)];
            }, 50); // 每 50 毫秒換一次名字

            // 3秒後停止抽籤並公佈結果
            setTimeout(() => {
                stopDraw();
            }, 3000);
        }

        function stopDraw() {
            clearInterval(drawInterval);
            
            // 隨機決定最終贏家
            const finalWinner = names[Math.floor(Math.random() * names.length)];
            const randomQuote = funnyQuotes[Math.floor(Math.random() * funnyQuotes.length)];
            
            // 更新 UI
            nameDisplay.innerText = finalWinner;
            nameDisplay.style.color = ''; // 清除隨機顏色
            nameDisplay.classList.add('text-purple-600'); // 最終顯示紫色
            subtitle.innerText = randomQuote;
            
            // 切換特效
            nameDisplayContainer.classList.remove('shake');
            
            // 強制重繪以觸發動畫
            void nameDisplay.offsetWidth; 
            nameDisplay.classList.add('pop');

            // 放超大煙火！
            fireworks();

            // 恢復按鈕狀態
            setTimeout(() => {
                isDrawing = false;
                drawBtn.innerText = "🤔 不信邪？再抽一次！";
                drawBtn.classList.remove("opacity-50", "cursor-not-allowed");
            }, 1000);
        }

        // 煙火特效邏輯 (基於 canvas-confetti)
        function fireworks() {
            var duration = 3 * 1000; // 煙火持續 3 秒
            var animationEnd = Date.now() + duration;
            var defaults = { startVelocity: 30, spread: 360, ticks: 60, zIndex: 0 };

            function randomInRange(min, max) {
                return Math.random() * (max - min) + min;
            }

            var interval = setInterval(function() {
                var timeLeft = animationEnd - Date.now();

                if (timeLeft <= 0) {
                    return clearInterval(interval);
                }

                var particleCount = 50 * (timeLeft / duration);
                
                // 從左右兩側隨機噴發
                confetti(Object.assign({}, defaults, { 
                    particleCount, 
                    origin: { x: randomInRange(0.1, 0.3), y: Math.random() - 0.2 },
                    colors: ['#ff0000', '#00ff00', '#0000ff', '#ffff00', '#ff00ff']
                }));
                confetti(Object.assign({}, defaults, { 
                    particleCount, 
                    origin: { x: randomInRange(0.7, 0.9), y: Math.random() - 0.2 },
                    colors: ['#ff0000', '#00ff00', '#0000ff', '#ffff00', '#ff00ff']
                }));
            }, 250);

            // 中間一次大爆炸
            confetti({
                particleCount: 150,
                spread: 100,
                origin: { y: 0.6 },
                colors: ['#FFD700', '#FF8C00', '#FF0000']
            });
        }
    </script>
</body>
</html>
