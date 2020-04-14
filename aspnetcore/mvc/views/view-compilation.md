---
title: Compilation de fichiers Razor dans ASP.NET Core
author: rick-anderson
description: Découvrez comment se produit la compilation de fichiers Razor dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277265"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Compilation de fichiers Razor dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Les fichiers Razor avec une extension *.cshtml* sont compilés à la fois le temps de construction et de publication en utilisant le [Razor SDK](xref:razor-pages/sdk). La compilation Runtime peut être activée en configurant votre projet.

## <a name="razor-compilation"></a>Compilation Razor

La compilation de fichiers Razor par défaut est activée par défaut par le Razor SDK. Lorsqu’elle est activée, la compilation runtime complète la compilation du temps de construction, permettant aux fichiers Razor d’être mis à jour s’ils sont édités.

## <a name="enable-runtime-compilation-at-project-creation"></a>Activez la compilation de l’exécution à la création de projet

Les modèles de projet Razor Pages et MVC comprennent une option pour activer la compilation en temps d’exécution lorsque le projet est créé. Cette option est prise en charge dans ASP.NET Core 3.1 et plus tard.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans le **Créer un nouveau dialogue ASP.NET application web Core** :

1. Sélectionnez soit **l’application Web,** soit le modèle de projet **d’application Web (Model-View-Controller).**
1. Sélectionnez la case à cocher **de compilation Active Razor runtime.**

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Utilisez `-rrc` l’option ou `--razor-runtime-compilation` le modèle. Par exemple, la commande suivante crée un nouveau projet Razor Pages avec une compilation en temps d’exécution activée :

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Activer la compilation en temps d’exécution dans un projet existant

Pour activer la compilation en temps d’exécution pour tous les environnements d’un projet existant :

1. Installez le package [NuGet Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)
1. Mettre à jour `Startup.ConfigureServices` la méthode du <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>projet pour inclure un appel à . Par exemple :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Activer conditionnellement la compilation en temps d’exécution dans un projet existant

La compilation Runtime peut être activée de telle sorte qu’elle n’est disponible que pour le développement local. L’activation conditionnelle de cette manière garantit que la sortie publiée :

* Utilise les vues compilées.
* N’active pas les observateurs de fichiers en production.

Pour activer la compilation en temps d’exécution uniquement dans l’environnement développement :

1. Installez le package [NuGet Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)
1. Modifier la `environmentVariables` section profil de lancement dans *launchSettings.json*:
    * Vérifier `ASPNETCORE_ENVIRONMENT` est `"Development"`réglé à .
    * Définissez `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` sur `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.

Dans l’exemple suivant, la compilation en temps `IIS Express` `RazorPagesApp` d’exécution est activée dans l’environnement développement pour les profils et les profils de lancement :

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

Aucune modification de code n’est nécessaire dans la classe du `Startup` projet. Au moment de l’exécution, ASP.NET Core recherche un [attribut HostingStartup au niveau de l’assemblage](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) dans `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`. L’attribut `HostingStartup` spécifie le code de démarrage de l’application à exécuter. Ce code de démarrage permet la compilation en temps d’exécution.

## <a name="additional-resources"></a>Ressources supplémentaires

* [RazorCompileOnBuild et RazorCompileOnPublish](xref:razor-pages/sdk#properties) propriétés.
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Consultez [l’échantillon de compilation en temps d’exécution sur GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) pour un échantillon qui montre faire fonctionner la compilation en temps d’exécution à travers les projets.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Les fichiers Razor avec une extension *.cshtml* sont compilés à la fois le temps de construction et de publication en utilisant le [Razor SDK](xref:razor-pages/sdk). La compilation au moment de l’exécution peut éventuellement être activée en configurant votre application.

## <a name="razor-compilation"></a>Compilation Razor

La compilation de fichiers Razor par défaut est activée par défaut par le Razor SDK. Lorsqu’elle est activée, la compilation runtime complète la compilation du temps de construction, permettant aux fichiers Razor d’être mis à jour s’ils sont édités.

## <a name="runtime-compilation"></a>Compilation du runtime

Pour activer la compilation de temps d’exécution pour tous les environnements et modes de configuration :

1. Installez le package [NuGet Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)

1. Mettre à jour `Startup.ConfigureServices` la méthode du <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>projet pour inclure un appel à . Par exemple :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Activer conditionnellement la compilation de l’exécution

La compilation Runtime peut être activée de telle sorte qu’elle n’est disponible que pour le développement local. L’activation conditionnelle de cette manière garantit que la sortie publiée :

* Utilise les vues compilées.
* Est plus petit dans la taille.
* N’active pas les observateurs de fichiers en production.

Pour activer la compilation en temps d’exécution en fonction de l’environnement et du mode configuration :

1. Référence conditionnelle du package [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) `Configuration` basé sur la valeur active :

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Mettre à jour `Startup.ConfigureServices` la méthode du `AddRazorRuntimeCompilation`projet pour inclure un appel à . Exécuter `AddRazorRuntimeCompilation` sous condition de telle sorte qu’il `ASPNETCORE_ENVIRONMENT` ne fonctionne `Development`en mode Debug que lorsque la variable est réglée sur :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [RazorCompileOnBuild et RazorCompileOnPublish](xref:razor-pages/sdk#properties) propriétés.
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Consultez [l’échantillon de compilation en temps d’exécution sur GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) pour un échantillon qui montre faire fonctionner la compilation en temps d’exécution à travers les projets.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Un fichier Razor est compilé au moment de l’exécution, quand la vue MVC ou la page Razor associée est appelée. Les fichiers Razor sont compilés au moment de la génération et de la publication à l’aide du kit [SDK Razor](xref:razor-pages/sdk).

## <a name="razor-compilation"></a>Compilation Razor

La compilation au moment de la génération et de la publication des fichiers Razor est activée par défaut par le kit SDK Razor. La modification des fichiers Razor une fois que ceux-ci sont mis à jour est prise en charge au moment de la génération. Par défaut, seuls les fichiers *Views.dll* et les fichiers autres que *.cshtml* compilés ou les assemblys de référence nécessaires à la compilation des fichiers Razor sont déployés avec votre application.

> [!IMPORTANT]
> L’outil de précompilation est désormais déprécié et sera supprimé dans ASP.NET Core 3.0. Nous vous recommandons de migrer vers le [SDK Razor](xref:razor-pages/sdk).
>
> Le kit SDK Razor est actif seulement si aucune propriété spécifique à la précompilation n’est définie dans le fichier projet. Par exemple, la définition de la propriété `MvcRazorCompileOnPublish` du fichier *.csproj* sur la valeur `true` désactive le kit SDK Razor.

## <a name="runtime-compilation"></a>Compilation du runtime

La compilation au moment de la génération est complétée par la compilation au moment de l’exécution des fichiers Razor. ASP.NET Core MVC recompile les fichiers Razor quand le contenu d’un fichier *.cshtml* change.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
