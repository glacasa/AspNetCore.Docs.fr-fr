---
<span data-ttu-id="14f6a-101">titre : « ASP.NET Core Blazor les meilleures pratiques en matière de performances de Webassembly » auteur : Description : « conseils pour améliorer les performances dans ASP.net Core Blazor applications webassembly et éviter les problèmes de performances courants ».</span><span class="sxs-lookup"><span data-stu-id="14f6a-101">title: 'ASP.NET Core Blazor WebAssembly performance best practices' author: description: 'Tips for increasing performance in ASP.NET Core Blazor WebAssembly apps and avoiding common performance problems.'</span></span>
<span data-ttu-id="14f6a-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="14f6a-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="14f6a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="14f6a-103">'Blazor'</span></span>
- <span data-ttu-id="14f6a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="14f6a-104">'Identity'</span></span>
- <span data-ttu-id="14f6a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="14f6a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="14f6a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="14f6a-106">'Razor'</span></span>
- <span data-ttu-id="14f6a-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="14f6a-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="14f6a-108">BlazorMeilleures pratiques relatives aux performances de ASP.net Core Webassembly</span><span class="sxs-lookup"><span data-stu-id="14f6a-108">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="14f6a-109">Par [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="14f6a-109">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="14f6a-110">Cet article fournit des instructions pour ASP.NET Core Blazor meilleures pratiques en matière de performances Webassembly.</span><span class="sxs-lookup"><span data-stu-id="14f6a-110">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="14f6a-111">Éviter les rendus de composants inutiles</span><span class="sxs-lookup"><span data-stu-id="14f6a-111">Avoid unnecessary component renders</span></span>

Blazor<span data-ttu-id="14f6a-112">l’algorithme de différenciation de l’algorithme évite le rerendu d’un composant lorsque l’algorithme constate que le composant n’a pas changé.</span><span class="sxs-lookup"><span data-stu-id="14f6a-112">'s diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="14f6a-113">Remplacement <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> pour un contrôle affiné sur le rendu des composants.</span><span class="sxs-lookup"><span data-stu-id="14f6a-113">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="14f6a-114">Si vous créez un composant d’interface utilisateur qui n’est jamais modifié après le rendu initial, configurez <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pour retourner `false` :</span><span class="sxs-lookup"><span data-stu-id="14f6a-114">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="14f6a-115">La plupart des applications n’ont pas besoin d’un contrôle affiné, mais elles <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> peuvent également être utilisées pour restituer de manière sélective un composant qui répond à un événement d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="14f6a-115">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can also be used to selectively render a component responding to a UI event.</span></span>

<span data-ttu-id="14f6a-116">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="14f6a-116">In the following example:</span></span>

* <span data-ttu-id="14f6a-117"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>est substitué et défini sur la valeur du <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> champ, qui est initialement le `false` moment où le composant est chargé.</span><span class="sxs-lookup"><span data-stu-id="14f6a-117"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="14f6a-118">Lorsque le bouton est sélectionné, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> a la valeur `true` , ce qui force le rendu du composant avec le mis à jour `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="14f6a-118">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="14f6a-119">Immédiatement après le rerendu, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> affecte à la valeur de retour la valeur <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> `false` pour empêcher un nouveau rendu jusqu’à la prochaine sélection du bouton.</span><span class="sxs-lookup"><span data-stu-id="14f6a-119">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="14f6a-120">Pour plus d'informations, consultez <xref:blazor/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="14f6a-120">For more information, see <xref:blazor/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="14f6a-121">Virtualiser les fragments réutilisables</span><span class="sxs-lookup"><span data-stu-id="14f6a-121">Virtualize re-usable fragments</span></span>

<span data-ttu-id="14f6a-122">Les composants offrent une approche pratique pour créer des fragments réutilisables de code et de balisage.</span><span class="sxs-lookup"><span data-stu-id="14f6a-122">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="14f6a-123">En général, nous vous recommandons de créer des composants individuels qui correspondent le mieux aux exigences de l’application.</span><span class="sxs-lookup"><span data-stu-id="14f6a-123">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="14f6a-124">L’inconvénient est que chaque composant enfant supplémentaire contribue au temps total nécessaire pour afficher un composant parent.</span><span class="sxs-lookup"><span data-stu-id="14f6a-124">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="14f6a-125">Pour la plupart des applications, la surcharge supplémentaire est négligeable.</span><span class="sxs-lookup"><span data-stu-id="14f6a-125">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="14f6a-126">Les applications qui produisent un grand nombre de composants doivent envisager d’utiliser des stratégies pour réduire la charge de traitement, par exemple pour limiter le nombre de composants rendus.</span><span class="sxs-lookup"><span data-stu-id="14f6a-126">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="14f6a-127">Par exemple, une grille ou une liste qui restitue des centaines de lignes contenant des composants nécessite une utilisation intensive du processeur pour le rendu.</span><span class="sxs-lookup"><span data-stu-id="14f6a-127">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="14f6a-128">Envisagez de virtualiser une disposition de grille ou de liste afin que seul un sous-ensemble des composants soit rendu à un moment donné.</span><span class="sxs-lookup"><span data-stu-id="14f6a-128">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="14f6a-129">Pour obtenir un exemple de rendu de sous-ensemble de composants, consultez les composants suivants dans l' [exemple d’application de virtualisation (référentiel GitHub ASPNET/Samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="14f6a-129">For an example of component subset rendering, see the following components in the [Virtualization sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="14f6a-130">`Virtualize`composant ([Shared/virtualiser. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)) : composant écrit en C# qui implémente <xref:Microsoft.AspNetCore.Components.ComponentBase> pour afficher un ensemble de lignes de données météorologiques en fonction du défilement de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="14f6a-130">`Virtualize` component ([Shared/Virtualize.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="14f6a-131">`FetchData`composant ([pages/fetchData. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)) : utilise le `Virtualize` composant pour afficher 25 lignes de données météorologiques à la fois.</span><span class="sxs-lookup"><span data-stu-id="14f6a-131">`FetchData` component ([Pages/FetchData.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="14f6a-132">Éviter l’interopérabilité JavaScript pour marshaler des données</span><span class="sxs-lookup"><span data-stu-id="14f6a-132">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="14f6a-133">Dans Blazor Webassembly, un appel Interop JavaScript (js) doit traverser la limite Webassembly-js.</span><span class="sxs-lookup"><span data-stu-id="14f6a-133">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="14f6a-134">La sérialisation et la désérialisation du contenu entre les deux contextes entraînent la surcharge du traitement de l’application.</span><span class="sxs-lookup"><span data-stu-id="14f6a-134">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="14f6a-135">Les appels d’interopérabilité JS fréquents ont souvent des répercussions néfastes sur les performances.</span><span class="sxs-lookup"><span data-stu-id="14f6a-135">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="14f6a-136">Pour réduire le marshalling des données à travers la limite, déterminez si l’application peut regrouper de nombreuses charges utiles dans une seule grande charge utile pour éviter le basculement de contexte élevé entre webassembly et JS.</span><span class="sxs-lookup"><span data-stu-id="14f6a-136">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="14f6a-137">Utiliser System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="14f6a-137">Use System.Text.Json</span></span>

Blazor<span data-ttu-id="14f6a-138">l’implémentation de l’interopérabilité JS de est basée sur <xref:System.Text.Json> , qui est une bibliothèque de SÉRIALISATION JSON haute performance avec une faible allocation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="14f6a-138">'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="14f6a-139">L’utilisation <xref:System.Text.Json> de n’entraîne pas de taille de charge utile d’application supplémentaire par rapport à l’ajout d’une ou de plusieurs autres bibliothèques JSON.</span><span class="sxs-lookup"><span data-stu-id="14f6a-139">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="14f6a-140">Pour plus d’informations sur la migration, consultez [How to Migrate from Newtonsoft. JSON to System. Text. JSON](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="14f6a-140">For migration guidance, see [How to migrate from Newtonsoft.Json to System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="14f6a-141">Utiliser les API d’interopérabilité JS synchrones et démarshalées lorsque cela est approprié</span><span class="sxs-lookup"><span data-stu-id="14f6a-141">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

Blazor<span data-ttu-id="14f6a-142">Webassembly offre deux versions supplémentaires de <xref:Microsoft.JSInterop.IJSRuntime> sur la version unique disponible pour les Blazor applications serveur :</span><span class="sxs-lookup"><span data-stu-id="14f6a-142"> WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="14f6a-143"><xref:Microsoft.JSInterop.IJSInProcessRuntime>permet d’appeler des appels d’interopérabilité JS de manière synchrone, ce qui a moins de surcharge que les versions asynchrones :</span><span class="sxs-lookup"><span data-stu-id="14f6a-143"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="14f6a-144"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>autorise les appels Interop JS démarshalés :</span><span class="sxs-lookup"><span data-stu-id="14f6a-144"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="14f6a-145">Bien que l’utilisation <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> de ait la surcharge la plus faible des approches de l’interopérabilité de js, les API JavaScript requises pour interagir avec ces API ne sont actuellement pas documentées et sujettes à des modifications importantes dans les versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="14f6a-145">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="14f6a-146">Réduire la taille de l’application</span><span class="sxs-lookup"><span data-stu-id="14f6a-146">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="14f6a-147">Liaison en langage intermédiaire (IL)</span><span class="sxs-lookup"><span data-stu-id="14f6a-147">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="14f6a-148">[Liaison d’un Blazor L’application webassembly](xref:host-and-deploy/blazor/configure-linker) réduit la taille de l’application en découpant le code inutilisé dans les fichiers binaires de l’application.</span><span class="sxs-lookup"><span data-stu-id="14f6a-148">[Linking a Blazor WebAssembly app](xref:host-and-deploy/blazor/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="14f6a-149">Par défaut, l’éditeur de liens est activé uniquement lors de la génération de la `Release` Configuration.</span><span class="sxs-lookup"><span data-stu-id="14f6a-149">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="14f6a-150">Pour tirer parti de ce, publiez l’application pour le déploiement à l’aide de la commande [dotnet Publish](/dotnet/core/tools/dotnet-publish) avec l’option [-c |--configuration](/dotnet/core/tools/dotnet-publish#options) définie sur `Release` :</span><span class="sxs-lookup"><span data-stu-id="14f6a-150">To benefit from this, publish the app for deployment using the [dotnet publish](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="disable-unused-features"></a><span data-ttu-id="14f6a-151">Désactiver les fonctionnalités inutilisées</span><span class="sxs-lookup"><span data-stu-id="14f6a-151">Disable unused features</span></span>

Blazor<span data-ttu-id="14f6a-152">Le runtime de webassembly comprend les fonctionnalités .NET suivantes qui peuvent être désactivées si l’application n’en a pas besoin pour une plus petite taille de charge utile :</span><span class="sxs-lookup"><span data-stu-id="14f6a-152"> WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="14f6a-153">Un fichier de données est inclus pour corriger les informations de fuseau horaire.</span><span class="sxs-lookup"><span data-stu-id="14f6a-153">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="14f6a-154">Si l’application n’a pas besoin de cette fonctionnalité, envisagez de la désactiver en définissant la `BlazorEnableTimeZoneSupport` propriété MSBuild dans le fichier projet de l’application sur `false` :</span><span class="sxs-lookup"><span data-stu-id="14f6a-154">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="14f6a-155">Les informations de classement sont incluses pour que les API telles que <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> fonctionnent correctement.</span><span class="sxs-lookup"><span data-stu-id="14f6a-155">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="14f6a-156">Si vous êtes certain que l’application n’a pas besoin des données de classement, envisagez de la désactiver en définissant la `BlazorWebAssemblyPreserveCollationData` propriété MSBuild dans le fichier projet de l’application sur `false` :</span><span class="sxs-lookup"><span data-stu-id="14f6a-156">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
