---
layout: post
section: "Tensorflow.js"
title: "Tensorflow.js Setting"
description: ""
cover: /assets/images/posts/2023-07-25-Tensorflow-js-Setting.png
date: "2023/07/25 10:42:00"
categories: posts
tags: ["Tensorflow.js", "Development", "Environment", "Install", "Setting"]
---

### 브라우저 설정

브라우저 기반 프로젝트에서 "Tensorflow.js"를 가져오는 두 가지 주요 방법이 있습니다.

1. 스크립트 태그 사용하기
2. "NPM"에서 설치하고 "Parcel", "Webpack" 또는 "Rollup"과 같은 빌드 도구를 사용하기

#### 스크립트 태그를 통한 사용법

기본 "HTML" 파일에 다음 스크립트 태그를 추가합니다.

```
<script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@2.0.0/dist/tf.min.js"></script>
```

##### 샘플 코드

```js
// Define a model for linear regression.
const model = tf.sequential();
model.add(tf.layers.dense({units : 1, inputShape : [1]}));
model.compile ({loss : 'meanSquaredError', optimizer : 'sgd'});

// Generate some synthetic data for training.
const x = tf.tensor2d([1, 2, 3, 4], [4, 1]);
const y = tf.tensor2d([1, 3, 5, 7], [4, 1]);

// Train the model using the data.
await model.fit(xs, ys, {epochs: 10});
// Use the model to do interence on a data point the model has't seen before:
model.predict(tf.tensor2d([5], [1, 1])).print();
// Open the browser devtools to see the output
```

<script type="text/javascript">
    async function TensorflowJsSettingRun001() {
        // Define a model for linear regression.
        const model = tf.sequential();
        model.add(tf.layers.dense({units : 1, inputShape : [1]}));
        model.compile ({loss : 'meanSquaredError', optimizer : 'sgd'});

        // Generate some synthetic data for training.
        const x = tf.tensor2d([1, 2, 3, 4], [4, 1]);
        const y = tf.tensor2d([1, 3, 5, 7], [4, 1]);

        // Train the model using the data.
        await model.fit(x, y, {epochs: 10});
        // Use the model to do interence on a data point the model has't seen before:
        model.predict(tf.tensor2d([5], [1, 1])).print();

        document.getElementById('tensorflow-js-setting-example-001').innerText = model.predict(tf.tensor2d([5], [1, 1])).dataSync();
        // Open the browser devtools to see the output
    }
</script>

<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<div class="row">
    <div class="col-9" id="tensorflow-js-setting-example-001"></div>
    <div class="col-3">
        <button type="button" class="btn btn-secondary" onClick="TensorflowJsSettingRun001()">실행</button>
    </div>
</div>

#### "NPM"에서 설치

`npm cli` 도구 또는 `yarn`을 사용하여 "Tensorflow.js"를 설치할 수 있습니다.

```sh
npm install @tensorflow/tfjs
```

##### 샘플 코드

```js
import * as tf from '@tensorflow/tfjs';
// Define a model for linear regression.
const model = tf.sequential();\
model.add(tf.layers.dense({units: 1, inputShape: [1]}));
model.compile ({loss : 'meanSquaredError', optimizer : 'sgd'});

// Generate some synthetic data for training.
const x = tf.tensor2d([1, 2, 3, 4], [4, 1]);
const y = tf.tensor2d([1, 3, 5, 7], [4, 1]);

// Train the model using the data.
await model.fit(x, y, {epochs: 10})

// Use the model to do inference on a data point the model hasn't seen before:
model.predict(tf.tensor2d([5], [1, 1])).print();
// Open the browser devtools to see the output
```

#### Node.js 설정

`npm cli` 도구 또는 `yarn`을 사용하여 "Tensorflow.js"를 설치할 수 있습니다.

##### 네이티브 C++ 바인딩으로 "Tensorflow.js"를 설치

```sh
npm install @tensorflow/tfjs-node
```

##### (리눅스 전용) 시스템에 CUDA를 지원하는 NVIDIA® GPU가 있는 경우 GPU 패키지를 사용하여 성능을 끌어올릴 수 있습니다.

```sh
npm install @tensorflow/tfjs-node-gpu
```

##### "PURE JAVASCRIPT" 버전을 설치합니다.

> 이 버전은 성능면에서 가장 느린 옵셥입니다.

```sh
npm install @tensorflow/tfjs
```


###### 샘플 코드

```js
const tf = require('@tensorflow/tfjs');
// Optional Load the binding:
// Use '@tensorflow/tfjs-node-gpu' if running with GPU. require('@tensorflow/tfjs-node');

// Train a simple model:
const model = tf.sequential();
model.add(tf.layers.dense({units: 100, activation: 'relu', inputShape: [10]}));
model.add(tf.layers.dense({units: 1, activation: 'linear'}));
model.compile({optimizer: 'sgd', loss: 'meanSquaredError'});

const x = tf.randomNormal ([100, 10]);
const y = tf.randomNormal ([100, 1]);

model.fit(x, y, {
    epochs : 100,
    callbacks : {
        onEpochEnd : (epoch, log) => console.log(`Epoch ${epoch}: loss = ${log.loss}`)
    }
});
```

> __Typescript__
> 
> "Typescript"를 사용할 때 프로젝트에서 엄격한 `null` 검사를 사용하거나 컴파일 중에 오류가 발생하는 경우 `tsconfig.json` 파일에서 `skipLibCheck: true`를 설정해야 할 수 있습니다.
