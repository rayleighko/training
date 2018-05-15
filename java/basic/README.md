## 1장 기본 프로그래밍 구조  

이 문서를 통해 JAVA 프로그래밍의 기초를 학습하며, 간단한 예제를 풀어나갈 것이다.  

### Ubuntu LTS 18.04에서 JAVA 설정하기  

필자는 java 홈페이지에서 `jdk-10.0.1_linux-x64_bin.tar.gz`을 다운로드 해 아래의 과정을 진행하였다.  

```download jdk-10.0.1 for linux 

cd Download/  
tar -xvf jdk-10.0.1_linux-x64_bin.tar.gz  
sudo mkdir /usr/lib/jvm  
sudo mv jdk-10.0.1 /usr/lib/jvm  
sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk-10.0.1/bin/java 1  
sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk-10.0.1/bin/javac 1  
sudo update-alternatives --install /usr/bin/javaws javaws /usr/lib/jvm/jdk-10.0.1/bin/javaws 1  
sudo update-alternatives --install /usr/bin/jshell jshell /usr/lib/jvm/jdk-10.0.1/bin/jshell 1  
```  

위의 과정을 거치면 java를 설치하고, shell에서 java, javac, javaws 명령을 사용하는 것이 가능해진다. 이제 마음놓고 앞으로의 실습을 진행하도록 하자.   

### 학습 목표 설명

> java는 기본적으로 terminal과 편하게 IDE를 사용하여 작성할 수 있다. 필자의 경우에는 앞서 설명한 것과 같이 terminal 환경에서 진행할 것이다.  
> 
> 여기에서 사용될 명령어는 java, javac, javaws, jshell 등이 될 것이고, 사용될 환경은 `Ubuntu LTS 18.04`, `bash shell`으로 진행하게 된다.  

우선 필자의 경우는 이전에 Java를 학습했기 때문에 정말 기본적인 사항에 대해서는 원할한 학습을 위해 생략하고자 한다.  

### JShell  

우리는 앞서 'JShell' 명령을 선언하였다. 다음의 예제를 따라해보자.  

```
$ jshell 
|  Welcome to JShell -- Version 10.0.1
|  For an introduction type: /help intro

jshell> 15
$1 ==> 15

jshell> "Hello, World"
$2 ==> "Hello, World"

jshell> $1*3
$3 ==> 45

jshell> int answer = 90
answer ==> 90

jshell> answer * 3
$5 ==> 270

jshell> new Random() // 여기서 'Shift + Tab'을 누르고, 'v'를 누르면 아래와 같은 모양이 자동 완성된다(동시에 누르면 안된다).

jshell> Random = new Random() // 여기에 아래와 같이 변수 이름을 입력하고 변수를 지정해보자.

jshell> Random generator = new Random()
generator ==> java.util.Random@6d4b1c02

// 이제 generator 변수로 호출할 수 있는 메서드를 확인할 수도 있다. 'tab'을 눌러보자.

jshell> generator.
doubles(         equals(          getClass()
hashCode()       ints(            longs(
nextBoolean()    nextBytes(       nextDouble()
nextFloat()      nextGaussian()   nextInt(
nextLong()       notify()         notifyAll()
setSeed(         toString()       wait(

jshell> generator.nextInt()
$7 ==> -450996732

jshell> Duration // 여기서 'Shift + Tab'을 누르고, 'i'를 누르면 import 대상을 찾아준다. 다음과 같이 말이다.

jshell> Duration
0: Do nothing
1: import: java.time.Duration
2: import: javafx.util.Duration
3: import: javax.xml.datatype.Duration
Choice: // 1을 입력하면 아래와 같이 import 된다.
Imported: java.time.Duration

// 종료는 '/exit'을 입력하거나 'Ctrl + D'를 누르면 된다.
```
  
### "Hello, World" 프로그램 분석하기  

다음의 프로그램을 분석해보자.  

```
package ch01.sec01;

public class HelloWorld {
	public static void main(String[] args) {
		System.out.println("Hello, World!");
	}
}
```
  
우리는 아주 간단한 이 프로그램을 면밀하게 살펴볼 필요가 있다. 왜냐하면 가장 기본적인 문장이지만, 정확하게 이해하지 못하고 있기 때문이다.  

* JAVA는 객체 지향 언어이므로 프로그램에서 대부분은 객체(object)를 조작해서 동작한다. 각 객체는 특정 클래스(class)에 속하며, 그 객체를 각각 클래스의 인스턴스(instance)라고 한다. 클래스에는 객체 상태와 할 수 있는 일(method)를 정의한다. JAVA는 모든 코드를 클래스 안에 정의한다. 자세한 내용은 다음 article(2장)을 활용하도록 하자. 이 프로그램은 **HelloWorld** 클래스 하나로 구성되어 있다.  

* main은 메서드(method)이다. 즉, 클래스 안에 선언된 함수이다. 일반적으로 main은 프로그램을 실행할 때 첫 번째로 호출하는 메서드이다. 이 메서드는 객체가 없어도 작동하도록 static으로 선언한다. 또 값을 반환하지 않으므로 void로 선언했다. 매개변수로 선언된 `String[] args`는 이후에 설명하도록 하겠다.  

* JAVA는 많은 기능을 public이나 private으로 선언하는데 지정할 수 있는 형태가 두 개 더 있다. 여기에서는 public으로 선언하였는데, 이는 뜻 그대로 '공개'되어 있어 다른 클래스에서도 접근이 가능하다는 의미이다. 자세한 내용은 이후에 살펴보자.  

* 패키지(Package)는 관련 있는 클래스를 모아 놓은 집합이라고 보면 된다. 클래스를 패키지 안에 넣어 관련 있는 클래스를 함께 관리하고, 같은 이름을 지닌 클래스가 여러 개 있어도 관련된 것끼리 묶었기 때문에 충돌할 염려를 줄일 수 있다. 여기서 사용된 `package ch01.sec01`는 장과 절 번호로 소스 코드를 구분하였다. 이 경우 HelloWorld 클래스의 전체 이름은 `ch01.sec01.HelloWorld`이 된다.  

* main 메서드의 body에는 System.out을 사용한 명령이 한 줄 적혀있다. 여기서 System.out은 JAVA 프로그램에서 '표준 출력'을 나타내는 객체다.  

### 기본 타입  

JAVA는 객체 지향 언이이지만 그렇다고 자바의 모든 것이 객체로 구성된 것은 아니다. 몇 가지 값은 기본 타입에 속한다.  

이러한 기본 타입 중 네 가지(byte, short, int, long)는 부호 있는 정수 타입이고, 두 가지(float, double)는 부동소수점 타입이며, 하나는 문자열 인코딩에 사용하는 문자 타입인 char, 나머지 하나는 진릿값을 나타내는 boolean 타입이다.  
#### 정수 타입  

대부분은 'int' 타입이 가장 잘맞다. 물론 지구의 인구를 표현하기 위해서는 'long'을 사용해야 한다. 'byte'나 'short'는 특수한 응용이나 저장 공간이 귀할 때 큰 배열을 만드는 용도로 사용된다.  

> 'long' 타입으로도 충분하지 않을 때는 'BigInteger' 클래스를 사용해야 한다.  

C/C++의 경우에는 컴파일하는 프로세서에 따라 정수 타입이 다르지만, JAVA의 경우에는 머신과 상관없이 동일하다. 이는 JAVA의 매커니즘이 '한 번 작성하고, 어디서나 실행'이기 때문이다.  

정수 리터럴을 이용해 정수의 형태를 지정할 수 있다. 'long' 타입은 접미어 'L'을 리터럴로 사용하여 '4000000000L'과 같이 나타낼 수 있다.  

그러나 'byte'나 'short'는 리터럴이 없어서 '(byte) 127'과 같이 캐스트(cast) 표기법을 사용해야 한다.  

> 이러한 리터럴은 진법의 구분에서도 사용한다. '0xCAFEBABE'는 16진수를, '0b1001'은 2진수를 나타낸다.  
>
> 숫자에 리터럴을 줄 수도 있는데, 이는 사람이 구분하기 위해 사용한다. 가령 '1_000_000'은 1,000,000을 나타내며 컴파일 시 밑줄은 삭제된다.  

#### 부동소수점 타입  

'float' 타입 숫자에는 접미어 'F'를 붙여 '3.14F'와 같이 사용한다. 만약 부동소수점 리터럴에 접미어 'F' 가 붙지않으면, 'double' 타입이 된다(default가 double이지만 접미어 'D'를 붙여 double 타입의 리터럴을 추가할 수 있다).  

> 무한대를 나타내는 **Double.POSITIVE_INFINITY**, 음의 무한대를 나타내는 **Double.NEGATIVE_INFIITY**, 숫자가 아니라는 것(Not a Number)을 나타내는 **Double.NaN** 등 특별한 부동소수점 값이 있다는 것도 알고 넘어가자.  

이러한 부동소수점 수는 금융 계산에는 적합하지 않다. 그 이유는 2진수 체계로 10진수 체계를 제대로 표현하지 못한다는 오류에서 시작되는데, 이는 '반올림 오류'라고 불린다.  

예를 들어 System.out.println(2.0 - 1.1) 명령은 예상처럼 0.9가 아니라 0.89999999999999999를 출력한다. 이처럼 부동소수점 수를 2진수 체계로 나타내면 10진수 체계로 1/3을 정확히 나타낼 수 없듯이, 1/10을 2진수 체계인 컴퓨터가 정확하게 나타낼 수 없기 때문에 금융 계산과 같이 정확한 값을 표현할 경우에는 적합하지 않다.  

> 큰 수의 정확한 숫자 계산이 필요할 때는 BigDecimal 클래스를 사용하자.  

이외에도 'char'과 'boolean'이 존재하며, 각각 문자, 참과 거짓을 나타내기 위한 자료형이다.  

