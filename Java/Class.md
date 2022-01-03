# 클래스(Class)
>클래스(static 코드) -생성(인스턴스 화)-> 인스턴스(dynamic memory)

## 클래스 생성하기
- 클래스를 사용하기 위해서는 클래스를 생성하여야 함
- new 예약어를 이용하여 클래스 생성
- 클래스형 변수이름 = new 생성자;
```
Student studentA = new Student();
```

## 인스턴스와 힙(heap) 메모리
- 하나의 클래스 코드로부터 여러 개의 인스턴스를 생성
- 인스턴스는 힙 메모리에 생성됨
- 각각의 인스턴스는 다른 메모리에 다른 값을 가짐
- 힙 메모리는 동적 메모리

## 용어 정리
1. 객체: 객체 지향 프로그램의 대상, 생성된 인스턴스
2. 클래스: 객체를 프로그래밍하기 위해 코드로 만든 상태
3. 인스턴스: 클래스가 메모리에 생성된 상태
4. 맴버 변수: 클래스의 속성 , 특성
5. 메서드: 맴버 변수를 이용하여 클래스의 기능을 구현
6. 참조 변수: 메모리에 생성된 인스턴스를 가리키는 변수
7. 잠조 값: 생성된 인스턴스릐 메모리 주소 값

## 디폴트 생성자(default constructor)
- 하나의 클래스에는 반드시 적어도 하나 이상의 Constructor가 존재
- 프로그래머가 Constructor를 기술하지 않으면 Default Constructor가 자동으로 생심(컴파일러가 코드에 넣어 줌)
- Default Constructor는 매개 변수가 없음
- Default Constructor는 구현부가 없음
- 만약 클래스에 매개변수가 있는 생성자를 추가하면 디폴트 생성자는 제공되지 않음

## 생성자 오버로드(constructor overload)
- 필요에 의해 생성자 추가 하는 경우 여러 개의 생성자가 하나의 클래스에 있음(overload)

## 참조 자료형(reference data type)
- 변수의 자료형 - 기본 자료형(int, long, float, double 등), 참조 자료형(String, Data, Student 등)

## 정보은닉(information hiding)
- private 접근 제어자
- 클래스의 외부에서 클래스 내부의 맴버 변수나 메서드에 접근(access)하지 못하게 하는 경우 사용
- 맴버 변수나 메서드를 외부에서 사용하지 못하도록 하여 오류를 줄일 수 있음
- 변수에 대해서는 필요한 경우 get(), set() 메서드를 제공

## public > protected > default > private
1. public: 접근 제한이 없음
2. protected: 동일한 패키지 내에 존재하거나 파생클래스에서만 접근 가능
3. dafault: 아무런 접근 제한자를 명시하지 않으면 default값이 되며, 동일한 패키지 내에서만 접근이 가능
4. private: 자기 자신의 클래스 내에서만 접근이 가능
