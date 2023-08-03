---
layout: post
section: "Tensorflow.js"
title: "Tensorflow.js Transfer Learning Audio Recognizer"
description: ""
cover: /assets/images/posts/2023-08-03-Tensorflow-js-Transfer-Learning-Audio-Recognizer.png
date: "2023/08/03 09:51:00"
categories: posts
tags: ["Tensorflow.js", "Tutorial", "Transfer Learning", "Audio Recognizer"]
---

### 전이학습 / 오디오 인식기

전이학습을 사용하여 상대적으로 적은 양의 훈련 데이터로 짧은 소리를 분류하는 모델을 만듭니다. 음성 명령 인식을 위해 사전에 훈련된 모델을 사용하게 됩니다. 사용자 정의 사운드 클래스를 인식하도록 해당 모델 위에 새 모델을 훈련합니다.

[Speech Command Recognizer](https://github.com/tensorflow/tfjs-models/tree/master/speech-commands)

### 1. 소개

오디오 인식 네트워크를 빌드하고 소리를 만들어 브라우저의 슬라이더를 제어하는 것을 Tensorflow.js 로 만들어 보려 합니다.

먼저 20개의 음성 명령을 인식할 수 있는 사전 훈련된 모델을 로드하고 실행합니다. 그런 다음 마이크를 사용하여 소리를 인식하고 슬라이더를 왼쪽 또는 오른쪽으로 이동시키는 간단한 신명망을 구축하고 훈련합니다.

### 2. Tensorflow.js 와 오디오 모델을 로드

아래와 같은 HTML 파일을 만듭니다.

```
<html>
  <head>
    <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs"></script>
    <script src="https://cdn.jsdelivr.net/npm/@tensorflow-models/speech-commands"></script>
  </head>
  <body>
    <div id="console"></div>
    <script src="index.js"></script>
  </body>
</html>
```

첫번째 스크립트는 Tensorflow.js 라이브러리이고, 두번째 파일은 미리 훈련된 음성 명령 모델입니다. 

### 3. 실시간 예측

아래의 코드를 작성하시면 미리 훈련된 모델을 실행해 볼 수 있습니다.

```js
let recognizer;

function predictWord(console) {
    // Array of words that the recognizer is trained to recognize.
    const words = recognizer.wordLabels();
    recognizer.listen(({scores}) => {
        // Turn scores into a list of (score, word) pairs.
        scores.sort((s1, s2) => s2.score - s1.score);
        console.textContent = scores[0].word;
    }, { probabilityThreshold: 0.75 });
}

async function app() {
    recognizer = speechCommands.create('BROWSER_FFT');
    await recognizer.ensureModelLoaded();
    predictWord(document.querySelector('#console'))
}

app();
```

<script src="https://cdn.jsdelivr.net/npm/@tensorflow-models/speech-commands"></script>

<script>
let recognizer;

function predictWord(con) {
    // Array of words that the recognizer is trained to recognize.
    const words = recognizer.wordLabels();
    console.log(words);
    recognizer.listen(({scores}) => {
        // Turn scores into a list of (score, word) pairs.
        scores.sort((s1, s2) => s2.score - s1.score);
        console.log(scores);
        con.textContent = scores[0].word;
    }, { probabilityThreshold: 0.75 });
}

async function app1() {
    recognizer = speechCommands.create('BROWSER_FFT');
    await recognizer.ensureModelLoaded();
    predictWord(document.getElementById('Tensorflow-js-Transfer-Learning-Audio-Recognizer-1'))
}

app1();
</script>

<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<div class="row mb-3">
    <div class="col-9 p-3">
        <div id="Tensorflow-js-Transfer-Learning-Audio-Recognizer">
            <div id="Tensorflow-js-Transfer-Learning-Audio-Recognizer-1"></div>
        </div>
    </div>
    <div class="col-3 ps-5">
        <button type="button" class="btn btn-secondary" onClick="app1()">실행</button>
    </div>
</div>

### 4. 예측 테스트

모델을 다운로드하는 데 약간의 시간이 걸릴 수 있습니다. 모델이 로드되는 즉시 페이지 상단에 단어가 표시됩니다. 이 모델은 0에서 9까지의 숫자와 "left", "right", "yes", "no"등과 같은 몇 가지 추가 명령을 인식하도록 훈련되었습니다.

그 단어 중 하나를 말하면 인식을 하게 됩니다. 모델이 실행되는 빈도를 제어하는 `probabilityThreshold`를 사용할 수 있습니다. 0.75는 모델이 주어진 단어를 들었다고 75% 이상 확신할 때 실행된다는 것을 의미합니다.

[Speech Command Recognizer](https://github.com/tensorflow/tfjs-models/blob/master/speech-commands/README.md)

### 5. 데이터 수집

재미있게 만들기 위해 전체 단어 대신 짧은 소리를 사용하여 음성을 인식할 수 있습니다. "왼쪽", "오른쪽" 및 "노이즈"의 3가지 명령을 인식하도록 모델을 훈련하려 합니다. "노이지"를 인식하는 것은 음성 감지에서 매우 중요합니다.

먼저 데이터를 수집해야 합니다. `<div id="console">` 앞의 `<body>` 태그 안에 다음을 추가하여 앱에 간단한 UI를 추가합니다.

```html
<button id="left" onmousedown="collect(0)" onmouseup="collect(null)">Left</button>
<button id="right" onmousedown="collect(1)" onmouseup="collect(null)">Right</button>
<button id="noise" onmousedown="collect(2)" onmouseup="collect(null)">Noise</button>
```

그리고 데이터 수집을 위한 자바스크립트 함수를 정의합니다.

```js
// One frame is ~23ms of audio.
const NUM_FRAMES = 3;
let examples = [];

function collect(label) {
    if(recognizer.isListening()) {
        return recognizer.stopListening();
    }
    if(label === null) {
        return;
    }
    recognizer.listen(async ({spectrogram: { frameSize, data}}) => {
        let vals = normalize(data.subarray(-frameSize * NUM_FRAMES));
        examples.push({vals, label});
        document.querySelector("#console").textContent = `${examples.length} examples collected`;
    }, {
        overlapFactor: 0.999,
        includeSpectrogram: true,
        invokeCallbackOnNoiseAndUnknown: true
    });
}

function normalize(x) {
    const mean = -100;
    const std = 10;
    return x.map(x => (x - mean) / std);
}
```

`app()` 함수에서 `predictWord()` 코드를 제거해야 합니다.

```js
async function app() {
    recognizer = speechCommands.create('BROWSER_FFT');
    await recognizer.ensureModelLoaded();
    // predictWord() no longer called.
}
```

모델에서 인식할 세 가지 명령에 해당하는 "왼쪽", "오른쪽" 및 "노이지"라는 레이블이 붙은 세 개의 버튼을 UI 에 추가했으며, 이 버튼을 누르면 모델에 대한 학습 예제를 생성하는 새로 추가된 `collect()` 함수가 호출됩니다.

`collect()`는 레이블을 `recognitionr.listen()`의 출력과 연결합니다. `includeSpectrogram`이 참이므로, `recognitionr.listen()`은 오디오의 1초에 대한 원시 스펙트로그램(주파수 데이터)을 43개 프레임으로 나누어 제공하고 각 프레임은 오디오의 `~23ms` 입니다.

```js
recognizer.listen(async ({spectrogram: {frameSize, data}}) => {
...
}, {includeSpectrogram: true});
```

단어 대신 짧은 소리를 사용하고 싶기 때문에 마지막 3개 프레임(~70ms)만 고려합니다.

```js
let vals = normalize(data.subarray(-frameSize * NUM_FRAMES));
```

그리고 수치 문제를 피하기 위해 데이터를 평균 0과 표준 편차 1로 정규화합니다. 이 경우 스펙트로그램 값은 일반적으로 -100 정도의 큰 음수이고 편차는 10입니다.

```js
const mean = -100;
const std = 10;
return x.map(x => (x - mean) / std);
```

| Field | Description |
| ----- | ----------- |
| label | "왼쪽", "오른쪽" 및 "노이즈"에 대해 각각 0, 1 및 2. |
| vals  | 주파수 정보를 담고 있는 696개의 숫자(스펙트로그램) |

모든 데이터를 examples 변수에 저장합니다.


<script>
// One frame is ~23ms of audio.
const NUM_FRAMES = 3;
let examples = [];

function collect(label) {
    if(recognizer.isListening()) {
        return recognizer.stopListening();
    }
    if(label === null) {
        return;
    }
    recognizer.listen(async ({spectrogram: { frameSize, data}}) => {
        let vals = normalize(data.subarray(-frameSize * NUM_FRAMES));
        examples.push({vals, label});
        document.getElementById('Tensorflow-js-Transfer-Learning-Audio-Recognizer-Console-2').textContent = `${examples.length} examples collected`;
    }, {
        overlapFactor: 0.999,
        includeSpectrogram: true,
        invokeCallbackOnNoiseAndUnknown: true
    });
}

function normalize(x) {
    const mean = -100;
    const std = 10;
    return x.map(x => (x - mean) / std);
}
</script>

<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<div class="row mb-3">
    <div class="col-9 p-3">
        <div id="Tensorflow-js-Transfer-Learning-Audio-Recognizer-2">
            <div id="Tensorflow-js-Transfer-Learning-Audio-Recognizer-Console-2"></div>
        </div>
    </div>
    <div class="col-3 ps-5">
        <button id="left" type="button" class="btn btn-secondary pe-2 mb-2" onmousedown="collect(0)" onmouseup="collect(null)">Left</button>
        <button id="right" type="button" class="btn btn-secondary pe-2 mb-2" onmousedown="collect(1)" onmouseup="collect(null)">Right</button>
        <button id="noise" type="button" class="btn btn-secondary pe-2 mb-2" onmousedown="collect(2)" onmouseup="collect(null)">Noise</button>
    </div>
</div>

### 모델 훈련

버튼을 클릭하여 모델을 훈련할 수 있는 UI 를 만듭니다.

```html
<button id="train" onclick="train()">Train</button>
```

실제 자바스크립트로 모델을 훈련하는 코드를 작성합니다.


```js
const INPUT_SHAPE = [NUM_FRAMES, 232, 1];
let model;

async function train() {
    toggleButtons(false);
    const ys = tf.oneHot(examples.map(e => e.label), 3);
    const xsShape = [examples.length, ...INPUT_SHAPE];
    const xs = tf.tensor(flatten(examples.map(e => e.vals)), xsShape);

    await model.fit(xs, ys, {
        batchSize: 16,
        epochs: 10,
        callbacks: {
            onEpochEnd: (epoch, logs) => {
                document.querySelector('#console').textContent = `Accuracy: ${(logs.acc * 100).toFixed(1)}% Epoch: ${epoch + 1}`;
            }
        }
    });
    tf.dispose([xs, ys]);
    toggleButtons(true);
}

function buildModel() {
    model = tf.sequential();
    model.add(tf.layers.depthwiseConv2d({
        depthMultiplier: 8,
        kernelSize: [NUM_FRAMES, 3],
        activation: 'relu',
        inputShape: INPUT_SHAPE
    }));
    model.add(tf.layers.maxPooling2d({poolSize: [1, 2], strides: [2, 2]}));
    model.add(tf.layers.flatten());
    model.add(tf.layers.dense({units: 3, activation: 'softmax'}));
    const optimizer = tf.train.adam(0.01);
    model.compile({
        optimizer,
        loss: 'categoricalCrossentropy',
        metrics: ['accuracy']
    });
}

function toggleButtons(enable) {
    document.querySelectorAll('button').forEach(b => b.disabled = !enable);
}

function flatten(tensors) {
    const size = tensors[0].length;
    const result = new Float32Array(tensors.length * size);
    tensors.forEach((arr, i) => result.set(arr, i * size));
    return result;
}
```

앱이 로드되면 `buildModel()`을 호출합니다.

```js
async function app() {
    recognizer = speechCommands.create('BROWSER_FFT');
    await recognizer.ensureModelLoaded();
    // Add this line.
    buildModel();
}
```

데이터를 다시 수집하고 "훈련"을 클릭하면 훈련을 테스트하거나 예측과 함께 훈련을 테스트하기 위해 10단계까지 기다릴 수 있습니다.

`buildModel()`은 모델 아키텍처를 정의하고 `train()`은 수집된 데이터를 사용하여 모델을 교육합니다.

##### 모델 아키텍처

이 모델에는 오디오 데이터(스펙트로그램으로 표시됨)를 처리하는 컨볼루션 계층, Max Pooling 계층, 평탄화 계층 및 3 가지 작업에 매핑되는 dense 계층의 4개 계층이 있습니다.

```js
model = tf.sequential();
model.add(tf.layers.depthwiseConv2d({
   depthMultiplier: 8,
   kernelSize: [NUM_FRAMES, 3],
   activation: 'relu',
   inputShape: INPUT_SHAPE
}));
model.add(tf.layers.maxPooling2d({poolSize: [1, 2], strides: [2, 2]}));
model.add(tf.layers.flatten());
model.add(tf.layers.dense({units: 3, activation: 'softmax'}));
```

모델의 입력 형태는 `[NUM_FRAMES, 232, 1]`이며 각 프레임은 서로 다른 주파수에 해당하는 232개의 숫자를 포함하는 23ms 오디오입니다. (232는 사람의 음성을 캡처하는 데 필요한 주파수 버킷의 양). 

> 전체 단어를 말하는 대신 소리를 내기 때문에 3 프레임 길이(~70ms 샘플)의 샘플을 사용

모델을 컴파일하여 Learning 준비를 합니다.

```js
const optimizer = tf.train.adam(0.01);
model.compile({
    optimizer,
    loss: 'categoricalCrossentropy',
    metrics: ['accuracy']
});
```

딥러닝에서 흔히 사용되는 `Adam` 최적화 프로그램과 분류에 사용되는 표준 손실 함수인 `categoricalCrossEntropy`를 사용합니다. 즉, 예측 확률(클래스당 하나의 확률)이 실제 클래스의 확률이 100%이고 다른 모든 클래스의 확률이 0%인지 측정합니다. 또한 모니터링할 메트릭으로 정확도를 제공하여 각 학습 에포크 후에 모델이 올바른 예제의 백분율을 제공합니다.

##### 훈련

훈련은 배치 크기 16(한 번에 16개의 처리)을 사용하여 데이터에 대해 10회 진행되며 UI에서 현재 정확도를 보여줍니다.

```js
await model.fit(xs, ys, {
   batchSize: 16,
   epochs: 10,
   callbacks: {
     onEpochEnd: (epoch, logs) => {
       document.querySelector('#console').textContent =
           `Accuracy: ${(logs.acc * 100).toFixed(1)}% Epoch: ${epoch + 1}`;
     }
   }
 });
```

<script>
const INPUT_SHAPE = [NUM_FRAMES, 232, 1];
let model;

async function train() {
 toggleButtons(false);
 const ys = tf.oneHot(examples.map(e => e.label), 3);
 const xsShape = [examples.length, ...INPUT_SHAPE];
 const xs = tf.tensor(flatten(examples.map(e => e.vals)), xsShape);

 await model.fit(xs, ys, {
   batchSize: 16,
   epochs: 10,
   callbacks: {
     onEpochEnd: (epoch, logs) => {
       document.getElementById('Tensorflow-js-Transfer-Learning-Audio-Recognizer-Console-3').textContent =
           `Accuracy: ${(logs.acc * 100).toFixed(1)}% Epoch: ${epoch + 1}`;
     }
   }
 });
 tf.dispose([xs, ys]);
 toggleButtons(true);
}

function buildModel() {
 model = tf.sequential();
 model.add(tf.layers.depthwiseConv2d({
   depthMultiplier: 8,
   kernelSize: [NUM_FRAMES, 3],
   activation: 'relu',
   inputShape: INPUT_SHAPE
 }));
 model.add(tf.layers.maxPooling2d({poolSize: [1, 2], strides: [2, 2]}));
 model.add(tf.layers.flatten());
 model.add(tf.layers.dense({units: 3, activation: 'softmax'}));
 const optimizer = tf.train.adam(0.01);
 model.compile({
   optimizer,
   loss: 'categoricalCrossentropy',
   metrics: ['accuracy']
 });
}

function toggleButtons(enable) {
 document.querySelectorAll('button').forEach(b => b.disabled = !enable);
}

function flatten(tensors = []) {
    const size = tensors[0].length;
    const result = new Float32Array(tensors.length * size);
    tensors.forEach((arr, i) => result.set(arr, i * size));
    return result;
}

async function app2() {
    recognizer = speechCommands.create('BROWSER_FFT');
    await recognizer.ensureModelLoaded();
    // Add this line.
    buildModel();
}
app2();
</script>

<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<!-- 동작하지 않음 -->
<!-- <div class="row mb-3">
    <div class="col-9 p-3">
        <div id="Tensorflow-js-Transfer-Learning-Audio-Recognizer-3">
            <div id="Tensorflow-js-Transfer-Learning-Audio-Recognizer-Console-3"></div>
        </div>
    </div>
    <div class="col-3 ps-5">
        <button id="train" type="button" class="btn btn-secondary" onclick="train()">Train</button>
    </div>
</div> -->

### 음성 인식을 통한 제어

슬라이드를 표시하는 UI를 업데이트합니다.

```html
<button id="listen" onclick="listen()">Listen</button>
<input type="range" id="output" min="0" max="10" step="0.1">
```

아래의 코드는 슬라이더를 제어하는 함수와 소리를 듣고 훈련된 모델에 의해서 라벨링된 데이터로 매칭하여 슬라이드를 움직이는 코드입니다.

```js
async function moveSlider(labelTensor) {
 const label = (await labelTensor.data())[0];
 document.getElementById('console').textContent = label;
 if (label == 2) {
   return;
 }
 let delta = 0.1;
 const prevValue = +document.getElementById('output').value;
 document.getElementById('output').value =
     prevValue + (label === 0 ? -delta : delta);
}

function listen() {
 if (recognizer.isListening()) {
   recognizer.stopListening();
   toggleButtons(true);
   document.getElementById('listen').textContent = 'Listen';
   return;
 }
 toggleButtons(false);
 document.getElementById('listen').textContent = 'Stop';
 document.getElementById('listen').disabled = false;

 recognizer.listen(async ({spectrogram: {frameSize, data}}) => {
   const vals = normalize(data.subarray(-frameSize * NUM_FRAMES));
   const input = tf.tensor(vals, [1, ...INPUT_SHAPE]);
   const probs = model.predict(input);
   const predLabel = probs.argMax(1);
   await moveSlider(predLabel);
   tf.dispose([input, probs, predLabel]);
 }, {
   overlapFactor: 0.999,
   includeSpectrogram: true,
   invokeCallbackOnNoiseAndUnknown: true
 });
}
```

##### 실시간 예측

`listen()`은 마이크를 듣고 실시간으로 예측합니다. 이 코드는 원시 스펙트로그램을 정규화하고 마지막 `NUM_FRAMES` 프레임을 제외한 모든 프레임을 삭제하는 `collect()` 메서드와 유사합니다. 유일한 차이점은 예측을 얻기 위해 훈련된 모델도 호출한다는 것입니다.

```js
const probs = model.predict(input);
const predLabel = probs.argMax(1);
await moveSlider(predLabel);
```

`model.predict(input)`의 출력은 클래스 수에 대한 확률 분포를 나타내는 모양 `[1, numClasses]`의 Tensor입니다. 더 간단히 말해서, 이것은 합계가 1인 각 가능한 출력 클래스에 대한 신뢰도의 집합입니다. Tensor의 외부 차원은 1입니다. 이는 배치의 크기이기 때문입니다.

확률 분포를 가장 가능성이 높은 클래스를 나타내는 단일 정수로 변환하기 위해 확률이 가장 높은 클래스 인덱스를 반환하는 `probs.argMax(1)`를 호출합니다. 마지막 차원인 `numClasses`에 대해 `argMax`를 계산하려고 하므로 축 매개변수로 "1"을 전달합니다.

##### 슬라이더 업데이트

`moveSlider()`는 레이블이 `0("Left")`이면 슬라이더 값을 감소시키고 레이블이 `1("Right")`이면 증가시키고 레이블이 `2("Noise")`이면 무시합니다.

##### 텐서 폐기

GPU 메모리를 정리하려면 출력 Tensor에서 `tf.dispose()`를 수동으로 호출하는 것이 중요합니다. 수동 `tf.dispose()`의 대안은 `tf.tidy()`에서 함수 호출을 래핑하는 것이지만 비동기 함수와 함께 사용할 수 없습니다.

```js
tf.dispose([input, probs, predLabel]);
```
