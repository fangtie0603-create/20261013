---
title: 選擇題測驗卷網站講義（學生版）

---

---
title: 選擇題測驗卷網站講義（學生版）
tags: [114程式設計與實習_上學期]

---

# 選擇題測驗卷網站講義（學生版）

學號：415730893　　姓名：余方鉞

> **填寫方式**
> 1. 每個學習都要放：**執行截圖**、**三次問 AI 的提示詞**、**最後採用的程式碼**。
> 2. 問 AI 的提示詞請**逐字貼上**自己實際輸入的內容（不要寫摘要），第一次、第二次、第三次依序記錄。
> 3. 程式碼貼在「點開貼上」的收合區塊裡，貼上**你最後真正採用、而且能執行**的版本。

---

## 學習1：產生一個選擇題測驗卷網站

https://cfchen58.synology.me/115/week4/stage1/

**這個階段的目標：** 用 p5.js 做出一個一次顯示一題、四個選項、答完會顯示對錯與總分的測驗網站（題目先寫在程式裡）。
**這個階段會修改的檔案：** index.html、sketch.js

### 執行截圖



![學習1截圖]![image](https://hackmd.io/_uploads/ByFZylMoGx.png)


### 第一次問 AI

```tex!
（逐字貼上你第一次問 AI 的提示詞）
```

### 第二次問 AI

```tex!
（逐字貼上你第二次問 AI 的提示詞）
```

### 第三次問 AI

```tex!
（逐字貼上你第三次問 AI 的提示詞）
```

### 程式碼內容

:::info
:::spoiler 點開貼上學習1的程式碼
```javascript=
//// 題庫資料陣列：包含題目、四個選項、以及正確答案的索引 (0 代表第一個選項)
let questions = [
  {
    question: "p5.js 是基於哪一種程式語言延伸而來的？",
    options: ["Python", "JavaScript", "C++", "Java"],
    answer: 1
  },
  {
    question: "在 p5.js 中，用來設定畫布大小的函式是什麼？",
    options: ["size()", "canvas()", "createCanvas()", "windowSize()"],
    answer: 2
  },
  {
    question: "下列哪一個函式會在網頁載入時執行且只執行一次？",
    options: ["setup()", "draw()", "preload()", "mousePressed()"],
    answer: 0
  },
  {
    question: "哪一個 p5.js 函式會持續不斷地重複執行以產生動畫？",
    options: ["setup()", "draw()", "loop()", "update()"],
    answer: 1
  }
];

let currentQuestion = 0; // 目前題目的索引
let score = 0;           // 累計得分
let state = "quiz";      // 遊戲狀態: 'quiz' (作答中), 'feedback' (顯示回饋), 'end' (結算)
let optionButtons = [];  // 儲存四個選項按鈕

function setup() {
  createCanvas(600, 480);
  textAlign(CENTER, CENTER);
  
  // 建立四個選項按鈕
  for (let i = 0; i < 4; i++) {
    let btn = createButton('');
    btn.size(440, 45);
    btn.position(width / 2 - 220, 200 + i * 60);
    btn.style('font-size', '16px');
    btn.style('background-color', '#ffffff');
    btn.style('border', '1px solid #ccc');
    btn.style('border-radius', '8px');
    btn.style('cursor', 'pointer');
    
    // 點擊事件
    let index = i;
    btn.mousePressed(() => checkAnswer(index));
    optionButtons.push(btn);
  }
  
  updateQuestion();
}

function draw() {
  background(240, 244, 248);
  
  if (state === "quiz" || state === "feedback") {
    // 顯示進度與標題
    fill(80);
    textSize(18);
    text(`第 \({currentQuestion + 1} 題 / 共\){questions.length} 題`, width / 2, 45);
    
    // 顯示題目
    fill(30, 40, 50);
    textSize(20);
    text(questions[currentQuestion].question, width / 2, 110);
    
    // 顯示答題回饋
    if (state === "feedback") {
      textSize(18);
      let q = questions[currentQuestion];
      if (selectedOption === q.answer) {
        fill(40, 160, 80);
        text("答對了！🎉", width / 2, 445);
      } else {
        fill(220, 60, 60);
        text(`答錯囉！正確答案是：${q.options[q.answer]}`, width / 2, 445);
      }
    }
    
  } else if (state === "end") {
    // 隱藏所有選項按鈕
    for (let btn of optionButtons) {
      btn.hide();
    }
    
    // 顯示結算畫面
    fill(30, 40, 50);
    textSize(32);
    text("測驗結束！", width / 2, 140);
    
    textSize(24);
    text(`你的總分是：${score} 分`, width / 2, 220);
    
    let totalPossible = questions.length * 25; // 每題 25 分
    let percentage = (score / totalPossible) * 100;
    text(`正確率：${percentage}%`, width / 2, 270);
  }
}

let selectedOption = null;

function updateQuestion() {
  if (currentQuestion < questions.length) {
    let q = questions[currentQuestion];
    for (let i = 0; i < 4; i++) {
      optionButtons[i].show();
      optionButtons[i].html(q.options[i]);
      optionButtons[i].style('background-color', '#ffffff');
    }
    state = "quiz";
  } else {
    state = "end";
  }
}

function checkAnswer(index) {
  if (state !== "quiz") return; // 避免在回饋等待期間重複點擊
  
  selectedOption = index;
  state = "feedback";
  
  let q = questions[currentQuestion];
  
  // 檢查對錯並給予按鈕顏色提示
  if (index === q.answer) {
    score += 25; // 每題 25 分
    optionButtons[index].style('background-color', '#d4edda'); // 淺綠色
  } else {
    optionButtons[index].style('background-color', '#f8d7da'); // 淺紅色
    optionButtons[q.answer].style('background-color', '#d4edda'); // 標示出正確答案
  }
  
  // 延遲 1.5 秒後自動進入下一題
  setTimeout(() => {
    currentQuestion++;
    updateQuestion();
  }, 1500);
}

```
:::


---

## 學習2：網頁設定為響應式網頁

https://cfchen58.synology.me/115/week4/stage2/

**這個階段的目標：** 讓網站在電腦、平板、手機（直向與橫向）都能正常顯示，視窗大小改變時版面自動調整。
**這個階段會修改的檔案：** index.html、sketch.js

### 執行截圖

（把截圖拖曳到這裡，或貼上圖片連結）

![學習2截圖](請貼上截圖)

### 第一次問 AI

```tex!
（逐字貼上你第一次問 AI 的提示詞）
```

### 第二次問 AI

```tex!
（逐字貼上你第二次問 AI 的提示詞）
```

### 第三次問 AI

```tex!
（逐字貼上你第三次問 AI 的提示詞）
```

### 程式碼內容

:::info
:::spoiler 點開貼上學習2的程式碼
```javascript=
//// 測驗題目資料
const questions = [
  {
    question: "下列哪一個是 p5.js 的主要繪圖基底？",
    options: ["Canvas (畫布)", "DOM", "WebGL", "SVG"],
    answer: 0
  },
  {
    question: "在 p5.js 中，用來設定畫布大小的函式是什麼？",
    options: ["size()", "createCanvas()", "windowSize()", "canvasSize()"],
    answer: 1
  },
  {
    question: "p5.js 預設的 60fps 動畫迴圈函式名稱是？",
    options: ["setup()", "update()", "draw()", "loop()"],
    answer: 2
  },
  {
    question: "下列何者不是 p5.js 常用的互動事件函式？",
    options: ["mousePressed()", "keyPressed()", "mouseMoved()", "keyboardClicked()"],
    answer: 3
  }
];

let currentQ = 0;       // 目前題數索引
let score = 0;          // 總分
let selectedOption = null; // 使用者選的選項 (-1 為未選)
let showFeedback = false;  // 是否顯示單題對錯結果
let isFinished = false;    // 是否測驗結束

function setup() {
  // 建立符合全視窗大小的畫布，支援響應式
  createCanvas(windowWidth, windowHeight);
  textAlign(CENTER, CENTER);
  rectMode(CENTER);
}

function draw() {
  background(240, 243, 246);

  // 根據畫布大小動態計算排版尺寸（適應手機、平板、電腦）
  let baseW = min(width * 0.9, 700); // 容器最大寬度
  let startY = height * 0.25;         // 內容起始高度

  if (!isFinished) {
    // === 標題與進度 ===
    fill(40);
    textSize(Math.max(width * 0.025, 16));
    text(`第 \({currentQ + 1} 題 / 共\){questions.length} 題`, width / 2, startY - 40);

    // === 題目外框與文字 ===
    fill(255);
    stroke(220);
    strokeWeight(2);
    rect(width / 2, startY + 30, baseW, 90, 12);

    noStroke();
    fill(30);
    textSize(Math.max(width * 0.03, 15));
    // 簡單處理多行文字顯示範圍
    text(questions[currentQ].question, width / 2, startY + 30, baseW - 40, 70);

    // === 選項按鈕 ===
    let optY = startY + 110;
    let optH = Math.max(height * 0.07, 45);
    let optSpacing = optH + 12;

    for (let i = 0; i < 4; i++) {
      let currentBoxY = optY + i * optSpacing;
      
      // 滑鼠懸停特效
      let isHover = 
        mouseX > width / 2 - baseW / 2 && 
        mouseX < width / 2 + baseW / 2 && 
        mouseY > currentBoxY - optH / 2 && 
        mouseY < currentBoxY + optH / 2;

      // 按鈕顏色變化
      if (showFeedback) {
        if (i === questions[currentQ].answer) {
          fill(46, 204, 113); // 正確選項顯示綠色
        } else if (i === selectedOption) {
          fill(231, 76, 60);  // 答錯選到的選項顯示紅色
        } else {
          fill(255);
        }
      } else {
        if (isHover) {
          fill(225, 235, 255);
        } else {
          fill(255);
        }
      }

      stroke(isHover && !showFeedback ? 52, 152, 219 : 210);
      strokeWeight(isHover && !showFeedback ? 2 : 1);
      rect(width / 2, currentBoxY, baseW, optH, 8);

      // 選項文字
      noStroke();
      fill(showFeedback && (i === questions[currentQ].answer || i === selectedOption) ? 255 : 40);
      textSize(Math.max(width * 0.025, 14));
      text(`\({String.fromCharCode(65 + i)}.\){questions[currentQ].options[i]}`, width / 2, currentBoxY);
    }

    // === 答題回饋與「下一題」按鈕 ===
    if (showFeedback) {
      let btnY = optY + 4 * optSpacing + 20;
      let btnW = 160;
      let btnH = 45;

      fill(52, 152, 219);
      noStroke();
      rect(width / 2, btnY, btnW, btnH, 8);

      fill(255);
      textSize(16);
      text(currentQ < questions.length - 1 ? "下一題" : "看總分", width / 2, btnY);
    }

  } else {
    // === 結算畫面 ===
    fill(40);
    textSize(Math.max(width * 0.04, 24));
    text("測驗結束！", width / 2, height * 0.35);

    textSize(Math.max(width * 0.035, 20));
    fill(52, 152, 219);
    text(`你的總分：\({score} /\){questions.length * 25} 分`, width / 2, height * 0.45);

    // 重新測驗按鈕
    let rBtnW = 180;
    let rBtnH = 50;
    let rBtnY = height * 0.6;

    let isHoverReset = 
      mouseX > width / 2 - rBtnW / 2 && 
      mouseX < width / 2 + rBtnW / 2 && 
      mouseY > rBtnY - rBtnH / 2 && 
      mouseY < rBtnY + rBtnH / 2;

    fill(isHoverReset ? 39, 110, 170 : 52, 152, 219);
    noStroke();
    rect(width / 2, rBtnY, rBtnW, rBtnH, 8);

    fill(255);
    textSize(16);
    text("重新測驗", width / 2, rBtnY);
  }
}

function mousePressed() {
  let baseW = min(width * 0.9, 700);
  let startY = height * 0.25;

  if (!isFinished) {
    let optY = startY + 110;
    let optH = Math.max(height * 0.07, 45);
    let optSpacing = optH + 12;

    if (!showFeedback) {
      // 點擊選項
      for (let i = 0; i < 4; i++) {
        let currentBoxY = optY + i * optSpacing;
        if (
          mouseX > width / 2 - baseW / 2 && 
          mouseX < width / 2 + baseW / 2 && 
          mouseY > currentBoxY - optH / 2 && 
          mouseY < currentBoxY + optH / 2
        ) {
          selectedOption = i;
          showFeedback = true;
          if (selectedOption === questions[currentQ].answer) {
            score += 25; // 每題 25 分
          }
        }
      }
    } else {
      // 點擊「下一題」按鈕
      let btnY = optY + 4 * optSpacing + 20;
      let btnW = 160;
      let btnH = 45;

      if (
        mouseX > width / 2 - btnW / 2 && 
        mouseX < width / 2 + btnW / 2 && 
        mouseY > btnY - btnH / 2 && 
        mouseY < btnY + btnH / 2
      ) {
        if (currentQ < questions.length - 1) {
          currentQ++;
          showFeedback = false;
          selectedOption = null;
        } else {
          isFinished = true;
        }
      }
    }
  } else {
    // 點擊「重新測驗」按鈕
    let rBtnW = 180;
    let rBtnH = 50;
    let rBtnY = height * 0.6;

    if (
      mouseX > width / 2 - rBtnW / 2 && 
      mouseX < width / 2 + rBtnW / 2 && 
      mouseY > rBtnY - rBtnH / 2 && 
      mouseY < rBtnY + rBtnH / 2
    ) {
      currentQ = 0;
      score = 0;
      selectedOption = null;
      showFeedback = false;
      isFinished = false;
    }
  }
}

// 視窗大小改變時自動調整畫布尺寸
function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}

```
:::


---

## 學習3：設定嵌入 Google 字型，網頁文字採用這些字型

https://cfchen58.synology.me/115/week4/stage3/

**這個階段的目標：** 從 Google Fonts 嵌入繁體中文字型，並讓畫布上的題目與選項文字使用這些字型。
**這個階段會修改的檔案：** index.html、sketch.js

### 執行截圖

（把截圖拖曳到這裡，或貼上圖片連結）

![學習3截圖](請貼上截圖)

### 第一次問 AI

```tex!
（逐字貼上你第一次問 AI 的提示詞）
```

### 第二次問 AI

```tex!
（逐字貼上你第二次問 AI 的提示詞）
```

### 第三次問 AI

```tex!
（逐字貼上你第三次問 AI 的提示詞）
```

### 程式碼內容

:::info
:::spoiler 點開貼上學習3的程式碼
```javascript=
//學習3程式碼所在

```
:::


---

## 學習4：設定題庫並抽題顯示題目網頁（CSV 檔案）

https://cfchen58.synology.me/115/week4/stage4/

**這個階段的目標：** 把題目移到 questions.csv，網站讀取題庫後每次隨機抽出 5 題。
**這個階段會修改的檔案：** index.html、sketch.js、questions.csv

### 執行截圖

（把截圖拖曳到這裡，或貼上圖片連結）

![學習4截圖](請貼上截圖)

### 第一次問 AI

```tex!
（逐字貼上你第一次問 AI 的提示詞）
```

### 第二次問 AI

```tex!
（逐字貼上你第二次問 AI 的提示詞）
```

### 第三次問 AI

```tex!
（逐字貼上你第三次問 AI 的提示詞）
```

### 程式碼內容

:::info
:::spoiler 點開貼上學習4的程式碼
```javascript=
//學習4程式碼所在

```
:::


---

## 學習5：利用 Google Sheets 當題庫

https://cfchen58.synology.me/115/week4/stage5/

**這個階段的目標：** 把題庫放在 Google 試算表，網站直接讀取，老師改試算表，網站題目就跟著更新。
**這個階段會修改的檔案：** index.html、sketch.js（questions.csv 當備用題庫）

### 執行截圖

（把截圖拖曳到這裡，或貼上圖片連結）

![學習5截圖](請貼上截圖)

### 第一次問 AI

```tex!
（逐字貼上你第一次問 AI 的提示詞）
```

### 第二次問 AI

```tex!
（逐字貼上你第二次問 AI 的提示詞）
```

### 第三次問 AI

```tex!
（逐字貼上你第三次問 AI 的提示詞）
```

### 程式碼內容

:::info
:::spoiler 點開貼上學習5的程式碼
```javascript=
//學習5程式碼所在

```
:::


---

## 我的心得

這五個學習中，哪一個最困難？你是怎麼解決的？（請寫出實際發生的事）

＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿
