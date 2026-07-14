# 卡通设计系统 — Design System

## 配色方案

### 学科主色调

每个学科有专属主色调，用于页面的主要装饰元素（标题栏、按钮、进度条等）：

| 学科 | 主色 | 辅色 | 背景渐变 |
|------|------|------|----------|
| 语文 | `#E74C3C` (暖红) | `#F9E79F` (浅黄) | `#FDF2E9 → #FDEBD0` |
| 数学 | `#3498DB` (天蓝) | `#AED6F1` (浅蓝) | `#EBF5FB → #D6EAF8` |
| 英语 | `#9B59B6` (紫色) | `#E8DAEF` (浅紫) | `#F5EEF8 → #EBDEF0` |
| 科学 | `#27AE60` (草绿) | `#ABEBC6` (浅绿) | `#EAFAF1 → #D5F5E3` |
| 道德与法治 | `#E67E22` (橙色) | `#FAE5D3` (浅橙) | `#FDF2E9 → `#FAE5D3` |
| 美术 | `#E91E63` (粉红) | `#F8BBD0` (浅粉) | `#FCE4EC → #F8BBD0` |
| 音乐 | `#FF9800` (金黄) | `#FFE0B2` (浅金) | `#FFF8E1 → #FFE0B2` |

### 通用辅助色

```css
--color-correct: #2ECC71;    /* 答对 - 绿色 */
--color-wrong: #E74C3C;      /* 答错 - 红色（但文案用"再试一次"） */
--color-star: #F1C40F;       /* 星星 - 金色 */
--color-bg-card: #FFFFFF;    /* 卡片背景 - 白色 */
--color-text-main: #2C3E50;  /* 主文字 - 深蓝灰 */
--color-text-light: #7F8C8D; /* 辅助文字 - 灰色 */
--color-shadow: rgba(0,0,0,0.1); /* 阴影 */
```

### 渐变与装饰

- 页面背景使用学科对应的柔和渐变
- 卡片使用白色背景 + 柔和阴影 `box-shadow: 0 4px 15px rgba(0,0,0,0.08)`
- 按钮使用主色到辅色的渐变 `linear-gradient(135deg, var(--primary), var(--secondary))`
- 进度条使用主色渐变 + 发光效果

## 字体规范

```css
/* 全局字体栈 - 优先使用系统圆体字 */
font-family: "Comic Sans MS", "YouYuan", "幼圆", "Microsoft YaHei", 
             "PingFang SC", sans-serif;

/* 字号层级 */
--fs-hero: 36px;      /* 封面大标题 */
--fs-h1: 28px;        /* 章节标题 */
--fs-h2: 22px;        /* 小节标题 */
--fs-body: 18px;      /* 正文（最小字号） */
--fs-large: 24px;     /* 重点文字 */
--fs-button: 20px;    /* 按钮文字 */
--fs-caption: 14px;   /* 辅助说明 */
```

**规则**：
- 正文字号不小于 18px，照顾小学生视力
- 标题加粗 `font-weight: 700`
- 知识点关键词使用主色 + 加粗强调
- 行间距 `line-height: 1.8`，段落间距充足

## 布局规范

### 整体结构

```
┌─────────────────────────────────┐
│        封面区（全屏高度）         │
│   [卡通形象] [知识点标题]         │
│       [开始学习 按钮]            │
├─────────────────────────────────┤
│      [进度条] [星星计数]          │  ← 固定顶部
├─────────────────────────────────┤
│    知识讲解区（分步骤展开）       │
│   [步骤1] [步骤2] [步骤3]...     │
├─────────────────────────────────┤
│    互动练习区                    │
│   [选择题] [填空题]              │
│   [拖拽游戏] [连线游戏]          │
├─────────────────────────────────┤
│    完成总结区                    │
│   [获得徽章] [学习总结]          │
│   [再玩一次] [分享]              │
└─────────────────────────────────┘
```

### 响应式断点

```css
/* 移动端优先 */
.container { max-width: 100%; padding: 16px; }

/* 平板 */
@media (min-width: 768px) {
  .container { max-width: 720px; padding: 24px; }
}

/* 桌面 */
@media (min-width: 1024px) {
  .container { max-width: 900px; padding: 32px; }
}
```

### 卡片样式

```css
.card {
  background: #FFFFFF;
  border-radius: 20px;
  padding: 24px;
  margin-bottom: 20px;
  box-shadow: 0 4px 15px rgba(0,0,0,0.08);
  border: 3px solid transparent;
  transition: all 0.3s ease;
}
.card:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(0,0,0,0.12);
}
```

### 按钮样式

```css
.btn {
  padding: 14px 32px;
  font-size: 20px;
  font-weight: 700;
  border: none;
  border-radius: 50px;
  cursor: pointer;
  background: linear-gradient(135deg, var(--primary), var(--secondary));
  color: white;
  box-shadow: 0 4px 12px rgba(0,0,0,0.15);
  transition: all 0.2s ease;
}
.btn:hover { transform: scale(1.05); }
.btn:active { transform: scale(0.98); }
.btn-large { padding: 18px 48px; font-size: 24px; }
```

## 卡通插画实现方式

### 优先级（按优先使用顺序）

1. **Emoji** — 大量使用 emoji 作为装饰和图标，简单高效
   - 角色：🦊🐱🐰🐼🦁🐸
   - 装饰：⭐🌟✨🎈🎉🎊
   - 学科：📚🔢🔤🔬⚖️🎨🎵

2. **CSS 绘制** — 用纯 CSS 绘制简单卡通形状
   - 圆形角色头部：`border-radius: 50%`
   - 云朵：多个 `border-radius` 组合
   - 对话气泡：`border-radius` + `::after` 三角

3. **内联 SVG** — 需要复杂图形时使用内联 SVG
   - 卡通角色全身
   - 场景插图
   - 复杂图标

### 对话气泡样式

```css
.speech-bubble {
  position: relative;
  background: #FFFFFF;
  border-radius: 18px;
  padding: 16px 20px;
  box-shadow: 0 3px 10px rgba(0,0,0,0.1);
  border: 2px solid var(--primary);
}
.speech-bubble::after {
  content: '';
  position: absolute;
  bottom: -12px;
  left: 30px;
  border: 6px solid transparent;
  border-top-color: var(--primary);
}
```

## 动画规范

### 通用动画

```css
/* 弹入动画 - 元素出现时 */
@keyframes bounceIn {
  0% { transform: scale(0.3); opacity: 0; }
  50% { transform: scale(1.05); }
  70% { transform: scale(0.95); }
  100% { transform: scale(1); opacity: 1; }
}

/* 摇摆动画 - 卡通角色待机 */
@keyframes wobble {
  0%, 100% { transform: rotate(-3deg); }
  50% { transform: rotate(3deg); }
}

/* 浮动动画 - 装饰元素 */
@keyframes float {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}

/* 星星弹出 - 获得奖励时 */
@keyframes starPop {
  0% { transform: scale(0) rotate(0deg); opacity: 0; }
  50% { transform: scale(1.3) rotate(180deg); }
  100% { transform: scale(1) rotate(360deg); opacity: 1; }
}

/* 抖动 - 答错时 */
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-8px); }
  75% { transform: translateX(8px); }
}
```

### 使用原则

- 页面加载时元素依次弹入（`animation-delay` 递增）
- 卡通角色持续轻微摇摆/浮动，营造活力感
- 交互反馈动画时长 0.3-0.5s，不宜过长
- 答对：绿色闪光 + 星星弹出 + 角色欢呼
- 答错：轻微抖动 + 角色鼓励 + 提示再试

## 进度条样式

```css
.progress-bar {
  width: 100%;
  height: 24px;
  background: #E0E0E0;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: inset 0 2px 4px rgba(0,0,0,0.1);
}
.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, var(--primary), var(--secondary));
  border-radius: 12px;
  transition: width 0.5s ease;
  box-shadow: 0 0 10px rgba(52,152,219,0.5);
}
```

## 星星与徽章

```css
.star-earned {
  animation: starPop 0.5s ease forwards;
  filter: drop-shadow(0 2px 4px rgba(241,196,15,0.5));
}
.star-empty { opacity: 0.3; filter: grayscale(100%); }

.badge {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 36px;
  background: linear-gradient(135deg, #F1C40F, #E67E22);
  box-shadow: 0 4px 15px rgba(241,196,15,0.4);
  border: 4px solid #FFFFFF;
}
```
