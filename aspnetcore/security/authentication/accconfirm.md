---
title: Account confirmation and password recovery in ASP.NET Core (Confirmation de compte et récupération de mot de passe dans ASP.NET Core)
author: rick-anderson
description: Découvrez comment créer une application ASP.NET Core avec une confirmation par e-mail et une réinitialisation du mot de passe.
ms.author: riande
ms.date: 03/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: 8d4488b3953a8c87033d3a092b656409a0c6a52d
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724365"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="c5f86-103">Account confirmation and password recovery in ASP.NET Core (Confirmation de compte et récupération de mot de passe dans ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="c5f86-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="c5f86-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)et [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="c5f86-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="c5f86-105">Ce didacticiel montre comment créer une application ASP.NET Core avec une confirmation par e-mail et une réinitialisation du mot de passe.</span><span class="sxs-lookup"><span data-stu-id="c5f86-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="c5f86-106">Ce didacticiel n’est **pas** une rubrique de départ.</span><span class="sxs-lookup"><span data-stu-id="c5f86-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="c5f86-107">Vous devez connaître les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="c5f86-107">You should be familiar with:</span></span>

* [<span data-ttu-id="c5f86-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5f86-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="c5f86-109">Authentification</span><span class="sxs-lookup"><span data-stu-id="c5f86-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="c5f86-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c5f86-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="c5f86-111">Consultez [ce fichier PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) pour obtenir la version ASP.net Core 1,1.</span><span class="sxs-lookup"><span data-stu-id="c5f86-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="c5f86-112">Prérequis</span><span class="sxs-lookup"><span data-stu-id="c5f86-112">Prerequisites</span></span>

[<span data-ttu-id="c5f86-113">.NET Core 3,0 SDK ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="c5f86-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="c5f86-114">Créer et tester une application Web avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="c5f86-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="c5f86-115">Exécutez les commandes suivantes pour créer une application Web avec l’authentification.</span><span class="sxs-lookup"><span data-stu-id="c5f86-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="c5f86-116">Exécutez l’application, sélectionnez le lien **Register** et inscrivez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c5f86-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="c5f86-117">Une fois inscrit, vous êtes redirigé vers la `/Identity/Account/RegisterConfirmation` page vers qui contient un lien pour simuler la confirmation de l’e-mail :</span><span class="sxs-lookup"><span data-stu-id="c5f86-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="c5f86-118">Sélectionnez le `Click here to confirm your account` lien.</span><span class="sxs-lookup"><span data-stu-id="c5f86-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="c5f86-119">Sélectionnez le lien de **connexion** et connectez-vous avec les mêmes informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="c5f86-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="c5f86-120">Sélectionnez le `Hello YourEmail@provider.com!` lien, qui vous redirige vers la `/Identity/Account/Manage/PersonalData` page.</span><span class="sxs-lookup"><span data-stu-id="c5f86-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="c5f86-121">Sélectionnez l’onglet **données personnelles** sur la gauche, puis sélectionnez **supprimer**.</span><span class="sxs-lookup"><span data-stu-id="c5f86-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="c5f86-122">Configurer un fournisseur de messagerie</span><span class="sxs-lookup"><span data-stu-id="c5f86-122">Configure an email provider</span></span>

<span data-ttu-id="c5f86-123">Dans ce didacticiel, [SendGrid](https://sendgrid.com) est utilisé pour envoyer des messages électroniques.</span><span class="sxs-lookup"><span data-stu-id="c5f86-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="c5f86-124">Vous avez besoin d’un compte et d’une clé SendGrid pour envoyer des messages électroniques.</span><span class="sxs-lookup"><span data-stu-id="c5f86-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="c5f86-125">Vous pouvez utiliser d’autres fournisseurs de courrier électronique.</span><span class="sxs-lookup"><span data-stu-id="c5f86-125">You can use other email providers.</span></span> <span data-ttu-id="c5f86-126">Nous vous recommandons d’utiliser SendGrid ou un autre service de messagerie pour envoyer des courriers électroniques.</span><span class="sxs-lookup"><span data-stu-id="c5f86-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="c5f86-127">SMTP est difficile à sécuriser et à configurer correctement.</span><span class="sxs-lookup"><span data-stu-id="c5f86-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="c5f86-128">Le compte SendGrid peut nécessiter l' [Ajout d’un expéditeur](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="c5f86-128">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="c5f86-129">Créez une classe pour extraire la clé de messagerie sécurisée.</span><span class="sxs-lookup"><span data-stu-id="c5f86-129">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="c5f86-130">Pour cet exemple, créez *services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="c5f86-130">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="c5f86-131">Configurer des secrets d’utilisateur SendGrid</span><span class="sxs-lookup"><span data-stu-id="c5f86-131">Configure SendGrid user secrets</span></span>

<span data-ttu-id="c5f86-132">Définissez les `SendGridUser` et `SendGridKey` avec l' [outil de gestion de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="c5f86-132">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="c5f86-133">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c5f86-133">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="c5f86-134">Sur Windows, le gestionnaire de secret stocke les paires clé/valeur dans un *secrets.jssur* le fichier dans le `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` répertoire.</span><span class="sxs-lookup"><span data-stu-id="c5f86-134">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="c5f86-135">Le contenu du *secrets.jssur* le fichier n’est pas chiffré.</span><span class="sxs-lookup"><span data-stu-id="c5f86-135">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="c5f86-136">Le balisage suivant montre l' *secrets.jssur* le fichier.</span><span class="sxs-lookup"><span data-stu-id="c5f86-136">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="c5f86-137">La `SendGridKey` valeur a été supprimée.</span><span class="sxs-lookup"><span data-stu-id="c5f86-137">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="c5f86-138">Pour plus d’informations, consultez le [modèle d’options](xref:fundamentals/configuration/options) et la [configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c5f86-138">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="c5f86-139">Installer SendGrid</span><span class="sxs-lookup"><span data-stu-id="c5f86-139">Install SendGrid</span></span>

<span data-ttu-id="c5f86-140">Ce didacticiel montre comment ajouter des notifications par courrier électronique par le biais de [SendGrid](https://sendgrid.com/), mais vous pouvez envoyer des courriers électroniques à l’aide de SMTP et d’autres mécanismes.</span><span class="sxs-lookup"><span data-stu-id="c5f86-140">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="c5f86-141">Installez le `SendGrid` package NuGet :</span><span class="sxs-lookup"><span data-stu-id="c5f86-141">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c5f86-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5f86-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c5f86-143">À partir de la console du gestionnaire de package, entrez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="c5f86-143">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="c5f86-144">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="c5f86-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c5f86-145">À partir de la console, entrez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="c5f86-145">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="c5f86-146">Pour vous inscrire à un compte SendGrid gratuit, consultez [prise en main de SendGrid](https://sendgrid.com/free/) gratuitement.</span><span class="sxs-lookup"><span data-stu-id="c5f86-146">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="c5f86-147">Implémenter IEmailSender</span><span class="sxs-lookup"><span data-stu-id="c5f86-147">Implement IEmailSender</span></span>

<span data-ttu-id="c5f86-148">Pour implémenter `IEmailSender` , créez *services/EMailSender. cs* avec du code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="c5f86-148">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="c5f86-149">Configurer le démarrage pour la prise en charge de la messagerie</span><span class="sxs-lookup"><span data-stu-id="c5f86-149">Configure startup to support email</span></span>

<span data-ttu-id="c5f86-150">Ajoutez le code suivant à la `ConfigureServices` méthode dans le fichier *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="c5f86-150">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="c5f86-151">Ajoutez `EmailSender` en tant que service temporaire.</span><span class="sxs-lookup"><span data-stu-id="c5f86-151">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="c5f86-152">Inscrivez l' `AuthMessageSenderOptions` instance de configuration.</span><span class="sxs-lookup"><span data-stu-id="c5f86-152">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="c5f86-153">Inscrire, confirmer l’e-mail et réinitialiser le mot de passe</span><span class="sxs-lookup"><span data-stu-id="c5f86-153">Register, confirm email, and reset password</span></span>

<span data-ttu-id="c5f86-154">Exécutez l’application Web et testez le processus de confirmation et de récupération du mot de passe du compte.</span><span class="sxs-lookup"><span data-stu-id="c5f86-154">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="c5f86-155">Exécuter l’application et inscrire un nouvel utilisateur</span><span class="sxs-lookup"><span data-stu-id="c5f86-155">Run the app and register a new user</span></span>
* <span data-ttu-id="c5f86-156">Vérifiez votre adresse de messagerie pour obtenir le lien de confirmation du compte.</span><span class="sxs-lookup"><span data-stu-id="c5f86-156">Check your email for the account confirmation link.</span></span> <span data-ttu-id="c5f86-157">Consultez le [message de débogage](#debug) si vous n’obtenez pas l’e-mail.</span><span class="sxs-lookup"><span data-stu-id="c5f86-157">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="c5f86-158">Cliquez sur le lien pour confirmer votre adresse de messagerie.</span><span class="sxs-lookup"><span data-stu-id="c5f86-158">Click the link to confirm your email.</span></span>
* <span data-ttu-id="c5f86-159">Connectez-vous avec votre adresse de messagerie et votre mot de passe.</span><span class="sxs-lookup"><span data-stu-id="c5f86-159">Sign in with your email and password.</span></span>
* <span data-ttu-id="c5f86-160">Déconnectez-vous.</span><span class="sxs-lookup"><span data-stu-id="c5f86-160">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="c5f86-161">Test de réinitialisation du mot de passe</span><span class="sxs-lookup"><span data-stu-id="c5f86-161">Test password reset</span></span>

* <span data-ttu-id="c5f86-162">Si vous êtes connecté, sélectionnez **déconnexion**.</span><span class="sxs-lookup"><span data-stu-id="c5f86-162">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="c5f86-163">Sélectionnez le lien **se connecter** , puis sélectionnez le lien vous **avez oublié votre mot de passe ?** .</span><span class="sxs-lookup"><span data-stu-id="c5f86-163">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="c5f86-164">Entrez l’adresse de messagerie que vous avez utilisée pour inscrire le compte.</span><span class="sxs-lookup"><span data-stu-id="c5f86-164">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="c5f86-165">Un e-mail contenant un lien pour réinitialiser votre mot de passe est envoyé.</span><span class="sxs-lookup"><span data-stu-id="c5f86-165">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="c5f86-166">Vérifiez votre adresse de messagerie, puis cliquez sur le lien pour réinitialiser votre mot de passe.</span><span class="sxs-lookup"><span data-stu-id="c5f86-166">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="c5f86-167">Une fois que votre mot de passe a été réinitialisé avec succès, vous pouvez vous connecter avec votre adresse de messagerie et votre nouveau mot de passe.</span><span class="sxs-lookup"><span data-stu-id="c5f86-167">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="c5f86-168">Modifier le délai d’expiration de l’e-mail et de l’activité</span><span class="sxs-lookup"><span data-stu-id="c5f86-168">Change email and activity timeout</span></span>

<span data-ttu-id="c5f86-169">Le délai d’inactivité par défaut est de 14 jours.</span><span class="sxs-lookup"><span data-stu-id="c5f86-169">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="c5f86-170">Le code suivant définit le délai d’expiration d’inactivité sur 5 jours :</span><span class="sxs-lookup"><span data-stu-id="c5f86-170">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="c5f86-171">Modifier toutes les durées de vie des jetons de protection des données</span><span class="sxs-lookup"><span data-stu-id="c5f86-171">Change all data protection token lifespans</span></span>

<span data-ttu-id="c5f86-172">Le code suivant modifie le délai d’expiration de tous les jetons de protection des données à 3 heures :</span><span class="sxs-lookup"><span data-stu-id="c5f86-172">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="c5f86-173">Les Identity jetons utilisateur intégrés (voir [AspNetCore/SRC/ Identity /Extensions.Core/SRC/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) ont un [délai d’expiration d’un jour](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="c5f86-173">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="c5f86-174">Modifier la durée de vie du jeton d’e-mail</span><span class="sxs-lookup"><span data-stu-id="c5f86-174">Change the email token lifespan</span></span>

<span data-ttu-id="c5f86-175">La durée de vie des jetons par défaut des [ Identity jetons utilisateur](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) est d' [un jour](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="c5f86-175">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="c5f86-176">Cette section montre comment modifier la durée de vie des jetons de courrier électronique.</span><span class="sxs-lookup"><span data-stu-id="c5f86-176">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="c5f86-177">Ajoutez un [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personnalisé et <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="c5f86-177">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="c5f86-178">Ajoutez le fournisseur personnalisé au conteneur de service :</span><span class="sxs-lookup"><span data-stu-id="c5f86-178">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="c5f86-179">Renvoyer l’e-mail de confirmation</span><span class="sxs-lookup"><span data-stu-id="c5f86-179">Resend email confirmation</span></span>

<span data-ttu-id="c5f86-180">Consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="c5f86-180">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="c5f86-181">Message de débogage</span><span class="sxs-lookup"><span data-stu-id="c5f86-181">Debug email</span></span>

<span data-ttu-id="c5f86-182">Si vous ne parvenez pas à utiliser le courrier électronique :</span><span class="sxs-lookup"><span data-stu-id="c5f86-182">If you can't get email working:</span></span>

* <span data-ttu-id="c5f86-183">Définissez un point d’arrêt dans `EmailSender.Execute` pour vérifier que `SendGridClient.SendEmailAsync` est appelé.</span><span class="sxs-lookup"><span data-stu-id="c5f86-183">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="c5f86-184">Créez une [application console pour envoyer du courrier électronique](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) à l’aide d’un code similaire à `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="c5f86-184">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="c5f86-185">Passez en revue la page [activité de messagerie](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="c5f86-185">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="c5f86-186">Vérifiez votre dossier de courrier indésirable.</span><span class="sxs-lookup"><span data-stu-id="c5f86-186">Check your spam folder.</span></span>
* <span data-ttu-id="c5f86-187">Essayez un autre alias de messagerie sur un autre fournisseur de messagerie (Microsoft, Yahoo, Gmail, etc.)</span><span class="sxs-lookup"><span data-stu-id="c5f86-187">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="c5f86-188">Essayez d’envoyer à différents comptes de messagerie.</span><span class="sxs-lookup"><span data-stu-id="c5f86-188">Try sending to different email accounts.</span></span>

<span data-ttu-id="c5f86-189">**Une meilleure pratique de sécurité** consiste à **ne pas** utiliser les secrets de production dans le test et le développement.</span><span class="sxs-lookup"><span data-stu-id="c5f86-189">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="c5f86-190">Si vous publiez l’application sur Azure, définissez les secrets SendGrid en tant que paramètres d’application dans le portail d’application Web Azure.</span><span class="sxs-lookup"><span data-stu-id="c5f86-190">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="c5f86-191">Le système de configuration est configuré pour lire des clés à partir de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="c5f86-191">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="c5f86-192">Combiner les comptes de connexion sociale et locale</span><span class="sxs-lookup"><span data-stu-id="c5f86-192">Combine social and local login accounts</span></span>

<span data-ttu-id="c5f86-193">Pour compléter cette section, vous devez d’abord activer un fournisseur d’authentification externe.</span><span class="sxs-lookup"><span data-stu-id="c5f86-193">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="c5f86-194">Consultez [l’authentification Facebook, Google et fournisseur externe](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c5f86-194">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="c5f86-195">Vous pouvez combiner des comptes locaux et sociaux en cliquant sur le lien de votre adresse de messagerie.</span><span class="sxs-lookup"><span data-stu-id="c5f86-195">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="c5f86-196">Dans l’ordre suivant, « RickAndMSFT@gmail.com » est d’abord créé comme connexion locale ; Toutefois, vous pouvez d’abord créer le compte en tant que connexion sociale, puis ajouter une connexion locale.</span><span class="sxs-lookup"><span data-stu-id="c5f86-196">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Application Web : RickAndMSFT@gmail.com utilisateur authentifié](accconfirm/_static/rick.png)

<span data-ttu-id="c5f86-198">Cliquez sur le lien **gérer** .</span><span class="sxs-lookup"><span data-stu-id="c5f86-198">Click on the **Manage** link.</span></span> <span data-ttu-id="c5f86-199">Notez la valeur 0 externe (connexions sociales) associée à ce compte.</span><span class="sxs-lookup"><span data-stu-id="c5f86-199">Note the 0 external (social logins) associated with this account.</span></span>

![Gérer l’affichage](accconfirm/_static/manage.png)

<span data-ttu-id="c5f86-201">Cliquez sur le lien vers un autre service de connexion et acceptez les demandes de l’application.</span><span class="sxs-lookup"><span data-stu-id="c5f86-201">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="c5f86-202">Dans l’image suivante, Facebook est le fournisseur d’authentification externe :</span><span class="sxs-lookup"><span data-stu-id="c5f86-202">In the following image, Facebook is the external authentication provider:</span></span>

![Gérer l’affichage de vos connexions externes à l’objet Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="c5f86-204">Les deux comptes ont été combinés.</span><span class="sxs-lookup"><span data-stu-id="c5f86-204">The two accounts have been combined.</span></span> <span data-ttu-id="c5f86-205">Vous pouvez vous connecter avec l’un ou l’autre de ces comptes.</span><span class="sxs-lookup"><span data-stu-id="c5f86-205">You are able to sign in with either account.</span></span> <span data-ttu-id="c5f86-206">Vous souhaiterez peut-être que vos utilisateurs ajoutent des comptes locaux au cas où le service d’authentification de la connexion sociale est défaillant ou qu’ils aient perdu l’accès à leur compte social.</span><span class="sxs-lookup"><span data-stu-id="c5f86-206">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="c5f86-207">Activer la confirmation du compte une fois qu’un site a des utilisateurs</span><span class="sxs-lookup"><span data-stu-id="c5f86-207">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="c5f86-208">L’activation de la confirmation de compte sur un site avec des utilisateurs verrouille tous les utilisateurs existants.</span><span class="sxs-lookup"><span data-stu-id="c5f86-208">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="c5f86-209">Les utilisateurs existants sont bloqués, car leurs comptes ne sont pas confirmés.</span><span class="sxs-lookup"><span data-stu-id="c5f86-209">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="c5f86-210">Pour contourner le verrouillage de l’utilisateur existant, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="c5f86-210">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="c5f86-211">Mettez à jour la base de données pour marquer tous les utilisateurs existants comme étant confirmés.</span><span class="sxs-lookup"><span data-stu-id="c5f86-211">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="c5f86-212">Confirmez les utilisateurs existants.</span><span class="sxs-lookup"><span data-stu-id="c5f86-212">Confirm existing users.</span></span> <span data-ttu-id="c5f86-213">Par exemple, envoyez des e-mails par lots avec des liens de confirmation.</span><span class="sxs-lookup"><span data-stu-id="c5f86-213">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="c5f86-214">Prérequis</span><span class="sxs-lookup"><span data-stu-id="c5f86-214">Prerequisites</span></span>

[<span data-ttu-id="c5f86-215">.NET Core 2,2 SDK ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="c5f86-215">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="c5f86-216">Créer une application Web et une structureIdentity</span><span class="sxs-lookup"><span data-stu-id="c5f86-216">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="c5f86-217">Exécutez les commandes suivantes pour créer une application Web avec l’authentification.</span><span class="sxs-lookup"><span data-stu-id="c5f86-217">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="c5f86-218">Tester l’inscription d’un nouvel utilisateur</span><span class="sxs-lookup"><span data-stu-id="c5f86-218">Test new user registration</span></span>

<span data-ttu-id="c5f86-219">Exécutez l’application, sélectionnez le lien **Register** et inscrivez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c5f86-219">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="c5f86-220">À ce stade, la seule validation sur l’e-mail est avec l' [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="c5f86-220">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="c5f86-221">Après avoir envoyé l’inscription, vous êtes connecté à l’application.</span><span class="sxs-lookup"><span data-stu-id="c5f86-221">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="c5f86-222">Plus loin dans ce didacticiel, le code est mis à jour afin que les nouveaux utilisateurs ne puissent pas se connecter tant que leur adresse de messagerie n’a pas été validée.</span><span class="sxs-lookup"><span data-stu-id="c5f86-222">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="c5f86-223">Notez que le champ de la table `EmailConfirmed` est `False` .</span><span class="sxs-lookup"><span data-stu-id="c5f86-223">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="c5f86-224">Vous souhaiterez peut-être réutiliser cet e-mail à l’étape suivante lorsque l’application envoie un e-mail de confirmation.</span><span class="sxs-lookup"><span data-stu-id="c5f86-224">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="c5f86-225">Cliquez avec le bouton droit sur la ligne et sélectionnez **supprimer**.</span><span class="sxs-lookup"><span data-stu-id="c5f86-225">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="c5f86-226">La suppression de l’alias de messagerie le rend plus facile dans les étapes suivantes.</span><span class="sxs-lookup"><span data-stu-id="c5f86-226">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="c5f86-227">Demander une confirmation par courrier électronique</span><span class="sxs-lookup"><span data-stu-id="c5f86-227">Require email confirmation</span></span>

<span data-ttu-id="c5f86-228">Il est recommandé de confirmer l’e-mail d’une nouvelle inscription d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c5f86-228">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="c5f86-229">La confirmation par courrier électronique vous permet de vérifier qu’ils n’empruntent pas l’identité d’une autre personne (c’est-à-dire qu’ils ne sont pas inscrits auprès de l’e-mail de quelqu’un d’autre).</span><span class="sxs-lookup"><span data-stu-id="c5f86-229">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="c5f86-230">Supposons que vous disposiez d’un forum de discussion et que vous souhaitiez empêcher « yli@example.com » de s’inscrire en tant que «» nolivetto@contoso.com .</span><span class="sxs-lookup"><span data-stu-id="c5f86-230">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="c5f86-231">Sans confirmation par courrier électronique, « nolivetto@contoso.com » pourrait recevoir un courrier indésirable de votre application.</span><span class="sxs-lookup"><span data-stu-id="c5f86-231">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="c5f86-232">Supposons que l’utilisateur a accidentellement été inscrit comme « ylo@example.com » et n’avait pas remarqué la mauvaise orthographe de « Yli ».</span><span class="sxs-lookup"><span data-stu-id="c5f86-232">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="c5f86-233">Ils ne peuvent pas utiliser la récupération de mot de passe car l’application n’a pas leur adresse de messagerie correcte.</span><span class="sxs-lookup"><span data-stu-id="c5f86-233">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="c5f86-234">La confirmation par e-mail fournit une protection limitée des robots.</span><span class="sxs-lookup"><span data-stu-id="c5f86-234">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="c5f86-235">La confirmation par e-mail ne fournit pas de protection contre les utilisateurs malveillants disposant de nombreux comptes de messagerie.</span><span class="sxs-lookup"><span data-stu-id="c5f86-235">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="c5f86-236">En général, vous souhaitez empêcher les nouveaux utilisateurs de publier des données sur votre site Web avant qu’ils n’aient un e-mail confirmé.</span><span class="sxs-lookup"><span data-stu-id="c5f86-236">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="c5f86-237">Mise à jour `Startup.ConfigureServices` pour exiger un e-mail confirmé :</span><span class="sxs-lookup"><span data-stu-id="c5f86-237">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="c5f86-238">`config.SignIn.RequireConfirmedEmail = true;`empêche les utilisateurs inscrits de se connecter jusqu’à ce que leur adresse de messagerie soit confirmée.</span><span class="sxs-lookup"><span data-stu-id="c5f86-238">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="c5f86-239">Configurer le fournisseur de messagerie</span><span class="sxs-lookup"><span data-stu-id="c5f86-239">Configure email provider</span></span>

<span data-ttu-id="c5f86-240">Dans ce didacticiel, [SendGrid](https://sendgrid.com) est utilisé pour envoyer des messages électroniques.</span><span class="sxs-lookup"><span data-stu-id="c5f86-240">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="c5f86-241">Vous avez besoin d’un compte et d’une clé SendGrid pour envoyer des messages électroniques.</span><span class="sxs-lookup"><span data-stu-id="c5f86-241">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="c5f86-242">Vous pouvez utiliser d’autres fournisseurs de courrier électronique.</span><span class="sxs-lookup"><span data-stu-id="c5f86-242">You can use other email providers.</span></span> <span data-ttu-id="c5f86-243">ASP.NET Core 2. x comprend `System.Net.Mail` , qui vous permet d’envoyer des e-mails à partir de votre application.</span><span class="sxs-lookup"><span data-stu-id="c5f86-243">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="c5f86-244">Nous vous recommandons d’utiliser SendGrid ou un autre service de messagerie pour envoyer des courriers électroniques.</span><span class="sxs-lookup"><span data-stu-id="c5f86-244">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="c5f86-245">SMTP est difficile à sécuriser et à configurer correctement.</span><span class="sxs-lookup"><span data-stu-id="c5f86-245">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="c5f86-246">Créez une classe pour extraire la clé de messagerie sécurisée.</span><span class="sxs-lookup"><span data-stu-id="c5f86-246">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="c5f86-247">Pour cet exemple, créez *services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="c5f86-247">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="c5f86-248">Configurer des secrets d’utilisateur SendGrid</span><span class="sxs-lookup"><span data-stu-id="c5f86-248">Configure SendGrid user secrets</span></span>

<span data-ttu-id="c5f86-249">Définissez les `SendGridUser` et `SendGridKey` avec l' [outil de gestion de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="c5f86-249">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="c5f86-250">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c5f86-250">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="c5f86-251">Sur Windows, le gestionnaire de secret stocke les paires clé/valeur dans un *secrets.jssur* le fichier dans le `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` répertoire.</span><span class="sxs-lookup"><span data-stu-id="c5f86-251">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="c5f86-252">Le contenu du *secrets.jssur* le fichier n’est pas chiffré.</span><span class="sxs-lookup"><span data-stu-id="c5f86-252">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="c5f86-253">Le balisage suivant montre l' *secrets.jssur* le fichier.</span><span class="sxs-lookup"><span data-stu-id="c5f86-253">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="c5f86-254">La `SendGridKey` valeur a été supprimée.</span><span class="sxs-lookup"><span data-stu-id="c5f86-254">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="c5f86-255">Pour plus d’informations, consultez le [modèle d’options](xref:fundamentals/configuration/options) et la [configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c5f86-255">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="c5f86-256">Installer SendGrid</span><span class="sxs-lookup"><span data-stu-id="c5f86-256">Install SendGrid</span></span>

<span data-ttu-id="c5f86-257">Ce didacticiel montre comment ajouter des notifications par courrier électronique par le biais de [SendGrid](https://sendgrid.com/), mais vous pouvez envoyer des courriers électroniques à l’aide de SMTP et d’autres mécanismes.</span><span class="sxs-lookup"><span data-stu-id="c5f86-257">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="c5f86-258">Installez le `SendGrid` package NuGet :</span><span class="sxs-lookup"><span data-stu-id="c5f86-258">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c5f86-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c5f86-259">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c5f86-260">À partir de la console du gestionnaire de package, entrez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="c5f86-260">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="c5f86-261">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="c5f86-261">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c5f86-262">À partir de la console, entrez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="c5f86-262">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="c5f86-263">Pour vous inscrire à un compte SendGrid gratuit, consultez [prise en main de SendGrid](https://sendgrid.com/free/) gratuitement.</span><span class="sxs-lookup"><span data-stu-id="c5f86-263">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="c5f86-264">Implémenter IEmailSender</span><span class="sxs-lookup"><span data-stu-id="c5f86-264">Implement IEmailSender</span></span>

<span data-ttu-id="c5f86-265">Pour implémenter `IEmailSender` , créez *services/EMailSender. cs* avec du code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="c5f86-265">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="c5f86-266">Configurer le démarrage pour la prise en charge de la messagerie</span><span class="sxs-lookup"><span data-stu-id="c5f86-266">Configure startup to support email</span></span>

<span data-ttu-id="c5f86-267">Ajoutez le code suivant à la `ConfigureServices` méthode dans le fichier *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="c5f86-267">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="c5f86-268">Ajoutez `EmailSender` en tant que service temporaire.</span><span class="sxs-lookup"><span data-stu-id="c5f86-268">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="c5f86-269">Inscrivez l' `AuthMessageSenderOptions` instance de configuration.</span><span class="sxs-lookup"><span data-stu-id="c5f86-269">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="c5f86-270">Activer la récupération du mot de passe et la confirmation du compte</span><span class="sxs-lookup"><span data-stu-id="c5f86-270">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="c5f86-271">Le modèle dispose du code pour la confirmation du compte et la récupération du mot de passe.</span><span class="sxs-lookup"><span data-stu-id="c5f86-271">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="c5f86-272">Recherchez la `OnPostAsync` méthode dans *Areas/ Identity /pages/Account/Register.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="c5f86-272">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="c5f86-273">Empêchez les utilisateurs nouvellement inscrits d’être automatiquement connectés en commentant la ligne suivante :</span><span class="sxs-lookup"><span data-stu-id="c5f86-273">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="c5f86-274">La méthode Complete est affichée avec la ligne modifiée mise en surbrillance :</span><span class="sxs-lookup"><span data-stu-id="c5f86-274">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="c5f86-275">Inscrire, confirmer l’e-mail et réinitialiser le mot de passe</span><span class="sxs-lookup"><span data-stu-id="c5f86-275">Register, confirm email, and reset password</span></span>

<span data-ttu-id="c5f86-276">Exécutez l’application Web et testez le processus de confirmation et de récupération du mot de passe du compte.</span><span class="sxs-lookup"><span data-stu-id="c5f86-276">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="c5f86-277">Exécuter l’application et inscrire un nouvel utilisateur</span><span class="sxs-lookup"><span data-stu-id="c5f86-277">Run the app and register a new user</span></span>
* <span data-ttu-id="c5f86-278">Vérifiez votre adresse de messagerie pour obtenir le lien de confirmation du compte.</span><span class="sxs-lookup"><span data-stu-id="c5f86-278">Check your email for the account confirmation link.</span></span> <span data-ttu-id="c5f86-279">Consultez le [message de débogage](#debug) si vous n’obtenez pas l’e-mail.</span><span class="sxs-lookup"><span data-stu-id="c5f86-279">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="c5f86-280">Cliquez sur le lien pour confirmer votre adresse de messagerie.</span><span class="sxs-lookup"><span data-stu-id="c5f86-280">Click the link to confirm your email.</span></span>
* <span data-ttu-id="c5f86-281">Connectez-vous avec votre adresse de messagerie et votre mot de passe.</span><span class="sxs-lookup"><span data-stu-id="c5f86-281">Sign in with your email and password.</span></span>
* <span data-ttu-id="c5f86-282">Déconnectez-vous.</span><span class="sxs-lookup"><span data-stu-id="c5f86-282">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="c5f86-283">Afficher la page gérer</span><span class="sxs-lookup"><span data-stu-id="c5f86-283">View the manage page</span></span>

<span data-ttu-id="c5f86-284">Sélectionnez votre nom d’utilisateur dans la fenêtre navigateur : ![ navigateur avec le nom d’utilisateur](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="c5f86-284">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="c5f86-285">La page gérer s’affiche avec l’onglet **Profil** sélectionné.</span><span class="sxs-lookup"><span data-stu-id="c5f86-285">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="c5f86-286">L' **e-mail** affiche une case à cocher indiquant que le message électronique a été confirmé.</span><span class="sxs-lookup"><span data-stu-id="c5f86-286">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="c5f86-287">Test de réinitialisation du mot de passe</span><span class="sxs-lookup"><span data-stu-id="c5f86-287">Test password reset</span></span>

* <span data-ttu-id="c5f86-288">Si vous êtes connecté, sélectionnez **déconnexion**.</span><span class="sxs-lookup"><span data-stu-id="c5f86-288">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="c5f86-289">Sélectionnez le lien **se connecter** , puis sélectionnez le lien vous **avez oublié votre mot de passe ?** .</span><span class="sxs-lookup"><span data-stu-id="c5f86-289">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="c5f86-290">Entrez l’adresse de messagerie que vous avez utilisée pour inscrire le compte.</span><span class="sxs-lookup"><span data-stu-id="c5f86-290">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="c5f86-291">Un e-mail contenant un lien pour réinitialiser votre mot de passe est envoyé.</span><span class="sxs-lookup"><span data-stu-id="c5f86-291">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="c5f86-292">Vérifiez votre adresse de messagerie, puis cliquez sur le lien pour réinitialiser votre mot de passe.</span><span class="sxs-lookup"><span data-stu-id="c5f86-292">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="c5f86-293">Une fois que votre mot de passe a été réinitialisé avec succès, vous pouvez vous connecter avec votre adresse de messagerie et votre nouveau mot de passe.</span><span class="sxs-lookup"><span data-stu-id="c5f86-293">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="c5f86-294">Modifier le délai d’expiration de l’e-mail et de l’activité</span><span class="sxs-lookup"><span data-stu-id="c5f86-294">Change email and activity timeout</span></span>

<span data-ttu-id="c5f86-295">Le délai d’inactivité par défaut est de 14 jours.</span><span class="sxs-lookup"><span data-stu-id="c5f86-295">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="c5f86-296">Le code suivant définit le délai d’expiration d’inactivité sur 5 jours :</span><span class="sxs-lookup"><span data-stu-id="c5f86-296">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="c5f86-297">Modifier toutes les durées de vie des jetons de protection des données</span><span class="sxs-lookup"><span data-stu-id="c5f86-297">Change all data protection token lifespans</span></span>

<span data-ttu-id="c5f86-298">Le code suivant modifie le délai d’expiration de tous les jetons de protection des données à 3 heures :</span><span class="sxs-lookup"><span data-stu-id="c5f86-298">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="c5f86-299">Les Identity jetons utilisateur intégrés (voir [AspNetCore/SRC/ Identity /Extensions.Core/SRC/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) ont un [délai d’expiration d’un jour](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="c5f86-299">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="c5f86-300">Modifier la durée de vie du jeton d’e-mail</span><span class="sxs-lookup"><span data-stu-id="c5f86-300">Change the email token lifespan</span></span>

<span data-ttu-id="c5f86-301">La durée de vie des jetons par défaut des [ Identity jetons utilisateur](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) est d' [un jour](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="c5f86-301">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="c5f86-302">Cette section montre comment modifier la durée de vie des jetons de courrier électronique.</span><span class="sxs-lookup"><span data-stu-id="c5f86-302">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="c5f86-303">Ajoutez un [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personnalisé et <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="c5f86-303">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="c5f86-304">Ajoutez le fournisseur personnalisé au conteneur de service :</span><span class="sxs-lookup"><span data-stu-id="c5f86-304">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="c5f86-305">Renvoyer l’e-mail de confirmation</span><span class="sxs-lookup"><span data-stu-id="c5f86-305">Resend email confirmation</span></span>

<span data-ttu-id="c5f86-306">Consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="c5f86-306">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="c5f86-307">Message de débogage</span><span class="sxs-lookup"><span data-stu-id="c5f86-307">Debug email</span></span>

<span data-ttu-id="c5f86-308">Si vous ne parvenez pas à utiliser le courrier électronique :</span><span class="sxs-lookup"><span data-stu-id="c5f86-308">If you can't get email working:</span></span>

* <span data-ttu-id="c5f86-309">Définissez un point d’arrêt dans `EmailSender.Execute` pour vérifier que `SendGridClient.SendEmailAsync` est appelé.</span><span class="sxs-lookup"><span data-stu-id="c5f86-309">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="c5f86-310">Créez une [application console pour envoyer du courrier électronique](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) à l’aide d’un code similaire à `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="c5f86-310">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="c5f86-311">Passez en revue la page [activité de messagerie](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="c5f86-311">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="c5f86-312">Vérifiez votre dossier de courrier indésirable.</span><span class="sxs-lookup"><span data-stu-id="c5f86-312">Check your spam folder.</span></span>
* <span data-ttu-id="c5f86-313">Essayez un autre alias de messagerie sur un autre fournisseur de messagerie (Microsoft, Yahoo, Gmail, etc.)</span><span class="sxs-lookup"><span data-stu-id="c5f86-313">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="c5f86-314">Essayez d’envoyer à différents comptes de messagerie.</span><span class="sxs-lookup"><span data-stu-id="c5f86-314">Try sending to different email accounts.</span></span>

<span data-ttu-id="c5f86-315">**Une meilleure pratique de sécurité** consiste à **ne pas** utiliser les secrets de production dans le test et le développement.</span><span class="sxs-lookup"><span data-stu-id="c5f86-315">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="c5f86-316">Si vous publiez l’application sur Azure, vous pouvez définir les secrets SendGrid en tant que paramètres d’application dans le portail d’application Web Azure.</span><span class="sxs-lookup"><span data-stu-id="c5f86-316">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="c5f86-317">Le système de configuration est configuré pour lire des clés à partir de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="c5f86-317">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="c5f86-318">Combiner les comptes de connexion sociale et locale</span><span class="sxs-lookup"><span data-stu-id="c5f86-318">Combine social and local login accounts</span></span>

<span data-ttu-id="c5f86-319">Pour compléter cette section, vous devez d’abord activer un fournisseur d’authentification externe.</span><span class="sxs-lookup"><span data-stu-id="c5f86-319">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="c5f86-320">Consultez [l’authentification Facebook, Google et fournisseur externe](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c5f86-320">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="c5f86-321">Vous pouvez combiner des comptes locaux et sociaux en cliquant sur le lien de votre adresse de messagerie.</span><span class="sxs-lookup"><span data-stu-id="c5f86-321">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="c5f86-322">Dans l’ordre suivant, « RickAndMSFT@gmail.com » est d’abord créé comme connexion locale ; Toutefois, vous pouvez d’abord créer le compte en tant que connexion sociale, puis ajouter une connexion locale.</span><span class="sxs-lookup"><span data-stu-id="c5f86-322">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Application Web : RickAndMSFT@gmail.com utilisateur authentifié](accconfirm/_static/rick.png)

<span data-ttu-id="c5f86-324">Cliquez sur le lien **gérer** .</span><span class="sxs-lookup"><span data-stu-id="c5f86-324">Click on the **Manage** link.</span></span> <span data-ttu-id="c5f86-325">Notez la valeur 0 externe (connexions sociales) associée à ce compte.</span><span class="sxs-lookup"><span data-stu-id="c5f86-325">Note the 0 external (social logins) associated with this account.</span></span>

![Gérer l’affichage](accconfirm/_static/manage.png)

<span data-ttu-id="c5f86-327">Cliquez sur le lien vers un autre service de connexion et acceptez les demandes de l’application.</span><span class="sxs-lookup"><span data-stu-id="c5f86-327">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="c5f86-328">Dans l’image suivante, Facebook est le fournisseur d’authentification externe :</span><span class="sxs-lookup"><span data-stu-id="c5f86-328">In the following image, Facebook is the external authentication provider:</span></span>

![Gérer l’affichage de vos connexions externes à l’objet Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="c5f86-330">Les deux comptes ont été combinés.</span><span class="sxs-lookup"><span data-stu-id="c5f86-330">The two accounts have been combined.</span></span> <span data-ttu-id="c5f86-331">Vous pouvez vous connecter avec l’un ou l’autre de ces comptes.</span><span class="sxs-lookup"><span data-stu-id="c5f86-331">You are able to sign in with either account.</span></span> <span data-ttu-id="c5f86-332">Vous souhaiterez peut-être que vos utilisateurs ajoutent des comptes locaux au cas où le service d’authentification de la connexion sociale est défaillant ou qu’ils aient perdu l’accès à leur compte social.</span><span class="sxs-lookup"><span data-stu-id="c5f86-332">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="c5f86-333">Activer la confirmation du compte une fois qu’un site a des utilisateurs</span><span class="sxs-lookup"><span data-stu-id="c5f86-333">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="c5f86-334">L’activation de la confirmation de compte sur un site avec des utilisateurs verrouille tous les utilisateurs existants.</span><span class="sxs-lookup"><span data-stu-id="c5f86-334">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="c5f86-335">Les utilisateurs existants sont bloqués, car leurs comptes ne sont pas confirmés.</span><span class="sxs-lookup"><span data-stu-id="c5f86-335">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="c5f86-336">Pour contourner le verrouillage de l’utilisateur existant, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="c5f86-336">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="c5f86-337">Mettez à jour la base de données pour marquer tous les utilisateurs existants comme étant confirmés.</span><span class="sxs-lookup"><span data-stu-id="c5f86-337">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="c5f86-338">Confirmez les utilisateurs existants.</span><span class="sxs-lookup"><span data-stu-id="c5f86-338">Confirm existing users.</span></span> <span data-ttu-id="c5f86-339">Par exemple, envoyez des e-mails par lots avec des liens de confirmation.</span><span class="sxs-lookup"><span data-stu-id="c5f86-339">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
