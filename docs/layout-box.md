# Box

Diferente de **Column** e **Row**, Box não alinha os elementos em uma direção vertical ou horizontal. Cada elemento será colocado um sobre o outro, semelhante ao que acontece quando não especificamos um layout. Algo também semelhante ao que temos no **FramewLayout**, com cada componente sobrepondo o outro em uma espécie de camadas.

A diferença principal é que você pode aplicar o alinhamento de cada componente manualmente, através do ```Modifier.align()```. Veja o exemplo:

```kotlin
@Composable
fun BoxExample() {
    Box(
        modifier = Modifier.size(200.dp)
    ) {
        Text(
            text = "Top start",
            modifier = Modifier.align(Alignment.TopStart)
        )
        Text(
            text = "Top end",
            modifier = Modifier.align(Alignment.TopEnd)
        )
        Text(
            text = "Center",
            modifier = Modifier.align(Alignment.Center)
        )
        Text(
            text = "Bottom start",
            modifier = Modifier.align(Alignment.BottomStart)
        )
        Text(
            text = "Bottom end",
            modifier = Modifier.align(Alignment.BottomEnd)
        )
    }
}
```

![Box](img-01.png)

Veja um exemplo de layout básico que pode ser criado utilizando Box e como ele pode ser útil:

```kotlin
@Composable
fun VideoItem() {
    Box(
        modifier = Modifier
            .width(200.dp)
            .height(100.dp)
    ) {
        Image(
            painter = painterResource(id = R.drawable.img_sample),
            contentDescription = "Video image",
            contentScale = ContentScale.Crop,
            modifier = Modifier
                .fillMaxSize()
        )
        Icon(
            painter = painterResource(id = R.drawable.baseline_play_circle_outline_24),
            contentDescription = "Play icon",
            tint = Color.White,
            modifier = Modifier
                .size(50.dp)
                .align(Alignment.Center),
        )
        Text(
            text = "15:23",
            color = Color.White,
            fontSize = 12.sp,
            modifier = Modifier
                .background(Color.Black)
                .padding(2.dp)
                .align(Alignment.BottomEnd)
        )
    }
}
```

![Box](img-02.png)

Lembre-se que a ordem dos componentes importa, pois cada componente será colocado um sobre o outro. Portanto, se trocarmos a posição do Icon() e Image() do exemplo acima, deixando Icon() em primeiro e Image() em segundo, o ícone não será exibido na tela. Isso acontece porque a imagem sobrepões o ícone, já que ela está ocupando todo o espaço com ```Modifier.fillMaxSize()```, enquanto o ícone está usando um tamanho fixo de 50.dp e em uma camada inferior a da imagem.

- Box também tem uma opção de alinhamento: **contentAlignment**. O padrão é ```Alignment.TopStart```. Ao definir contentAlignment como ```Alignment.Center```, por exemplo, os componentes ainda serão colocados uns sobre os outros, mas iniciando no centro.

## :link: Referências:
- [Compose layout basics (documentação)](https://developer.android.com/jetpack/compose/layouts/basics)