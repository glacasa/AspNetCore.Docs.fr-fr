---
title: 'Configurer ASP.NET Core:::no-loc(Identity):::'
author: AdrienTorris
description: 'Comprenez ASP.NET Core :::no-loc(Identity)::: valeurs par défaut et apprenez à configurer des :::no-loc(Identity)::: Propriétés pour utiliser des valeurs personnalisées.'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity-configuration
ms.openlocfilehash: 5c999b426742cf75b1997f5b40223e2dda112901
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160293"
---
# <a name="configure-aspnet-core-no-locidentity"></a>Configurer ASP.NET Core:::no-loc(Identity):::

ASP.NET Core :::no-loc(Identity)::: utilise les valeurs par défaut pour les paramètres tels que la stratégie de mot de passe, le verrouillage et la configuration des cookies. Ces paramètres peuvent être remplacés dans la `Startup` classe.

## <a name="no-locidentity-options"></a>:::no-loc(Identity):::Options

La classe [ :::no-loc(Identity)::: options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) représente les options qui peuvent être utilisées pour configurer le :::no-loc(Identity)::: système. `:::no-loc(Identity):::Options`doit être défini **après** l’appel `Add:::no-loc(Identity):::` de ou de `AddDefault:::no-loc(Identity):::` .

### <a name="claims-no-locidentity"></a>Légitimité:::no-loc(Identity):::

[ :::no-loc(Identity)::: Options. les :::no-loc(Identity)::: revendications](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) spécifient les [ :::no-loc(Identity)::: options de revendications](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) avec les propriétés indiquées dans le tableau suivant.

| Propriété | Description | Default |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | Obtient ou définit le type de revendication utilisé pour une revendication de rôle. | [ClaimTypes. Role](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | Obtient ou définit le type de revendication utilisé pour la revendication de cachet de sécurité. | `AspNet.:::no-loc(Identity):::.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | Obtient ou définit le type de revendication utilisé pour la revendication de l’identificateur d’utilisateur. | [ClaimTypes. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | Obtient ou définit le type de revendication utilisé pour la revendication de nom d’utilisateur. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>Verrouillage.

Le verrouillage est défini dans la méthode [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) :

[!code-csharp[](identity-configuration/sample/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

Le code précédent est basé sur le `Login` :::no-loc(Identity)::: modèle. 

Les options de verrouillage sont définies dans `StartUp.ConfigureServices` :

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

Le code précédent définit les [ :::no-loc(Identity)::: options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) avec les valeurs par défaut.

Une authentification réussie réinitialise le nombre d’échecs de tentatives d’accès et réinitialise l’horloge.

[ :::no-loc(Identity)::: Options. le verrouillage](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) spécifie le [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) avec les propriétés affichées dans le tableau.

| Propriété | Description | Default |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | Détermine si un nouvel utilisateur peut être verrouillé. | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | Durée pendant laquelle un utilisateur est verrouillé lorsqu’un verrouillage se produit. | 5 minutes |
| [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | Nombre d’échecs de tentative d’accès jusqu’à ce qu’un utilisateur soit verrouillé, si le verrouillage est activé. | 5 |

### <a name="password"></a>Mot de passe

Par défaut, :::no-loc(Identity)::: exige que les mots de passe contiennent un caractère majuscule, un caractère minuscule, un chiffre et un caractère non alphanumérique. Les mots de passe doivent comporter au moins six caractères.

Les mots de passe sont configurés avec :

* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions>dans `Startup.ConfigureServices` .
* [ `[StringLength]` attributs](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) de `Password` Propriétés si :::no-loc(Identity)::: est généré automatiquement [au sein de l’application](xref:security/authentication/scaffold-identity). `InputModel``Password`les propriétés se trouvent dans les fichiers suivants :
  * `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
  * `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

[ :::no-loc(Identity)::: Options. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) spécifie le [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) avec les propriétés indiquées dans le tableau.

| Propriété | Description | Default |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Requiert un nombre compris entre 0-9 dans le mot de passe. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Longueur minimale du mot de passe. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Requiert un caractère minuscule dans le mot de passe. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Requiert un caractère non alphanumérique dans le mot de passe. | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | S’applique uniquement à ASP.NET Core 2,0 ou version ultérieure.<br><br> Requiert le nombre de caractères distincts dans le mot de passe. | 1 |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Requiert un caractère majuscule dans le mot de passe. | `true` |

### <a name="sign-in"></a>Connexion

Le code suivant définit les `SignIn` paramètres (aux valeurs par défaut) :

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

[ :::no-loc(Identity)::: Options. Signy](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) spécifie le [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) avec les propriétés affichées dans le tableau.

| Propriété | Description | Default |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | Requiert un e-mail confirmé pour la connexion. | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | Nécessite un numéro de téléphone confirmé pour la connexion. | `false` |

### <a name="tokens"></a>Jetons

[ :::no-loc(Identity)::: Options. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) spécifie le [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) avec les propriétés affichées dans le tableau.

| Propriété | Description |
| -------- | ----------- |
| [AuthenticatorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider) | Obtient ou définit le `AuthenticatorTokenProvider` utilisé pour valider les connexions à deux facteurs avec un authentificateur. |
| [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider) | Obtient ou définit le `ChangeEmailTokenProvider` utilisé pour générer les jetons utilisés dans les e-mails de confirmation de modification de courrier électronique. |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) | Obtient ou définit le `ChangePhoneNumberTokenProvider` utilisé pour générer les jetons utilisés lors de la modification des numéros de téléphone. |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) | Obtient ou définit le fournisseur de jetons utilisé pour générer les jetons utilisés dans les e-mails de confirmation de compte. |
| [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider) | Obtient ou définit le [IUserTwoFactorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) utilisé pour générer les jetons utilisés dans les courriers électroniques de réinitialisation de mot de passe. |
| [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap) | Utilisé pour construire un [fournisseur de jetons utilisateur](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) avec la clé utilisée comme nom du fournisseur. |

### <a name="user"></a>Utilisateur

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[ :::no-loc(Identity)::: Options. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) spécifie l' [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) avec les propriétés affichées dans le tableau.

| Propriété | Description | Default |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | Caractères autorisés dans le nom d’utilisateur. | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | Nécessite que chaque utilisateur dispose d’un e-mail unique. | `false` |

### <a name="cookie-settings"></a>Paramètres de cookie

Configurez le cookie de l’application dans `Startup.ConfigureServices` . [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_:::no-loc(Identity):::ServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) doit être appelé **après** l’appel `Add:::no-loc(Identity):::` de ou de `AddDefault:::no-loc(Identity):::` .

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

Pour plus d’informations, consultez [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).

## <a name="password-hasher-options"></a>Options de hachage de mot de passe

<xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions>Obtient et définit les options de hachage de mot de passe.

| Option | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions.CompatibilityMode> | Mode de compatibilité utilisé lors du hachage de nouveaux mots de passe. La valeur par défaut est <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherCompatibilityMode.:::no-loc(Identity):::V3>. Le premier octet d’un mot de passe haché, appelé *marqueur de format*, spécifie la version de l’algorithme de hachage utilisé pour hacher le mot de passe. Lors de la vérification d’un mot de passe par rapport à un hachage, la <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasher`1.VerifyHashedPassword*> méthode sélectionne l’algorithme approprié en fonction du premier octet. Un client est en mesure de s’authentifier, quelle que soit la version de l’algorithme utilisée pour hacher le mot de passe. La définition du mode de compatibilité affecte le hachage de *nouveaux mots de passe*. |
| <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions.IterationCount> | Nombre d’itérations utilisées pour hacher les mots de passe à l’aide de PBKDF2. Cette valeur est utilisée uniquement lorsque <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions.CompatibilityMode> a la valeur <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherCompatibilityMode.:::no-loc(Identity):::V3> . La valeur doit être un entier positif et la valeur par défaut est `10000` . |

Dans l’exemple suivant, <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions.IterationCount> a la valeur `12000` dans `Startup.ConfigureServices` :

```csharp
// using Microsoft.AspNetCore.:::no-loc(Identity):::;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```

## <a name="globally-require-all-users-to-be-authenticated"></a>Exiger que tous les utilisateurs soient authentifiés globalement

[!INCLUDE[](~/includes/requireAuth.md)]