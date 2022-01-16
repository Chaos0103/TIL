# 01. 배열이란?

## 자료를 순차적으로 관리하는 구조, 배열
> 배열을 사용하면 자료형이 같은 자료 여러 개를 한 번에 관리할 수 있음
## 배열 선언과 초기화
```java
자료형[] 배열 이름 = new 자료형[개수];
자료형 배열 이름[] = new 자료형[개수];
```
- 배열 요소: 배열을 이루는 각각의 자료
- 저장하려는 자료의 성격에 맞게 자료형을 정하고 선언하려는 배열 요소 개수만큼 []안에 적음
- new 예약어는 배열을 새로 만들라는 의미
- 배열을 선언하면 선언한 자료형과 배열 길이에 따라 메모리가 할당
### 배열 초기화하기
> 자바에서 배열을 선언하면 그와 동시에 각 요소의 값이 초기화
- 배열의 자료형에 따라 정수는 0, 실수는 0.0, 객체 배열은 null로 초기화
- 배열 선언과 동시에 특정 값으로 초기화할 수도 있음
- 배열이 초기화 요소의 개수만큼 생성되므로 []안의 개수는 생략
```java
int[] studentIDs = new int[] {101, 102, 103};  // 개수는 생략함
```
- 값을 넣어서 초기화할 때 []안에 개수를 쓰면 오류가 발생
```java
int[] studentIDs = new int[3] {101, 102, 103};  // 오류 발생
```
- 선언과 동시에 초기화할 때 다음과 같이 `new int[]` 부분을 생략할 수도 있음
```java
int[] studentIDs = {101, 102, 103};  // int형 요소가 3개인 배열 생성
```
- 배열의 자료형을 먼저 선언하고 초기화하는 경우에는 `new int[]`를 생략 할 수 없음
```java
int[] studentIDs;  // 배열 자료형 선언
studentIDs = new int[] {101, 102, 103}  // new int[]를 생략할 수 없음
```
## 배열 사용하기
> 선언한 배열의 각 요소에 값을 넣을 때나 배열 요소에 있는 값을 가져올 때는 []를 사용
```java
studentIDs[0] = 10;
```
### 인덱스 연산자 []
> 배열 이름에 []를 사용하는 것을 인덱스 연산이라고 함
- 인덱스 연산자의 기능은 배열 요소가 저장된 메모리 위치를 찾아 주는 역할
### 배열 순서는 0번부터
- 배열 길이(처음에 선언한 배열 전체 요소 개수)가 n이라고 하면, 배열의 순서는 0번부터 n-1번까지
- 0번 요소를 배열의 첫 번째 요소라고 함
- 자바의 배열은 배열 길이를 나타내는 length 속성을 가짐
- 자바에서 배열 길이는 처음에 선언한 배열의 전체 요소 개수를 의미
- 전체 길이를 알고 싶은 배열 이름 뒤에 도트(.) 연산자를 붙이고 length 속성을 쓰면 배열 길이를 반환
### 전체 배열 길이와 유효한 요소 값
- 배열을 사용할 때 처음 선언한 배열 길이만큼 값을 저장해서 사용하는 경우는 많지 않음
- 전체 배열 길이와 현재 배열에 유효한 값이 저장되어 있는 배열 요소 개수가 같다고 혼동하지 말 것
## 문자 저장 배열 만들기
> 문자 자료형 배열은 `char[]`로 선언
## 객체 배열 사용하기
- 동일한 기본 자료형(int 등) 변수 여러 개를 배열로 사용할 수 있듯이 참조 자료형 변수도 여러 개를 배열로 사용할 수 있음
- 객체 배열은 int나 char 등 기본 자료형 배열과 사용 방법이 조금 다름
> 주소 값을 담을 공간이 n개 만들어지고 자동으로 각 공간은 '비어 있다'는 의미의 null값으로 초기화
## 배열 복사하기
> 기존 배열과 자료형 및 배열 크기가 똑같은 배열을 새로 만들거나 배열의 모든 요소에 자료가 꽉 차서 더 큰 배열을 만들어 기존 배열에 저장된 자료를 가져오려 할 때 배열을 복사
1. 기존 배열과 배열 길이가 같거나 더 긴 배열을 만들고 for문을 사용하여 각 요소 값을 반복해서 복사하는 방법
2. `System.arraycopy()` 메서드를 사용하는 방법

|매개변수|설명|
|-|-|
|src|복사할 배열 이름|
|srcPos|복사할 배열의 첫 번째 위치|
|dest|복사해서 붙여 넣을 대상 배열 이름|
|destPos|복사해서 대상 배열에 붙여 넣기를 시작할 첫 번째 위치|
|length|src에서 dest로 자료를 복사할 요소 개수|

### 객체 배열 복사하기
- 객체 배열도 마찬가지고 복사해서 사용할 수 있음
### 얕은 복사(shallow copy)
- 두 배열의 서로 다른 요소가 같은 인스턴스를 가리키고 있으므로 복사되는 배열의 인스턴스 값이 변경되면 두 배열 모두 영향을 받음
- 주소 값만 복사한다고 해서 '얕은 복사'라고 함
### 깊은 복사(deep copy)
- 반복문을 사용하건 `System.arraycopy()` 메서드를 사용하건 객체 배열을 복사하면 항상 인스턴스 주소가 복사
- 대부분의 경우는 이렇게 해도 문제가 없지만, 인스턴스를 따로 관리하고 싶다면 직접 인스턴스를 만들고 그 값을 복사해야 함
- 복사한 배열 요소는 기본 배열 요소와 서로 다른 인스턴스를 가리키므로 기존 배열의 요소 값이 변경되어도 영향을 받지 않는다는 것을 알 수 있음
## 향상된 for문과 배열
> 향상된 for문(enhanced for loop): 배열의 처음에서 끝까지 모든 요소를 참조할 때 사용하면 편리한 반복문
- 향상된 for문은 배열 요소 값을 순서대로 하나씩 가져와서 변수에 대입함
- 초기화와 종료 조건이 없기 때문에 모든 배열의 시작 요소부터 끝 요소까지 실행함
```java
for(변수:배열){
    반복 실행문;
}
```

<br/>

# 02. 다차원 배열

## 다차원 배열이란?
> 이차원 이상으로 구현한 배열
- 다차원 배열은 평면이나 공간 개념을 구현하는 데 사용

## 이차원 배열
```java
int[][] arr = new int[2][3];
```
- 배열의 모든 요소를 참조하려면 각 행의 기준으로 열 값을 순회하면 됨
- 이차원 배열을 초기화하려면 행과 열 개수에 맞추어서 중괄호 {}안에 콤마(,)로 구분해 값을 적음
```java
int[][] arr = {{1, 2, 3}, {4, 5, 6}};
```
- 전체 배열 길이인 `arr.length`는 행의 개수를 각 행의 길이 `arr[i].length`는 열의 개수를 나타냄

<br/>

# 03. ArrayList 클래스 사용하기

## 기본 배열의 단점과 ArrayList
- 기존 배열은 프로그램에서 사용하려면 항상 배열 길이를 정하고 시작
- 자바는 객체 배열을 좀 더 쉽게 사용할 수 있도록 객체 배열 클래스 ArrayList를 제공
- ArrayList 클래스는 객체 배열을 관리할 수 있는 멤버 변수와 메서드를 제공하므로 사용 방법만 알아 두면 편리하게 사용할 수 있음
## ArrayList 클래스의 주요 메서드
- 프로그램을 만들 때 가장 많이 사용하는 메서드

|메서드|설명|
|-|-|
|boolean add(E e)|요소 하나를 배열에 추가, E는 요소의 자료형을 의미|
|int size()|배열에 추가된 요소 전체 개수를 반환|
|E get(int index)|배열의 index 위치에 있는 요소 값을 반환|
|E remove(int index)|배열의 index 위치에 있는 요소 값을 제거하고 그 값을 반환|
|boolean isEmpty()|배열이 비어 있는지 확인|

## ArrayList 클래스 활용하기
> ArrayList를 사용할 때 어떤 자료형 객체를 사용하여 프로그래밍할 것인지 선언할 수 있음
```java
ArrayList<E> arr = new ArrayList<E>();
```
- 배열을 선언하는 부분의 <>안에 사용할 객체릐 자료형(E)을 쓰면 됨
```java
ArrayList<Book> arr = new ArrayList<Book>();
```
- 임포트(import): 내 코드에 없는 클래스를 가져와 사용할 때 이 클래스가 어디에 구현되어 있다고 알려주기 위해 코드 맨 위에 선언하는 것
- ArrayList를 사용하려면 자바 클래스를 선언하기 전에 `import java.util.ArrayList;` 문장을 반드시 써 주어야 함
- ArrayList를 생성할 때는 미리 지정할 필요 없이 `add()` 메서드를 사용해 생성자만 호출
- 요소를 하나 가져오는 메서드는 `get()`
- 배열에 추가된 요소 개수만큼만 출력하기 위해 `size()` 메서드를 사용
- `size()` 메서드는 배열에 유효한 값이 저장된 요소 개수를 반환

<br/>

# 04. 배열 응용 프로그램

## Student 클래스 구현하기
<details><summary>Student.java</summary>
<p>

```java
package Java.chapter03.arraylist;
import java.util.ArrayList;

public class Student {
    // Student 클래스의 멤버 변수
    int studentID;
    String studentName;
    ArrayList<Subject> subjectList;  // ArrayList 선언하기

    // 생성자
    public Student(int studentID, String studentName) {
        this.studentID = studentID;
        this.studentName = studentName;
        subjectList = new ArrayList<Subject>();  // ArrayList 생성하기
    }

    // 학생이 수강하는 과목을 subjectList 배열에 하나씩 추가하는 메서드
    public void addSubject(String name, int score) {
        Subject subject = new Subject();    // Subject 생성하기
        subject.setName(name);              // 과목 이름 추가하기
        subject.setScorePoint(score);       // 점수 추가하기
        subjectList.add(subject);           // 배열에 저장하기
    }
    
    public void showStudentInfo() {
        int total = 0;
        // 배열 요소 값 출력
        for(Subject s : subjectList) {
            total += s.getScorePoint();  // 총점 더하기
            System.out.println("학생 " + studentName +"의 " + s.getName() + " 과목 성적은 " + s.getScorePoint() + "입니다.");
        }
        System.out.println("학생 " + studentName + "의 총점은 " + total + " 입니다.");
    }
}

```

<p>
</details>

## Subject 클래스 구현하기
<details><summary>Subject.java</summary>
<p>

```java
package Java.chapter03.arraylist;

public class Subject {
    // Subject 클래스의 멤버 변수
    private String name;  // 과목 이름
    private int scorePoint;  // 과목 점수

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getScorePoint() {
        return scorePoint;
    }

    public void setScorePoint(int scorePoint) {
        this.scorePoint = scorePoint;
    }
}

```

<p>
</details>

## 테스트 클래스 구현 후 결과 확인하기
<details><summary>StudentTest.java</summary>
<p>

```java
package Java.chapter03.arraylist;

public class StudentTest {
    public static void main(String[] args) {
        Student studentLee = new Student(1001, "Lee");
        studentLee.addSubject("국어", 100);
        studentLee.addSubject("수학", 50);

        Student studentKim = new Student(1002, "Kim");
        studentKim.addSubject("국어", 70);
        studentKim.addSubject("수학", 85);
        studentKim.addSubject("영어", 100);

        studentLee.showStudentInfo();
        System.out.println("===================================");
        studentKim.showStudentInfo();
    }
}
```

<p>
</details>