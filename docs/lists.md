# Listas

Exibir listas é uma função bastante comum em qualquer app e nessa seção vamos aprender um pouco sobre como listas funcionam e podem ser implementadas no Compose.

## Scroll com Modifier

Às vezes não precisamos de uma lista em si, mas queremos apenas um **conteúdo rolável**, como o **ScrollView** do XML. Existem 2 modificadores úteis que podem ser usados com layouts como **Column** para obter esse comportamento: ```Modifier.verticalScroll()``` e ```Modifier.horizontalScroll()```. Veja um exemplo abaixo:

```kotlin
@Composable
fun MyScreen() {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .verticalScroll(rememberScrollState())
    ) {
       // Conteúdo que pode ultrapassar a resolução da tela verticalmente
    }
}
```

Isso é útil para layouts ou listas pequenas que ultrapassam a tela, porém, pode causar problemas de desempenho com listas grandes ou com tamanho desconhecido, pois todos os itens serão compostos e processados, estejam ou não visíveis na tela.

## LazyColumn e LazyRow

Compose fornece um conjunto de componentes que apenas compõem e processa os itens atualmente visíveis. Um comportamento bem similar ao **RecyclerView** do XML. Alguns desses componentes são o ***LazyColumn*** e ***LazyRow***, que como os nomes sugerem, fornecem uma lista de rolagem vertical e horizontal, respectivamente.

Vamos criar uma lista básica de imagens utilizando ***LazyColumn***. Primeiro vamos criar uma **data class** para representar as imagens:

```kotlin
data class ResourceImage(
    @DrawableRes val resourceId: Int,
    val title: String
)
```

Agora vamos criar o item da lista:

```kotlin
@Composable
private fun ImageItem(
    image: ResourceImage,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    val shape = RoundedCornerShape(16.dp)
    OutlinedCard(
        shape = shape,
        border = BorderStroke(1.dp, Color.Black),
        modifier = modifier
            .fillMaxWidth()
            .padding(8.dp)
            .clip(shape)
            .clickable { onClick.invoke() }
    ) {
        Row(verticalAlignment = Alignment.CenterVertically) {
            Image(
                painter = painterResource(id = image.resourceId),
                contentDescription = image.title,
                contentScale = ContentScale.Crop,
                modifier = Modifier
                    .size(100.dp)
                    .clip(shape)
            )
            Spacer(Modifier.width(8.dp))
            Text(
                text = image.title,
                fontSize = 18.sp
            )
        }
    }
}
```

E agora a nossa função utilizando a **LazyColumn**:

```kotlin
@Composable
private fun ImageList(
    imageList: List<ResourceImage>,
    modifier: Modifier = Modifier
) {
    val context = LocalContext.current
    LazyColumn(modifier = modifier) {
        items(imageList) { image ->
            ImageItem(
                image = image,
                onClick = {
                    Toast.makeText(context, image.title, Toast.LENGTH_SHORT).show()
                }
            )
        }
    }
}
```

Para fins de exemplo, um **Toast** será exibido quando tocar no item da lista. Por fim, temos a nossa ***ImageScreen***:

```kotlin
@Composable
fun ImageScreen() {
    Column(
        modifier = Modifier
            .fillMaxSize()
    ) {
        val imageList = remember {
            List(25) { index ->
                ResourceImage(
                    resourceId = if (index % 2 == 0) R.drawable.img_nature else R.drawable.img_moon_space,
                    title = "Image $index"
                )
            }
        }
        ImageList(imageList = imageList)
    }
}
```

<img src="../lists/img-01.gif" alt="LazyColumn" width="50%" height="30%"/>

Uma ***LazyRow*** segue o mesmo princípio e é fácil implementar. Para evitar uma seção longa, não teremos exemplos dela por aqui.

## LazyListScope

Diferente de outros componentes no Compose onde o último parâmetro é um **content** que espera um Composable qualquer, as lazy lists funcionam de uma maneira um pouco diferente, fornecendo um ***LazyListScope.()***. Como pode ver no código da **ImageList()**, nós usamos a função ***items()*** que recebe como argumento a nossa lista de imagens. Além de ***items()***, podemos utilizar a ***itemsIndexed()***, que segue praticamente o mesmo princípio do items(), mas incluindo um index; e também a ***item()***, que pode ser usada para criar um item específico sem necessariamente fazer parte da lista.

Vamos modificar um pouco a função **ImageList()**, adicionando um ***item()*** antes e depois da nossa lista de imagens. Veja o exemplo:

```kotlin
@Composable
private fun ImageListWithItem(
    imageList: List<ResourceImage>,
    modifier: Modifier = Modifier,
) {
    val context = LocalContext.current
    LazyColumn(modifier = modifier) {
        item {
            Box(
                modifier = Modifier
                    .fillMaxWidth()
                    .height(50.dp)
                    .background(Color.Blue)
                    .padding(8.dp)
            ) {
                Text(
                    text = "Minhas imagens",
                    color = Color.White,
                    fontSize = 16.sp,
                    modifier = Modifier.align(Alignment.CenterStart)
                )
            }
        }

        items(imageList) { image ->
            ImageItem(
                image = image,
                onClick = {
                    Toast.makeText(context, image.title, Toast.LENGTH_SHORT).show()
                }
            )
        }

        item {
            Box(
                modifier = Modifier
                    .fillMaxWidth()
                    .height(50.dp)
                    .background(Color.Red)
                    .padding(8.dp)
            ) {
                Text(
                    text = "Nenhuma nova imagem por enquanto.",
                    color = Color.White,
                    fontSize = 16.sp,
                    modifier = Modifier.align(Alignment.CenterStart)
                )
            }
        }
    }
}
```

<img src="../lists/img-02.gif" alt="LazyColumn com item" width="50%" height="30%"/>

## Lazy grids

Além da ***LazyColumn*** e ***LazyRow***, Compose oferece as opções ***LazyVerticalGrid*** e ***LazyHorizontalGrid***. Assim como as citadas anteriormente, as listas em formato grid são bem fáceis de utilizar. Vamos reutilizar o código que já fizemos antes e modificar apenas as funções ***ImageList()*** e ***ImageItem()***.

```kotlin
@Composable
private fun ImageList(
    imageList: List<ResourceImage>,
    modifier: Modifier = Modifier
) {
    val context = LocalContext.current
    LazyVerticalGrid(
        columns = GridCells.Fixed(3),
        modifier = modifier
    ) {
        items(imageList) { image ->
            ImageItem(
                image = image,
                onClick = {
                    Toast.makeText(context, image.title, Toast.LENGTH_SHORT).show()
                }
            )
        }
    }
}

@Composable
private fun ImageItem(
    image: ResourceImage,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
) {
    Card(
        shape = RoundedCornerShape(0.dp),
        modifier = modifier
            .aspectRatio(1f)
            .padding(2.dp)
            .clickable { onClick.invoke() }
    ) {
        Image(
            painter = painterResource(id = image.resourceId),
            contentDescription = image.title,
            contentScale = ContentScale.Crop,
            modifier = Modifier
                .aspectRatio(1f)
        )
    }
}
```

<img src="../lists/img-03.png" alt="LazyVerticalGrid" width="50%" height="30%"/>

Como pode ver, a utilização é bem simples. Precisamos apenas definir obrigatoriamente um número de ***columns*** da grid, que no caso definimos com um número fixo de 3 itens com ```GridCells.Fixed(3)```. Também podemos utilizar um número adaptável, como ```GridCells.Adaptive(128.dp)```, para definir cada coluna como tendo pelo menos 128.dp. Isso pode ser útil para diferentes tamanhos de tela, já que ela tentará se adaptar de acordo.

Se fizermos a modificação de ```columns = GridCells.Fixed(3)``` para ```columns = GridCells.Adaptive(128.dp)```, podemos obter os mesmos 3 itens dependendo do tamanho da tela. Porém, ao girarmos a tela e ficarmos no modo landscape, veremos bem mais itens, como mostra a imagem abaixo.

<img src="../lists/img-04.png" alt="LazyVerticalGrid Adaptive" width="80%" height="50%"/>

## Conclusão

Há muito mais para se ver sobre **listas no Compose**. Cada componente oferece também outras opções de personalização que não foram mostrados nessa seção, como ***contentPadding***, ***verticalArrangement*** e ***horizontalAlignment***. Também há a opção de [Lazy staggered grid](https://developer.android.com/jetpack/compose/lists#lazy-staggered-grid) que pode ser útil em diferentes casos, além de [animação de itens](https://developer.android.com/jetpack/compose/lists#item-animations) com ```Modifier.animateItemPlacement()```.

Essa seção focou em mostrar um pouco sobre como as listas funcionam no Compose. Para quem veio do **RecyclerView**, as lazy lists no Compose são o paraíso. Leia a documentação para saber muito mais sobre listas.

## :link: Conteúdos auxiliares:
- [Lists and grids (documentação)](https://developer.android.com/jetpack/compose/lists)