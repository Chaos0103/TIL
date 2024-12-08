# 자원 정리

<!-- TOC -->

* [자원 (Resource)](#자원-resource)
  * [자원의 종류](#자원의-종류)
* [try-catch-finally 구문](#try-catch-finally-구문)
  * [try-catch-finally 미적용 예제](#try-catch-finally-미적용-예제)
    * [코드](#코드)
    * [실행 결과](#실행-결과)
    * [문제점](#문제점)
  * [try-catch-finally 적용 예제 1](#try-catch-finally-적용-예제-1)
    * [코드](#코드-1)
    * [실행 결과](#실행-결과-1)
    * [문제점](#문제점-1)
  * [try-catch-finally 적용 예제 2](#try-catch-finally-적용-예제-2)
    * [코드](#코드-2)
    * [실행 결과](#실행-결과-2)
  * [문제점 정리](#문제점-정리)
    * [2가지 핵심 문제](#2가지-핵심-문제)
    * [4가지 부가 문제](#4가지-부가-문제)
* [try-with-resources 구문](#try-with-resources-구문)
  * [try-with-resources 적용 예제](#try-with-resources-적용-예제)
    * [코드](#코드-3)
    * [실행 결과](#실행-결과-3)
  * [try-with-resources 장점](#try-with-resources-장점)
  * [Closeable, AutoCloseable](#closeable-autocloseable)
    * [AutoCloseable](#autocloseable)
    * [Closeable](#closeable)

<!-- TOC -->

# 자원 (Resource)

> 자바에서 자원이란 사용 후 반드시 해제해야 하는 외부 시스템과의 연결을 의미한다.

* 자원을 닫지 않으면 **메모리 누수**나 **시스템 리소스 고갈** 문제가 발생할 수 있다.

## 자원의 종류

* **파일**: `FileReader`, `BufferedReader` 등
* **데이터베이스 연결**: `Connection`, `Statement` 등
* **네트워크 소켓**: `Socket`

# try-catch-finally 구문

## try-catch-finally 미적용 예제

### 코드

```java
public class ResourceCloseMainV1 {

    public static void main(String[] args) {
        try {
            logic();
        } catch (CallException e) {
            System.out.println("CallException 예외 처리");

            Throwable[] suppressed = e.getSuppressed();
            for (Throwable throwable : suppressed) {
                System.out.println("suppressedEx = " + throwable);
            }

            throw new RuntimeException(e);
        } catch (CloseException e) {
            System.out.println("CloseException 예외 처리");
            throw new RuntimeException(e);
        }
    }

    private static void logic() throws CallException, CloseException {
        ResourceV1 resource1 = new ResourceV1("resource1");
        ResourceV1 resource2 = new ResourceV1("resource2");

        resource1.call();
        resource2.callEx();

        System.out.println("자원 정리");
        resource2.closeEx();
        resource1.closeEx();
    }
}
```

### 실행 결과

```text
resource1 call
resource2 callEx
CallException 예외 처리
Exception in thread "main" java.lang.RuntimeException: network.tcp.autocloseable.CallException: resource2 ex
	at network.tcp.autocloseable.ResourceCloseMainV1.main(ResourceCloseMainV1.java:10)
Caused by: network.tcp.autocloseable.CallException: resource2 ex
	at network.tcp.autocloseable.ResourceV1.callEx(ResourceV1.java:17)
	at network.tcp.autocloseable.ResourceCloseMainV1.logic(ResourceCloseMainV1.java:22)
	at network.tcp.autocloseable.ResourceCloseMainV1.main(ResourceCloseMainV1.java:7)
```

### 문제점

* `logic()`에서 `resource2.callEx()`를 호출하면서 `CallException`이 발생했다.
* 예외가 발생하면서 이후 코드는 정상 호출되지 않았고, **자원이 정리되지 않는 문제**가 발생했다.

## try-catch-finally 적용 예제 1

### 코드

```java
public class ResourceCloseMainV2 {

    public static void main(String[] args) {
        try {
            logic();
        } catch (CallException e) {
            System.out.println("CallException 예외 처리");
            throw new RuntimeException(e);
        } catch (CloseException e) {
            System.out.println("CloseException 예외 처리");
            throw new RuntimeException(e);
        }
    }

    private static void logic() throws CallException, CloseException {
        ResourceV1 resource1 = null;
        ResourceV1 resource2 = null;

        try {
            resource1 = new ResourceV1("resource1");
            resource2 = new ResourceV1("resource2");

            resource1.call();
            resource2.callEx();
        } catch (CallException e) {
            System.out.println("ex: " + e);
            throw e;
        } finally {
            if (resource2 != null) {
                resource2.closeEx();
            }

            if (resource1 != null) {
                resource1.closeEx();
            }
        }
    }
}
```

### 실행 결과

```text
resource1 call
resource2 callEx
ex: network.tcp.autocloseable.CallException: resource2 ex
resource2 closeEx
CloseException 예외 처리
Exception in thread "main" java.lang.RuntimeException: network.tcp.autocloseable.CloseException: resource2 ex
	at network.tcp.autocloseable.ResourceCloseMainV2.main(ResourceCloseMainV2.java:13)
Caused by: network.tcp.autocloseable.CloseException: resource2 ex
	at network.tcp.autocloseable.ResourceV1.closeEx(ResourceV1.java:26)
	at network.tcp.autocloseable.ResourceCloseMainV2.logic(ResourceCloseMainV2.java:32)
	at network.tcp.autocloseable.ResourceCloseMainV2.main(ResourceCloseMainV2.java:7)
```

### 문제점

* **null 체크**
  * `resource2` 객체를 생성하기 전에 예외가 발생하면 null이므로, null 체크를 해야한다.
  * `resource1`의 경우에도 생성하는 중에 예외가 발생하면 null이므로, null 체크가 필요하다.
* **자원 정리중에 예외 발생**
  * `finally` 코드 블록은 항상 호출되기 때문에 자원이 잘 정리될 것 같지만, `resource2.closeEx()`를 호출하면서 예외가 발생한다.
  * 예외가 발생하면서 이후 코드는 정상 호출되지 않았고, **자원이 정리되지 않는 문제**가 발생했다.
* **핵심 예외가 변경**
  * 코드에서 발생한 핵심적인 예외는 `CallException`이지만, `finally` 코드 불록에서 자원을 정리하면서 `CloseException` 예외가 추가로 발생했다.
  * `logic()`을 호출한 클라이언트는 핵심 예외인 `CallException`이 아닌, 자원을 정리하는 과정에서 발생한 `CloseException`을 받게 된다.

## try-catch-finally 적용 예제 2

### 코드

```java
public class ResourceCloseMainV3 {

    public static void main(String[] args) {
        try {
            logic();
        } catch (CallException e) {
            System.out.println("CallException 예외 처리");
            throw new RuntimeException(e);
        } catch (CloseException e) {
            System.out.println("CloseException 예외 처리");
            throw new RuntimeException(e);
        }
    }

    private static void logic() throws CallException, CloseException {
        ResourceV1 resource1 = null;
        ResourceV1 resource2 = null;

        try {
            resource1 = new ResourceV1("resource1");
            resource2 = new ResourceV1("resource2");

            resource1.call();
            resource2.callEx();
        } catch (CallException e) {
            System.out.println("ex: " + e);
            throw e;
        } finally {
            if (resource2 != null) {
                try {
                    resource2.closeEx();
                } catch (CloseException e) {
                    System.out.println("close ex: " + e);
                }
            }

            if (resource1 != null) {
                try {
                    resource1.closeEx();
                } catch (CloseException e) {
                    System.out.println("close ex: " + e);
                }
            }
        }
    }
}
```

### 실행 결과

```text
resource1 call
resource2 callEx
ex: network.tcp.autocloseable.CallException: resource2 ex
resource2 closeEx
close ex: network.tcp.autocloseable.CloseException: resource2 ex
resource1 closeEx
close ex: network.tcp.autocloseable.CloseException: resource1 ex
CallException 예외 처리
Exception in thread "main" java.lang.RuntimeException: network.tcp.autocloseable.CallException: resource2 ex
	at network.tcp.autocloseable.ResourceCloseMainV3.main(ResourceCloseMainV3.java:10)
Caused by: network.tcp.autocloseable.CallException: resource2 ex
	at network.tcp.autocloseable.ResourceV1.callEx(ResourceV1.java:17)
	at network.tcp.autocloseable.ResourceCloseMainV3.logic(ResourceCloseMainV3.java:26)
	at network.tcp.autocloseable.ResourceCloseMainV3.main(ResourceCloseMainV3.java:7)
```

## 문제점 정리

### 2가지 핵심 문제

* `close()` 시점에 실수로 예외를 던지면, 이후 다른 자원을 닫을 수 없는 문제 발생
* `finally` 블럭 안에서 자원을 닫을 때 예외가 발생하면, 핵심 예외가 `finally`에서 발생한 부가 예외로 바뀌면서 핵심 예외가 사라짐

### 4가지 부가 문제

* `resource` 변수를 선언하면서 동시에 할당할 수 없음 (`try`, `finally` 코드 블록과 변수 스코프가 다른 문제)
* `catch` 이후에 `finally` 코드 블록을 호출하면서 자원 정리가 조금 늦어짐
* 개발자가 실수로 `close()`를 호출하지 않을 가능성
* 보통 자원을 생성한 순서의 역순으로 자원을 닫아야 하지만, 개발자가 `close()` 호출 순서를 실수

# try-with-resources 구문

## try-with-resources 적용 예제

### 코드

```java
package network.tcp.autocloseable;

public class ResourceCloseMainV4 {

    public static void main(String[] args) {
        try {
            logic();
        } catch (CallException e) {
            System.out.println("CallException 예외 처리");
            throw new RuntimeException(e);
        } catch (CloseException e) {
            System.out.println("CloseException 예외 처리");
            throw new RuntimeException(e);
        }
    }

    private static void logic() throws CallException, CloseException {
        try (
            ResourceV2 resource1 = new ResourceV2("resource1");
            ResourceV2 resource2 = new ResourceV2("resource2")
        ) {
            resource1.call();
            resource2.callEx();
        } catch (CallException e) {
            System.out.println("ex: " + e);
            throw e;
        }
    }
}
```

### 실행 결과

```text
resource1 call
resource2 callEx
resource2 close
resource1 close
ex: network.tcp.autocloseable.CallException: resource2 ex
CallException 예외 처리
suppressedEx = network.tcp.autocloseable.CloseException: resource2 ex
suppressedEx = network.tcp.autocloseable.CloseException: resource1 ex
Exception in thread "main" java.lang.RuntimeException: network.tcp.autocloseable.CallException: resource2 ex
	at network.tcp.autocloseable.ResourceCloseMainV4.main(ResourceCloseMainV4.java:16)
Caused by: network.tcp.autocloseable.CallException: resource2 ex
	at network.tcp.autocloseable.ResourceV2.callEx(ResourceV2.java:17)
	at network.tcp.autocloseable.ResourceCloseMainV4.logic(ResourceCloseMainV4.java:29)
	at network.tcp.autocloseable.ResourceCloseMainV4.main(ResourceCloseMainV4.java:7)
	Suppressed: network.tcp.autocloseable.CloseException: resource2 ex
		at network.tcp.autocloseable.ResourceV2.close(ResourceV2.java:23)
		at network.tcp.autocloseable.ResourceCloseMainV4.logic(ResourceCloseMainV4.java:24)
		... 1 more
	Suppressed: network.tcp.autocloseable.CloseException: resource1 ex
		at network.tcp.autocloseable.ResourceV2.close(ResourceV2.java:23)
		at network.tcp.autocloseable.ResourceCloseMainV4.logic(ResourceCloseMainV4.java:24)
		... 1 more
```

## try-with-resources 장점

* 리소스 누수 방지
  * 모든 리스소가 제대로 닫히도록 보장한다.
  * 실수로 `finally` 블록을 적지 않거나, `finally` 블록 안에서 자원 해제 코드를 누락하는 문제들을 예방할 수 있다.
* 코드 간결성 및 가독성 향상
  * 명시적인 `close()` 호출이 필요 없어 코드가 더 간결하고 읽기 쉬워진다.
* 스코프 범위 한정
  * 변수의 스코프가 `try` 블록 안으로 한정된다. 따라서 코드 유지보수가 더 쉬워진다.
* 조금 더 빠른 자원 해제
  * `try` 블록이 끝나면 즉시 `close()`를 호출한다.
* 자원 정리 순서
  * 먼저 선언한 자원을 나중에 정리한다.
* 부가 예외 포함
  * `try-with-resources`는 핵심 예외를 반환한다.
  * 부가 예외는 핵심 예외안에 Suppressed로 담아서 반환한다.

## Closeable, AutoCloseable

* `try-with-resources`에서 사용할 수 있는 자원은 반드시 `Closeable`, `AutoCloseable` 인터페이스를 구현해야 한다.
* 두 인터페이스 모두 자원을 닫을 수 있는 메서드를 제공한다.

### AutoCloseable

```java
package java.lang;

public interface AutoCloseable {
    void close() throws Exception;
}
```

* 자바 7부터 제공하는 인터페이스로, 자원을 닫기 위한 `close()` 메서드를 정의한다.
* `AutoCloseable`를 구현한 객체는 `try-with-resources` 구문에서 사용할 수 있다.
* 모든 자원에 적용 가능한 범용적인 인터페이스로 `Exception`을 던질 수 있다.

### Closeable

```java
package java.io;

import java.io.IOException;

public interface Closeable extends AutoCloseable {
    public void close() throws IOException;
}
```

* 자바 5부터 제공하는 인터페이스로, I/O 자원을 닫기 위한 `close()` 메서드를 정의한다.
* 자바 7에서 `AutoCloseable`이 새롭게 등장한 후, 기존 `Closeable` 인터페이스에 `AutoCloseable`을 상속받는 구조가 추가됬다.
* **I/O 작업에 특화**된 인터페이스로, `IOException`만 던질 수 있다. 주로 파일 입출력에 사용된다.