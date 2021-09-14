선택과 표현 처리: enum과 when
============================
> 코틀린의 구성요소 중 when에 대해서 알아보자    

코틀린의 `when`은 자바의 swtich를 대치하되 훨씬 더 강력한 기능을 제공한다.      
그리고 when을 알아보면서 코틀린에서 `enum`을 선언하는 방법과 `스마트 캐스트`에 대해서도 알아보자  

# enum 클래스 정의   

```kt
enum class Color {
    RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
}
```
자바에서는 class 대신 enum 을 사용했지만   
코틀린에서는 class 앞에 enum 을 사용한다.   
    
코틀린에서 `enum`은 **소프트 키워드**로,         
특정 위치에서는 문법으로 사용되지만 이외의 위치에서는 변수의 이름으로도 활용 가능하다.         

```kt
enum class Color (val r: Int, val g: int, val b: Int) {
    RED(255, 0, 0), ORANGE(255, 165, 0),
    YELLOW(255, 255, 0), GREEN(0, 255, 0), BLUE(0, 0, 255),
    INDIGO(75, 0, 130), VIOLET(238, 130, 238);                  // enum 에서는 세미콜론 필수 
    
    fun rgb() = (r * 256 + g) * 256 + b                         // 각각의 클래스 안에서 메서등 정의  
}
```
자바와 마찬가지로 enum은 단순 열거가 아닌 프로퍼티를 가진 객체로 통용된다.     
enum에서도 일반적인 클래스와 마찬가지로 생성자와 프로퍼티를 선언한다.        

* 각 enum 상수를 정의할 때는 그 상수에 해당하는 프로퍼티 값을 지정해야만 한다.      
* enum 클래스 안에 메서드를 정의하는 경우 반드시 enum 상수 목록과 메서드 정의 사이에 세미콜론을 넣어야한다.    
  
## when으로 enum 클래스 다루기     
각각의 단어마다 고유한 연산이 있다고 가정을 하자.      
자바라면 switch 문을 이용해서 단어마다 함수의 실행을 다르게 가져갈 수 있다.     
코틀린에서는 when을 이용해서 이러한 기능을 대신한다.     

```kt
fun getMemonic(color: Color) = 
    when (color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
        Color.GREEN -> "Gave"
        Color.BLUE -> "Vattle"
        Color.INDIGO -> "In"
        Color.VIOLET -> "Vain"
    }
>>> print;n(getMemonic(Color.BLUE))
Battle
```
눈치가 빠르다면 알 수 있듯이 **if 와 마찬가지로 when 도 `식`이다.**       
따라서 본문인 함수에 when을 바로 사용하고 있는 것을 알 수 있다.     
  
기능적인 특성으로는, **자바와 달리 break를 사용하지 않아도 된다.**            
성공적으로 매치되는 분기를 찾으면 `when`은 그 분기만 실행하기 때문이다.         

```kt
fun getWarmth(color: Color) = when(color) {
    Color.RED, Color.ORANGE, Color.YELLOW -> "warm"
    Color.GREEN -> "neutral"
    Color.BLUE, Color.INDIGO, Cololr.VIOLET -> "coid"
}
>>> println(getWarmth(Color.ORANGE))
warm
```
만약, 한 분기 안에서 여러 값을 매치 패턴으로 사용할 수 도 있는데 이럴 때 값 사이를 `,`로 분리하면 된다.    

```kt
import ch02.colors.Color
import ch02.colors.Color.*

fun getWarmth(color: Color) = when(color) {
    RED, ORANGE, YELLOW -> "warm"
    GREEN -> "neutral"
    BLUE, INDIGO, VIOLET -> "coid"
}
```
참고로, 상수 상수 값을 `import`하면 자바의 `static import` 처럼 코드를 더 간결히 만들 수 있다.   

## when과 임의의 객체를 함께 사용 



















  
