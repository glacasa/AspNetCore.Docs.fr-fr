---
title: Liaison Blazor de données ASP.net Core
author: guardrex
description: En savoir plus sur les fonctionnalités de liaison de données pour Blazor les composants et les éléments DOM dans les applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: b4951c5eb712b15db3a7c1ccd57ae01c530a23ef
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967166"
---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="00171-103">Liaison Blazor de données ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="00171-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="00171-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="00171-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="00171-105">les composants fournissent des fonctionnalités de liaison de données via un [`@bind`](xref:mvc/views/razor#bind) attribut d’élément HTML nommé avec une Razor valeur de champ, de propriété ou d’expression.</span><span class="sxs-lookup"><span data-stu-id="00171-105"> components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="00171-106">L’exemple suivant lie la `CurrentValue` propriété à la valeur de la zone de texte :</span><span class="sxs-lookup"><span data-stu-id="00171-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="00171-107">Lorsque la zone de texte perd le focus, la valeur de la propriété est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="00171-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="00171-108">La zone de texte est mise à jour dans l’interface utilisateur uniquement lorsque le composant est rendu, et non en réponse à la modification de la valeur de la propriété.</span><span class="sxs-lookup"><span data-stu-id="00171-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="00171-109">Étant donné que les composants s’affichent après l’exécution du code du gestionnaire d’événements, les mises à jour de propriétés sont *généralement* reflétées dans l’interface utilisateur immédiatement après le déclenchement d’un gestionnaire d’événements.</span><span class="sxs-lookup"><span data-stu-id="00171-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="00171-110">L' `@bind` utilisation de `CurrentValue` avec la`<input @bind="CurrentValue" />`propriété () équivaut essentiellement à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="00171-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="00171-111">Lors du rendu du composant, le `value` de l’élément d’entrée provient de `CurrentValue` la propriété.</span><span class="sxs-lookup"><span data-stu-id="00171-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="00171-112">Lorsque l’utilisateur tape dans la zone de texte et modifie le focus de `onchange` l’élément, l’événement `CurrentValue` est déclenché et la propriété est définie sur la valeur modifiée.</span><span class="sxs-lookup"><span data-stu-id="00171-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="00171-113">En réalité, la génération de code est plus complexe `@bind` , car gère les cas où les conversions de types sont effectuées.</span><span class="sxs-lookup"><span data-stu-id="00171-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="00171-114">En principe, `@bind` associe la valeur actuelle d’une expression à `value` un attribut et gère les modifications à l’aide du gestionnaire inscrit.</span><span class="sxs-lookup"><span data-stu-id="00171-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="00171-115">Liez une propriété ou un champ à d’autres événements en incluant `@bind:event` également un attribut `event` avec un paramètre.</span><span class="sxs-lookup"><span data-stu-id="00171-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="00171-116">L’exemple suivant lie la `CurrentValue` propriété à l' `oninput` événement :</span><span class="sxs-lookup"><span data-stu-id="00171-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="00171-117">Contrairement `onchange`à, qui se déclenche lorsque l’élément perd `oninput` le focus, se déclenche lorsque la valeur de la zone de texte change.</span><span class="sxs-lookup"><span data-stu-id="00171-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="00171-118">Utilisez `@bind-{ATTRIBUTE}` avec `@bind-{ATTRIBUTE}:event` la syntaxe pour lier des attributs d' `value`élément autres que.</span><span class="sxs-lookup"><span data-stu-id="00171-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="00171-119">Dans l’exemple suivant, le style du paragraphe est mis à jour `paragraphStyle` lorsque la valeur change :</span><span class="sxs-lookup"><span data-stu-id="00171-119">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="00171-120">La liaison d’attribut respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="00171-120">Attribute binding is case sensitive.</span></span> <span data-ttu-id="00171-121">Par exemple, `@bind` est valide et `@Bind` n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="00171-121">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="00171-122">Valeurs inanalysables</span><span class="sxs-lookup"><span data-stu-id="00171-122">Unparsable values</span></span>

<span data-ttu-id="00171-123">Quand un utilisateur fournit une valeur non analysable à un élément DataBound, la valeur unanalysable est automatiquement rétablie à sa valeur précédente lorsque l’événement de liaison est déclenché.</span><span class="sxs-lookup"><span data-stu-id="00171-123">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="00171-124">Examinez le cas suivant :</span><span class="sxs-lookup"><span data-stu-id="00171-124">Consider the following scenario:</span></span>

* <span data-ttu-id="00171-125">Un `<input>` élément est lié à un `int` type dont la valeur initiale est `123`:</span><span class="sxs-lookup"><span data-stu-id="00171-125">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="00171-126">L’utilisateur met à jour la valeur de l’élément à `123.45` dans la page et modifie le focus de l’élément.</span><span class="sxs-lookup"><span data-stu-id="00171-126">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="00171-127">Dans le scénario précédent, la valeur de l’élément est rétablie `123`à.</span><span class="sxs-lookup"><span data-stu-id="00171-127">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="00171-128">Lorsque la valeur `123.45` est rejetée en faveur de la valeur d' `123`origine de, l’utilisateur sait que sa valeur n’a pas été acceptée.</span><span class="sxs-lookup"><span data-stu-id="00171-128">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="00171-129">Par défaut, la liaison s’applique à l' `onchange` événement de`@bind="{PROPERTY OR FIELD}"`l’élément ().</span><span class="sxs-lookup"><span data-stu-id="00171-129">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="00171-130">Utilisez `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` pour déclencher la liaison sur un événement différent.</span><span class="sxs-lookup"><span data-stu-id="00171-130">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="00171-131">Pour l' `oninput` événement (`@bind:event="oninput"`), la réversion se produit après toute séquence de touches qui introduit une valeur non analysable.</span><span class="sxs-lookup"><span data-stu-id="00171-131">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="00171-132">Lorsque vous ciblez l' `oninput` événement `int`avec un type lié, un utilisateur ne peut pas taper `.` un caractère.</span><span class="sxs-lookup"><span data-stu-id="00171-132">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="00171-133">Un `.` caractère est immédiatement supprimé, de sorte que l’utilisateur reçoit des commentaires immédiats que seuls des nombres entiers sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="00171-133">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="00171-134">Il existe des scénarios dans lesquels le rétablissement de la valeur `oninput` de l’événement n’est pas idéal, par exemple lorsque l’utilisateur doit être autorisé à effacer `<input>` une valeur non analysable.</span><span class="sxs-lookup"><span data-stu-id="00171-134">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="00171-135">Les alternatives sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="00171-135">Alternatives include:</span></span>

* <span data-ttu-id="00171-136">N’utilisez pas `oninput` l’événement.</span><span class="sxs-lookup"><span data-stu-id="00171-136">Don't use the `oninput` event.</span></span> <span data-ttu-id="00171-137">Utilisez l’événement `onchange` par défaut (spécifiez `@bind="{PROPERTY OR FIELD}"`uniquement), où une valeur non valide n’est pas rétablie tant que l’élément n’a pas perdu le focus.</span><span class="sxs-lookup"><span data-stu-id="00171-137">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="00171-138">Effectuer une liaison à un type Nullable, `int?` tel `string`que ou, et fournir une logique personnalisée pour gérer les entrées non valides.</span><span class="sxs-lookup"><span data-stu-id="00171-138">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="00171-139">Utilisez un [composant de validation de formulaire](xref:blazor/forms-validation), `InputNumber` tel `InputDate`que ou.</span><span class="sxs-lookup"><span data-stu-id="00171-139">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="00171-140">Les composants de validation de formulaire offrent une prise en charge intégrée pour gérer les entrées non valides.</span><span class="sxs-lookup"><span data-stu-id="00171-140">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="00171-141">Composants de validation de formulaire :</span><span class="sxs-lookup"><span data-stu-id="00171-141">Form validation components:</span></span>
  * <span data-ttu-id="00171-142">Permet à l’utilisateur de fournir une entrée non valide et de recevoir des `EditContext`erreurs de validation sur le associé.</span><span class="sxs-lookup"><span data-stu-id="00171-142">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="00171-143">Affichez les erreurs de validation dans l’interface utilisateur sans interférer avec l’utilisateur qui saisit des données Webform supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="00171-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="00171-144">Chaînes de format</span><span class="sxs-lookup"><span data-stu-id="00171-144">Format strings</span></span>

<span data-ttu-id="00171-145">La liaison de données <xref:System.DateTime> fonctionne avec les [`@bind:format`](xref:mvc/views/razor#bind)chaînes de format à l’aide de.</span><span class="sxs-lookup"><span data-stu-id="00171-145">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="00171-146">D’autres expressions de mise en forme, telles que les formats de devise ou de nombre, ne sont pas disponibles pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="00171-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="00171-147">Dans le code précédent, le `<input>` type de champ de l'`type`élément () est `text`défini par défaut sur.</span><span class="sxs-lookup"><span data-stu-id="00171-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="00171-148">`@bind:format`est pris en charge pour la liaison des types .NET suivants :</span><span class="sxs-lookup"><span data-stu-id="00171-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="00171-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="00171-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="00171-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="00171-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="00171-151">L' `@bind:format` attribut spécifie le format de date à appliquer `value` au de `<input>` l’élément.</span><span class="sxs-lookup"><span data-stu-id="00171-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="00171-152">Le format est également utilisé pour analyser la valeur lorsqu’un `onchange` événement se produit.</span><span class="sxs-lookup"><span data-stu-id="00171-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="00171-153">La spécification d’un format `date` pour le type de champ Blazor n’est pas recommandée, car dispose d’une prise en charge intégrée pour mettre en forme les dates.</span><span class="sxs-lookup"><span data-stu-id="00171-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="00171-154">Malgré la recommandation, utilisez uniquement le `yyyy-MM-dd` format de date pour que la liaison fonctionne correctement si un format est fourni avec le `date` type de champ :</span><span class="sxs-lookup"><span data-stu-id="00171-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="00171-155">Liaison parent-enfant avec les paramètres de composant</span><span class="sxs-lookup"><span data-stu-id="00171-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="00171-156">La liaison reconnaît les paramètres du `@bind-{PROPERTY}` composant, où peut lier une valeur de propriété d’un composant parent à un composant enfant.</span><span class="sxs-lookup"><span data-stu-id="00171-156">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="00171-157">La liaison d’un enfant à un parent est couverte dans la [liaison enfant-parent avec la section de liaison chaînée](#child-to-parent-binding-with-chained-bind) .</span><span class="sxs-lookup"><span data-stu-id="00171-157">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="00171-158">Le composant enfant suivant (`ChildComponent`) a un `Year` paramètre de composant `YearChanged` et un rappel :</span><span class="sxs-lookup"><span data-stu-id="00171-158">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="00171-159">`EventCallback<T>`est expliqué dans <xref:blazor/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="00171-159">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="00171-160">Le composant parent suivant utilise :</span><span class="sxs-lookup"><span data-stu-id="00171-160">The following parent component uses:</span></span>

* <span data-ttu-id="00171-161">`ChildComponent`et lie le `ParentYear` paramètre du parent au `Year` paramètre sur le composant enfant.</span><span class="sxs-lookup"><span data-stu-id="00171-161">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="00171-162">L' `onclick` événement est utilisé pour déclencher la `ChangeTheYear` méthode.</span><span class="sxs-lookup"><span data-stu-id="00171-162">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="00171-163">Pour plus d'informations, consultez <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="00171-163">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="00171-164">Le chargement `ParentComponent` de génère le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="00171-164">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="00171-165">Si la valeur de la `ParentYear` propriété est modifiée en sélectionnant le bouton dans `ParentComponent`le, `Year` la propriété de `ChildComponent` est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="00171-165">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="00171-166">La nouvelle valeur de `Year` est rendue dans l’interface utilisateur lorsque `ParentComponent` le est restitué à nouveau :</span><span class="sxs-lookup"><span data-stu-id="00171-166">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="00171-167">Le `Year` paramètre peut être lié, car il a un `YearChanged` événement auxiliaire qui correspond au type du `Year` paramètre.</span><span class="sxs-lookup"><span data-stu-id="00171-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="00171-168">Par Convention, `<ChildComponent @bind-Year="ParentYear" />` est essentiellement équivalent à l’écriture :</span><span class="sxs-lookup"><span data-stu-id="00171-168">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="00171-169">En général, une propriété peut être liée à un gestionnaire d’événements correspondant en incluant `@bind-{PROPRETY}:event` un attribut.</span><span class="sxs-lookup"><span data-stu-id="00171-169">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="00171-170">Par exemple, la propriété `MyProp` peut être liée à `MyEventHandler` à l’aide des deux attributs suivants :</span><span class="sxs-lookup"><span data-stu-id="00171-170">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="00171-171">Liaison enfant-parent avec liaison chaînée</span><span class="sxs-lookup"><span data-stu-id="00171-171">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="00171-172">Un scénario courant consiste à chaîner un paramètre lié aux données à un élément de page dans la sortie du composant.</span><span class="sxs-lookup"><span data-stu-id="00171-172">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="00171-173">Ce scénario est appelé *liaison chaînée* car plusieurs niveaux de liaison se produisent simultanément.</span><span class="sxs-lookup"><span data-stu-id="00171-173">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="00171-174">Une liaison chaînée ne peut pas être `@bind` implémentée avec la syntaxe dans l’élément de la page.</span><span class="sxs-lookup"><span data-stu-id="00171-174">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="00171-175">Le gestionnaire d’événements et la valeur doivent être spécifiés séparément.</span><span class="sxs-lookup"><span data-stu-id="00171-175">The event handler and value must be specified separately.</span></span> <span data-ttu-id="00171-176">Toutefois, un composant parent peut utiliser `@bind` la syntaxe avec le paramètre du composant.</span><span class="sxs-lookup"><span data-stu-id="00171-176">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="00171-177">Le composant `PasswordField` suivant (*passwordField. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="00171-177">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="00171-178">Définit la `<input>` valeur d’un élément sur `Password` une propriété.</span><span class="sxs-lookup"><span data-stu-id="00171-178">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="00171-179">Expose les modifications de `Password` la propriété à un composant parent avec un [EventCallback suivante](xref:blazor/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="00171-179">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="00171-180">Utilise l' `onclick` événement pour déclencher la `ToggleShowPassword` méthode.</span><span class="sxs-lookup"><span data-stu-id="00171-180">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="00171-181">Pour plus d'informations, consultez <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="00171-181">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

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
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="00171-182">Le `PasswordField` composant est utilisé dans un autre composant :</span><span class="sxs-lookup"><span data-stu-id="00171-182">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="00171-183">Pour effectuer des vérifications ou des erreurs d’interruption sur le mot de passe dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="00171-183">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="00171-184">Créez un champ de stockage pour `Password` (`password` dans l’exemple de code suivant).</span><span class="sxs-lookup"><span data-stu-id="00171-184">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="00171-185">Effectuez les vérifications ou les erreurs d’interruption `Password` dans la méthode setter.</span><span class="sxs-lookup"><span data-stu-id="00171-185">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="00171-186">L’exemple suivant fournit un retour immédiat à l’utilisateur si un espace est utilisé dans la valeur du mot de passe :</span><span class="sxs-lookup"><span data-stu-id="00171-186">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
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
        showPassword = !showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="00171-187">Cases d’option</span><span class="sxs-lookup"><span data-stu-id="00171-187">Radio buttons</span></span>

<span data-ttu-id="00171-188">Pour plus d’informations sur la liaison à des cases d’option <xref:blazor/forms-validation#work-with-radio-buttons>dans un formulaire, consultez.</span><span class="sxs-lookup"><span data-stu-id="00171-188">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
