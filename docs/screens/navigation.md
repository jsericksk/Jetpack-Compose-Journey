# Navegação

Já criamos as duas telas principais no tópico anterior, então agora veremos uma forma de navegar entre elas.

## Navigation Component

O [**Navigation Component**](https://developer.android.com/guide/navigation) é bem conhecido no sistema de Views e também possui uma versão para Compose. Por ser o componente oficial de navegação, vamos utilizá-lo. É importante saber que existem diversas alternativas para atingir essa função, como por exemplo as bibliotecas [Voyager](https://github.com/adrielcafe/voyager) e [Decompose](https://github.com/arkivanov/Decompose), que inclusive oferecem alguns recursos a mais que o **Navigation Component** não possui.

Antes de tudo, adicione a dependência do **Navigation Component** (cheque a versão atual na [documentação](https://developer.android.com/guide/navigation)):

```gradle
implementation("androidx.navigation:navigation-compose:$version")
```

Existem 3 conceitos principais no **Navigation Component**:

- **Navigation Graph**: Uma estrutura de dados que define todos os destinos de navegação no aplicativo e como eles se conectam. No caso desse projeto de exemplo, o arquivo **AppNavHost** que vamos criar mais a frente será nosso gráfico de navegação.
- **NavHost**: O componente principal onde faz referência a um **NavController**, assim como define um **destino inicial**. Ele também é responsável por conter as **composable()** (extension function de **NavGraphBuilder**) que são usadas para compor as telas do app. Cada tela é um **destino** e possui uma **rota** exclusiva do tipo String.
- **NavController:** O coordenador central para gerenciar a navegação entre destinos. O **NavController** oferece métodos para navegar entre destinos, lidar com deep links, gerenciar backstack e muito mais.

## Organizando

Seguindo o que já vínhamos fazendo, vamos criar um novo pacote chamado **navigation**. Mais uma vez, essa organização é opcional e você pode escolher os nomes que preferir. Nesse pacote, crie dois novos arquivos, chamados **AppNavHost** e **Screen**. Veja o exemplo abaixo:

```
...
navigation:
    AppNavHost.kt
    Screen.kt
screens:
    home:
        HomeScreen.kt
        ...
    tracking:
        TrackingScreen.kt
        ...
...
```

## Criando a classe Screen

O que vamos ter na **Screen** é nada mais do que as nossas telas, organizando com uma **sealed class**. Veja o código dela abaixo:

```kotlin
const val TRACKING_CODE = "tracking_code"
const val TRACKING_CEP = "tracking_cep"

sealed class Screen(val route: String) {
    data object HomeScreen : Screen("home_screen")
    data object TrackingScreen : Screen(
        "tracking_screen/{$TRACKING_CODE}/{$TRACKING_CEP}"
    ) {
        fun routeWithArgs(code: String, cep: Int) = "tracking_screen/$code/$cep"
    }
}
```

Deixei as constantes no mesmo arquivo, mas você pode colocá-las em outro lugar se preferir.

Como você pode observar, a **Screen** possui uma propriedade chamada **route**. Ela será útil para identificar o nome da rota da tela no nosso **gráfico de navegação**. O **Navigation Component** funciona por meio de "rotas", onde cada tela possui uma rota e para navegar para uma outra tela, nós passamos a rota da mesma. Como a **TrackingScreen** é uma tela que vai receber alguns dados, ela é um pouco diferente, contendo também os possíveis argumentos. Também criamos nela  uma função auxiliar que cria uma rota com argumentos, que serão o **code** e **cep**.

## Criando o AppNavHost

Vamos então criar o **AppNavHost**, inicialmente apenas com a **HomeScreen** para comentarmos por partes:

```kotlin
@Composable
fun AppNavHost(navController: NavHostController) {
    NavHost(
        navController = navController,
        startDestination = Screen.HomeScreen.route
    ) {
        composable(route = Screen.HomeScreen.route) {
            HomeScreen(
                onNavigateToTracking = { code, cep ->
                    navController.navigate(
                        Screen.TrackingScreen.routeWithArgs(code, cep)
                    )
                }
            )
        }
    }
}
```

Vamos aos pontos importantes:

- Nosso **AppNavHost** faz uso da função **NavHost**, que possui como argumentos obrigatórios um **navController** e um **startDestination**. É através do **NavHostController** que podemos navegar de uma tela à outra, por exemplo. Já o **startDestination**, como o próprio nome diz, é o destino inicial que será usado por esse **NavHost**, que no nosso caso é a **HomeScreen**.
- Dentro do **NavHost**, podemos utilizar a função **composable()**, responsável por definir a nossa tela. É preciso especificar uma rota, o que fazemos definindo com a rota da **HomeScreen**. Ela também possui vários outros parâmetros, como **arguments** e algumas opções de animação, que vamos ver mais a frente.
- Como **content** da **composable()**, usamos a nossa **HomeScreen** de fato. Quando **onNavigateToTracking** for acionada, navegamos da **HomeScreen** para a **TrackingScreen**. Para isso utilizamos a função **navigate()** do **NavHostController**.

Agora que já vimos os principais pontos, vamos finalizar o **AppNavHost** com a **composable()** da **TrackingScreen**:

```kotlin
@Composable
fun AppNavHost(navController: NavHostController) {
    NavHost(
        navController = navController,
        startDestination = Screen.HomeScreen.route
    ) {
        composable(route = Screen.HomeScreen.route) {
            HomeScreen(
                onNavigateToTracking = { code, cep ->
                    navController.navigate(
                        Screen.TrackingScreen.routeWithArgs(code, cep)
                    )
                }
            )
        }

        composable(
            route = Screen.TrackingScreen.route,
            arguments = listOf(
                navArgument(name = TRACKING_CODE) {
                    type = NavType.StringType
                },
                navArgument(name = TRACKING_CEP) {
                    type = NavType.IntType
                }
            )
        ) { navBackStackEntry ->
            navBackStackEntry.arguments?.let { bundle ->
                TrackingScreen(
                    code = bundle.getString(TRACKING_CODE) ?: "",
                    cep = bundle.getInt(TRACKING_CEP),
                    onNavigateBack = { navController.popBackStack() }
                )
            }
        }
    }
}
```

Como a **TrackingScreen** é uma tela que vai receber argumentos, precisamos especificar isso através do parâmetro **arguments** da **composable()**. Passamos então uma lista de argumentos, onde cada um deles é um **navArgument**. Como **name**, utilizamos as constantes que criamos antes. Já como o **type**, especificamos qual é o tipo de argumento esperado, que pode ser uma String (**StringType**), um Boolean (**BoolType**), um Int (**IntType**) etc. Como estamos esperando uma String para o **code** e um Int para o **cep**, especificamos ambos.

Na chamada da **TrackingScreen**, temos mais alguns pontos de destaque. Para obter os argumentos, utilizamos o **NavBackStackEntry** disponível no lambda da **composable()**, usando **navBackStackEntry.arguments**. Como o **Bundle** retornado de **navBackStackEntry.arguments** pode ser nulo, fazemos uma pequena verificação para evitar um **NullPointerException**. No **onNavigateBack**, chamamos **navController.popBackStack()**, que irá retornar à tela anterior.

## Adicionando o AppNavHost na MainActivity

Como agora as nossas telas serão gerenciadas pelo **AppNavHost**, temos que modificar o conteúdo exibido no **setContent** do **onCreate()** da **MainActivity**. Veja o código:

```kotlin
class MainActivity : ComponentActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MyAppTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    val navController = rememberNavController()
                    AppNavHost(navController)
                }
            }
        }
    }
}
```

E é isso! O app já deve se comportar devidamente com a navegação entre as duas telas, como pode ver na imagem abaixo.

<img src="../navigation/img-01.gif" alt="Navigation" width="50%" height="20%"/>

## Usando SavedStateHandle

Atualmente estamos obtendo os argumentos passados da **HomeScreen** diretamente com o **NavBackStackEntry** no **AppNavHost**, mais especificamente na **composable()** da rota da **TrackingScreen**. No entanto, existe também uma outra maneira de obter esses argumentos através do módulo [**SavedStateHandle**](https://developer.android.com/topic/libraries/architecture/viewmodel/viewmodel-savedstate). Vamos fazer isso e ver como as coisas mudam um pouco.

#### Modificando o TrackingViewModel

Precisamos primeiro alterar o **TrackingViewModel** que já fizemos antes. Veja como ele ficará agora:

```kotlin
class TrackingViewModel(
    private val savedStateHandle: SavedStateHandle
) : ViewModel() {
    private val _uiState = MutableStateFlow(
        TrackingUiState(
            code = savedStateHandle.get<String>(TRACKING_CODE) ?: "",
            cep = savedStateHandle.get<Int>(TRACKING_CEP) ?: 0,
        )
    )
    val uiState: StateFlow<TrackingUiState> = _uiState.asStateFlow()
}
```

Foi adicionado a propriedade do **SavedStateHandle** e a função **getTrackingInfo()** foi removida. Como vimos na versão anterior, **getTrackingInfo()** só servia para inicializar o **code** e **cep**. Nessa nova versão, a **TrackingUiState** é inicializada diretamente com os valores do **SavedStateHandle** através da função **get()**, que espera uma **key** para buscar o argumento. Como ela pode retornar null se não houver nada encontrado com a **key** passada, adicionamos com um valor padrão.

O **SavedStateHandle** pode ser usado de outras formas, inclusive com **Flows**, porém não vamos ver essa abordagem aqui, pois não é o caso.

#### Modificando a TrackingScreen

Como agora vamos obter os argumentos diretamente do **ViewModel**, não precisamos mais que a **TrackingScreen** tenha os parâmetros **code** e **cep**. Além disso, a abordagem para instanciar o **TrackingViewModel** vai mudar, pois agora ele espera um argumento **SavedStateHandle**. Veja como ela fica agora:

```kotlin
@Composable
fun TrackingScreen(
    onNavigateBack: () -> Unit,
    trackingViewModel: TrackingViewModel = viewModel {
        val savedStateHandle = createSavedStateHandle()
        TrackingViewModel(savedStateHandle)
    }
) {
    val uiState by trackingViewModel.uiState.collectAsStateWithLifecycle()
    TrackingContent(
        uiState = uiState,
        onNavigateBack = onNavigateBack
    )
}
```

Note que para utilizar a função **viewModel** que possui um lambda para criar o **SavedStateHandle** através da função **createSavedStateHandle()**, você precisa da seguinte dependência (cheque a versão atual na [documentação](https://developer.android.com/jetpack/androidx/releases/lifecycle)):

```gradle
implementation("androidx.lifecycle:lifecycle-viewmodel-compose:$version")
```

Como o **Navigation Component** já inclui essa dependência por padrão, você não precisa adicioná-la explicitamente se não desejar.

#### Modificando o AppNavHost

Agora também precisamo fazer leves alterações no **AppNavHost**, removendo os argumentos da **TrackingScreen** que não existem mais:

```kotlin
@Composable
fun AppNavHost(navController: NavHostController) {
    NavHost(
        navController = navController,
        startDestination = Screen.HomeScreen.route
    ) {
        ...

        composable(
            route = Screen.TrackingScreen.route,
            arguments = listOf(
                navArgument(name = TRACKING_CODE) {
                    type = NavType.StringType
                },
                navArgument(name = TRACKING_CEP) {
                    type = NavType.IntType
                }
            )
        ) {
            TrackingScreen(onNavigateBack = { navController.popBackStack() })
        }
    }
}
```

## Adicionando animação ao navegar entre as telas

Se você olhar bem a imagem demonstrando a navegação do app, verá que não há nenhuma grande animação. A função **composable()** possui alguns parâmetros para modificar as animações de transição, que são: **enterTransition**, **exitTransition**, **popEnterTransition** e **popExitTransition**, onde podemos utilizar animações do tipo **EnterTransition** e **ExitTransition**.

Vamos ver um exemplo básico de quando essas animações são usadas. Digamos que a **HomeScreen** navegue para a **TrackingScreen**:

- **TrackingScreen** executa **enterTransition**.
- **HomeScreen** executa **exitTransition**.

Nós voltamos à tela anterior (**HomeScreen**), seja tocando no botão voltar ou no ícone de arrow back:

- **TrackingScreen** executa **popExitTransition**.
- **HomeScreen** executa **popEnterTransition**.

Agora que já sabemos como funciona, vamos aplicar uma pequena animação de slide ao abrir e fechar a **TrackingScreen**. Veja como a **composable()** da **TrackingScreen** fica agora:

```kotlin
private const val ANIMATION_DURATION = 700

@Composable
fun AppNavHost(navController: NavHostController) {
    NavHost(
        ...
    ) {
        ...
        composable(
            route = Screen.TrackingScreen.route,
            arguments = listOf(
                navArgument(name = TRACKING_CODE) {
                    type = NavType.StringType
                },
                navArgument(name = TRACKING_CEP) {
                    type = NavType.IntType
                }
            ),
            enterTransition = {
                slideIntoContainer(
                    towards = AnimatedContentTransitionScope.SlideDirection.Right,
                    animationSpec = tween(durationMillis = ANIMATION_DURATION)
                )
            },
            popExitTransition = {
                slideOutOfContainer(
                    towards = AnimatedContentTransitionScope.SlideDirection.Left,
                    animationSpec = tween(durationMillis = ANIMATION_DURATION)
                )
            }
        ) {
            TrackingScreen(onNavigateBack = { navController.popBackStack() })
        }
    }
}
```

Usamos as funções **slideIntoContainer()** e **slideOutOfContainer()** fornecidas pelo Compose para criar uma animação de slide com uma direção. Veja o resultado:

<img src="../navigation/img-02.gif" alt="Navigation com animação" width="50%" height="20%"/>

Modificando para **SlideDirection.Up** na **slideIntoContainer()** e **SlideDirection.Down** na **slideOutOfContainer()**:

<img src="../navigation/img-03.gif" alt="Navigation com animação" width="50%" height="20%"/>

E claro, também é possível utilizar animações personalizadas, bem como outros tipos de animação, mas para não se estender muito, vamos ficando por aqui nesse tópico.

## Resolvendo um problema comum de navegação

Existe um problema bem antigo e conhecido no **Navigation Component** da navegação abrindo múltiplas telas ou telas em branco ao voltar com **navController.popBackStack()**. No nosso código atual, se o usuário tocar o botão de "rastrear" na **HomeScreen** múltiplas vezes em um curto período de tempo, a **TrackingScreen** será aberta múltiplas vezes. Um outro problema similar acontece quando o ícone de arrow back é tocado múltiplas vezes na **TrackingScreen** e uma tela em branco é exibida. Isso também pode acontecer com dispositivos mais lentos, onde às vezes dão duplo clique acidental.

Você pode checar essa [issue](https://github.com/google/accompanist/issues/1320) e [essa](https://github.com/google/accompanist/issues/1408) para referência. Como antigamente só era possível navegar com animação através da **Navigation-Animation** da [**Accompanist**](https://github.com/google/accompanist), essas issues se encontram no repositório dela.

Veja a imagem abaixo para ter ideia do problema:

<img src="../navigation/img-04.gif" alt="Navigation com problemas" width="50%" height="20%"/>

Existem algumas formas de resolver isso. Uma delas seria desabilitar múltiplos cliques no componente em um curto período de tempo, mas isso pode ser complicado e não atinge diretamente o problema, já que se trata de um problema na navegação. Uma outra opção para esse caso é criar uma [função auxiliar](https://github.com/android/compose-samples/issues/456#issuecomment-886944725) que verifica se o status atual do **Lifecycle** é **Lifecycle.State.RESUMED**, pois caso contrário, significa que esse **NavBackStackEntry** já processou um evento de navegação. Veja o código abaixo:

```kotlin
private fun NavBackStackEntry.lifecycleIsResumed() =
        this.lifecycle.currentState == Lifecycle.State.RESUMED
```

Agora basta fazer uma verificação com essa função antes de navegar:

```kotlin
@Composable
fun AppNavHost(navController: NavHostController) {
    NavHost(
       ...
    ) {
        composable(
            route = Screen.HomeScreen.route,
            ...
        ) { navBackStackEntry ->
            HomeScreen(
                onNavigateToTracking = { code, cep ->
                    if (navBackStackEntry.lifecycleIsResumed()) {
                        navController.navigate(
                            Screen.TrackingScreen.routeWithArgs(code, cep)
                        )
                    }
                },
            )
        }

        composable(
            route = Screen.TrackingScreen.route,
            ...
        ) { navBackStackEntry ->
            TrackingScreen(
                onNavigateBack = {
                    if (navBackStackEntry.lifecycleIsResumed()) {
                        navController.popBackStack()
                    }
                }
            )
        }
    }
}
```

<img src="../navigation/img-05.gif" alt="Navigation sem problemas" width="50%" height="20%"/>


#### Problema de argumento com URL

Outro problema comum que pode ocorrer é um **java.lang.IllegalArgumentException** ao tentar passar URLs como argumento. Digamos que tentássemos passar uma URL **https://site.com** como **code** e **123456** como **cep** no código do projeto atual, teríamos a seguinte mensagem de erro crashando o app: ```java.lang.IllegalArgumentException: Navigation destination that matches request NavDeepLinkRequest{ uri=android-app://androidx.navigation/tracking_screen/https://site.com/123456 } cannot be found in the navigation graph ComposeNavGraph```.

O erro é bem claro: a rota não foi encontrada no gráfico de navegação. Isso não é um problema particular de URLs, mas da **barra (/)**. Como as rotas no **Navigation Component** funcionam num padrão de URLs, a barra acaba interferindo na rota esperada. Pensando num conceito de navegação web, é como se a nossa URL fosse **https://trackingscreen.com/CODE/CEP** e tentássemos navegar para **https://trackingscreen.com/CODE///CEP**, que claramente é uma URL inválida.

Para resolver isso, você pode codificar a URL antes de passá-la como argumento. Por exemplo:

```kotlin
@Composable
fun AppNavHost(navController: NavHostController) {
    NavHost(
        ...
    ) {
        composable(route = Screen.HomeScreen.route ) { navBackStackEntry ->
            HomeScreen(
                onNavigateToPage = { url ->
                    if (navBackStackEntry.lifecycleIsResumed()) {
                        val encodedUrl = URLEncoder.encode(url, "utf-8")
                        navController.navigate(
                            Screen.PageScreen.routeWithArgs(encodedUrl)
                        )
                    }
                }
            )
        }
        ...
    }
}
```

Não é necessário usar ```URLDecoder.decode()``` na **composable()** da **PageScreen**, pois o argumento será decodificado automaticamente na navegação.

## Conclusão

Há vários outros pontos que não foram abordados nessa seção sobre **Navigation Component**. Você pode ver alguns deles na documentação oficial, como [**argumentos opcionais**](https://developer.android.com/jetpack/compose/navigation#optional-args) e [**deep links**](https://developer.android.com/jetpack/compose/navigation#deeplinks).

## :link: Conteúdos auxiliares:
- [Navigation with Compose (documentação)](https://developer.android.com/jetpack/compose/navigation)
- [Jetpack Compose Navigation (codelab)](https://developer.android.com/codelabs/jetpack-compose-navigation)
- [Navigation in Jetpack compose. Full guide Beginner to Advanced (artigo)](https://medium.com/@KaushalVasava/navigation-in-jetpack-compose-full-guide-beginner-to-advanced-950c1133740)