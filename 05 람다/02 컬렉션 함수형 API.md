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
나이의 최댓값을 구하고 나이가 그 최댓값과 같은 모든 사람을 반환하는 코드다.    
그러나 100명의 사림이 있다면 100번 최댓값을 구하는 연산이 실행되느 아래와 같이 개선한다.       
        
```kt    
val maxAge = people.maxBy(Person::age) !!.age        
people.filter { it.age == maxAge }
```     
즉, filter 안에서 호출하면 이를 지속적으로 수행하는 것이므로,        
이를 따로 분리 시켜서 동작하는 형태로 하자.(복잡도가 엄청 감소되었을 것이다)         


```kt
val numbers = mapOf(0 to "zero", 1 to "one")      
println(numbers.mapValues { it.value.toUpperCase() })    
>>> {0=ZERO, 1=ONE}
```
Map의 경우 filterKeys()와 mapKeys()를 통해 키값을 걸러내거나 변환하고,      
filterValues()와 mapValues()를 통해 값을 걸러 내거나 변환한다.          

# all, any count, find: 컬렉션에 술어 적용     
* all : 컬렉션의 모든 요소들이 조건을 만족하는지 판단하다.      
* any : 컬렉션의 어느 하나 요소라도 조건을 만족하는지 판단한다.         
* count : 조건을 만족하는 컬렉션 원소를 반환한다.      
* find : 조건을 만족하는 첫번째 원소를 반환한다.   
   
```kt  
val canBeInClub27 = { p:Person -> p.age <= 27 }
val people = listOf(Person("Alice", 27), Person("Bob", 31))
println(people.all(canBeInClub27)
>>> false
```
모든 원소가 이 술어를 만족하는지 궁금하다면 all 함수를 쓴다.  

```kt
val canBeInClub27 = { p:Person -> p.age <= 27 }
val people = listOf(Person("Alice", 27), Person("Bob", 31))
println(people.any(canBeInClub27)
>>> true
```
술어를 만족하는 어느 한 원소라도 존재하는지 궁금하다면 any 함수를 쓴다.    

```kt
val list = listOf(1, 2, 3)     
println(!list.all { it == 3 })   
>>> true
println(list.any { it != 3 })
>>> true
```
`!all()`는 모든 조건을 만족하지 않는지에 대해서 검사한다.      
참고로 any() 에서 '부정 조건'을 사용했을 때와 결과가 같다.            
다만, 가독성을 높이려면 any와 all 앞에 !를 붙이지 않는 편이 낫다.     

```kt
val people = listOf(Person("Alice", 27), Person("Bob" ,31))
println(people.count(canBeInClub27))
>>> 1
```

술어를 만족하는 원소의 개수를 구하려면 count를 사용한다.    

```kt
println(people.filter(canBeInClub27).size)
```
간혹 size를 사용하는 경우가 있는데   
size를 사용할 경우, 조건을 만족하는 모든 원소가 들어가는 중간 컬렉션이 생긴다.(원소 여부 판단)      
count는 개수만을 추적하지, 조건을 만족하는 원소를 따로 저장하지 않으므로 훨씬 효율적이다.        

```kt
val people = listOf(Person("Alice", 27), Person("Bob", 31))
println(people.find(canBeInClub27))
>>> Person(name=Alice, age=27)
```
술어를 만족하는 원소를 하나 찾고 싶으면 find 함수를 사용하면 된다.      
이 식은 조건을 만족하는 원소가 하나라도 있는 경우 가장 먼저 조건을 만족한다고 확인된 원소를 반환한다.       
만족하는 원소가 전혀 없는 경우 null 을 반환한다. (find()는 firstOrNull과 같다.)     
보다 명확하게 표현하고 싶다면 `firstOrNull()` 을 쓰면 된다.      
   
# groupBy: 리스트를 여러 그룹으로 이뤄진 맵으로 변경      

컬렉션의 모든 원소를 어떤 특성에 따라 여러 그룹으로 나누고 싶다고 가정하자.(원소들을 그룹핑한다.)          
조건을 파라미터로 전달하면 컬렉션을 자동으로 구분해주는 함수가 있으면 편리할 텐데 groupBy()가 이 역할을 한다.          

```kt
val people = listOf(Person("Alice", 31), ... , Person("Bob", 29), Person("Carol", 31))
println(people.groupBy { it.age })   
>>> 29=[Person(name=Bob, age=29)],
>>> 31=[Person(name=Alice, age=31), Person(name=Carol, age=31)]
```  
연산의 결과로, 원소를 구분하는 **특성이 키**가 되고 **키 값에 따른 각 값**으로 이루어진 **Map이다.**   
각 그룹은 리스트다. 따라서 groupBy의 결과 타입은 `Map<Int, List<Person>>` 이다.      
필요하면 이 맵을 `mapKey()`나 `mapValues()` 등을 사용해서 변경할 수 있다.       

```kt
val list = listOf("a", "ab", "b")
println(list.groupBy(String::first))
>>> {a=[a, ab], b=[b]}
```
`first()`는 String의 확장 함수이지만, 여전히 멤버참조를 사용해 first에 접근할 수 있다.     
      
# flatMap과 flatten: 중첩된 컬렉션 안의 원소 처리        
```kt
class Book(val title: String, val authors: List<String>)
```
Book으로 표현한 책에 대한 정보를 저장하는 도서관이 있다고 가정하자   
   
```kt  
books.flatMap { it.authors }.toSet() // books 컬렉션에 있는 책을 쓴 모든 저자의 집합, 해당 요소만 빼서 컬렉션 만드는 듯
```  
flatMap() 함수는 먼저 인자로 주어진 람다를 컬렉션의 모든 객체에 적용하고          
람다를 적용한 결과로 얻어지는 여러 리스트를 한 리스트로 한 대 모은다.          

```kt
val strings = listOf("abc", "def")
println(Strings.flatMap { it.toList() }) // toList로 원소 하나하나를 리스트로 쪼갬 -> 이걸 다시 flatMap으로 한데 뭉침 
>>> [a, b, c, d, e, f]   
```   
`toList()` 함수를 문자열에 적용하면, 그 문자열에 속한 모든 문자로 이뤄진 리스트가 만들어진다.         
`map()`과 `toList()`를 함께 사용하면, 표현한 것처럼 문자로 이뤄진 리스트로 이뤄진 리스트가 생긴다.       
`flatMap()`함수는 다음 단계로 리스트의 리스트에 들어있던 모든 원소로 이뤄진 단일 리스트를 반환한다.     

```kt
val books = listOf(Book("Thursday Next", listOf("Jasper Fforde")),
                   Book("Nort", listOf("Terry Pratchett")),
                   Book("Good Omens", listOf("Terry Pratchett", "Neil Gaiman")))
println(books.flatMap { it.authors }.toSet())
>>> [Jasper, Fforde, Terry Pratchett, Neil Gaiman]
```























