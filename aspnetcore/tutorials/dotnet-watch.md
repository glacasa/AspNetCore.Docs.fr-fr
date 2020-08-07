---
title: Développer des applications ASP.NET Core à l’aide d’un observateur de fichiers
author: rick-anderson
description: Ce tutoriel montre comment installer et utiliser l’outil Observateur de fichiers (dotnet watch) de l’interface de ligne de commande .NET Core dans une application ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/dotnet-watch
ms.openlocfilehash: 5648ae9e75b48fa4749ed6bcc01b91559dbd2939
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913772"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Développer des applications ASP.NET Core à l’aide d’un observateur de fichiers

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch`est un outil qui exécute une commande [CLI .net Core](/dotnet/core/tools) lorsque les fichiers sources changent. Par exemple, un changement de fichier peut déclencher la compilation, l’exécution de tests ou le déploiement.

Ce tutoriel utilise une API web existante avec deux points de terminaison : un qui retourne une somme et un qui retourne un produit. La méthode du produit comporte un bogue, qui est résolu dans ce tutoriel.

Téléchargez l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample). Elle se compose de deux projets : *WebApp* (API web ASP.NET Core) et *WebAppTests* (API de tests unitaires pour le web).

Dans une interface de commande, accédez au dossier *WebApp*. Exécutez la commande suivante :

```dotnetcli
dotnet run
```

> [!NOTE]
> Vous pouvez utiliser `dotnet run --project <PROJECT>` pour spécifier un projet à exécuter. Par exemple, `dotnet run --project WebApp` à la racine de l’exemple d’application aura également pour effet d’exécuter le projet *WebApp*.

La sortie de la console affiche des messages semblables à ce qui suit (indiquant que l’application est en cours d’exécution et en attente de demandes) :

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Dans un navigateur web, accédez à `http://localhost:<port number>/api/math/sum?a=4&b=5`. Vous devez voir le résultat `9`.

Accédez à l’API du produit (`http://localhost:<port number>/api/math/product?a=4&b=5`). Elle retourne `9`, et non pas `20` comme prévu. Ce problème est résolu plus tard dans le tutoriel.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>Ajouter `dotnet watch` à un projet

L’outil Observateur de fichiers `dotnet watch` est inclus dans la version 2.1.300 du kit SDK .NET Core. Les étapes suivantes sont requises quand vous utilisez une version antérieure du kit SDK .NET Core.

1. Ajoutez une référence de package `Microsoft.DotNet.Watcher.Tools` dans le fichier *.csproj* :

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. Installez le package `Microsoft.DotNet.Watcher.Tools` en exécutant la commande suivante :

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Exécuter les commandes de l’interface CLI de .NET Core avec `dotnet watch`

Toutes les [commandes de l’interface CLI de .NET Core](/dotnet/core/tools#cli-commands) peuvent être exécutées avec `dotnet watch`. Par exemple :

| Commande | Commande avec watch |
| ---- | ----- |
| dotnet run | dotnet watch run |
| dotnet run -f netcoreapp2.0 | dotnet watch run -f netcoreapp2.0 |
| dotnet run -f netcoreapp2.0 -- --arg1 | dotnet watch run -f netcoreapp2.0 -- --arg1 |
| dotnet test | dotnet watch test |

Exécutez `dotnet watch run` dans le dossier *WebApp*. La sortie de la console indique que `watch` a démarré.

> [!NOTE]
> Vous pouvez utiliser `dotnet watch --project <PROJECT>` pour spécifier un projet à surveiller. Par exemple, `dotnet watch --project WebApp run` à la racine de l’exemple d’application aura également pour effet d’exécuter le projet *WebApp* et d’en effectuer le suivi.

## <a name="make-changes-with-dotnet-watch"></a>Apporter des modifications avec `dotnet watch`

Vérifiez que `dotnet watch` est en cours d’exécution.

Corrigez le bogue présent dans la méthode `Product` de *MathController.cs* afin qu’elle retourne le produit et non la somme :

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Enregistrez le fichier . La sortie de la console indique que `dotnet watch` a détecté un changement de fichier et a redémarré l’application.

Vérifiez que `http://localhost:<port number>/api/math/product?a=4&b=5` retourne le résultat correct.

## <a name="run-tests-using-dotnet-watch"></a>Exécuter les tests à l’aide de `dotnet watch`

1. Changez la méthode `Product` de *MathController.cs* pour qu’elle retourne à nouveau la somme. Enregistrez le fichier .
1. Dans une interface de commande, accédez au dossier *WebAppTests*.
1. Exécutez [dotnet restore](/dotnet/core/tools/dotnet-restore).
1. Exécutez `dotnet watch test`. Sa sortie indique qu’un test a échoué et que l’observateur est en attente de changement de fichier :

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Corrigez le code de la méthode `Product` afin qu’elle retourne le produit. Enregistrez le fichier .

`dotnet watch` détecte le changement de fichier et réexécute les tests. La sortie de la console indique que les tests ont réussi.

## <a name="customize-files-list-to-watch"></a>Personnaliser la liste de fichiers à observer

Par défaut, `dotnet-watch` effectue le suivi de tous les fichiers qui correspondent aux modèles Glob suivants :

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

Vous pouvez ajouter plusieurs éléments à la liste de suivi en modifiant le fichier *.csproj*. Vous pouvez spécifier des éléments individuellement ou en utilisant des modèles Glob.

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>Exclusion de fichiers à observer

`dotnet-watch` peut être configuré pour ignorer ses paramètres par défaut. Pour ignorer des fichiers spécifiques, ajoutez l’attribut `Watch="false"` à la définition d’un élément dans le fichier *.csproj* :

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a>Personnaliser les projets de suivi

`dotnet-watch` n’est pas limité aux projets C#. Vous pouvez créer des projets de suivi personnalisés pour gérer différents scénarios. Considérez l’organisation de projets suivante :

* **test**
  * *UnitTests/UnitTests.csproj*
  * *IntegrationTests/IntegrationTests.csproj*

Si l’objectif est d’observer les deux projets, créez un fichier projet personnalisé configuré à cette fin :

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

Pour démarrer l’observation de fichiers sur les deux projets, passez au dossier *test*. Exécutez la commande suivante :

```dotnetcli
dotnet watch msbuild /t:Test
```

VSTest s’exécute quand n’importe quel fichier change dans un des projets de test.

## <a name="dotnet-watch-in-github"></a>`dotnet-watch` dans GitHub

`dotnet-watch`fait partie du référentiel GitHub [dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).
