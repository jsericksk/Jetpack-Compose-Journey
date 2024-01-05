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
}
```

![Text](img-01.png)

## TextField

Aqui você começará a ver coisas interessantes que se repetirão por todo o ecossistema do Compose. Vamos falar sobre algumas delas antes de ver o código do TextField:

- Como se trata de um campo de texto editável, o **TextField** usará o que já falamos no começo sobre **estados** (o texto) e **eventos** (quando o usuário digitar algo). No exemplo do código, temos uma linha importante: ```var text by remember { mutableStateOf("") }```. Basicamente essa variável será lida como um estado que, ao ser modificada, acionará a **recomposição**. Ou seja, o texto do TextField mudará. Veremos explicações melhores sobre isso na seção sobre **estados**.
- TextField tem diversos parâmetros que **recebem como argumentos funções Composable**. Por exemplo, **label**. Ao invés de passarmos apenas uma String ou algo do tipo, colocamos o nosso próprio componente **Text()** ou o que quisermos de Composable. Como a grande maioria dos parâmetros é opcional, eles também já vêm com configurações padrão.
- Ele usa o padrão de **state hoisting (elevação de estado)**. Recebe o valor com **value** e, quando esse valor muda, aciona **onValueChange** com o novo valor. Veremos mais sobre isso na seção sobre esse tema.


