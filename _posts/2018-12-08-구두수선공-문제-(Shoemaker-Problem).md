---
layout: post
title: 구두수선공 문제 (Shoemaker Problem)
feature-img: "assets/img/picture/shoemaker.jpg"
thumbnail: "assets/img/picture/shoemaker.jpg"
tags: [알고리즘, Algorithm, Javascript]
excerpt_separator: <!--more-->
---

# 문제
어떤 구두 수선공에게  N개의 주문이 들어와 있다. 그는 하루에 한 가지 작업만 할 수 있으며 보통 한 작업을 하는데 며칠이 걸린다. i 번째 작업에 대해  Ti라는 정수는 구두 수선공이 작업을 끝내는데 걸리는 시간이 며칠인지를 나타낸다. 하지만 인기가 좋으면 대가가 따른다. 구두 수선공은  i 번째 작업을 시작하기 전까지 지연되는 날 수를 기준으로 하루에  Si 달러의 벌금을 내야 한다. 벌금의 합계가 최소가 되는 작업 순서를 계산하는 알고리즘을 설명하라.
<!--more-->
# 입출력 예제
## 입력
N: 작업 갯수, Ti: 작업 시간(일), Si: 일 시작전 까지 하루당 벌금($)
```
4
3 4
1 1000
2 2
5 5
```

## 출력
```
2 1 3 4
```
두번째 작업을 가장 먼저 수행한다.

# 풀이
1. 입력받은 T, S 배열을 `works`라는 배열에 오브젝트로 변환해서 저장한다. 오브젝트로 변환할 때 작업순서를 나타내는 필드를 추가하는 것이 중요하다. 이후 정렬을 마치면 작업 순서를 알 수 없기 때문이다.
2. 버블정렬로 인접한 두 작업의 `T*S` 값을 비교해서 `T*S`값이 큰 작업을 뒤로 보낸다. 뒤로 보낸다는 것은 작업 순서를 뒤로 미룬다는 것을 의미한다.
3. `works` 배열을 루프 돌면서 작업순서를 출력한다.

# 코드
```js
const main = (n, t, s) => {
  let works = [];

  // t,s를 오브젝트로 변환후 배열에 삽입
  for (let i=0; i<n; i++) {
    works.push({
      number: i+1,
      due: t[i],
      panelty: s[i]
    });
  }

  for (let i=0; i<n-1; i++) {
    for (let j=0; j<n-1; j++) {
      const iValue = works[i].due * works[i].panelty;
      const jValue = works[j].due * works[j].panelty;

      if (iValue > jValue) {
        const temp = works[i];
        works[i] = works[j];
        works[j] = temp;
      }
    }
  }

  console.log(works.map(work => work.number).join(' ')); // => 2 1 3 4
};

main(4, [3, 1, 2, 5], [4, 1000, 2, 5]);
```
