# 交互模式 — Interaction Patterns

本文档提供四种核心交互模块的实现模板。所有代码使用原生 HTML/CSS/JS，不依赖外部库。

## 模块一：卡通动画讲解

### 功能说明

知识点分步骤展示，每步配合动画演示。学生点击"下一步"逐步学习，可回看上一步。

### HTML 结构

```html
<div class="lesson-section" id="lessonSection">
  <div class="lesson-header">
    <span class="lesson-mascot">🦊</span>
    <div class="speech-bubble">
      <p id="lessonText">欢迎来到学习小课堂！点击"下一步"开始吧～</p>
    </div>
  </div>
  
  <div class="lesson-stage" id="lessonStage">
    <!-- 动画演示区域，内容由 JS 动态填充 -->
  </div>
  
  <div class="lesson-controls">
    <button class="btn btn-secondary" id="prevBtn" onclick="prevStep()">⬅️ 上一步</button>
    <div class="step-indicator">
      <span id="currentStep">1</span> / <span id="totalSteps">5</span>
    </div>
    <button class="btn" id="nextBtn" onclick="nextStep()">下一步 ➡️</button>
  </div>
</div>
```

### JavaScript 实现

```javascript
// 知识点步骤数据 — 根据具体知识点填充
const lessonSteps = [
  {
    text: "小朋友们，今天我们来认识'分数'！🦊",
    stage: `<div class="big-emoji">🍕</div><p>看这个披萨，它是一个整体</p>`,
    mascot: "🦊"
  },
  {
    text: "如果把它切成 2 份，每份就是二分之一！",
    stage: `<div class="pizza-animation"><!-- CSS 动画演示切割 --></div>`,
    mascot: "🦊"
  },
  // ... 更多步骤
];

let currentStepIndex = 0;

function showStep(index) {
  const step = lessonSteps[index];
  document.getElementById('lessonText').textContent = step.text;
  document.getElementById('lessonStage').innerHTML = step.stage;
  document.querySelector('.lesson-mascot').textContent = step.mascot || '🦊';
  document.getElementById('currentStep').textContent = index + 1;
  document.getElementById('totalSteps').textContent = lessonSteps.length;
  
  // 按钮状态
  document.getElementById('prevBtn').disabled = index === 0;
  document.getElementById('nextBtn').textContent = 
    index === lessonSteps.length - 1 ? '完成讲解 ✅' : '下一步 ➡️';
  
  // 触发弹入动画
  document.getElementById('lessonStage').classList.remove('animate-bounce-in');
  void document.getElementById('lessonStage').offsetWidth; // 强制重排
  document.getElementById('lessonStage').classList.add('animate-bounce-in');
  
  updateProgress((index + 1) / lessonSteps.length * 50); // 讲解占 50% 进度
}

function nextStep() {
  if (currentStepIndex < lessonSteps.length - 1) {
    currentStepIndex++;
    showStep(currentStepIndex);
  } else {
    // 讲解完成，滚动到练习区
    document.getElementById('quizSection').scrollIntoView({ behavior: 'smooth' });
  }
}

function prevStep() {
  if (currentStepIndex > 0) {
    currentStepIndex--;
    showStep(currentStepIndex);
  }
}

showStep(0); // 初始化
```

---

## 模块二：随堂小测验

### 功能说明

3-5 道题目，支持选择题和填空题。答题后即时反馈，答对给星星，答错鼓励再试。全部完成后显示总分。

### HTML 结构

```html
<div class="quiz-section" id="quizSection">
  <h2>📝 随堂小测验</h2>
  <div id="quizContainer">
    <!-- 题目由 JS 动态生成 -->
  </div>
  <div class="quiz-result" id="quizResult" style="display:none;">
    <div class="badge">🏆</div>
    <h3 id="resultTitle">太棒了！</h3>
    <p id="resultScore"></p>
    <button class="btn" onclick="resetQuiz()">再做一次 🔄</button>
  </div>
</div>
```

### JavaScript 实现

```javascript
// 测验题目数据 — 根据具体知识点填充
const quizQuestions = [
  {
    type: "choice",
    question: "下面的分数哪个最大？",
    options: ["1/2", "1/4", "1/8", "1/3"],
    answer: 0,
    explanation: "分母越小，每份就越大！1/2 表示把整体分成 2 份取 1 份，是最大的。"
  },
  {
    type: "fill",
    question: "把一个蛋糕分成 4 份，每份是这个蛋糕的（  ）分之（  ）",
    answer: "四一",
    explanation: "分成 4 份，每份就是四分之一，写作 1/4。"
  },
  // ... 更多题目
];

let quizAnswers = []; // 记录每题是否答对
let earnedStars = 0;

function renderQuiz() {
  const container = document.getElementById('quizContainer');
  container.innerHTML = '';
  quizQuestions.forEach((q, i) => {
    const card = document.createElement('div');
    card.className = 'card quiz-card';
    card.id = `quiz-${i}`;
    
    if (q.type === 'choice') {
      card.innerHTML = `
        <h3>第 ${i + 1} 题 <span class="question-star">⭐</span></h3>
        <p class="question-text">${q.question}</p>
        <div class="options">
          ${q.options.map((opt, j) => `
            <button class="option-btn" onclick="checkAnswer(${i}, ${j})">
              ${String.fromCharCode(65 + j)}. ${opt}
            </button>
          `).join('')}
        </div>
        <div class="feedback" id="feedback-${i}"></div>
      `;
    } else if (q.type === 'fill') {
      card.innerHTML = `
        <h3>第 ${i + 1} 题 <span class="question-star">⭐</span></h3>
        <p class="question-text">${q.question}</p>
        <input type="text" class="fill-input" id="input-${i}" 
               placeholder="在这里输入答案..." 
               onkeypress="if(event.key==='Enter') checkFillAnswer(${i})">
        <button class="btn" onclick="checkFillAnswer(${i})">提交 ✅</button>
        <div class="feedback" id="feedback-${i}"></div>
      `;
    }
    container.appendChild(card);
  });
}

function checkAnswer(qIndex, selected) {
  const q = quizQuestions[qIndex];
  const card = document.getElementById(`quiz-${qIndex}`);
  const feedback = document.getElementById(`feedback-${qIndex}`);
  const buttons = card.querySelectorAll('.option-btn');
  
  buttons.forEach(btn => btn.disabled = true);
  
  if (selected === q.answer) {
    buttons[selected].classList.add('correct');
    feedback.innerHTML = `<p class="feedback-correct">🎉 答对了！${q.explanation}</p>`;
    quizAnswers[qIndex] = true;
    earnedStars++;
    showStarAnimation();
  } else {
    buttons[selected].classList.add('wrong');
    buttons[q.answer].classList.add('correct');
    feedback.innerHTML = `<p class="feedback-wrong">💪 差一点点！${q.explanation}</p>`;
    quizAnswers[qIndex] = false;
  }
  
  checkQuizComplete();
}

function checkFillAnswer(qIndex) {
  const q = quizQuestions[qIndex];
  const input = document.getElementById(`input-${qIndex}`);
  const feedback = document.getElementById(`feedback-${qIndex}`);
  const userAnswer = input.value.trim();
  
  if (!userAnswer) return;
  
  input.disabled = true;
  
  if (userAnswer === q.answer) {
    input.classList.add('correct');
    feedback.innerHTML = `<p class="feedback-correct">🎉 答对了！${q.explanation}</p>`;
    quizAnswers[qIndex] = true;
    earnedStars++;
    showStarAnimation();
  } else {
    input.classList.add('wrong');
    feedback.innerHTML = `<p class="feedback-wrong">💪 再想想！正确答案是：${q.answer}。${q.explanation}</p>`;
    quizAnswers[qIndex] = false;
  }
  
  checkQuizComplete();
}

function checkQuizComplete() {
  const answered = quizAnswers.filter(a => a !== undefined).length;
  if (answered === quizQuestions.length) {
    setTimeout(() => {
      const correct = quizAnswers.filter(a => a === true).length;
      const result = document.getElementById('quizResult');
      const title = document.getElementById('resultTitle');
      const score = document.getElementById('resultScore');
      
      if (correct === quizQuestions.length) {
        title.textContent = '全对了！太厉害了！🌟';
      } else if (correct >= quizQuestions.length * 0.6) {
        title.textContent = '做得不错！继续加油！👍';
      } else {
        title.textContent = '没关系，多练习就会更好！😊';
      }
      score.textContent = `答对 ${correct} / ${quizQuestions.length} 题，获得 ${earnedStars} ⭐`;
      result.style.display = 'block';
      result.scrollIntoView({ behavior: 'smooth' });
      updateProgress(100); // 测验完成，进度 100%
    }, 1000);
  }
}

function resetQuiz() {
  quizAnswers = [];
  earnedStars = 0;
  document.getElementById('quizResult').style.display = 'none';
  renderQuiz();
}

renderQuiz();
```

---

## 模块三：拖拽/连线游戏

### 拖拽匹配游戏

#### 功能说明

左侧显示一组项目，右侧显示对应答案，学生拖拽左项到右项完成匹配。

#### HTML + CSS + JS 实现

```html
<div class="game-section">
  <h2>🎮 拖拽匹配游戏</h2>
  <p class="game-instruction">把左边的图片拖到右边对应的文字上！</p>
  
  <div class="drag-game-area">
    <div class="drag-items" id="dragItems">
      <!-- 可拖拽元素 -->
    </div>
    <div class="drop-zones" id="dropZones">
      <!-- 放置区域 -->
    </div>
  </div>
  
  <div class="game-feedback" id="gameFeedback"></div>
</div>
```

```css
.drag-game-area {
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
  gap: 20px;
  padding: 20px;
}
.drag-item {
  width: 100px;
  height: 100px;
  background: white;
  border-radius: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 40px;
  cursor: grab;
  box-shadow: 0 3px 10px rgba(0,0,0,0.1);
  border: 3px solid var(--primary);
  user-select: none;
  transition: transform 0.2s;
}
.drag-item:active { cursor: grabbing; transform: scale(1.1); }
.drag-item.dragging { opacity: 0.5; }
.drop-zone {
  min-width: 120px;
  min-height: 100px;
  border: 3px dashed var(--primary);
  border-radius: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 18px;
  font-weight: 700;
  padding: 12px;
  transition: all 0.3s;
}
.drop-zone.drag-over {
  background: var(--secondary);
  border-style: solid;
  transform: scale(1.05);
}
.drop-zone.matched {
  background: var(--color-correct);
  color: white;
  border-color: var(--color-correct);
}
```

```javascript
// 拖拽游戏数据 — 根据具体知识点填充
const dragGameData = [
  { item: "🍕", match: "1/2", id: 0 },
  { item: "🍰", match: "1/4", id: 1 },
  { item: "🍫", match: "1/3", id: 2 },
  { item: "🍪", match: "1/8", id: 3 },
];

function initDragGame() {
  const itemsContainer = document.getElementById('dragItems');
  const zonesContainer = document.getElementById('dropZones');
  
  // 随机打乱顺序
  const shuffled = [...dragGameData].sort(() => Math.random() - 0.5);
  const shuffledZones = [...dragGameData].sort(() => Math.random() - 0.5);
  
  shuffled.forEach(d => {
    const el = document.createElement('div');
    el.className = 'drag-item';
    el.draggable = true;
    el.textContent = d.item;
    el.dataset.id = d.id;
    el.addEventListener('dragstart', onDragStart);
    el.addEventListener('dragend', onDragEnd);
    itemsContainer.appendChild(el);
  });
  
  shuffledZones.forEach(d => {
    const zone = document.createElement('div');
    zone.className = 'drop-zone';
    zone.textContent = d.match;
    zone.dataset.id = d.id;
    zone.addEventListener('dragover', onDragOver);
    zone.addEventListener('dragleave', onDragLeave);
    zone.addEventListener('drop', onDrop);
    zonesContainer.appendChild(zone);
  });
}

let draggedElement = null;

function onDragStart(e) {
  draggedElement = e.target;
  e.target.classList.add('dragging');
  e.dataTransfer.effectAllowed = 'move';
}

function onDragEnd(e) {
  e.target.classList.remove('dragging');
}

function onDragOver(e) {
  e.preventDefault();
  if (!e.currentTarget.classList.contains('matched')) {
    e.currentTarget.classList.add('drag-over');
  }
}

function onDragLeave(e) {
  e.currentTarget.classList.remove('drag-over');
}

function onDrop(e) {
  e.preventDefault();
  const zone = e.currentTarget;
  zone.classList.remove('drag-over');
  
  if (zone.classList.contains('matched')) return;
  
  const draggedId = draggedElement.dataset.id;
  const zoneId = zone.dataset.id;
  
  if (draggedId === zoneId) {
    // 匹配正确
    zone.classList.add('matched');
    zone.innerHTML += `<br><span style="font-size:32px">${draggedElement.textContent}</span>`;
    draggedElement.style.visibility = 'hidden';
    showGameFeedback('🎉 配对成功！', true);
    earnedStars++;
    showStarAnimation();
    checkGameComplete();
  } else {
    // 匹配错误
    showGameFeedback('💪 再试一次！', false);
    zone.style.animation = 'shake 0.3s';
    setTimeout(() => zone.style.animation = '', 300);
  }
}

function showGameFeedback(msg, correct) {
  const fb = document.getElementById('gameFeedback');
  fb.textContent = msg;
  fb.className = correct ? 'feedback-correct' : 'feedback-wrong';
  setTimeout(() => { fb.textContent = ''; }, 2000);
}

function checkGameComplete() {
  const matched = document.querySelectorAll('.drop-zone.matched').length;
  if (matched === dragGameData.length) {
    const fb = document.getElementById('gameFeedback');
    fb.innerHTML = '🎊 全部配对成功！你真棒！获得 ' + dragGameData.length + ' ⭐';
    updateProgress(75); // 游戏完成更新进度
  }
}

// 触摸设备支持（移动端拖拽）
function setupTouchDrag() {
  document.querySelectorAll('.drag-item').forEach(item => {
    let touchOffsetX, touchOffsetY;
    
    item.addEventListener('touchstart', (e) => {
      const touch = e.touches[0];
      const rect = item.getBoundingClientRect();
      touchOffsetX = touch.clientX - rect.left;
      touchOffsetY = touch.clientY - rect.top;
      draggedElement = item;
      item.classList.add('dragging');
    });
    
    item.addEventListener('touchmove', (e) => {
      e.preventDefault();
      const touch = e.touches[0];
      item.style.position = 'fixed';
      item.style.left = (touch.clientX - touchOffsetX) + 'px';
      item.style.top = (touch.clientY - touchOffsetY) + 'px';
      item.style.zIndex = 1000;
    });
    
    item.addEventListener('touchend', (e) => {
      item.classList.remove('dragging');
      item.style.position = '';
      item.style.left = '';
      item.style.top = '';
      item.style.zIndex = '';
      
      const touch = e.changedTouches[0];
      const target = document.elementFromPoint(touch.clientX, touch.clientY);
      const zone = target?.closest('.drop-zone');
      if (zone && !zone.classList.contains('matched')) {
        const fakeEvent = { preventDefault: () => {}, currentTarget: zone };
        onDrop(fakeEvent);
      }
    });
  });
}

initDragGame();
setupTouchDrag();
```

### 连线游戏（替代方案）

当拖拽不适合时，可使用点击连线方式：点击左侧一个项，再点击右侧一个项，自动连线。

```javascript
// 连线游戏核心逻辑
let selectedItem = null;

function onItemClick(itemId) {
  if (selectedItem === null) {
    selectedItem = itemId;
    document.querySelector(`[data-item-id="${itemId}"]`).classList.add('selected');
  } else {
    // 检查匹配
    if (selectedItem === itemId) {
      // 同侧取消选择
      document.querySelector(`[data-item-id="${itemId}"]`).classList.remove('selected');
    } else {
      drawLine(selectedItem, itemId);
    }
    selectedItem = null;
  }
}
```

---

## 模块四：学习进度追踪

### 功能说明

顶部固定进度条 + 星星计数器，实时反映学习进度。完成后展示总结徽章。

### HTML 结构

```html
<!-- 固定顶部进度条 -->
<div class="progress-header" id="progressHeader">
  <div class="progress-info">
    <span class="mascot-mini">🦊</span>
    <span class="progress-label">学习进度</span>
    <div class="progress-bar">
      <div class="progress-fill" id="progressFill" style="width: 0%"></div>
    </div>
    <span class="progress-percent" id="progressPercent">0%</span>
  </div>
  <div class="star-counter">
    <span class="star-icon">⭐</span>
    <span id="starCount">0</span>
  </div>
</div>

<!-- 完成总结 -->
<div class="completion-section" id="completionSection" style="display:none;">
  <div class="badge large-badge">🏆</div>
  <h2>恭喜完成学习！</h2>
  <p id="completionText"></p>
  <div class="star-display" id="starDisplay"></div>
  <button class="btn btn-large" onclick="location.reload()">再学一次 🔄</button>
</div>
```

### JavaScript 实现

```javascript
let totalProgress = 0;
let totalStars = 0;

function updateProgress(percent) {
  totalProgress = Math.max(totalProgress, percent);
  document.getElementById('progressFill').style.width = totalProgress + '%';
  document.getElementById('progressPercent').textContent = Math.round(totalProgress) + '%';
  
  if (totalProgress >= 100) {
    showCompletion();
  }
}

function showStarAnimation() {
  totalStars++;
  document.getElementById('starCount').textContent = totalStars;
  
  // 创建飞出的星星动画
  const star = document.createElement('div');
  star.className = 'flying-star';
  star.textContent = '⭐';
  document.body.appendChild(star);
  setTimeout(() => star.remove(), 1000);
}

function showCompletion() {
  const section = document.getElementById('completionSection');
  const text = document.getElementById('completionText');
  const stars = document.getElementById('starDisplay');
  
  let message = '';
  if (totalStars >= 8) {
    message = '你简直是学习小天才！🌟';
  } else if (totalStars >= 5) {
    message = '做得太棒了！继续加油！👍';
  } else {
    message = '完成了学习，下次可以做得更好！😊';
  }
  
  text.textContent = message;
  stars.innerHTML = '⭐'.repeat(totalStars) + '☆'.repeat(Math.max(0, 10 - totalStars));
  section.style.display = 'block';
}
```

### CSS 补充

```css
.progress-header {
  position: sticky;
  top: 0;
  z-index: 100;
  background: rgba(255,255,255,0.95);
  backdrop-filter: blur(10px);
  padding: 12px 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 2px 10px rgba(0,0,0,0.08);
  border-radius: 0 0 16px 16px;
}
.progress-info {
  display: flex;
  align-items: center;
  gap: 12px;
  flex: 1;
}
.progress-bar {
  width: 200px;
  height: 20px;
  background: #E0E0E0;
  border-radius: 10px;
  overflow: hidden;
}
.star-counter {
  font-size: 24px;
  font-weight: 700;
  display: flex;
  align-items: center;
  gap: 4px;
}
.flying-star {
  position: fixed;
  font-size: 30px;
  pointer-events: none;
  animation: starFly 1s ease-out forwards;
  z-index: 9999;
}
@keyframes starFly {
  0% { transform: scale(0) translateY(0); opacity: 1; }
  50% { transform: scale(1.5) translateY(-50px); opacity: 1; }
  100% { transform: scale(0.5) translateY(-150px); opacity: 0; }
}
```
