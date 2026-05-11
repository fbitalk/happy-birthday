# 生日网站重设计 - 实现计划

> **For agentic workers:** 使用 superpowers:subagent-driven-development 或 superpowers:executing-plans 按任务执行。步骤使用 `- [ ]` 语法跟踪。

**目标:** 将现有两个页面合并为一个智能单页应用，根据日期自动切换生日/非生日模式，采用游戏化互动风格。

**架构:** 单 HTML 文件，CSS 内联，JS 管理状态机（idle→stage1→stage2→stage3→finale），Canvas 粒子系统，Web Audio 音效，LocalStorage 持久化。

**技术栈:** 纯 HTML/CSS/JS，无外部依赖

---

## 文件结构

- **创建:** `index.html`（全新单文件，覆盖现有）

---

### Task 1: HTML 骨架 + 模式检测 + 状态机

**文件:**
- 创建: `index.html`

- [ ] **Step 1: 写入 HTML 骨架与 CSS 变量**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>🎂 生日快乐</title>
    <style>
        :root {
            --gold: #FFD700;
            --coral: #FF6B6B;
            --neon-green: #00FF88;
            --cool-blue: #4ECDC4;
            --deep-bg: #0a0a1a;
            --card-bg: rgba(255,255,255,0.05);
            --text: #fff;
            --text-secondary: rgba(255,255,255,0.7);
        }
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: 'Comic Neue', 'Segoe UI', cursive, sans-serif;
            background: var(--deep-bg);
            min-height: 100vh;
            overflow-x: hidden;
            position: relative;
            display: flex;
            justify-content: center;
            align-items: center;
            touch-action: manipulation;
            -webkit-tap-highlight-color: transparent;
        }
        canvas#bgCanvas {
            position: fixed; top: 0; left: 0; z-index: 0;
        }
        .main-container {
            position: relative; z-index: 10;
            width: 100%; max-width: 500px;
            min-height: 100vh;
            display: flex; flex-direction: column;
            align-items: center; justify-content: center;
            padding: 20px;
        }
        .screen { display: none; width: 100%; }
        .screen.active { display: flex; flex-direction: column; align-items: center; }
    </style>
</head>
<body>
    <canvas id="bgCanvas"></canvas>
    <div class="main-container" id="mainContainer">
        <!-- 各阶段内容由 JS 动态渲染 -->
    </div>
    <script>
        // === 模式检测 ===
        const BIRTH_MONTH = 6, BIRTH_DAY = 26;
        const now = new Date();
        const isBirthday = now.getMonth() + 1 === BIRTH_MONTH && now.getDate() === BIRTH_DAY;

        // === 状态机 ===
        const STATE = {
            IDLE: 'idle', STAGE1: 'stage1', STAGE2: 'stage2',
            STAGE3: 'stage3', FINALE: 'finale',
            NON_BIRTHDAY: 'non_birthday'
        };
        let currentState = isBirthday ? STATE.IDLE : STATE.NON_BIRTHDAY;

        // === LocalStorage ===
        const LS = {
            get(key, def = 0) {
                try { const v = localStorage.getItem('hb_' + key); return v !== null ? JSON.parse(v) : def; }
                catch(e) { return def; }
            },
            set(key, val) {
                try { localStorage.setItem('hb_' + key, JSON.stringify(val)); } catch(e) {}
            }
        };
        const eggsCollected = LS.get('eggs', 0);
        const visitCount = LS.get('visits', 0);
        LS.set('visits', visitCount + 1);
    </script>
</body>
</html>
```

- [ ] **Step 2: 添加模式检测逻辑和状态机初始化**

在 script 末尾追加主入口：

```javascript
        // === 初始化 ===
        document.addEventListener('DOMContentLoaded', () => {
            if (isBirthday) {
                startBirthdayMode();
            } else {
                startNonBirthdayMode();
            }
        });

        function startBirthdayMode() {
            currentState = STATE.STAGE1;
            renderStage1();
        }

        function startNonBirthdayMode() {
            currentState = STATE.NON_BIRTHDAY;
            renderNonBirthday();
        }
```

- [ ] **Step 3: 验证** — 用浏览器打开 index.html，确认页面加载无报错（打开控制台检查）

- [ ] **Step 4: 提交**

```bash
git add index.html && git commit -m "feat: 添加 HTML 骨架 + 日期检测 + 状态机"
```

---

### Task 2: Canvas 星空粒子背景

**文件:**
- 修改: `index.html`（在 `<script>` 中追加 Canvas 背景系统）

- [ ] **Step 1: 实现星空粒子系统**

在 script 中追加：

```javascript
        // === Canvas 背景系统 ===
        const bgCanvas = document.getElementById('bgCanvas');
        const ctx = bgCanvas.getContext('2d');
        let particles = [];
        let bgWidth, bgHeight;

        function resizeCanvas() {
            bgWidth = bgCanvas.width = window.innerWidth;
            bgHeight = bgCanvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        class Particle {
            constructor() {
                this.reset();
                this.y = Math.random() * bgHeight;
            }
            reset() {
                this.x = Math.random() * bgWidth;
                this.y = -10;
                this.size = Math.random() * 2 + 0.5;
                this.speed = Math.random() * 0.5 + 0.1;
                this.opacity = Math.random() * 0.8 + 0.2;
                this.twinkleSpeed = Math.random() * 0.02 + 0.005;
                this.twinkleOffset = Math.random() * Math.PI * 2;
            }
            update() {
                this.y += this.speed;
                if (this.y > bgHeight + 10) this.reset();
            }
            draw() {
                const twinkle = Math.sin(Date.now() * this.twinkleSpeed + this.twinkleOffset) * 0.3 + 0.7;
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
                ctx.fillStyle = `rgba(255,255,255,${this.opacity * twinkle})`;
                ctx.fill();
            }
        }

        function initParticles(count = 150) {
            particles = [];
            for (let i = 0; i < count; i++) {
                particles.push(new Particle());
            }
        }
        initParticles();

        function animateBackground() {
            ctx.clearRect(0, 0, bgWidth, bgHeight);
            // 生日模式暖色光晕
            if (isBirthday) {
                const grd = ctx.createRadialGradient(bgWidth/2, bgHeight/2, 0, bgWidth/2, bgHeight/2, Math.max(bgWidth, bgHeight)/1.5);
                grd.addColorStop(0, 'rgba(102, 51, 153, 0.15)');
                grd.addColorStop(1, 'rgba(10, 10, 26, 0)');
                ctx.fillStyle = grd;
                ctx.fillRect(0, 0, bgWidth, bgHeight);
            }
            particles.forEach(p => { p.update(); p.draw(); });
            requestAnimationFrame(animateBackground);
        }
        animateBackground();
```

- [ ] **Step 2: 验证** — 打开浏览器，确认星空粒子在背景中流动，生日模式下有紫色光晕

- [ ] **Step 3: 提交**

```bash
git add index.html && git commit -m "feat: 添加 Canvas 星空粒子背景系统"
```

---

### Task 3: 生日模式 — Stage1 拆礼物

**文件:**
- 修改: `index.html`

- [ ] **Step 1: 实现礼物盒 CSS 和渲染函数**

在 `<style>` 中追加礼物盒样式，在 script 中追加渲染逻辑。完整代码块如下。

在 CSS 中追加：

```css
        /* 礼物盒 */
        .gift-wrapper { perspective: 800px; cursor: pointer; margin: 20px 0; }
        .gift-3d { width: 140px; height: 160px; position: relative; transform-style: preserve-3d; animation: giftFloat 3s ease-in-out infinite; }
        @keyframes giftFloat { 0%,100%{transform:translateY(0) rotateY(0deg)} 50%{transform:translateY(-15px) rotateY(10deg)} }
        .gift-body { width: 140px; height: 100px; position: absolute; bottom: 0; background: linear-gradient(135deg, #ff6b6b, #c0392b); border-radius: 12px; box-shadow: 0 10px 40px rgba(255,107,107,0.4); }
        .gift-lid { width: 150px; height: 35px; position: absolute; top: 0; left: -5px; background: linear-gradient(135deg, #ff8787, #e74c3c); border-radius: 12px; transform-origin: bottom center; transition: transform 0.8s cubic-bezier(0.68,-0.55,0.27,1.55); box-shadow: 0 5px 20px rgba(0,0,0,0.3); }
        .gift-lid.open { transform: rotateX(-120deg) translateY(-30px); }
        .gift-ribbon-v { width: 24px; height: 100%; position: absolute; left: 50%; transform: translateX(-50%); background: var(--gold); z-index: 5; bottom: 0; border-radius: 4px; }
        .gift-ribbon-h { width: 100%; height: 24px; position: absolute; top: 50%; transform: translateY(-50%); background: var(--gold); z-index: 5; border-radius: 4px; }
        .gift-bow { position: absolute; top: -20px; left: 50%; transform: translateX(-50%); width: 60px; height: 40px; z-index: 10; }
        .gift-bow::before, .gift-bow::after { content:''; position: absolute; width: 30px; height: 30px; background: var(--gold); border-radius: 50% 50% 50% 0; top: 0; }
        .gift-bow::before { left: 0; transform: rotate(-30deg); }
        .gift-bow::after { right: 0; transform: rotate(30deg) scaleX(-1); }
        .gift-sparkle { position: absolute; pointer-events: none; animation: sparkleBurst 0.8s ease-out forwards; }
        @keyframes sparkleBurst { 0%{transform:translate(0,0) scale(1);opacity:1} 100%{transform:translate(var(--sx),var(--sy)) scale(0);opacity:0} }

        /* 门票 */
        .ticket { background: linear-gradient(135deg, #1a1a2e, #2d2d44); border: 2px solid var(--gold); border-radius: 16px; padding: 30px 20px; text-align: center; animation: ticketFlyIn 0.8s cubic-bezier(0.68,-0.55,0.27,1.55); box-shadow: 0 0 40px rgba(255,215,0,0.3); max-width: 300px; }
        @keyframes ticketFlyIn { 0%{transform:scale(0) rotate(180deg);opacity:0} 100%{transform:scale(1) rotate(0);opacity:1} }
        .ticket h2 { color: var(--gold); font-size: 1.4rem; margin-bottom: 10px; }
        .ticket .ticket-name { color: var(--coral); font-size: 1.8rem; font-weight: bold; margin: 10px 0; }
        .ticket .ticket-info { color: rgba(255,255,255,0.7); font-size: 0.9rem; }

        /* 游乐园入口按钮 */
        .pixel-btn { background: linear-gradient(180deg, #ff6b6b 0%, #c0392b 100%); color: white; border: 3px solid #8b0000; padding: 14px 32px; font-size: 1.1rem; font-family: inherit; border-radius: 8px; cursor: pointer; transition: all 0.1s; box-shadow: 0 6px 0 #8b0000, 0 8px 20px rgba(0,0,0,0.3); text-transform: uppercase; letter-spacing: 2px; }
        .pixel-btn:active { transform: translateY(4px); box-shadow: 0 2px 0 #8b0000, 0 4px 10px rgba(0,0,0,0.3); }
```

在 `<script>` 中追加 Stage1 渲染和交互：

```javascript
        // === Stage1: 拆礼物 ===
        const mainContainer = document.getElementById('mainContainer');

        function renderStage1() {
            mainContainer.innerHTML = `
                <div class="screen active" id="stage1">
                    <h2 style="color:var(--gold);text-align:center;text-shadow:0 0 20px rgba(255,215,0,0.5);margin-bottom:20px;">🎉 有一份神秘礼物 🎉</h2>
                    <div class="gift-wrapper" id="giftWrapper">
                        <div class="gift-3d">
                            <div class="gift-bow"></div>
                            <div class="gift-lid" id="giftLid"></div>
                            <div class="gift-body">
                                <div class="gift-ribbon-v"></div>
                                <div class="gift-ribbon-h"></div>
                            </div>
                        </div>
                    </div>
                    <p style="color:var(--text-secondary);margin-top:24px;animation:pulse 2s infinite;">点击打开礼物盒 ✨</p>
                </div>
            `;
            @keyframes pulse { 0%,100%{opacity:0.6;transform:scale(1)} 50%{opacity:1;transform:scale(1.05)} }

            document.getElementById('giftWrapper').addEventListener('click', openGift);
        }

        function openGift() {
            const lid = document.getElementById('giftLid');
            lid.classList.add('open');
            createSparkles(document.getElementById('giftWrapper'));
            playSound('pop');

            setTimeout(() => {
                showTicket();
            }, 800);
        }

        function createSparkles(container) {
            const colors = ['#FFD700','#FF6B6B','#4ECDC4','#fff'];
            const rect = container.getBoundingClientRect();
            const cx = rect.left + rect.width/2;
            const cy = rect.top + rect.height/2;
            for (let i = 0; i < 20; i++) {
                const s = document.createElement('div');
                s.className = 'gift-sparkle';
                s.style.cssText = `
                    left:${cx}px;top:${cy}px;
                    width:8px;height:8px;
                    background:${colors[Math.floor(Math.random()*colors.length)]};
                    border-radius:50%;
                    --sx:${(Math.random()-0.5)*200}px;
                    --sy:${(Math.random()-0.5)*200}px;
                    position:fixed;z-index:1000;
                `;
                document.body.appendChild(s);
                setTimeout(() => s.remove(), 800);
            }
        }

        function showTicket() {
            mainContainer.innerHTML = `
                <div class="screen active">
                    <div class="ticket">
                        <h2>🎪 生日游乐园</h2>
                        <div class="ticket-name">黄子琳</div>
                        <div class="ticket-info">🎫 凭此票入场<br>📅 6月26日 · 仅此一天</div>
                    </div>
                    <button class="pixel-btn" id="enterParkBtn" style="margin-top:30px;">🎮 进入游乐园</button>
                </div>
            `;
            playSound('ding');
            document.getElementById('enterParkBtn').addEventListener('click', () => {
                currentState = STATE.STAGE2;
                renderStage2();
            });
        }
```

- [ ] **Step 2: 验证** — 打开浏览器，点击礼物盒查看弹开动画和门票出现

- [ ] **Step 3: 提交**

```bash
git add index.html && git commit -m "feat: 实现生日模式 Stage1 拆礼物 + 门票"
```

---

### Task 4: 生日模式 — Stage2 游乐园（3个摊位）

**文件:**
- 修改: `index.html`

- [ ] **Step 1: 实现游乐园容器、进度条、戳气球摊位**

```javascript
        // === Stage2: 游乐园 ===
        let carnivalProgress = { balloon: false, candle: false, cake: false };
        let balloonsPopped = 0;
        const BALLOON_TOTAL = 12;
        let candleClicks = 0;
        const CANDLE_NEEDED = 8;

        function renderStage2() {
            carnivalProgress = { balloon: false, candle: false, cake: false };
            balloonsPopped = 0;
            candleClicks = 0;
            updateCarnivalUI();
        }

        function updateCarnivalUI() {
            const done = Object.values(carnivalProgress).filter(Boolean).length;
            mainContainer.innerHTML = `
                <div class="screen active" style="width:100%;">
                    <div style="display:flex;gap:8px;width:100%;background:rgba(0,0,0,0.3);border-radius:20px;padding:6px;margin-bottom:20px;">
                        <div style="flex:1;text-align:center;padding:8px;border-radius:14px;background:${carnivalProgress.balloon?'var(--gold)':'rgba(255,255,255,0.1)'};transition:0.3s;">🎈</div>
                        <div style="flex:1;text-align:center;padding:8px;border-radius:14px;background:${carnivalProgress.candle?'var(--gold)':'rgba(255,255,255,0.1)'};transition:0.3s;">🕯️</div>
                        <div style="flex:1;text-align:center;padding:8px;border-radius:14px;background:${carnivalProgress.cake?'var(--gold)':'rgba(255,255,255,0.1)'};transition:0.3s;">🎂</div>
                    </div>
                    <div id="carnivalGame"></div>
                </div>
            `;
            const gameEl = document.getElementById('carnivalGame');
            if (!carnivalProgress.balloon) renderBalloonGame(gameEl);
            else if (!carnivalProgress.candle) renderCandleGame(gameEl);
            else if (!carnivalProgress.cake) renderCakeGame(gameEl);
        }

        function renderBalloonGame(container) {
            const balloonColors = ['#ff6b6b','#feca57','#48dbfb','#ff9ff3','#54a0ff','#5f27cd','#00d2d3','#f368e0','#ff6348','#ffd700','#2ecc71','#e74c3c'];
            container.innerHTML = `
                <h3 style="color:white;text-align:center;margin-bottom:10px;">🎈 戳气球 (${balloonsPopped}/${BALLOON_TOTAL})</h3>
                <div id="balloonField" style="position:relative;width:100%;height:350px;overflow:hidden;border-radius:16px;background:rgba(255,255,255,0.03);"></div>
            `;
            const field = document.getElementById('balloonField');
            for (let i = 0; i < BALLOON_TOTAL; i++) {
                const balloon = document.createElement('div');
                const color = balloonColors[i];
                balloon.innerHTML = '🎈';
                balloon.style.cssText = `
                    position:absolute;font-size:2.2rem;cursor:pointer;
                    left:${Math.random()*80+5}%;top:${Math.random()*70+10}%;
                    animation: balloonFloat ${Math.random()*3+2}s ease-in-out infinite;
                    animation-delay:${Math.random()*2}s;
                    filter:drop-shadow(0 0 8px ${color});
                    transition:transform 0.1s;
                `;
                balloon.addEventListener('click', (e) => popBalloon(balloon, e));
                field.appendChild(balloon);
            }
            // 气球浮动动画
            const style = document.createElement('style');
            style.textContent = `@keyframes balloonFloat { 0%,100%{transform:translateY(0) rotate(0deg)} 50%{transform:translateY(-25px) rotate(5deg)} }`;
            field.appendChild(style);
        }

        function popBalloon(balloon, e) {
            balloon.style.transform = 'scale(3)';
            balloon.style.opacity = '0';
            balloon.style.transition = 'all 0.3s';
            balloon.style.pointerEvents = 'none';
            balloonsPopped++;
            playSound('pop');
            // 粒子效果
            const rect = balloon.getBoundingClientRect();
            createPopParticles(rect.left + rect.width/2, rect.top + rect.height/2);
            // 祝福词浮动
            const words = ['快乐!','幸福!','健康!','好运!','美丽!','加油!','棒棒!','开心!'];
            showFloatingText(rect.left + rect.width/2, rect.top, words[Math.floor(Math.random()*words.length)], '#ffd700');

            document.querySelector('#balloonField h3')?.remove();
            const h3 = document.createElement('h3');
            h3.style.cssText = 'color:white;text-align:center;margin-bottom:10px;';
            h3.textContent = `🎈 戳气球 (${balloonsPopped}/${BALLOON_TOTAL})`;
            document.getElementById('balloonField').parentElement.insertBefore(h3, document.getElementById('balloonField'));

            if (balloonsPopped >= BALLOON_TOTAL) {
                setTimeout(() => {
                    carnivalProgress.balloon = true;
                    playSound('ding');
                    updateCarnivalUI();
                }, 500);
            }
        }

        function createPopParticles(x, y) {
            const colors = ['#ff6b6b','#feca57','#48dbfb','#ff9ff3','#00d2d3'];
            for (let i = 0; i < 8; i++) {
                const p = document.createElement('div');
                p.style.cssText = `
                    position:fixed;left:${x}px;top:${y}px;width:6px;height:6px;
                    background:${colors[Math.floor(Math.random()*colors.length)]};
                    border-radius:50%;pointer-events:none;z-index:1000;
                    animation:particleBurst 0.6s ease-out forwards;
                    --px:${(Math.random()-0.5)*100}px;--py:${(Math.random()-0.5)*100}px;
                `;
                document.body.appendChild(p);
                setTimeout(() => p.remove(), 600);
            }
            const style = document.createElement('style');
            style.textContent = `@keyframes particleBurst { 0%{transform:translate(0,0) scale(1);opacity:1} 100%{transform:translate(var(--px),var(--py)) scale(0);opacity:0} }`;
            document.body.appendChild(style);
            setTimeout(() => style.remove(), 700);
        }

        function showFloatingText(x, y, text, color) {
            const el = document.createElement('div');
            el.textContent = text;
            el.style.cssText = `
                position:fixed;left:${x}px;top:${y}px;color:${color};font-size:1.5rem;
                font-weight:bold;pointer-events:none;z-index:1001;
                animation:floatUp 1.5s ease-out forwards;
            `;
            document.body.appendChild(el);
            setTimeout(() => el.remove(), 1500);
            const s = document.createElement('style');
            s.textContent = `@keyframes floatUp { 0%{transform:translateY(0) scale(1);opacity:1} 100%{transform:translateY(-80px) scale(1.5);opacity:0} }`;
            document.body.appendChild(s);
            setTimeout(() => s.remove(), 1600);
        }
```

- [ ] **Step 2: 实现吹蜡烛和切蛋糕摊位**

```javascript
        function renderCandleGame(container) {
            container.innerHTML = `
                <h3 style="color:white;text-align:center;margin-bottom:10px;">🕯️ 吹蜡烛</h3>
                <div id="candleArea" style="text-align:center;padding:30px;background:rgba(255,255,255,0.03);border-radius:16px;">
                    <div style="font-size:5rem;position:relative;display:inline-block;">
                        🎂
                        <div id="candleFlame" style="position:absolute;top:-30px;left:50%;transform:translateX(-50%);font-size:2rem;animation:flameFlicker 0.3s infinite alternate;">🕯️</div>
                    </div>
                    <p style="color:var(--text-secondary);margin-top:20px;" id="blowHint">💨 快速点击屏幕吹灭蜡烛！</p>
                    <div style="margin-top:10px;background:rgba(255,255,255,0.1);border-radius:10px;height:8px;overflow:hidden;">
                        <div id="blowProgress" style="width:0%;height:100%;background:var(--gold);transition:width 0.2s;"></div>
                    </div>
                </div>
            `;
            @keyframes flameFlicker { 0%{transform:translateX(-50%) scaleX(1)} 100%{transform:translateX(-50%) scaleX(0.7) scaleY(1.2)} }

            const area = document.getElementById('candleArea');
            area.addEventListener('click', blowCandle);
            area.addEventListener('touchstart', (e) => { e.preventDefault(); blowCandle(); });
        }

        function blowCandle() {
            candleClicks++;
            const progress = Math.min(100, (candleClicks / CANDLE_NEEDED) * 100);
            document.getElementById('blowProgress').style.width = progress + '%';
            document.getElementById('blowHint').textContent = `💨 继续吹！(${candleClicks}/${CANDLE_NEEDED})`;
            playSound('pop');

            if (candleClicks >= CANDLE_NEEDED) {
                const flame = document.getElementById('candleFlame');
                if (flame) flame.style.display = 'none';
                document.getElementById('blowHint').textContent = '🎉 蜡烛灭了！';
                carnivalProgress.candle = true;
                playSound('ding');
                setTimeout(() => updateCarnivalUI(), 800);
            }
        }

        function renderCakeGame(container) {
            let sliced = false;
            container.innerHTML = `
                <h3 style="color:white;text-align:center;margin-bottom:10px;">🎂 切蛋糕</h3>
                <div id="cakeArea" style="position:relative;text-align:center;padding:30px;background:rgba(255,255,255,0.03);border-radius:16px;cursor:pointer;">
                    <div id="cakeDisplay" style="font-size:6rem;">🎂</div>
                    <p style="color:var(--text-secondary);margin-top:15px;">🔪 用手指在蛋糕上划一刀！</p>
                </div>
            `;

            let startX = 0, startY = 0;
            const cakeArea = document.getElementById('cakeArea');

            const onStart = (e) => {
                if (sliced) return;
                const t = e.touches ? e.touches[0] : e;
                startX = t.clientX; startY = t.clientY;
            };
            const onEnd = (e) => {
                if (sliced) return;
                const t = e.changedTouches ? e.changedTouches[0] : e;
                const dx = t.clientX - startX, dy = t.clientY - startY;
                if (Math.abs(dx) > 40 || Math.abs(dy) > 40) {
                    sliced = true;
                    document.getElementById('cakeDisplay').innerHTML = '🍰';
                    document.getElementById('cakeArea').querySelector('p').textContent = '✨ 蛋糕切开啦！';
                    playSound('ding');
                    createPopParticles(
                        document.getElementById('cakeDisplay').getBoundingClientRect().left + 50,
                        document.getElementById('cakeDisplay').getBoundingClientRect().top + 50
                    );
                    // 大粒子爆发
                    for (let i = 0; i < 3; i++) {
                        setTimeout(() => createPopParticles(
                            document.getElementById('cakeDisplay').getBoundingClientRect().left + Math.random() * 80,
                            document.getElementById('cakeDisplay').getBoundingClientRect().top + Math.random() * 80
                        ), i * 150);
                    }
                    carnivalProgress.cake = true;
                    setTimeout(() => {
                        currentState = STATE.STAGE3;
                        renderStage3();
                    }, 1200);
                }
            };
            cakeArea.addEventListener('mousedown', onStart);
            cakeArea.addEventListener('mouseup', onEnd);
            cakeArea.addEventListener('touchstart', onStart);
            cakeArea.addEventListener('touchend', onEnd);
        }
```

- [ ] **Step 3: 验证** — 测试所有三个摊位，确认进度条更新，切蛋糕后进入 Stage3

- [ ] **Step 4: 提交**

```bash
git add index.html && git commit -m "feat: 实现生日模式 Stage2 游乐园（戳气球+吹蜡烛+切蛋糕）"
```

---

### Task 5: 生日模式 — Stage3 惊喜绽放 + Finale

**文件:**
- 修改: `index.html`

- [ ] **Step 1: 实现 Stage3 烟花秀和最终祝福**

在 script 中追加：

```javascript
        // === Stage3: 惊喜绽放 ===
        function renderStage3() {
            mainContainer.innerHTML = `
                <div class="screen active" style="text-align:center;">
                    <div id="finaleContent" style="animation:finaleAppear 1.5s ease-out;">
                        <div style="font-size:4rem;animation:bounceIn 0.8s cubic-bezier(0.68,-0.55,0.27,1.55);">🎉</div>
                        <h1 style="color:var(--gold);font-size:2rem;text-shadow:0 0 30px rgba(255,215,0,0.6);margin:20px 0;animation:bounceIn 1s cubic-bezier(0.68,-0.55,0.27,1.55);">
                            生日快乐，黄子琳！
                        </h1>
                        <p style="color:var(--text-secondary);font-size:1.2rem;margin:20px 0;" id="eggCount"></p>
                        <div id="hiddenEnding" style="display:none;"></div>
                        <button class="pixel-btn" id="restartBtn" style="margin-top:20px;background:linear-gradient(180deg,#ffd700,#b8860b);border-color:#8b6914;">🔄 再来一次</button>
                    </div>
                </div>
            `;
            @keyframes finaleAppear { 0%{opacity:0;transform:scale(0.5)} 100%{opacity:1;transform:scale(1)} }
            @keyframes bounceIn { 0%{transform:scale(0) rotate(-10deg)} 60%{transform:scale(1.1) rotate(3deg)} 100%{transform:scale(1) rotate(0)} }

            document.getElementById('eggCount').textContent = eggsCollected >= 5
                ? `🥚 你找到了全部 ${eggsCollected}/5 个隐藏彩蛋！`
                : `🥚 你找到了 ${eggsCollected}/5 个隐藏彩蛋`;

            if (eggsCollected >= 5) {
                document.getElementById('hiddenEnding').innerHTML = `
                    <div style="background:rgba(255,215,0,0.1);border:2px solid var(--gold);border-radius:16px;padding:20px;margin:20px 0;animation:bounceIn 1.5s cubic-bezier(0.68,-0.55,0.27,1.55);">
                        <p style="color:var(--gold);font-size:1.3rem;">🌟 隐藏结局解锁！</p>
                        <p style="color:white;margin-top:10px;">你是真正的彩蛋大师！<br>黄子琳收到了双倍的祝福！</p>
                    </div>
                `;
            }

            document.getElementById('restartBtn').addEventListener('click', () => {
                currentState = STATE.STAGE1;
                renderStage1();
            });

            // 屏幕震动
            triggerShake();
            // 烟花秀
            launchFireworks();
            playSound('ding');
        }

        function triggerShake() {
            document.body.style.animation = 'screenShake 0.5s ease-out';
            setTimeout(() => document.body.style.animation = '', 500);
            const s = document.createElement('style');
            s.textContent = `@keyframes screenShake { 0%,100%{transform:translate(0)} 20%{transform:translate(-8px,4px)} 40%{transform:translate(8px,-4px)} 60%{transform:translate(-4px,-2px)} 80%{transform:translate(4px,2px)} }`;
            document.body.appendChild(s);
            setTimeout(() => s.remove(), 600);
        }

        function launchFireworks() {
            const colors = ['#ff6b6b','#feca57','#48dbfb','#ff9ff3','#54a0ff','#00d2d3','#ffd700','#ff6348'];
            for (let burst = 0; burst < 5; burst++) {
                setTimeout(() => {
                    const cx = Math.random() * window.innerWidth * 0.6 + window.innerWidth * 0.2;
                    const cy = Math.random() * window.innerHeight * 0.4 + window.innerHeight * 0.1;
                    for (let i = 0; i < 24; i++) {
                        const p = document.createElement('div');
                        const angle = (Math.PI * 2 * i) / 24;
                        const dist = 80 + Math.random() * 120;
                        p.style.cssText = `
                            position:fixed;left:${cx}px;top:${cy}px;width:4px;height:4px;
                            background:${colors[Math.floor(Math.random()*colors.length)]};
                            border-radius:50%;pointer-events:none;z-index:999;
                            animation:fireworkBurst ${1+Math.random()}s ease-out forwards;
                            --fx:${Math.cos(angle)*dist}px;--fy:${Math.sin(angle)*dist}px;
                        `;
                        document.body.appendChild(p);
                        setTimeout(() => p.remove(), 1200);
                    }
                }, burst * 800);
            }
            const s = document.createElement('style');
            s.textContent = `@keyframes fireworkBurst { 0%{transform:translate(0,0) scale(1);opacity:1} 100%{transform:translate(var(--fx),var(--fy)) scale(0);opacity:0} }`;
            document.body.appendChild(s);
            setTimeout(() => s.remove(), 5000);
        }
```

- [ ] **Step 2: 验证** — 完成 Stage2 后确认进入 Stage3，烟花和屏幕震动效果正常

- [ ] **Step 3: 提交**

```bash
git add index.html && git commit -m "feat: 实现生日模式 Stage3 烟花秀+最终祝福+隐藏结局"
```

---

### Task 6: 非生日模式

**文件:**
- 修改: `index.html`

- [ ] **Step 1: 实现非生日模式完整逻辑**

```javascript
        // === 非生日模式 ===
        let nonBirthdayDialogue = 0;
        const NON_BIRTHDAY_DIALOGUES = [
            { title: '咦？👀', text: '今天好像不是你的生日哦~' },
            { title: '哈哈哈 😂', text: '想提前感受生日气氛？做梦！' },
            { title: '不过... 🤔', text: '既然来了，玩玩这个吧！' },
            { title: '看看你的手速 🎯', text: '试试抓住下面那个乱跑的礼物盒！' },
        ];
        let giftClicks = 0;
        const GIFT_CLICK_MESSAGES = [
            '差一点！', '想得美！', '手速不行啊~', '太慢了！',
            '哈哈抓不到！', '加油！快抓到了！', '你行不行啊~', '还差一点点！',
            '继续继续！', '好吧，你赢了...但还是没有生日！',
        ];

        function renderNonBirthday() {
            giftClicks = 0;
            nonBirthdayDialogue = 0;
            renderNonBirthdayDialogue();
        }

        function renderNonBirthdayDialogue() {
            const d = NON_BIRTHDAY_DIALOGUES[nonBirthdayDialogue] || NON_BIRTHDAY_DIALOGUES[0];
            mainContainer.innerHTML = `
                <div class="screen active" id="nonBdayScreen">
                    <div style="font-size:4rem;margin-bottom:20px;">🚪🔒</div>
                    <h2 style="color:var(--neon-green);text-shadow:0 0 20px rgba(0,255,136,0.5);">${d.title}</h2>
                    <p style="color:var(--text-secondary);margin:15px 0;text-align:center;font-size:1.1rem;">${d.text}</p>
                    <button class="pixel-btn" id="nextDialogueBtn" style="background:linear-gradient(180deg,#00FF88,#008844);border-color:#006633;">
                        ${nonBirthdayDialogue < NON_BIRTHDAY_DIALOGUES.length - 1 ? '👉 继续' : '🎮 抓礼物'}
                    </button>
                    <div style="margin-top:30px;text-align:center;color:var(--text-secondary);font-size:0.85rem;background:rgba(255,255,255,0.03);padding:15px;border-radius:12px;">
                        <p>⏳ 距离黄子琳的生日还有 <span style="color:var(--neon-green);font-weight:bold;" id="countdownDisplay"></span></p>
                        <p style="margin-top:8px;">👀 你是第 <span style="color:var(--cool-blue);">${visitCount}</span> 个提前来的人</p>
                    </div>
                </div>
            `;

            // 倒计时
            updateCountdown();
            setInterval(updateCountdown, 60000);

            document.getElementById('nextDialogueBtn').addEventListener('click', () => {
                nonBirthdayDialogue++;
                if (nonBirthdayDialogue < NON_BIRTHDAY_DIALOGUES.length) {
                    playSound('pop');
                    renderNonBirthdayDialogue();
                } else {
                    renderGiftChaseGame();
                }
            });
        }

        function updateCountdown() {
            const now = new Date();
            let nextBirthday = new Date(now.getFullYear(), 5, 26); // month 5 = June
            if (now > nextBirthday) {
                nextBirthday = new Date(now.getFullYear() + 1, 5, 26);
            }
            const diff = nextBirthday - now;
            const days = Math.floor(diff / (1000*60*60*24));
            const hours = Math.floor((diff % (1000*60*60*24)) / (1000*60*60));
            const mins = Math.floor((diff % (1000*60*60)) / (1000*60));
            const el = document.getElementById('countdownDisplay');
            if (el) el.textContent = `${days}天 ${hours}时 ${mins}分`;
        }

        function renderGiftChaseGame() {
            mainContainer.innerHTML = `
                <div class="screen active" id="chaseGame">
                    <h3 style="color:var(--neon-green);text-shadow:0 0 20px rgba(0,255,136,0.5);">🎯 抓住礼物盒！</h3>
                    <p style="color:var(--text-secondary);margin:10px 0;">点击礼物盒，看看你能抓住几次</p>
                    <div id="chaseField" style="position:relative;width:100%;height:300px;border-radius:16px;background:rgba(255,255,255,0.02);border:1px dashed rgba(255,255,255,0.1);overflow:hidden;">
                        <div id="chaseGift" style="position:absolute;font-size:3rem;cursor:pointer;transition:all 0.15s cubic-bezier(0.25,0.46,0.45,0.94);user-select:none;">🎁</div>
                    </div>
                    <p id="chaseMessage" style="color:var(--neon-green);font-size:1.2rem;margin-top:15px;min-height:30px;"></p>
                    <p style="color:var(--text-secondary);margin-top:5px;">点击次数：<span id="chaseCount">0</span></p>
                </div>
            `;

            const gift = document.getElementById('chaseGift');
            const field = document.getElementById('chaseField');
            const msg = document.getElementById('chaseMessage');
            const countEl = document.getElementById('chaseCount');

            function moveGift() {
                const maxX = field.clientWidth - 60;
                const maxY = field.clientHeight - 60;
                gift.style.left = Math.random() * maxX + 'px';
                gift.style.top = Math.random() * maxY + 'px';
            }
            moveGift();

            gift.addEventListener('click', () => {
                giftClicks++;
                countEl.textContent = giftClicks;
                const msgText = GIFT_CLICK_MESSAGES[Math.min(giftClicks - 1, GIFT_CLICK_MESSAGES.length - 1)];
                msg.textContent = msgText;
                playSound('pop');
                moveGift();
                if (giftClicks >= 20 && !gift.dataset.rewarded) {
                    gift.dataset.rewarded = '1';
                    setTimeout(() => {
                        msg.textContent = '🌟 安慰奖：你获得了一个隐藏祝福！';
                        msg.style.color = 'var(--gold)';
                        playSound('ding');
                    }, 500);
                }
            });
        }
```

- [ ] **Step 2: 验证** — 修改系统日期到非6月26日测试，确认非生日模式正常

- [ ] **Step 3: 提交**

```bash
git add index.html && git commit -m "feat: 实现非生日模式（对话+抓礼物游戏+倒计时）"
```

---

### Task 7: Web Audio 音效系统

**文件:**
- 修改: `index.html`

- [ ] **Step 1: 实现音效引擎**

```javascript
        // === 音效系统 ===
        let audioCtx = null;
        function getAudioCtx() {
            if (!audioCtx) {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            }
            if (audioCtx.state === 'suspended') audioCtx.resume();
            return audioCtx;
        }

        function playSound(type) {
            try {
                const ctx = getAudioCtx();
                const now = ctx.currentTime;
                const gain = ctx.createGain();
                gain.connect(ctx.destination);

                if (type === 'pop') {
                    const osc = ctx.createOscillator();
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(600, now);
                    osc.frequency.exponentialRampToValueAtTime(200, now + 0.1);
                    gain.gain.setValueAtTime(0.3, now);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.1);
                    osc.connect(gain);
                    osc.start(now); osc.stop(now + 0.1);
                } else if (type === 'ding') {
                    const osc = ctx.createOscillator();
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(880, now);
                    osc.frequency.setValueAtTime(1100, now + 0.1);
                    gain.gain.setValueAtTime(0.3, now);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.4);
                    osc.connect(gain);
                    osc.start(now); osc.stop(now + 0.4);
                } else if (type === 'boom') {
                    const osc = ctx.createOscillator();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(150, now);
                    osc.frequency.exponentialRampToValueAtTime(30, now + 0.3);
                    gain.gain.setValueAtTime(0.5, now);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.3);
                    osc.connect(gain);
                    osc.start(now); osc.stop(now + 0.3);
                } else if (type === 'shimmer') {
                    for (let i = 0; i < 5; i++) {
                        const osc = ctx.createOscillator();
                        osc.type = 'sine';
                        const freq = 800 + Math.random() * 1600;
                        osc.frequency.setValueAtTime(freq, now + i * 0.05);
                        const g = ctx.createGain();
                        g.gain.setValueAtTime(0.15, now + i * 0.05);
                        g.gain.exponentialRampToValueAtTime(0.01, now + i * 0.05 + 0.3);
                        g.connect(ctx.destination);
                        osc.connect(g);
                        osc.start(now + i * 0.05);
                        osc.stop(now + i * 0.05 + 0.3);
                    }
                }
            } catch(e) { /* 静默降级 */ }
        }
```

- [ ] **Step 2: 在所有交互点调用 playSound()**

确保以下位置已调用 playSound：
- 打开礼物盒 → `playSound('pop')` (已在 Task 3 添加)
- 戳气球 → `playSound('pop')` (已在 Task 4 添加)
- 吹蜡烛点击 → `playSound('pop')` (已在 Task 4 添加)
- 切蛋糕完成 → `playSound('ding')` (已在 Task 4 添加)
- 门票出现 → `playSound('ding')` (已在 Task 3 添加)
- 最终烟花 → `playSound('shimmer')` (需在 Stage3 追加)
- 摊位完成 → `playSound('ding')` (已在 Task 4 添加)

- [ ] **Step 3: 验证** — 测试所有交互点是否发出音效

- [ ] **Step 4: 提交**

```bash
git add index.html && git commit -m "feat: 添加 Web Audio API 音效系统"
```

---

### Task 8: 收尾优化

**文件:**
- 修改: `index.html`

- [ ] **Step 1: 添加彩蛋收集系统**

在 Stage1 门票显示后，Stage2 进行中，随机位置闪现小彩蛋供收集：

```javascript
        // === 彩蛋系统 ===
        function spawnHiddenEgg() {
            if (currentState !== STATE.STAGE2 && currentState !== STATE.STAGE1) return;
            const egg = document.createElement('div');
            egg.textContent = '🥚';
            egg.style.cssText = `
                position:fixed;font-size:2rem;cursor:pointer;z-index:100;
                left:${Math.random()*80+10}%;top:${Math.random()*70+20}%;
                animation:eggGlow 0.8s ease-in-out 3;
                pointer-events:auto;
            `;
            egg.addEventListener('click', (e) => {
                e.stopPropagation();
                LS.set('eggs', LS.get('eggs', 0) + 1);
                showFloatingText(e.clientX, e.clientY, '+1 🥚', '#FFD700');
                playSound('ding');
                egg.remove();
            });
            document.body.appendChild(egg);
            setTimeout(() => egg.remove(), 3000);
        }

        // 在 Stage1 和 Stage2 期间每隔一段时间生成彩蛋
        setInterval(() => {
            if (currentState === STATE.STAGE1 || currentState === STATE.STAGE2) {
                if (Math.random() < 0.3) spawnHiddenEgg();
            }
        }, 5000);
```

追加 CSS：

```css
        @keyframes eggGlow { 0%,100%{filter:drop-shadow(0 0 5px #FFD700)} 50%{filter:drop-shadow(0 0 20px #FFD700) scale(1.2)} }
```

- [ ] **Step 2: 添加响应式适配**

```css
        @media (max-width: 480px) {
            .ticket { padding: 20px 15px; }
            .ticket .ticket-name { font-size: 1.4rem; }
            .pixel-btn { padding: 12px 24px; font-size: 0.95rem; }
            h1 { font-size: 1.5rem !important; }
            h2 { font-size: 1.2rem !important; }
        }
```

- [ ] **Step 3: 清理旧文件并最终提交**

```bash
git rm index.md
git add index.html && git commit -m "feat: 添加彩蛋收集系统+响应式适配，清理旧文件"
```

- [ ] **Step 4: 全流程测试**

1. 修改系统日期到 6月26日 → 验证生日模式完整流程
2. 修改系统日期到其他日期 → 验证非生日模式
3. 在手机或模拟器中测试移动端体验
4. 验证 LocalStorage 持久化（刷新页面彩蛋计数保留）

---

## 自审检查

**1. Spec 覆盖:**
- 日期检测 → Task 1
- 状态机 → Task 1
- Canvas 星空背景 → Task 2
- Stage1 拆礼物 → Task 3
- Stage2 游乐园（3个摊位）→ Task 4
- Stage3 惊喜绽放 → Task 5
- 非生日模式（对话+游戏+倒计时）→ Task 6
- 音效系统 → Task 7
- 彩蛋收集 → Task 8
- 响应式 → Task 8

**2. 占位符扫描:** 无 TBD/TODO

**3. 类型一致性:** `playSound` 参数统一为字符串类型，`STATE` 枚举值一致
