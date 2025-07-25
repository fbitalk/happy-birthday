<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>黄子琳的生日惊喜 🎂</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Arial, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            overflow: hidden;
            position: relative;
        }

        /* 星星背景 */
        .stars {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
        }

        .star {
            position: absolute;
            width: 2px;
            height: 2px;
            background: white;
            border-radius: 50%;
            animation: twinkle 3s infinite;
        }

        @keyframes twinkle {
            0%, 100% { opacity: 0; transform: scale(0.5); }
            50% { opacity: 1; transform: scale(1); }
        }

        /* 主容器 */
        .container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            padding: 20px;
            position: relative;
            z-index: 10;
        }

        /* 欢迎页面 */
        .welcome-screen {
            text-align: center;
            animation: fadeIn 1s ease-out;
        }

        .welcome-title {
            font-size: 2.5rem;
            color: white;
            margin-bottom: 30px;
            text-shadow: 0 0 30px rgba(255,255,255,0.5);
            animation: glow 2s ease-in-out infinite alternate;
        }

        @keyframes glow {
            from { text-shadow: 0 0 30px rgba(255,255,255,0.5); }
            to { text-shadow: 0 0 40px rgba(255,255,255,0.8), 0 0 50px rgba(255,255,255,0.5); }
        }

        /* 礼物盒子 */
        .gift-box {
            width: 150px;
            height: 150px;
            position: relative;
            cursor: pointer;
            transform-style: preserve-3d;
            animation: float 3s ease-in-out infinite;
            user-select: none;
        }

        @keyframes float {
            0%, 100% { transform: translateY(0) rotateY(0deg); }
            50% { transform: translateY(-20px) rotateY(180deg); }
        }

        .box {
            width: 100%;
            height: 100px;
            background: linear-gradient(45deg, #ff6b6b, #ee5a6f);
            border-radius: 10px;
            position: absolute;
            bottom: 0;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
        }

        .lid {
            width: 110%;
            height: 30px;
            background: linear-gradient(45deg, #ff8787, #ff6b6b);
            border-radius: 10px;
            position: absolute;
            top: -15px;
            left: -5%;
            transform-origin: bottom;
            transition: transform 0.5s;
            box-shadow: 0 5px 20px rgba(0,0,0,0.3);
        }

        .ribbon {
            position: absolute;
            width: 30px;
            height: 100%;
            background: gold;
            left: 50%;
            transform: translateX(-50%);
            top: -15px;
            height: 115px;
        }

        .ribbon::before {
            content: '';
            position: absolute;
            width: 60px;
            height: 60px;
            background: gold;
            top: -20px;
            left: 50%;
            transform: translateX(-50%) rotate(45deg);
            border-radius: 10px;
        }

        .gift-box:hover .lid {
            transform: rotateX(-45deg);
        }

        .gift-box:active {
            transform: scale(0.95);
        }

        /* 点击提示 */
        .click-hint {
            color: white;
            font-size: 1.2rem;
            margin-top: 40px;
            opacity: 0.8;
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); opacity: 0.8; }
            50% { transform: scale(1.05); opacity: 1; }
        }

        /* 惊喜内容 */
        .surprise-content {
            display: none;
            text-align: center;
            animation: slideUp 0.8s ease-out;
        }

        .surprise-content.show {
            display: block;
        }

        @keyframes slideUp {
            from { transform: translateY(50px); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }

        .birthday-message {
            background: rgba(255, 255, 255, 0.95);
            padding: 30px;
            border-radius: 20px;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
            max-width: 90vw;
            backdrop-filter: blur(10px);
        }

        .message-title {
            font-size: 2rem;
            color: #764ba2;
            margin-bottom: 20px;
            animation: bounce 1s ease-out;
        }

        @keyframes bounce {
            0%, 20%, 50%, 80%, 100% { transform: translateY(0); }
            40% { transform: translateY(-30px); }
            60% { transform: translateY(-15px); }
        }

        .message-text {
            font-size: 1.1rem;
            color: #555;
            line-height: 1.8;
            margin-bottom: 25px;
        }

        /* 蛋糕 */
        .cake {
            font-size: 5rem;
            margin: 20px 0;
            animation: spin 3s linear infinite;
        }

        @keyframes spin {
            from { transform: rotate(0deg); }
            to { transform: rotate(360deg); }
        }

        /* 按钮 */
        .surprise-btn {
            background: linear-gradient(45deg, #667eea, #764ba2);
            color: white;
            border: none;
            padding: 15px 40px;
            font-size: 1.1rem;
            border-radius: 50px;
            cursor: pointer;
            transition: all 0.3s;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            margin: 10px;
        }

        .surprise-btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 15px 40px rgba(0,0,0,0.3);
        }

        .surprise-btn:disabled {
            background: #ccc;
            cursor: not-allowed;
            transform: none;
        }

        /* 烟花效果 */
        .firework {
            position: absolute;
            width: 5px;
            height: 5px;
            border-radius: 50%;
            animation: firework 1s ease-out forwards;
            z-index: 1000;
        }

        @keyframes firework {
            0% {
                transform: translate(0, 0) scale(1);
                opacity: 1;
            }
            100% {
                transform: translate(var(--x), var(--y)) scale(0);
                opacity: 0;
            }
        }

        /* 漂浮的心形 */
        .heart {
            position: absolute;
            color: #ff6b6b;
            font-size: 2rem;
            animation: floatHeart 3s ease-out forwards;
            pointer-events: none;
            z-index: 999;
        }

        @keyframes floatHeart {
            0% {
                transform: translateY(0) rotate(0deg);
                opacity: 1;
            }
            100% {
                transform: translateY(-100vh) rotate(360deg);
                opacity: 0;
            }
        }

        /* 彩带 */
        .confetti {
            position: absolute;
            width: 10px;
            height: 20px;
            background: #f39c12;
            animation: confetti 3s ease-out forwards;
            z-index: 999;
        }

        @keyframes confetti {
            0% {
                transform: translateY(-100vh) rotate(0deg);
                opacity: 1;
            }
            100% {
                transform: translateY(100vh) rotate(720deg);
                opacity: 0;
            }
        }

        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        .hidden {
            display: none !important;
        }
    </style>
</head>
<body>
    <div class="stars"></div>
    
    <div class="container">
        <!-- 欢迎页面 -->
        <div class="welcome-screen" id="welcomeScreen">
            <h1 class="welcome-title">🎉 有一份神秘礼物 🎉</h1>
            <div class="gift-box" id="giftBox">
                <div class="ribbon"></div>
                <div class="lid"></div>
                <div class="box"></div>
            </div>
            <p class="click-hint">点击打开礼物盒 ✨</p>
        </div>

        <!-- 惊喜内容 -->
        <div class="surprise-content" id="surpriseContent">
            <div class="birthday-message">
                <div class="cake">🎂</div>
                <h2 class="message-title" id="messageTitle">祝黄子琳小朋友生日快乐！</h2>
                <p class="message-text" id="messageText">
                    今天是你的特别日子，愿你的每一天都充满阳光和欢笑！<br>
                    愿你像我一样，永远快乐、永远可爱！
                </p>
                <button class="surprise-btn" id="nextBtn">🎁 打开下一个惊喜</button>
                <button class="surprise-btn" id="fireworkBtn">🎆 放烟花</button>
            </div>
        </div>
    </div>

    <script>
        // 生成星星背景
        function createStars() {
            const starsContainer = document.querySelector('.stars');
            for (let i = 0; i < 100; i++) {
                const star = document.createElement('div');
                star.className = 'star';
                star.style.left = Math.random() * 100 + '%';
                star.style.top = Math.random() * 100 + '%';
                star.style.animationDelay = Math.random() * 3 + 's';
                starsContainer.appendChild(star);
            }
        }

        // 惊喜消息数组
        const surprises = [
            {
                title: "🌟 子琳是最棒的！",
                text: "你的笑容像阳光一样灿烂<br>照亮了所有人的心！"
            },
            {
                title: "🦄 梦想成真！",
                text: "愿你的每一个愿望都能实现<br>每一个梦想都能开花！"
            },
            {
                title: "🌈 彩虹般的未来！",
                text: "愿你的人生像彩虹一样绚烂<br>充满无限可能！"
            },
            {
                title: "👑 公主的祝福！",
                text: "愿你永远保持童真 <br> 快乐地成长每一天！"
            },
            {
                title: "🎨 创造奇迹！",
                text: "你是独一无二的宝贝<br>未来由你来创造！"
            },
            {
                title: " 最后一句！",
                text: "你的祝福已全部到账<br> 开始你的新一岁吧！"
            },
            {
                title: " 不要再点了！",
                text: "已经没有了<br>退出吧！"
            },
            {
                title: " 为什么还点！",
                text: "已经没有了<br>退出吧！"
            },
            {
                title: " 你真闲！",
                text: "不会有人还无聊的点这个吧<br>不会吧！"
            },
            {
                title: " 真的什么都没有了！",
                text: "你的生日<br>所以你可以开心忆点点点！"
            },
            {
                title: " 真的什么都没有了！",
                text: "你的生日<br>所以你可以开心忆点点点！"
            },
            {
                title: " 真的什么都没有了！",
                text: "你的生日<br>所以你可以开心忆点点点！"
            },
            {
                title: " 真的什么都没有了！",
                text: "你的生日<br>所以你可以开心忆点点点！"
            },
            {
                title: " 真的什么都没有了！",
                text: "你的生日<br>所以你可以开心忆点点点！"
            },
            {
                title: " 真的什么都没有了！",
                text: "你的生日<br>所以你可以开心忆点点点！"
            },
            {
                title: " 😂😂😂",
                text: "天哪，你真的忆点点点了<br>居然点了这么多次！"
            },
            {
                title: " 不知道该说什么！",
                text: "你的生日<br>所以你可以开心忆点点点！"
            },
            {
                title: "🎨 隐藏惊喜！",
                text: "算了，给你这个吧<br>这是隐藏惊喜！"
            },
            {
                title: " 笑笑！",
                text: "恭喜你找到了我写的祝福<br>祝你生日快乐！"
            }
        ];

        let currentSurprise = 0;

        // 打开礼物
        function openGift() {
            console.log('礼物盒被点击了！'); // 调试信息
            const welcomeScreen = document.getElementById('welcomeScreen');
            const surpriseContent = document.getElementById('surpriseContent');
            
            welcomeScreen.classList.add('hidden');
            surpriseContent.classList.add('show');
            
            createHearts();
            createConfetti();
            playSound();
        }

        // 下一个惊喜
        function nextSurprise() {
            currentSurprise++;
            
            if (currentSurprise >= surprises.length) {
                const nextBtn = document.getElementById('nextBtn');
                nextBtn.disabled = true;
                nextBtn.textContent = '🎁 惊喜已全部展示完毕';
                return;
            }
            
            const surprise = surprises[currentSurprise];
            
            document.getElementById('messageTitle').innerHTML = surprise.title;
            document.getElementById('messageText').innerHTML = surprise.text;
            
            // 添加动画效果
            const message = document.querySelector('.birthday-message');
            message.style.animation = 'none';
            setTimeout(() => {
                message.style.animation = 'slideUp 0.8s ease-out';
            }, 10);
            
            createHearts();
            playSound();
            
            if (currentSurprise === surprises.length - 1) {
                const nextBtn = document.getElementById('nextBtn');
                nextBtn.textContent = '🎁 最后一个惊喜了';
            }
        }

        // 创建烟花
        function createFireworks() {
            const colors = ['#ff6b6b', '#4ecdc4', '#45b7d1', '#96ceb4', '#feca57', '#ff9ff3'];
            
            for (let i = 0; i < 30; i++) {
                const firework = document.createElement('div');
                firework.className = 'firework';
                firework.style.left = '50%';
                firework.style.top = '50%';
                firework.style.background = colors[Math.floor(Math.random() * colors.length)];
                
                const angle = (Math.PI * 2 * i) / 30;
                const velocity = 100 + Math.random() * 200;
                firework.style.setProperty('--x', Math.cos(angle) * velocity + 'px');
                firework.style.setProperty('--y', Math.sin(angle) * velocity + 'px');
                
                document.body.appendChild(firework);
                
                setTimeout(() => {
                    if (firework.parentNode) {
                        firework.remove();
                    }
                }, 1000);
            }
            playSound();
        }

        // 创建漂浮的心
        function createHearts() {
            const hearts = ['❤️', '💕', '💖', '💗', '💝'];
            for (let i = 0; i < 10; i++) {
                setTimeout(() => {
                    const heart = document.createElement('div');
                    heart.className = 'heart';
                    heart.innerHTML = hearts[Math.floor(Math.random() * hearts.length)];
                    heart.style.left = Math.random() * 100 + '%';
                    heart.style.bottom = '0';
                    document.body.appendChild(heart);
                    setTimeout(() => {
                        if (heart.parentNode) {
                            heart.remove();
                        }
                    }, 3000);
                }, i * 200);
            }
        }

        // 创建彩带
        function createConfetti() {
            const colors = ['#ff6b6b', '#4ecdc4', '#45b7d1', '#96ceb4', '#feca57'];
            for (let i = 0; i < 20; i++) {
                setTimeout(() => {
                    const confetti = document.createElement('div');
                    confetti.className = 'confetti';
                    confetti.style.left = Math.random() * 100 + '%';
                    confetti.style.background = colors[Math.floor(Math.random() * colors.length)];
                    confetti.style.transform = `rotate(${Math.random() * 360}deg)`;
                    document.body.appendChild(confetti);
                    setTimeout(() => {
                        if (confetti.parentNode) {
                            confetti.remove();
                        }
                    }, 3000);
                }, i * 100);
            }
        }

        // 播放音效（模拟）
        function playSound() {
            document.body.style.filter = 'brightness(1.2)';
            setTimeout(() => {
                document.body.style.filter = 'brightness(1)';
            }, 200);
        }

        // 页面加载完成后初始化
        document.addEventListener('DOMContentLoaded', function() {
            console.log('页面加载完成');
            
            // 创建星星背景
            createStars();
            
            // 添加事件监听器
            const giftBox = document.getElementById('giftBox');
            const nextBtn = document.getElementById('nextBtn');
            const fireworkBtn = document.getElementById('fireworkBtn');
            
            if (giftBox) {
                giftBox.addEventListener('click', openGift);
                giftBox.addEventListener('touchstart', openGift);
                console.log('礼物盒事件监听器已添加');
            }
            
            if (nextBtn) {
                nextBtn.addEventListener('click', nextSurprise);
            }
            
            if (fireworkBtn) {
                fireworkBtn.addEventListener('click', createFireworks);
            }
        });

        // 添加触摸反馈
        document.addEventListener('touchstart', function(e) {
            const touch = e.touches[0];
            const sparkle = document.createElement('div');
            sparkle.style.position = 'absolute';
            sparkle.style.left = touch.pageX + 'px';
            sparkle.style.top = touch.pageY + 'px';
            sparkle.style.width = '20px';
            sparkle.style.height = '20px';
            sparkle.style.background = 'radial-gradient(circle, rgba(255,255,255,0.8) 0%, transparent 70%)';
            sparkle.style.borderRadius = '50%';
            sparkle.style.pointerEvents = 'none';
            sparkle.style.animation = 'fadeIn 0.5s ease-out forwards';
            sparkle.style.zIndex = '9999';
            document.body.appendChild(sparkle);
            setTimeout(() => {
                if (sparkle.parentNode) {
                    sparkle.remove();
                }
            }, 500);
        });
    </script>
</body>
</html>
