# d3.js

뭐가 있는지 감만 좀 잡는 정리

## d3의 정신

## 기반기술 - SVG

확장 가능한 벡터 그래픽 - 2차원 그래픽을 표현하기 위해 만들어진 XML 파일 형식의 마크업 언어 벡터는 기본적인 도형, 패스 등 일러스트에서 작업하는 모든 것. **SVG는 코드로 수정할 수 있기 때문에 다른 이미지 형식보다 강력**

- x, y: 좌표
- width, height
- 도형) rect, circle, ellipse, line, polyline, polygon, path
- cx:원의 중심 중 x값, cy:원의 중심 중 y값, r:원의 반지름
- [svg 기본 도형 attribute](https://developer.mozilla.org/ko/docs/Web/SVG/Tutorial/%EA%B8%B0%EB%B3%B8_%EB%8F%84%ED%98%95)

### coordinate

![좌표찍어라](https://alexwlchan.net/images/2019/cartesian_coordinates.svg)

왼쪽 맨 끝점이 origin, translate가 그 지점을 기준으로 진행됨

```js
var svg = d3.select("body").append("svg")
    .attr("width", outerWidth)
    .attr("height", outerHeight);

var g = svg.append("g")
    .attr("transform", "translate("
      + marginLeft + ","
      + marginTop + ")");
```

### fundamental shapes

- rect : x, y, widht, height, rx, ry
- circle : cx, cy, r
- line : x1, x2, y1, y2 ...
- d3.svg.line, d3.svg.area

## d3 API

### setAttribute

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
- attr : svg attribute 설정
- style : svg style 설정
- append: selectAll같은 elem[]에다가 추가

### enter, update, exit

![???뭐야이게](https://lumiamitie.github.io/images/post_image/d3_enter_update_exit/d3_enter_update_exit01.PNG)

join처럼 생각하기

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

### data bind

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
  bars.attr("width", function(d){return d;})

  //exit
  bars.exit().remove();
}
```

### scales

척도. scale은 어떤 범위의 숫자를 다른 범위의 숫자로 변경해주는 함수. 숫자의 확장. 도메인을 지정하면 range의 범위로 => 선형변환, 일종의 일대일함수를 반환한다고 생각하면 될듯yo

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
  - scaleLinear : 분절되지 않은 연속적인 정의역
  - scaleSqrt : 제곱근 분절 정의역
  - scaleDate : 날짜를 분절 정의역으로 사용
  - scaleOrdinal : range와 domain을 일대일 매핑
- domain : 정의역의 실질적인 범위, derives from data(d3.max, d3.min, extent의 api로 데이터에서 추출)
- range : 치역(반환값)의 범위
- interpolate : 보간, 정의역에 대응하는 뭔가 치역을 갖다 붙이는 행위

### axis

차트에서 축은 너무나 자주 사용되서 축을 생성하는 기능이 있음  
scale을 넘겨주면 알아서 그려줌 => 축을 그리는 기준을 제공  

```js
const resizer = ()_=>{
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

### ticks

```js
var x = d3.scale.linear()
    .domain([12, 24])
    .range([0, 720]);

x.ticks(5); // [12, 14, 16, 18, 20, 22, 24]
```

분절점, scale domain값 사이 적절하게 나눠서 ticks 인자만큼의 값을 집어넣는다  
축만들때 위치 나누기 좋음