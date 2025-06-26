<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>某个人正在看着</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background: #000;
            overflow: hidden;
            font-family: Arial, sans-serif;
            position: relative;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        /* 星空背景 */
        #stars-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1;
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

        /* 流星效果 */
        .shooting-star {
            position: absolute;
            width: 4px;
            height: 4px;
            background: linear-gradient(45deg, #fff, transparent);
            border-radius: 50%;
            animation: shoot 3s linear infinite;
        }

        @keyframes shoot {
            0% {
                transform: translateX(0) translateY(0);
                opacity: 1;
            }
            100% {
                transform: translateX(-300px) translateY(300px);
                opacity: 0;
            }
        }

        /* 对话框 */
        #dialogue-container {
            position: relative;
            z-index: 10;
            text-align: center;
            max-width: 90%;
            padding: 20px;
        }

        .dialogue-box {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border: 2px solid rgba(255, 255, 255, 0.3);
            border-radius: 20px;
            padding: 30px 20px;
            color: white;
            font-size: 18px;
            line-height: 1.6;
            box-shadow: 0 0 30px rgba(255, 255, 255, 0.5);
            animation: glow 2s ease-in-out infinite alternate;
            display: none;
        }

        .dialogue-box.active {
            display: block;
            animation: fadeIn 0.5s ease-out, glow 2s ease-in-out infinite alternate;
        }

        @keyframes fadeIn {
            from {
                opacity: 0;
                transform: scale(0.8) translateY(20px);
            }
            to {
                opacity: 1;
                transform: scale(1) translateY(0);
            }
        }

        @keyframes glow {
            from { box-shadow: 0 0 30px rgba(255, 255, 255, 0.5); }
            to { box-shadow: 0 0 50px rgba(255, 255, 255, 0.8), 0 0 100px rgba(255, 255, 255, 0.4); }
        }

        .click-hint {
            color: rgba(255, 255, 255, 0.7);
            font-size: 14px;
            margin-top: 20px;
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0%, 100% { opacity: 0.7; }
            50% { opacity: 1; }
        }

        /* 震撼特效 */
        .shock-effect {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 0;
            height: 0;
            border-radius: 50%;
            background: radial-gradient(circle, rgba(255,255,255,0.8) 0%, transparent 70%);
            animation: shockwave 1s ease-out;
            z-index: 100;
            pointer-events: none;
        }

        @keyframes shockwave {
            to {
                width: 200vw;
                height: 200vw;
                opacity: 0;
            }
        }

        /* 彩虹文字效果 */
        .rainbow-text {
            background: linear-gradient(45deg, #ff0000, #ff7f00, #ffff00, #00ff00, #0000ff, #4b0082, #9400d3);
            background-size: 200% 200%;
            -webkit-background-clip: text;
            background-clip: text;
            -webkit-text-fill-color: transparent;
            animation: rainbow 3s ease-in-out infinite;
            font-size: 24px;
            font-weight: bold;
        }

        @keyframes rainbow {
            0%, 100% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
        }

        /* 粒子爆炸效果 */
        .particle {
            position: absolute;
            width: 5px;
            height: 5px;
            background: #fff;
            border-radius: 50%;
            pointer-events: none;
        }

        /* 手机适配 */
        @media (max-width: 768px) {
            .dialogue-box {
                font-size: 16px;
                padding: 20px 15px;
            }
            .rainbow-text {
                font-size: 20px;
            }
        }
    </style>
</head>
<body>
    <div id="stars-container"></div>
    
    <div id="dialogue-container">
        <div class="dialogue-box active" id="dialogue-1">
            <p>嗨，黄子琳！👋</p>
            <p>又来了？</p>
            <p class="click-hint">点击继续...</p>
        </div>
        
        <div class="dialogue-box" id="dialogue-2">
            <p>不是过完生日了吗？🎂</p>
            <p>怎么，还想再来一次？</p>
            <p class="click-hint">点击继续...</p>
        </div>
        
        <div class="dialogue-box" id="dialogue-3">
            <p>等等...让我看看日历 📅</p>
            <p>今天好像不是你的生日啊！</p>
            <p class="click-hint">点击继续...</p>
        </div>
        
        <div class="dialogue-box" id="dialogue-4">
            <p>哈哈哈哈哈！😂</p>
            <p class="rainbow-text">还想过生日，你在想屁吃？</p>
            <p>乖乖等下一次吧！</p>
            <p style="font-size: 14px; margin-top: 15px;">（明年见啦～）✨</p>
            <p class="click-hint">点击继续...</p>
        </div>
        
        <div class="dialogue-box" id="dialogue-5">
            <p>明年再来吧，不用再点了。</p>
            <p class="click-hint">点击继续...</p>
        </div>
        
        <div class="dialogue-box" id="dialogue-6">
            <p>明年再来吧，不用再点了。</p>
            <p class="click-hint">点击继续...</p>
        </div>
        
        <div class="dialogue-box" id="dialogue-7">
            <p>明年再来吧，不用再点了。</p>
            <p class="click-hint">点击继续...</p>
        </div>
        
        <div class="dialogue-box" id="dialogue-8">
            <p>明年再来吧，不用再点了。</p>
            <p class="click-hint">点击继续...</p>
        </div>
        
        <div class="dialogue-box" id="dialogue-9">
            <p>明年再来吧，不用再点了。</p>
            <p class="click-hint">点击继续...</p>
        </div>
        
        <div class="dialogue-box" id="dialogue-10">
            <p>明年再来吧，不用再点了。</p>
            <p class="click-hint">点击继续...</p>
        </div>
        
        <div class="dialogue-box" id="dialogue-11">
            <p>真是执着呢，又是这样，那我只能再放一点东西了 😏</p>
            <p class="click-hint">点击继续...</p>
        </div>
        
        <div class="dialogue-box" id="dialogue-12">
            <p>嫌人生太慢？下个生日太慢了？</p>
            <p class="rainbow-text">这里你可以体验不同的人生。</p>
            <p class="click-hint">点击进入游戏 🎮</p>
        </div>
    </div>

    <script>
        // 创建星空
        function createStars() {
            const container = document.getElementById('stars-container');
            const starCount = 150;
            
            for (let i = 0; i < starCount; i++) {
                const star = document.createElement('div');
                star.className = 'star';
                star.style.left = Math.random() * 100 + '%';
                star.style.top = Math.random() * 100 + '%';
                star.style.animationDelay = Math.random() * 3 + 's';
                star.style.animationDuration = (Math.random() * 3 + 2) + 's';
                container.appendChild(star);
            }
            
            // 创建流星
            setInterval(() => {
                const shootingStar = document.createElement('div');
                shootingStar.className = 'shooting-star';
                shootingStar.style.left = Math.random() * 100 + '%';
                shootingStar.style.top = Math.random() * 50 + '%';
                container.appendChild(shootingStar);
                
                setTimeout(() => {
                    shootingStar.remove();
                }, 3000);
            }, 2000);
        }

        // 对话管理
        let currentDialogue = 1;
        const totalDialogues = 12;

        function showNextDialogue() {
            if (currentDialogue < totalDialogues) {
                // 创建震撼波效果
                createShockwave();
                
                // 隐藏当前对话
                document.getElementById(`dialogue-${currentDialogue}`).classList.remove('active');
                currentDialogue++;
                
                // 显示下一个对话
                setTimeout(() => {
                    document.getElementById(`dialogue-${currentDialogue}`).classList.add('active');
                    
                    // 第4个对话时创建粒子爆炸
                    if (currentDialogue === 4) {
                        createParticleExplosion();
                    }
                    // 最后一个对话时跳转到游戏
                    if (currentDialogue === totalDialogues) {
                        setTimeout(() => {
                            window.open('https://game.hqstar.cn', '_blank');
                        }, 2000);
                    }
                }, 300);
            }
        }

        // 震撼波效果
        function createShockwave() {
            const shockwave = document.createElement('div');
            shockwave.className = 'shock-effect';
            document.body.appendChild(shockwave);
            
            setTimeout(() => {
                shockwave.remove();
            }, 1000);
        }

        // 粒子爆炸效果
        function createParticleExplosion() {
            const particleCount = 50;
            const centerX = window.innerWidth / 2;
            const centerY = window.innerHeight / 2;
            
            for (let i = 0; i < particleCount; i++) {
                const particle = document.createElement('div');
                particle.className = 'particle';
                particle.style.left = centerX + 'px';
                particle.style.top = centerY + 'px';
                
                const angle = (Math.PI * 2 * i) / particleCount;
                const velocity = Math.random() * 200 + 100;
                const color = `hsl(${Math.random() * 360}, 100%, 50%)`;
                particle.style.background = color;
                particle.style.boxShadow = `0 0 10px ${color}`;
                
                document.body.appendChild(particle);
                
                // 动画
                let distance = 0;
                const animate = () => {
                    distance += velocity / 60;
                    particle.style.transform = `translate(${Math.cos(angle) * distance}px, ${Math.sin(angle) * distance}px)`;
                    particle.style.opacity = 1 - distance / 300;
                    
                    if (distance < 300) {
                        requestAnimationFrame(animate);
                    } else {
                        particle.remove();
                    }
                };
                
                requestAnimationFrame(animate);
            }
        }

        // 初始化
        createStars();

        // 点击事件
        document.addEventListener('click', (e) => {
            if (e.target.closest('.dialogue-box')) {
                showNextDialogue();
            }
        });

        // 触摸事件（移动端）
        document.addEventListener('touchstart', (e) => {
            if (e.target.closest('.dialogue-box')) {
                e.preventDefault();
                showNextDialogue();
            }
        });
    </script>
</body>
</html>
