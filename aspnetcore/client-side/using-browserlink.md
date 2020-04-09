---
title: Lien de navigateur dans ASP.NET core
author: ncarandini
description: Explique comment Browser Link est une fonctionnalité Visual Studio qui relie l’environnement de développement avec un ou plusieurs navigateurs Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658850"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="621eb-103">Lien de navigateur dans ASP.NET core</span><span class="sxs-lookup"><span data-stu-id="621eb-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="621eb-104">Par [Nicolà Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), et [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="621eb-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="621eb-105">Browser Link est une fonctionnalité Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="621eb-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="621eb-106">Il crée un canal de communication entre l’environnement de développement et un ou plusieurs navigateurs Web.</span><span class="sxs-lookup"><span data-stu-id="621eb-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="621eb-107">Vous pouvez utiliser Browser Link pour actualiser votre application web dans plusieurs navigateurs à la fois, ce qui est utile pour les tests inter-navigateurs.</span><span class="sxs-lookup"><span data-stu-id="621eb-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="621eb-108">Configuration de lien de navigateur</span><span class="sxs-lookup"><span data-stu-id="621eb-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="621eb-109">Ajoutez le package [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) à votre projet.</span><span class="sxs-lookup"><span data-stu-id="621eb-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="621eb-110">Pour ASP.NET core Razor Pages ou projets MVC, permettent également la compilation en temps <xref:mvc/views/view-compilation>d’exécution des fichiers Razor (*.cshtml)* tel que décrit dans .</span><span class="sxs-lookup"><span data-stu-id="621eb-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="621eb-111">Les modifications de la syntaxe razor ne sont appliquées que lorsque la compilation en temps d’exécution a été activée.</span><span class="sxs-lookup"><span data-stu-id="621eb-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="621eb-112">Lors de la conversion d’un projet core 2.0 ASP.NET pour ASP.NET Core 2.1 et la transition vers le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), installez le package [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) pour la fonctionnalité Browser Link.</span><span class="sxs-lookup"><span data-stu-id="621eb-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="621eb-113">Les modèles de projet ASP.NET Core 2.1 utilisent le `Microsoft.AspNetCore.App` métapackage par défaut.</span><span class="sxs-lookup"><span data-stu-id="621eb-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="621eb-114">Le ASP.NET Core 2.0 **Web Application**, **Vide**, et **Web API** modèles de projet utilisent le [métapackage Microsoft.AspNetCore.All](xref:fundamentals/metapackage), qui contient une référence paquet pour [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="621eb-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="621eb-115">Par conséquent, `Microsoft.AspNetCore.All` l’utilisation du métapackage ne nécessite aucune autre action pour rendre Browser Link disponible pour une utilisation.</span><span class="sxs-lookup"><span data-stu-id="621eb-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="621eb-116">Le modèle ASP.NET projet **d’application Web** Core 1.x a une référence de paquet pour le paquet [Microsoft.VisualStudio.Web.BrowserLink.](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)</span><span class="sxs-lookup"><span data-stu-id="621eb-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="621eb-117">D’autres types de projet vous `Microsoft.VisualStudio.Web.BrowserLink`obligent à ajouter une référence globale à .</span><span class="sxs-lookup"><span data-stu-id="621eb-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="621eb-118">Configuration</span><span class="sxs-lookup"><span data-stu-id="621eb-118">Configuration</span></span>

<span data-ttu-id="621eb-119">Appelez `UseBrowserLink` dans la méthode `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="621eb-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="621eb-120">L’appel `UseBrowserLink` est généralement `if` placé à l’intérieur d’un bloc qui ne permet browser Link dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="621eb-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="621eb-121">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="621eb-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="621eb-122">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="621eb-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="621eb-123">Comment utiliser Browser Link</span><span class="sxs-lookup"><span data-stu-id="621eb-123">How to use Browser Link</span></span>

<span data-ttu-id="621eb-124">Lorsque vous avez un projet ASP.NET Core ouvert, Visual Studio affiche le contrôle de la barre d’outils Browser Link à côté du contrôle de la barre d’outils **Debug Target** :</span><span class="sxs-lookup"><span data-stu-id="621eb-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Menu déroulant Browser Link](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="621eb-126">À partir du contrôle de la barre d’outils Browser Link, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="621eb-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="621eb-127">Rafraîchissez l’application web dans plusieurs navigateurs à la fois.</span><span class="sxs-lookup"><span data-stu-id="621eb-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="621eb-128">Ouvrez le **tableau de bord de lien de navigateur**.</span><span class="sxs-lookup"><span data-stu-id="621eb-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="621eb-129">Activez ou désactivez **Browser Link**.</span><span class="sxs-lookup"><span data-stu-id="621eb-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="621eb-130">Remarque : Browser Link est désactivé par défaut dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="621eb-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="621eb-131">Activez ou désactivez [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="621eb-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="621eb-132">Rafraîchir l’application web dans plusieurs navigateurs à la fois</span><span class="sxs-lookup"><span data-stu-id="621eb-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="621eb-133">Pour choisir un seul navigateur Web pour lancer lors du démarrage du projet, utilisez le menu déroulant dans le contrôle de la barre d’outils **Debug Target** :</span><span class="sxs-lookup"><span data-stu-id="621eb-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Menu Déroulant F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="621eb-135">Pour ouvrir plusieurs navigateurs à la fois, choisissez **Browse avec...** à partir de la même baisse.</span><span class="sxs-lookup"><span data-stu-id="621eb-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="621eb-136">Maintenez la clé <kbd>Ctrl</kbd> pour sélectionner les navigateurs que vous voulez, puis cliquez sur **Parcourir**:</span><span class="sxs-lookup"><span data-stu-id="621eb-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![Ouvrez de nombreux navigateurs à la fois](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="621eb-138">La capture d’écran suivante montre Visual Studio avec la vue Index ouverte et deux navigateurs ouverts:</span><span class="sxs-lookup"><span data-stu-id="621eb-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Synchroniser avec deux navigateurs exemple](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="621eb-140">Survolez le contrôle de la barre d’outils Browser Link pour voir les navigateurs connectés au projet :</span><span class="sxs-lookup"><span data-stu-id="621eb-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Pointe de vol stationnaire](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="621eb-142">Modifiez la vue d’index, et tous les navigateurs connectés sont mis à jour lorsque vous cliquez sur le bouton de rafraîchissement de Browser Link :</span><span class="sxs-lookup"><span data-stu-id="621eb-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![navigateurs-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="621eb-144">Browser Link fonctionne également avec les navigateurs que vous lancez de l’extérieur de Visual Studio et naviguez vers l’URL de l’application.</span><span class="sxs-lookup"><span data-stu-id="621eb-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="621eb-145">Le tableau de bord de lien de navigateur</span><span class="sxs-lookup"><span data-stu-id="621eb-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="621eb-146">Ouvrez la fenêtre de tableau de **bord de lien** de navigateur à partir du menu de chute de Browser Link pour gérer la connexion avec les navigateurs ouverts :</span><span class="sxs-lookup"><span data-stu-id="621eb-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![tableau de bord open-browserslink](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="621eb-148">Si aucun navigateur n’est connecté, vous pouvez démarrer une session de non-débugging en sélectionnant le lien **View in Browser** :</span><span class="sxs-lookup"><span data-stu-id="621eb-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="621eb-150">Sinon, les navigateurs connectés sont affichés avec le chemin vers la page que chaque navigateur affiche:</span><span class="sxs-lookup"><span data-stu-id="621eb-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="621eb-152">Vous pouvez également cliquer sur un nom de navigateur individuel pour actualiser uniquement ce navigateur.</span><span class="sxs-lookup"><span data-stu-id="621eb-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="621eb-153">Activez ou désactivez le lien de navigateur</span><span class="sxs-lookup"><span data-stu-id="621eb-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="621eb-154">Lorsque vous réactivez Browser Link après l’avoir désactivé, vous devez actualiser les navigateurs pour les reconnecter.</span><span class="sxs-lookup"><span data-stu-id="621eb-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="621eb-155">Activez ou désactivez CSS Auto-Sync</span><span class="sxs-lookup"><span data-stu-id="621eb-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="621eb-156">Lorsque CSS Auto-Sync est activé, les navigateurs connectés sont automatiquement actualisés lorsque vous modifiez les fichiers CSS.</span><span class="sxs-lookup"><span data-stu-id="621eb-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="621eb-157">Fonctionnement</span><span class="sxs-lookup"><span data-stu-id="621eb-157">How it works</span></span>

<span data-ttu-id="621eb-158">Browser Link [SignalR](xref:signalr/introduction) utilise pour créer un canal de communication entre Visual Studio et le navigateur.</span><span class="sxs-lookup"><span data-stu-id="621eb-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="621eb-159">Lorsque Browser Link est activé, SignalR Visual Studio agit comme un serveur que plusieurs clients (navigateurs) peuvent se connecter.</span><span class="sxs-lookup"><span data-stu-id="621eb-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="621eb-160">Browser Link enregistre également un composant middleware dans le pipeline de la demande ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="621eb-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="621eb-161">Ce composant injecte des références spéciales `<script>` dans chaque demande de page du serveur.</span><span class="sxs-lookup"><span data-stu-id="621eb-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="621eb-162">Vous pouvez voir les références de script en sélectionnant la source `<body>` De **vue** dans le navigateur et en faisant défiler jusqu’à la fin du contenu de l’étiquette :</span><span class="sxs-lookup"><span data-stu-id="621eb-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="621eb-163">Vos fichiers sources ne sont pas modifiés.</span><span class="sxs-lookup"><span data-stu-id="621eb-163">Your source files aren't modified.</span></span> <span data-ttu-id="621eb-164">Le composant middleware injecte dynamiquement les références de script.</span><span class="sxs-lookup"><span data-stu-id="621eb-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="621eb-165">Parce que le code côté navigateur est tout JavaScript, il fonctionne sur tous les navigateurs qui SignalR prend en charge sans nécessiter un plug-in navigateur.</span><span class="sxs-lookup"><span data-stu-id="621eb-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
