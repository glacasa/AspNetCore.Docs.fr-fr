---
title: Fournisseurs de politiques d’autorisation personnalisées dans ASP.NET Core
author: mjrousos
description: Découvrez comment utiliser un IAuthorizationPolicyProvider personnalisé dans une application ASP.NET Core pour générer dynamiquement des stratégies d’autorisation.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2c67e25ff73bc8c3a5f3af4730a509b2385fc1cf
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661772"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="d08b9-103">Fournisseurs de politiques d’autorisation personnalisées utilisant IAuthorizationPolicyProvider dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d08b9-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="d08b9-104">Par [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="d08b9-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="d08b9-105">Typiquement, lorsque vous utilisez l’autorisation `AuthorizationOptions.AddPolicy` fondée sur la [politique,](xref:security/authorization/policies)les polices sont enregistrées en appelant dans le cadre de la configuration du service d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="d08b9-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="d08b9-106">Dans certains scénarios, il peut ne pas être possible (ou souhaitable) d’enregistrer toutes les politiques d’autorisation de cette façon.</span><span class="sxs-lookup"><span data-stu-id="d08b9-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="d08b9-107">Dans ces cas, vous `IAuthorizationPolicyProvider` pouvez utiliser une coutume pour contrôler la façon dont les stratégies d’autorisation sont fournies.</span><span class="sxs-lookup"><span data-stu-id="d08b9-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="d08b9-108">Voici quelques exemples de scénarios où un [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) personnalisé peut être utile :</span><span class="sxs-lookup"><span data-stu-id="d08b9-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="d08b9-109">Utilisation d’un service externe pour fournir une évaluation des politiques.</span><span class="sxs-lookup"><span data-stu-id="d08b9-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="d08b9-110">En utilisant un large éventail de politiques (pour différents numéros de pièce ou âges, `AuthorizationOptions.AddPolicy` par exemple), il n’est donc pas logique d’ajouter chaque politique d’autorisation individuelle avec un appel.</span><span class="sxs-lookup"><span data-stu-id="d08b9-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="d08b9-111">Créer des stratégies à l’exécution en fonction de l’information d’une source de données externe (comme une base de données) ou déterminer les exigences d’autorisation de manière dynamique par le biais d’un autre mécanisme.</span><span class="sxs-lookup"><span data-stu-id="d08b9-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="d08b9-112">Afficher ou télécharger le [code d’échantillon](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) du [référentiel AspNetCore GitHub](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="d08b9-112">[View or download sample code](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="d08b9-113">Téléchargez le fichier zip de dépôt dotnet/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="d08b9-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="d08b9-114">Décompressez le dossier.</span><span class="sxs-lookup"><span data-stu-id="d08b9-114">Unzip the file.</span></span> <span data-ttu-id="d08b9-115">Naviguez vers le dossier du projet *src/Security/samples/CustomPolicyProvider.*</span><span class="sxs-lookup"><span data-stu-id="d08b9-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="d08b9-116">Personnaliser la récupération des politiques</span><span class="sxs-lookup"><span data-stu-id="d08b9-116">Customize policy retrieval</span></span>

<span data-ttu-id="d08b9-117">ASP.NET les applications Core utilisent une `IAuthorizationPolicyProvider` implémentation de l’interface pour récupérer les stratégies d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="d08b9-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="d08b9-118">Par défaut, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) est enregistré et utilisé.</span><span class="sxs-lookup"><span data-stu-id="d08b9-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="d08b9-119">`DefaultAuthorizationPolicyProvider`renvoie les `AuthorizationOptions` politiques `IServiceCollection.AddAuthorization` fournies par un appel.</span><span class="sxs-lookup"><span data-stu-id="d08b9-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="d08b9-120">Personnalisez ce comportement `IAuthorizationPolicyProvider` en enregistrant une implémentation différente dans le conteneur [d’injection](xref:fundamentals/dependency-injection) de dépendance de l’application.</span><span class="sxs-lookup"><span data-stu-id="d08b9-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="d08b9-121">L’interface `IAuthorizationPolicyProvider` contient trois API :</span><span class="sxs-lookup"><span data-stu-id="d08b9-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="d08b9-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) retourne une politique d’autorisation pour un prénom.</span><span class="sxs-lookup"><span data-stu-id="d08b9-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="d08b9-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) retourne la politique d’autorisation `[Authorize]` par défaut (la stratégie utilisée pour les attributs sans une stratégie spécifiée).</span><span class="sxs-lookup"><span data-stu-id="d08b9-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="d08b9-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) retourne la politique d’autorisation de repli (la politique utilisée par le Middleware d’autorisation lorsqu’aucune politique n’est spécifiée).</span><span class="sxs-lookup"><span data-stu-id="d08b9-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="d08b9-125">En mettant en œuvre ces API, vous pouvez personnaliser la façon dont les stratégies d’autorisation sont fournies.</span><span class="sxs-lookup"><span data-stu-id="d08b9-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="d08b9-126">Paramétrisé autoriser l’exemple d’attribut</span><span class="sxs-lookup"><span data-stu-id="d08b9-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="d08b9-127">Un scénario `IAuthorizationPolicyProvider` où il `[Authorize]` est utile est d’activer des attributs personnalisés dont les exigences dépendent d’un paramètre.</span><span class="sxs-lookup"><span data-stu-id="d08b9-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="d08b9-128">Par exemple, dans la documentation [d’autorisation fondée sur](xref:security/authorization/policies) les politiques, une politique fondée sur l’âge (« AtLeast21 ») a été utilisée comme échantillon.</span><span class="sxs-lookup"><span data-stu-id="d08b9-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="d08b9-129">Si différentes actions de contrôleur dans une application doivent être mises à la disposition des utilisateurs de *différents* âges, il pourrait être utile d’avoir de nombreuses politiques basées sur l’âge.</span><span class="sxs-lookup"><span data-stu-id="d08b9-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="d08b9-130">Au lieu d’enregistrer toutes les différentes stratégies basées `AuthorizationOptions`sur l’âge dont l’application aura besoin, vous pouvez générer les politiques dynamiquement avec une coutume `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="d08b9-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="d08b9-131">Pour faciliter l’utilisation des stratégies, vous pouvez annoter des actions avec un attribut d’autorisation personnalisé comme `[MinimumAgeAuthorize(20)]`.</span><span class="sxs-lookup"><span data-stu-id="d08b9-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="d08b9-132">Attributs d’autorisation personnalisée</span><span class="sxs-lookup"><span data-stu-id="d08b9-132">Custom Authorization attributes</span></span>

<span data-ttu-id="d08b9-133">Les politiques d’autorisation sont identifiées par leurs noms.</span><span class="sxs-lookup"><span data-stu-id="d08b9-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="d08b9-134">La `MinimumAgeAuthorizeAttribute` coutume décrite précédemment doit cartographier les arguments en une chaîne qui peut être utilisée pour récupérer la stratégie d’autorisation correspondante.</span><span class="sxs-lookup"><span data-stu-id="d08b9-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="d08b9-135">Vous pouvez le faire en `AuthorizeAttribute` dérivant et `AuthorizeAttribute.Policy` en faisant envelopper la propriété de propriété. `Age`</span><span class="sxs-lookup"><span data-stu-id="d08b9-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

<span data-ttu-id="d08b9-136">Ce type d’attribut a une `Policy` chaîne basée`"MinimumAge"`sur le préfixe codé dur ( ) et un intégrateur passé par l’intermédiaire du constructeur.</span><span class="sxs-lookup"><span data-stu-id="d08b9-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="d08b9-137">Vous pouvez l’appliquer aux actions `Authorize` de la même manière que les autres attributs, sauf qu’il prend un intégrer comme un paramètre.</span><span class="sxs-lookup"><span data-stu-id="d08b9-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="d08b9-138">Custom IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="d08b9-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="d08b9-139">La `MinimumAgeAuthorizeAttribute` coutume facilite la demande de polices d’autorisation pour tout âge minimum désiré.</span><span class="sxs-lookup"><span data-stu-id="d08b9-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="d08b9-140">Le prochain problème à résoudre est de s’assurer que les politiques d’autorisation sont disponibles pour tous ces âges différents.</span><span class="sxs-lookup"><span data-stu-id="d08b9-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="d08b9-141">C’est `IAuthorizationPolicyProvider` là qu’un est utile.</span><span class="sxs-lookup"><span data-stu-id="d08b9-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="d08b9-142">Lors `MinimumAgeAuthorizationAttribute`de l’utilisation , les `"MinimumAge" + Age`noms de `IAuthorizationPolicyProvider` la politique d’autorisation suivra le modèle , de sorte que la coutume doit générer des politiques d’autorisation par:</span><span class="sxs-lookup"><span data-stu-id="d08b9-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="d08b9-143">Analyser l’âge du nom de la police.</span><span class="sxs-lookup"><span data-stu-id="d08b9-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="d08b9-144">Utilisation `AuthorizationPolicyBuilder` pour créer une nouvelle`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="d08b9-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="d08b9-145">Dans ce cas et les exemples suivants, on suppose que l’utilisateur est authentifié via un cookie.</span><span class="sxs-lookup"><span data-stu-id="d08b9-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="d08b9-146">Le `AuthorizationPolicyBuilder` devrait être construit avec au moins un nom de régime d’autorisation ou toujours réussir.</span><span class="sxs-lookup"><span data-stu-id="d08b9-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="d08b9-147">Sinon, il n’y a aucune information sur la façon de fournir un défi à l’utilisateur et une exception sera lancée.</span><span class="sxs-lookup"><span data-stu-id="d08b9-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="d08b9-148">Ajout d’exigences à la `AuthorizationPolicyBuilder.AddRequirements`police en fonction de l’âge avec .</span><span class="sxs-lookup"><span data-stu-id="d08b9-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="d08b9-149">Dans d’autres scénarios, `RequireClaim` `RequireRole`vous `RequireUserName` pouvez utiliser, , ou à la place.</span><span class="sxs-lookup"><span data-stu-id="d08b9-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="d08b9-150">Fournisseurs de politiques d’autorisation multiples</span><span class="sxs-lookup"><span data-stu-id="d08b9-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="d08b9-151">Lors de `IAuthorizationPolicyProvider` l’utilisation des implémentations personnalisées, `IAuthorizationPolicyProvider`gardez à l’esprit que ASP.NET Core n’utilise qu’une seule instance de .</span><span class="sxs-lookup"><span data-stu-id="d08b9-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="d08b9-152">Si un fournisseur personnalisé n’est pas en mesure de fournir des stratégies d’autorisation pour tous les noms de police qui seront utilisés, il doit s’en remettre à un fournisseur de sauvegarde.</span><span class="sxs-lookup"><span data-stu-id="d08b9-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="d08b9-153">Par exemple, envisagez une application qui a besoin à la fois de politiques d’âge coutumière et d’une récupération plus traditionnelle des politiques fondées sur des rôles.</span><span class="sxs-lookup"><span data-stu-id="d08b9-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="d08b9-154">Une telle application pourrait utiliser un fournisseur de stratégie d’autorisation personnalisé qui :</span><span class="sxs-lookup"><span data-stu-id="d08b9-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="d08b9-155">Tentatives d’analyser les noms des politiques.</span><span class="sxs-lookup"><span data-stu-id="d08b9-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="d08b9-156">Appels à un autre `DefaultAuthorizationPolicyProvider`fournisseur de police (comme ) si le nom de la police ne contient pas d’âge.</span><span class="sxs-lookup"><span data-stu-id="d08b9-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="d08b9-157">L’exemple `IAuthorizationPolicyProvider` de mise en œuvre ci-dessus peut être mis à jour pour utiliser le `DefaultAuthorizationPolicyProvider` en créant un fournisseur de stratégie de sauvegarde dans son constructeur (à utiliser au cas où le nom de la stratégie ne correspond pas à son modèle prévu de «MinimumAge» - âge).</span><span class="sxs-lookup"><span data-stu-id="d08b9-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="d08b9-158">Ensuite, `GetPolicyAsync` la méthode peut être `BackupPolicyProvider` mise à jour pour utiliser le au lieu de retourner nul:</span><span class="sxs-lookup"><span data-stu-id="d08b9-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="d08b9-159">Stratégie par défaut</span><span class="sxs-lookup"><span data-stu-id="d08b9-159">Default policy</span></span>

<span data-ttu-id="d08b9-160">En plus de fournir des `IAuthorizationPolicyProvider` politiques d’autorisation nommées, une coutume doit être mise en œuvre `GetDefaultPolicyAsync` pour fournir une politique d’autorisation pour `[Authorize]` les attributs sans nom de stratégie spécifié.</span><span class="sxs-lookup"><span data-stu-id="d08b9-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="d08b9-161">Dans de nombreux cas, cet attribut d’autorisation ne nécessite qu’un `RequireAuthenticatedUser`utilisateur authentifié, de sorte que vous pouvez faire la politique nécessaire avec un appel à :</span><span class="sxs-lookup"><span data-stu-id="d08b9-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="d08b9-162">Comme avec tous les `IAuthorizationPolicyProvider`aspects d’une coutume, vous pouvez personnaliser cela, au besoin.</span><span class="sxs-lookup"><span data-stu-id="d08b9-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="d08b9-163">Dans certains cas, il peut être souhaitable de `IAuthorizationPolicyProvider`récupérer la politique par défaut d’un repli .</span><span class="sxs-lookup"><span data-stu-id="d08b9-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="d08b9-164">Politique de repli</span><span class="sxs-lookup"><span data-stu-id="d08b9-164">Fallback policy</span></span>

<span data-ttu-id="d08b9-165">Une `IAuthorizationPolicyProvider` coutume peut `GetFallbackPolicyAsync` implémenter en option pour fournir une stratégie qui est utilisée lors de [la combinaison des stratégies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) et lorsqu’aucune stratégie n’est spécifiée.</span><span class="sxs-lookup"><span data-stu-id="d08b9-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="d08b9-166">Si `GetFallbackPolicyAsync` elle renvoie une police non nulle, la police retournée est utilisée par le Middleware d’autorisation lorsqu’aucune police n’est spécifiée pour la demande.</span><span class="sxs-lookup"><span data-stu-id="d08b9-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="d08b9-167">Si aucune politique de repli n’est requise, le fournisseur peut retourner `null` ou s’en remettre au fournisseur de repli :</span><span class="sxs-lookup"><span data-stu-id="d08b9-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="d08b9-168">Utilisez un IAuthorizationPolicyProvider personnalisé</span><span class="sxs-lookup"><span data-stu-id="d08b9-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="d08b9-169">Pour utiliser les `IAuthorizationPolicyProvider`stratégies personnalisées à partir d’un , vous devez:</span><span class="sxs-lookup"><span data-stu-id="d08b9-169">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="d08b9-170">Enregistrez `AuthorizationHandler` les types appropriés avec injection de dépendance (décrit dans [l’autorisation fondée sur la politique),](xref:security/authorization/policies#authorization-handlers)comme avec tous les scénarios d’autorisation fondés sur la politique.</span><span class="sxs-lookup"><span data-stu-id="d08b9-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="d08b9-171">Enregistrez `IAuthorizationPolicyProvider` le type personnalisé dans la collecte `Startup.ConfigureServices`de services d’injection de dépendance de l’application (dans ) pour remplacer le fournisseur de stratégie par défaut.</span><span class="sxs-lookup"><span data-stu-id="d08b9-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="d08b9-172">Un échantillon `IAuthorizationPolicyProvider` complet personnalisé est disponible dans le [référentiel GitHub dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="d08b9-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span></span>
