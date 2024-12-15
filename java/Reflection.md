<!-- TOC -->
* [리플렉션](#리플렉션)
  * [얻을 수 있는 정보](#얻을-수-있는-정보)
* [클래스 메타데이터 조회](#클래스-메타데이터-조회)
  * [클래스에서 찾기](#클래스에서-찾기)
  * [인스턴스에서 찾기](#인스턴스에서-찾기)
  * [문자로 찾기](#문자로-찾기)
* [기본 탐색](#기본-탐색)
* [메서드 탐색과 동적 호출](#메서드-탐색과-동적-호출)
  * [getXxx() vs getDeclareXxx()](#getxxx-vs-getdeclarexxx)
  * [예제 코드](#예제-코드)
* [필드 탐색과 값 변경](#필드-탐색과-값-변경)
  * [예제 코드](#예제-코드-1)
* [생성자 탐색과 객체 생성](#생성자-탐색과-객체-생성)
  * [예제 코드](#예제-코드-2)
* [리플렉션과 주의사항](#리플렉션과-주의사항)
<!-- TOC -->

# 리플렉션

> 클래스가 제공하는 다양한 정보를 동적으로 분석하고 사용하는 기능

* 리플렉션을 통해 프로그램 실행 중에 클래스, 메서드, 필드 등에 대한 정보를 얻거나, 새로운 객체를 생성하고 메서드를 호출하며, 필드의 값을 읽고 쓸 수 있다.

## 얻을 수 있는 정보

* **클래스의 메타데이터**: 클래스 이름, 접근 제어자, 부모 클래스, 구현된 인터페이스 등
* **필드 정보**: 필드의 이름, 타입, 접급 제어자를 확인하고, 해당 필드의 값을 읽거나 수정할 수 있다.
* **메서드 정보**: 메서드 이름, 반환 타입, 매개변수 정보를 확인하고, 실행 중에 동적으로 메서드를 호출할 수 있다.
* **생성자 정보**: 생성자의 매개변수 타입과 개수를 확인하고, 동적으로 객체를 생성할 수 있다.

# 클래스 메타데이터 조회

* 클래스의 메터데이터는 `Class`라는 클래스로 표현되며, 획득하는 방법은 3가지이다.

## 클래스에서 찾기

```java
Class<BasicData> basicDataClass = BasicData.class;
```

* 클래스명에 `.class`를 사용하면 획득할 수 있다.

## 인스턴스에서 찾기

```java
BasicData instance = new BasicData();
Class<? extends BasicData> basicDataClass = instance.getClass();
```

* 인스턴스에서 `.getClass()` 메서드를 호출하면 획득할 수 있다.
* 반환 타입이 `Class<? extends BasicData>`인 이유는, 실제 인스턴스가 자식 타입일 수도 있기 때문이다.

## 문자로 찾기

```java
String className = "reflection.data.BasicData";
Class<?> basicDataClass = Class.forName(className);
```

* 단순히 문자로 클래스의 메타데이터를 조회할 수 있다.

# 기본 탐색

* 클래스 이름, 패키지, 부모 클래스, 구현한 인터페이스, 수정자 정보 등 다양한 정보를 획득할 수 있다.
* 수정자는 접근 제어자와 비 접근 제어자(기타 수정자)로 나눌 수 있다.
  * 접근 제어자: `public`, `protected`, `default`, `private`
  * 비 접근 제어자: `static`, `final`, `abstract`, `synchronized`, `volatile` 등
* `getModifiers()`를 통해 수정자가 조합된 숫자를 얻고, `Modifier`를 사용해서 실제 수정자 정보를 확인할 수 있다.

# 메서드 탐색과 동적 호출

* `Class.getMethods()`또는 `Class.getDeclareMethods()`를 호출하면 `Method`라는 메서드의 메타 데이터를 얻을 수 있다.

## getXxx() vs getDeclareXxx()

* `getXxx()`: 해당 클래스와 상위 클래스에서 상속된 **모든 public 메서드를 반환**
* `getDeclareXxx()`: **해당 클래스에서 선언된 모든 메서드를 반환**하며, 접근 제어자에 관계없이 반환. 상속된 메서드는 포함하지 않음

## 예제 코드

```java
public class BasicData {
    public String hello(String str) {
        System.out.println("BasicData.hello");
        return str + " hello";
    }

    public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
        //정적 메서드 호출 - 일반적인 메서드 호출
        BasicData BasicDataInstance = new BasicData();
        BasicDataInstance.call();

        //동적 메서드 호춣 - 리플렉션 사용
        Class<? extends BasicData> helloClass = BasicDataInstance.getClass();
        String methodName = "hello";

        Method method = helloClass.getDeclaredMethod(methodName, String.class);
        Object returnValue = method.invoke(BasicDataInstance, "hi");
        System.out.println("returnValue = " + returnValue);
    }
}
```

**일반적인 메서드 호출 - 정적**

* 인스턴스의 참조를 통해 메서드를 호출하는 방식이 일반적인 메서드 호출 방식이다.
* 코드를 변경하지 않는 이상 다른 메서드로 변경하는 것이 불가능하다.

**동적 메서드 호출 - 리플렉션 사용**

```java
public Method getMethod(String name, Class<?>... parameterTypes) {
}

public Method getDeclaredMethod(String name, Class<?>... parameterTypes) {
}
```

* 클래스 메타데이터가 제공하는 getMethod(), getDeclaredMethod()에 메서드 이름, 사용하는 매개변수의 타입을 전달하면 원하는 메서드를 찾을 수 있다.

```java
public Object invoke(Object obj, Object... args) throws IllegalAccessException, InvocationTargetException {
}
```

* `invoke()` 메서드에 실행할 인스턴스와 인자를 전달하면, 해당 인스턴스에 있는 메서드를 실행할 수 있다.

# 필드 탐색과 값 변경

## 예제 코드

```java
public class User {
    private String id;
    private String name;
    private Integer age;

    public User() {
    }

    public User(String id, String name, Integer age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException {
        User user = new User("id1", "userA", 20);
        System.out.println("기존 이름 = " + user.getName());

        Class<? extends User> aClass = user.getClass();
        Field nameField = aClass.getDeclaredField("name");

        //private 필드에 접근 허용, private 메서드도 이렇게 호출 가능
        nameField.setAccessible(true);
        nameField.set(user, "userB");
        System.out.println("변경된 이름 = " + user.getName());
    }
}
```

```java
public Field getField(String name) throws NoSuchFieldException, SecurityException {
}

public Field getDeclaredField(String name) throws NoSuchFieldException, SecurityException {
}
```

* name으로 필드를 조회한다.

```java
public void setAccessible(boolean flag) {
}
```

* private 접근 제어자를 사용하는 필드는 직접 접근해 값을 변경하는 것이 불가능하다.
* 리플랙션은 setAccessible()로 private 필드에 접근할 수 있는 특별한 기능을 제공한다.

```java
public void set(Object obj, Object value) throws IllegalArgumentException, IllegalAccessException {
}
```

* 인스턴스에 있는 필드의 값을 변경한다.

# 생성자 탐색과 객체 생성

## 예제 코드

```java
public class BasicData {

    private BasicData(String data) {
        System.out.println("BasicData.BasicData: " + data);
    }

    public void call() {
        System.out.println("BasicData.call");
    }

    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
        Class<?> aClass = Class.forName("reflection.data.BasicData");

        Constructor<?> constructor = aClass.getDeclaredConstructor(String.class);
        constructor.setAccessible(true);
        Object instance = constructor.newInstance("hello");
        System.out.println("instance = " + instance);

        Method method1 = aClass.getDeclaredMethod("call");
        method1.invoke(instance);
    }
}
```

```java
public Constructor<T> getConstructor(Class<?>... parameterTypes) throws NoSuchMethodException, SecurityException {
}

public Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes) throws NoSuchMethodException, SecurityException {
}
```

* parameterTypes로 받은 타입을 사용하는 생성자를 조회한다.

```java
public T newInstance(Object... initargs) throws InstantiationException, IllegalAccessException, IllegalArgumentException, InvocationTargetException {
}
```

* 찾은 생성자를 사용해서 객체를 생성한다.

# 리플렉션과 주의사항

* 리플렉션을 활용하면 private 접근 제어자도 직접 접근해서 값을 변경할 수 있지만, 이는 객체 지향 프로그래밍의 원칙을 위반하는 행위로 간주될 수 있다.
  * private 접근 제어자는 클래스 내부에서만 데이터를 보호하고, 외부에서의 직접적인 접근을 방지하기 위해 사용된다.
  * 리플랙션을 통해 이러한 접근 제한을 무시하는 것은 캡슐화 및 유지보수성에 악영향을 미칠 수 있다.
* 리플렉션을 사용할 때는 반드시 신중하게 접근해야 하며, 가능한 경우 접근 메서드(ex: getter, setter)를 사용하는 것이 바람직하다.
  * 리플렉션은 주로 테스트나 라이브러리 개발 같은 상황에서 유용하게 사용되지만, 일반적인 애플리케이션 코드에서는 권장되지 않는다.
  * 이를 무분별하게 사용하면 코드의 가독성과 안정성을 크게 저하시킬 수 있다.
