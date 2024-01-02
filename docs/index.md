# Introdução

Esse conteúdo foi planejado para ser algo direto ao ponto para iniciantes, mas em alguns momentos você poderá ver diversas informações importantes que podem tornar alguns tópicos relativamente extensos. Cada seção abordará temas específicos, sendo como uma espécie de capítulos, e terá referências tanto à documentação quanto a artigos que eu achar interessantes.

As seções estão numa ordem cronológica do que acredito ser interessante aprender primeiro, mas essa ordem pode mudar ou não ser completamente eficiente. Se você não for um completo iniciante no Compose, não precisa se prender a essa ordem, caso contrário, é recomendável segui-la.

## Requisitos mínimos

Para fins de compatibilidade e evitar problemas, é recomendável que você esteja usando:

- **Android Studio Hedgehog - 2023.1.1** ou alguma versão mais recente.
- [Compose BOM 2023.10.01](https://developer.android.com/jetpack/compose/bom/bom-mapping) ou alguma versão mais recente.
- Kotlin 1.9.20 ou superior.
- kotlinCompilerExtensionVersion 1.5.6 ou superior.
- Material Design 3.

Todo o conteúdo está sendo criado com base nos componentes do Material Design 3. Há várias diferenças em alguns componentes do MD2 e MD3 no Compose, portanto, é recomendável seguir o MD3 para evitar problemas com o conteúdo. Ao criar um projeto Compose no Android Studio Hedgehog, o MD3 é incluído por padrão.

- Você pode usar o [Material Theme Builder](https://m3.material.io/theme-builder) para criar seu tema rapidamente.

Vamos utilizar as dependências padrão que vêm configuradas ao criar um projeto Compose, que atualmente na versão do  Android Studio Hedgehog são:

```gradle
dependencies {
    implementation("androidx.core:core-ktx:1.12.0")
    implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.6.2")
    implementation("androidx.activity:activity-compose:1.8.2")

    implementation(platform("androidx.compose:compose-bom:2023.10.01"))
    implementation("androidx.compose.ui:ui")
    implementation("androidx.compose.ui:ui-graphics")
    implementation("androidx.compose.ui:ui-tooling-preview")
    implementation("androidx.compose.material3:material3")

    testImplementation("junit:junit:4.13.2")
    androidTestImplementation("androidx.test.ext:junit:1.1.5")
    androidTestImplementation("androidx.test.espresso:espresso-core:3.5.1")
    androidTestImplementation(platform("androidx.compose:compose-bom:2023.10.01"))
    androidTestImplementation("androidx.compose.ui:ui-test-junit4")
    debugImplementation("androidx.compose.ui:ui-tooling")
    debugImplementation("androidx.compose.ui:ui-test-manifest")
}
```

Se alguma nova dependência for necessária para todas as seções, essa seção será atualizada. Se apenas uma seção específica precisar de uma nova dependência, como uma seção de navegação que utiliza o [Navigation Component](https://developer.android.com/guide/navigation/navigation-getting-started), apenas a seção específica terá a dependência.