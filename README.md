# タイピングソフト

## ひな形作成
`index.html`
```html
<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ピロぷりタイプ</title>
</head>

<body>
    <script src="script.js"></script>
</body>

</html>
```

`index.html`
```html
<body>
    <div class="timer" id="timer">0</div>
    <div class="container">
        <div class="type-display" id="typeDisplay">type</div>
        <textarea class="typeInput" id="typeInput">type</textarea>
    </div>
    <script src="script.js"></script>
</body>
```

## CSSをあてていきます。

`style.css`
```css
*{
    box-sizing: border-box;
}

body {
    height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
    background-color: blueviolet;
}

.container {
    background-color: aqua;
    font-size: 3rem;
    font-weight: 400;
    padding: 4rem;
    border-radius: 1rem;
    width: 1000px;
    min-height: 600px;
    max-width: 90%;
    box-shadow: 1px 5px 5px #727272;
}

.container .type-Input {
    margin-top: 4rem;
    font-size: 3rem;
}

.timer {
    position: absolute;
    top: 2rem;
    font-size: 5rem;
    font-weight: bolder;
    color: red;
}

.type-display {
    margin-bottom: 1rem;
    margin-left: 1rem;
}

.type-Input {
    background: transparent;
    border: 2px solid #ff0000;
    outline: none;
    width: 100%;
    height: 14rem;
    margin: auto;
    resize: none;
    padding: 1rem 1rem;
    border-radius: 1rem;
    font-size: 1rem;
}

.type-Input:focus {
    border-color: black;
}
```

## 画面が読み取られたときにオートフォーカスが当たるようにしたい。
`index.html`
```html
<textarea class="type-Input" id="typeInput" autofocus>type</textarea>
```

`script.js`
```js
const RANDOM_SENTENCE_URL_API = "https://api.quotable.io/random"

// 非同期
function GetRandomSentence() {
    return fetch(RANDOM_SENTENCE_URL_API)
    .then((responce) => responce.json())
    .then((data) => console.log(data.content));
}

GetRandomSentence();
```

## `async`と`await`を使って非同期処理をする。
`script.js`
```js
// ランダムな文章を取得する。(非同期)
async function RenderNextSentence() {
    const sentence = await GetRandomSentence();
    console.log(sentence);
}

RenderNextSentence();
```

## `console.log`を外す
`script.js`
```js
function GetRandomSentence() {
    return fetch(RANDOM_SENTENCE_URL_API)
    .then((responce) => responce.json())
    .then((data) => data.content); // console.logを外す。
}
```

## htmlのtypeDisplayにAPIの値を反映させる
`script.js`
```js
async function RenderNextSentence() {
    const sentence = await GetRandomSentence();
    console.log(sentence);

    typeDisplay.innerText = sentence;
}

RenderNextSentence();
```

## これで実行環境にてランダムな文章が表示されていることを確認する。

## 取得した文章を一文字ずつ分解して、さらにspanタグに一文字ずつ格納するようにする。
`script.js`
```js
async function RenderNextSentence() {
    const sentence = await GetRandomSentence();
    // console.log(sentence);

    typeDisplay.innerText = sentence;

    let oneText = sentence.split("");
    
    oneText.forEach((character) => {
        const characterSpan = document.createElement("span");
        characterSpan.innerText = character;
        console.log(characterSpan)
    });
}
```

## typeDisplayの中にspanタグで区切られたsentenceを入れたいので以下のようにします。
## さらに一文字ずつ色が変わるようにカスタマイズしたい。
`script.js`
```js
async function RenderNextSentence() {
    const sentence = await GetRandomSentence();
    // console.log(sentence);

    typeDisplay.innerText = ""; // 空にする

    let oneText = sentence.split("");
    
    oneText.forEach((character) => {
        const characterSpan = document.createElement("span");
        characterSpan.innerText = character;
        console.log(characterSpan)
        typeDisplay.appendChild(characterSpan);
        characterSpan.classList.add("correct");
    });
}
```

`style.css`
```css
.correct {
    color: green;
}
```

## テキストボックスの中身を消す操作
`script.js`
```js
const typeInput = document.getElementById("typwInput");

// 省略

async function RenderNextSentence() {
    const sentence = await GetRandomSentence();
    // console.log(sentence);

    typeDisplay.innerText = "";

    let oneText = sentence.split("");
    
    oneText.forEach((character) => {
        const characterSpan = document.createElement("span");
        characterSpan.innerText = character;
        console.log(characterSpan)
        typeDisplay.appendChild(characterSpan);
        characterSpan.classList.add("correct");
    });

    // テキストボックスの中身を消す
    typeInput.innerText = "";
}
```

## 入力した文字を認識させるための記述
`script.js`
```js
const typeInput = document.getElementById("typeInput");

// inputテキスト入力があっているか間違っているかの処理
typeInput.addEventListener("input", () => {
    console.log("a");
});
```

`script.js`
```js
// inputテキスト入力があっているか間違っているかの処理
typeInput.addEventListener("input", () => {
    const sentenceArray = typeDisplay.querySelectorAll("span");
    // console.log(sentenceArray)
    const arrayValue = typeInput.value.split("");
    // console.log(arrayValue);
    sentenceArray.forEach((characterSpan,index) => {
        if(arrayValue[index] == null) {
            characterSpan.classList.remove("correct");
            characterSpan.classList.remove("incorrect");
        } else if(characterSpan.innerText == arrayValue[index]) {
            console.log("correct");
            characterSpan.classList.add("correct");
            characterSpan.classList.remove("incorrect");
        } else {
            characterSpan.classList.add("incorrect");
            characterSpan.classList.remove("correct");
        }
    });
});
```

# JavaScript全体のコード
`script.js`
```js
const RANDOM_SENTENCE_URL_API = "https://api.quotable.io/random";
const typeDisplay = document.getElementById("typeDisplay");
const typeInput = document.getElementById("typeInput");
const timer = document.getElementById("timer");

const typeSound = new Audio("./audio/typing-sound.mp3");
const wrongSound = new Audio("./audio/wrong.mp3");
const correctSound = new Audio("./audio/correct.mp3");

// inputテキスト入力があっているか間違っているかの処理
typeInput.addEventListener("input", () => {
    // タイプ音をつける
    typeSound.play();
    typeSound.currentTime = 0;
    const sentenceArray = typeDisplay.querySelectorAll("span");
    // console.log(sentenceArray)
    const arrayValue = typeInput.value.split("");
    // console.log(arrayValue);
    let correct = true;
    sentenceArray.forEach((characterSpan,index) => {
        if(arrayValue[index] == null) {
            characterSpan.classList.remove("correct");
            characterSpan.classList.remove("incorrect");
            correct = false;
        } else if(characterSpan.innerText == arrayValue[index]) {
            console.log("correct");
            characterSpan.classList.add("correct");
            characterSpan.classList.remove("incorrect");
        } else {
            characterSpan.classList.add("incorrect");
            characterSpan.classList.remove("correct");

            wrongSound.play();
            wrongSound.currentTime = 0;

            correct = false;
        }
    });
    if(correct == true){
        correctSound.play();
        correctSound.currentTime = 0;
        RenderNextSentence();
    }
});

// 非同期
function GetRandomSentence() {
    return fetch(RANDOM_SENTENCE_URL_API)
    .then((response) => response.json())
    .then((data) => data.content);
}

// ランダムな文章を取得する。
async function RenderNextSentence() {
    const sentence = await GetRandomSentence();
    // console.log(sentence);

    typeDisplay.innerText = "";

    let oneText = sentence.split("");
    
    oneText.forEach((character) => {
        const characterSpan = document.createElement("span");
        characterSpan.innerText = character;
        // console.log(characterSpan)
        typeDisplay.appendChild(characterSpan);
        // characterSpan.classList.add("correct");
    });

    // テキストボックスの中身を消す
    typeInput.value = "";

    StartTimer();
}

let startTime;
let originTime = 30;
function StartTimer() {
    timer.innerText = originTime;
    startTime = new Date();
    // console.log(startTime)
    setInterval(() => {
        timer.innerText = originTime - getTimerTime();
        if(timer.innerText <= 0) TimeUp();
    }, 1000);
}

function getTimerTime() {
    return Math.floor((new Date() - startTime) / 1000);
}

function TimeUp() {
    RenderNextSentence();
}

RenderNextSentence();
```