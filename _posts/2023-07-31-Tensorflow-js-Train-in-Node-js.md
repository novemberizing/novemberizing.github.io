---
layout: post
section: "Tensorflow.js"
title: "Tensorflow.js Training in Node.js"
description: ""
cover: /assets/images/posts/2023-07-31-Tensorflow-js-Train-in-Node-js.png
date: "2023/07/28 10:00:00"
categories: posts
tags: ["Tensorflow.js", "Tutorial", "Train Model", "Handwritten Digit CNN"]
---

### 1. 소개

`Tensorflow.js`를 사용하여 `Node.js` 웹 서버를 구축하여 서버 측에서 야구 투구 유형을 훈련하고 분류하는 방법을 기술합니다. 피치 센서 데이터에서 피치 유형을 예측하고 웹 클라이언트에서 예측을 호출하도록 모델을 교육하는 웹 어플리케이션을 빌드합니다.

- `Node.js`와 함께 사용할 `Tensorflow.js npm` 패키지를 설치하고 설정하는 방법
- `Node.js` 환경에서 학습 및 테스트 데이터에 액세스 하는 방법
- `Node.js` 서버에서 `Tensorflow.js`로 모델을 훈련시키는 방법
- 클라이언트/서버 애플리케이션에서 추론을 위해 훈련된 모델을 배포하는 방법

### 2. `Node.js` 앱 설정

`Node.js` 및 `npm`을 설치합니다.

> [https://github.com/tensorflow/tfjs/tree/master/tfjs-node#installing](https://github.com/tensorflow/tfjs/tree/master/tfjs-node)
>
> `Node`용 `Tensorflow.js`가 지원하는 플랫폼
>
> - Mac OS X CPU(10.12.6 Siera 이상)
> - Linux CPU(Ubuntu 14.04 이상)
> - Linux GPU(Ubuntu 14.04 이상 및 CUDNN v8 포함 Cuda 11.2) - [소프트웨어 요구사항](https://www.tensorflow.org/install/gpu?hl=ko#software_requirements)
> - Windows CPU(Win 7 이상)
> - Windows GPU(Win 7 이상 및 CUDNN v8 포함 Cuda 11.2) - [Windows 설정](https://www.tensorflow.org/install/gpu?hl=ko#windows_setup)
>
> GPU 지원을 위해 tfjs-node-gpu@1.2.4 이상을 사용하려면 시스템에 다음 NVIDIA® 소프트웨어가 설치되어 있어야 합니다.
>
> - NVIDIA® GPU drivers	(>450.x)
> - CUDA® Toolkit (11.2)
> - cuDNN SDK (8.1.0)
>
> 다른 리눅스에서도 작동할 수 있습니다.

##### Node.js CPU / Tensorflow.js 설치

```sh
npm install @tensorflow/tfjs-node
```

##### Linux/Windows GPU / Tensorflow.js 설치

```sh
npm install @tensorflow/tfjs-node-gpu
```

##### Windows & Mac OS X / Python 2.7 & Python 3

`Windows` / `Mac OS X`에는 `Python 2.7` 과 `Python 3`이 필요합니다.

`node-gyp`에 대한 `Windows` 및 `OSX` 빌드 지원에는 `Python 2.7`이 필요합니다. `@tensorflow/tfjs-node` 또는 `@tensorflow/tfjs-node-gpu`를 설치하기 전에 이 버전이 있는지 확인하십시오. `Python 3.x`가 있는 컴퓨터는 바인딩을 제대로 설치하지 않습니다.

`Windows`에 대한 자세한 문제 해결은 [TensorFlow.js Node.js bindings Windows troubleshooting](https://github.com/tensorflow/tfjs/blob/master/tfjs-node/WINDOWS_TROUBLESHOOTING.md)를 확인하십시오.

##### Windows / Visual Studio ("Desktop development with C++") 설치

Visual Studio 를 설치해야 합니다. 설치 시에 `Desktop development with C++`를 선택하여 설치해야 합니다.

[Visual Studio Download](https://visualstudio.microsoft.com/free-developer-offers/)

##### Max OS X / Xcode 설치

컴퓨터에 `Xcode` 설정이 없는 경우 다음 명령을 실행하십사오.

```sh
$ xcode-select --install
```

> `Mac OS Catalina`의 경우 [Installing node-gyp using the Xcode Command Line Tools via manual download](https://github.com/nodejs/node-gyp/blob/main/macOS_Catalina.md#installing-node-gyp-using-the-xcode-command-line-tools-via-manual-download)

해당 작업이 완료되면 `@tensorflow/tfjs-node` 패키지를 다시 설치해야 합니다.

```sh
npm install
```

`@tensorflow/tfjs-node` 또는 `@tensorflow/tfjs-node-gpu` 패키지는 이미 `@tensorflow/tfjs`와 함께 제공되므로 `package.json` 파일에 포함하기만 하면 됩니다.

##### `M1` 칩이 장착된 `Mac OS X`

`M1` 칩이 있는 `Mac`의 경우 `tfjs-node`는 `arm64` 빌드만 지원합니다. `tfjs-node`를 설치하려면 터미널 앱에서 `Rosetta`가 꺼져 있는지 확인해야 합니다. 터미널을 시작하고 다음 명령이 `arm64`를 응답으로 표시하는지 확인합니다.

```sh
uname -m
```

`arm64` 바이너리로 노드 버전을 설치하십시오. 다음 명령으로 `arm64`도 표시되는지 확인할 수 있습니다.

```sh
node -e 'console.log(os.arch())'
```

이제 앞에서 설명한 대로 `tfjs-node`를 설치할 수 있습니다.

#### 개발환경구축

`Node.js` 앱을 위한 `./baseball` 이라는 디렉터리를 만듭니다.

```sh
cd baseball
npm init

...

npm install @tensorflow/tfjs-node --save
npm install @tensorflow/tfjs-node-gpu --save
npm install argparse --save
npm install socket.io --save
npm install socket.io-client --save
npm install clang-format --save-dev
npm install mkdirp --save-dev
npm install webpack --save-dev
npm install webpack-cli --save-dev
npm install webpack-dev-server --save-dev
npm install --save-dev html-loader
```

package.json 파일에 아래의 설정을 추가합니다.

```json
...

  "resolutions": {
    "node-fetch": "2.6.7"
  }

...
```

webpack.config.js 파일을 작성하고 아래의 내용을 추가합니다.

```
/**
 * @license
 * Copyright 2019 Google LLC. All Rights Reserved.
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 * http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 * =============================================================================
 */

const path = require('path');

module.exports = {
    entry: './src/client.js',
    output: {path: path.resolve(__dirname, 'dist'), filename: 'bundle.js'},
    devServer: {historyApiFallback: true, host: '0.0.0.0'}
};
```

> 라이센스: 라이선스 적용 시 아파치 재단의 이름과 라이선스의 내용을 명시해야 하며, 아파치 라이선스 2.0이 적용된 소스 코드를 수정했을 경우 외부에 그 사실을 밝혀야 한다. 저도 구글에서 작성한 webpack.config.js 를 사용하니 라이센스를 내용을 명시하고 사실을 알려야겠죠.

package.json 파일의 실행 스크립트를 설정합니다.

```json
...

  "scripts": {
    "client": "webpack && webpack-dev-server",
    "server": "node ./src/server.js"
  },

...
```

src/client.js

``js
console.log("hello client");
```

src/server.js

``js
console.log("hello server");
```

를 작성하고 `npm run server` 와 `npm run client` 를 수행해 봅니다.

### 3. 훈련과 테스트 데이터 설정

아래 링크를 통해서 구글에서 제공하는 훈련 및 테스트 데이터를 다운받을 수 있습니다.

- [pitch_type_training_data.csv](https://storage.googleapis.com/mlb-pitch-data/pitch_type_training_data.csv)
- [pitch_type_test_data](https://storage.googleapis.com/mlb-pitch-data/pitch_type_test_data.csv)

```sh
wget https://storage.googleapis.com/mlb-pitch-data/pitch_type_training_data.csv -OutFile pitch_type_training_data.csv
wget https://storage.googleapis.com/mlb-pitch-data/pitch_type_test_data.csv -OutFile pitch_type_test_data.csv
```

> 윈도우즈의 파워쉘에서 수행하였습니다.

샘플 훈련 데이터가 아래처럼 보여질 것입니다.

```csv
vx0,vy0,vz0,ax,ay,az,start_speed,left_handed_pitcher,pitch_code
7.69914900671662,-132.225686405648,-6.58357157666866,-22.5082591074995,28.3119270826735,-16.5850095967027,91.1,0,0
6.68052308575228,-134.215511616881,-6.35565979491619,-19.6602769147989,26.7031848314466,-14.3430602022656,92.4,0,0
2.56546504690782,-135.398673977074,-2.91657310799559,-14.7849950586111,27.8083916890792,-21.5737737390901,93.1,0,0
```

피치 센서 데이터는 8가지 필드가 존재합니다.

- 볼 속도 (vx0, vy0, vz0)
- 공 가속도 (ax, ay, az)
- 피치의 시작 속도 (start_speed)
- 왼손잡이와 오른손잡이 (left_handed_pitcher)
- 피치 코드 (pitch_code)
    - 피치코드는 `Fastball (2-seam)`, `Fastball (4-seam)`, `Fastball (sinker)`, `Fastball (cutter)`, `Slider`, `Changeup`, `Curveball` 7가지 투구 유형 중 하나입니다.

목표는 피치 센서 데이터가 주어진 피치 유형을 예측할 수 있는 모델을 구축하는 것입니다.

데이터를 준비하고 훈련을 위해서 `tf.data.csv api`를 사용하여 데이터를 로드합니다. 또한 최소-최대 정규화 척더를 사용하여 데이터를 정규화(`normalization`)합니다. 아래의 코드는 데이터를 로드하고 정규화하는 코드입니다.

normalize.js / 최소-최대 정규화 함수

```js
export default function normalize(value, min, max) {
    if(min === undefined || max === undefined) {
        return value;
    }
    return (value - min) / (max - min);
}
```

prepare.js

```js
import tf from "@tensorflow/tfjs";
import normalize from "./normalize.js";

const TRAIN_DATA_PATH = 'https://storage.googleapis.com/mlb-pitch-data/pitch_type_training_data.csv';
const TEST_DATA_PATH = 'https://storage.googleapis.com/mlb-pitch-data/pitch_type_test_data.csv';

// Constants from training data:
const VX0_MIN = -18.885;
const VX0_MAX = 18.065;
const VY0_MIN = -152.463;
const VY0_MAX = -86.374;
const VZ0_MIN = -15.5146078412997;
const VZ0_MAX = 9.974;
const AX_MIN = -48.0287647107959;
const AX_MAX = 30.592;
const AY_MIN = 9.397;
const AY_MAX = 49.18;
const AZ_MIN = -49.339;
const AZ_MAX = 2.95522851438373;
const START_SPEED_MIN = 59;
const START_SPEED_MAX = 104.4;

const NUM_PITCH_CLASSES = 7;
const TRAINING_DATA_LENGTH = 7000;
const TEST_DATA_LENGTH = 700;

/**
 * Converts a row from the csv into features and labels.
 * Each feature field is normalized within training data constants:
 */

function csvTransform(o) {
    const values = [
        normalize(xs.vx0, VX0_MIN, VX0_MAX),
        normalize(xs.vy0, VY0_MIN, VY0_MAX),
        normalize(xs.vz0, VZ0_MIN, VZ0_MAX), normalize(xs.ax, AX_MIN, AX_MAX),
        normalize(xs.ay, AY_MIN, AY_MAX), normalize(xs.az, AZ_MIN, AZ_MAX),
        normalize(xs.start_speed, START_SPEED_MIN, START_SPEED_MAX),
        xs.left_handed_pitcher
  ];

  return {xs: values, ys: ys.pitch_code};
}

const trainingData = tf.data.csv(TRAIN_DATA_PATH, {columnConfigs: {pitch_code: {isLabel: true}}})
                            .map(csvTransform)
                            .shuffle(TRAINING_DATA_LENGTH)
                            .batch(100);

// Load all training data in one batch to use for eval:
const trainingValidationData = tf.data.csv(TRAIN_DATA_PATH, {columnConfigs: {pitch_code: {isLabel: true}}})
                                      .map(csvTransform)
                                      .batch(TRAINING_DATA_LENGTH);

// Load all test data in one batch to use for eval:
const testValidationData = tf.data.csv(TEST_DATA_PATH, {columnConfigs: {pitch_code: {isLabel: true}}})
                                  .map(csvTransform)
                                  .batch(TEST_DATA_LENGTH);
```

### 4. 피치 유형을 분류하는 모델 생성

모델을 만듭니다. `tf.layers API`를 사용하여 입력(`[8]` 피치 센서 값의 모양)을 `ReLU` 활성화 단위로 구성된 숨겨진 `hidden fully-connected` 레이어 3개에 연결한 다음 각각 출력 중 하나를 나타내는 7개의 구성된 하나의 `softmax` 출력 레이어에 연결합니다.

`adam` 옵티마이저와 `sparseCategoricalCrossentropy` 손실 함수로 모델을 훈련합니다.

> - [훈련모델](https://www.tensorflow.org/js/guide/train_models?hl=ko)

prepare.js 에 모델을 생성하고 컴파일 하는 코드와 필요한 함수들을 정의한 아래의 코드를 삽입합니다.


```js
const model = tf.sequential();
model.add(tf.layers.dense({units: 250, activation: 'relu', inputShape: [8]}));
model.add(tf.layers.dense({units: 175, activation: 'relu'}));
model.add(tf.layers.dense({units: 150, activation: 'relu'}));
model.add(tf.layers.dense({units: NUM_PITCH_CLASSES, activation: 'softmax'}));

model.compile({
  optimizer: tf.train.adam(),
  loss: 'sparseCategoricalCrossentropy',
  metrics: ['accuracy']
});
```

prepare.js 모듈을 완성하기 위해 유효성 검사 및 테스트 데이터 세트를 평가하고 단일 샘플의 피치 유형을 예측하고 정확도 메트릭을 계산하는 함수를 작성하여 prepare.js 파일 끝에 추가합니다.


```js
// Returns pitch class evaluation percentages for training data
// with an option to include test data
async function evaluate(useTestData) {
  let results = {};
  await trainingValidationData.forEachAsync(pitchTypeBatch => {
    const values = model.predict(pitchTypeBatch.xs).dataSync();
    const classSize = TRAINING_DATA_LENGTH / NUM_PITCH_CLASSES;
    for (let i = 0; i < NUM_PITCH_CLASSES; i++) {
      results[pitchFromClassNum(i)] = {
        training: calcPitchClassEval(i, classSize, values)
      };
    }
  });

  if (useTestData) {
    await testValidationData.forEachAsync(pitchTypeBatch => {
      const values = model.predict(pitchTypeBatch.xs).dataSync();
      const classSize = TEST_DATA_LENGTH / NUM_PITCH_CLASSES;
      for (let i = 0; i < NUM_PITCH_CLASSES; i++) {
        results[pitchFromClassNum(i)].validation =
            calcPitchClassEval(i, classSize, values);
      }
    });
  }
  return results;
}

async function predictSample(sample) {
  let result = model.predict(tf.tensor(sample, [1,sample.length])).arraySync();
  var maxValue = 0;
  var predictedPitch = 7;
  for (var i = 0; i < NUM_PITCH_CLASSES; i++) {
    if (result[0][i] > maxValue) {
      predictedPitch = i;
      maxValue = result[0][i];
    }
  }
  return pitchFromClassNum(predictedPitch);
}

// Determines accuracy evaluation for a given pitch class by index
function calcPitchClassEval(pitchIndex, classSize, values) {
  // Output has 7 different class values for each pitch, offset based on
  // which pitch class (ordered by i)
  let index = (pitchIndex * classSize * NUM_PITCH_CLASSES) + pitchIndex;
  let total = 0;
  for (let i = 0; i < classSize; i++) {
    total += values[index];
    index += NUM_PITCH_CLASSES;
  }
  return total / classSize;
}

// Returns the string value for Baseball pitch labels
function pitchFromClassNum(classNum) {
  switch (classNum) {
    case 0:
      return 'Fastball (2-seam)';
    case 1:
      return 'Fastball (4-seam)';
    case 2:
      return 'Fastball (sinker)';
    case 3:
      return 'Fastball (cutter)';
    case 4:
      return 'Slider';
    case 5:
      return 'Changeup';
    case 6:
      return 'Curveball';
    default:
      return 'Unknown';
  }
}

export {
  evaluate,
  model,
  pitchFromClassNum,
  predictSample,
  testValidationData,
  trainingData,
  TEST_DATA_LENGTH
}
```

### 5. 서버에서 모델 훈련

`server.js`에서 모델 교육 및 평가를 수행하는 서버 코드를 작성합니다. 먼저 HTTP 서버를 만들고 socket.io API를 사용하여 양방향 소켓 연결을 엽니다. 그런 다음 `model.fitDataset API`를 사용하여 모델 교육을 실행하고 앞에서 작성한 `prepare.evaluate()` 메서드를 사용하여 모델 정확도를 평가합니다. 10번의 반복에 대해 훈련 및 평가하고 메트릭을 콘솔에 출력합니다.

```js
import http from "http";
import * as socketio from "socket.io";
import * as prepare from "./prepare.js";

const TIMEOUT_BETWEEN_EPOCHS_MS = 500;
const PORT = 8001;

// util function to sleep for a given ms
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

// Main function to start server, perform model training, and emit stats via the socket connection
async function run() {
  const port = process.env.PORT || PORT;
  const server = http.createServer();
  const io = new socketio.Server(server);

  server.listen(port, () => {
    console.log(`  > Running socket on port: ${port}`);
  });

  io.on('connection', (socket) => {
    socket.on('predictSample', async (sample) => {
      io.emit('predictResult', await prepare.predictSample(sample));
    });
  });

  let numTrainingIterations = 10;
  for (var i = 0; i < numTrainingIterations; i++) {
    console.log(`Training iteration : ${i+1} / ${numTrainingIterations}`);
    await prepare.model.fitDataset(prepare.trainingData, {epochs: 1});
    console.log('accuracyPerClass', await prepare.evaluate(true));
    await sleep(TIMEOUT_BETWEEN_EPOCHS_MS);
  }

  io.emit('trainingComplete', true);
}

run();
```

`model.fitDataset API`를 사용하면 한 번의 호출로 여러 에포크를 훈련할 수도 있습니다.

### 6. 클라이언트 페이지 및 출력

서버가 준비되었으므로 클라이언트 코드를 작성하고 브라우저에서 실행할 수 있도록 합니다. 서버에서 모델 예측을 호출하고 그 결과를 표시하는 간단한 페이지를 만듭니다.

index.html

```html
<!doctype html>
<html>
  <head>
    <title>Pitch Training Accuracy</title>
  </head>
  <body>
    <h3 id="waiting-msg">Waiting for server...</h3>
    <p>
    <span style="font-size:16px" id="trainingStatus"></span>
    <p>
    <div id="predictContainer" style="font-size:16px;display:none">
      Sensor data: <span id="predictSample"></span>
      <button style="font-size:18px;padding:5px;margin-right:10px" id="predict-button">Predict Pitch</button><p>
      Predicted Pitch Type: <span style="font-weight:bold" id="predictResult"></span>
    </div>
    <script src="dist/bundle.js"></script>
    <style>
      html,
      body {
        font-family: Roboto, sans-serif;
        color: #5f6368;
      }
      body {
        background-color: rgb(248, 249, 250);
      }
    </style>
  </body>
</html>
```

client.js 코드

```js
import io from 'socket.io-client';
const predictContainer = document.getElementById('predictContainer');
const predictButton = document.getElementById('predict-button');

const socket =
    io('http://localhost:8001',
       {reconnectionDelay: 300, reconnectionDelayMax: 300});

const testSample = [2.668,-114.333,-1.908,4.786,25.707,-45.21,78,0]; // Curveball

predictButton.onclick = () => {
  predictButton.disabled = true;
  socket.emit('predictSample', testSample);
};

// functions to handle socket events
socket.on('connect', () => {
    document.getElementById('waiting-msg').style.display = 'none';
    document.getElementById('trainingStatus').innerHTML = 'Training in Progress';
});

socket.on('trainingComplete', () => {
  document.getElementById('trainingStatus').innerHTML = 'Training Complete';
  document.getElementById('predictSample').innerHTML = '[' + testSample.join(', ') + ']';
  predictContainer.style.display = 'block';
});

socket.on('predictResult', (result) => {
  plotPredictResult(result);
});

socket.on('disconnect', () => {
  document.getElementById('trainingStatus').innerHTML = '';
  predictContainer.style.display = 'none';
  document.getElementById('waiting-msg').style.display = 'block';
});

function plotPredictResult(result) {
  predictButton.disabled = false;
  document.getElementById('predictResult').innerHTML = result;
  console.log(result);
}
```

클라이언트는 `trainingComplete` 소켓 메시지를 처리하여 예측 버튼을 표시합니다. 이 버튼을 클릭하면 클라이언트는 샘플 센서 데이터와 함께 소켓 메시지를 보냅니다. `predictResult` 메시지를 받으면 페이지에 예측을 표시합니다.

클라이언트는 trainingComplete 소켓 메시지를 처리하여 예측 버튼을 표시합니다. 이 버튼을 클릭하면 클라이언트는 샘플 센서 데이터와 함께 소켓 메시지를 보냅니다. predictResult 메시지를 받으면 페이지에 예측을 표시합니다.

### 7. 앱 실행

```sh
npm run client

...

npm run server
```

각각 다른 콘솔에서 서버와 클라이언트를 

브라우저에서 클라이언트 페이지를 엽니다( http://localhost:8080). 모델 학습이 완료되면 Predict Sample 버튼을 클릭합니다. 브라우저에 예측 결과가 표시되어야 합니다. 테스트 CSV 파일의 몇 가지 예를 사용하여 샘플 센서 데이터를 자유롭게 수정하고 모델이 얼마나 정확하게 예측하는지 확인하십시오.

[소스](https://github.com/novemberizing/dev/tree/main/tensorflow/baseball)