# Componentes 01: Text, TextField, Button, Image...

Componentes obviamente são uma parte fundamental de qualquer app e nessa seção você verá alguns dos mais importantes que podem ser usados no Compose. Já vimos os componentes **Text()** e **Image()** em algumas seções anteriores, mas nessa veremos outros de uma maneira um pouco mais ampla.

Todos os componentes possuem diversos parâmetros, então obviamente não abordaremos todos eles. Você pode navegar no componente específico ou colocar seu mouse sobre o componente para ver os parâmetros.

## Text

O componente Text não tem muitos segredos, então vamos ver apenas um exemplo com várias propriedades.

```kotlin
@Composable
private fun Text() {
    Text(
        text = "Jetpack Compose",
        color = Color.Blue,
        fontSize = 16.sp,
        fontWeight = FontWeight.Bold,
        fontFamily = FontFamily.SansSerif,
        letterSpacing = 4.sp
    )
}
```

![Text](img-01.png)

## TextField

Aqui você começará a ver coisas interessantes que se repetirão bastante no Compose. Vamos falar sobre algumas delas antes de ver o código do TextField:

- Como se trata de um campo de texto editável, o **TextField** usará o que já falamos no começo sobre **estados** (o texto) e **eventos** (quando o usuário digitar algo). No exemplo do código, temos uma linha importante: ```var text by remember { mutableStateOf("") }```. Basicamente essa variável será lida como um estado que, ao ser modificada, acionará a **recomposição**. Ou seja, o texto do TextField mudará. Veremos explicações melhores sobre isso na seção sobre **estados**.
- TextField tem diversos parâmetros que **recebem como argumentos funções Composable**. Por exemplo, **label**. Ao invés de passarmos apenas uma String ou algo do tipo, colocamos o nosso próprio componente **Text()** ou o que quisermos de Composable. Isso é muito útil no Compose e já vimos isso várias vezes na seção de layouts de forma implícita. Por exemplo, **Column** tem como último parâmetro ```content: @Composable() (ColumnScope.() -> Unit)```.
- Ele usa o padrão de **state hoisting (elevação de estado)**. Recebe o valor com **value** e, quando esse valor muda, aciona **onValueChange** com o novo valor. Veremos mais sobre isso na seção sobre esse tema.

Vamos ao exemplo de um TextField:

```kotlin
@Composable
private fun TextField() {
    var text by remember { mutableStateOf("") }
    TextField(
        value = text,
        onValueChange = { newText ->
            text = newText
        },
        label = {
            Text(text = "Label")
        },
        colors = TextFieldDefaults.colors(
            unfocusedIndicatorColor = Color.Blue,
            unfocusedLabelColor = Color.Blue
        )
    )
}
```

![TextField](img-02.png)

Se trocarmos a propriedade **label** por **placeholder**, quando digitarmos algo, o **placeholder** não irá aparecer:

![TextField com placeholder](img-03.png)

Também temos várias outras opções, como **leadingIcon**, **trailingIcon** e **keyboardOptions**:

```kotlin
@Composable
private fun TextField() {
    var text by remember { mutableStateOf("") }
    TextField(
        value = text,
        onValueChange = { newText ->
            text = newText
        },
        label = {
            Text(text = "E-mail")
        },
        leadingIcon = {
            Icon(
                imageVector = Icons.Default.Email,
                contentDescription = "Email icon"
            )
        },
        trailingIcon = {
            IconButton(onClick = { text = "" }) {
                Icon(
                    imageVector = Icons.Default.Close,
                    contentDescription = "Close icon"
                )
            }
        },
        keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Email),
        colors = TextFieldDefaults.colors(
            unfocusedIndicatorColor = Color.Blue,
            focusedLabelColor = Color.Blue
        )
    )
}
```

![TextField](img-04.png)

##### É bom lembrar...
Veja que usamos 2 ícones. Nós poderíamos definir as cores desses ícones com a propriedade **tint**, mas não fizemos isso. No TextField, evite modificar as cores de componentes como esse diretamente. Através da opção **colors** do TextField, você pode adicionar as cores do **leadingIcon** e **trailingIcon**, tanto nos estados ***focused*** quanto ***unfocused***, assim como foi feito no **IndicatorColor**. Se adicionarmos uma cor manualmente ao Icon(), perderemos essa função.

#### OutlinedTextField

Também temos a variação do TextField: **OutlinedTextField**. Basta mudar o nome **TextField** para **OutlinedTextField** que o estilo mudará. No exemplo abaixo, também usamos uma transformação de texto em formato de senha.

```kotlin
@Composable
private fun OutlinedTextField() {
    var text by remember { mutableStateOf("") }
    OutlinedTextField(
        value = text,
        onValueChange = { newText ->
            text = newText
        },
        label = {
            Text(text = "Senha")
        },
        visualTransformation = PasswordVisualTransformation(),
        keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Password),
        colors = TextFieldDefaults.colors(
            unfocusedIndicatorColor = Color.Blue,
            unfocusedLabelColor = Color.Blue,
            unfocusedContainerColor = Color.White
        )
    )
}
```

![OutlinedTextField](img-05.png)

Você também pode mudar o shape do TextField/OutlinedTextField com a propriedade **shape**, por exemplo: ```shape = CircleShape```.

![OutlinedTextField CircleShape](img-06.png)

#### BasicTextField

Compose também oferece a opção de **BasicTextField**, que é basicamente um TextField sem decorações que pode ser usado quando você quiser personalizar um TextField completamente. As opções que você viu acima são fornecidas através do pacote do ***Material Design 3***, que implementa nativamente diretrizes do Material Design 3, enquanto o BasicTextField é do ***androidx.compose.foundation.text***. As implementações do ***Material Design 3*** são mais recomendadas e serão suficientes na maioria das vezes. Consulte a [documentação](https://developer.android.com/jetpack/compose/text/user-input) caso queira saber mais sobre TextFields. Não há imagens de exemplo porque ele basicamente é apenas um Text editável, mas a implementação básica é:

```kotlin
@Composable
private fun BasicTextField() {
    var text by remember { mutableStateOf("") }
    BasicTextField(
        value = text,
        onValueChange = { newText ->
            text = newText
        }
    )
}
```