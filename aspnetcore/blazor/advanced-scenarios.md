---
title: ASP.NET Core Blazor des scénarios avancés
author: guardrex
description: En savoir plus sur les Blazorscénarios avancés dans, y compris l’intégration de la logique manuelle RenderTreeBuilder dans une application.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 9f1e5ea4d883a027f40ac0eccc7a9bba1435139d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767198"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="406b1-103">Scénarios avancés ASP.NET Core éblouissants</span><span class="sxs-lookup"><span data-stu-id="406b1-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="406b1-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="406b1-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="406b1-105">Gestionnaire de circuit du serveur éblouissant</span><span class="sxs-lookup"><span data-stu-id="406b1-105">Blazor Server circuit handler</span></span>

<span data-ttu-id="406b1-106">Le serveur éblouissant permet au code de définir un *Gestionnaire de circuit*qui permet d’exécuter du code sur les modifications de l’état du circuit d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="406b1-106">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="406b1-107">Un gestionnaire de circuit est implémenté en dérivant de `CircuitHandler` et en inscrivant la classe dans le conteneur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="406b1-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="406b1-108">L’exemple suivant d’un gestionnaire de circuit effectue le suivi des connexions signaler ouvertes :</span><span class="sxs-lookup"><span data-stu-id="406b1-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

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

<span data-ttu-id="406b1-109">Les gestionnaires de circuits sont inscrits à l’aide de DI.</span><span class="sxs-lookup"><span data-stu-id="406b1-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="406b1-110">Les instances délimitées sont créées par instance d’un circuit.</span><span class="sxs-lookup"><span data-stu-id="406b1-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="406b1-111">À l' `TrackingCircuitHandler` aide de dans l’exemple précédent, un service Singleton est créé car l’état de tous les circuits doit être suivi :</span><span class="sxs-lookup"><span data-stu-id="406b1-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="406b1-112">Si les méthodes d’un gestionnaire de circuit personnalisé lèvent une exception non gérée, l’exception est irrémédiable au circuit du serveur éblouissant.</span><span class="sxs-lookup"><span data-stu-id="406b1-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="406b1-113">Pour tolérer des exceptions dans le code d’un gestionnaire ou les méthodes appelées, encapsulez le code dans une ou plusieurs instructions [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) avec la gestion des erreurs et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="406b1-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="406b1-114">Lorsqu’un circuit se termine parce qu’un utilisateur s’est déconnecté et que l’infrastructure nettoie l’état du circuit, le Framework supprime l’étendue de l’injection de port.</span><span class="sxs-lookup"><span data-stu-id="406b1-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="406b1-115">La suppression de l’étendue supprime tous les services d’étendue de circuit qui implémentent <xref:System.IDisposable?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="406b1-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="406b1-116">Si un service d’injection de services lève une exception non gérée pendant la suppression, le Framework journalise l’exception.</span><span class="sxs-lookup"><span data-stu-id="406b1-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="406b1-117">Logique RenderTreeBuilder manuelle</span><span class="sxs-lookup"><span data-stu-id="406b1-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="406b1-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`fournit des méthodes pour manipuler des composants et des éléments, notamment la génération manuelle de composants dans du code C#.</span><span class="sxs-lookup"><span data-stu-id="406b1-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="406b1-119">L’utilisation `RenderTreeBuilder` de pour créer des composants est un scénario avancé.</span><span class="sxs-lookup"><span data-stu-id="406b1-119">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="406b1-120">Un composant mal formé (par exemple, une balise de balisage non fermée) peut entraîner un comportement indéfini.</span><span class="sxs-lookup"><span data-stu-id="406b1-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="406b1-121">Prenons le composant `PetDetails` suivant, qui peut être intégré manuellement à un autre composant :</span><span class="sxs-lookup"><span data-stu-id="406b1-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="406b1-122">Dans l’exemple suivant, la boucle de la `CreateComponent` méthode génère trois `PetDetails` composants.</span><span class="sxs-lookup"><span data-stu-id="406b1-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="406b1-123">Lors de `RenderTreeBuilder` l’appel de méthodes pour créer`OpenComponent` les `AddAttribute`composants (et), les numéros de séquence sont des numéros de ligne de code source.</span><span class="sxs-lookup"><span data-stu-id="406b1-123">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="406b1-124">L’algorithme de différence éblouissant s’appuie sur les numéros de séquence correspondant à des lignes de code distinctes, et non sur des appels d’appel distincts.</span><span class="sxs-lookup"><span data-stu-id="406b1-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="406b1-125">Lors de la création d' `RenderTreeBuilder` un composant avec des méthodes, coder en dur les arguments pour les numéros de séquence.</span><span class="sxs-lookup"><span data-stu-id="406b1-125">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="406b1-126">**L’utilisation d’un calcul ou d’un compteur pour générer le numéro de séquence peut entraîner des performances médiocres.**</span><span class="sxs-lookup"><span data-stu-id="406b1-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="406b1-127">Pour plus d’informations, consultez la section [numéros de séquence liés à des numéros de ligne de code et non à un ordre d’exécution](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="406b1-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="406b1-128">`BuiltContent`-</span><span class="sxs-lookup"><span data-stu-id="406b1-128">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="406b1-129">Les types dans `Microsoft.AspNetCore.Components.RenderTree` autorisent le traitement des *résultats* des opérations de rendu.</span><span class="sxs-lookup"><span data-stu-id="406b1-129">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="406b1-130">Il s’agit des détails internes de l’implémentation du Framework éblouissant.</span><span class="sxs-lookup"><span data-stu-id="406b1-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="406b1-131">Ces types doivent être considérés comme *instables* et susceptibles d’être modifiés dans les versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="406b1-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="406b1-132">Les numéros de séquence sont liés aux numéros de ligne de code et non à l’ordre d’exécution</span><span class="sxs-lookup"><span data-stu-id="406b1-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="406b1-133">Les fichiers de composants Razor (*. Razor*) sont toujours compilés.</span><span class="sxs-lookup"><span data-stu-id="406b1-133">Razor component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="406b1-134">La compilation est un avantage potentiel de l’interprétation du code, car l’étape de compilation peut être utilisée pour injecter des informations qui améliorent les performances de l’application au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="406b1-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="406b1-135">Un exemple clé de ces améliorations concerne les *numéros de séquence*.</span><span class="sxs-lookup"><span data-stu-id="406b1-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="406b1-136">Les numéros de séquence indiquent au runtime que les sorties proviennent de lignes de code distinctes et ordonnées.</span><span class="sxs-lookup"><span data-stu-id="406b1-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="406b1-137">Le runtime utilise ces informations pour générer des différences d’arborescence efficaces en temps linéaire, ce qui est beaucoup plus rapide qu’un algorithme de comparaison d’arborescence générale.</span><span class="sxs-lookup"><span data-stu-id="406b1-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="406b1-138">Considérez le fichier de composant Razor (*. Razor*) suivant :</span><span class="sxs-lookup"><span data-stu-id="406b1-138">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="406b1-139">Le code précédent se compile comme suit :</span><span class="sxs-lookup"><span data-stu-id="406b1-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="406b1-140">Lorsque le code s’exécute pour la première fois, si `someFlag` est `true`, le générateur reçoit :</span><span class="sxs-lookup"><span data-stu-id="406b1-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="406b1-141">Séquence</span><span class="sxs-lookup"><span data-stu-id="406b1-141">Sequence</span></span> | <span data-ttu-id="406b1-142">Type</span><span class="sxs-lookup"><span data-stu-id="406b1-142">Type</span></span>      | <span data-ttu-id="406b1-143">Données</span><span class="sxs-lookup"><span data-stu-id="406b1-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="406b1-144">0</span><span class="sxs-lookup"><span data-stu-id="406b1-144">0</span></span>        | <span data-ttu-id="406b1-145">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="406b1-145">Text node</span></span> | <span data-ttu-id="406b1-146">Premier</span><span class="sxs-lookup"><span data-stu-id="406b1-146">First</span></span>  |
| <span data-ttu-id="406b1-147">1</span><span class="sxs-lookup"><span data-stu-id="406b1-147">1</span></span>        | <span data-ttu-id="406b1-148">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="406b1-148">Text node</span></span> | <span data-ttu-id="406b1-149">Seconde</span><span class="sxs-lookup"><span data-stu-id="406b1-149">Second</span></span> |

<span data-ttu-id="406b1-150">Imaginez que `someFlag` devient `false`et le balisage est de nouveau restitué.</span><span class="sxs-lookup"><span data-stu-id="406b1-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="406b1-151">Cette fois-ci, le générateur reçoit :</span><span class="sxs-lookup"><span data-stu-id="406b1-151">This time, the builder receives:</span></span>

| <span data-ttu-id="406b1-152">Séquence</span><span class="sxs-lookup"><span data-stu-id="406b1-152">Sequence</span></span> | <span data-ttu-id="406b1-153">Type</span><span class="sxs-lookup"><span data-stu-id="406b1-153">Type</span></span>       | <span data-ttu-id="406b1-154">Données</span><span class="sxs-lookup"><span data-stu-id="406b1-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="406b1-155">1</span><span class="sxs-lookup"><span data-stu-id="406b1-155">1</span></span>        | <span data-ttu-id="406b1-156">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="406b1-156">Text node</span></span>  | <span data-ttu-id="406b1-157">Seconde</span><span class="sxs-lookup"><span data-stu-id="406b1-157">Second</span></span> |

<span data-ttu-id="406b1-158">Quand le runtime effectue une comparaison, il constate que l’élément au niveau `0` de la séquence a été supprimé. il génère donc le *script d’édition*trivial suivant :</span><span class="sxs-lookup"><span data-stu-id="406b1-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="406b1-159">Supprimez le premier nœud de texte.</span><span class="sxs-lookup"><span data-stu-id="406b1-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="406b1-160">Le problème de la génération par programmation des numéros de séquence</span><span class="sxs-lookup"><span data-stu-id="406b1-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="406b1-161">Imaginez plutôt que vous avez écrit la logique de générateur d’arborescence de rendu suivante :</span><span class="sxs-lookup"><span data-stu-id="406b1-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="406b1-162">La première sortie est désormais :</span><span class="sxs-lookup"><span data-stu-id="406b1-162">Now, the first output is:</span></span>

| <span data-ttu-id="406b1-163">Séquence</span><span class="sxs-lookup"><span data-stu-id="406b1-163">Sequence</span></span> | <span data-ttu-id="406b1-164">Type</span><span class="sxs-lookup"><span data-stu-id="406b1-164">Type</span></span>      | <span data-ttu-id="406b1-165">Données</span><span class="sxs-lookup"><span data-stu-id="406b1-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="406b1-166">0</span><span class="sxs-lookup"><span data-stu-id="406b1-166">0</span></span>        | <span data-ttu-id="406b1-167">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="406b1-167">Text node</span></span> | <span data-ttu-id="406b1-168">Premier</span><span class="sxs-lookup"><span data-stu-id="406b1-168">First</span></span>  |
| <span data-ttu-id="406b1-169">1</span><span class="sxs-lookup"><span data-stu-id="406b1-169">1</span></span>        | <span data-ttu-id="406b1-170">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="406b1-170">Text node</span></span> | <span data-ttu-id="406b1-171">Seconde</span><span class="sxs-lookup"><span data-stu-id="406b1-171">Second</span></span> |

<span data-ttu-id="406b1-172">Ce résultat est identique au cas précédent, donc aucun problème négatif n’existe.</span><span class="sxs-lookup"><span data-stu-id="406b1-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="406b1-173">`someFlag`se `false` trouve sur le deuxième rendu et la sortie est :</span><span class="sxs-lookup"><span data-stu-id="406b1-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="406b1-174">Séquence</span><span class="sxs-lookup"><span data-stu-id="406b1-174">Sequence</span></span> | <span data-ttu-id="406b1-175">Type</span><span class="sxs-lookup"><span data-stu-id="406b1-175">Type</span></span>      | <span data-ttu-id="406b1-176">Données</span><span class="sxs-lookup"><span data-stu-id="406b1-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="406b1-177">0</span><span class="sxs-lookup"><span data-stu-id="406b1-177">0</span></span>        | <span data-ttu-id="406b1-178">Nœud de texte</span><span class="sxs-lookup"><span data-stu-id="406b1-178">Text node</span></span> | <span data-ttu-id="406b1-179">Seconde</span><span class="sxs-lookup"><span data-stu-id="406b1-179">Second</span></span> |

<span data-ttu-id="406b1-180">Cette fois-ci, l’algorithme diff constate que *deux* modifications ont été apportées, et l’algorithme génère le script Edit suivant :</span><span class="sxs-lookup"><span data-stu-id="406b1-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="406b1-181">Remplacez la valeur du premier nœud de texte par `Second`.</span><span class="sxs-lookup"><span data-stu-id="406b1-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="406b1-182">Supprimez le deuxième nœud de texte.</span><span class="sxs-lookup"><span data-stu-id="406b1-182">Remove the second text node.</span></span>

<span data-ttu-id="406b1-183">La génération des numéros de séquence a perdu toutes les informations utiles sur `if/else` l’emplacement où les branches et les boucles étaient présentes dans le code d’origine.</span><span class="sxs-lookup"><span data-stu-id="406b1-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="406b1-184">Il en résulte une comparaison de **deux fois plus longtemps** qu’auparavant.</span><span class="sxs-lookup"><span data-stu-id="406b1-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="406b1-185">Il s’agit d’un exemple trivial.</span><span class="sxs-lookup"><span data-stu-id="406b1-185">This is a trivial example.</span></span> <span data-ttu-id="406b1-186">Dans des cas plus réalistes avec des structures complexes et profondément imbriquées, et surtout avec des boucles, le coût des performances est généralement plus élevé.</span><span class="sxs-lookup"><span data-stu-id="406b1-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="406b1-187">Au lieu d’identifier immédiatement les blocs de boucle ou les branches qui ont été insérés ou supprimés, l’algorithme diff doit être recurse profondément dans les arborescences de rendu.</span><span class="sxs-lookup"><span data-stu-id="406b1-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="406b1-188">Cela entraîne généralement la création de scripts de modification plus longs, car l’algorithme diff est mal informé de la relation entre les anciennes et les nouvelles structures.</span><span class="sxs-lookup"><span data-stu-id="406b1-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="406b1-189">Conseils et conclusions</span><span class="sxs-lookup"><span data-stu-id="406b1-189">Guidance and conclusions</span></span>

* <span data-ttu-id="406b1-190">Les performances de l’application sont affectées si les numéros séquentiels sont générés dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="406b1-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="406b1-191">L’infrastructure ne peut pas créer ses propres numéros de séquence automatiquement au moment de l’exécution, car les informations nécessaires n’existent pas, sauf si elles sont capturées au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="406b1-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="406b1-192">N’écrivez pas de longs blocs de logique `RenderTreeBuilder` implémentée manuellement.</span><span class="sxs-lookup"><span data-stu-id="406b1-192">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="406b1-193">Préférer les fichiers *. Razor* et permettre au compilateur de gérer les numéros de séquence.</span><span class="sxs-lookup"><span data-stu-id="406b1-193">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="406b1-194">Si vous ne pouvez pas éviter une `RenderTreeBuilder` logique manuelle, fractionnez les blocs de code longs en éléments `OpenRegion` / `CloseRegion` plus petits inclus dans des appels.</span><span class="sxs-lookup"><span data-stu-id="406b1-194">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="406b1-195">Chaque région a son propre espace distinct pour les numéros de séquence, ce qui vous permet de redémarrer à partir de zéro (ou tout autre nombre arbitraire) à l’intérieur de chaque région.</span><span class="sxs-lookup"><span data-stu-id="406b1-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="406b1-196">Si les numéros de séquence sont codés en dur, l’algorithme diff exige uniquement que les numéros de séquence augmentent dans la valeur.</span><span class="sxs-lookup"><span data-stu-id="406b1-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="406b1-197">La valeur initiale et les écarts ne sont pas pertinents.</span><span class="sxs-lookup"><span data-stu-id="406b1-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="406b1-198">Une option légitime consiste à utiliser le numéro de ligne de code comme numéro de séquence, ou à commencer à partir de zéro et à augmenter par des ou des centaines (ou un intervalle de préférence).</span><span class="sxs-lookup"><span data-stu-id="406b1-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="406b1-199">utilise des numéros de séquence, tandis que d’autres infrastructures d’interface utilisateur de comparaison d’arborescence ne les utilisent pas.</span><span class="sxs-lookup"><span data-stu-id="406b1-199"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="406b1-200">La comparaison est beaucoup plus rapide lorsque les numéros de séquence sont Blazor utilisés et présente l’avantage d’une étape de compilation qui traite automatiquement les numéros séquentiels pour les développeurs qui créent des fichiers *. Razor* .</span><span class="sxs-lookup"><span data-stu-id="406b1-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a><span data-ttu-id="406b1-201">Effectuer des transferts de données Blazor volumineux dans des applications serveur</span><span class="sxs-lookup"><span data-stu-id="406b1-201">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="406b1-202">Dans certains scénarios, de grandes quantités de données doivent être transférées entre BlazorJavaScript et.</span><span class="sxs-lookup"><span data-stu-id="406b1-202">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="406b1-203">En général, les transferts de données volumineux se produisent dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="406b1-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="406b1-204">Les API du système de fichiers du navigateur sont utilisées pour charger ou télécharger un fichier.</span><span class="sxs-lookup"><span data-stu-id="406b1-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="406b1-205">L’interopérabilité avec une bibliothèque tierce est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="406b1-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="406b1-206">Dans Blazor le serveur, une limitation est en place pour empêcher le passage de messages volumineux qui peuvent entraîner des problèmes de performances.</span><span class="sxs-lookup"><span data-stu-id="406b1-206">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="406b1-207">Tenez compte des conseils suivants lors du développement de code qui transfère des Blazordonnées entre JavaScript et :</span><span class="sxs-lookup"><span data-stu-id="406b1-207">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="406b1-208">Découpez les données en éléments plus petits et envoyez les segments de données de façon séquentielle jusqu’à ce que toutes les données soient reçues par le serveur.</span><span class="sxs-lookup"><span data-stu-id="406b1-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="406b1-209">N’allouez pas d’objets volumineux dans du code JavaScript et C#.</span><span class="sxs-lookup"><span data-stu-id="406b1-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="406b1-210">Ne bloquez pas le thread d’interface utilisateur principal pendant de longues périodes lors de l’envoi ou de la réception de données.</span><span class="sxs-lookup"><span data-stu-id="406b1-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="406b1-211">Libérez la mémoire consommée lorsque le processus est terminé ou annulé.</span><span class="sxs-lookup"><span data-stu-id="406b1-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="406b1-212">Appliquer les exigences supplémentaires suivantes pour des raisons de sécurité :</span><span class="sxs-lookup"><span data-stu-id="406b1-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="406b1-213">Déclarez la taille maximale du fichier ou des données qui peut être passée.</span><span class="sxs-lookup"><span data-stu-id="406b1-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="406b1-214">Déclarez le taux de téléchargement minimal du client au serveur.</span><span class="sxs-lookup"><span data-stu-id="406b1-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="406b1-215">Une fois les données reçues par le serveur, les données peuvent être :</span><span class="sxs-lookup"><span data-stu-id="406b1-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="406b1-216">Stocké temporairement dans une mémoire tampon jusqu’à ce que tous les segments soient collectés.</span><span class="sxs-lookup"><span data-stu-id="406b1-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="406b1-217">Consommé immédiatement.</span><span class="sxs-lookup"><span data-stu-id="406b1-217">Consumed immediately.</span></span> <span data-ttu-id="406b1-218">Par exemple, les données peuvent être stockées immédiatement dans une base de données ou écrites sur le disque à mesure que chaque segment est reçu.</span><span class="sxs-lookup"><span data-stu-id="406b1-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="406b1-219">La classe de chargeur de fichiers suivante gère l’interopérabilité de JS avec le client.</span><span class="sxs-lookup"><span data-stu-id="406b1-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="406b1-220">La classe de chargeur utilise l’interopérabilité JS pour :</span><span class="sxs-lookup"><span data-stu-id="406b1-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="406b1-221">Interroger le client pour envoyer un segment de données.</span><span class="sxs-lookup"><span data-stu-id="406b1-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="406b1-222">Abandonner la transaction si le délai d’interrogation est dépassé.</span><span class="sxs-lookup"><span data-stu-id="406b1-222">Abort the transaction if polling times out.</span></span>

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

<span data-ttu-id="406b1-223">Dans l'exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="406b1-223">In the preceding example:</span></span>

* <span data-ttu-id="406b1-224">A `_maxBase64SegmentSize` la valeur `8192`, qui est calculée à partir `_maxBase64SegmentSize = _segmentSize * 4 / 3`de.</span><span class="sxs-lookup"><span data-stu-id="406b1-224">The `_maxBase64SegmentSize` is set to `8192`, which is calculated from `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="406b1-225">Les API de gestion de mémoire .NET Core de bas niveau sont utilisées pour stocker les segments de mémoire `_uploadedSegments`sur le serveur dans.</span><span class="sxs-lookup"><span data-stu-id="406b1-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `_uploadedSegments`.</span></span>
* <span data-ttu-id="406b1-226">Une `ReceiveFile` méthode est utilisée pour gérer le téléchargement via l’interopérabilité JS :</span><span class="sxs-lookup"><span data-stu-id="406b1-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="406b1-227">La taille du fichier est déterminée en octets par le biais `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`de l’interopérabilité js avec.</span><span class="sxs-lookup"><span data-stu-id="406b1-227">The file size is determined in bytes through JS interop with `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="406b1-228">Le nombre de segments à recevoir est calculé et stocké `numberOfSegments`dans.</span><span class="sxs-lookup"><span data-stu-id="406b1-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="406b1-229">Les segments sont demandés dans `for` une boucle via l’interopérabilité js avec `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span><span class="sxs-lookup"><span data-stu-id="406b1-229">The segments are requested in a `for` loop through JS interop with `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="406b1-230">Tous les segments, mais le dernier doit être de 8 192 octets avant le décodage.</span><span class="sxs-lookup"><span data-stu-id="406b1-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="406b1-231">Le client est contraint d’envoyer les données de manière efficace.</span><span class="sxs-lookup"><span data-stu-id="406b1-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="406b1-232">Pour chaque segment reçu, les vérifications sont effectuées avant le décodage <xref:System.Convert.TryFromBase64String*>.</span><span class="sxs-lookup"><span data-stu-id="406b1-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String*>.</span></span>
  * <span data-ttu-id="406b1-233">Un flux avec les données est retourné en tant que <xref:System.IO.Stream> nouveau`SegmentedStream`() une fois le téléchargement terminé.</span><span class="sxs-lookup"><span data-stu-id="406b1-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="406b1-234">La classe de flux segmentée expose la liste de segments sous la forme d’une classe <xref:System.IO.Stream>ReadOnly non recherchée :</span><span class="sxs-lookup"><span data-stu-id="406b1-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

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

<span data-ttu-id="406b1-235">Le code suivant implémente les fonctions JavaScript pour recevoir les données :</span><span class="sxs-lookup"><span data-stu-id="406b1-235">The following code implements JavaScript functions to receive the data:</span></span>

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
