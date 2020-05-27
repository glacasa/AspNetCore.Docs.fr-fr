---
<span data-ttu-id="1ac1e-101">titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris l’intégration de la logique manuelle RenderTreeBuilder dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-101">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-103">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-104">'Identity'</span></span>
- <span data-ttu-id="1ac1e-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-106">'Razor'</span></span>
- <span data-ttu-id="1ac1e-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="1ac1e-108">ASP.NET Core des Blazor scénarios avancés</span><span class="sxs-lookup"><span data-stu-id="1ac1e-108">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="1ac1e-109">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1ac1e-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a>Blazor<span data-ttu-id="1ac1e-110">Gestionnaire de circuits de serveur</span><span class="sxs-lookup"><span data-stu-id="1ac1e-110"> Server circuit handler</span></span>

Blazor<span data-ttu-id="1ac1e-111">Le serveur permet au code de définir un *Gestionnaire de circuit*qui permet d’exécuter du code sur les modifications de l’état du circuit d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-111"> Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="1ac1e-112">Un gestionnaire de circuit est implémenté en dérivant de `CircuitHandler` et en inscrivant la classe dans le conteneur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-112">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="1ac1e-113">L’exemple suivant d’un gestionnaire de circuit effectue le suivi des SignalR connexions ouvertes :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-113">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

<span data-ttu-id="1ac1e-114">Les gestionnaires de circuits sont inscrits à l’aide de DI.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-114">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="1ac1e-115">Les instances délimitées sont créées par instance d’un circuit.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-115">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="1ac1e-116">À l’aide `TrackingCircuitHandler` de dans l’exemple précédent, un service Singleton est créé car l’état de tous les circuits doit être suivi :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-116">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="1ac1e-117">Si les méthodes d’un gestionnaire de circuit personnalisé lèvent une exception non gérée, l’exception est irrécupérable pour le Blazor circuit serveur.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-117">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="1ac1e-118">Pour tolérer des exceptions dans le code d’un gestionnaire ou les méthodes appelées, encapsulez le code dans une ou plusieurs instructions [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) avec la gestion des erreurs et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-118">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="1ac1e-119">Lorsqu’un circuit se termine parce qu’un utilisateur s’est déconnecté et que l’infrastructure nettoie l’état du circuit, le Framework supprime l’étendue de l’injection de port.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-119">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="1ac1e-120">La suppression de l’étendue supprime tous les services d’étendue de circuit qui implémentent <xref:System.IDisposable?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="1ac1e-120">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="1ac1e-121">Si un service d’injection de services lève une exception non gérée pendant la suppression, le Framework journalise l’exception.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-121">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="1ac1e-122">Logique RenderTreeBuilder manuelle</span><span class="sxs-lookup"><span data-stu-id="1ac1e-122">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="1ac1e-123"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>fournit des méthodes pour manipuler des composants et des éléments, notamment la génération manuelle de composants dans du code C#.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-123"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="1ac1e-124">L’utilisation de <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> pour créer des composants est un scénario avancé.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-124">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="1ac1e-125">Un composant mal formé (par exemple, une balise de balisage non fermée) peut entraîner un comportement indéfini.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-125">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="1ac1e-126">Prenons le `PetDetails` composant suivant, qui peut être intégré manuellement à un autre composant :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-126">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="1ac1e-127">Dans l’exemple suivant, la boucle de la `CreateComponent` méthode génère trois `PetDetails` composants.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-127">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="1ac1e-128">Lors <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> de l’appel de méthodes pour créer les composants ( `OpenComponent` et `AddAttribute` ), les numéros de séquence sont des numéros de ligne de code source.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-128">When calling <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="1ac1e-129">L' Blazor algorithme de différence s’appuie sur les numéros de séquence correspondant à des lignes de code distinctes, et non sur des appels d’appel distincts.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-129">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="1ac1e-130">Lors de la création d’un composant avec des <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> méthodes, coder en dur les arguments pour les numéros de séquence.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-130">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="1ac1e-131">**L’utilisation d’un calcul ou d’un compteur pour générer le numéro de séquence peut entraîner des performances médiocres.**</span><span class="sxs-lookup"><span data-stu-id="1ac1e-131">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="1ac1e-132">Pour plus d’informations, consultez la section [numéros de séquence liés à des numéros de ligne de code et non à un ordre d’exécution](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="1ac1e-132">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="1ac1e-133">`BuiltContent`-</span><span class="sxs-lookup"><span data-stu-id="1ac1e-133">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="1ac1e-134">Les types dans <xref:Microsoft.AspNetCore.Components.RenderTree> autorisent le traitement des *résultats* des opérations de rendu.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-134">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="1ac1e-135">Il s’agit des détails internes de l' Blazor implémentation du Framework.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-135">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="1ac1e-136">Ces types doivent être considérés comme *instables* et susceptibles d’être modifiés dans les versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-136">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="1ac1e-137">Les numéros de séquence sont liés aux numéros de ligne de code et non à l’ordre d’exécution</span><span class="sxs-lookup"><span data-stu-id="1ac1e-137">Sequence numbers relate to code line numbers and not execution order</span></span>

Razor<span data-ttu-id="1ac1e-138">les fichiers de composants (*. Razor*) sont toujours compilés.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-138"> component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="1ac1e-139">La compilation est un avantage potentiel de l’interprétation du code, car l’étape de compilation peut être utilisée pour injecter des informations qui améliorent les performances de l’application au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-139">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="1ac1e-140">Un exemple clé de ces améliorations concerne les *numéros de séquence*.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-140">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="1ac1e-141">Les numéros de séquence indiquent au runtime que les sorties proviennent de lignes de code distinctes et ordonnées.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-141">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="1ac1e-142">Le runtime utilise ces informations pour générer des différences d’arborescence efficaces en temps linéaire, ce qui est beaucoup plus rapide qu’un algorithme de comparaison d’arborescence générale.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-142">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="1ac1e-143">Considérez le Razor fichier de composant (*. Razor*) suivant :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-143">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="1ac1e-144">Le code précédent se compile comme suit :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-144">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="1ac1e-145">Lorsque le code s’exécute pour la première fois, si `someFlag` est `true` , le générateur reçoit :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-145">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="1ac1e-146">Séquence</span><span class="sxs-lookup"><span data-stu-id="1ac1e-146">Sequence</span></span> | <span data-ttu-id="1ac1e-147">Type</span><span class="sxs-lookup"><span data-stu-id="1ac1e-147">Type</span></span>      | <span data-ttu-id="1ac1e-148">Données</span><span class="sxs-lookup"><span data-stu-id="1ac1e-148">Data</span></span>   |
| :---
<span data-ttu-id="1ac1e-149">titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris l’intégration de la logique manuelle RenderTreeBuilder dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-149">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-150">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-150">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-151">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-152">'Identity'</span></span>
- <span data-ttu-id="1ac1e-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-154">'Razor'</span></span>
- <span data-ttu-id="1ac1e-155">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-155">'SignalR' uid:</span></span> 

<span data-ttu-id="1ac1e-156">---: | ---titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris comment incorporer la logique RenderTreeBuilder manuelle dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-156">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-157">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-157">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-158">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-159">'Identity'</span></span>
- <span data-ttu-id="1ac1e-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-161">'Razor'</span></span>
- <span data-ttu-id="1ac1e-162">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1ac1e-163">titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris l’intégration de la logique manuelle RenderTreeBuilder dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-163">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-164">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-164">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-165">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-166">'Identity'</span></span>
- <span data-ttu-id="1ac1e-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-167">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-168">'Razor'</span></span>
- <span data-ttu-id="1ac1e-169">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-169">'SignalR' uid:</span></span> 

<span data-ttu-id="1ac1e-170">----- | :----: | | 0 | Nœud de texte | Premier | | 1 | Nœud de texte | Deuxième |</span><span class="sxs-lookup"><span data-stu-id="1ac1e-170">----- | :----: | | 0        | Text node | First  | | 1        | Text node | Second |</span></span>

<span data-ttu-id="1ac1e-171">Imaginez que `someFlag` devient `false` et le balisage est de nouveau restitué.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-171">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="1ac1e-172">Cette fois-ci, le générateur reçoit :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-172">This time, the builder receives:</span></span>

| <span data-ttu-id="1ac1e-173">Séquence</span><span class="sxs-lookup"><span data-stu-id="1ac1e-173">Sequence</span></span> | <span data-ttu-id="1ac1e-174">Type</span><span class="sxs-lookup"><span data-stu-id="1ac1e-174">Type</span></span>       | <span data-ttu-id="1ac1e-175">Données</span><span class="sxs-lookup"><span data-stu-id="1ac1e-175">Data</span></span>   |
| :---
<span data-ttu-id="1ac1e-176">titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris l’intégration de la logique manuelle RenderTreeBuilder dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-176">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-177">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-178">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-179">'Identity'</span></span>
- <span data-ttu-id="1ac1e-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-181">'Razor'</span></span>
- <span data-ttu-id="1ac1e-182">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-182">'SignalR' uid:</span></span> 

<span data-ttu-id="1ac1e-183">---: | ---titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris comment incorporer la logique RenderTreeBuilder manuelle dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-183">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-184">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-185">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-186">'Identity'</span></span>
- <span data-ttu-id="1ac1e-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-188">'Razor'</span></span>
- <span data-ttu-id="1ac1e-189">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1ac1e-190">titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris l’intégration de la logique manuelle RenderTreeBuilder dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-190">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-191">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-192">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-193">'Identity'</span></span>
- <span data-ttu-id="1ac1e-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-195">'Razor'</span></span>
- <span data-ttu-id="1ac1e-196">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1ac1e-197">titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris l’intégration de la logique manuelle RenderTreeBuilder dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-197">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-198">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-199">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-200">'Identity'</span></span>
- <span data-ttu-id="1ac1e-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-202">'Razor'</span></span>
- <span data-ttu-id="1ac1e-203">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-203">'SignalR' uid:</span></span> 

<span data-ttu-id="1ac1e-204">----- | :----: | | 1 | Nœud de texte | Deuxième |</span><span class="sxs-lookup"><span data-stu-id="1ac1e-204">----- | :----: | | 1        | Text node  | Second |</span></span>

<span data-ttu-id="1ac1e-205">Quand le runtime effectue une comparaison, il constate que l’élément au niveau de la séquence `0` a été supprimé. il génère donc le *script d’édition*trivial suivant :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-205">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="1ac1e-206">Supprimez le premier nœud de texte.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-206">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="1ac1e-207">Le problème de la génération par programmation des numéros de séquence</span><span class="sxs-lookup"><span data-stu-id="1ac1e-207">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="1ac1e-208">Imaginez plutôt que vous avez écrit la logique de générateur d’arborescence de rendu suivante :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-208">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="1ac1e-209">La première sortie est désormais :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-209">Now, the first output is:</span></span>

| <span data-ttu-id="1ac1e-210">Séquence</span><span class="sxs-lookup"><span data-stu-id="1ac1e-210">Sequence</span></span> | <span data-ttu-id="1ac1e-211">Type</span><span class="sxs-lookup"><span data-stu-id="1ac1e-211">Type</span></span>      | <span data-ttu-id="1ac1e-212">Données</span><span class="sxs-lookup"><span data-stu-id="1ac1e-212">Data</span></span>   |
| :---
<span data-ttu-id="1ac1e-213">titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris l’intégration de la logique manuelle RenderTreeBuilder dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-213">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-214">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-214">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-215">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-215">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-216">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-216">'Identity'</span></span>
- <span data-ttu-id="1ac1e-217">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-217">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-218">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-218">'Razor'</span></span>
- <span data-ttu-id="1ac1e-219">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-219">'SignalR' uid:</span></span> 

<span data-ttu-id="1ac1e-220">---: | ---titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris comment incorporer la logique RenderTreeBuilder manuelle dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-220">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-221">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-221">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-222">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-223">'Identity'</span></span>
- <span data-ttu-id="1ac1e-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-225">'Razor'</span></span>
- <span data-ttu-id="1ac1e-226">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1ac1e-227">titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris l’intégration de la logique manuelle RenderTreeBuilder dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-227">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-228">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-228">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-229">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-230">'Identity'</span></span>
- <span data-ttu-id="1ac1e-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-232">'Razor'</span></span>
- <span data-ttu-id="1ac1e-233">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-233">'SignalR' uid:</span></span> 

<span data-ttu-id="1ac1e-234">----- | :----: | | 0 | Nœud de texte | Premier | | 1 | Nœud de texte | Deuxième |</span><span class="sxs-lookup"><span data-stu-id="1ac1e-234">----- | :----: | | 0        | Text node | First  | | 1        | Text node | Second |</span></span>

<span data-ttu-id="1ac1e-235">Ce résultat est identique au cas précédent, donc aucun problème négatif n’existe.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-235">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="1ac1e-236">`someFlag`se trouve `false` sur le deuxième rendu et la sortie est :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-236">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="1ac1e-237">Séquence</span><span class="sxs-lookup"><span data-stu-id="1ac1e-237">Sequence</span></span> | <span data-ttu-id="1ac1e-238">Type</span><span class="sxs-lookup"><span data-stu-id="1ac1e-238">Type</span></span>      | <span data-ttu-id="1ac1e-239">Données</span><span class="sxs-lookup"><span data-stu-id="1ac1e-239">Data</span></span>   |
| :---
<span data-ttu-id="1ac1e-240">titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris l’intégration de la logique manuelle RenderTreeBuilder dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-240">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-241">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-241">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-242">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-243">'Identity'</span></span>
- <span data-ttu-id="1ac1e-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-245">'Razor'</span></span>
- <span data-ttu-id="1ac1e-246">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-246">'SignalR' uid:</span></span> 

<span data-ttu-id="1ac1e-247">---: | ---titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris comment incorporer la logique RenderTreeBuilder manuelle dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-247">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-248">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-248">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-249">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-250">'Identity'</span></span>
- <span data-ttu-id="1ac1e-251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-251">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-252">'Razor'</span></span>
- <span data-ttu-id="1ac1e-253">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1ac1e-254">titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris l’intégration de la logique manuelle RenderTreeBuilder dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-254">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-255">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-255">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-256">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-257">'Identity'</span></span>
- <span data-ttu-id="1ac1e-258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-258">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-259">'Razor'</span></span>
- <span data-ttu-id="1ac1e-260">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-260">'SignalR' uid:</span></span> 

<span data-ttu-id="1ac1e-261">----- | ---titre : « ASP.NET Core Blazor des scénarios avancés » auteur : Description : « en savoir plus sur les scénarios avancés dans Blazor , y compris comment incorporer la logique RenderTreeBuilder manuelle dans une application. »</span><span class="sxs-lookup"><span data-stu-id="1ac1e-261">----- | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="1ac1e-262">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-262">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1ac1e-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-263">'Blazor'</span></span>
- <span data-ttu-id="1ac1e-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-264">'Identity'</span></span>
- <span data-ttu-id="1ac1e-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="1ac1e-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1ac1e-266">'Razor'</span></span>
- <span data-ttu-id="1ac1e-267">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-267">'SignalR' uid:</span></span> 

<span data-ttu-id="1ac1e-268">--- | | 0 | Nœud de texte | Deuxième |</span><span class="sxs-lookup"><span data-stu-id="1ac1e-268">--- | | 0        | Text node | Second |</span></span>

<span data-ttu-id="1ac1e-269">Cette fois-ci, l’algorithme diff constate que *deux* modifications ont été apportées, et l’algorithme génère le script Edit suivant :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-269">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="1ac1e-270">Remplacez la valeur du premier nœud de texte par `Second` .</span><span class="sxs-lookup"><span data-stu-id="1ac1e-270">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="1ac1e-271">Supprimez le deuxième nœud de texte.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-271">Remove the second text node.</span></span>

<span data-ttu-id="1ac1e-272">La génération des numéros de séquence a perdu toutes les informations utiles sur l’emplacement où les `if/else` branches et les boucles étaient présentes dans le code d’origine.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-272">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="1ac1e-273">Il en résulte une comparaison de **deux fois plus longtemps** qu’auparavant.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-273">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="1ac1e-274">Il s’agit d’un exemple trivial.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-274">This is a trivial example.</span></span> <span data-ttu-id="1ac1e-275">Dans des cas plus réalistes avec des structures complexes et profondément imbriquées, et surtout avec des boucles, le coût des performances est généralement plus élevé.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-275">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="1ac1e-276">Au lieu d’identifier immédiatement les blocs de boucle ou les branches qui ont été insérés ou supprimés, l’algorithme diff doit être recurse profondément dans les arborescences de rendu.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-276">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="1ac1e-277">Cela entraîne généralement la création de scripts de modification plus longs, car l’algorithme diff est mal informé de la relation entre les anciennes et les nouvelles structures.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-277">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="1ac1e-278">Conseils et conclusions</span><span class="sxs-lookup"><span data-stu-id="1ac1e-278">Guidance and conclusions</span></span>

* <span data-ttu-id="1ac1e-279">Les performances de l’application sont affectées si les numéros séquentiels sont générés dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-279">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="1ac1e-280">L’infrastructure ne peut pas créer ses propres numéros de séquence automatiquement au moment de l’exécution, car les informations nécessaires n’existent pas, sauf si elles sont capturées au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-280">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="1ac1e-281">N’écrivez pas de longs blocs de logique implémentée manuellement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> .</span><span class="sxs-lookup"><span data-stu-id="1ac1e-281">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="1ac1e-282">Préférer les fichiers *. Razor* et permettre au compilateur de gérer les numéros de séquence.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-282">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="1ac1e-283">Si vous ne pouvez pas éviter <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> une logique manuelle, fractionnez les blocs de code longs en éléments plus petits inclus dans des <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> appels.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-283">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="1ac1e-284">Chaque région a son propre espace distinct pour les numéros de séquence, ce qui vous permet de redémarrer à partir de zéro (ou tout autre nombre arbitraire) à l’intérieur de chaque région.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-284">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="1ac1e-285">Si les numéros de séquence sont codés en dur, l’algorithme diff exige uniquement que les numéros de séquence augmentent dans la valeur.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-285">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="1ac1e-286">La valeur initiale et les écarts ne sont pas pertinents.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-286">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="1ac1e-287">Une option légitime consiste à utiliser le numéro de ligne de code comme numéro de séquence, ou à commencer à partir de zéro et à augmenter par des ou des centaines (ou un intervalle de préférence).</span><span class="sxs-lookup"><span data-stu-id="1ac1e-287">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="1ac1e-288">utilise des numéros de séquence, tandis que d’autres infrastructures d’interface utilisateur de comparaison d’arborescence ne les utilisent pas.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-288"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="1ac1e-289">La comparaison est beaucoup plus rapide lorsque les numéros de séquence sont utilisés et Blazor présente l’avantage d’une étape de compilation qui traite automatiquement les numéros séquentiels pour les développeurs qui créent des fichiers *. Razor* .</span><span class="sxs-lookup"><span data-stu-id="1ac1e-289">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a><span data-ttu-id="1ac1e-290">Effectuer des transferts de données volumineux dans des Blazor applications serveur</span><span class="sxs-lookup"><span data-stu-id="1ac1e-290">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="1ac1e-291">Dans certains scénarios, de grandes quantités de données doivent être transférées entre JavaScript et Blazor .</span><span class="sxs-lookup"><span data-stu-id="1ac1e-291">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="1ac1e-292">En général, les transferts de données volumineux se produisent dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-292">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="1ac1e-293">Les API du système de fichiers du navigateur sont utilisées pour charger ou télécharger un fichier.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-293">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="1ac1e-294">L’interopérabilité avec une bibliothèque tierce est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-294">Interop with a third party library is required.</span></span>

<span data-ttu-id="1ac1e-295">Dans Blazor le serveur, une limitation est en place pour empêcher le passage de messages volumineux qui peuvent entraîner des problèmes de performances.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-295">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="1ac1e-296">Tenez compte des conseils suivants lors du développement de code qui transfère des données entre JavaScript et Blazor :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-296">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="1ac1e-297">Découpez les données en éléments plus petits et envoyez les segments de données de façon séquentielle jusqu’à ce que toutes les données soient reçues par le serveur.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-297">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="1ac1e-298">N’allouez pas d’objets volumineux dans du code JavaScript et C#.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-298">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="1ac1e-299">Ne bloquez pas le thread d’interface utilisateur principal pendant de longues périodes lors de l’envoi ou de la réception de données.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-299">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="1ac1e-300">Libérez la mémoire consommée lorsque le processus est terminé ou annulé.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-300">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="1ac1e-301">Appliquer les exigences supplémentaires suivantes pour des raisons de sécurité :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-301">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="1ac1e-302">Déclarez la taille maximale du fichier ou des données qui peut être passée.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-302">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="1ac1e-303">Déclarez le taux de téléchargement minimal du client au serveur.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-303">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="1ac1e-304">Une fois les données reçues par le serveur, les données peuvent être :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-304">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="1ac1e-305">Stocké temporairement dans une mémoire tampon jusqu’à ce que tous les segments soient collectés.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-305">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="1ac1e-306">Consommé immédiatement.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-306">Consumed immediately.</span></span> <span data-ttu-id="1ac1e-307">Par exemple, les données peuvent être stockées immédiatement dans une base de données ou écrites sur le disque à mesure que chaque segment est reçu.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-307">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="1ac1e-308">La classe de chargeur de fichiers suivante gère l’interopérabilité de JS avec le client.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-308">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="1ac1e-309">La classe de chargeur utilise l’interopérabilité JS pour :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-309">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="1ac1e-310">Interroger le client pour envoyer un segment de données.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-310">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="1ac1e-311">Abandonner la transaction si le délai d’interrogation est dépassé.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-311">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="1ac1e-312">Dans l'exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-312">In the preceding example:</span></span>

* <span data-ttu-id="1ac1e-313">`maxBase64SegmentSize`A la valeur `8192` , qui est calculée à partir de `maxBase64SegmentSize = segmentSize * 4 / 3` .</span><span class="sxs-lookup"><span data-stu-id="1ac1e-313">The `maxBase64SegmentSize` is set to `8192`, which is calculated from `maxBase64SegmentSize = segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="1ac1e-314">Les API de gestion de mémoire .NET Core de bas niveau sont utilisées pour stocker les segments de mémoire sur le serveur dans `uploadedSegments` .</span><span class="sxs-lookup"><span data-stu-id="1ac1e-314">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `uploadedSegments`.</span></span>
* <span data-ttu-id="1ac1e-315">Une `ReceiveFile` méthode est utilisée pour gérer le téléchargement via l’interopérabilité JS :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-315">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="1ac1e-316">La taille du fichier est déterminée en octets par le biais de l’interopérabilité JS avec `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)` .</span><span class="sxs-lookup"><span data-stu-id="1ac1e-316">The file size is determined in bytes through JS interop with `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="1ac1e-317">Le nombre de segments à recevoir est calculé et stocké dans `numberOfSegments` .</span><span class="sxs-lookup"><span data-stu-id="1ac1e-317">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="1ac1e-318">Les segments sont demandés dans une `for` boucle via l’interopérabilité js avec `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)` .</span><span class="sxs-lookup"><span data-stu-id="1ac1e-318">The segments are requested in a `for` loop through JS interop with `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="1ac1e-319">Tous les segments, mais le dernier doit être de 8 192 octets avant le décodage.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-319">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="1ac1e-320">Le client est contraint d’envoyer les données de manière efficace.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-320">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="1ac1e-321">Pour chaque segment reçu, les vérifications sont effectuées avant le décodage <xref:System.Convert.TryFromBase64String%2A> .</span><span class="sxs-lookup"><span data-stu-id="1ac1e-321">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String%2A>.</span></span>
  * <span data-ttu-id="1ac1e-322">Un flux avec les données est retourné en tant que nouveau <xref:System.IO.Stream> ( `SegmentedStream` ) une fois le téléchargement terminé.</span><span class="sxs-lookup"><span data-stu-id="1ac1e-322">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="1ac1e-323">La classe de flux segmentée expose la liste de segments sous la forme d’une classe ReadOnly non recherchée <xref:System.IO.Stream> :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-323">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
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

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

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

<span data-ttu-id="1ac1e-324">Le code suivant implémente les fonctions JavaScript pour recevoir les données :</span><span class="sxs-lookup"><span data-stu-id="1ac1e-324">The following code implements JavaScript functions to receive the data:</span></span>

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
