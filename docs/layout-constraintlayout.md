# ConstraintLayout

ConstraintLayout tem um comportamento similar ao que vemos no ConstraintLayout do XML. Ele permite posicionar Composables em relação a outros Composables. Uma das principais diferenças entre ele e os demais layouts citados anteriormente é que para utilizá-lo você precisa importar sua dependência no **build.gradle (app)**:

```gradle
implementation("androidx.constraintlayout:constraintlayout-compose:$version")
```

## Quando usar

De acordo com a documentação oficial, considere usar ConstraintLayout nos seguintes cenários:

- Para evitar o aninhamento de vários **Columns** e **Rows** para posicionar elementos na tela para melhorar a legibilidade do código.
- Para posicionar Composables em relação a outros Composables ou para posicionar Composables com base em **guidelines**, **barriers** ou **chains**.

É importante notar que no sistema de Views, **ConstraintLayout era a maneira recomendada de criar layouts grandes e complexos**, já que uma hierarquia de Views plana era melhor para o desempenho do que Views aninhadas, como vários **LinearLayouts**. No entanto, **isso não é uma preocupação no Compose, que é capaz de lidar com eficiência com hierarquias profundas de layout** e você não precisa se preocupar com o aninhamento de **Columns**, **Rows** e **Box** em questão de desempenho.

## Utilização

ConstraintLayout no Compose funciona da seguinte forma:

- Crie referências para cada Composable usando ```createRefs()``` ou ```createRefFor()```;
- As restrições são fornecidas usando o ```Modifier.constrainAs()``` no Composable desejado, que toma a referência como parâmetro e permite especificar suas restrições no lambda: ```Modifier.constrainAs() { // restrições }```;
- As restrições são especificadas usando ```linkTo()``` ou outros métodos úteis;
- ```parent``` é uma referência existente que pode ser usada para especificar restrições para o próprio ```ConstraintLayout```.

Vamos reutilizar um pouco do que já fizemos nos layouts anteriores e ver isso na prática.

#### Usando como uma Column

O código abaixo terá o mesmo resultado de uma **Column**, como fizemos no exemplo que vimos na seção de Column:

```kotlin
@Composable
fun Profile() {
    ConstraintLayout {
        // Referências
        val (profileImage, profileName) = createRefs()

        Image(
            painter = painterResource(id = R.drawable.baseline_person_24),
            contentDescription = "Profile image",
            modifier = Modifier
                .size(50.dp)
                // Atribui a referência "profileImage" a esse Composable Image()
                .constrainAs(profileImage) {
                    // Especificamos as constraints, onde "parent" é o próprio ConstraintLayout
                    top.linkTo(parent.top)
                    start.linkTo(parent.start)
                }
        )

        Text(
            text = "John",
            modifier = Modifier
                .constrainAs(profileName) {
                    top.linkTo(profileImage.bottom)
                }
        )
    }
}
```

![ConstraintLayout como Column](img-01.png)

#### Usando como uma Row

Se alterarmos a constraint do **Text()** para ```start.linkTo(profileImage.end)``` no código acima, teremos o mesmo resultado de uma Row:

```kotlin
@Composable
fun Profile() {
    ConstraintLayout {
        ...
        Text(
            text = "John",
            modifier = Modifier
                .constrainAs(profileName) {
                    start.linkTo(profileImage.end)
                }
        )
    }
}
```

![ConstraintLayout como Row](img-02.png)

#### Usando como uma Box

Também podemos fazer com o **ConstraintLayout** o mesmo layout que fizemos com **Box** anteriormente. Isso acontece porque ConstraintLayout também não coloca os elementos em uma direção específica, mas uns sobre os outros se não adicionarmos **constraints** que mudem isso. Veja o exemplo:

```kotlin
@Composable
fun VideoItem() {
    ConstraintLayout(
        modifier = Modifier
            .width(200.dp)
            .height(100.dp)
    ) {
        val (videoImage, playIcon, duration) = createRefs()
        Image(
            painter = painterResource(id = R.drawable.img_sample),
            contentDescription = "Video image",
            contentScale = ContentScale.Crop,
            modifier = Modifier
                .fillMaxSize()
                .constrainAs(videoImage) {
                    start.linkTo(parent.start)
                }
        )
        Icon(
            painter = painterResource(id = R.drawable.baseline_play_circle_outline_24),
            contentDescription = "Play icon",
            tint = Color.White,
            modifier = Modifier
                .size(50.dp)
                .constrainAs(playIcon) {
                    start.linkTo(videoImage.start)
                    end.linkTo(videoImage.end)
                    top.linkTo(videoImage.top)
                    bottom.linkTo(videoImage.bottom)
                }
        )
        Text(
            text = "15:23",
            color = Color.White,
            fontSize = 12.sp,
            modifier = Modifier
                .background(Color.Black)
                .padding(2.dp)
                .constrainAs(duration) {
                    bottom.linkTo(videoImage.bottom)
                    end.linkTo(videoImage.end)
                }
        )
    }
}
```

![ConstraintLayout como Box](img-03.png)

## Considerações finais

Nos exemplos, vimos que em grande parte podemos fazer com **ConstraintLayout** o mesmo que fazemos com outros layouts. Isso não significa que esse seja o objetivo dele e você deva sempre utilizar **ConstraintLayout** para tudo. Sempre considere se é necessário utilizá-lo e se ele atende suas necessidades. Normalmente, na maioria das vezes, você irá utilizar combinações de **Column**, **Row** e **Box**.

## :link: Referências:
- [ConstraintLayout in Compose (documentação)](https://developer.android.com/jetpack/compose/layouts/constraintlayout)