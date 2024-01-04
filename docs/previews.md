# Previews: Visualize seus layouts

Jetpack Compose fornece um meio de visualizar seus layouts e componentes através de **Previews**. Previews nada mais são do que funções Composable com mais uma anotação: ```@Preview```. Ao anotar a função com ```@Preview```, o Android Studio carregará a visualização desse Composable. Veja o exemplo:

![Previews](img-01.png)

Normalmente, você adiciona o conteúdo da Preview com o tema do seu app, que é uma função Composable (normalmente) localizada no arquivo **Theme.kt**, onde são definidas as configurações do tema, como cores, shapes, typography etc. Como geralmente você define as cores e outros atributos dos componentes usando o tema do app, como um **Text()** com ```color = MaterialTheme.colorScheme.onPrimary``` (pode mudar dependendo do light e dark mode) ao invés de ```Color.White``` (valor literal), por exemplo, você se beneficia de algumas propriedades da Preview que vão ser abordadas em seguida.

Nesse caso, no exemplo da imagem acima, ao invés disso:

```kotlin
@Preview
@Composable
private fun Preview() { 
    Item()
}
```

Teremos isso:

```kotlin
@Preview
@Composable
private fun Preview() {
    MyAppTheme {
        Item()
    }
}
```

## Propriedades

Preview possui várias propriedades que você pode definir para alterar o comportamento da visualização. Veja alguns exemplos a seguir.

#### Mostrando UI do sistema

```@Preview(showSystemUi = true)```:

![Preview com SystemUI](img-02.png)

#### Tema light e dark

Para ver uma real diferença, teremos um exemplo usando cores do tema, como ```MaterialTheme.colorScheme.surface``` e ```MaterialTheme.colorScheme.onSurface```

```kotlin
@Composable
private fun Profile() {
    Column(
        horizontalAlignment = Alignment.CenterHorizontally,
        modifier = Modifier
            .width(250.dp)
            .background(MaterialTheme.colorScheme.surface)
    ) {
        Icon(
            painter = painterResource(id = R.drawable.baseline_person_24),
            contentDescription = "Profile image",
            tint = MaterialTheme.colorScheme.onSurface,
            modifier = Modifier
                .size(50.dp)
        )
        Text(
            text = "John",
            color = MaterialTheme.colorScheme.onSurface
        )
    }
}
```
Podemos usar várias **@Preview** numa mesma função, dessa forma carregará múltiplas previews do nosso conteúdo com as modificações que adicionarmos. Estamos modificando a propriedade **name** para a legibilidade no Android Studio e a **uiMode** de uma das Previews para usar o dark mode do tema.

```kotlin
@Preview(name = "Default")
@Preview(name = "Dark theme", uiMode = Configuration.UI_MODE_NIGHT_YES)
@Composable
private fun Preview() {
    MyAppTheme {
        Profile()
    }
}
```

![Preview com tema light e dark](img-03.png)

#### Outras propriedades

Há algumas outras propriedades que você pode utilizar para alterar sua Preview, sendo algumas delas:

- **showBackground** e **backgroundColor**.
- **device**, onde você pode usar um valor de **Devices**, como **Devices.PIXEL_3A_XL** (por padrão, nenhum é usado). Juntamente com **showSystemUi = true**, ela muda a aparência do dispositivo mostrado na Preview.
- **locale**, que você pode utilizar para carregar recursos, como valores do **strings.xml**, de acordo com o locale passado.

## :link: Conteúdos auxiliares:
- [Previews (documentação)](https://developer.android.com/jetpack/compose/tooling/previews)