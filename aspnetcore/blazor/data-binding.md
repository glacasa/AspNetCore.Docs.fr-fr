---
title: ASP.NET Liaison de Blazor données de base
author: guardrex
description: Renseignez-vous sur les fonctionnalités Blazor de liaison de données pour les composants et les éléments DOM dans les applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: a7b3730dad48b5bbb6134dab181051da4e3651b4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320954"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a><span data-ttu-id="960b7-103">ASP.NET Liaison de Blazor données de base</span><span class="sxs-lookup"><span data-stu-id="960b7-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="960b7-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="960b7-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="960b7-105">Les composants Razor fournissent des fonctionnalités [`@bind`](xref:mvc/views/razor#bind) de liaison de données via un attribut d’élément HTML nommé avec une valeur d’expression de champ, de propriété ou de rasoir.</span><span class="sxs-lookup"><span data-stu-id="960b7-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="960b7-106">L’exemple suivant `CurrentValue` lie la propriété à la valeur de la boîte à texte :</span><span class="sxs-lookup"><span data-stu-id="960b7-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="960b7-107">Lorsque la boîte de texte perd la mise au point, la valeur de la propriété est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="960b7-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="960b7-108">La boîte de texte n’est mise à jour dans l’interface utilisateur que lorsque le composant est rendu, et non en réponse à la modification de la valeur de la propriété.</span><span class="sxs-lookup"><span data-stu-id="960b7-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="960b7-109">Étant donné que les composants se rendent après l’exécution du code du gestionnaire d’événements, les mises à jour de propriété sont *généralement* reflétées dans l’interface utilisateur immédiatement après qu’un gestionnaire d’événements est déclenché.</span><span class="sxs-lookup"><span data-stu-id="960b7-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="960b7-110">`@bind` L’utilisation `CurrentValue` avec`<input @bind="CurrentValue" />`la propriété ( ) est essentiellement équivalente à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="960b7-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="960b7-111">Lorsque le composant est `value` rendu, l’élément `CurrentValue` d’entrée provient de la propriété.</span><span class="sxs-lookup"><span data-stu-id="960b7-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="960b7-112">Lorsque l’utilisateur tape dans la boîte `onchange` de texte et `CurrentValue` modifie la mise au point de l’élément, l’événement est déclenché et la propriété est définie à la valeur modifiée.</span><span class="sxs-lookup"><span data-stu-id="960b7-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="960b7-113">En réalité, la génération de `@bind` code est plus complexe car gère les cas où des conversions de type sont effectuées.</span><span class="sxs-lookup"><span data-stu-id="960b7-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="960b7-114">En principe, `@bind` associe la valeur actuelle `value` d’une expression à un attribut et gère les changements à l’aide du gestionnaire enregistré.</span><span class="sxs-lookup"><span data-stu-id="960b7-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="960b7-115">Lier une propriété ou un champ `@bind:event` sur `event` d’autres événements en incluant également un attribut avec un paramètre.</span><span class="sxs-lookup"><span data-stu-id="960b7-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="960b7-116">L’exemple suivant `CurrentValue` lie la `oninput` propriété sur l’événement :</span><span class="sxs-lookup"><span data-stu-id="960b7-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="960b7-117">Contrairement `onchange`à , qui s’allume lorsque l’élément perd la mise au point, `oninput` se déclenche lorsque la valeur de la boîte de texte change.</span><span class="sxs-lookup"><span data-stu-id="960b7-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="960b7-118">Utiliser `@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` avec la syntaxe pour `value`lier les attributs d’éléments autres que .</span><span class="sxs-lookup"><span data-stu-id="960b7-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="960b7-119">Dans l’exemple suivant, le style du `_paragraphStyle` paragraphe est mis à jour lorsque la valeur change :</span><span class="sxs-lookup"><span data-stu-id="960b7-119">In the following example, the paragraph's style is updated when the `_paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="_paragraphStyle" />
</p>

<p @bind-style="_paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string _paragraphStyle = "color:red";
}
```

<span data-ttu-id="960b7-120">La liaison d’attribut est sensible au cas.</span><span class="sxs-lookup"><span data-stu-id="960b7-120">Attribute binding is case sensitive.</span></span> <span data-ttu-id="960b7-121">Par exemple, `@bind` est `@Bind` valide, et est invalide.</span><span class="sxs-lookup"><span data-stu-id="960b7-121">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="960b7-122">Valeurs non imposables</span><span class="sxs-lookup"><span data-stu-id="960b7-122">Unparsable values</span></span>

<span data-ttu-id="960b7-123">Lorsqu’un utilisateur fournit une valeur impardonnable à un élément lié aux données, la valeur impardonnable est automatiquement revenue à sa valeur antérieure lorsque l’événement de liaison est déclenché.</span><span class="sxs-lookup"><span data-stu-id="960b7-123">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="960b7-124">Examinez le cas suivant :</span><span class="sxs-lookup"><span data-stu-id="960b7-124">Consider the following scenario:</span></span>

* <span data-ttu-id="960b7-125">Un `<input>` élément est `int` lié à un `123`type avec une valeur initiale de :</span><span class="sxs-lookup"><span data-stu-id="960b7-125">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="960b7-126">L’utilisateur met à jour `123.45` la valeur de l’élément dans la page et modifie la mise au point de l’élément.</span><span class="sxs-lookup"><span data-stu-id="960b7-126">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="960b7-127">Dans le scénario précédent, la valeur de `123`l’élément est retournée à .</span><span class="sxs-lookup"><span data-stu-id="960b7-127">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="960b7-128">Lorsque la `123.45` valeur est rejetée en `123`faveur de la valeur originale de , l’utilisateur comprend que leur valeur n’a pas été acceptée.</span><span class="sxs-lookup"><span data-stu-id="960b7-128">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="960b7-129">Par défaut, la liaison s’applique à l’événement de `onchange` l’élément (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="960b7-129">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="960b7-130">Utiliser `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` pour déclencher la liaison sur un événement différent.</span><span class="sxs-lookup"><span data-stu-id="960b7-130">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="960b7-131">Pour `oninput` l’événement (`@bind:event="oninput"`), la réversion se produit après tout coup de clés qui introduit une valeur impardonnable.</span><span class="sxs-lookup"><span data-stu-id="960b7-131">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="960b7-132">Lorsque vous `oninput` ciblez `int`l’événement avec un type lié, `.` un utilisateur est empêché de taper un personnage.</span><span class="sxs-lookup"><span data-stu-id="960b7-132">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="960b7-133">Un `.` personnage est immédiatement supprimé, de sorte que l’utilisateur reçoit une rétroaction immédiate que seuls les numéros entiers sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="960b7-133">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="960b7-134">Il existe des scénarios où `oninput` le retour de la valeur sur l’événement n’est pas `<input>` idéal, comme lorsque l’utilisateur doit être autorisé à effacer une valeur impardonnable.</span><span class="sxs-lookup"><span data-stu-id="960b7-134">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="960b7-135">Les solutions de rechange comprennent :</span><span class="sxs-lookup"><span data-stu-id="960b7-135">Alternatives include:</span></span>

* <span data-ttu-id="960b7-136">N’utilisez pas `oninput` l’événement.</span><span class="sxs-lookup"><span data-stu-id="960b7-136">Don't use the `oninput` event.</span></span> <span data-ttu-id="960b7-137">Utilisez l’événement par défaut `onchange` (seulement spécifier `@bind="{PROPERTY OR FIELD}"`), où une valeur invalide n’est pas retournée jusqu’à ce que l’élément perd la mise au point.</span><span class="sxs-lookup"><span data-stu-id="960b7-137">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="960b7-138">Lier à un type `int?` nul, tel que ou `string`, et de fournir la logique personnalisée pour gérer les entrées invalides.</span><span class="sxs-lookup"><span data-stu-id="960b7-138">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="960b7-139">Utilisez un composant de `InputNumber` validation `InputDate` [de formulaire,](xref:blazor/forms-validation)tel que ou .</span><span class="sxs-lookup"><span data-stu-id="960b7-139">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="960b7-140">Les composants de validation des formulaires ont un support intégré pour gérer les entrées non valides.</span><span class="sxs-lookup"><span data-stu-id="960b7-140">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="960b7-141">Composants de validation de formulaire :</span><span class="sxs-lookup"><span data-stu-id="960b7-141">Form validation components:</span></span>
  * <span data-ttu-id="960b7-142">Permettre à l’utilisateur de fournir des `EditContext`entrées non valides et de recevoir des erreurs de validation sur le .</span><span class="sxs-lookup"><span data-stu-id="960b7-142">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="960b7-143">Afficher les erreurs de validation dans l’interface utilisateur sans interférer avec l’utilisateur entrant des données Webform supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="960b7-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="960b7-144">Chaînes de format</span><span class="sxs-lookup"><span data-stu-id="960b7-144">Format strings</span></span>

<span data-ttu-id="960b7-145">La liaison <xref:System.DateTime> de données [`@bind:format`](xref:mvc/views/razor#bind)fonctionne avec des chaînes de format utilisant .</span><span class="sxs-lookup"><span data-stu-id="960b7-145">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="960b7-146">D’autres expressions de format, telles que les formats de devise ou de numéro, ne sont pas disponibles pour le moment.</span><span class="sxs-lookup"><span data-stu-id="960b7-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="960b7-147">Dans le code `<input>` précédent, le type`type`de champ `text`de l’élément ( ) par défaut à .</span><span class="sxs-lookup"><span data-stu-id="960b7-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="960b7-148">`@bind:format`est pris en charge pour lier les types .NET suivants:</span><span class="sxs-lookup"><span data-stu-id="960b7-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="960b7-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="960b7-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="960b7-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="960b7-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="960b7-151">L’attribut `@bind:format` spécifie le `value` format `<input>` de date à appliquer à l’élément.</span><span class="sxs-lookup"><span data-stu-id="960b7-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="960b7-152">Le format est également utilisé pour analyser `onchange` la valeur lorsqu’un événement se produit.</span><span class="sxs-lookup"><span data-stu-id="960b7-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="960b7-153">Spécifier un `date` format pour le Blazor type de champ n’est pas recommandé parce qu’il a intégré le support aux dates de format.</span><span class="sxs-lookup"><span data-stu-id="960b7-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="960b7-154">Malgré la recommandation, n’utilisez que le `yyyy-MM-dd` format de date pour `date` la liaison pour fonctionner correctement si un format est fourni avec le type de champ :</span><span class="sxs-lookup"><span data-stu-id="960b7-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="960b7-155">Liaison parent-enfant avec paramètres de composant</span><span class="sxs-lookup"><span data-stu-id="960b7-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="960b7-156">La liaison reconnaît les `@bind-{PROPERTY}` paramètres des composants, où la valeur de la propriété d’un composant parent jusqu’à une composante enfant.</span><span class="sxs-lookup"><span data-stu-id="960b7-156">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="960b7-157">La liaison d’un enfant à un parent est couverte dans la liaison enfant-parent avec la section [de liaison enchaînée.](#child-to-parent-binding-with-chained-bind)</span><span class="sxs-lookup"><span data-stu-id="960b7-157">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="960b7-158">La composante enfant`ChildComponent`suivante `Year` ( ) `YearChanged` a un paramètre de composant et un rappel :</span><span class="sxs-lookup"><span data-stu-id="960b7-158">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="960b7-159">`EventCallback<T>`est expliqué <xref:blazor/event-handling#eventcallback>dans .</span><span class="sxs-lookup"><span data-stu-id="960b7-159">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="960b7-160">La composante parente suivante utilise :</span><span class="sxs-lookup"><span data-stu-id="960b7-160">The following parent component uses:</span></span>

* <span data-ttu-id="960b7-161">`ChildComponent`et lie `ParentYear` le paramètre du `Year` parent au paramètre sur la composante enfant.</span><span class="sxs-lookup"><span data-stu-id="960b7-161">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="960b7-162">L’événement `onclick` est utilisé `ChangeTheYear` pour déclencher la méthode.</span><span class="sxs-lookup"><span data-stu-id="960b7-162">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="960b7-163">Pour plus d’informations, consultez <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="960b7-163">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="960b7-164">Le `ParentComponent` chargement de la produit la majoration suivante :</span><span class="sxs-lookup"><span data-stu-id="960b7-164">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="960b7-165">Si la valeur `ParentYear` de la propriété est modifiée `ParentComponent`en `Year` sélectionnant `ChildComponent` le bouton dans le , la propriété de la est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="960b7-165">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="960b7-166">La nouvelle `Year` valeur de est rendue dans `ParentComponent` l’interface utilisateur lorsque l’assurance-chômage est rendue:</span><span class="sxs-lookup"><span data-stu-id="960b7-166">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="960b7-167">Le `Year` paramètre est liant `YearChanged` parce qu’il a `Year` un événement compagnon qui correspond au type de paramètre.</span><span class="sxs-lookup"><span data-stu-id="960b7-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="960b7-168">Par convention, `<ChildComponent @bind-Year="ParentYear" />` est essentiellement équivalent à l’écriture:</span><span class="sxs-lookup"><span data-stu-id="960b7-168">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="960b7-169">En général, une propriété peut être liée à `@bind-{PROPRETY}:event` un gestionnaire d’événement correspondant en incluant un attribut.</span><span class="sxs-lookup"><span data-stu-id="960b7-169">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="960b7-170">Par exemple, `MyProp` la propriété `MyEventHandler` peut être liée à l’utilisation des deux attributs suivants :</span><span class="sxs-lookup"><span data-stu-id="960b7-170">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="960b7-171">Liaison enfant-parent avec liaison enchaînée</span><span class="sxs-lookup"><span data-stu-id="960b7-171">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="960b7-172">Un scénario courant consiste à enchaîner un paramètre lié aux données à un élément de page dans la sortie du composant.</span><span class="sxs-lookup"><span data-stu-id="960b7-172">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="960b7-173">Ce scénario est appelé une *liaison enchaînée* parce que plusieurs niveaux de liaison se produisent simultanément.</span><span class="sxs-lookup"><span data-stu-id="960b7-173">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="960b7-174">Une liaison enchaînée ne peut `@bind` pas être implémentée avec la syntaxe dans l’élément de la page.</span><span class="sxs-lookup"><span data-stu-id="960b7-174">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="960b7-175">Le gestionnaire et la valeur de l’événement doivent être spécifiés séparément.</span><span class="sxs-lookup"><span data-stu-id="960b7-175">The event handler and value must be specified separately.</span></span> <span data-ttu-id="960b7-176">Un composant parent, cependant, peut utiliser `@bind` la syntaxe avec le paramètre du composant.</span><span class="sxs-lookup"><span data-stu-id="960b7-176">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="960b7-177">Le `PasswordField` composant suivant (*PasswordField.razor*):</span><span class="sxs-lookup"><span data-stu-id="960b7-177">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="960b7-178">Définit `<input>` la valeur d’un élément pour une `Password` propriété.</span><span class="sxs-lookup"><span data-stu-id="960b7-178">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="960b7-179">Expose les changements `Password` de propriété à un composant parent avec un [EventCallback](xref:blazor/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="960b7-179">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="960b7-180">Utilise `onclick` l’événement est `ToggleShowPassword` utilisé pour déclencher la méthode.</span><span class="sxs-lookup"><span data-stu-id="960b7-180">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="960b7-181">Pour plus d’informations, consultez <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="960b7-181">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

<span data-ttu-id="960b7-182">Le `PasswordField` composant est utilisé dans un autre composant :</span><span class="sxs-lookup"><span data-stu-id="960b7-182">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="960b7-183">Pour effectuer des vérifications ou des erreurs de piégeage sur le mot de passe dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="960b7-183">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="960b7-184">Créez un champ `Password` `_password` d’appui pour (dans le code exemple suivant).</span><span class="sxs-lookup"><span data-stu-id="960b7-184">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="960b7-185">Effectuez les vérifications `Password` ou les erreurs de piégeage dans le setter.</span><span class="sxs-lookup"><span data-stu-id="960b7-185">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="960b7-186">L’exemple suivant fournit une rétroaction immédiate à l’utilisateur si un espace est utilisé dans la valeur du mot de passe :</span><span class="sxs-lookup"><span data-stu-id="960b7-186">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="960b7-187">Cases d’option</span><span class="sxs-lookup"><span data-stu-id="960b7-187">Radio buttons</span></span>

<span data-ttu-id="960b7-188">Pour plus d’informations sur la liaison <xref:blazor/forms-validation#work-with-radio-buttons>aux boutons radio sous une forme, voir .</span><span class="sxs-lookup"><span data-stu-id="960b7-188">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
