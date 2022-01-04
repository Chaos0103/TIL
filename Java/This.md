# This
## this가 하는 일
- 자신의 메모리를 가리킴(많이 사용)
- 생성자에서 다른 생성자를 호출(많이 사용)
- 자신의 주소를 반환함

## 자신의 메모리를 가리키는 this
- 생성된 인스턴스 스스로를 가리키는 예약어
```
public Person(String name, int age){
  this.name = name;
  this.age = age;
}
```
> 위 코드에서 this를 생략하게 되면 name이나 age는 파라미터로 사용되는 name과 age로 인식된다.

## 생성자에서 다른 생성자를 호출하는 this
- this를 이용하여 다른 생성를 호출할 때는 그 이전에 어떤 statement도 사용할 수 없다.
```
public Person(){
  this("이름없음", 1);
public Person(String name, int age){
  this.name = name;
  this.age = age;
}
```
> 위와 같이 생성자가 여러개이고 파라미터만 다른 경우 constructor overloading이라고 한다.
