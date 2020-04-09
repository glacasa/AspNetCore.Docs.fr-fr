---
title: ASP.NET scénarios Blazor avancés de base
author: guardrex
description: Découvrez les scénarios Blazoravancés dans , y compris comment intégrer la logique manuelle RenderTreeBuilder dans une application.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659452"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="68dc5-103">ASP.NET scénarios avancés de Core Blazor</span><span class="sxs-lookup"><span data-stu-id="68dc5-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="68dc5-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="68dc5-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="68dc5-105">Gestionnaire de circuit Blazor Server</span><span class="sxs-lookup"><span data-stu-id="68dc5-105">Blazor Server circuit handler</span></span>

<span data-ttu-id="68dc5-106">Blazor Server permet au code de définir un *gestionnaire de circuit,* ce qui permet d’exécuter du code sur les modifications apportées à l’état du circuit d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="68dc5-106">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="68dc5-107">Un gestionnaire de circuit est `CircuitHandler` mis en œuvre en dérivant et en enregistrant la classe dans le conteneur de service de l’application.</span><span class="sxs-lookup"><span data-stu-id="68dc5-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="68dc5-108">L’exemple suivant d’un gestionnaire de circuit suit les connexions SignalR ouvertes :</span><span class="sxs-lookup"><span data-stu-id="68dc5-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

<span data-ttu-id="68dc5-109">Les gestionnaires de circuits sont enregistrés à l’aide de DI.</span><span class="sxs-lookup"><span data-stu-id="68dc5-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="68dc5-110">Les instances portées sont créées par exemple d’un circuit.</span><span class="sxs-lookup"><span data-stu-id="68dc5-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="68dc5-111">À `TrackingCircuitHandler` l’aide de l’exemple précédent, un service singleton est créé parce que l’état de tous les circuits doit être suivi :</span><span class="sxs-lookup"><span data-stu-id="68dc5-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="68dc5-112">Si les méthodes d’un gestionnaire de circuit personnalisé jettent une exception non maniée, l’exception est fatale au circuit Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="68dc5-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="68dc5-113">Pour tolérer les exceptions dans le code d’un gestionnaire ou appelées méthodes, enveloppez le code dans une ou plusieurs instructions [d’essai-capture](/dotnet/csharp/language-reference/keywords/try-catch) avec la manipulation d’erreur et la connexion.</span><span class="sxs-lookup"><span data-stu-id="68dc5-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="68dc5-114">Lorsqu’un circuit se termine parce qu’un utilisateur s’est déconnecté et que le cadre nettoie l’état du circuit, le cadre dispose de la portée DI du circuit.</span><span class="sxs-lookup"><span data-stu-id="68dc5-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="68dc5-115">L’élimination de la portée dispose de tous <xref:System.IDisposable?displayProperty=fullName>les services d' DI à portée de circuit qui mettent en œuvre .</span><span class="sxs-lookup"><span data-stu-id="68dc5-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="68dc5-116">Si un service DI organise une exception non gérée lors de l’élimination, le cadre enregistre l’exception.</span><span class="sxs-lookup"><span data-stu-id="68dc5-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="68dc5-117">Logique manuelle RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="68dc5-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="68dc5-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`fournit des méthodes pour manipuler les composants et les éléments, y compris les composants de construction manuellement dans le code C.</span><span class="sxs-lookup"><span data-stu-id="68dc5-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="68dc5-119">L’utilisation de `RenderTreeBuilder` composants pour créer est un scénario avancé.</span><span class="sxs-lookup"><span data-stu-id="68dc5-119">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="68dc5-120">Un composant mal formé (par exemple, une étiquette de balisage non fermé) peut entraîner un comportement indéfini.</span><span class="sxs-lookup"><span data-stu-id="68dc5-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="68dc5-121">Considérez `PetDetails` le composant suivant, qui peut être intégré manuellement dans un autre composant :</span><span class="sxs-lookup"><span data-stu-id="68dc5-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="68dc5-122">Dans l’exemple suivant, `CreateComponent` la boucle `PetDetails` de la méthode génère trois composants.</span><span class="sxs-lookup"><span data-stu-id="68dc5-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="68dc5-123">Lorsque `RenderTreeBuilder` vous appelez des méthodes`OpenComponent` `AddAttribute`pour créer les composants ( et ), les nombres de séquences sont des numéros de ligne de code source.</span><span class="sxs-lookup"><span data-stu-id="68dc5-123">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="68dc5-124">L’algorithme de différence Blazor s’appuie sur les nombres de séquences correspondant à des lignes de code distinctes, et non sur des invocations d’appels distinctes.</span><span class="sxs-lookup"><span data-stu-id="68dc5-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="68dc5-125">Lors de la `RenderTreeBuilder` création d’un composant avec des méthodes, codez dur les arguments pour les nombres de séquences.</span><span class="sxs-lookup"><span data-stu-id="68dc5-125">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="68dc5-126">**L’utilisation d’un calcul ou d’un compteur pour générer le nombre de séquences peut entraîner de mauvaises performances.**</span><span class="sxs-lookup"><span data-stu-id="68dc5-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="68dc5-127">Pour plus d’informations, voir les numéros de séquence se rapportent aux numéros de ligne de code et non à la section [de l’ordre d’exécution.](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)</span><span class="sxs-lookup"><span data-stu-id="68dc5-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="68dc5-128">`BuiltContent`Composant:</span><span class="sxs-lookup"><span data-stu-id="68dc5-128">`BuiltContent` component:</span></span>

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> <span data-ttu-id="68dc5-129">Les types `Microsoft.AspNetCore.Components.RenderTree` dans le traitement des *résultats* des opérations de rendu.</span><span class="sxs-lookup"><span data-stu-id="68dc5-129">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="68dc5-130">Ce sont des détails internes de la mise en œuvre du cadre Blazor.</span><span class="sxs-lookup"><span data-stu-id="68dc5-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="68dc5-131">Ces types doivent être considérés comme *instables* et susceptibles d’être modifiés dans les versions futures.</span><span class="sxs-lookup"><span data-stu-id="68dc5-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="68dc5-132">Les numéros de séquence se rapportent aux numéros de ligne de code et non à l’ordre d’exécution</span><span class="sxs-lookup"><span data-stu-id="68dc5-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="68dc5-133">Les fichiers de composants de rasoir (*.razor*) sont toujours compilés.</span><span class="sxs-lookup"><span data-stu-id="68dc5-133">Razor component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="68dc5-134">La compilation est un avantage potentiel par rapport au code d’interprétation parce que l’étape de compilation peut être utilisée pour injecter des informations qui améliorent les performances de l’application au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="68dc5-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="68dc5-135">Un exemple clé de ces améliorations concerne les *nombres de séquences*.</span><span class="sxs-lookup"><span data-stu-id="68dc5-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="68dc5-136">Les nombres de séquences indiquent à l’heure d’exécution dont les sorties proviennent de quelles lignes de code distinctes et ordonnées.</span><span class="sxs-lookup"><span data-stu-id="68dc5-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="68dc5-137">Le temps d’exécution utilise cette information pour générer des diffs d’arbre efficaces en temps linéaire, ce qui est beaucoup plus rapide que ce qui est normalement possible pour un algorithme général diff arbre.</span><span class="sxs-lookup"><span data-stu-id="68dc5-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="68dc5-138">Considérez le composant razor suivant (*.razor*) fichier:</span><span class="sxs-lookup"><span data-stu-id="68dc5-138">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="68dc5-139">Le code précédent se compile à quelque chose comme ce qui suit:</span><span class="sxs-lookup"><span data-stu-id="68dc5-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="68dc5-140">Lorsque le code s’exécute pour `someFlag` `true`la première fois, le cas échéant, le constructeur reçoit :</span><span class="sxs-lookup"><span data-stu-id="68dc5-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="68dc5-141">Séquence</span><span class="sxs-lookup"><span data-stu-id="68dc5-141">Sequence</span></span> | <span data-ttu-id="68dc5-142">Type</span><span class="sxs-lookup"><span data-stu-id="68dc5-142">Type</span></span>      | <span data-ttu-id="68dc5-143">Données</span><span class="sxs-lookup"><span data-stu-id="68dc5-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="68dc5-144">0</span><span class="sxs-lookup"><span data-stu-id="68dc5-144">0</span></span>        | <span data-ttu-id="68dc5-145">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="68dc5-145">Text node</span></span> | <span data-ttu-id="68dc5-146">Premier</span><span class="sxs-lookup"><span data-stu-id="68dc5-146">First</span></span>  |
| <span data-ttu-id="68dc5-147">1</span><span class="sxs-lookup"><span data-stu-id="68dc5-147">1</span></span>        | <span data-ttu-id="68dc5-148">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="68dc5-148">Text node</span></span> | <span data-ttu-id="68dc5-149">Seconde</span><span class="sxs-lookup"><span data-stu-id="68dc5-149">Second</span></span> |

<span data-ttu-id="68dc5-150">Imaginez `someFlag` `false`que devient , et la majoration est rendue à nouveau.</span><span class="sxs-lookup"><span data-stu-id="68dc5-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="68dc5-151">Cette fois, le constructeur reçoit :</span><span class="sxs-lookup"><span data-stu-id="68dc5-151">This time, the builder receives:</span></span>

| <span data-ttu-id="68dc5-152">Séquence</span><span class="sxs-lookup"><span data-stu-id="68dc5-152">Sequence</span></span> | <span data-ttu-id="68dc5-153">Type</span><span class="sxs-lookup"><span data-stu-id="68dc5-153">Type</span></span>       | <span data-ttu-id="68dc5-154">Données</span><span class="sxs-lookup"><span data-stu-id="68dc5-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="68dc5-155">1</span><span class="sxs-lookup"><span data-stu-id="68dc5-155">1</span></span>        | <span data-ttu-id="68dc5-156">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="68dc5-156">Text node</span></span>  | <span data-ttu-id="68dc5-157">Seconde</span><span class="sxs-lookup"><span data-stu-id="68dc5-157">Second</span></span> |

<span data-ttu-id="68dc5-158">Lorsque le temps d’exécution effectue un diff, `0` il voit que l’élément à la séquence a été supprimé, de sorte qu’il génère le *script d’édition*trivial suivant:</span><span class="sxs-lookup"><span data-stu-id="68dc5-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="68dc5-159">Retirez le premier nœud texte.</span><span class="sxs-lookup"><span data-stu-id="68dc5-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="68dc5-160">Le problème avec la génération de nombres de séquences programmatiquement</span><span class="sxs-lookup"><span data-stu-id="68dc5-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="68dc5-161">Imaginez plutôt que vous avez écrit la logique suivante de constructeur d’arbres de rendu:</span><span class="sxs-lookup"><span data-stu-id="68dc5-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="68dc5-162">Maintenant, la première sortie est:</span><span class="sxs-lookup"><span data-stu-id="68dc5-162">Now, the first output is:</span></span>

| <span data-ttu-id="68dc5-163">Séquence</span><span class="sxs-lookup"><span data-stu-id="68dc5-163">Sequence</span></span> | <span data-ttu-id="68dc5-164">Type</span><span class="sxs-lookup"><span data-stu-id="68dc5-164">Type</span></span>      | <span data-ttu-id="68dc5-165">Données</span><span class="sxs-lookup"><span data-stu-id="68dc5-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="68dc5-166">0</span><span class="sxs-lookup"><span data-stu-id="68dc5-166">0</span></span>        | <span data-ttu-id="68dc5-167">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="68dc5-167">Text node</span></span> | <span data-ttu-id="68dc5-168">Premier</span><span class="sxs-lookup"><span data-stu-id="68dc5-168">First</span></span>  |
| <span data-ttu-id="68dc5-169">1</span><span class="sxs-lookup"><span data-stu-id="68dc5-169">1</span></span>        | <span data-ttu-id="68dc5-170">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="68dc5-170">Text node</span></span> | <span data-ttu-id="68dc5-171">Seconde</span><span class="sxs-lookup"><span data-stu-id="68dc5-171">Second</span></span> |

<span data-ttu-id="68dc5-172">Ce résultat est identique au cas précédent, il n’existe donc aucun problème négatif.</span><span class="sxs-lookup"><span data-stu-id="68dc5-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="68dc5-173">`someFlag`est `false` sur le deuxième rendu, et la sortie est:</span><span class="sxs-lookup"><span data-stu-id="68dc5-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="68dc5-174">Séquence</span><span class="sxs-lookup"><span data-stu-id="68dc5-174">Sequence</span></span> | <span data-ttu-id="68dc5-175">Type</span><span class="sxs-lookup"><span data-stu-id="68dc5-175">Type</span></span>      | <span data-ttu-id="68dc5-176">Données</span><span class="sxs-lookup"><span data-stu-id="68dc5-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="68dc5-177">0</span><span class="sxs-lookup"><span data-stu-id="68dc5-177">0</span></span>        | <span data-ttu-id="68dc5-178">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="68dc5-178">Text node</span></span> | <span data-ttu-id="68dc5-179">Seconde</span><span class="sxs-lookup"><span data-stu-id="68dc5-179">Second</span></span> |

<span data-ttu-id="68dc5-180">Cette fois, l’algorithme diff voit que *deux* changements se sont produits, et l’algorithme génère le script d’édition suivant:</span><span class="sxs-lookup"><span data-stu-id="68dc5-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="68dc5-181">Changer la valeur du premier `Second`nœud texte à .</span><span class="sxs-lookup"><span data-stu-id="68dc5-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="68dc5-182">Retirez le deuxième nœud texte.</span><span class="sxs-lookup"><span data-stu-id="68dc5-182">Remove the second text node.</span></span>

<span data-ttu-id="68dc5-183">Générer les numéros de séquence a `if/else` perdu toutes les informations utiles sur l’endroit où les branches et les boucles étaient présentes dans le code d’origine.</span><span class="sxs-lookup"><span data-stu-id="68dc5-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="68dc5-184">Il en résulte un diff **deux fois** plus longtemps qu’avant.</span><span class="sxs-lookup"><span data-stu-id="68dc5-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="68dc5-185">C’est un exemple anodin.</span><span class="sxs-lookup"><span data-stu-id="68dc5-185">This is a trivial example.</span></span> <span data-ttu-id="68dc5-186">Dans les cas plus réalistes avec des structures complexes et profondément imbriquées, et surtout avec les boucles, le coût de performance est généralement plus élevé.</span><span class="sxs-lookup"><span data-stu-id="68dc5-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="68dc5-187">Au lieu d’identifier immédiatement les blocs de boucle ou les branches qui ont été insérés ou enlevés, l’algorithme diff doit se récurer profondément dans les arbres de rendu.</span><span class="sxs-lookup"><span data-stu-id="68dc5-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="68dc5-188">Il en résulte généralement d’avoir à construire des scripts d’édition plus longs parce que l’algorithme diff est mal informé sur la façon dont les anciennes et les nouvelles structures se rapportent les unes aux autres.</span><span class="sxs-lookup"><span data-stu-id="68dc5-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="68dc5-189">Orientations et conclusions</span><span class="sxs-lookup"><span data-stu-id="68dc5-189">Guidance and conclusions</span></span>

* <span data-ttu-id="68dc5-190">Les performances de l’application souffrent si les nombres de séquences sont générés dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="68dc5-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="68dc5-191">Le cadre ne peut pas créer automatiquement ses propres numéros de séquence au moment de l’exécution parce que les informations nécessaires n’existent pas à moins qu’elles ne soient capturées au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="68dc5-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="68dc5-192">N’écrivez pas de longs `RenderTreeBuilder` blocs de logique appliquée manuellement.</span><span class="sxs-lookup"><span data-stu-id="68dc5-192">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="68dc5-193">Préférez les fichiers *.razor* et permettez au compilateur de traiter les numéros de séquence.</span><span class="sxs-lookup"><span data-stu-id="68dc5-193">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="68dc5-194">Si vous n’êtes `RenderTreeBuilder` pas en mesure d’éviter la `OpenRegion` / `CloseRegion` logique manuelle, divisez de longs blocs de code en petits morceaux enveloppés dans des appels.</span><span class="sxs-lookup"><span data-stu-id="68dc5-194">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="68dc5-195">Chaque région dispose de son propre espace distinct de nombres de séquences, de sorte que vous pouvez redémarrer à partir de zéro (ou tout autre nombre arbitraire) à l’intérieur de chaque région.</span><span class="sxs-lookup"><span data-stu-id="68dc5-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="68dc5-196">Si les nombres de séquences sont codés en dur, l’algorithme diff exige seulement que les nombres de séquences augmentent en valeur.</span><span class="sxs-lookup"><span data-stu-id="68dc5-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="68dc5-197">La valeur initiale et les lacunes ne sont pas pertinentes.</span><span class="sxs-lookup"><span data-stu-id="68dc5-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="68dc5-198">Une option légitime est d’utiliser le numéro de ligne de code comme numéro de séquence, ou de commencer à partir de zéro et d’augmenter par un ou des centaines (ou tout intervalle préféré).</span><span class="sxs-lookup"><span data-stu-id="68dc5-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="68dc5-199">utilise des nombres de séquences, tandis que d’autres cadres d’interface utilisateur qui diffent les arbres ne les utilisent pas.</span><span class="sxs-lookup"><span data-stu-id="68dc5-199"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="68dc5-200">Diffing est beaucoup plus rapide lorsque Blazor les nombres de séquences sont utilisés, et a l’avantage d’une étape de compilation qui traite automatiquement des numéros de séquence pour les développeurs auteur de fichiers *.razor.*</span><span class="sxs-lookup"><span data-stu-id="68dc5-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a><span data-ttu-id="68dc5-201">Effectuer de grands Blazor transferts de données dans les applications Server</span><span class="sxs-lookup"><span data-stu-id="68dc5-201">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="68dc5-202">Dans certains scénarios, de grandes quantités de Blazordonnées doivent être transférées entre JavaScript et .</span><span class="sxs-lookup"><span data-stu-id="68dc5-202">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="68dc5-203">En règle générale, les transferts de données importants se produisent lorsque :</span><span class="sxs-lookup"><span data-stu-id="68dc5-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="68dc5-204">Les API du système de fichiers de navigateur sont utilisés pour télécharger ou télécharger un fichier.</span><span class="sxs-lookup"><span data-stu-id="68dc5-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="68dc5-205">Interop avec une bibliothèque tierce est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="68dc5-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="68dc5-206">Dans Blazor Server, une limitation est en place pour empêcher de passer des messages simples et grands qui peuvent entraîner des problèmes de performances.</span><span class="sxs-lookup"><span data-stu-id="68dc5-206">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="68dc5-207">Considérez les directives suivantes lors de l’élaboration du code qui transfère des données entre JavaScript et Blazor:</span><span class="sxs-lookup"><span data-stu-id="68dc5-207">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="68dc5-208">Trancher les données en petits morceaux et envoyer les segments de données en séquentiel jusqu’à ce que toutes les données soient reçues par le serveur.</span><span class="sxs-lookup"><span data-stu-id="68dc5-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="68dc5-209">N’allouez pas de gros objets dans le code JavaScript et C.</span><span class="sxs-lookup"><span data-stu-id="68dc5-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="68dc5-210">Ne bloquez pas le thread principal d’interface utilisateur pendant de longues périodes lors de l’envoi ou de la réception de données.</span><span class="sxs-lookup"><span data-stu-id="68dc5-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="68dc5-211">Libérez toute mémoire consommée lorsque le processus est terminé ou annulé.</span><span class="sxs-lookup"><span data-stu-id="68dc5-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="68dc5-212">Appliquer les exigences supplémentaires suivantes aux fins de la sécurité :</span><span class="sxs-lookup"><span data-stu-id="68dc5-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="68dc5-213">Déclarez la taille maximale du fichier ou des données qui peut être adoptée.</span><span class="sxs-lookup"><span data-stu-id="68dc5-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="68dc5-214">Déclarez le taux de téléchargement minimum du client au serveur.</span><span class="sxs-lookup"><span data-stu-id="68dc5-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="68dc5-215">Une fois que les données sont reçues par le serveur, les données peuvent être :</span><span class="sxs-lookup"><span data-stu-id="68dc5-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="68dc5-216">Stockés temporairement dans un tampon de mémoire jusqu’à ce que tous les segments soient collectés.</span><span class="sxs-lookup"><span data-stu-id="68dc5-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="68dc5-217">Consommé immédiatement.</span><span class="sxs-lookup"><span data-stu-id="68dc5-217">Consumed immediately.</span></span> <span data-ttu-id="68dc5-218">Par exemple, les données peuvent être stockées immédiatement dans une base de données ou écrites sur disque au fur et à mesure que chaque segment est reçu.</span><span class="sxs-lookup"><span data-stu-id="68dc5-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="68dc5-219">La classe de téléchargement de fichiers suivante gère JS interop avec le client.</span><span class="sxs-lookup"><span data-stu-id="68dc5-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="68dc5-220">La classe de téléchargement utilise JS interop pour :</span><span class="sxs-lookup"><span data-stu-id="68dc5-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="68dc5-221">Sondage du client pour envoyer un segment de données.</span><span class="sxs-lookup"><span data-stu-id="68dc5-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="68dc5-222">Annulez la transaction si les temps de vote sont annulés.</span><span class="sxs-lookup"><span data-stu-id="68dc5-222">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="68dc5-223">Dans l'exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="68dc5-223">In the preceding example:</span></span>

* <span data-ttu-id="68dc5-224">Le `_maxBase64SegmentSize` est `8192`réglé à , `_maxBase64SegmentSize = _segmentSize * 4 / 3`qui est calculé à partir de .</span><span class="sxs-lookup"><span data-stu-id="68dc5-224">The `_maxBase64SegmentSize` is set to `8192`, which is calculated from `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="68dc5-225">Les API de gestion de la mémoire de base de `_uploadedSegments`bas niveau .NET sont utilisées pour stocker les segments de mémoire sur le serveur dans .</span><span class="sxs-lookup"><span data-stu-id="68dc5-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `_uploadedSegments`.</span></span>
* <span data-ttu-id="68dc5-226">Une `ReceiveFile` méthode est utilisée pour gérer le téléchargement via JS interop :</span><span class="sxs-lookup"><span data-stu-id="68dc5-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="68dc5-227">La taille du fichier est déterminée dans `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`les octets par L’intermédiaire de JS interop avec .</span><span class="sxs-lookup"><span data-stu-id="68dc5-227">The file size is determined in bytes through JS interop with `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="68dc5-228">Le nombre de segments à recevoir `numberOfSegments`sont calculés et stockés dans .</span><span class="sxs-lookup"><span data-stu-id="68dc5-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="68dc5-229">Les segments sont `for` demandés en boucle `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`par L’intermédiaire de JS interop avec .</span><span class="sxs-lookup"><span data-stu-id="68dc5-229">The segments are requested in a `for` loop through JS interop with `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="68dc5-230">Tous les segments, sauf le dernier doivent être 8 192 octets avant de décoder.</span><span class="sxs-lookup"><span data-stu-id="68dc5-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="68dc5-231">Le client est obligé d’envoyer les données d’une manière efficace.</span><span class="sxs-lookup"><span data-stu-id="68dc5-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="68dc5-232">Pour chaque segment reçu, les contrôles <xref:System.Convert.TryFromBase64String*>sont effectués avant de décoder avec .</span><span class="sxs-lookup"><span data-stu-id="68dc5-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String*>.</span></span>
  * <span data-ttu-id="68dc5-233">Un flux avec les données <xref:System.IO.Stream> est`SegmentedStream`retourné comme un nouveau ( ) après le téléchargement est terminé.</span><span class="sxs-lookup"><span data-stu-id="68dc5-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="68dc5-234">La classe de flux segmentée expose la liste des segments <xref:System.IO.Stream>comme un readonly non-seekable :</span><span class="sxs-lookup"><span data-stu-id="68dc5-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

<span data-ttu-id="68dc5-235">Le code suivant implémente les fonctions JavaScript pour recevoir les données :</span><span class="sxs-lookup"><span data-stu-id="68dc5-235">The following code implements JavaScript functions to receive the data:</span></span>

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
