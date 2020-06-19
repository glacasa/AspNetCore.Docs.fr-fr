---
title: Héberger et déployer des ASP.NET CoreBlazor
author: guardrex
description: Découvrez comment héberger et déployer des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 3a3c5ab5365e5b4312dd3fd516f4906155911cc9
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103678"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="df86c-103">Héberger et déployer des ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="df86c-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="df86c-104">Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="df86c-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="df86c-105">Publier l’application</span><span class="sxs-lookup"><span data-stu-id="df86c-105">Publish the app</span></span>

<span data-ttu-id="df86c-106">Les applications sont publiées pour le déploiement dans la configuration Release.</span><span class="sxs-lookup"><span data-stu-id="df86c-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="df86c-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df86c-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="df86c-108">Sélectionnez **générer**  >  **publier {application}** dans la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="df86c-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="df86c-109">Sélectionnez l’onglet *Cible de publication*.</span><span class="sxs-lookup"><span data-stu-id="df86c-109">Select the *publish target*.</span></span> <span data-ttu-id="df86c-110">Pour publier localement, sélectionnez **Dossier**.</span><span class="sxs-lookup"><span data-stu-id="df86c-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="df86c-111">Acceptez l’emplacement par défaut dans le champ **Choisir un dossier** ou spécifiez un autre emplacement.</span><span class="sxs-lookup"><span data-stu-id="df86c-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="df86c-112">Cliquez sur le bouton **Publier**.</span><span class="sxs-lookup"><span data-stu-id="df86c-112">Select the **Publish** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="df86c-113">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="df86c-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="df86c-114">Sélectionnez **générer**  >  **publier dans le dossier**.</span><span class="sxs-lookup"><span data-stu-id="df86c-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="df86c-115">Confirmez le dossier pour recevoir les ressources publiées et sélectionnez **publier**.</span><span class="sxs-lookup"><span data-stu-id="df86c-115">Confirm the folder to receive the published assets and select **Publish**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="df86c-116">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="df86c-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="df86c-117">Utilisez la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) pour publier l’application avec une configuration Release :</span><span class="sxs-lookup"><span data-stu-id="df86c-117">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="df86c-118">La publication de l’application déclenche une [restauration](/dotnet/core/tools/dotnet-restore) des dépendances du projet et [crée](/dotnet/core/tools/dotnet-build) le projet avant de créer les ressources pour le déploiement.</span><span class="sxs-lookup"><span data-stu-id="df86c-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="df86c-119">Dans le cadre du processus de génération, les assemblys et méthodes inutilisés sont supprimés pour réduire la durée du chargement et la taille du téléchargement de l’application.</span><span class="sxs-lookup"><span data-stu-id="df86c-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="df86c-120">Emplacements de publication :</span><span class="sxs-lookup"><span data-stu-id="df86c-120">Publish locations:</span></span>

* Blazor<span data-ttu-id="df86c-121">Webassembly</span><span class="sxs-lookup"><span data-stu-id="df86c-121"> WebAssembly</span></span>
  * <span data-ttu-id="df86c-122">Autonome : l’application est publiée dans le dossier */bin/Release/{Target Framework}/Publish/wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="df86c-122">Standalone: The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="df86c-123">Pour déployer l’application en tant que site statique, copiez le contenu du dossier *wwwroot* sur l’hôte de site statique.</span><span class="sxs-lookup"><span data-stu-id="df86c-123">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="df86c-124">Hébergé : l' Blazor application Webassembly cliente est publiée dans le dossier */bin/Release/{Target Framework}/Publish/wwwroot* de l’application serveur, ainsi que toutes les autres ressources Web statiques de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="df86c-124">Hosted: The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="df86c-125">Déployez le contenu du dossier de *publication* sur l’hôte.</span><span class="sxs-lookup"><span data-stu-id="df86c-125">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="df86c-126">Serveur : l’application est publiée dans le dossier */bin/Release/{Target Framework}/Publish* .</span><span class="sxs-lookup"><span data-stu-id="df86c-126"> Server: The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="df86c-127">Déployez le contenu du dossier de *publication* sur l’hôte.</span><span class="sxs-lookup"><span data-stu-id="df86c-127">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="df86c-128">Les ressources du dossier sont déployées sur le serveur web.</span><span class="sxs-lookup"><span data-stu-id="df86c-128">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="df86c-129">Le déploiement peut être un processus manuel ou automatisé, en fonction des outils de développement utilisés.</span><span class="sxs-lookup"><span data-stu-id="df86c-129">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="df86c-130">Chemin de base de l’application</span><span class="sxs-lookup"><span data-stu-id="df86c-130">App base path</span></span>

<span data-ttu-id="df86c-131">Le *chemin d’accès de base* de l’application est le chemin de l’URL racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="df86c-131">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="df86c-132">Considérez les éléments suivants ASP.NET Core application et Blazor sous-application :</span><span class="sxs-lookup"><span data-stu-id="df86c-132">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="df86c-133">L’application ASP.NET Core est nommée `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="df86c-133">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="df86c-134">L’application réside physiquement dans *d:/MyApp*.</span><span class="sxs-lookup"><span data-stu-id="df86c-134">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="df86c-135">Les demandes sont reçues à l’adresse `https://www.contoso.com/{MYAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="df86c-135">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="df86c-136">Une Blazor application nommée `CoolApp` est une sous-application de `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="df86c-136">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="df86c-137">La sous-application réside physiquement dans *d:/MyApp/coolapp*.</span><span class="sxs-lookup"><span data-stu-id="df86c-137">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="df86c-138">Les demandes sont reçues à l’adresse `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="df86c-138">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="df86c-139">Si vous ne spécifiez pas de configuration supplémentaire pour `CoolApp` , la sous-application dans ce scénario n’a aucune connaissance de son emplacement sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="df86c-139">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="df86c-140">Par exemple, l’application ne peut pas construire des URL relatives correctes pour ses ressources sans savoir qu’elle réside sur le chemin d’URL relatif `/CoolApp/` .</span><span class="sxs-lookup"><span data-stu-id="df86c-140">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="df86c-141">Pour fournir la configuration du Blazor chemin d’accès de base de l’application `https://www.contoso.com/CoolApp/` , l’attribut de la `<base>` balise `href` est défini sur le chemin d’accès racine relatif dans le fichier *pages/_Host. cshtml* ( Blazor Server) ou *wwwroot/index.html* ( Blazor webassembly) :</span><span class="sxs-lookup"><span data-stu-id="df86c-141">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="df86c-142">Les applications serveur définissent également le chemin d’accès de base côté serveur en appelant <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> dans le pipeline de demande de l’application `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="df86c-142"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="df86c-143">En fournissant le chemin d’URL relatif, un composant qui ne se trouve pas dans le répertoire racine peut construire des URL relatives au chemin d’accès racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="df86c-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="df86c-144">Les composants à différents niveaux de la structure de répertoires peuvent créer des liens vers d’autres ressources à des emplacements de l’application.</span><span class="sxs-lookup"><span data-stu-id="df86c-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="df86c-145">Le chemin d’accès de base de l’application est également utilisé pour intercepter les liens hypertexte sélectionnés où la `href` cible du lien se trouve dans l’espace URI du chemin d’accès de base de l’application.</span><span class="sxs-lookup"><span data-stu-id="df86c-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="df86c-146">Le Blazor routeur gère la navigation interne.</span><span class="sxs-lookup"><span data-stu-id="df86c-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="df86c-147">Dans de nombreux scénarios d’hébergement, le chemin d’URL relatif à l’application est la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="df86c-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="df86c-148">Dans ce cas, le chemin d’accès de base de l’URL relative de l’application est une barre oblique ( `<base href="/" />` ), qui est la configuration par défaut pour une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="df86c-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="df86c-149">Dans d’autres scénarios d’hébergement, tels que les pages GitHub et les sous-applications IIS, le chemin d’accès de base de l’application doit être défini sur le chemin d’URL relatif du serveur de l’application.</span><span class="sxs-lookup"><span data-stu-id="df86c-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="df86c-150">Pour définir le chemin d’accès de base de l’application, mettez à jour la `<base>` balise dans les `<head>` éléments tag du fichier *pages/_Host. cshtml* ( Blazor Server) ou *wwwroot/index.html* ( Blazor webassembly).</span><span class="sxs-lookup"><span data-stu-id="df86c-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="df86c-151">Affectez `href` à l’attribut la valeur `/{RELATIVE URL PATH}/` (la barre oblique de fin est requise), où `{RELATIVE URL PATH}` est le chemin d’URL relatif complet de l’application.</span><span class="sxs-lookup"><span data-stu-id="df86c-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="df86c-152">Pour une Blazor application Webassembly avec un chemin d’URL relatif non racine (par exemple, `<base href="/CoolApp/">` ), l’application ne parvient pas à trouver ses ressources lorsqu’elle est *exécutée localement*.</span><span class="sxs-lookup"><span data-stu-id="df86c-152">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="df86c-153">Pour surmonter ce problème pendant le développement local et les tests, vous pouvez fournir un argument de *base de chemin* qui correspond à la valeur `href` de la balise `<base>` au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="df86c-153">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="df86c-154">N’incluez pas de barre oblique finale.</span><span class="sxs-lookup"><span data-stu-id="df86c-154">Don't include a trailing slash.</span></span> <span data-ttu-id="df86c-155">Pour passer l’argument de base Path lors de l’exécution locale de l’application, exécutez la `dotnet run` commande à partir du répertoire de l’application avec l' `--pathbase` option :</span><span class="sxs-lookup"><span data-stu-id="df86c-155">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="df86c-156">Pour une Blazor application Webassembly avec un chemin d’URL relatif `/CoolApp/` ( `<base href="/CoolApp/">` ), la commande est la suivante :</span><span class="sxs-lookup"><span data-stu-id="df86c-156">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="df86c-157">L' Blazor application Webassembly répond localement à l’adresse `http://localhost:port/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="df86c-157">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="df86c-158">Déploiement</span><span class="sxs-lookup"><span data-stu-id="df86c-158">Deployment</span></span>

<span data-ttu-id="df86c-159">Pour obtenir des conseils de déploiement, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="df86c-159">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
