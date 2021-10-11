수신 객체 지정 람다: with apply
=================================
코틀린 표준 라이브러리 메서드 중 하나인 `with()`과 `apply()`에 대해서 알아보자    
이번 절에선느 자바의 람다에는 없는 코틀린 람다의 독특한 기능인 수신 객체 지정 람다에 대해서 알아보자   

**수신 객체 지정 람다**는 수신 객체를 명시하지 않고 람다의 본문 안에서 다른 객체의 메서드를 호출하는 것이다.     

# wtih() 함수   
어떤 객체의 이름을 반복하지 않고도 그 객체에 대해 다양한 연산을 수행할 수 있다면 좋을 것이다.         
코틀린에서는 `with()`라는 라이브러리 함수를 통해 제공한다.      

```kt
fun alphabet(): String {
    val result = StringBuilder()
    for (letter in 'A'.. 'Z') {
        result.append(letter)
    }
    result.append("\nNow I know the alphabet!")
    return result.toString()
}
fun main() {
    println(alphabet())
}
>>> ABCDEFGHIJKLMNOPQRSTUVWXYZ
>>> Now I know the alphabet!  
```
위 코드에서는 result를 자주 사용하면서 result의 여러 메서드를 호출하고 있다.     

```kt
fun alphabet(): String {
    val stringBuilder = StringBuilder()
    return with(stringBuilder) {                // -> 람다식이다.(매개변수2개인데 마지막이 람다여서 밖으로 뺐다.)     
        for (letter in 'A'.. 'Z') {
            this.append(letter)                 // this를 명시해서 수신 객체의 메서드를 호출한다.
        }
        append("\nNow I know the alphabet!")    // this 생략도 가능하다.
        this.toString()                         // 람다에서 값을 반환한다.   
    }
}
fun main() {
    println(alphabet())
}
```
with문은 언어가 제공하는 특별한 구문처럼 보이지만, 실제로는 파라미터가 2개 있는 함수이다.   
첫번째 파라미터는 

일전에 코틀린 람다에서 `this`는 불가능하다고 했지만 **수신 객체 지정 람다**에서는 가능하다.      

