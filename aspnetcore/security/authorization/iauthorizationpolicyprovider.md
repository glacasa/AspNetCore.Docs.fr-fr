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
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Fournisseurs de politiques d’autorisation personnalisées utilisant IAuthorizationPolicyProvider dans ASP.NET Core 

Par [Mike Rousos](https://github.com/mjrousos)

Typiquement, lorsque vous utilisez l’autorisation `AuthorizationOptions.AddPolicy` fondée sur la [politique,](xref:security/authorization/policies)les polices sont enregistrées en appelant dans le cadre de la configuration du service d’autorisation. Dans certains scénarios, il peut ne pas être possible (ou souhaitable) d’enregistrer toutes les politiques d’autorisation de cette façon. Dans ces cas, vous `IAuthorizationPolicyProvider` pouvez utiliser une coutume pour contrôler la façon dont les stratégies d’autorisation sont fournies.

Voici quelques exemples de scénarios où un [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) personnalisé peut être utile :

* Utilisation d’un service externe pour fournir une évaluation des politiques.
* En utilisant un large éventail de politiques (pour différents numéros de pièce ou âges, `AuthorizationOptions.AddPolicy` par exemple), il n’est donc pas logique d’ajouter chaque politique d’autorisation individuelle avec un appel.
* Créer des stratégies à l’exécution en fonction de l’information d’une source de données externe (comme une base de données) ou déterminer les exigences d’autorisation de manière dynamique par le biais d’un autre mécanisme.

Afficher ou télécharger le [code d’échantillon](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) du [référentiel AspNetCore GitHub](https://github.com/dotnet/AspNetCore). Téléchargez le fichier zip de dépôt dotnet/AspNetCore. Décompressez le dossier. Naviguez vers le dossier du projet *src/Security/samples/CustomPolicyProvider.*

## <a name="customize-policy-retrieval"></a>Personnaliser la récupération des politiques

ASP.NET les applications Core utilisent une `IAuthorizationPolicyProvider` implémentation de l’interface pour récupérer les stratégies d’autorisation. Par défaut, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) est enregistré et utilisé. `DefaultAuthorizationPolicyProvider`renvoie les `AuthorizationOptions` politiques `IServiceCollection.AddAuthorization` fournies par un appel.

Personnalisez ce comportement `IAuthorizationPolicyProvider` en enregistrant une implémentation différente dans le conteneur [d’injection](xref:fundamentals/dependency-injection) de dépendance de l’application. 

L’interface `IAuthorizationPolicyProvider` contient trois API :

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) retourne une politique d’autorisation pour un prénom.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) retourne la politique d’autorisation `[Authorize]` par défaut (la stratégie utilisée pour les attributs sans une stratégie spécifiée). 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) retourne la politique d’autorisation de repli (la politique utilisée par le Middleware d’autorisation lorsqu’aucune politique n’est spécifiée). 

En mettant en œuvre ces API, vous pouvez personnaliser la façon dont les stratégies d’autorisation sont fournies.

## <a name="parameterized-authorize-attribute-example"></a>Paramétrisé autoriser l’exemple d’attribut

Un scénario `IAuthorizationPolicyProvider` où il `[Authorize]` est utile est d’activer des attributs personnalisés dont les exigences dépendent d’un paramètre. Par exemple, dans la documentation [d’autorisation fondée sur](xref:security/authorization/policies) les politiques, une politique fondée sur l’âge (« AtLeast21 ») a été utilisée comme échantillon. Si différentes actions de contrôleur dans une application doivent être mises à la disposition des utilisateurs de *différents* âges, il pourrait être utile d’avoir de nombreuses politiques basées sur l’âge. Au lieu d’enregistrer toutes les différentes stratégies basées `AuthorizationOptions`sur l’âge dont l’application aura besoin, vous pouvez générer les politiques dynamiquement avec une coutume `IAuthorizationPolicyProvider`. Pour faciliter l’utilisation des stratégies, vous pouvez annoter des actions avec un attribut d’autorisation personnalisé comme `[MinimumAgeAuthorize(20)]`.

## <a name="custom-authorization-attributes"></a>Attributs d’autorisation personnalisée

Les politiques d’autorisation sont identifiées par leurs noms. La `MinimumAgeAuthorizeAttribute` coutume décrite précédemment doit cartographier les arguments en une chaîne qui peut être utilisée pour récupérer la stratégie d’autorisation correspondante. Vous pouvez le faire en `AuthorizeAttribute` dérivant et `AuthorizeAttribute.Policy` en faisant envelopper la propriété de propriété. `Age`

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

Ce type d’attribut a une `Policy` chaîne basée`"MinimumAge"`sur le préfixe codé dur ( ) et un intégrateur passé par l’intermédiaire du constructeur.

Vous pouvez l’appliquer aux actions `Authorize` de la même manière que les autres attributs, sauf qu’il prend un intégrer comme un paramètre.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Custom IAuthorizationPolicyProvider

La `MinimumAgeAuthorizeAttribute` coutume facilite la demande de polices d’autorisation pour tout âge minimum désiré. Le prochain problème à résoudre est de s’assurer que les politiques d’autorisation sont disponibles pour tous ces âges différents. C’est `IAuthorizationPolicyProvider` là qu’un est utile.

Lors `MinimumAgeAuthorizationAttribute`de l’utilisation , les `"MinimumAge" + Age`noms de `IAuthorizationPolicyProvider` la politique d’autorisation suivra le modèle , de sorte que la coutume doit générer des politiques d’autorisation par:

* Analyser l’âge du nom de la police.
* Utilisation `AuthorizationPolicyBuilder` pour créer une nouvelle`AuthorizationPolicy`
* Dans ce cas et les exemples suivants, on suppose que l’utilisateur est authentifié via un cookie. Le `AuthorizationPolicyBuilder` devrait être construit avec au moins un nom de régime d’autorisation ou toujours réussir. Sinon, il n’y a aucune information sur la façon de fournir un défi à l’utilisateur et une exception sera lancée.
* Ajout d’exigences à la `AuthorizationPolicyBuilder.AddRequirements`police en fonction de l’âge avec . Dans d’autres scénarios, `RequireClaim` `RequireRole`vous `RequireUserName` pouvez utiliser, , ou à la place.

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

## <a name="multiple-authorization-policy-providers"></a>Fournisseurs de politiques d’autorisation multiples

Lors de `IAuthorizationPolicyProvider` l’utilisation des implémentations personnalisées, `IAuthorizationPolicyProvider`gardez à l’esprit que ASP.NET Core n’utilise qu’une seule instance de . Si un fournisseur personnalisé n’est pas en mesure de fournir des stratégies d’autorisation pour tous les noms de police qui seront utilisés, il doit s’en remettre à un fournisseur de sauvegarde. 

Par exemple, envisagez une application qui a besoin à la fois de politiques d’âge coutumière et d’une récupération plus traditionnelle des politiques fondées sur des rôles. Une telle application pourrait utiliser un fournisseur de stratégie d’autorisation personnalisé qui :

* Tentatives d’analyser les noms des politiques. 
* Appels à un autre `DefaultAuthorizationPolicyProvider`fournisseur de police (comme ) si le nom de la police ne contient pas d’âge.

L’exemple `IAuthorizationPolicyProvider` de mise en œuvre ci-dessus peut être mis à jour pour utiliser le `DefaultAuthorizationPolicyProvider` en créant un fournisseur de stratégie de sauvegarde dans son constructeur (à utiliser au cas où le nom de la stratégie ne correspond pas à son modèle prévu de «MinimumAge» - âge).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Ensuite, `GetPolicyAsync` la méthode peut être `BackupPolicyProvider` mise à jour pour utiliser le au lieu de retourner nul:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Stratégie par défaut

En plus de fournir des `IAuthorizationPolicyProvider` politiques d’autorisation nommées, une coutume doit être mise en œuvre `GetDefaultPolicyAsync` pour fournir une politique d’autorisation pour `[Authorize]` les attributs sans nom de stratégie spécifié.

Dans de nombreux cas, cet attribut d’autorisation ne nécessite qu’un `RequireAuthenticatedUser`utilisateur authentifié, de sorte que vous pouvez faire la politique nécessaire avec un appel à :

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Comme avec tous les `IAuthorizationPolicyProvider`aspects d’une coutume, vous pouvez personnaliser cela, au besoin. Dans certains cas, il peut être souhaitable de `IAuthorizationPolicyProvider`récupérer la politique par défaut d’un repli .

## <a name="fallback-policy"></a>Politique de repli

Une `IAuthorizationPolicyProvider` coutume peut `GetFallbackPolicyAsync` implémenter en option pour fournir une stratégie qui est utilisée lors de [la combinaison des stratégies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) et lorsqu’aucune stratégie n’est spécifiée. Si `GetFallbackPolicyAsync` elle renvoie une police non nulle, la police retournée est utilisée par le Middleware d’autorisation lorsqu’aucune police n’est spécifiée pour la demande.

Si aucune politique de repli n’est requise, le fournisseur peut retourner `null` ou s’en remettre au fournisseur de repli :

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Utilisez un IAuthorizationPolicyProvider personnalisé

Pour utiliser les `IAuthorizationPolicyProvider`stratégies personnalisées à partir d’un , vous devez:

* Enregistrez `AuthorizationHandler` les types appropriés avec injection de dépendance (décrit dans [l’autorisation fondée sur la politique),](xref:security/authorization/policies#authorization-handlers)comme avec tous les scénarios d’autorisation fondés sur la politique.
* Enregistrez `IAuthorizationPolicyProvider` le type personnalisé dans la collecte `Startup.ConfigureServices`de services d’injection de dépendance de l’application (dans ) pour remplacer le fournisseur de stratégie par défaut.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Un échantillon `IAuthorizationPolicyProvider` complet personnalisé est disponible dans le [référentiel GitHub dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).
