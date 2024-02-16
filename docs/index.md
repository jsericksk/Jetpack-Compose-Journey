<p align="center">
  <img src="https://raw.githubusercontent.com/jsericksk/Jetpack-Compose-Journey/main/images/compose-journey.png" width="150">
</p>
<h1 align="center">
  <a href="https://github.com/jsericksk/Jetpack-Compose-Journey">Jetpack Compose Journey</a>
</h1>

Esse conteúdo foi planejado para ser algo direto ao ponto para iniciantes, mas em alguns momentos você poderá ver diversas informações importantes que podem tornar alguns tópicos relativamente extensos. Cada seção abordará temas específicos, sendo como espécie de capítulos, e terá referências tanto à documentação quanto a conteúdos que eu achar interessantes.

As seções estão organizadas numa ordem cronológica do que acredito ser interessante aprender primeiro, mas essa ordem pode mudar ou não ter grande eficiência. Se você não for um completo iniciante no Compose, não precisa se prender a ela, caso contrário, é recomendável segui-la.

Os códigos de exemplos usados em cada seção podem ser encontrados no repositório [Compose Journey Samples
](https://github.com/jsericksk/Compose-Journey-Samples).

## Requisitos mínimos

É necessário que você conheça a linguagem [Kotlin](https://kotlinlang.org/docs/home.html) e, de preferência, tenha familiaridade com Android nativo. Todas as seções cobrem apenas conteúdos relacionados ao Jetpack Compose. Conteúdos específicos do Android, como **Activities** e **Services**, por exemplo, não são explorados aqui.

Para fins de compatibilidade e evitar problemas, é recomendável que você esteja usando:

- **Android Studio Hedgehog - 2023.1.1** ou superior.
- [Compose BOM 2023.10.01](https://developer.android.com/jetpack/compose/bom/bom-mapping) ou superior.
- **Kotlin** 1.9.20 ou superior.
- **kotlinCompilerExtensionVersion** 1.5.6 ou superior.
- **Material Design 3**.

Todo o conteúdo está sendo criado com base nos componentes do **Material Design 3**. Há várias diferenças em alguns componentes do MD2 e MD3 no Compose, portanto, é recomendável seguir o MD3 para evitar problemas com o conteúdo. Ao criar um projeto Compose no Android Studio Hedgehog, o MD3 é incluído por padrão.

- Você pode usar o [Material Theme Builder](https://m3.material.io/theme-builder) para criar seu tema rapidamente.

Vamos utilizar as dependências que vêm configuradas ao criar um projeto Compose, que geralmente são:

```gradle
dependencies {
    implementation("androidx.core:core-ktx:$version")
    implementation("androidx.lifecycle:lifecycle-runtime-ktx:$version")
    implementation("androidx.activity:activity-compose:$version")

    implementation(platform("androidx.compose:compose-bom:$version"))
    implementation("androidx.compose.ui:ui")
    implementation("androidx.compose.ui:ui-graphics")
    implementation("androidx.compose.ui:ui-tooling-preview")
    implementation("androidx.compose.material3:material3")

    testImplementation("junit:junit:$version")
    androidTestImplementation("androidx.test.ext:junit:$version")
    androidTestImplementation("androidx.test.espresso:espresso-core:$version")
    androidTestImplementation(platform("androidx.compose:compose-bom:$version"))
    androidTestImplementation("androidx.compose.ui:ui-test-junit4")
    debugImplementation("androidx.compose.ui:ui-tooling")
    debugImplementation("androidx.compose.ui:ui-test-manifest")
}
```

As seções que precisarem de novas dependências terão essa informação inclusa.

## Não esqueça a documentação oficial

O foco desse conteúdo não é substituir a documentação oficial nem você deve evitar de lê-la. A maioria do conteúdo terá como fonte de apoio a própria documentação. Em muitas seções você lerá coisas que foram retiradas dela, às vezes usando outras palavras, tentando tornar mais simples ou mais explicativo. Tentarei melhorar apenas o que eu achar necessário.