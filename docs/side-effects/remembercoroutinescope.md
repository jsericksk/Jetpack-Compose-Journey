
# rememberCoroutineScope

rememberCoroutineScope é uma função Composable que retorna um CoroutineScope vinculada ao ponto da composição onde é chamada. O escopo será cancelado quando a chamada sair da composição.

Diferente da **LaunchedEffect** que só pode ser usada dentro de uma outra função Composable por ser uma função Composable, **rememberCoroutineScope** é útil para iniciar uma coroutine fora de uma Composable, mas com escopo para que seja cancelada automaticamente ao sair da composição.

Ele é bem comum de ser utilizado, tanto que já o utilizamos uma vez quando aprendemos sobre o componente **Navigation Drawer**. Um outro exemplo que podemos ver sua utilização é na implementação de uma [Pager](https://developer.android.com/jetpack/compose/layouts/pager). No código abaixo, não podemos chamar ```pagerState.animateScrollToPage(5)``` no onClick do **Button()**, pois **animateScrollToPage()** é uma suspend function. Para isso podemos recorrer ao **rememberCoroutineScope**.

```kotlin
@OptIn(ExperimentalFoundationApi::class)
@Composable
private fun MyHorizontalPager() {
    val pagerState = rememberPagerState(pageCount = { 10 })
    Column {
        HorizontalPager(state = pagerState) { page ->
            Text(
                text = "Página atual: $page",
                modifier = Modifier
                    .fillMaxWidth()
                    .height(100.dp)
            )
        }

        val coroutineScope = rememberCoroutineScope()
        Button(
            onClick = {
                // animateScrollToPage() é uma suspend function
                coroutineScope.launch {
                    pagerState.animateScrollToPage(5)
                }
            },
            modifier = Modifier.align(Alignment.CenterHorizontally)
        ) {
            Text(text = "Pular para a página 5")
        }
    }
}
```