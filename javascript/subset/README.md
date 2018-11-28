## subset

[뒤로가기](/javascript/README.md)

자바스크립트의 서브셋은 대부분 보안을 목적으로 하고 있다. 광고 서버와 같은 신뢰할 수 없는 곳에서 온 스크립트라도, 보안 서브셋을 사용하여 작성되었다면 그 스크립트는 안전하게 실행될 수 있다.
자바스크립트 확장은 표준이 아니므로, 모든 브라우저 간에 호환되는 언어를 요구하는 웹 개발자들에게는 그다지 유용하지 않을 것이다. 그럼에도 이 장에서 자바스크립트 확장을 다루는 이유는 다음과 같다.
>자바스크립트 확장 버전은 아주 강력하다.
>언젠가는 표준이 될 것이다.
>파이어폭스에서는 이러한 확장을 사용할 수 있다.
>서버에서 스파이더몽키나 라이노를 자바스크립트 엔진으로 사용한다면, 이러한 확장을 서버 측 자바스크립트 프로그래밍에 사용할 수 있다.

###자바스크립트 서브셋
대부분의 자바스크립트 서브셋은 신뢰할 수 없는 코드를 안전하게 실행하기 위해 정의되었다. 그런데 이와는 다른 이유로 정의된 흥미로운 서브셋이 하나 있다.
#####1. 좋은 부분들
더글라스 크락포드의 책 에서는 자바스크립트 서브셋을 다루는데, 이는 언어 요소중에서 더글락포드가 생각하기에 사용할 가치가 있는 부분만으로 구성된 것이다. 이 서브셋의 목적은 언어를 단순화 하고, 애매한 부분과 결점을 숨기며, 궁극적으로는 프로그래밍을 좀 더 쉽게 하고 더 나은 프로그래밍을 만들 수 있도록 하는 것이다. 크락포드는 이 서브셋을 만들게 된 동기를 다음과 같이 설명한다.
>대부분의 프로그래밍 언어는 좋은 부분과 나쁜 부분을 포함하고 있다. 나는 단지 좋은 부분을 선택하고 나쁜 부분을 피함으로써 더 나은 프로그래머가 될 수 있음을 발견했다.

크락포드의 서브셋에는 with문과 continue문, eval() 함수가 없다. 또한 함수를 정의할 때는 오직 함수 정의 표현식만을 사용하고, 정의문은 사용하지 않는다. 루프와 조건부의 본문은 중괄호로 둘러 싸야 하며, 중괄호 생략은 불가하다. 쉼표연산자, 비트연산자, ++와, --도 없다. ==와 !==연산제 대신 ===와 !===를 권장한다.
자바스크립트에는 블록 유효범위가 없으므로, 크락포드의 서브셋은 var문이 함수 몸체의 최상단에만 나타나도록 제한한다. 프로그래머들은 함수 내의 모든 변수를 함수의 가장 처음에 선언해야 하며, 하나의 변수에 하나의 var를 사용해야 한다. 이 서브셋은 전역 변수 사용을 장려하지 않지만 이것은 실제 언어의 제한이 아니라 코딩 규칙이다.
#####2.보안을 위한 서브셋
Good Parts는 프로그래머의 생산성을 향상시키려는 열망과 미학적 이유를 위해 설계된 자바스크립트의 서브셋이다. 이보다 큰 부류의 서브셋이 있는데, 이 서브셋은 보안 컨테이너 혹은 샌드박스 안에서, 신뢰할 수 없는 자바스크립트 코드를 안전하게 실행하려는 목적으로 설계되었다. 보안 서브셋에서는 샌드박스를 깰 수 있는 코드나 전역 실행 환경에 영향을 주는 모든 언어 기능, API를 허용하지 않는다. 각 서브셋은 코드를 구문 분석하고 코드가 해당 서브셋의 규칙을 따르는지를 검사하는 정적 검사기와 합쳐져 있다. 이는 매우 제한적인 경향이 있기 때문에, 몇몇 샌드박식 시스템은 좀 더 크고 덜 제한적인 서브셋을 먼저 정의하고, 만약 코드가 이 서브셋을 따른다.
정적 검사를 통해 자바스크립트 안전성을 검증하려면, 몇 가지 기능은 반드시 제거되어야 한다.
1.eval()과 Function() 생성자는 모든 보안 서브셋에서 허용되지 않는데, 이는 eval()과 Function() 생성자가 임의의 코드문자열을 실행할 수 있고, 그 문자열들은 정적으로 분석될 수 없기 때문이다.
2.this키워드는 제한되거나 아예 금지되어야 하는데, 이는(엄격하지 않은 모드에서) 함수가 this를 통해 전역 객체에 접근할 수 있기 때문이다. 전역 객체에 대한 접근을 막는 것을 모든 샌드박싱 시스템의 주요 목적 중 하나다.
3.보안을 위한 서브셋에서는 보통 with문의 사용이 금지되는데, 이는 with문을 사용한 경우, 정적 코드 검증이 더욱 어려워지기 때문이다.
4.특정 전역 변수는 보안 서브셋에서는 허용되지 않는다. 클라이언트 측 자바스크립트에서 브라우저의 Window 객체는 window DOM과 전역 객체라는 두 가지 역할을 한다. 따라서 코드는 임의의 window 객체를 참조할 수 없다. 이와 비슷하게, document 객체에는 페이지 내용을 완전히 제어할 수 있는 메서드들이 있다. 이는 신뢰되지 않은 코드에 너무 강력한 권한을 부여한다. document와 같은 전역 변수에 대해 두가지 접근법을 취할 수 있다. 1. 전역변수 전체에 대해 사용을 금지하고, 대신 웹 페이지의 제한된 부분에만 접근할 수 있는 커스텀 API를 샌드박스 내의 코드가 사용할 수 있게 제공하는 것이다. 2. 샌드박스 내의 코드가 실행되는 컨테이너가 표준 DOM API 중에서 안전한 부분만 구현한 퍼사드나 프락시 document 객체를 제공하는 것이다.
5.보안 서브셋에서는 일부 특수 프로퍼티와 메서드를 금지하고 있는데, 이는 샌드박스 내에서 실행되는 코드에 너무 강력한 권한을 준다. 금지 프로퍼티는 argument 객체의 caller와 callee프로퍼티, 함수의 call()과 apply() 메서드, constructor와 prototype 프로퍼티가 있다.
6. . 연산자를 사용하도록 프로퍼티 접근 표현식이 작성되었다면, 정적 분석만으로도 특수 프로퍼티에 대한 접근은 충분히 막을 수 있다. 그러나 대괄호를 사용해서 프로퍼티 접근을 하는 경우는 훨씬 어려운대 대괄호를 사용한 임의 문자열 표현식은 정적으로 분석할 수 없기 때문이다. 이와 같은 이유로 보안 서브셋에서는 대괄호에 들어가는 값이 숫자나 문자열 리터럴이 아니라면, 보통 대괄호를 사용하는 것을 금지한다.

이러한 제한 중 몇 가지(eval() 사용과 with문을 금지하는 등의)는 프로그래머에게 그리 부담 되지 않는데, 이는 자바스크립트 프로그래밍에서 그다지 일상적으로 사용되는 기능이 아니기 때문이다. 반면에 프로퍼티에 접근할 때 대괄호를 사용을 제한하는 등의 사항은 매우 부담스럽기 때문에, 이렇게 부담이 큰 부분에는 보통 코드 변환이 사용된다. 예를 들면 변환기는 대괄호가 사용된 부분을 발견하면, 실행 시간 검사를 수행하는 함수를 호출하게끔 자동으로 변환한다.

현재 보안 서브셋은 꽤 다양하게 구현되어 있다. 이 서브셋 모두 완전히 기술하는 것은 이 책의 범위를 넘어서지만, 가장 중요한 몇가지에 대해서는 짧게 다뤄볼 것이다. (또한 개인공부적으로 이곳은 좀 더 성숙, 숙달 되고 나서 진행하려 하기 때문에 뭐가 있는지만 기술하고 넘어가겠다.)
>ADsafe
>dojox.secure
>Caja
>FBJS
>마이크로소프트 Web Sandbox

#####상수와 범위 변수
언어 확장에 대해 살펴보자. const 키워드를 사용하여 상수를 정의할 수 있다.
```
const pi = 3.14;				->상수를 정의하고 값을 할당한다.
pi = 4;							->이후 모든 할당은 조용히 무시된다.
const pi = 4;					->상수를 재정의하는 것은 잘못된 것이다.
var pi = 4;						->이 또한 잘못된 것이다.
```
const 키워드의 작동 방식은 var 키워드와 매우 비슷하다. 블록 유효범위가 없으며 상수는 함수 정의부의 최상단으로 끌어올려진다.
변수에 대한 블록 유효범위가 없다는 것은 오랫동안 자바스크립트의 단점으로 간주되었고, let 키워드를 추가함으로써 이를 해결하고 있다.
>var와 마찬가지로 변수를 정의함
>for/in 루프에서 var를 대신하여 사용
>블록 구문에서 새 별수를 정의하고 명시적으로 그 변수의 유효범위를 분리하기 위해 사용
>유효범위가 단일 표현식인 변수를 정의하는데 사용

let을 사용하는 가장 간단한 방법은 var대신 쓰는 것이다. let을 사용하여 정의된 변수는 가장 가까운 블록 내에서만 유효하다. 만약 루프 내에서 let을 사용하여 변수를 정의했다면, 이 변수는 루프 밖에서는 존재하지 않을 것이다.
```
function oddsums(n) {
	let total = 0, result=[];							->함수 전체가 유효 범위다.
    for(let x = 1;, x <= n; x++) {						->x는 오직 루프 내에서만 유효하다.
    	let odd = 2*x-1;								->odd는 오직 이 루프 내에서만 유효하다.
        total +=odd;
        result.push(total);
    }
    //여기서 x나 odd를 사용하려면 참조 에러가 발생한다.
    return result;
}
oddsum(5)												->[1,4,9,16,25]를 반환한다.
```
이는 유효범위가 루프에 한정된 변수를 생성하는데, 루프의 조건절과 증가절에서 이 변수를 사용할 수 있다. 또한, for/in 루프 내에서 같은 방식으로 let을 사용할 수 있다.
```
o = {x:1,y:2};
for(let p in o) console.log(p);						->x와 y를 출력한다.
for each(let v in o) console.log(v);				->1과 2를 출력한다.
console.log(p);										->참조 에러: p는 이 유효범위에 정의되지 않았다.
```
선언문 우측에 오는 변수 초기화 표현식은 선언하려는 변수의 유효범위 안에서 평가된다. 그러나 for 루프의 초기화 표현식은 새로 선언되는 변수의 유효범위 바깥에서 평가된다. 이는 루프에서 선언되는 새 변수가 같은 이름을 가진 다른 변수를 가릴 때만 문제가 된다.
```
let x = 1;
for(let x = x + 1; x < 5; x++)
	console.log(x);							->2, 3, 4를 출력한다
{											->새 변수 유효범위를 만들도록 블록을 시작한다.
	let x = x + 1;							->이 블록에서 x는 undefined이다. 따라 x+1은 NaN이다.
    console.log(x);							->NaN을 출력한다.
}
```
var를 사용하여 정의도니 변수는 그 변수가 정의된 함수 내부에 전체에 존재하지만, 실제 var문이 실행되기 전까지는 초기화되지 않는다. 즉, var문이 나오기 전에 변수를 사용하면 그 변수의 값은 undefined이다. let으로 선언된 변수의 경우도 비슷하다. 규칙이 적용되는 범위가 블록이라는 점만 다를 뿐이다.
```
let x=1, y=2
let (x=x+1,y=y+2) {						->이 블록에서 새로 정의되는 x,y는 밖에서 정의된 x, y를 가리킨다.
	console.log(x+y);					->5를 출력한다.
};
console.log(x+y);						->3을 출력한다.
```
let 블록의 변수 초기화 표현식이 블록 내부가 아니라 블록 밖의 유효범위에서 해석된다는 사실을 이해해야 한다. 앞의 코드에서 기존 변수x에 1을 더한 값을 새 변수x에 할당하고 잇다.
let블록의 변형으로, 변수 및 변수 초기화 표현식을 둘러싼 괄호 다음에 블록이 아닌 단일 표현식이 온다. 이를 let표현식이라 하며, 앞서 나온 코드는 다음과 같이 작성될 수 있다.
```
let x=1, y=2;
console.log(let (x=x+1,y=x+2) x+y);					->5를 출력한다.
```
###해체 할당
스파이더 몽키 1.7에는 해체 할당이라고 하는 복합 할당의 한 종류가 구현되어 있다.(파이썬이나 루비에서 해체 할당을 보았을 수도 있다.) 해체 할당에서 등호의 오른쪽에 있는 값은 배열 또는 객체('구조화된'값)이고, 왼쪽에는 배열문법이나 객체 리터럴 문법과 비슷한 문법으로 하나 이상의 변수 이름을 지정한다.
해체 할당이 일어나면, 등호의 오른쪽에서 하나 이상의 값이 추출되어 등호의 왼쪽에 지정된 변수로 저장된다. 해체 할당은 일반 할당 연산자에서의 사용 외에 var와 let을 사용하여 새 변수를 초기화 할 때 사용될 수도 있다.
```
let [x,y] = [1,2];							->let x=1, y=2와 같다
[x,y] = [x+1,y+1];							->x = x+1, y = y+1과 같다.
[x,y] = [y,x];								->두 변수의 값을 바꾼다.
console.log([x,y]);							->[3,2]를 출력한다.
```
해체 할당을 사용하면 배열을 반환하는 함수의 결과를 다루기가 수월해 진다.
```
//[x,y] 좌표를 [r,theta] 극좌표로 변환한다.
function polar(x,y) {
	return [Math.sqrt(x*x+y*y), Math.atan2(y,x)];
}
//극좌표를 데카르트 좌표로 변환한다.
function cartesian(r,theta) {
	return [r*Math.cos(theta), r*Math.sin(theta)];
}
let [r,theta] = polar(1.0, 1.0);							->r=Math.sqrt(2), theta=Math.PI/4
let [x,y] = cartesian(r,theta);								->x=1.0, y=1.0
```
```
let [x,y] = [1];							->x=1, y=undefined
[x,y] = [1,2,3];							->x=1, y=2
[,x,,y] = [1,2,3,4];						->x=2, y=4
```
해체 할당문의 값은 개별적으로 추출한 값이 아니라 전체 데이터 구조다. 따라서 다음과 같은 체인 할당을 할 수 있다.
```
let first, second, all;
all = [first,second] = [1,2,3,4];					->first=1, second=2, all=[1,2,3,4]
```
심지어 해체 할당은 중첩 배열에도 사용할 수 있다. 다음처럼 오른쪽이 중첩 배열일 때 해체 할당을 적용하려면, 왼쪽 또한 중첩될 배열 리터럴처럼 보이도록 구성해야 한다.
```
let [one, [twoA, twoB]] = [1, [2,2.5], 3];			->one=1,twoA=2,twoB=2.5;
```
특히 프로퍼티와 변수 이름에 같은 식별자를 사용하려는 유횩이 자주있기 때문에 혼란스러울 수 있다. 다음 예제에서 r,g,b는 프로퍼티 이름이고 red,green,blue는 변수 이름이라는 것을 확실히 이해해야 한다.
```
let transparent = {r:0.0, g:0.0, b:0.0, a:1.0};				->RGBA 색상
let {r:red, g:green, b:blue} = transparent;					->red=0.0,green=0.0,blue=0.0
```
다음 예제는 삼각근 계산을 하는 코드를 단순화 하기 위해, Math 객체의 함수를 변수에 복사한다.
```
//let sin=Math.sin, cos=Math.cos, tan=Math.tan과 같다.
let {sin:sin, cos:cos, tan:tan} = Math;
```
중첩 배열에 할당을 사용할 수 있는 것처럼 중첩된 객체에도 해체 할당을 사용할 수 있다. 사실, 임의의 데이터 구조를 나타내기 위해 중첩 배열과 중첩 객체 두 문법을 결합할 수도 있는데, 예를들면 다음과 같다.
```
//중첩된 데이터 구조: 객체 배열을 포함한 객체
let data = {
	name: "destructuring assignment",
    type: "extenstion",
    impl: [{engine: "spidermonkey", version: 1.7},
    		{engine: "rhino", version: 1.7}]
};
//앞의 데이터에서 값 네 개를 추출하기 위해 해체할당을 사용한다.
let ({name:feature, impl: [{engine:impl1, version:v1},{engine:impl2}]} = data) {
	console.log(feature);									->"destructuring assigment"을 출력한다.
    console.log(impl1);										->"spidermonkey"를 출력한다.
    console.log(v1);										->1.7을 출력한다.
    console.log(impl2);										->"rhino"를 출력한다.
}
```
###순회
모질라의 자바스크립트 확장에는 for each 루프와 파이썬 방식의 이터레이터, 제너레이터가 새롭게 포함되어있다.
#####1.for/each 루프
E4X에서 표준이 된 루프 구문이다. E4X은 자바스크립트 프로그램에 XML 태그가 구문 그대로 나타나는 것을 허용하는 언어 확장이고, XML 데이터를 처리하기 위한 문법과 API가 추가되었다. E4X는 웹브라우저에서는 널리 사용되고 있지 않지만 모지랄 자바스크립트 1.6이 지원한다.
for/each 루프는 for/in루프와 매우 비슷하다. 그러나 객체의 프로퍼티를 순회하지 않고 프로퍼티의 값을 순회한다.
```
let o = {one: 1, two: 2, three: 3}
for(let p in o) console.log(p);						->for/in: 'one', 'two', 'three'를 출력한다.
for each(let v in o) console.log(v);				->for/each: 1,2,3을 출력한다.
```
보통은 숫자 인덱스를 순서대로 순회하지만, 실제로 이 숫자 인덱스 순서는 표준이 아니고 for/each 루프에서 순서를 지키도록 요구하지도 않는다.
```
a = ['one', 'two', 'three'];
for(let p in a) console.log(p);							->배열 인덱스 0,1,2를 출력한다.
for each(let v in a) console.log(v);					->배열 요소 'one', 'two', 'three'를 출력한다.
```
for/each 루프는 배열 요소만 순회하는 것이 아니라, 배열이 상속한 메서드를 포함하여 배열의 열거 가능한 모든 프로퍼티를 순회한다는 사실을 유념하라. 이러한 이유로 for/each 루프에 배열을 사용하는 것은 권장하지 않는다.
#####2.이터레이터
자바스크립트 1.7은 for/in루프가 더 일반적인 동작을 하도록 개선하였다.
이터레이터는 컬렉션의 값들을 순회하고, 컬렉션에서 현재의 위치를 추적하는 데 필요한 모든 상태를 유지하는 객체다. 이터레이터에는 반드시 next() 메서드가 있어야 하며, next()를 호출하면 컬렉션의 다음 값이 반환된다. 
예를 들어 다음에 살펴볼 counter() 함수는 이터레이터를 반환하고, 이 이터레이터는 next()를 호출할 때마다 연속적으로 더 큰 정수를 반환한다. 함수 유효범위를 현재 카운터의 상태를 저장하기 위한 글로저로 사용하였다.
```
//이터레이터를 반환하는 함수.
function counter(start) {
	let nextValue = Math.round(start);								->이터레이터의 priavte 상태
    return { next: function() {return nextValue+++; }};				->이터레이터 객체를 반환한다.
}
let serialNumberGenerator = counter(1000);
let sn1 = serialNumberGenerator.next();								->1000
let sn2 = serialNumberGenerator.next();								->1001
```
유한한 컬랙션에서 더 순회할 값이 없을 때, next() 메서드는 StopInteration 예외를 발생시켜야 한다. StopIteration은 자바스크립트 1.7 전역 객체의 프로퍼티다. StopIteration 프로퍼티의 값은 일반 객체(프로퍼티가 없는)로, 이 객체는 순회를 끝내는 용도만을 위해 예약된 객체다. 특히 StopIteration은 TypeError()나 RangeError() 같은 생성자 함수가 아님을 유의하라. 다음 예제에서 rangeInter() 메서드는 주어진 범위 내의 정수를 순회하는 이터레이터를 반환한다.
```
//주어진 정수 범위를 순회하는 이터레이터를 반환한다.
function rangeInter(first, last) {
	let nextValue = Math.ceil(first);
    return {
    	next: function() {
        	if (nextValue > last) throw StopIteration;
            return nextValue++;
        }
    };
}
//range이터레이터를 사용한 순회, 예외를 처리해야 하는 점이 불편하다.
let r = rangeInter(1,5);								->이터레이터 객체를 얻는다.
while(true) {											->루프 내에서 이터레이터 객체를 사용한다.
	try {
    	console.log(r.next());							->이터레이터의 next() 메서드를 호출한다.
    }
    catch(e) {
    	if(e == StopIteration) break;					->StopIteration을 만나면 루프를 종료한다.
        else throw e;
    }
}
```
StopIteration 예외를 명시적으로 처리해야만 하는 루프에서 이터레이터 객체를 사용하는 것이 얼마나 불편할 수 있는지 살펴보라. 이러한 불편함 때문에 이터레이터 객체를 직접적으로 자주 사용하지는 않을 것이다. 대신 순회 가능 객체를 사용한다. 이객체는 순회될 수 있는 컬렉션이다. 순회 가능 객체는 반드시 interator() 메서드(앞뒤로 두 개의 밑줄을 포함)를 정의해야 하고 이 메서드는 컬렉션에 대한 이터레이터 객체를 반환해야 한다.
다음 코드는 range() 함수를 정의하는데, 이 함수는 순회 가능 객체(이터레이터가 아니라)를 반환하고, 이 순회 가능한 객체는 정수 간의 범위를 나타낸다. while 루프에서 range 이터레이터를 사용하는 것에 비해, for/in 루프에서 순회 가능한 range(객체)를 사용하는 편이 얼마나 더 쉬운지 살펴보라.
```
//숫자의 범위를 표현하는 순회 가능 객체를 반환한다.
function range(min,max) {
	return {												->범위를 나타내는 객체를 반환한다.
    	get min() { return min; },							->범위의 경계는 변하지 않기 때문에
        get max() { return max; },							->그 값을 클로저에 저장한다.
        includes: function(x) {								->주어진 값이 범위에 들어올 수 있는지 확인하는 테스트
        	return min <= x && x x <= max;
        },
        toString: function() {								->범위는 문자열로 표현될 수 있다.
        	return "[" + min + "," + max + "]";
        },
        __interator__: function() {							->범위 내에 있는 정수는 순회 가능하다.
        	let val = Math.ceil(min);						->현재 위치를 클로저 상에 저장한다.
            return {										->이터레이터객체를 반환한다.
            	next: function() {							->다음정수를 반환한다.
                	if(val > max)							->만약 마지막 범위를 지녔다면 중지한다.
                    	throw StopIteration;
                    return val++;							->그렇지 않으면 값을 반환하고, 다음 값을 위해 증가시킨다.
                }
            };
        }
    };
}
//이런 방식으로 범위를 순회할 수 있다.
for(let i in range(1,10)) console.log(i);					->1부터 10까지의 숫자를 출력한다.
```
순회 가능 객체와 그 이터레이터를 만들려면 iterator() 메서드를 작성하고 Stopiteration 예외를 발생시켜야 하지만(보통의 경우) 직접 iterator() 메서드를 호출하거나 Stopiteration 예외를 처리할 필요는 없다. 만약 어떤 이유 때문에 순회 가능 객체로부터 이터레이터 객체를 명시적으로 얻으려면, iterator()함수를 호출하면 된다. 만약 이 함수의 인자가 순회 가능 객체라면, 이 함수는 단순히 iterator()메서드를 호출한 결과를 반환하기 때문에 코드를 깔끔하게 유지할 수 있다.
만약 iterator() 메서드가 없는 객체에 대해 iterator() 함수를 호출하면, iterator()함수는 해당 객체에 대한 임의의 이터레이터 객체를 반환한다. 이 이터레이터의 next() 메서드는 두 개의 값을 가진 배열을 반환하는데, 첫 번째 배열 요소는 프로퍼티 이름이고, 두 번째 배열 요소는 프로퍼티 값이다.
```
for(let [k,v] in iterator({a:1,b:2}))					->키와 값을 순회한다.
	console.log(k + "=" + v);							->"a=1", 그다음 "b=2"를 출력한다.
```
iterator() 함수가 반환한 이터레이터 에는 두 가지 중요한 특징이 있다. 먼저 이 이터레이터는 상속한 프로퍼티는 무시하고 오직 그 객체가 소유한 프로퍼티만 순회하는데, 보통 이 방식이 이터레이터에 기대하는 동안 방식이다. 두 번째로 만약 iterator()에 두 번째 인자로 true를 넘기면, 반환된 이터레이터는 프로퍼티 값이 아니라 오직 프로퍼티의 이름만 순회한다. 다음 코드는 이 두 가지 특징을 보여준다.
```
o = {x:1, y:2}											->두 개의 프로퍼티가 있는 객체
Object.prototype.z = 3;									->이제 모든 객체는 z를 상속한다.
for(p in o) console.log(p);								->x, y, z를 출력
for(p in iterator(o, true)) console.log(p);				->x와 y만 출력
```
#####3.제너레이터
새로운 키워드 yield를 사용하며, yield키워드는 어떤 값을 반환하는 함수 내에서 사용되지만, yield와 return의 차이범은 yield는 호출자에게 값을 반환하고도 함수를 종료하지 않고 실행상태를 유지한다는 점이다.
yield 키워드를 사용하는 모든 함수는 제너레이터 함수다. 제너레이터 함수는 yield를 사용하여 값을 반환한다. 제너레이터 함수는 함수 몸체의 끝에 이르기 전에 return문을 사용하여 함수를 종료할 수 있지만, return 문으로 값을 반환해서는 안된다. yield를 사용한다는 점, 값을 반환과 관련한 제한을 제외하면 제너레이터 함수는 일반 함수와의 차이범이 없다. 제너레이터 함수 또한 function 키워드를 사용하여 정의되고, 이들에 대한 typeof 연산자의 결과는 function이며, 일반 함수가 그런 것처럼 Function.prototype을 상속받는다. 그러나 함수가 호출되면 제너레이터 함수는 일반 함수와 완전히 다르게 동작한다. **제너레이터 함수를 호출하면 함수의 본문을 실행하는 것이 아니라, 제너레이터 객체를 반환 값으로 받게 된다.**
제너레이터 객체는 제너레이터 함수의 현재 실행 상태를 나타낸다. 제너레이터는 next() 메서드가 정의되어 있고, 이 메서드는 제너레이터 함수의 실행을 재개하고, 다음 yield문을 만날 때까지 제너레이터 함수가 계속 실행되도록 한다. 만약 제너레이터 함수가 yield문을 만나면, yield문의 값은 제너레이터의 next() 메서드의 반환값이 된다. 만약 제너레이터 함수가 종료되면(return 문을 실행하거나 함수 몸체의 끝에 이르렀을 때), 제너레이터의 next() 메서드는 StopInteration 예외를 발생시킬 것이다.
StopInteration 예외를 발생시킬수 있는 next() 메서드가 제너레이터에 있다는 사실은 명백히 제너레이터가 이터레이터 객체임을 알려준다. 사실 제너레이터는 순회 가능한 이터레이터이며 이는 제너레이터가 for/in루프에 사용될 수 있다는 뜻이다. 다음 코드는 제너레이터 함수를 작성하는 것과 그 함수가 중간마다 넘긴 값을 순회하는 것이 얼마나 쉬운지를 보여준다.
```
//두 정수의 범위를 순회하는 제너레이터 함수를 정의한다.
function range(min, max) {
	for(let i = Math.ceil(min); i <= max; i++) yield i;
}
//제너레이터를 얻기 위해 제너레이터 함수(range)를 호출하고, 그 결과 값을 순회한다.
for(let n in range(3,8)) console.log(n)						->3에서 8까지 숫자를 출력한다.
```
제너레이터 함수는 종료될 필요가 없다. 사실 제너레이터 함수를 사용하는 고전적인 예제는 피보나치 수열 값을 yield하는 것이다.
```
//피보나치 수열을 실행 중간마다 밖으로 넘기는 함수.
function fibonacci() {
	let x = 0, y = 1;
    while(true) {
    	yield y;
        [x,y] = [y,x+y];
    }
}
//제너레이터를 얻기 위해, 제너레이터 함수를 호출한다.
f = fibonacci();
//제너레이터를 이터레이터로 사용한다. 처음 10개의 피보나치 숫자를 출력한다.
for(let i = 0; i < 10; i++) console.log(f.next());
```
fibonacci() 제너레이터 함수는 절대 return 하지 않는다는 점을 주목하라. 이러한 이유로 fibonacci() 제너레이터 함수는 결코 StopInteration 예외를 발생시키지 않을 것이다. 여기서 for/in 루프에서 순회 가능한 객체로 제너레이터를 사용하여 무한 루프를 도는 대신, 제너레이터의 next() 메서드를 명시적으로 열번 호출한다. 코드가 모두 실행되고 나서도 제너레이터 f는 여전히 제너레이터 함수 실행 상태를 유지하고 있다. 만약 제너레이터 함수를 더 사용하지 않는 다면, f의 close() 메서드를 호출하여 실행 상태를 풀어줄 수 있다.
```
f.close();
```
만약 함수가 실행되고 있던 위치가 하나 또는 그 이상의 try 블록 내부였다면, close()가 반횐되기 전에 모든 finally 절이 실행된다. close()는 값을 반환하지 않지만, 만약 finally 블록내에서 예외가 발생한다면 이 예외는 close() 호출자에게 전파될 것이다.
제너레이터는 보통 연속적인 데이터를 처리하는 데 유용하고, 이러한 데이터는 리스트 , 텍스트상의 여러 줄, 구문 분석기가 뱉어내는 토큰 등이 있다. 제너레이터는 셀 명령어의 유닉스 스타일 파이프라인과 유사한 방법으로 체이닝될 수 있다. 이러한 접근 방식의 흥미로운 점은 lazy 접근법이라는 점이다. 값이 필요할 때 제너레이터(혹은 제너레이터 파이프라인)로부터 값을 하나씩 끌어온다는 점이다.
```
//문자열 s를 한 번에 한 줄씩 중간에 외부로 넘기는 제너레이터.
//s.split()을 사용하지 않는다는 점을 주목하라.
//왜냐하면 split() 메서드는 한 번에 모든 문자열을 처리하고 배열에 할당하는데
//여기서 필요한 시점에 하나씩 처리할 것이기 때문이다.
function eachline(s) {
	let p;
    while((p = s.indexOf('\n')) != -1) {
    	yield s.substring(0,p);
        s = s.substring(p+1);
    }
    if(s.length > 0) yield s;
}
//순회 가능한 i의 각 요소 x에 대해 f(x)를 호출하고, f(x)의 반환 값을 중간에 외부로 넘기는 제너레이터 함수.
funtion map(i, f) {
	for(let x in i) yield f(x);
}
//i의 요소 x에 대해 f(x)가 참이면 x 값을 중간에 외부로 넘기는 제너레이터 함수.
function select(i, f) {
	for(let x in i) {
    	if(f(x)) yield x;
    }
}
//처리할 텍스트 문자열.
let text = " #comment \n \n hello \nworld\n quit \n unreached \n";

//이제 텍스트를 처리할 제너레이터 파이프라인을 만든다.
//먼저, 텍스트를 줄 단위로 쪼갠다.
let lines = eachline(text);

//그 다음, 각 줄의 처음과 끝에 있는 공백을 제거한다.
let trimed = map(line, function(line) { return line.trim(); });

//빈 줄과 주석은 무시한다.
let nonblank = select(trimmed, function(line) {
	return line.length > 0 && line[0] != "#"
});

//이제 공백이 제거되고 주석 등이 걸러진 문자열을 파이프라인에서 가져와서 처리한다.
//quit 라는 문자열을 만나면 중지한다.
for (let line in nonblank) {
	if(line === "quit") break;
    console.log(line);
}
```
보통 제너레이터는 생성될 때 초기화 된다. 제너레이터 함수로 넘겨진 값들은 제너레이터가 받는 유일한 값이다. 그러나 실행 중인 제너레이터에 추가적인 입력 값을 제공하는 것도 가능하다. 모든 제너레이터에는 send() 메서드가 있고, 이 메서드는 next() 메서드와 마찬가지로 제너레이터 함수 실행을 재개한다. next() 메서드와 다른 점은 send() 메서드에는 값을 전달할 수 있다는 것이고, 이 값은 yield 표현식의 값이 된다.
next()와 send() 메서드 외에도 제너레이터 함수를 재개하는 방법은 throw()  메서드를 사용하는 것이다. 만약 throw() 메서드를 호출하면, yield 표현식은 throw() 메서드에 넘겨진 인자를 예외를 발생시키는 데 사용할 것이다.
```
//초기 값부터 카운트하는 제너레이터 함수.
//증가 값을 지정하기 위해 제너레이터의 send() 메서드를 사용한다.
//그리고 초기 값을 재설정하려고 제너레이터의 throw("reset")을 사용하는데, 이는 오직 예제를 위한 것이다.
//이런 식으로 throw()를 사용하는 것은 나쁜 방식이다.
function counter(initial) {
	let nextValue = initial;								->초기 값을 지정한다.
    while(true) {
    	try {
        	let increment = yield nextValue;				->값을 외부로 보내고 increment를 얻는다.
            if(increment)									->만약 외부에서 increment를 지정한다면,
            	nextValue += increment;						->increment 값을 사용하고,
            else nextValue++;								->그렇지 않으면 1만큼 증가시킨다.
        }
        catch(e) {											->어디선가 발생자의 throw()를 
        	if(e === "reset")								->호출하면 이곳의 코드가 실행된다.
            	nextValue = initial;
            else throw e;
        }
    }
}
let c = counter(10);						->초기 값 10 을 지정하여 제너레이터 생성
console.log(c.next());						->10 출력
console.log(c.send(2));						->12 출력
console.log(c.throw("reset"));				->10 출력
```
#####4. 배열 함축
배열 함축은 파이썬에서 가져온 자바스크립트 1.7의 또 다른 기능이다. 배열 함축은 다른 배열이나 순회 가능한 객체의 요소를 사용하여 배열을 초기화하는 기법이다.
range() 함수를 사용한 배열 함축이고, 주어진 범위에서 짝수에 대한 제곱 값을 가진 배열을 초기화 한다.
```
let evensquares = [x*x for (x in range(0,10)) if (x % 2 === 0)]
```
다음 코드와 같다.
```
let evensquares = [];
for (x in range(0,10)) {
	in (x % 2 === 0)
    	evensquares.push(x*x);
}
```
일반적으로 배열 함축은 다음과 같은 형태를 하고 있다.
```
[ 표현식 for ( 변수 in 객체 ) if ( 조건 )]
```
다음은 배열 함축 문법을 명확히 살펴보기 위한 더욱 구체적인 예이다.
```
data = [2,3,4,-5];
squares = [x*x for each (x in data)];

//음수가 아닌 배열 요소에 대해서만 제곱근을 취한다.
roots = [Math.sqrt(x) for each (x in data) if (x >= 0)]

//이제 객체의 프로퍼티 이름을 가진 배열을 만들 것이다.
o = {a:1, b:2, f: function() {}}
let allkeys = [p for (p in o)]
let ownkeys = [p for (p in o) if (o.hasOwnProperty(p))]
let notfuncs = [k for ([k,v] in Iterator(o)) if (typeof v !== "function")]
```
#####5.제너레이터 표현식
자바스크립트 1.8에서 배열 함축을 둘러싼 대괄호를 괄호로 바꾸면 제네레이터 표현식 이라는 것을 만들 수 있다. 제너레이터 표현식은 배열 함축과 비슷하지만, 표현식의 값은 배열이 아니라 제너레이터 객체다. 배열 함축 대신 제너레이터 표현식을 사용할 때의 이점은 필요할 때만 표현식을 평가할 수 있다는 점이다. 한 번에 전부 계산하는 것이 아니라 필요할 때 필요한 만큼 수행을 할 수 있는 것이다. 그리고 배열 함추깅 유한한 배열을 다루지만, 제너레이터 표현식을 사용하는 것의 단점은, 제너레이터 표현식은 그 값에 대해 임의 접근을 할 수 없고 오직 순차 접근만 해야 한다는 것이다. 즉, 제너레이터 표현식에서는 배열처럼 인덱스를 사용하여 접근할 수 없다. 만약 n 번째 값을 얻으려면, 그 전에 n-1 만큼의 값을 순회해야만 한다.
```
function map(i, f) {				->i의 각 요소 x에 대해 f(x)의 반환 값을 중간마다 내보내는 제너레이터.
	for(let x in i) yield f(x);
}
```
제너레이터 표현식은 map()과 같은 함수를 작성하거나 사용할 필요를 없애 준다. 제너레이터 g가 중간마다 내보낸 값 x와, 각 x에 대해 f(x)의 결과 값을 중간마다 내보내는 제너레이터 h를 얻으려면 다음과 같이 코드를 작성하면 된다.
```
let h = (f(x) for (x in g));
```
eachline() 제너레이터를 이용하면 다음과 같이 주석과 빈 줄을 걸러낼 수 있다.
```
let lines = eachline(text);
let trimmed = (l.trim() for (l in lines));
let nonblank = (l for (l in trimmed) if (l.length > 0 && l[0]!='#'));
```
###약칭 함수
자바스크립트 1.8에는 함수 작성을 간단하게 하기 위한 약칭 문법이 포함되어 있다. 만약 함수가 단일 평가식을 평가하고 그 값을 반환한다면, return 키워드와 함수 몸체를 둘러싼 중괄호를 생략하고 단순히 인자 목록 뒤에 두기만 하면 된다.
```
let succ = function(x) x+1, yes = function() ture, no = function() flase()

//배열 숫자 역순으로 정렬한다.
data.sort(function(a,b) b-a);

//data 배열의 모든 요소를 제곱하고 그 합을 반환하는 함수를 정의한다.
let sumOfSquares = function(data)
	Array.reduce(Array.map(data, function(x) x*x), function(x,y) x+y)
```
###다중 catch절
자바스크립트 1.5에서 try/catch문은 여러 catch문을 사용할 수 있도록 확장되었다. 이 기능을 사용하려면 catch 절의 매개변수 이름 다음에 if 키워드와 조건문표현을 두어야 한다.
```
try{
	//여기서 여러 종류의 예외가 발생할 수 있다.
    throw 1;
}
catch(e if e instanceof ReferenceError) {
	//참조 에러를 처리한다.
}
catch(e if e === "quit") {
	//문자열이 quit인 경우를 처리한다.
}
catch(e in typeof e === "string") {
	//예외가 문자열이고 quit가 아닌 경우는 여기서 처리한다.
}
catch(e) {
	//다른 모든 경우를 처리한다.
}
finally {
	//finally 절은 별 다를 바 없이 작동한다.
}
```
###E4X: XML을 위한 ECMAScript
E4X로 더 잘 알려진 XML을 위한 ECMAScript는, 자바스크립트에 대한 표준 확장으로서 XML 문서를 처리하는 데 필요한 강력한 기능들을 포함하고 있다.
E4X는 XML 문서를 XML 객체(또는 XML 문서의 요소나 속성)로 표현하고, XML조각(공통된 부모에 포함되어 있지 않은 하나 이상의 XML요소)을 밀접한 관계의 XMLList 객체로 표현한다. 이번 절 전체에서 XML 객체를 생성하고 XML 객체를 다루는 여러 방법을 살펴보자. XML객체는 근본적으로 새로운 종류의 객체고, XML 객체를 지원하는 특수 목적 E4X 문법 또한 그렇다. 알다시피 typeof 연산자는 함수가 아닌 모든 표준 자바스크립트 객체에 대해 "object"를 반환한다. 함수가 그렇듯, XML 객체는 일반적인 자바스크립트 객체와 다르고 typeof 연산자는 XML 객체에 대해 "xml"을 반환한다. XML객체가 클라이언트 측 자바스크립트에서 사용하는 DOM 객체와 관련이 없다는 사실은 중요하다.
```
//XML 객체를 생성한다.
var pt =
	<periodictable>
    	<element id = "1"><name>Hydrogen</name></element>
        <element id = "2"><name>Helium</name></element>
        <element id = "3"><name>Lithium</name></element>
	</periodictable>
//주기율표(periodictable)에 새 요소를 추가한다.
pt.element += <element id="4"><name>Beryllium</name></element>;
```
E4X의 XML 리터럴 문법은 중괄호를 이스케이프 문자로 사용하고, 이를 통해 자바스크립트 표현식을 XML 내에둘 수 있다. 다음 예는 방금 보았던 XML요소를 생성하는 다른 방법이다.
```
pt = <periodictable></periodictable>;
var elements = ["Hydrogen", "Helium", "Lithium"];
//배열을 사용하여 XML 태그를 만든다.
for(var n = 0; n <elements.length; n++) {
	pt.element += <element id={n+1}><name>{element[n]}</name></element>;
}
```
이러한 리터럴 문법 외에도 문자열로부터 해석된 XML 또한 사용할 수 있다. 다음 코드는 주기율표에 새로운 요소를 추가한다.
```
pt.element += new XML('<element id="5"><name>Boron</name></element>');
```
여러 XML 조각을 한번에 처리해야할 때는 XML() 대신 XMLList를 사용한다.
```
pt.element += new XMLList('<element id="6"><name>Carbon</name></element>'
	+ '<element id="7"><name>Nitrogen</name></element>');
```
한번  XML 문서가 정리되면, E4X에서 제공하는 직관적인 문법을 사용하여 XML 문서의 내용에 접근할 수 있다.
```
var elements = pt.element;			->모든 <element> 태그의 목록으로의 평가된다.
var names = pt.element.name;		->모든 <name> 태그의 목록으로 평가된다.
var n = names[0];					->"Hydrogen": <name> 태그의 0번째 내용.
```
마침표가 두 개 붙은 ..연산자는 자손연산자이고, 이 연산자를 일반 맴버 접근 연산자.을 사용하는 곳에 사용하는 곳에 사용할 수 있다.
```
//모든 <name> 태그의 목록을 얻는 또 다른 방법이다.
var names2 = pt..name;
```
E4X는 와일드카드 연산자도 지원한다.
```
//모든 <element> 태그의 모든 하위 태그를 얻는다.
//이것은 모든 <name> 태그의 목록을 얻는 또 다른 방법이다.
var names3 = pt.element.*;
```
E4X에서 속성 이름은 @ 문자를 사용하여 태그 이름과 구분할 수 있다.
```
//Helium의 원소 번호는 몇번인가?
var atomicNumber = pt.element[1].@id;
```
속성 이름에 대한 와일드카드 연산자는 @*이다.
```
//모든 <element> 태그의 전체 속성 목록.
var atomicNums = pt.element.@*;
```
E4X에는 임의의 단정 표현식을 사용하여 목록을 걸러내는 문법이 정의되고 있고, 이 문법은 강력하고도 매우 간결하다.
```
//모든 요소에 대해 id 속성이 3보다 작은 요소만 포함한다.
var lightElements = pt.element.(@id < 3);
//모든 요소에 대해 태그 이름이 "B"로 시작하는 것만 포함하고,
//걸러낸 각각의 element 태그 아래에 <name>태그를 만든다.
var bElementNames = pt.element.(name.charAt(0) == 'B').name;
```
XML 태그와 속성 목록을 순회하기 위해 E4X표준이 정한 것이다. for/each는 for/in과 비슷하다는 점을 떠올려보라. 둘의 차이는 for/in이 객체의 프로퍼티를 순회하고, for/each는 객체의 프로퍼티 값을 순회한다는 점이다.
```
//주기율 테이블의 각 요소의 이름을 출력한다.
for each (var e in pt.element) {
	console.log(e.name);
}
//요소의 원자 번호를 출력한다.
for each (var n in pt.element.@*)
	console.log(n);
```
E4X 표현식은 할당 표현식의 왼쪽에 나올 수 있다. 따라서 이미 존재하는 태그와 속성을 변경할 수 있고, 새로운 태그와 속성을 추가할 수도 있다.
```
//Hydrogen에 대한 <element> 태그에 새로운 속성을 추가하고 새로운 하위 요소를 추가하여 다음과 같은 구조로 만든다.
//<element id="1" symbol="H">
//	<name>Hydrogen</name>
//	<weight>1.00794</weight>
//</element>
pt.element[0].@symbol = "H";
pt.element[0].weight = 1.00794;
```
속성과 태그를 제거하는 것 또한 표준 delete 연산자를 사용하여 쉽게 할 수 있다.
```
delete pt.element[0].@symbol;			->속성을 삭제한다.
delete pt..weight;						->모든 <weight> 태그를 삭제한다.
```
E4X는 XML 객체에 몇 가지 메서드들을 정의하고 있는데, 다음 코드는 그 중 하나인 insertChildBefore()에 대한 예다.
```
pt.insertChildBefore(pt.element[1], <element id="1"><name>Deuterium</name></element>);
```
E4X는 XML 네임스페이스를 완전히 인식하며, XML네임스페이스에 대한 문법과 API를 제공한다.
```
//"default xml namespace" 구문을 사용하여 기본 네임스페이스를 정의한다.
default xml namespace = "http://www.w3.org/1999/xhtml";

//몇 개의 svg 태그를 가진 xhtml 문서다.
d = <html>
		<body>
        	이것은 작고 빨간 사각형이다.
            <svg xmlns="http://www.w3.org/2000/svg" width="10" height="10">
            	<rect x="0" y="0" width="10" height="10" fill="red"/>
            </svg>
        </body>
    <html>

//body 요소, 네임스페이스 uri, 지역 이름(네임스페이스 접두사가 붙이 않은 이름)
var tagname = d.body.name();
var bodyns = tagname.uri;
var localname = tagname.localName;

//<svg> 요소를 선택하는 것은 까다로운데, 이는 svg가 기본 네임스페이스가 없기 때문이다.
//따라서 svg에 대한 Namespace 객체를 생성하고, :: 연산자를 사용하여 네임스페이스를 태그이름에 추가한다.
var avg = new Namespace('http://www.w3.org/200svg');
var color = d.svg::rec.@fill									->color 변수의 값은 "red" 이다.
```