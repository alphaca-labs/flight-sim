# 웹 3D 비행시뮬레이션 — UI/HUD 디자인 가이드

> **컨셉:** 미래적 SF 전투기 콕핏 HUD — 에이스컴뱃/워 썬더 레퍼런스
> **구현:** Three.js `<canvas>` 위 HTML/CSS 오버레이 (`position: absolute`)
> **우선순위:** 데스크톱 퍼스트, 모바일 터치 기본 지원

---

## 1. 컬러 팔레트

### 1.1 기본 컬러

| 토큰 | HEX | RGB | 용도 |
|-------|------|-----|------|
| `--hud-bg` | `#0a0e17` | `10, 14, 23` | HUD 패널 배경 (투명도와 함께 사용) |
| `--hud-bg-glass` | `rgba(10, 14, 23, 0.65)` | — | 글래스모피즘 패널 배경 |
| `--hud-border` | `rgba(0, 243, 255, 0.25)` | — | 패널 보더 |
| `--hud-cyan` | `#00F3FF` | `0, 243, 255` | 주요 HUD 텍스트/아이콘/글로우 |
| `--hud-cyan-dim` | `#007A80` | `0, 122, 128` | 비활성 HUD 요소 |
| `--hud-green` | `#00FF88` | `0, 255, 136` | 정상 상태, 체크포인트 통과 |
| `--hud-amber` | `#FFB800` | `255, 184, 0` | 주의/경고 (스톨 임박) |
| `--hud-red` | `#FF2E4C` | `255, 46, 76` | 위험/충돌/저고도 |
| `--hud-white` | `#E8F0FF` | `232, 240, 255` | 수치/데이터 텍스트 |
| `--hud-white-dim` | `rgba(232, 240, 255, 0.5)` | — | 보조 텍스트/라벨 |

### 1.2 글로우 & 이펙트

| 토큰 | 값 | 용도 |
|-------|-----|------|
| `--glow-cyan` | `0 0 8px #00F3FF, 0 0 20px rgba(0, 243, 255, 0.3)` | 시안 텍스트/보더 글로우 |
| `--glow-green` | `0 0 8px #00FF88, 0 0 20px rgba(0, 255, 136, 0.3)` | 정상 상태 글로우 |
| `--glow-red` | `0 0 8px #FF2E4C, 0 0 20px rgba(255, 46, 76, 0.4)` | 경고 글로우 |
| `--glow-amber` | `0 0 8px #FFB800, 0 0 20px rgba(255, 184, 0, 0.3)` | 주의 글로우 |
| `--glass-blur` | `blur(12px)` | 글래스모피즘 블러 |
| `--scanline` | `repeating-linear-gradient(0deg, transparent, transparent 2px, rgba(0,243,255,0.03) 2px, rgba(0,243,255,0.03) 4px)` | 스캔라인 오버레이 |

### 1.3 그라데이션

```css
/* 패널 상단 하이라이트 */
--panel-gradient: linear-gradient(
  180deg,
  rgba(0, 243, 255, 0.08) 0%,
  rgba(10, 14, 23, 0.65) 100%
);

/* 위험 상태 배경 비네팅 */
--danger-vignette: radial-gradient(
  ellipse at center,
  transparent 50%,
  rgba(255, 46, 76, 0.15) 100%
);
```

---

## 2. 타이포그래피

### 2.1 폰트 스택

```css
/* 주요 HUD 폰트 — 계기판/모노스페이스 */
--font-hud: 'Share Tech Mono', 'JetBrains Mono', 'Fira Code', 'Courier New', monospace;

/* 타이틀/메뉴용 */
--font-display: 'Orbitron', 'Exo 2', 'Rajdhani', sans-serif;

/* Google Fonts CDN */
/* <link href="https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=Orbitron:wght@400;700;900&display=swap" rel="stylesheet"> */
```

### 2.2 타입 스케일

| 레벨 | 용도 | font-size | font-weight | letter-spacing | text-transform |
|-------|------|-----------|-------------|----------------|----------------|
| `hud-value-xl` | 속도/고도 수치 (대형) | `28px` | `700` | `2px` | `none` |
| `hud-value` | 속도/고도 수치 | `22px` | `700` | `2px` | `none` |
| `hud-label` | 계기 라벨 (SPD, ALT) | `11px` | `400` | `3px` | `uppercase` |
| `hud-data` | 부스트%, 좌표 등 | `14px` | `400` | `1px` | `none` |
| `hud-warning` | 경고 텍스트 | `18px` | `700` | `4px` | `uppercase` |
| `menu-title` | 게임 타이틀 | `48px` | `900` | `6px` | `uppercase` |
| `menu-subtitle` | 서브타이틀 | `16px` | `400` | `4px` | `uppercase` |
| `menu-button` | 버튼 텍스트 | `18px` | `700` | `3px` | `uppercase` |

### 2.3 공통 텍스트 스타일

```css
/* 모든 HUD 텍스트 기본 */
.hud-text {
  font-family: var(--font-hud);
  color: var(--hud-cyan);
  text-shadow: var(--glow-cyan);
  -webkit-font-smoothing: antialiased;
}

/* 수치 전용 — tabular figures로 숫자 정렬 */
.hud-numeric {
  font-variant-numeric: tabular-nums;
  font-feature-settings: 'tnum';
}
```

---

## 3. 글로벌 레이아웃

### 3.1 HUD 컨테이너

Three.js `<canvas>`와 동일 사이즈, 그 위에 absolute 포지셔닝.

```css
/* 전체 게임 컨테이너 */
#game-container {
  position: relative;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background: #000;
}

/* Three.js 캔버스 */
#game-canvas {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
}

/* HUD 오버레이 (Three.js 위) */
#hud-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 10;
  pointer-events: none;          /* 3D 클릭 통과 */
  user-select: none;
}

/* HUD 내부 인터랙티브 요소만 클릭 활성화 */
#hud-overlay .interactive {
  pointer-events: auto;
}
```

### 3.2 HUD 요소 배치 맵

```
┌─────────────────────────────────────────────────────┐
│  [스로틀바]                      [점수/체크포인트]   │
│   ┃ 좌상단                          우상단          │
│   ┃                                                 │
│ [속도계]     [  인공수평의  ]        [고도계]        │
│  좌측 중앙        중앙               우측 중앙       │
│                                                     │
│                                                     │
│ [미니맵]      [경고 텍스트]    [부스트 게이지]       │
│  좌하단          중앙 하단          우하단           │
└─────────────────────────────────────────────────────┘
```

---

## 4. HUD 요소 상세

### 4.1 공통 패널 스타일

모든 HUD 패널의 베이스 스타일.

```css
.hud-panel {
  position: absolute;
  background: var(--hud-bg-glass);
  backdrop-filter: var(--glass-blur);
  -webkit-backdrop-filter: var(--glass-blur);
  border: 1px solid var(--hud-border);
  border-radius: 4px;
  padding: 8px 12px;
  box-shadow:
    inset 0 1px 0 rgba(0, 243, 255, 0.1),
    0 0 15px rgba(0, 243, 255, 0.05);
}

/* 스캔라인 오버레이 (::after 의사 요소) */
.hud-panel::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: var(--scanline);
  pointer-events: none;
  border-radius: inherit;
  opacity: 0.5;
}
```

### 4.2 속도계 (좌측 중앙)

```css
#speedometer {
  position: absolute;
  left: 24px;
  top: 50%;
  transform: translateY(-50%);
  width: 100px;
  text-align: center;
}

#speedometer .hud-label {
  font-family: var(--font-hud);
  font-size: 11px;
  font-weight: 400;
  letter-spacing: 3px;
  text-transform: uppercase;
  color: var(--hud-white-dim);
  margin-bottom: 4px;
}

#speedometer .hud-value {
  font-family: var(--font-hud);
  font-size: 28px;
  font-weight: 700;
  letter-spacing: 2px;
  color: var(--hud-cyan);
  text-shadow: var(--glow-cyan);
  font-variant-numeric: tabular-nums;
  line-height: 1;
}

#speedometer .hud-unit {
  font-family: var(--font-hud);
  font-size: 11px;
  color: var(--hud-cyan-dim);
  letter-spacing: 1px;
  margin-top: 2px;
}

/* 속도 테이프 (세로 눈금바) */
#speed-tape {
  position: absolute;
  left: 130px;
  top: 50%;
  transform: translateY(-50%);
  width: 4px;
  height: 200px;
  background: rgba(0, 243, 255, 0.1);
  border-radius: 2px;
}

#speed-tape .indicator {
  position: absolute;
  bottom: 0;               /* 속도에 비례하여 height 조절 */
  width: 100%;
  background: linear-gradient(0deg, var(--hud-cyan), transparent);
  border-radius: 2px;
  box-shadow: 0 0 6px rgba(0, 243, 255, 0.4);
  transition: height 0.15s ease-out;
}
```

### 4.3 고도계 (우측 중앙)

```css
#altimeter {
  position: absolute;
  right: 24px;
  top: 50%;
  transform: translateY(-50%);
  width: 100px;
  text-align: center;
}

#altimeter .hud-label {
  font-family: var(--font-hud);
  font-size: 11px;
  font-weight: 400;
  letter-spacing: 3px;
  text-transform: uppercase;
  color: var(--hud-white-dim);
  margin-bottom: 4px;
}

#altimeter .hud-value {
  font-family: var(--font-hud);
  font-size: 28px;
  font-weight: 700;
  letter-spacing: 2px;
  color: var(--hud-cyan);
  text-shadow: var(--glow-cyan);
  font-variant-numeric: tabular-nums;
  line-height: 1;
}

#altimeter .hud-unit {
  font-family: var(--font-hud);
  font-size: 11px;
  color: var(--hud-cyan-dim);
  letter-spacing: 1px;
  margin-top: 2px;
}

/* 고도 테이프 (속도 테이프와 대칭) */
#alt-tape {
  position: absolute;
  right: 130px;
  top: 50%;
  transform: translateY(-50%);
  width: 4px;
  height: 200px;
  background: rgba(0, 243, 255, 0.1);
  border-radius: 2px;
}

/* 저고도 시 고도계 색상 변경 */
#altimeter.low-alt .hud-value {
  color: var(--hud-amber);
  text-shadow: var(--glow-amber);
}

#altimeter.danger-alt .hud-value {
  color: var(--hud-red);
  text-shadow: var(--glow-red);
  animation: hud-blink 0.5s ease-in-out infinite;
}
```

### 4.4 인공수평의 — Attitude Indicator (중앙)

CSS + JS로 롤/피치를 시각화하는 중앙 계기.

```css
#attitude-indicator {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  width: 200px;
  height: 200px;
  pointer-events: none;
}

/* 외곽 원형 프레임 */
#attitude-indicator .frame {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border: 2px solid rgba(0, 243, 255, 0.3);
  border-radius: 50%;
  box-shadow:
    inset 0 0 20px rgba(0, 243, 255, 0.05),
    0 0 10px rgba(0, 243, 255, 0.1);
}

/* 중앙 십자선 (고정) */
#attitude-indicator .crosshair {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  width: 40px;
  height: 2px;
  background: var(--hud-cyan);
  box-shadow: var(--glow-cyan);
}

#attitude-indicator .crosshair::before {
  content: '';
  position: absolute;
  left: 50%;
  top: -20px;
  transform: translateX(-50%);
  width: 2px;
  height: 40px;
  background: var(--hud-cyan);
  box-shadow: var(--glow-cyan);
}

/* 피치 래더 (JS에서 transform: translateY() 제어) */
#attitude-indicator .pitch-ladder {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);        /* JS에서 rotate + translateY 적용 */
  width: 120px;
  overflow: hidden;
}

#attitude-indicator .pitch-line {
  width: 60px;
  height: 1px;
  background: rgba(0, 243, 255, 0.5);
  margin: 20px auto;
}

#attitude-indicator .pitch-line.horizon {
  width: 100px;
  height: 2px;
  background: var(--hud-cyan);
  box-shadow: 0 0 6px rgba(0, 243, 255, 0.4);
}

/* 좌우 롤 마커 (삼각형) */
#attitude-indicator .roll-marker {
  position: absolute;
  top: 8px;
  left: 50%;
  transform: translateX(-50%);
  width: 0;
  height: 0;
  border-left: 6px solid transparent;
  border-right: 6px solid transparent;
  border-top: 8px solid var(--hud-cyan);
  filter: drop-shadow(0 0 4px rgba(0, 243, 255, 0.6));
}

/* 뱅크 앵글 아크 (상단 반원) — SVG 또는 border-radius */
#attitude-indicator .bank-arc {
  position: absolute;
  top: -4px;
  left: -4px;
  width: calc(100% + 8px);
  height: calc(100% + 8px);
  border: 1px solid rgba(0, 243, 255, 0.15);
  border-radius: 50%;
  clip-path: polygon(10% 0%, 90% 0%, 80% 25%, 20% 25%);
}
```

### 4.5 스로틀 바 (좌상단)

세로형 슬라이더로 현재 추력 % 표시.

```css
#throttle-bar {
  position: absolute;
  left: 24px;
  top: 24px;
  width: 32px;
  height: 180px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 6px;
}

#throttle-bar .label {
  font-family: var(--font-hud);
  font-size: 10px;
  letter-spacing: 2px;
  text-transform: uppercase;
  color: var(--hud-white-dim);
  writing-mode: vertical-rl;
  text-orientation: mixed;
  /* 또는 가로 라벨: 상단 배치 */
}

#throttle-bar .track {
  position: relative;
  width: 6px;
  height: 140px;
  background: rgba(0, 243, 255, 0.1);
  border-radius: 3px;
  border: 1px solid rgba(0, 243, 255, 0.15);
  overflow: hidden;
}

#throttle-bar .fill {
  position: absolute;
  bottom: 0;
  width: 100%;
  border-radius: 3px;
  background: linear-gradient(
    0deg,
    var(--hud-cyan-dim) 0%,
    var(--hud-cyan) 100%
  );
  box-shadow: 0 0 8px rgba(0, 243, 255, 0.4);
  transition: height 0.1s ease-out;
  /* JS에서 height: {throttle}% 설정 */
}

/* 100% 시 글로우 강화 */
#throttle-bar .fill.max {
  background: var(--hud-cyan);
  box-shadow: 0 0 12px rgba(0, 243, 255, 0.7);
}

#throttle-bar .value {
  font-family: var(--font-hud);
  font-size: 13px;
  font-weight: 700;
  color: var(--hud-cyan);
  text-shadow: var(--glow-cyan);
  font-variant-numeric: tabular-nums;
}
```

### 4.6 부스트 쿨다운 게이지 (우하단)

원형 게이지, conic-gradient로 쿨다운 시각화.

```css
#boost-gauge {
  position: absolute;
  right: 32px;
  bottom: 32px;
  width: 56px;
  height: 56px;
  display: flex;
  align-items: center;
  justify-content: center;
}

#boost-gauge .ring {
  position: absolute;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  /* JS에서 --boost-pct (0~100) CSS 변수 업데이트 */
  background: conic-gradient(
    var(--hud-cyan) calc(var(--boost-pct, 100) * 1%),
    rgba(0, 243, 255, 0.15) calc(var(--boost-pct, 100) * 1%)
  );
  mask: radial-gradient(
    circle,
    transparent 60%,
    black 61%
  );
  -webkit-mask: radial-gradient(
    circle,
    transparent 60%,
    black 61%
  );
  transition: none;              /* 실시간 업데이트 — transition 없음 */
}

/* 사용 가능 상태 (100%) */
#boost-gauge.ready .ring {
  box-shadow: 0 0 12px rgba(0, 243, 255, 0.5);
}

#boost-gauge .icon {
  font-family: var(--font-hud);
  font-size: 14px;
  font-weight: 700;
  color: var(--hud-cyan);
  text-shadow: var(--glow-cyan);
  z-index: 1;
  /* "BST" 또는 ⚡ 아이콘 */
}

/* 쿨다운 중 */
#boost-gauge.cooldown .icon {
  color: var(--hud-cyan-dim);
  text-shadow: none;
}

/* 사용 가능 시 펄스 */
#boost-gauge.ready .ring {
  animation: boost-pulse 2s ease-in-out infinite;
}

@keyframes boost-pulse {
  0%, 100% { opacity: 1; filter: brightness(1); }
  50% { opacity: 0.8; filter: brightness(1.3); }
}
```

### 4.7 미니맵 (좌하단)

```css
#minimap {
  position: absolute;
  left: 24px;
  bottom: 24px;
  width: 140px;
  height: 140px;
  border-radius: 50%;
  overflow: hidden;
  background: rgba(10, 14, 23, 0.75);
  backdrop-filter: var(--glass-blur);
  -webkit-backdrop-filter: var(--glass-blur);
  border: 1px solid rgba(0, 243, 255, 0.25);
  box-shadow:
    inset 0 0 20px rgba(0, 243, 255, 0.05),
    0 0 15px rgba(0, 243, 255, 0.08);
}

/* 미니맵 내부 캔버스 (2D 컨텍스트로 렌더링) */
#minimap canvas {
  width: 100%;
  height: 100%;
  border-radius: 50%;
}

/* 플레이어 마커 (중앙) */
#minimap .player-marker {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%) rotate(var(--heading, 0deg));
  width: 0;
  height: 0;
  border-left: 5px solid transparent;
  border-right: 5px solid transparent;
  border-bottom: 10px solid var(--hud-cyan);
  filter: drop-shadow(0 0 4px rgba(0, 243, 255, 0.8));
}

/* 체크포인트 점 */
#minimap .checkpoint-dot {
  position: absolute;
  width: 4px;
  height: 4px;
  border-radius: 50%;
  background: var(--hud-green);
  box-shadow: 0 0 4px var(--hud-green);
}

/* 외곽 나침반 표시 */
#minimap .compass-label {
  position: absolute;
  font-family: var(--font-hud);
  font-size: 9px;
  color: rgba(0, 243, 255, 0.5);
  letter-spacing: 1px;
}
#minimap .compass-label.north { top: 4px; left: 50%; transform: translateX(-50%); }
#minimap .compass-label.south { bottom: 4px; left: 50%; transform: translateX(-50%); }
#minimap .compass-label.east { right: 6px; top: 50%; transform: translateY(-50%); }
#minimap .compass-label.west { left: 6px; top: 50%; transform: translateY(-50%); }
```

### 4.8 점수 / 체크포인트 (우상단)

```css
#score-panel {
  position: absolute;
  right: 24px;
  top: 24px;
  text-align: right;
  min-width: 120px;
}

#score-panel .checkpoint-count {
  font-family: var(--font-hud);
  font-size: 22px;
  font-weight: 700;
  color: var(--hud-green);
  text-shadow: var(--glow-green);
  letter-spacing: 2px;
  font-variant-numeric: tabular-nums;
}

#score-panel .checkpoint-label {
  font-family: var(--font-hud);
  font-size: 10px;
  color: var(--hud-white-dim);
  letter-spacing: 3px;
  text-transform: uppercase;
  margin-top: 2px;
}

#score-panel .elapsed-time {
  font-family: var(--font-hud);
  font-size: 16px;
  font-weight: 400;
  color: var(--hud-white);
  text-shadow: 0 0 6px rgba(232, 240, 255, 0.2);
  letter-spacing: 1px;
  font-variant-numeric: tabular-nums;
  margin-top: 8px;
}

#score-panel .time-label {
  font-family: var(--font-hud);
  font-size: 10px;
  color: var(--hud-white-dim);
  letter-spacing: 3px;
  text-transform: uppercase;
  margin-top: 2px;
}

/* 체크포인트 통과 시 팝업 */
#checkpoint-popup {
  position: absolute;
  top: 30%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-family: var(--font-display);
  font-size: 24px;
  font-weight: 700;
  letter-spacing: 4px;
  text-transform: uppercase;
  color: var(--hud-green);
  text-shadow: var(--glow-green);
  opacity: 0;
  pointer-events: none;
}

#checkpoint-popup.show {
  animation: checkpoint-flash 1.2s ease-out forwards;
}

@keyframes checkpoint-flash {
  0% { opacity: 0; transform: translate(-50%, -50%) scale(0.8); }
  15% { opacity: 1; transform: translate(-50%, -50%) scale(1.1); }
  30% { transform: translate(-50%, -50%) scale(1); }
  100% { opacity: 0; transform: translate(-50%, -60%) scale(1); }
}
```

---

## 5. 경고 시스템

### 5.1 저고도 경고

```css
#warning-low-alt {
  position: absolute;
  bottom: 25%;
  left: 50%;
  transform: translateX(-50%);
  font-family: var(--font-hud);
  font-size: 18px;
  font-weight: 700;
  letter-spacing: 4px;
  text-transform: uppercase;
  color: var(--hud-amber);
  text-shadow: var(--glow-amber);
  opacity: 0;
  pointer-events: none;
}

#warning-low-alt.active {
  opacity: 1;
  animation: hud-blink 0.8s ease-in-out infinite;
}

/* 고도 100m 미만: 앰버 → 레드 전환 */
#warning-low-alt.critical {
  color: var(--hud-red);
  text-shadow: var(--glow-red);
  animation: hud-blink 0.4s ease-in-out infinite;
}
```

### 5.2 스톨 경고

```css
#warning-stall {
  position: absolute;
  top: 35%;
  left: 50%;
  transform: translateX(-50%);
  font-family: var(--font-hud);
  font-size: 20px;
  font-weight: 700;
  letter-spacing: 6px;
  text-transform: uppercase;
  color: var(--hud-red);
  text-shadow: var(--glow-red);
  opacity: 0;
  pointer-events: none;
}

#warning-stall.active {
  opacity: 1;
  animation: hud-blink 0.3s ease-in-out infinite;
}
```

### 5.3 공통 블링크 애니메이션

```css
@keyframes hud-blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.2; }
}
```

### 5.4 화면 비네팅 (위험 상태)

```css
#danger-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: var(--danger-vignette);
  pointer-events: none;
  opacity: 0;
  transition: opacity 0.3s ease;
  z-index: 5;                    /* HUD 아래, 캔버스 위 */
}

#danger-overlay.active {
  opacity: 1;
}
```

---

## 6. 충돌 오버레이

```css
#crash-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 50;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  pointer-events: none;
  opacity: 0;
}

#crash-overlay.active {
  opacity: 1;
  pointer-events: auto;
  animation: crash-in 0.3s ease-out forwards;
}

/* 배경 — 붉은 플래시 → 다크 페이드 */
#crash-overlay .backdrop {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(255, 46, 76, 0.4);
  animation: crash-flash 0.6s ease-out forwards;
}

@keyframes crash-flash {
  0% { background: rgba(255, 255, 255, 0.8); }
  20% { background: rgba(255, 46, 76, 0.5); }
  100% { background: rgba(10, 14, 23, 0.85); }
}

/* "CRASH" 텍스트 */
#crash-overlay .crash-text {
  position: relative;
  z-index: 1;
  font-family: var(--font-display);
  font-size: 64px;
  font-weight: 900;
  letter-spacing: 12px;
  text-transform: uppercase;
  color: var(--hud-red);
  text-shadow:
    0 0 20px rgba(255, 46, 76, 0.8),
    0 0 40px rgba(255, 46, 76, 0.4),
    0 0 80px rgba(255, 46, 76, 0.2);
}

/* 리스폰 카운트다운 */
#crash-overlay .respawn-timer {
  position: relative;
  z-index: 1;
  font-family: var(--font-hud);
  font-size: 16px;
  color: var(--hud-white-dim);
  letter-spacing: 2px;
  margin-top: 16px;
}

@keyframes crash-in {
  0% { opacity: 0; }
  100% { opacity: 1; }
}

/* 글리치 효과 (선택적) */
#crash-overlay .crash-text.glitch {
  animation: glitch-text 0.1s steps(2) 3;
}

@keyframes glitch-text {
  0% { transform: translate(0); }
  25% { transform: translate(-4px, 2px); }
  50% { transform: translate(4px, -2px); }
  75% { transform: translate(-2px, -1px); }
  100% { transform: translate(0); }
}
```

---

## 7. 메뉴 화면

### 7.1 시작 화면

```css
#start-screen {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 100;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: linear-gradient(
    180deg,
    rgba(10, 14, 23, 0.9) 0%,
    rgba(10, 14, 23, 0.7) 50%,
    rgba(10, 14, 23, 0.95) 100%
  );
  backdrop-filter: blur(4px);
  -webkit-backdrop-filter: blur(4px);
}

/* 게임 타이틀 */
#start-screen .title {
  font-family: var(--font-display);
  font-size: 48px;
  font-weight: 900;
  letter-spacing: 6px;
  text-transform: uppercase;
  color: var(--hud-cyan);
  text-shadow:
    0 0 10px rgba(0, 243, 255, 0.6),
    0 0 30px rgba(0, 243, 255, 0.3),
    0 0 60px rgba(0, 243, 255, 0.15);
  margin-bottom: 8px;
}

#start-screen .subtitle {
  font-family: var(--font-hud);
  font-size: 16px;
  font-weight: 400;
  letter-spacing: 4px;
  text-transform: uppercase;
  color: var(--hud-white-dim);
  margin-bottom: 48px;
}

/* 시작 버튼 */
#start-screen .start-btn {
  font-family: var(--font-display);
  font-size: 18px;
  font-weight: 700;
  letter-spacing: 3px;
  text-transform: uppercase;
  color: var(--hud-cyan);
  background: rgba(0, 243, 255, 0.08);
  border: 1px solid rgba(0, 243, 255, 0.4);
  border-radius: 4px;
  padding: 14px 48px;
  cursor: pointer;
  transition: all 0.2s ease;
  text-shadow: var(--glow-cyan);
  box-shadow:
    0 0 15px rgba(0, 243, 255, 0.1),
    inset 0 0 15px rgba(0, 243, 255, 0.05);
  pointer-events: auto;
}

#start-screen .start-btn:hover {
  background: rgba(0, 243, 255, 0.15);
  border-color: rgba(0, 243, 255, 0.7);
  box-shadow:
    0 0 25px rgba(0, 243, 255, 0.2),
    inset 0 0 20px rgba(0, 243, 255, 0.1);
  transform: scale(1.02);
}

#start-screen .start-btn:active {
  transform: scale(0.98);
  background: rgba(0, 243, 255, 0.2);
}

/* 조작법 토글 */
#start-screen .controls-toggle {
  font-family: var(--font-hud);
  font-size: 12px;
  letter-spacing: 2px;
  text-transform: uppercase;
  color: var(--hud-white-dim);
  background: none;
  border: none;
  cursor: pointer;
  margin-top: 32px;
  padding: 8px 16px;
  transition: color 0.2s ease;
  pointer-events: auto;
}

#start-screen .controls-toggle:hover {
  color: var(--hud-cyan);
}

/* 조작법 패널 */
#start-screen .controls-panel {
  margin-top: 16px;
  padding: 20px 32px;
  background: var(--hud-bg-glass);
  backdrop-filter: var(--glass-blur);
  -webkit-backdrop-filter: var(--glass-blur);
  border: 1px solid var(--hud-border);
  border-radius: 6px;
  max-height: 0;
  overflow: hidden;
  transition: max-height 0.4s ease, padding 0.4s ease;
  padding-top: 0;
  padding-bottom: 0;
}

#start-screen .controls-panel.open {
  max-height: 400px;
  padding-top: 20px;
  padding-bottom: 20px;
}

/* 키 바인딩 행 */
#start-screen .key-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 6px 0;
  border-bottom: 1px solid rgba(0, 243, 255, 0.08);
}

#start-screen .key-row:last-child {
  border-bottom: none;
}

#start-screen .key-name {
  font-family: var(--font-hud);
  font-size: 12px;
  color: var(--hud-white-dim);
  letter-spacing: 1px;
}

#start-screen .key-bind {
  font-family: var(--font-hud);
  font-size: 12px;
  color: var(--hud-cyan);
  background: rgba(0, 243, 255, 0.08);
  border: 1px solid rgba(0, 243, 255, 0.2);
  border-radius: 3px;
  padding: 2px 8px;
  letter-spacing: 1px;
}
```

### 7.2 일시정지 메뉴

```css
#pause-menu {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 100;
  display: none;                 /* JS에서 flex로 토글 */
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: rgba(10, 14, 23, 0.85);
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
}

#pause-menu.active {
  display: flex;
  animation: menu-fade-in 0.25s ease-out;
}

@keyframes menu-fade-in {
  0% { opacity: 0; }
  100% { opacity: 1; }
}

#pause-menu .pause-title {
  font-family: var(--font-display);
  font-size: 32px;
  font-weight: 700;
  letter-spacing: 8px;
  text-transform: uppercase;
  color: var(--hud-cyan);
  text-shadow: var(--glow-cyan);
  margin-bottom: 40px;
}

/* 메뉴 아이템 */
#pause-menu .menu-item {
  font-family: var(--font-display);
  font-size: 18px;
  font-weight: 700;
  letter-spacing: 3px;
  text-transform: uppercase;
  color: var(--hud-white-dim);
  background: none;
  border: 1px solid transparent;
  border-radius: 4px;
  padding: 12px 40px;
  margin: 6px 0;
  cursor: pointer;
  transition: all 0.2s ease;
  min-width: 240px;
  text-align: center;
  pointer-events: auto;
}

#pause-menu .menu-item:hover {
  color: var(--hud-cyan);
  border-color: rgba(0, 243, 255, 0.3);
  background: rgba(0, 243, 255, 0.06);
  text-shadow: var(--glow-cyan);
}

#pause-menu .menu-item:active {
  transform: scale(0.97);
  background: rgba(0, 243, 255, 0.12);
}
```

---

## 8. 모바일 터치 컨트롤

### 8.1 가상 조이스틱 (좌측)

```css
#virtual-joystick {
  position: absolute;
  left: 24px;
  bottom: 80px;
  width: 120px;
  height: 120px;
  border-radius: 50%;
  background: rgba(10, 14, 23, 0.5);
  border: 1px solid rgba(0, 243, 255, 0.2);
  pointer-events: auto;
  touch-action: none;
  display: none;                 /* 터치 디바이스에서만 표시 */
}

@media (pointer: coarse) {
  #virtual-joystick { display: block; }
}

#virtual-joystick .knob {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);   /* JS에서 드래그 위치로 업데이트 */
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: rgba(0, 243, 255, 0.2);
  border: 1px solid rgba(0, 243, 255, 0.5);
  box-shadow: 0 0 8px rgba(0, 243, 255, 0.3);
}
```

### 8.2 스로틀 슬라이더 (우측)

```css
#touch-throttle {
  position: absolute;
  right: 24px;
  bottom: 120px;
  width: 40px;
  height: 160px;
  border-radius: 20px;
  background: rgba(10, 14, 23, 0.5);
  border: 1px solid rgba(0, 243, 255, 0.2);
  pointer-events: auto;
  touch-action: none;
  display: none;
}

@media (pointer: coarse) {
  #touch-throttle { display: block; }
}

#touch-throttle .thumb {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);       /* JS에서 bottom 값 조절 */
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background: rgba(0, 243, 255, 0.25);
  border: 1px solid rgba(0, 243, 255, 0.5);
  box-shadow: 0 0 6px rgba(0, 243, 255, 0.3);
}
```

### 8.3 부스트 버튼 (우하단)

```css
#touch-boost {
  position: absolute;
  right: 80px;
  bottom: 32px;
  width: 56px;
  height: 56px;
  border-radius: 50%;
  background: rgba(0, 243, 255, 0.1);
  border: 2px solid rgba(0, 243, 255, 0.4);
  pointer-events: auto;
  touch-action: none;
  display: none;
  font-family: var(--font-hud);
  font-size: 11px;
  font-weight: 700;
  color: var(--hud-cyan);
  text-shadow: var(--glow-cyan);
  text-align: center;
  line-height: 56px;
  letter-spacing: 1px;
}

@media (pointer: coarse) {
  #touch-boost { display: block; }
}

#touch-boost:active {
  background: rgba(0, 243, 255, 0.25);
  box-shadow: 0 0 15px rgba(0, 243, 255, 0.5);
}

#touch-boost.cooldown {
  opacity: 0.4;
  border-color: rgba(0, 243, 255, 0.15);
}
```

---

## 9. 애니메이션 가이드

### 9.1 이징 & 듀레이션

| 카테고리 | duration | easing | 용도 |
|----------|----------|--------|------|
| HUD 수치 변화 | `100–150ms` | `ease-out` | 속도/고도 바 fill 높이 |
| 경고 깜빡임 | `300–800ms` | `ease-in-out` | 블링크 (위험도에 따라 빠르게) |
| 메뉴 전환 | `250ms` | `ease-out` | 페이드인/아웃 |
| 버튼 호버 | `200ms` | `ease` | 배경/보더 색상 변화 |
| 체크포인트 팝업 | `1200ms` | `ease-out` | 스케일 + 페이드 |
| 충돌 플래시 | `600ms` | `ease-out` | 배경 색상 전환 |
| 부스트 펄스 | `2000ms` | `ease-in-out` | 글로우 밝기 순환 |
| 글리치 | `100ms` | `steps(2)` | 충돌 텍스트 위치 떨림 |
| 패널 펼치기 | `400ms` | `ease` | max-height 전환 |

### 9.2 글로우 펄스 (공통)

HUD 요소에 생기를 부여하는 미묘한 글로우 순환.

```css
@keyframes glow-pulse {
  0%, 100% {
    text-shadow:
      0 0 8px rgba(0, 243, 255, 0.6),
      0 0 20px rgba(0, 243, 255, 0.3);
  }
  50% {
    text-shadow:
      0 0 12px rgba(0, 243, 255, 0.8),
      0 0 30px rgba(0, 243, 255, 0.4);
  }
}

/* 중요 수치에 적용 */
.glow-animate {
  animation: glow-pulse 3s ease-in-out infinite;
}
```

### 9.3 시작 화면 진입 시퀀스

```css
#start-screen .title {
  animation: title-enter 1s ease-out 0.2s both;
}

#start-screen .subtitle {
  animation: subtitle-enter 0.8s ease-out 0.6s both;
}

#start-screen .start-btn {
  animation: btn-enter 0.6s ease-out 1.0s both;
}

@keyframes title-enter {
  0% { opacity: 0; transform: translateY(-20px); letter-spacing: 20px; }
  100% { opacity: 1; transform: translateY(0); letter-spacing: 6px; }
}

@keyframes subtitle-enter {
  0% { opacity: 0; }
  100% { opacity: 1; }
}

@keyframes btn-enter {
  0% { opacity: 0; transform: translateY(10px); }
  100% { opacity: 1; transform: translateY(0); }
}
```

---

## 10. 반응형 Breakpoints

### 10.1 기본 전략

- **데스크톱 퍼스트** — 기본 스타일이 데스크톱용
- Three.js 캔버스는 항상 100vw × 100vh
- HUD 요소 크기/위치를 breakpoint별 조정
- 모바일에서는 터치 컨트롤 추가 표시

### 10.2 Breakpoints

```css
/* ──── Tablet (≤1024px) ──── */
@media (max-width: 1024px) {
  #speedometer .hud-value,
  #altimeter .hud-value {
    font-size: 22px;
  }

  #attitude-indicator {
    width: 160px;
    height: 160px;
  }

  #minimap {
    width: 110px;
    height: 110px;
  }

  #throttle-bar {
    height: 140px;
  }

  #start-screen .title {
    font-size: 36px;
    letter-spacing: 4px;
  }

  #crash-overlay .crash-text {
    font-size: 48px;
    letter-spacing: 8px;
  }
}

/* ──── Mobile (≤768px) ──── */
@media (max-width: 768px) {
  #speedometer {
    left: 16px;
    width: 80px;
  }

  #speedometer .hud-value,
  #altimeter .hud-value {
    font-size: 18px;
  }

  #speedometer .hud-label,
  #altimeter .hud-label {
    font-size: 9px;
  }

  #altimeter {
    right: 16px;
    width: 80px;
  }

  #attitude-indicator {
    width: 120px;
    height: 120px;
  }

  /* 미니맵 축소 */
  #minimap {
    width: 90px;
    height: 90px;
    left: 16px;
    bottom: 100px;            /* 조이스틱 위로 이동 */
  }

  /* 스코어 패널 축소 */
  #score-panel {
    right: 16px;
    top: 16px;
  }

  #score-panel .checkpoint-count {
    font-size: 18px;
  }

  #score-panel .elapsed-time {
    font-size: 13px;
  }

  /* 스로틀 바 → 상단 가로로 전환 또는 숨김 (터치 슬라이더가 대체) */
  #throttle-bar {
    display: none;
  }

  /* 부스트 게이지 축소 */
  #boost-gauge {
    width: 44px;
    height: 44px;
    right: 24px;
    bottom: 100px;            /* 터치 부스트 버튼 위 */
  }

  /* 속도/고도 테이프 숨김 */
  #speed-tape,
  #alt-tape {
    display: none;
  }

  /* 시작 화면 */
  #start-screen .title {
    font-size: 28px;
    letter-spacing: 3px;
  }

  #start-screen .subtitle {
    font-size: 12px;
    letter-spacing: 2px;
  }

  #start-screen .start-btn {
    font-size: 16px;
    padding: 12px 36px;
  }

  /* 충돌 오버레이 */
  #crash-overlay .crash-text {
    font-size: 36px;
    letter-spacing: 6px;
  }

  /* 일시정지 메뉴 */
  #pause-menu .pause-title {
    font-size: 24px;
    letter-spacing: 5px;
  }

  #pause-menu .menu-item {
    font-size: 16px;
    padding: 10px 32px;
    min-width: 200px;
  }
}

/* ──── Small Mobile (≤480px) ──── */
@media (max-width: 480px) {
  #speedometer .hud-value,
  #altimeter .hud-value {
    font-size: 16px;
  }

  #attitude-indicator {
    width: 90px;
    height: 90px;
  }

  #minimap {
    width: 70px;
    height: 70px;
  }

  #start-screen .title {
    font-size: 22px;
  }

  #crash-overlay .crash-text {
    font-size: 28px;
  }
}
```

### 10.3 성능 우선 미디어 쿼리

```css
/* 저사양 기기: 애니메이션/글로우 최소화 */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }

  .hud-text { text-shadow: none; }
  .hud-panel { backdrop-filter: none; }
}

/* 블러 미지원 기기 폴백 */
@supports not (backdrop-filter: blur(1px)) {
  .hud-panel {
    background: rgba(10, 14, 23, 0.85);
  }
}
```

---

## 11. CSS 커스텀 속성 종합

`<style>` 최상단에 선언할 전체 변수 목록.

```css
:root {
  /* Colors */
  --hud-bg: #0a0e17;
  --hud-bg-glass: rgba(10, 14, 23, 0.65);
  --hud-border: rgba(0, 243, 255, 0.25);
  --hud-cyan: #00F3FF;
  --hud-cyan-dim: #007A80;
  --hud-green: #00FF88;
  --hud-amber: #FFB800;
  --hud-red: #FF2E4C;
  --hud-white: #E8F0FF;
  --hud-white-dim: rgba(232, 240, 255, 0.5);

  /* Glows */
  --glow-cyan: 0 0 8px #00F3FF, 0 0 20px rgba(0, 243, 255, 0.3);
  --glow-green: 0 0 8px #00FF88, 0 0 20px rgba(0, 255, 136, 0.3);
  --glow-red: 0 0 8px #FF2E4C, 0 0 20px rgba(255, 46, 76, 0.4);
  --glow-amber: 0 0 8px #FFB800, 0 0 20px rgba(255, 184, 0, 0.3);

  /* Effects */
  --glass-blur: blur(12px);
  --scanline: repeating-linear-gradient(
    0deg, transparent, transparent 2px,
    rgba(0,243,255,0.03) 2px, rgba(0,243,255,0.03) 4px
  );

  /* Fonts */
  --font-hud: 'Share Tech Mono', 'JetBrains Mono', 'Fira Code', 'Courier New', monospace;
  --font-display: 'Orbitron', 'Exo 2', 'Rajdhani', sans-serif;

  /* Dynamic (JS 업데이트) */
  --boost-pct: 100;
  --heading: 0deg;
}
```

---

## 12. HTML 구조 요약

```html
<div id="game-container">
  <canvas id="game-canvas"></canvas>

  <!-- 위험 비네팅 (z:5) -->
  <div id="danger-overlay"></div>

  <!-- HUD 레이어 (z:10) -->
  <div id="hud-overlay">
    <!-- 좌상단: 스로틀 -->
    <div id="throttle-bar" class="hud-panel">...</div>

    <!-- 좌측 중앙: 속도계 -->
    <div id="speedometer" class="hud-panel">
      <div class="hud-label">SPD</div>
      <div class="hud-value hud-numeric">000</div>
      <div class="hud-unit">km/h</div>
    </div>
    <div id="speed-tape">...</div>

    <!-- 중앙: 인공수평의 -->
    <div id="attitude-indicator">...</div>

    <!-- 우측 중앙: 고도계 -->
    <div id="altimeter" class="hud-panel">
      <div class="hud-label">ALT</div>
      <div class="hud-value hud-numeric">0000</div>
      <div class="hud-unit">m</div>
    </div>
    <div id="alt-tape">...</div>

    <!-- 우상단: 점수 -->
    <div id="score-panel">...</div>

    <!-- 좌하단: 미니맵 -->
    <div id="minimap">...</div>

    <!-- 우하단: 부스트 -->
    <div id="boost-gauge">...</div>

    <!-- 경고 -->
    <div id="warning-low-alt">⚠ PULL UP</div>
    <div id="warning-stall">⚠ STALL</div>

    <!-- 체크포인트 팝업 -->
    <div id="checkpoint-popup">CHECKPOINT</div>

    <!-- 모바일 터치 -->
    <div id="virtual-joystick">...</div>
    <div id="touch-throttle">...</div>
    <div id="touch-boost">BST</div>
  </div>

  <!-- 충돌 오버레이 (z:50) -->
  <div id="crash-overlay">
    <div class="backdrop"></div>
    <div class="crash-text">CRASH</div>
    <div class="respawn-timer">Respawning in 3...</div>
  </div>

  <!-- 메뉴 (z:100) -->
  <div id="start-screen">...</div>
  <div id="pause-menu">...</div>
</div>
```

---

## 13. 디자인 노트

### 시각적 계층
1. **Three.js 캔버스** — z:0, 3D 월드
2. **위험 비네팅** — z:5, 상태에 따라 투명/비네팅
3. **HUD 오버레이** — z:10, 상시 표시, `pointer-events: none`
4. **충돌 오버레이** — z:50, 충돌 시에만 표시
5. **메뉴 레이어** — z:100, 시작/일시정지

### 구현 팁
- `backdrop-filter` 미지원 시 불투명도 높인 폴백 사용
- `font-variant-numeric: tabular-nums`로 숫자 정렬 유지 (계기판 떨림 방지)
- HUD 수치 업데이트는 `textContent` 직접 변경 (DOM 오버헤드 최소)
- 경고 상태는 CSS 클래스 토글로 제어 (`.active`, `.critical`)
- `conic-gradient` 부스트 게이지는 `--boost-pct` CSS 변수로 JS에서 제어
- 모바일 터치 컨트롤은 `@media (pointer: coarse)` 감지로 자동 표시
- `prefers-reduced-motion` 존중하여 접근성 확보
