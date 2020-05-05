---
title: Fournisseurs de stratégies d’autorisation personnalisés dans ASP.NET Core
author: mjrousos
description: Découvrez comment utiliser un IAuthorizationPolicyProvider personnalisé dans une application ASP.NET Core pour générer dynamiquement des stratégies d’autorisation.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 1db78e5b2cea964471e4eea090f713f6af5f4740
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777538"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Fournisseurs de stratégies d’autorisation personnalisés utilisant IAuthorizationPolicyProvider dans ASP.NET Core 

Par [Mike Rousos](https://github.com/mjrousos)

En général, lors de l’utilisation de l' [autorisation basée sur](xref:security/authorization/policies)la `AuthorizationOptions.AddPolicy` stratégie, les stratégies sont inscrites en appelant dans le cadre de la configuration du service d’autorisation. Dans certains scénarios, il n’est pas possible (ou souhaitable) d’inscrire toutes les stratégies d’autorisation de cette manière. Dans ce cas, vous pouvez utiliser un personnalisé `IAuthorizationPolicyProvider` pour contrôler la façon dont les stratégies d’autorisation sont fournies.

Voici quelques exemples de scénarios dans lesquels un [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) personnalisé peut être utile :

* Utilisation d’un service externe pour fournir une évaluation de la stratégie.
* À l’aide d’un large éventail de stratégies (pour différents nombres de pièces ou âges, par exemple), il n’est pas judicieux d’ajouter chaque stratégie `AuthorizationOptions.AddPolicy` d’autorisation avec un appel.
* Création de stratégies au moment de l’exécution en fonction des informations contenues dans une source de données externe (par exemple, une base de données) ou détermination dynamique des exigences d’autorisation par le biais d’un autre mécanisme.

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) à partir du [référentiel GitHub AspNetCore](https://github.com/dotnet/AspNetCore). Téléchargez le fichier ZIP du référentiel dotnet/AspNetCore. Décompressez le fichier. Accédez au dossier de projet *src/Security/Samples/CustomPolicyProvider* .

## <a name="customize-policy-retrieval"></a>Personnaliser la récupération de stratégie

Les applications ASP.NET Core utilisent une implémentation de `IAuthorizationPolicyProvider` l’interface pour récupérer des stratégies d’autorisation. Par défaut, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) est inscrit et utilisé. `DefaultAuthorizationPolicyProvider`retourne des stratégies à `AuthorizationOptions` partir du fourni `IServiceCollection.AddAuthorization` dans un appel.

Personnalisez ce comportement en inscrivant `IAuthorizationPolicyProvider` une implémentation différente dans le conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) de l’application. 

L' `IAuthorizationPolicyProvider` interface contient trois API :

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) retourne une stratégie d’autorisation pour un nom donné.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) retourne la stratégie d’autorisation par défaut (la stratégie `[Authorize]` utilisée pour les attributs sans une stratégie spécifiée). 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) retourne la stratégie d’autorisation de secours (la stratégie utilisée par l’intergiciel (middleware) d’autorisation quand aucune stratégie n’est spécifiée). 

En implémentant ces API, vous pouvez personnaliser la façon dont les stratégies d’autorisation sont fournies.

## <a name="parameterized-authorize-attribute-example"></a>Exemple d’attribut Authorize paramétrable

Un scénario où `IAuthorizationPolicyProvider` est utile est l’activation `[Authorize]` d’attributs personnalisés dont les spécifications dépendent d’un paramètre. Par exemple, dans la documentation [d’autorisation basée sur des stratégies](xref:security/authorization/policies) , une stratégie basée sur l’âge (« AtLeast21 ») a été utilisée comme exemple. Si des actions de contrôleur différentes dans une application doivent être mises à la disposition des utilisateurs de *différents* âges, il peut être utile de disposer de nombreuses stratégies d’âge différentes. Au lieu d’inscrire toutes les autres stratégies basées sur l’âge nécessaires à `AuthorizationOptions`l’application, vous pouvez générer les stratégies de manière dynamique avec un `IAuthorizationPolicyProvider`personnalisé. Pour faciliter l’utilisation des stratégies, vous pouvez annoter des actions avec un attribut `[MinimumAgeAuthorize(20)]`d’autorisation personnalisé comme.

## <a name="custom-authorization-attributes"></a>Attributs d’autorisation personnalisés

Les stratégies d’autorisation sont identifiées par leur nom. La procédure `MinimumAgeAuthorizeAttribute` personnalisée décrite précédemment doit mapper des arguments dans une chaîne qui peut être utilisée pour récupérer la stratégie d’autorisation correspondante. Pour ce faire, vous pouvez dériver `AuthorizeAttribute` de et rendre `Age` la propriété encapsulée dans la `AuthorizeAttribute.Policy` propriété.

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

Ce type d’attribut a `Policy` une chaîne basée sur le préfixe codé en`"MinimumAge"`dur () et un entier passé par le biais du constructeur.

Vous pouvez l’appliquer aux actions de la même façon que d' `Authorize` autres attributs, sauf qu’il prend un entier comme paramètre.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>IAuthorizationPolicyProvider personnalisé

Le personnalisé `MinimumAgeAuthorizeAttribute` simplifie la demande de stratégies d’autorisation pour tout âge minimal souhaité. Le prochain problème à résoudre consiste à s’assurer que les stratégies d’autorisation sont disponibles pour tous ces âges. C’est là qu' `IAuthorizationPolicyProvider` un est utile.

Lorsque vous `MinimumAgeAuthorizationAttribute`utilisez, les noms de stratégie d’autorisation suivent `"MinimumAge" + Age`le modèle, de `IAuthorizationPolicyProvider` sorte que le personnalisé doit générer des stratégies d’autorisation en :

* Analyse de l’âge à partir du nom de la stratégie.
* Utilisation `AuthorizationPolicyBuilder` de pour créer un nouveau`AuthorizationPolicy`
* Dans cet exemple et les exemples suivants, on suppose que l’utilisateur est authentifié via un cookie. `AuthorizationPolicyBuilder` Doit être construit avec au moins un nom de schéma d’autorisation, ou toujours correctement. Dans le cas contraire, il n’y a pas d’informations sur la façon de fournir une stimulation à l’utilisateur et une exception sera levée.
* Ajout de spécifications à la stratégie en fonction de l' `AuthorizationPolicyBuilder.AddRequirements`âge avec. Dans d’autres scénarios, vous pouvez `RequireClaim`utiliser `RequireRole`, ou `RequireUserName` à la place.

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

## <a name="multiple-authorization-policy-providers"></a>Plusieurs fournisseurs de stratégies d’autorisation

Lorsque vous utilisez `IAuthorizationPolicyProvider` des implémentations personnalisées, gardez à l’esprit que ASP.net Core utilise `IAuthorizationPolicyProvider`uniquement une instance de. Si un fournisseur personnalisé n’est pas en mesure de fournir des stratégies d’autorisation pour tous les noms de stratégie qui seront utilisés, il doit s’en remettre à un fournisseur de sauvegarde. 

Par exemple, considérez une application qui nécessite à la fois des stratégies d’âge personnalisées et une récupération de stratégie basée sur les rôles plus classique. Une telle application peut utiliser un fournisseur de stratégie d’autorisation personnalisé qui :

* Tentatives d’analyse des noms de stratégie. 
* Appelle un fournisseur de stratégie différent (par `DefaultAuthorizationPolicyProvider`exemple) si le nom de la stratégie ne contient pas d’ancienneté.

L’exemple `IAuthorizationPolicyProvider` d’implémentation illustré ci-dessus peut être mis `DefaultAuthorizationPolicyProvider` à jour pour utiliser le en créant un fournisseur de stratégie de sauvegarde dans son constructeur (à utiliser si le nom de la stratégie ne correspond pas au modèle attendu « minimum » + Age).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

La `GetPolicyAsync` méthode peut ensuite être mise à jour pour utiliser `BackupPolicyProvider` le au lieu de retourner la valeur NULL :

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Stratégie par défaut

En plus de fournir des stratégies d’autorisation nommées `IAuthorizationPolicyProvider` , un personnalisé `GetDefaultPolicyAsync` doit implémenter pour fournir une `[Authorize]` stratégie d’autorisation pour les attributs sans nom de stratégie spécifié.

Dans de nombreux cas, cet attribut d’autorisation nécessite uniquement un utilisateur authentifié, ce qui vous permet d’effectuer la stratégie nécessaire avec `RequireAuthenticatedUser`un appel à :

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Comme pour tous les aspects d’un `IAuthorizationPolicyProvider`personnalisé, vous pouvez le personnaliser en fonction des besoins. Dans certains cas, il peut être souhaitable de récupérer la stratégie par défaut à partir `IAuthorizationPolicyProvider`d’un secours.

## <a name="fallback-policy"></a>Stratégie de secours

Un personnalisé `IAuthorizationPolicyProvider` peut éventuellement implémenter `GetFallbackPolicyAsync` pour fournir une stratégie utilisée lors de la [combinaison de stratégies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) et quand aucune stratégie n’est spécifiée. Si `GetFallbackPolicyAsync` retourne une stratégie non null, la stratégie retournée est utilisée par l’intergiciel (middleware) d’autorisation quand aucune stratégie n’est spécifiée pour la demande.

Si aucune stratégie de secours n’est requise, le fournisseur `null` peut retourner ou retarder le fournisseur de secours :

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Utiliser un IAuthorizationPolicyProvider personnalisé

Pour utiliser des stratégies personnalisées `IAuthorizationPolicyProvider`à partir d’un, vous devez :

* Inscrire les types `AuthorizationHandler` appropriés avec l’injection de dépendances (décrite dans [autorisation basée sur la stratégie](xref:security/authorization/policies#authorization-handlers)), comme avec tous les scénarios d’autorisation basés sur des stratégies.
* Inscrivez le type `IAuthorizationPolicyProvider` personnalisé dans la collection de services d’injection de dépendances `Startup.ConfigureServices`de l’application (dans) pour remplacer le fournisseur de stratégie par défaut.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Un exemple personnalisé `IAuthorizationPolicyProvider` complet est disponible dans le [référentiel GitHub dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).
