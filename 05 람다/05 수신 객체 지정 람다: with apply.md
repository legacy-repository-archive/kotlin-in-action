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
**첫번째 파라미터는 우리가 생략할 타겟이고, 두번째 파라미터는 람다식이다**   
위 코드에서는 가독성 및 컨벤션으로 람다를 밖으로 빼내어 코드를 정의하고 있다.       

with() 함수는 첫번째 인자로 받은 객체를 두 번째 인자로 받은 람다의 수신 객체로 만든다.     
인자로 받은 람다 본문에서는 **this를 사용해 그 수신 객체에 접근할 수 있다.**           
일전에 코틀린 람다에서 `this`는 불가능하다고 했지만 **수신 객체 지정 람다에서는 가능하다.**           
      
또한, 일반적인 this와 마찬가지로 this와 점을 사용하지 않고,         
프로퍼티나 메서드 이름만 사용해도 수신 객체의 멤버에 접근할 수 있다.          

```
// 수신 객체 지정 람다와 확장 함수 비교    
this가 함수의 수신 객체를 가리키는 비슷한 개념을 떠올린 독자가 있을지도 모르겠다.      
확장 함수 안에서 this는 그 함수가 확장하는 타입의 인스턴스를 가리킨다.       
그리고 그 수신 객체 this의 멤버를 호출할 때는 'this.'를 생략할 수 있다.    
    
어떤 의미에서는 확장 함수를 수신 객체 지정 함수라 할 수도 있다.   
   
|일반 함수|일반 람다|    
|확장함수| 수신 객체 지정 람다|     

람다는 일반 함수와 비슷한 동작을 정의하는 한 방법이다.    
수신 객체 지정 람다는 확장 함수와 비슷한 동작을 정의하는 한 방법이다.   
```

이전 코드를 더욱 리팩토링하면 아래와 같다.   

```kr
fun alphabet() = with(StringBuilder()) {
    for (letter in 'A'.. 'Z') {
        append(letter)                 // this를 명시해서 수신 객체의 메서드를 호출한다.
    }
    append("\nNow I know the alphabet!")    // this 생략도 가능하다.
    toString()                         // 람다에서 값을 반환한다.   
}
```
불필요한 변수를 없애면, alphabet 함수가 식의 결과를 바로 반환하게 된다.    
따라서 식을 본문으로 하는 함수로 표현할 수 있다.      

```
// 메서드 이름 충돌  
with()에게 인자로 넘긴 객체의 클래스와        
with()을 사용하는 코드가 들어있는 클래스 안에 이름이 같은 메서드가 있으면?        
그런 경우 this 참조 앞에 레이블을 붙이면 호출하고 싶은 메서드를 명확히 정할 수 있다.  
  
alphabet 함수가 OuterClass의 메서드라고 가정한다.    
람다식 내에서 바깥쪽 클래스 OuterClass에 정의된 toString을 호출하고 싶다면?     

this@OuterClass.toString() 으로 호출가능하다.    
```   
with()가 반환하는 값은 람다 코드를 실행한 결과이며, 그 결과는 람다 식의 본문에 있는 마지막 식의 값이다.   
하짐나 때로는 람다의 결과 대신 사용된 수식 객체 자체가 필요한 경우가 있다.   
그럴 때는 apply() 라이브러리 함수를 사용할 수 있다.   

# apply 함수   





