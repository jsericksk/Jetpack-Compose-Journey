# Navigation Drawer

Navigation Drawer é um componente bem conhecido e utilizado em apps. Na versão de componentes do Material Design 2 no Compose, o **Navigation Drawer** era parte do **Scaffold**. Nos componentes do M3, ele se tornou independente.

Vamos criar uma implementação básica dele e ver como fica. Primeiro, vamos criar uma **sealed class** que servirá para agrupar os itens do nosso Navigation Drawer.

```kotlin
sealed class NavDrawerItem(
    @DrawableRes val iconResId: Int,
    @StringRes val titleResId: Int,
) {
    data object Home : NavDrawerItem(R.drawable.baseline_home_24, R.string.home)
    data object PrivacyPolicy : NavDrawerItem(R.drawable.baseline_privacy_tip_24, R.string.privacy_policy)
    data object SavedFiles : NavDrawerItem(R.drawable.baseline_save_24, R.string.saved_files)
    data object ShareApp : NavDrawerItem(R.drawable.baseline_share_24, R.string.share_app)
}
```

Agora vamos criar o Navigation Drawer em si fazendo uso da ***ModalNavigationDrawer***.

```kotlin
@Composable
fun MyNavigationDrawer(
    drawerState: DrawerState,
    modifier: Modifier = Modifier,
    content: @Composable () -> Unit
) {
    val coroutineScope = rememberCoroutineScope()
    val items = listOf(
        NavDrawerItem.Home,
        NavDrawerItem.PrivacyPolicy,
        NavDrawerItem.SavedFiles,
        NavDrawerItem.ShareApp,
    )
    ModalNavigationDrawer(
        drawerState = drawerState,
        drawerContent = {
            ModalDrawerSheet {
                Spacer(Modifier.height(22.dp))
                Text(
                    text = stringResource(id = R.string.app_name),
                    fontWeight = FontWeight.Bold,
                    fontSize = 16.sp,
                    modifier = Modifier.padding(start = 30.dp)
                )
                Spacer(Modifier.height(22.dp))
                items.forEach { item ->
                    NavigationDrawerItem(
                        icon = {
                            Icon(
                                painter = painterResource(id = item.iconResId),
                                contentDescription = stringResource(id = item.titleResId)
                            )
                        },
                        label = {
                            Text(
                                text = stringResource(id = item.titleResId)
                            )
                        },
                        selected = (item == NavDrawerItem.Home),
                        onClick = {
                            coroutineScope.launch { drawerState.close() }
                            when (item) {
                                is NavDrawerItem.Home -> {}
                                is NavDrawerItem.PrivacyPolicy -> {}
                                is NavDrawerItem.SavedFiles -> {}
                                is NavDrawerItem.ShareApp -> {}
                            }
                        },
                        modifier = modifier.padding(NavigationDrawerItemDefaults.ItemPadding)
                    )
                }
            }
        },
        modifier = modifier,
        content = content
    )
}
```

Com isso, já temos o nosso Navigation Drawer sem muitos segredos. O destaque fica para a ***ModalDrawerSheet*** e ***NavigationDrawerItem***, onde temos algumas opções de personalização a mais. Poderíamos usar qualquer Composable no **drawerContent** da ***ModalNavigationDrawer***, afinal ela é apenas o container, mas para fins de conveniência e como o Compose oferece esses componentes, usamos ***ModalDrawerSheet*** e ***NavigationDrawerItem*** que dão vida ao Navigation Drawer.

Por fim, podemos usar essa função a nível de tela, utilizando um **Scaffold** como **content**. Vamos reutilizar parte do código que vimos na seção sobre Scaffold, adicionando um **navigationIcon** na **TopAppBar** para exibir o ícone comumente usado em um Navigation Drawer. ```drawerState.open()``` é uma **suspension function**, por isso temos que utilizar **coroutineScope**.

```kotlin
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun HomeScreen() {
    val coroutineScope = rememberCoroutineScope()
    val drawerState = rememberDrawerState(DrawerValue.Closed)
    MyNavigationDrawer(drawerState = drawerState) {
        Scaffold(
            topBar = {
                TopAppBar(
                    colors = TopAppBarDefaults.topAppBarColors(
                        containerColor = MaterialTheme.colorScheme.primaryContainer
                    ),
                    title = {
                        Text("TopAppBar")
                    },
                    navigationIcon = {
                        IconButton(
                            onClick = {
                                coroutineScope.launch {
                                    drawerState.open()
                                }
                            }
                        ) {
                            Icon(
                                imageVector = Icons.Default.Menu,
                                contentDescription = "Menu"
                            )
                        }
                    }
                )
            },
            floatingActionButton = {
                FloatingActionButton(onClick = {}) {
                    Icon(
                        imageVector = Icons.Default.Add,
                        contentDescription = "Add"
                    )
                }
            }
        ) { paddingValues ->
            Column(
                verticalArrangement = Arrangement.Center,
                horizontalAlignment = Alignment.CenterHorizontally,
                modifier = Modifier
                    .padding(paddingValues)
                    .fillMaxSize()
            ) {
                Text(
                    text = "Conteúdo da tela",
                    fontSize = 24.sp
                )
            }
        }
    }
}
```

<img src="../navigationdrawer/img-01.gif" alt="Navigation Drawer" width="50%" height="20%"/>

## Opções de controle e personalização

Por padrão, o **Navigatrion Drawer** abre com o gesto de arrastar. Você pode desativar isso definindo ```gesturesEnabled = false``` na **ModalNavigationDrawer**.

Tanto o ***ModalDrawerSheet*** quanto ***NavigationDrawerItem*** possuem opções de personalização que você pode usar para mudar a aparência dos itens, como shape e cores.

## :link: Conteúdos auxiliares:
- [Navigation drawer (documentação)](https://developer.android.com/jetpack/compose/components/drawer)
- [Navigation drawer (Material Design 3)](https://m3.material.io/components/navigation-drawer)
- [Material 3 Navigation Drawer with Android Jetpack Compose (artigo)](https://tomas-repcik.medium.com/material-3-navigation-drawer-with-android-jetpack-compose-eda9285f9f4c)