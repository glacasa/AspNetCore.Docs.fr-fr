---
title: Configuration de connexion externe Facebook dans ASP.NET Core
author: rick-anderson
description: Tutorial avec des exemples de code démontrant l’intégration de l’authentification de l’utilisateur de compte Facebook dans une application ASP.NET Core existante.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277299"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="d1ab5-103">Configuration de connexion externe Facebook dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1ab5-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="d1ab5-104">Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d1ab5-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="d1ab5-105">Ce tutoriel avec des exemples de code montre comment permettre à vos utilisateurs de se connecter avec leur compte Facebook à l’aide d’un échantillon ASP.NET projet Core 3.0 créé sur la [page précédente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d1ab5-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="d1ab5-106">Nous commençons par créer un IDENTIFIANT d’application Facebook en suivant les [étapes officielles](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="d1ab5-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="d1ab5-107">Créer l’application dans Facebook</span><span class="sxs-lookup"><span data-stu-id="d1ab5-107">Create the app in Facebook</span></span>

* <span data-ttu-id="d1ab5-108">Ajoutez le package [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet au projet.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="d1ab5-109">Naviguez vers la page [de l’application Facebook Developers](https://developers.facebook.com/apps/) et connectez-vous.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="d1ab5-110">Si vous n’avez pas déjà de compte Facebook, utilisez le **lien Facebook** sur la page de connexion pour en créer un.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="d1ab5-111">Une fois que vous avez un compte Facebook, suivez les instructions pour vous inscrire en tant que développeur Facebook.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="d1ab5-112">Du menu **My Apps,** sélectionnez **Create App** pour créer un nouvel IDENTIFIANT d’application.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Facebook pour le portail des développeurs ouvert dans Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="d1ab5-114">Remplissez le formulaire et appuyez sur le bouton **Create App ID.**</span><span class="sxs-lookup"><span data-stu-id="d1ab5-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Créer un nouveau formulaire d’identification d’application](index/_static/FBNewAppId.png)

* <span data-ttu-id="d1ab5-116">Sur la nouvelle carte App, sélectionnez **Ajouter un produit**.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="d1ab5-117">Sur la carte **Facebook Login,** cliquez **sur Set Up**</span><span class="sxs-lookup"><span data-stu-id="d1ab5-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Page d’installation de produits](index/_static/FBProductSetup.png)

* <span data-ttu-id="d1ab5-119">**L’assistant Quickstart** lance avec **Choisissez une plate-forme** comme première page.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="d1ab5-120">Déroucher l’assistant pour l’instant en cliquant sur le lien **Paramètres** **de connexion FaceBook** dans le menu en bas à gauche:</span><span class="sxs-lookup"><span data-stu-id="d1ab5-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Passer le démarrage rapide](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="d1ab5-122">Vous êtes présenté avec la page **Paramètres OAuth Client** :</span><span class="sxs-lookup"><span data-stu-id="d1ab5-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Page Paramètres OAuth Client](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="d1ab5-124">Entrez votre URI de développement avec */signin-facebook* joint dans le champ valide `https://localhost:44320/signin-facebook` **OAuth Redirect URIs** (par exemple: ).</span><span class="sxs-lookup"><span data-stu-id="d1ab5-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="d1ab5-125">L’authentification Facebook configurée plus tard dans ce tutoriel traitera automatiquement les demandes à *l’itinéraire /signin-facebook* pour implémenter le flux OAuth.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="d1ab5-126">L’URI */signin-facebook* est défini comme le rappel par défaut du fournisseur d’authentification Facebook.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="d1ab5-127">Vous pouvez modifier le rappel par défaut URI tout en configurant le middleware d’authentification Facebook via la propriété [héritée RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) de la classe [FacebookOptions.](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)</span><span class="sxs-lookup"><span data-stu-id="d1ab5-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="d1ab5-128">Cliquez sur **Enregistrer les modifications**.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="d1ab5-129">Cliquez sur **Paramètres** > **Lien de base** dans la navigation gauche.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="d1ab5-130">Sur cette page, prenez `App ID` note `App Secret`de votre et de votre .</span><span class="sxs-lookup"><span data-stu-id="d1ab5-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="d1ab5-131">Vous ajouterez les deux dans votre application ASP.NET Core dans la section suivante :</span><span class="sxs-lookup"><span data-stu-id="d1ab5-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="d1ab5-132">Lors du déploiement du site, vous devez revoir la page d’installation **Facebook Login** et enregistrer un nouvel URI public.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="d1ab5-133">Stockez l’ID de l’application Facebook et le secret</span><span class="sxs-lookup"><span data-stu-id="d1ab5-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="d1ab5-134">Stockez des paramètres sensibles tels que l’ID de l’application Facebook et des valeurs secrètes avec [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d1ab5-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="d1ab5-135">Pour cet échantillon, utilisez les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="d1ab5-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="d1ab5-136">Initialiser le projet de stockage secret par les instructions à [Enable stockage secret](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="d1ab5-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="d1ab5-137">Stockez les réglages sensibles dans `Authentication:Facebook:AppId` le `Authentication:Facebook:AppSecret`magasin secret local avec les clés secrètes et :</span><span class="sxs-lookup"><span data-stu-id="d1ab5-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="d1ab5-138">Configurer Facebook Authentification</span><span class="sxs-lookup"><span data-stu-id="d1ab5-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="d1ab5-139">Ajouter le service `ConfigureServices` Facebook dans la méthode dans le fichier *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="d1ab5-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="d1ab5-140">Connectez-vous à Facebook</span><span class="sxs-lookup"><span data-stu-id="d1ab5-140">Sign in with Facebook</span></span>

* <span data-ttu-id="d1ab5-141">Exécutez l’application et **sélectionnez Log in**.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="d1ab5-142">**Utilisez un autre service pour vous connecter.**, sélectionnez Facebook.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="d1ab5-143">Vous êtes redirigé vers **Facebook** pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="d1ab5-144">Entrez vos informations d’identification Facebook.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="d1ab5-145">Vous êtes redirigé vers votre site où vous pouvez définir votre e-mail.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="d1ab5-146">Vous êtes maintenant connecté à l’aide de vos informations d’identification Facebook:</span><span class="sxs-lookup"><span data-stu-id="d1ab5-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="d1ab5-147">Réagir pour annuler autoriser l’inscription externe</span><span class="sxs-lookup"><span data-stu-id="d1ab5-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="d1ab5-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>peut fournir un chemin de réorientation vers l’agent utilisateur lorsque l’utilisateur n’approuve pas la demande d’autorisation demandée.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="d1ab5-149">Le code suivant `AccessDeniedPath` `"/AccessDeniedPathInfo"`définit le :</span><span class="sxs-lookup"><span data-stu-id="d1ab5-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="d1ab5-150">Nous recommandons `AccessDeniedPath` que la page contienne les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="d1ab5-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="d1ab5-151">L’authentification à distance a été annulée.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="d1ab5-152">Cette application nécessite une authentification.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-152">This app requires authentication.</span></span>
* <span data-ttu-id="d1ab5-153">Pour essayer de nouveau vous connecter, sélectionnez le lien Login.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="d1ab5-154">Test AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="d1ab5-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="d1ab5-155">Naviguez vers [facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="d1ab5-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="d1ab5-156">Si vous êtes connecté, vous devez vous déconnecter.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="d1ab5-157">Exécutez l’application et sélectionnez l’inscription Facebook.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="d1ab5-158">Sélectionnez **Pas maintenant**.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-158">Select **Not now**.</span></span> <span data-ttu-id="d1ab5-159">Vous êtes redirigé `AccessDeniedPath` vers la page spécifiée.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="d1ab5-160">Consultez la référence [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API pour plus d’informations sur les options de configuration prises en charge par l’authentification De Facebook.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="d1ab5-161">Les options de configuration peuvent être utilisées pour :</span><span class="sxs-lookup"><span data-stu-id="d1ab5-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="d1ab5-162">Demandez des informations différentes sur l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-162">Request different information about the user.</span></span>
* <span data-ttu-id="d1ab5-163">Ajoutez des arguments de chaîne de requête pour personnaliser l’expérience de connexion.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="d1ab5-164">Dépannage</span><span class="sxs-lookup"><span data-stu-id="d1ab5-164">Troubleshooting</span></span>

* <span data-ttu-id="d1ab5-165">**ASP.NET Core 2.x seulement:** Si Identity n’est pas `services.AddIdentity` `ConfigureServices`configurée en appelant, tenter d’authentifier se traduira par *ArgumentException: L’option «SignInScheme» doit être fournie*.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="d1ab5-166">Le modèle de projet utilisé dans ce tutoriel garantit que cela est fait.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="d1ab5-167">Si la base de données du site n’a pas été créée en appliquant la migration initiale, vous obtenez *une opération de base de données a échoué pendant le traitement de l’erreur de demande.*</span><span class="sxs-lookup"><span data-stu-id="d1ab5-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="d1ab5-168">Appuyez **sur Appliquer les migrations** pour créer la base de données et rafraîchir pour continuer au-delà de l’erreur.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d1ab5-169">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="d1ab5-169">Next steps</span></span>

* <span data-ttu-id="d1ab5-170">Cet article a montré comment vous pouvez authentifier avec Facebook.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="d1ab5-171">Vous pouvez suivre une approche similaire pour authentifier avec d’autres fournisseurs répertoriés sur la [page précédente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d1ab5-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="d1ab5-172">Une fois que vous publiez votre site Web `AppSecret` sur l’application Web Azure, vous devez réinitialiser le portail des développeurs Facebook.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="d1ab5-173">Définissez `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` les paramètres et les paramètres d’application dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="d1ab5-174">Le système de configuration est configuré pour lire les clés des variables de l’environnement.</span><span class="sxs-lookup"><span data-stu-id="d1ab5-174">The configuration system is set up to read keys from environment variables.</span></span>
