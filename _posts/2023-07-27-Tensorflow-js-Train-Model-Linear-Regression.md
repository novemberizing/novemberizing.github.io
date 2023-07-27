---
layout: post
section: "Tensorflow.js"
title: "Tensorflow.js Train model Linear Regression"
description: ""
cover: /assets/images/posts/2023-07-27-Tensorflow-js-Train-model-Linear-Regression.png
date: "2023/07/25 10:42:00"
categories: posts
tags: ["Tensorflow.js", "Tutorial", "Train Model", "Linear Regression"]
---

### 2D 데이터에서 예측값 내기

이 예제는 자동차 세트를 설명하는 숫자 데이터에서 예측값을 내도록 모델들 훈련합니다.

머신러닝 모델을 학습시키는 단계는 다음과 같습니다.

1. 작업을 체계적으로 정리:

    - 회귀 문제인가, 분류 문제인가?
    - 지도 학습을 통해 수행할 수 있는가, 아니면 비지도 학습을 통해 수행할 수 있는가?
    - 입력 데이터의 모양은 어떤가? 출력 데이터는 어떻게 표시되어야 하는가?

2. 데이터 준비:

    - 가능하면 데이터를 정리하고 수동으로 패턴을 검사합니다.
    - 학습에 사용하기 전에 데이터를 셔플링합니다.
    - 신경망에 합당한 범위로 데이터를 정규화합니다. 일반적으로 수치 데이터에는 0~1 또는 -1~1이 적합합니다.
    - 데이터를 텐서로 변환합니다.

3. 모델 빌드 및 실행:

tf.sequential 또는 tf.model을 사용하여 모델을 정의한 다음 `tf.layers.*`를 사용하여 모델에 레이어를 추가합니다. 옵티마이저 (일반적으로 adam이 적합함), 매개변수 (배치 크기, Epochs 수)를 선택합니다. 문제에 적합한 손실 함수를 선택하고 진행 상황을 평가하는 데 도움이 되는 정확도 측정항목을 선택합니다. meanSquaredError는 회귀 문제의 일반적인 손실 함수입니다. 손실이 감소하는지 학습을 모니터링합니다.

4. 모델 평가

학습 중에 모니터링할 수 있는 모델의 평가 측정항목을 선택합니다. 학습이 완료되면 예측 품질을 파악할 수 있도록 테스트 예측을 시도합니다.

#### 1. 소개

이 예제에서는 자동차 세트를 설명하는 수치 데이터로 예측을 수행하도록 모델을 학습시킵니다.

> 이 연습에서는 다양한 종류의 모델을 학습시키는 일반적인 단계를 보여주지만 작은 데이터 세트와 간단한 모델을 사용합니다.

연속적인 숫자를 예측하도록 모델을 학습시키기 때문에 이 작업을 회귀작업이라고도 합니다. 많은 입력 예시와 올바른 출력을 인식시켜 모델을 학습시키는데 이러한 학습을 지도학습이라고 합니다.

##### 빌드대상

`Tensorflow.js`를 사용해 브라우저에서 모델을 학습시키는 웹페이지를 만듭니다. 자동차의 '마력'이 주어지면 모델은 '갤런당 마일'(MPG)을 예측하는 방법을 학습합니다.

##### 진행

- 데이터를 로드하고 학습에 사용할 수 있도록 준비
- 모델의 아키텍처를 정의
- 모델을 학습시키고 학습하는 동안 성능을 모니터링
- 몇 가지 예측을 수행하여 학습된 모델을 평가

> - [But what is a neural network? | Chapter 1, Deep learning](https://www.youtube.com/watch?v=aircAruvnKk)
> - [Deep Learning in JS - Ashi Krishnan - JSConf EU 2018](https://www.youtube.com/watch?v=SV-cgdobtTA)

#### 2. 설정

다음과 같은 HTML 페이지를 작성하고 자바스크립트 코드를 포함시킵니다.

index.html

```html
<!DOCTYPE html>
<html>
    <head>
        <title>TensorFlow.js Tutorial</title>
        <!-- Import TensorFlow.js -->
        <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@2.0.0/dist/tf.min.js"></script>
        <!-- Import tfjs-vis -->
        <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs-vis@1.0.2/dist/tfjs-vis.umd.min.js"></script>
        <!-- Import the main script -->
        <script type="text/javascript">
        </script>
    </head>
    <body>
    </body>
</html>
```
#### 3. 입력 데이터 로드, 형식 지정, 시각화

첫번째 단계에서는 모델 학습에 사용할 데이터를 로드하고, 형식을 지정하고, 시각화를 수행합니다.

구글에서 호스팅한 JSON 파일에서 'cars' 데이터 세트를 로드합니다. 이 데이터 세트에는 주어진 각 자동차에 대한 다양한 특징이 많이 포함되어 있습니다. 이 예제에서는 마력과 갤런당 마일에 대한 데이터만 추출하려고 합니다.

스크립트 추가

```js
/**
 * Get the care data reduced to just the variable we are interested and cleaned of missing data.
 */
async function getData() {
    const carsDataResponse = await fetch('https://storage.googleapis.com/tfjs-tutorials/carsData.json');
    const carsData = await carsDataResponse.json();
    const cleaned = carsData.map(car => ({
        mpg: car.Miles_per_Gallon,
        horsepower: car.Horsepower
    })).filter(car => (car.mpg != null && car.horsepower != null));

    return cleaned;
}
```

`getData()` 함수에서는 `https://storage.googleapis.com/tfjs-tutorials/carsData.json`에서 데이터를 로드하고 갤런당 마일이나 마력이 정의되지 않은 항목을 삭제하여 리턴해줍니다.

이 데이터를 산점도에 그려서 데이터가 어떻게 표시되는지 확인할 수 있습니다.

```js
async function run() {
    // Load and plot the original input data that we are going to train on.
    const data = await getData();
    const values = data.map(d => ({
        x: d.horsepower,
        y: d.mpg
    }));

    tfvis.render.scatterplot(
        { name: 'Horsepower v MPG' },
        { values },
        {
            xLabel: 'Horsepower',
            yLabel: 'MPG',
            height: 300
        }
    );
}
```
<script type="text/javascript">
async function getData() {
    const carsDataResponse = await fetch('https://storage.googleapis.com/tfjs-tutorials/carsData.json');
    const carsData = await carsDataResponse.json();
    const cleaned = carsData.map(car => ({
        mpg: car.Miles_per_Gallon,
        horsepower: car.Horsepower
    })).filter(car => (car.mpg != null && car.horsepower != null));

    return cleaned;
}

async function run001() {
    // Load and plot the original input data that we are going to train on.
    const data = await getData();
    const values = data.map(d => ({
        x: d.horsepower,
        y: d.mpg
    }));

    await tfvis.render.scatterplot(
        document.getElementById("Tensorflow-js-Training-Model-Linear-Regression"),
        { values },
        {
            xLabel: 'Horsepower',
            yLabel: 'MPG',
            height: 300
        }
    );
}
</script>
<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<div class="row mb-3">
    <div class="col-9 p-3">
        <div id="Tensorflow-js-Training-Model-Linear-Regression"></div>
    </div>
    <div class="col-3 ps-5">
        <button type="button" class="btn btn-secondary" onClick="run001()">실행</button>
    </div>
</div>

실행을 누르면 오른쪽에 데이터의 산점도가 표시됩니다. 이 패널을 바이저라고 하며 `tfjs-vis`에서 제공합니다. 이 패널에 시각화를 편리하게 표시할 수 있습니다.

일반적으로 데이터로 작업할 때는 데이터를 시각적으로 표시할 방법을 찾고 필요한 경우 데이터를 정리하는 것이 좋습니다. 이 경우에는 필수 필드 중에 일부가 포함되지 않는 `carsData`의 특정 항목을 삭제해야 했습니다. 데이터를 시각화하면 모델이 학습할 수 있는 데이터에 어떤 구조가 있는지 파악할 수 있습니다.

위 산점도에서는 마력과 MPG 사이에 음의 상관관계가 있음을 알 수 있습니다. 즉, 마력이 증가하면 일반적으로 자동차의 갤런당 마일이 감소합니다.

> 주의: 데이터 구조(패턴)가 없다면, (즉, 데이터가 무작위인 경우) 모델이 실제로 학습할 수 있는 내용이 없습니다.

##### 작업 개념화

입력 데이터의 구조

```
...
{
  "mpg":15,
  "horsepower":165,
},
{
  "mpg":18,
  "horsepower":150,
},
{
  "mpg":16,
  "horsepower":150,
},
...
```

목표는 숫자 1개, 마력을 가져와 숫자 1개, 갤론당 마일을 예측하도록 모델을 학습시키는 것입니다. 일대일 매핑에 대해 작업하는 것입니다.

> 이 예시와 마력, MPG를 신경망에 제공하면 신경망은 이러한 예시로부터 마력에 따른 MPG를 예측하는 공식(또는 함수)을 학습합니다. 올바른 답변을 가지고 있는 예시로부터 학습하는 방법을 지도학습이라고 합니다.

#### 4. 모델 아키텍처 정의

모델 아키텍처를 설명하는 코드를 작성합니다. 모델 아키텍처란 '모델이 실행될 때 모델이 실행하는 함수' 또는 '응답을 계산하기 위해 모델에서 사용하는 알고리즘'으로 표현할 수 있습니다.

ML 모델은 입력을 받고 출력을 생성하는 알고리즘입니다. 신경망을 사용할 때 이 알고리즘은 출력에 적용되는 '가중치'(숫자)가 있는 뉴런 레이어의 집합이며 학습 프로세스는 이러한 가중치에 이상적인 값을 학습합니다.

```js
function createModel() {
    // Create a sequential model
    const model = tf.sequential();

    // Add a single input layer
    model.add(tf.layers.dense({inputShape: [1], units: 1, useBias: true}));
    
    // Add an output layer
    model.add(tf.layers.dense({units: 1, useBias: true}));

    return model;
}
```

이 모델은 Tensorflow.js에서 정의할 수 있는 가장 간단한 모델 중 하나입니다.

##### 코드 분석

- 모델 인스턴스화

```js
const model = tf.sequential();
```

`tf.Model` 객체를 인스턴스화합니다. 이 모델은 입력이 출력으로 곧바로 흘러가므로 `sequential` 모델입니다. 다른 종류의 모델에는 분기 또는 여러 입력 및 출력이 있을 수 있지만 대부분의 경우 모델은 순차적입니다. 순차 모델에는 더욱 사용하기 쉬운 API 도 있습니다.

##### 레이어 추가

```js
model.add(tf.layers.dense({inputShape: [1], units: 1, useBias: true}));
```

이렇게 하면 네트워크에 입력 레이어가 추가되며 이 레이어는 숨겨진 단위 하나가 있는 dense 레이어에 자동으로 연결됩니다. dense 레이어는 행렬(가중치)에 입력을 곱한 후 그 결과에 숫자(편향)를 더하는 레이어의 유형입니다. 네트워크의 첫 번째 레이어이믈로 inputShape 를 정의해야 합니다. inputShape 는 입력(특정 자동차의 마력)으로 숫자 1이 있으므로 `[1]`입니다.

units는 레이어에서 가중치 행렬의 크기를 설정합니다. 1로 설정하면 데이터의 입력 특징별로 가중치가 1이 됩니다.

> 참고: 밀집 레이어에는 기본적으로 편향 항이 있는 useBias 를 true 로 설정할 필요가 없으으로 앞으로는 tf.layers.dense에 대한 호출에서 생략합니다.

units는 레이어에서 가중치 행렬의 크기를 설정합니다. 1로 설정하면 데이터의 입력 특징별로 가중치가 1이 됩니다.

참고: dense 레이어에는 기본적으로 편향 항이 있어 useBias를 true로 설정할 필요가 없으므로 앞으로는 tf.layers.dense에 대한 호출에서 생략합니다.

```js
model.add(tf.layers.dense({units: 1}));
```

위의 코드는 출력 레이어를 생성합니다. 숫자 1을 출력하려고 하므로 units를 1로 설정합니다.

> 참고: 이 예시에서는 히든 레이어에 1개의 단위가 있으므로 위의 최종 출력 레이러를 추가할 필요가 없습니다. (즉, 히든 레이어를 출력 레이어로 사용할 수 있습니다). 그러나 별도의 출력 레이어를 정의하면 히든 레이어의 단위 수를 수정하는 동시에 입력 및 출력의 일대일 매핑을 그대로 유지할 수 있습니다.

##### 인스턴스 만들기

앞에서 정의한 run 함수에 아래의 코드를 추가합니다.

```js
// Create the model
const model = createModel();
tfvis.show.modelSummary({ name: "Model Summary" }, model);
```

모델의 인스턴스가 생성되고 웹페이지에 레이어 요약이 표시됩니다.

<script type="text/javascript">
function createModel() {
    // Create a sequential model
    const model = tf.sequential();

    // Add a single input layer
    model.add(tf.layers.dense({inputShape: [1], units: 1, useBias: true}));
    
    // Add an output layer
    model.add(tf.layers.dense({units: 1, useBias: true}));

    return model;
}

async function run002() {
    // Load and plot the original input data that we are going to train on.
    const data = await getData();
    const values = data.map(d => ({
        x: d.horsepower,
        y: d.mpg
    }));

    const model = createModel();
    tfvis.show.modelSummary(document.getElementById('Tensorflow-js-Training-Model-Linear-Regression-Define-Model'), model);
}
</script>
<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<div class="row mb-3">
    <div class="col-9 p-3">
        <div id="Tensorflow-js-Training-Model-Linear-Regression-Define-Model"></div>
    </div>
    <div class="col-3 ps-5">
        <button type="button" class="btn btn-secondary" onClick="run002()">실행</button>
    </div>
</div>

#### 5. 학습용 데이터 준비

머신러닝 모델 학습을 실용적으로 활용하는 Tensorflow.js의 성능 이점을 얻기 위해서는 데이터를 텐서로 변환해야 합니다. 데이터에 대해 셔플 및 정규화와 같은 권장되는 다양한 변환을 수행합니다.

```js
/**
 * Convert the input data to tensors that we can use for machine learning.
 * We will also do the important best practices of _shuffling_ the data and _normalizing_ the data MPG on the y-axis.
 */
function convertToTensor(data) {
    // Wrapping these calculations in a tidy will dispose any intermediate tensors.
    return tf.tidy(() => {
        // Step 1. Shuffle the data
        tf.util.shuffle(data);

        // Step 2. Convert data to Tensor
        const inputs = data.map(d => d.horsepower);
        const labels = data.map(d => d.mpg);
        const inputTensor = tf.tensor2d(inputs, [inputs.length, 1]);
        const labelTensor = tf.tensor2d(labels, [labels.length, 1]);

        // Step 3. Normalize the data to the range 0 - 1 using min-max scaling
        const inputMax = inputTensor.max();
        const inputMin = inputTensor.min();
        const labelMax = inputTensor.max();
        const labelMin = inputTensor.min();

        const normalizedInputs = inputTensor.sub(inputMin).div(inputMax.sub(inputMin));
        const normalizedLabels = labelTensor.sub(labelMin).div(labelMax.sub(labelMin));
        return {
            inputs: normalizedInputs,
            labels: normalizedLabels,
            // Return the min/max bounds so we can use them later.
            inputMax,
            inputMin,
            labelMax,
            labelMin,
        }
    });
}
```

##### 데이터 셔플링

```js
// Step 1. Shuffle the data
tf.util.shuffle(data);
```

여기서는 학습 알고리즘에 제공할 예시의 순서를 무작위로 지정합니다. 일반적으로 학습하는 동안 데이터 세트는 모델이 학습할 크기가 작은 하위 집합(배치)으로 분할되기 때문에 셔플이 중요합니다. 셔플은 각 배치에 전체 데이터 분포의 데이터가 다양하게 포함되도록 하는데 도움이 됩니다. 데이터가 다양하게 포함되도록 하면 모델에 다음과 같은 이점이 있습니다.

- 제공된 데이터의 순서에만 의존하여 학습하지 않도록 합니다.
- 하위 그룹의 구조에 민감해지지 않도록 합니다. 예를 들어 학습의 전반부에만 높은 마력의 자동차가 있다면 나머지 데이터 세트에는 적용되지 않는 관계를 학습할 수 있습니다.

> 권장사항: Tensorflw.js 의 학습 알고리즘에 대해 데이터를 처리하기 전에 항상 데이터를 셔플링해야 합니다.

##### 텐서로 변환

```js
// Step 2. Convert data to Tensor
const inputs = data.map(d => d.horsepower)
const labels = data.map(d => d.mpg);

const inputTensor = tf.tensor2d(inputs, [inputs.length, 1]);
const labelTensor = tf.tensor2d(labels, [labels.length, 1]);
```

여기서는 두 개의 배열을 만듭니다. 하나는 입력 예시(마력)용이고 다른 하나는 실제 출력 값(머신러닝에서 라벨이라고 함)을 위한 배열입니다.

그런 다음 각 배열 데이터를 2D 텐서로 변환합니다. 텐서의 모양은 `[num_examples, num_features_per_example]`입니다. 여기에는 `inputs.length` 예시가 있으며 각 예시에는 1 입력 특징(마력)이 있습니다.

##### 데이터 정규화

```js
//Step 3. Normalize the data to the range 0 - 1 using min-max scaling
const inputMax = inputTensor.max();
const inputMin = inputTensor.min();
const labelMax = labelTensor.max();
const labelMin = labelTensor.min();

const normalizedInputs = inputTensor.sub(inputMin).div(inputMax.sub(inputMin));
const normalizedLabels = labelTensor.sub(labelMin).div(labelMax.sub(labelMin));
```

다음으로 머신러닝 학습을 위한 또 다른 권장사항을 수행합니다. 여기서는 최소-최대 조정을 사용하여 데이터를 숫자 범위 0-1로 정규화합니다. Tensorflow.js로 빌드할 많은 머신러닝 모델의 내부는 너무 크지 않은 숫자에 대해 작동하도록 설꼐되기 때문에 정규화가 중요합니다. 데이터를 정규화하는 일반적인 범위는 0 ~ 1 또는 -1 ~ 1 입니다. 데이터를 합당한 범위로 정규화하는 습관을 들이면 모델을 보다 성공적으로 학습시키게 됩니다.

> 권장사항: 학습 전에 항상 데이터 정규화를 고려해야 합니다. 정규화 없이 학습시킬 수 있는 데이터 세트도 있지만 데이터를 정규화하면 효과적인 학습을 방해하는 문제가 완전히 사라지는 경우가 종종 있습니다.

데이터를 텐서로 변환하기 전에 정규화할 수 있습니다. 여기서는 Tensorflow.js 의 벡터화를 활용해 루프를 위한 명시적 구문을 작성할 필요 없이 최소-최대 조정 작업을 수행할 수 있으므로 변환하기 전에 정규화합니다.

##### 데이터 및 정규화 경계 반환

```js
return {
  inputs: normalizedInputs,
  labels: normalizedLabels,
  // Return the min/max bounds so we can use them later.
  inputMax,
  inputMin,
  labelMax,
  labelMin,
};
```

출력을 정규화하지 않은 원래의 상태로 다시 되돌려서 원래의 조정으로 가져오고 향후 입력 데이터를 동일한 방식으로 정규화할 수 있도록 학습 중에 정규화에 사용한 값을 유지하려고 합니다.

<script type="text/javascript">
    /**
 * Convert the input data to tensors that we can use for machine learning.
 * We will also do the important best practices of _shuffling_ the data and _normalizing_ the data MPG on the y-axis.
 */
function convertToTensor(data) {
    // Wrapping these calculations in a tidy will dispose any intermediate tensors.
    return tf.tidy(() => {
        // Step 1. Shuffle the data
        tf.util.shuffle(data);

        // Step 2. Convert data to Tensor
        const inputs = data.map(d => d.horsepower);
        const labels = data.map(d => d.mpg);
        const inputTensor = tf.tensor2d(inputs, [inputs.length, 1]);
        const labelTensor = tf.tensor2d(labels, [labels.length, 1]);

        // Step 3. Normalize the data to the range 0 - 1 using min-max scaling
        const inputMax = inputTensor.max();
        const inputMin = inputTensor.min();
        const labelMax = inputTensor.max();
        const labelMin = inputTensor.min();

        const normalizedInputs = inputTensor.sub(inputMin).div(inputMax.sub(inputMin));
        const normalizedLabels = labelTensor.sub(labelMin).div(labelMax.sub(labelMin));
        return {
            inputs: normalizedInputs,
            labels: normalizedLabels,
            // Return the min/max bounds so we can use them later.
            inputMax,
            inputMin,
            labelMax,
            labelMin,
        }
    });
}
</script>

#### 6. 모델 학습

모델 학습

모델 인스턴스를 생성하고 데이터를 텐서로 표현했으니 학습 프로세스를 시작할 모든 준비를 마친 것입니다.

```js
async function trainModel(model, inputs, labels) {
    // Prepare the model for training.
    model.compile({
        optimizer: tf.train.adam(),
        loss: tf.losses.meanSquaredError,
        metrics: ['mse']
    });
    const batchSize = 32;
    const epochs = 50;

    return await model.fit(inputs, labels, {
        batchSize,
        epochs,
        shuffle: true,
        callbacks: tfvis.show.fitCallbacks(
            { name: 'Training Performance' },
            ['loss', 'mse'],
            { height: 200, callbacks: ['onEpochEnd'] }
        )
    });
}
```

##### 학습 준비

```js
// Prepare the model for training.
model.compile({
  optimizer: tf.train.adam(),
  loss: tf.losses.meanSquaredError,
  metrics: ['mse'],
});
```

모델을 학습하기 전에 먼저 컴파일을 해야 합니다. 그러려면 다음과 같이 매우 중요한 항목을 지정행야 합니다.

| Param     | Description |
| --------- | ----------- |
| optimizer | 모델이 예시를 보면서 업데이트하는 데 적용할 알고리즘입니다. Tensorflow.js에는 다양한 옵티마이저가 있지만 여기에서는 실제로 매우 효과적이며 구성이 필요 없은 adam 옵티마이저를 선택했습니다. |
| loss      | 표시되는 각 배치 (데이터 하위 집합)를 얼마나 잘 학습하고 있는지 모델에 알려줄 함수입니다. 여기서는 meanSquaredError를 사용해 모델이 수행한 예측을 실제 값과 비교합니다. |

```js
const batchSize = 32;
const epochs = 50;
```

다음으로 batchSize와 epochs의 수를 선택합니다.

| Param     | Description |
| --------- | ----------- |
| batchSize | 각 학습 반복에서 모델이 보게될 데이터 하위 집합의 크기를 나타냅니다. 일반적인 배치 크기는 32 ~ 512 범위입니다. 모든 문제에 맞는 이상적인 배치 크기는 없습니다. |
| epochs    | 모델이 제공된 데이터 세트를 볼 횟수입니다. |

##### 학습 루프 시작

```js
return await model.fit(inputs, labels, {
  batchSize,
  epochs,
  callbacks: tfvis.show.fitCallbacks(
    { name: 'Training Performance' },
    ['loss', 'mse'],
    { height: 200, callbacks: ['onEpochEnd'] }
  )
});
```

model.fit은 학습 루프를 시작하기 위해 호출하는 함수입니다. 비동기 함수이므로 제공된 Promise를 반환하여 학습이 완료되는 시기를 호출자가 결정할 수 있습니다.

학습 진행 상황을 모니터링하기 위해 model.fit 에 일부 콜백을 전달합니다. `tfvis.show.fitCallbacks`를 사용해 앞에서 지정한 손실및 MSE 측정항목에 대한 차트를 그리는 함수를 생성합니다.

##### 모델 학습

run 함수에서 위의 함수를 추가합니다.

```js
    // Convert the data to a form we can use for training.
    const tensorData = convertToTensor(data);
    const {inputs, labels} = tensorData;

    // Train the model
    await trainModel(model, inputs, labels);
    console.log('Done Training');
```

실행을 누르면 실행 결과를 확인할 수 있습니다.

<script type="text/javascript">
async function trainModel(model, inputs, labels) {
    // Prepare the model for training.
    model.compile({
        optimizer: tf.train.adam(),
        loss: tf.losses.meanSquaredError,
        metrics: ['mse']
    });
    const batchSize = 32;
    const epochs = 50;

    return await model.fit(inputs, labels, {
        batchSize,
        epochs,
        shuffle: true,
        callbacks: tfvis.show.fitCallbacks(
            document.getElementById("Tensorflow-js-Training-Model-Linear-Regression-Train-Model"),
            ['loss', 'mse'],
            { height: 200, callbacks: ['onEpochEnd'] }
        )
    });
}

async function run003() {
    // Load and plot the original input data that we are going to train on.
    const data = await getData();
    const values = data.map(d => ({
        x: d.horsepower,
        y: d.mpg
    }));

    const model = createModel();
    
    // Convert the data to a form we can use for training.
    const tensorData = convertToTensor(data);
    console.log(tensorData);
    const {inputs, labels} = tensorData;

    // Train the model
    await trainModel(model, inputs, labels);
    console.log('Done Training');
}
</script>
<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<div class="row mb-3">
    <div class="col-9 p-3">
        <div id="Tensorflow-js-Training-Model-Linear-Regression-Train-Model"></div>
    </div>
    <div class="col-3 ps-5">
        <button type="button" class="btn btn-secondary" onClick="run003()">실행</button>
    </div>
</div>

이 그래프는 앞에서 만든 콜백에 의해 생성되며 각 작업이 끝나면 전체 데이터 세트의 평균 손실 및 MSE 를 표시합니다.

모델을 학습시킬 때 손실이 감소하는지 확인하고 싶다면 이 경우 측정 항목은 오차를 나타내므로 측정항목도 함께 감소하는지 확인하려고 합니다.

[Gradient descent, how neural networks learn \| Chapter 2, Deep learning](https://www.youtube.com/watch?v=IHZwWFHWa-w)

#### 7. 예측 수행

이제 모델이 학습되었으니 몇 가지 예측을 해 보겠습니다. 마력이 낮은 것부터 높은 것까지 일정한 범위의 숫자를 어떻게 예측하는지 확인하여 모델을 평가해 보겠습니다.

```js
function testModel(model, inputData, normalizationData) {
    const { inputMax, inputMin, labelMin, labelMax } = normalizationData;

    // Generate predictions for a uniform range of numbers between 0 and 1;
    // We unnormalize the data by doing the inverse of the min-max scaling that we did earlier.
    const [xs, preds] = tf.tidy(() => {
        const xs = tf.linspace(0, 1, 100);
        const preds = model.predict(xs.reshape([100, 1]));

        const unNormXs = xs.mul(inputMax.sub(inputMin)).add(inputMin);
        const unNormPreds = preds.mul(labelMax.sub(labelMin)).add(labelMin);

        // Return unnormalize the data
        return [ unNormXs.dataSync(), unNormPreds.dataSync()];
    });

    const predictedPoints = Array.from(xs).map((val, i) => {
        return { x: val, y: preds[i]};
    });

    const originalPoints = inputData.map(d => ({
        x: d.horsepower, y: d.mpg
    }));

    tfvis.render.scatterplot(
        { name: 'Model Predictions vs Original Data' },
        { values: [originalPoints, predictedPoints], series: ['original', 'predicted']},
        {
            xLabel: 'Horsepower',
            yLabel: 'MPG',
            height: 300
        }
    );
}
```

```js
const xs = tf.linspace(0, 1, 100);
const preds = model.predict(xs.reshape([100, 1]));
```

모델에 제공할 새 예시를 100개 생성합니다. Model.predict는 이러한 예시를 모델에 제공하는 방법입니다. 학습할 때와 유사한 형태 (`[num_examples, num_features_per_example]`)여야 합니다.

```js
// Un-normalize the data
const unNormXs = xs
  .mul(inputMax.sub(inputMin))
  .add(inputMin);

const unNormPreds = preds
  .mul(labelMax.sub(labelMin))
  .add(labelMin);
```

데이터를 0 ~ 1 이 아닌 원래 범위로 되돌리려면 정규화 중에 계산한 값을 사용하고 연산을 반전시킵니다.

```js
return [unNormXs.dataSync(), unNormPreds.dataSync()];
```

`.dataSync()`는 텐서에 저장된 값의 `typedarray`를 가져오는 데 사용할 수 있는 메서드입니다. 이 메서드를 통해 일반 자바스크립트에서 해당 값을 처리할 수 있습니다. 일반적으로 권장되는 `.data()` 메서드의 동기식 버전입니다.

run 함수에 다음을 추가합니다.

```js
    // Make some predictions using the model and compare them to the original data
    testModel(model, data, tensorData);
```

> 선형 회귀란 입력 데이터에 나타나는 추세에 선을 맞추려고 시도하는 방법입니다.

<script type="text/javascript">
function testModel(model, inputData, normalizationData) {
    const { inputMax, inputMin, labelMin, labelMax } = normalizationData;

    // Generate predictions for a uniform range of numbers between 0 and 1;
    // We unnormalize the data by doing the inverse of the min-max scaling that we did earlier.
    const [xs, preds] = tf.tidy(() => {
        const xs = tf.linspace(0, 1, 100);
        const preds = model.predict(xs.reshape([100, 1]));

        const unNormXs = xs.mul(inputMax.sub(inputMin)).add(inputMin);
        const unNormPreds = preds.mul(labelMax.sub(labelMin)).add(labelMin);

        // Return unnormalize the data
        return [ unNormXs.dataSync(), unNormPreds.dataSync()];
    });

    const predictedPoints = Array.from(xs).map((val, i) => {
        return { x: val, y: preds[i]};
    });

    const originalPoints = inputData.map(d => ({
        x: d.horsepower, y: d.mpg
    }));

    tfvis.render.scatterplot(
        document.getElementById("Tensorflow-js-Training-Model-Linear-Regression-Test-Model"),
        { values: [originalPoints, predictedPoints], series: ['original', 'predicted']},
        {
            xLabel: 'Horsepower',
            yLabel: 'MPG',
            height: 300
        }
    );
}

async function run004() {
    // Load and plot the original input data that we are going to train on.
    const data = await getData();
    const values = data.map(d => ({
        x: d.horsepower,
        y: d.mpg
    }));

    const model = createModel();
    
    // Convert the data to a form we can use for training.
    const tensorData = convertToTensor(data);
    console.log(tensorData);
    const {inputs, labels} = tensorData;

    // Train the model
    await trainModel(model, inputs, labels);

    testModel(model, data, tensorData);
}
</script>

<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<div class="row mb-3">
    <div class="col-9 p-3">
        <div id="Tensorflow-js-Training-Model-Linear-Regression-Test-Model"></div>
    </div>
    <div class="col-3 ps-5">
        <button type="button" class="btn btn-secondary" onClick="run004()">실행</button>
    </div>
</div>

#### 9. 시도해 볼 만한 작업

- Epoch 를 변경하여 실험합니다.
- 히든 레이어의 단위 수를 늘려 실험합니다.
- 처음에 추가한 히든 레이어와 최종 출력 레이어 사이에 히든 레이어를 추가하는 실험을 진행합니다.

```js
model.add(tf.layers.dense({units: 50, activation: 'sigmoid'}));
```

이러한 히든 레이어의 가장 중요하고 새로운 특징은 비선형 활성화 함수를 도입한다는 점입니다. 이 경우에는 시그모이드 활성화입이다.
