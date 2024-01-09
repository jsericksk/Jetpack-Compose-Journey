# Componentes 01: Text, TextField, Button, Image...

Componentes obviamente são uma parte fundamental de qualquer app e nessa seção você verá alguns dos mais importantes que podem ser usados no Compose. Já vimos os componentes **Text()** e **Image()** em algumas seções anteriores, mas nessa veremos outros de uma maneira um pouco mais ampla.

Todos os componentes possuem diversos parâmetros, então obviamente não abordaremos todos eles. Você pode navegar no componente específico ou colocar seu mouse sobre o componente para ver os parâmetros.

## Recursos

Antes de falarmos sobre o **Text()** e outros componentes, é importante sabermos primeiro que também podemos usar recursos criados no projeto. Recursos são arquivos adicionais e conteúdo estático que seu código usa, como bitmaps, strings, instruções de animação e muito mais. Dois recursos bastante utilizados são recursos de strings e de cores. Compose oferece algumas funções Composable simples para isso, veja nos exemplos abaixo:

- ```Text(text = stringResource(R.string.compose))```
- ```Text(text = stringResource(R.string.congratulate, "New Year", 2024))```
- ```Divider(color = colorResource(R.color.purple_200))```

Veja a [documentação](https://developer.android.com/jetpack/compose/resources) para saber um pouco mais.

## Text

O componente Text não tem muitos segredos, então vamos ver apenas um exemplo com várias propriedades.

```kotlin
@Composable
private fun Text() {
    Text(
        text = "Jetpack Compose",
        color = Color.Blue,
        fontSize = 16.sp,
        fontWeight = FontWeight.Bold,
        fontFamily = FontFamily.SansSerif,
        letterSpacing = 4.sp
    )
}
```

![Text](text/img-01.png)

## :link: Conteúdos auxiliares:
- [Style text (documentação)](https://developer.android.com/jetpack/compose/text/style-text)