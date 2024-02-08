# Telas, ViewModels e navegação

Na grande maioria das vezes, os aplicativos possuem mais de uma tela e uma navegação entre elas. Nessa seção veremos um pouco sobre a construção de um app simples com telas, uma navegação entre elas e também um componente muito conhecido e utilizado no Android, que é o **ViewModel**.

## O que faremos

Vamos construir um app bem básico com apenas duas telas. Será um app do tipo rastreador de encomendas, com uma tela inicial e uma tela de informações sobre o rastreio. Para não tornar as coisas muito complexas e como esse não é o objetivo dessa seção, não há uma funcionalidade real no app, nem conexão com a internet ou coisas do tipo.

Diferente das seções anteriores, dessa vez teremos uma pequena organização a mais. Você pode fazer da forma que preferir, mas vamos utilizar um padrão bem utilizado, que é o de um nome de pacote por tela. Primeiro criaremos um pacote **screens** onde terá mais dois pacotes representando as duas telas, **home** e **tracking**. Cada pacote terá um arquivo relacionado aquela tela. Veja o exemplo abaixo:

```
...
screens:
    home:
        HomeScreen.kt
        HomeViewModel.kt
        ...
    tracking:
        TrackingScreen.kt
        ...
...
```

## Criando as strings

Dessa vez não usaremos hardcoded strings, mas colocaremos todos os textos em [**res/strings.xml**](https://developer.android.com/guide/topics/resources/localization). Abaixo estão todas as strings que usaremos nessa seção.

```xml
<resources>
    <string name="app_name">Compose Journey</string>

    <string name="tracker">Rastreador</string>
    <string name="tracking_code">Código de rastreio</string>
    <string name="cep">CEP</string>
    <string name="track">Rastrear</string>
    <string name="tracking">Rastreamento</string>
    <string name="order_status">Status: Em trânsito</string>
</resources>
```

## Criando a HomeScreen

Vamos começar criando a **HomeScreen**:

<details>
  <summary>Ver código da HomeScreen</summary>

```kotlin
const val UndefinedCep = -1

@Composable
fun HomeScreen(onNavigateToTracking: (code: String, cep: Int) -> Unit) {
    var code by remember { mutableStateOf("") }
    var cepNumber by remember { mutableIntStateOf(UndefinedCep) }
    val cepText: String = remember(cepNumber) {
        if (cepNumber == UndefinedCep) "" else cepNumber.toString()
    }
    Column(
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally,
        modifier = Modifier
            .fillMaxSize()
            .padding(24.dp)
    ) {
        val verticalSpacing = 14.dp
        Text(
            text = stringResource(id = R.string.tracker),
            color = MaterialTheme.colorScheme.primary,
            fontSize = 34.sp,
            fontWeight = FontWeight.Bold
        )
        Spacer(Modifier.height(verticalSpacing + verticalSpacing))
        CommonTextField(
            text = code,
            onTextChange = {
                code = it
            },
            label = stringResource(id = R.string.tracking_code),
            leadingIcon = Icons.Default.Info
        )
        Spacer(Modifier.height(verticalSpacing))
        CommonTextField(
            text = cepText,
            onTextChange = { updatedCep ->
                updatedCep.toIntOrNull()?.let { cep ->
                    cepNumber = cep
                }
                if (updatedCep.isBlank()) {
                    cepNumber = UndefinedCep
                }
            },
            label = stringResource(id = R.string.cep),
            leadingIcon = Icons.Default.LocationOn,
            keyboardType = KeyboardType.Number
        )
        Spacer(Modifier.height(verticalSpacing + verticalSpacing))
        Button(
            onClick = {
                onNavigateToTracking.invoke(code, cepNumber)
            },
            contentPadding = PaddingValues(16.dp),
            modifier = Modifier.fillMaxWidth()
        ) {
            Text(stringResource(id = R.string.track))
        }
    }
}

@Composable
private fun CommonTextField(
    text: String,
    onTextChange: (String) -> Unit,
    label: String,
    leadingIcon: ImageVector,
    modifier: Modifier = Modifier,
    keyboardType: KeyboardType = KeyboardType.Text
) {
    OutlinedTextField(
        value = text,
        onValueChange = onTextChange,
        label = {
            Text(text = label)
        },
        leadingIcon = {
            Icon(
                imageVector = leadingIcon,
                contentDescription = null
            )
        },
        shape = RoundedCornerShape(16.dp),
        singleLine = true,
        keyboardOptions = KeyboardOptions(keyboardType = keyboardType),
        modifier = modifier.fillMaxWidth()
    )
}
```
</details>

<img src="../screens/img-01.png" alt="HomeScreen" width="50%" height="20%"/>

É uma tela comum sem muitos segredos. Criamos a função **CommonTextField()**, sendo uma forma de componentizar o **OutlinedTextField** expondo apenas algumas das opções de personalizações que fazem sentido pra gente nesse contexto, além de usar o padrão de **state hoisting** que já vimos antes. Um destaque que vale a pena mencionar na **HomeScreen()** é o uso da **CommonTextField()** de **CEP** com uma pequena lógica para impedir a inserção de outros caracteres que não sejam números para um **CEP**. Mesmo definindo ```keyboardType = KeyboardType.Number```, o usuário ainda pode colar ou digitar caracteres como pontos (.), vírgulas (,) etc., então desejamos evitar isso.

Outro ponto importante é o lambda **onNavigateToTracking** como parâmetro da **HomeScreen()**. Você verá mais sobre ele mais a frente, quando chegarmos no tópico sobre navegação. Mas a princípio, a utilidade dele é ser invocado quando quisermos navegar da **HomeScreen()** para a **TrackingScreen()**, que também criaremos mais a frente. Como pode ver, ele é invocado no **onClick** do **Button()** de rastrear.

## ViewModel e state holder

Digamos que você precise validar o CEP ou até mesmo o código de rastreio, seja verificando se possuem determinada quantidade de caracteres ou se são válidos de acordo com alguma lógica de negócios vinda de uma **API**, por exemplo. Uma solução bem comum seria fazer isso através de um [**ViewModel**](https://developer.android.com/topic/libraries/architecture/viewmodel).

Um **ViewModel** ajuda a abstrair a lógica de negócios e também pode ser detentora de estado (state holder) no nível de tela. Ele expõe o estado à UI e encapsula a lógica de negócios relacionada. Sua principal vantagem é que ele armazena em cache o estado e o persiste durante alterações de configuração, como rotação de tela.

Pensando na nossa ideia de app, em outras palavras o **ViewModel** seria uma ponte entre os dados da tela e uma lógica de negócios, em, por exemplo, um esquema do tipo: **HomeScreen <--> HomeViewModel <--> TrackingRepository**, onde ao tocar no botão rastrear da **HomeScreen**, chamaríamos uma função do **HomeViewModel**, que por sua vez chamaria uma função do **TrackingRepository** para obter alguma informação, retornando essa informação para o **HomeViewModel** e posteriormente atualizando a UI da **HomeScreen**. No projeto que vamos fazer, esse "esquema" não faz muito sentido, pois ao tocar no botão rastrear vamos navegar para a **TrackingScreen**.

- É provável que você já tenha conhecimento sobre **ViewModel** e algum padrão de arquitetura, como **MVVM**, porém, achei interessante deixar essa pequena introdução. Não teremos nada relacionado a repositórios ou busca de dados em uma API nessa seção, pois esse não é o objetivo.

## UiState e UiEvent

Antes de tudo, precisamos elevar o estado da nossa **HomeScreen** para que possa ser usado posteriormente pelo **HomeViewModel**. Vamos fazer isso criando uma **data class HomeUiState** e, além dela, uma **sealed class HomeUiEvent**, representando os dados e eventos úteis da tela. Veja o código de ambas abaixo:

```kotlin
const val UndefinedCep = -1

data class HomeUiState(
    val code: String = "",
    val cep: Int = UndefinedCep
) {
    val cepText: String = if (cep == UndefinedCep) "" else cep.toString()
    val canNavigateToTrackingScreen: Boolean = code.isNotBlank() && cepText.isNotBlank()
}

sealed class HomeUiEvent {
    data class CodeChanged(val code: String) : HomeUiEvent()
    data class CepChanged(val cep: Int) : HomeUiEvent()
}
```

Na **HomeUiState**, nós temos mais uma propriedade a mais do que vimos antes, que é a **canNavigateToTrackingScreen**. O nome dela diz tudo, mas basicamente será usada apenas para habilitar ou desabilitar o botão de rastrear, responsável por navegar para a próxima tela. Precisamos de um cep do tipo **String** porque **TextField** só funciona com **Strings** (**cep** poderia ser do tipo **String** e evitaríamos ter **cepText**, mas para fins de exemplo, vamos manter como está). Também inicializamos **code** e **cep** com valores padrão. **É útil sempre inicializar os dados com valores padrão para facilitar a criação posteriormente**.

Já a **HomeUiEvent** será útil para encapsular a lógica de eventos que podem acontecer na nossa tela. No caso, a mudança nos valores de **code** e **cep**. Também há o evento de **navegar para a próxima tela** que ocorre com o toque no botão de rastrear, mas não vamos incluir ele por aqui. Essa classe fará mais sentido quando criarmos o **HomeViewModel** e reconstruir a **HomeScreen** logo abaixo.

## Criando o HomeViewModel

Agora vamos criar o **HomeViewModel** que serve como o state holder da nossa **HomeScreen**:

```kotlin
class HomeViewModel : ViewModel() {
    private val _uiState = MutableStateFlow(HomeUiState())
    val uiState: StateFlow<HomeUiState> = _uiState.asStateFlow()

    fun onUiEvent(event: HomeUiEvent) {
        when (event) {
            is HomeUiEvent.CodeChanged -> {
                _uiState.update { it.copy(code = event.code) }
            }
            is HomeUiEvent.CepChanged -> {
                _uiState.update { it.copy(cep = event.cep) }
            }
        }
    }
}
```

Estamos utilizando **StateFlow** para armazenar o estado da UI. Poderia ser feito de diversas outras formas, inclusive com **mutableState()**, mas vamos usar a abordagem com **Flow**. Como única função, temos **onUiEvent()**. Como não podemos modificar o estado da UI diretamente pela **HomeScreen** ao utilizarmos essa abordagem, encapsulamos essa lógica no **ViewModel** e tornamos essa tarefa um pouco mais fácil. Outro padrão comum poderia ser criar uma função para modificar cada estado, como **onCodeChanged(newCode: String)**, mas também não vamos por esse caminho, já que é praticamente o mesmo com pequenas diferenças. Chamaremos **onUiEvent** sempre que necessário na **HomeScreen**.

## Refazendo a HomeScreen

Como agora o estado da tela está no **HomeViewModel**, temos que fazer algumas alterações na **HomeScreen**. A primeira delas é criar uma nova função chamada **HomeContent** que terá um código bem similar ao que já vimos antes, porém, dessa vez utilizando a **HomeUiState** e **HomeUiEvent**. Basicamente ela terá o conteúdo principal da **HomeScreen**, recebendo o estado e expondo os eventos.

<details>
  <summary>Ver código da HomeContent</summary>

```kotlin
@Composable
fun HomeContent(
    uiState: HomeUiState,
    onUiEvent: (HomeUiEvent) -> Unit,
    onNavigateToTracking: () -> Unit
) {
    Column(
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally,
        modifier = Modifier
            .fillMaxSize()
            .padding(24.dp)
    ) {
        val verticalSpacing = 14.dp
        Text(
            text = stringResource(id = R.string.tracker),
            color = MaterialTheme.colorScheme.primary,
            fontSize = 34.sp,
            fontWeight = FontWeight.Bold
        )
        Spacer(Modifier.height(verticalSpacing + verticalSpacing))
        CommonTextField(
            text = uiState.code,
            onTextChange = {
                onUiEvent.invoke(HomeUiEvent.CodeChanged(it))
            },
            label = stringResource(id = R.string.tracking_code),
            leadingIcon = Icons.Default.Info
        )
        Spacer(Modifier.height(verticalSpacing))
        CommonTextField(
            text = uiState.cepText,
            onTextChange = { updatedCep ->
                updatedCep.toIntOrNull()?.let { cep ->
                    onUiEvent.invoke(HomeUiEvent.CepChanged(cep))
                }
                if (updatedCep.isBlank()) {
                    onUiEvent.invoke(HomeUiEvent.CepChanged(UndefinedCep))
                }
            },
            label = stringResource(id = R.string.cep),
            leadingIcon = Icons.Default.LocationOn,
            keyboardType = KeyboardType.Number
        )
        Spacer(Modifier.height(verticalSpacing + verticalSpacing))
        Button(
            onClick = onNavigateToTracking,
            contentPadding = PaddingValues(16.dp),
            enabled = uiState.canNavigateToTrackingScreen,
            modifier = Modifier.fillMaxWidth()
        ) {
            Text(text = stringResource(id = R.string.track))
        }
    }
}
```
</details>

Como pode ver, não estamos usando o **HomeViewModel** ainda. Agora vamos refazer a **HomeScreen** que usará o **HomeViewModel** e a **HomeContent**. Veja o código completo:

<details>
  <summary>Ver código da HomeScreen</summary>

```kotlin
@Composable
fun HomeScreen(
    onNavigateToTracking: (code: String, cep: Int) -> Unit,
    homeViewModel: HomeViewModel = viewModel()
) {
    val uiState by homeViewModel.uiState.collectAsStateWithLifecycle()
    HomeContent(
        uiState = uiState,
        onUiEvent = homeViewModel::onUiEvent,
        onNavigateToTracking = {
            onNavigateToTracking.invoke(uiState.code, uiState.cep)
        }
    )
}

@Composable
fun HomeContent(
    uiState: HomeUiState,
    onUiEvent: (HomeUiEvent) -> Unit,
    onNavigateToTracking: () -> Unit
) {
    Column(
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally,
        modifier = Modifier
            .fillMaxSize()
            .padding(24.dp)
    ) {
        val verticalSpacing = 14.dp
        Text(
            text = stringResource(id = R.string.tracker),
            color = MaterialTheme.colorScheme.primary,
            fontSize = 34.sp,
            fontWeight = FontWeight.Bold
        )
        Spacer(Modifier.height(verticalSpacing + verticalSpacing))
        CommonTextField(
            text = uiState.code,
            onTextChange = {
                onUiEvent.invoke(HomeUiEvent.CodeChanged(it))
            },
            label = stringResource(id = R.string.tracking_code),
            leadingIcon = Icons.Default.Info
        )
        Spacer(Modifier.height(verticalSpacing))
        CommonTextField(
            text = uiState.cepText,
            onTextChange = { updatedCep ->
                updatedCep.toIntOrNull()?.let { cep ->
                    onUiEvent.invoke(HomeUiEvent.CepChanged(cep))
                }
                if (updatedCep.isBlank()) {
                    onUiEvent.invoke(HomeUiEvent.CepChanged(UndefinedCep))
                }
            },
            label = stringResource(id = R.string.cep),
            leadingIcon = Icons.Default.LocationOn,
            keyboardType = KeyboardType.Number
        )
        Spacer(Modifier.height(verticalSpacing + verticalSpacing))
        Button(
            onClick = onNavigateToTracking,
            contentPadding = PaddingValues(16.dp),
            enabled = uiState.canNavigateToTrackingScreen,
            modifier = Modifier.fillMaxWidth()
        ) {
            Text(text = stringResource(id = R.string.track))
        }
    }
}

@Composable
private fun CommonTextField(
    text: String,
    onTextChange: (String) -> Unit,
    label: String,
    leadingIcon: ImageVector,
    modifier: Modifier = Modifier,
    keyboardType: KeyboardType = KeyboardType.Text
) {
    OutlinedTextField(
        value = text,
        onValueChange = onTextChange,
        label = {
            Text(text = label)
        },
        leadingIcon = {
            Icon(
                imageVector = leadingIcon,
                contentDescription = null
            )
        },
        shape = RoundedCornerShape(16.dp),
        singleLine = true,
        keyboardOptions = KeyboardOptions(keyboardType = keyboardType),
        modifier = modifier.fillMaxWidth()
    )
}
```
</details>

Na função **HomeScreen**, instanciamos o **HomeViewModel** e observamos o estado da UI coletando o **StateFlow** de **uiState** com a função **collectAsStateWithLifecycle()**. Qualquer alteração no estado da UI irá refletir na **HomeContent**. Lembre-se que para utilizar essa função, é necessário importar a dependência abaixo (cheque a versão atual na [documentação](https://developer.android.com/jetpack/androidx/releases/lifecycle)):

```gradle
implementation("androidx.lifecycle:lifecycle-runtime-compose:$version")
```

#### Por que criar duas funções, HomeScreen e HomeContent?

Não é o caso nesse projeto, mas normalmente o **ViewModel** usa algumas classes com **injeção de dependências**, utilizando alguma biblioteca para isso, como [**Hilt**](https://developer.android.com/training/dependency-injection/hilt-android). Por exemplo, o nosso **HomeViewModel** poderia ser algo como abaixo:

```kotlin
@HiltViewModel
class HomeViewModel @Inject constructor(
    private val trackingRepository: TrackingRepository
) : ViewModel() {
    // Restante do código
}
```

Nesse cenário bem comum, [**Previews** têm uma limitação em relação a **ViewModels**](https://developer.android.com/jetpack/compose/tooling/previews#preview-viewmodel). Se criarmos todo o conteúdo da tela e ao mesmo tempo instanciarmos o **ViewModel** na mesma função, nós obteríamos um erro ao tentar visualizar o layout com **Preview** e o layout não seria exibido. Em resumo, isso é feito principalmente para tornar as **Previews** acessíveis e também facilitar passar dados mais controlados para a tela em outros casos, como testes e as próprias **Previews**.

O [**Now in Android**](https://github.com/android/nowinandroid/blob/main/feature/interests/src/main/kotlin/com/google/samples/apps/nowinandroid/feature/interests/InterestsScreen.kt) segue um padrão similar, mas tendo nomenclaturas com sufixo **Route** e **Screen**. Por exemplo, **HomeRoute** e **HomeScreen**. Os nomes das funções ficam a seu critério, é claro.

## Criando a TrackingScreen, TrackingViewModel e TrackingUiState

Agora que já vimos alguns dos conceitos básicos que estamos usando, vamos criar a **TrackingScreen**, **TrackingViewModel** e **TrackingUiState**. Antes de tudo, crie um novo pacote chamado **tracking** onde esses arquivos serão criados, assim como já discutimos antes sobre a organização.

<details>
  <summary>Ver código da TrackingScreen</summary>

```kotlin
@Composable
fun TrackingScreen(
    code: String,
    cep: Int,
    onNavigateBack: () -> Unit,
    trackingViewModel: TrackingViewModel = viewModel()
) {
    val uiState by trackingViewModel.uiState.collectAsStateWithLifecycle()
    LaunchedEffect(Unit) {
        trackingViewModel.getTrackingInfo(code = code, cep = cep)
    }
    TrackingContent(
        uiState = uiState,
        onNavigateBack = onNavigateBack
    )
}

@OptIn(ExperimentalMaterial3Api::class)
@Composable
private fun TrackingContent(
    uiState: TrackingUiState,
    onNavigateBack: () -> Unit
) {
    Scaffold(
        topBar = {
            CenterAlignedTopAppBar(
                title = { Text(text = stringResource(id = R.string.tracking)) },
                colors = TopAppBarDefaults.topAppBarColors(
                    containerColor = MaterialTheme.colorScheme.primary,
                    titleContentColor = MaterialTheme.colorScheme.onPrimary,
                    navigationIconContentColor = MaterialTheme.colorScheme.onPrimary
                ),
                navigationIcon = {
                    IconButton(onClick = onNavigateBack) {
                        Icon(
                            imageVector = Icons.AutoMirrored.Default.ArrowBack,
                            contentDescription = null
                        )
                    }
                }
            )
        }
    ) { paddingValues ->
        MainContent(
            uiState = uiState,
            modifier = Modifier.padding(paddingValues)
        )
    }
}

@Composable
private fun MainContent(
    uiState: TrackingUiState,
    modifier: Modifier = Modifier
) {
    Column(
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally,
        modifier = modifier
            .fillMaxSize()
            .padding(24.dp)
    ) {
        Card(
            shape = RoundedCornerShape(16.dp),
            modifier = Modifier.padding(12.dp)
        ) {
            Column(
                horizontalAlignment = Alignment.CenterHorizontally,
                modifier = Modifier.padding(24.dp)
            ) {
                Text(
                    text = stringResource(id = R.string.tracking_code),
                    fontSize = 16.sp,
                    fontWeight = FontWeight.Normal
                )
                Spacer(Modifier.height(2.dp))
                Text(
                    text = uiState.code,
                    fontSize = 22.sp,
                    fontWeight = FontWeight.Bold,
                    textAlign = TextAlign.Center
                )
                Spacer(Modifier.height(16.dp))
                Text(
                    text = stringResource(id = R.string.cep),
                    fontSize = 16.sp,
                    fontWeight = FontWeight.Normal
                )
                Spacer(Modifier.height(2.dp))
                Text(
                    text = uiState.cep.toString(),
                    fontSize = 22.sp,
                    fontWeight = FontWeight.Bold,
                    textAlign = TextAlign.Center
                )
            }
        }
        Spacer(Modifier.height(16.dp))
        Row(verticalAlignment = Alignment.CenterVertically) {
            Icon(
                imageVector = Icons.Default.LocationOn,
                contentDescription = null
            )
            Spacer(Modifier.width(6.dp))
            Text(
                text = stringResource(id = R.string.order_status),
                fontSize = 18.sp,
                fontWeight = FontWeight.Normal,
                textAlign = TextAlign.Center
            )
        }
    }
}
```
</details>

<img src="../screens/img-02.png" alt="HomeScreen" width="50%" height="20%"/>

A **TrackingScreen** não possui nada do que já não vimos antes, mas como destaque, é possível perceber que ela possui 2 parâmetros, **code** e **cep**, que são os que iremos passar quando navegar da **HomeScreen**, além do **onNavigateBack**, que será acionado quando o ícone de arrow back for tocado para voltar à tela anterior. Ela também utiliza uma função do **TrackingViewModel** chamada **getTrackingInfo()**, que veremos posteriormente que é apenas uma forma de inicializar o estado da tela. Estamos usando **LaunchedEffect(Unit)** para que essa função seja chamada apenas na primeira composição da tela.

É importante notar que em um cenário real, onde **getTrackingInfo()** fosse uma função que busca informações em um repositório com base no código de rastreio e cep e devesse ser chamada apenas na primeira vez que a tela é exibida, chamá-la dessa forma com **LaunchedEffect(Unit)** talvez não fosse o mais ideal, pois a função ainda seria chamada em rotações de tela e buscaria os dados novamente. Mas funciona bem como exemplo.

```kotlin
class TrackingViewModel : ViewModel() {
    private val _uiState = MutableStateFlow(TrackingUiState())
    val uiState: StateFlow<TrackingUiState> = _uiState.asStateFlow()

    fun getTrackingInfo(
        code: String,
        cep: Int
    ) {
        _uiState.update { it.copy(code = code, cep = cep) }
    }
}
```

**TrackingViewModel** também não tem nada demais, então pouparei os comentários.

```kotlin
data class TrackingUiState(
    val code: String = "",
    val cep: Int = 0
)
```

Existe uma pequena observação sobre a **TrackingUiState**. Como pode ver, ela é bastante parecida com a **HomeUiState**. Normalmente ela teria mais propriedades, como por exemplo, uma para especificar o estado atual da tela, seja **loading**, **success** ou **error**, já que na teoria essa tela iria buscar informações de algum dado remoto e isso demoraria um pouco para ser exibido, podendo também ocasionar em erro. Mas para fins de simplificação, não teremos nada disso. Como também não teremos nenhum evento muito importante e esses dados não vão mudar, não faremos uma **TrackingUiEvent** aqui.

## Conclusão

Chegamos ao fim do tópico sobre telas e **ViewModels**. No próximo tópico dessa seção, veremos finalmente a navegação entre essas duas telas, onde a **HomeScreen** irá passar os dados **code** e **cep** para a **TrackingScreen**.