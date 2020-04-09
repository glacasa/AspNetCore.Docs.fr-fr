---
title: Interface utilisateur Razor réutilisable dans les bibliothèques de classes avec ASP.NET Core
author: Rick-Anderson
description: Explique comment créer une interface utilisateur Réutilisable Razor en utilisant des vues partielles dans une bibliothèque de classe à ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: f24dc62eba345a8a3d35143805b4966cb51832fa
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667565"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>Créez une interface utilisateur réutilisable à l’aide du projet de bibliothèque de la classe Razor dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Les vues de rasoir, les pages, les contrôleurs, les modèles de page, [les composants Razor,](xref:blazor/class-libraries) [les composants de vue,](xref:mvc/views/view-components)et les modèles de données peuvent être intégrés dans une bibliothèque de classe Razor (RCL). La RCL peut être empaquetée et réutilisée. Les applications peuvent inclure la RCL et remplacer les vues et les pages qu’elle contient. Quand une vue, une vue partielle ou une page Razor est présente dans l’application web et la RCL, le balisage Razor (fichier *.cshtml*) dans l’application web est prioritaire.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Créer une bibliothèque de classes contenant l’interface utilisateur Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* De Visual Studio **sélectionnez Créez un nouveau projet**.
* Sélectionnez **Razor Class Library** > **Next**.
* Nommez la bibliothèque (par exemple, "RazorClassLib"), > **Créer**. Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.
* Sélectionnez **les pages et les vues de support** si vous avez besoin de prendre en charge les vues. Par défaut, seules les pages Razor sont prises en charge. Sélectionnez **Create** (Créer).

Le modèle de bibliothèque de classe Razor (RCL) par défaut au développement de composants Razor par défaut. **L’option Des pages et des vues** de support prend en charge les pages et les vues.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

À partir de la ligne de commande, exécutez `dotnet new razorclasslib`. Par exemple :

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Le modèle de bibliothèque de classe Razor (RCL) par défaut au développement de composants Razor par défaut. Passez `--support-pages-and-views` l’option`dotnet new razorclasslib --support-pages-and-views`() pour fournir un soutien pour les pages et les vues.

Pour plus d’informations, consultez [dotnet new](/dotnet/core/tools/dotnet-new). Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.

---

Ajoutez des fichiers Razor à la RCL.

Les modèles ASP.NET Core supposent que le contenu RCL se trouve dans le dossier *Zones.* Voir [la mise en page RCL Pages](#rcl-pages-layout) `~/Pages` pour `~/Areas/Pages`créer un RCL qui expose le contenu dans plutôt que .

## <a name="reference-rcl-content"></a>Contenu de référence RCL

La RCL peut être référencée par :

* Un package NuGet. Consultez [Création de packages NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) et [Créer et publier un package NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* Un fichier *{NomProjet}.csproj*. Consultez [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Substituer des vues, des vues partielles et des pages

Quand une vue, une vue partielle ou une page Razor est présente dans l’application web et la RCL, le balisage Razor (fichier *.cshtml*) dans l’application web est prioritaire. Par exemple, ajoutez *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* à WebApp1, et Page1 dans le WebApp1 aura préséance sur Page1 dans le RCL.

Dans l’exemple proposé sous forme de téléchargement, renommez *WebApp1/Areas/MyFeature2* en *WebApp1/Areas/MyFeature* pour tester la priorité.

Copiez la vue partielle *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* dans *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Mettez à jour le balisage pour indiquer le nouvel emplacement. Générez et exécutez l’application pour vérifier que la version de la vue partielle de l’application est utilisée.

### <a name="rcl-pages-layout"></a>Mise en page de RCL Pages

Pour référencer le contenu RCL comme s’il faisait partie du dossier *Pages* de l’application Web, créez le projet RCL avec la structure de fichier suivante :

* *RazorUIClassLib/Pages*
* *RazorUIClassLib/Pages/Partagé*

Supposons *RazorUIClassLib/Pages/Shared* contient deux fichiers partiels: *_Header.cshtml* et *_Footer.cshtml*. Les `<partial>` balises pourraient être ajoutées au fichier *_Layout.cshtml* :

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>Créer un RCL avec des actifs statiques

Un RCL peut nécessiter des actifs statiques complémentaires qui peuvent être référencés par le RCL ou l’application consommatrice du RCL. ASP.NET Core permet de créer des CCR qui incluent des actifs statiques qui sont disponibles pour une application consommatrice.

Pour inclure les actifs complémentaires dans le cadre d’un RCL, créez un dossier *wwwroot* dans la bibliothèque de classe et incluez tous les fichiers requis dans ce dossier.

Lors de l’emballage d’un RCL, tous les actifs compagnons dans le dossier *wwwroot* sont automatiquement inclus dans le paquet.

### <a name="exclude-static-assets"></a>Exclure les actifs statiques

Pour exclure les actifs statiques, `$(DefaultItemExcludes)` ajoutez le chemin d’exclusion souhaité au groupe immobilier dans le dossier du projet. Entrées séparées avec un`;`point-virgule ( ).

Dans l’exemple suivant, la feuille de style *lib.css* dans le dossier *wwwroot* n’est pas considérée comme un actif statique et n’est pas incluse dans le RCL publié :

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Intégration de typescript

Pour inclure les fichiers TypeScript dans un RCL :

1. Placez les fichiers TypeScript (*.ts*) à l’extérieur du dossier *wwwroot.* Par exemple, placez les fichiers dans un dossier *Client.*

1. Configurez la sortie de build TypeScript pour le dossier *wwwroot.* Réglez la `TypescriptOutDir` `PropertyGroup` propriété à l’intérieur d’un dans le fichier du projet :

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Inclure la cible TypeScript comme `ResolveCurrentProjectStaticWebAssets` une dépendance de la `PropertyGroup` cible en ajoutant la cible suivante à l’intérieur d’un dans le fichier du projet :

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Consommer du contenu d’un RCL référencé

Les fichiers inclus dans le dossier *wwwroot* du RCL sont exposés soit à `_content/{LIBRARY NAME}/`la RCL ou à l’application consommatrice sous le préfixe . Par exemple, une bibliothèque nommée *Razor.Class.Lib* donne `_content/Razor.Class.Lib/`un chemin vers le contenu statique à . Lorsque vous produisez un paquet NuGet et le nom d’assemblage n’est pas le même que l’ID du paquet, utilisez l’ID du paquet pour `{LIBRARY NAME}`.

L’application de consommation fait référence `<script>` `<style>`aux `<img>`actifs statiques fournis par la bibliothèque avec , , , et d’autres balises HTML. L’application consommatrice doit avoir [la prise en charge statique de fichiers](xref:fundamentals/static-files) activée dans `Startup.Configure`:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Lors de l’exécution de`dotnet run`l’application de consommation à partir de la sortie de construction ( ), les actifs web statiques sont activés par défaut dans l’environnement de développement. Pour soutenir les actifs dans d’autres `UseStaticWebAssets` environnements lorsqu’il s’exécute à partir de la production de construction, faites appel au constructeur hôte dans *Program.cs*:

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

L’appel `UseStaticWebAssets` n’est pas nécessaire lors`dotnet publish`de l’exécution d’une application à partir de la sortie publiée ( ).

### <a name="multi-project-development-flow"></a>Flux de développement multi-projets

Lorsque l’application consommatrice s’exécute :

* Les actifs de la RCL restent dans leurs dossiers d’origine. Les actifs ne sont pas transférés à l’application de consommation.
* Tout changement dans le dossier *wwwroot* de la RCL se reflète dans l’application de consommation après la reconstruction du RCL et sans reconstruire l’application consommatrice.

Lorsque le RCL est construit, un manifeste est produit qui décrit les emplacements statiques d’actifs Web. L’application de consommation lit le manifeste au moment de l’exécution pour consommer les actifs de projets et de paquets référencés. Lorsqu’un nouvel actif est ajouté à un RCL, le RCL doit être reconstruit pour mettre à jour son manifeste avant qu’une application consommatrice puisse accéder au nouvel actif.

### <a name="publish"></a>Publish

Lorsque l’application est publiée, les actifs complémentaires de tous les projets et forfaits `_content/{LIBRARY NAME}/`référencés sont copiés dans le dossier *wwwroot* de l’application publiée en dessous .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Les vues de rasoir, les pages, les contrôleurs, les modèles de page, [les composants Razor,](xref:blazor/class-libraries) [les composants de vue,](xref:mvc/views/view-components)et les modèles de données peuvent être intégrés dans une bibliothèque de classe Razor (RCL). La RCL peut être empaquetée et réutilisée. Les applications peuvent inclure la RCL et remplacer les vues et les pages qu’elle contient. Quand une vue, une vue partielle ou une page Razor est présente dans l’application web et la RCL, le balisage Razor (fichier *.cshtml*) dans l’application web est prioritaire.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Créer une bibliothèque de classes contenant l’interface utilisateur Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.
* Sélectionnez **Application web ASP.NET Core**.
* Nommez la bibliothèque (par exemple, « RazorClassLib ») > **OK**. Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.
* Vérifiez que **ASP.NET Core 2.1** ou ultérieur est sélectionné.
* Sélectionnez **Razor Class Library** > **OK**.

Un RCL a le dossier de projet suivant :

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

À partir de la ligne de commande, exécutez `dotnet new razorclasslib`. Par exemple :

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Pour plus d’informations, consultez [dotnet new](/dotnet/core/tools/dotnet-new). Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.

---

Ajoutez des fichiers Razor à la RCL.

Les modèles ASP.NET Core supposent que le contenu RCL se trouve dans le dossier *Zones.* Voir [la mise en page RCL Pages](#rcl-pages-layout) `~/Pages` pour `~/Areas/Pages`créer un RCL qui expose le contenu dans plutôt que .

## <a name="reference-rcl-content"></a>Contenu de référence RCL

La RCL peut être référencée par :

* Un package NuGet. Consultez [Création de packages NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) et [Créer et publier un package NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* Un fichier *{NomProjet}.csproj*. Consultez [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Procédure pas à pas : Créer un projet RCL et utiliser à partir d’un projet Razor Pages

Vous pouvez télécharger et tester le [projet complet](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) au lieu de le créer de toutes pièces. L’exemple proposé sous forme de téléchargement contient du code et des liens supplémentaires qui facilitent le test du projet. Si vous souhaitez commenter le mode d’obtention des exemples (téléchargement ou création au moyen d’instructions détaillées), entrez vos commentaires dans [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6098).

### <a name="test-the-download-app"></a>Tester l’application téléchargée

Si vous n’avez pas téléchargé l’application complète et que vous souhaitez créer le projet pas à pas, passez à la [section suivante](#create-an-rcl).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ouvrez le fichier *.sln* dans Visual Studio. Exécutez l'application.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

À partir d’une invite de commandes dans le répertoire *cli*, générez la RCL et l’application web.

```dotnetcli
dotnet build
```

Passez au répertoire *WebApp1* et exécutez l’application :

```dotnetcli
dotnet run
```

---

Suivez les instructions contenues dans [Test WebApp1](#test-webapp1)

## <a name="create-an-rcl"></a>Créer un RCL

Dans cette section, un RCL est créé. Des fichiers Razor sont ajoutés à la RCL.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Créez le projet RCL :

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.
* Sélectionnez **Application web ASP.NET Core**.
* Nommez l’application **RazorUIClassLib** > **OK**.
* Vérifiez que **ASP.NET Core 2.1** ou ultérieur est sélectionné.
* Sélectionnez **Razor Class Library** > **OK**.
* Ajoutez un fichier de vue partielle Razor nommé *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

À partir de la ligne de commande, exécutez ce qui suit :

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Les commandes précédentes :

* Crée `RazorUIClassLib` le RCL.
* Créent une page _Message Razor et l’ajoutent à la RCL. Le paramètre `-np` crée la page sans `PageModel`.
* Crée un fichier [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) et l’ajoute au RCL.

Le fichier *_ViewStart.cshtml* est nécessaire pour utiliser la mise en page du projet Razor Pages (qui est ajouté dans la section suivante).

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Ajouter des fichiers et des dossiers Razor au projet

* Remplacez le balisage dans *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* par le code suivant :

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Remplacez le balisage dans *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* par le code suivant :

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` est nécessaire pour utiliser la vue partielle (`<partial name="_Message" />`). Au lieu d’inclure la directive `@addTagHelper`, vous pouvez ajouter un fichier *_ViewImports.cshtml*. Par exemple :

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  Pour plus d’informations sur *_ViewImports.cshtml*, voir [Directives partagées d’importation](xref:mvc/views/layout#importing-shared-directives)

* Générez la bibliothèque de classes pour vérifier l’absence d’erreurs de compilateur :

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

La sortie de build contient *RazorUIClassLib.dll* et *RazorUIClassLib.Views.dll*. *RazorUIClassLib.Views.dll* contient le contenu Razor compilé.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Utiliser la bibliothèque de l’interface utilisateur Razor à partir d’un projet Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Créez l’application web Razor Pages :

* Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur la solution > **Ajouter** >  **Nouveau projet**.
* Sélectionnez **Application web ASP.NET Core**.
* Nommez l’application **WebApp1**.
* Vérifiez que **ASP.NET Core 2.1** ou ultérieur est sélectionné.
* Sélectionnez **Application web** > **OK**.

* Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **WebApp1**, puis sélectionnez **Définir comme projet de démarrage**.
* Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **WebApp1**, puis sélectionnez **Dépendances de build** > **Dépendances du projet**.
* Cochez **RazorUIClassLib** comme dépendance de **WebApp1**.
* Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **WebApp1**, puis sélectionnez **Ajouter** > **Référence**.
* Dans la boîte de dialogue **Gestionnaire de références**, cochez **RazorUIClassLib** > **OK**.

Exécutez l'application.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Créez une application Web Razor Pages et un fichier de solution contenant l’application Razor Pages et le RCL :

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Générez et exécutez l’application web :

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>Tester WebApp1

Naviguez `/MyFeature/Page1` pour vérifier que la bibliothèque de classe Razor UI est utilisée.

## <a name="override-views-partial-views-and-pages"></a>Substituer des vues, des vues partielles et des pages

Quand une vue, une vue partielle ou une page Razor est présente dans l’application web et la RCL, le balisage Razor (fichier *.cshtml*) dans l’application web est prioritaire. Par exemple, ajoutez *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* à WebApp1, et Page1 dans le WebApp1 aura préséance sur Page1 dans le RCL.

Dans l’exemple proposé sous forme de téléchargement, renommez *WebApp1/Areas/MyFeature2* en *WebApp1/Areas/MyFeature* pour tester la priorité.

Copiez la vue partielle *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* dans *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Mettez à jour le balisage pour indiquer le nouvel emplacement. Générez et exécutez l’application pour vérifier que la version de la vue partielle de l’application est utilisée.

### <a name="rcl-pages-layout"></a>Mise en page de RCL Pages

Pour référencer le contenu RCL comme s’il faisait partie du dossier *Pages* de l’application Web, créez le projet RCL avec la structure de fichier suivante :

* *RazorUIClassLib/Pages*
* *RazorUIClassLib/Pages/Partagé*

Supposons *RazorUIClassLib/Pages/Shared* contient deux fichiers partiels: *_Header.cshtml* et *_Footer.cshtml*. Les `<partial>` balises pourraient être ajoutées au fichier *_Layout.cshtml* :

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/class-libraries>
