---
layout: post
section: "Tensorflow.js"
title: "Tensorflow.js Transfer Learning Image Classification"
description: ""
cover: /assets/images/posts/2023-08-01-Tensorflow-js-Transfer-Learning-Image-Classification.png
date: "2023/07/28 10:00:00"
categories: posts
tags: ["Tensorflow.js", "Tutorial", "Transfer Learning", "Image Classification"]
---

정교한 딥러닝 모델에는 수백만 개의 매겨변수(가중치)가 있으며 이를 처음부터 훈련하려면 종종 많은 양의 컴퓨팅 리소스 데이터가 필요합니다. 전이학습은 관련 작업에 대해 이미 훈련된 모델의 일부를 가져와 새 모델에서 재사용함으로써 손쉽게 데이터를 구축하는 기술입니다.

전이학습을 통해서 이미지 내에서 수천 가지의 다양한 객체를 인식하도록 이미 훈련된 모델을 활용하여 고유한 이미지 인식기를 구축할 수 있습니다. 사전에 훈련된 모델의 기존 지식을 조정함으로 필요한 원래 모델보다 훨씬 적은 훈련 데이터를 사용하여 자체 이미지 클래스를 감지할 수 있습니다.

전이학습은 새 모델을 신속하게 개발하고 브라우저 및 모바일 장치와 같이 리소스가 제한된 환경에서 모델을 정의하는데 유용합니다.

대부분 전이 학습을 할 때 원래 모델의 가중치를 조정하지 않습니다. 대신 최종 레이어를 제거하고 잘린 모델의 출력 위에 새로운 모델을 훈련합니다.

### 이미지 분류

- 전이학습을 사용하여 최소한의 훈련 데이터로 매우 정확한 모델을 생성
- MobileNet 이라는 이미지 분류를 위해 사전 훈련된 모델을 사용
- 인식하는 이미지 클래스를 정의하기 위해 해달 모델 위에 새 모델을 훈련

### 소개

`Tensorflow.js`를 사용하여 브라우저에서 바로 학습시킬 간단한 커스텀 이미지 분류기를 빌드

- 브라우저에서 이미지 분류에 널리 사용되는 선행 학습 모델 (MobileNet)을 로드하고 실행
- 전이학습

### `Tensorflow.js` 및 `MobileNet` 모델 로드

index.html

```html
<html>
  <head>
    <!-- Load the latest version of TensorFlow.js -->
    <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs"></script>
    <script src="https://cdn.jsdelivr.net/npm/@tensorflow-models/mobilenet"></script>
  </head>
  <body>
    <div id="console"></div>
    <!-- Add an image that we will use to test -->
    <img id="img" crossorigin src="https://i.imgur.com/JlUvsxa.jpg" width="227" height="227"/>
    <!-- Load index.js after the content of the page -->
    <script src="index.js"></script>
  </body>
</html>
```

<div id="console"></div>
<img id="img" crossorigin src="https://i.imgur.com/JlUvsxa.jpg" width="227" height="227"/>

4. 브라우저에서 추론을 위해서 `MobileNet` 설정

```js
let net;

async function app();
    console.log("Loading mobilenet...");
    // Load the model.
    net = await mobilenet.load();
    console.log("Successfully loaded model");
    // Make a prediction through the model on our image.
    const img = document.getElementById("img");
    const result await net.classify(img);
    console.log(result);
}

app();
```

<script>
let net;

async function app(){
    console.log("Loading mobilenet...");
    // Load the model.
    net = await mobilenet.load();
    console.log("Successfully loaded model");
    // Make a prediction through the model on our image.
    const img = document.getElementById("img");
    const result = await net.classify(img);
    console.log(result);
}
app();
</script>

### 브라우저에서 MobileNet 추론 테스트

개 사진이 표시되고 개발자 도구의 자바스크립트 콘솔에는 MobileNet의 상위 예측이 표시됩니다. 모델을 다운로드하는 데는 다소 시간이 걸릴 수 있습니다.

### 웹캠 이미지로 브라우저에서 MobileNet 추론 실행

실시간으로 보다 상호작용이 가능하도록 만들어 보겠습니다. 웹캠을 통해 들어오는 이미지에 대한 예측을 하도록 웹캠을 설정합니다.

먼저 웹캠 동영상 요소를 설정합니다.

html

```html
<video autoplay playsinline muted id="webcam" width="224" height="224"></video>
```

javascript `app()` 함수에 다음과 같은 코드를 추가합니다. 이제 이전에 추가한 app() 함수에서 웹캠 요소를 통해 예측을 수행하는 무한 루프를 만들 수 있습니다.

```js
const webcamElement = document.getElementById('webcam');

async function app() {
  console.log('Loading mobilenet..');

  // Load the model.
  net = await mobilenet.load();
  console.log('Successfully loaded model');

  // Create an object from Tensorflow.js data API which could capture image
  // from the web camera as Tensor.
  const webcam = await tf.data.webcam(webcamElement);
  while (true) {
    const img = await webcam.capture();
    const result = await net.classify(img);

    document.getElementById('console').innerText = `
      prediction: ${result[0].className}\n
      probability: ${result[0].probability}
    `;
    // Dispose the tensor to release the memory.
    img.dispose();

    // Give some breathing room by waiting for the next animation frame to
    // fire.
    await tf.nextFrame();
  }
}
```

웹페이지에서 콘솔을 열면 웹갬에서 수집된 모든 프레임에 대한 확률과 함께 `MobileNet` 예측이 표시됩니다. `ImageNet` 데이터 세트는 일반적으로 웹캠에 표시되는 이미지처럼 보이지 않을 수 있으므로 무의미한 결과로 보일 수 있습니다.

<script>
// const webcamElement = document.getElementById('webcam');

async function app1(webcamElement) {
    console.log('Loading mobilenet..');
    // Load the model.
    net = await mobilenet.load();
    console.log('Successfully loaded model');
    // Create an object from Tensorflow.js data API which could capture image
    // from the web camera as Tensor.
    const webcam = await tf.data.webcam(webcamElement);
    while (true) {
        const img = await webcam.capture();
        const result = await net.classify(img);
        document.getElementById('Tensorflow-js-Transfer-Learning-Image-Classification-Console-1').innerText = `
            prediction: ${result[0].className}\n
            probability: ${result[0].probability}
        `;
        // Dispose the tensor to release the memory.
        img.dispose();
        // Give some breathing room by waiting for the next animation frame to
        // fire.
        await tf.nextFrame();
    }
}
</script>

<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<div class="row mb-3">
    <div class="col-9 p-3">
        <div id="Tensorflow-js-Transfer-Learning-Image-Classification">
            <video autoplay playsinline muted id="webcam" width="224" height="224" resizeWidth="224" resizeHeight="224"></video>
            <div id="Tensorflow-js-Transfer-Learning-Image-Classification-Console-1"></div>
        </div>
    </div>
    <div class="col-3 ps-5">
        <button type="button" class="btn btn-secondary" onClick="app1(document.getElementById('webcam'))">실행</button>
    </div>
</div>

### MobileNet 예측 위에 커스텀 분류기 추가

웹캠에서 바로 커스텀 클래스 객체 분류기를 만들어 보려 합니다. MobileNet을 통해 분류는 하겠지만 이번에는 특정 웹캠 이미지에 대해 모델이ㅡ 내부 표현을 이용해 분류합니다.

`K-Nearest Neighbors Classifier`라는 모듈을 사용해 웹캠 이미지를 다양한 카테고리에 효과적으로 배치할 수 있으며, 사용자가 예측츨 요청하는 경우 예측을 수행하는 대상과 가장 유사한 활성화를 가진 클래스를 간단하게 선택합니다.

html 헤더에 다음을 추가하여야 합니다.

```html
<script src="https://cdn.jsdelivr.net/npm/@tensorflow-models/knn-classifier"></script>
```

동영상 요소 아래 버튼 3개를 추가합니다. 이 3개의 버튼은 모델에 학습 이미지를 추가하는데 사용합니다.

```html
<button id="class-a">Add A</button>
<button id="class-b">Add B</button>
<button id="class-c">Add C</button>
```

스크립트 상단에서 분류기(Classification)을 만들기 위한 코드를 작성합니다.

```js
const classifier = knnClassifier.create();
async function app(webcamElement) {
    console.log('Loading mobilenet..');
    // Load the model.
    net = await mobilenet.load();
    console.log('Successfully loaded model');
    // Create an object from Tensorflow.js data API which could capture image from the web camera as Tensor.
    const webcam = await tf.data.webcam(webcamElement);
    // Reads an image from the webcam and associates it with a specific class index.
    const addExample = async classId => {
        // Capture an image from the web camera.
        const img = await webcam.capture();
        // Get the intermediate activation of MobileNet 'conv_preds' and pass that to the KNN classifier.
        const activation = net.infer(img, true);
        // Pass the intermediate activation to the classifier.
        classifier.addExample(activation, classId);
        // Dispose the tensor to release the memory.
        img.dispose();
    };
    // When clicking a button, add an example for that class.
    document.getElementById('class-a').addEventListener('click', () => addExample(0));
    document.getElementById('class-b').addEventListener('click', () => addExample(1));
    document.getElementById('class-c').addEventListener('click', () => addExample(2));
    while (true) {
        if (classifier.getNumClasses() > 0) {
            const img = await webcam.capture();
            // Get the activation from mobilenet from the webcam.
            const activation = net.infer(img, 'conv_preds');
            // Get the most likely class and confidence from the classifier module.
            const result = await classifier.predictClass(activation);
            const classes = ['A', 'B', 'C'];
            document.getElementById('console').innerText = `
                prediction: ${classes[result.label]}\n
                probability: ${result.confidences[result.label]}
            `;
            // Dispose the tensor to release the memory.
            img.dispose();
        }
        await tf.nextFrame();
    }
}
```

<script>
const classifier = knnClassifier.create();
async function app2(webcamElement) {
    console.log('Loading mobilenet..');
    // Load the model.
    net = await mobilenet.load();
    console.log('Successfully loaded model');
    // Create an object from Tensorflow.js data API which could capture image from the web camera as Tensor.
    const webcam = await tf.data.webcam(webcamElement);
    // Reads an image from the webcam and associates it with a specific class index.
    const addExample = async classId => {
        console.log("classId");
        // Capture an image from the web camera.
        const img = await webcam.capture();
        // Get the intermediate activation of MobileNet 'conv_preds' and pass that to the KNN classifier.
        const activation = net.infer(img, true);
        // Pass the intermediate activation to the classifier.
        classifier.addExample(activation, classId);
        // Dispose the tensor to release the memory.
        img.dispose();
    };
    // When clicking a button, add an example for that class.
    document.getElementById('class-a').addEventListener('click', () => addExample(0));
    document.getElementById('class-b').addEventListener('click', () => addExample(1));
    document.getElementById('class-c').addEventListener('click', () => addExample(2));
    while (true) {
        if (classifier.getNumClasses() > 0) {
            const img = await webcam.capture();
            // Get the activation from mobilenet from the webcam.
            const activation = net.infer(img, 'conv_preds');
            // Get the most likely class and confidence from the classifier module.
            const result = await classifier.predictClass(activation);
            const classes = ['A', 'B', 'C'];
            document.getElementById('Tensorflow-js-Transfer-Learning-Image-Classification-Console-2').innerText = `
                prediction: ${classes[result.label]}\n
                probability: ${result.confidences[result.label]}
            `;
            // Dispose the tensor to release the memory.
            img.dispose();
        }
        await tf.nextFrame();
    }
}
</script>

일반 사물 또는 얼굴/신체 동작을 사용하여 세 가지 클래스 각각에 대해 이미지를 캡처할 수 있습니다. 추가 버튼 중 하나를 클릭할 때마다 이미지 하나가 학습 예시로 추가됩니다. 이 작업을 수행하는 동안 모델은 들어오는 웹캠 이미지를 계속해서 예측하고 그 결과를 실시간으로 표시합니다.

<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<div class="row mb-3">
    <div class="col-9 p-3">
        <div id="Tensorflow-js-Transfer-Learning-Image-Classification-2">
            <video autoplay playsinline muted id="webcam2" width="224" height="224"></video>
            <button id="class-a" class="btn btn-secondary">Add A</button>
            <button id="class-b" class="btn btn-secondary">Add B</button>
            <button id="class-c" class="btn btn-secondary">Add C</button>
            <div id="Tensorflow-js-Transfer-Learning-Image-Classification-Console-2"></div>
        </div>
    </div>
    <div class="col-3 ps-5">
        <button type="button" class="btn btn-secondary" onClick="app2(document.getElementById('webcam2'))">실행</button>
    </div>
</div>
