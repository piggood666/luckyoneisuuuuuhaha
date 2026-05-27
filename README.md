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
    <!-- 引入好玩的字體 (海報風格的 Dela Gothic One) -->
    <link href="https://fonts.googleapis.com/css2?family=Dela+Gothic+One&family=Noto+Sans+TC:wght@900&display=swap" rel="stylesheet">
    
    <style>
        body {
            font-family: 'Noto Sans TC', sans-serif;
            /* 幽默且豐富的四色動態漸層背景 */
            background: linear-gradient(-45deg, #ee7752, #e73c7e, #23a6d5, #23d5ab);
            background-size: 400% 400%;
            animation: gradientBG 15s ease infinite;
            overflow: hidden; /* 防止煙火造成捲軸 */
        }

        @keyframes gradientBG {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        /* 漂浮表情符號特效 */
        .floating-emoji {
            position: absolute;
            font-size: 2.5rem;
            opacity: 0.4;
            animation: floatUp linear infinite;
            z-index: -1;
            user-select: none;
            pointer-events: none;
        }

        @keyframes floatUp {
            0% { transform: translateY(100vh) rotate(0deg) scale(0.8); opacity: 0; }
            10% { opacity: 0.6; }
            90% { opacity: 0.6; }
            100% { transform: translateY(-10vh) rotate(360deg) scale(1.2); opacity: 0; }
        }

        .name-box {
            box-shadow: 0 15px 35px rgba(0,0,0,0.3), inset 0 0 20px rgba(255,255,255,0.6);
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

        /* 日式海報風格標題（正常舒適微調放大版） */
        .title-poster-style {
            font-family: 'Dela Gothic One', 'Noto Sans TC', sans-serif;
            color: #FFF200; /* 亮鮮黃 */
            display: inline-block;
            transform: none; 
            -webkit-text-stroke: 2.8px #000000; /* 微調描邊配合字體稍微放大 */
            /* 重疊的複古波普風 3D 立體陰影 */
            text-shadow: 
                3px 3px 0px #000000,
                -1px -1px 0px #000000,
                1px -1px 0px #000000,
                -1px 1px 0px #000000,
                5px 5px 0px #FF3366,   /* 霓虹粉 */
                8px 8px 0px #00E5FF,   /* 科技青 */
                10px 10px 18px rgba(0,0,0,0.5);
            letter-spacing: 4px; /* 調整字距，更顯精緻 */
            animation: titlePulse 2s ease-in-out infinite alternate;
        }

        @keyframes titlePulse {
            0% { transform: scale(0.97); }
            100% { transform: scale(1.03); }
        }

        /* 🎯 符號的活潑搖擺效果 */
        .emoji-active {
            display: inline-block;
            animation: emojiWiggle 0.8s ease-in-out infinite alternate;
            filter: drop-shadow(3px 3px 0px rgba(0,0,0,0.4));
        }

        @keyframes emojiWiggle {
            0% { transform: rotate(-12deg) scale(0.95); }
            100% { transform: rotate(12deg) scale(1.1); }
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
<body class="h-screen w-screen flex flex-col items-center justify-center text-gray-800 p-4 relative z-0">

    <!-- 漂浮背景容器 -->
    <div id="bg-container" class="absolute inset-0 overflow-hidden -z-10 pointer-events-none"></div>

    <!-- 幽默標題 -->
    <div class="text-center mb-10 mt-4 select-none">
        <h1 class="text-5xl md:text-6xl lg:text-7xl font-black mb-2 flex flex-wrap items-center justify-center gap-3 md:gap-5">
            <span class="emoji-active text-5xl md:text-7xl lg:text-8xl">🎯</span>
            <span class="title-poster-style px-5 py-2">命運的輪盤</span>
            <span class="emoji-active text-5xl md:text-7xl lg:text-8xl">🎯</span>
        </h1>
        <p id="subtitle" class="text-lg md:text-2xl font-bold text-gray-800 mt-6 bg-white/70 backdrop-blur-sm inline-block px-6 py-2 rounded-full shadow-sm">
            準備好迎接你的命運了嗎？
        </p>
    </div>

    <!-- 顯示名字的區域 -->
    <div id="nameDisplayContainer" class="w-full max-w-2xl bg-white rounded-3xl p-8 md:p-16 mb-10 name-box flex flex-col items-center justify-center min-h-[250px] border-4 border-yellow-400 relative overflow-hidden">
        <!-- 裝飾用背景文字 -->
        <div class="absolute inset-0 opacity-5 flex items-center justify-center text-9xl font-black select-none pointer-events-none">
            ?
        </div>
        
        <h2 id="nameDisplay" class="text-6xl md:text-8xl font-black text-red-600 tracking-widest text-center z-10">
            誰是幸運兒
        </h2>
    </div>

    <!-- 控制按鈕 -->
    <button id="drawBtn" onclick="startDraw()" class="btn-magic text-white font-black text-2xl md:text-4xl py-5 px-14 rounded-full shadow-[0_12px_24px_rgba(221,36,118,0.6)] border-4 border-white cursor-pointer select-none">
        👇 按下去，決定命運！
    </button>

    <!-- 幽默註腳 -->
    <p class="mt-8 text-sm md:text-base text-white font-bold opacity-80 drop-shadow-md bg-black/30 px-4 py-1 rounded-full">
        免責聲明：本機器保證絕對(不)公平，抽中請自行認命，不要打工程師。
    </p>

    <script>
        // 名單資料 (已剔除：陳玟心、陳清如、巫品佳、陳映、林佑穎)
        const names = [
            "嚴培心", "邱語真", "吳廷蔚", "李書驊", "馮焱玲", "郭芊妤", "許華珍", 
            "黃翊庭", "洪藝書", "林祐亘", "溫芸玄", "劉峻羽", "楊于萱", "鄧伊婷", 
            "林雨蒨", "潘芊妤", "王俐捷", "陳是寧", "莊捷涵", "陳楷薇", "林品亨", 
            "劉家槥", "邱凡純", "陳綮曜", "李銘杰", "黃阡瑜", "林沛涵", "蘇佑丞", 
            "蘇佑葳", "黃芷嫻", "李依倫", "葉芊惠", "謝秀慧", "何詠貽", "陳韋仲", 
            "楊弘运", "吳柔葳", "林文婷"
        ];

        // 剩餘的可抽名單池 (不重複抽籤機制)
        let pool = [...names];

        // 幽默台詞
        const funnyQuotes = [
            "天啊！怎麼會是你！😂",
            "命中注定躲不掉，認命吧！🎯",
            "這就是傳說中的天選之子嗎？✨",
            "大家都在笑你，你看到了嗎？🤣",
            "快去買樂透！運氣全用在這了！🎰",
            "神明說：『就是你了！』🙏",
            "恭喜老爺！賀喜夫人！🎉",
            "不要懷疑，你的螢幕沒壞，就是你！👀",
            "我就知道幕後黑手是你！🤫"
        ];

        let drawInterval;
        let isDrawing = false;
        
        const nameDisplay = document.getElementById('nameDisplay');
        const nameDisplayContainer = document.getElementById('nameDisplayContainer');
        const drawBtn = document.getElementById('drawBtn');
        const subtitle = document.getElementById('subtitle');

        // --- 背景漂浮物系統 ---
        function createFloatingEmojis() {
            const container = document.getElementById('bg-container');
            const emojis = ['🎲', '✨', '🍀', '🎉', '🤑', '💎', '🔥', '🎰'];
            
            setInterval(() => {
                const el = document.createElement('div');
                el.innerText = emojis[Math.floor(Math.random() * emojis.length)];
                el.className = 'floating-emoji';
                el.style.left = Math.random() * 100 + 'vw';
                el.style.animationDuration = (Math.random() * 5 + 5) + 's'; // 5-10秒
                container.appendChild(el);
                
                setTimeout(() => {
                    el.remove();
                }, 10000);
            }, 800);
        }
        
        window.onload = createFloatingEmojis;

        // --- 音效系統 (使用 Web Audio API 原生合成) ---
        let audioCtx;

        // 變更：改回完全同步的初始化函數！徹底保留點擊事件的 Gesture 權限
        function initAudio() {
            try {
                if (!audioCtx) {
                    audioCtx = new (window.AudioContext || window.webkitAudioContext)();
                }
                if (audioCtx && audioCtx.state === 'suspended') {
                    audioCtx.resume();
                }
            } catch (e) {
                console.warn("音訊上下文初始化失敗:", e);
            }
        }

        // 抽籤時的快速跳動音效
        function playBeep() {
            if (!audioCtx) return;
            
            // 自動修復被暫停的狀態
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }
            
            const now = audioCtx.currentTime;
            const osc = audioCtx.createOscillator();
            const gainNode = audioCtx.createGain();
            
            osc.type = 'triangle';
            osc.frequency.setValueAtTime(800, now);
            osc.frequency.exponentialRampToValueAtTime(100, now + 0.05);
            
            gainNode.gain.setValueAtTime(0.5, now);
            gainNode.gain.exponentialRampToValueAtTime(0.001, now + 0.05);
            
            osc.connect(gainNode);
            gainNode.connect(audioCtx.destination);
            
            osc.start(now);
            osc.stop(now + 0.05);
        }

        // 勝利/中獎的登登登登音效
        function playWinSound() {
            if (!audioCtx) return;
            
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }
            
            const now = audioCtx.currentTime;
            const chord = [523.25, 659.25, 783.99, 1046.50]; // C5, E5, G5, C6
            
            chord.forEach((freq, i) => {
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                
                osc.type = (i % 2 === 0) ? 'square' : 'sawtooth';
                osc.frequency.setValueAtTime(freq, now + i * 0.1);
                
                gain.gain.setValueAtTime(0, now + i * 0.1);
                gain.gain.linearRampToValueAtTime(0.4, now + i * 0.1 + 0.05);
                
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now + i * 0.1);
                
                if (i === chord.length - 1) {
                    gain.gain.linearRampToValueAtTime(0.6, now + i * 0.1 + 0.1);
                    gain.gain.exponentialRampToValueAtTime(0.001, now + i * 0.1 + 2.5);
                    osc.stop(now + i * 0.1 + 2.5);
                    
                    // 重低音襯底
                    const bassOsc = audioCtx.createOscillator();
                    const bassGain = audioCtx.createGain();
                    bassOsc.type = 'square';
                    bassOsc.frequency.setValueAtTime(261.63, now + i * 0.1);
                    bassGain.gain.setValueAtTime(0, now + i * 0.1);
                    bassGain.gain.linearRampToValueAtTime(0.5, now + i * 0.1 + 0.1);
                    bassGain.gain.exponentialRampToValueAtTime(0.001, now + i * 0.1 + 2.5);
                    bassOsc.connect(bassGain);
                    bassGain.connect(audioCtx.destination);
                    bassOsc.start(now + i * 0.1);
                    bassOsc.stop(now + i * 0.1 + 2.5);
                } else {
                    gain.gain.exponentialRampToValueAtTime(0.001, now + i * 0.1 + 0.2);
                    osc.stop(now + i * 0.1 + 0.2);
                }
            });
        }

        // 默默重置抽籤名單池的函數 (在背景執行，不干擾使用者)
        function silentResetPool() {
            pool = [...names];
        }

        // 變更：移除了非同步 async/await，改用完全同步的 click 流程，徹底解鎖瀏覽器音訊
        function startDraw() {
            if (isDrawing) return;
            
            // 1. 同步、即時解鎖 Web Audio 權限！(絕對不經過 async/await 微任務打斷)
            initAudio();
            
            // 2. 立即嘗試同步播放第一聲 Beep 聲
            playBeep();

            // 檢查名單是否已抽完，若抽完了則默默自動重置
            if (pool.length === 0) {
                silentResetPool();
            }
            
            isDrawing = true;
            drawBtn.innerText = "😵 命運轉盤狂飆中... 😵";
            drawBtn.classList.add("opacity-50", "cursor-not-allowed");
            
            subtitle.innerText = "系統正在閉著眼睛瞎挑一個中獎人... 🙈";
            
            nameDisplay.classList.remove('pop');
            nameDisplay.classList.remove('text-purple-600');
            nameDisplay.classList.add('text-red-600');
            nameDisplayContainer.classList.add('shake');

            // 瘋狂洗牌特效 (只在剩餘的名單 pool 裡面跑亂數，確保不會閃到已被過濾的人)
            drawInterval = setInterval(() => {
                const randomName = pool[Math.floor(Math.random() * pool.length)];
                nameDisplay.innerText = randomName;
                
                const colors = ['#EF4444', '#3B82F6', '#10B981', '#F59E0B', '#8B5CF6'];
                nameDisplay.style.color = colors[Math.floor(Math.random() * colors.length)];
                
                playBeep();
            }, 60);

            // 3秒後停止抽籤並公佈結果
            setTimeout(() => {
                stopDraw();
            }, 3000);
        }

        function stopDraw() {
            clearInterval(drawInterval);
            
            // 從不重複名單池中抽出並移除一人
            const randomIndex = Math.floor(Math.random() * pool.length);
            const finalWinner = pool.splice(randomIndex, 1)[0];

            const randomQuote = funnyQuotes[Math.floor(Math.random() * funnyQuotes.length)];
            
            nameDisplay.innerText = finalWinner;
            nameDisplay.style.color = '';
            nameDisplay.classList.add('text-purple-600');
            
            // 如果剛好全部抽完，在下一次抽之前先貼心提示
            if (pool.length === 0) {
                subtitle.innerText = `🎉 恭喜壓軸的 ${finalWinner}！名單也剛好功成身退，下一次點擊將自動開啟新的一輪！👏`;
            } else {
                subtitle.innerText = randomQuote;
            }
            
            nameDisplayContainer.classList.remove('shake');
            
            void nameDisplay.offsetWidth; 
            nameDisplay.classList.add('pop');

            // 播放勝利音效與施放煙火
            playWinSound();
            fireworks();

            setTimeout(() => {
                isDrawing = false;
                drawBtn.innerText = "🤔 不信邪？再抽一次！";
                drawBtn.classList.remove("opacity-50", "cursor-not-allowed");
            }, 1000);
        }

        // 煙火特效邏輯 (超浮誇版本)
        function fireworks() {
            var duration = 5 * 1000; // 延長至 5 秒
            var animationEnd = Date.now() + duration;
            var defaults = { startVelocity: 45, spread: 360, ticks: 100, zIndex: 100 };

            function randomInRange(min, max) {
                return Math.random() * (max - min) + min;
            }

            // 開場先來三發超大連爆
            confetti({ particleCount: 250, spread: 120, origin: { y: 0.6, x: 0.5 }, colors: ['#FFD700', '#FF8C00', '#FF0000', '#FFFFFF'], startVelocity: 70 });
            setTimeout(() => confetti({ particleCount: 200, spread: 100, origin: { y: 0.6, x: 0.2 }, colors: ['#00FF00', '#00FFFF', '#FF00FF'], startVelocity: 80 }), 200);
            setTimeout(() => confetti({ particleCount: 200, spread: 100, origin: { y: 0.6, x: 0.8 }, colors: ['#FF00FF', '#FFFF00', '#0000FF'], startVelocity: 80 }), 400);

            var interval = setInterval(function() {
                var timeLeft = animationEnd - Date.now();

                if (timeLeft <= 0) {
                    clearInterval(interval);
                    // 終場超級大爆發
                    confetti({ particleCount: 400, spread: 180, origin: { y: 0.5 }, startVelocity: 60, colors: ['#FFD700', '#FF8C00', '#FF0000', '#FF1493', '#00BFFF'] });
                    return;
                }

                var particleCount = 80 * (timeLeft / duration);
                
                // 左右兩側瘋狂噴發
                confetti(Object.assign({}, defaults, { 
                    particleCount, 
                    origin: { x: randomInRange(0.0, 0.3), y: Math.random() - 0.2 },
                    colors: ['#ff0000', '#00ff00', '#0000ff', '#ffff00', '#ff00ff']
                }));
                confetti(Object.assign({}, defaults, { 
                    particleCount, 
                    origin: { x: randomInRange(0.7, 1.0), y: Math.random() - 0.2 },
                    colors: ['#ff0000', '#00ff00', '#0000ff', '#ffff00', '#ff00ff']
                }));

                // 隨機從底部往上打的煙火
                if (Math.random() > 0.6) {
                    confetti({
                        particleCount: 80,
                        spread: 80,
                        startVelocity: randomInRange(50, 80),
                        origin: { x: randomInRange(0.3, 0.7), y: 1 },
                        colors: ['#FFD700', '#FF1493', '#00FFFF', '#FFFFFF']
                    });
                }
            }, 200); // 縮短間隔，噴得更密集
        }
    </script>
</body>
</html>
