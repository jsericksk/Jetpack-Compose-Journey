# Disposable Effect

Para side effects que precisam ser limpos após uma mudança nos valores das keys ou se o Composable sair da composição, use **DisposableEffect**. Ele é um pouco semelhante ao **LaunchedEffect**, que aceita um número variável de keys e reinicia o efeito caso o valor de uma das keys mude, porém, possui uma cláusula **onDispose** final obrigatória que pode ser usada para fazer a limpeza, liberar recursos, remover eventos de listeners etc. 

Veja um exemplo simples retirado da própria documentação oficial, que pode ser usado para enviar eventos analíticos com base em eventos do [Lifecycle](https://developer.android.com/topic/libraries/architecture/lifecycle) usando um **LifecycleObserver**:

```kotlin
@Composable
private fun LifeCycleObserver(
    lifecycleOwner: LifecycleOwner = LocalLifecycleOwner.current,
    onStart: () -> Unit,
    onStop: () -> Unit,
) {
    // Atualiza com segurança os lambdas atuais quando um novo é fornecido
    val currentOnStart by rememberUpdatedState(onStart)
    val currentOnStop by rememberUpdatedState(onStop)

    // Se `lifecycleOwner` mudar, descarta e reinicia o efeito
    DisposableEffect(lifecycleOwner) {
        val observer = LifecycleEventObserver { _, event ->
            if (event == Lifecycle.Event.ON_START) {
                currentOnStart()
            } else if (event == Lifecycle.Event.ON_STOP) {
                currentOnStop()
            }
        }

        // Adiciona o observer ao ciclo de vida
        lifecycleOwner.lifecycle.addObserver(observer)

        // Quando o efeito deixar a composição, remove o observer
        onDispose {
            lifecycleOwner.lifecycle.removeObserver(observer)
        }
    }
}
```

## :link: Conteúdos auxiliares:
- [Side-effects in Compose (documentação)](https://developer.android.com/jetpack/compose/side-effects)
- [How to Use DisposableEffect in Jetpack Compose (artigo)](https://developermemos.com/posts/disposableeffect-jetpack-compose)