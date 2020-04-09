---
title: ASP.NET formulaires Blazor de base et validation
author: guardrex
description: Apprenez à utiliser les formulaires Blazoret les scénarios de validation sur le terrain dans .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 0359a9337860d9b8ce0b81d8833a034a898b05a5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218958"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET formulaires et validations Core Blazor

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

Les formulaires et la validation sont pris en charge dans Blazor à [l’aide d’annotations de données](xref:mvc/models/validation).

Le `ExampleModel` type suivant définit la logique de validation à l’aide d’annotations de données :

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Un formulaire est `EditForm` défini à l’aide du composant. Le formulaire suivant montre des éléments typiques, des composants et du code Razor :

```razor
<EditForm Model="@_exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="_exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel _exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

Dans l'exemple précédent :

* Le formulaire valide l’entrée de l’utilisateur sur le `name` terrain à l’aide de la validation définie dans le `ExampleModel` type. Le modèle est créé dans `@code` le bloc du composant`_exampleModel`et tenu dans un champ privé ( ). Le champ est `Model` attribué à `<EditForm>` l’attribut de l’élément.
* Le `InputText` composant `@bind-Value` lie :
  * La propriété`_exampleModel.Name`du modèle `InputText` ( `Value` ) à la propriété du composant.
  * Un rendez-vous `InputText` de changement `ValueChanged` délégué à la propriété du composant.
* Le `DataAnnotationsValidator` composant attache le support de validation à l’aide d’annotations de données.
* Le `ValidationSummary` composant résume les messages de validation.
* `HandleValidSubmit`est déclenché lorsque le formulaire se soumet avec succès (validation de la passe).

Un ensemble de composants d’entrée intégrés sont disponibles pour recevoir et valider l’entrée de l’utilisateur. Les entrées sont validées lorsqu’elles sont modifiées et lorsqu’un formulaire est soumis. Les composants d’entrée disponibles sont indiqués dans le tableau suivant.

| Composant d’entrée | Rendu comme&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Tous les composants d’entrée, y compris, `EditForm`prennent en charge les attributs arbitraires. Tout attribut qui ne correspond pas à un paramètre de composant est ajouté à l’élément HTML rendu.

Les composants d’entrée fournissent un comportement par défaut pour valider sur modifier et modifier leur classe CSS pour refléter l’état de champ. Certains composants incluent la logique d’analyse utile. Par `InputDate` exemple, `InputNumber` et gérer les valeurs impardonnables gracieusement en les enregistrant comme erreurs de validation. Les types qui peuvent accepter des valeurs nulles soutiennent `int?`également l’annulation du champ cible (par exemple, ).

Le `Starship` type suivant définit la logique de validation à l’aide `ExampleModel`d’un ensemble plus large de propriétés et d’annotations de données que les précédents :

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

Dans l’exemple `Description` précédent, est facultatif parce qu’aucune annotation de données n’est présente.

Le formulaire suivant valide l’entrée de `Starship` l’utilisateur à l’aide de la validation définie dans le modèle :

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@_starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="_starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="_starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="_starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="_starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="_starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="_starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship _starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

Le `EditForm` crée `EditContext` une valeur en [cascade](xref:blazor/components#cascading-values-and-parameters) qui suit les métadonnées sur le processus de modification, y compris les champs ont été modifiés et les messages de validation en cours. Le `EditForm` fournit également des événements pratiques`OnValidSubmit`pour `OnInvalidSubmit`valide et invalide soumet ( , ). Vous pouvez `OnSubmit` également utiliser pour déclencher la validation et vérifier les valeurs de champ avec le code de validation personnalisé.

Dans l’exemple suivant :

* La `HandleSubmit` méthode s’exécute lorsque le bouton **Soumettre** est sélectionné.
* Le formulaire est validé à `EditContext`l’aide de la forme .
* Le formulaire est également validé `EditContext` en `ServerValidate` passant la méthode qui appelle un point de terminaison API web sur le serveur *(non affiché*).
* Le code supplémentaire est exécuté en fonction du résultat de `isValid`la validation côté client et serveur en vérifiant .

```razor
<EditForm EditContext="@_editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = _editContext.Validate() && 
            await ServerValidate(_editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a>InputText basé sur l’événement d’entrée

Utilisez `InputText` le composant pour créer un `input` composant personnalisé `change` qui utilise l’événement au lieu de l’événement.

Créez un composant avec la balisage suivante `InputText` et utilisez le composant comme il est utilisé :

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>Travailler avec des boutons radio

Lorsque vous travaillez avec des boutons radio sous une forme, la liaison des données est traitée différemment des autres éléments parce que les boutons radio sont évalués en groupe. La valeur de chaque bouton radio est fixe, mais la valeur du groupe de boutons radio est la valeur du bouton radio sélectionné. L’exemple suivant montre comment :

* Gérer la liaison de données pour un groupe de boutons radio.
* Validation de prise `InputRadio` en charge à l’aide d’un composant personnalisé.

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

Ce `EditForm` qui suit `InputRadio` utilise le composant précédent pour obtenir et valider une note de l’utilisateur :

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="_model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="_model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @_model.Rating</p>

@code {
    private Model _model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a>Soutien à la validation

Le `DataAnnotationsValidator` composant attache le support de validation à `EditContext`l’aide d’annotations de données à la cascade . Permettre le support de validation à l’aide d’annotations de données nécessite ce geste explicite. Pour utiliser un système de validation différent des `DataAnnotationsValidator` annotations de données, remplacez-le par une implémentation personnalisée. La mise en œuvre de base ASP.NET est disponible pour inspection dans la source de référence: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazoreffectue deux types de validation :

* *La validation de champ* est effectuée lorsque l’utilisateur s’œil hors d’un champ. Lors de la `DataAnnotationsValidator` validation sur le terrain, le composant associe tous les résultats de validation signalés sur le terrain.
* *La validation du modèle* est effectuée lorsque l’utilisateur soumet le formulaire. Lors de la `DataAnnotationsValidator` validation du modèle, le composant tente de déterminer le champ en fonction du nom du membre que le résultat de validation indique. Les résultats de validation qui ne sont pas associés à un membre individuel sont associés au modèle plutôt qu’à un champ.

### <a name="validation-summary-and-validation-message-components"></a>Validation Résumé et Validation Des composants du message

Le `ValidationSummary` composant résume tous les messages de validation, qui est similaire à [l’aide à l’étiquette résumée de validation](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Messages de validation de sortie `Model` pour un modèle spécifique avec le paramètre :
  
```razor
<ValidationSummary Model="@_starship" />
```

Le `ValidationMessage` composant affiche des messages de validation pour un champ spécifique, qui est similaire à [l’aide à l’étiquette de message de validation](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Spécifier le `For` champ de validation avec l’attribut et une expression lambda nommant la propriété modèle :

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

Les `ValidationMessage` `ValidationSummary` éléments et les composants sont à l’appui d’attributs arbitraires. Tout attribut qui ne correspond pas à un `<div>` `<ul>` paramètre de composant est ajouté au produit ou à l’élément.

### <a name="custom-validation-attributes"></a>Attributs de validation personnalisés

Pour s’assurer qu’un résultat de validation est correctement associé à un <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> champ lors <xref:System.ComponentModel.DataAnnotations.ValidationResult>de l’utilisation d’un attribut de [validation personnalisé,](xref:mvc/models/validation#custom-attributes)passez le contexte de validation lors de la création de la :

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazorpaquet de validation des annotations de données

Le [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) est un paquet qui comble les lacunes de l’expérience de validation à l’aide du `DataAnnotationsValidator` composant. Le paquet est actuellement *expérimental*.

### <a name="compareproperty-attribute"></a>[CompareProperty] attribut

Le <xref:System.ComponentModel.DataAnnotations.CompareAttribute> ne fonctionne pas `DataAnnotationsValidator` bien avec le composant, car il n’associe pas le résultat de validation avec un membre spécifique. Cela peut entraîner un comportement incohérent entre la validation au niveau du terrain et lorsque l’ensemble du modèle est validé sur un soumettre. Le paquet expérimental [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* introduit un `ComparePropertyAttribute`attribut de validation supplémentaire, qui fonctionne autour de ces limitations. Dans Blazor une `[CompareProperty]` application, est un `[Compare]` remplacement direct de l’attribut.

### <a name="nested-models-collection-types-and-complex-types"></a>Modèles imbriqués, types de collecte et types complexes

Blazorfournit un support pour valider l’entrée de formulaire `DataAnnotationsValidator`à l’aide d’annotations de données avec le intégré . Toutefois, `DataAnnotationsValidator` le seul valide les propriétés de haut niveau du modèle liées à la forme qui ne sont pas des propriétés de type collection ou complexe.

Pour valider l’ensemble du graphique d’objets du modèle lié, y compris les propriétés de type collecte et complexe, utilisez le `ObjectGraphDataAnnotationsValidator` produit fourni par le package *expérimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Annoter les `[ValidateComplexType]`propriétés du modèle avec . Dans les classes de `ShipDescription` modèles suivantes, la classe contient des annotations de données supplémentaires à valider lorsque le modèle est lié à la forme :

*Starship.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

*ShipDescription.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>Activez le bouton soumettre en fonction de la validation du formulaire

Pour activer et désactiver le bouton soumettre en fonction de la validation du formulaire :

* Utilisez le formulaire `EditContext` pour attribuer le modèle lorsque le composant est initialisé.
* Validez le formulaire dans `OnFieldChanged` le rappel du contexte pour activer et désactiver le bouton soumettre.
* Débranchez le gestionnaire `Dispose` d’événements dans la méthode. Pour plus d’informations, consultez <xref:blazor/lifecycle#component-disposal-with-idisposable>.

```razor
@implements IDisposable

<EditForm EditContext="@_editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private bool _formInvalid = true;
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
        _editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        _formInvalid = !_editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        _editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

Dans l’exemple `_formInvalid` précédent, définissez `false` si :

* Le formulaire est préchargé avec des valeurs valides par défaut.
* Vous voulez que le bouton soumettre soit activé lorsque le formulaire se charge.

Un effet secondaire de l’approche précédente est qu’un `ValidationSummary` composant est peuplé de champs invalides après que l’utilisateur interagit avec n’importe quel champ. Ce scénario peut être abordé de l’une ou l’autre des façons suivantes :

* N’utilisez pas `ValidationSummary` de composant sur le formulaire.
* Rendre `ValidationSummary` le composant visible lorsque le bouton soumettre `HandleValidSubmit` est sélectionné (par exemple, dans une méthode).

```razor
<EditForm EditContext="@_editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@_displaySummary" />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private string _displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        _displaySummary = "display:block";
    }
}
```
