# Modifiers

Já vimos em seções anteriores vários usos do Modifier. Essa seção é dedicada em falar um pouco sobre ele e algumas outras funções que ele fornece.

Os modificadores são praticamente onipresentes e permitem decorar as funções Composables de várias formas. Como já vimos antes, além de várias outras opções, com ele é possível fazer coisas como:

- Adicionar uma cor de fundo com ```Modifier.background()```;
- Alterar o tamanho com ```Modifier.size()```;
- Adicionar um padding com ```Modifier.padding()```.

## Um parâmetro Modifier opcional

Você já deve ter notado que praticamente todo componente no Compose possui um parâmetro **Modifier** opcional, certo? Isso acontece porque **é uma boa prática sempre adicionar um parâmetro Modifier opcional aos Composables**, pois como já foi discutido antes, com ele você pode alterar alguns comportamentos do Composable sem muito esforço.

Portanto, quando estiver criando seu componente, é recomendável sempre colocar um parâmetro Modifier opcional. É importante ser opcional porque o chamador nem sempre irá usar o Modifier. **Ele deve ser o primeiro parâmetro opcional**. Veja o exemplo abaixo, os parâmetros **text** e **onClick** não são opcionais, portanto, ele fica logo abaixo deles:

```kotlin
@Composable
fun MyCustomButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    colors: MyButtonColors = MyButtonColors.colors()
)
```

**EVITE**:

```kotlin
@Composable
fun MyCustomButton(
    text: String,
    onClick: () -> Unit
)
```

**EVITE**:

```kotlin
@Composable
fun IconButton(
    buttonBitmap: ImageBitmap,
    modifier: Modifier = Modifier,
    tint: Color = Color.Black
) {
    Box(Modifier.padding(16.dp)) {
        Icon(
            buttonBitmap,
            tint = tint,
            // Modifier deve ser colocado no layout mais externo
            // e deve ser o primeiro na cadeia. Aqui ele deveria 
            // estar no Box
            modifier = Modifier.aspectRatio(1f).then(modifier),
        )
    }
}
```

**EVITE**:

```kotlin
@Composable
fun Icon(
    bitmap: ImageBitmap,
    // padding(8.dp) será perdido assim que o chamador definir seu próprio Modifier
    modifier: Modifier = Modifier.padding(8.dp)
    tint: Color = Color.Black
) {
    Box(modifier) {
        ...
    }
}
```

**FAÇA**:

```kotlin
@Composable
fun IconButton(
    buttonBitmap: ImageBitmap,
    modifier: Modifier = Modifier,
    tint: Color = Color.Black
) {
    Box(modifier.padding(16.dp)) {
        ...
    }
}
```

**EVITE**:

```kotlin
@Composable
fun CheckboxRow(
    checked: Boolean,
    onCheckedChange: (Boolean) -> Unit,
    // Modifier destina-se a especificar o comportamento
    // externo do CheckboxRow nesse caso, não suas subpartes
    rowModifier: Modifier = Modifier,
    checkboxModifier: Modifier = Modifier
)
```

#### Modifier como último argumento

Não é uma regra específica, mas como o Modifier costuma ter vários modificadores, é interessante sempre colocá-lo como último argumento. Ao invés disso:

```kotlin
@Composable
private fun Item() {
    Text(
        text = "Jetpack Compose",
        color = Color.White,
        modifier = Modifier
            .background(Color.Black)
            .padding(4.dp)
            .clickable {},
        fontSize = 18.sp,
        fontFamily = FontFamily.Cursive
    )
}
```

Faça isso:

```kotlin
@Composable
private fun Item() {
    Text(
        text = "Jetpack Compose",
        color = Color.White,
        fontSize = 18.sp,
        fontFamily = FontFamily.Cursive,
        modifier = Modifier
            .background(Color.Black)
            .padding(4.dp)
            .clickable {}
    )
}
```

## A ordem dos modificadores importa

A ordem dos Modifiers é **significativa**. Como cada função faz alterações no Modifier anterior, a sequência afeta o resultado final. Vejamos um exemplo disso:

```kotlin
@Composable
private fun Item() {
    Box(
        modifier = Modifier
            .size(150.dp)
            .background(Color.Blue)
            .padding(24.dp)
    ) {
        Text(
            text = "Ordem importa!",
            color = Color.White
        )
    }
}
```

![Modifiers](modifiers/img-01.png)

Agora vamos mudar a ordem do ***background*** e do ***padding***:

```kotlin
@Composable
private fun Item() {
    Box(
        modifier = Modifier
            .size(150.dp)
            .padding(24.dp)
            .background(Color.Blue)
    ) {
        Text(
            text = "Ordem importa!",
            color = Color.White
        )
    }
}
```

![Modifiers](modifiers/img-02.png)

Isso aconteceu porque no primeiro exemplo definimos a cor de fundo antes de definirmos um espaçamento, o que fez com que a cor preenchesse todo o Box. Já no segundo exemplo, definimos um padding antes e, por consequência, a cor de fundo só preencheu o espaço já modificado com padding.

## Margin no Compose

![Margin e Padding](modifiers/img-03.png)

Diferente do sistema de Views, o Compose não possui uma opção para **margin** como no XML. Essa funcionalidade pode ser obtida com ```Modifier.padding()``` utilizando o mesmo princípio da ordem de modificadores que vimos acima.

Vamos ver um pequeno exemplo. Veja o código abaixo:

```kotlin
@Composable
private fun MarginAndPadding() {
    Column(
        horizontalAlignment = Alignment.CenterHorizontally,
        modifier = Modifier
            .background(Color.Black)
            .width(150.dp)
    ) {
        Text(
            text = "Texto 1",
            color = Color.White,
            modifier = Modifier
                .background(Color(0xFF004D40))
        )
        Text(
            text = "Texto 2",
            color = Color.White,
            modifier = Modifier
                .background(Color(0xFF004D40))
        )
    }
}
```

![Modifier](modifiers/img-04.png)

Agora vamos aplicar uma **margin** em ambos os textos utilizando ```Modifier.padding()```:

```kotlin
@Composable
private fun MarginAndPadding() {
    Column(
       ...
    ) {
        Text(
            ...
            modifier = Modifier
                .padding(6.dp) // Margin
                .background(Color(0xFF004D40))
        )
        Text(
            ...
            modifier = Modifier
                .padding(6.dp) // Margin
                .background(Color(0xFF004D40))
        )
    }
}
```

![Modifier](modifiers/img-05.png)

Agora vamos adicionar um **padding**:

```kotlin
@Composable
private fun MarginAndPadding() {
    Column(
       ...
    ) {
        Text(
            ...
            modifier = Modifier
                .padding(6.dp) // Margin
                .background(Color(0xFF004D40))
                .padding(6.dp) // Padding
        )
        Text(
            ...
            modifier = Modifier
                .padding(6.dp) // Margin
                .background(Color(0xFF004D40))
                .padding(6.dp) // Padding
        )
    }
}
```

![Modifier](modifiers/img-06.png)

Como você pode ver, ambas as funcionalidades de **margin** e **padding** podem ser atingidas com ```Modifier.padding()```, apenas tendo em mente a ordem dos modificadores. Note que da forma que estamos usando, o **padding** será aplicado em todas as direções (left, right, top e bottom), pois o argumento padrão é **all**. Existem outras funções ```Modifier.padding()``` que aceitam argumentos diferentes, como direções (start, end, top e bottom) ou orientações (vertical e horizontal). Por exemplo:

```kotlin
@Composable
private fun MarginAndPadding() {
    Column(
       ...
    ) {
        Text(
            ...
             modifier = Modifier
                .padding(bottom = 6.dp) // Margin
                .background(Color(0xFF004D40))
                .padding(
                    start = 4.dp,
                    end = 4.dp,
                    top = 8.dp,
                    bottom = 8.dp
                ) // Padding
        )
        Text(
            ...
             modifier = Modifier
                .padding(top = 6.dp) // Margin
                .background(Color(0xFF004D40))
                .padding(
                    start = 4.dp,
                    end = 4.dp,
                    top = 8.dp,
                    bottom = 8.dp
                ) // Padding
        )
    }
}
```

![Modifier](modifiers/img-07.png)

Como usamos os mesmos valores para as direções com a mesma orientação, podemos apenas utilizar **horizontal** e **vertical** para atingir o mesmo resultado:

```kotlin
@Composable
private fun MarginAndPadding() {
    Column(
       ...
    ) {
        Text(
            ...
            modifier = Modifier
                .padding(bottom = 6.dp) // Margin
                .background(Color(0xFF004D40))
                .padding(horizontal = 4.dp, vertical = 8.dp) // Padding
        )
        Text(
            ...
            modifier = Modifier
                .padding(top = 6.dp) // Margin
                .background(Color(0xFF004D40))
                .padding(horizontal = 4.dp, vertical = 8.dp) // Padding
        )
    }
}
```

![Modifier](modifiers/img-07.png)

Se tudo que você precisa é de uma **margin** em um componente para criar um espaço em relação ao outro, como um **marginTop** do XML, você pode utilizar o componente **Spacer()** com ```Modifier.height()``` ou ```Modifier.width()```, que pode ser um pouco mais intuitivo. Por exemplo, ao invés disso:

```kotlin
@Composable
private fun MarginAndPadding() {
    Column(
       ...
    ) {
        Text(
            ...
            modifier = Modifier
                .background(Color(0xFF004D40))
                .padding(horizontal = 4.dp, vertical = 8.dp) // Padding
        )
        Text(
            ...
            modifier = Modifier
                .padding(top = 8.dp) // Equivalente ao marginTop do XML
                .background(Color(0xFF004D40))
                .padding(horizontal = 4.dp, vertical = 8.dp) // Padding
        )
    }
}
```

Você pode fazer isso:

```kotlin
@Composable
private fun MarginAndPadding() {
    Column(
       ...
    ) {
        Text(
            ...
            modifier = Modifier
                .background(Color(0xFF004D40))
                .padding(horizontal = 4.dp, vertical = 8.dp) // Padding
        )
        Spacer(Modifier.height(8.dp))
        Text(
            ...
            modifier = Modifier
                .background(Color(0xFF004D40))
                .padding(horizontal = 4.dp, vertical = 8.dp) // Padding
        )
    }
}
```

## Segurança de escopo no Compose

No Compose, há modificadores que só podem ser usados ​​quando aplicados a filhos de determinados Composables. O Compose impõe isso por meio de escopos personalizados.

Por exemplo, se você quiser deixar um filho tão grande quanto o pai Box sem afetar o tamanho do Box, use o ```Modificador.matchParentSize()```. Ele só está disponível em ```BoxScope```. Portanto, ele só pode ser usado em um filho dentro de um dos Box pais.

A segurança do escopo evita que você adicione modificadores que não funcionariam em outros Composables e escopos, além de economizar tempo de tentativa e erro.

```kotlin
@Composable
private fun Item() {
    Box(
        modifier = Modifier
            .size(100.dp)
            .background(Color.Blue)
    ) {
        Box(
            modifier = Modifier
                .background(Color.Red)
                // Funciona, pois está em um BoxScope
                .matchParentSize()
        )
    }
}
```

```kotlin
@Composable
private fun Item() {
    Column(
        modifier = Modifier
            .size(100.dp)
            .background(Color.Blue)
    ) {
        Box(
            modifier = Modifier
                .background(Color.Red)
                // Não funciona, pois está em um ColumnScope
                .matchParentSize()
        )
    }
}
```

## Conclusão

Você pode ler mais sobre **Modifiers** na documentação e nos artigos auxiliares. Como pôde ver, eles são bem importantes e sempre utilizados no Compose. Há uma vasta lista de Modifiers, essa seção focou mais na teoria do que prática sobre Modifiers específicos. Ao longo do tempo, você aprenderá e usará vários deles.

## :link: Conteúdos auxiliares:
- [Modifiers (documentação)](https://developer.android.com/jetpack/compose/modifiers)
- [Modifier parameter (guidelines)](https://github.com/androidx/androidx/blob/androidx-main/compose/docs/compose-component-api-guidelines.md#modifier-parameter)
- [What can Advanced / Lesser Known Modifiers do for your UI? — A Comprehensive Exploration in Jetpack Compose (artigo)](https://proandroiddev.com/what-can-advanced-lesser-known-modifiers-do-for-your-ui-9c76855bced6)