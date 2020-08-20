---
title: Blazor WebAssemblyMeilleures pratiques en matière de performances de ASP.net Core
author: pranavkm
description: Conseils pour améliorer les performances dans les Blazor WebAssembly applications ASP.net Core et éviter les problèmes de performances courants.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 819947be90e7f09c7ba853df1af1f3c7066c0219
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625815"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>Blazor WebAssemblyMeilleures pratiques en matière de performances de ASP.net Core

Par [Pranav Krishnamoorthy](https://github.com/pranavkm)

Cet article fournit des instructions pour ASP.NET Core Blazor WebAssembly meilleures pratiques en matière de performances.

## <a name="avoid-unnecessary-component-renders"></a>Éviter les rendus de composants inutiles

Blazorl’algorithme de différenciation de l’algorithme évite le rerendu d’un composant lorsque l’algorithme constate que le composant n’a pas changé. Remplacement <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> pour un contrôle affiné sur le rendu des composants.

Si vous créez un composant d’interface utilisateur qui n’est jamais modifié après le rendu initial, configurez <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pour retourner `false` :

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

La plupart des applications n’ont pas besoin d’un contrôle affiné, mais elles <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> peuvent être utilisées pour afficher de manière sélective un composant répondant à un événement d’interface utilisateur. L’utilisation de <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> peut également être importante dans les scénarios où un grand nombre de composants sont rendus. Imaginez une grille, où l’utilisation de <xref:Microsoft.AspNetCore.Components.EventCallback> dans un composant dans une cellule de la grille appelle <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> sur la grille. L’appel <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> de provoque un nouveau rendu de chaque composant enfant. Si seulement un petit nombre de cellules requièrent un rerendu, utilisez <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pour éviter une altération des performances des rendus inutiles.

Dans l’exemple suivant :

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> est substitué et défini sur la valeur du <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> champ, qui est initialement le `false` moment où le composant est chargé.
* Lorsque le bouton est sélectionné, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> a la valeur `true` , ce qui force le rendu du composant avec le mis à jour `currentCount` .
* Immédiatement après le rerendu, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> affecte à la valeur de retour la valeur <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> `false` pour empêcher un nouveau rendu jusqu’à la prochaine sélection du bouton.

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

Pour plus d'informations, consultez <xref:blazor/components/lifecycle#after-component-render>.

## <a name="virtualize-re-usable-fragments"></a>Virtualiser les fragments réutilisables

Les composants offrent une approche pratique pour créer des fragments réutilisables de code et de balisage. En général, nous vous recommandons de créer des composants individuels qui correspondent le mieux aux exigences de l’application. L’inconvénient est que chaque composant enfant supplémentaire contribue au temps total nécessaire pour afficher un composant parent. Pour la plupart des applications, la surcharge supplémentaire est négligeable. Les applications qui produisent un grand nombre de composants doivent envisager d’utiliser des stratégies pour réduire la charge de traitement, par exemple pour limiter le nombre de composants rendus.

Par exemple, une grille ou une liste qui restitue des centaines de lignes contenant des composants nécessite une utilisation intensive du processeur pour le rendu. Envisagez de virtualiser une disposition de grille ou de liste afin que seul un sous-ensemble des composants soit rendu à un moment donné. Pour obtenir un exemple de rendu de sous-ensemble de composants, consultez les composants suivants dans l' [ `Virtualization` exemple d’application (référentiel GitHub ASPNET/Samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize` Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ) : composant écrit en C# qui implémente <xref:Microsoft.AspNetCore.Components.ComponentBase> pour afficher un ensemble de lignes de données météorologiques en fonction du défilement de l’utilisateur.
* `FetchData` Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ) : utilise le `Virtualize` composant pour afficher 25 lignes de données météorologiques à la fois.

## <a name="avoid-javascript-interop-to-marshal-data"></a>Éviter l’interopérabilité JavaScript pour marshaler des données

Dans Blazor WebAssembly , un appel Interop JavaScript (js) doit traverser la limite Webassembly-js. La sérialisation et la désérialisation du contenu entre les deux contextes entraînent la surcharge du traitement de l’application. Les appels d’interopérabilité JS fréquents ont souvent des répercussions néfastes sur les performances. Pour réduire le marshalling des données à travers la limite, déterminez si l’application peut regrouper de nombreuses charges utiles dans une seule grande charge utile pour éviter le basculement de contexte élevé entre webassembly et JS.

## <a name="use-systemtextjson"></a>Utiliser System.Text.Jssur

Blazorl’implémentation de l’interopérabilité JS de est basée sur <xref:System.Text.Json> , qui est une bibliothèque de SÉRIALISATION JSON haute performance avec une faible allocation de mémoire. L’utilisation <xref:System.Text.Json> de n’entraîne pas de taille de charge utile d’application supplémentaire par rapport à l’ajout d’une ou de plusieurs autres bibliothèques JSON.

Pour obtenir des conseils sur la migration, consultez [Comment migrer de `Newtonsoft.Json` vers `System.Text.Json` ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>Utiliser les API d’interopérabilité JS synchrones et démarshalées lorsque cela est approprié

Blazor WebAssembly offre deux versions supplémentaires de <xref:Microsoft.JSInterop.IJSRuntime> sur la version unique disponible pour les Blazor Server applications :

* <xref:Microsoft.JSInterop.IJSInProcessRuntime> permet d’appeler des appels d’interopérabilité JS de manière synchrone, ce qui a moins de surcharge que les versions asynchrones :

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

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> autorise les appels Interop JS démarshalés :

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
  > Bien que l’utilisation <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> de ait la surcharge la plus faible des approches de l’interopérabilité de js, les API JavaScript requises pour interagir avec ces API ne sont actuellement pas documentées et sujettes à des modifications importantes dans les versions ultérieures.

## <a name="reduce-app-size"></a>Réduire la taille de l’application

### <a name="intermediate-language-il-linking"></a>Liaison en langage intermédiaire (IL)

La [liaison d’une Blazor WebAssembly application](xref:blazor/host-and-deploy/configure-linker) réduit la taille de l’application en découpant le code inutilisé dans les fichiers binaires de l’application. Par défaut, l’éditeur de liens est activé uniquement lors de la génération de la `Release` Configuration. Pour tirer parti de ce, publiez l’application pour le déploiement à l’aide de la [`dotnet publish`](/dotnet/core/tools/dotnet-publish) commande avec l’option [-c |--configuration](/dotnet/core/tools/dotnet-publish#options) définie sur `Release` :

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a>Charger des assemblys en différé

Chargez les assemblys au moment de l’exécution lorsque les assemblys sont requis par un itinéraire. Pour plus d'informations, consultez <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="compression"></a>Compression

Quand une Blazor WebAssembly application est publiée, la sortie est compressée de manière statique lors de la publication afin de réduire la taille de l’application et de supprimer la surcharge liée à la compression du Runtime. Blazor s’appuie sur le serveur pour effectuer des negotation de contenu et traiter des fichiers compressés statiquement.

Une fois qu’une application a été déployée, vérifiez que l’application dessert des fichiers compressés. Examinez l’onglet réseau dans le Outils de développement d’un navigateur et vérifiez que les fichiers sont pris en charge avec `Content-Encoding: br` ou `Content-Encoding: gz` . Si l’hôte ne dessert pas de fichiers compressés, suivez les instructions de la procédure <xref:blazor/host-and-deploy/webassembly#compression> .

### <a name="disable-unused-features"></a>Désactiver les fonctionnalités inutilisées

Blazor WebAssemblyle runtime de comprend les fonctionnalités .NET suivantes qui peuvent être désactivées si l’application n’en a pas besoin pour une plus petite taille de charge utile :

* Un fichier de données est inclus pour corriger les informations de fuseau horaire. Si l’application n’a pas besoin de cette fonctionnalité, envisagez de la désactiver en définissant la `BlazorEnableTimeZoneSupport` propriété MSBuild dans le fichier projet de l’application sur `false` :

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* Les informations de classement sont incluses pour que les API telles que <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> fonctionnent correctement. Si vous êtes certain que l’application n’a pas besoin des données de classement, envisagez de la désactiver en définissant la `BlazorWebAssemblyPreserveCollationData` propriété MSBuild dans le fichier projet de l’application sur `false` :

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
