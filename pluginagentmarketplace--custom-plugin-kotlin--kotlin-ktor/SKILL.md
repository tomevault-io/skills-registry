---
name: kotlin-ktor
description: Ktor framework - routing, authentication, WebSockets Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Kotlin Ktor Skill

Build production-ready backends with Ktor.

## Topics Covered

### Routing
```kotlin
fun Application.module() {
    install(ContentNegotiation) { json() }
    routing {
        route("/api/v1") {
            get("/users") { call.respond(userService.findAll()) }
            get("/users/{id}") {
                val id = call.parameters["id"]?.toLongOrNull()
                    ?: throw BadRequestException("Invalid ID")
                call.respond(userService.findById(id) ?: throw NotFoundException())
            }
        }
    }
}
```

### JWT Authentication
```kotlin
install(Authentication) {
    jwt("auth") {
        verifier(JWT.require(Algorithm.HMAC256(secret)).build())
        validate { credential ->
            if (credential.payload.getClaim("userId").asString().isNotEmpty())
                UserPrincipal(credential.payload)
            else null
        }
    }
}

authenticate("auth") { userRoutes() }
```

### Testing
```kotlin
@Test
fun `GET users returns list`() = testApplication {
    application { module() }
    client.get("/api/v1/users").apply {
        assertThat(status).isEqualTo(HttpStatusCode.OK)
    }
}
```

## Troubleshooting

| Issue | Resolution |
|-------|------------|
| 404 for valid route | Order specific routes before wildcards |
| JSON not parsed | Install ContentNegotiation plugin |

## Usage
```
Skill("kotlin-ktor")
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
