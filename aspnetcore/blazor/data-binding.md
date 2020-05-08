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
# <a name="aspnet-core-blazor-data-binding"></a>Liaison Blazor de données ASP.net Core

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

Razorles composants fournissent des fonctionnalités de liaison de données via un [`@bind`](xref:mvc/views/razor#bind) attribut d’élément HTML nommé avec une Razor valeur de champ, de propriété ou d’expression.

L’exemple suivant lie la `CurrentValue` propriété à la valeur de la zone de texte :

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Lorsque la zone de texte perd le focus, la valeur de la propriété est mise à jour.

La zone de texte est mise à jour dans l’interface utilisateur uniquement lorsque le composant est rendu, et non en réponse à la modification de la valeur de la propriété. Étant donné que les composants s’affichent après l’exécution du code du gestionnaire d’événements, les mises à jour de propriétés sont *généralement* reflétées dans l’interface utilisateur immédiatement après le déclenchement d’un gestionnaire d’événements.

L' `@bind` utilisation de `CurrentValue` avec la`<input @bind="CurrentValue" />`propriété () équivaut essentiellement à ce qui suit :

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Lors du rendu du composant, le `value` de l’élément d’entrée provient de `CurrentValue` la propriété. Lorsque l’utilisateur tape dans la zone de texte et modifie le focus de `onchange` l’élément, l’événement `CurrentValue` est déclenché et la propriété est définie sur la valeur modifiée. En réalité, la génération de code est plus complexe `@bind` , car gère les cas où les conversions de types sont effectuées. En principe, `@bind` associe la valeur actuelle d’une expression à `value` un attribut et gère les modifications à l’aide du gestionnaire inscrit.

Liez une propriété ou un champ à d’autres événements en incluant `@bind:event` également un attribut `event` avec un paramètre. L’exemple suivant lie la `CurrentValue` propriété à l' `oninput` événement :

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Contrairement `onchange`à, qui se déclenche lorsque l’élément perd `oninput` le focus, se déclenche lorsque la valeur de la zone de texte change.

Utilisez `@bind-{ATTRIBUTE}` avec `@bind-{ATTRIBUTE}:event` la syntaxe pour lier des attributs d' `value`élément autres que. Dans l’exemple suivant, le style du paragraphe est mis à jour `paragraphStyle` lorsque la valeur change :

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

La liaison d’attribut respecte la casse. Par exemple, `@bind` est valide et `@Bind` n’est pas valide.

## <a name="unparsable-values"></a>Valeurs inanalysables

Quand un utilisateur fournit une valeur non analysable à un élément DataBound, la valeur unanalysable est automatiquement rétablie à sa valeur précédente lorsque l’événement de liaison est déclenché.

Examinez le cas suivant :

* Un `<input>` élément est lié à un `int` type dont la valeur initiale est `123`:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* L’utilisateur met à jour la valeur de l’élément à `123.45` dans la page et modifie le focus de l’élément.

Dans le scénario précédent, la valeur de l’élément est rétablie `123`à. Lorsque la valeur `123.45` est rejetée en faveur de la valeur d' `123`origine de, l’utilisateur sait que sa valeur n’a pas été acceptée.

Par défaut, la liaison s’applique à l' `onchange` événement de`@bind="{PROPERTY OR FIELD}"`l’élément (). Utilisez `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` pour déclencher la liaison sur un événement différent. Pour l' `oninput` événement (`@bind:event="oninput"`), la réversion se produit après toute séquence de touches qui introduit une valeur non analysable. Lorsque vous ciblez l' `oninput` événement `int`avec un type lié, un utilisateur ne peut pas taper `.` un caractère. Un `.` caractère est immédiatement supprimé, de sorte que l’utilisateur reçoit des commentaires immédiats que seuls des nombres entiers sont autorisés. Il existe des scénarios dans lesquels le rétablissement de la valeur `oninput` de l’événement n’est pas idéal, par exemple lorsque l’utilisateur doit être autorisé à effacer `<input>` une valeur non analysable. Les alternatives sont les suivantes :

* N’utilisez pas `oninput` l’événement. Utilisez l’événement `onchange` par défaut (spécifiez `@bind="{PROPERTY OR FIELD}"`uniquement), où une valeur non valide n’est pas rétablie tant que l’élément n’a pas perdu le focus.
* Effectuer une liaison à un type Nullable, `int?` tel `string`que ou, et fournir une logique personnalisée pour gérer les entrées non valides.
* Utilisez un [composant de validation de formulaire](xref:blazor/forms-validation), `InputNumber` tel `InputDate`que ou. Les composants de validation de formulaire offrent une prise en charge intégrée pour gérer les entrées non valides. Composants de validation de formulaire :
  * Permet à l’utilisateur de fournir une entrée non valide et de recevoir des `EditContext`erreurs de validation sur le associé.
  * Affichez les erreurs de validation dans l’interface utilisateur sans interférer avec l’utilisateur qui saisit des données Webform supplémentaires.

## <a name="format-strings"></a>Chaînes de format

La liaison de données <xref:System.DateTime> fonctionne avec les [`@bind:format`](xref:mvc/views/razor#bind)chaînes de format à l’aide de. D’autres expressions de mise en forme, telles que les formats de devise ou de nombre, ne sont pas disponibles pour l’instant.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Dans le code précédent, le `<input>` type de champ de l'`type`élément () est `text`défini par défaut sur. `@bind:format`est pris en charge pour la liaison des types .NET suivants :

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

L' `@bind:format` attribut spécifie le format de date à appliquer `value` au de `<input>` l’élément. Le format est également utilisé pour analyser la valeur lorsqu’un `onchange` événement se produit.

La spécification d’un format `date` pour le type de champ Blazor n’est pas recommandée, car dispose d’une prise en charge intégrée pour mettre en forme les dates. Malgré la recommandation, utilisez uniquement le `yyyy-MM-dd` format de date pour que la liaison fonctionne correctement si un format est fourni avec le `date` type de champ :

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Liaison parent-enfant avec les paramètres de composant

La liaison reconnaît les paramètres du `@bind-{PROPERTY}` composant, où peut lier une valeur de propriété d’un composant parent à un composant enfant. La liaison d’un enfant à un parent est couverte dans la [liaison enfant-parent avec la section de liaison chaînée](#child-to-parent-binding-with-chained-bind) .

Le composant enfant suivant (`ChildComponent`) a un `Year` paramètre de composant `YearChanged` et un rappel :

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

`EventCallback<T>`est expliqué dans <xref:blazor/event-handling#eventcallback>.

Le composant parent suivant utilise :

* `ChildComponent`et lie le `ParentYear` paramètre du parent au `Year` paramètre sur le composant enfant.
* L' `onclick` événement est utilisé pour déclencher la `ChangeTheYear` méthode. Pour plus d'informations, consultez <xref:blazor/event-handling>.

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

Le chargement `ParentComponent` de génère le balisage suivant :

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Si la valeur de la `ParentYear` propriété est modifiée en sélectionnant le bouton dans `ParentComponent`le, `Year` la propriété de `ChildComponent` est mise à jour. La nouvelle valeur de `Year` est rendue dans l’interface utilisateur lorsque `ParentComponent` le est restitué à nouveau :

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Le `Year` paramètre peut être lié, car il a un `YearChanged` événement auxiliaire qui correspond au type du `Year` paramètre.

Par Convention, `<ChildComponent @bind-Year="ParentYear" />` est essentiellement équivalent à l’écriture :

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

En général, une propriété peut être liée à un gestionnaire d’événements correspondant en incluant `@bind-{PROPRETY}:event` un attribut. Par exemple, la propriété `MyProp` peut être liée à `MyEventHandler` à l’aide des deux attributs suivants :

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Liaison enfant-parent avec liaison chaînée

Un scénario courant consiste à chaîner un paramètre lié aux données à un élément de page dans la sortie du composant. Ce scénario est appelé *liaison chaînée* car plusieurs niveaux de liaison se produisent simultanément.

Une liaison chaînée ne peut pas être `@bind` implémentée avec la syntaxe dans l’élément de la page. Le gestionnaire d’événements et la valeur doivent être spécifiés séparément. Toutefois, un composant parent peut utiliser `@bind` la syntaxe avec le paramètre du composant.

Le composant `PasswordField` suivant (*passwordField. Razor*) :

* Définit la `<input>` valeur d’un élément sur `Password` une propriété.
* Expose les modifications de `Password` la propriété à un composant parent avec un [EventCallback suivante](xref:blazor/event-handling#eventcallback).
* Utilise l' `onclick` événement pour déclencher la `ToggleShowPassword` méthode. Pour plus d'informations, consultez <xref:blazor/event-handling>.

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

Le `PasswordField` composant est utilisé dans un autre composant :

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Pour effectuer des vérifications ou des erreurs d’interruption sur le mot de passe dans l’exemple précédent :

* Créez un champ de stockage pour `Password` (`password` dans l’exemple de code suivant).
* Effectuez les vérifications ou les erreurs d’interruption `Password` dans la méthode setter.

L’exemple suivant fournit un retour immédiat à l’utilisateur si un espace est utilisé dans la valeur du mot de passe :

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

## <a name="radio-buttons"></a>Cases d’option

Pour plus d’informations sur la liaison à des cases d’option <xref:blazor/forms-validation#work-with-radio-buttons>dans un formulaire, consultez.
