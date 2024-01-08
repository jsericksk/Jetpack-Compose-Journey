# Theme

Essa seção é dedicada a entender um pouco sobre como funciona e como modificar o tema dos nossos apps. Como mencionado na introdução, esse conteúdo utiliza o **Material Design 3**, que é a evolução do **Material Design**. Ao criar um projeto Compose nas versões mais recentes do Android Studio, o tema já inclui a dependência do Material Design 3.

Normalmente, as configurações de tema ficam no pacote **theme** após o projeto ser criado, onde o ***Theme.kt*** possui a implementação principal do tema.

## Criando um tema

Você pode escolher as cores que preferir para o tema. Vou utilizar o [Material Theme Builder](https://m3.material.io/theme-builder), que é um site bastante útil para criar nosso tema. Após personalizar as cores, você pode exportar o tema em um formato .zip para diversas plataformas e sistemas, como Views ou Jetpack Compose. Para Compose, ele exporta 2 arquivos principais no pacote theme: **Color.kt** e **Theme.kt**, contendo as cores e configurações do tema, respectivamente.

## Theme

O arquivo **Theme.kt** geralmente contém a função Composable principal do tema, contendo a chamada para a ```MaterialTheme```. Veja o pequeno exemplo:

```kotlin
private val LightColors = lightColorScheme(
    primary = md_theme_light_primary,
    onPrimary = md_theme_light_onPrimary,
    // outras cores
)

private val DarkColors = darkColorScheme(
    primary = md_theme_dark_primary,
    onPrimary = md_theme_dark_onPrimary,
    // outras cores
)

@Composable
fun MyAppTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    // Dynamic color is available on Android 12+
    dynamicColor: Boolean = true,
    content: @Composable () -> Unit
) {
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
        }
        darkTheme -> DarkColors
        else -> LightColors
    }

    MaterialTheme(
        colorScheme = colorScheme,
        typography = Typography,
        content = content
    )
}
```

O código padrão do Compose ao criar um novo projeto já vem com a configuração de [cores dinâmicas](https://developer.android.com/jetpack/compose/designsystems/material3#dynamic_color_schemes), como pode ver na **MyAppTheme**. A partir do Android 12, o tema usará cores dinâmicas se estiver habilitado. Ele também vem com a função ```isSystemInDarkTheme()```, que como o próprio nome já diz, checa se o dispositivo está usando o tema dark.

Além da tipografia, você também pode definir shapes personalizados. Por exemplo, criando um novo arquivo **Shape.kt** no pacote **theme** com o código:

```kotlin
val shapes = Shapes(
    extraSmall = RoundedCornerShape(4.dp),
    small = RoundedCornerShape(8.dp),
    medium = RoundedCornerShape(12.dp),
    large = RoundedCornerShape(16.dp),
    extraLarge = RoundedCornerShape(24.dp)
)
```

Agora basta alterar nossa **MyAppTheme** para incluir os shapes personalizados:

```kotlin
@Composable
fun MyAppTheme(
   ...
) {
    ...
    MaterialTheme(
        colorScheme = colorScheme,
        typography = Typography,
        shapes = shapes,
        content = content
    )
}
```

## Utilizando propriedades do tema

Após definir as configurações do tema, você pode usar suas propriedades através da ```MaterialTheme```. Por exemplo:

```kotlin
Text(
    text = "Material Design 3",
    color = MaterialTheme.colorScheme.primary,
    style = MaterialTheme.typography.titleLarge
)

Card(
    shape = MaterialTheme.shapes.medium
) {
    ...
}
```

## Conclusão

Não há muito mais para falar sobre temas. Você pode ler a documentação e conteúdos auxiliares se quiser saber mais.

## :link: Conteúdos auxiliares:
- [Material Design 3 in Compose (documentação)](https://developer.android.com/jetpack/compose/designsystems/material3)
-  [Theming in Compose with Material 3 (codelab)](https://developer.android.com/codelabs/jetpack-compose-theming)