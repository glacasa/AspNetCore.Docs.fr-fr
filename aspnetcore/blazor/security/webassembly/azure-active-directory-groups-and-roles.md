---
title: ASP.NET Core Blazor WebAssembly avec des groupes et des rôles Azure Active Directory
author: guardrex
description: Découvrez comment configurer Blazor WebAssembly pour utiliser des groupes et des rôles Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: adc16989e5b4e2e639896e5fe9562e42cb8ceeb4
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102742"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="f275a-103">Groupes de Azure AD, rôles administratifs et rôles définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="f275a-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="f275a-104">Par [Luke Latham](https://github.com/guardrex) et [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="f275a-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="f275a-105">Azure Active Directory (AAD) fournit plusieurs approches d’autorisation qui peuvent être combinées avec ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="f275a-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="f275a-106">Groupes définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="f275a-106">User-defined groups</span></span>
  * <span data-ttu-id="f275a-107">Sécurité</span><span class="sxs-lookup"><span data-stu-id="f275a-107">Security</span></span>
  * <span data-ttu-id="f275a-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="f275a-108">Microsoft 365</span></span>
  * <span data-ttu-id="f275a-109">Distribution</span><span class="sxs-lookup"><span data-stu-id="f275a-109">Distribution</span></span>
* <span data-ttu-id="f275a-110">Rôles</span><span class="sxs-lookup"><span data-stu-id="f275a-110">Roles</span></span>
  * <span data-ttu-id="f275a-111">Rôles d’administration intégrés</span><span class="sxs-lookup"><span data-stu-id="f275a-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="f275a-112">Rôles définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="f275a-112">User-defined roles</span></span>

<span data-ttu-id="f275a-113">Les instructions de cet article s’appliquent aux Blazor WebAssembly scénarios de déploiement AAD décrits dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="f275a-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="f275a-114">Autonome avec des comptes Microsoft</span><span class="sxs-lookup"><span data-stu-id="f275a-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="f275a-115">Autonome avec AAD</span><span class="sxs-lookup"><span data-stu-id="f275a-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="f275a-116">Hébergé avec AAD</span><span class="sxs-lookup"><span data-stu-id="f275a-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="f275a-117">Microsoft Graph l’autorisation de l’API</span><span class="sxs-lookup"><span data-stu-id="f275a-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="f275a-118">Un appel d' [API Microsoft Graph](/graph/use-the-api) est requis pour tous les utilisateurs d’applications possédant plus de cinq rôles d’administrateur AAD intégrés et appartenances à des groupes de sécurité.</span><span class="sxs-lookup"><span data-stu-id="f275a-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five built-in AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="f275a-119">Pour autoriser les appels API Graph, accordez à l’application autonome ou cliente d’une solution hébergée l' Blazor une des [autorisations API Graph](/graph/permissions-reference) suivantes dans la portail Azure :</span><span class="sxs-lookup"><span data-stu-id="f275a-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="f275a-120">`Directory.Read.All` est l’autorisation à privilèges minimaux et est l’autorisation utilisée pour l’exemple décrit dans cet article.</span><span class="sxs-lookup"><span data-stu-id="f275a-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="f275a-121">Groupes définis par l’utilisateur et rôles d’administration intégrés</span><span class="sxs-lookup"><span data-stu-id="f275a-121">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="f275a-122">Pour configurer l’application dans le Portail Azure pour fournir une `groups` revendication d’appartenance, consultez les articles Azure suivants.</span><span class="sxs-lookup"><span data-stu-id="f275a-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="f275a-123">Affecter des utilisateurs à des groupes AAD définis par l’utilisateur et à des rôles d’administration intégrés.</span><span class="sxs-lookup"><span data-stu-id="f275a-123">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="f275a-124">Rôles utilisant les groupes de sécurité Azure AD</span><span class="sxs-lookup"><span data-stu-id="f275a-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="f275a-125">`groupMembershipClaims` attribut</span><span class="sxs-lookup"><span data-stu-id="f275a-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="f275a-126">Les exemples suivants partent du principe qu’un utilisateur est affecté au rôle d' *administrateur de facturation* intégré AAD.</span><span class="sxs-lookup"><span data-stu-id="f275a-126">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="f275a-127">La `groups` revendication unique envoyée par AAD présente les groupes et les rôles de l’utilisateur en tant qu’ID d’objet (Guid) dans un tableau JSON.</span><span class="sxs-lookup"><span data-stu-id="f275a-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="f275a-128">L’application doit convertir le tableau JSON de groupes et de rôles en `group` revendications individuelles pour lesquelles l’application peut créer des [stratégies](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="f275a-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="f275a-129">Lorsque le nombre de rôles d’administration Azure intégrés et de groupes définis par l’utilisateur est supérieur à cinq, AAD envoie une `hasgroups` revendication avec une `true` valeur au lieu d’envoyer une `groups` revendication.</span><span class="sxs-lookup"><span data-stu-id="f275a-129">When the number of assigned built-in Azure Administrative Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="f275a-130">Toute application pouvant comporter plus de cinq rôles et groupes affectés à ses utilisateurs doit effectuer un appel de API Graph séparé pour obtenir les rôles et les groupes d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f275a-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="f275a-131">L’exemple d’implémentation fourni dans cet article s’adresse à ce scénario.</span><span class="sxs-lookup"><span data-stu-id="f275a-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="f275a-132">Pour plus d’informations, consultez `groups` l' `hasgroups` article et les informations sur les revendications dans les jetons d’accès de la [plateforme Microsoft Identity : revendications de charge utile](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="f275a-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="f275a-133">Étendez <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> pour inclure les propriétés de tableau pour les groupes et les rôles.</span><span class="sxs-lookup"><span data-stu-id="f275a-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="f275a-134">Assignez un tableau vide à chaque propriété afin qu' `null` il ne soit pas nécessaire de vérifier si ces propriétés sont utilisées ultérieurement dans les `foreach` boucles.</span><span class="sxs-lookup"><span data-stu-id="f275a-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="f275a-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="f275a-135">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

<span data-ttu-id="f275a-136">Dans l’application autonome ou l’application cliente d’une Blazor solution hébergée, créez une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personnalisée.</span><span class="sxs-lookup"><span data-stu-id="f275a-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="f275a-137">Utilisez l’étendue (autorisation) appropriée pour les appels API Graph qui obtiennent des informations sur les rôles et les groupes.</span><span class="sxs-lookup"><span data-stu-id="f275a-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="f275a-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="f275a-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

<span data-ttu-id="f275a-139">Dans `Program.Main` ( `Program.cs` ), ajoutez le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service d’implémentation et ajoutez un nommé <xref:System.Net.Http.HttpClient> pour effectuer des demandes de API Graph.</span><span class="sxs-lookup"><span data-stu-id="f275a-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="f275a-140">L’exemple suivant nomme le client `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="f275a-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="f275a-141">Créez des classes d’objets d’annuaire AAD pour recevoir les groupes et rôles Open Data Protocol (OData) d’un appel de API Graph.</span><span class="sxs-lookup"><span data-stu-id="f275a-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="f275a-142">OData arrive au format JSON, et un appel à <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> remplit une instance de la `DirectoryObjects` classe.</span><span class="sxs-lookup"><span data-stu-id="f275a-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="f275a-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="f275a-143">`DirectoryObjects.cs`:</span></span>

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

<span data-ttu-id="f275a-144">Créez une fabrique d’utilisateurs personnalisée pour traiter les revendications de rôles et de groupes.</span><span class="sxs-lookup"><span data-stu-id="f275a-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="f275a-145">L’exemple d’implémentation suivant gère également le `roles` tableau de revendications, qui est abordé dans la section [rôles définis par l’utilisateur](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="f275a-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="f275a-146">Si la `hasgroups` revendication est présente, le nommé <xref:System.Net.Http.HttpClient> est utilisé pour effectuer une demande autorisée à API Graph pour obtenir les rôles et les groupes de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f275a-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="f275a-147">Cette implémentation utilise le Identity point de terminaison Microsoft Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` (documentation de l'[API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="f275a-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="f275a-148">Les instructions de cette rubrique seront mises à jour pour Identity v 2.0 lorsque les packages MSAL seront mis à niveau pour v 2.0.</span><span class="sxs-lookup"><span data-stu-id="f275a-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="f275a-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="f275a-149">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> _logger;
    private readonly IHttpClientFactory _clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        _clientFactory = clientFactory;
        _logger = logger;
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

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = _clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        _logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    _logger.LogError("Graph API access token failure: {MESSAGE}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="f275a-150">Il n’est pas nécessaire de fournir du code pour supprimer la revendication d’origine `groups` , le cas échéant, car elle est automatiquement supprimée par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="f275a-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="f275a-151">L’approche dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="f275a-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="f275a-152">Ajoute une <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personnalisée pour joindre des jetons d’accès aux demandes sortantes.</span><span class="sxs-lookup"><span data-stu-id="f275a-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="f275a-153">Ajoute un nommé <xref:System.Net.Http.HttpClient> pour effectuer des demandes d’API Web à un point de terminaison d’API Web sécurisé et externe.</span><span class="sxs-lookup"><span data-stu-id="f275a-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="f275a-154">Utilise le nommé <xref:System.Net.Http.HttpClient> pour effectuer des demandes autorisées.</span><span class="sxs-lookup"><span data-stu-id="f275a-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="f275a-155">La couverture générale de cette approche est disponible dans l' <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span><span class="sxs-lookup"><span data-stu-id="f275a-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="f275a-156">Inscrire la fabrique dans `Program.Main` ( `Program.cs` ) de l’application autonome ou de l’application cliente d’une solution hébergée Blazor .</span><span class="sxs-lookup"><span data-stu-id="f275a-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="f275a-157">Consentement de l' `Directory.Read.All` étendue d’autorisation en tant qu’étendue supplémentaire pour l’application :</span><span class="sxs-lookup"><span data-stu-id="f275a-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

<span data-ttu-id="f275a-158">Créez une [stratégie](xref:security/authorization/policies) pour chaque groupe ou rôle dans `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="f275a-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="f275a-159">L’exemple suivant crée une stratégie pour le rôle d’administrateur de *facturation* intégré AAD :</span><span class="sxs-lookup"><span data-stu-id="f275a-159">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="f275a-160">Pour obtenir la liste complète des ID d’objets de rôle AAD, consultez la section [ID de groupe de rôles AAD administrative](#aad-adminstrative-role-group-ids) .</span><span class="sxs-lookup"><span data-stu-id="f275a-160">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="f275a-161">Dans les exemples suivants, l’application utilise la stratégie précédente pour autoriser l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f275a-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="f275a-162">Le [ `AuthorizeView` composant](xref:blazor/security/index#authorizeview-component) fonctionne avec la stratégie :</span><span class="sxs-lookup"><span data-stu-id="f275a-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="f275a-163">L’accès à un composant entier peut être basé sur la stratégie à l’aide de la [ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) :</span><span class="sxs-lookup"><span data-stu-id="f275a-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="f275a-164">Si l’utilisateur n’est pas connecté, il est redirigé vers la page de connexion AAD, puis renvoyé au composant une fois qu’il se connecte.</span><span class="sxs-lookup"><span data-stu-id="f275a-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="f275a-165">Une vérification de stratégie peut également être [effectuée dans le code avec la logique procédurale](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="f275a-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="f275a-166">Rôles définis par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="f275a-166">User-defined roles</span></span>

<span data-ttu-id="f275a-167">Une application inscrite à AAD peut également être configurée pour utiliser des rôles définis par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f275a-167">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="f275a-168">Pour configurer l’application dans le Portail Azure pour fournir une `roles` revendication d’appartenance, consultez [Comment : ajouter des rôles d’application dans votre application et les recevoir dans le jeton](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) de la documentation Azure.</span><span class="sxs-lookup"><span data-stu-id="f275a-168">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="f275a-169">L’exemple suivant suppose qu’une application est configurée avec deux rôles :</span><span class="sxs-lookup"><span data-stu-id="f275a-169">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="f275a-170">Bien que vous ne puissiez pas attribuer des rôles à des groupes de sécurité sans compte Azure AD Premium, vous pouvez affecter des utilisateurs à des rôles et recevoir une `roles` revendication pour les utilisateurs disposant d’un compte Azure standard.</span><span class="sxs-lookup"><span data-stu-id="f275a-170">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="f275a-171">Les instructions de cette section ne nécessitent pas de compte Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="f275a-171">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="f275a-172">Plusieurs rôles sont affectés dans le Portail Azure en **_rajoutant un utilisateur_** pour chaque attribution de rôle supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="f275a-172">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="f275a-173">La `roles` revendication unique envoyée par AAD présente les rôles définis par l’utilisateur en tant que `appRoles` `value` s dans un tableau JSON.</span><span class="sxs-lookup"><span data-stu-id="f275a-173">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="f275a-174">L’application doit convertir le tableau JSON de rôles en `role` revendications individuelles.</span><span class="sxs-lookup"><span data-stu-id="f275a-174">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="f275a-175">La `CustomUserFactory` section des [groupes définis par l’utilisateur et des rôles administratifs intégrés AAD](#user-defined-groups-and-built-in-administrative-roles) est configurée pour agir sur une `roles` revendication avec une valeur de tableau JSON.</span><span class="sxs-lookup"><span data-stu-id="f275a-175">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="f275a-176">Ajoutez et inscrivez l' `CustomUserFactory` dans l’application autonome ou l’application cliente d’une solution hébergée Blazor , comme indiqué dans la section [groupes définis par l’utilisateur et rôles d’administration intégrés à AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="f275a-176">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="f275a-177">Il n’est pas nécessaire de fournir du code pour supprimer la revendication d’origine `roles` , car elle est automatiquement supprimée par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="f275a-177">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="f275a-178">Dans `Program.Main` l’application autonome ou l’application cliente d’une Blazor solution hébergée, spécifiez la revendication nommée « `role` » comme revendication de rôle :</span><span class="sxs-lookup"><span data-stu-id="f275a-178">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="f275a-179">Les approches d’autorisation des composants sont fonctionnelles à ce stade.</span><span class="sxs-lookup"><span data-stu-id="f275a-179">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="f275a-180">L’un des mécanismes d’autorisation des composants peut utiliser le `admin` rôle pour autoriser l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="f275a-180">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="f275a-181">[ `AuthorizeView` composant](xref:blazor/security/index#authorizeview-component) (exemple : `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="f275a-181">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="f275a-182">[ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemple : `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="f275a-182">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="f275a-183">[Logique procédurale](xref:blazor/security/index#procedural-logic) (exemple : `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="f275a-183">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="f275a-184">Plusieurs tests de rôle sont pris en charge :</span><span class="sxs-lookup"><span data-stu-id="f275a-184">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="f275a-185">ID de groupe de rôles d’administration AAD</span><span class="sxs-lookup"><span data-stu-id="f275a-185">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="f275a-186">Les ID d’objet présentés dans le tableau suivant sont utilisés pour créer des [stratégies](xref:security/authorization/policies) pour les `group` revendications.</span><span class="sxs-lookup"><span data-stu-id="f275a-186">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="f275a-187">Les stratégies permettent à une application d’autoriser les utilisateurs à effectuer différentes activités dans une application.</span><span class="sxs-lookup"><span data-stu-id="f275a-187">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="f275a-188">Pour plus d’informations, consultez la section [groupes définis par l’utilisateur et rôles d’administration intégrés à AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="f275a-188">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="f275a-189">Rôle d’administration AAD</span><span class="sxs-lookup"><span data-stu-id="f275a-189">AAD Administrative Role</span></span> | <span data-ttu-id="f275a-190">ID de l'objet</span><span class="sxs-lookup"><span data-stu-id="f275a-190">Object ID</span></span>
--- | ---
<span data-ttu-id="f275a-191">Administrateur d’application</span><span class="sxs-lookup"><span data-stu-id="f275a-191">Application administrator</span></span> | <span data-ttu-id="f275a-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="f275a-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="f275a-193">Développeur d’applications</span><span class="sxs-lookup"><span data-stu-id="f275a-193">Application developer</span></span> | <span data-ttu-id="f275a-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="f275a-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="f275a-195">Administrateur d’authentification</span><span class="sxs-lookup"><span data-stu-id="f275a-195">Authentication administrator</span></span> | <span data-ttu-id="f275a-196">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="f275a-196">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="f275a-197">Administrateur Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="f275a-197">Azure DevOps administrator</span></span> | <span data-ttu-id="f275a-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="f275a-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="f275a-199">Administrateur Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="f275a-199">Azure Information Protection administrator</span></span> | <span data-ttu-id="f275a-200">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="f275a-200">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="f275a-201">Administrateur de jeux de clés IEF B2C</span><span class="sxs-lookup"><span data-stu-id="f275a-201">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="f275a-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="f275a-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="f275a-203">Administrateur de la stratégie B2C IEF</span><span class="sxs-lookup"><span data-stu-id="f275a-203">B2C IEF Policy administrator</span></span> | <span data-ttu-id="f275a-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="f275a-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="f275a-205">Administrateur de workflow utilisateur B2C</span><span class="sxs-lookup"><span data-stu-id="f275a-205">B2C user flow administrator</span></span> | <span data-ttu-id="f275a-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="f275a-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="f275a-207">Administrateur de l’attribut de workflow de l’utilisateur B2C</span><span class="sxs-lookup"><span data-stu-id="f275a-207">B2C user flow attribute administrator</span></span> | <span data-ttu-id="f275a-208">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="f275a-208">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="f275a-209">Administrateur de facturation</span><span class="sxs-lookup"><span data-stu-id="f275a-209">Billing administrator</span></span> | <span data-ttu-id="f275a-210">69ff516a-B57D-4697-A429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="f275a-210">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="f275a-211">Administrateur d’application cloud</span><span class="sxs-lookup"><span data-stu-id="f275a-211">Cloud application administrator</span></span> | <span data-ttu-id="f275a-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="f275a-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="f275a-213">Administrateur d’appareil cloud</span><span class="sxs-lookup"><span data-stu-id="f275a-213">Cloud device administrator</span></span> | <span data-ttu-id="f275a-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="f275a-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="f275a-215">Administrateur de conformité</span><span class="sxs-lookup"><span data-stu-id="f275a-215">Compliance administrator</span></span> | <span data-ttu-id="f275a-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="f275a-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="f275a-217">Administrateur des données de conformité</span><span class="sxs-lookup"><span data-stu-id="f275a-217">Compliance data administrator</span></span> | <span data-ttu-id="f275a-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="f275a-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="f275a-219">Administrateur de l’accès conditionnel</span><span class="sxs-lookup"><span data-stu-id="f275a-219">Conditional Access administrator</span></span> | <span data-ttu-id="f275a-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="f275a-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="f275a-221">Approbateur d’accès au référentiel sécurisé client</span><span class="sxs-lookup"><span data-stu-id="f275a-221">Customer LockBox access approver</span></span> | <span data-ttu-id="f275a-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="f275a-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="f275a-223">Administrateur Desktop Analytics</span><span class="sxs-lookup"><span data-stu-id="f275a-223">Desktop Analytics administrator</span></span> | <span data-ttu-id="f275a-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="f275a-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="f275a-225">Lecteurs d’annuaires</span><span class="sxs-lookup"><span data-stu-id="f275a-225">Directory readers</span></span> | <span data-ttu-id="f275a-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="f275a-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="f275a-227">Administrateur Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="f275a-227">Dynamics 365 administrator</span></span> | <span data-ttu-id="f275a-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="f275a-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="f275a-229">Administrateur Exchange</span><span class="sxs-lookup"><span data-stu-id="f275a-229">Exchange administrator</span></span> | <span data-ttu-id="f275a-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="f275a-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="f275a-231">IdentityAdministrateur du fournisseur externe</span><span class="sxs-lookup"><span data-stu-id="f275a-231">External Identity Provider administrator</span></span> | <span data-ttu-id="f275a-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="f275a-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="f275a-233">Administrateur général</span><span class="sxs-lookup"><span data-stu-id="f275a-233">Global administrator</span></span> | <span data-ttu-id="f275a-234">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="f275a-234">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="f275a-235">Lecteur général</span><span class="sxs-lookup"><span data-stu-id="f275a-235">Global reader</span></span> | <span data-ttu-id="f275a-236">f6903b21-6aba-4124-B44C-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="f275a-236">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="f275a-237">Administrateur de groupes</span><span class="sxs-lookup"><span data-stu-id="f275a-237">Groups administrator</span></span> | <span data-ttu-id="f275a-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="f275a-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="f275a-239">Inviteur d’invités</span><span class="sxs-lookup"><span data-stu-id="f275a-239">Guest inviter</span></span> | <span data-ttu-id="f275a-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="f275a-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="f275a-241">Administrateur du support technique</span><span class="sxs-lookup"><span data-stu-id="f275a-241">Helpdesk administrator</span></span> | <span data-ttu-id="f275a-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="f275a-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="f275a-243">Administrateur Intune</span><span class="sxs-lookup"><span data-stu-id="f275a-243">Intune administrator</span></span> | <span data-ttu-id="f275a-244">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="f275a-244">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="f275a-245">Administrateur Kaizala</span><span class="sxs-lookup"><span data-stu-id="f275a-245">Kaizala administrator</span></span> | <span data-ttu-id="f275a-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="f275a-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="f275a-247">Administrateur de licence</span><span class="sxs-lookup"><span data-stu-id="f275a-247">License administrator</span></span> | <span data-ttu-id="f275a-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="f275a-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="f275a-249">Lecteur de confidentialité du Centre de messages</span><span class="sxs-lookup"><span data-stu-id="f275a-249">Message center privacy reader</span></span> | <span data-ttu-id="f275a-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="f275a-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="f275a-251">Lecteur du Centre de messages</span><span class="sxs-lookup"><span data-stu-id="f275a-251">Message center reader</span></span> | <span data-ttu-id="f275a-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="f275a-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="f275a-253">Administrateur d’applications Office</span><span class="sxs-lookup"><span data-stu-id="f275a-253">Office apps administrator</span></span> | <span data-ttu-id="f275a-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="f275a-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="f275a-255">Administrateur de mots de passe</span><span class="sxs-lookup"><span data-stu-id="f275a-255">Password administrator</span></span> | <span data-ttu-id="f275a-256">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="f275a-256">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="f275a-257">Administrateur Power BI</span><span class="sxs-lookup"><span data-stu-id="f275a-257">Power BI administrator</span></span> | <span data-ttu-id="f275a-258">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="f275a-258">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="f275a-259">Administrateur de plateforme Power</span><span class="sxs-lookup"><span data-stu-id="f275a-259">Power platform administrator</span></span> | <span data-ttu-id="f275a-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="f275a-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="f275a-261">Administrateur d’authentification privilégié</span><span class="sxs-lookup"><span data-stu-id="f275a-261">Privileged authentication administrator</span></span> | <span data-ttu-id="f275a-262">0829f731-b46d-419F-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="f275a-262">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="f275a-263">Administrateur de rôle privilégié</span><span class="sxs-lookup"><span data-stu-id="f275a-263">Privileged role administrator</span></span> | <span data-ttu-id="f275a-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="f275a-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="f275a-265">Lecteur de rapports</span><span class="sxs-lookup"><span data-stu-id="f275a-265">Reports reader</span></span> | <span data-ttu-id="f275a-266">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="f275a-266">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="f275a-267">Administrateur de recherche</span><span class="sxs-lookup"><span data-stu-id="f275a-267">Search administrator</span></span> | <span data-ttu-id="f275a-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="f275a-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="f275a-269">Éditeur de recherche</span><span class="sxs-lookup"><span data-stu-id="f275a-269">Search editor</span></span> | <span data-ttu-id="f275a-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="f275a-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="f275a-271">Administrateur de sécurité</span><span class="sxs-lookup"><span data-stu-id="f275a-271">Security administrator</span></span> | <span data-ttu-id="f275a-272">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="f275a-272">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="f275a-273">Opérateur de sécurité</span><span class="sxs-lookup"><span data-stu-id="f275a-273">Security operator</span></span> | <span data-ttu-id="f275a-274">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="f275a-274">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="f275a-275">Lecteur de sécurité</span><span class="sxs-lookup"><span data-stu-id="f275a-275">Security reader</span></span> | <span data-ttu-id="f275a-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="f275a-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="f275a-277">Administrateur de support de service</span><span class="sxs-lookup"><span data-stu-id="f275a-277">Service support administrator</span></span> | <span data-ttu-id="f275a-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="f275a-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="f275a-279">Administrateur SharePoint</span><span class="sxs-lookup"><span data-stu-id="f275a-279">SharePoint administrator</span></span> | <span data-ttu-id="f275a-280">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="f275a-280">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="f275a-281">Administrateur Skype Entreprise</span><span class="sxs-lookup"><span data-stu-id="f275a-281">Skype for Business administrator</span></span> | <span data-ttu-id="f275a-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="f275a-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="f275a-283">Administrateur des communications Teams</span><span class="sxs-lookup"><span data-stu-id="f275a-283">Teams Communications Administrator</span></span> | <span data-ttu-id="f275a-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="f275a-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="f275a-285">Ingénieur de support des communications Teams</span><span class="sxs-lookup"><span data-stu-id="f275a-285">Teams Communications Support Engineer</span></span> | <span data-ttu-id="f275a-286">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="f275a-286">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="f275a-287">Spécialiste des communications des équipes</span><span class="sxs-lookup"><span data-stu-id="f275a-287">Teams Communications Specialist</span></span> | <span data-ttu-id="f275a-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="f275a-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="f275a-289">Administrateur du service Teams</span><span class="sxs-lookup"><span data-stu-id="f275a-289">Teams Service Administrator</span></span> | <span data-ttu-id="f275a-290">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="f275a-290">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="f275a-291">Administrateur d’utilisateurs</span><span class="sxs-lookup"><span data-stu-id="f275a-291">User administrator</span></span> | <span data-ttu-id="f275a-292">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="f275a-292">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f275a-293">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f275a-293">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
