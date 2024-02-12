# Checkbox, RadioButton e Switch

Os componentes **Checkbox**, **RadioButton** e **Switch** fazem parte da categoria de controles de seleção, permitindo aos usuários selecionarem entre um estado marcado/desmarcado ou ativado/desativado.

## Checkbox

O **Checkbox** é utilizado principalmente para representar um estado de "marcado/desmarcado", além de uma seleção múltipla e não exclusiva de itens. A implementação básica de um **Checkbox** segue o código abaixo:

```kotlin
@Composable
private fun Checkbox() {
    var checked by remember { mutableStateOf(true) }
    Checkbox(
        checked = checked,
        onCheckedChange = { isChecked ->
            checked = isChecked
        }
    )
}
```

<img src="../selectioncontrols/img-01.png" alt="Checkbox" width="30%" height="10%"/>

Não há muito segredo, mas como pode ver, ele só possui a estrutura para o **Checkbox** em si. O formato de layout com texto ao lado deve ser montado personalizadamente. Isso também vale para o **RadioButton** e **Switch**. Vamos ver então uma implementação comum, com um **Text()** ao lado. Além disso, também podemos definir as cores para os diferentes estados (checked, unchecked etc.) através do parâmetro **colors**. Veja o código:

```kotlin
@Composable
private fun Checkbox() {
    var checked by remember { mutableStateOf(true) }
    Row(
        verticalAlignment = Alignment.CenterVertically,
        modifier = Modifier
            .clip(RoundedCornerShape(16.dp))
            .clickable { checked = !checked }
            .padding(horizontal = 6.dp)
    ) {
        Checkbox(
            checked = checked,
            onCheckedChange = { isChecked ->
                checked = isChecked
            },
            colors = CheckboxDefaults.colors(
                checkedColor = Color.Black,
                uncheckedColor = Color.Red,
                checkmarkColor = Color.Yellow
            )
        )
        Text(
            text = "Tema Dark",
            fontSize = 18.sp,
            modifier = Modifier.padding(start = 6.dp)
        )
    }
}
```

<img src="../selectioncontrols/img-02.gif" alt="Checkbox" width="60%" height="20%"/>

## RadioButton

Diferente do **Checkbox**, o **RadioButton** possui os parâmetros principais como **selected** e **onClick**. Ele é mais comumente usado para uma lista de opções onde um único item é selecionável. Também é possível definir cores personalizadas para ele, mas vamos usar as cores padrão. Veja o pequeno exemplo:

```kotlin
@Composable
private fun RadioButton() {
    val options = listOf("Padrão do sistema", "Dark", "Light")
    var selectedOption by remember { mutableStateOf(options[0]) }
    Column(
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally,
        modifier = Modifier
            .padding(18.dp)
    ) {
        Text(
            text = "Selecione o tema do app",
            fontSize = 22.sp
        )
        Spacer(Modifier.height(18.dp))
        options.forEach { option ->
            Row(
                verticalAlignment = Alignment.CenterVertically,
                modifier = Modifier
                    .fillMaxWidth()
                    .clip(RoundedCornerShape(16.dp))
                    .clickable { selectedOption = option }
            ) {
                RadioButton(
                    selected = (option == selectedOption),
                    onClick = { selectedOption = option }
                )
                Text(
                    text = option,
                    fontSize = 18.sp,
                    modifier = Modifier.padding(start = 6.dp)
                )
            }
        }
    }
}
```

<img src="../selectioncontrols/img-03.gif" alt="RadioButton" width="80%" height="30%"/>

## Switch

**Switch** é comumente utilizado para representar um estado de "ligado/desligado" e é bem similar ao **Checkbox** em implementação, mas há um parâmetro adicional **thumbContent** que você pode utilizar para personalizar o conteúdo desenhado dentro da **thumb**. No código de exemplo abaixo, estamos modificando o ícone de acordo com o estado atual do **Switch**, além de definir algumas cores personalizadas.

```kotlin
@Composable
private fun Switch() {
    var checked by remember { mutableStateOf(true) }
    Row(
        verticalAlignment = Alignment.CenterVertically,
        modifier = Modifier
            .clip(RoundedCornerShape(16.dp))
            .clickable { checked = !checked }
            .padding(horizontal = 6.dp)
    ) {
        Switch(
            checked = checked,
            onCheckedChange = { isChecked ->
                checked = isChecked
            },
            thumbContent = {
                val icon = if (checked) Icons.Filled.Check else Icons.Filled.Close
                Icon(
                    imageVector = icon,
                    contentDescription = null,
                    modifier = Modifier.size(SwitchDefaults.IconSize),
                )
            },
            colors = SwitchDefaults.colors(
                checkedThumbColor = Color.White,
                uncheckedThumbColor = Color.Black,
                checkedTrackColor = Color.Black,
                uncheckedTrackColor = Color.DarkGray,
                checkedIconColor = Color.Black,
                uncheckedIconColor = Color.White,
                checkedBorderColor = Color.Green,
                uncheckedBorderColor = Color.Red
            )
        )
        Text(
            text = "Tema Dark",
            fontSize = 18.sp,
            modifier = Modifier.padding(start = 6.dp)
        )
    }
}
```

<img src="../selectioncontrols/img-04.gif" alt="Switch" width="60%" height="20%"/>

- Se desejar ter o ícone personalizado apenas quando estiver no estado de ativado, você pode tornar **thumbContent** null quando o estado for desativado. Por exemplo:

```kotlin
@Composable
private fun Switch() {
    var checked by remember { mutableStateOf(true) }
    Row(
       ...
    ) {
        Switch(
            ...,
            thumbContent = if (checked) {
                {
                    Icon(
                        imageVector = Icons.Filled.Check,
                        contentDescription = null,
                        modifier = Modifier.size(SwitchDefaults.IconSize),
                    )
                }
            } else {
                null
            },
            ...
        )
        ...
    }
}
```

## :link: Conteúdos auxiliares:
- [Checkbox (Material Design 3)](https://m3.material.io/components/checkbox/overview)
- [Switch (documentação)](https://developer.android.com/jetpack/compose/components/switch)
- [Switch (Material Design 3)](https://m3.material.io/components/switch/overview)