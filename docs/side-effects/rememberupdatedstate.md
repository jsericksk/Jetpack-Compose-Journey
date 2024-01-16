# rememberUpdatedState

**LaunchedEffect** reinicia quando o valor de uma das keys muda. No entanto, em algumas situações você pode querer capturar um valor em seu efeito que, se for alterado, você não deseja que o efeito seja reiniciado. Para isso, é necessário utilizar **rememberUpdatedState** para criar uma referência a este valor que possa ser capturado e atualizado. Essa abordagem é útil para efeitos que contêm operações de longa duração que podem ser caras ou proibitivas para recriar e reiniciar.

Lendo o enunciado acima, pode parecer muito confuso e a documentação oficial também não ajuda muito nesse caso. Então vamos criar um exemplo simplificado de download de arquivo. Novamente, o código demonstrado abaixo não é uma abordagem recomendada, mas serve de exemplo para esse tópico.

Primeiro, vamos criar uma **suspend function downloadFile()**:

```kotlin
private suspend fun downloadFile(onDownloadFinished: () -> Unit) {
    withContext(Dispatchers.IO) {
        delay(3000)
        withContext(Dispatchers.Main) {
            onDownloadFinished.invoke()
        }
    }
}
```

Ela não contém nada demais, possui apenas um delay de 3 segundos para simular um download de arquivo. Após o delay, invoca **onDownloadFinished()**, que será usado posteriormente para exibir um **Toast**.

Agora vamos criar a nossa função que utiliza um **LaunchedEffect**:

```kotlin
@Composable
private fun FileDownload(
    url: String,
    onFileNameObtained: (String) -> Unit,
    fileName: String,
    onDownloadFinished: () -> Unit
) {
    val context = LocalContext.current
    LaunchedEffect(Unit) {
        onFileNameObtained.invoke(URLUtil.guessFileName(url, null, null))
        downloadFile(
            onDownloadFinished = {
                Toast.makeText(
                    context,
                    "Arquivo \"$fileName\" baixado com sucesso!",
                    Toast.LENGTH_SHORT
                ).show()
                onDownloadFinished.invoke()
            }
        )
    }
}
```

Já vimos **LaunchedEffect** antes, então sabemos o que vai acontecer. Não queremos que ele seja reiniciado quando o valor de alguma key muda, por isso passamos **Unit** como key, informando que o nosso código no **LaunchedEffect** só será executado uma vez na composição. Assim que entramos no LaunchedEffect, usamos ```URLUtil.guessFileName()```, que é uma função que obtém o nome de arquivo a partir de uma URL, e então informamos ao chamador da **FileDownload()** que o nome de arquivo real foi obtido e ele faz o que desejar com isso. No nosso caso, isso servirá para atualizar **fileName** posteriormente, pois inicialmente ele possui um nome desconhecido.

Quando o download é concluído, uma mensagem **Toast** é exibida na tela.

Agora vamos criar a **DownloadScreen()** que faz uso da **FileDownload()**:

```kotlin
@Composable
private fun DownloadScreen() {
    val fileUrl = "https://site.com/files/video-123.mp4"
    val defaultFileName = "???"
    var fileName by remember { mutableStateOf(defaultFileName) }
    var isDownloadingFile by remember { mutableStateOf(false) }

    if (isDownloadingFile) {
        FileDownload(
            url = fileUrl,
            fileName = fileName,
            onFileNameObtained = { realFileName ->
                fileName = realFileName
            },
            onDownloadFinished = {
                isDownloadingFile = false
            }
        )
    }

    Column(
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center,
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        if (isDownloadingFile) {
            Column(horizontalAlignment = Alignment.CenterHorizontally) {
                LinearProgressIndicator(
                    strokeCap = StrokeCap.Round,
                    modifier = Modifier.fillMaxWidth()
                )
                Spacer(Modifier.height(14.dp))
                Text(
                    text = "Baixando arquivo... Aguarde.",
                    fontSize = 22.sp,
                    textAlign = TextAlign.Center
                )
            }
        } else {
            Text(
                text = "Nenhum arquivo sendo baixado no momento.",
                fontSize = 22.sp,
                textAlign = TextAlign.Center
            )
        }

        Spacer(Modifier.height(18.dp))

        Button(
            onClick = {
                fileName = defaultFileName
                isDownloadingFile = true
            },
            enabled = !isDownloadingFile
        ) {
            Text(
                text = "Baixar",
                fontSize = 18.sp
            )
        }
    }
}
```

É uma tela simples que simula um download de arquivo. Quando tocamos no botão de baixar, definimos **isDownloadingFile** como true, fazendo então nossa **FileDownload()** entrar na composição e iniciar o download. Como já vimos na **FileDownload()**, antes mesmo do download iniciar, **fileName** é atualizado de **"???"** para o nome real obtido a partir da URL, que no caso é **"video-123.mp4"**.

O resultado que esperamos é que um Toast com a mensagem **"Arquivo "video-123.mp4" baixado com sucesso!"** seja exibido na tela após 3 segundos, quando o download finalizar. Veja o resultado real:

<img src="../rememberupdatedstate/img-01.gif" alt="rememberUpdatedState" width="50%" height="30%"/>

Claramente não funcionou. Mas o que aconteceu? Vamos reler o que foi afirmado antes sobre o LaunchedEffect na nossa FileDownload(): **Não queremos que ele seja reiniciado quando o valor de alguma key muda, por isso passamos Unit como key, informando que o ___nosso código no LaunchedEffect só será executado uma vez na composição___.**

**fileName** até é atualizado com sucesso ao obter o nome real do arquivo e isso acontece bem antes do download finalizar, porém, o código no nosso LaunchedEffect não saberá disso, pois ele não será reiniciado. Significa que quando usamos o trecho a seguir no **onDownloadFinished**:

```kotlin
Toast.makeText(
    context,
    "Arquivo \"$fileName\" baixado com sucesso!",
    Toast.LENGTH_SHORT
).show()
```

A referência que temos de **fileName** ainda é a primeira antes de entrarmos no LaunchedEffect, ou seja, na primeira composição. Após entrarmos, mesmo que **onFileNameObtained()** seja invocado logo no início e atualize com sucesso o **fileName**, o que ainda temos é **???** como o valor de **fileName**.

Nesse tipo de situação, podemos utilizar **rememberUpdatedState**. Vamos fazer apenas uma pequena alteração na **FileDownload()**:

```kotlin
@Composable
private fun FileDownload(
    url: String,
    onFileNameObtained: (String) -> Unit,
    fileName: String,
    onDownloadFinished: () -> Unit
) {
    val context = LocalContext.current
    val realFileName by rememberUpdatedState(newValue = fileName)
    LaunchedEffect(Unit) {
        onFileNameObtained.invoke(URLUtil.guessFileName(url, null, null))
        downloadFile(
            onDownloadFinished = {
                Toast.makeText(
                    context,
                    "Arquivo \"$realFileName\" baixado com sucesso!",
                    Toast.LENGTH_SHORT
                ).show()
                onDownloadFinished.invoke()
            }
        )
    }
}
```

<img src="../rememberupdatedstate/img-02.gif" alt="rememberUpdatedState" width="50%" height="30%"/>

Agora temos o trecho mágico ```val realFileName by rememberUpdatedState(newValue = fileName)``` que usa **fileName** para se manter atualizada. Dessa forma, ao usarmos ela no Toast, obteremos o resultado desejado, pois **realFileName** agora terá o valor atualizado de **fileName**, mesmo sem precisarmos reiniciar o **LaunchedEffect** e consequentemente o download.

É claro que todo esse exemplo foi criado apenas com o intuito de ilustrar a funcionalidade do **rememberUpdatedState**. Para fins práticos, a função **FileDownload()** não precisaria existir e poderíamos ter o seguinte código na **DownloadScreen()**:

```kotlin
@Composable
private fun DownloadScreen() {
    ...

    if (isDownloadingFile) {
        val context = LocalContext.current
        LaunchedEffect(Unit) {
            fileName = URLUtil.guessFileName(fileUrl, null, null)
            downloadFile(
                onDownloadFinished = {
                    Toast.makeText(
                        context,
                        "Arquivo \"$fileName\" baixado com sucesso!",
                        Toast.LENGTH_SHORT
                    ).show()
                    isDownloadingFile = false
                }
            )
        }
    }

    ...
}
```

O resultado seria o mesmo que a nossa **FileDownload()** com **rememberUpdatedState**, pois agora estamos alterando o valor de **fileName** de fato antes de ser usado pelo Toast, dentro do mesmo escopo, o que não acontece no caso da FileDownload(), que delega essa função para seu chamador e o LaunchedEffect/Toast não tem mais ciência sobre a atualização.

## :link: Conteúdos auxiliares:
- [Side-effects in Compose (documentação)](https://developer.android.com/jetpack/compose/side-effects)
- [Jetpack Compose Side-Effects III — rememberUpdatedState (artigo)](https://proandroiddev.com/jetpack-compose-side-effects-iii-rememberupdatedstate-c8df7b90a01d)