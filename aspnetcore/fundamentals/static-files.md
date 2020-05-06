---
title: Fichiers statiques dans ASP.NET Core
author: rick-anderson
description: Découvrez comment délivrer et sécuriser des fichiers statiques, et comment configurer le comportement d’un intergiciel (middleware) hébergeant des fichiers statiques dans une application web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: f7697260e6ab29d0e9ba955dfdf0c8c81e4e1130
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775490"
---
# <a name="static-files-in-aspnet-core"></a>Fichiers statiques dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Scott Addie](https://twitter.com/Scott_Addie)

Les fichiers statiques, comme les fichiers HTML, CSS, images et JavaScript, sont des ressources qu’une application ASP.NET Core délivre directement aux clients. Une configuration est nécessaire pour pouvoir délivrer ces fichiers.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Délivrer des fichiers statiques

Les fichiers statiques sont stockés dans le répertoire [racine Web](xref:fundamentals/index#web-root) du projet. Le répertoire par défaut est *{content root}/wwwroot*, mais il peut être modifié à l’aide de la méthode [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) . Pour plus d’informations, consultez [Racine du contenu](xref:fundamentals/index#content-root) et [Racine web](xref:fundamentals/index#web-root).

L’hôte web de l’application doit être informé du répertoire racine du contenu.

::: moniker range=">= aspnetcore-2.0"

La méthode `WebHost.CreateDefaultBuilder` définit le répertoire actif comme racine du contenu :

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Définissez le répertoire actif comme racine du contenu en appelant [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) à l’intérieur de `Program.Main` :

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

Les fichiers statiques sont accessibles via un chemin d’accès relatif à la [racine Web](xref:fundamentals/index#web-root). Par exemple, le modèle de projet **Application web** contient plusieurs dossiers dans le dossier *wwwroot* :

* **wwwroot**
  * **format**
  * **images**
  * **js**

Le format d’URI pour accéder à un fichier dans le sous-dossier *images* est *http://\<adresse_serveur>/images/\<nom_fichier_image>*. Par exemple : *http://localhost:9189/images/banner3.svg* .

::: moniker range=">= aspnetcore-2.1"

Si vous ciblez .NET Framework, ajoutez le package [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) au projet. Si vous ciblez .NET Core, le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) comprend ce package.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Si vous ciblez .NET Framework, ajoutez le package [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) au projet. Si vous ciblez .NET Core, le métapackage [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) inclut ce package.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Ajoutez le package [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) au projet.

::: moniker-end

Configurez le [middleware](xref:fundamentals/middleware/index) qui permet de délivrer des fichiers statiques.

### <a name="serve-files-inside-of-web-root"></a>Délivrer des fichiers dans la racine web

Appelez la méthode [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dans `Startup.Configure` :

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

La surcharge de `UseStaticFiles` méthode sans paramètre marque les fichiers dans la [racine Web](xref:fundamentals/index#web-root) comme étant reservables. Le balisage suivant référence *wwwroot/images/banner1.svg* :

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

Dans le code précédent, le caractère `~/` tilde pointe vers la [racine Web](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Délivrer des fichiers en dehors de la racine web

Prenons l’exemple d’une hiérarchie de répertoires dans laquelle les fichiers statiques à prendre en charge se trouvent en dehors de la [racine Web](xref:fundamentals/index#web-root):

* **wwwroot**
  * **format**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*

Vous permettez à une requête d’accéder au fichier *banner1.svg* en configurant le middleware (intergiciel) des fichiers statiques comme suit :

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

Dans le code précédent, la hiérarchie de répertoires *MyStaticFiles* est exposée publiquement via le segment d’URI *StaticFiles*. Une requête à *http://\<adresse_serveur>/StaticFiles/images/banner1.svg* délivre le fichier *banner1.svg*.

Le balisage suivant référence *MyStaticFiles/images/banner1.svg* :

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Définir des en-têtes de réponse HTTP

Un objet [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) peut être utilisé pour définir des en-têtes de réponse HTTP. En plus de configurer le service de fichiers statiques à partir de la [racine Web](xref:fundamentals/index#web-root), le `Cache-Control` code suivant définit l’en-tête :

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

La méthode [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) se trouve dans le package [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).

Les fichiers peuvent être mis en cache publiquement pendant 10 minutes (600 secondes) dans l’environnement de développement :

![En-têtes de réponse montrant que l’en-tête Cache-Control a été ajouté](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorisations des fichiers statiques

Le middleware de fichiers statiques ne fournit pas de vérification des autorisations. Tous les fichiers qu’il délivre, notamment ceux sous *wwwroot*, sont accessibles publiquement. Pour délivrer des fichiers en fonction d’une autorisation :

* Stockez-les en dehors de *wwwroot* et de tout répertoire accessible au middleware de fichiers statiques.
* Délivrez-les via une méthode d’action à laquelle une autorisation est appliquée. Retournez un objet [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) :

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Activer l’exploration des répertoires

L’exploration des répertoires permet aux utilisateurs de votre application web de voir une liste des répertoires et des fichiers dans un répertoire spécifié. L’exploration des répertoires est désactivée par défaut pour des raisons de sécurité (consultez [Considérations](#considerations)). Activez l’exploration des répertoires en appelant la méthode [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) dans `Startup.Configure` :

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Ajoutez les services nécessaires en appelant la méthode [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) depuis `Startup.ConfigureServices` :

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Le code précédent permet l’exploration des répertoires du dossier *wwwroot/images* en utilisant l’URL *http://\<adresse_serveur>/MyImages*, avec des liens vers chaque fichier et dossier :

![exploration des répertoires](static-files/_static/dir-browse.png)

Consultez [Considérations](#considerations) sur les risques de sécurité lors de l’activation de l’exploration.

Notez les deux appels de `UseStaticFiles` dans l’exemple suivant. Le premier appel permet de délivrer des fichiers statiques dans le dossier *wwwroot*. Le deuxième appel active l’exploration des répertoires du dossier *wwwroot/images* en utilisant l’URL *http://\<adresse_serveur>/MyImages* :

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Délivrer un document par défaut

La définition d’une page d’accueil par défaut donne aux visiteurs un point de départ logique lors de la visite de votre site. Pour délivrer une page par défaut sans que l’utilisateur qualifie complètement l’URI, appelez la méthode [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) depuis `Startup.Configure` :

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles` doit être appelé avant `UseStaticFiles` pour délivrer le fichier par défaut. `UseDefaultFiles` est un module de réécriture d’URL qui ne délivre pas réellement le fichier. Activez le middleware de fichiers statiques via `UseStaticFiles` pour délivrer le fichier.

Avec `UseDefaultFiles`, les requêtes sur un dossier recherchent :

* *default.htm*
* *default.html*
* *index.htm*
* *index. html*

Le premier fichier trouvé dans la liste est délivré comme si la requête était l’URI qualifié complet. L’URL du navigateur continue de refléter l’URI demandé.

Le code suivant change le nom de fichier par défaut en *mydefault.html* :

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>combine les fonctionnalités de `UseStaticFiles`, `UseDefaultFiles`et éventuellement `UseDirectoryBrowser`.

Le code suivant active la possibilité de délivrer des fichiers statiques et le fichier par défaut. L’exploration des répertoires n’est pas activée.

```csharp
app.UseFileServer();
```

Le code suivant s’appuie sur la surcharge sans paramètre en activant l’exploration des répertoires :

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Considérez la hiérarchie de répertoires suivante :

* **wwwroot**
  * **format**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*
  * *default.html*

Le code suivant active les fichiers statiques, les fichiers par défaut et l’exploration des répertoires de `MyStaticFiles` :

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser` doit être appelé quand la valeur de la propriété `EnableDirectoryBrowsing` est `true` :

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

En utilisant la hiérarchie de fichiers et le code précédent, les URL sont résolues comme suit :

| URI            |                             response  |
| ------- | ------|
| *http://\<server_address>/staticfiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<server_address>/staticfiles*             |     MyStaticFiles/default.html |

Si aucun fichier nommé default n’existe dans le répertoire *MyStaticFiles*, *http://\<adresse_serveur>/StaticFiles* retourne la liste des répertoires avec des liens cliquables :

![Liste des fichiers statiques](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> et <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> effectuent une redirection côté client à partir de `http://{SERVER ADDRESS}/StaticFiles` (sans barre oblique) vers `http://{SERVER ADDRESS}/StaticFiles/` (avec barre oblique). Les URL relatives du répertoire *StaticFiles* ne sont pas valides sans barre oblique de fin.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

La classe [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) contient une propriété `Mappings` agissant comme un mappage des extensions de fichiers à des types de contenu MIME. Dans l’exemple suivant, plusieurs extensions de fichiers sont inscrites avec des types MIME connus. L’extension *.rtf* est remplacée et *.mp4* est supprimée.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Consultez [Types de contenu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Types de contenu non standard

Static File Middleware comprend près de 400 types connus de contenu de fichier. Si l’utilisateur demande un fichier d’un type inconnu, Static File Middleware transmet la requête à l’intergiciel (middleware) suivant dans le pipeline. Si aucun intergiciel ne gère la requête, une réponse *404 introuvable* est retournée. Si l’exploration des répertoires est activée, un lien vers le fichier est affiché dans la liste de répertoires.

Le code suivant permet de délivrer des types inconnus et rend le fichier inconnu en tant qu’image :

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

Avec le code précédent, une requête pour un fichier avec un type de contenu inconnu est retournée en tant qu’image.

> [!WARNING]
> L’activation de [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) présente un risque de sécurité. Il est désactivé par défaut et son utilisation est déconseillée. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fournit une alternative plus sûre pour délivrer des fichiers avec des extensions non standard.

## <a name="serve-files-from-multiple-locations"></a>Servir des fichiers à partir de plusieurs emplacements

`UseStaticFiles`la `UseFileServer` valeur par défaut est le fournisseur de fichiers qui pointe vers *wwwroot*. Vous pouvez fournir des instances supplémentaires `UseStaticFiles` de `UseFileServer` et avec d’autres fournisseurs de fichiers pour servir des fichiers à partir d’autres emplacements. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

### <a name="considerations"></a>Considérations

> [!WARNING]
> `UseDirectoryBrowser` et `UseStaticFiles` peuvent entraîner une fuite de secrets. La désactivation de l’exploration de répertoires est fortement recommandée en production. Examinez attentivement les répertoires qui sont activés via `UseStaticFiles` ou `UseDirectoryBrowser`. L’ensemble du répertoire et de ses sous-répertoires deviennent accessibles publiquement. Stocker les fichiers pouvant être affectés au public dans un répertoire dédié, par exemple * \<content_root>/wwwroot*. Séparez ces fichiers des vues MVC Razor , des pages (2. x uniquement), des fichiers de configuration, etc.

* Les URL pour le contenu exposé avec `UseDirectoryBrowser` et `UseStaticFiles` sont soumises aux restrictions de respect de la casse et de caractères du système de fichiers sous-jacent. Par exemple, Windows ne respecte pas la casse, mais macOS et Linux la respectent.

* Les applications ASP.NET Core hébergées dans IIS utilisent le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pour transférer toutes les requêtes à l’application, notamment les requêtes de fichiers statiques. Le gestionnaire de fichiers statiques d’IIS n’est pas utilisé. Il ne lui est pas permis de traiter les requêtes avant qu’elles soient gérées par le module.

* Effectuez les étapes suivantes dans le Gestionnaire des services Internet (IIS) pour supprimer le gestionnaire de fichiers statiques d’IIS au niveau du serveur ou du site web :
    1. Accédez à la fonctionnalité **Modules**.
    1. Sélectionnez **StaticFileModule** dans la liste.
    1. Cliquez sur **Supprimer** dans l’encadré **Actions**.

> [!WARNING]
> Si le gestionnaire de fichiers statiques d’IIS est activé **et** que le module ASP.NET Core est incorrectement configuré, les fichiers statiques peuvent être délivrés. Cela se produit par exemple si le fichier *web.config* n’est pas déployé.

* Placez les fichiers de code (y compris les fichiers *. cs* et *. cshtml*) en dehors de la [racine Web](xref:fundamentals/index#web-root)du projet d’application. Par conséquent, une séparation logique est créée entre le contenu côté client et le code basé sur le serveur de l’application. Ceci empêche la fuite de code côté serveur.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Middleware](xref:fundamentals/middleware/index)
* [Introduction à ASP.NET Core](xref:index)
