# Componentes 01: Text, TextField, Button, Image...

Componentes obviamente são uma parte fundamental de qualquer app e nessa seção você verá alguns dos mais importantes que podem ser usados no Compose. Já vimos os componentes **Text()** e **Image()** em algumas seções anteriores, mas nessa veremos outros de uma maneira um pouco mais ampla.

Todos os componentes possuem diversos parâmetros, então obviamente não abordaremos todos eles. Você pode navegar no componente específico ou colocar seu mouse sobre o componente para ver os parâmetros.

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
}g
```

![Text](text/img-01.png)

## :link: Conteúdos auxiliares:
- [Style text (documentação)](https://developer.android.com/jetpack/compose/text/style-text)