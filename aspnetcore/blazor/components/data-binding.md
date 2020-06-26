---
title: BlazorLiaison de données ASP.net Core
author: guardrex
description: En savoir plus sur les fonctionnalités de liaison de données pour les composants et les éléments DOM dans les Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/data-binding
ms.openlocfilehash: 35873e57171b4d86affcb475ad2d55aef443d3b5
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399177"
---
# <a name="aspnet-core-blazor-data-binding"></a>BlazorLiaison de données ASP.net Core

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

Razorles composants fournissent des fonctionnalités de liaison de données via un attribut d’élément HTML nommé [`@bind`](xref:mvc/views/razor#bind) avec une valeur de champ, de propriété ou d' Razor expression.

L’exemple suivant lie la `CurrentValue` propriété à la valeur de la zone de texte :

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Lorsque la zone de texte perd le focus, la valeur de la propriété est mise à jour.

La zone de texte est mise à jour dans l’interface utilisateur uniquement lorsque le composant est rendu, et non en réponse à la modification de la valeur de la propriété. Étant donné que les composants s’affichent après l’exécution du code du gestionnaire d’événements, les mises à jour de propriétés sont *généralement* reflétées dans l’interface utilisateur immédiatement après le déclenchement d’un gestionnaire d’événements.

L’utilisation [`@bind`](xref:mvc/views/razor#bind) de avec la `CurrentValue` propriété ( `<input @bind="CurrentValue" />` ) équivaut essentiellement à ce qui suit :

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Lors du rendu du composant, le `value` de l’élément d’entrée provient de la `CurrentValue` propriété. Lorsque l’utilisateur tape dans la zone de texte et modifie le focus de l’élément, l' `onchange` événement est déclenché et la `CurrentValue` propriété est définie sur la valeur modifiée. En réalité, la génération de code est plus complexe, car [`@bind`](xref:mvc/views/razor#bind) gère les cas où les conversions de types sont effectuées. En principe, [`@bind`](xref:mvc/views/razor#bind) associe la valeur actuelle d’une expression à un `value` attribut et gère les modifications à l’aide du gestionnaire inscrit.

Liez une propriété ou un champ à d’autres événements en incluant également un `@bind:event` attribut avec un `event` paramètre. L’exemple suivant lie la `CurrentValue` propriété à l' `oninput` événement :

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Contrairement à `onchange` , qui se déclenche lorsque l’élément perd le focus, `oninput` se déclenche lorsque la valeur de la zone de texte change.

Utilisez `@bind-{ATTRIBUTE}` avec la `@bind-{ATTRIBUTE}:event` syntaxe pour lier des attributs d’élément autres que `value` . Dans l’exemple suivant, le style du paragraphe est mis à jour lorsque la `paragraphStyle` valeur change :

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

La liaison d’attribut respecte la casse :

* `@bind`est valide.
* `@Bind`et `@BIND` ne sont pas valides.

## <a name="unparsable-values"></a>Valeurs inanalysables

Quand un utilisateur fournit une valeur non analysable à un élément DataBound, la valeur unanalysable est automatiquement rétablie à sa valeur précédente lorsque l’événement de liaison est déclenché.

Examinez le cas suivant :

* Un `<input>` élément est lié à un `int` type dont la valeur initiale est `123` :

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* L’utilisateur met à jour la valeur de l’élément à `123.45` dans la page et modifie le focus de l’élément.

Dans le scénario précédent, la valeur de l’élément est rétablie à `123` . Lorsque la valeur `123.45` est rejetée en faveur de la valeur d’origine de `123` , l’utilisateur sait que sa valeur n’a pas été acceptée.

Par défaut, la liaison s’applique à l’événement de l’élément `onchange` ( `@bind="{PROPERTY OR FIELD}"` ). Utilisez `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` pour déclencher la liaison sur un événement différent. Pour l' `oninput` événement ( `@bind:event="oninput"` ), la réversion se produit après toute séquence de touches qui introduit une valeur non analysable. Lorsque vous ciblez l' `oninput` événement avec un `int` type lié, un utilisateur ne peut pas taper un `.` caractère. Un `.` caractère est immédiatement supprimé, de sorte que l’utilisateur reçoit des commentaires immédiats que seuls des nombres entiers sont autorisés. Il existe des scénarios dans lesquels le rétablissement de la valeur de l' `oninput` événement n’est pas idéal, par exemple lorsque l’utilisateur doit être autorisé à effacer une valeur non analysable `<input>` . Les alternatives sont les suivantes :

* N’utilisez pas l' `oninput` événement. Utilisez l’événement par défaut `onchange` (spécifiez uniquement `@bind="{PROPERTY OR FIELD}"` ), où une valeur non valide n’est pas rétablie tant que l’élément n’a pas perdu le focus.
* Effectuer une liaison à un type Nullable, tel que `int?` ou `string` , et fournir une logique personnalisée pour gérer les entrées non valides.
* Utilisez un [composant de validation de formulaire](xref:blazor/forms-validation), tel que <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> ou <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> . Les composants de validation de formulaire offrent une prise en charge intégrée pour gérer les entrées non valides. Composants de validation de formulaire :
  * Permet à l’utilisateur de fournir une entrée non valide et de recevoir des erreurs de validation sur le associé <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .
  * Affichez les erreurs de validation dans l’interface utilisateur sans interférer avec l’utilisateur qui saisit des données Webform supplémentaires.

## <a name="format-strings"></a>Chaînes de format

La liaison de données fonctionne avec les <xref:System.DateTime> chaînes de format à l’aide de `@bind:format` . D’autres expressions de mise en forme, telles que les formats de devise ou de nombre, ne sont pas disponibles pour l’instant.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Dans le code précédent, le `<input>` type de champ de l’élément ( `type` ) est défini par défaut sur `text` . `@bind:format`est pris en charge pour la liaison des types .NET suivants :

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

L' `@bind:format` attribut spécifie le format de date à appliquer au `value` de l' `<input>` élément. Le format est également utilisé pour analyser la valeur lorsqu’un `onchange` événement se produit.

La spécification d’un format pour le `date` type de champ n’est pas recommandée, car Blazor dispose d’une prise en charge intégrée pour mettre en forme les dates. Malgré la recommandation, utilisez uniquement le `yyyy-MM-dd` format de date pour que la liaison fonctionne correctement si un format est fourni avec le `date` type de champ :

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Liaison parent-enfant avec les paramètres de composant

La liaison reconnaît les paramètres du composant, où `@bind-{PROPERTY}` peut lier une valeur de propriété d’un composant parent à un composant enfant. La liaison d’un enfant à un parent est couverte dans la [liaison enfant-parent avec la section de liaison chaînée](#child-to-parent-binding-with-chained-bind) .

Le composant enfant suivant ( `ChildComponent` ) a un `Year` paramètre de composant et un `YearChanged` Rappel :

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

<xref:Microsoft.AspNetCore.Components.EventCallback%601>est expliqué dans <xref:blazor/components/event-handling#eventcallback> .

Le composant parent suivant utilise :

* `ChildComponent`et lie le `ParentYear` paramètre du parent au `Year` paramètre sur le composant enfant.
* L' `onclick` événement est utilisé pour déclencher la `ChangeTheYear` méthode. Pour plus d’informations, consultez <xref:blazor/components/event-handling>.

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

Le chargement de `ParentComponent` génère le balisage suivant :

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Si la valeur de la `ParentYear` propriété est modifiée en sélectionnant le bouton dans le `ParentComponent` , la `Year` propriété de `ChildComponent` est mise à jour. La nouvelle valeur de `Year` est rendue dans l’interface utilisateur lorsque le est restitué à nouveau `ParentComponent` :

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

En général, une propriété peut être liée à un gestionnaire d’événements correspondant en incluant un `@bind-{PROPRETY}:event` attribut. Par exemple, la propriété `MyProp` peut être liée à à `MyEventHandler` l’aide des deux attributs suivants :

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Liaison enfant-parent avec liaison chaînée

Un scénario courant consiste à chaîner un paramètre lié aux données à un élément de page dans la sortie du composant. Ce scénario est appelé *liaison chaînée* car plusieurs niveaux de liaison se produisent simultanément.

Une liaison chaînée ne peut pas être implémentée avec [`@bind`](xref:mvc/views/razor#bind) la syntaxe dans l’élément de la page. Le gestionnaire d’événements et la valeur doivent être spécifiés séparément. Toutefois, un composant parent peut utiliser [`@bind`](xref:mvc/views/razor#bind) la syntaxe avec le paramètre du composant.

Le `PasswordField` composant suivant ( `PasswordField.razor` ) :

* Définit `<input>` la valeur d’un élément sur `Password` une propriété.
* Expose les modifications de la `Password` propriété à un composant parent avec un [`EventCallback`](xref:blazor/components/event-handling#eventcallback) .
* Utilise l' `onclick` événement pour déclencher la `ToggleShowPassword` méthode. Pour plus d’informations, consultez <xref:blazor/components/event-handling>.

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

* Créez un champ de stockage pour `Password` ( `password` dans l’exemple de code suivant).
* Effectuez les vérifications ou les erreurs d’interruption dans la méthode `Password` Setter.

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

Pour plus d’informations sur la liaison à des cases d’option dans un formulaire, consultez <xref:blazor/forms-validation#work-with-radio-buttons> .
