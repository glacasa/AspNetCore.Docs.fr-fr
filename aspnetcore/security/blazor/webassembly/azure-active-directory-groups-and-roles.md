---
title: ASP.NET Core Blazor Webassembly avec des groupes et des rôles Azure Active Directory
author: guardrex
description: Découvrez comment configurer Blazor Webassembly pour utiliser des groupes et des rôles Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/aad-groups-roles
ms.openlocfilehash: 87cdf02a6f6babc869d90658e6a7cd54db73bb68
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756026"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="7e874-103">Groupes de Azure AD, rôles administratifs et rôles définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="7e874-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="7e874-104">Par [Luke Latham](https://github.com/guardrex) et [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="7e874-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="7e874-105">Azure Active Directory (AAD) fournit plusieurs approches d’autorisation qui peuvent être combinées avec ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="7e874-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="7e874-106">Groupes définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="7e874-106">User-defined groups</span></span>
  * <span data-ttu-id="7e874-107">Sécurité</span><span class="sxs-lookup"><span data-stu-id="7e874-107">Security</span></span>
  * <span data-ttu-id="7e874-108">O365</span><span class="sxs-lookup"><span data-stu-id="7e874-108">O365</span></span>
  * <span data-ttu-id="7e874-109">Distribution</span><span class="sxs-lookup"><span data-stu-id="7e874-109">Distribution</span></span>
* <span data-ttu-id="7e874-110">Rôles</span><span class="sxs-lookup"><span data-stu-id="7e874-110">Roles</span></span>
  * <span data-ttu-id="7e874-111">Rôles d’administration intégrés</span><span class="sxs-lookup"><span data-stu-id="7e874-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="7e874-112">Rôles définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="7e874-112">User-defined roles</span></span>

<span data-ttu-id="7e874-113">Les instructions de cet article s’appliquent aux Blazor scénarios de déploiement de Webassembly AAD décrits dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="7e874-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="7e874-114">Autonome avec des comptes Microsoft</span><span class="sxs-lookup"><span data-stu-id="7e874-114">Standalone with Microsoft Accounts</span></span>](xref:security/blazor/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="7e874-115">Autonome avec AAD</span><span class="sxs-lookup"><span data-stu-id="7e874-115">Standalone with AAD</span></span>](xref:security/blazor/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="7e874-116">Hébergé avec AAD</span><span class="sxs-lookup"><span data-stu-id="7e874-116">Hosted with AAD</span></span>](xref:security/blazor/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="7e874-117">Groupes définis par l’utilisateur et rôles d’administration intégrés</span><span class="sxs-lookup"><span data-stu-id="7e874-117">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="7e874-118">Pour configurer l’application dans le Portail Azure pour fournir une `groups` revendication d’appartenance, consultez les articles Azure suivants.</span><span class="sxs-lookup"><span data-stu-id="7e874-118">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="7e874-119">Affecter des utilisateurs à des groupes AAD définis par l’utilisateur et à des rôles d’administration intégrés.</span><span class="sxs-lookup"><span data-stu-id="7e874-119">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="7e874-120">Rôles utilisant les groupes de sécurité Azure AD</span><span class="sxs-lookup"><span data-stu-id="7e874-120">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="7e874-121">attribut groupMembershipClaims</span><span class="sxs-lookup"><span data-stu-id="7e874-121">groupMembershipClaims attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="7e874-122">Les exemples suivants partent du principe qu’un utilisateur est affecté au rôle d' *administrateur de facturation* intégré AAD.</span><span class="sxs-lookup"><span data-stu-id="7e874-122">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="7e874-123">La `groups` revendication unique envoyée par AAD présente les groupes et les rôles de l’utilisateur en tant qu’ID d’objet (Guid) dans un tableau JSON.</span><span class="sxs-lookup"><span data-stu-id="7e874-123">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="7e874-124">L’application doit convertir le tableau JSON de groupes et de rôles en `group` revendications individuelles pour lesquelles l’application peut créer des [stratégies](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="7e874-124">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="7e874-125">Étendez <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> pour inclure les propriétés de tableau pour les groupes et les rôles.</span><span class="sxs-lookup"><span data-stu-id="7e874-125">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span>

<span data-ttu-id="7e874-126">*CustomUserAccount.cs*:</span><span class="sxs-lookup"><span data-stu-id="7e874-126">*CustomUserAccount.cs*:</span></span>

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

<span data-ttu-id="7e874-127">Créez une fabrique d’utilisateur personnalisée dans l’application autonome ou l’application cliente d’une solution hébergée.</span><span class="sxs-lookup"><span data-stu-id="7e874-127">Create a custom user factory in the standalone app or Client app of a Hosted solution.</span></span> <span data-ttu-id="7e874-128">La fabrique suivante est également configurée pour gérer les `roles` tableaux de revendications, qui sont traités dans la section [rôles définis par l’utilisateur](#user-defined-roles) :</span><span class="sxs-lookup"><span data-stu-id="7e874-128">The following factory is also configured to handle `roles` claim arrays, which are covered in the [User-defined roles](#user-defined-roles) section:</span></span>

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

<span data-ttu-id="7e874-129">Il n’est pas nécessaire de fournir du code pour supprimer la revendication d’origine `groups` , car elle est automatiquement supprimée par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="7e874-129">There's no need to provide code to remove the original `groups` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="7e874-130">Inscrire la fabrique dans `Program.Main` (*Program.cs*) de l’application autonome ou de l’application cliente d’une solution hébergée :</span><span class="sxs-lookup"><span data-stu-id="7e874-130">Register the factory in `Program.Main` (*Program.cs*) of the standalone app or Client app of a Hosted solution:</span></span>

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

<span data-ttu-id="7e874-131">Créez une [stratégie](xref:security/authorization/policies) pour chaque groupe ou rôle dans `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="7e874-131">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="7e874-132">L’exemple suivant crée une stratégie pour le rôle d’administrateur de *facturation* intégré AAD :</span><span class="sxs-lookup"><span data-stu-id="7e874-132">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="7e874-133">Pour obtenir la liste complète des ID d’objets de rôle AAD, consultez la section [ID de groupe de rôles AAD administrative](#aad-adminstrative-role-group-ids) .</span><span class="sxs-lookup"><span data-stu-id="7e874-133">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="7e874-134">Dans les exemples suivants, l’application utilise la stratégie précédente pour autoriser l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7e874-134">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="7e874-135">Le [composant AuthorizeView](xref:security/blazor/index#authorizeview-component) fonctionne avec la stratégie :</span><span class="sxs-lookup"><span data-stu-id="7e874-135">The [AuthorizeView component](xref:security/blazor/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="7e874-136">L’accès à un composant entier peut être basé sur la stratégie à l’aide de la [ `[Authorize]` directive d’attribut](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) :</span><span class="sxs-lookup"><span data-stu-id="7e874-136">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:security/blazor/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="7e874-137">Si l’utilisateur n’est pas connecté, il est redirigé vers la page de connexion AAD, puis renvoyé au composant une fois qu’il se connecte.</span><span class="sxs-lookup"><span data-stu-id="7e874-137">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="7e874-138">Une vérification de stratégie peut également être [effectuée dans le code avec la logique procédurale](xref:security/blazor/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="7e874-138">A policy check can also be [performed in code with procedural logic](xref:security/blazor/index#procedural-logic):</span></span>

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

### <a name="user-defined-roles"></a><span data-ttu-id="7e874-139">Rôles définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="7e874-139">User-defined roles</span></span>

<span data-ttu-id="7e874-140">Une application inscrite à AAD peut également être configurée pour utiliser des rôles définis par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7e874-140">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="7e874-141">Pour configurer l’application dans le Portail Azure pour fournir une `roles` revendication d’appartenance, consultez [Comment : ajouter des rôles d’application dans votre application et les recevoir dans le jeton](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) de la documentation Azure.</span><span class="sxs-lookup"><span data-stu-id="7e874-141">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="7e874-142">L’exemple suivant suppose qu’une application est configurée avec deux rôles :</span><span class="sxs-lookup"><span data-stu-id="7e874-142">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="7e874-143">Bien que vous ne puissiez pas attribuer des rôles à des groupes de sécurité sans compte Azure AD Premium, vous pouvez affecter des utilisateurs à des rôles et recevoir une `roles` revendication pour les utilisateurs disposant d’un compte Azure standard.</span><span class="sxs-lookup"><span data-stu-id="7e874-143">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="7e874-144">Les instructions de cette section ne nécessitent pas de compte Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="7e874-144">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="7e874-145">Plusieurs rôles sont affectés dans le Portail Azure en **_rajoutant un utilisateur_** pour chaque attribution de rôle supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="7e874-145">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="7e874-146">La `roles` revendication unique envoyée par AAD présente les rôles définis par l’utilisateur en tant que `appRoles` `value` s dans un tableau JSON.</span><span class="sxs-lookup"><span data-stu-id="7e874-146">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="7e874-147">L’application doit convertir le tableau JSON de rôles en `role` revendications individuelles.</span><span class="sxs-lookup"><span data-stu-id="7e874-147">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="7e874-148">La `CustomUserFactory` section des [groupes définis par l’utilisateur et des rôles administratifs intégrés AAD](#user-defined-groups-and-built-in-administrative-roles) est configurée pour agir sur une `roles` revendication avec une valeur de tableau JSON.</span><span class="sxs-lookup"><span data-stu-id="7e874-148">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="7e874-149">Ajoutez et inscrivez l' `CustomUserFactory` dans l’application autonome ou l’application cliente d’une solution hébergée, comme indiqué dans la section [groupes définis par l’utilisateur et rôles d’administration intégrés à AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="7e874-149">Add and register the `CustomUserFactory` in the standalone app or Client app of a Hosted solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="7e874-150">Il n’est pas nécessaire de fournir du code pour supprimer la revendication d’origine `roles` , car elle est automatiquement supprimée par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="7e874-150">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="7e874-151">Dans `Program.Main` l’application autonome ou l’application cliente d’une solution hébergée, spécifiez la revendication nommée « `role` » comme revendication de rôle :</span><span class="sxs-lookup"><span data-stu-id="7e874-151">In `Program.Main` of the standalone app or Client app of a Hosted solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="7e874-152">Les approches d’autorisation des composants sont fonctionnelles à ce stade.</span><span class="sxs-lookup"><span data-stu-id="7e874-152">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="7e874-153">L’un des mécanismes d’autorisation des composants peut utiliser le `admin` rôle pour autoriser l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="7e874-153">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="7e874-154">[Composant AuthorizeView](xref:security/blazor/index#authorizeview-component) (exemple : `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="7e874-154">[AuthorizeView component](xref:security/blazor/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="7e874-155">[ `[Authorize]` directive d’attribut](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemple : `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="7e874-155">[`[Authorize]` attribute directive](xref:security/blazor/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="7e874-156">[Logique procédurale](xref:security/blazor/index#procedural-logic) (exemple : `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="7e874-156">[Procedural logic](xref:security/blazor/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="7e874-157">Plusieurs tests de rôle sont pris en charge :</span><span class="sxs-lookup"><span data-stu-id="7e874-157">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="7e874-158">ID de groupe de rôles d’administration AAD</span><span class="sxs-lookup"><span data-stu-id="7e874-158">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="7e874-159">Les ID d’objet présentés dans le tableau suivant sont utilisés pour créer des [stratégies](xref:security/authorization/policies) pour les `group` revendications.</span><span class="sxs-lookup"><span data-stu-id="7e874-159">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="7e874-160">Les stratégies permettent à une application d’autoriser les utilisateurs à effectuer différentes activités dans une application.</span><span class="sxs-lookup"><span data-stu-id="7e874-160">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="7e874-161">Pour plus d’informations, consultez la section [groupes définis par l’utilisateur et rôles d’administration intégrés à AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="7e874-161">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="7e874-162">Rôle d’administration AAD</span><span class="sxs-lookup"><span data-stu-id="7e874-162">AAD Administrative Role</span></span> | <span data-ttu-id="7e874-163">ID de l'objet</span><span class="sxs-lookup"><span data-stu-id="7e874-163">Object ID</span></span>
--- | ---
<span data-ttu-id="7e874-164">Administrateur d’application</span><span class="sxs-lookup"><span data-stu-id="7e874-164">Application administrator</span></span> | <span data-ttu-id="7e874-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="7e874-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="7e874-166">Développeur d’applications</span><span class="sxs-lookup"><span data-stu-id="7e874-166">Application developer</span></span> | <span data-ttu-id="7e874-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="7e874-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="7e874-168">Administrateur d’authentification</span><span class="sxs-lookup"><span data-stu-id="7e874-168">Authentication administrator</span></span> | <span data-ttu-id="7e874-169">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="7e874-169">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="7e874-170">Administrateur Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="7e874-170">Azure DevOps administrator</span></span> | <span data-ttu-id="7e874-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="7e874-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="7e874-172">Administrateur Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="7e874-172">Azure Information Protection administrator</span></span> | <span data-ttu-id="7e874-173">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="7e874-173">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="7e874-174">Administrateur de jeux de clés IEF B2C</span><span class="sxs-lookup"><span data-stu-id="7e874-174">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="7e874-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="7e874-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="7e874-176">Administrateur de la stratégie B2C IEF</span><span class="sxs-lookup"><span data-stu-id="7e874-176">B2C IEF Policy administrator</span></span> | <span data-ttu-id="7e874-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="7e874-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="7e874-178">Administrateur de workflow utilisateur B2C</span><span class="sxs-lookup"><span data-stu-id="7e874-178">B2C user flow administrator</span></span> | <span data-ttu-id="7e874-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="7e874-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="7e874-180">Administrateur de l’attribut de workflow de l’utilisateur B2C</span><span class="sxs-lookup"><span data-stu-id="7e874-180">B2C user flow attribute administrator</span></span> | <span data-ttu-id="7e874-181">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="7e874-181">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="7e874-182">Administrateur de facturation</span><span class="sxs-lookup"><span data-stu-id="7e874-182">Billing administrator</span></span> | <span data-ttu-id="7e874-183">69ff516a-B57D-4697-A429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="7e874-183">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="7e874-184">Administrateur d’application cloud</span><span class="sxs-lookup"><span data-stu-id="7e874-184">Cloud application administrator</span></span> | <span data-ttu-id="7e874-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="7e874-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="7e874-186">Administrateur d’appareil cloud</span><span class="sxs-lookup"><span data-stu-id="7e874-186">Cloud device administrator</span></span> | <span data-ttu-id="7e874-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="7e874-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="7e874-188">Administrateur de conformité</span><span class="sxs-lookup"><span data-stu-id="7e874-188">Compliance administrator</span></span> | <span data-ttu-id="7e874-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="7e874-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="7e874-190">Administrateur des données de conformité</span><span class="sxs-lookup"><span data-stu-id="7e874-190">Compliance data administrator</span></span> | <span data-ttu-id="7e874-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="7e874-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="7e874-192">Administrateur de l’accès conditionnel</span><span class="sxs-lookup"><span data-stu-id="7e874-192">Conditional Access administrator</span></span> | <span data-ttu-id="7e874-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="7e874-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="7e874-194">Approbateur d’accès au référentiel sécurisé client</span><span class="sxs-lookup"><span data-stu-id="7e874-194">Customer LockBox access approver</span></span> | <span data-ttu-id="7e874-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="7e874-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="7e874-196">Administrateur Desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="7e874-196">Desktop Analytics administrator</span></span> | <span data-ttu-id="7e874-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="7e874-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="7e874-198">Lecteurs d’annuaires</span><span class="sxs-lookup"><span data-stu-id="7e874-198">Directory readers</span></span> | <span data-ttu-id="7e874-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="7e874-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="7e874-200">Administrateur Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="7e874-200">Dynamics 365 administrator</span></span> | <span data-ttu-id="7e874-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="7e874-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="7e874-202">Administrateur Exchange</span><span class="sxs-lookup"><span data-stu-id="7e874-202">Exchange administrator</span></span> | <span data-ttu-id="7e874-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="7e874-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="7e874-204">IdentityAdministrateur du fournisseur externe</span><span class="sxs-lookup"><span data-stu-id="7e874-204">External Identity Provider administrator</span></span> | <span data-ttu-id="7e874-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="7e874-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="7e874-206">Administrateur général</span><span class="sxs-lookup"><span data-stu-id="7e874-206">Global administrator</span></span> | <span data-ttu-id="7e874-207">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="7e874-207">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="7e874-208">Lecteur général</span><span class="sxs-lookup"><span data-stu-id="7e874-208">Global reader</span></span> | <span data-ttu-id="7e874-209">f6903b21-6aba-4124-B44C-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="7e874-209">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="7e874-210">Administrateur de groupes</span><span class="sxs-lookup"><span data-stu-id="7e874-210">Groups administrator</span></span> | <span data-ttu-id="7e874-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="7e874-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="7e874-212">Inviteur d’invités</span><span class="sxs-lookup"><span data-stu-id="7e874-212">Guest inviter</span></span> | <span data-ttu-id="7e874-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="7e874-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="7e874-214">Administrateur du support technique</span><span class="sxs-lookup"><span data-stu-id="7e874-214">Helpdesk administrator</span></span> | <span data-ttu-id="7e874-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="7e874-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="7e874-216">Administrateur Intune</span><span class="sxs-lookup"><span data-stu-id="7e874-216">Intune administrator</span></span> | <span data-ttu-id="7e874-217">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="7e874-217">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="7e874-218">Administrateur Kaizala</span><span class="sxs-lookup"><span data-stu-id="7e874-218">Kaizala administrator</span></span> | <span data-ttu-id="7e874-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="7e874-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="7e874-220">Administrateur de licence</span><span class="sxs-lookup"><span data-stu-id="7e874-220">License administrator</span></span> | <span data-ttu-id="7e874-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="7e874-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="7e874-222">Lecteur de confidentialité du Centre de messages</span><span class="sxs-lookup"><span data-stu-id="7e874-222">Message center privacy reader</span></span> | <span data-ttu-id="7e874-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="7e874-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="7e874-224">Lecteur du Centre de messages</span><span class="sxs-lookup"><span data-stu-id="7e874-224">Message center reader</span></span> | <span data-ttu-id="7e874-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="7e874-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="7e874-226">Administrateur d’applications Office</span><span class="sxs-lookup"><span data-stu-id="7e874-226">Office apps administrator</span></span> | <span data-ttu-id="7e874-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="7e874-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="7e874-228">Administrateur de mots de passe</span><span class="sxs-lookup"><span data-stu-id="7e874-228">Password administrator</span></span> | <span data-ttu-id="7e874-229">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="7e874-229">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="7e874-230">Administrateur Power BI</span><span class="sxs-lookup"><span data-stu-id="7e874-230">Power BI administrator</span></span> | <span data-ttu-id="7e874-231">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="7e874-231">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="7e874-232">Administrateur de plateforme Power</span><span class="sxs-lookup"><span data-stu-id="7e874-232">Power platform administrator</span></span> | <span data-ttu-id="7e874-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="7e874-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="7e874-234">Administrateur d’authentification privilégié</span><span class="sxs-lookup"><span data-stu-id="7e874-234">Privileged authentication administrator</span></span> | <span data-ttu-id="7e874-235">0829f731-b46d-419F-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="7e874-235">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="7e874-236">Administrateur de rôle privilégié</span><span class="sxs-lookup"><span data-stu-id="7e874-236">Privileged role administrator</span></span> | <span data-ttu-id="7e874-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="7e874-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="7e874-238">Lecteur de rapports</span><span class="sxs-lookup"><span data-stu-id="7e874-238">Reports reader</span></span> | <span data-ttu-id="7e874-239">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="7e874-239">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="7e874-240">Administrateur de recherche</span><span class="sxs-lookup"><span data-stu-id="7e874-240">Search administrator</span></span> | <span data-ttu-id="7e874-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="7e874-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="7e874-242">Éditeur de recherche</span><span class="sxs-lookup"><span data-stu-id="7e874-242">Search editor</span></span> | <span data-ttu-id="7e874-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="7e874-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="7e874-244">Administrateur de sécurité</span><span class="sxs-lookup"><span data-stu-id="7e874-244">Security administrator</span></span> | <span data-ttu-id="7e874-245">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="7e874-245">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="7e874-246">Opérateur de sécurité</span><span class="sxs-lookup"><span data-stu-id="7e874-246">Security operator</span></span> | <span data-ttu-id="7e874-247">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="7e874-247">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="7e874-248">Lecteur de sécurité</span><span class="sxs-lookup"><span data-stu-id="7e874-248">Security reader</span></span> | <span data-ttu-id="7e874-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="7e874-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="7e874-250">Administrateur de support de service</span><span class="sxs-lookup"><span data-stu-id="7e874-250">Service support administrator</span></span> | <span data-ttu-id="7e874-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="7e874-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="7e874-252">Administrateur SharePoint</span><span class="sxs-lookup"><span data-stu-id="7e874-252">SharePoint administrator</span></span> | <span data-ttu-id="7e874-253">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="7e874-253">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="7e874-254">Administrateur Skype Entreprise</span><span class="sxs-lookup"><span data-stu-id="7e874-254">Skype for Business administrator</span></span> | <span data-ttu-id="7e874-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="7e874-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="7e874-256">Administrateur des communications Teams</span><span class="sxs-lookup"><span data-stu-id="7e874-256">Teams Communications Administrator</span></span> | <span data-ttu-id="7e874-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="7e874-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="7e874-258">Ingénieur de support des communications Teams</span><span class="sxs-lookup"><span data-stu-id="7e874-258">Teams Communications Support Engineer</span></span> | <span data-ttu-id="7e874-259">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="7e874-259">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="7e874-260">Spécialiste des communications des équipes</span><span class="sxs-lookup"><span data-stu-id="7e874-260">Teams Communications Specialist</span></span> | <span data-ttu-id="7e874-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="7e874-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="7e874-262">Administrateur du service Teams</span><span class="sxs-lookup"><span data-stu-id="7e874-262">Teams Service Administrator</span></span> | <span data-ttu-id="7e874-263">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="7e874-263">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="7e874-264">Administrateur d’utilisateurs</span><span class="sxs-lookup"><span data-stu-id="7e874-264">User administrator</span></span> | <span data-ttu-id="7e874-265">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="7e874-265">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e874-266">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7e874-266">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:security/blazor/index>
