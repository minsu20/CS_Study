# 전략패턴?
**런타임시에 우리에게 알고리즘을 선택할수 있도록 허용**해주는 행위 패턴이다.

실행 중에 객체의 행동 또는 알고리즘을 선택하고 변경할 수 있도록 해준다. 다른 말로 하면, 특정한 행동을 캡슐화하여 사용하는 패턴이다. 전략 패턴에서는 컨텍스트(Context)가 전략(Strategy) 객체를 사용하여 필요한 작업을 수행하고, 전략은 인터페이스를 통해 컨텍스트에 연결된다.
<img width="894" alt="Untitled" src="https://github.com/minsu20/CS_Study/assets/86006389/10e6352c-9315-4f70-aa20-ed577d4e518d">

**구성 요소:**

1. **Context**: 전략을 사용하는 역할. 전략 객체를 구성하거나 이를 통해 비즈니스 로직을 실행한다.
2. **Strategy**: 모든 전략 클래스에 대한 인터페이스 또는 추상 클래스. Context는 Strategy 인터페이스를 사용하여 알고리즘을 실행한다.
3. **ConcreteStrategy**: Strategy 인터페이스를 구현하는 클래스들. 실제 알고리즘의 구현부이다.

전략 패턴으로 구현된 코드는 **직접 행위에 대한 코드를 수정할 필요 없이 전략만 변경하여 유연하게 확장**할 수 있게 된다.

## 전략 패턴이 사용하지 않은 경우

```java
@Service
@Transactional
@RequiredArgsConstructor
public class SignInUserCase {

    private final MemberAuthUserCase internalAuthService;
    private final TokenUtil tokenUtil;
    private final KakaoSignInUserCase kakaoSignInUserCase;
    private final AppleSignInUserCase appleSignInUserCase;

    public SignInResponse signIn(String token, String providerInfo) {
        //1. 사용자 정보 가져오기
        Member member = getUserDataFromPlatform(token, providerInfo);
        //2. 로그인 및 회원가입
        Member authenticatedMember = internalAuthService.auth(member, providerInfo);
        //3. security 처리
        AuthenticationUtil.makeAuthentication(authenticatedMember);
        //4. token 만들기
        TokenInfoResponse tokenResponse = tokenUtil.createToken(authenticatedMember, authenticatedMember.getRegistrationStatus().equals(RegistrationStatus.COMPLETED));
        //5. refresh token 저장
        tokenUtil.storeRefreshToken(authenticatedMember.getSocialId(), tokenResponse);

        return SignInResponse.from(tokenResponse, authenticatedMember.getRegistrationStatus());
    }

    private Member getUserDataFromPlatform(String accessToken, String providerInfo) {
        if (providerInfo.contains("kakao")) return kakaoSignInUserCase.getUserData(accessToken);
        else return appleSignInUserCase.getUserData(accessToken);
    }
}
```

만약 여기서 구글 로그인이 추가되었다면

```java
@Service
@Transactional
@RequiredArgsConstructor
public class SignInUserCase {

    private final MemberAuthUserCase internalAuthService;
    private final TokenUtil tokenUtil;
    private final KakaoSignInUserCase kakaoSignInUserCase;
    private final AppleSignInUserCase appleSignInUserCase;
		//====바뀐 부분====//
		private final GoogleSignInUserCase googleSignInUserCase;

    public SignInResponse signIn(String token, String providerInfo) {
        //1. 사용자 정보 가져오기
        Member member = getUserDataFromPlatform(token, providerInfo);
        //2. 로그인 및 회원가입
        Member authenticatedMember = internalAuthService.auth(member, providerInfo);
        //3. security 처리
        AuthenticationUtil.makeAuthentication(authenticatedMember);
        //4. token 만들기
        TokenInfoResponse tokenResponse = tokenUtil.createToken(authenticatedMember, authenticatedMember.getRegistrationStatus().equals(RegistrationStatus.COMPLETED));
        //5. refresh token 저장
        tokenUtil.storeRefreshToken(authenticatedMember.getSocialId(), tokenResponse);

        return SignInResponse.from(tokenResponse, authenticatedMember.getRegistrationStatus());
    }

    private Member getUserDataFromPlatform(String accessToken, String providerInfo) {
        if (providerInfo.contains("kakao")) return kakaoSignInUserCase.getUserData(accessToken);
        //====바뀐 부분======//
				else if (providerInfo.contains("google") return googleSignInUserCase.getUserData(accessToken);
				else return appleSignInUserCase.getUserData(accessToken);
    }
}
```

이와 같은 구조는 기능을 확장하기 위해서는 수정이 필요한 구조입니다. 이는 객체지향 설계 5원칙인 SOLID 중 **개방-폐쇄 원칙(OCP: Open-Closed Principle)** 의 위배로, 이를 전략 패턴을 사용하여 개발-폐쇄 원칙을 따르는 구조로 개선해봅시다.

## 전략 패턴 적용

1. Startegy (인터페이스)

```java
public interface SignInProvider {
    Member getUserData(String accessToken);
}
```

2. **ConcreteStrategy (구현체)**

```java
@Service("kakao")
public class KakaoSignInUserCase implements LoginProvider {
    //... implementation
}

@Service("apple")
public class AppleSignInUserCase implements LoginProvider {
    //... implementation
}
```

3. Context

```java
@Service
@Transactional
@RequiredArgsConstructor
public class SignInUserCase {

    private final MemberAuthUserCase internalAuthService;
    private final TokenUtil tokenUtil;
    private final Map<String, SignInProvider> signInProviders;

    public SignInResponse signIn(String token, String providerInfo) {
        //1. 사용자 정보 가져오기
        Member member = getUserDataFromPlatform(token, providerInfo);
        //2. 로그인 및 회원가입
        Member authenticatedMember = internalAuthService.auth(member, providerInfo);
        //3. security 처리
        AuthenticationUtil.makeAuthentication(authenticatedMember);
        //4. token 만들기
        TokenInfoResponse tokenResponse = tokenUtil.createToken(authenticatedMember, authenticatedMember.getRegistrationStatus().equals(RegistrationStatus.COMPLETED));
        //5. refresh token 저장
        tokenUtil.storeRefreshToken(authenticatedMember.getSocialId(), tokenResponse);

        return SignInResponse.from(tokenResponse, authenticatedMember.getRegistrationStatus());
    }

	 // Context 부분 //
    private Member getUserDataFromPlatform(String accessToken, String providerInfo) {
        SignInProvider signInProvider = signInProviders.get(providerInfo);
        if (signInProvider == null) {
            throw new IllegalArgumentException("Unknown provider: " + providerInfo);
        }
        return signInProvider.getUserData(accessToken);
    }
}
```
