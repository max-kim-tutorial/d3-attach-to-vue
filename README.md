# D3.js

Data Driven Document  
어렵어렵  

## 설명

Data Driven Document : 데이터는 우리가 가진 데이터, 문서는 HTML이나 SVG처럼 웹 브라우저에서 렌더링할 수 있는 웹 기반 문서. d3는 웹 문서를 데이터 중심(driven)으로 다룬다.

### 일반적인 작동 순서

- 브라우저 메모리로 데이터를 불러온다(Loading)
- 필요한 HTML 문서 요소를 새로 만들어서 데이터를 엮는다(Binding)
- 각 문서요소에 엮인 개별 데이터를 토대로 해당 문서요소를 변환시킨다 => 관련 시각적 프로퍼티를 지정한다.(Transforming)
- 사용자 입력에 대한 반응으로 문서요소의 상태를 한 값에서 다른 값으로 전이시킨다.(Transitioning)

### 메서드 체인

- D3는 좀 심하다 싶을 정도로 체인 문법을 이용한다.
- 코드 한줄에 여러 동작을 실행한다 => 함수형 지향하는 모습을 볼 수 있다
- 이거 어디 책에서 d3 만든 방식이 넘 좋다! 이런식으로 소개했는데 어디였지;;

## 예제들

### 1. select : 변화의 시작점

```js
var dataset = [ 5, 10, 15, 20, 25 ];

d3.select("body")          // 1 바디부터 먼저 선택 => obj
  .selectAll("p")          // 2 그 아래 p 요소 전부선택 => obj
  .data(dataset)           // 3 data를 선택한 selection 객체에 바인딩(빈 selection에 바인딩)
  .enter()                 // 4 대응하는 p요소가 없는 데이터에 대해 새로운 selection 반환 => obj
  .append("p")             // 5 만들어져 선택된 요소들에 대해 실제 요소 생성 => 실제요소 만듬
  .text("New paragraph!"); // 6 텍스트 넣는다 => 내용넣음
```

- 조작하고자 하는 요소를 선택한다. => 이 요소는 **처음에 존재하지 않을 수도 있다**
- 선택이 있든 없든간에 이 객체를 시작으로 다음 작업들이 실행된다
- selection 객체에 대해서 data()를 통해 특정 데이터를 바인드하고
- enter()과 exit을 통해 데이터에 대응하는 객체를 다룰 수 있는 기능을 제공
- enter는 selection에 바인드된 데이터 중에 아직 실제 문서 요소를 가지지 못하는 것들을 찾아내서 **가상의 객체**로 만들어 반환한다. 옵젝을 까보면 각각의 data요소들이 __data__이런식으로 바인딩되어이씀
- append는 selection obj를 바탕으로 이를 **실제요소**로 바꿔준다

#### 한번더 정리 + 반환값

- select : 조건과 일치하는 첫번째 문서요소의 참조(selection 객체)를 반환
- selectAll : selection 객체 배열 반환
- data : selection 객체에 __data__ 프로퍼티로 매핑한 새로운 selection 가상 객체 반환. groups는 empty
- enter : selection 객체는 맞는데 groups로 원래 data 바인딩할때 enter 프로퍼티에 있었던 배열이 옮겨가고 exit은 없어짐 => 실제 객체, 이때 selection 객체와 data를 비교하여 실제 elem을 가지지 못한 데이터들을 실제 객체(플레이스홀더의 참조값)로 변환
- append : 새롭게 생성한 문서요소(들)을 반환한다.

#### enter vs select

- enter : 바인드된 데이터 중에서 아직 실제 문서 요소를 가지지 못하는 것들**만** 찾아내서 가상의 객체로 만들어서 반환. 이미 요소가 존재할 경우에는 selectAll 같은 경우는 빈 selection이 아니라 이미 존재하는 요소가 선택된 상태가 됨. enter단에서 미리 존재하는 요소들은 무시됨
- 요소가 만들어진 이후에 문서 요소를 조작하기 위해서는 data만 조작하면 된다. => 문서 요소에 따라 다르게
- 관련 DOM요소보다 데이터 값이 많으면 enter가 문서요소를 생성

### 2. enter, update, exit : d3의 가장 특징적인 개념

![???뭐야이게](https://lumiamitie.github.io/images/post_image/d3_enter_update_exit/d3_enter_update_exit01.PNG)

```js
d3.select('svg')
  .selectAll('rect')
  // 요소가 아무것도 없는데 data는 있으니까
  // 그 개수만큼 append(rect) 실행됨 => enter는 여기까지 실행
  .data(dataset)
  .enter().append('rect')
  .attr()
  .attr()
```

- enter: 집합처럼 생각하셈. data만 있는 것들. 아직 그려지지 않은 것들. 그것들을 실제 요소들과 연결함. append 따위로 데이터의 개수만큼 요소를 주로 추가하게 됨
- update: `selectAll(circle).data(data)` 이미 그려졌고 data에도 있는 것들. 재활용을 하기 위해 쓰인다(가상돔스러움) 선택한 요소들의 attr값을 바꿔주는 느낌
- exit: 이미 그려졌는데 data에는 없는 것들=> 주로 삭제를 하게됨
- enter => update => exit

### 3. data bind : 데이터 매핑

- 시각화를 위한 데이터 === 텍스트 기반 데이터
- **데이터는 페이지의 문서요소와 엮여야만 한다**
- 데이터가 입력값이고 시각 요소의 속성이 출력값이다. 숫자가 크면 막대가 길어지고, 특별한 범주로 분류되면 색깔이 변하는 등 이런 매핑 규칙은 프로그래밍해야 한다.
- 데이터와 문서요소를 엮으면 그 데이터는 DOM에 존재하지 않고 문서요소의 `__data__`속성으로 메모리에 존재함. 엮여있는지 아닌지 판단하려면 일단 콘솔을 봐야하고, 꺼내쓰려면 콜백함수를 사용하든지 다른 조치가 필요하다.
- 넘긴 데이터 집합이 무엇이든 그 데이터 개수만큼 알아서 순회하고 메서드 체인으로 연결된 뒤쪽 메서드를 실행한다. 그러면서 메서드 안의 컨텍스트를 갱신. d는 순회하고 있는 지점의 개별 데이터를 항상 참조할 수 있다.
- **데이터는 시각화를 언제나 결정한다**

```js
// 데이터에 접근
text(d => d)
```

- 데이터를 매핑하기 위해서는 data와 selection이 필요하다
- json이나 CSV도 가능하다.

```js
svg.selectAll("circle")
    // 데이터 바인딩
    .data(data)
  // 여기서 마구 그려주기 시작
  .enter().append("circle")
    // 불러다가 사용 가능해짐
    .attr("cx", x)
    .attr("cy", y)
    .attr("r", 2.5);

var data = {
  'data1':[10,20,30,40,50,60,70,80,90,100],
  'data2':[50,55,60,65,70,75,80,85,90],
  'data3':[40,20,30,80,10,25,90,45],
  'data4':[5,100,15,70,35,90,25,75,45,50,100]
};

// 막대그래프를 그리는 함수	
function drawbar(bardata) {
  var bars = d3.select("svg").selectAll("rect").data(bardata);
  
  //enter
  //rect는 x, y, rx, ry, width, height
  bars.enter()
    .append("rect")
    // 콜백의 인자로 데이터에 접근해야함
    .attr("width", d => d)
    .attr("height", 15)
    .attr("x", 20)
    .attr("y", function(d, i){
      return 20 + i*20;
    });

  //update => 데이터에 반응성을 부여. enter나 exit 없이 그냥 실행문 있을때
  // data에 맵핑된 selection에서 data에 접근하려면 콜백함수를 이용한다
  bars.attr("width", function(d){return d;})

  //exit
  bars.exit().remove();
}
```

### 4. scale: domain(정의역)과 range(치역)을 매핑한 함수

- 척도를 사용하면 데이터 값을 만들려는 시각화에서 필요한 적절한 값으로 비율을 바꿔서 쉽게 매핑할 수 있음
- 척도는 사용자가 정의한 파라미터를 가지고 있는 함수로, 척도를 생성한 후에 데이터값을 넘겨서 호출하면 비율이 바뀐 출력값으로 반환한다.(일대일 함수, x -> y)
- 눈금은 척도의 시각적 표현인 축(axis)의 한 표현이다. 척도는 직접 시각적 결과물을 내지 않는 수학적 요소다. 축에 붙였을때 시각적 요소가 됨
- 척도는 엄청나게 큰 값들을 어떤 비율로 변환하여 표현하는 것을 도와준다.
- 몇개의 elem이 width에서 잘리는걸 방지해주기도 함

```js
// 정의역 지정 => 치역range지정 => 함수
var x = d3.scaleLinear()
    .domain([10, 130])
    .range([0, 960]);

x(20); // 80
x(50); // 320

var x = d3.scale.log()
    // 최대를 구하나봄
    .domain(d3.extent(numbers))
    .range([0, 720]);
```

- scale : mapping a dimension of abstract data from visual representation. 
  - scaleLinear : 분절되지 않은 연속적인 정의역, 선형 척도
  - scaleSqrt : 제곱근 분절 정의역
  - scaleDate : 날짜를 분절 정의역으로 사용
  - scaleOrdinal : 서열척도, 치역이 카테고리 이름 같이 측정 불가능한 값일때 사용하는 척도이다.
- domain : 정의역의 실질적인 범위, derives from data(d3.max, d3.min, extent의 api로 데이터에서 추출). 주로는 입력 가능한 데이터의 범위를 표현
- nice : 정의역을 깔끔한 반올림 값으로 시작하고 끝나도록 확장한다. 입력 정의역이 무엇이든 가장 가까운 반올림 값으로 양 끝을 확장시킨다. 
- range : 치역(반환값)의 범위, domain으로 넘긴 값에 대응되서 값이 나오도록 한다. 최종 반환값은 함수 => 이때 픽셀값, margin을 고려해서 값이 나오도록 한다. 차트의 좌표값으로 표현될 것이기 때무네. 화면에 출력하기 위한 데이터 값의 범위 
  - rangeRound : 척도의 모든 치역값이 반올림된다
- interpolate : 보간, 정의역에 대응하는 뭔가 치역을 갖다 붙이는 행위

#### 정규화

- 어떤 숫자 값을 최솟값과 최댓값이 0과 1이 되도록 매핑하는 과정
- 선형 척도는 D3가 수행하는 일종의 수학적 정규화 과정이라고 할 수 있다. 입력 값은 정의역을 기준으로 정규화되고 정규화된 값은 치역을 기준으로 비율이 변경된다.

#### 도메인 구하기 위해 사용할 수 있는 메소드

- extent : 배열에서 최대 최소값을 찾는다
- max : 배열에서 최대값을 찾는다
- min : 배열에서 최소값을 찾는다

### 5. axis: 척도의 시각적 표현

- 척도와 형태가 거의 유사하다. 정의한 파라미터를 가진 함수로, 함수를 호출하면 어떤 값을 반환하는 대신에 선과 라벨, 구분자를 가진 시각적 요소가 생성된다는 점에서 다르다.
- 축을 반환하는 함수는 SVG 표준을 따라서 SVG 관련 문서요소를 생성하고, 서열 척도를 제외한 측량 가능한 척도와 사용하기 위해 만들어졌다.
- 실제로 축을 선언해서 선과 라벨을 SVG안으로 넣으려면 만든 xAxis함수를 호출해야 한다.
- call : 인자로 넣은 함수를 호출하는 메소드. 메서드 체인 앞단의 선택물을 가져와서 함수로 건네준다. 주로 이제 축 만들때 append랑 같이 쓰는데, 모든 문서요소들을 한 그룹으로 묶고 call은 g를 xAxis 척도로 넘겨버린다. 
- 클래스를 할당해서 css로 바꾸는게 편하다

```js
const xAxis = d3.svg.axis().scale(xScale).orient("bottom")
//혹은
const xAxis = d3.axisBottom(xScale)
svg.append("g").call(xAxis)
```

- 2차원 차트의 축은 상하좌우다. 상하좌우에 해당하는 메소드들은
  - d3.axisTop(scale)
  - d3.axisBottom(scale)
  - d3.axisLeft(scale)
  - d3.axisRight(scale)
- 축함수는 range의 값을 **픽셀 크기**로 인식하여 그린다.
- translate로 움직여줘야 내가 바라는 곳에 위치할 가능성이 높다.

```js
const resizer = ()_=> {
  const h = document.getElementById('graph').offsetHeight;
  const yscale = d3.scaleLinear()
    .domain([0, 4955]) //실제값의 범위
    .range([h - 20, 0 + 20]); //변환할 값의 범위(역으로 처리했음!), 위아래 패딩 20을 줬다!

  d3.select('#yaxis')
    .attr('transform', 'translate(50, 0)') //살짝 오른쪽으로 밀고
    .call(d3.axisLeft(yscale)); //축함수를 넘기면 알아서 그려줌.
};

window.addEventListener('resize', resizer);
resizer();
```

- css는 다음과 같이 적용시키면 점선 축을 얻을 수 있다. svg의 스타일 요소들은 다음에 한번 보구
- 축만들면 그 안에는 svg로 path,line,text가 있나보다

```css
.axis path,
.axis line {
  fill:none;
  stroke:black;
  shape-rendering: crispEdge;
}
.axis text {
  font-family: sans-serif;
  font-size:11px;
}
```

### 6. ticks: 축의 구분자

```js
var x = d3.scale.linear()
    .domain([12, 24])
    .range([0, 720]);

const xAxis = d3.svg.axisBottom(x).ticks(5)
```

- 분절점, scale domain값 사이 적절하게 나눠서 ticks 인자만큼의 값을 **최대한** 집어넣는다(정의역 시작 + 분절점 + 정의역 끝)
- tick은 지멋대로 동작한다 : 딱 떨어지지 않는 수로 잘릴 수 있는 경우, 요청한 값보다 크거나 작더라도 간격을 설정해 가독성 좋은 숫자로 설정한다. === 눈금의 라벨을 읽기 쉽도록 보장한다
- ticks : 해당 스케일의 입력 정의역에서 대략 count수 만큼의 대표값들을 반환. 
- tickFormat : 출력용 구분자 값으로 사용할 적절한 number format을 반환. count는 구분값을 생성하기 위해 사용되는 count와 같은 값을 사용함. 스케일 내장 구분자 포매터. 눈금의 표현에 단위를 붙이거나 할때 유용하다.

### 7. attr/style : 직접 svg를 조작한다.

d3 api에 elem에 직접 접근해서 modify

```js
// 선택자를 그대로 사용할 수 있음
// svg elem은 그냥 다른 elem이랑 같은 취급
d3.selectAll("circle")
    .attr("cx", 20)
    .attr("cy", 12)
    .attr("r", 24)
    .style("fill", "red");
```

- select : elem 선택
- attr : svg/html attribute 설정
  - html : class(d3.classed), id, src, width, alt, height
  - svg(svg의 모든 프로퍼티도 속성으로 지정된다) : x, y, rx, ry, fill, stroke 등등
  - SVG는 돔 안에 존재하며 HTML 문서요소처럼 동작하기 때문에 두 메서드를 사용해서 SVG를 사용하기 좋다.
  - attr 인자에 객체로 넘겨도 좋다. `attr({x:0, y:0})`
  
  ```js
  const circles = svg.selectAll("circle")
    .data(dataset)
    .enter()
    .append("circle")
  
  circles.attr("cx", (d,i) => (i*50)+25)
    .attr("cy", h/2)
    .attr("r", d => d)
  ```
- style : svg style 설정 => css
- append: selectAll같은 elem[]에다가 추가

### 8. update: 시간에 따른 데이터의 갱신 처리

### 8. transition/motion: 효과 주기


## reference

- https://www.44bits.io/ko/post/d3js-basic-understanding-select-and-enter-api