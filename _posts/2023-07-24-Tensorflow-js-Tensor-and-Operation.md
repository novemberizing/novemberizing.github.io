---
layout: post
section: "Tensorflow.js"
title: "Tensorflow.js Tensor and Operation"
# cover: /assets/images/posts/2022-01-08-Decentralization.png
date: "2023/07/24 10:41:00"
categories: posts
tags: ["Tensorflow.js", "Tensor", "Operation"]
---

#### 텐서와 연산

'Tensorflow.js'는 'Javascript'에서 텐서를 사용하여 계산을 정의하고 실행하는 프레임워크입니다. 텐서는 벡터와 행렬을 더 높은 차원으로 일반화한 것입니다.

<!-- TODO: UPDATE TENSOR INFO -->

##### 텐서

'Tensorflow.js'에서 데이터의 중심 단위는 `tf.Tensor`이고 하나 이상의 차원 배열로 형성된 값의 집합입니다. `tf.Tensor`는 다차원 배열과 매우 유사합니다.

__`tf.Tensor` 의 속성__

| 속성 | 설명 |
| ---- | ---- |
| rank | 텐서에 포함된 차원 수를 정의 |
| shape | 데이터의 각 차원의 크기를 정의 |
| dtype | 텐서의 데이터 유형을 정의 |

> 참고: 차원이라는 용어는 순위와 같은 의미로 사용됩니다. 머신러닝에서 텐서의 차원은 때때로 특정 차원으 크기를 나타낼 수 있습니다. (예: 형상 행렬 [10, 5]는 랭크 2텐서 또는 2차원 텐서입니다. 첫 번째 차원은 10입니다.)

`tf.tensor()` 메서드를 사용하여 배열에서 `tf.Tensor`를 만들 수 있습니다.

```js
// Create a rank-2 tensor (matrix) matrix tensor from a multidimensional array.
const a = tf.tensor([[1, 2], [3, 4]]);
console.log('shape:', a.shape);
a.print()

// Or you can create a tensor from a flat array and specify a shape.
const shape = [2, 2];
const b = tf.tensor([1, 2, 3, 4], shape);
console.log('shape:', b.shape);
b.print();
```

<!-- TODO: EXAMPLE CODE AND RUN -->

기본적으로 `tf.Tensor`는 `float32` `dtype`이며, `tf.Tensor`는 `bool`, `int32`, `complex64` 및 `string` `dtype`으로 만들 수 있습니다.

```js
const a = tf.Tensor([[1, 2], [3, 4]], [2, 2], 'int32');
console.log('shape:', a.shape);
console.log('dtype', a.dtype);
a.print();
```

'Tensorflow.js'는 또한 무작위 텐서, 특정 값으로 채워진 텐서, `HTMLImageElement`의 텐서 및 텐서를 만드는 일련의 편의 메서드를 제공합니다.

> __Tensors / Creation__
>
> We have utility functions for common cases like Scalar, 1D, 2D, 3D and 4D tensors, as well a number of functions to initialize tensors in ways usefull for machine learning.
>
> `tf.tensor (values, shape?, dtype?)`
> 
> Creates a `tf.Tensor` with the provided values, shape and dtype.
>
> [Tensors / Creation](https://js.tensorflow.org/api/latest/?hl=ko&_gl=1*gwost2*_ga*NTIwMTkwMjg4LjE2ODgwMTY5NDQ.*_ga_W0YLR4190T*MTY5MDE2Mjc5OC40LjEuMTY5MDE2MzA4NS4wLjAuMA..#Tensors-Creation)

#### 텐서의 형상 변경하기

`tf.Tensor`의 요소 수는 형상에 있는 크기의 곱입니다. 종종 같은 크기의 여러 형상이 있을 수 있기 때문에 `tf.Tensor`를 같은 크기의 다른 형상으로 재구성할 수 있으면 유용합니다. 재구성은 `reshape()` 메서드를 사용하여 가능합니다.

```js
const a = tf.tensor([[1, 2], [3, 4]]);
console.log('a shape:', a.shape);
a.print();

const b = a.reshape([4, 1]);
console.log('b shape:', b.shape);
b.print();
```

##### 텐서에서 값 가져오기

`Tensor.array()` 또는 `Tensor.data()` 메서드를 사용하여 `tf.Tensor()`에서 값을 가져올 수도 있습니다.

```js
const a = tf.tensor([[1, 2], [3, 4]]);
// Returns the multi-dimensional array of values.
a.array()
 .then(array => console.log(array));
// Returns the flattened data that backs the tensor
a.data()
 .then(data => console.log(data));
```

또한 사용하기 더 간단하지만 애플리케이션에서 성능 문제를 일으킬 수도 있는 메서드의 동기 버전도 제공합니다. 운영 애플리케이션에서는 항상 비동기 메서드를 선호해야 합니다.

```js
const a = tf.tensor([[1, 2], [3, 4]]);
// Returns the multi dimenstional rray of values.
console.log(a.arraySync());
// Returns the flattened data that backs the tensor.
console.log(a.dataSync());
```

#### 연산

텐서는 데이터를 저장할 수 있지만 연산을 사용하면 해당 데이터를 조작할 수 있습니다. 'Tensorflow.js'는 또한 텐서에서 수행될 수 있는 선형 대수 및 머신러닝에 적합한 다양한 연산을 제공합니다.

1. tf.Tensor의 모든 요소 x2 계산하기

```js
const x = tx.tensor([1, 2, 3, 4]);
const y = x.square(); // equivalent to tf.square(x)
y.print();
```

2. 요소별로 두 개의 tf.Tensor 요소 추가하기

```js
const a = tf.tensor([1, 2, 3, 4]);
const b = tf.tensor([10, 20, 30, 40]);
const y = a.add(b); // equivalent to tf.add(a, b)
y.print();
```

텐서는 변경이 불가능하기 때문에 이러한 연산은 값을 변경하지 않습니다. 대신 연산 반환은 항상 새 `tf.Tensor`를 반환합니다.

> 참고: 대부분의 연산은 `tf.Tensor`를 반환하지만 결과는 실제로 아직 준비되지 않았을 수 있습니다. 즉, 반환되는 `tf.Tensor`가 실제로 계산에 대한 핸들임을 의미합니다. `Tensor.data()` 또는 `Tensor.array()`를 호출하면 이러한 메서드는 계산이 완료된 경우에만 값으로 해결되는 `Promise`를 반환합니다. 'UI' 컨텍스트(예: 브라우저 앱)에서 실행하는 경우 계산이 완료될 때까지 UI 스레드를 차단하지 않도록 동기식 메서드 대신 이러한 메서드의 비동기 버전을 항상 선호해야 합니다.

['Tensorflow.js'가 지원하는 연산 목록](https://js.tensorflow.org/api/latest/?hl=ko&_gl=1*jiux9*_ga*NTIwMTkwMjg4LjE2ODgwMTY5NDQ.*_ga_W0YLR4190T*MTY5MDE2Mjc5OC40LjEuMTY5MDE2MzA4NS4wLjAuMA..#Operations)

#### 메모리

'WebGL' 백엔드를 사용할 때 `tf.Tensor` 메모리는 반드시 명시적으로 관리해야 합니다. 해제되는 메모리를 위해 `tf.Tensor`가 범위를 벗어나기에 충분하지 않습니다.

`tf.Tensor`의 메모리를 삭제하려면 `dispose()` 메서드 또는 `tf.dispose()`를 사용할 수 있습니다.

```js
const a = tf.tensor([[1, 2], [3, 4]]);
a.dispose();  // equivalent to tf.dispose(a)
```

애플리케이션에서 여러 연산을 함께 연결하는 것은 매우 일반적입니다. 이를 처리하기 위해 모든 중간 변수에 관한 참조를 유지하면 코드 가독성이 떨어질 수 있습니다. 해당 문제를 해결하기 위해 'Tensorflow.js'는 함수 실행 후 함수가 반환하지 않는 모든 `tf.Tensor`를 정리하는 `tf.tidy()` 메서드를 제공합니다. 이는 함수가 실행될 때 지역 변수가 정리되는 방식과 유사합니다.

```js
const a = tf.tensor([[1, 2,], [3, 4]]);
const y = tf.tidy(() => {
  const result = a.square().log().neg();
  return result;
});
```

이 예제에서 `square()` 및 `log()`의 결과는 자동으로 삭제됩니다. `neg()`의 결과느 `tf.tidy()`의 반환 값이므로 삭제되지 않습니다.

'Tensorflow.js'에서 추적하는 텐서 수를 가져올 수도 있습니다.

```js
console.log(tf.memory());
```

`tf.memory()`로 출력된 객체는 현재 할당된 메모리 양에 대한 정보를 포함합니다.

> `tf.memory()` <sub>function</sub>
>
> Returns memory into at the current time in the program. The result is an object with the following properties:
>
> - numBytes: Number of bytes allocated (undisposed) at this time.
> - numTensors: Number of unique tensors allocated.
> - numDataBuffers: Number of unique data buffers allocated (undisposed) at this time, which is ≤ the number of tensors (e.g. `a.reshape(newShape)` makes a new Tensor that shares the same data buffer with a).
> - unreliable: True if the memory usage is unreliable. See reasons when unreliable is true.
> - reasons: `string[]`, reasons why the memory is unreliable, present if unreliable is true.
> 
> WebGL Properties
>
> - numBytesInGPU: Number of bytes allocated (undisposed) in the GPU only at this time.
>
> Returns: MemoryInfo
>
> [tf.memory()](https://js.tensorflow.org/api/latest/?hl=ko&_gl=1*1ruqudm*_ga*NTIwMTkwMjg4LjE2ODgwMTY5NDQ.*_ga_W0YLR4190T*MTY5MDE2Mjc5OC40LjEuMTY5MDE2MzA4NS4wLjAuMA..#memory)
