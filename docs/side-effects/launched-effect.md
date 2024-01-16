# Side Effects

Um side effect (efeito colateral) é uma mudança no estado do aplicativo que ocorre fora do escopo de uma função Composable. Devido ao ciclo de vida e às propriedades dos Composables, como recomposições imprevisíveis, execução de recomposições de Composables em ordens diferentes ou recomposições que podem ser descartadas (como falamos na seção de conhecimentos básicos iniciais), **o ideal é que funções Composable sejam livres de efeitos colaterais**.

No entanto, **efeitos colaterais às vezes são necessários**. Por exemplo, para desencadear um evento único, como mostrar um **Snackbar** ou navegar para outra tela dada uma determinada condição de estado. Essas ações devem ser chamadas a partir de um ambiente controlado que esteja ciente do ciclo de vida do Composable.

## LaunchedEffect

Para chamar **suspend functions** dentro de uma função Composable, podemos utilizar o **LaunchedEffect**. Quando LaunchedEffect entrar na composição, ele lança uma coroutine com o bloco de código passado como parâmetro. A coroutine será cancelada se LaunchedEffect sair da composição.

Vamos ver um exemplo simples, que não é o ideal no "mundo real", mas ainda pode exemplificar bem um uso do LaunchedEffect. Temos uma classe **ProfileDataSource** com uma **suspend function getProfileName()**. Por fim, temos uma Composable **Profile()** responsável por exibir um loading ou texto de sucesso, exibindo o nome de perfil obtido através da **getProfileName()**, ou um texto de erro. Obviamente, isso não é possível no cenário comum de uma função Composable, pois não podemos simplesmente chamar **getProfileName()** na **Profile()**. Primeiro, porque é uma suspend function, e, segundo, porque ela seria chamada inúmeras vezes devido a recomposição. Veja o exemplo de código:

```kotlin
class ProfileDataSource {

    suspend fun getProfileName(): String {
        delay(3000L)
        return "Compose Journey"
    }
}

enum class State {
    Loading,
    Success,
    Error
}

@Composable
private fun Profile() {
    val profileDataSource = remember { ProfileDataSource() }
    var state by remember { mutableStateOf(State.Loading) }
    var profileName by remember { mutableStateOf("") }

    // Isso não é possível
    profileName = profileDataSource.getProfileName()

    Column(
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally,
        modifier = Modifier.fillMaxSize()
    ) {
        when (state) {
            State.Loading -> {
                CircularProgressIndicator(color = Color.Blue)
            }
            State.Success -> {
                Text(text = "Nome do perfil: $profileName")
            }
            State.Error -> {
                Text(text = "Erro ao obter o nome do perfil.")
            }
        }
    }
}
```

Para resolver esse problema, podemos utilizar **LaunchedEffect**:

```kotlin
@Composable
private fun Profile() {
    val profileDataSource = remember { ProfileDataSource () }
    var state by remember { mutableStateOf(State.Loading) }
    var profileName by remember { mutableStateOf("") }

    LaunchedEffect(Unit) {
        profileName = profileDataSource.getProfileName()
        state = State.Success
    }

    ...
}
```

Dessa forma, obtemos o nome do perfil e fazemos a atualização necessária em **state**. **Unit** aqui é usado como **key**, pois queremos executar esse trecho do LaunchedEffect apenas na primeira composição. LaunchedEffect aceita um número variável de **keys**. Quando o valor de uma das keys muda, a coroutine existente será cancelada e o trecho de LaunchedEffect será iniciado em uma nova coroutine.

Veja um pequeno código de **timer** abaixo que exemplifica o LaunchedEffect reagindo com as mudanças de uma **key**. Poderíamos fazer o código abaixo de inúmeras formas diferentes, mas novamente, serve como um exemplo.

```kotlin
@Composable
private fun CountdownTimer() {
    val initialTime = 5
    var currentTime by remember { mutableIntStateOf(initialTime) }

    LaunchedEffect(key1 = currentTime) {
        if (currentTime > 0) {
            delay(1000L)
            currentTime -= 1
        }
    }

    Box(
        contentAlignment = Alignment.Center,
        modifier = Modifier
            .size(250.dp)
            .clickable { currentTime = initialTime }
            .padding(50.dp)
            .border(
                width = 3.dp,
                color = Color.Red,
                shape = CircleShape
            )
    ) {
        Text(
            text = "$currentTime",
            fontSize = 24.sp,
            fontWeight = FontWeight.Bold
        )
    }
}
```

<img src="../launched-effect/img-01.gif" alt="LaunchedEffect" width="50%" height="30%"/>

No exemplo acima, estamos usando a suspend function **delay()** e LaunchedEffect irá atualizar **currentTime** enquanto ele for > 0. Toda vez que clicarmos na tela, o contador será reiniciado, pois definimos **currentTime = initialTime** no clickable do Box. Não precisamos de nenhum loop para isso, pois LaunchedEffect reinicia o trecho de código dentro dele sempre que o valor da **key currentTime** muda.

## :link: Conteúdos auxiliares:
- [Side-effects in Compose (documentação)](https://developer.android.com/jetpack/compose/side-effects)
- [Jetpack Compose Side-Effects I — LaunchedEffect (artigo)](https://proandroiddev.com/jetpack-compose-side-effects-launchedeffect-59d2330d7834)