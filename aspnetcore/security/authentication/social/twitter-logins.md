---
title: Configuration de connexion externe Twitter avec ASP.NET Core
author: rick-anderson
description: Ce tutoriel démontre l’intégration de l’authentification des utilisateurs de compte Twitter dans une application ASP.NET Core existante.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277286"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="25021-103">Configuration de connexion externe Twitter avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="25021-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="25021-104">Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="25021-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="25021-105">Cet exemple montre comment permettre aux utilisateurs de [se connecter avec leur compte Twitter à](https://dev.twitter.com/web/sign-in/desktop-browser) l’aide d’un échantillon ASP.NET projet Core 3.0 créé sur la page [précédente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="25021-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="25021-106">Créer l’application sur Twitter</span><span class="sxs-lookup"><span data-stu-id="25021-106">Create the app in Twitter</span></span>

* <span data-ttu-id="25021-107">Ajoutez le package [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet au projet.</span><span class="sxs-lookup"><span data-stu-id="25021-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="25021-108">Naviguez [https://apps.twitter.com/](https://apps.twitter.com/) et connectez-vous.</span><span class="sxs-lookup"><span data-stu-id="25021-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="25021-109">Si vous n’avez pas déjà de compte Twitter, utilisez le lien **[Inscrivez-vous maintenant](https://twitter.com/signup)** pour en créer un.</span><span class="sxs-lookup"><span data-stu-id="25021-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="25021-110">Sélectionnez **Créer une application**.</span><span class="sxs-lookup"><span data-stu-id="25021-110">Select **Create an app**.</span></span> <span data-ttu-id="25021-111">Remplissez le **nom de l’application,** la **description de l’application** et **le site Web** public URI (cela peut être temporaire jusqu’à ce que vous enregistrez le nom de domaine) :</span><span class="sxs-lookup"><span data-stu-id="25021-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="25021-112">Cochez la case à côté pour **activer connectez-vous avec Twitter**</span><span class="sxs-lookup"><span data-stu-id="25021-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="25021-113">Microsoft.AspNetCore.Identity exige des utilisateurs d’avoir une adresse e-mail par défaut.</span><span class="sxs-lookup"><span data-stu-id="25021-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="25021-114">Allez à l’onglet **Autorisations,** cliquez sur le bouton **Modifier** et cochez la case à côté **de l’adresse e-mail request des utilisateurs**.</span><span class="sxs-lookup"><span data-stu-id="25021-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="25021-115">Entrez votre URI de développement avec `/signin-twitter` joint dans le champ **d’URL De rappel** (par exemple : `https://webapp128.azurewebsites.net/signin-twitter`).</span><span class="sxs-lookup"><span data-stu-id="25021-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="25021-116">Le système d’authentification Twitter configuré `/signin-twitter` plus tard dans cet échantillon traitera automatiquement les demandes en cours d’implémentation du flux OAuth.</span><span class="sxs-lookup"><span data-stu-id="25021-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="25021-117">Le segment `/signin-twitter` URI est défini comme le rappel par défaut du fournisseur d’authentification Twitter.</span><span class="sxs-lookup"><span data-stu-id="25021-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="25021-118">Vous pouvez modifier le rappel par défaut URI tout en configurant le middleware d’authentification Twitter via la propriété [héritée RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) de la classe [TwitterOptions.](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)</span><span class="sxs-lookup"><span data-stu-id="25021-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="25021-119">Remplissez le reste du formulaire et sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="25021-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="25021-120">Les nouveaux détails de l’application sont affichés :</span><span class="sxs-lookup"><span data-stu-id="25021-120">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="25021-121">Stockez la clé API grand public De Twitter et le secret</span><span class="sxs-lookup"><span data-stu-id="25021-121">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="25021-122">Stockez des paramètres sensibles tels que la clé API grand public De Twitter et secret avec [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="25021-122">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="25021-123">Pour cet échantillon, utilisez les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="25021-123">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="25021-124">Initialiser le projet de stockage secret par les instructions à [Enable stockage secret](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="25021-124">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="25021-125">Stockez les réglages sensibles dans `Authentication:Twitter:ConsumerKey` le `Authentication:Twitter:ConsumerSecret`magasin secret local avec les clés de secrets et :</span><span class="sxs-lookup"><span data-stu-id="25021-125">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="25021-126">Ces jetons peuvent être trouvés sur l’onglet **Keys and Access Tokens** après la création d’une nouvelle application Twitter :</span><span class="sxs-lookup"><span data-stu-id="25021-126">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="25021-127">Configurer l’authentification Twitter</span><span class="sxs-lookup"><span data-stu-id="25021-127">Configure Twitter Authentication</span></span>

<span data-ttu-id="25021-128">Ajouter le service `ConfigureServices` Twitter dans la méthode dans *Startup.cs* fichier:</span><span class="sxs-lookup"><span data-stu-id="25021-128">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="25021-129">Consultez la référence [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API pour plus d’informations sur les options de configuration prises en charge par l’authentification Twitter.</span><span class="sxs-lookup"><span data-stu-id="25021-129">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="25021-130">Cela peut être utilisé pour demander des informations différentes sur l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="25021-130">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="25021-131">Connectez-vous avec Twitter</span><span class="sxs-lookup"><span data-stu-id="25021-131">Sign in with Twitter</span></span>

<span data-ttu-id="25021-132">Exécutez l’application et **sélectionnez Log in**.</span><span class="sxs-lookup"><span data-stu-id="25021-132">Run the app and select **Log in**.</span></span> <span data-ttu-id="25021-133">Une option de connexion avec Twitter apparaît:</span><span class="sxs-lookup"><span data-stu-id="25021-133">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="25021-134">En cliquant sur **Twitter** redirige vers Twitter pour l’authentification:</span><span class="sxs-lookup"><span data-stu-id="25021-134">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="25021-135">Après avoir entré vos informations d’identification Twitter, vous êtes redirigé vers le site Web où vous pouvez définir votre e-mail.</span><span class="sxs-lookup"><span data-stu-id="25021-135">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="25021-136">Vous êtes maintenant connecté à l’aide de vos informations d’identification Twitter:</span><span class="sxs-lookup"><span data-stu-id="25021-136">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="25021-137">Dépannage</span><span class="sxs-lookup"><span data-stu-id="25021-137">Troubleshooting</span></span>

* <span data-ttu-id="25021-138">**ASP.NET Core 2.x seulement:** Si Identity n’est pas `services.AddIdentity` `ConfigureServices`configurée en appelant, tenter d’authentifier se traduira par *ArgumentException: L’option «SignInScheme» doit être fournie*.</span><span class="sxs-lookup"><span data-stu-id="25021-138">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="25021-139">Le modèle de projet utilisé dans cet échantillon garantit que cela est fait.</span><span class="sxs-lookup"><span data-stu-id="25021-139">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="25021-140">Si la base de données du site n’a pas été créée en appliquant la migration initiale, vous obtiendrez *une opération de base de données a échoué pendant le traitement de l’erreur de demande.*</span><span class="sxs-lookup"><span data-stu-id="25021-140">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="25021-141">Appuyez **sur Appliquer les migrations** pour créer la base de données et rafraîchir pour continuer au-delà de l’erreur.</span><span class="sxs-lookup"><span data-stu-id="25021-141">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="25021-142">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="25021-142">Next steps</span></span>

* <span data-ttu-id="25021-143">Cet article a montré comment vous pouvez authentifier avec Twitter.</span><span class="sxs-lookup"><span data-stu-id="25021-143">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="25021-144">Vous pouvez suivre une approche similaire pour authentifier avec d’autres fournisseurs répertoriés sur la [page précédente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="25021-144">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="25021-145">Une fois que vous publiez votre site Web `ConsumerSecret` sur l’application Web Azure, vous devez réinitialiser le portail des développeurs Twitter.</span><span class="sxs-lookup"><span data-stu-id="25021-145">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="25021-146">Définissez `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret` les paramètres et les paramètres d’application dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="25021-146">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="25021-147">Le système de configuration est configuré pour lire les clés des variables de l’environnement.</span><span class="sxs-lookup"><span data-stu-id="25021-147">The configuration system is set up to read keys from environment variables.</span></span>
