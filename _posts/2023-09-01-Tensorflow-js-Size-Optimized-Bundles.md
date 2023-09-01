---
layout: post
section: "Tensorflow.js"
title: "Tensorflow.js Size Optimized Bundles"
description: ""
cover: /assets/images/posts/2023-09-01-Tensorflow-js-Size-Optimized-Bundles.png
date: "2023/09/01 11:33:00"
categories: posts
tags: ["Tensorflow.js", "Tutorial", "Size Optimize Bundles"]
---

### Tensorflow.js 로 번들 크기 최적화

Tensorflow.js 3.0 은 크기에 최적화된 프로덕션 지향 브러우저 번들 빌드를 지원합니다. 브라우저에서 사용하기 위해서 더 작은 크기의 자바스크립트 코드로 최적화하는 것입니다. 이 기능은 특히 페이로드에 바이트를 줄이는 데 도움이 되는 프로덕션을 만들기 위한 것입니다. 이 기능을 사용하려면 ES Modules, Webpack 또는 Rollup 과 같은 자바스크립트 번들링 도구와 Tree-shaking/dead-code 제거와 같은 개념을 미리 알고 있어야 합니다.

#### 용어

ES Module - 표준 자바스크립트 모듈 시스템입니다. ES6/ES2015에서 도입된 import & export 문들 사용하여 식별할 수 있습니다.

번들링 - 자바스크립트 코드들을 가지고 와서 브라우저에서 사용할 수 있는 하나 이상의 자바스크립트 코드로 그룹화/번들링합니다. 번들링은 일반적으로 브라우저에 제공되는 최종 코드를 생성하는 단계입니다. 응용 프로그램은 일반적으로 트랜스파일된 라이브러리 소스에서 직접 번들링을 수행합니다. 일반적으로 번들러에서 롤업 및 웹팩이 포함됩니다. 번들링의 최정 결과는 번들로 알려져 있습니다. (또는 여러 부분으로 분할되는 경우 정크라고도 합니다.)

Tree-shaking / Dead code 제거 - 최종 작성된 응용 프로그램에서 사용하지 않는 코드를 제거합니다.

연산 - 하나 이상의 텐서를 출력으로 생성하는 하나 이상의 텐서에 대한 수학 연산입니다. 작업은 '고수준' 코드이며 다른 작업을 사용하여 논리를 정의할 수 있습니다.

커널 - 특정 하드웨어 기능에 연결된 작업의 특정 구현입니다. 커널은 '낮은 수준'이며 백엔드에 따라 다릅니다. 일부 작업에는 작업에서 커널로 일대일 매핑이 있는 반면 다른 작업에서는 여러 커널을 사용합니다.

#### 맞춤형 빌드에 대한 접근 방식

자바스크립티 모듈 시스템(ESM)을 최대한 활용하고 Tensorflow.js 사용자가 동일한 작업을 수행할 수 있도록 합니다. Tensorflow.js를 기존 번들러에서 가능한 한 Tree-shakeable 로 만듭니다. 이를 통해 사용자는 코드 분할과 같은 기능을 포함하여 이러한 번들러의 모든 기능을 활용할 수 있습니다. 번들 크기에 민감하지 않은 사용자를 위해 사용 편의성을 유지합니다. 이는 라이브러리의 많은 기본값이 크기 최적화된 빌드보다 사용자 용이성을 지원하기 때문에 프로덕션 빌드에 더 많은 노력이 필요하다는 것을 의미합니다. 워크플로우의 주요 목표는 최적화하려는 프로그램에 필요한 기능만 포함하는 Tensorflow.js 용 맞춤형 자바스크립트 모듈을 생성하는 것입니다. 또한 사용자 코드에서 모듈 시스템을 통해 지정하기 쉽지 않은 부분을 처리하기 위해 사용자 CLI 도구도 제공합니다.

- model.json 파일에 저장된 모델 사양
- 우리가 사용하는 백엔드 특정 커널 디스패치 시스탬에 대한 연산

#### 크기에 최적화된 사용자 정의 번들을 만드는 방법

##### 1단계: 프로그램에서 사용 중인 커널 확인

이 단계를 통해 실행하는 모든 모델에서 사용하는 모든 커널 또는 백엔드에서 제공하는 코드를 사전/사후 처리하는지 확인할 수 있습니다.
`tf.profile`을 사용하여 Tensorflow.js를 사용하는 애플리케이션 부분을 실행하고 커널을 가져옵니다.

```js
const profileInfo = await tf.profile(() => {
    // You must profile all uses of tf symbols.
    runAllMyTfjsCode();
});
const kernelNames = profileInfo.kernelNames;
console.log(kernelNames);
```

다음 단계를 위해 해당 커널 목록을 클립보드에 복사합니다. 사용자 지정 번들에서 사용하려는 것과 동일한 백엔드를 사용하여 코드를 프로파일링해야 합니다. 모델이 변경되거나 사전/사후 처리 코드가 변경되면 이 단계를 반복해야 합니다.

##### 2단계: 사용자 정의 tfjs 모듈에 대한 구성 파일 작성

```json
{
  "kernels": ["Reshape", "_FusedMatMul", "Identity"],
  "backends": [
      "cpu"
  ],
  "models": [
      "./model/model.json"
  ],
  "outputPath": "./custom_tfjs",
  "forwardModeOnly": true
}
```

| 설정 | 설명 |
| --- | ---- |
| kernels | 번들에 포함할 커널 목록입니다. 1단계의 출력에서 이것을 복사합니다. |
| backends | 포함하려는 백엔드 목록입니다. 유효한 옵션은 "cpu", "weggl", 및 "wasm"입니다. |
| model | 어플리케이션에서 로드하는 모델의 `model.json` 파일 목록입니다. 프로그램이 `tfjs_converter`를 사용하여 그래프 모델을 로드하지 않는 경우 비어 있을 수 있습니다. |
| outputPath | 생성된 모듈을 넣을 폴더의 경로입니다. |
| forwardModeOnly | 이전에 나열된 커널에 대한 그라디언트를 포함하려면 이를 `false`로 설정하십시오. |

##### 3단계: 사용자 지정 tfjs 모듈 생성

구성 파일을 인수로 사용하여 사용자 정의 빌드 도구를 실행하십시오. (`@tensorflow/tfjs` 패키지가 설치되어 있어야 합니다.)

```sh
npx tfjs-custom-module  --config custom_tfjs_config.json
```

##### 4단계: 번들러를 구성하여 `tfjs`의 별칭을 새 사용자 지정 모듈로 지정합니다.

`webpack` 및 `rollup`과 같은 번들러에서 `tfjs` 모듈에 대한 기존 참조를 별칭으로 지정하여 새로 생성된 사용자 지정 `tfjs` 모듈을 가리킬 수 있습니다. 번들 크기를 최대한 줄이려면 별칭을 지정해야 하는 세 가지 모듈이 있습니다.

웹팩

```js
...

config.resolve = {
  alias: {
    '@tensorflow/tfjs$':
        path.resolve(__dirname, './custom_tfjs/custom_tfjs.js'),
    '@tensorflow/tfjs-core$': path.resolve(
        __dirname, './custom_tfjs/custom_tfjs_core.js'),
    '@tensorflow/tfjs-core/dist/ops/ops_for_converter': path.resolve(
        __dirname, './custom_tfjs/custom_ops_for_converter.js'),
  }
}

...
```

##### 5단계: 번들 생성

번들러를 실행하여 번들을 생성합니다. 번들의 크기는 모듈 앨리어싱 없이 번들러를 실행할 때보다 작아야 합니다. 이와 같은 시각화 도구를 사용하여 최종 번들로 만든 요소를 확인할 수도 있습니다.
