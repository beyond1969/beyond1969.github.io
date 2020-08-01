---
title: "[Android] 생성자와 @JvmOverload 어노테이션"
date: 2020-08-01
categories: Android, Kotlin
---

# 개요

Kotlin으로 작업중 커스텀뷰를 만들일이 있었는데 생성자를 다음과 같은 방법으로 설정하는 것을 확인.

```kotlin
class TitlebarView @JvmOverloads constructor(context: Context, attrs: AttributeSet?=null, defStyleAttr: Int=0)
    : FrameLayout(context, attrs, defStyleAttr){
        // 이하 생략
```

Java를 이용해서 커스텀뷰를 만들때는 다음과 같이 생성했었다.

```java
class TitlebarView extends FrameLayout {
    public TitlebarView(Context context) {
        this(context, null, 0);
    }
    public TitlebarView(Context context, AttributeSet attrs) {
        this(context, attrs, 0);
    }
    public TitlebarView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }
    // 이하 생략
}
```

두 언어 사이에 생성자를 만드는 방법의 차이를 조사해봤다.

# Kotlin에서의 생성자

## 기본 생성자

Kotlin은 Java와 다르게 클래스 선언부에 생성자를 선언해 줄 수 있다.

```kotlin
class NoParameterClass() {
    // 생성자 인자가 없는 클래스
}

class ParameterClass(number: Int, str: String) {
    // 생성자 인자가 있는 클래스
}
```

Kotlin에서도 Java와 마찬가지로 기본 생성자에 인자를 넣게 된다면 인자가 없는 생성자를 따로 선언해 주지 않는 한 사용할 수 없다.

## init 블록

클래스 선언부에 생성자를 인자 형식으로 선언해주기 때문에 기존에 생성자에서 처리하던 로직들을 수행해줄 영역이 `init` 이다.

``` kotlin
class InitClass(numberParam: Int) {
    var numberMember: Int = 2
    init {
        this.numberMember = numberParam
    }
}
```

## 생성자 인자를 멤버변수로 사용

위의 init 블록을 이용해 기본 생성자로부터 전달받은 인자로 멤버를 할당하는 것은 기존 Java에서와 다를바 없어보인다. 

인자에 var와 같은 한정자를 선언하면 생성자의 인자 자체를 멤버 변수로 사용 가능하다.

```kotlin
class ExampleClass(var number0: Int, var number1: Int) {
    fun getSum(): Int {
        return number0 + number1
    }
}
```

## 다수의 생성자 선언

다수의 생성자를 선언할때는 위와 같은 방식만으로는 불가능하다. kotlin은 다수의 부 생성자를 만들기 위해 `constructor`를 지원한다.

```kotlin
class ExampleClass {
    var number: Int = 0
    constructor() {
        number = 1
    }
    constructor(number: Int) {
        this.number = number
    }
}
```

그러나 이 방법은 다음과 같이 선언하는 경우에는 에러가 발생한다.

```kotlin
class ExampleClass {
    constructor() {
        
    }
    constructor(var number: Int) {	// error1
        
    }
}
```

```kotlin
class ExampleClass() {
    var number: Int = 0
    constructor(number: Int) {	// error2
        this.number = number
    }
}
```

### error1

error1은 부 생성자의 인자 영역에 var 키워드를 사용할 경우 발생한다.

constructor 키워드를 사용하는 생성자는 부 생성자이므로 기본 생성자가 아닌 영역에서 var를 사용하는 것은 금지된다.

### error2

error2는 기본 생성자가 존재하는 경우에 부 생성자를 만드는 경우에 발생 가능하다.

모든 부 생성자들은 기본 생성자가 존재하는 경우 반드시 기본 생성자를 상속 받아야만 한다.

error2의 에러를 해결하려면 다음과 같이 변경하면 된다.

```kotlin
class ExampleClass() {
    var number: Int = 0
    constructor(number: Int): this() {
        this.number = number
    }
}
```

만일 기본 생성자에 인자가 존재한다면 `constructor`로 만들어진 부 생성자에도 동일한 인자가 존재해야한다.

```kotlin
class ExampleClass(name: String) {
    var number: Int = 0
    constructor(number: Int, name: String): this(name) {
        this.number = number
    }
}
```

기본 생성자에 var가 존재하는 경우는 다음과 같다.

```kotlin
class ExampleClass(var name: String) {
    var number: Int = 0
    constructor(number: Int, name: String): this(name) {
        this.number = number
        this.name = "$name secondary" 	// constructor name을 사용해서 멤버 name 초기화
    }
}
```

### 커스텀뷰를 만들때 사용될 형태

```kotlin
class ExampleClass: FrameLayout{
    constructor(context: Context): this(context, null, 0) 
    constructor(context: Context, attributeSet: AttributeSet?): this(context, attributeSet, 0) 
    constructor(context: Context, attributeSet: AttributeSet?, defStyleAttr: Int): super(context, attributeSet, defStyleAttr) {
        // init custom view
    }
}
```

FrameLayout을 상속받아 커스텀뷰를 만들때 생성자의 형태이다.

이렇게 보아선 Java에서의 그것과 별반 다르지 않다. constructor를 사용한다는 것을 빼면 Java에서와 같이 여러개의 생성자를 선언해 주어야한다. 

# @JvmOverloads

[Kotlin 공식 문서](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-overloads/)

해당 어노테이션을 선언해주면 Kotlin 컴파일러에게 해당 함수가 기본값을 가지는 인자를 포함한 여러개의 오버로드 함수를 만들것을 알리게 된다.

최초 개요에서 작성한 코드를 다시 불러와 보자

```kotlin
class TitlebarView @JvmOverloads constructor(context: Context, attrs: AttributeSet?=null, defStyleAttr: Int=0)
    : FrameLayout(context, attrs, defStyleAttr){
        // 이하 생략
```

해당 코드를 익숙한 다음과 같은 형태로 바꿔보자

```kotlin
class TitlebarView: FrameLayout{
    @JvmOverloads 
    constructor(context: Context, attrs: AttributeSet?=null, defStyleAttr: Int=0): super(context, attrs, defStyleAttr)
    // 이하 생략
```

이 형태는 앞선 3개의 `constructor` 선언 클래스, 바로 위의 클래스 선언부에서의 `constructor` 선언 클래스와 동일하다.

사실 `@JvmOverloads` 어노테이션은 클래스 생성자에 국한되는 어노테이션은 아니고 다수의 기본값을 가지는 오버로드 함수를 선언할때 사용된다. 

커스텀뷰를 만들때 이러한 상황이 생겨서 꽤나 코드 수를 줄이는데 도움이 될 것 같다.

