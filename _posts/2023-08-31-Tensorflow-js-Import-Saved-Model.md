---
layout: post
section: "Tensorflow.js"
title: "Tensorflow.js Import Saved Model"
description: ""
cover: /assets/images/posts/2023-08-31-Tensorflow-js-Import-Saved-Model.png
date: "2023/08/31 10:11:00"
categories: posts
tags: ["Tensorflow.js", "Tutorial", "Import", "Saved Model", "Conversion"]
---

### Tensorflow GraphDef 기반 모델을 Tensorflow.js 로 가져오기

Tensorflow GraphDef 기반 모델은 다음 형식 중 하나로 저장할 수 있습니다.

- TensorFlow 저장된 모델
- 고정 모델
- Tensorflow Hub 모듈

위의 모든 형식은 Tensorflow.js 변환기에서 Tensorflow.js 로 직접 로드할 수 있는 형식으로 변환하여 추론할 수 있습니다.

#### 요구사항

변환 절차에는 Python 환경이 필요한데 pipenv 또는 virtualenv 를 사용하여 환경의 격리를 유지해야 합니다. 변환기를 설치하려면 다음 명령을 실행하세요.

```sh
pip install tensorflowjs
```

Tensorflow 모델을 Tensorflow.JS 로 가져오는 것은 2단계의 프로세스를 거칩니다. 먼저 기본 모델을 Tensorflow.js 웹 형식으로 변환한 다음 Tensorflow.js 로 로드 합니다.

#### 1단계: 기존 Tensorflow 모델을 Tensorflow.js 웹 형식으로 변환하기

pip 패키지에서 제공하는 변환기 스크립트를 실행합니다.

##### 저장된 모델

```sh
tensorflowjs_converter                                      \
    --input_format=tf_saved_model                           \
    --output_node_names='MobilenetV1/Predictions/Reshape_1' \
    --saved_model_tags=serve                                \
    /mobilenet/saved_model                                  \
    /mobilenet/web_model
```

##### 고정모델

```sh
tensorflowjs_converter                                      \
    --input_format=tf_frozen_model                          \
    --output_node_names='MobilenetV1/Predictions/Reshape_1' \
    /mobilenet/frozen_model.pb                              \
    /mobilenet/web_model
```

##### Tensorflow Hub 모듈

```sh
tensorflowjs_converter                                                          \
    --input_format=tf_hub                                                       \
    'https://tfhub.dev/google/imagenet/mobilenet_v1_100_224/classification/1'   \
    /mobilenet/web_model
```

#### `tensorflowjs_converter` 파라미터 설명

| 파라미터 | 설명 |
| -------- | ---- |
| input_path | 저장된 모델 디렉토리, 고정 모델 파일, Tensorflow Hub 모듈 핸들 또는 경로의 전체 경로 |
| output_path | 모든 출력 아티팩트의 경로 |
| --input_format | 입력 모델의 형식은 저장된 모델에 `tf_saved_model`, 고정 모델에 `tf_frozen_model`, 세션 번들에 `tf_session_bundle`, Tensorflow Hub 모듈에 `tf_hub`, Keras HDF5에 `Keras`를 사용합니다. |
| --output_node_names | 쉼표로 구분된 출력 노드의 이름입니다. |
| --saved_model_tags | 저장된 모델 변환과 로드할 MetaGraphDef 의 태그에만 쉼표로 구분된 형식으로 적용됩니ㅏㄷ. 기본적으로 `serve`가 됩니다. |
| --signature_name | Tensorflow Hub 모듈 변환과 로드할 서명에만 적용됩니다. 기본 값은 `default`입니다. |

> [TF Hub 모듈의 공통 서명](https://www.tensorflow.org/hub/common_signatures/)

보다 자세한 명령어를 확인하려면 도움말을 쉘에서 확인할 수 있습니다.

```sh
tensorflowjs_converter --help
```

위의 변환 스크립트는 두 가지 유형의 파일을 생성합니다.

- model.json: 데이터 흐름 그래프 및 가중치 매니페스트
- group1-shard\*of\* : 바이너리 가중치 파일 모음

#### 2단계: 브라우저에서 로드하기 및 실행하기

1. tfjs-converter npm 패키지 설치하기

```
npm install @tensorflow/tfjs
```

2. FrozenModel 클래스를 인스턴스화하고 추론을 실행합니다.

```js
import * as tf from '@tensorflow/tfjs';
import { loadGraphModel } from '@tensorflow/tfjs-converter';

const MODEL_URL = 'model_directory/model.json';

const model = await loadGraphModel(MODEL_URL);
const cat = documenet.getElementById('cat');

model.execute(tf.browser.fromPixels(cat));
```

`loadGraphModel API`는 요청과 함께 자격 증명 또는 사용자 정의 헤더를 보내는 데 사용할 수 있는 추가 `LoadOptions` 매개 변수를 허용합니다.

> [tf.loadGraphModel(modelUrl, options?)](https://js.tensorflow.org/api/1.0.0/?hl=ko&_gl=1*1efy7hq*_ga*MTgwMDE4MTczMC4xNjkzNDQ0MjE0*_ga_W0YLR4190T*MTY5MzQ0NDIxNC4xLjEuMTY5MzQ0NTM1NC4wLjAuMA..#loadGraphModel)

#### 지원되는 연산

현재 Tensorflow.js 는 제한된 집합의 Tensorflow 연산만을 지원합니다. 모델이 지원되지 않는 연산을 사용하는 경우 `tensorflowjs_converter` 스크립트가 실패하고 모델에서 지원되지 않는 연산 목록을 출력합니다.

#### 가중치만 로드하기

가중치만 로드하려는 경우 다음 코드 조각을 사용할 수 있습니다.

```js
import * as tf from '@tensorflow/tfjs';

const weightManifestUrl = "https://example.org/model/weights_manifest.json";

const manifest = await fetch(weightManifestUrl);

this.weightManifest = await manifest.json();
const weightMap = await tf.io.loadWeights(this.weightManifest, "https://example.org/model");
```
