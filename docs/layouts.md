# Layouts: Column, Row, Box, ConstraintLayout

Construir seus layouts é obviamente uma parte fundamental em qualquer app. Essa seção descreve os principais layouts que você pode utilizar no Compose.

## Sem especificar layout

Sem especificar um layout, os componente Composable vão ser desenhados uns sobre o outro, deixando completamente desorganizado. Veja um exemplo:

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

## Column

Como o nome sugere, uma **Column** é uma espécie de coluna onde cada elemento é colocado um abaixo do outro, verticalmente.