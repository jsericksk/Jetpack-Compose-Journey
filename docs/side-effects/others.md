# Outros side effects

Além dos já citados anteriormente, que são os mais comumente utilizados, existem alguns outros side effects que podem ser usados em determinadas situações. Para evitar alongar a seção sobre esse tema, vamos ver apenas um pequeno resumo retirado da documentação sobre cada um dos demais.

## produceState

**produceState** lança uma corrotina com escopo para composição que pode enviar valores para um **State**. Use-o para converter o estado não-Compose em estado Compose, por exemplo, trazendo estado externo orientado por assinatura, como **Flow**, **LiveData** ou **RxJava** para a composição. [Leia mais na documentação](https://developer.android.com/jetpack/compose/side-effects#producestate).

## derivedStateOf

**derivedStateOf** converte um ou vários objetos de estado em outro estado. No Compose, a recomposição ocorre sempre que um objeto de estado observado ou uma entrada Composable muda. Um objeto de estado ou entrada pode mudar com mais frequência do que a UI realmente precisa atualizar, levando a uma recomposição desnecessária.

Você deve usar o **derivedStateOf** quando suas entradas para um Composable mudam com mais frequência do que o necessário para recompor. Isso geralmente ocorre quando algo muda com frequência, como uma posição de rolagem, mas o Composable só precisa reagir a isso quando ultrapassa um determinado limite. **derivedStateOf** cria um novo objeto de estado Compose e você pode observar que ele atualiza apenas o necessário. Dessa forma, ele atua de forma semelhante ao [distinctUntilChanged()](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/distinct-until-changed.html) do **Kotlin Flows**.

[Leia mais na documentação](https://developer.android.com/jetpack/compose/side-effects#derivedstateof) e veja também [esse artigo](https://medium.com/androiddevelopers/jetpack-compose-when-should-i-use-derivedstateof-63ce7954c11b) sobre quando usar **derivedStateOf**.

## snapshotFlow

**snapshotFlow** converte o estado do Compose (**State<T>**) em um cold Flow. **snapshotFlow** executa seu bloco quando coletado e emite o resultado dos objetos **State** lidos nele. Quando um dos objetos **State** lidos dentro do bloco **snapshotFlow** sofrer mutação, o Flow emitirá o novo valor para seu coletor se o novo valor não for igual ao valor emitido anteriormente (esse comportamento também é semelhante ao de **Flow.distinctUntilChanged()**). [Leia mais na documentação](https://developer.android.com/jetpack/compose/side-effects#snapshotFlow).

## Conclusão

Vimos nessa seção o que são e como trabalhar com **side effects**, além de ser notório que existem vários para diferentes situações. Apesar disso, pode ser comum não os utilizar muito, até porque, como vimos logo no início da seção, **o ideal é que funções Composable sejam livres de efeitos colaterais**.

Pode ser um pouco confuso entender tudo de início ou quando utilizar alguns deles, mas não se preocupe com isso. Com o tempo e com a prática, entendê-los será apenas um **efeito colateral**. :)

Como sempre, não se esqueça de ler mais sobre eles na documentação oficial.

## :link: Conteúdos auxiliares:
- [Side-effects in Compose (documentação)](https://developer.android.com/jetpack/compose/side-effects)
- [Advanced State and Side Effects in Jetpack Compose (codelab)](https://developer.android.com/codelabs/jetpack-compose-advanced-state-side-effects)