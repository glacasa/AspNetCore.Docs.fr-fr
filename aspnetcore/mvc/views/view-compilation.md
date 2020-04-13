---
title: Compilation de fichiers Razor dans ASP.NET Core
author: rick-anderson
description: Découvrez comment se produit la compilation de fichiers Razor dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 0afd39fdb5a6f570e0e78ad54f6c436460bad3a6
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223957"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Compilation de fichiers Razor dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Les fichiers Razor avec une extension *.cshtml* sont compilés à la fois le temps de construction et de publication en utilisant le [Razor SDK](xref:razor-pages/sdk). La compilation au moment de l’exécution peut éventuellement être activée en configurant votre application.

## <a name="razor-compilation"></a>Compilation Razor

La compilation au moment de la génération et de la publication des fichiers Razor est activée par défaut par le kit SDK Razor. Quand elle est activée, la compilation au moment de l’exécution complète la compilation au moment de la génération, ce qui permet aux fichiers Razor d’être mis à jour s’ils sont modifiés.

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

  [!code-csharp[](~/mvc/views/view-compilation/sample/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [RazorCompileOnBuild et RazorCompileOnPublish](xref:razor-pages/sdk#properties) propriétés.
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Consultez [l’échantillon de calcul de l’exécution sur GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) pour un échantillon qui montre faire fonctionner la compilation en temps d’exécution sur les projets.

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
