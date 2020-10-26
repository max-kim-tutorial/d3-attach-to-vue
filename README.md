# D3.js

Data Driven Document  
어렵어렵  

## 이 문서가 생긴 이유

- 회사에서 원격주문 백오피스 대시보드 만들고있는데 백엔드에서 받은 데이터를 그래프로 표현해야 할 일이 생겼다.
- 프로젝트 데드라인이 좀 빠듯한 편이고 백엔드 API도 거의 나왔지만 개인적으로 d3와 svg를 써보고 싶은 마음에 리서치를 좀 해보겠다고 사수한테 말했다. d3를 쓸지 좀 더 조작이 쉬운 Vue 생태계의 데이터 시각화 라이브러리를 쓸지 판단도 해보겠다고 했다.
- 5개월쯤 전에 React로 [데이터 시각화 웹사이트](https://github.com/MaxKim-J/HUFS-Second-Major-Visualize)를 만든적이 있는데, 이때도 d3를 적용해서 완벽 커스텀이 가능한 데이터 시각화를 구현하고 싶었지만 복잡한데다가 SVG를 만져본적도 없었고 기말고사가 곧이어서 포기하고 d3를 감싼 React 생태계의 데이터 시각화 라이브러리인 [Recharts](https://github.com/recharts/recharts)를 사용했었다.
- 지금은 회사 실무에서 SVG도 꽤 만져봤고 오랜만에 새로운 기술을 공부할 기회라고 생각해서... d3에 대한 공부 내용과 프로젝트 적용 여부를 여기에다가 정리해본다.
- 여기다가 정리한 후에 d3 빨리 배우기 이런식으로 블로그에 글 써봐도 좋을 것 같당.

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
  - scaleOrdinal : 서열척도, 치역이 카테고리 이름 같이 측정 불가능한 값일때 사용하는 척도이다. 서열척도는 정량적 척도처럼 연속적인 치역을 반환하지 않고, 분절된 이산범위를 사용한다. 분절된 이산범위란 치역이 사전에 결정되어 있고 숫자가 아닐 수도 있는 범위를 의미한다.
- domain : 정의역의 실질적인 범위, derives from data(d3.max, d3.min, extent의 api로 데이터에서 추출). 주로는 입력 가능한 데이터의 범위를 표현
- range : 치역(반환값)의 범위, domain으로 넘긴 값에 대응되서 값이 나오도록 한다. 최종 반환값은 함수 => 이때 픽셀값, margin을 고려해서 값이 나오도록 한다. 차트의 좌표값으로 표현될 것이기 때무네. 화면에 출력하기 위한 데이터 값의 범위 
  - rangeRound() : 척도의 모든 치역값이 반올림된다
  - rangeBand() : 치역의 양 끝 점을 전달 인자로 받아서 정의역의 개수를 기준으로 그 수만큼 청크 혹은 대역으로 조각낸다. 약간 tick이랑 역할이 비슷함. 두 번째 인자로 0과 1 사이의 소수를 전달하여 간격을 조절할 수 있다(백분율)
  - rangeRoundBands() : 더 균등하게 배분 가능함. 치역이 가장 가까운 정수 픽셀 값으로 반올림. 저거 위에 두개 짬뽕(v4에서 deprecate됨. rangeBand랑 padding을 같이 써야함)
- nice : 정의역을 깔끔한 반올림 값으로 시작하고 끝나도록 확장한다. 입력 정의역이 무엇이든 가장 가까운 반올림 값으로 양 끝을 확장시킨다. 
- interpolate : 보간, 정의역에 대응하는 뭔가 치역을 갖다 붙이는 행위

#### 정규화

- 어떤 숫자 값을 최솟값과 최댓값이 0과 1이 되도록 매핑하는 과정
- 선형 척도는 D3가 수행하는 일종의 수학적 정규화 과정이라고 할 수 있다. 입력 값은 정의역을 기준으로 정규화되고 정규화된 값은 치역을 기준으로 비율이 변경된다.

#### 도메인 구하기 위해 사용할 수 있는 메소드

- extent : 배열에서 최대 최소값을 찾는다
- max : 배열에서 최대값을 찾는다
- min : 배열에서 최소값을 찾는다
- d3.range : 최대값을 주면 등차수열 배열 리턴
- padding : 치역 메소드의 끝에 붙는데, 대역 간격을 줄일 수 있는 매소드다. 

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

### 8. length가 유지되는 dataSet 갱신 : update 활용

- 실제 대부분의 데이터는 시간에 따라서 변화하며, 시각화에 이런 변화를 반영되어야 한다.
- 가장 쉽게 생각할 수 있는 시나리오
  - 데이터셋 값 변경
  - 새 값을 이미 존재하는 SVG 문서요소에 반영(값 덮어쓰기)
  - 차트 출력을 갱신하는데 필요한 새 속성값 지정
- draw 메서드를 호출하면 순식간에 svg를 그려버리기 때문에 갱신 관련 코드를 다른 코드와 분리하는게 좋고, 페이지를 불러온 후에 갱신을 하기 위해 어떤 시발점이 필요하다.(Vue에서는 부모 컴넌에서 prop dataSet을 바꿔줄 것이다.)

```js
  svg.selectAll('rect')
    // 이미 있는 selection에서 데이터 바인딩 다시한번
    .data(dataSet)
    // 이미 있으니까 append할 필요 없구
    // attr는 변화시킨 데이터에 맞게 변화를 시켜준다
    .attr("x", (d,i) => xScale(i))
    .attr("y", (d) => height - d)
    .attr("height", d => d)
    .attr("width", 20)
    .attr('fill', 'green')
```

- 이미 있다고 생각하고 + 데이터셋이 바뀌면 어떤 속성이 따라 바뀌냐 == 이걸로 update를 해줄 수 있게 로직을 짜면 될거같다

#### 축 갱신

```js
  svg.select('.yAxis')
    .transition()
    .duration(1000)
    .call(this.getAxis())
```

- 축도 call을 통해 축 생성자 함수를 부르는 식으로 갱신이 가능하다.

### 9. length가 유지되지 않는 dataSet 갱신 : enter, exit 활용

- 일단 먼저 축척단에서 늘어나거나 줄어든 자료의 길이를 반영한다.

```js
xScale.domain(d3.range(dataset.length));
```

- data 메소드는 데이터와 엮여있는 문서요소의 참조를 반환한다.
- enter()는 부족한 선택물을 감지하고, exit는 남는 선택물을 감지할 수 있다. 따라서 length가 바뀌었을 때는 enter()을 통해 append하면 생성되어있는 요소들보다 넘칠 경우 생성물을 더 그려준다. 

```js
// 먼저 바깥족 위치로 설정하고 가져온다(??)
bars.enter()
  .append('rect')
  .attr('x', w)
```

- 문서요소가 데이터보다 많을 경우는 죽인다. 문서요소를 추출해서 오른쪽으로 사라지도록 트랜지션을 걸고 제거한다.
- remove는 독특한 트랜지션 메서드인데, 이전 트랜지션이 완전히 끝나기를 기다렸다가 DOM에서 문서요소를 제거한다.

```js
bars.exit()
  .transition()
  .duration(500)
  .attr("x", w)
  .remove()
```

- 배열에서 첫 값을 빼더라도 가장 마지막 막대가 제거된다. 데이터 값이 갱신중이긴 하지만 막대는 생성할때 엮인 값에 얽매이지 않고 새로운 값을 할당받기 때문이다. 완전히 새로운 객체로 제어를 하기 때문에, 객체 불변성이 유지되지 않는 것이다. 
- 이걸 고치고 싶을수도 있는데 key함수를 통해 배열의 키값을 연결해줘서, 요소들의 순서가 그대로 유지되도록 할 수 있다.
- data메소드의 두번째 인자로 콜백함수를 전달하는 방식으로 이를 적용할 수 있다.

```js
const dataSet = [
  {key:0, value:23},
  {key:1, value:29},
  {key:2, value:27},
  {key:3, value:26},
]

svg.selectAll('rect')
  .data(dataset, d => d.key)
  .enter()
  .append('rect')
```

### 10. transition/motion: 갱신시 효과 주기

- 갱신될때마다 트랜지션을 줄 수가 있다. 
- 갱신 dataBinding 다음에 바로 transition을 넣어주면 된다

```js
 svg.selectAll('rect')
    .data(dataSet)
    .transition()
    .attr("x", (d,i) => xScale(i))
    .attr("y", (d) => height - (d*2))
    .attr("height", d => yScale(d))
    .attr('fill', 'blue')
```

- transition을 추가하면 d3는 새로운 값을 즉각 반영하지 않고 시간 개념을 도입한다. 시작과 끝 값을 정규화하고 중간 상태의 모든 값을 계산한다.

#### 트랜지션 관련 메서드

- duration : attr의 적용까지 걸리는 시간은 250밀리초 정도인데, 트랜지션의 지속 시간을 제어할 수 있다. 실제 지속 시간이 그대로 반영되지는 않음. 어떤 svg냐에 따라 양상이 달라짐
- ease: cubic-in-out이 디폴트로 되어있음. linear, cicle, elastic, bounce 등이 있다.
- delay : 트랜지션의 시작 시점을 지정한다. duration보다 앞서있는게 좋다. 동적인 지연값을 주면 시차 지연을 두면서 시각화 요소들이 서로 다른 타이밍에 트랜지션이 될 수 있을 것이다. 인자로 함수를 넘길 수 있다 `(d,i) =>`
- each : 트랜지션이 시작하거나 끝나는 시점에 무언가를 동작시켜야 할때, 원소별로 임의의 코드를 실행할 수 있다. 첫번째 인자로 start 혹은 end를 넘기고 콜백을 넘기면 동작을 지정할 수 있다.

## 고민

- 반응형은 어캐..? 막대그래프라고 치면 svg전체 크기와 함께 좌표값도 전부 수정해야 하는걸까????? 회사에서 해보자 한번 => 넘 까다로워따...
- template단에서 대략적인 svg틀을 잡아두고 select로는 만들어놓은 elem을 선택하고 그 이하로 만드는게 더 명시적일거같다
- 기존의 enter로 들어있는 차트 그리는 함수를 재사용할 수도 있을거 같다. watch단에서 prev랑 current 파악해서 길이가 어떻게 달라졌는지 파악하면 좋을듯? => 그렇게 해봤다
- 이게 처음 차트를 그릴때 enter을 update상황에서 그대로 쓰기가 힘들다 => 또 다른 함수가 필요함 그래서 함수만 따지면 4개 필요함 initial, enter, update, exit... 이거보다 더 효율적인 방법 있을거 같긴 한데 지금으로선 찾기 힘들다
- 아니면 exit update enter을 모두 나눠서 적용해도 될거같기도 => 명시적일거같다

## 결론

- Vue에서는 그래도 차트를 그리고 update, enter, exit시키는 로직을 재사용하기에는 쉬워 보였다.
- 회사에서 하는 거는 데드라인이 있고 반응형까지 적용해야 하는 프로젝트이기 때문에, d3가 그렇게 적합해보이지 않는다.
- update, exit, enter모두 일관성있게 명시적으로 선언적으로 반복되는 로직을 계속 작성해야하기 때문에 유지보수에 강점이 있지 않다.
- 결론적으로는 가성비가 떨어진다. 매우 복잡한 차트나 데이터 시각화가 필요한 프로젝트에서는, 구현할 데이터 시각화의 복잡성이 d3의 복잡성을 상회하는 더 큰 프로젝트에서는 쓸만하겠지만, 막대 그래프, 꺾은선 그래프 정도만 필요한 프로젝트에서는 d3를 감싼, Vue와의 호환성이 더 좋은 라이브러리를 쓰는 것이 옳을 것이라 생각한다.
- 그래두.. d3를 배워보면서 필요할때 Vue 데이터 시각화 라이브러리들의 디테일한 커스텀을 할 수 있을 것이고, 실무에서 쓰면서 대충만 알고 있었던 SVG에 대해 좀더 깊게 탐구해볼 수 있었다. 이제 라이브러리 찾아야지
- React로 데이터 시각화 프로젝트 할때 시간이 없어서 포기했던 d3를 다시 볼수 있어서 좋았다..

## reference

- [Scott Murray - Interactive Data Visualization for the web, O`REILLY](https://www.oreilly.com/library/view/interactive-data-visualization/9781449340223/)
- [44bits- 장대한 시각화도 select() 부터](https://www.44bits.io/ko/post/d3js-basic-understanding-select-and-enter-api)
- [zziuni - d3.js API Docs 한국어 번역](https://github.com/zziuni/d3)
- [이민호 - enter, update, exit](https://lumiamitie.github.io/d3/d3-enter-update-exit/)
- [HAMA - d3 Tansition으로 작업하기](https://hamait.tistory.com/338)