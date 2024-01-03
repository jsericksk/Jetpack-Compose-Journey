# Column

Construir seus layouts é obviamente uma parte fundamental em qualquer app. Essa seção descreve um pouco sobre o layout Column.

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

![Sem layout](img-01.png)

## Column

Como o nome sugere, uma **Column** é uma espécie de coluna onde cada elemento é colocado um abaixo do outro, verticalmente.
É o equivalente a um **LinearLayout** com a orientação vertical. Vamos usar o código anterior e envolvê-lo em uma Column para ver como fica:

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

    ![Alignment.Start](img-03.png)

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

    ![Alignment.CenterHorizontally](img-04.png)

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

    ![Alignment.End](img-05.png)

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

E, claro, também podemos modificar o **verticalArrangement**.

=== "Arrangement.Top" 

    ![Arrangement.Top](img-06.png)

    ```kotlin
    @Composable
    fun Profile() {
        Column(
           verticalArrangement = Arrangement.Top,
           modifier = Modifier.size(150.dp)
        ) {
            ...
        }
    }
    ```

=== "Arrangement.Center" 

    ![Arrangement.Center](img-07.png)

    ```kotlin
    @Composable
    fun Profile() {
        Column(
           verticalArrangement = Arrangement.Center,
           modifier = Modifier.size(150.dp)
        ) {
            ...
        }
    }
    ```

=== "Arrangement.Bottom" 

    ![Arrangement.Bottom](img-08.png)

    ```kotlin
    @Composable
    fun Profile() {
        Column(
           verticalArrangement = Arrangement.Bottom,
           modifier = Modifier.size(150.dp)
        ) {
            ...
        }
    }
    ```

=== "Arrangement.SpaceBetween" 

    ![Arrangement.SpaceBetween](img-09.png)

    ```kotlin
    @Composable
    fun Profile() {
        Column(
           verticalArrangement = Arrangement.SpaceBetween,
           modifier = Modifier.size(150.dp)
        ) {
            ...
        }
    }
    ```

Também podemos fazer uma combinações de **verticalArrangement** e **horizontalAlignment** para deixar todo o conteúdo centralizado verticalmente e horizontalmente, por exemplo.

![Column centralizada](img-10.png)

```kotlin
@Composable
fun Profile() {
    Column(
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally,
        modifier = Modifier.size(150.dp)
    ) {
        ...
    }
}
```

## :link: Referências:
- [Compose layout basics (documentação)](https://developer.android.com/jetpack/compose/layouts/basics)