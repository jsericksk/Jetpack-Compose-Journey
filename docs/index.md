# Introdução

Esse conteúdo foi planejado para ser algo direto ao ponto para iniciantes, mas em alguns momentos você poderá ver diversas informações importantes que podem tornar alguns tópicos relativamente extensos. Cada seção abordará temas específicos, sendo como espécie de capítulos, e terá referências tanto à documentação quanto a conteúdos que eu achar interessantes.

As seções estão numa ordem cronológica do que acredito ser interessante aprender primeiro, mas essa ordem pode mudar ou não ter grande eficiência. Se você não for um completo iniciante no Compose, não precisa se prender a essa ordem, caso contrário, é recomendável segui-la.

## Requisitos mínimos

Para fins de compatibilidade e evitar problemas, é recomendável que você esteja usando:

- **Android Studio Hedgehog - 2023.1.1** ou superior.
- [Compose BOM 2023.10.01](https://developer.android.com/jetpack/compose/bom/bom-mapping) ou superior.
- Kotlin 1.9.20 ou superior.
- kotlinCompilerExtensionVersion 1.5.6 ou superior.
- Material Design 3.

Todo o conteúdo está sendo criado com base nos componentes do Material Design 3. Há várias diferenças em alguns componentes do MD2 e MD3 no Compose, portanto, é recomendável seguir o MD3 para evitar problemas com o conteúdo. Ao criar um projeto Compose no Android Studio Hedgehog, o MD3 é incluído por padrão.

- Você pode usar o [Material Theme Builder](https://m3.material.io/theme-builder) para criar seu tema rapidamente.

Vamos utilizar as dependências que vêm configuradas ao criar um projeto Compose, que atualmente na versão do  Android Studio Hedgehog são:

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

O foco desse conteúdo não é substituir a documentação oficial nem você deve evitar de lê-la, é claro. Em muitas seções você lerá coisas que foram retiradas da própria documentação, mas usando outras palavras, tentando tornar mais simples ou mais explicativo. Tentarei melhorar apenas o que eu achar necessário.