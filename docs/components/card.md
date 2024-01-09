# Card

Cards também são bastante importantes e nessa seção vamos ver um pouco sobre eles. Assim como outros components que já vimos, Card tem diversas variações. Veja uma implementação básica:

```kotlin
@Composable
private fun Card() {
    Card(
        shape = RoundedCornerShape(16.dp)
    ) {
        Image(
            painter = painterResource(id = R.drawable.img_nature),
            contentDescription = "Image",
            contentScale = ContentScale.Crop,
            modifier = Modifier
                .size(200.dp)
        )
        Column(modifier = Modifier.padding(16.dp)) {
            Text(
                text = "Nature image",
                color = Color.Black,
                fontSize = 16.sp
            )
            Text(
                text = "1.2 MB",
                fontSize = 12.sp,
            )
        }
    }
}
```

![Card](card/img-01.png)

Um ***ElevatedCard***, que como o nome diz, possui uma elevação e shadow de acordo com a elevação.

```kotlin
@Composable
private fun Card() {
    ElevatedCard(
        shape = RoundedCornerShape(16.dp),
        elevation = CardDefaults.cardElevation(
            defaultElevation = 12.dp
        ),
        colors = CardDefaults.cardColors(
            containerColor = Color.White
        )
    ) {
        ...
    }
}
```

![ElevatedCard](card/img-02.png)

E também temos o ***OutlinedCard***:

```kotlin
@Composable
private fun Card() {
    OutlinedCard(
        shape = RoundedCornerShape(16.dp),
        border = BorderStroke(1.dp, Color.Black)
    ) {
        ...
    }
}
```

![OutlinedCard](card/img-03.png)

## :link: Conteúdos auxiliares:
- [Card (documentação)](https://developer.android.com/jetpack/compose/components/card)
- [Cards (Material Design 3)](https://m3.material.io/components/cards/overview)