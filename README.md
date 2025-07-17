# 숲 속 소년의 전도 여행 게임 소스 설명서

**목차**
1. [개요](#개요)
2. [전체 구조](#전체-구조)
3. [스타일(CSS) 설명](#스타일css-설명)
4. [HTML 구조 설명](#html-구조-설명)
5. [JavaScript 코드 설명](#javascript-코드-설명)
    - [1. 캔버스 및 변수 초기화](#1-캔버스-및-변수-초기화)
    - [2. SVG 이미지 리소스](#2-svg-이미지-리소스)
    - [3. 게임 데이터 구조](#3-게임-데이터-구조)
    - [4. 주요 함수 설명](#4-주요-함수-설명)
        - [isOccupied](#isoccupied)
        - [placeItemNoOverlap](#placeitemnooverlap)
        - [resetGame](#resetgame)
        - [draw](#draw)
        - [update](#update)
    - [5. 입력(키보드, 터치) 처리](#5-입력키보드-터치-처리)
    - [6. 게임 시작 및 이미지 로딩](#6-게임-시작-및-이미지-로딩)
6. [게임 플레이 방법](#게임-플레이-방법)

---

## 개요

이 문서는 **숲 속 소년의 전도 여행 게임**의 HTML/JS 소스 코드를 쉽게 이해할 수 있도록, 각 부분별로 상세히 설명합니다.

- **플랫폼**: 웹 브라우저 (PC/모바일)
- **주요 특징**:  
  - 키보드/가상버튼 이동  
  - SVG 기반 캐릭터, 십자가, 성경, 나무  
  - 겹치지 않는 랜덤 아이템/장애물 배치  
  - 점수 및 게임오버, 수집완료 표시  
  - '다시 시작' 버튼 제공

---

## 전체 구조

- `<head>`: 문서 정보, 스타일 정의  
- `<body>`:  
    - 캔버스(Canvas) – 게임 화면  
    - 점수 표시 영역  
    - 재시작 버튼  
    - 모바일 방향키(가상 버튼)  
- `<script>`: 게임 동작 로직(JS)

---

## 스타일(CSS) 설명

```css
body { background: #b2e59f; margin: 0; }
canvas { display: block; margin: 30px auto; background: #7ec850; border: 3px solid #333; touch-action: none; }
#score { text-align: center; font-size: 1.5em; margin-top: 10px; }
.dir-btn { ... }
.dir-btn:active { ... }

```

- 배경색과 기본 레이아웃 지정
- 캔버스(game area)는 중앙 정렬, 둥근 테두리
- 점수 표시는 상단 중앙
- .dir-btn: 모바일 터치 방향키 버튼(크고, 둥근 스타일)
- 터치 시 색상 변환(피드백)
  
---

## HTML 구조 설명

```html
<canvas id="game" width="480" height="320"></canvas>
<div id="score">점수: 0</div>
<div style="text-align:center;">
  <button id="restartBtn" style="display:none;">다시 시작하기</button>
</div>
<div id="touch-controls" style="text-align:center; margin-top:8px;">
  <button class="dir-btn" data-dir="up">⬆️</button><br>
  <button class="dir-btn" data-dir="left">⬅️</button>
  <button class="dir-btn" data-dir="down">⬇️</button>
  <button class="dir-btn" data-dir="right">➡️</button>
</div>

```

- Canvas: 실제 게임 그래픽 표시
- 점수: 현재 점수 및 상태 표시
- 재시작 버튼: 게임오버/성공 시 등장
- 가상 방향키: 모바일 조작용(각 버튼에 data-dir 속성)

---

## JavaScript 코드 설명

### 1. 캔버스 및 변수 초기화

```js
const canvas = document.getElementById('game');
const ctx = canvas.getContext('2d');
const tile = 40; // 한 칸 크기(px)
const mapW = canvas.width / tile;
const mapH = canvas.height / tile;

```

- 캔버스/2D 컨텍스트 준비
- 맵은 '타일' 단위로 분할 (가로x세로)

---

### 2. SVG 이미지 리소스

- 각 오브젝트(플레이어, 십자가, 성경, 나무)를 SVG로 정의
- SVG → Image로 변환(svgToImg 함수)

```js
const playerSVG = `<svg ...>...</svg>`;
const playerImg = svgToImg(playerSVG);

```

- SVG를 data URL로 만들어 이미지 객체로 변환

---

### 3. 게임 데이터 구조

- player: 위치(x, y)
- trees: 장애물 배열({x, y})
- items: 수집 아이템 배열({x, y, type})
- score: 현재 점수
- gameOver: 게임 상태

---

### 4. 주요 함수 설명

```js
function isOccupied(x, y, arr) {
  return arr.some(obj => obj.x === x && obj.y === y);
}

```
- 특정 위치에 이미 객체가 있는지 확인

```js
function placeItemNoOverlap(type, items, trees, player) {
  // 겹치지 않는 곳에 랜덤 배치
}

```
- 장애물/플레이어와 겹치지 않는 칸에 아이템 배치
  
```js
function resetGame() {
  // 모든 게임 변수 초기화, 랜덤 배치, 점수 초기화 등
}

```
- 새 게임 시작 시 모든 데이터 초기화 및 배치

```js
function draw() {
  // 모든 객체(나무, 아이템, 플레이어) 캔버스에 그림
}

```
- 화면 갱신

```js
function update() {
  // 충돌(장애물/아이템), 점수 처리, 성공/실패 판정
}

```
- 이동 후 상태 업데이트, 아이템 수집/게임오버 판정

---

### 5. 입력(키보드, 터치) 처리

```js
document.addEventListener('keydown', e => {
  // 화살표 방향키 입력 시 이동
});
document.querySelectorAll('.dir-btn').forEach(btn => {
  btn.addEventListener('touchstart', ...);
});

```

- 방향키(↑, ↓, ←, →)로 한 칸씩 이동
- 각 버튼별 방향 데이터 읽어 이동

---

### 6. 게임 시작 및 이미지 로딩

- 이미지가 로드된 후 draw() 실행
- 최초 진입 시 resetGame()으로 초기화
-restartBtn(재시작) 클릭 시에도 resetGame()
---

## 게임 플레이 방법

1. **방향키(↑, ↓, ←, →)** 로 소년을 이동합니다.
2. 숲 속에 흩어진 **아이템(✝️, 📖)** 을 모두 모으세요.
3. **나무(🌲)** 에 부딪히면 게임 오버입니다.
4. 모든 아이템을 모으면 클리어!\
   → '다시 시작하기' 버튼으로 재도전 가능.

---
