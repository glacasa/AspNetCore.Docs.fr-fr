---
title: Gérer les erreurs dans Blazor ASP.NET applications Core
author: guardrex
description: Découvrez comment ASP.NET Core Blazor Blazor comment gère les exceptions non gérées et comment développer des applications qui détectent et manipulent les erreurs.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 4fdaf7fb90d126b8f7f029aac3af49eec3b69e74
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80382273"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a><span data-ttu-id="26be8-103">Gérer les erreurs dans Blazor ASP.NET applications Core</span><span class="sxs-lookup"><span data-stu-id="26be8-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="26be8-104">Par [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="26be8-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="26be8-105">Cet article décrit Blazor comment gère les exceptions non gérées et comment développer des applications qui détectent et manipulent les erreurs.</span><span class="sxs-lookup"><span data-stu-id="26be8-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="26be8-106">Erreurs détaillées pendant le développement</span><span class="sxs-lookup"><span data-stu-id="26be8-106">Detailed errors during development</span></span>

<span data-ttu-id="26be8-107">Lorsqu’une Blazor application ne fonctionne pas correctement pendant le développement, la réception d’informations d’erreur détaillées de l’application aide à dépanner et à résoudre le problème.</span><span class="sxs-lookup"><span data-stu-id="26be8-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="26be8-108">Lorsqu’une erreur Blazor se produit, les applications affichent une barre d’or au bas de l’écran :</span><span class="sxs-lookup"><span data-stu-id="26be8-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="26be8-109">Pendant le développement, la barre d’or vous dirige vers la console du navigateur, où vous pouvez voir l’exception.</span><span class="sxs-lookup"><span data-stu-id="26be8-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="26be8-110">En production, la barre d’or informe l’utilisateur qu’une erreur s’est produite et recommande de rafraîchir le navigateur.</span><span class="sxs-lookup"><span data-stu-id="26be8-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="26be8-111">L’interface utilisateur pour cette expérience Blazor de traitement des erreurs fait partie des modèles de projet.</span><span class="sxs-lookup"><span data-stu-id="26be8-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="26be8-112">Dans Blazor une application WebAssembly, personnalisez l’expérience dans le fichier *wwwroot/index.html* :</span><span class="sxs-lookup"><span data-stu-id="26be8-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="26be8-113">Dans Blazor une application Server, personnalisez l’expérience dans le fichier *Pages/_Host.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="26be8-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="26be8-114">L’élément `blazor-error-ui` est caché par Blazor les styles inclus dans les modèles *(wwwroot/css/site.css*) et ensuite montré quand une erreur se produit:</span><span class="sxs-lookup"><span data-stu-id="26be8-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (*wwwroot/css/site.css*) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="26be8-115">Comment Blazor une application Server réagit aux exceptions non gérées</span><span class="sxs-lookup"><span data-stu-id="26be8-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="26be8-116">Le serveur est un cadre majestueux.</span><span class="sxs-lookup"><span data-stu-id="26be8-116"> Server is a stateful framework.</span></span> <span data-ttu-id="26be8-117">Alors que les utilisateurs interagissent avec une application, ils maintiennent une connexion au serveur connu sous le nom de *circuit*.</span><span class="sxs-lookup"><span data-stu-id="26be8-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="26be8-118">Le circuit contient des instances de composante active, ainsi que de nombreux autres aspects de l’état, tels que:</span><span class="sxs-lookup"><span data-stu-id="26be8-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="26be8-119">La production la plus récente rendue des composants.</span><span class="sxs-lookup"><span data-stu-id="26be8-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="26be8-120">L’ensemble actuel de délégués chargés de la gestion des événements qui pourraient être déclenchés par des événements côté client.</span><span class="sxs-lookup"><span data-stu-id="26be8-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="26be8-121">Si un utilisateur ouvre l’application dans plusieurs onglets de navigateur, il dispose de plusieurs circuits indépendants.</span><span class="sxs-lookup"><span data-stu-id="26be8-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="26be8-122">traite la plupart des exceptions non manipulées comme fatales au circuit où elles se produisent.</span><span class="sxs-lookup"><span data-stu-id="26be8-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="26be8-123">Si un circuit est terminé en raison d’une exception non gérée, l’utilisateur ne peut continuer à interagir avec l’application qu’en rechargeant la page pour créer un nouveau circuit.</span><span class="sxs-lookup"><span data-stu-id="26be8-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="26be8-124">Les circuits en dehors de celui qui est terminé, qui sont des circuits pour d’autres utilisateurs ou d’autres onglets de navigateur, ne sont pas affectés.</span><span class="sxs-lookup"><span data-stu-id="26be8-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="26be8-125">Ce scénario est similaire à&mdash;une application de bureau qui bloque l’application écrasée doit être redémarré, mais d’autres applications ne sont pas affectées.</span><span class="sxs-lookup"><span data-stu-id="26be8-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="26be8-126">Un circuit est terminé lorsqu’une exception non gérée se produit pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="26be8-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="26be8-127">Une exception non gérée laisse souvent le circuit dans un état indéfini.</span><span class="sxs-lookup"><span data-stu-id="26be8-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="26be8-128">Le fonctionnement normal de l’application ne peut pas être garanti après une exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="26be8-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="26be8-129">Les failles de sécurité peuvent apparaître dans l’application si le circuit se poursuit.</span><span class="sxs-lookup"><span data-stu-id="26be8-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="26be8-130">Gérer les exceptions non gérées dans le code des développeurs</span><span class="sxs-lookup"><span data-stu-id="26be8-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="26be8-131">Pour qu’une application se poursuive après une erreur, l’application doit avoir une logique de manipulation d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="26be8-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="26be8-132">Les sections ultérieures de cet article décrivent des sources potentielles d’exceptions non manipulées.</span><span class="sxs-lookup"><span data-stu-id="26be8-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="26be8-133">En production, ne pas rendre de messages d’exception de cadre ou empiler des traces dans l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="26be8-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="26be8-134">Le rendu des messages d’exception ou des traces d’empilage pourrait :</span><span class="sxs-lookup"><span data-stu-id="26be8-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="26be8-135">Divulguer des informations sensibles aux utilisateurs finaux.</span><span class="sxs-lookup"><span data-stu-id="26be8-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="26be8-136">Aidez un utilisateur malveillant à découvrir les faiblesses d’une application qui peut compromettre la sécurité de l’application, du serveur ou du réseau.</span><span class="sxs-lookup"><span data-stu-id="26be8-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="26be8-137">Erreurs de connexion avec un fournisseur persistant</span><span class="sxs-lookup"><span data-stu-id="26be8-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="26be8-138">Si une exception non gérée se produit, <xref:Microsoft.Extensions.Logging.ILogger> l’exception est enregistrée dans les instances configurées dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="26be8-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="26be8-139">Par défaut, Blazor les applications se connectent pour consoler la sortie avec le fournisseur d’enregistrement de console.</span><span class="sxs-lookup"><span data-stu-id="26be8-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="26be8-140">Envisagez de vous connecter à un endroit plus permanent avec un fournisseur qui gère la taille du journal et la rotation des journaux.</span><span class="sxs-lookup"><span data-stu-id="26be8-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="26be8-141">Pour plus d’informations, consultez <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="26be8-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="26be8-142">Pendant le Blazor développement, envoie généralement tous les détails des exceptions à la console du navigateur pour aider à débogage.</span><span class="sxs-lookup"><span data-stu-id="26be8-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="26be8-143">Dans la production, les erreurs détaillées dans la console du navigateur sont désactivées par défaut, ce qui signifie que les erreurs ne sont pas envoyées aux clients, mais les détails complets de l’exception sont toujours connectés côté serveur.</span><span class="sxs-lookup"><span data-stu-id="26be8-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="26be8-144">Pour plus d’informations, consultez <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="26be8-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="26be8-145">Vous devez décider quels incidents enregistrer et le niveau de gravité des incidents enregistrés.</span><span class="sxs-lookup"><span data-stu-id="26be8-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="26be8-146">Les utilisateurs hostiles pourraient être en mesure de déclencher des erreurs délibérément.</span><span class="sxs-lookup"><span data-stu-id="26be8-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="26be8-147">Par exemple, ne connectez pas un incident `ProductId` à partir d’une erreur où une inconnue est fournie dans l’URL d’un composant qui affiche les détails du produit.</span><span class="sxs-lookup"><span data-stu-id="26be8-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="26be8-148">Toutes les erreurs ne doivent pas être traitées comme des incidents de haute gravité pour l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="26be8-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="26be8-149">Lieux où des erreurs peuvent se produire</span><span class="sxs-lookup"><span data-stu-id="26be8-149">Places where errors may occur</span></span>

<span data-ttu-id="26be8-150">Le code cadre et le code d’application peuvent déclencher des exceptions non gérées dans l’un des endroits suivants :</span><span class="sxs-lookup"><span data-stu-id="26be8-150">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="26be8-151">Instantanéisation des composants</span><span class="sxs-lookup"><span data-stu-id="26be8-151">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="26be8-152">Méthodes de cycle de vie</span><span class="sxs-lookup"><span data-stu-id="26be8-152">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="26be8-153">Logique de rendu</span><span class="sxs-lookup"><span data-stu-id="26be8-153">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="26be8-154">Gestionnaires d’événements</span><span class="sxs-lookup"><span data-stu-id="26be8-154">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="26be8-155">Élimination des composants</span><span class="sxs-lookup"><span data-stu-id="26be8-155">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="26be8-156">Interopérabilité JavaScript</span><span class="sxs-lookup"><span data-stu-id="26be8-156">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="26be8-157">[BlazorRerendering serveur](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="26be8-157">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="26be8-158">Les exceptions non gérées précédentes sont décrites dans les sections suivantes de cet article.</span><span class="sxs-lookup"><span data-stu-id="26be8-158">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="26be8-159">Instantanéisation des composants</span><span class="sxs-lookup"><span data-stu-id="26be8-159">Component instantiation</span></span>

<span data-ttu-id="26be8-160">Lorsque Blazor crée une instance d’un composant:</span><span class="sxs-lookup"><span data-stu-id="26be8-160">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="26be8-161">Le constructeur du composant est invoqué.</span><span class="sxs-lookup"><span data-stu-id="26be8-161">The component's constructor is invoked.</span></span>
* <span data-ttu-id="26be8-162">Les constructeurs de tous les services DI non-singleton fournis [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) au [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) constructeur du composant par l’intermédiaire de la directive ou de l’attribut sont invoqués.</span><span class="sxs-lookup"><span data-stu-id="26be8-162">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="26be8-163">Un Blazor circuit Server échoue lorsque tout constructeur exécuté `[Inject]` ou un setter pour toute propriété jette une exception non manipulée.</span><span class="sxs-lookup"><span data-stu-id="26be8-163">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="26be8-164">L’exception est fatale parce que le cadre ne peut pas instantané le composant.</span><span class="sxs-lookup"><span data-stu-id="26be8-164">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="26be8-165">Si la logique du constructeur peut jeter des exceptions, l’application doit piéger les exceptions à l’aide [d’une déclaration try-catch](/dotnet/csharp/language-reference/keywords/try-catch) avec la manipulation des erreurs et la connexion.</span><span class="sxs-lookup"><span data-stu-id="26be8-165">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="26be8-166">Méthodes de cycle de vie</span><span class="sxs-lookup"><span data-stu-id="26be8-166">Lifecycle methods</span></span>

<span data-ttu-id="26be8-167">Pendant la durée de Blazor vie d’un composant, invoque les méthodes de [cycle de vie](xref:blazor/lifecycle)suivantes :</span><span class="sxs-lookup"><span data-stu-id="26be8-167">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="26be8-168">Si une méthode de cycle de vie jette une exception, de façon synchrone ou asynchrone, l’exception est fatale à un Blazor circuit Server.</span><span class="sxs-lookup"><span data-stu-id="26be8-168">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="26be8-169">Pour que les composants traitent des erreurs dans les méthodes du cycle de vie, ajoutez la logique de gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="26be8-169">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="26be8-170">Dans l’exemple `OnParametersSetAsync` suivant où appelle une méthode pour obtenir un produit :</span><span class="sxs-lookup"><span data-stu-id="26be8-170">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="26be8-171">Une exception jetée `ProductRepository.GetProductByIdAsync` dans la méthode `try-catch` est traitée par une déclaration.</span><span class="sxs-lookup"><span data-stu-id="26be8-171">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="26be8-172">Lorsque `catch` le bloc est exécuté :</span><span class="sxs-lookup"><span data-stu-id="26be8-172">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="26be8-173">`loadFailed`est configuré à `true`, qui est utilisé pour afficher un message d’erreur à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="26be8-173">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="26be8-174">L’erreur est enregistrée.</span><span class="sxs-lookup"><span data-stu-id="26be8-174">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="26be8-175">Logique de rendu</span><span class="sxs-lookup"><span data-stu-id="26be8-175">Rendering logic</span></span>

<span data-ttu-id="26be8-176">La majoration déclarative `.razor` dans un fichier de composant `BuildRenderTree`est compilée dans une méthode C appelée .</span><span class="sxs-lookup"><span data-stu-id="26be8-176">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="26be8-177">Lorsqu’un composant `BuildRenderTree` rend, exécute et construit une structure de données décrivant les éléments, le texte et les composants pour enfants du composant rendu.</span><span class="sxs-lookup"><span data-stu-id="26be8-177">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="26be8-178">La logique de rendu peut jeter une exception.</span><span class="sxs-lookup"><span data-stu-id="26be8-178">Rendering logic can throw an exception.</span></span> <span data-ttu-id="26be8-179">Un exemple de ce `@someObject.PropertyName` scénario se `@someObject` `null`produit lorsque vous êtes évalué, mais est .</span><span class="sxs-lookup"><span data-stu-id="26be8-179">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="26be8-180">Une exception non manipulée lancée par la Blazor logique de rendu est fatale à un circuit Server.</span><span class="sxs-lookup"><span data-stu-id="26be8-180">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="26be8-181">Pour éviter une exception de référence nulle `null` dans le rendu logique, vérifiez un objet avant d’accéder à ses membres.</span><span class="sxs-lookup"><span data-stu-id="26be8-181">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="26be8-182">Dans l’exemple `person.Address` suivant, les propriétés ne sont pas accessibles si `person.Address` c’est `null`:</span><span class="sxs-lookup"><span data-stu-id="26be8-182">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="26be8-183">Le code précédent `person` suppose que `null`n’est pas .</span><span class="sxs-lookup"><span data-stu-id="26be8-183">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="26be8-184">Souvent, la structure du code garantit l’existence d’un objet au moment où le composant est rendu.</span><span class="sxs-lookup"><span data-stu-id="26be8-184">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="26be8-185">Dans ces cas, il n’est `null` pas nécessaire de vérifier dans la logique de rendu.</span><span class="sxs-lookup"><span data-stu-id="26be8-185">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="26be8-186">Dans l’exemple `person` précédent, peut `person` être garanti d’exister parce qu’il est créé lorsque le composant est instantané.</span><span class="sxs-lookup"><span data-stu-id="26be8-186">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="26be8-187">Gestionnaires d’événements</span><span class="sxs-lookup"><span data-stu-id="26be8-187">Event handlers</span></span>

<span data-ttu-id="26be8-188">Le code côté client déclenche des invocations de code CMD lorsque les gestionnaires d’événements sont créés à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="26be8-188">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="26be8-189">Autres `@on...` attributs</span><span class="sxs-lookup"><span data-stu-id="26be8-189">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="26be8-190">Le code de gestionnaire d’événements peut jeter une exception non manipulée dans ces scénarios.</span><span class="sxs-lookup"><span data-stu-id="26be8-190">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="26be8-191">Si un gestionnaire d’événements lance une exception non gérée (par exemple, une Blazor requête de base de données échoue), l’exception est fatale à un circuit Server.</span><span class="sxs-lookup"><span data-stu-id="26be8-191">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="26be8-192">Si l’application appelle le code qui pourrait échouer pour des raisons externes, piègez les exceptions à l’aide [d’une déclaration try-catch](/dotnet/csharp/language-reference/keywords/try-catch) avec manipulation d’erreurs et enregistrement.</span><span class="sxs-lookup"><span data-stu-id="26be8-192">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="26be8-193">Si le code utilisateur ne piège pas et ne gère pas l’exception, le cadre enregistre l’exception et met fin au circuit.</span><span class="sxs-lookup"><span data-stu-id="26be8-193">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="26be8-194">Élimination des composants</span><span class="sxs-lookup"><span data-stu-id="26be8-194">Component disposal</span></span>

<span data-ttu-id="26be8-195">Un composant peut être supprimé de l’interface utilisateur, par exemple, parce que l’utilisateur a navigué vers une autre page.</span><span class="sxs-lookup"><span data-stu-id="26be8-195">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="26be8-196">Lorsqu’un composant <xref:System.IDisposable?displayProperty=fullName> qui implémente est supprimé de l’interface utilisateur, le cadre appelle la méthode du <xref:System.IDisposable.Dispose*> composant.</span><span class="sxs-lookup"><span data-stu-id="26be8-196">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span>

<span data-ttu-id="26be8-197">Si la méthode `Dispose` du composant jette une exception non manipulée, l’exception est fatale à un Blazor circuit serveur.</span><span class="sxs-lookup"><span data-stu-id="26be8-197">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="26be8-198">Si la logique d’élimination peut jeter des exceptions, l’application doit piéger les exceptions à l’aide [d’une déclaration try-catch](/dotnet/csharp/language-reference/keywords/try-catch) avec la manipulation des erreurs et la connexion.</span><span class="sxs-lookup"><span data-stu-id="26be8-198">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="26be8-199">Pour plus d’informations <xref:blazor/lifecycle#component-disposal-with-idisposable>sur l’élimination des composants, voir .</span><span class="sxs-lookup"><span data-stu-id="26be8-199">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="26be8-200">Interopérabilité JavaScript</span><span class="sxs-lookup"><span data-stu-id="26be8-200">JavaScript interop</span></span>

<span data-ttu-id="26be8-201">`IJSRuntime.InvokeAsync<T>`permet au code .NET de passer des appels asynchrones vers l’heure d’exécution JavaScript dans le navigateur de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="26be8-201">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="26be8-202">Les conditions suivantes s’appliquent au traitement des erreurs avec `InvokeAsync<T>`:</span><span class="sxs-lookup"><span data-stu-id="26be8-202">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="26be8-203">Si un `InvokeAsync<T>` appel échoue de façon synchrone, une exception .NET se produit.</span><span class="sxs-lookup"><span data-stu-id="26be8-203">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="26be8-204">Un appel `InvokeAsync<T>` à peut échouer, par exemple, parce que les arguments fournis ne peuvent pas être sérialisés.</span><span class="sxs-lookup"><span data-stu-id="26be8-204">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="26be8-205">Le code des développeurs doit saisir l’exception.</span><span class="sxs-lookup"><span data-stu-id="26be8-205">Developer code must catch the exception.</span></span> <span data-ttu-id="26be8-206">Si le code d’application dans une méthode de gestionnaire d’événements ou Blazor de cycle de vie des composants ne gère pas une exception, l’exception qui en résulte est fatale à un circuit serveur.</span><span class="sxs-lookup"><span data-stu-id="26be8-206">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="26be8-207">Si un `InvokeAsync<T>` appel échoue asynchronement, <xref:System.Threading.Tasks.Task> le .NET échoue.</span><span class="sxs-lookup"><span data-stu-id="26be8-207">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="26be8-208">Un appel `InvokeAsync<T>` peut échouer, par exemple, parce que le code côté `Promise` JavaScript `rejected`jette une exception ou renvoie un qui a terminé comme .</span><span class="sxs-lookup"><span data-stu-id="26be8-208">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="26be8-209">Le code des développeurs doit saisir l’exception.</span><span class="sxs-lookup"><span data-stu-id="26be8-209">Developer code must catch the exception.</span></span> <span data-ttu-id="26be8-210">Si vous utilisez [l’opérateur d’attente,](/dotnet/csharp/language-reference/keywords/await) envisagez d’envelopper l’appel de méthode dans un énoncé [d’essai-capture](/dotnet/csharp/language-reference/keywords/try-catch) avec la manipulation d’erreur et la connexion.</span><span class="sxs-lookup"><span data-stu-id="26be8-210">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="26be8-211">Dans le cas contraire, le code défaillant entraîne une Blazor exception non manipulée qui est fatale à un circuit Server.</span><span class="sxs-lookup"><span data-stu-id="26be8-211">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="26be8-212">Par défaut, `InvokeAsync<T>` les appels doivent être effectués dans un certain délai, sinon les heures d’appel sont terminées. La période de délai d’attente par défaut est d’une minute.</span><span class="sxs-lookup"><span data-stu-id="26be8-212">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="26be8-213">Le délai d’attente protège le code contre une perte de connectivité réseau ou de code JavaScript qui ne renvoie jamais un message d’achèvement.</span><span class="sxs-lookup"><span data-stu-id="26be8-213">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="26be8-214">Si les temps d’appel, le résultat `Task` échoue avec un <xref:System.OperationCanceledException>.</span><span class="sxs-lookup"><span data-stu-id="26be8-214">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="26be8-215">Pièger et traiter l’exception par l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="26be8-215">Trap and process the exception with logging.</span></span>

<span data-ttu-id="26be8-216">De même, le code JavaScript peut initier [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) des appels vers des méthodes .NET indiquées par l’attribut.</span><span class="sxs-lookup"><span data-stu-id="26be8-216">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="26be8-217">Si ces méthodes .NET jettent une exception non manipulée :</span><span class="sxs-lookup"><span data-stu-id="26be8-217">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="26be8-218">L’exception n’est pas considérée Blazor comme fatale à un circuit Server.</span><span class="sxs-lookup"><span data-stu-id="26be8-218">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="26be8-219">Le côté `Promise` JavaScript est rejeté.</span><span class="sxs-lookup"><span data-stu-id="26be8-219">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="26be8-220">Vous avez la possibilité d’utiliser le code de traitement des erreurs soit sur le côté .NET ou le côté JavaScript de l’appel de méthode.</span><span class="sxs-lookup"><span data-stu-id="26be8-220">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="26be8-221">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="26be8-221">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>Blazor<span data-ttu-id="26be8-222">Prerendering serveur</span><span class="sxs-lookup"><span data-stu-id="26be8-222"> Server prerendering</span></span>

Blazor<span data-ttu-id="26be8-223">les composants peuvent être prédiliés à l’aide [de l’aide à l’étiquette de composant](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) de sorte que leur balisage HTML rendu soit retourné dans le cadre de la demande initiale de HTTP de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="26be8-223"> components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="26be8-224">Cela fonctionne par:</span><span class="sxs-lookup"><span data-stu-id="26be8-224">This works by:</span></span>

* <span data-ttu-id="26be8-225">Création d’un nouveau circuit pour tous les composants préditeurs qui font partie de la même page.</span><span class="sxs-lookup"><span data-stu-id="26be8-225">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="26be8-226">Génération du HTML initial.</span><span class="sxs-lookup"><span data-stu-id="26be8-226">Generating the initial HTML.</span></span>
* <span data-ttu-id="26be8-227">Traiter le `disconnected` circuit comme jusqu’à ce SignalR que le navigateur de l’utilisateur établisse une connexion vers le même serveur.</span><span class="sxs-lookup"><span data-stu-id="26be8-227">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="26be8-228">Lorsque la connexion est établie, l’interactivité sur le circuit est reprise et la balisage HTML des composants est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="26be8-228">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="26be8-229">Si un composant jette une exception non manipulée pendant le prerendering, par exemple, pendant une méthode de cycle de vie ou dans la logique de rendu :</span><span class="sxs-lookup"><span data-stu-id="26be8-229">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="26be8-230">L’exception est fatale au circuit.</span><span class="sxs-lookup"><span data-stu-id="26be8-230">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="26be8-231">L’exception est jetée vers `Component` le haut de la pile d’appel de l’aide tag.</span><span class="sxs-lookup"><span data-stu-id="26be8-231">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="26be8-232">Par conséquent, l’ensemble de la demande HTTP échoue à moins que l’exception ne soit explicitement prise par le code développeur.</span><span class="sxs-lookup"><span data-stu-id="26be8-232">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="26be8-233">Dans des circonstances normales lorsque le pré-processus échoue, continuer à construire et à rendre le composant n’a pas de sens parce qu’un composant de travail ne peut pas être rendu.</span><span class="sxs-lookup"><span data-stu-id="26be8-233">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="26be8-234">Pour tolérer les erreurs qui peuvent se produire pendant le prérendering, la logique de manipulation d’erreur doit être placée à l’intérieur d’un composant qui peut jeter des exceptions.</span><span class="sxs-lookup"><span data-stu-id="26be8-234">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="26be8-235">Utilisez des instructions [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) avec la manipulation des erreurs et la connexion.</span><span class="sxs-lookup"><span data-stu-id="26be8-235">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="26be8-236">Au lieu `Component` d’envelopper `try-catch` l’aide Tag dans une déclaration, `Component` placez la logique de manipulation d’erreur dans le composant rendu par l’aide Tag.</span><span class="sxs-lookup"><span data-stu-id="26be8-236">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="26be8-237">Scénarios avancés</span><span class="sxs-lookup"><span data-stu-id="26be8-237">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="26be8-238">Rendu récursif</span><span class="sxs-lookup"><span data-stu-id="26be8-238">Recursive rendering</span></span>

<span data-ttu-id="26be8-239">Les composants peuvent être imbriqués de façon récursive.</span><span class="sxs-lookup"><span data-stu-id="26be8-239">Components can be nested recursively.</span></span> <span data-ttu-id="26be8-240">Ceci est utile pour représenter les structures de données récursives.</span><span class="sxs-lookup"><span data-stu-id="26be8-240">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="26be8-241">Par exemple, `TreeNode` un composant `TreeNode` peut rendre plus de composants pour chacun des enfants du nœud.</span><span class="sxs-lookup"><span data-stu-id="26be8-241">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="26be8-242">Lors du rendu récursivement, évitez les modèles de codage qui se traduisent par une récursion infinie :</span><span class="sxs-lookup"><span data-stu-id="26be8-242">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="26be8-243">Ne récursez pas une structure de données qui contient un cycle.</span><span class="sxs-lookup"><span data-stu-id="26be8-243">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="26be8-244">Par exemple, ne faites pas un nœud d’arbre dont les enfants s’incluent.</span><span class="sxs-lookup"><span data-stu-id="26be8-244">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="26be8-245">Ne créez pas une chaîne de mises en page qui contiennent un cycle.</span><span class="sxs-lookup"><span data-stu-id="26be8-245">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="26be8-246">Par exemple, ne créez pas de mise en page dont la mise en page est elle-même.</span><span class="sxs-lookup"><span data-stu-id="26be8-246">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="26be8-247">Ne laissez pas un utilisateur final violer les invariants de récursion (règles) par le biais d’appels malveillants de données ou d’interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="26be8-247">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="26be8-248">Boucles infinies pendant le rendu :</span><span class="sxs-lookup"><span data-stu-id="26be8-248">Infinite loops during rendering:</span></span>

* <span data-ttu-id="26be8-249">Provoque le processus de rendu de continuer pour toujours.</span><span class="sxs-lookup"><span data-stu-id="26be8-249">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="26be8-250">Équivaut à créer une boucle indéterminée.</span><span class="sxs-lookup"><span data-stu-id="26be8-250">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="26be8-251">Dans ces scénarios, Blazor un circuit serveur affecté échoue, et le thread tente généralement de :</span><span class="sxs-lookup"><span data-stu-id="26be8-251">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="26be8-252">Consommez autant de temps de processeur que le système d’exploitation, indéfiniment.</span><span class="sxs-lookup"><span data-stu-id="26be8-252">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="26be8-253">Consommez une quantité illimitée de mémoire du serveur.</span><span class="sxs-lookup"><span data-stu-id="26be8-253">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="26be8-254">Consommer une mémoire illimitée équivaut au scénario où une boucle indéterminée ajoute des entrées à une collection sur chaque itération.</span><span class="sxs-lookup"><span data-stu-id="26be8-254">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="26be8-255">Pour éviter les modèles de récursion infinis, assurez-vous que le code de rendu récursif contient des conditions d’arrêt appropriées.</span><span class="sxs-lookup"><span data-stu-id="26be8-255">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="26be8-256">Logique d’arbre de rendu personnalisée</span><span class="sxs-lookup"><span data-stu-id="26be8-256">Custom render tree logic</span></span>

<span data-ttu-id="26be8-257">La Blazor plupart des composants sont implémentés comme des `RenderTreeBuilder` fichiers *.razor* et sont compilés pour produire une logique qui fonctionne sur un pour rendre leur sortie.</span><span class="sxs-lookup"><span data-stu-id="26be8-257">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="26be8-258">Un développeur peut `RenderTreeBuilder` implémenter manuellement la logique à l’aide du code procédural C.</span><span class="sxs-lookup"><span data-stu-id="26be8-258">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="26be8-259">Pour plus d’informations, consultez <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="26be8-259">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="26be8-260">L’utilisation de la logique manuelle de constructeur d’arbres de rendu est considérée comme un scénario avancé et dangereux, non recommandé pour le développement général des composants.</span><span class="sxs-lookup"><span data-stu-id="26be8-260">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="26be8-261">Si `RenderTreeBuilder` le code est écrit, le développeur doit garantir la justesse du code.</span><span class="sxs-lookup"><span data-stu-id="26be8-261">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="26be8-262">Par exemple, le développeur doit s’assurer que :</span><span class="sxs-lookup"><span data-stu-id="26be8-262">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="26be8-263">Appels `OpenElement` vers `CloseElement` et sont correctement équilibrés.</span><span class="sxs-lookup"><span data-stu-id="26be8-263">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="26be8-264">Les attributs ne sont ajoutés qu’aux bons endroits.</span><span class="sxs-lookup"><span data-stu-id="26be8-264">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="26be8-265">La logique incorrecte de fabricant d’arbres de rendu manuel peut causer un comportement arbitraire indéfini, y compris des plantages, des pends de serveur et des vulnérabilités de sécurité.</span><span class="sxs-lookup"><span data-stu-id="26be8-265">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="26be8-266">Considérez la logique manuelle de constructeur d’arbre de rendu sur le même niveau de complexité et avec le même niveau de *danger* que le code d’assemblage d’écriture ou les instructions MSIL à la main.</span><span class="sxs-lookup"><span data-stu-id="26be8-266">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
