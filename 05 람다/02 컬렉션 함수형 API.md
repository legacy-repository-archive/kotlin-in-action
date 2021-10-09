컬렉션 함수형 API
====================
함수형 프로그래밍 스타일을 사용하면 컬렉션을 다룰 때 편리하다.    
이번 절에서는 컬렉션을 다루는 코틀린 표준 라이브러리를 살펴본다.     
  
# 필수적인 함수: filter와 map    
## filter
filter와 map은 컬렉션을 활용할 때 기반이 되는 함수다.        
   
```kt
data class Person(val name: String, val age: Int)    
```  
```kt
val list = listOf(1, 2, 3, 4)
println(list.filter { it % 2 == 0} )
>>> [2, 4]    
```  
filter 함수는 컬렉션을 이터레이션 하면서 true 값을 반환하는 원소만을 모은다.        
즉, 결과는 입력 컬렉션의 원소중에서 주어진 술어를 만족하는 원소만으로 이루어진 **새로운 컬렉션이다.**      

```kt
val people = listOf(Person("Alice", 29), Person("Bob", 31))       
println(people.filter { it.age > 30 })            
>>> [Person(name=Bob, age=31)]          
```   
filter 함수는 컬렉션에서 원치 않는 원소를 제거한다.   

## Map 
map 함수는 주어진 람다를 컬렉션의 각 원소에 적용한 결과를 모아서 새 컬렉션을 만든다.      

```kt
val list = listOf(1, 2, 3, 4)
println(list.map { it * it })
>>> [1, 4, 9, 16]
```
컬렉션의 개수는 같지만, 각 원소는 주어진 함수에 의해 변환된 새로운 컬렉션이다.   

```kt
val people = listOf(Person("Alice", 29), Person("Bob", 31))
println(people.map(Person:name))
// println(people.map { it.name })
>>> [Alice, Bob]
```
멤버 참조를 사용해 더 멋지게 작성할 수도 있다.      

```kt
people.filter { it.gae > 30 }.map(Person::name)
```
filter와 같이 연쇄시켜서 작성할 수도 있다.    

```kt
people.filter { it.age == people.maxBy(Person::age) !!.age }    
```


```kt    
val maxAge = people.filter { it.age == people.maxBy(Person::age) !!.age }    
people.filter { it.age == maxAge }
```     



 










