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
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a>Gérer les erreurs dans Blazor ASP.NET applications Core

Par [Steve Sanderson](https://github.com/SteveSandersonMS)

Cet article décrit Blazor comment gère les exceptions non gérées et comment développer des applications qui détectent et manipulent les erreurs.

## <a name="detailed-errors-during-development"></a>Erreurs détaillées pendant le développement

Lorsqu’une Blazor application ne fonctionne pas correctement pendant le développement, la réception d’informations d’erreur détaillées de l’application aide à dépanner et à résoudre le problème. Lorsqu’une erreur Blazor se produit, les applications affichent une barre d’or au bas de l’écran :

* Pendant le développement, la barre d’or vous dirige vers la console du navigateur, où vous pouvez voir l’exception.
* En production, la barre d’or informe l’utilisateur qu’une erreur s’est produite et recommande de rafraîchir le navigateur.

L’interface utilisateur pour cette expérience Blazor de traitement des erreurs fait partie des modèles de projet.

Dans Blazor une application WebAssembly, personnalisez l’expérience dans le fichier *wwwroot/index.html* :

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

Dans Blazor une application Server, personnalisez l’expérience dans le fichier *Pages/_Host.cshtml* :

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

L’élément `blazor-error-ui` est caché par Blazor les styles inclus dans les modèles *(wwwroot/css/site.css*) et ensuite montré quand une erreur se produit:

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

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a>Comment Blazor une application Server réagit aux exceptions non gérées

BlazorLe serveur est un cadre majestueux. Alors que les utilisateurs interagissent avec une application, ils maintiennent une connexion au serveur connu sous le nom de *circuit*. Le circuit contient des instances de composante active, ainsi que de nombreux autres aspects de l’état, tels que:

* La production la plus récente rendue des composants.
* L’ensemble actuel de délégués chargés de la gestion des événements qui pourraient être déclenchés par des événements côté client.

Si un utilisateur ouvre l’application dans plusieurs onglets de navigateur, il dispose de plusieurs circuits indépendants.

Blazortraite la plupart des exceptions non manipulées comme fatales au circuit où elles se produisent. Si un circuit est terminé en raison d’une exception non gérée, l’utilisateur ne peut continuer à interagir avec l’application qu’en rechargeant la page pour créer un nouveau circuit. Les circuits en dehors de celui qui est terminé, qui sont des circuits pour d’autres utilisateurs ou d’autres onglets de navigateur, ne sont pas affectés. Ce scénario est similaire à&mdash;une application de bureau qui bloque l’application écrasée doit être redémarré, mais d’autres applications ne sont pas affectées.

Un circuit est terminé lorsqu’une exception non gérée se produit pour les raisons suivantes :

* Une exception non gérée laisse souvent le circuit dans un état indéfini.
* Le fonctionnement normal de l’application ne peut pas être garanti après une exception non gérée.
* Les failles de sécurité peuvent apparaître dans l’application si le circuit se poursuit.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Gérer les exceptions non gérées dans le code des développeurs

Pour qu’une application se poursuive après une erreur, l’application doit avoir une logique de manipulation d’erreurs. Les sections ultérieures de cet article décrivent des sources potentielles d’exceptions non manipulées.

En production, ne pas rendre de messages d’exception de cadre ou empiler des traces dans l’interface utilisateur. Le rendu des messages d’exception ou des traces d’empilage pourrait :

* Divulguer des informations sensibles aux utilisateurs finaux.
* Aidez un utilisateur malveillant à découvrir les faiblesses d’une application qui peut compromettre la sécurité de l’application, du serveur ou du réseau.

## <a name="log-errors-with-a-persistent-provider"></a>Erreurs de connexion avec un fournisseur persistant

Si une exception non gérée se produit, <xref:Microsoft.Extensions.Logging.ILogger> l’exception est enregistrée dans les instances configurées dans le conteneur de service. Par défaut, Blazor les applications se connectent pour consoler la sortie avec le fournisseur d’enregistrement de console. Envisagez de vous connecter à un endroit plus permanent avec un fournisseur qui gère la taille du journal et la rotation des journaux. Pour plus d’informations, consultez <xref:fundamentals/logging/index>.

Pendant le Blazor développement, envoie généralement tous les détails des exceptions à la console du navigateur pour aider à débogage. Dans la production, les erreurs détaillées dans la console du navigateur sont désactivées par défaut, ce qui signifie que les erreurs ne sont pas envoyées aux clients, mais les détails complets de l’exception sont toujours connectés côté serveur. Pour plus d’informations, consultez <xref:fundamentals/error-handling>.

Vous devez décider quels incidents enregistrer et le niveau de gravité des incidents enregistrés. Les utilisateurs hostiles pourraient être en mesure de déclencher des erreurs délibérément. Par exemple, ne connectez pas un incident `ProductId` à partir d’une erreur où une inconnue est fournie dans l’URL d’un composant qui affiche les détails du produit. Toutes les erreurs ne doivent pas être traitées comme des incidents de haute gravité pour l’enregistrement.

## <a name="places-where-errors-may-occur"></a>Lieux où des erreurs peuvent se produire

Le code cadre et le code d’application peuvent déclencher des exceptions non gérées dans l’un des endroits suivants :

* [Instantanéisation des composants](#component-instantiation)
* [Méthodes de cycle de vie](#lifecycle-methods)
* [Logique de rendu](#rendering-logic)
* [Gestionnaires d’événements](#event-handlers)
* [Élimination des composants](#component-disposal)
* [Interopérabilité JavaScript](#javascript-interop)
* [BlazorRerendering serveur](#blazor-server-prerendering)

Les exceptions non gérées précédentes sont décrites dans les sections suivantes de cet article.

### <a name="component-instantiation"></a>Instantanéisation des composants

Lorsque Blazor crée une instance d’un composant:

* Le constructeur du composant est invoqué.
* Les constructeurs de tous les services DI non-singleton fournis [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) au [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) constructeur du composant par l’intermédiaire de la directive ou de l’attribut sont invoqués.

Un Blazor circuit Server échoue lorsque tout constructeur exécuté `[Inject]` ou un setter pour toute propriété jette une exception non manipulée. L’exception est fatale parce que le cadre ne peut pas instantané le composant. Si la logique du constructeur peut jeter des exceptions, l’application doit piéger les exceptions à l’aide [d’une déclaration try-catch](/dotnet/csharp/language-reference/keywords/try-catch) avec la manipulation des erreurs et la connexion.

### <a name="lifecycle-methods"></a>Méthodes de cycle de vie

Pendant la durée de Blazor vie d’un composant, invoque les méthodes de [cycle de vie](xref:blazor/lifecycle)suivantes :

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

Si une méthode de cycle de vie jette une exception, de façon synchrone ou asynchrone, l’exception est fatale à un Blazor circuit Server. Pour que les composants traitent des erreurs dans les méthodes du cycle de vie, ajoutez la logique de gestion des erreurs.

Dans l’exemple `OnParametersSetAsync` suivant où appelle une méthode pour obtenir un produit :

* Une exception jetée `ProductRepository.GetProductByIdAsync` dans la méthode `try-catch` est traitée par une déclaration.
* Lorsque `catch` le bloc est exécuté :
  * `loadFailed`est configuré à `true`, qui est utilisé pour afficher un message d’erreur à l’utilisateur.
  * L’erreur est enregistrée.

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Logique de rendu

La majoration déclarative `.razor` dans un fichier de composant `BuildRenderTree`est compilée dans une méthode C appelée . Lorsqu’un composant `BuildRenderTree` rend, exécute et construit une structure de données décrivant les éléments, le texte et les composants pour enfants du composant rendu.

La logique de rendu peut jeter une exception. Un exemple de ce `@someObject.PropertyName` scénario se `@someObject` `null`produit lorsque vous êtes évalué, mais est . Une exception non manipulée lancée par la Blazor logique de rendu est fatale à un circuit Server.

Pour éviter une exception de référence nulle `null` dans le rendu logique, vérifiez un objet avant d’accéder à ses membres. Dans l’exemple `person.Address` suivant, les propriétés ne sont pas accessibles si `person.Address` c’est `null`:

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

Le code précédent `person` suppose que `null`n’est pas . Souvent, la structure du code garantit l’existence d’un objet au moment où le composant est rendu. Dans ces cas, il n’est `null` pas nécessaire de vérifier dans la logique de rendu. Dans l’exemple `person` précédent, peut `person` être garanti d’exister parce qu’il est créé lorsque le composant est instantané.

### <a name="event-handlers"></a>Gestionnaires d’événements

Le code côté client déclenche des invocations de code CMD lorsque les gestionnaires d’événements sont créés à l’aide de :

* `@onclick`
* `@onchange`
* Autres `@on...` attributs
* `@bind`

Le code de gestionnaire d’événements peut jeter une exception non manipulée dans ces scénarios.

Si un gestionnaire d’événements lance une exception non gérée (par exemple, une Blazor requête de base de données échoue), l’exception est fatale à un circuit Server. Si l’application appelle le code qui pourrait échouer pour des raisons externes, piègez les exceptions à l’aide [d’une déclaration try-catch](/dotnet/csharp/language-reference/keywords/try-catch) avec manipulation d’erreurs et enregistrement.

Si le code utilisateur ne piège pas et ne gère pas l’exception, le cadre enregistre l’exception et met fin au circuit.

### <a name="component-disposal"></a>Élimination des composants

Un composant peut être supprimé de l’interface utilisateur, par exemple, parce que l’utilisateur a navigué vers une autre page. Lorsqu’un composant <xref:System.IDisposable?displayProperty=fullName> qui implémente est supprimé de l’interface utilisateur, le cadre appelle la méthode du <xref:System.IDisposable.Dispose*> composant.

Si la méthode `Dispose` du composant jette une exception non manipulée, l’exception est fatale à un Blazor circuit serveur. Si la logique d’élimination peut jeter des exceptions, l’application doit piéger les exceptions à l’aide [d’une déclaration try-catch](/dotnet/csharp/language-reference/keywords/try-catch) avec la manipulation des erreurs et la connexion.

Pour plus d’informations <xref:blazor/lifecycle#component-disposal-with-idisposable>sur l’élimination des composants, voir .

### <a name="javascript-interop"></a>Interopérabilité JavaScript

`IJSRuntime.InvokeAsync<T>`permet au code .NET de passer des appels asynchrones vers l’heure d’exécution JavaScript dans le navigateur de l’utilisateur.

Les conditions suivantes s’appliquent au traitement des erreurs avec `InvokeAsync<T>`:

* Si un `InvokeAsync<T>` appel échoue de façon synchrone, une exception .NET se produit. Un appel `InvokeAsync<T>` à peut échouer, par exemple, parce que les arguments fournis ne peuvent pas être sérialisés. Le code des développeurs doit saisir l’exception. Si le code d’application dans une méthode de gestionnaire d’événements ou Blazor de cycle de vie des composants ne gère pas une exception, l’exception qui en résulte est fatale à un circuit serveur.
* Si un `InvokeAsync<T>` appel échoue asynchronement, <xref:System.Threading.Tasks.Task> le .NET échoue. Un appel `InvokeAsync<T>` peut échouer, par exemple, parce que le code côté `Promise` JavaScript `rejected`jette une exception ou renvoie un qui a terminé comme . Le code des développeurs doit saisir l’exception. Si vous utilisez [l’opérateur d’attente,](/dotnet/csharp/language-reference/keywords/await) envisagez d’envelopper l’appel de méthode dans un énoncé [d’essai-capture](/dotnet/csharp/language-reference/keywords/try-catch) avec la manipulation d’erreur et la connexion. Dans le cas contraire, le code défaillant entraîne une Blazor exception non manipulée qui est fatale à un circuit Server.
* Par défaut, `InvokeAsync<T>` les appels doivent être effectués dans un certain délai, sinon les heures d’appel sont terminées. La période de délai d’attente par défaut est d’une minute. Le délai d’attente protège le code contre une perte de connectivité réseau ou de code JavaScript qui ne renvoie jamais un message d’achèvement. Si les temps d’appel, le résultat `Task` échoue avec un <xref:System.OperationCanceledException>. Pièger et traiter l’exception par l’enregistrement.

De même, le code JavaScript peut initier [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) des appels vers des méthodes .NET indiquées par l’attribut. Si ces méthodes .NET jettent une exception non manipulée :

* L’exception n’est pas considérée Blazor comme fatale à un circuit Server.
* Le côté `Promise` JavaScript est rejeté.

Vous avez la possibilité d’utiliser le code de traitement des erreurs soit sur le côté .NET ou le côté JavaScript de l’appel de méthode.

Pour plus d’informations, consultez les articles suivants :

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>BlazorPrerendering serveur

Blazorles composants peuvent être prédiliés à l’aide [de l’aide à l’étiquette de composant](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) de sorte que leur balisage HTML rendu soit retourné dans le cadre de la demande initiale de HTTP de l’utilisateur. Cela fonctionne par:

* Création d’un nouveau circuit pour tous les composants préditeurs qui font partie de la même page.
* Génération du HTML initial.
* Traiter le `disconnected` circuit comme jusqu’à ce SignalR que le navigateur de l’utilisateur établisse une connexion vers le même serveur. Lorsque la connexion est établie, l’interactivité sur le circuit est reprise et la balisage HTML des composants est mise à jour.

Si un composant jette une exception non manipulée pendant le prerendering, par exemple, pendant une méthode de cycle de vie ou dans la logique de rendu :

* L’exception est fatale au circuit.
* L’exception est jetée vers `Component` le haut de la pile d’appel de l’aide tag. Par conséquent, l’ensemble de la demande HTTP échoue à moins que l’exception ne soit explicitement prise par le code développeur.

Dans des circonstances normales lorsque le pré-processus échoue, continuer à construire et à rendre le composant n’a pas de sens parce qu’un composant de travail ne peut pas être rendu.

Pour tolérer les erreurs qui peuvent se produire pendant le prérendering, la logique de manipulation d’erreur doit être placée à l’intérieur d’un composant qui peut jeter des exceptions. Utilisez des instructions [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) avec la manipulation des erreurs et la connexion. Au lieu `Component` d’envelopper `try-catch` l’aide Tag dans une déclaration, `Component` placez la logique de manipulation d’erreur dans le composant rendu par l’aide Tag.

## <a name="advanced-scenarios"></a>Scénarios avancés

### <a name="recursive-rendering"></a>Rendu récursif

Les composants peuvent être imbriqués de façon récursive. Ceci est utile pour représenter les structures de données récursives. Par exemple, `TreeNode` un composant `TreeNode` peut rendre plus de composants pour chacun des enfants du nœud.

Lors du rendu récursivement, évitez les modèles de codage qui se traduisent par une récursion infinie :

* Ne récursez pas une structure de données qui contient un cycle. Par exemple, ne faites pas un nœud d’arbre dont les enfants s’incluent.
* Ne créez pas une chaîne de mises en page qui contiennent un cycle. Par exemple, ne créez pas de mise en page dont la mise en page est elle-même.
* Ne laissez pas un utilisateur final violer les invariants de récursion (règles) par le biais d’appels malveillants de données ou d’interop JavaScript.

Boucles infinies pendant le rendu :

* Provoque le processus de rendu de continuer pour toujours.
* Équivaut à créer une boucle indéterminée.

Dans ces scénarios, Blazor un circuit serveur affecté échoue, et le thread tente généralement de :

* Consommez autant de temps de processeur que le système d’exploitation, indéfiniment.
* Consommez une quantité illimitée de mémoire du serveur. Consommer une mémoire illimitée équivaut au scénario où une boucle indéterminée ajoute des entrées à une collection sur chaque itération.

Pour éviter les modèles de récursion infinis, assurez-vous que le code de rendu récursif contient des conditions d’arrêt appropriées.

### <a name="custom-render-tree-logic"></a>Logique d’arbre de rendu personnalisée

La Blazor plupart des composants sont implémentés comme des `RenderTreeBuilder` fichiers *.razor* et sont compilés pour produire une logique qui fonctionne sur un pour rendre leur sortie. Un développeur peut `RenderTreeBuilder` implémenter manuellement la logique à l’aide du code procédural C. Pour plus d’informations, consultez <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.

> [!WARNING]
> L’utilisation de la logique manuelle de constructeur d’arbres de rendu est considérée comme un scénario avancé et dangereux, non recommandé pour le développement général des composants.

Si `RenderTreeBuilder` le code est écrit, le développeur doit garantir la justesse du code. Par exemple, le développeur doit s’assurer que :

* Appels `OpenElement` vers `CloseElement` et sont correctement équilibrés.
* Les attributs ne sont ajoutés qu’aux bons endroits.

La logique incorrecte de fabricant d’arbres de rendu manuel peut causer un comportement arbitraire indéfini, y compris des plantages, des pends de serveur et des vulnérabilités de sécurité.

Considérez la logique manuelle de constructeur d’arbre de rendu sur le même niveau de complexité et avec le même niveau de *danger* que le code d’assemblage d’écriture ou les instructions MSIL à la main.
