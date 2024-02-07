# Conhecimentos básicos iniciais

Essa seção descreve algumas coisas básicas importantes para você entender e ficar um pouco mais familiarizado com o Compose. Ao longo de outras seções, muito do que está sendo dito aqui será abordado de uma forma mais abrangente.

## Funções @Composable

Como você já deve ter notado ao criar seu primeiro projeto Compose, a **MainActivity** vem com uma função **Greeting()** com a anotação **@Composable**. Essa anotação serve para dizer ao compilador do Compose que essa função converterá dados em UI, isso é, irá transformar nosso código escrito em Kotlin (comumente com outras funções @Composable) em uma bela tela ou componente. Nesse caso específico, ela está usando apenas a função **Text()**, que é também uma @Composable e um componente padrão do Material 3 para textos.

Algumas notas importantes:

- Funções Composable que emitem UI não precisam retornar nada, pois elas descrevem o estado da tela desejado em vez de construir widgets de UI.
- A [convenção](https://github.com/androidx/androidx/blob/androidx-main/compose/docs/compose-api-guidelines.md#naming-unit-composable-functions-as-entities) de nomenclatura para funções Composable que não retornam nada (Unit) é a **PascalCase** e devem ser substantivos, como é o caso de **Greeting()**. Já funções Composable que retornam algo devem estar no formato **camelCase**, como **stringResource()**.
- São [idempotentes](https://en.wikipedia.org/wiki/Idempotence#Computer_science_meaning) e livres de efeitos colaterais.

## Fases do Jetpack Compose

O Compose tem três fases principais:

1. **Composição**: ***Qual*** UI mostrar. O Compose executa funções Composable e cria uma descrição da sua IU.
2. **Layout**: ***Onde*** colocar a UI. Esta fase consiste em duas etapas: ***medição*** e ***posicionamento***. Os elementos de layout medem e posicionam a si mesmos e a quaisquer elementos filhos em coordenadas 2D, para cada nó na árvore de layout.
3. **Desenho**: ***Como*** é renderizado. Os elementos da UI são desenhados em um **Canvas**, geralmente uma tela de dispositivo.

![Fases do Jetpack Compose](basic/img-01.png)

A ordem dessas fases é geralmente a mesma, permitindo que os dados fluam em uma direção, da **composição** ao **layout** e ao **desenho**, para produzir um quadro (também conhecido como **fluxo de dados unidirecional**). [**BoxWithConstraints**](https://developer.android.com/jetpack/compose/layouts/basics#constraints) e [**LazyColumn/LazyRow**](../lists) são exceções notáveis, onde a composição de seus filhos depende da fase de layout dos pais.

Você pode presumir com segurança que essas três fases acontecem virtualmente para cada quadro, mas, por uma questão de desempenho, o Compose evita repetir o trabalho que calcularia os mesmos resultados a partir das mesmas entradas em todas essas fases. O Compose ignora a execução de uma função Composable se puder reutilizar um resultado anterior, e a UI do Compose não reorganiza ou redesenha a árvore inteira se não for necessário. Ele executa apenas a quantidade mínima de trabalho necessária para atualizar a IU. Essa otimização é possível porque o Compose rastreia leituras de estado nas diferentes fases.

As informações acima foram retiradas diretamente da [documentação sobre esse tópico](https://developer.android.com/jetpack/compose/phases). Leia mais no link referenciado para ver mais detalhadamente sobre as fases do Compose, se desejar.

## Recomposição

Diferente do sistema de Views que usa o paradigma imperativo, o Compose utiliza o paradigma declarativo. Em um modelo de UI imperativo, para alterar um widget, você chama um setter no widget para alterar seu estado interno. Por exemplo: ```textView.setText("Texto do TextView")```. O Compose funciona de forma diferente. Ao invés de setarmos o valor do texto diretamente no componente **Text()** (o equivalente ao **TextView** aqui), o nosso componente **Text()** recebe o texto (**estado**) e sempre que esse texto for modificado (por conta de algum **evento**), **Text()** é chamada novamente com esse novo valor. Esse processo que ocorre quando um estado muda e o Compose atualiza a UI é chamado de **recomposição**.

Compose trabalha com **estados e eventos**. Um estado é qualquer valor que pode mudar com o tempo. Já um evento é tudo aquilo que acontece que pode modificar a UI, ou seja, os estados. Por exemplo, um clique em algum componente.

Portanto, recomposição é o processo de chamar suas funções Composable novamente quando as entradas mudam. Esse processo é feito pelo Compose. Quando o Compose recompõe com base em novas entradas, ele chama apenas as funções ou lambdas que podem ter sido alteradas e ignora o restante. Ao ignorar todas as funções ou lambdas que não possuem parâmetros alterados, o Compose pode recompor com eficiência.

#### Não espere uma ordem na chamada de funções Composable

Se uma função Composable contém chamadas para outras funções Composable, essas funções poderão ser executadas em qualquer ordem. O Compose pode reconhecer que alguns elementos da UI têm maior prioridade do que outros e desenhá-los primeiro. Por exemplo:

```kotlin
@Composable
fun MyScreen() {
    Column {
        TopComponent()
        ItemList()
        BottomComponent()
    }
}
```

Não espere que TopComponent() será chamada primeiro, ItemList() depois e, por fim, BottomComponent(). Isso nem sempre pode ser verdade e essas funções devem ser independentes uma da outra.

## Compose utiliza muito do poder do Kotlin

Como o Compose é desenvolvido em torno do Kotlin, ele utiliza muito de seu poder e facilidades. Uma regalia praticamente onipresente graças a isso são os [argumentos padrão](https://kotlinlang.org/docs/functions.html#default-arguments) e [argumentos nomeados](https://kotlinlang.org/docs/functions.html#named-arguments) nas funções @Composable. Pegando a função Greeting() de exemplo, veja que temos 2 parâmetros: **name** e **modifier**. No entanto, ao chamá-la, não usamos o modifier, apenas o name, pois já passamos um Modifier como valor padrão. Isso só é possível graças a essas características do Kotlin citadas anteriormente.

Você pode não ter notado, mas a função **Text()** segue essa mesma característica. Ela possui vários outros parâmetros, como **color**, **fontSize**, **textAlign** etc., mas como eles já estão definidos com valores padrão, não precisamos passar nenhum valor, com exceção do texto em si, que é obrigatório (modifier é usado apenas como exemplo, mas ele também não é obrigatório).

Você verá isso se repetindo em praticamente todos os componentes do Compose, inclusive usará bastante disso, já que como é de se imaginar, facilita muito a componentização.

#### Nomear argumentos é uma boa prática

É recomendável na maioria das vezes nomear seus argumentos. Por exemplo:

```kotlin
@Composable
fun Profile(
    name: String,
    age: Int,
    online: Boolean
) {...}
```

Ao invés disso: ```Profile("John", 20, true)```

Faça isso: ```Profile(name = "John", age = 20, online = true)```

Essa não é uma boa prática exclusiva no contexto do Compose, mas no código Kotlin no geral, porém, como normalmente as funções Composable têm diversos parâmetros, essa prática acaba sendo vantajosa. Obviamente isso nem sempre é necessário, quando a função tem apenas 1 ou 2 parâmetros, por exemplo, mas na maioria das vezes é uma boa prática para fins de legibilidade.

## Funções Composable com sua devida visibilidade

Tanto na documentação oficial quanto nesse conteúdo você verá exemplos usando funções sem nenhum modificador de acesso explícito, o que significa funções públicas por padrão no Kotlin. Como criamos muitas funções Composable que não estão presentes dentro de uma classe ou outro escopo específico, ao deixarmos uma função public, podemos usá-la em qualquer parte do projeto naquele módulo. Isso significa que se tivermos uma função **ImageList()** no arquivo **HomeScreen.kt**, por exemplo, poderemos chamá-la em qualquer parte do projeto naquele módulo. Mas se **ImageList()** só faz sentido para a **HomeScreen**, então é importante que você deixe-a privada para evitar esse problema.

Sua função só será usada naquele arquivo específico? Não use:

```kotlin
@Composable
fun ImageList() {...}
```

Use:

```kotlin
@Composable
private fun ImageList() {...}
```


## :link: Conteúdos auxiliares:
- [Thinking in Compose (documentação)](https://developer.android.com/jetpack/compose/mental-model)
- [Thinking in Compose (artigo)](https://medium.com/androiddevelopers/thinking-in-compose-c4ef150bb7cf)