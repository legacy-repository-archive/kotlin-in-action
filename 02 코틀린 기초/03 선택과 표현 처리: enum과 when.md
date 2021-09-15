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
분기 조건에 상수만을 사용할 수 있는 자바의 switch와 달리,     
**코틀린의 when의 분기 조건은 임의의 객체를 허용한다.**     

```kt
fun mix(c1: Color, c2: Color) = when(setOf(c1, c2)) {
    setOf(RED, YELLOW) -> ORANGE
    setOf(YELLOW, BLUE) -> GREEN
    setOf(BLUE, VIOLET) -> INDIGO
    else -> throw Exception("Dirty color")
    }
>>> println(mix(BLUE, YELLOW))
GREEN
```  
우선 이해를 하기 위해서 `setOf()`에 대해서 알 필요가 있다.                   
`setOf()`은 코틀린 표준 라이브러리에서 인자로 전달 받은                    
여러 객체를 그 객체들을 포함하는 집합인 Set 객체로 만드는 함수다.(원소의 순서는 중요하지 않다.)               
          
when 식은 인자 값과 매치하는 조건 값을 찾을 때까지 각 분기를 검사한다.                
**분기 조건에 있는 객체 사이를 매치할 때 동등성을 사용한다.**               
그리고 그 매치하는 둘이 같지 않을 경우 계속 다음 분기 조건 객체와 비교를 한다.       
모든 분기 식에서 만족하는 조건을 찾을 수 없다면 else 분기의 문장을 계산한다.      
**when의 분기 조건 부분에 `식`을 넣을 수 있기 때문에 더 간결하게 코드를 작성할 수 있다.**       

## 인자 없는 when 사용    
```kt
fun mix(c1: Color, c2: Color) = when(setOf(c1, c2)) {
    setOf(RED, YELLOW) -> ORANGE
    setOf(YELLOW, BLUE) -> GREEN
    setOf(BLUE, VIOLET) -> INDIGO
    else -> throw Exception("Dirty color")
    }
>>> println(mix(BLUE, YELLOW))
GREEN
```  
위 코드에서 각 분기만 `setOf()`는 호출하면서 불 필요한 Set 인스턴스를 생성한다.      
**불필요한 가비지 객체가 늘어나는 것을 방지하기 위해서는 어떻게 해야할까? 🤔**    
인자가 없는 when 식을 사용하면 불필요한 객체 생성을 막을 수 있다.(가독성 떨어짐)       
     
```kt
fun mixOptimized(cl: Color, c2: Color) = when {
    (C1 == RED && C2 == YELLOW) || (C1 == YELLOW && C2 == RED) => ORANGE     
    (C1 == YELLOW && C2 == BLUE) || (C1 == BLUE && C2 == YELLOW) => GREEN      
    (C1 == BLUE && C2 == VIOLET) || (C1 == VIOLET && C2 == BLUE) => INDIGO   
    
    else -> throw new Exception("Dirty color")  
}
>>> println(mixOptimized(BLUE, YELLOW)
GREEN
```
**when 에 아무 인자도 없으려면 각 분기의 조건이 불리언 결과를 계산하는 식어야 한다.**              
       
## 스마트 캐스트: 타입 검사와 타입 캐스트를 조합   
   
```kt
interface Expr
class Num(val value: Int): Expr                    // Expr 인터페이스 구현 및 value 프로퍼티만 존재하는 단순한 클래스    
class Sum(val left: Expr, val right: Expr): Expr   // Expr 인터페이스 구현 및 left 및 right 프로퍼티가 존재하는 클래스    
```  
`(1 + 2) + 4`라는 식을 저장하고자 하면 `Sum(Sum(Num(1), Num(2)), Num(4))` 라는 구조의 객체가 생긴다.      
       
Expr 인터페이스에는 2가지 구현 클래스가 존재한다.        
따라서 식을 평가하려면 2가지 경우를 고려해야 한다.     
    
* 어떤 식이 수라면 그 값을 반환한다.     
* 어떤 식이 합계라면 좌항과 우항의 값을 계산한 다음에 그 2값을 합한 값을 반환한다.      

```kt
fun eval(e: Expr): Int {
    if(e is Num) {
        val n = e as Num  // Num으로 타입 변환하는데, 이는 불필요한 중복이다.   
        return n.value
    }
    if(e is Sum) {
        return eval(e.right) + eval(e.left) // 변수 e에 대해 스마트 캐스트를 사용한다.   
    }
    throw IllegalArgumentException("Unknown expression")
}
>>> println(eval(Sum(Sum(Num(1), Num(2)), Num(4))))
7
```  
           
**코틀린에서는 is를 사용해 변수 타입을 검사한다.(instanceof)**                    
자바에서는 `instanceOf`로 확인한 다음에 명시적으로 해당 클래스로 변수 타입을 캐스팅해야 한다.          
코틀린에서는 프로그래머 대신 **컴파일러가 이런 캐스팅을 해준다는 특징이 있다.**    

```kt
if(e is Sum) {
    return eval(e.right) + eval(e.left) // 스마트 캐스트 대상의 프로퍼티는 val 이어야함, 커스텀 접근자 안 됨
}
```
**`is`로 검사하고 나면 컴파일러가 캐스팅을 수행하면서 이후 부터는 캐스팅된 클래스의 기능을 활용할 수 있다.**     
이렇듯 개발자가 명시적으로 캐스팅을 하지 않았지만 컴파일러가 `is`를 통해 캐스팅하는 기능을 **스마트 캐스트**라고 부른다.   
                      
스마트 캐스트는 `is`로 **변수에 든 값(인스턴스필드)의 타입을 검사한 다음에 그 값이 바뀔 수 없는 경우에만 작동한다.**          
즉, **`스마트 캐스트` 대상 변수의 프로퍼티는 반드시 `val`이어야 하며 커스텀 접근자를 사용하면 안 된다.**               
                
이유로서는, `val`이 아니거나 val이지만 커스텀 접근자를 사용하는 경우에는   
해당 프로퍼티에 대한 접근이 항상 같은 값을 내놓는다는 것을 확신할 수 없기 때문이다.   
  
```kt
val n = e as Num
```
원하는 타입으로 **명시적으로 타입 캐스팅을 하려면 `as` 키워드를 사용한다.**

## 리팩토링: if를 when으로 변경   
코틀린의 if는 문이 아니라 식이다.   
그렇기 때문에 3항 연산식이 없으며 계산 결과값을 사용할 일이 없으면 사용을 고민해봐야한다.   
    
```kt
fun eval(e: Exper): Int = 
    if(e is Num) {
        e.value
    } else if(e is Sum) {
        eval(e.right) + eval(e.left)
    } else {
        throw IllegalArgument("Unknown expression")
    }
>>> println(eval(Sum(Num(1), Num(2)))
3
```  
if분기에 식이 하나밖에 없다면 중괄호를 생략해도 된다.          
if분기에 블록을 사용하는 경우 그 블록의 마지막 식이 그 분기의 결과 값이다.   

```kt
fun eval(e: Expr): Int = 
    when (e) {       
        is Num -> e.value       
        is Sum -> eval(e.right) + eval(e.left)        
        else -> throw IllegalArgumentException("Unknown expression")     
    }
// 자바로 비유하면 switch(객체) instanceOf(클래스타입) 같은 형태이다.        
```
`when` 식을 앞에서 살펴본 값 동등성 검사가 아닌 다른 기능에도 쓸 수 있다.             
`when` 식을 이용해서 값의 타입(is)에 따라 다른 로직을 실행시킬 수 있다.(캐스팅도 필요없다)                    
   
`if` 대신 `when`를 사용해야할 경우는 언제일지는 한번 생각해보자   

## 
`if`나 `when`의 각 분기에서 수행해야 하는 로직이 복잡해지면 분기 본문에 블록을 사용할 수 있다.     
블록의 마지막 문장이 블록의 결과가 되며 두 줄 이상의 코드가 실행되어야 할 때 주로 사용한다.   

```kt
fun evalWithLoggin(e: Expr): Int = 
    when(e) {
        is Num -> {
            println("num: ${e.value}")   
            e.value
        }
        is Sum -> {
            val left = evalWithLogging(e.left)
            val right = evalWithLogging(e.right)
            println("sum: $left + $right")
            left + right
        }
        else -> throw IllegalArgumentException("Unknown expression")
    }
    
>>> println(evalWithLogging(Sum(Sum(Num(1), Num(2)), Num(4))))
num: 1
num: 2
sum: 1 + 2
num: 4
sum 3 + 4
7
```
신기하게 결과값만 메서드를 실행하는게 아니라     
메서드 안에 있는 각각의 객체들에 대해서 메서드를 수행하고 있는 것을 알 수 있다.   

```kt
println(evalWithLogging(Num(1)))
println(evalWithLogging(Num(2)))
println(evalWithLogging(Sum(1+2)))
println(evalWithLogging(Num(4)))
println(evalWithLogging(Sum(3+4)))
```
'블록의 마지막 식이 블록의 결과'라는 규칙은 블록이 값을 만들어내야 하는 경우 항상 성립한다.       
식이 본문인 함수는 블록을 본문으로 가질 수 없고, 블록이 본문인 함수는 내부에 return 문이 반드시 있어야 한다.       












  










  
