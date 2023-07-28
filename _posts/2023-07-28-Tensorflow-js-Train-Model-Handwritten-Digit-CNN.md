---
layout: post
section: "Tensorflow.js"
title: "Tensorflow.js Train model Handwritten Digit CNN"
description: ""
cover: /assets/images/posts/2023-07-28-Tensorflow-js-Train-Model-Handwritten-Digit-CNN.png
date: "2023/07/28 10:00:00"
categories: posts
tags: ["Tensorflow.js", "Tutorial", "Train Model", "Handwritten Digit CNN"]
---

### CNN으로 손글씨 숫자 인식

입력 데이터의 카테고리 예측을 분류 작업이라고 합니다. 분류 작업을 사용하려면 라벨에 대한 적절한 데이터 표현이 필요합니다. 라벨의 일반적인 표현에는 카테고리의 원-핫 인코딩이 포함됩니다.

##### 모델 빌드 및 실행

- 컨볼루셔널 모델은 이미지 작업에 효과적
- 분류 문제에서는 일반적으로 손실 함수에 범주형 교차 엔트로피를 사용
- 학습을 모니터링하여 손실이 감소하고 정확성이 개선되는지 확인

##### 모델 평가

- 학습이 끝난 후 모델을 평가할 방법을 결정하여 해결하고자 하는 초기 문제에서 잘 작동하는지 확인
- 클래스당 정확성과 혼동 행렬은 전체적인 정확성보다는 모델 성능을 상세 분석하는 데 유용

### 1. 소개

이 예제는 컨볼루셔널 신경망(CNN)을 활용해 필기 입력 숫자를 인식하는 Tensorflow.js 모델을 빌드합니다. 먼저 수천 개의 필기 입력 숫자 이미지와 해당 라벨을 인식하도록 분류기를 학습시킵니다. 그런 다음 모델에서 인식한 적 없는 테스트 데이터를 사용해 분류기의 정확성을 평가합니다.

이 작업은 입력 이미지에 카테고리(이미지에 표시되는 숫자)를 할당하도록 모델을 학습시키므로 분류 작업으로 간주됩니다. 많은 입력 예시와 올바른 출력을 인식시켜 모델을 학습시키는데, 이러한 학습을 지도학습이라고 합니다.

> Tensorflow.js 를 사용하여 브라우저에서 모델을 학습시키는 웹페이지를 만들고, 특정 크기의 흑백 이미지를 제공하여 이미지에 표시된 숫자를 분류합니다.
>
> - 데이터를 로드합니다.
> - 모델의 아키텍처를 정의합니다.
> - 모델을 학습시키고 학습하는 동안 성능을 모니터링합니다.
> - 몇 가지 예측을 수행하여 학습된 모델을 평가합니다.

### 2. 설정

아래와 같은 HTML 페이지를 작성하고 자바스크립트를 포함시킵니다.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>TensorFlow.js Tutorial</title>

  <!-- Import TensorFlow.js -->
  <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@1.0.0/dist/tf.min.js"></script>
  <!-- Import tfjs-vis -->
  <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs-vis@1.0.2/dist/tfjs-vis.umd.min.js"></script>

  <!-- Import the data file -->
  <script src="data.js" type="module"></script>

  <!-- Import the main script file -->
  <script src="script.js" type="module"></script>

</head>

<body>
</body>
</html>
```

#### 데이터 및 코드의 자바스크립트 파일 만들기

<script>
/**
 * @license
 * Copyright 2018 Google LLC. All Rights Reserved.
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

const IMAGE_SIZE = 784;
const NUM_CLASSES = 10;
const NUM_DATASET_ELEMENTS = 65000;

const NUM_TRAIN_ELEMENTS = 55000;
const NUM_TEST_ELEMENTS = NUM_DATASET_ELEMENTS - NUM_TRAIN_ELEMENTS;

const MNIST_IMAGES_SPRITE_PATH =
    'https://storage.googleapis.com/learnjs-data/model-builder/mnist_images.png';
const MNIST_LABELS_PATH =
    'https://storage.googleapis.com/learnjs-data/model-builder/mnist_labels_uint8';

/**
 * A class that fetches the sprited MNIST dataset and returns shuffled batches.
 *
 * NOTE: This will get much easier. For now, we do data fetching and
 * manipulation manually.
 */
class MnistData {
  constructor() {
    this.shuffledTrainIndex = 0;
    this.shuffledTestIndex = 0;
  }

  async load() {
    // Make a request for the MNIST sprited image.
    const img = new Image();
    const canvas = document.createElement('canvas');
    const ctx = canvas.getContext('2d');
    const imgRequest = new Promise((resolve, reject) => {
      img.crossOrigin = '';
      img.onload = () => {
        img.width = img.naturalWidth;
        img.height = img.naturalHeight;

        const datasetBytesBuffer =
            new ArrayBuffer(NUM_DATASET_ELEMENTS * IMAGE_SIZE * 4);

        const chunkSize = 5000;
        canvas.width = img.width;
        canvas.height = chunkSize;

        for (let i = 0; i < NUM_DATASET_ELEMENTS / chunkSize; i++) {
          const datasetBytesView = new Float32Array(
              datasetBytesBuffer, i * IMAGE_SIZE * chunkSize * 4,
              IMAGE_SIZE * chunkSize);
          ctx.drawImage(
              img, 0, i * chunkSize, img.width, chunkSize, 0, 0, img.width,
              chunkSize);

          const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);

          for (let j = 0; j < imageData.data.length / 4; j++) {
            // All channels hold an equal value since the image is grayscale, so
            // just read the red channel.
            datasetBytesView[j] = imageData.data[j * 4] / 255;
          }
        }
        this.datasetImages = new Float32Array(datasetBytesBuffer);

        resolve();
      };
      img.src = MNIST_IMAGES_SPRITE_PATH;
    });

    const labelsRequest = fetch(MNIST_LABELS_PATH);
    const [imgResponse, labelsResponse] =
        await Promise.all([imgRequest, labelsRequest]);

    this.datasetLabels = new Uint8Array(await labelsResponse.arrayBuffer());

    // Create shuffled indices into the train/test set for when we select a
    // random dataset element for training / validation.
    this.trainIndices = tf.util.createShuffledIndices(NUM_TRAIN_ELEMENTS);
    this.testIndices = tf.util.createShuffledIndices(NUM_TEST_ELEMENTS);

    // Slice the the images and labels into train and test sets.
    this.trainImages =
        this.datasetImages.slice(0, IMAGE_SIZE * NUM_TRAIN_ELEMENTS);
    this.testImages = this.datasetImages.slice(IMAGE_SIZE * NUM_TRAIN_ELEMENTS);
    this.trainLabels =
        this.datasetLabels.slice(0, NUM_CLASSES * NUM_TRAIN_ELEMENTS);
    this.testLabels =
        this.datasetLabels.slice(NUM_CLASSES * NUM_TRAIN_ELEMENTS);
  }

  nextTrainBatch(batchSize) {
    return this.nextBatch(
        batchSize, [this.trainImages, this.trainLabels], () => {
          this.shuffledTrainIndex =
              (this.shuffledTrainIndex + 1) % this.trainIndices.length;
          return this.trainIndices[this.shuffledTrainIndex];
        });
  }

  nextTestBatch(batchSize) {
    return this.nextBatch(batchSize, [this.testImages, this.testLabels], () => {
      this.shuffledTestIndex =
          (this.shuffledTestIndex + 1) % this.testIndices.length;
      return this.testIndices[this.shuffledTestIndex];
    });
  }

  nextBatch(batchSize, data, index) {
    const batchImagesArray = new Float32Array(batchSize * IMAGE_SIZE);
    const batchLabelsArray = new Uint8Array(batchSize * NUM_CLASSES);

    for (let i = 0; i < batchSize; i++) {
      const idx = index();

      const image =
          data[0].slice(idx * IMAGE_SIZE, idx * IMAGE_SIZE + IMAGE_SIZE);
      batchImagesArray.set(image, i * IMAGE_SIZE);

      const label =
          data[1].slice(idx * NUM_CLASSES, idx * NUM_CLASSES + NUM_CLASSES);
      batchLabelsArray.set(label, i * NUM_CLASSES);
    }

    const xs = tf.tensor2d(batchImagesArray, [batchSize, IMAGE_SIZE]);
    const labels = tf.tensor2d(batchLabelsArray, [batchSize, NUM_CLASSES]);

    return {xs, labels};
  }
}
</script>

위 HTML 파일과 같은 폴더에 `data.js` 파일을 만들고 [여기에 있는 파일](https://storage.googleapis.com/tfjs-tutorials/mnist_data.js)의 내용을 복사하고 붙여 넣습니다.

#### 첫 번째 자바스크립트 코드를 작성

```js
<script type="module">
    var run = () => {
        console.log("Hello Tensorflow");
    }
</script>
```
<script>
    function run001() {
        console.log("Hello Tensorflow");
    }
</script>

<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<div class="row mb-3">
    <div class="col-9 p-3">
        <div id="Tensorflow-js-Train-Model-Handwritten-Digit-CNN-First"></div>
    </div>
    <div class="col-3 ps-5">
        <button type="button" class="btn btn-secondary" onClick="run001()">실행</button>
    </div>
</div>

### 3. 데이터 로드

이 예제에서는 아래와 같이 이미지에서 숫자를 인식하도록 모델을 학습시킵니다. 이러한 이미지는 [MNIST](https://yann.lecun.com/exdb/mnist/)라는 이름의 데이터 세트에 포함된 28x28 픽셀의 그레이 스케일 이미지입니다.

![4](/assets/images/posts/Tensorflow-js-Train-Model-Handwritten-Digit-CNN/c01d85004462fd48.png)
![3](/assets/images/posts/Tensorflow-js-Train-Model-Handwritten-Digit-CNN/58d4cf23ee7202be.png)
![8](/assets/images/posts/Tensorflow-js-Train-Model-Handwritten-Digit-CNN/8998bab63049c12a.png)

`data.js`에는 다음과 같은 2개의 public 메서드가 포함된 MnistData 클래스가 있습니다.

| Method | Description |
| ------ | ----------- |
| nextTrainBatch(batchSize) | 학습 세트에서 이미지와 해당 라벨의 무작위 배치를 반환 |
| nextTestBatch(batchSize) | 테스트 세트에서 이미지와 해당 라벨의 배치를 반환 |

MnistData 클래스는 중요 단계인 데이터의 셔플링 및 정규화도 수행합니다.

총 65,000 개의 이미지가 있는데 그 중 최대 55,000 개의 이미지를 사용해 모델을 학습시키고 나머지 이미지 10,000개는 학습 완료 후에 모델의 성능을 테스트할 때 사용합니다.

#### 올바르게 데이터가 로드되는지 테스트

```js
import {MnistData} from './data.js';

async function showExamples(data) {
  // Create a container in the visor
  const surface =
    tfvis.visor().surface({ name: 'Input Data Examples', tab: 'Input Data'});

  // Get the examples
  const examples = data.nextTestBatch(20);
  const numExamples = examples.xs.shape[0];

  // Create a canvas element to render each example
  for (let i = 0; i < numExamples; i++) {
    const imageTensor = tf.tidy(() => {
      // Reshape the image to 28x28 px
      return examples.xs
        .slice([i, 0], [1, examples.xs.shape[1]])
        .reshape([28, 28, 1]);
    });

    const canvas = document.createElement('canvas');
    canvas.width = 28;
    canvas.height = 28;
    canvas.style = 'margin: 4px;';
    await tf.browser.toPixels(imageTensor, canvas);
    surface.drawArea.appendChild(canvas);

    imageTensor.dispose();
  }
}

async function run() {
    const data = new MnistData();
    await data.load();
    await showExamples(data);
}
```

<script>
    async function showExamples(data, visor, element) {
    // Create a container in the visor
        visor = visor || tfvis.visor();
        const surface = visor.surface({ name: 'Input Data Examples', tab: 'Input Data'});
        const o = document.getElementById(element ||"Tensorflow-js-Train-Model-Handwritten-Digit-CNN-Second");
        o.appendChild(visor.el);
        // TODO: CUSTOMIZE
        visor.el.querySelector(".visor-controls").remove();
        visor.el.querySelector(".visor").className = "visor";
        visor.el.querySelector(".visor-tabs").className = "visor-tabs";

        // Get the examples
        const examples = data.nextTestBatch(20);
        const numExamples = examples.xs.shape[0];

        // Create a canvas element to render each example
        for (let i = 0; i < numExamples; i++) {
            const imageTensor = tf.tidy(() => {
            // Reshape the image to 28x28 px
            return examples.xs
                .slice([i, 0], [1, examples.xs.shape[1]])
                .reshape([28, 28, 1]);
            });

            const canvas = document.createElement('canvas');
            canvas.width = 28;
            canvas.height = 28;
            canvas.style = 'margin: 4px;';
            await tf.browser.toPixels(imageTensor, canvas);
            surface.drawArea.appendChild(canvas);

            imageTensor.dispose();
        }
    }

    async function run002() {
        const data = new MnistData();
        await data.load();
        await showExamples(data);
    }
</script>
<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<div class="row mb-3">
    <div class="col-9 p-3">
        <div id="Tensorflow-js-Train-Model-Handwritten-Digit-CNN-Second"></div>
    </div>
    <div class="col-3 ps-5">
        <button type="button" class="btn btn-secondary" onClick="run002()">실행</button>
    </div>
</div>

목표는 이미지 1개를 가져와 이미지가 속할 수 있는 클래스 10개 각각의 점수(숫자 0 ~ 9)를 예측하는 모델을 학습시키는 것입니다.

각 이미지는 너무 28 픽셀, 높이 28 픽셀이며 그레이 스케일 이미지이므로 색상 채널이 1개입니다. 따라서 각 이미지의 모양은 [28, 28, 1] 입니다.

이미지의 모양 [28, 28, 1] 에서 1 ~ 10 사이의 값으로 매핑을 시키는 것입니다.

### 4. 모델 아키텍처 정의

이 섹션에서는 모델 아키텍처를 정의합니다. 모델 아키텍처란 모델이 실행될 때 모델이 실행하는 함수 또는 응답을 계산하기 위해 모델에서 사용하는 알고리즘으로 표현할 수 있습니다.

머신러닝에서는 아키텍처(알고리즘)를 정의하고 학습 프로세스에서 해당 알고리즘의 매개변수를 학습하도록 합니다.

```js
function getModel() {
    const model = tf.sequential();

    const IMAGE_WIDTH = 28;
    const IMAGE_HEIGHT = 28;
    const IMAGE_CHANNELS = 1;

    /**
     * In the first layer of our convolutional neural network we have to specify the input shape.
     * The we specify some parameters for the convolution operation that takes place in this layer.
     */

    model.add(tf.layers.conv2d({
        inputShape: [IMAGE_WIDTH, IMAGE_HEIGHT, IMAGE_CHANNELS],
        kernelSize: 5,
        filters: 8,
        strides: 1,
        activation: "relu",
        kernelInitializer: 'varianceScaling'
    }));

    /**
     * The MaxPooling layer acts as a sort of downsampling using max values in a region instead of averaging
     */

    model.add(tf.layers.maxPooling2d({
        poolSize: [2, 2], strides: [2, 2]
    }));

    /**
     * Repeat another conv2d + maxPooling stack.
     * Note that we have more filters in the convolutoin.
     */
    model.add(tf.layers.conv2d({
        kernelSize: 5,
        filters: 16,
        strides: 1,
        activation: 'relu',
        kernelInitializer: 'varianceScaling'
    }));

    model.add(tf.layers.maxPooling2d({
        poolSize: [2, 2], strides: [2, 2]
    }));

    /**
     * Now we flatten the output from the 2D filters into a 1D vector to prepare it for input into our last layer.
     * This is common practice when feeding higher dimensional data to a final classification output layer.
     */

    model.add(tf.layers.flatten());
    
    /**
     * Our last layer is a dense layer which has 10 output units, one for each output class (i.e. 0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
     */

    const NUM_OUTPUT_CLASSES = 10;

    model.add(tf.layers.dense({
        units: NUM_OUTPUT_CLASSES,
        kerenlInitializer: 'varianceScalig',
        activation: 'softmax'
    }));

    /**
     * Choose an optimizer, loss functoin and accuracy metric, the the compile and return the model.
     */

    const optimizer = tf.train.adam();
    model.compile({
        optimizer: optimizer,
        loss: 'categoricalCrossentropy',
        metrics: ['accuracy']
    });

    return model;
}
```

<script>
function getModel() {
    const model = tf.sequential();

    const IMAGE_WIDTH = 28;
    const IMAGE_HEIGHT = 28;
    const IMAGE_CHANNELS = 1;

    /**
     * In the first layer of our convolutional neural network we have to specify the input shape.
     * The we specify some parameters for the convolution operation that takes place in this layer.
     */

    model.add(tf.layers.conv2d({
        inputShape: [IMAGE_WIDTH, IMAGE_HEIGHT, IMAGE_CHANNELS],
        kernelSize: 5,
        filters: 8,
        strides: 1,
        activation: "relu",
        kernelInitializer: 'varianceScaling'
    }));

    /**
     * The MaxPooling layer acts as a sort of downsampling using max values in a region instead of averaging
     */

    model.add(tf.layers.maxPooling2d({
        poolSize: [2, 2], strides: [2, 2]
    }));

    /**
     * Repeat another conv2d + maxPooling stack.
     * Note that we have more filters in the convolutoin.
     */
    model.add(tf.layers.conv2d({
        kernelSize: 5,
        filters: 16,
        strides: 1,
        activation: 'relu',
        kernelInitializer: 'varianceScaling'
    }));

    model.add(tf.layers.maxPooling2d({
        poolSize: [2, 2], strides: [2, 2]
    }));

    /**
     * Now we flatten the output from the 2D filters into a 1D vector to prepare it for input into our last layer.
     * This is common practice when feeding higher dimensional data to a final classification output layer.
     */

    model.add(tf.layers.flatten());
    
    /**
     * Our last layer is a dense layer which has 10 output units, one for each output class (i.e. 0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
     */

    const NUM_OUTPUT_CLASSES = 10;

    model.add(tf.layers.dense({
        units: NUM_OUTPUT_CLASSES,
        kerenlInitializer: 'varianceScalig',
        activation: 'softmax'
    }));

    /**
     * Choose an optimizer, loss functoin and accuracy metric, the the compile and return the model.
     */

    const optimizer = tf.train.adam();
    model.compile({
        optimizer: optimizer,
        loss: 'categoricalCrossentropy',
        metrics: ['accuracy']
    });

    return model;
}
</script>

#### 컨볼루션

```js
model.add(tf.layers.conv2d({
    inputShape: [IMAGE_WIDTH, IMAGE_HEIGHT, IMAGE_CHANNELS],
    kernelSize: 5,
    filters: 8,
    strides: 1,
    activation: 'relu',
    kernelInitializer: 'varianceScaling'
}));
```

- 순차적 모델을 사용합니다.
- 밀집 레이어 대신 conv2d 레이어를 사용합니다.

> - [이미지 커널의 시각적 설명](https://setosa.io/ev/image-kernels/)
> - [시각 인식을 위한 컨볼루셔널 신경망](https://cs231n.github.io/convolutional-networks/)

여기서는 순차적 모델을 사용합니다.

##### conv2d 에 대한 구성 객체

| Param | Description |
| ----- | ----------- |
| inputShape | 모델의 첫 번째 레이러로 전달될 데이터의 모양입니다. 이 경우의 MNIST 예시는 28x28 픽셀 흑백 이미지입니다. 이미지 데이터의 표준 형식은 `[row, column, depth]`이미로 여기서는 `[28, 28, 1]`의 모양을 구성합니다. 각 차원의 픽셀 수로 28개의 행과 열이 있고 이미지에 색상 채널이 1개만 있으므로 깊이는 1입니다. 입력 모양에 배치 크기를 지정하지 않는다는 점에 유의하세요. 레이어는 배치 크기의 제약이 없도록 설계되므로 추론 중에 모든 배치 크기의 텐서를 전달할 수 있습니다. |
| kernelSize | 입력 데이터에 적용되는 슬라이딩 컨볼루션 필터 창의 크기입니다. 여기서는 kernelSize를 5로 설정하며 정사각형의 5x5 컨볼루셔널 창을 나타냅니다. |
| filters | 입력 데이터에 적용할 kernelSize 크기의 필터 창 수입니다. 여기서는 데이터에 8개의 필터를 적용합니다. |
| strides | 슬라이딩 창의 보폭(즉, 이미지에서 이동할 때마다 필터가 변경하는 픽셀의 수)입니다. 여기서는 스트라이드를 1로 지정합니다. 필터가 1픽셀 보폭으로 이미지에서 슬라이딩한다는 의미입니다. |
| activation | 컨볼루션이 안료된 후 데이터에 적용할 활성화 함수입니다. 이 경우에는 ML 모델에서 일반적인 활성화 함수인 정류 선형 유닌(ReLU) 함수를 적용합니다. |
| kernelInitializer | 모델 가중치를 무작위로 초기화하는 데 사용하는 메서드로서 동적인 학습에 매우 중요합니다. |

dense 레이어만 사용해 이미지 분류기를 빌드할 수도 있지만 컨볼루셔널 레이어는 많은 이미지 기반 작업에서 그 효과가 입증된 레이어입니다.

##### 데이터 표현 평면화

```js
model.add(tf.layers.flatten());
```

이미지는 고차원 데이터이며 컨볼루션 연산에서는 전달된 데이터의 크기를 늘리는 경향이 있습니다. 최종 분류 레이어로 전달하기 전에 데이터를 하나의 긴 배열로 평면화해야 합니다. 최종 레이어로 사용하는 dense 레이어에서 tensor1d만 사용하므로 이 단계는 대부분의 분류 작업에서 일반적으로 사용됩니다.

> 참고: 평면화된 레이어에는 가중치가 없습니다. 단지 입력을 긴 배욜로 전개할 뿐입니다.

##### 최종 확률 분포 계산

```js
const NUM_OUTPUT_CLASSES = 10;
model.add(tf.layers.dense({
    units: NUM_OUTPUT_CLASSES,
    kernelInitializer: 'varianceScaling',
    activation: 'softmax'
}));
```

dense 레이어와 소프트 맥스 활성화를 함께 사용하여 가능한 10개의 클래스에 대한 확률 분포를 계산합니다. 점수가 가장 높은 클래스가 예측 숫자가 됩니다.

여기서는 1 ~ 10 매핑(입력 이미지 1개에 확률 10개)을 적용해야 하므로 출력 레이어에 10개의 단위가 존재합니다.

소프트맥스는 분류 작업의 마지막 레이어에서 사용할 가능성이 가장 높은 활성화입니다.

##### 옵티마이저 및 손실 함수 선택

```js
const optimizer = tf.train.adam();
model.compile({
    optimizer: optimizer,
    loss: 'categoricalCrossentropy',
    metrics: ['accuracy'],
});
```

옵티마이저, 손실함수, 추적할 측정항목을 지정하는 모델을 컴파일합니다.

> `categoricalCrossentropy`는 모델의 마지막 레이엉에서 생성된 확률 분포와 True 라벨에서 지정한 확률 분포 간의 오차를 측정합니다.

예를 들어 숫자가 실제로 7을 나타낼 경우 다음과 같은 결과가 표시될 수 있습니다.


| 색인      | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
| --------  | - | - | - | - | - | - | - | - | - | - |
| True 라벨 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 |
| 예측      | 0.1 | 0.01 | 0.01 | 0.01 | 0.20 | 0.01 | 0.01 | 0.60 | 0.03 | 0.02 |

범주형 교차 엔트로피는 예측 벡터가 True 라벨 벡터와 얼마나 유사한지를 나타내는 하나의 숫자를 생성합니다.

여기서 라벨에 사용되는 데이터 표현을 원-핫 인코딩이라고 하며 분류 문제에서 널시 사용됩니다. 각 예시의 클래스마다 연결된 확률이 있습니다. 어떻게 설정해야 할지 정확히 안다면 이 확률을 1로 설정하고 다른 확률은 0으로 설정하면 됩니다. 

> [표현: 특성 추출](https://developers.google.com/machine-learning/crash-course/representation/feature-engineering?hl=ko)

모니터링할 다른 측정항목은 accuracy이며 분류 문제에 대한 모든 예측 중 올바른 예측의 비율입니다.

### 5. 모델 학습

```js
async function train(model, data) {
    const metrics = ['loss', 'val_loss', 'acc', 'val_acc'];
    const container = {
        name: 'Model Training', tab: 'Model', styles: { height: '1000px' }
    };
    const fitCallbacks = tfvis.show.fitCallbacks(container, metrics);

    const BATCH_SIZE = 512;
    const TRAIN_DATA_SIZE = 5500;
    const TEST_DATA_SIZE = 1000;

    const [ trainXs, trainYs ] = tf.tidy(() => {
        const d = data.nextTrainBatch(TRAIN_DATA_SIZE);
        return [
            d.xs.reshape([TRAIN_DATA_SIZE, 28, 28, 1]),
            d.labels
        ];
    });

    const [ testXs, testYs ] = tf.tidy(() => {
        const d = data.nextTestBatch(TEST_DATA_SIZE);
        return [
            d.xs.reshape([TEST_DATA_SIZE, 28, 28, 1]),
            d.labels
        ];
    });

    return model.fit(trainXs, trainYs, {
        batchSize: BATCH_SIZE,
        validationData: [testXs, testYs],
        epochs: 10,
        shuffle: true,
        callbacks: fitCallbacks
    });
}
```

run 함수에 다음의 코드를 추가합니다.

```js
const model = getModel();
tfvis.show.modelSummary({name: 'Model Architecture', tab: 'Model'}, model);
await train(model, data);
```

<script>
async function train(model, data) {
    const metrics = ['loss', 'val_loss', 'acc', 'val_acc'];
    const container = {
        name: 'Model Training', tab: 'Model', styles: { height: '1000px' }
    };
    const fitCallbacks = tfvis.show.fitCallbacks(document.getElementById("Tensorflow-js-Train-Model-Handwritten-Digit-CNN-Third"), metrics);

    const BATCH_SIZE = 512;
    const TRAIN_DATA_SIZE = 5500;
    const TEST_DATA_SIZE = 1000;

    const [ trainXs, trainYs ] = tf.tidy(() => {
        const d = data.nextTrainBatch(TRAIN_DATA_SIZE);
        return [
            d.xs.reshape([TRAIN_DATA_SIZE, 28, 28, 1]),
            d.labels
        ];
    });

    const [ testXs, testYs ] = tf.tidy(() => {
        const d = data.nextTestBatch(TEST_DATA_SIZE);
        return [
            d.xs.reshape([TEST_DATA_SIZE, 28, 28, 1]),
            d.labels
        ];
    });

    return model.fit(trainXs, trainYs, {
        batchSize: BATCH_SIZE,
        validationData: [testXs, testYs],
        epochs: 10,
        shuffle: true,
        callbacks: fitCallbacks
    });
}
</script>

<script>
    async function run003() {
        const data = new MnistData();
        await data.load();
        await showExamples(data, tfvis.visor(), "Tensorflow-js-Train-Model-Handwritten-Digit-CNN-Third");
        const model = getModel();
        tfvis.show.modelSummary(document.getElementById("Tensorflow-js-Train-Model-Handwritten-Digit-CNN-Third"), model);

        await train(model, data);
        console.log("done");
    }
</script>
<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<div class="row mb-3">
    <div class="col-9 p-3">
        <div id="Tensorflow-js-Train-Model-Handwritten-Digit-CNN-Third"></div>
    </div>
    <div class="col-3 ps-5">
        <button type="button" class="btn btn-secondary" onClick="run003()">실행</button>
    </div>
</div>

#### 측정항목 모니터링

```js
const metrics = ['loss', 'val_loss', 'acc', 'val_acc'];
```

여기서는 모니터링할 측정항목을 결정합니다. 학습 세트에서의 손실과 정확성을 모니터링하고 검증 세트에서의 손실과 정확성도 모니터링합니다.

> 주의: Layers API 를 사용하면 배치와 Epochs 마다 손실 및 정확성이 계산됩니다.

#### 데이터를 텐서로 준비

```js
const BATCH_SIZE = 512;
const TRAIN_DATA_SIZE = 5500;
const TEST_DATA_SIZE = 1000;

const [trainXs, trainYs] = tf.tidy(() => {
  const d = data.nextTrainBatch(TRAIN_DATA_SIZE);
  return [
    d.xs.reshape([TRAIN_DATA_SIZE, 28, 28, 1]),
    d.labels
  ];
});

const [testXs, testYs] = tf.tidy(() => {
  const d = data.nextTestBatch(TEST_DATA_SIZE);
  return [
    d.xs.reshape([TEST_DATA_SIZE, 28, 28, 1]),
    d.labels
  ];
});
```

여기서는 2개의 데이터 세트, 즉 모델을 학습시킬 학습 세트와 각 Epoch가 끝날 때 모델을 테스트할 검증 세트를 만듭니다. 단, 검증 세트의 데이터는 학습 중에 모델에 표시되지 않습니다.

제공된 데이터 클래스를 사용하면 이미지 데이터에서 텐서를 쉽게 가져올 수 있습니다. 하지만 모델에 전달하기 전에 모델에서 예상하는 모양(`[num_examples, image_width, image_height, channels]`)으로 텐서를 바꿔야 합니다. 각 데이터 세트에는 입력(x)과 라벨(y)이 모두 있습니다.

> 참고: trainDataSize를 5,500으로 설정하고 testDataSize를 1,000으로 설정하면 빠르게 실험을 진행할 수 있습니다.

```js
return model.fit(trainXs, trainYs, {
  batchSize: BATCH_SIZE,
  validationData: [testXs, testYs],
  epochs: 10,
  shuffle: true,
  callbacks: fitCallbacks
});
```

model.fit을 호출해 학습 루프를 시작합니다. 또한 각 Epoch가 끝난 후 모델이 테스트에 사용할 데이터(학습에는 사용하지 않음)를 나타내기 위해 validationData 속성도 전달합니다.

학습 데이터는 결과가 좋지만 검증 데이터는 결과가 좋지 않다면 모델이 학습 데이터에 과적합했을 가능성이 크며 이전에 인식되지 않은 입력에 효과적으로 일반화되지 않는다는 의미입니다.

### 6. 모델 평가

검증 정확성은 이전에 인식되지 않은 데이터(어떤 식으로든 검증 세트와 유사한 데이터)에서 모델이 어떻게 작동할지를 예측하는 데 도움이 됩니다. 하지만 다양한 클래스에서 성능을 상세 분석하고 싶을 수도 있습니다.

```js
const classNames = ['Zero', 'One', 'Two', 'Three', 'Four', 'Five', 'Six', 'Seven', 'Eight', 'Nine'];
function doPrediction(model, data, testDataSize = 500) {
    const IMAGE_WIDTH = 28;
    const IMAGE_HEIGHT = 28;
    const testData = data.nextTestBatch(testDataSize);
    const testxs = testData.xs.reshape([testDataSize, IMAGE_WIDTH, IMAGE_HEIGHT, 1]);
    const labels = testData.labels.argMax(-1);
    const preds = model.predict(testxs).argMax(-1);

    testxs.dispose();
    return [preds, labels];
}
async function showAccuracy(model, data) {
    const [preds, labels] = doPrediction(model, data);
    const classAccuracy = await tfvis.metrics.perClassAccuracy(labels, preds);
    const container = {name: 'Accuracy', tab: 'Evaluation'};
    tfvis.show.perClassAccuracy(container, classAccuracy, classNames);

    labels.dispose();
}

async function showConfusion(model, data) {
    const [preds, labels] = doPrediction(model, data);
    const confusionMatrix = await tfvis.metrics.confusionMatrix(labels, preds);
    const container = {name: 'Confusion Matrix', tab: 'Evaluation'};
    tfvis.render.confusionMatrix(container, {values: confusionMatrix, tickLabels: classNames});

    labels.dispose();
}
```

<script>
const classNames = ['Zero', 'One', 'Two', 'Three', 'Four', 'Five', 'Six', 'Seven', 'Eight', 'Nine'];
function doPrediction(model, data, testDataSize = 500) {
    const IMAGE_WIDTH = 28;
    const IMAGE_HEIGHT = 28;
    const testData = data.nextTestBatch(testDataSize);
    const testxs = testData.xs.reshape([testDataSize, IMAGE_WIDTH, IMAGE_HEIGHT, 1]);
    const labels = testData.labels.argMax(-1);
    const preds = model.predict(testxs).argMax(-1);

    testxs.dispose();
    return [preds, labels];
}
async function showAccuracy(model, data) {
    const [preds, labels] = doPrediction(model, data);
    const classAccuracy = await tfvis.metrics.perClassAccuracy(labels, preds);
    const container = document.getElementById("Tensorflow-js-Train-Model-Handwritten-Digit-CNN-Fourth");
    tfvis.show.perClassAccuracy(container, classAccuracy, classNames);
    console.log(1);

    labels.dispose();
}

async function showConfusion(model, data) {
    const [preds, labels] = doPrediction(model, data);
    const confusionMatrix = await tfvis.metrics.confusionMatrix(labels, preds);
    const container = document.getElementById("Tensorflow-js-Train-Model-Handwritten-Digit-CNN-Fourth");
    tfvis.render.confusionMatrix(container, {values: confusionMatrix, tickLabels: classNames});
    console.log(2);
    labels.dispose();
}
</script>

#### 예측 수행

```js
function doPrediction(model, data, testDataSize = 500) {
    const IMAGE_WIDTH = 28;
    const IMAGE_HEIGHT = 28;
    const testData = data.nextTestBatch(testDataSize);
    const testxs = testData.xs.reshape([testDataSize, IMAGE_WIDTH, IMAGE_HEIGHT, 1]);
    const labels = testData.labels.argMax(-1);
    const preds = model.predict(testxs).argMax(-1);

    testxs.dispose();
    return [preds, labels];
}
```

먼저 예측을 수행해야 합니다. 여기서는 이미지 500개를 가져와 이미지에 포함된 숫자를 예측합니다. 나중에 수를 늘려 더 많은 이미지 모음에서 테스트해 볼 수 있습니다.

특히 argmax 함수는 확률이 가장 높은 클래스 색인을 제공합니다. 모델은 각 클래스의 확률을 출력한다는 점을 유의하세요. 여기서는 가장 높은 확률을 찾아 이를 예측으로 할당합니다.

또한 한 번에 500개의 예시 모두에 대한 예측을 수행할 수도 있습니다.

> 참고: 여기서는 확률 임계값을 사용하지 않습니다. 상대적으로 낮더라도 가장 높은 값을 사용합니다. 이 프로젝트를 확장하여 필요한 최소 확률을 설정하고 이 분류 임계값을 충족하는 클래스가 없으면 숫자를 찾을 수 없음이라고 표시할 수 있습니다.

doPredctions 에서는 모델이 학습된 후 일반적으로 예측을 수행하는 방법을 보여줍니다. 하지만 새 데이터에서는 기존 라벨이 사용되지 않습니다.

#### 클래스당 정확성 표시

```js
async function showAccuracy() {
  const [preds, labels] = doPrediction();
  const classAccuracy = await tfvis.metrics.perClassAccuracy(labels, preds);
  const container = { name: 'Accuracy', tab: 'Evaluation' };
  tfvis.show.perClassAccuracy(container, classAccuracy, classNames);

  labels.dispose();
}
```

예측 집합과 라벨을 사용해 각 클래스의 정확성을 계산할 수 있습니다.

#### 혼동 행렬 표시

```js
async function showConfusion() {
  const [preds, labels] = doPrediction();
  const confusionMatrix = await tfvis.metrics.confusionMatrix(labels, preds);
  const container = { name: 'Confusion Matrix', tab: 'Evaluation' };
  tfvis.render.confusionMatrix(container, {values: confusionMatrix, tickLabels: classNames});

  labels.dispose();
}
```

혼동 행렬은 클래스당 정확성과 비슷하지만 상세 분석을 통해 잘못된 분류의 패턴을 보여줍니다. 이를 통해 모델에 특정 클래스 쌍에 대한 혼동이 있는지 확인하 수 있습니다.

run 함수 하단에 다음 코드를 추가하면 평가를 표시합니다.

```js
await showAccuracy(model, data);
await showConfusion(model, data);
```

<script>
    async function run004() {
        const data = new MnistData();
        await data.load();
        await showExamples(data, tfvis.visor(), "Tensorflow-js-Train-Model-Handwritten-Digit-CNN-Fourth");
        const model = getModel();
        tfvis.show.modelSummary(document.getElementById("Tensorflow-js-Train-Model-Handwritten-Digit-CNN-Fourth"), model);

        await train(model, data);
        await showAccuracy(model, data);
        await showConfusion(model, data);
    }
</script>
<!-- TODO: LOADING 적용 / https://loading.io/css/ -->
<div class="row mb-3">
    <div class="col-9 p-3">
        <div id="Tensorflow-js-Train-Model-Handwritten-Digit-CNN-Fourth"></div>
    </div>
    <div class="col-3 ps-5">
        <button type="button" class="btn btn-secondary" onClick="run004()">실행</button>
    </div>
</div>
