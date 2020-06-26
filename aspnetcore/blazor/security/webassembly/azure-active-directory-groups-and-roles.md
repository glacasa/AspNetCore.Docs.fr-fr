---
title: ASP.NET Core Blazor WebAssembly avec des groupes et des rôles Azure Active Directory
author: guardrex
description: Découvrez comment configurer Blazor WebAssembly pour utiliser des groupes et des rôles Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 6e27b062d7b5a1b72804fe5d4ea31ec65358ce45
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402154"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Groupes de Azure AD, rôles administratifs et rôles définis par l’utilisateur

Par [Luke Latham](https://github.com/guardrex) et [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) fournit plusieurs approches d’autorisation qui peuvent être combinées avec ASP.NET Core Identity :

* Groupes définis par l’utilisateur
  * Sécurité
  * O365
  * Distribution
* Rôles
  * Rôles d’administration intégrés
  * Rôles définis par l’utilisateur

Les instructions de cet article s’appliquent aux Blazor WebAssembly scénarios de déploiement AAD décrits dans les rubriques suivantes :

* [Autonome avec des comptes Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Autonome avec AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Hébergé avec AAD](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a>Groupes définis par l’utilisateur et rôles d’administration intégrés

Pour configurer l’application dans le Portail Azure pour fournir une `groups` revendication d’appartenance, consultez les articles Azure suivants. Affecter des utilisateurs à des groupes AAD définis par l’utilisateur et à des rôles d’administration intégrés.

* [Rôles utilisant les groupes de sécurité Azure AD](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims`attribut](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

Les exemples suivants partent du principe qu’un utilisateur est affecté au rôle d' *administrateur de facturation* intégré AAD.

La `groups` revendication unique envoyée par AAD présente les groupes et les rôles de l’utilisateur en tant qu’ID d’objet (Guid) dans un tableau JSON. L’application doit convertir le tableau JSON de groupes et de rôles en `group` revendications individuelles pour lesquelles l’application peut créer des [stratégies](xref:security/authorization/policies) .

Étendez <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> pour inclure les propriétés de tableau pour les groupes et les rôles.

`CustomUserAccount.cs`:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; }

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; }
}
```

Créez une fabrique d’utilisateur personnalisée dans l’application autonome ou l’application cliente d’une solution hébergée. La fabrique suivante est également configurée pour gérer les `roles` tableaux de revendications, qui sont traités dans la section [rôles définis par l’utilisateur](#user-defined-roles) :

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomUserFactory(NavigationManager navigationManager,
        IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            foreach (var group in account.Groups)
            {
                userIdentity.AddClaim(new Claim("group", group));
            }
        }

        return initialUser;
    }
}
```

Il n’est pas nécessaire de fournir du code pour supprimer la revendication d’origine `groups` , car elle est automatiquement supprimée par l’infrastructure.

Inscrire la fabrique dans `Program.Main` ( `Program.cs` ) de l’application autonome ou de l’application cliente d’une solution hébergée :

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");
    
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

Créez une [stratégie](xref:security/authorization/policies) pour chaque groupe ou rôle dans `Program.Main` . L’exemple suivant crée une stratégie pour le rôle d’administrateur de *facturation* intégré AAD :

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Pour obtenir la liste complète des ID d’objets de rôle AAD, consultez la section [ID de groupe de rôles AAD administrative](#aad-adminstrative-role-group-ids) .

Dans les exemples suivants, l’application utilise la stratégie précédente pour autoriser l’utilisateur.

Le [ `AuthorizeView` composant](xref:blazor/security/index#authorizeview-component) fonctionne avec la stratégie :

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

L’accès à un composant entier peut être basé sur la stratégie à l’aide de la [ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) :

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Si l’utilisateur n’est pas connecté, il est redirigé vers la page de connexion AAD, puis renvoyé au composant une fois qu’il se connecte.

Une vérification de stratégie peut également être [effectuée dans le code avec la logique procédurale](xref:blazor/security/index#procedural-logic):

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

### <a name="user-defined-roles"></a>Rôles définis par l’utilisateur

Une application inscrite à AAD peut également être configurée pour utiliser des rôles définis par l’utilisateur.

Pour configurer l’application dans le Portail Azure pour fournir une `roles` revendication d’appartenance, consultez [Comment : ajouter des rôles d’application dans votre application et les recevoir dans le jeton](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) de la documentation Azure.

L’exemple suivant suppose qu’une application est configurée avec deux rôles :

* `admin`
* `developer`

> [!NOTE]
> Bien que vous ne puissiez pas attribuer des rôles à des groupes de sécurité sans compte Azure AD Premium, vous pouvez affecter des utilisateurs à des rôles et recevoir une `roles` revendication pour les utilisateurs disposant d’un compte Azure standard. Les instructions de cette section ne nécessitent pas de compte Azure AD Premium.
>
> Plusieurs rôles sont affectés dans le Portail Azure en **_rajoutant un utilisateur_** pour chaque attribution de rôle supplémentaire.

La `roles` revendication unique envoyée par AAD présente les rôles définis par l’utilisateur en tant que `appRoles` `value` s dans un tableau JSON. L’application doit convertir le tableau JSON de rôles en `role` revendications individuelles.

La `CustomUserFactory` section des [groupes définis par l’utilisateur et des rôles administratifs intégrés AAD](#user-defined-groups-and-built-in-administrative-roles) est configurée pour agir sur une `roles` revendication avec une valeur de tableau JSON. Ajoutez et inscrivez l' `CustomUserFactory` dans l’application autonome ou l’application cliente d’une solution hébergée, comme indiqué dans la section [groupes définis par l’utilisateur et rôles d’administration intégrés à AAD](#user-defined-groups-and-built-in-administrative-roles) . Il n’est pas nécessaire de fournir du code pour supprimer la revendication d’origine `roles` , car elle est automatiquement supprimée par l’infrastructure.

Dans `Program.Main` l’application autonome ou l’application cliente d’une solution hébergée, spécifiez la revendication nommée « `role` » comme revendication de rôle :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

Les approches d’autorisation des composants sont fonctionnelles à ce stade. L’un des mécanismes d’autorisation des composants peut utiliser le `admin` rôle pour autoriser l’utilisateur :

* [ `AuthorizeView` composant](xref:blazor/security/index#authorizeview-component) (exemple : `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemple : `@attribute [Authorize(Roles = "admin")]` )
* [Logique procédurale](xref:blazor/security/index#procedural-logic) (exemple : `if (user.IsInRole("admin")) { ... }` )

  Plusieurs tests de rôle sont pris en charge :

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a>ID de groupe de rôles d’administration AAD

Les ID d’objet présentés dans le tableau suivant sont utilisés pour créer des [stratégies](xref:security/authorization/policies) pour les `group` revendications. Les stratégies permettent à une application d’autoriser les utilisateurs à effectuer différentes activités dans une application. Pour plus d’informations, consultez la section [groupes définis par l’utilisateur et rôles d’administration intégrés à AAD](#user-defined-groups-and-built-in-administrative-roles) .

Rôle d’administration AAD | ID de l'objet
--- | ---
Administrateur d’application | fa11557b-4f15-4ddd-85d5-313c7cd74047
Développeur d’applications | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Administrateur d’authentification | 02d110a1-96b1-419e-af87-746461b60ed7
Administrateur Azure DevOps | a5311ace-ca41-44cd-b833-8d22caa0b34f
Administrateur Azure Information Protection | 18632dce-f9b5-4f01-abb5-37051f06860e
Administrateur de jeux de clés IEF B2C | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
Administrateur de la stratégie B2C IEF | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
Administrateur de workflow utilisateur B2C | baa531b7-8cf0-44ad-8f98-eded88dae827
Administrateur de l’attribut de workflow de l’utilisateur B2C | dd0baca0-a535-48c1-b871-8431abe16452
Administrateur de facturation | 69ff516a-B57D-4697-A429-9de4af7b5609
Administrateur d’application cloud | 250b5fe3-b553-458d-9a53-b782c13c34bf
Administrateur d’appareil cloud | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
Administrateur de conformité | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
Administrateur des données de conformité | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Administrateur de l’accès conditionnel | 8f71a611-137d-49af-87ad-e97f1fd5da76
Approbateur d’accès au référentiel sécurisé client | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
Administrateur Desktop Analytics | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Lecteurs d’annuaires | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Administrateur Dynamics 365 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Administrateur Exchange | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
IdentityAdministrateur du fournisseur externe | febfaeb4-e478-407a-b4b3-f4d9716618a2
Administrateur général | a45ba61b-44db-462c-924b-3b2719152588
Lecteur général | f6903b21-6aba-4124-B44C-76671796b9d5
Administrateur de groupes | 158b3e5a-d89d-460b-92b5-3b34985f0197
Inviteur d’invités | 4c730a1d-cc22-44af-8f9f-4eec635c7502
Administrateur du support technique | 108678c8-6628-44e1-8d01-caf598a6a5f5
Administrateur Intune | 79950741-23fa-4189-b2cb-46640601c497
Administrateur Kaizala | d6322af2-48e7-42e0-8c68-0bbe31af3412
Administrateur de licence | 3355458a-e423-44bf-8b98-4ac5e572cea5
Lecteur de confidentialité du Centre de messages | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
Lecteur du Centre de messages | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Administrateur d’applications Office | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Administrateur de mots de passe | 466e48b7-5d66-4ae5-8911-1a118de74941
Administrateur Power BI | 984e83b8-8337-4255-91a1-acb663175ab4
Administrateur de plateforme Power | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
Administrateur d’authentification privilégié | 0829f731-b46d-419F-9742-aeb122367d11
Administrateur de rôle privilégié | f20a725a-d1c8-4107-83ea-1171c97d00c7
Lecteur de rapports | 54635450-e8ed-4f2d-9632-07db2517b4de
Administrateur de recherche | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Éditeur de recherche | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
Administrateur de sécurité | 20fa50e3-6531-44d8-bd39-b251420568ad
Opérateur de sécurité | 43aae017-8e51-4188-91ab-e6debd572800
Lecteur de sécurité | 45035cd3-fd97-4250-8197-3a53d3562d9b
Administrateur de support de service | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
Administrateur SharePoint | e1c32229-875e-461d-ae24-3cb99116e86c
Administrateur Skype Entreprise | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Administrateur des communications Teams | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Ingénieur de support des communications Teams | 802dd94e-d717-46f6-af98-b9167071e9fc
Spécialiste des communications des équipes | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Administrateur du service Teams | 8846a0be-197b-443a-b13c-11192691fa24
Administrateur d’utilisateurs | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
