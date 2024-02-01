# Navegação

Já criamos as duas telas principais no tópico anterior, então agora veremos uma forma de navegar entre elas.

## Navigation Component

O [**Navigation Component**](https://developer.android.com/guide/navigation) é bem conhecido no sistema de Views e também possui uma versão para Compose. Por ser o componente oficial de navegação, vamos utilizá-lo. É importante saber que existem diversas alternativas para atingir essa função de navegação, como por exemplo as bibliotecas [Voyager](https://github.com/adrielcafe/voyager) e [Decompose](https://github.com/arkivanov/Decompose), que inclusive oferecem alguns recursos a mais que o **Navigation Component** não possui.

## Organizando

Seguindo o que já vinhamos fazendo, vamos criar um novo pacote chamado **navigation**. Mais uma vez, essa organização é opcional e você pode escolher os nomes que preferir. Nesse pacote, crie dois novos arquivos, chamados **AppNavHost** e **Screen**. Veja o exemplo abaixo:

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
fun AppNavHost() {
    val navController = rememberNavController()
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

- Nosso **AppNavHost** faz uso da função **NavHost**, que possui como argumentos obrigatórios um **navController** e um **startDestination**. É através do **NavHostController** criado com **rememberNavController()** que podemos navegar de uma tela à outra, por exemplo. Já o **startDestination**, como o próprio nome diz, é o destino inicial que será usado por esse **NavHost**, que no nosso caso é a **HomeScreen**.
- Dentro do **NavHost**, podemos utilizar a função **composable()**, responsável por definir a nossa tela. É preciso especificar uma rota, o que fazemos definindo com a rota da **HomeScreen**. Ela também possui vários outros parâmetros, como **arguments** e algumas opções de animação, que vamos ver mais a frente.
- Como **content** da **composable()**, usamos a nossa **HomeScreen** de fato. Quando **onNavigateToTracking** for acionada, navegamos da **HomeScreen** para a **TrackingScreen**. Para isso utilizamos a função **navigate()** do **NavHostController** que criamos inicialmente.

Agora que já vimos os principais pontos, vamos finalizar o **AppNavHost** com a **composable()** da **TrackingScreen**:

```kotlin
@Composable
fun AppNavHost() {
    val navController = rememberNavController()
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
                    AppNavHost()
                }
            }
        }
    }
}
```

E é isso! O app já deve se comportar devidamente com a navegação entre as duas telas, como pode ver na imagem abaixo.

<img src="../navigation/img-01.gif" alt="Navigation" width="50%" height="20%"/>