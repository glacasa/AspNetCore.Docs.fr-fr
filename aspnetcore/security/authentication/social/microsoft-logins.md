---
title: Configuration de connexion externe de compte Microsoft avec ASP.NET Core
author: rick-anderson
description: Cet exemple démontre l’intégration de l’authentification de l’utilisateur du compte Microsoft dans une application ASP.NET Core existante.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 12c86456dad86731b86487a3a4dd725f36677002
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384044"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="5fc38-103">Configuration de connexion externe de compte Microsoft avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5fc38-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="5fc38-104">Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5fc38-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5fc38-105">Cet exemple vous montre comment permettre aux utilisateurs de se connecter avec leur compte Microsoft de travail, d’école ou personnel en utilisant le projet ASP.NET Core 3.0 créé sur la [page précédente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="5fc38-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="5fc38-106">Créez l’application dans microsoft Developer Portal</span><span class="sxs-lookup"><span data-stu-id="5fc38-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="5fc38-107">Ajoutez le package [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet au projet.</span><span class="sxs-lookup"><span data-stu-id="5fc38-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="5fc38-108">Naviguez vers le portail Azure - Page [d’enregistrement de l’application](https://go.microsoft.com/fwlink/?linkid=2083908) et créez ou connectez-vous à un compte Microsoft :</span><span class="sxs-lookup"><span data-stu-id="5fc38-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="5fc38-109">Si vous n’avez pas de compte Microsoft, sélectionnez **Créer un**.</span><span class="sxs-lookup"><span data-stu-id="5fc38-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="5fc38-110">Après la signature, vous êtes redirigé vers la page **d’inscriptions App** :</span><span class="sxs-lookup"><span data-stu-id="5fc38-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="5fc38-111">Sélectionner **Nouvelle inscription**</span><span class="sxs-lookup"><span data-stu-id="5fc38-111">Select **New registration**</span></span>
* <span data-ttu-id="5fc38-112">Entrez un **nom**.</span><span class="sxs-lookup"><span data-stu-id="5fc38-112">Enter a **Name**.</span></span>
* <span data-ttu-id="5fc38-113">Sélectionnez une option pour **les types de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="5fc38-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="5fc38-114">Sous **Redirect URI**, entrez votre URL de développement avec `/signin-microsoft` joint.</span><span class="sxs-lookup"><span data-stu-id="5fc38-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="5fc38-115">Par exemple : `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="5fc38-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="5fc38-116">Le système d’authentification Microsoft configuré `/signin-microsoft` plus tard dans cet échantillon traitera automatiquement les demandes en route pour implémenter le flux OAuth.</span><span class="sxs-lookup"><span data-stu-id="5fc38-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="5fc38-117">Sélect **Registre**</span><span class="sxs-lookup"><span data-stu-id="5fc38-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="5fc38-118">Créer un secret client</span><span class="sxs-lookup"><span data-stu-id="5fc38-118">Create client secret</span></span>

* <span data-ttu-id="5fc38-119">Dans le volet gauche, sélectionnez **Certificats et secrets**.</span><span class="sxs-lookup"><span data-stu-id="5fc38-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="5fc38-120">Sous **les secrets de client**, sélectionnez Nouveau secret **client**</span><span class="sxs-lookup"><span data-stu-id="5fc38-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="5fc38-121">Ajoutez une description pour le secret du client.</span><span class="sxs-lookup"><span data-stu-id="5fc38-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="5fc38-122">Sélectionnez le bouton **Ajouter.**</span><span class="sxs-lookup"><span data-stu-id="5fc38-122">Select the **Add** button.</span></span>

* <span data-ttu-id="5fc38-123">Sous **les secrets de client,** copiez la valeur du secret du client.</span><span class="sxs-lookup"><span data-stu-id="5fc38-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="5fc38-124">Le segment `/signin-microsoft` URI est défini comme le rappel par défaut du fournisseur d’authentification Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5fc38-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="5fc38-125">Vous pouvez modifier la callback par défaut URI tout en configurant le middleware d’authentification Microsoft via la propriété [héritée RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) de la classe [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)</span><span class="sxs-lookup"><span data-stu-id="5fc38-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="5fc38-126">Stockez l’IDENTIFIANT et le secret du client Microsoft</span><span class="sxs-lookup"><span data-stu-id="5fc38-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="5fc38-127">Stockez des paramètres sensibles tels que l’IDENTIFIANT du client Microsoft et des valeurs secrètes avec [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="5fc38-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="5fc38-128">Pour cet échantillon, utilisez les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="5fc38-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="5fc38-129">Initialiser le projet de stockage secret par les instructions à [Enable stockage secret](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="5fc38-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="5fc38-130">Stockez les réglages sensibles dans `Authentication:Microsoft:ClientId` le `Authentication:Microsoft:ClientSecret`magasin secret local avec les clés secrètes et :</span><span class="sxs-lookup"><span data-stu-id="5fc38-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="5fc38-131">Configurer l’authentification du compte Microsoft</span><span class="sxs-lookup"><span data-stu-id="5fc38-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="5fc38-132">Ajoutez le service de `Startup.ConfigureServices`compte Microsoft au :</span><span class="sxs-lookup"><span data-stu-id="5fc38-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="5fc38-133">Pour plus d’informations sur les options de configuration prises en charge par l’authentification de compte Microsoft, consultez la référence [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API.</span><span class="sxs-lookup"><span data-stu-id="5fc38-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="5fc38-134">Cela peut être utilisé pour demander des informations différentes sur l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5fc38-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="5fc38-135">Connectez-vous au compte Microsoft</span><span class="sxs-lookup"><span data-stu-id="5fc38-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="5fc38-136">Exécutez l’application et cliquez sur **Connectez-vous**.</span><span class="sxs-lookup"><span data-stu-id="5fc38-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="5fc38-137">Une option de connexion avec Microsoft apparaît.</span><span class="sxs-lookup"><span data-stu-id="5fc38-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="5fc38-138">Lorsque vous cliquez sur Microsoft, vous êtes redirigé vers Microsoft pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="5fc38-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="5fc38-139">Après s’être inscrit avec votre compte Microsoft, vous serez invité à laisser l’application accéder à vos informations :</span><span class="sxs-lookup"><span data-stu-id="5fc38-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="5fc38-140">Appuyez **oui** et vous serez redirigé vers le site Web où vous pouvez définir votre e-mail.</span><span class="sxs-lookup"><span data-stu-id="5fc38-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="5fc38-141">Vous êtes maintenant connecté à l’aide de vos informations d’identification Microsoft:</span><span class="sxs-lookup"><span data-stu-id="5fc38-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="5fc38-142">Dépannage</span><span class="sxs-lookup"><span data-stu-id="5fc38-142">Troubleshooting</span></span>

* <span data-ttu-id="5fc38-143">Si le fournisseur de compte Microsoft vous redirige vers une page d’erreur de `#` signe, notez le titre d’erreur et les paramètres de la chaîne de requête de description directement après le (hashtag) dans l’Uri.</span><span class="sxs-lookup"><span data-stu-id="5fc38-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="5fc38-144">Bien que le message d’erreur semble indiquer un problème avec l’authentification Microsoft, la cause la plus fréquente est votre application Uri ne correspond pas à l’un des **URIs Redirect** spécifiés pour la plate-forme **Web.**</span><span class="sxs-lookup"><span data-stu-id="5fc38-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="5fc38-145">Si Identity n’est pas `services.AddIdentity` `ConfigureServices`configurée en appelant, tenter d’authentifier se traduira par *ArgumentException: L’option «SignInScheme» doit être fournie*.</span><span class="sxs-lookup"><span data-stu-id="5fc38-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="5fc38-146">Le modèle de projet utilisé dans cet échantillon garantit que cela est fait.</span><span class="sxs-lookup"><span data-stu-id="5fc38-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="5fc38-147">Si la base de données du site n’a pas été créée en appliquant la migration initiale, vous obtiendrez *une opération de base de données a échoué pendant le traitement de l’erreur de demande.*</span><span class="sxs-lookup"><span data-stu-id="5fc38-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="5fc38-148">Appuyez **sur Appliquer les migrations** pour créer la base de données et rafraîchir pour continuer au-delà de l’erreur.</span><span class="sxs-lookup"><span data-stu-id="5fc38-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5fc38-149">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="5fc38-149">Next steps</span></span>

* <span data-ttu-id="5fc38-150">Cet article a montré comment vous pouvez authentifier avec Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5fc38-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="5fc38-151">Vous pouvez suivre une approche similaire pour authentifier avec d’autres fournisseurs répertoriés sur la [page précédente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="5fc38-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="5fc38-152">Une fois que vous publiez votre site Web sur l’application Web Azure, créez un nouveau secret client dans le portail microsoft Developer.</span><span class="sxs-lookup"><span data-stu-id="5fc38-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="5fc38-153">Définissez `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` les paramètres et les paramètres d’application dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="5fc38-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="5fc38-154">Le système de configuration est configuré pour lire les clés des variables de l’environnement.</span><span class="sxs-lookup"><span data-stu-id="5fc38-154">The configuration system is set up to read keys from environment variables.</span></span>
