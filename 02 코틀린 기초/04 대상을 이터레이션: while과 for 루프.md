대상을 이터레이션: while과 for 루프
=================================
코틀린 특성 중 자바와 가장 비슷한 것이 이터레이션이다.       
**코틀린 while 루프는 자바와 동일하고 for는 for-each 루프에 해당하는 형태만 존재한다.**          
코틀린의 for는 `for<아이템> in<원소들>`헝태를 취한다.     

# while 루프
코틀린의 while 루프는 자바와 동일하다.   
  
```kt
while(조건) {
    /*...*/
}
```
```kt
do {
    /*...*/
} while (조건)
```

# 수에 대한 이터레이션: 범위와 수열    
> 코틀린의 for 루프는 `범위`를 사용한다.              
   
```kt
val oneToTen = 1..10
```    
범위는 기본적으로 두 값으로 이뤄진 구간이며 정수 등의 숫자 타입의 값이다.       
정수 범위로 수행할 수 있는 가장 단순한 작업은 범위에 속한 모든 값에 대한 이터레이션이다.                
어떤 범위에 속한 값을 일정한 순서로 이터레이션하는 경우를 수열이라고 부른다.                
        
```kt
fun fizzBuzz(i: Int) = when {
    i % 15 == 0 -> "FizzBuzz"
    i % 3 == 0 -> "Fizz"
    i % 5 == 0 -> "Buzz"
    else -> "$i"
}
fin someFunction() {
    for(i in 1..100) {
        print(fizzBuzz(i))
    }
}
>>> 1 2 Fizz 4 Buzz Fizz 7
```
코틀린에서는 **`..` 연산자로 시작 값과 끝 값을 연결해서 범위를 만들 수 있다.**          
코틀린의 범위는 **양끝은 포함하는 구간이다.(끝 값이 닫는 괄호가 아닌 열린 괄호)**       

**일반 증가**
```kt
for (i in 100..200) { 
    // i가 100 부터 200(포함)까지 반복 
}
```
**일반 감소**
```kt
for (i in 100 downTo 1) { 
    // i가 100부터 1(포함)까지 반복 
} 
```  
**step 설정**
```kt
for (i in 100 downTo 1 step 2) { 
    // i가 100부터 1(포함)까지 2단계씩 뛰면서 (-2씩 증가) 반복 
}
```  
코틀린의 for는 크게 위와 같이 나누어져있으며 `downTo` 키워드를 사용하면 감소 수열을 사용할 수 있다.        
`step`키워드를 이용하면 수열간의 간격을 조절 할 수 있다.      
  
```kt
for (i in 1 until size) {
    // 1 부터 size-1 까지  
} 
for (i in 1..size-1) { 
    // 위와 동일 
}
```
자바와 달리 마지막 원소가 닫는 괄호가 아니기에 조금 햇갈려할 수 있다.        
이럴 때는 `until`을 사용하면 닫는 괄호 반복문을 사용할 수 있다.     

## 맵에 대한 이터레이션  
일반적인 컬렉션에 대해서 `for in`구문을 주로 사용한다.     
**그렇다면 순서가 없는 `Map`은 어떻게 처리할까? 🤔**    

```kt
someMap[key] = data
println(someMap[key])
```
참고로 Map 같은 경우 `get/put` 메서드 대신에 `[]`만으로 할당 호출이 가능하다.    
     
```kt
fun main(args: Array) { 
    val binaryReps = TreeMap() 
    for (c in 'A'..'F') { 
        val binary = Integer.toBinaryString(c.toInt()) // 이진법 값을 구한다.   
        binaryReps[c] = binary -> c를 키로 c의 2진 표현법을 넣는다.      
    } 
    for ((letter, binary) in binaryReps) { // 키와 벨류 값을 얻는다.   
        println("$letter = $binary")       // 문자열 템플릿 
    } 
}
```
in 연산자는 확장성을 가지기 때문에 여러가지 형태로 사용할수 있다.    
그렇기 때문에 `Map`자료 구조도 `for..in` 형태를 사용하고            
일반적인 컬레션과 달리 `(key, value)`형태로 값을 추출하면서 동작한다.     

```kt
val list = arrayListOf("10", "11", "12") 
for ((index, value) in list.withIndex()) { 
    println("index: $index, vaue: $value") 
}
```
사실 List 같은 경우도 `(index, value)`형태로 값을 추출할 수도 있다.    
   
# in으로 컬렉션이나 범위의 원소 검사     

 












