---
title: Présentation de ASP.NET CoreBlazor
author: guardrex
description: Explorez ASP.NET Core Blazor , un moyen de créer une interface utilisateur Web interactive côté client avec .net dans une application ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 06/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/index
ms.openlocfilehash: 30f11a137e711b1cf7a8b036af92fbb5fa2a1f05
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402570"
---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="161fa-103">Présentation de ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="161fa-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="161fa-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="161fa-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="161fa-105">*Bienvenue Blazor !*</span><span class="sxs-lookup"><span data-stu-id="161fa-105">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="161fa-106">est une infrastructure permettant de créer une interface utilisateur Web interactive côté client avec .NET :</span><span class="sxs-lookup"><span data-stu-id="161fa-106"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="161fa-107">Créez des interfaces utilisateur interactives riches à l’aide de C# plutôt que JavaScript.</span><span class="sxs-lookup"><span data-stu-id="161fa-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="161fa-108">Partagez la logique d’application côté serveur et côté client écrite dans .NET.</span><span class="sxs-lookup"><span data-stu-id="161fa-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="161fa-109">Affichez l’interface utilisateur en langage HTML et CSS pour une large prise en charge des navigateurs, y compris les navigateurs mobiles.</span><span class="sxs-lookup"><span data-stu-id="161fa-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="161fa-110">Intégrez les plateformes d’hébergement modernes, telles que l' [amarrage](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="161fa-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="161fa-111">L’utilisation de .NET dans le développement web côté client offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="161fa-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="161fa-112">Écriture de code dans C# plutôt que dans JavaScript.</span><span class="sxs-lookup"><span data-stu-id="161fa-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="161fa-113">Tirez parti de l’écosystème .NET existant des bibliothèques .NET.</span><span class="sxs-lookup"><span data-stu-id="161fa-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="161fa-114">Partagez la logique de l’application sur le serveur et le client.</span><span class="sxs-lookup"><span data-stu-id="161fa-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="161fa-115">Bénéficiez des performances, de la fiabilité et de la sécurité de .NET.</span><span class="sxs-lookup"><span data-stu-id="161fa-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="161fa-116">Restez productif grâce à Visual Studio sur Windows, Linux et macOS.</span><span class="sxs-lookup"><span data-stu-id="161fa-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="161fa-117">Développez avec un ensemble commun de langages, de frameworks et d’outils stables, riches en fonctionnalités et faciles à utiliser.</span><span class="sxs-lookup"><span data-stu-id="161fa-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="161fa-118">Components</span><span class="sxs-lookup"><span data-stu-id="161fa-118">Components</span></span>

Blazor<span data-ttu-id="161fa-119">les applications sont basées sur des *composants*.</span><span class="sxs-lookup"><span data-stu-id="161fa-119"> apps are based on *components*.</span></span> <span data-ttu-id="161fa-120">Un composant dans Blazor est un élément de l’interface utilisateur, tel qu’une page, une boîte de dialogue ou un formulaire de saisie de données.</span><span class="sxs-lookup"><span data-stu-id="161fa-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="161fa-121">Les composants sont des classes .NET intégrées dans des assemblys .NET qui :</span><span class="sxs-lookup"><span data-stu-id="161fa-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="161fa-122">Définissent la logique de rendu de l’interface utilisateur flexible.</span><span class="sxs-lookup"><span data-stu-id="161fa-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="161fa-123">Gèrent les événements de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="161fa-123">Handle user events.</span></span>
* <span data-ttu-id="161fa-124">Peuvent être imbriqués et réutilisés.</span><span class="sxs-lookup"><span data-stu-id="161fa-124">Can be nested and reused.</span></span>
* <span data-ttu-id="161fa-125">Peut être partagé et distribué en tant que [ Razor bibliothèques de classes](xref:razor-pages/ui-class) ou [packages NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="161fa-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="161fa-126">La classe Component est généralement écrite sous la forme d’une [Razor](xref:mvc/views/razor) page de balisage avec une `.razor` extension de fichier.</span><span class="sxs-lookup"><span data-stu-id="161fa-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="161fa-127">Les composants dans Blazor sont officiellement désignés sous le terme de \* Razor composants\*.</span><span class="sxs-lookup"><span data-stu-id="161fa-127">Components in Blazor are formally referred to as *Razor components*.</span></span> Razor<span data-ttu-id="161fa-128">est une syntaxe pour combiner le balisage HTML avec du code C# conçu pour la productivité des développeurs.</span><span class="sxs-lookup"><span data-stu-id="161fa-128"> is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> Razor<span data-ttu-id="161fa-129">vous permet de basculer entre le balisage HTML et C# dans le même fichier avec la prise en charge [IntelliSense](/visualstudio/ide/using-intellisense) .</span><span class="sxs-lookup"><span data-stu-id="161fa-129"> allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> Razor<span data-ttu-id="161fa-130">Les pages et MVC utilisent également Razor .</span><span class="sxs-lookup"><span data-stu-id="161fa-130"> Pages and MVC also use Razor.</span></span> <span data-ttu-id="161fa-131">Contrairement aux Razor pages et MVC, qui sont générés autour d’un modèle de demande/réponse, les composants sont utilisés spécifiquement pour la logique et la composition de l’interface utilisateur côté client.</span><span class="sxs-lookup"><span data-stu-id="161fa-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="161fa-132">Le Razor balisage suivant illustre un composant ( `Dialog.razor` ), qui peut être imbriqué dans un autre composant :</span><span class="sxs-lookup"><span data-stu-id="161fa-132">The following Razor markup demonstrates a component (`Dialog.razor`), which can be nested within another component:</span></span>

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

<span data-ttu-id="161fa-133">Le contenu du corps (`ChildContent`) et le titre (`Title`) de la boîte de dialogue sont fournis par le composant qui utilise ce composant dans son interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="161fa-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="161fa-134">`OnYes` est une méthode C# déclenchée par l’événement `onclick` du bouton.</span><span class="sxs-lookup"><span data-stu-id="161fa-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="161fa-135">utilise des balises HTML naturelles pour la composition de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="161fa-135"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="161fa-136">Les éléments HTML spécifient des composants et les attributs d’une balise passent les valeurs aux propriétés d’un composant.</span><span class="sxs-lookup"><span data-stu-id="161fa-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="161fa-137">Dans l’exemple suivant, le composant `Index` utilise le composant `Dialog`.</span><span class="sxs-lookup"><span data-stu-id="161fa-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="161fa-138">`ChildContent` et `Title` sont définis par les attributs et le contenu de l’élément `<Dialog>`.</span><span class="sxs-lookup"><span data-stu-id="161fa-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="161fa-139">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="161fa-139">`Pages/Index.razor`:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="161fa-140">La boîte de dialogue s’affiche lorsque le parent ( `Pages/Index.razor` ) est accessible dans un navigateur :</span><span class="sxs-lookup"><span data-stu-id="161fa-140">The dialog is rendered when the parent (`Pages/Index.razor`) is accessed in a browser:</span></span>

![Composant de boîte de dialogue affiché dans le navigateur](index/_static/dialog.png)

<span data-ttu-id="161fa-142">Quand ce composant est utilisé dans l’application, IntelliSense dans [Visual Studio](/visualstudio/ide/using-intellisense)et [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) accélère le développement avec l’achèvement de la syntaxe et des paramètres.</span><span class="sxs-lookup"><span data-stu-id="161fa-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="161fa-143">Les composants s’affichent dans une représentation en mémoire du DOM (Document Object Model) du navigateur appelé *arborescence de rendu*, utilisée pour mettre à jour l’interface utilisateur de manière flexible et efficace.</span><span class="sxs-lookup"><span data-stu-id="161fa-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

Blazor WebAssembly<span data-ttu-id="161fa-144">est une infrastructure d’application à page unique pour la création d’applications Web interactives côté client avec .NET.</span><span class="sxs-lookup"><span data-stu-id="161fa-144"> is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor WebAssembly<span data-ttu-id="161fa-145">utilise des normes Web ouvertes sans plug-ins ou code transpilation et fonctionne dans tous les navigateurs Web modernes, y compris les navigateurs mobiles.</span><span class="sxs-lookup"><span data-stu-id="161fa-145"> uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="161fa-146">L’exécution de code .NET dans des navigateurs Web est rendue possible par [Webassembly](https://webassembly.org) (abrégé `wasm` ).</span><span class="sxs-lookup"><span data-stu-id="161fa-146">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="161fa-147">WebAssembly est un format bytecode compact optimisé pour un téléchargement rapide et une vitesse d’exécution maximale.</span><span class="sxs-lookup"><span data-stu-id="161fa-147">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="161fa-148">WebAssembly est un standard web ouvert pris en charge dans les navigateurs web sans plug-in.</span><span class="sxs-lookup"><span data-stu-id="161fa-148">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="161fa-149">Le code WebAssembly peut accéder à toutes les fonctionnalités du navigateur via JavaScript, appelé *interopérabilité JavaScript* (ou *interop JavaScript*).</span><span class="sxs-lookup"><span data-stu-id="161fa-149">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="161fa-150">Le code .NET exécuté via WebAssembly dans le navigateur s’exécute dans le bac à sable JavaScript du navigateur avec les protections offertes par le bac à sable contre les actions malveillantes sur l’ordinateur client.</span><span class="sxs-lookup"><span data-stu-id="161fa-150">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="161fa-151">![Blazor WebAssemblyexécute du code .NET dans le navigateur avec webassembly.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="161fa-151">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="161fa-152">Quand une Blazor WebAssembly application est générée et exécutée dans un navigateur :</span><span class="sxs-lookup"><span data-stu-id="161fa-152">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="161fa-153">Les fichiers de code C# et les Razor fichiers sont compilés dans des assemblys .net.</span><span class="sxs-lookup"><span data-stu-id="161fa-153">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="161fa-154">Les assemblys et le runtime .NET sont téléchargés dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="161fa-154">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor WebAssembly<span data-ttu-id="161fa-155">amorce le Runtime .NET et configure le runtime pour charger les assemblys de l’application.</span><span class="sxs-lookup"><span data-stu-id="161fa-155"> bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="161fa-156">Le Blazor WebAssembly Runtime utilise l’interopérabilité JavaScript pour gérer les appels de l’API de manipulation et du navigateur DOM.</span><span class="sxs-lookup"><span data-stu-id="161fa-156">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="161fa-157">La taille de l’application publiée, sa *taille de charge utile*, est un facteur de performance essentiel pour qu’une application soit facile à utiliser.</span><span class="sxs-lookup"><span data-stu-id="161fa-157">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="161fa-158">Le téléchargement d’une application volumineuse dans un navigateur prend un certain temps, ce qui nuit à l’expérience utilisateur.</span><span class="sxs-lookup"><span data-stu-id="161fa-158">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor WebAssembly<span data-ttu-id="161fa-159">optimise la taille de la charge utile pour réduire les temps de téléchargement :</span><span class="sxs-lookup"><span data-stu-id="161fa-159"> optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="161fa-160">Du code inutilisé est extrait de l’application lorsqu’elle est publiée par l’[éditeur de liens de langage intermédiaire (IL)](xref:blazor/host-and-deploy/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="161fa-160">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="161fa-161">Réponses HTTP compressées.</span><span class="sxs-lookup"><span data-stu-id="161fa-161">HTTP responses are compressed.</span></span>
* <span data-ttu-id="161fa-162">Le runtime .NET et les assemblys sont mis en cache dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="161fa-162">The .NET runtime and assemblies are cached in the browser.</span></span>

## Blazor Server

Blazor<span data-ttu-id="161fa-163">dissocie la logique de rendu des composants de l’application des mises à jour de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="161fa-163"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor Server<span data-ttu-id="161fa-164">prend en charge l’hébergement de Razor composants sur le serveur dans une application ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="161fa-164"> provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="161fa-165">Les mises à jour de l’interface utilisateur sont gérées via une [SignalR](xref:signalr/introduction) connexion.</span><span class="sxs-lookup"><span data-stu-id="161fa-165">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="161fa-166">Le runtime gère l’envoi des événements d’interface utilisateur du navigateur au serveur et applique les mises à jour de l’interface utilisateur renvoyées par le serveur dans le navigateur après avoir exécuté les composants.</span><span class="sxs-lookup"><span data-stu-id="161fa-166">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="161fa-167">La connexion utilisée par Blazor Server pour communiquer avec le navigateur est également utilisée pour gérer les appels Interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="161fa-167">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="161fa-168">![Blazor Serverexécute du code .NET sur le serveur et interagit avec le Document Object Model sur le client via une SignalR connexion](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="161fa-168">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="161fa-169">Interopérabilité JavaScript</span><span class="sxs-lookup"><span data-stu-id="161fa-169">JavaScript interop</span></span>

<span data-ttu-id="161fa-170">Pour les applications qui nécessitent des bibliothèques JavaScript tierces et l’accès à des API de navigateur, les composants interagissent avec JavaScript.</span><span class="sxs-lookup"><span data-stu-id="161fa-170">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="161fa-171">Les composants peuvent utiliser les mêmes API ou bibliothèques que JavaScript.</span><span class="sxs-lookup"><span data-stu-id="161fa-171">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="161fa-172">Le code C# peut appeler du code JavaScript, et le code JavaScript peut appeler du code C#.</span><span class="sxs-lookup"><span data-stu-id="161fa-172">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="161fa-173">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="161fa-173">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="161fa-174">Partage de code et .NET Standard</span><span class="sxs-lookup"><span data-stu-id="161fa-174">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="161fa-175">implémente [.NET Standard 2,1](/dotnet/standard/net-standard), qui permet Blazor aux projets de référencer des bibliothèques conformes à .NET standard 2,1 ou à des spécifications antérieures.</span><span class="sxs-lookup"><span data-stu-id="161fa-175"> implements [.NET Standard 2.1](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard 2.1 or earlier specifications.</span></span> <span data-ttu-id="161fa-176">.NET Standard est une spécification formelle d’API .NET qui sont communes aux implémentations .NET.</span><span class="sxs-lookup"><span data-stu-id="161fa-176">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="161fa-177">.NET Standard bibliothèques de classes peuvent être partagées entre différentes plateformes .NET, telles que Blazor , .NET Framework, .net Core, Xamarin, mono et Unity.</span><span class="sxs-lookup"><span data-stu-id="161fa-177">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="161fa-178">Les API qui ne sont pas applicables à l’intérieur d’un navigateur web (par exemple l’accès au système de fichiers, l’ouverture d’un socket et le threading) lèvent une <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="161fa-178">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="161fa-179">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="161fa-179">Additional resources</span></span>

* [<span data-ttu-id="161fa-180">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="161fa-180">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="161fa-181">Guide C#</span><span class="sxs-lookup"><span data-stu-id="161fa-181">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="161fa-182">HTML</span><span class="sxs-lookup"><span data-stu-id="161fa-182">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="161fa-183">[Isard Blazor ](https://github.com/AdrienTorris/awesome-blazor) Liens vers la communauté</span><span class="sxs-lookup"><span data-stu-id="161fa-183">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
