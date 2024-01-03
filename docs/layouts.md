# Layouts: Column, Row, Box, ConstraintLayout

Construir seus layouts é obviamente uma parte fundamental em qualquer app. Essa seção descreve os principais layouts que você pode utilizar no Compose.

## Sem layout

Sem especificar um layout, os componentes Composable vão ser colocados uns sobre os outros, deixando completamente desorganizado. Veja um exemplo:

```kotlin
@Composable
fun Profile() {
    Image(
        painter = painterResource(id = R.drawable.baseline_person_24),
        contentDescription = "profile image",
        modifier = Modifier.size(50.dp)
    )
    Text(text = "John")
}
```

![Sem layout demo](img-01.png)

## Column

Como o nome sugere, uma **Column** é uma espécie de coluna onde cada elemento é colocado um abaixo do outro, verticalmente.
Seria o mesmo que um **LinearLayout** com a orientação definida como vertical. Vamos usar o código anterior e envolvê-lo em uma Column para ver como fica:

```kotlin
@Composable
fun Profile() {
    Column {
        Image(
            painter = painterResource(id = R.drawable.baseline_person_24),
            contentDescription = "profile image",
            modifier = Modifier.size(50.dp)
        )
        Text(text = "John")
    }
}
```

![Column](img-02.png)

### Alinhamento de uma Column

Uma Column possui mais 2 parâmetros que podem ser usados para alinhar o conteúdo horizontal e vertical.
Veja como ficaria o alinhamento horizontal ao modificar o **horizontalAlignment**:

=== "Alignment.Start" 

    ![Column com Alignment.Start](img-03.png)

    ```kotlin
    @Composable
    fun Profile() {
        Column(
           horizontalAlignment = Alignment.Start,
           modifier = Modifier.width(250.dp)
        ) {
            ...
        }
    }
    ```

=== "Alignment.CenterHorizontally" 

    ![Column com Alignment.CenterHorizontally](img-04.png)

    ```kotlin
    @Composable
    fun Profile() {
        Column(
           horizontalAlignment = Alignment.CenterHorizontally,
           modifier = Modifier.width(250.dp)
        ) {
            ...
        }
    }
    ```

=== "Alignment.End" 

    ![Column com Alignment.End](img-05.png)

    ```kotlin
    @Composable
    fun Profile() {
        Column(
           horizontalAlignment = Alignment.End,
           modifier = Modifier.width(250.dp)
        ) {
            ...
        }
    }
    ```
