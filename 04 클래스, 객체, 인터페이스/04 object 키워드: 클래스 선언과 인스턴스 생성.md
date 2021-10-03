object 키워드: 클래스 선언과 인스턴스 생성
===================================
코틀린의 object 키워드는 다양한 상황에서 사용하지만     
모든 경우 클래스를 정의하면서 동시에 인스턴를 생성한다는 공통점이 있다.     
  
* **객체 선언**은 싱글턴을 정의하는 방법 중 하나이다.  
* **동반 객체**는 인스턴스 메서드는 아니지만 어떤 클래스와 관련 있는 메서드와 팩토리 메서드를 담을 때 사용한다.   
    동반 객체 메서드에 접근할 때는 포함된 클래스의 이름을 사용할 수 없다.    
* 객체 식은 코틀린의 특성에 대해 자세히 설명한다.   

# 객체 선언
객체지향 시스템을 설계하다 보면 인스턴스가 하나만 필요한 클래스가 유용한 경우가 많다.       
**코틀린은 객체 선언 기능을 통해 싱글턴을 언어에서 기본 지원한다.**     
객체 선언은 클래스 선언과 그 클래스에 속한 단일 인스턴스의 선언을 합친 선언이다.(스레드 세이프하려나?)     

```kt
object Payroll {
    val allEmployee = arrayListOf<Person>()
    fun calculateSalary() {
        for(person in allEmployee) {
            //
        }
    }
}
```
`객체 선언`은 **object 키워드로 시작한다.**     
`객체 선언`은 클래스를 정의하고 인스턴스를 만들어서 변수에 저장하는 모든 작업을 단 한 문장으로 처리한다.        
(싱글톤 초기화는 스레드 세이프한가? 에 대한 주제는 생각해봐야할 듯, [사이트](https://stackoverflow.com/questions/35587652/kotlin-thread-safe-native-lazy-singleton-with-parameter))      
   
일반 클래스와 마찬가지로 프로퍼티, 메서드, 초기화 블록등이 들어 갈 수 있다.      
하지만 생성자는 객체 선언에 사용할 수 없다.       
싱글턴 객체는 `객체 선언`문이 있는 위치에서 생성자 호출 없이 즉시 만들어진다.(객체 생성 불필요)      

```kt
fun main() {
    Payroll.allEmployee.add(Person())
    Payroll.calculateSalary()
}
```
변수와 마찬가지로 객체 선언에 사용한 이름 뒤에 마침표`.`를 붙이면 객체에 속한 메서드나 프로퍼티에 접근할 수 있다.     

```kt
object CaseInsensitiveFileComparator : Comparator<File> {
    override fun compare(o1: File, o2: File): Int {
        return o1.path.compareTo(o2.path, ignoreCase = true)
    }
}
```
```kt
fun main() {
    println(CaseInsensitiveFileComparator.compare(File("/User"), File("/user")))
}
>>> 0
```
`객체 선언`도 클래스나 인터페이스를 선언할 수 있다.           
프레임워크를 사용하기 위해 특정 인터페이스를 구현해야 하는데,        
그 구현 내부에 다른 상태가 필요하지 않은 경우에 이런 기능이 유용하다.   

```kt
fun main() {
    val files = listOf(File("/z"), File("/a"))
    println(files.sortedWith(CaseInsensitiveFileComparator))
}
```
일반 객체를 사용할 수 있는 곳에서는 항상 싱글턴 객체를 사용할 수 있다.       
`Comparator`를 인자로 받는 `sortedWith`메서드를 통해 정렬을 진행하고 있다.      

```
// 싱글턴과 의존 관계  
싱글턴 패턴과 마찬가지 이유로 대규모 소프트웨어 시스템에서는 객체 선언이 항상 적합하지는 않다.      
의존관계가 별로 많지 않은 소규모 소프트웨어에서는 싱글턴이나 객체 선언이 유용하지만,                
시스템을 구현하는 다양한 구성 요소와 상호작용하는 대규모 컴포넌트에는 싱글턴이 적합하지 않다.               
이유는 객체 생성을 제어할 방법이 없고 생성자 파라미터를 지정할 수 없어서다.          
  
생성을 제어할 수 없고 생성자 파라미터를 지정할 수 없으므로        
단위 테스트를 하거나 소프트웨어 시스템의 설정이 달라질 때 객체를 대체하거나 객체의 의존관계를 바꿀 수 없다.    
따라서 그런 기능이 필요하다면 자바와 마찬가지로 의존관계 주입 프레임워크(스프릥, 구글 주스)와 코틀린 클래스를 함께 사용해야한다.  
```

클래스 안에서도 객체 선언을 할 수 있다.  

```kt
data class Person(val name: String) {
    object NameComparator : Comparator<Person> {
        override fun compare(o1: Person, o2: Person): Int = o1.name.compareTo(o2.name)
    }
}
fun main() {
    val persons = listOf(Person("Bob"), Person("Alice"))
    println(persons.sortedWith(Person.NameComparator))
}
>>> [Person(name=Alice), Person(name=Bob)]
```
이와 같이 `캡슐화`에 초점을 맞추어 객체 내부에 싱글턴 객체를 정의할 수 있다.   

```
// 코틀린 객체를 자바에서 사용하기  
코틀린 객체 선언은 유일한 인스턴스에 대한 정적인 빌드가 있는 자바 클래스로 컴파일 된다.    
이 때 인스턴스 필드의 이름은 항상 INSTANCE다.       
싱글턴 패턴을 자바에서 구현해도 비슷한 필드가 필요하다.    
자바 코드에서 코틀린 싱글턴 객체를 사용하려면 정적인 INSTANCE 필드를 통하면 된다.   

CaseInsensitiveFileComparator.INSTANCE.compare(file1, file2);

이 예제에서 INSTANCE 필드의 타입은 CaseInsensitiveFileComparator 다.   
```

# 동반 객체    
코틀린 클래스 안에는 정적인 멤버가 없다.     
코틀린 언어는 자바 static 키워드를 지원하지 않는다.      
그 대신, 코틀린에서는 패키지 수준의 `최상위 함수`와 `객체 선언`을 활용한다.     
    
대부분의 경우 최상위 함수를 호출하는 편을 권장하지만,            
**최상위 함수 같은 경우 `private` 접근자로 지정된 멤버에는 접근할 수 없다.**          
그래서 클래스의 인스턴스와 관계없이 호출해야 하지만, 클래스 내부 정보에 접근해야 하는 함수가 필요할 때는      
클래스에 중첩된 객체 선언의 멤버 함수로 정의해야한다.(팩토리 메서드에서 유용하게 사용될 것이다.)    

```kt
class A {
    companion object {
        const val HELLO_WORLD = "HelloWorld"
        fun bar() = println("Companion object called")
    }
}
fun main() {
    println(A.HELLO_WORLD)
    A.bar()
}
>>> "HelloWorld"
>>> "Companion object called"
```
클래스 안에 정의된 객체 중 하나에 companion이라는 특별한 표시를 붙이면 그 클래스의 동반 객체로 만들 수 있다.     
동반 객체의 프로퍼티나 메서드에 접근하려면 그 동반 객체가 정의된 클래스(상위 클래스) 이름을 사용한다.       
객체를 인스턴스화 하지 않아도 접근이 가능하다는 이야기이고, 정적 메서드 호출이나 정적 필드 사용 구문과 같아진다.  
       
동반 객체는 상위 클래스의 private 멤버를 호출할 수 있다.      
이는 곧, 상위 클래스의 private 생성자를 호출하기 좋다는 의미기도 하다.       
따라서 동반 객체는 팩토리 패턴을 구현하기 가장 적합한 위치이다.     

```kt
class User {
    val nickname: String
    constructor(email: String) {
        nickname = email.substringBefore('@')
    }
    
    constructor(facebookAccountId: Int) {
        nickname = getFacebookName(facebookAccountId)
    }
}
```
부생성자 2개를 가진 클래스이다.         
이 클래스를 팩토리 메서드로 만들면 아래와 같은 형태를 가질 수 있다.      
  
```
class User private constructor(val nickname: String){
    companion object {
        fun newSubscribingUser(email: String) = User(email.substringBefore('@')) 
        fun newFaceBookUser(accountId: Int) = User(getFaceBookName(accountId))
    }
}
fun main() {
    val newFaceBookUser = User.newFaceBookUser(1)
    println(newFaceBookUser.nickname)

    val newSubscribingUser = User.newSubscribingUser("kwj1270@naver.com")
    println(newSubscribingUser.nickname)
}
>>> facebook
>>> kwj1270
```

# 동반 객체를 일반 객체처럼 사용      
동반 객체는 클래스 안에 정의된 일반 객체다.    

* 동반 객체에 이름을 붙일 수 있다.  
* 동반 객체가 인터페이스를 구현할 수 있다.   
* 동반 객체 안에 확장 함수와 프로퍼티를 정의할 수 있다.

```kt
class Person(val name: String) {
    companion object Loader {
        fun fromJSON(jsonTest: String) : Person = ...
    }
}
fun main() {
    val person = Person.Loader.fromJSON("{name: 'Dmitry'}")
    println(person.name)
    
    val person2 = Person.fromJSON("{name: 'Hello'}")
    println(person2.name)
}
>>> Dmitry
>>> Hello
```
서비스에서 사용하기 위해 객체를 JSON으로 직렬화하거나 역직려화해야한다.      
직렬화 로직을 동반 객체 안에 넣음으로써 편리성을 증대시킬 수 있다.       

대부분의 경우 클래스 이름을 통해 동반 객체에 속한 멤버를 참조할 수 있으므로 객체의 이름을 짓지 않아도 된다.       
하지만 필요하다면 동반 객체에도 이름을 붙일 수 있으며, 이름을 짓지 않으면 기본적으로 Companion이 된다.     

## 동반 객체에서 인터페이스 구현  
다른 객체 선언과 마찬가지로 동반 객체도 인터페이스를 구현할 수 있다.       
인터페이스를 구현하는 동반 객체를 참조할 때 객체를 둘러싼 클래스의 이름을 바로 사용할 수 있다.   

```kt
interface JSONFactory<T> {
    fun fromJSON(jsonText: String) : T
}

class Person(val name: String) {
    companion object : JSONFactory<Person> {
        override fun fromJSON(jsonText: String) = Person("fromJSON")
    }
}
```

JSON 역직렬화하는 구문을 인터페이스로 분리했고 동반 객체에서 이를 구현하는 방식으로 정의했다.   

```kt
interface JSONFactory2<T> {
    fun loadFromJSON(factory: JSONFactory<T>): T
}

class SampleClass {
    companion object : JSONFactory2<Person> {
        override fun loadFromJSON(factory: JSONFactory<Person>) = Person("loadFromJSON")
    }
}

fun main() {
    SampleClass.loadFromJSON(Person)
}
```
JSON으로부터 다시 원소를 만들어내는 추상 팩토리가 있다면 Person 객체를 그 팩토리에 넘길 수 있다.    
즉, 여기서 동반 객체가 구현한 JSONFactory 인스턴스를 넘길때, 외부 클래스인 Person 이름 자체를 넘겨도 된다.(객체 아님)    

```
// 코틀린 동반 객체와 정적 멤버  

클래스의 동반 객체는 일반 객체와 비슷한 방식으로, 클래스에 정의된 인스턴스를 가리키는 정적 필드로 컴파일 된다.   
동반 객체에 이름을 붙이지 않았다면 자바쪽에서 Companion이라는 이름으로 그 참조에 접근할 수 있다.   

/* 자바 */
Person.Companion.fromJSON("...");

동반 객체에게 이름을 붙였다면 Companion 대신 그 이름이 사용된다.   
때로 자바에서 사용하기 위해 코틀린 클래스의 멤버를 정적인 멤버로 만들어야 할 필요가 있다.   
그런 경우 @JvmStatic 어노테이션을 코틀린 멤버에 붙이면 된다.   
정적 필드가 필요하다면 @JvmField 어노테이션을 최상위 프로퍼티나 객체에서 선언된 프로퍼티 앞에 붙인다.   
이 기능은 자바와의 상호 운용성을 위해 존재하며, 정확히 말하자면 코틀린 핵심 언어가 제공하는 기능은 아니다.   
   
코틀린에서도 자바의 정적 필드나 메서드를 사용할 수 있다.     
그런 경우 자바와 똑같은 구문을 사용한다.     
```
 
## 동반 객체 확장   
자바의 정적 메서드나 코틀린의 동반 객체 메서드처럼       
기존 클래스에 대해 호출할 수있는 새로운 함수를 정의하고 싶으면 어떻게 할까?       
클래스에 동반 객체가 있다면 그 객체 안에 함수를 정의함으로써 클래스에 대해 호출할 수 있는 확장 함수를 만들 수 있다.     

```kt
data class Person(val firstName: String, val lastName: String) {
    companion object {
    }
}

fun Person.Companion.fromJSON(json: String) = Person("$json 파라미터로 받은 확장 함수", " 된다.")

fun main() {
    val fromJSON = Person.fromJSON("오호~")
    println(fromJSON)
}
>>> Person(firstName=오호~ 파라미터로 받은 확장 함수, lastName= 된다.) 
```
동반 객체의 이름을 통해 확장함수를 정의할 수 있고,      
이후에는 외부 클래스의 정적 메서드처럼 외부 클래스로부터 바로 호출이 가능하다.(동반 클래스 함수 같아 보임)   
단, 동반 객체에 대한 확장함수이므로 빈 동반 클래스라도 정의가 되어있어야 한다.(그래야 Companion 접근 가능)

# 객체 식: 무명 내부 클래스를 다른 방식으로 작성  
object 키워드는 싱글턴과 같은 객체를 정의하고 그 객체에 이름을 붙일 때만 사용하지는 않는다.       
**무명 객체**를 정의할 때도 object 키워드를 쓴다.(자바의 무명 내부 클래스)     

```kt
fun main() {
    val window = Box(2)
    window.addMouseListener(object : MouseAdapter(){
        override fun mouseClicked(e: MouseEvent?) {
        }
        override fun mouseEntered(e: MouseEvent?) {
        }
    })
}
```
객체 선언과 비슷한 방식으로 선언하되, 한가지 차이는 객체 이름이 빠졌다는 점이다.        
객체 식은 클래스를 정의하고 그 클래스에 속한 인스턴스를 생성하지만, 그 클래스나 인스턴스에 이름을 붙이지는 않는다.    
이런 경우 보통 함수를 호출하면서 인자로 무명 객체를 넘기기 때문에 클래스와 인스턴스 이름 모두 필요하지 않다.   
하지만, 객체에 이름을 붙여야 한다면 변수에 무명 객체를 대입하면 된다.   

```kt
fun main() {
    val listener = object: MouseAdapter() {
        override fun mouseClicked(e: MouseEvent?) = super.mouseClicked(e)
        override fun mouseEntered(e: MouseEvent?) = super.mouseEntered(e)
    }
}
```
한 인터페이스만 구현하거나 한 클래스만 확장할 수 있는 자바의 무명 내부 클래스와 달리      
코틀린 무명 클래스는 여러 인터페이스를 구현하거나 클래스를 확장하면서 인터페이스를 구현할 수 있다.        

```
객체 선언과 달리 무명 객체는 싱글턴이 아니다.    
객체 식이 쓰일 때마다 새로운 인스턴스가 생성된다.     
```
     
```kt
fun countClicks(window :JComponent){
    var clickCount = 0
    var enterCount = 0

    window.addMouseListener(object : MouseAdapter(){
        override fun mouseClicked(e: MouseEvent?) {
            clickCount++
        }

        override fun mouseEntered(e: MouseEvent?) {
            enterCount++
        }
    })
}
```
객체 식 안의 코드는 그 식이 포함된 함수의 변수에 접근할 수 있다.          
하지만 자바와 달리 final 이 아닌 변수도 객체 식 안에서 사용할 수 있다.      
따라서 객체 식 안에서 그 변수의 값을 변경할 수 있다.(자바에서는 effectively final 개념이 있어서 안된다.)    

```
객체 식은 무명 객체 안에서 여러 메서드를 오버라이드해야 하는 경우에 훨씬 더 유용하다.   
메서드가 하나뿐인 인터페이스를 구현해야한다면 코틀린의 SAM 변환 지원을 활용하는 편이 낫다.
SAM 변환을 사용하려면 무명 객체 대신 함수 리터럴(람다)을 사용해야한다.   

SAM은 자바의 함수형 인터페이스와 비슷한 개념으로 Single Abstract Method 인터페이스를 의미한다.   
```

