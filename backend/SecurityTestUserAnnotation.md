<!-- TOC -->
* [@WithMockUser](#withmockuser)
* [@WithAnonymousUser](#withanonymoususer)
* [@WithUserDetails](#withuserdetails)
<!-- TOC -->

# @WithMockUser

> 사용자 인증 정보를 담은 Authentication을 UsernamePasswordAuthenticationToken으로 넣어주고, Principal은 User 객체에 넣어 SecurityContext에
> 보관해준다.

* Controller Layer 테스트 시에 Spring Security에 설정한 인증 정보를 제공한다.

```java

@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
@WithSecurityContext(
    factory = WithMockUserSecurityContextFactory.class
)
public @interface WithMockUser {
    String value() default "user";

    String username() default "";

    String[] roles() default {"USER"};

    String[] authorities() default {};

    String password() default "password";

    @AliasFor(
        annotation = WithSecurityContext.class
    )
    TestExecutionEvent setupBefore() default TestExecutionEvent.TEST_METHOD;
}
```

# @WithAnonymousUser

* `@WithMockUser`와 반대의 의미로 인증되지 않은 사용자를 만들어서 제공한다.
* UsernamePasswordAuthenticationToken이 아닌, AnonymousAuthenticationToken을 Authentication에 담아서 제공한다.

```java

@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
@WithSecurityContext(
    factory = WithAnonymousUserSecurityContextFactory.class
)
public @interface WithAnonymousUser {
    @AliasFor(
        annotation = WithSecurityContext.class
    )
    TestExecutionEvent setupBefore() default TestExecutionEvent.TEST_METHOD;
}
```

# @WithUserDetails

* 사용자 정보가 `@WithMockUser`나 `@WithAnonymousUser`와 동일한 경우는 바로 사용하면 되지만, 추가적인 정보가 있다면 `UserDetailsService`를 구현한 서비스에서 사용자 정보를
  load하도록 설정해줄 수 있다.

```java

@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
@WithSecurityContext(
    factory = WithUserDetailsSecurityContextFactory.class
)
public @interface WithUserDetails {
    String value() default "user";

    String userDetailsServiceBeanName() default "";

    @AliasFor(
        annotation = WithSecurityContext.class
    )
    TestExecutionEvent setupBefore() default TestExecutionEvent.TEST_METHOD;
}
```
