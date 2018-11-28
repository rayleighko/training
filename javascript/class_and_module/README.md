## class and module

[뒤로가기](/javascript/README.md)

각 객체는 다른 모든 객체와 구분되는 프로퍼티의 고유한 집으로 취급하였다. 그러나 객체끼리 프로퍼티를 공유할 수 있도록 클래스를 정의하는 것도 종종 유용하다.
클래스의 구성원, 즉 인스턴스는 상태를 정의하거나 저장하는 프로퍼티를 가지고 있고, 그들의 동작을 정의하는 프로퍼티(보통 메서드)를 가지고 있기도 하다. 이러한 동작(메서드)은 클래스 수준에서 정의되고 모든 인스턴스가 공유한다.
자바스크립트에서 클래스는 프로토타입 기반의 상속 메커니즘을 기반으로 하고 있다. 두 객체가 같은 프로토타입 객체로부터 프로퍼티를 상속받았다면, 둘은 같은 클래스의 인스턴스다.
자바스크립트 클래스와 프로토타입 기반 상속 메커니즘은 자바나 그와 비슷한 언어의 크래스 상속과는 상당히 다르다는 사실을 이해해야 한다.
자바스크립트 클래스의 중요한 특징 중 하나는 동적으로 확장될 수 있다는 것이다. 또한, 객체의 형식보다는 객체의 능력을 더 중요시하는 덕 타이핑 이라는 프로그래밍 철학도 다룬다.

###클래스와 프로토타입
자바스크립트 클래스는 같은 프로토타입 객체로부터 프로퍼티를 상속받은 객체의 집합이다. 따라서 프로토타입 객체는 클래스의 핵심이라고 할 수 있다. 이전 단원에서 inherit() 함수를 정의했는데, 이 함수는 지정된 프로토타입 객체를 상속하는 객체를 생성하고 반환한다. 만약 프로토타입 객체를 정의하고 이 프로토타입을 상속하는 객체를 생성하기 위해 inherit() 함수를 사용한다면, 자바스크립트에서는 클래스를 정의한 것이다. 일반적으로 인스턴스는 추가적인 초기화 작업이 필요하기 때문에, 객체를 생성하고 초기화하는 함수를 작성하는 일은 일반적이다.
다음은 값 범위를 표현하는 클래스를 위한 프로토타입 객체를 정의하고, 또한 새 인스턴스를 생성하고 초기화하는 팩터리 함수도 정의한다.
```
//range 객체를 반환하는 팩터리 함수다.
function range(from, to) {
	//아래에 정의한 range.methods 프로토타입 객체를 상속하는 객체를 생성하기 위해 inherit()함수를 사용한다.
    //range.methods 프로토타입 객체는 이 함수의 프로퍼티로 저장되고 모든 range 객체가 공유하는 메서드를 정의하고 있다.
    var r = inherit(range.methods);
    
    //이 range 객체의 시작과 끝을 (객체의 상태로) 저장한다. 이 프로퍼티들은 해당 객체의 고유한 값이고 상속되지 않는다.
    r.from = from;
    r.to = to;
    
    //생성한 객체를 반환한다.
    retrun r;
}
//이 프로토타입 객체는 모든 range 객체가 상속하는 메서드를 정의한다.
range.methods = {
	//x 값이 범위 내에 있다면 true를, 그렇지 않으면 false를 반환한다.
    //이메서드는 텍스트 범위나 날짜 범위에 대해서도 숫자 범위와 마찬가지로 작동한다.
    includes: function(x) { return this.from <= x && x <= this.to; },
    //범위 내의 각 정수에 대해 f를 한 번씩 호출한다. 이 메서드는 숫자 범위에 대해서만 작동한다.
    foreach: function(f) { 
    	for (var x = Math.ceil(this.from); x <= this.to; x++) f(x);
    },
    //범위를 표현하는 문자열을 반환한다.
    toString: function() { return "(" + this.from + "..." + this.to + ")"; }
};
//range 객체를 사용하는 몇 가지 예제들.
var r = range(1,3);				-> range 객체를 생성
r.includes(2);					-> true: 2가 범위에 있다.
r.foreach(console.log);			-> 1 2 3 을 출력한다.
console.log(r);					-> (1...3)을 출력
```
이 코드에는 range 객체를 생성하는 팩터리 함수 range()가 있다. 클래스 프로토타입 객체를 저장하기 위한 장소로, 편의상 range() 함수의 range.methods 프로퍼티를 사용하고 있다는 것을 눈여겨보자.
이러한 방식으로 프토로타입 객체를 생성자 함수의 프로퍼티에 두는 것은 특별한 이유가 있어서도 아니고, 관용적인 방법도 아니다. 둘째로, range() 함수는 각 range 객체에 from 프로퍼티와 to 프로퍼티를 정의하고 있음을 주목하자.
이 프로퍼티들은 각 range객체의 고유 상태를 저장하고, 공유되거나 상속하지 않는다. 마지막으로 range.methods의 모든 메서드는 상속되고 공유되며 from,to 프로퍼티를 참조하기 위해 this를 사용하고 있음을 유의하라.
this는 메서드의 호출 대상 객체를 가리킨다. 이렇게 this를 사용하는 것은 모든 클래스 메서드의 기본 특징이다.

###클래스와 생성자
이전 예제는 생성자를 정의하지 않았기 때문에 관용적인 방법은 아니며, 생성자는 새로 생성된 객체를 초기화하는 용도로 사용되는 함수다. 생성자는 이전에 다룬 new 키워드를 사용하여 호출된다. 생성자를 호출하면 자동으로 **새로운 객체**가 생성되고, 생성자 함수 내부에서 새로 생성된 객체를 사용하기 때문에, **생성자 함수는 새 객체의 상태를 초기화 하는데만 신경** 쓰면 된다. 생성자 호출의 핵심적인 특징은 **생성자의 prototype 프로퍼티가 새 객체의 프로토타입으로 사용**된다는 것이다. 이는 한 생성자를 통해 생성된 모든 객체는 같은 객체를 상속하고, 따라서 같은 클래스의 맴버임을 뜻한다.
```
//이 함수는 새 Range 객체를 초기화 하는 생성자 함수다.
//이 함수는 어떤 객체도 내부에서 생성하고 반환하지 않는다. 단지 this를 초기화할 뿐이다.
function Range(from, to) {
	//이 Range 객체의 시작과 끝을(객체의 상태로) 저장한다.
    //이 프로퍼티들은 해당 객체의 고유한 값이고 상속되지 않는다.
    this.from = from;
    this.to = to;
}
//모든 Range 객체는 이 객체를 상속한다. 모든 객체가 이 객체를 상속하려면, 프로퍼티 이름은 반드시 "prototype"이어야 한다.
Range.prototype = {
	//x 값이 범위내에 있다면 true를, 그렇지 않으면 false를 반환한다.
    //이 메서드는 텍스트 범위 및 날짜 범위에 대해서도 숫자 범위와 마찬가지로 작동한다.
    include: function(x) { return this.from <= x && x <= this.to; },
    //범위 내의 각 정수에 대해 f를 한 번씩 호출한다. 이 메서드는 숫자 범위에 대해서만 작동한다.
    foreach: function(f) {
    	for(var x = Math.ceil(this.from); x <= this.to; x++) f(x);
    },
    //범위를 표현하는 문자열을 반환한다.
    toString: function() { return "(" + this.from + "..." + this.to + ")"; }
};

//Range 객체를 사용하는 몇 가지 예제.
var r = new Range(1,3);			->Range 객체를 생성한다.
r.includes(2);					->true: 2가 범위에 있다.
r.foreach(console.log);			->1 2 3을 출력
console.lof(r);					->(1...3)을 출력
```

range() 팩터리 함수는 생성자로 변경되면서 이름이 Range()로 바뀌었다. 클래스와 생성자 함수의 이름을 대문자로 시작하는 것은 매우 일반적인 코딩 규칙이다. 일반 함수와 메서드는 소문자로 이름을 시작한다.
다음으로 range() 팩터리 함수가 일반 함수처럼 호출되는 반면에, Range() 생성자는 new키워드를 사용하여 호출한다.
Range() 생성자는 new 키워드를 사용하여 호출되기 떄문에, inherit()을 호출하거나 새 객체를 생성하기 위한 어떤 별도의 행동도 할 필요가 없다. 새 객체는 생성자 함수가 실행되기 전에 자동으로 생성되고, 생성자 함수 내에서 this 값으로 접근할 수 있다.
Range() 생성자는 그저 새 객체를 초기화 하기만 하면 되고 생성된 객체를 반환할 필요도 없다. 생성자를 호출하면 새 객체는 자동으로 생성되고, 새 객체의 메서드로서 생성자 함수가 호출된 다음, 초기화가 완료된 새 객체가 반환된다.
생성자 호출이 일반적인 함수 호출과 크게 다른것이 생성자 이름의 첫 글자를 대문자로 하는 또하나 이유다. 생성자는 new 키워드를 사용하여 호출된다고 가정하기 때문에, 일반적인 함수 호출처럼 호출하면 보통 제대로 작동하지 않는다.
일반적인 함수와 구분되도록 작명 규칙을 지키는 것은 다른 프로그래머가 언제 new를 사용해야 하는지를 알게 하는 데 도움이 된다.

다른 핵심적인 차이는 프로토타입 객체의 이름을 짓는 방법이다. 첫 번째 예제에서 프로토타입은 range.methods였다. 이는 편하고 알아보기 쉬운 이름이기는 하지만 임의적이다. 두번째 예제에서 프로토타입은 Range.prototype이고 이 이름은 **정해진 규칙**이다. Range() 생성자를 호출하면 자동으로 Range.prototype이 새로 생성된 Range 객체의 프로토타입으로 지정된다.

마지막으로 변하지 않은 부분은 range의 메서드들은 두 클래스 모두 같은 방식으로 정의되고 호출되었다.
#####1.생성자와 클래스 구별
프로토타입 객체는 클래스를 구별할 때 핵심적인 역할을 한다. 두 객체는 같은 프로토타입 객체를 상속한 경우에만 같은 클래스의 인스턴스다. 새로 생성된 객체의 상태를 초기화하는 생성자 함수는 클래스구별의 핵심이 아니다. 서로 다른 두 생성자 함수라도 같은 프로토타입 객체를 가리키는 prototype 프로퍼티를 가질 수 있다. 그러면 두 생성자는 같은 클래스의 인스턴스를 만드는 데 사용될 수 있다.
생성자가 prototype만큼 객체 구별에 핵심적인 역할을 하지는 않더라도, 생성자는 클래스를 대표하는 역할을 맡는다. 당연하게도 대부분의 생성자 함수 이름은 클래스 이름과 같다. 예를 들어 Range() 생성자는 Range 객체를 만든다는 식이다.
그러나 더 근본적으로, 생성자는 객체가 어떤 클래스에 속한 것인지 검사할 때 instanceof 연산자와 같이 사용된다. 객체 r이 Range 객체인지를 알고싶다면, 다음과 같이 할 수 있다.
```
r instanceof Range				-> r이 Range.prototype을 상속했다면 true를 반환한다.
```
#####2.constructor 프로퍼티
Range()에서는 클래스의 메서드를 정의한 객체를 Range.prototype에 할당했다. 메서드를 객체 리터럴의 프로퍼티로 정의하는 것이 편리하긴 하지만, 그래야만 객체를 만들 수 있는 것은 아니다. 모든 자바스크립트 함수는 생성자로 사용될 수 있는데, 함수가 생성자로 호출되려면 prototype 프로퍼티가 있어야 한다. 따라서 **모든 자바스크립트 함수에는 자동으로 prototype 프로퍼티가 설정**된다.
이 prototype 프로퍼티의 값은 constructor 프로퍼티 하나만 가진 객체다. constructor 프로퍼티는 열거되지 않으며 constructor 프로퍼티 값은 해당 함수 객체다.
```
var F = function() {};					->이것은 함수 객체다.
var p = F.prototype;					->이것은 F와 연관이 있는 프로토타입 객체다.
var c = p.constructor;					->이것은 프로토타입과 관련한 함수 객체다.
c === f									->true: 모든 함수에 대해 F.prototype.constructor==F이다.
```
미리 정의된 프로토타입 객체가 있고, 이 프로토타입 객체가 constructor 프로퍼티를 가지고 있다는 말은, 일반적으로 **어떤 객체가 자기 자신의 생성자를 가리키는 constructor 프로퍼티 또한 상속하고 있음**을 뜻한다. 따라서 생성자는 클래스를 구별하는데 사용될 수 있고, constructor 프로퍼티를 통해 객체의 클래스를 얻을 수 있다.
```
var o = new F();				-> 클래스 F의 객체 o를 생성한다.
o.constructor === F				-> true: constructor 프로퍼티는 클래스를 가리킨다.
![photo1](./photo1.png)
```
Range 클래스는 미리 정의되어 있던 Range.prototype을 별도로 정의한 객체로 덮어 쓴다. 그리고 이 별도로 정의한 프로토타입 객체에서 constructor 프로퍼티가 없다. 따라서 Range 클래스의 인스턴스에도 constructor 프로퍼티는 없을 것이다. 이 문제는 명시적으로 프로토타입 객체에 constructor 프로퍼티를 추가함으로써 해결할 수 있다.
```
Range.prototype = {							->역 참조를 위해 constructor 프로퍼티를 명시적으로 설정한다.
	constructor: Range,
    includes: function(x) { return this.from <= x && x <= this.to; },
    foreach: function(f) {
    	for( var x = Math.ceil(this.from); x <= this.to; ++ ) f(x);
    },
    toStringL function() { return "(" + this.from + "..." + this.to + ")"; }
};
```
일반적으로 사용하는 또 다른 기법은, constructor 프로퍼티가 있는 미리 정의되어 있는 prototype 객체를 사용하는 것이다. 거기에 하나씩 메서드를 추가해 가면 된다.
```
//미리 정의되어 있는 Range.prototype 객체를 확장하기 때문에, 자동으로 생성된 Range.prototype.constructor 프로퍼티를 덮어쓰지 않는다.
Range.prototype.includes = function(x) { return this.from <= x && x <= this.to; };
Range.prototype.foreach = function(f) {
	for( var x = Math.ceil(this.from); x <= this.to; x++ ) f(x);
};
Range.prototype.toString = function() {
	return "(" + this.from + "..." + this.to + ")";
};
```

###자바 스타일 클래스
만약 자바나 그와 비슷한 강력한 타입의 객체 지향 언어를 사용하여 프로그램을 작성해봤다면, 다음의 네 가지 맴버 유형에 익숙할 것이다.
1. 인스턴스 필드
인스턴스마다 있는 프로퍼티나 변수로, 개별 객체의 상태를 저장한다.
2. 인스턴스 메서드
클래스의 모든 인스턴스가 공유하는 메서드로, 개별 인스턴스를 대상으로 호출 된다.
3. 클래스 필드
인스턴스가 아니라 클래스와 관련된 변수다.
4. 클래스 메서드
인스턴스가 아니라 클래스와 관련된 메서드다.

**자바스크립트가 자바와 다른 점 한 가지는 함수가 값이라는 점이고, 따라서 메서드와 필드 사이에는 뚜렷한 구분이 없다.**

만약 프로퍼티 값이 함수라면 그 프로퍼티는 메서드이고, 함수가 아니라면 보통 프로퍼티나 '필드'일 뿐이다. 이런 차이가 있음에도, 자바의 네 가지 맴버 유형을 자바스크립트에서도 따라할 수 있다. 자바스크립트로 클래스를 정의할 때는 세 가지 객체가 관련된다. 그리고 이 세 객체의 프로퍼티는 각각 다른 종류의 맴버 구실을 한다.
1. 생성자 객체
앞에서 다루었듯이, 생성자 함수(객체)는 클래스 이름을 정의한다. 이 생성자 객체에 추가한 프로퍼티는 클래스 필드와 클래스 메서드다.(필드와 메서드는 프로퍼티 값이 함수인지 아닌지에 따라 결정된다.)
2. 프로토타입 객체
이 객체의 프로퍼티는 클래스의 모든 인스턴스에 상속된다. 그리고 그 값이 함수인 프로퍼티는 인스턴스 메서드로 작동한다.
3. 인스턴스 객체
각 인스턴스는 독립적인 객체이고, 인스턴스에 직접 정의한 프로퍼티는 다른 인스턴스에 공유되지 않는다. 함수가 아닌 프로퍼티는 클래스의 인스턴스 필드로 작동한다.

자바스크립트에서 클래스를 정의하는 과정은 세 단계의 알고리즘으로 줄일 수 있다.
1. 새 객체의 인스턴스 프로퍼티를 설정하는 생성자 함수를 작성한다.
2. 생성자의 프로토타입 객체에 인스턴스 메서드를 정의한다.
3. 생성자에 클래스 필드와 클래스 프로퍼티를 정의한다.
이런 알고리즘으로 defineClass() 함수로 구현할 수 있다.

```
function defineClass(constructor,			->인스턴스 프로퍼티를 설정하는 함수.
						methods,			->인스턴스 메서드: 프로토타입 복사됨.
                        statics)			->클래스 프로퍼티: 생성자로 복사됨.
{
	if (methods) extend(constructor.prototype, methods);
	if (static) extend(constructor, static);
    return constructor;
}
//Range 클래스를 변형한 클래스다.
var SimpleRange = defineClass(
	function(f,t) { this.f = f; this.t = t;},
    {
		includes: function(x) { return this.f <= x && x <= this.t;},
        toString: function() { return this.f + "..." + this.t; }
	},
    { upto: function(t) { return new SimpleRange(0, t); }}
)
```
다음은 클래스 정의는 앞의 코드보다 길며, 이 예제는 복소수를 표현하는 클래스를 정의하고, 자바스크립트에서 어떻게 자바 스타일의 클래스 맴버를 구현할 수 있는지 보여줄 것이다. 앞서 다룬 defineClass()를 사용하지 않고 수동으로 클래스를 정의한다.
```
Complex.js
//이 파일은 복소수를 표현하기 위한 Complex 클래스를 정의한다. 복소수는 실수부와 허수부로 나뉘며, 허수 i는 -1 제곱근 이라는 것을 상기하라.
//이 생성자 함수는 생성하는 모든 인스턴스에 인스턴스 필드 r과 i를 정의한다. 이 필드들을 객체의 상태를 실수부와 허수부로 나누어 보관한다.
function Complex(real, imaginary) {
	if (isNaN(real) || isNaN(imaginary))		->두 인자가 모두 숫자인지 확인.
    	throw new TypeError();					->숫자가 아니라면 예외 발생.
    this.r = real;								->복소수의 실수부.
    this.i = imaginary;							->복소수의 허수부.
}
//인스턴스 메서드는 프로토타입 객체에 함수 값을 가진 프로퍼티로 정의된다.
//여기 정의된 메서드들은 모든 인스턴스에 상속되고, 객체가 행동하는 방식을 규정한다.
//자바스크립트 인스턴스 메서드는 인스턴스 필드에 접근하기 위해 this키워드를 사용한다.

//이 복소수와 다른 복소수를 더하고, 더한 값을 새 Complex 객체로 반환한다.
Complex.prototype.add = function(that) {
	return new Complex(this.r + that.r, this.i + that.i);
}

//이 복소수와 다른 복소수를 곱하고 결과를 새로운 객체로 반환한다.
Complex.prototype.mul = function(that) {
	return new Complex(this.r * that.r - this.i * that.i, this.r * that*i + this.i * that.r);
}

//복소수의 실제 크기를 반환한다. 이는 복소평면의 원금(0,0)으로부터의 거리로 정의된다.
Complex.prototype.mag = function() {
	return Math.sqrt(this.r*this.r + this.i*this.i)
};

//이 복소수의 부정 값을 반환한다.
Complex.prototype.neg = function() { return new Complex(-this.r, -this.i); };

//Complex 객체를 유용한 문자열로 변환한다.
Complex.prototype.toString = function() {
	return "{" + this.r + "," + this.i + "}";
};

//이 Complex 객체가 다른 Complex 객체와 같은 값을 가졌는지를 검사한다.
Complex.prototype.equals = function(that) {
	return that != null &&
    	that.constructor === Complex &&
        that.r === that.r && this.i === that.i;
};

//클래스 필드(상수 같은)와 클래스 메서드는 생성자의 프로퍼티로 정의된다.
//클래스 메서드는 일반적으로 this 키워드를 사용하지 않음을 주의하라.
//클래스 메서드는 오직 메서드에 전달된 인자만을 사용하여 수행된다.

//유용한 몇 가지 복소수 클래스 필드로 미리 정의한다. 상수임을 나타내도록 이름은 대문자로 되어 있다.
//ECMAScript 5에서는 이런 프로퍼티들을 실제로 읽기 전용으로 만들 수 있다.
Complex.ZERO = new Complex(0,0);
Complex.ONE = new Complex(1,0);
Complex.I = new Complex(0,1);

//이 클래스 메서드는 인스턴스 메서드 toString()이 반호나한 포맷에 따라 문자열을 분석하고 Complex 객체를 반환한다.
//문제가 있다면 TypeError 예외를 발생시킨다.
Complex.parse = function(s) {
	try {
    	var m = Complex._format.exec(s);
        return new Complex(parseFloat(m[1]), parseFloat(m[2]));
    } catch (x) {
    	throw new TypeError ("Can`t parse '" + s + "'as a Complex number.");
    }
};

//앞의 Complex.parse()에서 사용하는 "private"클래스 필드다.
//필드 이름 앞의 밑줄은 이 필드가 내부적으로만 사용되어야 하고 공용 API의 일부로 간주되어서는 안 된다는 의미다.
Complex._format = /^\{([^,],([^}]+)\}$/;

//이와 같이 Complex 클래스를 정의하면, 다음 코드와 같이 생성자와 인스턴스 필드, 인스턴스 메서드, 클래스 필드, 클래스 메서드를 사용할 수 있다.
var c = new Complex(2,3);								->생성자로 새 객체를 만든다.
var d = new Complex(c.i,c.r);							->c의 인스턴스 프로퍼티를 사용한다.
c.add(d).toString();									->"{5,5}": 인스턴스 메서드 사용.
//클래스 메서드와 필드를 사용하는 좀 더 복잡한 표현식.
Complex.parse(c.toString()).							->c를 문자열로 바꾸고 다시 변환한다음,
	add(c.neg()).										->부정값을 더하면,
    equals(Complex.ZERO)								->결과는 언제나 0이다.
```

자바스크립트가 자바 스타일의 클래스 맴버를 흉내 낼 수 있다고 하더라도, 자바의 중요한 특징 중 자바스크립트가 지원하지 않는 것이 몇 가지 있다. 
먼저 자바는 인스턴스 메서드 안에서 인스턴스 필드를 메서드의 지역 변수처럼 사용할 수 있고, this를 비롯한 어떤 접두사도 붙일 필요가 없다. 자바스크립트는 이를 지원하지 않지만, with문을 사용하여 비슷한 효과를 얻을 수 있다(권장하지는 않는다고 한다.)
```
Complex.prototype.toString = function() {
	with(this) {
    	retrn "{" + r + "," + i + "}";
    }
};
```
자바에서는 final을 사용하여 상수 필드를 정의할 수 있다. 그리고 클래스 내부에서만 사용하고 외부에서 볼 수 없는 필드나 메서드는 private으로 정의할 수 있다.
자바스크립트에는 이런 키워드들이 없어서, 위의 예제에서는 이럴 떄 힌트를 제공하는 표기 규칙을 사용한다. 이를테면 값이 변경되면 안 되는 프로퍼티들은 이름이 대문자이고, 밑줄로 시작하는 이름의 프로퍼티는 클래스 외부에서 사용되면 안 된다는 뜻이다.

###클래스 확장하기
자바스크립트의 프로토타입 기반 상속 메커니즘은 동적이다. 객체는 자신의 프로토타입에서 프로퍼티를 상속받는데, 심지어 이는 객체가 생성된 이후에 프로토타입이 변경되더라도 마찬가지다. 다시 말해 자바스크립트 객체의 프로토타입에 메서드를 추가함으로써 간단히 자바스크립트 클래스를 확장할 수 있다.
```
//이 복소수에 대한 켤레 복소수를 반환한다.
Complex.prototype.conj = function() { return new Complex(this.r, -this.i); };
```
이처럼 내장된 자바스크립트 클래스의 프로토타입 객체 또한 열려있고, 이는 숫자, 문자열, 배열, 함수등의 객체에 우리가 마음대로 메서드를 추가할 수 있다. 이전에 ECMAScript 3에는 없는 bind() 메서드를 function 클래스에 추가하는 사례를 한적이 있다.
```
if (!Function.prototype.bind) {
	Function.prototype.bind = function(o /*, args */) {
    	//bind 메서드의 코드
    };
}
```
이것은 다른 예제이다. 다음함수에서 개인적으로 이해되지 않았던 부분들의 함수도 있으니 참고 바란다.
```
//주어진 반복 수만큼 함수 f를 여러 번 호출한다. 예를 들어 "hello"를 세번 출력하려면 다음과 같이 한다.
//var n = 3;	n.times(function(n) { console.log(n + " hello"); });
Number.prototype.times = function(f, context) {
	var n = Number(this);
    for(var i = 0;, i < n; i++) f.call(context, i);
};

//ECMAScript 5에 있는 String.trim() 메서드를 정의한다. 만약 해당 메서드가 이미 있다면 정의하지 않는다.
//이 메서드는 문자열의 시작과 끝의 공백을 제거한 문자열을 반환한다.
String.prototype.trim = String.prototype.trim || function() {
	if (!this) return this;
    return this.replace(/^\s+|\s+$/g, "");
};

//함수 이름을 반환한다. 만약 (비표준) name 프로퍼티가 있다면 그것을 사용한다.
//그렇지 않으면, 함수를 문자열로 변환하고 문자열에서 이름을 추출한다.
//이름이 없는 함수에 대해서는 빈 문자열을 반환한다.
Function.prototype.getName = function() {
	return this.name || this.toString().match(/function\s*([^(]*)\(/)[1];
};

//추가 내용
function Product(name, price) {
	this.name = name;
    this.price = price;
    
    if(price < 0){
    	throw RangeError('허용 불가능' + this.name);
	}
}
function Food(name, price) {
	product.call(this,name,price);
    this.category = 'food';
}
var cheese = new Food('feta', 5);

만약 n.times가 n.times(function(n) { console.log(n + " hello" + this.num + this.num1), {num:5 , num1:6}; }); 로 넘겨졌다면 num:5 와 num1:6는 context에 전달되어 this값들의 console에 들어가게 된다.

f는 function(n) { console.log(n + " hello" + this.num + this.num1 ) 과 반응한다.

i는 앞에 있는 바깥 매게변수들을 다 처리하고 log안에 있는 n과 반응.			-> 정확한 대답인지는 잘 모르겠음.
아마 this값 이기 때문에 자신의 객체로 반응 하기위해서 context가 앞에 있다고 생각됨.
```
Object.prototype에도 메서드를 추가할수 있지만, 그러면 모든 객체에서 메서드를 사용할 수 있으나 ECMAScript 5 이전 버전에서는 이러한 추가 메서드가 열거되지 않게 할 방법이 없기 때문에 for/in 루트에서 열거될 것이다.
호스트 환경(웹브라우저 같은)에서 정의된 클래스를 이러한 방식으로 확장할 수 있는지는 호스트 환경의 구현체마다 다르다. 예를 들면 많은 브라우저에서는 HTMLElement.prototype에 메서드를 추가할 수 있고, 현재 문서의 HTML 태그를 표현하는 객체들은 이렇게 추가된 메서드를 상송할 것이다. 그러나 이는 마이크로소프트 인터넷 익스플로러의 현재 버전에서는 작동하지 않는데, 이 때문에 클라이언트 측 프로그래밍에서 이러한 기법은 사용은 몹시 제한 받는다.

###클래스와 자료형
자바스크립트에는 null, undefined, 불리언, 숫자, 문자열, 함수, 객체 등의 몇 안 되는 자료형이 있다. typeof 연산자를 사용하면 이러한 형식을 구분할 수 있다. 그러나 일반적으로 클래스는 독자적인 자료형으로 다루는 것이 편하고, 클래스를 기준으로 객체를 구분하는 것이 편리하다. 자바스크립트 핵심부의 내장 객체(그리고 일반적인 클라이언트 측 자바스크립트의 호스트 객체)들은 classof()함수와 같은 코드를 사용하여, class속성을 기준으로 구별할 수 있다. 하지만 이 장에서 다룬 기법을 사용하여서 클래스를 정의하면, 인스턴스 객체의 "class" 속성은 언제나 "Object"이다. 따라서 classof() 함수는 이런 상황에는 도움이 되지 않는다.

다음 세부 황목에서는 객체의 클래스를 판단하는 세 가지 기법으로 instanceof 연산자, constructor 프로퍼티, 생성자 함수 이름을 설명한다. 다음 기법이 만족스럽지 못하면 덕 타이핑으로 마무리 한다. 덕 타이핑은 객체의 클래스가 무엇인지보다는 객체가 무엇을 하느냐(어떤 메서드를 가지고 있느냐)에 더 중점을 두는 프로그래밍 철학이다.
#####1. instanceof연산자
표현식 o instanceof c는 만약 o가 c.prototype을 상속한다면 true다. 이런 상속은 직접적일 필요는 없다. 만약 o가 c.prototype을 상속한 어떤 객체를 상속한다 해도, 이 표현식은 여전히 true일 것이다.
생상자는 클래스를 구별하는 대표 수단으로서의 역할을 하지만, 클래스 구별의 핵심은 프로토타입이다. instanceof 연산자는 생성자 함수를 요구하지만, 실제로 instanceof 연산자는 객체가 어떤 프로토타입을 상속했는지를 검사하며 객체를 생성하는 데 어떤 생성자를 사용했는지를 테스트하지는 않는다.
만약 어떤 객체의 프로토타입 체인에 특별한 프로토타입 객체가 있는지를 검사하려 하고 생성자 함수를 검사의 기준으로 삼고 싶지 않다면, isPrototype()메서드를 대신 사용할 수 있다. range 클래스가 객체 r의 프로토타입인지 알고싶다면?
```
range.methods.isPrototypeOf(r)
```
instanceof 연산자와 isPrototypeOf() 메서드의 한 가지 단점은 이들은 오직 주어진 객체와 클래스의 관계만 테스트할 뿐, 어떤 객체의 클래스가 무엇인지 알아내는 데 사용할 수 없다는 것이다. 더 심각한 단점은 둘 이상의 창이나 프레임을 사용하는 웹 애플리케이션의 클라이언트 측 자바스크립트에서 나타난다. 각창이나 프레임은 서로 구분되는 실행 컨텍스트고, 각각 자신의 전역 객체와 생성자 함수의 집합이 있다. 서로 다른 두 프레임에 생성된 두 배열은, 이름은 같지만 실제로는 별개의 프로토타입 객체를 상속한다, 즉, 하나의 프레임에 만들어진 배열은 다른 프레임에 있는 Array() 생성자의 인스턴스가 아니다.
#####2. constructor 프로퍼티
생성자는 대표적인 클래스 구별 수단이기 때문에, 이는 간단한 방법이다.
```
function typeAndValue(x) {
	if(x === null) return "";								->null과 undefined에는 생성자가 없다.
    switch(x.constructor) {
    case Number:	return "Number " + x;					->원시 형식
    case String:	return "String " + x;
    case Data:		return "Date " + x;						->내장 형식
    case RegExp:	return "Regexp " + x;
    case Complex:	return "Complex " + x;					->사용자 정의 형식
    }
}
```
이 코드에서 case 키워드 뒤에 나오는 표현식은 함수다. 만약 typeof 연산자를 사용했거나 객체의 class 속성을 추가했다면 문자열이 대신 사용되었을 것이다.
constructor 프로퍼티를 사용하는 이 기법은 instanceof와 같은 문제를 안고 있다. 값을 공유하는 여러 실행 컨텍스트(브라우저의 창 하나에 여러 프레임이 있는 경우)가 있을 때, 이 기법은 작동하지 않는다. 이 경우 각 프레임에는 별도의 생성자들이 유지되기 때문에, 한 프레임의 Array생성자는 다른 프레임에 있는 Array생성자와 다르다.
또한 자바스크립트 객체 가운데는 constructor 프로퍼티가 없는 것도 있을 수 있다. constructor 프로퍼티는 각 함수에 기본 프로토타입 객체가 있다는 관례를 염두해 둔 것이지만, 실수로 또는 고의적으로 프로토타입에 constructor 프로퍼티를 생략하기는 쉽다. 예를 들면 이 장의 처음에 나온 두 클래스는 관례와는 다른 생성 방식을 따르고 있어서, 이들의 인스턴스에는 constructor 프로퍼티가 없다.
#####3.생성자 이름
다른 대안으로 클래스 식별자로 생성자 함수 대신 그 이름을 사용하는 것이다. 한 창에 있는 Array 생성자는 다른 창에 있는 Array 생성자와 같지 않지만, 그 이름은 같다. 몇몇 자바스크립트 구현체에서는 함수 이름을 함수 객체의 비표준 name 프로퍼티를 통해 얻을 수 있다. name 프로퍼티가 없는 자바스크립트 구현체에서는, 함수를 문자열로 변환한 다음 이름을 추출해 낼 수 있다.
다음 예제는 객체의 자료형을 문자열로 반환하는 type() 함수를 정의한다. 이 함수는 원시 자료형이나 함수는 typeof 연산자로 처리하고, 객체에 대해서는 class속성 값 또는 생성자의 이름을 반환한다. type() 함수는 classof() 함수와 예제의 Function.getName() 메서드를 사용한다.
```
//o의 형식을 문자열로 반환한다. null이면 "null"을 반환, NaN이면 "nan"을 반환
//typeof가 "object" 이외의 값을 반환하며, 그 값을 반환한다. (몇몇 구현체는 "obejct"이외의 값을 반환하며, 이 값을 반환한다.)
//클래스가 "Object"가 아니라면 그것을 반환한다. 생성자를 가지고 있고, 생성자가 이름을 가지고 있다면, 그것을 반환한다.
function type(o) {
	var t, c, n;
    if (o === null) return "null";
    if (o !== o) return "nan";
    
    //typeof의 결과가 "object"가 아니면 그 값을 사용한다. 이는 모든 원시 값과 함수를 판별한다.
    if ((t = typeof o) !== "object") return t;
    
    //객체의 클래스가 "object"가 아니라면, 그것을 반환한다. 이는 대부분의 네이티브 객체를 판별한다.
    if((c = classof(o)) !== "object") return c;
    
    //만약 객체가 생성자 이름을 가지고 있다면 생성자 이름을 반환한다.
    if(o.constructor && typeof o.constructor === "function" && (n = o.constructor.getName())) return n;
    
    //더는 자세한 형식을 판단할 수 없다. 따라서 "object"를 반환한다.
    return "Object";
}

//객체의 클래스를 반환한다.
function classof(o) {
	return Object.prototype.toString.call(o).slice(8,-1);
};

//함수 이름(아마 "")을 반환하거나 함수 형태가 아니라면 null을 반환한다.
Function.prototype.getName = function() {
	if("name" in this) return this.name;
    return this.name = this.toString().match(/function\s*([^(]*)\(/)[1];
}
```
객체의 클래스를 구별하는 데 생성자 이름을 사용하는 기법은 constructor 프로퍼티를 사용하는 것과 같은 문제를 안고 있는데, 객체에 반드시 constructor 프로퍼티가 있으리라는 보장이 없기 때문이다. 게다가 모든 함수가 이름을 가진 것도 아니다. 만약 이름이 없는 함수 정의 표현식을 사용하여 생성자를 정의한다면, getName() 메서드는 빈 문자열을 반환할 것이다.
```
//이 생성자는 이름이 없다.
var Complex = function(x,y) { this.r = x; this.i = y; }
//이 생성자는 이름이 있다.
var Range = function Range(f,t) { this.from = f; this.to = t; }
```
#####4.덕 타이핑
이전 객체 클래스 판별 기법중 완전히 문제가 없는 것은 없다. 대안은 이 문제를 아예 회피하는 것으로 '이 객체의 클래스가 무엇인가?'를 묻는 대신 '이 객체가 무엇을 할 수 있는가?' 를 묻는 것이다. 이러한 프로그래밍 접근법은 파이썬이나 루비 같은 언어에서는 일반적인데 다음 표현에 따라 덕 타이핑이라 불린다.
Range클래스를 예로 들수 있다 이 클래스는 숫자 범위를 엄두에 두고 설계 되었다. 하지만 Range() 생성자는 전달인자가 숫자인지를 확인하는 검사를 하지 않는다. 다만 Range() 생성자는 전달인자에 대해 > 연산자를 사용하고, 따라서 인자에 대해 크기 비교를 할 수 있다고 가정한다. 이와 비슷하게 include() 메서드는 <= 연산자를 사용하지만 범위의 끝에 대해서는 어떤 다른 가정도 하지 않는다. 이 클래스는 특정 자료형을 강요하지 않기 때문에, include() 메서드는 관계 연산자ㅗㄹ 비교 할 수 있는 어떤 자료형의 from과 to와도 잘 동작한다.
```
var lowecase = new Range("a", "z");
var thisYear = new Range(new Date(2009, 0, 1), New Date(2010, 0, 1));
```
Range클래스의 foreach()메서드 또한 범위의 시작과 끝에 대한 자료형을 명시적으로 검사하지 않는다. 하지만 Math.ceil()과 +,+ 연산자를 사용하였으므로 오직 숫자로 범위가 명시될 때만 정상 동작한다.
이전 유사 배열 객체에 대해, 많은 상황에서, 어떤 객체가 Array 클래스의 실제 인스턴스인지 알 필요는 없고, 양의 정수 값을 가진 length 프로퍼티가 있다는 사실을 아는 것만으로도 충분하다. 정수값을 가진 length 프로퍼티가 존재하는 객체라면, 그 객체는 '오리처럼 걷는 객체'로 다시말해 배열로 취급할 수 있는 것이다.
그러나 명심해 둘 것은 진짜 배열의 length 프로퍼티에는 특별한 동작이 있다는 점이다. 진짜 배열에 새로운 요소가 추가되면 length는 자동으로 갱신되고,이렇게 동작하는 length프로퍼티를 가진 객체라면 진짜 배열처럼 동작하는 객체로 볼 수 있을 것이다.
앞서 살펴본 덕 타이핑 예제는 < 연산자에 객체가 어떻게 반응하는지와 length프로퍼티의 특별한 동작 방식에 관계되어 있다. 그러나 일반적으로 덕 타이핑이 라고 하는 것은 객체가 어떤 메서드들을 구현하고 있는지 테스트하는 것이다. 엄격한 자료형 검사를 요구하는 triathlon() 함수는 아마도 인자로 TriAthlete 객체를 요구할 것이다. 그러나 덕 타이핑 버전의 triathlon() 함수는 아마 walk(),swim(),bike()메서드를 구현하고 있다면 어떤 객체든 받아들이도록 설계할 수 있을 것이다. 내친김에 Range 클래스는 <와 ++연산자 대신 시작점과 끝점 객체의 compareTo()와 succe()(successor) 메서드를 사용하도록 재설계할 수도 있을 것이다.
덕 타이핑을 구현하는 한 가지 방법은 무간섭주의다. 입력 객체가 필요한 메서드를 구현하고 있다고 가정하고, 이 입력 객체가 메서드를 실제로 구현하고 있는지를 검사하지 않는 것이다. 만약 이 가정이 틀렸다면, 코드가 존재하지 않는 메서드를 호출하려 할 때 오류가 발생할 것이다. 또 다른 접근 방식은 입력 객체를 검사하는 것이다. 그러나 이 그 클래스 대신, 적합한 이름을 가진 메서드를 구현하고 있는지 검사하는 것이다. 이를 통해 적절하지 않은 입력을 사전에 거부할 수 있고, 더 많은 정보를 에러 메시지에 포함할 수 있다.

다음은 덕 타이핑을 할 때 유용하게 사용할 수 있는 quacks() 함수를 정의한다. quacks() 함수는 어떤 객체(첫 번째 인자)가, 지정된 메서드(나머지 인자)를 구현하고 있는지를 검사한다. 두 번째 이후의 각 인자에 대해, 인자가 문자열이라면 문자열과 같은 메서드가 있는지 검사할 것이다. 만약 인자가 객체라면 이 객체가 가진 메서드와 같은 이름을 가진 메서드를 , 첫 번째 인자로 주어진 객체도 구현하고 있는지를 검사할 것이다. 만약 인자가 함수라면, 이 함수를 생성자 함수로 간주하고 생성자 함수의 프로토타입 객체와 첫 번째 인자의 객체가 서로 같은 이름의 함수를 구현하고 있는지를 검사한다.
```
//o가 나머지 인수에 의해 지정된 메서드를 구현하고 있으면 참을 반환한다.
function quacks(o /*, ...*/) {
	for(var i = 1; i <arguments.length; i++) {							->o 다음의 각각 인수에 대해,
    	var arg = arguments[i];
        switch(typeof arg) {											->만약 arg가
        case 'string':													->문자열이면: 메서드의 이름을 확인한다.
        	if (typeof o[arg] !== "function") return false;
            continue;
         case 'function':												->함수라면 함수 대신 프로토타입 객체를 사용하라.
         	//만약 인자가 함수이면, 함수의 프로토타입 객체를 사용한다.
            arg = arg.prototype;
         case 'object';													->객체라면: 메서드가 일치하는지를 검사한다.
         	for(var m in arg) {											->객체의 각 프로퍼티에 대해
            	if (typeof arg[m] !== "function") continue;
                if (typeof o[m] !== "function") return false;			->메서드가 아니면 건너뜀.
            }
        }
    }
    //여기까지 이르면 o는 메서드를 모두 구현하고 있다고 할 수 있다.
    return true;
}
```
quacks() 함수와 관련하여 명심해할 두가지 중요한 사항이 있다.
이 함수는 오직 객체에 특성 이름의 함수 프로퍼티가 존재하는지를 검사할 뿐이다. 이 프로퍼티들의 존재는 함수가 무엇을 하는지, 그 함수가 요구하는 인자가 몇개인지, 어떤 자료형인지 알려주지 않는다. 그러나 원래 덕 타이핑이란 그런것이다. 만약 여러분이 강력한 타입 검사 대신 덕 타이핑을 정의 한다면, 더 유연한 API를 만드는 것이다. 하지만, API 사용자에게 API를 정확하게 사용할 책임을 넘기게 된다. 
quacks() 함수와 관련하여 두번째 중요한 점은, 이 함수는 내장 클래스에 대해서는 작동하지 않는다는 것이다. 예를 들어 Array에 있는 메서드와 같은 이름을 가진 메서드가 객체 o에도 있는지를 검사하려 할때, quacks(o, Array)와 같은 코드는 사용할 수 없다. 왜냐하면, 내장 클래스의 메서드는 열거할 수 없어서,quacks() 함수 내의 for/in 루프는 내장클래스의 메서드를 볼 수 없기 때문이다.(ECMAScript 5에서는 Object.getOwnPropertyNames()를 사용함으로써 해결할 수 있다.)
###자바스크립트 객체 지향 기법
#####1. 예제: set 클래스
집합은 중복되지 않은 값을 정렬되지 않은 형태로 저장하는 데이터 구조다. 집합의 필수 연산은 어떤 값이 이미 집합에 있는지를 검사하는 것과 값을 집합에 추가하는 것이고, 일반적으로 이 연산은 빠르게 수행되도록 구현된다. 자바스크립트 객체는 기본적으로 프로퍼티 이름으로 이루어진 집합이다.(그리고 프로퍼티는 각자의 값을 가지고 있다.) 따라서 객체를 문자열의 집합으로 사용하는 것은 대수롭지 않은 일이다.
다음은 Set클래스를 구현하고 있다. 이 클래스는 자바스크립트 값을 고유 문자열로 매핑하고, 이 문자열을 프로퍼티 이름으로 사용한다.객체와 함수에는 단축된 형태로 자신을 확실하게 나타낼 수 있는 고유 문자열 표현이 없기에,Set 클래스는 세트에 저장되는모든 객체와 함수에 프로퍼티 식별자를 정의해야 한다.
```
function Set() {														->생성자 정의
	this.value = {};													->세트를 보관하는 this 객체의 프로퍼티
    this.n = 0;															->세트에 저장된 값의 개수
    this.add.apply(this, arguments);									->모든 인자를 values에 추가한다.
}

//세트로부터 인자를 추가한다.
Set.prototype.add = function() {
	for(var i = 0; i < arguments.length; i++) {							->각 인자에 대해 루프 순회
    	var val = arguments[i];											->세트에 추가될 값이다.
        var str = Set._v2s(val);										->해당값을 문자열로 반환
        if (!this.valus.hasOwnProperty(str)) {							->세트에 해당 값이 없다면,
        	this.values[str] = val;										->문자열과 값을 매핑한다.
            this.n++;													->세트에 크기를 늘린다.
        }
    }
    return this;														->메서드 체이닝 지원.
};

//세트로부터 인자를 제거한다.
Set.prototype.remove = function() {
	for (var i = 0; i < arguments.length; i++) {						->각 인자에 대해 루프순회
    	var str = Set._v2s(arguments[i]);								->문자열로 매핑한다.
        if(this.values.hasOwnProperty(str)) {							->세트에 값이 있으면,
        	delete this.values[str];									->제거한다.
            this.n--;													->세트의 크기를 감소시킨다.
        }
    }
    return this;														->메서드 체이닝 지원.
};

//만약 세트에 지정된 값이 있다면 true를 반환하고 없으면 false를 반환한다.
Set.prototype.contains = function(value) {
	return this.values.hasOwnProperty(Set._v2s(value));
};

//세트의 크기를 반환한다.
Set.prototype.size = function() { retrun this.n; };

//지정된 컨텍스트의 세트에 있는 각 요소에 대해 함수 f를 호출한다.
Set.prototype.foreach = function(f, context) {
	for(var s in this.values)											->세트의 각 문자열 순회
    	if(this.values.hasOwnProperty(s))								->상속된 프로퍼티는 무시한다.
        	f.call(context, this.values[s]);							->값에 대해 f를 호출한다.
};

//이 내부 함수는 모든 자바스크립트 값을 고유 문자열로 매핑한다.
Set._v2s = function(val) {
	switch(val) {
    	case undefined: 	return 'u';									->하나의 문자코드로
        case null: 			return 'n';									->나타낼 수 있는
        case true:			return 't';									->특별한 원시형.
        case false:			return 'f';
        default: switch(typeof val) {
        	case 'number': return '#' + val;							->숫자는 # 접두사를 붙인다.
            case 'string': return '"' + val;							->문자열은 " 접두사를 붙인다.
            default:	   return '@' + objectId(val);					->객체와 함수 앞에는 @를 붙인다.
        }
    }
	//모든 객체에 대해 문자열을 반환한다. 
    //이 함수는 서로 다른 객체에 대해서는 다른 문자열을 반환하고, 같은 객체에 대해서는 언제나 같은 문자열을 반환한다.
	//이를 위해 o에 프로퍼티를 만든다. ECMAScript 5에서 이 프로퍼티는 열거되지 않으며 읽기 전용이 될 수 있다.
	function objectId(o) {
		var prop = "|**objectid**|";									->id에 대한 전용 프로퍼티 이름.
    	if(!o.hasOwnProperty(prop))										->만약 객체에 id가 없다면
   	 		o[prop] = Set._v2s.next++;									->next를 할당한다.
    	return o[prop];													->id를 반환한다.
    }
};
Set._v2s.next = 100;													->객체 id 시작 값으로 100을 할당.
```

#####2. 예제 열거형
열거형은 나열될 수 있는 유한 개의 값 집합을 사용하여 정의하는 자료형이다.
c언어나, 거기서 파생된 언어들은 열거형을 정의할 때 보통 enum 키워드를 사용한다. ECMASript 5에서 enum은 예약어지만 실제로 사용되지는 않는데, 이는 자바스크립트가 언젠가는 내장 열거형을 지원할 가능성을 열어둔 것이라 할 수 있다.
그 시기가 오기 전까지는 예제를 다루는 것처럼 스스로 열거형을 정의하여 사용하면 된다. 예제는 단일 enumration() 함수로 구성되어 있다. 그러나 이 함수는 생성자 함수가 아니고 enumeration 클래스를 정의하지도 않는다. 이 함수는 **팩터리 함수**로, 호출될 때마다 새 클래스를 생성하고 반환한다. 사용법은 다음과 같다.
```
//Coin.Penny, Coin.Nickel등 네 개 값을 가진 Coin 클래스를 생성한다.
var Coin = enumration({Penny: 1, Nickel: 5, Dime: 10, Quarter: 25});
var c = Coin.Dime;														->c는 새 클래스의 인스턴스다.
c instanceof Coin														->true: instanceof는 작동한다.
c.constructor == Coin													->true:constructor 프로퍼티도 작동한다.
Coin.Quarter + 3*Coin.Nickel											->40: 각 값은 숫자로 변환된다.
Coin.Dime == 10															->true: 숫자 변환과 관련한 또 다른 예시
Coin.Dime > Coin.Nickel													->true: 비교 연산도 작동한다.
String(Coin.Dime) + ":" + Coin.Dime										->"Dime: 10": 문자열로 강제 변환한다.
```
주목할 점은 자바스크립트 클래스는 C++이나 자바 같은 언어의 정적 클래스보다 훨씬 유연하고 동적이라는 것이다.
```
//이 함수는 새 열거형을 생성한다. 인자 객체는 열거형의 각 인스턴스에 대한 이름과 값을 지정한다.
//이 함수의 반환 값은 새 클래스를 구별하는 생성자 함수다. 그러나 생성자를 직접 사용하면 예외가 발생한다는 것을 유념하라.
//열거형의 새 인스턴스를 생성하는 데 직접 생성자 함수를 사용할 수 없다. 반환된 생성자의 프로퍼티에는 생성자 그 자체를 가리키는 이름과 값 배열, 그리고 열거 함수 foreach()가 있다.
function enumeration(namesTovalues) {
	//이것은 더미 생성자 함수이고, 이 함수가 반환 값이 된다.
    var enumeration = function() {
    	throw "열거형은 인스턴스화 할 수 없습니다.";
    };
    
    //열거 값은 이 proto 객체를 상속한다.
    var proto = enumeration.prototype = {
    	constructor: enumeration,								->형식 구별을 위해 지정.
        toString: function() { return this.name; },				->name을 반환한다.
        valueOf: function() { return this.value; },				->value를 반환한다.
        toJSON: function() { return this.name; }				->직렬화를 위한 기능.
    };
    
    for(name in namesToValues) {								->각 값들을 순회환다.
    	var e = inherit(proto);									->해당 값을 나타내는 객체를 만든다.
        e.name = name;											->이름을 부여하고
        e.value = nameToValues[name];							->값을 설정한다.
        enumeration[name] = e;									->생성자의 프로퍼티에 이 객체를 지정하고 
        enumeration.values.push(e);								->값 배열에 저장한다.
    }
    
    //인스턴스를 순회하는 클래스 메서드
    enumeration.foreach = function(f,c) {
    	for(var i = 0; i < this.values.length; i++) f.call(c.this.values[i]);
    };
    
    //새로운 형식을 나타내는 생성자를 반환한다.
    return enumeration;
}
```
다음 예제는 카드의 각 세트와 카드 순위를 정의하기 위한 방식으로 enumeration() 함수를 사용하고, 또한 카드와 카드덱을 나타내는 클래스를 정의한다.
```
function Card(suit, rank) {
	this.suit = suit;
    this.rank = rank;
}

//하트, 클럽, 다이아몬드, 스페이드에 대한 각 세트와 순위를 정의한다.
Card.suit = enumeration({Club: 1, Diamonds: 2, Hearts: 3, Spades: 4});
Card.rank = enumeration({Two: 2, Three: 3, Four: 4, Five: 5, Six: 6, Seven: 7, Eight: 8, Nine: 9, Ten: 10, Jack: 11, Queen: 12, King: 13, Ace: 14});

//카드에 대한 텍스트 표현을 정의한다.
Card.prototype.toString = function() {
	return this.rank.toString() + " of " + this.suit.toString();
};

//포커에서처럼 두 카드의 순위를 비교한다.
Card.prototype.compareTo = function(that) {
	if (this.rank < that.rank) return -1;
    if (this.rank > that.rank) return 1;
    return 0;
}

//포커에서처럼 카드를 정렬하는 함수.
Card.orderByRank = function(a,b) { return a.compareTo(b); };

//브릿지에서처럼 카드를 정렬하는 함수.
Card.orderByRank = function(a,b) {
	if (a.suit < b.suit) return -1;
    if (a.suit > b.suit) return 1;
    if (a.rank < b.rank) return -1;
    if (a.rank > b.rank) return 1;
    return 0;
}

//일반적인 트럼프 한 벌을 나타내는 클래스를 정의한다.
function Deck() {
	var cards = this.card = [];
    Card.Suit.foreach(function(s) {
    	Card.Rank.foreach(function(r) {
        	cards.push(new Card(s,r));
        });
    });
}

//섞기(Shuffle) 메서드: 해당 프럼프 한 벌 내의 카드를 섞고 이를 반환한다.
Deck.prototype.shuffle = function() {
	//배열의 각 요소에 대해, 해당 요소의 인덱스보다 앞에 있는 요소 중 아무 요소나 선택하여 바꾼다.
    var deck = this.cards, len = deck.length;
    for(var i = len-i; i > 0; i--) {
    	var r = Math.floor(Math.random()*(i+1)), temp;
        temp = deck[i], deck[i] = deck[r], deck[r] = temp;
    }
    return this;
};

//카드를 분배하는(Deal) 메서드: 카드 배열을 반환한다.
Deck.prototype.deal = function(n) {
	if (this.cards.length < n) throw " Out of cards ";
    return this.cards.splice(this.cards.length-n, n);
};

//트럼프 한 벌을 새로 만들고 섞은 다음, 카드를 분배한다.
var deck = (new Deck()).shuffle();
var hand = deck.deal(13).sort(Card.orderBySuit);
```
#####3.표준 변환 메서드
변환 메서드가 아주 중요하다는 사실을 알고는 있어야 한다. 만약 여러분이 작성한 클래스에 변환 메서드를 구현하지 않았다면, 이는 단순히 실수로 구현하지 않은 것이 아니라 의도적인 것이어야 한다.

먼저, 가장 중요한 메서드는 toString()이다. 이 메서드의 목적은 객체의 문자열 표현을 반환하는 것이다. 자바스크립트는 필요한 때 자동으로 이 메서드를 호출한다. 예를 들자면 프로퍼티 이름이 같이 문자열을 요구하는 곳에 객체를 사용했을 때 또는 문자열 결합을 위해 + 연산자를 사용했을 때 toString() 메서드가 호출된다. 만약 이 메서드를 구현하지 않으면, 클래스는 Object.prototype의 기본 구현을 상속할 것이고, 그 기본 구현 메서드는 별 쓸모 없는 문자열 "[object Object]"을 반환할 것이다. toString() 메서드는 사람이 적절히 읽을 수 있는 문자열을 반환해야 한다. 꼭 그럴 필요가 없다고 해도, toString()을 정의하면 디버깅을 수월하게 할 수 있다. 
toLocalString()은  toString()과 밀접하게 연관이 있다. toLocalString() 메서드는 객체를 로케일에 맞는 문자열로 변환한다. 기본적으로 객체는 toString()메서드를 호출하는 기본 toLocalString() 메서드를 상속한다. 몇몇 내장 자료형은 실제로 로케일에 따른 문자열을 반환하는 toLocalString() 메서드를 갖고 있다. 만약 객체를 문자열로 반환하는 toString()메서드를 작성한다면, 마찬가지로 toLocalString()메서드도 정의하여 해당 객체가 toLocalString()메서드를 통해 변환될 수 있도록 해야 한다. 나중에 Set 클래스에 대해 toLocalString()을 적용해 볼 것이다.
세 번째 메서드는 valueOf()이다. 이 메서드는 객체를 원시 값으로 변환한다. 예를 들면 valueOf()메서드는 객체가 숫자 컨텍스트에서 산술 연산자(+ 제외)나 비교 연산자와 함께 사용될 때 자동으로 호출된다. 객체 대부분은 원시 값으로 변환할 필요가 없으므로, 이 메서드를 정의하지 않는다. 그러나 다음에나올 열거형은 valueOf()메서드가 중요하게 사용되는 경우이다.
네 번째 메서드는 toJSON()이고 JSON.stringify()에 의해 자동으로 호출된다. JSON 형식은 데이터 구조를 직렬화 하는데 사용되고 자바스크립트 원시 값, 배열, 일반 객체를 처리할 수 있다. JSON은 클래스의 자세한 구조를 포함하지 못하기 떄문에, 직렬화할 때 객체의 프로토타입과 생성자는 무시된다. 만약 Range 객체나 Complex객체에 대해 JSON.stringify()를 호출하면, {"from":1, "to":3} 또는 {"r":1, "i":-1}와 같은 값을 얻을 것이다. 만약 이 문자열을 JSON.parse()에 전달하면, Range 객체와 Complex 객체에 대해 적절한 프로퍼티를 가진 일반 객체를 얻겠지만 이 객체가 Range와 Complex 객체의 메서드를 상속하지는 않는다. 만약 다른 방식의 직렬화가 필요하다면 직접 toJSON() 메서드를 작성할 수도 있다.

다음은 Set 클래스에 valueOf() 메서드를 정의하는 것은 상식적이지 않다, 그러나 toString(), toLocalString(), toJSON() 메서드는 구현해야 할 것이다. 아래 코드는 그 사례다. Set.prototype에 메서드를 추가하기 위해 extend()함수를 사용하였다는 것에 유의하자.
```
//Set 프로토타입 객체에 앞에서 다룬 메서드들을 추가한다.
extend(Set.prototype, {
	//세트를 문자열로 변환한다.
    toString: function() {
    	var s = "{", i = 0;
        this.foreach(function(v) { s += ((i++ > 0)?", ":"") + v; });
        return s+ "}";
    },
    //toString()과 비슷하지만 모든 값에 대해 toLocaleString()을 호출한다.
    toLocalString: function() {
    	var s = "{", i = 0;
        this.foreach(function(v) {
        	if(i++ > 0) s += ", ";
            if(v == null) s += v;
            else s += v.toLocalString();
        })
        return s + "}";
    },
    //세트를 배열로 변환한다.
    toArray: function() {
    	var a = [];
        this.foreach(function(v) { a.push(v); });
    }
});

//JSON 문자열 변환을 위해 세트를 배열처럼 취급한다.
Set.prototype.toJSON = Set.prototype.toArray;
```
#####4.비교 메서드
자바스크립트의 동치 연산자들은 객체를 비교할 때, 값이 아니라 참조를 사용한다. 즉, 주어진 두 참조가 같은 객체를 그리키고 있는지를 살펴본다. 이런 연산자들은 두 객체에, 이름이 같고 값도 같은 프로퍼티가 존재하는지를 검사하지 않는다. 두 객체가 동등한지 또는 어느 한쪽이 더 크거나 작은지를 비교할 수 있게 만드는 것도 유용하다. 만약 여러분이 어떤 클래스를 정의하고 이 클래스의 인스턴스를 서로 비교하려면, 비교하는데 사용할 메서드를 정의해야한다.
자바프로그래밍 언어는 객체를 비교하기 위해 메서드를 사용하는데, 이 같은 규칙은 자바스크립트에서도 일반적으로 사용되는 유용한 방법이다. 인스턴스를 동등 비교 하려면, equals()라는 인스턴스 메서드를 정의해야 한다. equals() 메서드는 하나의 인자를 받고, 전달받은 인자와 equals() 메서드를 가진 객체가 같다면 true를 반환한다.같다 라는 것이 어떤 의미인지는 전적으로 우리에게 달렸다. 간단한 클래스라면 constructor 프로퍼티를 이용하여 우선 두 객체의 자료형을 비교하고, 그 다음에는 인스턴스 프로퍼티를 비교하여 같은 값을 가졌는지 살펴보면 될 것이다. Range 클래스에도 비슷한 방식으로 equals()를 구현하자.
```
//Range 클래스는 prototype을 재정의 하면서 constructor 프로퍼티를 정의하지 않았기 때문에, 여기서 constructor 프로퍼티를 추가한다.
Range.prototype.constructor = Range;
//Range는 Range가 아닌 다른 어떤 결과도 같지 않다. 두 Range는 그들의 시작점과 끝점이 같다면 같다.
Range.prototype.equals = function(that) {
	if(that == null) return false;							->null과 undefined는 제외한다.
    if(that.constructor !== Range) return false;			->Range가 아닌 값들도 제외한다.
    //시작점과 끝점이 같을 때만 ture를 반환한다.
    return this.from == that.from && this.to == that.th;
}
```
Set 클래스에 equals()메서드를 정의하는 것은 약간 요령이 필요하다. 두 세트의 프로퍼티 값만 비교하는 것이 아니라 좀 더 깊은 단계의 비교를 해야한다.
```
Set.prototype.equals = function(that) {
	//단순한 경우.
    if (this === that) return true;
    //만약 주어진 객체가 Set이 아니라면 이 객체와 같지 않다.
    //Set의 모든 서브클래스도 허용하도록 instanceof를 사용하지만 진정한 덕 타이핑을 원한다면 이 검사를 완화할 수 있다.
    //형식 검사를 강화할 수도 있다. instanceof는 null과 undefined값 또한 거부한다.
    if (!(that instanceof Set)) return false;
    //두 Set의 크기가 다르면 같지 않다.
    if(this.size() !== that.size()) return false;
    //이 객체의 모든 요소가 다른 객체에도 있는지를 검사한다. 두 Set이 다르면 foreach()를 벗어나도록 예외를 사용한다.
    try {
    	this.foreach(function(v) { if (!that.contains(v)) throw false; )};
        return ture;														->모든 요소가 같음. 두 Set은 같다.
    } catch (x) {
    	if(x === false) return false;										->이 객체의 요소가 다른 객체에는 없음.
        throw x;															->다른 종류의 예외라면 예외를 다시 던짐.
    }
};
```
compareTo() 메서드는 하나의 인자를 받고, 메서드 호출 대상 객체와 인자를 비교한다. 만약 this객체가 인자 객체보다 작으면 compareTo()는 0보다 작은 값을 반환해야 하며, this 객체가 인자보다 크면 0보다 큰값을 반환해야 한다. 만약 두값이 같으면 0을 반환한다.
```
a < b				->a.compareTo(b) < 0
a <= b				->a.compareTo(b) <= 0
a > b				->a.compareTo(b) > 0
a >= b				->a.compareTo(b) >= 0
a == b				->a.compareTo(b) == 0
a != b				->a.compareTo(b) != 0
```

Card 클래스에는 이러한 compareTo() 메서드가 정의되어 있고, 이와 비슷하게 Range 클래스에도 하한 값에 따라 정렬하는 메서드를 정의할 수 있다.
```
Ranbe.prototype.compareTo = function(that) {
	return this.from - that.from
};
```
Range 클래스에서 정의한 compareTo() 메서드는 시작값이 같다면 0을 반환하지만 equals() 메서드는 시작값과 끝값을 비교하는 이 메서드와는 같지 않다. 그래서 가장 좋은 방법은 equal()메서드와 compareTo() 메서드를 일관성 있게 작성하는 것이다. 다음은 Range클래스를 수정한 버전이다. 이 compareTo() 메서드는 equals()와 일관성을 유지할 뿐만 아니라 비교할 수 없는 값을 인자로 전달하면 예외를 발생시킨다.
```
//시작 값에 따라 range 인스턴스를 정렬하고, 만약 시작 값이 같으면 끝 값에 따라 정렬한다.
//만약 Range가 아닌 값이 전달되면 예외를 발생시킨다. 오직 this.equals(that)일 때만 0을 반환한다.
Range.prototype.compareTo = function(that) {
	if(!(that instanceof Range)) throw new Error("Can`t compare a Range with " + that);
    var diff = this - that.from;												->시작 값을 비교한다.
    if(diff == 0) diff = this.to - that.to;										->시작 값이 같으면 끝 값을 비교한다.
    return diff;
};
```
클래스에 comapreTo() 메서드를 정의하는 한 가지 이유는 해당 클래스의 인스턴스들로 구성된 배열을 정렬하기 위함이다. Array.sort() 메서드는 추가 인자로 인스턴스를 비교하는 함수를 받는데, 이 비교 함수는 compareTo() 메서드와 똑같은 반환 값 규칙을 사용한다. 앞서 살펴본 comapreTo()메서드를 이용하면, 다음과 같은 코드를 사용하여 Range 객체 배열을 쉽게 정렬할 수 있다.
```
reanges.sort(function(a,b) { reutnr compareTo(b); });
```
모든 클래스의 이런 식으로 두 인자를 비교하는 함수를 클래스 메서드로 제공하고, compareTo() 함수를 인스턴스 메서드로 제공하는 것을 충분히 고려해볼 만큼 정렬은 중요하다. 다른 비교 메서드 또한 쉽게 정의될 수 있다. 예를 들면 다음과 같다.
```
Range.byLowerBound = function(a,b) { return compareTo(b); };
```
이렇게 정의한 메서드를 사용하여 좀 더 간단히 정렬을 수행할 수 있다.
```
ranges.sort(Range.byLowerBound);
```
어떤 클래스는 두 가지 이상의 방법으로 정렬될 수 있다. 예를 들어, Card 클래스에는 무늬(suit)에 따라 카드를 정렬하는 메서드와 계급(rank)에 따라 정렬한 것은 그 한 사례다.
#####5. 메서드 빌려오기
자바스크립트의 메서드에는 특별한 것이 없다. 단순히 객체의 프로퍼티로 할당된 함수일 뿐이며, 객체를 **통해** 또는 객체를 **대상으로** 호출될 따름이다. 하나의 함수는 두 프로퍼티에 할당될 수 있고, 그런 다음에는 두개의 메서드로 작동한다. Set클래스에서 toArray()메서드를 복사하여 toJSON() 메서드로 사용한 것은 그 한 사례다.
심지어 하나의 함수는 하나 이상의 클래스의 메서드로 사용될 수도 있다. 예를들어, Array 클래스에 있는 내장 메서드 대부분은 충분히 일반화되어 있어서, 만약 여러분이 유사 배열 객체의 클래스를 작성한다면, Array.prototype으로부터 이 클래스의 프로퍼티 객체로 함수를 복사해 넣어도 된다. 전통적인 객체 지향 언어의 관점에서 자바스크립트를 바라보면, 어떤 클래스의 메서드를 다른 클래스의 메서드로 사용하는 것이 다중 상속의 한 형태로 생각될 수도 있다. 그러나 자바스크립트는 전통적인 객체 지향 언어가 아니고, 이런 메서드 재활용 방식을 비공식적인 용어를 사용하여 '메서드 빌려오기' 라고 부르는 것을 선호한다.
Array의 메서드만 이렇게 빌려올 수 있는 것이 아니다. 우리만의 일반화된 메서드도 작성할 수 있다. 다음 예제는 일반화된 toString()메서드와 equals()메서드를 정의하며, 이 메서드들은 Range 클래스, Complex 클래스, Card 클래스와 같이 간단한 클래스에 적합하게 사용될 수 있다. 만약 Range 클래스에 equals() 메서드가 없다면, 다음과 같이 일반화된 equals()메서드를 빌려올 수 있을 것이다.
```
Range.prototype.equals = generic.equals;
```
이 메서드는 오직 얕은 비교만 하며, 어떤 클래스가 equals() 메서드를 재정의한다면, 그런 클래스의 인스턴스들에 generic.equals()를 사용하는 것은 적합하지 않다. 아울러 이 메서드에는 객체가 Set에 삽입될 때 추가되는 프로퍼티를 처리하는 특수코드가 포함되어 있다.
```
var generic = {
	//상속받은 프로퍼티와 함수를 제외한 모든 프로퍼티의 이름과 값을 포함한 문자열을 반환한다.
    //가능하면 생성한 함수의 이름도 포함한다.
    toString: function() {
    	var s = '[';
        //만약 객체가 생성자를 가지고 있고, 생성자에 이름이 지정되어 있다면, 생성자의 이름(클래스이름)을 반환될 문자열에 추가한다.
        //함수의 name 프로퍼티는 비표준이고, 따라서 모든 인터프리터가 name 프로퍼티를 지원하지는 않음을 유념하라.
        if(this.constructor && this.constructor.name)
        	s += this.constructor.name + ": ";
        
        상속되지 않은 프로퍼티와 함수가 아닌 프로퍼티 모두를 열거한다.
        var n = 0;
        for (var name in this) {
        	if (!this.hasOwnProperty(name)) continue;						-> 상속된 값은 건너뛴다.
        	var value = this[name];
            if (typeof value === "function") continue;						-> 메서드도 건너뛴다.
            if (n++) s += ", ";
            s += name + '=' + value;
        }
        return s + ']';
    },
    //this와 that의 constructor 프로퍼티와 instance 프로퍼티를 비교함으로써 this와 that이 같은지를 검사한다.
    //인스턴스의 프로퍼티가 === 연산자를 통해 비교될 수 있는 경우에만 작동한다.
    //=== 연산자로 비교하려면 해당 객체가 원시 값을 반환해야 한다. (valueOf 메서드의 반환 값)
    //특수한 경우 Set 클래스가 추가한 프로퍼티는 무시한다.
    equals: function(that) {
    	if (that == null) return false;
        if (this.constructor !== that.constructor) return false;
        for (var name in this) {
        	if (name === "[**objectid**]") continue;						->특정 프로퍼티는 건너뛴다.
            if (!this.hasOwnProperty(name)) continue;						->상속된 프로퍼티는 건너뛴다.
            if (this[name] !== that[name]) return false;					->값들을 비교한다.
        }
        return ture;														->모든 프로퍼티가 같으면, 두객체는 같다.
    }
};
```
#####6.private 상태
전통적인 객체 지향 프로그래밍에서는 종종 중요한 상태 변수를 직접 읽거나 쓰는 것을 금지하고, 오직 객체의 메서드만을 통해 객체 상태에 접근하도록 허용함으로써 객체의 상태를 캡슐화 하거나 숨기는 것을 목표로 삼는다. 이 같은 목적을 위해 자바와 같은 언어는 "private" 인스턴스 필드를 선언하며, 이 private 필드는 오직 인스턴스 메서드로만 접근할 수도 있고 클래스 외부에는 보이지 않는다.
인스턴스를 생성할 때, 생성자 호출의 클로저에 포착된 변수(혹은 인자)를 사용하면 private 인스턴스 필드를 흉내 낼 수 있다. 그러려면 생성자 내부에서 함수들을 정의하고(따라서 생성자의 인자와 변수에 접근할 수 있다,), 이 함수들을 새로 생성한 객체의 프로퍼티로 할당해야 한다.
다음예제는 이 기법을 사용하여 Range클래스를 캡슐화시킨 것이다. 이from() 메서드와 to()메서드는 Rnage객체마다 정의되고 prototype으로부터 상속되지 않는다. Range의 다른 메서드는 이전 처럼 프로토타입에 정의되지만, 프로퍼티로부터 from, to를 직접 읽는 대신 from() 메서드와 to() 메서드를 호출하도록 수정되었다.
```
function Range(from, to){
	//this 객체의 프로퍼티로 form, to를 저장하지 말 것. 대신에 시작점과 끝점을 반환하는 접근자 함수를 정의한다.
    //인자로 넘어온 from, to 값은 클로저에 저장된다.
    this.from = function() { return from; };
    this.to = function() { return to; };
}
//프로토타입의 메서드들은 생성자에 인자로 전달된 from, to를 직접 볼 수 없다.
//프로토타입의 메서드들은 다른 모든 것과 마찬가지로 접근자 메서드를 호출해야 한다.
Range.prototype = {
	constructor: Range,
    includes: function(x) { return this.from() <= x && x <= this.to(); },
    foreach: function(x) {
    	for(var x = Math.ceil(this.from()), max = this.to(); x <= max; x++) f(x);
    },
    toString: function() { return "(" + this.from() + "..." + this.to() + ")"; }
};
```
새 버전의 Range 클래스에는 범위의 하한과 상한을 질의하는 메서드는 정의되어 있지만, 설정하는 메서드나 프로퍼티는 없다. 이는 인스턴스에 일종의 불변성을 제공하는데, 제대로 사용된다면 Range 인스턴스의 하한과 상한의 그 인스턴스가 생성된 이후에는 변하지 않을 것이다. 그러나 ECMAScript 5의 기능을 사용하지 않는 한, from 프로퍼티와 to 프로퍼티는 여전히 덮어 쓰일 수 있고, 따라서 Range 객체가 실제로 불변인 것은 아니다.
```
var r = new Range(1,5);						->불변 범위
r.from = function() { return 0; };			->메서드를 교체함으로써 변형된다.
```
이러한 캡슐화 기법에는 오버헤드가 있다는 것을 염두에 두자. 상태를 캡슐화하도록 클로저를 사용하는 클래스는 그렇지 않은 클래스보다 확실이 느리고 크다.
#####7.생성자 오버로딩과 팩터리 메서드
때로는 객체를 한 가지 이상의 방법으로 초기화 해야할 필요가 있다. 예를 들어 Complex 객체를 실수와 허수 대신 반경과 각도(극좌표)로 초기화한다거나, Set의 맴버를 생성자의 인자 대신 배열 원소들로 초기화 한다거나 하는 경우다.
이를 위한 한 가지 방법은 생성자를 오버로드하고, 전달인자에 따라 각기 다른 초기화 작업이 수행되도록 하는 것이다. 다음은 Set 생성자의 오버로드 버전이다.
```
function Set() {
	this.value = {};								->세트에 보관하는 this 객체의 프로퍼티.
    this.n = 0;										->세트에 얼마나 많은 값이 저장되어 있는지를 나타낸다.
    
    //하나의 유사 배열 객체가 전달되면, 그 요소들을 세트에 추가한다. 그렇지 않으면 모든 인자를 세트에 추가한다.
    if (arguments.length == 1 && isArrayLike(arguments[0]))
    	this.add.apply(this, arguments[0]);
    else if (arguments.length > 0)
    	this.add.apply(this, arguments);
}
```
이런 방식으로 Set() 생성자를 정의하면, 생성자를 호출할 떄 명시적으로 집합 원소들을 나열하거나 원소들의 배열을 생성자로 전달할 수 있다. 그러나 생성자에는 불행하게도 모호함이 있다. 맴버가 배열 하나뿐인 집합은 이 생성자를 사용하여 생성할 수 없는 것이다.(이를 가능하게 하려면 빈 집합을 만들고 add()메서드를 명시적으로 호출해야 한다.)
극좌표로 초기화되는 복소수의 경우에는, 생성자 오버로딩이 아예 불가능하다. 극좌표건 실수-허수 표현이건 두개의 부동소수점 인자를 사용하기 때문에, 세 번째 인자를 추가하지 않는 한 인자만으로는 어떤 표현이 원하는 것인지를 판단할 방법이 업식 때문이다. 대신 팩터리 메서드를 사용할 수 있는데, 팩터리 메서드는 해당 클래스의 인스턴스를 반환하는 클래스 메서드다. 다음은 극좌표를 사용하여 초기화된 Complex 객체를 반환하는 팩터리 메서드다.
```
Complex.polar = function(r, theta) {
	return new Complex(r*Math.cos(theta), r*Math.sin(theta));
};
```
다음은 배열을 사용하여 집합을 초기화하는 팩터리 메서드다.
```
Set.fromArray = function(a) {
	s = new Set();											-> 빈 세트를 만든다.
    s.add.spply(s, a);										-> 배열 a의 요소를 add 메서드에 전달한다.
    return s;												-> 세트를 반환한다.
}
```
팩터리 메서드의 매력은 메서드 이름을 원하는 대로 지을 수 있다는 점이고, 이름이 다른 메서드는 다른 방식의 초기화를 하도록 할 수 있다는 점이다. 그렇다 하더라도 생성자가 클래스를 대표하는 식별자 역할을 하기 때문에 일반적으로는 클래스마다 하나의 생성자를 둔다. 하지만 이것은 엄격히 따라야할 규칙은 아니다. 자바스크립트에서는 하나의 프로토타입 객체를 공유하는 생성자 함수를 여러 개 정의할 수 있다. 그리고 만약 이렇게 한다면, 이떤 생성자 함수를 사용하건 생성된 객체는 같은 자료형이 될 것이다. 다음은 이러한 형식의 보조생성자이지만, 이러한 기법이 권장되지 않는다.
```
//Set 클래스의 보조 생성자.
function SetFromArray(a){
	//인자 a의 요소를 개별 인자로 전달하여, Set()을 함수로 호출하여 새 객체를 초기화 된다.
    Set.apply(this, a);
}

//프로토타입을 설정함으로써 SetFromArray가 Set의 인스턴스를 생성하게 한다.
SetFromArray.prototpye = Set.prototype;

var s = new SetFromArray([1,2,3]);
s instanceof Set											-> true
```
ECMAScript 5에서는 함수의 bind() 메서드를 사용하여 이러한 종류의 보조 생성자를 만들 수 있다.

###서브 클래스
객체 지향 프로그래밍에서 클래스 B는 다른 클래스 A를 확장(extend)하거나 클래스 A의 하위 클래스가 될 수 있다. 이런 경우, 클래스 A를 슈퍼클래스라 하고 클래스 B를 서브클래스 라고 한다. 클래스 B의 인스턴스는 클래스 A의 모든 인스턴스 메서드를 상속한다. 클래스 B는 자신만의 인스턴스 메서드를 가질 수 있고, 그중 몇 가지는 클래스 A로부터 상속받은 메서드를 똑같은 이름으로 재정의 할 수도 있다.
클래스 B의 메서드가 클래스 A의 메서드를 재정의했을 때, 클래스 B의 재정의된 메서드에서 클래스 A의 원래 메서드를 호출할 수가 있는데, 이는 메서드 체이닝이라고 한다. 서브 클래스는 또한 자신의 서브클래스를 가질 수 있다.
클래스 계층 구조를 사용할 때는 추상클래스를 정의하는 것이 유용한 경우도 있다. 추상 클래스는 실제로는 구현되지 않은 추상 메서드가 하나 이상있는 클래스다. 이 추상 메서드의 실제 구현은 추상 클래스를 상속한 서브클래스가 담당한다.
자바스크립트에서 서브클래스를 만드는 핵심 프로토타입 객체를 적절하게 초기화하는 것이다. 만약 클래스 B가 클래스 A를 확장한다면 B.prototype은 반드시 A.prototype을 상속해야 한다. 그래야, 클래스 B의 인스턴스가 B.prototype을 상속하고 차례로 A.prototype을 상속하게 된다. 이번 절은 서브클래스와 관련하여 앞에서 언급한 각 용어를 다루고, 또한 서브클래스의 대안으로 조합에 대해서도 다뤄본다.
#####1. 서브클래스 정의
자바스크립트 객체는 클래스의 프로토타입 객체로부터 프로퍼티(보통 메서드)를 상속한다. 만약 객체 O가 클래스 B의 인스턴스이고 클래스 B가 클래스 A의 서브클래스라면, 객체 O는 틀림없이 클래스 A의 프로퍼티 또한 상속하고 있다. 클래스 B의 프로토타입 객체가 클래스 A의 프로토타입 객체를 상속했다는 것을 확실히 함으로써 이를 정리할 것이다. 이를 위해 inherit() 함수를 사용하여 다음과 같이 코드를 작성할 수 있다.
```
B.prototype = inherit(A.prototype);							->서브 클래스는 슈퍼클래스를 상속한다.
B.prototype.constructor = B;								->상속된 constuctor 프로퍼티를 재정의한다.
```
두 줄의 코드는 자바스크립트에서 서브클래스를 만드는 핵심이다. 이러한 프로토타입 상속이 없다면 프로토타입 객체는 Object.prototype을 상속받은 평범한 객체일 뿐이고, 이는 여러분이 만든 클래스는 다른 모든 클래스처럼 Object의 서브클래스가 된다는 것을 뜻한다.
defineClass() 함수에 위의 두줄을 추가하면, 다음예제 defineSubclass() 함수와 Function.prototype.extend()메서드로 변환할 수 있다.
```
//간단한 서브클래스를 생성하는 함수.
function defineSubclass(superclass,												->슈퍼클래스의 생성자
						constructor,											->새 클래스의 생성자
                        methods,												->인스턴스 메서드: prototype으로 복사됨
                        statics)												->클래스 프로퍼티: 생성자로 복사됨
{
	//서브클래스의 프로토타입 객체를 설정한다.
    constructor.prototype = inherit(superclass.prototype);
    constructor.prototype.constructor = constructor;
    //일반적인 클래스로 다룰 수 있도록 메서드와 정적 값들을 복사한다.
    if (methods) extend(constructor.prototype, methods);
    if (statics) extend(consructor, statics);
    //클래스를 반환한다.
    return constructor;
}
//또한,슈퍼클래스 생성자의 메서드로 서브클래스를 생성할 수 있다.
Function.prototype.extend = function(constructor, methods, statics) {
	return defineSubclass(this, constructor, methods, statics);
};
```
다음 예제는 defineSubclass()함수를 사용하지 않고 직접 서브클래스를 작성한다. 이 예제는 Set의 서브클래스로 SingletonSet을 정의한다. 이 클래스는 읽기 전용이고 하나의 상수 맴버만 가진 특별한 집합이다.
```
//생성자 함수.
function SingletonSet(member) {
	this.member = member;
}
//Set의 프로토타입을 상속한 프로토타입 객체를 생성한다.
SingletonSet.prototype = inherit(Set.prototype);

//프로토타입에 프로퍼티를 추가한다. 이 프로퍼티들은 Set.prototype에 있는 같은 이름을 가진 프로퍼티를 재정의한다.
extend(SingletonSet.prototype, {
	//constructor 프로퍼티를 SingletonSet에 적합하게 설정한다.
    consturctor: SingletonSet,
    //이 세트는 읽기 전용이다. add()와 remove()는 에러를 발생시킨다.
    add: function() { throw "read-only set"; },
    remove: function() {throw "read-only set"; },
    //SingletonSet의 크기는 언제나 1이다.
    size: function() { return 1; },
    //맴버가 하나만 있기 때문에 함수를 한 번만 호출하면 된다.
    foreach: function(f, context) { f.call(context, this.member); },
    //contains() 메서드는 간단하다. 한 가지 값에 대해서만 참이다.
    contains: function(x) { return x === this.member; }
});
```
이 클래스는 Set의 다섯 개 핵심 메서드를 구현하고 있지만, toString(), toArray(), equals() 같은 함수들은 슈퍼클래스로부터 상속한다. 이러한 메서드 상속이 서브클래스를 정의하는 이유다. 예를 들면, Set 클래스의 equals()메서드는 제대로 동작하는 size()와 foreach() 메서드가 있는 Set 객체를, 역시 제대로 동작하는 Size()와 contains() 메서드가 있는 Set 객체와 비교할 수 있다. SingletonSet은 Set의 서브클래스이기 때문에 이런 equals() 구현을 자동으로 상속하고 별도로 equals() 메서드를 따로 작성할 필요가 없다. 물론 하나의 맴버만 취급하는 SingletonSet의 특성상, SingletonSet에는 다음과 같이 equals()를 별도로 정의하는 것이 더 효율적일 것이다.
```
SingletonSet.prototpye.equals = function(that) {
	return that instanceof Set && that.size() == 1 && that.contains(this.member);
};
```
SingletonSet은 Set의 메서드를 정적으로 빌려오지 않는다는 점을 유념하라. 동적으로 Set클래스의 메서드를 상속하는 것이다.
따라서 Set.prototpye에 새 메서드를 추가하면 그 즉시 Set과 SingletonSet의 모든 인스턴스는 추가한 메서드를 사용할 수 있다.(Set.prototpye에 새로 추가한 메서드와 이름이 같은 메서드가 SingletonSet에 없다고 가정한다.)
#####2.생성자 체이닝과 메서드 체이닝
앞 절의 SingletonSet 클래스는 완전히 새로운 집합을 구현하여, 슈퍼클래스로 부터 상속한 핵심 메서드 들을 완전히 교체하였다. 그러나 서브클래스를 정의할때 종종 메서드를 완전히 교체하지 않고, 오직 슈퍼클래스 메서드의 행위를 확장하거나 수정만 하고 싶을 때가 있다. 이를 위해 서브클래스의 생성자와 메서드는 슈퍼클래스의 생성자와 메서드를 호출, 즉 체이닝 해야 한다.
다음 예제가 그 사례이고, 이 예제는 NonNullSet이라는 Set의 서브클래스를 정의하는데, NonNullSet은 null과 undefined값을 맴버로 허용하지 않는다. add는 이와같은 값을 테스트할 필요가 있다. 하지만 add() 메서드를 완전히 재구현하고 싶지는 않기 때문에, 슈퍼클래스의 add() 메서드를 체이닝 할 것이다.또한 NonNullSet() 생성자는 NonNullSet만을 위한 어떤 행동도 취하지 않는다. NonNullSet() 생성자는 슈퍼클래스 생성자가 새로운 객체를 초기화 할 수 있도록 인자를 슈퍼클래스의 생성자에 전달하기만 할 뿐이다.
```
//NonNullSet은 null과 undefined를 맴버로 허용하지 않는 Set의 서브클래스다.
function NonNullSet() {
	//NonNullSet을 위한 별도의 동작 없이, 단지 슈퍼클래스의 생성자를 체이닝 한다.
    //이 생성자 호출에 의해 생성된 객체를 초기화하기 위해 슈퍼클래스의 생성자를 일반 함수처럼 호출한다.
    Set.apply(this, arguments);
}
//Set의 서브클래스인 NonNullSet을 만든다.
NonNullSet.prototype = inherit(Set.prototype);
NonNullSet.prototpye.constructor = NonNullSet;

//null과 undefined를 제외하려면, add() 메서드만 재정의하면 된다.
NonNullSet.prototype.add = function() {
	//인자가 null 또는 undefined인지 여부를 검사한다.
    for( var i = 0; i < arguments.length; i++)
    	if (arguments[i] == null) throw new Error("null 또는 undefined는 NonNullSet에 추가할 수 없습니다.");
    
    //실제 맴버 삽입은 슈퍼클래스의 메서드를 체이닝 하여 수행한다.
    return Set.prototype.add.apply(this, arguments);
};
```
null을 허용하지 않는 집합이라는 개념을 '필터 적용 집합'으로 일반화 해보자. 이 집합에 원소가 추가되려면, 그원소는 반드시 필터 함수를 거쳐야 한다. 이제 클래스 팩터리 함수를 정의할 텐데, 이 함수는 필터 함수를 인자로 받고 새 Set 서브클래스를 반환한다. 사실 여기서 좀 더 일반화를 할 수 있는데, 클래스 팩터리 함수가 두 개의 인자를 받도록 정의하는 것이다. 첫 인자는 서버클래스를 만들 클래스이고, 두 번째 인자 add() 메서드에 적용될 필터다. 이 팩터리 함수를 filterSetSubclass()로 부르자. 사용법은 다음과 같다.
```
//문자열만 저장하는 Set 클래스를 정의한다.
var StringSet = filteredSetSubclass(Set, function(x) { return typeof x==="string"; });

//null, undefined 그리고 함수를 허용하지 않는 Set 클래스를 정의한다.
var MySet = filteredSetSubclass(NonNullSet, function(x) { return typeof x !== "function"; });
```
이 클래스 팩터리 함수의 코드는 다음에 나와있다. NonNullSet에서 다루었던 생성자 체이닝과 메서드 체이닝을 클래스 팩터리 함수가 어떻게 수행하는지 살펴보자.
```
//이 함수는 Set 서브클래스의 add() 메서드에 지정된 필터를 적용하여 재정의된 클래스를 반환한다.
function filteredSetSubclass(superclass, filter) {
	var constructor = function() {
    	superclass.apply(this, arguments);
    };
    var proto = constructor.prototype = inherit(superclass.prototype);
    proto.constructor = constructor;
    proto.add = function() {
    	//값을 추가하기 전에 모든 인자에 대해 필터를 적용한다.
        for (var i = 0, i < arguments.length; i++) {
        	var v = arguments[i];
            if (!filter(v)) throw("value " + v + " rejected by filter");
        }
        //슈퍼 클래스의 add 메서드에 체이닝 한다.
        superclass.prototype.add.apply(this, argument);
    };
    return constructor;
}
```
위의 예제의 흥미로운 점은 서브클래스를 생성하는 코드를 함수로 래핑함으로써, 서브클래스 생성자에서 superclass 인자를 사용할 수 있게 되었고, 실제 하드 코딩된 슈퍼클래스 이름 대신 메서드 체이닝 코드를 사용할 수 있따는 점이다. 이는, 만약 여러분이 슈퍼클래스를 변경하고 싶다면 코드에서 슈퍼클래스가 나오는 모든 위치를 찾아 바꾸지 않고, 한 부분만 변경하면 된다는 뜻이다.
이것은 클래스 팩터리 이외의 경우에도 사용할 가치가 있는 기법이다. 일례로, 래퍼 함수와 Function.prototype.extends() 메서드를 사용하면 NonNullSet을 다음과 같이 재작성할 수 있다.
```
NonNullSet = (function() {
	var superclass = Set;
    return superclass.extend(
    	function() { superclass.apply(this, arguments); },
        {
        	add : function() {
            	//인자 null또는 undefined 여부를 검사한다.
                for(var i = 0; i < arguments.length; i++)
                	if( arguments[i] == null) throw new Error("null 또는 undefined는 추가할 수 없습니다.");
                //실제 맴버 삽입은 슈퍼클래스의 메서드를 체이닝하여 수행한다.
                return superclass.prototype.add.apply(this, arguments);
            }
        });
}());
```
강조하건대, 이런 형태의 클래스 팩터리를 구현할 수 있는 것은 자바스크립트의 동적인 특성 때문이다. 자바나 C++같은 언어에는 클래스 팩터리와 같은 강력하고 유용한 기능이 없다.
#####3.조합 대 서브클래스
지금까지 특정 조건에 맞는 원소만 허용하는 집합을 정의하기 위해 특정 필터 함수를 사용하여 집합 원소의 자격을 제한하는 서브클래스를 만들었다. 이 경우, 슈퍼클래스는 하나이더라도 **필터의 종류가 달라지면 새로운 클래스를 생성**해야 한다. 그러나 좀 더 나은 방법이 있다. 객체 지향 설계에서 잘 알려진 원칙으로, '상속보다는 조합을 선호하라' 라는 것이다. 조합을 사용하면, 어떤 집합 객체를 감싸는 새로운 집합 객체를 만들어, 조건에 맞지 않는 객체에 대한 삽입 요청을 거른 뒤에 안쪽 집합 객체에 전달함으로써 목적을 달성할 수 있다.
```
//FilteredSet은 지정된 세트 객체를 래핑하고, 지정된 필터를 add() 메서드의 인자에 적용한다.
//세트의 다른 모든 핵심 메서드는 래핑된 세트 인스턴스 전달한다.
var FiletredSet = Set.extend(
	function FilteredSet(set, filter) {								->생성자
    	this.set = set;
        this.filter = filter;
    },
    {																->인스턴스 메서드
    	add: function() {
        	if(this.filter) {										->필터설정시 작동한다.
            	for(var i = 0; i < argumetns.length; i++) {
                	var v = arguments[i];
                    if(!this.filter(v))
                    	throw new Error("FilteredSet: value " + v + "filter에 의해 거부됨");
                }
            }
            this.set.add.apply(this.set, arguments);				->add() 메서드에 대한 요청을 this.set.add()로 전달
            return this;
        },
        //나머지 메서드들은 this.set으로 요청을 전달하기만 하고 다른행동은 하지 않는다.
        remove: function() {
        	this.set.remove.apply(this.set, arguments);
            return this;
        },
        contains: function(v) { return this.set.contains(v); },
        size: function() { return this.set.size(); },
        foreach: function(f,c) { this.set.foreach(f,c); }
});
```
이렇게 조합을 사용했을 때의 한 가지 이점은, 서브클래스는 FilteredSet 하나만 있으면 된다는 것이다. 이 클래스의 인스턴스를 이용하여 다른 집합 인스턴스의 원소 자격을 제한할 수 있는 것이다. 예를들어, 앞서 정의한 NonNullSet은 아래의 코드로 대신할 수 있다.
```
var s = new FilteredSet(new Set(), function(x) { return x !== null; });
```
심지어, 이미 필터가 적용된 집합을 필터링하는 또 다른 집합도 만들어 낼 수 있다.
```
var t = new FilteredSet(s, { function(x) { return !(x instanceof Set); }};)
```
#####4.클래스 계층 구조와 추상 클래스
앞에서는 '상속보다는 조합을 선호하라'고 했지만, 이원칙을 설명할 때에는 상속을 활용했다. 그 결과로, FilterSet 클래스는 instanceof Set이며, toString()과 equals() 같은 Set의 유용한 보조 메서드들을 상속받을 수 있었다. 충분히 실용적인 이유에서 였지만, Set 클래스와 같이 구체적 구현을 담은 클래스를 서브 클래싱 하지 않고도 조합을 적용할 수 있었다면 더 좋았을 것이다. 이전 예제의 SingletonSet 클래스도 같은 관점에서 볼 수 있는데, SingletonSet 클래스는 Set의 서브클래스고 보조 메서드들을 상속받을 수 있지만, 그 구체적인 구현은 슈퍼클래스와는 완전히 다르다. 사실상 SingletonSet은 Set 클래스의 특별한 버전이라기보다는 완전히 다른 Set이다. SingletonSet은 클래스 계층 구조에서 볼때 Set의 자손이 아니라 형제여야 한다.

전통적인 객체 지향 언어에서와 마찬가지로 자바스크립트에서의 해법 또한 구현과 인터페이스를 분리하는 것이다. toString() 같은 보조 메서드는 구현하지만 foreach()같은 핵심 메서드는 구현하지 않은 abstractSet 클래스를 정의한다고 가정해보자. 이제 Set, SingletonSet, FilteredSet등의 집합 구현체는 모두 AbstractSet의 서브클래스가 될 수 있다. FilteredSet과 SingletonSet은 더는 자신들과 관련이 없는 슈퍼클래스 구현체를 상속하지 않는다.
다음 예제는 한 단계 더 나아가서 추상 집합 클래스의 계층 구자를 정의한다. AbstractSet에는 오직 하나의 추상 메서드인 contains()만이 정의되어 있다. Set이 되려는 모든 클래스는 적어도 이 contains() 메서드는 정의해야 한다. AbstractSet을 상속한 AbstractEnumerableSet에는 추상 메서드 size()와 foreach()가 추가되어 있고, 그 메서드들을 이용하는 구체 메서드들(toString(), toArray(), equals() 등) 도 정의되어 있다. AbstractEnumerableSet은 읽기전용 집합이어서 add() 메서드와 remove() 메서드는 정의되어 있지 않다.SingletonSet은 구체적인 서브클래스 AbstractWritableSet은 추상 메서드 add()와 remove()를 정의하고, 그 메서드 위에서 구현된 union()과 intersection() 같은 구체 메서드도 정의하고 있다. AbstractWritableSet은 Set과 FilteredSet 클래스에 적합한 슈퍼클래스다. 이 예제에서는 AbstractWritableSet을 상속하여 Set과 FilteredSet을 구현하는 것은 생략하였지만, 대신 ArraySet이라는 새로운 구체 클래스를 포함했다.
서브클래스를 만들 때 Function.prototype.exteds를 사용한 것을 주목하자
```
//모든 추상 메서드에서 편의상 사용하는 메서드.
function abstractmethod() { throw new Error("추상 메서드를 호출하였습니다."); }

//AbstractSet 클래스는 추상 메서드 contains() 하나만 정의한다.
function AbstractSet() { return new Error("추상 클래스는 인스턴스화 할 수 없습니다."); }

AbstractSet.prototype.contains = abstractmethod;

//NotSet은 AbstractSet을 구현한 클래스다. 이 집합에 속한 맴버는 다른 집합의 맴버가 될 수 없다.
//NotSet은 다른 집합으로부터 정의되기 때문에 맴버를 추가할 수 없고, 무한한 맴버를 가지고 있기 때문에 열거될 수도 없다.
//NotSet에 대해서 할 수 있는 것은 오직 특정 값에 대한 맴버 자격 여부 검사뿐이다.
//이 서브클래스를 정의하려 앞서 정의한 Function.prototype.extend() 메서드를 사용한다.
var NotSet = AbstractSet.extend(
	function NotSet(set) { this.set = set; },
    {
    	contains: function(x) { return !this.set.contains(x); },
        toString: function(x) { return "~" + this.set.toString(); },
        equals: funcion(that) { return that instanceof NotSet && this.set.equals(that.set); }
    }
);

//AbstractEnumerbleSet은 AbstractSet의 추상 서브클래스다.
//이 클래스는 추상 메서드 Size(), foreach()를 정의하고, 구체 메서드인 inEmpty(), toArray(), to[Locale]String(), equals()를 정의한다.
//이 클래스의 서브클래스에서 contains(), size(), 그리고 foreach()를 구현할 때, 이 다섯가지 구체 메서드를 바로 사용할 수 있다.
var AbstractEnumerableSet = AbstractSet.extend(
	function() { return new Error("추상 클래스는 인스턴스화할 수 없습니다."); },
    {
    	size: abstractmethod,
        foreach: abstractmethod,
        isEmpty: function() { return this.size() == 0; },
        toString: function() {
        	var s = "{", i = 0;
            this.foreach(function(v) {
            	if(i++ > 0) s += ", ";
                s += v;
            });
            return s + "}";
        },
        toLocaleString: function() {
        	var s = "{", i = 0;
            this.foreach(function(v){
            	if(i++ > 0) s += ", ";
                if(v == null) s += v;								->null & undefined인 경우.
                else s += v.toLocaleString();						->이 외의 경우.
            });
            return s + "}";
        },
        toArray: function() {
        	var a = [];
            this.foreach(function(v) { a.push(v); });
        },
        equals: function(that) {
        	if (!(that instanceof AbstractEnumerableSet)) return false;
            //만약 크기가 같지 않다면, 두 집합은 다르다.
            if (this.size() != this.size()) return false;
            //this의 모든 요소가 that에도 있는지 검사한다.
            try {
            	this.foreach(function (v) {if (!that.contains(v)) throw false; });
                return true;										->모든 요소가 같음. 두 집합음 같음.
            } catch (x) {
            	if (x === false) return false;						->두 집합은 같지 않다.
                throw x;											->다른 예외가 발생하면 외부로 예외를 전달한다.
            }
        }
    }
);

//SingletonSet은 AbstractEnumerableSet의 구체 서브클래스다. SingletonSet 인스턴스는 하나의 맴버만 가진 읽기 전용 세트다.
var SingletonSet = AbstractEnumerableSet.extend(
	function SingletonSet(member) { this.member = member; },
    {
    	contains: function(x) { return x === this.member; },
        size: function() { return 1; },
        foreach: function(f,ctx) { f.call(ctx, this.member); }
    }
);

//AbstractWritableSet은 AbstractEnumerableSet의 추상 서브클래스다.
//이 클래스는 추상 메서드 add()와 remove()를 정의하고, 이 추상 메서드들을 사용하는 구체 메서드 union(), intersection(), difference()를 구현한다.
var AbstractWritableSet = AbstractEnumerableSet.extend(
	function() { throw new Error("추상 클래스는 인스턴스화 할 수 없습니다."); },
    {
    	add: abstractmethod,
        remove: abstractmethod,
        union: function(that) {
        	var self = this;
            that.foreach(function(v) { self.add(v); });
            return this;
        },
        intersection: function(that) {
        	var self = this;
            this.foreach(function(v) { if (!that.contains(v) self.remove(v); });
        },
        difference: function(that) {
        	var self = this;
            that.foreach(function(v) { self.remove(v); });
        }
    }
);

//ArraSet 은 AbstractWriatableSet의 구체 서브클래스다.
//이 클래스는 집합의 원소를 배열로 나타내고, contains() 메서드는 배열에 대해 선형 검색을 한다.
//contains() 메서드는 0(1)이 아니라 0(n)이기 때문에, 상대적으로 작은 집합에서만 사용되어야 한다.
//ArraySet의 구현은 ECMAScript 5의 Array 메서드인 indexOf()와 forEach()에 의존하고 있다는 점이다.
var ArraySet = AbstractWritableSet.extend(
	function ArraySet() {
    	this.values = [];
        this.add.apply(this, arguments);
    }
    {
    	contains: function(v) { return this.values.indexOf(v) != -1; },
        size: function() { return this.values.length; },
        foreach: function(f,c) { this.values.forEach(f, c); },
        add: function() {
        	for(var i = 0; i < arguments.length; i++) {
            	var arg = arguments[i];
                if (!this.contains(arg)) this.values.push(arg);
            }
            return this.;
        },
        remove: function() {
        	for(var i = 0; i < arguments.length; i++) {
            	var p = this.values.indexOf(arguments[i]);
                if(p == -1) continue;
                this.values.splice(p, 1);
            }
            return this;
        }
    }
);
```
###ECMAScript 5 클래스
ECMAScript 5에는 프로퍼티 속성(getter, setter, 열거여부, 쓰기여부, 설정여부)을 지정하는 메서드와 객체 확장을 제한하는 메서드가 추가되었다.
#####1. 열거되지 않는 프로퍼티 만들기
Set 클래스는 어떤 객체를 집합 원소로 저장하기 위해 집합에 추가되는 모든 객체에 'object id'프로퍼티를 추가하는 트릭을 사용했다. 만약 나중에 다른 코드가 for/in 루프에서 이 객체를 사용하면, 임의로 추가한 'object id' 프로퍼티로도 열거될 것이다. ECMAScript 5 에서는 해당 프로퍼티를 열거할 수 없도록 하여 이런 상황을 피할 수 있다. 다음 예제는 Object.defineProperty()를 사용하여 프로퍼티를 열거할 수 없게 하는 방법을 보여준다. 또한 getter 함수 여부, 객체 확장 기능 여부를 어떻게 테스트할 수 있는지도 다루고 있다.
```
//코드를 함수로 둘러싸서 함수 유효범위 안에서만 변수를 정의할 수 있다.
(function () {										->자기 호출 함수 : 해석과 동시에 실행되는 코드블럭
	//모든 객체가 상속하는 objectId 프로퍼티를 정의하지만, 이 프로퍼티는 열거되지 않는다.
    //이 프로퍼티가 읽힐 때 getter 함수가 호출되는데 setter 함수는 없기 때문에 이 프로퍼티는 읽기 전용이다.
    //또한 프로퍼티 속성을 설정 할 수 없기 떄문에, 이 프로퍼티는 임의로 삭제될 수 없다.
    Object.defineProperty(Object.prototype, "objectId", {
    									get: idGetter,						->값을 얻기 위한 메서드.
                                        enumerable: false,					->열거되지 않음.
                                        configurable: false});				->삭제할 수 없음.
    
    //이 함수는 objectId가 읽힐 때 호출되는 getter 함수다.
    function idGetter() {													->id를 반환하는 getter 함수.
    	if(!(idprop in this)) {												->만약 객체에 id가 없고
        	if(!object.isExtensible(this)) 									->만약 객체를 확장할 수 없다면,
            	throw Error("Can`t define id for nenextensible objects");
            object.defineProperty(this, idprop, {							->idprop을 생성한다.
            					value: nextid++,							->idprop의 값.
                                writable: false,							->읽기 전용을 나타냄
                                enumerable: false,							->열거되지 않음.
                                configurable: false});						->프로퍼티를 삭제할 수 없음.
        }
        return this[idprop];										->기존 프로퍼티 값이나 새로 만든 프로퍼티 값을 반환한다.
    };
    //다음 변수들은 idGetter()에서 사용되고 이 함수에서만 유효하다.
    var idprop = "|**objectId**|";											->이런 프로퍼티 이름은 사용되지 않는다 가정한다.
    var nextid = 1;															->시작 id 값을 지정.
}());														-> 코드를 바로 실행하기 위해 래퍼 함수를 호출.
```
#####2. 변경되지 않는 클래스 정의하기
변경되지 않는 인스턴스를 만드는 클래스를 정의할 때 편리하다. 다음 예제는 Range 클래스의 불변 버전으로서, Object.definedProperties()와 object.create()를 사용하여 불변성을 구현한다. 또한, Range클래스는 Object.defineProperties()를 사용하여 Range 클래스의 프로토타입 객체를 만들고, 인스턴스 메서드를 열거되지 않도록 변경한다. 그리고 거기서 더 나아가 인스턴스 메서드들을 읽기 전용으로 설정하고, 삭제될수 없게 하며, 실행 중에 대체 될 수 없도록 한다. 마지막으로 한가지 흥미로운 점은, 이 예제의 생성자 함수는 new 키워드 없이 호출하면 팩터리얼 함수로 동작한다는 것이다.
```
//이 함수는 new 키워드를 사용하면 생성자로, 그렇지 않으면 팩터리 함수로 작동한다.
function Range(from, to) {
	//다음은 읽기 전용 프로퍼티인 from과 to에 대한 프로퍼티 디스크립터 객체다.
    var props = {
    	from: {value:from, enumerable: true, writable:false, configurable: false},
        to: {value:from, enumerable: true, writable:false, configurable: false}
    };
    if(this instanceof Range)									->이 함수가 생성자로 호출되면
    	Object.definePropertyies(this, props);					->this 객체에 프로퍼티를 정의한다.
    else														->그렇지 않으면 팩터리 함수이고,
    	return Object.create(Range.prototype, props);			->새로운 Range 객체를 생성하고 props를 설정한 다음 반환한다
}
//같은 방식으로 Range.prototype 객체의 프로퍼티에도 프로퍼티 속성을 설정할 수 있다.
//다음 코드에서는 enumerable, writable, configurable 속성을 지정하지 않았기 때문에,
//그 값들은 기본적으로 모두 false이다.
object.defineProperties(Range.prototype, {
	includes: {
    	value: function(x) { return this.from <= x && x <= this.to; }
    },
    foreach: {
    	value: function(f) {
        	for(var x = Math.ceil(this.from); x <= this.to; x++) f(x);
        }
    },
    toString: {
    	value: function() { return "(" + this.from + "..." + this.to + ")"; }
    }
});
```
위 예제에서 변경할 수 없고 열거되지 않는 프로퍼티를 정의하기 위해 Object.defineProperties()와 Object.create()를 사용했다. 이 메서드들은 강력하지만, 해당 메서드에 전달해야 하는 프로퍼티 디스크립터 객체는 코드를 읽기 어렵게 만들수 있다. 대안은 이미 정의된 프로퍼티 속성을 수정할 수 있는 유틸리티 함수를 정의하는 것이다.
```
//객체 o의 프로퍼티 중 지정한 이름을 가진 프로퍼티의 값을 변경할 수 없게 하고, 설정될(configurable) 수도 없게 한다.
function freezeProps(o) {
    var props = (arguments.length == 1)									->인자가 하나라면,
    	? Object.getOwnPropertyNames(o)									->대상은 모든 프로퍼티이다.
        : Array.prototype.splice.call(arguments, 1);					->아니면 지정한 이름의 프로퍼티만.
    props.forEach(function(n) {
       	//원래부터 설정될 수 없는 프로퍼티는 무시한다.
        if (!Object.getOwnPropertyDescriptor(o, n).configurable) return;
        Object.defineProperty(o, n, { writable: false, configurable: false });
    });
    return o;
}
//객체 o의 프로퍼티 중 지정된 이름을 가진 프로퍼티를 열거되지 않게 한다.
//단, 해당 프로퍼티가 설정될 수 있어야 한다.
function hideProps(o) {
  	var props = (arguments.length == 1)								->인자가 하나라면,
		? Object.getOwnPropertyNames(o)								->대상은 모든 프로퍼티.
		:Array.prototype.splice.call(arguments, 1);				->아니면 지정한 이름의 프로퍼티만
    props.forEach(function(n) {								->각 프로퍼티를 for/in 루프에 의해 열거되지 않도록 한다.
     	//설정할 수 없는 프로퍼티는 무시한다.
        if (!Object.getOwnPropertyDescriptor(o, n).configurable) return;
        Object.defineProperty(o, n, { enumerable: false });
    });
    return o;
}
```
#####3.객체 상태를 캡슐화하기
이전 예제에서 생성자의 변수나 인자를 해당 생성자로 만든 객체의 private 상태처럼 사용하는 방법을 살펴보았다. 이 방법의 단점은, ECMAScript 3에서는 해당 private 상태에 점근할 수 있는 접근자 메서드를 임의로 교체할 수 있다는 것이다. ECMAScrip 5에서는 삭제할 수 없는 getter/setter 메서드 프로퍼티를 정의할 수 있으므로, 좀 더 견고한 캡슐화를 실현할 수 있다.
```
//이 Range 클래스의 from, to 프로퍼티는 변경될 수 있지만, 언제나 from 값이 to 값보다 같거나 작은 상태를 유지한다.
//시작점과 끝점 변수를 캡슐화하고 있다.
function Range(from, to) {
	//인스턴스가 생성될 때 올바른 값이 지정되었는지를 검증한다.
    if (from > to) throw new Error("Range: from must be <= to");
    //항상 from <= to를 유지하는 접근자 메서드를 정의한다.
    function getFrom() { return from; }
    function getTo() { return to; }
    function setFrom(f) {										->form 값이 to보다 크게 설정되지 않게 한다.
    	if(f <= to) from = f;
        else throw new Error("Range: from must be <= to");
    }
    function setTo(t) {
    	if(t >= from) to = t;									->to값이 from보다 작게 설정되지 않도록 한다.
        else throw new Error("Range: to must be >= from");
    }
    //열거될 수 있고 설정되지 않는 프로퍼티들을 정의한다.
    //이 프로퍼티들은 접근자 메서드를 사용한다.
    Object.definePropertyies(this, {
    	from: {get: getFrom, set: setFrom, enumerable: true, configurable: false},
        to: {get: getTo, set: setTo, enumerable: true, configurable: false}
    });
}
//프로토타입 객체는 이전의 예제와 동일하다.
//인스턴스 메서드는 from과 to로 일반적인 프로퍼티처럼 읽을 수 있다.
Range.prototype = hideProps({
	constructor: Range,
    includes: function(x) { return this.from <= && x <= this.to; },
    foreach: function(f) { for(var x = Math.ceil(this.from); x <= this.to; x++) f(x); },
    toString: function() { return "(" + this.from + "..." + this.to + ")"; }
});
```
#####4.클래스 확장 막기
프로토타입 객체에 새 메서드를 추가하여 클래스를 동적으로 확장하는 기법은 보통 자바스크립트의 기본 기능으로 간주된다. 그러나 ECMAScript 5에서는 원한다면 이를 금지할 수 있다. 
Object.preventExtensions()는 객체를 확장할 수 없게 하는데 이는 해당 객체에 새로운 프로퍼티가 추가될 수 없다는 뜻이다.
Object.seal()은 새로운 프로퍼티가 추가되는 것을 막을 뿐만 아니라, 현재 객체에 있는 모든 프로퍼티를 재설정될 수 없게 한다. 따라서 프로퍼티들은 삭제될 수 없다. 값 변경은 여전히 가능하다.
```
Object.seal(Object.prototype);
```
자바스크립트에서는 또한 객체의 메서드를 동적으로 교체할 수 있다.
```
var original_sort_method = Array.prototpye.sort;
Array.prototype.sort = function() {
	var start = new Date();
    original_sort_method.apply(this, arguments);
    var end = new Date;
    console.log("Array sort took " + (end - start) + " milliseconds.");
};
```
인스턴스 메서드를 읽기 전용으로 만들려면 이런 변경을 막을 수 있다. 앞에서 정의한 freezeProps() 유틸리티 함수는 이러한 목적을 달성하는 한가지 방법이다.
또 한가지 방법은 Object.freeze()를 사용하는 것으로, Object.freeze()는 Object.seal()의 모든 과정을 수행하고 추가로 모든 프로퍼티를 읽기 전용으로 설정하며 또한 재설정할 수 없도록 만든다.
읽기 전용 프로퍼티는 만약 객체 o가 읽기전용 프로퍼티 p를 상속했다면, o.p에 무언가를 할당하려는 시도는 실패할 것이고, 객체 o에 새로운 프로퍼티도 생성되지 않을 것이다. 재정의를 하려면 Object.defineProperty()나 Object.defineProperties()를 사용해야 하고, 새 프로퍼티를 만들려면 Object.create를 사용해야 한다.
#####5.서브클래스의 ECMAScript 5
ECMAScript 5의 기능을 사용하여 서브클래스를 만드는 방법을 보여준다. 이전 예제에 나온 AbstractWritableSet 클래스의 서브클래스로 StringSet클래스를 정의한다. 이 예제의 주된 기능은 Object.create() 메서드를 사용하여 슈퍼클래스의 프로토타입을 상속한 프로토타입 객체를 만드는 것과 새로 만들어진 객체의 프로퍼티를 정의하는 것이다. 앞서 언급했듯이 Object.create() 사용과 관련해서 어려운 점은, 다루기 까다로운 프로퍼티 디스크립터를 사용해야 한다는 것이다.
다른 흥미로운 점은 아무 것도 상속하지 않은 객체를 생성하기 위해 Object.create()에 null을 전달한다는 것이다. 집합의 원소를 지정하는 데 사용되는 이 객체는 프로토타입을 가지고 있지 않기 때문에 hasOwnProperty() 메서드 대신 in 연산자를 사용할 수 있다.
```
function StringSet() {
	this.set = Object.create(null)
    this.n = 0;
    this.add.apply(this, arguments);
}
//Object.create를 사용하면 한 번의 호출로 슈퍼클래스의 프로토타입을 상속하고 메서드 또한 정의할 수 있다는 점을 주목하라.
//writable, enumerable 그리고 configurable 속성 중 어떤 것도 지정하지 않았기 때문에,
//이들 프로퍼티의 속성은 기본 값인 false로 설정된다.
//이 클래스에 읽기 전용 메서드를 정의하면, 이 클래스의 서브 클래스를 정의하기가 까다로워질 수 있다.
StringSet.prototype = Object.create(AbstractWritableSet.prototype, {
	constructor: { value: StringSet },
    contains: { value: function(x) { return x in this.set; }},
    size: { value: function(x) { return this.n; }},
    foreach: { value: function(f,c) { Object.keys(this.set).forEach(f,c);}},
    add: {
    	value: functino() {
        	for(var i = o; i < arguments.length; i++) {
            	if(!(arguments[i] in this.set)) {
                	this.set[arguments[i]] = true;
                    this.n++;
                }
            }
            return this;
        }
    },
    remove: {
    	value: function() {
        	for(var i = 0; i < arguments.length; i++) {
            	if(arguments[i] in this.set) {
            		delete this.set[arguments[i]];
                    this.n--;
            	}
            }
            return this;
        }
    }
})
```
#####6.프로퍼티 디스크립터
이번에는 ECMAScript 5프로퍼티와 관련한 여러 작업을 보여주는 확장된 예제를 보여준다.
다음 예제는 properties() 메서드(열겨되지 않는)를 Object.prototpye에 추가한다. 이 메서드는 프로퍼티 목록을 나타내는 객체를 만환하고, 이 객체에는 프로퍼티와 프로퍼티 속성을 표시(디버깅 유용)하고, 프로퍼티 디스크립터(프로퍼티를 프로퍼티 속성과 함께 복사하려 할 때 필요하다.)를 얻고, 프로퍼티 속성을 설정(앞서 정의한 hideProps와 freezePros 함수의 대안)하는데 유용한 메서드들이 있다. 이 예제는 ECMAScript 5의 프로퍼티와 관련된 대부분의 기능을 다룰 뿐 아니라, 다음 절에서 설명할 모듈화 코딩 기법도 사용하고 있다.
```
//Object.prototype에 properties() 메서드를 정의한다.
//이 메서드는, 메서드를 호출한 객체에서 지정한 이름을 가진 프로퍼티를 나타나는 객체를 반환한다.
//(아무 인자없이 호출되면 호출한 객체의 모든 프로퍼티를 포함한 객체를 반환한다.)
//반환된 객체에는 toString()과 descriptors(), hide(), show() 같은 유용한 메서드가 정의되어 있다.
(function namespace() {												->모든것을 private 함수 유효범위로 둘러싼다.
	//이 함수는 모든 객체의 메서드가 된다.
    function properties() {
    	var names;													->프로퍼티 이름 배열
        if (arguments.length == 0)									->이 객체에 있는 모든 프로퍼티가 대상이다.
        	name = Object.getOwnPropertyNames(this);
        else if (arguments.length == 1 && Array.isArray(arguments[0]))
        	names = arguments[0];									->아니면 프로퍼티 이름을 저장한 배열이거나,
        else														->아니면 인자 목록에서 이름들을 얻는다.
        	names = Array.prototype.splice.call(arguments, 0);
        return new Properties(this, names);			->지정된 이름에 대한 프로퍼티를 나타내는 properties객체를 반환한다
    }
    //이 함수는 Object.prototype의 열거되지 않는 프로퍼티로 설정한다.
    //properties 프로퍼티가 이 private 함수 유효범위에서 외부로 내보내는 유일한 값이다.
    Object.defineProperty(Object.prototype, "properties", {
    	value: properties,
        enumerable: false, writable: true, configurable: true
    });
    //이 생성자 함수는 앞의 properties() 함수에 의해 호출된다.
    //properties 클래스는 객체 프로퍼티들의 집합을 나타낸다.
    function Properties(o, names) {
    	this.o = o;													->프로퍼티가 속한 객체
        this.names = names;											->프로퍼티 이름.
    }
    //이 객체가 나타내는 프로퍼티들을 열거되지 않게 만든다.
    Properties.prototype.hide = function() {
    	var o = this.o, hidden = { enumerable: false };
        this.names.forEach(function(n) {
        	if(o.hasOwnProperty(n))
            	Object.defineProperty(o, n, hidden);
        });
        return this;
    };
    //이 객체가 나타내는 프로퍼티들을 읽기 전용으로, 그리고 설정될 수 없도록 만든다.
    Properties.prototype.freeze = functiion() {
    	var o = this.o, frozen = { writable: false, configurable: false };
        this.names.forEach(function(n) {
        	if(o.hasOwnProperty(n)) Object.defineProperty(o, n, frozen);
        });
        return this;
    };
    //프로퍼티 이름과 디스크립터를 매핑하는 객체를 반복한다.
    //만약 프로퍼티를 프로퍼티 속성과 함께 복사하고 싶다면 다음과 같이 사용한다.
    //Object.defineProperties(dest, src.properties, descriptors());
    Properties.prototype.descriptors = function() {
    	var o = this.o, desc = {};
        this.names.forEach(function(n) {
        	if(!o.hasOwnProperty(n)) return
            desc[n] = Object.getOwnPropertyDescriptor(o,n);
        });
        return desc;
    };
    //프로퍼티 이름, 값, 속성을 나열하여 프로퍼티 목록을 보기 좋은 형태의 문자열로 변환한다.
    //설정(configurable)될 수 없음을 뜻하는 용어로 "permanent"를 사용하고, 
    //값을 변경할 수 없음을 의미하는 용어로 "readonly"를,
    //그리고 열거되지 않음을 뜻하는 용어로 "hidden"을 사용한다.
    //일반적으로 enumerable, writable, configurable 프로퍼티에는 나열될 속성이 없다.
    Properties.prototype.toString = function() {
    	var o = this.o;											->아래 중첩 함수 nameToString()에서 사용한다.
        var lines = this.names.map(nameToString);
        return "{\n " + lines.join(",\n ") + "\n}";
        
        function nameToString(n) {
        	var s = "", desc = Object.getOwnPropertyDescriptor(o, n);
            if (!desc) return "nonexistent " + n + ": undefined";
            if (!desc.configurable) s += "permanent ";
            if (!desc.get && !desc.set) || !desc.writable) s += "readonly ";
            if (!desc.enumerable) s += "accessor " + n
            if (desc.get || desc.set) s += "accessor " + n
            else s += n + ": " + ((typeof desc.value === "function")?"function":desc.value);
            return s;
        }
    };
    //마지막으로 이 예제에서 정의한 메서드를 사용하여, prototype.object의 인스턴스 메서드를 열거되지 않게 만든다.
    Properties.prototype.properties().hide();
}());	//함수를 정의하자마자 둘러싼 함수를 호출한다.
```
###모듈
코드를 클래스로 구성하는 중요한 이유 중 하나는 코드를 다욱 모듈화하여 여러 상황에서 재사용하기 적합하게 하는 것이다. 그러나 모듈화된 코드에 클래스만 있는 것은 아니다. 모듈은 자바스크립트 코드로 이루어진 하나의 파일이다. 모든 파일은 어떤 클래스의 정의, 관련된 클래스들, 유틸리티 함수 라이브러리등을 담을 수도 있고, 혹은 단순히 실행될 스크립트만을 포함할 수도 있다. 자바스크립트는 모듈에 대해 언어적 차원의 지원은 하고 있지 않은데(imports와 exports가 예약어로 지정되어 있으므로 추후 버전에서는 지원될지도 모른다.), 이는 자바스크립트 모듈을 작성하는 일이 대부분 특정 코딩 관습을 따르는 문제와 관련되어 있다는 뜻이다.

많은 자바스크립트 라이브러리와 클라이언트 측 프로그래밍 프레임워크는 일종의 모듈 시스템을 포함하고 있다. 예를 들면 Dojo툴킷과 구글의 Closure 라이브러리는 모듈을 정의하고 불러오기 위한 provide()와 require()함수를 정의하고 있다. 그리고 서버측 자바스크립트 표준화를 위한 움직임으로 Common에서도 모듈 명세를 만들었고, 이 명세에서는 require() 함수를 사용한다. 이러한 모듈 시스템들은 보통 모듈을 불러오거나 의존성을 관리하는 일을 맡아 처리해주지만, 이에 대한 논의는 이 책의 범주를 벗어나기 때문에 다루지 않는다. 만약 이러한 프레임워크 중 하나를 사용한다면 해당 프레임워크에 적합한 규칙에 따라 모듈을 사용하고 정의해야한다. 이 절에서는 일반적으로 사용할 수 있는 아주 간단한 모듈 규칙에 대해 다룰 것이다.

모듈의 목적은 서로 다른 소스를 조합하여 거대한 프로그램을 만드는 것이고, 모듈 제작자가 예상하지 않은 코드가 있더라도, 그 모든 코드가 제대로 작동하는 것이다. 그러려면 모듈은 전역 실행 환경을 수정해서는 안 되고, 뒤 따르는 모듈이 간섭 없는(또는 간섭을 거의 받지 않는)환경에서 실행되도록 해야한다. 따라서 각 모듈은 전역 심볼을 최소한으로 정의해야 하며, 이성적으로는 단 하나의 전역 심볼도 정의하지 않아야 한다. 이어지는 항목에서 이에 대한 방법을 다뤄볼 것이다. 자바스크립트에서 모듈 코드를 작성하는 것은 전혀 까다롭지 않다 사실 여기서 설명하는 기법들에 대한 예제는 이 책 전체에서 계속 다루어 온 것이다.
#####1.네임스페이스로서의 객체
모듈에서 전역 변수를 생성하는 것을 피하는 한 가지 방법은 네임스페이스 객체를 사용하는 것이다. 전역 함수와 전역 변수를 정의하는 대신에 , 네임스페이스 객체는 함수와 변수를 프로퍼티에 저장한다.(네임스페이스 객체는 전역 변수에 의해 참조될 수도 있다.) 예를들어 Set클래스를 생각해 보자. 이 클래스는 단일 전역 생성자 함수로 Set을 정의한다. 이 생성자 함수는 여러 인스턴스 메서드를 정의하지만, 이 메서드들을 Set.prototype의 프로퍼티에 저장하기 때문에 이 메서드들은 전역 객체가 아니다. 또한 유틸리티 함수 _V2s()도 Set의 프로퍼티에 저장한다.
```
var sets = {};
```
이 sets 객체는 모듈에 대한 네임 스페이스고, 각 세트 클래스는 sets의 프로퍼티로 정의된다.
```
sets.SingletonSet = sets.AbstractEnumerableSet.extend(...);
```
이런 방식으로 정의된 클래스를 사용하려면, 그 생성자를 참조할 때 네임스페이스도 생성자와 같이 포함해서 참조 하면 된다.
```
var s = new sets.SingletonSet(1);
```
모듈 작성자는 다른 어떤 모듈이 자신의 모듈을 사용할지 모르기에, 이러한 네임스페이스를 사용하여 모듈 간의 이름 충돌에 대비해야 한다. 그러나 모듈을 사용하는 프로그래머는 어떤 모듈이 사용되고 어떤 이름들이 정의되어 있는지 알고 있다. 모듈을 사용하는 프로그래머는 네임스페이스 구조를 엄격하게 유지하며 사용할 필요가 없고, 자주 사용하는 값을 전역 네임스페이스에 포함시켜도 된다. sets 네임스페이스로서의 Set클래스를 빈번히 사용하는 프로그래머는 다음과 같이 클래스를 가져올 수 있다.
```
var Set = sets.Set;
var s = new Set(1,2,3);
```
간혹 모듈 작성자는 더 깊은 중첩된 네임스페이스를 사용하기도 한다. 만약 sets 모듈이 컬렉션 모듈의 어러 그룹중 일부라면, Collections.sets를 네임스페이스로 사용하는 것이 바람직할 것이고, 그 모듈은 아마 다음과 같은 코드로 시작할 것이다.
```
var collections;											->단일 전역 변수를 정의(혹은 재정의) 한다.
if(!collections)											->만약 이미 존재하지 않는다면
	collections = {};										->최상위 네임스페이스 객체를 생성한다.
collection.sets = {}										->그리고 객체 내에 sets 네임스페이스를 만든다.
//이제 set 클래스를 collection.sets 안에 정의한다.
collections.sets.AbstractSet = function() { ... }
```
때로 최상위 네임스페이스는 그 모듈을 만든 개인이나 조직을 식별하는 데 사용되고, 네임스페이스 간의 이름 충돌을 방지한다. 만약 네임스페이스가 길면, 네임스페이스로부터 값을 가져오는 일이 모든 모듈 사용자에게 큰 일이 된다. 그러나 프로그래머들은, 일일이 긴 네임스페이스 이름을 사용하여 개별 클래스를 가져오기보다는, 모듈 전체를 전역 네임스페이스로 가져올 것이다.
```
var sets = com.davidflanagan.collections.sets;
```
관례상 모듈 파일 이름은 네임스페이스와 일치해야 한다. sets 모듈은 이름이 sets.js인 파일에 저장되어야 한다. 만약 어떤 모듈이 collections.sets라는 네임스페이스를 사용한다면, 이 파일은 collections/ 디렉토리에 저장되어야 한다. 네임스페이스로 com.davidflanagan.collections.sets를 사용하는 모듈의 파일 위치는 com/davidflanagan/collections/sets.js가 될것이다.
#####2.private 네임스페이스와 함수 유효범위
모듈에는 **외부로 내보내는 공개 API가 있다.** 이 API는 다른 프로그래머가 사용하도록 만들어진 함수나 클래스, 프로퍼티, 메서드다. 그러나 모듈을 구현하다 보면 해당 모듈 밖에서는 사용되지 않아야 할 추가적인 함수나 메서드를 만들어야 할 때도 있다. 일례로 Set.v2s() 함수를 보면, Set 클래스의 사용자가 이 함수를 호출하도록 허용하고 싶지 않다. 따라서 Set클래스의 사용자가 이 함수에 접근하지 못하게 막는 편이 더 좋겠다.
이렇게 함수나 메서드에 대한 접근을 막으러면, 해당 모듈을(이 경우에 Set 클래스)함수 내부에 정의하면 된다. 함수 내부에 정의된 변수와 함수는 해당 함수의 지역 변수와 지역 함수고 함수 외부에는 보이지 않는다. 사실상 이런 함수(모듈함수 라고 하기도 한다)의 유효범위를 해당 모듈의 "private"네임스페이스로 사용할 수 있다.
```
//전역 변수 Set을 선언하고 모듈 함수의 반환 값을 Set변수에 할당한다. 
//아래 시작 괄호와 함수 이름은 모듈 함수가 정의되고 바로 호출된다는 사실을 알려주며,
//return문은 모듈 함수가 함수 자체를 반환하지 않고 Set에 할당되는 어떤 값을 반환하는 사실을 알려준다.
//이는 함수 구문이 아니라 함수 표현식임을 유념하라.
//함수 표현식이기 떄문에 이름 "invocation"은 실제 전역 변수를 생성하지 않는다.
var Set = (function invocation() {
	function Set() {
    	this.values = {};
        this.n = 0;
        this.add.apply(this, arguments);
    }
    //이제 Set.prototype에 인스턴스 메서드를 정의한다.
    //간결함을 위해 이 부분의 코드는 생략하였다.
    Set.prototype.contains = function(value) {
    	//접두사를 붙여 구분했던 Set._v2s()가 아니라 v2s()를 호출한다.
        return this.values.hasOwnProperty(v2s(value));
    };
    Set.prototype.size = function() { return this.n; };
    Set.prototype.add = function() { /* ... */ };
    Set.prototype.remove = function() { /* ... */ };
    Set.prototype.foreach = function(f,context) { /* ... */ };
    
    //이들은 앞서 나온 인트턴스 메서드가 사용하는 헬퍼 메서드와 변수다.
    //모듈의 공개 API는 아니지만, 이 함수 유효범위 내에 숨겨져 있기 때문에 
    //이들의 Set의 프로퍼티로 지정하거나 앞에 밑줄을 뭍이는 식으로 구분할 필요가 없다.
    function v2s(val) { /*...*/ };
    function objectId(o) { /*...*/ };
    var nextId = 1;
    
    //이 모듈이 공개하는 API는 Set()생성자 함수다.
    //이 생성자 함수를 외부에서 사용하려면 private 네임스페이스로부터 외부로 생성자 함수를 내보내야 한다.
    //이를 위해 생성자를 반환함으로써 생성자를 외부로 내보낸다.
    //반환된 생성자는 첫 번째 행에 나온 할당 표현식의 값이 된다.
    return Set;
}());										->함수를 정의하고 즉시 호출한다.
```
이렇게 함수를 정의하고 바로 호출하는 방식은 자바스크립트에서 관행적으로 사용된다. private 네임스페이스 내에서 실행되는 코드 "(function() {"과 같은 문장으로 시작하여 "}());"로 끝난다. 모듈 함수 시작 부분의 시작 괄호는 이 것이 함수 정의 구문이 아니라 함수 표현식임을 알려주며, 따라서 코드의 의도를 명확하히 드러낼 수 있다면 어떤 함수 이름이든 접두어로 붙일 수 있다. 위의 예제에서는 해당 함수가 정의되고 즉시 호출된다는 사실을 강조하기 위해 invocation이라는 이름을 사용했다. 만약 해당 함수가 네임스페이스라는 사실을 좀 더 강조하고싶다면 이름을  "namespace"로 정해도 될 것이다.
기본적으로 모듈 코드는 함수 유효범위 내에 봉인되어 있으므로, 모듈 함수 외부에서 사용할 수 있도록 공개 API를 외부로 내보낼 몇 가지 방법이 필요하다. 위의 예제 에서 모듈 함수는 생성자를 반환하고 이를 전역 변수에 할당했다. 어떤 값이 반환된다는 사실은, 명백히 모듈 함수의 유효범위 바깥으로 값이 공개된다는 뜻이다. 하나 이상의 API를 갖는 모듈은, 네임스페이스 객체를 반환하면된다. 예로 든 집합 모듈의 경우에는 다음과 같은 코드를 작성할 수 잇을 것이다.
```
//모든 컬렉션과 관련된 모듈을 보관하는 단일 전역 변수를 생성한다.
var collections;
if(!collections) collections = {};

//이제 sets모듈을 정의한다.
collection.sets = (function namespace() {
	//지역 변수와 지역 함수를 사용하여 여러 종류의 set 클래스를 이곳에 정의한다.
    //...코드 생략...
    //네임스페이스 객체를 반환함으로써 API를 외부로 내보낸다.
    return {
    	//내보낼 프로퍼티 이름: 지역 변수 이름
        AbstractSet: AbstractSet,
        NotSet: NotSet,
        AbstractEnumerableSet: AbstractEnumerableSet,
        SingletonSet: SingletonSet,
        ArraySet: ArraySet
    };
}());
```
비슷한 기법으로 new를 사용하여 모듈 함수를 생성자처럼 호출한 다음에 생성자 내에서 this에 API들을 할당함으로써 API를 외부로 내보낼 수도 있다.
```
var collections;
if(!collections) collection = {};
collection.sets = (new function namespace() {
	//... 코드 생략 ...
    //API를 this 객체에 할당함으로써 외부로 내보낸다.
    this.AbstractSet = AbstractSet;
    this.NotSet = NotSet; //기타 등등..
    
    //어떤 값도 반환하지 않는다는 사실을 유념하자.
})
```
대안으로, 만약 전역 네임스페이스 객체가 이미 정의되어 있다면 모듈 함수는 해당 객체에 직접적으로 프로퍼티를 설정할 수 있다. 이렇게 할 경우에는 어떤 값도 반환할 필요가 없다.
```
var collections;
if(!collections) collections = {};
collections.sets = {};
(function namespace() {
	//.... 코드생략 ...
    //공개 API를 앞에서 맏느 네임스페이스 객체로 내보낸다.
    collection.sets.AbstractSet = Abstract;
    collection.sets.NotSet = NotSet;			->기타 등등..
    
    //앞 코드에서 내보내기를 완료했기 때문에, return문이 필요하지 않다.
}());
```
프레임워크에 모듈 로딩 시스템이 있다면, 모듈 API를 내보내는 메서드도 있을 것이다. 모듈 API를 등록하기 위해 provides() 같은 함수를 제공할 수도 있고, 또는 모듈이 자신의 API를 별도로 저장하는 내보내기 전용 객체가 있을 수도 있다. 자바스크립트 언어 차원에서 모듈 관리 기능을 제공하기 전까지는, 자신이 사용하는 프레임 워크나 툴킷에 가장 적합한 모듈 생성 방식과 모듈 내보내기 시스템을 선택해야 한다.