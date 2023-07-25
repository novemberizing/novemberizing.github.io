---
layout: post
section: "Tensorflow.js"
title: "Tensorflow.js Start"
description: ""
cover: /assets/images/posts/2023-07-25-Tensorflow-js-Setting.png
date: "2023/07/24 11:27:00"
categories: posts
tags: ["Tensorflow.js", "Development", "Environment", "Start"]
---

### Tensorflow.js 시작하기

"Tensorflow.js"는 웹 브라우저와 "Node.js"에서 기계 학습 모델을 교육하고 배포하기 위한 "Javascript" 라이브러리입니다.

#### 전제 조건

개발 환경에 다음이 설치되어 있어야 합니다.

- [Node.js](https://nodejs.org/en/download)
- [Yarn](https://classic.yarnpkg.com/en/docs/install#windows-stable)

#### 간단한 예제 실행

"Tensorflow.js"를 사용하기 위해 `html`페이지 헤더에 `<script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@latest"></script>`를 정의하셔야 합니다.

```html
<html>
  <head>
    ...
    <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@latest"></script>
    ...
  </head>

  ...
```

"Tensorflow.js"를 사용하기 위한 스크립트 설정을 마쳤다면, 아래와 같은 코드를 `<script>...</script>` 안에 정의하시고 페이지를 로딩하시면 됩니다.

```js
/**
 * This tiny example illustrates how little code is necessary build,
 * train, predict from a model in Tensorflow.js.
 * Edit this code and refresh the html document to quickly explore the API.
 * 
 * Tiny Tensorflow.js train / predict example.
 */
async function run() {
    // Create a simple model.
    const model = tf.sequential();
    model.add(tf.layers.dense({ units: 1, inputShape: [1] }));
    // Prepare the model for training: Specifiy the loss and the optimizer.
    model.compile({ loss: 'meanSquaredError', optimizer: 'sgd' });
    // Generate some synthetic data for training. (y = 2x - 1)
    const x = tf.tensor2d([-1, 0, 1, 2, 3, 4], [6, 1]);
    const y = tf.tensor2d([-3, -1, 1, 3, 5, 7], [6, 1]);

    // Train the model using the data.
    await model.fit(x, y, { epochs: 250 });

    // Use the model to do interface on a data point the model hasn't seen.
    // Should print approximately 39.
    console.log(model.predict(tf.tensor2d([20], [1, 1])).dataSync());
}

run();
```

<script type="text/javascript">
async function run() {
    // Create a simple model.
    const model = tf.sequential();
    model.add(tf.layers.dense({ units: 1, inputShape: [1] }));
    // Prepare the model for training: Specifiy the loss and the optimizer.
    model.compile({ loss: 'meanSquaredError', optimizer: 'sgd' });
    // Generate some synthetic data for training. (y = 2x - 1)
    const x = tf.tensor2d([-1, 0, 1, 2, 3, 4], [6, 1]);
    const y = tf.tensor2d([-3, -1, 1, 3, 5, 7], [6, 1]);

    // Train the model using the data.
    await model.fit(x, y, { epochs: 250 });

    // Use the model to do interface on a data point the model hasn't seen.
    // Should print approximately 39.
    document.getElementById('tensorflow-js-install-example-001').innerText = model.predict(tf.tensor2d([20], [1, 1])).dataSync();
}
</script>

<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<div class="row">
    <div class="col-9" id="tensorflow-js-install-example-001"></div>
    <div class="col-3">
        <button type="button" class="btn btn-secondary" onClick="run()">실행</button>
    </div>
</div>

자바스크립트를 실행시키면 38.31612014770508 과 같이 39에 가까운 숫자가 표시되어야 합니다.

위의 소스 코드는 `run()`이 수행되면 방정식 `y = 2x - 1` 을 만족하는 `x` 및 `y` 값을 사용하여 `tf.sequential` 모델을 훈련합니다. 그런 다음 보이지 않는 `x` 값 20에 대한 `y` 값을 예측하고 그 결과를 표시합니다.

`2 * 20 - 1`의 결과는 39이므로 예측된 `y` 값은 대략 39여야 합니다.

#### 기타

<!-- TODO: 내용 채우기 -->

##### 텐서를 직접 다루지 않고 머신러닝 프로그램 코딩

옵티마이저나 텐서 조작을 관리하지 않고 기계 학습을 시작하려면 `ml5.js` 라이브러리를 확인하세요.

"Tensorflow.js" 위에 구축된 `ml5.js` 라이브러리는 간결하고 접근하기 쉬운 "API"를 통해 웹 브라우저에서 기계 학습 알고리즘 및 모델에 대한 액세스를 제공합니다.

[ml5.js](https://ml5js.org/)

##### Tensorflow.js 설치

웹 브라우저 또는 "Node.js"에서 구현하기 위해 "Tensorflow.js"를 설치하는 방법입니다.

[Tensorflow.js setting](/posts/2023/07/25/Tensorflow-js-Setting.html)

##### 선행학습된 모델을 Tensorflow.js 로 변환

선행 학습된 모델을 "Python"에서 "Tensorflow.js"로 변환할 수 있습니다.

<!-- TODO: Keras 모델을 TensorFlow.js로 가져오기 -->
<!-- TODO: TensorFlow GraphDef 기반 모델을 TensorFlow.js로 가져오기 -->

##### 기존 Tensorflow.js 코드 예제

<!-- tfjs-examples 리포지토리는 TensorFlow.js를 사용하여 다양한 ML 작업에 대한 작은 예제 구현을 제공합니다. -->
<!-- TODO: 리포지토리 생성 -->

##### Tensorflow.js 모델의 동작 시각화

`tfjs-vis`는 Tensorflow.js와 함께 사용하기 위한 웹브라우저의 시각화용 작은 라이브러리입니다.

[tfjs-vis](https://github.com/tensorflow/tfjs/tree/master/tfjs-vis)
|
[demo](https://storage.googleapis.com/tfjs-vis/mnist/dist/index.html)

##### Tensorflow.js 로 처리할 데이터 준비

> __Data__
>
> Tensorflow.js data provides simple API to load and parse data from disk or over the web in a variety of formats, and to prepare that data for use in machine learning models (e.g. via operations like filter, map, shuffle, and batch).
>
> [Data](https://js.tensorflow.org/api/latest/?hl=ko&_gl=1*rpyc7p*_ga*NTIwMTkwMjg4LjE2ODgwMTY5NDQ.*_ga_W0YLR4190T*MTY5MDI0ODgxOC43LjEuMTY5MDI0OTQ5MS4wLjAuMA..#Data)
