---
layout: post
title: D3.js data, enter, exit 이해하기(1)
date: 2021-07-26 04:00:00 +0900
categories: [web, front-end]
tag: [javascript, js, es6, d3, d3.js, data, visualization]
toc: true
comments: true
image:
  src: /assets/img/d3logo.png
  alt: d3.js logo image
---


D3.js를 처음 쓰면 정말 쉽게 데이터를 다룰 수 있다는 것에 놀라고, 조금 더 쓰다보면 생각대로 쓰기 너무 어렵다는 것에 놀란다. 잘 쓰다가 움직이는 차트 만들려니 이렇게 어려운 함수였나 싶어 시행착오를 기록한다. 이 포스팅을 찾아 들어온 사람은 d3.js에 대한 기본적인 이해가 있을 것이라 가정한다.

<br>

용어 정의
* 엘리먼트: DOM을 구성하는 태그
* 노드: d3 셀렉션이 선택한 객체. 엘리먼트와 데이터로 구성되어 있다.

<br>

## 데이터 업데이트 함수
* data
* enter
* exit
* join (v6 이상)

<br>
<br>

## 예시 상황 설정
---

<br>

```javascript
/**
* @param data  맵핑할 배열 데이터
* @param key   데이터의 키 값
**/
selection.data([data[, key]])

selection.enter()

selection.exit()
```
예제를 통해 하나씩 이 함수의 작동 방법을 알아보자.

두 파일을 아래와 같이 만들고 브라우저로 실행하자.
```html
 <!-- index.html -->
<html>
<head>
    <script src="https://d3js.org/d3.v6.min.js"></script>
</head>
<body>
    <div id="container"></div>
    <script src="sample.js"></script>
</body>
</html>
```

```js
// example.js
let data0 = [1, 2, 3, 4]
let data1 = [10, 20, 30, 40]
let data2 = [1, 2, 3, 4, 5, 6]
let data3 = [1, 2]

let texts = svg.selectAll("p")
    .data(data1)
    .enter()
    .append("p")
    .text(d=>d)
```

브라우저 실행 모습

```
10
20
30
40
```

<br>

수 개의 엘리먼트가 있고, 각자 자신의 데이터를 갖고 있다. 데이터를 업데이트 한다는 것은 엘리먼트들의 데이터를 변경하고, 엘리먼트와 함께 데이터를 없애거나 추가하는 것을 의미한다.

<br>
<br>

### 데이터 크기가 변하지 않은 업데이트
---

<br>

example.js의 `texts` 셀렉션 객체는 div 엘리먼트를 선택하고 각각의 div 엘리먼트에는 데이터 `10, 20, 30, 40` 이 담겨있다. 아래 그림은 `data1` 배열에서 `data0` 배열로 div 엘리먼트 데이터를 업데이트한 과정을 보여준다.

<br>

![update_data0_text](/assets/img/update_data0_text.jpg){: .normal}

<br>

콘솔창을 보면 `texts` 객체에 접근해 업데이트하는 과정을 볼 수 있다.

<br>

엘리먼트를 개발자모드의 Properties탭에서 확인해보면 `__data__` 속성값만 바뀐 것을 볼 수 있다. `data()` 함수로 엘리먼트의 `__data__`를 업데이트 한 뒤  엘리먼트의 속성이나 값으로 적용하는 과정을 거쳐야 한다.

<br>

그래서 `texts` 객체의 데이터를 업데이트 하더라도, `texts.text(d=>d)` 명령을 실행할 때까지 DOM div 값이 변하지 않는다.

<br>

```javascript
texts.text(d=>d)
```

<br>

**data() 함수는 엘리먼트의 \_\_data\_\_ 속성에 값을 저장한다 것을 알았다.**

<br>
<br>

### 데이터 크기가 늘어나는 업데이트
---

<br>

데이터를 업데이트할 때 기존의 데이터 개수보다 많은 데이터를 사용하면 의도대로 동작하지 않는다. 처음 4개의 데이터가 있는 상태에서 6개의 데이터로 업데이트하면, 4개의 데이터만 업데이트되는 상황이다. 이전 코드가 남아있는게 아니다.

<br>

![update_data2_enter1](/assets/img/update_data2_enter1.jpg){: .normal}

<br>

DOM의 처음부터 존재하던 div 엘리먼트는 `data2` 으로 업데이트 됐지만, 데이터 개수만큼 div 엘리먼트가 생기는 것은 아니었다. 콘솔창 3번째 명령줄을 보면 `_groups` 속성값에 div 4개뿐이 없다는 것을 알 수 있다(`_groups`는 셀렉션에 의해 선택된 노드, 여기선 `texts` 객체가 선택한 노드와 같다). 왜 데이터 개수만큼 생기지 않았을까?

<br>

콘솔창 네 번째 명령 `texts.data(data2)` 실행 결과 객체는 `texts` 객체와 달리 `_enter`, `_exit` 속성을 포함한다. `_enter`, `_exit` 속성은 `enter()`, `exit()` 함수로 접근할 수 있다. 우리가 알아볼 데이터 업데이트와 이 속성들이 무슨 상관인지 알 수 있는 대목이다.

<br>

![update_data2_enter2](/assets/img/update_data2_enter2.jpg){: .normal}

<br>

`texts.data(data2).enter()` 명령의 의미는, `texts` 객체의 데이터를 `data2` 으로 업데이트하고 새로 맵핑된 노드가 포함된 배열을 반환하라는 뜻이다.

<br>

```js
// [10, 20, 30, 40]          업데이트 전
// [ 1,  2,  3,  4,  5,  6]  업데이트 후

texts.data(data2).enter()
// { _groups: [empty, empty, empty, empty, Ot, Ot]}
```

<br>

`_groups` 속성의 배열 마지막 뒤 두개에 의미 심장한 객체 `Ot` 두 개 있다. 이는 업데이트할 때 데이터가 엘리먼트 보다 많아 맵핑되지 못하고 남은 데이터만 담겨있는 노드다. 이 노드에 엘리먼트를 추가해주는 작업이 필요하다. 즉, **기존의 데이터 보다 더 많은 데이터로 업데이트할 경우 초과한 데이터 개수만큼의 노드를 enter() 함수로 얻을 수 있다.**

<br>

```js
texts.data(data2).enter().append("div").text(d=>d)
```

<br>
<br>

**주의** : `data()` 함수 호출은 셀렉션의 데이터를 변경 후 객체를 반환한다. 반환하는 이 객체는 `_enter`, `_exit` 속성을 포함하는 객체로 일반 셀렉션 객체와 차이가 있다. `_enter`, `_exit` 속성은 **`data()` 함수의 반환 결과에만 존재하는 것이다.**

<br>

```js
let textsArr = texts.data(data2);
textsArr.enter();
// {_groups: [empty, empty, empty, empty, Ot, Ot]}
```
```js
texts.data(data2);
texts.enter();     
// {_groups: [empty, empty, empty, empty]}
// 새로 추가된 노드를 선택할 수 없다
```

<br>
<br>

### 데이터 크기가 줄어드는 업데이트
---
<br>

이전 글에서 `exit()` 함수에 대한 언급이 없이 지나갔는데, 이 함수가 데이터 크기가 줄어드는 업데이트를 할 때 사용한다. 실행 예시부터 보자.

![update_data3_exit2](/assets/img/update_data3_exit2.jpg){: .normal}

<br>

위 이미지처럼 콘솔창처럼 data를 업데이트하고 엘리먼트에 적용해보자. 엘리먼트가 업데이트되긴 했지만, 새로 업데이트 된 데이터 개수의 엘리먼트만 바뀌고 나머지 엘리먼트는 그대로 있다. 우리는 데이터 개수만큼만 엘리먼트가 존재하길 바란다. 이때 `exit()` 함수를 사용한다.

`texts.data(data3).exit()` 명령으로 `_exit` 속성을 얻을 수 있다. `_exit` 속성은 새로 업데이트 된 데이터 중 맵핑되지 못한 엘리먼트를 갖고 있다.

<br>

```javascript
let r = texts.data(data3).exit();
// { _exit: [empty, empty, div, div]}

r.remove();
```

<br>

새 데이터에 맵핑되지 못한 div 두 엘리먼트를 제거하면 데이터 개수만큼 엘리먼트가 남게 된다.

<br>
<br>

### 특정 데이터만 업데이트하는 방법
---

<br>

```javascript
selection.data([data[, key]])
```

여기까지 오면서 `data()` 함수의 파라미터 `key` 를 한 번도 사용하지 않았다. `key`는 데이터의 불변성을 갖도록 하는데, 함수를 인자로 받는다. 아래 예시를 통해 어떻게 활용하는지 확인해보자.

<br>

아래의 코드(case1)는 4개의 데이터를 막대 그래프로 표현하는 기능을 한다. add 버튼을 누르면 `data`의 첫 번째 데이터를 버리고 마지막에 데이터를 삽입하는 원형큐처럼 동작한다. 막대 그래프는 데이터가 업데이트 될 때마다 함께 업데이트 된다.

```javascript
// case 1
// sample.js

let data = [10, 20, 30, 40]

let svg = d3.select("#container")
    .append("svg")
        .attr("width", 500)
        .attr("height", 300);

let bars = svg.selectAll("rect")
    .data(data)
    .enter()
    .append("rect")
    .attr("width", d=>d*10)
    .attr("height", 25)
    .attr("y", (d,i)=>i*30);

// Button for adding data
d3.select('#container').append("button")
.style("width", "80px")
.style("height", "50px")
.text("add")
.on('click', update)

// update
function update(){
    // Push NEW data, drop OLD data
    data.shift();
    data.push(Math.random()*50);

    // Select all rect and Update data
    let updateRect = svg.selectAll("rect")
        .data(data);

    // Select NEW node and create NEW element
    updateRect
    .enter()
    .append("rect")
    .attr("width", d=>d*10)
    .attr("y", (d,i)=>(i+1)*30)
    .transition()
    .attr("y", (d,i)=>i*30)
    .attr("height", 25);

    // Select 
    updateRect
    .transition()
    .attr("width", d=>d*10)
    .attr("height", 25)
    .attr("y", (d,i)=>i*30);

    updateRect
    .exit().remove();
}
```

<br>
<br>


아래의 코드(case2)도 위의 코드(case1)와 마찬가지로 add 버튼을 누를 때마다 데이터를 업데이트하고, 막대 그래프도 같이 업데이트 된다. 둘 다 실행해봤드면 차이점이 확연히 드러날 것이다.
```js
// case 2
// sample.js

// data with id
let data = [
    {id:1, value:10},
    {id:2, value:20},
    {id:3, value:30},
    {id:4, value:40},
]

let svg = d3.select("#container")
    .append("svg")
        .attr("width", 500)
        .attr("height", 300);

// bar
let bars = svg.selectAll("rect")
    .data(data, d=>d.id)
    .enter()
    .append("rect")
    .attr("width", d=>d.value*10)
    .attr("height", 25)
    .attr("y", (d,i)=>i*30);

// Button for adding data
d3.select('#container').append("button")
.style("width", "80px")
.style("height", "50px")
.text("add")
.on('click', update)

// update
function update(){
    // Push NEW data, drop OLD data
    data.shift();
    data.push({id:Date.now(), value:Math.random()*50});

    // Select all rect and Update data
    let updateRect = svg.selectAll("rect")
        .data(data, d=>d.id);

    // Select NEW node and create NEW element
    updateRect
    .enter()
    .append("rect")
    .attr("width", d=>d.value*10)
    .attr("y", (d,i)=>(i+1)*30)
    .transition()
    .attr("y", (d,i)=>i*30)
    .attr("height", 25);

    // Select 
    updateRect
    .transition()
    .attr("width", d=>d.value*10)
    .attr("height", 25)
    .attr("y", (d,i)=>i*30);

    updateRect
    .exit().remove();
}
```

case1 코드는 `data` 값이 업데이트 될때마다, 막대 그래프의 폭만 바뀐다. 반면 case2 코드는 아래에서 새로운 막대 그래프가 올라오고 이전의 막대 그래프는 사라진다. 
 
우리는 `data` 배열에서 가장 오래된 데이터를 버리고 최신 데이터를 추가하는 방식으로 조작하고 있다. 그러니 데이터가 업데이트 될 때마다 오래된 막대가 사라지고, 새로운 막대가 추가되는 것처럼 보이는게 시각적으로 더 좋을 것이다. 이 때 사용되는 것이 `data`에 `key`를 부여하는 것이다.


... 작성중