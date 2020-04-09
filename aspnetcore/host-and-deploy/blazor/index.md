---
title: Hôte et déploiement ASP.NET CoreBlazor
author: guardrex
description: Découvrez comment héberger Blazor et déployer des applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434263"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="ea15d-103">Héberger et déployer ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ea15d-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="ea15d-104">Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ea15d-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="ea15d-105">Publier l’application</span><span class="sxs-lookup"><span data-stu-id="ea15d-105">Publish the app</span></span>

<span data-ttu-id="ea15d-106">Les applications sont publiées pour le déploiement dans la configuration Release.</span><span class="sxs-lookup"><span data-stu-id="ea15d-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea15d-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea15d-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ea15d-108">Sélectionnez **Build** > **Publier 'APPLICATION'** à partir de la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="ea15d-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="ea15d-109">Sélectionnez l’onglet *Cible de publication*.</span><span class="sxs-lookup"><span data-stu-id="ea15d-109">Select the *publish target*.</span></span> <span data-ttu-id="ea15d-110">Pour publier localement, sélectionnez **Dossier**.</span><span class="sxs-lookup"><span data-stu-id="ea15d-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="ea15d-111">Acceptez l’emplacement par défaut dans le champ **Choisir un dossier** ou spécifiez un autre emplacement.</span><span class="sxs-lookup"><span data-stu-id="ea15d-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="ea15d-112">Cliquez sur le bouton **Publier**.</span><span class="sxs-lookup"><span data-stu-id="ea15d-112">Select the **Publish** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ea15d-113">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="ea15d-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ea15d-114">Utilisez la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) pour publier l’application avec une configuration Release :</span><span class="sxs-lookup"><span data-stu-id="ea15d-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="ea15d-115">La publication de l’application déclenche une [restauration](/dotnet/core/tools/dotnet-restore) des dépendances du projet et [crée](/dotnet/core/tools/dotnet-build) le projet avant de créer les ressources pour le déploiement.</span><span class="sxs-lookup"><span data-stu-id="ea15d-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="ea15d-116">Dans le cadre du processus de génération, les assemblys et méthodes inutilisés sont supprimés pour réduire la durée du chargement et la taille du téléchargement de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea15d-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="ea15d-117">Publier les lieux:</span><span class="sxs-lookup"><span data-stu-id="ea15d-117">Publish locations:</span></span>

* Blazor<span data-ttu-id="ea15d-118">WebAssembly (en)</span><span class="sxs-lookup"><span data-stu-id="ea15d-118"> WebAssembly</span></span>
  * <span data-ttu-id="ea15d-119">Autonome &ndash; L’application est publiée dans le *dossier /bin/Release/TARGET FRAMEWORK/publish/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="ea15d-119">Standalone &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="ea15d-120">Pour déployer l’application en tant que site statique, copiez le contenu du dossier *wwwroot* à l’hôte statique du site.</span><span class="sxs-lookup"><span data-stu-id="ea15d-120">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="ea15d-121">Hébergé &ndash; L’application WebAssembly client Blazor est publiée dans le dossier */bin/Release/TARGET FRAMEWORK/publish/wwwroot* de l’application serveur, ainsi que tous les autres actifs Web statiques de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="ea15d-121">Hosted &ndash; The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="ea15d-122">Déployez le contenu du dossier *de publication* à l’hôte.</span><span class="sxs-lookup"><span data-stu-id="ea15d-122">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="ea15d-123">Serveur &ndash; L’application est publiée dans le *dossier /bin/Release/TARGET FRAMEWORK/publish.*</span><span class="sxs-lookup"><span data-stu-id="ea15d-123"> Server &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="ea15d-124">Déployez le contenu du dossier *de publication* à l’hôte.</span><span class="sxs-lookup"><span data-stu-id="ea15d-124">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="ea15d-125">Les ressources du dossier sont déployées sur le serveur web.</span><span class="sxs-lookup"><span data-stu-id="ea15d-125">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="ea15d-126">Le déploiement peut être un processus manuel ou automatisé, en fonction des outils de développement utilisés.</span><span class="sxs-lookup"><span data-stu-id="ea15d-126">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="ea15d-127">Chemin de base de l’application</span><span class="sxs-lookup"><span data-stu-id="ea15d-127">App base path</span></span>

<span data-ttu-id="ea15d-128">Le *chemin de base de l’application* est le chemin d’URL racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea15d-128">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="ea15d-129">Considérez l’application et la Blazor sous-application ASP.NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="ea15d-129">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="ea15d-130">L’application ASP.NET Core est `MyApp`nommée :</span><span class="sxs-lookup"><span data-stu-id="ea15d-130">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="ea15d-131">L’application réside physiquement à *d:/MyApp*.</span><span class="sxs-lookup"><span data-stu-id="ea15d-131">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="ea15d-132">Les demandes `https://www.contoso.com/{MYAPP RESOURCE}`sont reçues à .</span><span class="sxs-lookup"><span data-stu-id="ea15d-132">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="ea15d-133">Une Blazor application `CoolApp` nommée est une `MyApp`sous-application de :</span><span class="sxs-lookup"><span data-stu-id="ea15d-133">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="ea15d-134">La sous-application réside physiquement à *d:/MyApp/CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="ea15d-134">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="ea15d-135">Les demandes `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`sont reçues à .</span><span class="sxs-lookup"><span data-stu-id="ea15d-135">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="ea15d-136">Sans spécifier `CoolApp`une configuration supplémentaire pour , la sous-application dans ce scénario n’a aucune connaissance de l’endroit où il réside sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="ea15d-136">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="ea15d-137">Par exemple, l’application ne peut pas construire des URL relatives correctes à `/CoolApp/`ses ressources sans savoir qu’elle réside sur le chemin relatif de l’URL .</span><span class="sxs-lookup"><span data-stu-id="ea15d-137">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="ea15d-138">Pour fournir la Blazor configuration pour le `https://www.contoso.com/CoolApp/`chemin `<base>` de `href` base de l’application de , l’attribut de l’étiquette estBlazor réglé sur le chemin de racine relative dans le fichier *Pages/_Host.cshtml* (Serveur) ou *wwwroot/index.html* fichier (WebAssembly):Blazor</span><span class="sxs-lookup"><span data-stu-id="ea15d-138">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="ea15d-139">Les applications serveur réglent en <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> outre le chemin de `Startup.Configure`base côté serveur en appelant dans le pipeline de demande de l’application de :</span><span class="sxs-lookup"><span data-stu-id="ea15d-139"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="ea15d-140">En fournissant le chemin URL relatif, un composant qui n’est pas dans le répertoire racine peut construire des URL par rapport à la trajectoire de racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea15d-140">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="ea15d-141">Les composants à différents niveaux de la structure de l’annuaire peuvent créer des liens vers d’autres ressources à des endroits dans l’ensemble de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea15d-141">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="ea15d-142">Le chemin de base de l’application est `href` également utilisé pour intercepter certains hyperliens lorsque la cible du lien se trouve dans l’espace URI de la trajectoire de base de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea15d-142">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="ea15d-143">Le Blazor routeur s’occupe de la navigation interne.</span><span class="sxs-lookup"><span data-stu-id="ea15d-143">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="ea15d-144">Dans de nombreux scénarios d’hébergement, le chemin URL relatif vers l’application est la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea15d-144">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="ea15d-145">Dans ces cas, le chemin de base relatif`<base href="/" />`de l’URL de Blazor l’application est une barre oblique vers l’avant (), qui est la configuration par défaut pour une application.</span><span class="sxs-lookup"><span data-stu-id="ea15d-145">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="ea15d-146">Dans d’autres scénarios d’hébergement, tels que GitHub Pages et les sous-applications IIS, le chemin de base de l’application doit être défini sur la trajectoire relative d’URL de l’application par le serveur.</span><span class="sxs-lookup"><span data-stu-id="ea15d-146">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="ea15d-147">Pour définir le chemin de base `<base>` de `<head>` l’application, mettez à jour l’étiquette dansBlazor les éléments de balises du fichier *Pages/_Host.cshtml* (Serveur) ou *wwwroot/index.html* fichier (WebAssembly).Blazor</span><span class="sxs-lookup"><span data-stu-id="ea15d-147">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="ea15d-148">Définissez `href` la `/{RELATIVE URL PATH}/` valeur d’attribut à (la barre oblique de fuite est nécessaire), où `{RELATIVE URL PATH}` est le chemin complet de l’APPLICATION URL relative.</span><span class="sxs-lookup"><span data-stu-id="ea15d-148">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="ea15d-149">Pour Blazor une application WebAssembly avec un chemin d’URL relative non-root (par exemple, `<base href="/CoolApp/">`), l’application ne parvient pas à trouver ses ressources *lorsqu’elle est exécutée localement*.</span><span class="sxs-lookup"><span data-stu-id="ea15d-149">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="ea15d-150">Pour surmonter ce problème pendant le développement local et les tests, vous pouvez fournir un argument de *base de chemin* qui correspond à la valeur `href` de la balise `<base>` au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="ea15d-150">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="ea15d-151">N’incluez pas une barre oblique de fuite.</span><span class="sxs-lookup"><span data-stu-id="ea15d-151">Don't include a trailing slash.</span></span> <span data-ttu-id="ea15d-152">Pour passer l’argument de base de chemin `dotnet run` lors de l’exécution de `--pathbase` l’application localement, exécutez la commande à partir de l’annuaire de l’application avec l’option:</span><span class="sxs-lookup"><span data-stu-id="ea15d-152">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="ea15d-153">Pour Blazor une application WebAssembly avec `/CoolApp/` un`<base href="/CoolApp/">`chemin URL relatif de ( ), la commande est:</span><span class="sxs-lookup"><span data-stu-id="ea15d-153">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="ea15d-154">L’application Blazor WebAssembly répond `http://localhost:port/CoolApp`localement à .</span><span class="sxs-lookup"><span data-stu-id="ea15d-154">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="ea15d-155">Déploiement</span><span class="sxs-lookup"><span data-stu-id="ea15d-155">Deployment</span></span>

<span data-ttu-id="ea15d-156">Pour obtenir des conseils de déploiement, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="ea15d-156">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
