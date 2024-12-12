<!-- TOC -->
* [mock() vs @Mock vs @MockBean](#mock-vs-mock-vs-mockbean)
  * [Mockito.mock()](#mockitomock)
  * [@Mock](#mock)
  * [@MockBean](#mockbean)
* [정리](#정리)
<!-- TOC -->

# mock() vs @Mock vs @MockBean

## Mockito.mock()

* 클래스 또는 인터페이스의 mock 객체를 생성할 수 있다.
* 또한, mock 객체를 사용하여 해당 객체가 가진 메서드의 반환값을 조작하거나 해당 메서드가 호출되었는지 확인할 수 있다.

```java

class MemberRepositoryTest {

    @Test
    void counts() {
        MemberRepository memberRepository = Mockito.mock(MemberRepository.class);
        given(memberRepository.counts())
            .willReturn(10);

        int counts = memberRepository.counts();

        assertThat(counts).isEqualTo(10);
    }
}
```

## @Mock

* `Mockito.mock()` 메서드의 축약어이다.
  * `Mockito.mock()` 메서드와 달리 테스트 클래스에서만 사용해야 한다.
  * 또한 Mockito annotation을 활성화해야 `@Mock` 어노테이션을 사용할 수 있다.
* JUnit5에서는 `@ExtendWith(MockitoExtension.class)`로 활성화 할 수 있다.

```java

@ExtendWith(MockitoExtension.class)
class MemberRepositoryTest {

    @Mock
    private MemberRepository memberRepository;

    @Test
    void counts() {
        given(memberRepository.counts())
            .willReturn(10);

        int counts = memberRepository.counts();

        assertThat(counts).isEqualTo(10);
    }
}
```

## @MockBean

* 스프링 애플리케이션 컨텍스트에 Mock 객체를 추가하기 위해 사용한다.
  * 스프링 애플리케이션 컨텍스트에서 동일한 유형을 가진 기존 빈을 대체한다.
    * 동일한 유형의 빈이 정의되어 있지 않으면 새로운 빈이 추가된다.
* 외부 서비스와 같은 특정 빈을 모킹해야 하는 통합 테스트 작성시에 유용하다.
* JUnit5에서는 `@ExtendWith(SpringExtension.class)`를 사용하여 테스트를 실행해야 한다.
  * 참고: `@SpringBootTest` 내부에 `@ExtendWith(SpringExtension.class)`가 선언되어 있다. 하지만 @SpringBootTest를 사용하면 스프링을 띄우고 테스트를
    실행한다.

```java
//@SpringBootTest
@ExtendWith(SpringExtension.class)
class MemberRepositoryTest {

    @MockBean
    private MemberRepository memberRepository;

    @Test
    void counts() {
        given(memberRepository.counts())
            .willReturn(10);

        int counts = memberRepository.counts();

        assertThat(counts).isEqualTo(10);
    }
}
```

# 정리

* `Mockito.mock()` 메서드나 `@Mock`을 사용하면, 스프링 애플리케이션 컨텍스트를 로딩하지 않고 사용할 수 있어, 단위 테스트 작성시 유용하게 사용될 수 있다.
* 하지만 단위 테스트 시에도 무분별하게 mocking을 통해 테스트를 작성하기보다, 외부 라이브러리와 같이 직접 제어할 수 없는 경우에 mocking을 사용하면 좋을 것 같다.
* 반면 `@MockBean`은 스프링 애플리케이션 컨텍스트를 로딩해야 사용할 수 있기 때문에, 스프링 애플리케이션 컨텍스트를 필요로 하는 통합 테스트에서 mocking이 필요할 때 유용하게 사용될 수 있다.
