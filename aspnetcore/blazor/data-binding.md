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
# <a name="aspnet-core-opno-locblazor-data-binding"></a>ASP.NET Liaison de Blazor données de base

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

Les composants Razor fournissent des fonctionnalités [`@bind`](xref:mvc/views/razor#bind) de liaison de données via un attribut d’élément HTML nommé avec une valeur d’expression de champ, de propriété ou de rasoir.

L’exemple suivant `CurrentValue` lie la propriété à la valeur de la boîte à texte :

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Lorsque la boîte de texte perd la mise au point, la valeur de la propriété est mise à jour.

La boîte de texte n’est mise à jour dans l’interface utilisateur que lorsque le composant est rendu, et non en réponse à la modification de la valeur de la propriété. Étant donné que les composants se rendent après l’exécution du code du gestionnaire d’événements, les mises à jour de propriété sont *généralement* reflétées dans l’interface utilisateur immédiatement après qu’un gestionnaire d’événements est déclenché.

`@bind` L’utilisation `CurrentValue` avec`<input @bind="CurrentValue" />`la propriété ( ) est essentiellement équivalente à ce qui suit :

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Lorsque le composant est `value` rendu, l’élément `CurrentValue` d’entrée provient de la propriété. Lorsque l’utilisateur tape dans la boîte `onchange` de texte et `CurrentValue` modifie la mise au point de l’élément, l’événement est déclenché et la propriété est définie à la valeur modifiée. En réalité, la génération de `@bind` code est plus complexe car gère les cas où des conversions de type sont effectuées. En principe, `@bind` associe la valeur actuelle `value` d’une expression à un attribut et gère les changements à l’aide du gestionnaire enregistré.

Lier une propriété ou un champ `@bind:event` sur `event` d’autres événements en incluant également un attribut avec un paramètre. L’exemple suivant `CurrentValue` lie la `oninput` propriété sur l’événement :

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Contrairement `onchange`à , qui s’allume lorsque l’élément perd la mise au point, `oninput` se déclenche lorsque la valeur de la boîte de texte change.

Utiliser `@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` avec la syntaxe pour `value`lier les attributs d’éléments autres que . Dans l’exemple suivant, le style du `_paragraphStyle` paragraphe est mis à jour lorsque la valeur change :

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

La liaison d’attribut est sensible au cas. Par exemple, `@bind` est `@Bind` valide, et est invalide.

## <a name="unparsable-values"></a>Valeurs non imposables

Lorsqu’un utilisateur fournit une valeur impardonnable à un élément lié aux données, la valeur impardonnable est automatiquement revenue à sa valeur antérieure lorsque l’événement de liaison est déclenché.

Examinez le cas suivant :

* Un `<input>` élément est `int` lié à un `123`type avec une valeur initiale de :

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* L’utilisateur met à jour `123.45` la valeur de l’élément dans la page et modifie la mise au point de l’élément.

Dans le scénario précédent, la valeur de `123`l’élément est retournée à . Lorsque la `123.45` valeur est rejetée en `123`faveur de la valeur originale de , l’utilisateur comprend que leur valeur n’a pas été acceptée.

Par défaut, la liaison s’applique à l’événement de `onchange` l’élément (`@bind="{PROPERTY OR FIELD}"`). Utiliser `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` pour déclencher la liaison sur un événement différent. Pour `oninput` l’événement (`@bind:event="oninput"`), la réversion se produit après tout coup de clés qui introduit une valeur impardonnable. Lorsque vous `oninput` ciblez `int`l’événement avec un type lié, `.` un utilisateur est empêché de taper un personnage. Un `.` personnage est immédiatement supprimé, de sorte que l’utilisateur reçoit une rétroaction immédiate que seuls les numéros entiers sont autorisés. Il existe des scénarios où `oninput` le retour de la valeur sur l’événement n’est pas `<input>` idéal, comme lorsque l’utilisateur doit être autorisé à effacer une valeur impardonnable. Les solutions de rechange comprennent :

* N’utilisez pas `oninput` l’événement. Utilisez l’événement par défaut `onchange` (seulement spécifier `@bind="{PROPERTY OR FIELD}"`), où une valeur invalide n’est pas retournée jusqu’à ce que l’élément perd la mise au point.
* Lier à un type `int?` nul, tel que ou `string`, et de fournir la logique personnalisée pour gérer les entrées invalides.
* Utilisez un composant de `InputNumber` validation `InputDate` [de formulaire,](xref:blazor/forms-validation)tel que ou . Les composants de validation des formulaires ont un support intégré pour gérer les entrées non valides. Composants de validation de formulaire :
  * Permettre à l’utilisateur de fournir des `EditContext`entrées non valides et de recevoir des erreurs de validation sur le .
  * Afficher les erreurs de validation dans l’interface utilisateur sans interférer avec l’utilisateur entrant des données Webform supplémentaires.

## <a name="format-strings"></a>Chaînes de format

La liaison <xref:System.DateTime> de données [`@bind:format`](xref:mvc/views/razor#bind)fonctionne avec des chaînes de format utilisant . D’autres expressions de format, telles que les formats de devise ou de numéro, ne sont pas disponibles pour le moment.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Dans le code `<input>` précédent, le type`type`de champ `text`de l’élément ( ) par défaut à . `@bind:format`est pris en charge pour lier les types .NET suivants:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

L’attribut `@bind:format` spécifie le `value` format `<input>` de date à appliquer à l’élément. Le format est également utilisé pour analyser `onchange` la valeur lorsqu’un événement se produit.

Spécifier un `date` format pour le Blazor type de champ n’est pas recommandé parce qu’il a intégré le support aux dates de format. Malgré la recommandation, n’utilisez que le `yyyy-MM-dd` format de date pour `date` la liaison pour fonctionner correctement si un format est fourni avec le type de champ :

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Liaison parent-enfant avec paramètres de composant

La liaison reconnaît les `@bind-{PROPERTY}` paramètres des composants, où la valeur de la propriété d’un composant parent jusqu’à une composante enfant. La liaison d’un enfant à un parent est couverte dans la liaison enfant-parent avec la section [de liaison enchaînée.](#child-to-parent-binding-with-chained-bind)

La composante enfant`ChildComponent`suivante `Year` ( ) `YearChanged` a un paramètre de composant et un rappel :

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

`EventCallback<T>`est expliqué <xref:blazor/event-handling#eventcallback>dans .

La composante parente suivante utilise :

* `ChildComponent`et lie `ParentYear` le paramètre du `Year` parent au paramètre sur la composante enfant.
* L’événement `onclick` est utilisé `ChangeTheYear` pour déclencher la méthode. Pour plus d’informations, consultez <xref:blazor/event-handling>.

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

Le `ParentComponent` chargement de la produit la majoration suivante :

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Si la valeur `ParentYear` de la propriété est modifiée `ParentComponent`en `Year` sélectionnant `ChildComponent` le bouton dans le , la propriété de la est mise à jour. La nouvelle `Year` valeur de est rendue dans `ParentComponent` l’interface utilisateur lorsque l’assurance-chômage est rendue:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Le `Year` paramètre est liant `YearChanged` parce qu’il a `Year` un événement compagnon qui correspond au type de paramètre.

Par convention, `<ChildComponent @bind-Year="ParentYear" />` est essentiellement équivalent à l’écriture:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

En général, une propriété peut être liée à `@bind-{PROPRETY}:event` un gestionnaire d’événement correspondant en incluant un attribut. Par exemple, `MyProp` la propriété `MyEventHandler` peut être liée à l’utilisation des deux attributs suivants :

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Liaison enfant-parent avec liaison enchaînée

Un scénario courant consiste à enchaîner un paramètre lié aux données à un élément de page dans la sortie du composant. Ce scénario est appelé une *liaison enchaînée* parce que plusieurs niveaux de liaison se produisent simultanément.

Une liaison enchaînée ne peut `@bind` pas être implémentée avec la syntaxe dans l’élément de la page. Le gestionnaire et la valeur de l’événement doivent être spécifiés séparément. Un composant parent, cependant, peut utiliser `@bind` la syntaxe avec le paramètre du composant.

Le `PasswordField` composant suivant (*PasswordField.razor*):

* Définit `<input>` la valeur d’un élément pour une `Password` propriété.
* Expose les changements `Password` de propriété à un composant parent avec un [EventCallback](xref:blazor/event-handling#eventcallback).
* Utilise `onclick` l’événement est `ToggleShowPassword` utilisé pour déclencher la méthode. Pour plus d’informations, consultez <xref:blazor/event-handling>.

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

Le `PasswordField` composant est utilisé dans un autre composant :

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

Pour effectuer des vérifications ou des erreurs de piégeage sur le mot de passe dans l’exemple précédent :

* Créez un champ `Password` `_password` d’appui pour (dans le code exemple suivant).
* Effectuez les vérifications `Password` ou les erreurs de piégeage dans le setter.

L’exemple suivant fournit une rétroaction immédiate à l’utilisateur si un espace est utilisé dans la valeur du mot de passe :

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

## <a name="radio-buttons"></a>Cases d’option

Pour plus d’informations sur la liaison <xref:blazor/forms-validation#work-with-radio-buttons>aux boutons radio sous une forme, voir .
