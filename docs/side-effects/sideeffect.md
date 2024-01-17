# SideEffect

Para compartilhar o estado do Compose com objetos não gerenciados pelo Compose, use o **SideEffect**. Usar SideEffect garante que o efeito seja executado após cada recomposição bem-sucedida. Diferente do **LaunchedEffect** e **DisposableEffect**, **SideEffect** não possui a função de chaves (keys).

Vamos ver um pequeno exemplo também retirado da documentação:

```kotlin
@Composable
fun rememberFirebaseAnalytics(user: User): FirebaseAnalytics {
    val analytics: FirebaseAnalytics = remember {
        FirebaseAnalytics()
    }

    // Em cada composição bem-sucedida, atualiza o FirebaseAnalytics com
    // o userType do usuário atual, garantindo que eventos de análise futuros
    // tenham esses metadados anexados
    SideEffect {
        analytics.setUserProperty("userType", user.userType)
    }
    return analytics
}
```

## :link: Conteúdos auxiliares:
- [Side-effects in Compose (documentação)](https://developer.android.com/jetpack/compose/side-effects)