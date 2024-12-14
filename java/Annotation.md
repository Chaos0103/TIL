<!-- TOC -->
* [애노테이션](#애노테이션)
* [애노테이션 정의](#애노테이션-정의)
  * [애노테이션 정의 규칙](#애노테이션-정의-규칙)
* [메타 애노테이션](#메타-애노테이션)
  * [@Retention](#retention)
  * [@Target](#target)
  * [@Documented](#documented)
  * [@Inherited](#inherited)
    * [클래스 상속에만 적용되는 이유](#클래스-상속에만-적용되는-이유)
* [애노테이션과 상속](#애노테이션과-상속)
* [자바 기본 애노테이션](#자바-기본-애노테이션)
  * [@Override](#override)
    * [@Retention(RetentionPolicy.SOURCE)](#retentionretentionpolicysource)
  * [@Deprecated](#deprecated)
  * [@SuppressWarnings](#suppresswarnings)
<!-- TOC -->

# 애노테이션

> 프로그램 실행 중에 읽어서 사용할 수 있는 주석

* 자바 애노테이션(Annotation)은 일반적으로 "주석" 또는 "메모"를 의미한다.
* 애노테이션은 코드에 추가적인 정보를 주석처럼 제공한다.
* 하지만 일반 주석과 달리, 애노테이션은 컴파일러나 런타임에서 해석될 수 있는 메타데이터를 제공한다.
* 즉, 애노테이션 코드에 메모를 달아놓는 것처럼 특정 정보나 지시를 추가하는 도구로, 코드에 대한 메타데이터를 표현하는 방법이다.
* 따라서 "애노테이션"이라는 이름은 코드에 대한 추가적인 정보를 주석처럼 달아놓는다는 뜻이다.

# 애노테이션 정의

* 애노테이션은 `@interface` 키워드로 정의한다.
* 애노테이션은 속성을 가질 수 있는데, 인터페이스와 비슷하게 정의한다.

## 애노테이션 정의 규칙

* **데이터 타입**
  * 기본 타입 (`int`, `float`, `boolean` 등)
  * `String`
  * `Class`(메타데이터) 또는 인터페이스
  * `enum`
  * 다른 애노테이션 타입
  * 위의 타입들의 배열
  * 이외의 타입은 정의할 수 없다. 즉, 일반적인 클래스를 사용할 수 없다.
* **default 값**
  * 요소에 default 값을 지정할 수 있다.
  * ex) `String value() default "기본 값을 적용합니다.";`
* **요소 이름**
  * 메서드 형태로 정의된다.
  * 괄호()를 포함하되 매개변수는 없어야 한다.
* **반환 값**
  * void를 반환 타입을 사용할 수 없다.
* **예외**
  * 예외를 선언할 수 없다.
* **특별한 요소 이름**
  * `value`라는 이름의 요소를 하나만 가질 경우, 애노테이션 사용 시 요소 이름을 생략할 수 있다.

# 메타 애노테이션

> 애노테이션을 정의하는데 사용하는 특별한 애노테이션

## @Retention

* 애노테이션의 생존 기간을 지정한다.

```java
package java.lang.annotation;

@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Retention {
    RetentionPolicy value();
}
```

```java
public enum RetentionPolicy {
    SOURCE,
    CLASS,
    RUNTIME
}
```

* `RetentionPolicy.SOURCE`: 소스 코드에만 남아있다. 컴파일 시점에 제거된다.
* `RetentionPolicy.CLASS`: 컴파일 후 class 파일까지는 남아있지만 자바 실행 시점에 제거된다. (default)
* `RetentionPolicy.RUNTIME`: 자바 실행 중에도 남아있다. 대부분 이 설정을 사용한다.

## @Target

* 애노테이션을 적용할 수 있는 위치를 지정한다.

```java
package java.lang.annotation;

@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Target {
    ElementType[] value();
}
```

```java
package java.lang.annotation;

public enum ElementType {
    TYPE,
    FIELD,
    METHOD,
    PARAMETER,
    CONSTRUCTOR,
    LOCAL_VARIABLE,
    ANNOTATION_TYPE,
    PACKAGE,
    TYPE_PARAMETER,
    TYPE_USE,
    MODULE,
    RECORD_COMPONENT;
}
```

* 주로 `TYPE`, `FIELD`, `METHOD`를 사용한다.

## @Documented

* 자바 API 문서를 만들 때 해당 애노테이션이 함께 포함되는지 지정한다.
* 보통 함께 사용한다.

## @Inherited

* 자식 클래스가 애노테이션을 상속 받을 수 있다.
* **해당 기능은 클래스 상속에서만 작동하고, 인터페이스의 구현체에는 적용되지 않는다.**

### 클래스 상속에만 적용되는 이유

1. 클래스 상속과 인터페이스 구현의 차이

* 클래스 상속은 자식 클래스가 부모 클래스의 속성과 메서드를 상속받는 개념이다.
* 즉, 자식 클래스는 부모 클래스의 특성을 이어받으므로, 부모 클래스에 정의된 애노테이션을 자식 클래스가 자동으로 상속받을 수 있는 논리적 기반이 있다.
* 인터페이스는 메서드의 시그니처만을 정의할 뿐, 상태나 행위를 가지지 않기 때문에, 인터페이스의 구현체가 애노테이션을 상속한다는 개념이 잘 맞지 않는다.

2. 인터페이스와 다중 구현, 다이아몬드 문제

* 인터페이스는 다중 구현이 가능하다.
* 만약 인터페이스의 애노테이션을 구현 클래스에서 상속하게 되면 여러 인터페이스의 애노테이션 간의 충돌이나 모호한 상황이 발생할 수 있다.

# 애노테이션과 상속

* 모든 애노테이션은 `java.lang.annotation.Annotation` 인터페이스를 묵시적으로 상속 받는다.

```java
package java.lang.annotation;

public interface Annotation {
    boolean equals(Object obj);

    int hashCode();

    String toString();

    Class<? extends Annotaion> annotationType();
}
```

* `java.lang.annotation.Annotation` 인터페이스는 개발자가 직접 구현하거나 확장할 수 있는 것이 아니라, 자바 언어 자체에서 애노테이션을 위한 기반으로 사용된다.
  * `boolean equals(Object obj)`: 두 애노테이션의 동일성을 비교한다.
  * `int hashCode()`: 애노테이션의 해시코드를 반환한다.
  * `String toString()`: 애노테이션의 문자열 표현을 반환한다.
  * `Class<? extends Annotation> annotationType()`: 애노테이션의 타입을 반환한다.
* 모든 애노테이션은 기본적으로 `Annotation` 인터페이스를 확장하며, 이로 인해 자바에서 애노테이션은 특별한 형태의 인터페이스로 간주된다.
* 하지만 자바에서 애노테이션을 정의할 때, 개발자가 명시적으로 `Annotation` 인터페이스를 상속하거나 구현할 필요는 없다.
* 애노테이션을 `@interface` 키워드를 통해 정의하면, 자바 컴파일러가 자동으로 `Annotation` 인터페이스를 확장하도록 처리해준다.
* 애노테이션은 다른 애노테이션이나 인터페이스를 직접 상속할 수 없다.
  * 오직 `java.lang.annotation.Annotation` 인터페이스만 상속한다.
  * 따라서 애노테이션 사이에는 상속이라는 개념이 존재하지 않는다.

# 자바 기본 애노테이션

## @Override

```java
package java.lang;

import java.lang.annotation.*;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}
```

* 매서드 재정의가 정확하게 잘 되었는지 컴파일러가 체크하는데 사용한다.
* 애노테이션을 사용하면 자바 컴파일러가 메서드 재정의 여부를 체크해준다.
* 개발자의 실수를 자바 컴파일러가 잡아주는 좋은 애노테이션이기 때문에, 사용을 강하게 권장한다.

### @Retention(RetentionPolicy.SOURCE)

* `RetentionPolicy.SOURCE`로 설정하면 컴파일 이후에 `@Override` 애노테이션은 제거된다.
* `@Override`는 컴파일 시점에만 사용하는 애노테이션이다. 런타임에는 필요하지 않으므로 `RetentionPolicy.SOURCE`로 설정되어 있다.

## @Deprecated

* 더 이상 사용되지 않는다는 뜻이다. 해당 애노테이션이 적용된 기능은 사용을 권하지 않는다.
  * 해당 요소를 사용하면 오류가 발생할 가능성이 있다.
  * 호환되지 않게 변경되거나 향후 버전에서 제거될 수 있다.
  * 더 나은 최신 대체 요소로 대체되었다.
  * 더 이상 사용되지 않는 기능이다.

```java
package java.lang;

import java.lang.annotation.*;

import static java.lang.annotation.ElementType.*;

@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(value = {CONSTRUCTOR, FIELD, LOCAL_VARIABLE, METHOD, PACKAGE, MODULE, PARAMETER, TYPE})
public @interface Deprecated {
    String since() default "";

    boolean forRemoval() default false;
}
```

* `since`: 더 이상 사용하지 않게된 버전 정보
* `forRemoval`: 미래 버전에 코드가 제거될 예정

## @SuppressWarnings

* 경고를 억제하는 애노테이션이다.
* 자바 컴파일러가 문제를 경고하지만, 개발자가 해당 문제를 잘 알고 있기때문에, 더는 경고하지 말라고 지시하는 애노테이션이다.

```java
package java.lang;

import java.lang.annotation.*;

import static java.lang.annotation.ElementType.*;

@Retention(RetentionPolicy.SOURCE)
public @interface SuppressWarnings {
    String[] value();
}
```

* `@SuppressWarnings`에 사용하는 value 값 정리
  * **all**: 모든 경고를 억제
  * **deprecation**: 사용이 권장되지 않는(deprecated) 코드를 사용할 때 발생하는 경고를 억제한다.
  * **unchecked**: 제네릭 타입과 관련된 unchecked 경고를 억제
  * **serial**: Serializable 인터페이스를 구현할 때 serialVersionUID 필드를 선언하지 않는 경우 발생하는 경고를 억제
  * **rawtypes**: 제네릭 타입이 명시되지 않은(raw) 타입을 사용할 때 발생하는 경고를 억제
  * **unused**: 사용되지 않는 변수, 메서드, 필드 등을 선언했을 때 발생하는 경고를 억제