---
name: spring-security-oauth2
description: OAuth2/OpenID Connect authentication with Google and Apple Sign-In for Spring Boot applications. Handles login, user registration from OAuth, and route protection. Use when this capability is needed.
metadata:
  author: gazolla
---

# Spring Security OAuth2 (Google + Apple)

Social login with Google and Apple using OAuth2/OpenID Connect.

## Use this skill when

- Implementing "Login with Google" or "Sign in with Apple"
- Need OAuth2/OIDC authentication
- Protecting routes based on authentication
- User mentions "login", "authentication", "Google sign-in", or "Apple login"

## Dependencies

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-client</artifactId>
</dependency>
```

## Configuration (application.yml)

```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          google:
            client-id: ${GOOGLE_CLIENT_ID}
            client-secret: ${GOOGLE_CLIENT_SECRET}
            scope: [email, profile]
          
          apple:
            client-id: ${APPLE_CLIENT_ID}
            client-secret: ${APPLE_CLIENT_SECRET}
            authorization-grant-type: authorization_code
            scope: [email, name]
            client-authentication-method: client_secret_post
        
        provider:
          apple:
            authorization-uri: https://appleid.apple.com/auth/authorize
            token-uri: https://appleid.apple.com/auth/token
            jwk-set-uri: https://appleid.apple.com/auth/keys
```

## Security Configuration

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
@RequiredArgsConstructor
public class SecurityConfig {

    private final CustomOAuth2UserService customOAuth2UserService;

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/", "/login", "/error", "/css/**", "/js/**").permitAll()
                .requestMatchers("/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .oauth2Login(oauth2 -> oauth2
                .loginPage("/login")
                .userInfoEndpoint(u -> u.userService(customOAuth2UserService))
                .defaultSuccessUrl("/dashboard", true)
            )
            .logout(logout -> logout
                .logoutSuccessUrl("/")
                .invalidateHttpSession(true)
            );
        
        return http.build();
    }
}
```

## CustomOAuth2UserService

```java
@Service
@RequiredArgsConstructor
public class CustomOAuth2UserService extends DefaultOAuth2UserService {

    private final UserRepository userRepository;

    @Override
    @Transactional
    public OAuth2User loadUser(OAuth2UserRequest request) {
        OAuth2User oauth2User = super.loadUser(request);
        String provider = request.getClientRegistration().getRegistrationId();
        
        String email = oauth2User.getAttribute("email");
        String name = oauth2User.getAttribute("name");
        
        User user = userRepository.findByEmail(email)
            .orElseGet(() -> userRepository.save(User.builder()
                .email(email)
                .name(name)
                .provider(AuthProvider.valueOf(provider.toUpperCase()))
                .build()));
        
        return new CustomUserPrincipal(user, oauth2User.getAttributes());
    }
}
```

## Login Page

```html
<div class="d-grid gap-3">
    <a href="/oauth2/authorization/google" class="btn btn-primary btn-lg">
        Continue with Google
    </a>
    <a href="/oauth2/authorization/apple" class="btn btn-dark btn-lg">
        Continue with Apple
    </a>
</div>
```

## Accessing Current User

```java
// In Controller
@GetMapping("/profile")
public String profile(@AuthenticationPrincipal CustomUserPrincipal principal) {
    User user = principal.getUser();
    // ...
}

// In Thymeleaf
<span th:text="${#authentication.principal.user.name}">User</span>
```

See `references/` for complete setup guides.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gazolla) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
