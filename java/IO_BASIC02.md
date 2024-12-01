# IO (Input / Output)

<!-- TOC -->

* [Reader, Writer](#reader-writer)
  * [byte를 다루는 클래스](#byte를-다루는-클래스)
* [기타 스트림](#기타-스트림)
  * [PrintStream](#printstream)
  * [DataStream](#datastream)
  * [ObjectStream](#objectstream)
    * [객체 직렬화 (Serialization)](#객체-직렬화-serialization)
    * [객체 직렬화의 한계](#객체-직렬화의-한계)
    * [객체 직렬화의 대안 1 - XML](#객체-직렬화의-대안-1---xml)
    * [객체 직렬화의 대안 2 - JSON](#객체-직렬화의-대안-2---json)
* [정리](#정리)

<!-- TOC -->

# Reader, Writer

> 모든 데이터는 byte 단위(숫자)로 저장된다.

* 자바는 byte를 다루는 I/O 클래스와 문자를 다루는 I/O 클래스를 둘로 나누었다.

## byte를 다루는 클래스

```java
public abstract class InputStream implements Closeable {
    public abstract int read() throws IOException;

    public int read(byte[] b) throws IOException {
        return read(b, 0, b.length);
    }

    public byte[] readAllBytes() throws IOException {
        return readNBytes(Integer.MAX_VALUE);
    }
}

public class FileInputStream extends InputStream {
}

public class ByteArrayInputStream extends InputStream {
}

public class Socket implements java.io.Closeable {
    private static class SocketInputStream extends InputStream {
    }
}
```

```java
public abstract class OutputStream implements Closeable, Flushable {
    public abstract void write(int b) throws IOException;

    public void write(byte[] b) throws IOException {
        write(b, 0, b.length);
    }
}

public class FileOutputStream extends OutputStream {
}

public class ByteArrayOutputStream extends OutputStream {
}

public class Socket implements java.io.Closeable {
    private static class SocketOutputStream extends OutputStream {
    }
}
```

* byte를 다루는 클래스는 `OutputStream`, `InputStream의` 자식이다.
  * 부모 클래스의 기본 기능도 `byte` 단위를 다룬다.
  * 클래스 이름 마지막에 보통 `OutputStream`, `InputStream이` 붙어있다.

```java
public abstract class Writer implements Appendable, Closeable, Flushable {
    public void write(String str) throws IOException {
        write(str, 0, str.length());
    }

    public void write(char[] cbuf) throws IOException {
        write(cbuf, 0, cbuf.length);
    }
}

public class OutputStreamWriter extends Writer {
}

public class FileWriter extends OutputStreamWriter {
}

public class BufferedWriter extends Writer {
}
```

```java
public abstract class Reader implements Readable, Closeable {
    public int read(char[] cbuf) throws IOException {
        return read(cbuf, 0, cbuf.length);
    }
}

public class InputStreamReader extends Reader {
}

public class FileReader extends InputStreamReader {
}

public class BufferedReader extends Reader {
}
```

* 문자를 다루는 클래스는 `Writer`, `Reader의` 자식이다.
  * 부모 클래스의 기본 기능은 `String`, `char` 같은 문자를 다룬다.
  * 클래스 이름 마지막에 보통 `Writer`, `Reader`가 붙어있다.

# 기타 스트림

## PrintStream

* 콘솔에 출력하는 것 처럼 파일이나 다른 스트림에 문자를 출력할 수 있다.

## DataStream

* 자바의 `String`, `int`, `double`, `boolean` 같은 데이터 형을 편리하게 다룰 수 있다.
* 주의할 점: 저장한 순서대로 읽어야 한다.

## ObjectStream

* 메모리에 보관되어 있는 인스턴스를 파일에 편리하게 저장할 수 있다.

### 객체 직렬화 (Serialization)

* 1990년대에 등장한 기술로, 메모리에 있는 객체 인스턴스를 바이트 스트림으로 변환하여 파일에 저장하거나 네트워크를 통해 전송할 수 있도록 하는 기능이다.
  * 객체의 상태를 유지하여 나중에 역직렬화(Deserialization)를 통해 원래의 객체로 복원할 수 있다.

```java
package java.io;

public interface Serializable {
}
```

* 아무런 기능없이, 단지 직렬화 가능한 클래스란은 것을 표시하기 위한 마커 인터페이스이다.

### 객체 직렬화의 한계

* 버전 관리의 어려움
  * 클래스 구조가 변경되면 이전에 직렬화된 객치와의 호환성 문제가 발생한다.
  * serialVersionUID 관리가 복잡하다.
* 플랫폼 종속성
  * 자바 직렬화는 자바 플랫폼에 종속적이어서 다른 언어나 시스템과의 상호 운용성이 떨어진다.
* 성능 이슈
  * 직렬화/역직렬화 과정이 상대적으로 느리고 리소스를 많이 사용한다.
* 유연성 부족
  * 직렬화된 형식의 커스터마이즈하기 어렵다.
* 크기 효율성
  * 직렬화된 데이터의 크기가 상대적으로 크다.

### 객체 직렬화의 대안 1 - XML

```xml

<member>
    <id>id1</id>
    <name>name1</name>
    <age>20</age>
</member>
```

* 플랫폼 종속성 문제를 해결하기 위해 2000년대 초반에 XML이라는 기술이 인기를 끌었다.
* 하지만 XML은 매우 유연하고 강력했지만, **복잡성**과 **무거움**이라는 문제가 있었다. 태그를 포함한 XML 문서의 크기가 커서 네트워크 전송 비용도 증가했다.

### 객체 직렬화의 대안 2 - JSON

```json
{
  "member": {
    "id": "id1",
    "name": "name1",
    "age": 20
  }
}
```

* JSON은 **가볍고 간결**하며, 자바스크립트와의 자연스러운 호환성 덕분에 웹 개발자들 사이에서 빠르게 확산되었다.
* 2000년대 후반, **웹 API와 RESTful 서비스**가 대중화되면서 JSON은 표준 데이터 교환 포맷으로 자리 잡았다.
* 지금은 웹 환경에서 데이터를 교환할 때 JSON이 사실상 표준 기술이다.

# 정리

* 기본(기반, 메인) 스트림
  * File, 메모리, 콘솔등에 직접 접근하는 스트림
  * 단독으로 사용할 수 있다.
  * FileInputStream, FileOutputStream, FileReader, FileWriter, ByteArrayInputStream, ByteArrayOutputStream
* 보조 스트림
  * 기본 스트림을 도와주는 스트림
  * 단독으로 사용할 수 없음, 반드시 대상 스트림이 있어야함
  * BufferedInputStream, BufferedOutputStream, InputStreamReader, OutputStreamWriter, DataOutputStream, DataInputStream,
    PrintStream
* 자바 객체 직렬화는 대부분 사용하지 않는다.
  * JSON이 사실상 표준이다.