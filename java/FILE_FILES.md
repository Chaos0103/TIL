# File & Files

<!-- TOC -->

* [File](#file)
  * [주요 메서드](#주요-메서드)
* [Files](#files)
  * [Files 특징](#files-특징)
  * [주요 메서드](#주요-메서드-1)
  * [Files.readAllLines() vs Files.lines()](#filesreadalllines-vs-fileslines)
    * [Files.readAllLines()](#filesreadalllines)
    * [Files.lines()](#fileslines)
* [경로 표시](#경로-표시)
* [파일 복사](#파일-복사)
  * [파일 복사 V1](#파일-복사-v1)
  * [파일 복사 V2](#파일-복사-v2)
  * [파일 복사 V3](#파일-복사-v3)

<!-- TOC -->

# File

* File 클래스는 자바 1.0부터 제공된다.
* File은 파일과 디렉토리를 둘 다 다루며, 다양한 기능의 모음을 제공한다.

## 주요 메서드

* `exists()`: 파일이나 디렉토리의 존재 여부를 확인
* `createNewFile()`: 새 파일을 생성
* `mkdir()`: 새 디랙토리를 생성
* `delete()`: 파일이나 디렉토리를 삭제
* `isFile()`: 파일인지 확인
* `isDirectory()`: 디렉토리인지 확인
* `getName()`: 파일이나 디렉토리의 이름을 반환
* `length()`: 파일의 크기를 바이트 단위로 반환
* `renameTo(File dest)`: 파일의 이름을 변경하거나 이동
* `lastModified()`: 마지막으로 수정된 시간을 반환

# Files

* Files와 Path는 자바 1.7부터 제공된다.

## Files 특징

* 성능과 편의성이 모두 개선되었다.
* File은 과거의 호환을 유지하기 위해 남겨둔 기능이므로, Files 사용을 먼저 고려한다.
  * 수 많은 유틸리티 기능이 존재한다.
  * File 클래스는 물론, File과 관련된 스트림(FileInputStream, FileWriter)의 사용을 고민하기 전에 Files에 있는 기능을 먼저 확인한다.
    * 성능도 좋고, 사용하기도 더 편리하다.
* Files는 직접 생성할 수 없고, **static 메서드**를 통해 기능을 제공한다.

## 주요 메서드

* `exists()`: 파일이나 디렉토리의 존재 여부를 확인
* `createFile()`: 새 파일을 생성
* `createDirectory()`: 새 디랙토리를 생성
* `delete()`: 파일이나 디렉토리를 삭제
* `isRegularFile()`: 일반 파일인지 확인
* `isDirectory()`: 디렉토리인지 확인
* `getFileName()`: 파일이나 디렉토리의 이름을 반환
* `size()`: 파일의 크기를 바이트 단위로 반환
* `move()`: 파일의 이름을 변경하거나 이동
* `getLastModifiedTime()`: 마지막으로 수정된 시간을 반환

## Files.readAllLines() vs Files.lines()

### Files.readAllLines()

* 파일을 한 번에 다 읽고, 라인 단위로 List에 나누어 저장하고 반환한다.

### Files.lines()

* 파일을 스트림 단위로 나누어 조회한다.
  * 파일이 아주 크다면 한 번에 모든 파일을 다 메모리에 올리는 것 보다, 파일을 부분으로 나누어 메모리에 올리는 것이 더 나은 선택일 수 있다.

# 경로 표시

* 절대 경로(Absolute path)
  * 경로의 처음부터 내가 입력한 모든 경로를 다 표현한다.
  * ex1) `/Users/dong82/private/study/inflearn/java/java-adv2`
  * ex2) `/Users/dong82/private/study/inflearn/java/java-adv2/temp/..`

* 정규 경로(Canonical path)
  * 경로의 계산이 모두 끝난 경로이다. 정규 경로는 하나만 존재한다.
  * ex) `/Users/dong82/private/study/inflearn/java/java-adv2`

# 파일 복사

## 파일 복사 V1

[파일 복사 V1](files/codes/files/FileCopyV1.md)

* `FileInputStream`에서 `readAllBytes`를 통해 한 번에 모든 데이터를 읽고 `write(bytes)`를 통해 한번에 모든 데이터를 저장한다.
  * 파일(copy.dat) -> 자바(byte) -> 파일(copy_new.dat)
* 자바가 `copy.dat` 파일의 데이터를 자바 프로세스가 사용하는 메모리에 불러온다. 그리고 메모리에 있는 데이터를 `copy_new.dat`에 전달한다.

## 파일 복사 V2

[파일 복사 V2](files/codes/files/FileCopyV2.md)

* `InputStream`의 `transferTo()` 메서드를 활용한다. (자바 9)
  * `transferTo()`: `InputStream`에서 읽은 데이터를 바로 `OutputStream`으로 출력한다.
  * `transferTo()`는 성능 최적화가 되어 있기 때문에, 비슷하거나 조금 더 빠르다.
  * 파일(copy.dat) -> 자바(byte) -> 파일(copy_new.dat)

## 파일 복사 V3

[파일 복사 V3](files/codes/files/FileCopyV3.md)

* `Files.copy()`: 자바에 파일 데이터를 불러오지 않고, 운영체제의 파일 복사 기능을 사용한다.
  * 파일(copy.dat) -> 파일(copy_new.dat)