# 문자 인코딩

<!-- TOC -->

* [컴퓨터와 문자 인코딩](#컴퓨터와-문자-인코딩)
  * [ASCII 문자 집합](#ascii-문자-집합)
  * [ISO_8859_1](#iso_8859_1)
  * [한글 문자 집합](#한글-문자-집합)
    * [EUC-KR](#euc-kr)
    * [MS949](#ms949)
  * [전세계 문자 집합](#전세계-문자-집합)
    * [문제점](#문제점)
    * [유니코드의 등장](#유니코드의-등장)
    * [UTF-16](#utf-16)
    * [UTF-8](#utf-8)
  * [UTF-8이 현대의 사실상 표준 인코딩 기술이 된 이유](#utf-8이-현대의-사실상-표준-인코딩-기술이-된-이유)
* [한글이 깨지는 가장 큰 2가지 이유](#한글이-깨지는-가장-큰-2가지-이유)

<!-- TOC -->

# 컴퓨터와 문자 인코딩

* 문자 인코딩: 문자 집합을 통해 문자를 숫자로 변환하는 것
* 문자 디코딩: 문자 집합을 통해 숫자를 문자로 변환하는 것

## ASCII 문자 집합

* 각 컴퓨터 회사가 독자적인 문자 집합을 사용한다면, 서로 다른 컴퓨터 간에 문자가 올바르게 표시되지 않는 문제가 발생할 수 있다.
* 이러한 호환성 문제를 해결하기 위해 ASCII(American Standard Code for Information Interchange)라는 표준 문자 집합이 1960년도에 개발되었다.
* 초기 컴퓨터에서는 주로 영문 알파벳, 숫자, 키보드의 특수문자, 스페이스, 엔터와 같은 기본적인 문자만 표현하면 충분했다.
* 따라서 7bit를 사용하여 총 128가지 문자를 표현할 수 있는 ASCII 공식 문자 집합이 만들어졌다.

## ISO_8859_1

* 1980년도 국제 표준화 기구에서 기존 ASCII에 서유럽 문자를 추가한 새로운 문자 규격을 만들었다.
* 8bit(1byte)를 사용하여 총 256가지 문자를 표현할 수 있다.
  * 기존 7bit ASCII(0 ~ 127)는 그대로 유지하고, 이후 128가지(128 ~ 255) 문자를 추가했다.
* 기존 ASCII 문자 집합과 호환이 가능하다.

## 한글 문자 집합

### EUC-KR

* 1980년도 초창기에 만들어진 한글 문자 집합이다.
* 모든 한글을 담는 것 보다는 자주 사용하는 한글 2,350개만 포함해서 만들었다. ('뷁' 미포함)
* 16bit(2byte)를 사용하여 총 65,536가지 문자를 표현할 수 있다.
  * 한글은 글자가 아주 많기 때문에 1byte(256가지 표현 가능)로 표현하는 것은 불가능하다.
* ASCII + 자주 사용하는 한글 2,350개 + 한국에서 자주 사용하는 기타 글자
  * 한국에서 자주 사용하는 한자 4,888개
  * 일본어 가타카나 등도 함께 포함
* ASCII는 1byte, 한글은 2byte를 사용한다.
  * 영어를 사용하면 1byte, 한글을 사용하면 2byte를 메모리에 저장한다.
* 기존 ASCII 문자 집합과 호환이 가능하다.

### MS949

* 1990년도 마이크로소프트가 EUC-KR을 확장하여 만든 한글 문자 집합이다.
* 기존 EUC-KR과 호환을 이루면서 한글 11,172자를 모두 수용하도록 만들었다.
* EUC-KR과 마찬가지로 ASCII는 1byte, 한글은 2byte를 사용한다.
* 기존 ASCII 문자 집합과 호환이 가능하다.
* 윈도우 시스템에서 계속 사용된다.

## 전세계 문자 집합

### 문제점

* EUC-KR이나 MS949 같은 한글 문자표를 PC에 설치하지 않으면 다른 나라 사람들은 한글로 작성된 문서를 열어볼 수 없다.
* 1980년대 말, 다양한 문자 인코딩 표준이 존재했지만, 이들은 모두 특정 언어 또는 문자 세트를 대상으로 했기 때문에 국제적으로 호환성 문제가 많았다.

### 유니코드의 등장

* 위 문제점을 해결하기 위해 전 세계의 모든 문자를 단일 문자 세트로 표현할 수 있는 유니코드(Unicode) 표준이 1990년대에 도입되었다.
* 즉, 전세계의 모든 문자와 기호를 하나의 표준으로 통합하여 표현할 수 있는 문자 집합을 반드는 것이다.
* UTF-16, UTF-8의 시작이다.

### UTF-16

* 1990년도, 16bit(2byte) 기반으로 한 UTF-16이 등장했다.
* 자주 사용하는 기본 다국어들은 2byte(65,536가지 표현 가능)로 표현한다.
  * 영어, 유럽 언어, 한국어, 중국어, 일본어 등
* 그 외는 4byte(약 42억가지 표현 가능)로 표현한다.
  * 고대 문자, 이모지, 중국어 확장 한자 등
* **단점: ASCII 영문도 2byte를 사용하고, ASCII와 호환되지 않는다.**
  * 영문의 경우 다른 집합보다 2배의 메모리를 더 사용한다.
  * 웹에 있는 문서의 80% 이상은 영어 문서이다.
  * ASCII와 호환되지 않는다는 점도 큰 단점 중 하나이다.
* 초반에는 UTF-16이 인기였으며, 이 시기에 등장한 자바도 언어 내부적으로 문자를 표현할 때 UTF-16을 사용한다.
  * 자바의 `char` 타입이 2byte를 사용한다.
* 대부분의 문자를 2byte로 처리하기 때문에 계산이 편리하다.

### UTF-8

* 1990년도, 8bit(1byte) 기반으로 가변길이 인코딩이 가능한 UTF-8이 등장했다.
* 1byte ~ 4byte를 사용해서 문자를 인코딩한다.
  * 1byte: ASCII, 영문, 기본 라틴 문자
  * 2byte: 그리스어, 하브라어 라틴 확장 문자
  * 3byte: 한글, 한자, 일본어
  * 4byte: 이모지, 고대 문자 등
* 단점: 상대적으로 사용이 복잡하다.
  * UTF-16은 대부분의 기본 문자들이 2byte로 표현되기 때문에, 문자열의 특정 문자에 접근하거나 문자 수를 세는 작업이 상대적으로 간단하다.
* 단점: ASCII를 제외한 일부 언어에서 더 많은 용량을 사용한다.
  * ASCII 문자를 1byte, 비 ASCII 문자를 2 ~ 4byte로 인코딩한다.
* **장점: ASCII 문자는 1바이트로 표현할 수 있고, ASCII 호환이 가능하다.**
* **현대의 사실상 표준 인코딩 기술이다.**
  * 1990년도 후반 ~ 2000년도 초반에 인터넷과 웹이 빠르게 성장하면서 저변 확대
  * 2008년 W3C 웹 표준에 UTF-8 채택
  * 현재 대부분의 웹사이트와 애플리케이션에서 기본 인코딩으로 사용

## UTF-8이 현대의 사실상 표준 인코딩 기술이 된 이유

* 저장 공간 절약과 네트워크 효율성
* ASCII와의 호환성

# 한글이 깨지는 가장 큰 2가지 이유

* EUC-KR(MS949), UTF-8이 서로 호환되지 않는다.
  * 한글이 깨지는 대부분의 문제는 UTF-8로 인코딩한 한글을 EUC-KR(MS949)로 디코딩하거나 또는 반대일 때 발생한다.
* EUC-KR(MS949) 또는 UTF-8로 인코딩한 한글을 ISO-8859-1로 디코딩할 때 발생한다.
  * ISO-8859-1은 한글을 지원하지 않는다.