---
layout: post
section: "Tensorflow.js"
title: "Tensorflow.js Conversion Import Keras"
description: ""
cover: /assets/images/posts/2023-08-04-Tensorflow-js-Conversion-Import-Keras.png
date: "2023/08/04 09:38:00"
categories: posts
tags: ["Tensorflow.js", "Tutorial", "Conversion", "Import Keras"]
---

### Keras 모델을 Tensorflow.js 로 가져오기

Keras 모델은 여러 형식 중 하나로 저장할 수 있습니다. '전체 모델' 형식은 추론 또는 추가 훈련을 위해 Tensorflow.js 에 직접 로드할 수 있는 Tensorflow.js Layer 형식으로 변환할 수 있습니다.

대상 Tensorflow.js Layer 형식은 model.json 파일과 바이너리 형식의 샤딩된 가중치 파일 집합이 포함된 디렉터리입니다. model.json 파일에는 모델 토폴로지('아키텍처' 또는 '그래프' 즉, 레이어에 대한 설명 및 연결 방법)와 가중치 파일의 매니페스트가 모두 포함되어 있습니다.

### 요구사항

변환 절차에는 Python 환경이 필요합니다. pipenv 또는 virtualenv 를 사용하여 격리된 것을 유지할 수 있습니다. 변환기를 설치하려면 `pip install tensorflowjs` 를 실행시켜야 합니다.

Keras 모델을 Tensorflow.js로 가져오는 것은 2단계 프로세스입니다. 먼저 기존 Keras 모델을 TF.js Layer 형식으로 변환한 다음 Tensorflow.js 로 로드합니다.

#### 1. 기존 Keras 모델을 TF.js Layer 형식으로 변환하기

Keras 모델은 일반적으로 `model.save(path)`를 통해 저장되며 모델 토폴로지와 가중치를 모두 포함하는 단일 HDF5(.h5) 파일을 생성합니다. 해당 파일을 TF.js Layer 형식으로 변환하려면 다음 명령을 실행해야 합니다. 

```sh
tensorflowjs_converter --input_format keras
                       [path to model:.h]
                       [path to target directory]
```

파이썬 API를 사용하여 TF.js Layer 형식으로 직접 내보낼 수 있습니다.

```python
import tensorflowjs as tfjs

def train(...):
    model = keras.models.Sequential()   # for example
    ...
    model.compile(...)
    model.fit(...)
    tfjs.converters.save_keras_model(model, tfjs_target_dir)
```
    
#### 2. Tensorflow.js 에 모델 로드하기

웹 서버를 사용하여 1단계에서 생성한 변환된 모델 파일을 서비스할 수 있습니다. Javascript 에서 파일 가져오기를 허용하려면 Cross-Origin Resource Sharing(CORS)을 허용하는 서버를 구성해야 할 수도 있습니ㅏㄷ.

그런 다음 model.json 파일에 URL을 제공하여 Tensorflow.js 에 모델을 로드합니다.

```js
import * as tf from '@tensorflow/tfjs';

const model = await tf.loadLayersModel('https://foo.bar/tfjs_artifacts/model.json');
```

이제 Javascript를 이용하여 모델을 추론, 평가 또는 재훈련할 수 있습니다.

```js
const example = tf.fromPixels(webcamElement);  // for example
const prediction = model.predict(example);
```

model.json 파일 이름을 사용하여 전체 모델을 참조합니다. `loadModel(...)`은 model.json를 가져온 다음 추가 HTTP(S) 요청을 수행하여 model.json 가중치 매니페스트에서 참조되는 분할된 가중치 파일을 가져옵니다. 이 접근 방식을 사용하면 model.json 및 가중치 분할 요소가 각각 일반적인 캐시 파일 크기 제한보다 작아서 모든 파일을 브라우저에서 캐시할 수 있습니다. 따라서 모델은 이후에 더 빨리 로드될 수 있습니다.

> Tensorflow.js Layer 는 현재 표준 Keras 구성을 사용하여 Keras 모델만 지원합니다. 지원되지 않는 연산 또는 레이어(예: 사용자 정의 레이어, 람다 레이어, 사용자 정의 Loss 및 사용자 정의 매트릭)를 사용하여 모델은 Javascript로 안정적으로 변환할 수 없는 Python 코드에 의존하기 때문에 자동으로 가져올 수 없습니다.
