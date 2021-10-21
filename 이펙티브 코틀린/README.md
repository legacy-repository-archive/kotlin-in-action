\### **Effective Kotlin** - **생성자에 매개변수가 많다면 Named arguments, Default arguments로 가독성을 높이자**    - 8:40 ~ 9:20 - **private 생성자나 열거 타입으로 싱글턴임을 보증하라**    - 10:00 ~ 11:26 

```
    // Kotlin의 singleton 구현체는 object로 선언 가능하다. 
    object Elvis {
        fun leaveTheBuilding() {
            println("Whoa baby, I'm outta here!");
        }
    }
    
    fun main() {
        Elvis.leaveTheBuilding();
    }
    
```


\- **인스턴스화를 막으려거든 private 생성자를 사용하라**    - 정적 멤버만 담은 유틸리티 클래스는 인스턴스로 만들어 쓰려고 설계한 것이 아니다    - 12.44 ~ 13:05 

```
    // Kotlin util class
    class NoConstructor private constructor() {
        val number = 1
        fun printHello() {
            println("Hello!")
        }
    }
    val noConstructor = NoConstructor() // Compile Error
    
    // 사실 이것도 필요없다. 
    // Top Level function을 사용하면 된다. (file level 선언)
    val number = 1
    fun printHello() {
        println("Hello!")
    }
    
```



1. \- **상속보다는 컴포지션을 사용하라**    - Kotlin에는 래퍼 클래스를 by 키워드를 통해 간단히 구현할 수 있다.    - 13:30 ~ 20:45 

   ```
       class InstrumentedHashSet<E>(
           private val s: MutableSet<E>
       ) : MutableSet<E> by s {
           val addCount = 0
               private set
           
           override fun add(element: E): Boolean {
               addCount++
               return s.add(element)
           }
       
           ovveride fun addAll(elements: Collection<E>): Boolean {
               addCount += elements.size
               return s.addAll(elements)
           }
       }
       
   ```

   
   \- **public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라**    - 24:30 ~ 26:44    - Public field는 API를 수정하지 않고는 내부 표현을 바꿀 수 없으며, 불변식을 보장하지 못한다. 그리고 별도의 부수 작업을 추가할 수 없으며 객체의 주요 특징인 캡슐화를 제공하지 못한다.    - 코틀린에서는 필드에서 Get, Set를 선언할 수 있으므로 직접 정의할 필요가 없다. 

   ```
           class Point {
               var x : Double = 0.0
                   get() {
                       println("x 필드 접근")
                       return field
                   }
               val y : Double = 0.0
                   get() {
                       println("y 필드 접근")
                       return field
                   }
           }
           
   ```

   
   \- **@Override annotation을 일관되게 사용하라**    - Kotlin에서는 오버라이딩을 할 경우 무조건 override keyword를 적용해야 함으로 일관성을 보장한다. (Compile error) - **변경 가능성을 최소화하라**    - 29:42 ~ 33:54    - 가변 객체 : 임의의 복잡한 상태에 놓일 수 있다. 상태 전이를 정밀하게 문서로 남겨놓지 않은 가변 클래스는 믿고 사용하기 어려울 수 있다.    - 불변 객체 : 객체 생성 이후에 변하지 않는 개체를 의미하며, 멀티 쓰레드 환경에서 안전하며 동기화할 필요가 없다.        - 변경 여부에 대하여서 잘 모르겠다면 우선 val로 선언하고 이후 변경이 필요할 때 var을 사용하여 열어주는 것이 좀 더 불변성을 잘 지킬 수 있는 방법이다 - **박싱된 기본 타입보다는 기본 타입을 사용하라**    - Kotlin은 컴파일러를 통해 박싱 여부를 결정하며, 필요한 경우 기본 타입을 사용한다. - **equals, hashcode와 toString을 항상 재정의하라**    - Kotlin의 Data class를 사용하면 각 메서드(equals, hashcode, toString)가 필드를 기준으로 정의된다. - **예외를 무시하지 마라**    - Kotlin은 CheckedException이 없다. 하지만 그와 상관없이 예외를 무시하지 마라.

   

   

   

 
2. *[*오전 1:29*]*

   \- **지연 초기화는 신중히 사용하라**    - 41:08 ~ 42:40    - 지연 초기화는 필드를 사용하는 시점에 초기화하는 것을 의미한다.    - 이 경우 동기화로 인한 성능 저하가 발생할 수 있다.        - Kotlin에서는 Delegated Properties(by lazy)를 통해 이를 쉽게 사용할 수 있다.        - 사용시에 동기화 수준을 최소화해서 호출하는 것을 권장한다. - **한정적 와일드 카드를 통해 API 유연도를 높이라**    - 42:50 ~ 43:41    - Kotlin의 Declaration-site variance, Use-site variance를 사용해 API 유연성을 높이자 

   ```
       // Java
       List<? extends Number> numbers;
       if(condition) {
           numbers = new ArrayList<Int>();
       } else {
           numbers = new ArrayList<Double>();
       }
       
       // Kotlin
       val numbers: List<Number> = if(condition) {
           ArrayList<Int>()
       } else {
           ArrayList<Double>()
       }
       
   ```

   
