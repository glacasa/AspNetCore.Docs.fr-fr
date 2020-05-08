---
title: Créer une application ASP.NET Core avec les données utilisateur protégées par l’autorisation
author: rick-anderson
description: Découvrez comment créer une Razor application de pages avec des données utilisateur protégées par une autorisation. Comprend le protocole HTTPs, l’authentification, Identityla sécurité ASP.net core.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: f52b08786dde54e7dcbd2e00f43badb58879cf79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775750"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="36b6c-104">Créer une application ASP.NET Core avec les données utilisateur protégées par l’autorisation</span><span class="sxs-lookup"><span data-stu-id="36b6c-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="36b6c-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="36b6c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="36b6c-106">Consultez [ce fichier PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) pour la version ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="36b6c-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="36b6c-107">Le ASP.NET Core version 1,1 de ce didacticiel se trouve dans [ce](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) dossier.</span><span class="sxs-lookup"><span data-stu-id="36b6c-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="36b6c-108">L’exemple 1,1 ASP.NET Core se trouve dans les [exemples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="36b6c-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="36b6c-109">Voir [ce PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="36b6c-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36b6c-110">Ce didacticiel montre comment créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="36b6c-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="36b6c-111">Il affiche la liste des contacts que les utilisateurs authentifiés (inscrits) ont créés.</span><span class="sxs-lookup"><span data-stu-id="36b6c-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="36b6c-112">Il existe trois groupes de sécurité :</span><span class="sxs-lookup"><span data-stu-id="36b6c-112">There are three security groups:</span></span>

* <span data-ttu-id="36b6c-113">Les **utilisateurs inscrits** peuvent afficher toutes les données approuvées et peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="36b6c-114">Les **responsables** peuvent approuver ou rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="36b6c-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="36b6c-115">Seuls les contacts approuvés sont visibles pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="36b6c-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="36b6c-116">**Les administrateurs** peuvent approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="36b6c-117">Les images de ce document ne correspondent pas exactement aux modèles les plus récents.</span><span class="sxs-lookup"><span data-stu-id="36b6c-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="36b6c-118">Dans l’image suivante, l’utilisateur Rick`rick@example.com`() est connecté.</span><span class="sxs-lookup"><span data-stu-id="36b6c-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="36b6c-119">Rick peut uniquement afficher les contacts approuvés et **modifier**/**supprimer**/**créer** des liens pour ses contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="36b6c-120">Seul le dernier enregistrement créé par Rick affiche des liens **modifier** et **supprimer** .</span><span class="sxs-lookup"><span data-stu-id="36b6c-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="36b6c-121">Les autres utilisateurs ne verront pas le dernier enregistrement jusqu’à ce qu’un responsable ou un administrateur change l’État en « approuvé ».</span><span class="sxs-lookup"><span data-stu-id="36b6c-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Capture d’écran montrant que Rick est connecté](secure-data/_static/rick.png)

<span data-ttu-id="36b6c-123">Dans l’image suivante, `manager@contoso.com` est connecté et dans le rôle du responsable :</span><span class="sxs-lookup"><span data-stu-id="36b6c-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Capture d' manager@contoso.com écran montrant la connexion](secure-data/_static/manager1.png)

<span data-ttu-id="36b6c-125">L’illustration suivante montre la vue des détails du gestionnaire d’un contact :</span><span class="sxs-lookup"><span data-stu-id="36b6c-125">The following image shows the managers details view of a contact:</span></span>

![Vue du gestionnaire d’un contact](secure-data/_static/manager.png)

<span data-ttu-id="36b6c-127">Les boutons **approuver** et **rejeter** s’affichent uniquement pour les responsables et les administrateurs.</span><span class="sxs-lookup"><span data-stu-id="36b6c-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="36b6c-128">Dans l’image suivante, `admin@contoso.com` est connecté et dans le rôle de l’administrateur :</span><span class="sxs-lookup"><span data-stu-id="36b6c-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Capture d' admin@contoso.com écran montrant la connexion](secure-data/_static/admin.png)

<span data-ttu-id="36b6c-130">L’administrateur dispose de tous les privilèges.</span><span class="sxs-lookup"><span data-stu-id="36b6c-130">The administrator has all privileges.</span></span> <span data-ttu-id="36b6c-131">Elle peut lire/modifier/supprimer un contact et modifier l’état des contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="36b6c-132">L’application a été créée par la [génération](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) de `Contact` modèles automatique du modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="36b6c-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="36b6c-133">L’exemple contient les gestionnaires d’autorisations suivants :</span><span class="sxs-lookup"><span data-stu-id="36b6c-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="36b6c-134">`ContactIsOwnerAuthorizationHandler`: Garantit qu’un utilisateur peut uniquement modifier ses données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="36b6c-135">`ContactManagerAuthorizationHandler`: Permet aux gestionnaires d’approuver ou de rejeter les contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="36b6c-136">`ContactAdministratorsAuthorizationHandler`: Permet aux administrateurs d’approuver ou de refuser des contacts, ainsi que de modifier/supprimer des contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="36b6c-137">Prérequis</span><span class="sxs-lookup"><span data-stu-id="36b6c-137">Prerequisites</span></span>

<span data-ttu-id="36b6c-138">Ce didacticiel est avancé.</span><span class="sxs-lookup"><span data-stu-id="36b6c-138">This tutorial is advanced.</span></span> <span data-ttu-id="36b6c-139">Vous devez connaître les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="36b6c-139">You should be familiar with:</span></span>

* [<span data-ttu-id="36b6c-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36b6c-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="36b6c-141">Authentification</span><span class="sxs-lookup"><span data-stu-id="36b6c-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="36b6c-142">Confirmation de compte et récupération de mot de passe</span><span class="sxs-lookup"><span data-stu-id="36b6c-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="36b6c-143">Autorisation</span><span class="sxs-lookup"><span data-stu-id="36b6c-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="36b6c-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="36b6c-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="36b6c-145">L’application de démarrage et la fin de l’application</span><span class="sxs-lookup"><span data-stu-id="36b6c-145">The starter and completed app</span></span>

<span data-ttu-id="36b6c-146">[Téléchargez](xref:index#how-to-download-a-sample) l’application [terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="36b6c-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="36b6c-147">[Testez](#test-the-completed-app) l’application terminée afin de vous familiariser avec ses fonctionnalités de sécurité.</span><span class="sxs-lookup"><span data-stu-id="36b6c-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="36b6c-148">L’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="36b6c-148">The starter app</span></span>

<span data-ttu-id="36b6c-149">[Téléchargez](xref:index#how-to-download-a-sample) l’application de [démarrage](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="36b6c-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="36b6c-150">Exécutez l’application, appuyez sur le lien **ContactManager** et vérifiez que vous pouvez créer, modifier et supprimer un contact.</span><span class="sxs-lookup"><span data-stu-id="36b6c-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="36b6c-151">Sécuriser les données utilisateur</span><span class="sxs-lookup"><span data-stu-id="36b6c-151">Secure user data</span></span>

<span data-ttu-id="36b6c-152">Les sections suivantes présentent les principales étapes à suivre pour créer l’application de données utilisateur sécurisé.</span><span class="sxs-lookup"><span data-stu-id="36b6c-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="36b6c-153">Il peut s’avérer utile de faire référence au projet terminé.</span><span class="sxs-lookup"><span data-stu-id="36b6c-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="36b6c-154">Lier les données de contact à l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="36b6c-154">Tie the contact data to the user</span></span>

<span data-ttu-id="36b6c-155">Utilisez l’IDENTIFIant utilisateur de l' [identité](xref:security/authentication/identity) ASP.net pour vous assurer que les utilisateurs peuvent modifier leurs données, mais pas les données d’autres utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="36b6c-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="36b6c-156">Ajoutez `OwnerID` et `ContactStatus` au `Contact` modèle :</span><span class="sxs-lookup"><span data-stu-id="36b6c-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="36b6c-157">`OwnerID`ID de l’utilisateur de la `AspNetUser` table dans la base de données d' [identité](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="36b6c-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="36b6c-158">Le `Status` champ détermine si un contact est visible par les utilisateurs généraux.</span><span class="sxs-lookup"><span data-stu-id="36b6c-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="36b6c-159">Créez une nouvelle migration et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="36b6c-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="36b6c-160">Ajouter des services de rôle à l’identité</span><span class="sxs-lookup"><span data-stu-id="36b6c-160">Add Role services to Identity</span></span>

<span data-ttu-id="36b6c-161">Ajoutez [rôles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pour ajouter des services de rôle :</span><span class="sxs-lookup"><span data-stu-id="36b6c-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="36b6c-162">Exiger des utilisateurs authentifiés</span><span class="sxs-lookup"><span data-stu-id="36b6c-162">Require authenticated users</span></span>

<span data-ttu-id="36b6c-163">Définissez la stratégie d’authentification par défaut pour exiger que les utilisateurs soient authentifiés :</span><span class="sxs-lookup"><span data-stu-id="36b6c-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="36b6c-164">Vous pouvez refuser l’authentification au niveau de la page Razor, du contrôleur ou de la méthode d' `[AllowAnonymous]` action avec l’attribut.</span><span class="sxs-lookup"><span data-stu-id="36b6c-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="36b6c-165">La définition de la stratégie d’authentification par défaut pour exiger que les utilisateurs soient authentifiés protège les Razor Pages et les contrôleurs nouvellement ajoutés.</span><span class="sxs-lookup"><span data-stu-id="36b6c-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="36b6c-166">L’authentification requise par défaut est plus sécurisée que le fait de s’appuyer sur de nouveaux contrôleurs et Razor Pages d’inclure l' `[Authorize]` attribut.</span><span class="sxs-lookup"><span data-stu-id="36b6c-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="36b6c-167">Ajoutez [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) aux pages d’index et de confidentialité afin que les utilisateurs anonymes puissent obtenir des informations sur le site avant de s’inscrire.</span><span class="sxs-lookup"><span data-stu-id="36b6c-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="36b6c-168">Configurer le compte de test</span><span class="sxs-lookup"><span data-stu-id="36b6c-168">Configure the test account</span></span>

<span data-ttu-id="36b6c-169">La `SeedData` classe crée deux comptes : administrateur et gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="36b6c-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="36b6c-170">Utilisez l' [outil secret Manager](xref:security/app-secrets) pour définir un mot de passe pour ces comptes.</span><span class="sxs-lookup"><span data-stu-id="36b6c-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="36b6c-171">Définissez le mot de passe à partir du répertoire du projet (répertoire contenant *Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="36b6c-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="36b6c-172">Si aucun mot de passe fort n’est spécifié, une exception est `SeedData.Initialize` levée lorsque la méthode est appelée.</span><span class="sxs-lookup"><span data-stu-id="36b6c-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="36b6c-173">Mettre `Main` à jour pour utiliser le mot de passe de test :</span><span class="sxs-lookup"><span data-stu-id="36b6c-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="36b6c-174">Créer les comptes de test et mettre à jour les contacts</span><span class="sxs-lookup"><span data-stu-id="36b6c-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="36b6c-175">Mettez à `Initialize` jour la méthode `SeedData` dans la classe pour créer les comptes de test :</span><span class="sxs-lookup"><span data-stu-id="36b6c-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="36b6c-176">Ajoutez l’ID d’utilisateur administrateur `ContactStatus` et les contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="36b6c-177">Faites de l’un des contacts « soumis » et un « rejeté ».</span><span class="sxs-lookup"><span data-stu-id="36b6c-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="36b6c-178">Ajoutez l’ID d’utilisateur et l’État à tous les contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="36b6c-179">Un seul contact est affiché :</span><span class="sxs-lookup"><span data-stu-id="36b6c-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="36b6c-180">Créer des gestionnaires d’autorisation propriétaire, responsable et administrateur</span><span class="sxs-lookup"><span data-stu-id="36b6c-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="36b6c-181">Créez une `ContactIsOwnerAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="36b6c-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="36b6c-182">Le `ContactIsOwnerAuthorizationHandler` vérifie que l’utilisateur agissant sur une ressource possède la ressource.</span><span class="sxs-lookup"><span data-stu-id="36b6c-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="36b6c-183">Contexte `ContactIsOwnerAuthorizationHandler` des appels [. Réussie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si l’utilisateur authentifié actuel est le propriétaire du contact.</span><span class="sxs-lookup"><span data-stu-id="36b6c-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="36b6c-184">Les gestionnaires d’autorisations sont généralement :</span><span class="sxs-lookup"><span data-stu-id="36b6c-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="36b6c-185">Retourne `context.Succeed` lorsque les spécifications sont satisfaites.</span><span class="sxs-lookup"><span data-stu-id="36b6c-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="36b6c-186">Retourne `Task.CompletedTask` lorsque les spécifications ne sont pas remplies.</span><span class="sxs-lookup"><span data-stu-id="36b6c-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="36b6c-187">`Task.CompletedTask`n’est pas un succès&mdash;ou un échec. il permet l’exécution d’autres gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="36b6c-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="36b6c-188">Si vous devez faire échouer explicitement, retournez le [contexte. Échoue](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="36b6c-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="36b6c-189">L’application permet aux propriétaires de contacts de modifier/supprimer/créer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="36b6c-190">`ContactIsOwnerAuthorizationHandler`n’a pas besoin de vérifier l’opération passée dans le paramètre d’exigence.</span><span class="sxs-lookup"><span data-stu-id="36b6c-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="36b6c-191">Créer un gestionnaire d’autorisations de gestionnaire</span><span class="sxs-lookup"><span data-stu-id="36b6c-191">Create a manager authorization handler</span></span>

<span data-ttu-id="36b6c-192">Créez une `ContactManagerAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="36b6c-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="36b6c-193">Le `ContactManagerAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un responsable.</span><span class="sxs-lookup"><span data-stu-id="36b6c-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="36b6c-194">Seuls les responsables peuvent approuver ou rejeter les modifications de contenu (nouveaux ou modifiés).</span><span class="sxs-lookup"><span data-stu-id="36b6c-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="36b6c-195">Créer un gestionnaire d’autorisations d’administrateur</span><span class="sxs-lookup"><span data-stu-id="36b6c-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="36b6c-196">Créez une `ContactAdministratorsAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="36b6c-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="36b6c-197">Le `ContactAdministratorsAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un administrateur.</span><span class="sxs-lookup"><span data-stu-id="36b6c-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="36b6c-198">L’administrateur peut effectuer toutes les opérations.</span><span class="sxs-lookup"><span data-stu-id="36b6c-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="36b6c-199">Inscrire les gestionnaires d’autorisations</span><span class="sxs-lookup"><span data-stu-id="36b6c-199">Register the authorization handlers</span></span>

<span data-ttu-id="36b6c-200">Les services qui utilisent Entity Framework Core doivent être inscrits pour l' [injection de dépendances](xref:fundamentals/dependency-injection) à l’aide de [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="36b6c-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="36b6c-201">`ContactIsOwnerAuthorizationHandler` Utilise ASP.net Core [identité](xref:security/authentication/identity), qui repose sur Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="36b6c-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="36b6c-202">Inscrire les gestionnaires auprès de la collection de services afin qu’ils soient disponibles `ContactsController` pour le via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="36b6c-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="36b6c-203">Ajoutez le code suivant à la fin de `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36b6c-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="36b6c-204">`ContactAdministratorsAuthorizationHandler`et `ContactManagerAuthorizationHandler` sont ajoutés en tant que singletons.</span><span class="sxs-lookup"><span data-stu-id="36b6c-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="36b6c-205">Il s’agit de singletons, car ils n’utilisent pas EF et toutes les informations `Context` nécessaires sont dans `HandleRequirementAsync` le paramètre de la méthode.</span><span class="sxs-lookup"><span data-stu-id="36b6c-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="36b6c-206">Autorisation du support</span><span class="sxs-lookup"><span data-stu-id="36b6c-206">Support authorization</span></span>

<span data-ttu-id="36b6c-207">Dans cette section, vous allez mettre à jour les Razor Pages et ajouter une classe d’exigences d’opérations.</span><span class="sxs-lookup"><span data-stu-id="36b6c-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="36b6c-208">Examiner la classe des exigences relatives aux opérations de contact</span><span class="sxs-lookup"><span data-stu-id="36b6c-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="36b6c-209">Passez en `ContactOperations` revue la classe.</span><span class="sxs-lookup"><span data-stu-id="36b6c-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="36b6c-210">Cette classe contient les spécifications prises en charge par l’application :</span><span class="sxs-lookup"><span data-stu-id="36b6c-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="36b6c-211">Créer une classe de base pour le Razor Pages contacts</span><span class="sxs-lookup"><span data-stu-id="36b6c-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="36b6c-212">Créez une classe de base qui contient les services utilisés dans le Razor Pages contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="36b6c-213">La classe de base place le code d’initialisation à un emplacement :</span><span class="sxs-lookup"><span data-stu-id="36b6c-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="36b6c-214">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="36b6c-214">The preceding code:</span></span>

* <span data-ttu-id="36b6c-215">Ajoute le `IAuthorizationService` service pour accéder aux gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="36b6c-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="36b6c-216">Ajoute le service `UserManager` d’identité.</span><span class="sxs-lookup"><span data-stu-id="36b6c-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="36b6c-217">Ajoutez la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="36b6c-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="36b6c-218">Mettre à jour CreateModel</span><span class="sxs-lookup"><span data-stu-id="36b6c-218">Update the CreateModel</span></span>

<span data-ttu-id="36b6c-219">Mettez à jour le constructeur de modèle de page `DI_BasePageModel` de création pour utiliser la classe de base :</span><span class="sxs-lookup"><span data-stu-id="36b6c-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="36b6c-220">Mettez à `CreateModel.OnPostAsync` jour la méthode pour :</span><span class="sxs-lookup"><span data-stu-id="36b6c-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="36b6c-221">Ajoutez l’ID utilisateur au `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="36b6c-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="36b6c-222">Appelez le gestionnaire d’autorisations pour vérifier que l’utilisateur a l’autorisation de créer des contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="36b6c-223">Mettre à jour IndexModel</span><span class="sxs-lookup"><span data-stu-id="36b6c-223">Update the IndexModel</span></span>

<span data-ttu-id="36b6c-224">Mettez à `OnGetAsync` jour la méthode afin que seuls les contacts approuvés soient affichés pour les utilisateurs généraux :</span><span class="sxs-lookup"><span data-stu-id="36b6c-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="36b6c-225">Mettre à jour EditModel</span><span class="sxs-lookup"><span data-stu-id="36b6c-225">Update the EditModel</span></span>

<span data-ttu-id="36b6c-226">Ajoutez un gestionnaire d’autorisations pour vérifier que l’utilisateur possède le contact.</span><span class="sxs-lookup"><span data-stu-id="36b6c-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="36b6c-227">Étant donné que l’autorisation des ressources est `[Authorize]` en cours de validation, l’attribut n’est pas suffisant.</span><span class="sxs-lookup"><span data-stu-id="36b6c-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="36b6c-228">L’application n’a pas accès à la ressource lors de l’évaluation des attributs.</span><span class="sxs-lookup"><span data-stu-id="36b6c-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="36b6c-229">L’autorisation basée sur les ressources doit être impérative.</span><span class="sxs-lookup"><span data-stu-id="36b6c-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="36b6c-230">Les vérifications doivent être effectuées une fois que l’application a accès à la ressource, soit en la chargeant dans le modèle de page, soit en la chargeant dans le gestionnaire lui-même.</span><span class="sxs-lookup"><span data-stu-id="36b6c-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="36b6c-231">Vous accédez fréquemment à la ressource en passant la clé de ressource.</span><span class="sxs-lookup"><span data-stu-id="36b6c-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="36b6c-232">Mettre à jour DeleteModel</span><span class="sxs-lookup"><span data-stu-id="36b6c-232">Update the DeleteModel</span></span>

<span data-ttu-id="36b6c-233">Mettez à jour le modèle de page de suppression pour utiliser le gestionnaire d’autorisations afin de vérifier que l’utilisateur dispose de l’autorisation de suppression sur le contact.</span><span class="sxs-lookup"><span data-stu-id="36b6c-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="36b6c-234">Injecter le service d’autorisation dans les vues</span><span class="sxs-lookup"><span data-stu-id="36b6c-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="36b6c-235">Actuellement, l’interface utilisateur affiche les liens modifier et supprimer pour les contacts que l’utilisateur ne peut pas modifier.</span><span class="sxs-lookup"><span data-stu-id="36b6c-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="36b6c-236">Injecter le service d’autorisation dans le fichier *pages/_ViewImports. cshtml* afin qu’il soit disponible pour tous les affichages :</span><span class="sxs-lookup"><span data-stu-id="36b6c-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="36b6c-237">Le balisage précédent ajoute `using` plusieurs instructions.</span><span class="sxs-lookup"><span data-stu-id="36b6c-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="36b6c-238">Mettez à jour les liens **modifier** et **supprimer** dans *pages/contacts/index. cshtml* afin qu’ils soient affichés uniquement pour les utilisateurs disposant des autorisations appropriées :</span><span class="sxs-lookup"><span data-stu-id="36b6c-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="36b6c-239">Le masquage des liens des utilisateurs qui n’ont pas l’autorisation de modifier des données ne sécurise pas l’application.</span><span class="sxs-lookup"><span data-stu-id="36b6c-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="36b6c-240">Le masquage des liens rend l’application plus conviviale en affichant uniquement des liens valides.</span><span class="sxs-lookup"><span data-stu-id="36b6c-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="36b6c-241">Les utilisateurs peuvent pirater les URL générées pour appeler des opérations de modification et de suppression sur les données qu’ils ne possèdent pas.</span><span class="sxs-lookup"><span data-stu-id="36b6c-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="36b6c-242">La page ou le contrôleur Razor doit appliquer les vérifications d’accès pour sécuriser les données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="36b6c-243">Mettre à jour les détails</span><span class="sxs-lookup"><span data-stu-id="36b6c-243">Update Details</span></span>

<span data-ttu-id="36b6c-244">Mettez à jour la vue Détails pour permettre aux responsables d’approuver ou de rejeter les contacts :</span><span class="sxs-lookup"><span data-stu-id="36b6c-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="36b6c-245">Mettez à jour le modèle de page de détails :</span><span class="sxs-lookup"><span data-stu-id="36b6c-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="36b6c-246">Ajouter ou supprimer un utilisateur dans un rôle</span><span class="sxs-lookup"><span data-stu-id="36b6c-246">Add or remove a user to a role</span></span>

<span data-ttu-id="36b6c-247">Consultez [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/8502) pour plus d’informations sur :</span><span class="sxs-lookup"><span data-stu-id="36b6c-247">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="36b6c-248">Suppression des privilèges d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="36b6c-248">Removing privileges from a user.</span></span> <span data-ttu-id="36b6c-249">Par exemple, la désactivation d’un utilisateur dans une application de conversation.</span><span class="sxs-lookup"><span data-stu-id="36b6c-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="36b6c-250">Ajout de privilèges à un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="36b6c-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="36b6c-251">Différences entre Challenge et interdire</span><span class="sxs-lookup"><span data-stu-id="36b6c-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="36b6c-252">Cette application définit la stratégie par défaut pour [exiger des utilisateurs authentifiés](#require-authenticated-users).</span><span class="sxs-lookup"><span data-stu-id="36b6c-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="36b6c-253">Le code suivant autorise les utilisateurs anonymes.</span><span class="sxs-lookup"><span data-stu-id="36b6c-253">The following code allows anonymous users.</span></span> <span data-ttu-id="36b6c-254">Les utilisateurs anonymes sont autorisés à afficher les différences entre la stimulation et l’interdiction.</span><span class="sxs-lookup"><span data-stu-id="36b6c-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="36b6c-255">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="36b6c-255">In the preceding code:</span></span>

* <span data-ttu-id="36b6c-256">Lorsque l’utilisateur n’est **pas** authentifié, un `ChallengeResult` est retourné.</span><span class="sxs-lookup"><span data-stu-id="36b6c-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="36b6c-257">Lorsqu’un `ChallengeResult` est retourné, l’utilisateur est redirigé vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="36b6c-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="36b6c-258">Lorsque l’utilisateur est authentifié, mais n’est pas autorisé, `ForbidResult` un est retourné.</span><span class="sxs-lookup"><span data-stu-id="36b6c-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="36b6c-259">Lorsqu’un `ForbidResult` est retourné, l’utilisateur est redirigé vers la page accès refusé.</span><span class="sxs-lookup"><span data-stu-id="36b6c-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="36b6c-260">Tester l’application terminée</span><span class="sxs-lookup"><span data-stu-id="36b6c-260">Test the completed app</span></span>

<span data-ttu-id="36b6c-261">Si vous n’avez pas encore défini de mot de passe pour les comptes d’utilisateurs amorcés, utilisez l' [outil secret Manager](xref:security/app-secrets#secret-manager) pour définir un mot de passe :</span><span class="sxs-lookup"><span data-stu-id="36b6c-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="36b6c-262">Choisissez un mot de passe fort : utilisez au moins huit caractères et au moins un caractère majuscule, un chiffre et un symbole.</span><span class="sxs-lookup"><span data-stu-id="36b6c-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="36b6c-263">Par exemple, `Passw0rd!` répond aux exigences de mot de passe fort.</span><span class="sxs-lookup"><span data-stu-id="36b6c-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="36b6c-264">Exécutez la commande suivante à partir du dossier du projet, `<PW>` où est le mot de passe :</span><span class="sxs-lookup"><span data-stu-id="36b6c-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="36b6c-265">Si l’application a des contacts :</span><span class="sxs-lookup"><span data-stu-id="36b6c-265">If the app has contacts:</span></span>

* <span data-ttu-id="36b6c-266">Supprimez tous les enregistrements de la `Contact` table.</span><span class="sxs-lookup"><span data-stu-id="36b6c-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="36b6c-267">Redémarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="36b6c-268">Un moyen simple de tester l’application terminée consiste à lancer trois navigateurs différents (ou des sessions incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="36b6c-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="36b6c-269">Dans un navigateur, inscrivez un nouvel utilisateur (par exemple, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="36b6c-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="36b6c-270">Connectez-vous à chaque navigateur avec un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="36b6c-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="36b6c-271">Vérifiez les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="36b6c-271">Verify the following operations:</span></span>

* <span data-ttu-id="36b6c-272">Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.</span><span class="sxs-lookup"><span data-stu-id="36b6c-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="36b6c-273">Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="36b6c-274">Les responsables peuvent approuver/rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="36b6c-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="36b6c-275">La `Details` vue affiche les boutons **approuver** et **rejeter** .</span><span class="sxs-lookup"><span data-stu-id="36b6c-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="36b6c-276">Les administrateurs peuvent approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="36b6c-277">Utilisateur</span><span class="sxs-lookup"><span data-stu-id="36b6c-277">User</span></span>                | <span data-ttu-id="36b6c-278">Amorcé par l’application</span><span class="sxs-lookup"><span data-stu-id="36b6c-278">Seeded by the app</span></span> | <span data-ttu-id="36b6c-279">Options</span><span class="sxs-lookup"><span data-stu-id="36b6c-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="36b6c-280">Non </span><span class="sxs-lookup"><span data-stu-id="36b6c-280">No</span></span>                | <span data-ttu-id="36b6c-281">Modifiez/supprimez les données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="36b6c-282">Oui</span><span class="sxs-lookup"><span data-stu-id="36b6c-282">Yes</span></span>               | <span data-ttu-id="36b6c-283">Approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="36b6c-284">Oui</span><span class="sxs-lookup"><span data-stu-id="36b6c-284">Yes</span></span>               | <span data-ttu-id="36b6c-285">Approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="36b6c-286">Créez un contact dans le navigateur de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="36b6c-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="36b6c-287">Copiez l’URL de la suppression et de la modification à partir du contact de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="36b6c-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="36b6c-288">Collez ces liens dans le navigateur de l’utilisateur de test pour vérifier que l’utilisateur de test ne peut pas effectuer ces opérations.</span><span class="sxs-lookup"><span data-stu-id="36b6c-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="36b6c-289">Créer l’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="36b6c-289">Create the starter app</span></span>

* <span data-ttu-id="36b6c-290">Créer une application Razor Pages nommée « ContactManager »</span><span class="sxs-lookup"><span data-stu-id="36b6c-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="36b6c-291">Créez l’application avec des **comptes d’utilisateur individuels**.</span><span class="sxs-lookup"><span data-stu-id="36b6c-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="36b6c-292">Nommez-la « ContactManager » pour que l’espace de noms corresponde à l’espace de noms utilisé dans l’exemple.</span><span class="sxs-lookup"><span data-stu-id="36b6c-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="36b6c-293">`-uld`spécifie la base de données locale au lieu de SQLite</span><span class="sxs-lookup"><span data-stu-id="36b6c-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="36b6c-294">Ajouter des *modèles/contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="36b6c-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="36b6c-295">Structurez `Contact` le modèle.</span><span class="sxs-lookup"><span data-stu-id="36b6c-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="36b6c-296">Créez la migration initiale et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="36b6c-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="36b6c-297">Si vous rencontrez un bogue avec `dotnet aspnet-codegenerator razorpage` la commande, consultez [ce problème GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="36b6c-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="36b6c-298">Mettez à jour l’ancre **ContactManager** dans le fichier *pages/shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="36b6c-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="36b6c-299">Tester l’application en créant, en modifiant et en supprimant un contact</span><span class="sxs-lookup"><span data-stu-id="36b6c-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="36b6c-300">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="36b6c-300">Seed the database</span></span>

<span data-ttu-id="36b6c-301">Ajoutez la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) au dossier *Data* :</span><span class="sxs-lookup"><span data-stu-id="36b6c-301">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="36b6c-302">Appeler `SeedData.Initialize` à `Main`partir de :</span><span class="sxs-lookup"><span data-stu-id="36b6c-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="36b6c-303">Vérifiez que l’application a amorcé la base de données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-303">Test that the app seeded the database.</span></span> <span data-ttu-id="36b6c-304">Si la base de coordonnées contient des lignes, la méthode Seed ne s’exécute pas.</span><span class="sxs-lookup"><span data-stu-id="36b6c-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="36b6c-305">Ce didacticiel montre comment créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="36b6c-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="36b6c-306">Il affiche la liste des contacts que les utilisateurs authentifiés (inscrits) ont créés.</span><span class="sxs-lookup"><span data-stu-id="36b6c-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="36b6c-307">Il existe trois groupes de sécurité :</span><span class="sxs-lookup"><span data-stu-id="36b6c-307">There are three security groups:</span></span>

* <span data-ttu-id="36b6c-308">Les **utilisateurs inscrits** peuvent afficher toutes les données approuvées et peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="36b6c-309">Les **responsables** peuvent approuver ou rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="36b6c-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="36b6c-310">Seuls les contacts approuvés sont visibles pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="36b6c-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="36b6c-311">**Les administrateurs** peuvent approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="36b6c-312">Dans l’image suivante, l’utilisateur Rick`rick@example.com`() est connecté.</span><span class="sxs-lookup"><span data-stu-id="36b6c-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="36b6c-313">Rick peut uniquement afficher les contacts approuvés et **modifier**/**supprimer**/**créer** des liens pour ses contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="36b6c-314">Seul le dernier enregistrement créé par Rick affiche des liens **modifier** et **supprimer** .</span><span class="sxs-lookup"><span data-stu-id="36b6c-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="36b6c-315">Les autres utilisateurs ne verront pas le dernier enregistrement jusqu’à ce qu’un responsable ou un administrateur change l’État en « approuvé ».</span><span class="sxs-lookup"><span data-stu-id="36b6c-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Capture d’écran montrant que Rick est connecté](secure-data/_static/rick.png)

<span data-ttu-id="36b6c-317">Dans l’image suivante, `manager@contoso.com` est connecté et dans le rôle du responsable :</span><span class="sxs-lookup"><span data-stu-id="36b6c-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Capture d' manager@contoso.com écran montrant la connexion](secure-data/_static/manager1.png)

<span data-ttu-id="36b6c-319">L’illustration suivante montre la vue des détails du gestionnaire d’un contact :</span><span class="sxs-lookup"><span data-stu-id="36b6c-319">The following image shows the managers details view of a contact:</span></span>

![Vue du gestionnaire d’un contact](secure-data/_static/manager.png)

<span data-ttu-id="36b6c-321">Les boutons **approuver** et **rejeter** s’affichent uniquement pour les responsables et les administrateurs.</span><span class="sxs-lookup"><span data-stu-id="36b6c-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="36b6c-322">Dans l’image suivante, `admin@contoso.com` est connecté et dans le rôle de l’administrateur :</span><span class="sxs-lookup"><span data-stu-id="36b6c-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Capture d' admin@contoso.com écran montrant la connexion](secure-data/_static/admin.png)

<span data-ttu-id="36b6c-324">L’administrateur dispose de tous les privilèges.</span><span class="sxs-lookup"><span data-stu-id="36b6c-324">The administrator has all privileges.</span></span> <span data-ttu-id="36b6c-325">Elle peut lire/modifier/supprimer un contact et modifier l’état des contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="36b6c-326">L’application a été créée par la [génération](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) de `Contact` modèles automatique du modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="36b6c-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="36b6c-327">L’exemple contient les gestionnaires d’autorisations suivants :</span><span class="sxs-lookup"><span data-stu-id="36b6c-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="36b6c-328">`ContactIsOwnerAuthorizationHandler`: Garantit qu’un utilisateur peut uniquement modifier ses données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="36b6c-329">`ContactManagerAuthorizationHandler`: Permet aux gestionnaires d’approuver ou de rejeter les contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="36b6c-330">`ContactAdministratorsAuthorizationHandler`: Permet aux administrateurs d’approuver ou de refuser des contacts, ainsi que de modifier/supprimer des contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="36b6c-331">Prérequis</span><span class="sxs-lookup"><span data-stu-id="36b6c-331">Prerequisites</span></span>

<span data-ttu-id="36b6c-332">Ce didacticiel est avancé.</span><span class="sxs-lookup"><span data-stu-id="36b6c-332">This tutorial is advanced.</span></span> <span data-ttu-id="36b6c-333">Vous devez connaître les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="36b6c-333">You should be familiar with:</span></span>

* [<span data-ttu-id="36b6c-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36b6c-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="36b6c-335">Authentification</span><span class="sxs-lookup"><span data-stu-id="36b6c-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="36b6c-336">Confirmation de compte et récupération de mot de passe</span><span class="sxs-lookup"><span data-stu-id="36b6c-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="36b6c-337">Autorisation</span><span class="sxs-lookup"><span data-stu-id="36b6c-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="36b6c-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="36b6c-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="36b6c-339">L’application de démarrage et la fin de l’application</span><span class="sxs-lookup"><span data-stu-id="36b6c-339">The starter and completed app</span></span>

<span data-ttu-id="36b6c-340">[Téléchargez](xref:index#how-to-download-a-sample) l’application [terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="36b6c-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="36b6c-341">[Testez](#test-the-completed-app) l’application terminée afin de vous familiariser avec ses fonctionnalités de sécurité.</span><span class="sxs-lookup"><span data-stu-id="36b6c-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="36b6c-342">L’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="36b6c-342">The starter app</span></span>

<span data-ttu-id="36b6c-343">[Téléchargez](xref:index#how-to-download-a-sample) l’application de [démarrage](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="36b6c-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="36b6c-344">Exécutez l’application, appuyez sur le lien **ContactManager** et vérifiez que vous pouvez créer, modifier et supprimer un contact.</span><span class="sxs-lookup"><span data-stu-id="36b6c-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="36b6c-345">Sécuriser les données utilisateur</span><span class="sxs-lookup"><span data-stu-id="36b6c-345">Secure user data</span></span>

<span data-ttu-id="36b6c-346">Les sections suivantes présentent les principales étapes à suivre pour créer l’application de données utilisateur sécurisé.</span><span class="sxs-lookup"><span data-stu-id="36b6c-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="36b6c-347">Il peut s’avérer utile de faire référence au projet terminé.</span><span class="sxs-lookup"><span data-stu-id="36b6c-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="36b6c-348">Lier les données de contact à l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="36b6c-348">Tie the contact data to the user</span></span>

<span data-ttu-id="36b6c-349">Utilisez l’IDENTIFIant utilisateur de l' [identité](xref:security/authentication/identity) ASP.net pour vous assurer que les utilisateurs peuvent modifier leurs données, mais pas les données d’autres utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="36b6c-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="36b6c-350">Ajoutez `OwnerID` et `ContactStatus` au `Contact` modèle :</span><span class="sxs-lookup"><span data-stu-id="36b6c-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="36b6c-351">`OwnerID`ID de l’utilisateur de la `AspNetUser` table dans la base de données d' [identité](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="36b6c-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="36b6c-352">Le `Status` champ détermine si un contact est visible par les utilisateurs généraux.</span><span class="sxs-lookup"><span data-stu-id="36b6c-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="36b6c-353">Créez une nouvelle migration et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="36b6c-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="36b6c-354">Ajouter des services de rôle à l’identité</span><span class="sxs-lookup"><span data-stu-id="36b6c-354">Add Role services to Identity</span></span>

<span data-ttu-id="36b6c-355">Ajoutez [rôles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pour ajouter des services de rôle :</span><span class="sxs-lookup"><span data-stu-id="36b6c-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="36b6c-356">Exiger des utilisateurs authentifiés</span><span class="sxs-lookup"><span data-stu-id="36b6c-356">Require authenticated users</span></span>

<span data-ttu-id="36b6c-357">Définissez la stratégie d’authentification par défaut pour exiger que les utilisateurs soient authentifiés :</span><span class="sxs-lookup"><span data-stu-id="36b6c-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="36b6c-358">Vous pouvez refuser l’authentification au niveau de la page Razor, du contrôleur ou de la méthode d' `[AllowAnonymous]` action avec l’attribut.</span><span class="sxs-lookup"><span data-stu-id="36b6c-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="36b6c-359">La définition de la stratégie d’authentification par défaut pour exiger que les utilisateurs soient authentifiés protège les Razor Pages et les contrôleurs nouvellement ajoutés.</span><span class="sxs-lookup"><span data-stu-id="36b6c-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="36b6c-360">L’authentification requise par défaut est plus sécurisée que le fait de s’appuyer sur de nouveaux contrôleurs et Razor Pages d’inclure l' `[Authorize]` attribut.</span><span class="sxs-lookup"><span data-stu-id="36b6c-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="36b6c-361">Ajoutez [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) aux pages d’index, à propos de et contact afin que les utilisateurs anonymes puissent obtenir des informations sur le site avant de s’inscrire.</span><span class="sxs-lookup"><span data-stu-id="36b6c-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="36b6c-362">Configurer le compte de test</span><span class="sxs-lookup"><span data-stu-id="36b6c-362">Configure the test account</span></span>

<span data-ttu-id="36b6c-363">La `SeedData` classe crée deux comptes : administrateur et gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="36b6c-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="36b6c-364">Utilisez l' [outil secret Manager](xref:security/app-secrets) pour définir un mot de passe pour ces comptes.</span><span class="sxs-lookup"><span data-stu-id="36b6c-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="36b6c-365">Définissez le mot de passe à partir du répertoire du projet (répertoire contenant *Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="36b6c-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="36b6c-366">Si aucun mot de passe fort n’est spécifié, une exception est `SeedData.Initialize` levée lorsque la méthode est appelée.</span><span class="sxs-lookup"><span data-stu-id="36b6c-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="36b6c-367">Mettre `Main` à jour pour utiliser le mot de passe de test :</span><span class="sxs-lookup"><span data-stu-id="36b6c-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="36b6c-368">Créer les comptes de test et mettre à jour les contacts</span><span class="sxs-lookup"><span data-stu-id="36b6c-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="36b6c-369">Mettez à `Initialize` jour la méthode `SeedData` dans la classe pour créer les comptes de test :</span><span class="sxs-lookup"><span data-stu-id="36b6c-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="36b6c-370">Ajoutez l’ID d’utilisateur administrateur `ContactStatus` et les contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="36b6c-371">Faites de l’un des contacts « soumis » et un « rejeté ».</span><span class="sxs-lookup"><span data-stu-id="36b6c-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="36b6c-372">Ajoutez l’ID d’utilisateur et l’État à tous les contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="36b6c-373">Un seul contact est affiché :</span><span class="sxs-lookup"><span data-stu-id="36b6c-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="36b6c-374">Créer des gestionnaires d’autorisation propriétaire, responsable et administrateur</span><span class="sxs-lookup"><span data-stu-id="36b6c-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="36b6c-375">Créez un dossier *authorization* et créez `ContactIsOwnerAuthorizationHandler` une classe dans celui-ci.</span><span class="sxs-lookup"><span data-stu-id="36b6c-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="36b6c-376">Le `ContactIsOwnerAuthorizationHandler` vérifie que l’utilisateur agissant sur une ressource possède la ressource.</span><span class="sxs-lookup"><span data-stu-id="36b6c-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="36b6c-377">Contexte `ContactIsOwnerAuthorizationHandler` des appels [. Réussie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si l’utilisateur authentifié actuel est le propriétaire du contact.</span><span class="sxs-lookup"><span data-stu-id="36b6c-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="36b6c-378">Les gestionnaires d’autorisations sont généralement :</span><span class="sxs-lookup"><span data-stu-id="36b6c-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="36b6c-379">Retourne `context.Succeed` lorsque les spécifications sont satisfaites.</span><span class="sxs-lookup"><span data-stu-id="36b6c-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="36b6c-380">Retourne `Task.CompletedTask` lorsque les spécifications ne sont pas remplies.</span><span class="sxs-lookup"><span data-stu-id="36b6c-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="36b6c-381">`Task.CompletedTask`n’est pas un succès&mdash;ou un échec. il permet l’exécution d’autres gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="36b6c-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="36b6c-382">Si vous devez faire échouer explicitement, retournez le [contexte. Échoue](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="36b6c-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="36b6c-383">L’application permet aux propriétaires de contacts de modifier/supprimer/créer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="36b6c-384">`ContactIsOwnerAuthorizationHandler`n’a pas besoin de vérifier l’opération passée dans le paramètre d’exigence.</span><span class="sxs-lookup"><span data-stu-id="36b6c-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="36b6c-385">Créer un gestionnaire d’autorisations de gestionnaire</span><span class="sxs-lookup"><span data-stu-id="36b6c-385">Create a manager authorization handler</span></span>

<span data-ttu-id="36b6c-386">Créez une `ContactManagerAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="36b6c-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="36b6c-387">Le `ContactManagerAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un responsable.</span><span class="sxs-lookup"><span data-stu-id="36b6c-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="36b6c-388">Seuls les responsables peuvent approuver ou rejeter les modifications de contenu (nouveaux ou modifiés).</span><span class="sxs-lookup"><span data-stu-id="36b6c-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="36b6c-389">Créer un gestionnaire d’autorisations d’administrateur</span><span class="sxs-lookup"><span data-stu-id="36b6c-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="36b6c-390">Créez une `ContactAdministratorsAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="36b6c-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="36b6c-391">Le `ContactAdministratorsAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un administrateur.</span><span class="sxs-lookup"><span data-stu-id="36b6c-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="36b6c-392">L’administrateur peut effectuer toutes les opérations.</span><span class="sxs-lookup"><span data-stu-id="36b6c-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="36b6c-393">Inscrire les gestionnaires d’autorisations</span><span class="sxs-lookup"><span data-stu-id="36b6c-393">Register the authorization handlers</span></span>

<span data-ttu-id="36b6c-394">Les services qui utilisent Entity Framework Core doivent être inscrits pour l' [injection de dépendances](xref:fundamentals/dependency-injection) à l’aide de [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="36b6c-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="36b6c-395">`ContactIsOwnerAuthorizationHandler` Utilise ASP.net Core [identité](xref:security/authentication/identity), qui repose sur Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="36b6c-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="36b6c-396">Inscrire les gestionnaires auprès de la collection de services afin qu’ils soient disponibles `ContactsController` pour le via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="36b6c-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="36b6c-397">Ajoutez le code suivant à la fin de `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36b6c-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="36b6c-398">`ContactAdministratorsAuthorizationHandler`et `ContactManagerAuthorizationHandler` sont ajoutés en tant que singletons.</span><span class="sxs-lookup"><span data-stu-id="36b6c-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="36b6c-399">Il s’agit de singletons, car ils n’utilisent pas EF et toutes les informations `Context` nécessaires sont dans `HandleRequirementAsync` le paramètre de la méthode.</span><span class="sxs-lookup"><span data-stu-id="36b6c-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="36b6c-400">Autorisation du support</span><span class="sxs-lookup"><span data-stu-id="36b6c-400">Support authorization</span></span>

<span data-ttu-id="36b6c-401">Dans cette section, vous allez mettre à jour les Razor Pages et ajouter une classe d’exigences d’opérations.</span><span class="sxs-lookup"><span data-stu-id="36b6c-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="36b6c-402">Examiner la classe des exigences relatives aux opérations de contact</span><span class="sxs-lookup"><span data-stu-id="36b6c-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="36b6c-403">Passez en `ContactOperations` revue la classe.</span><span class="sxs-lookup"><span data-stu-id="36b6c-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="36b6c-404">Cette classe contient les spécifications prises en charge par l’application :</span><span class="sxs-lookup"><span data-stu-id="36b6c-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="36b6c-405">Créer une classe de base pour le Razor Pages contacts</span><span class="sxs-lookup"><span data-stu-id="36b6c-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="36b6c-406">Créez une classe de base qui contient les services utilisés dans le Razor Pages contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="36b6c-407">La classe de base place le code d’initialisation à un emplacement :</span><span class="sxs-lookup"><span data-stu-id="36b6c-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="36b6c-408">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="36b6c-408">The preceding code:</span></span>

* <span data-ttu-id="36b6c-409">Ajoute le `IAuthorizationService` service pour accéder aux gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="36b6c-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="36b6c-410">Ajoute le service `UserManager` d’identité.</span><span class="sxs-lookup"><span data-stu-id="36b6c-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="36b6c-411">Ajoutez la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="36b6c-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="36b6c-412">Mettre à jour CreateModel</span><span class="sxs-lookup"><span data-stu-id="36b6c-412">Update the CreateModel</span></span>

<span data-ttu-id="36b6c-413">Mettez à jour le constructeur de modèle de page `DI_BasePageModel` de création pour utiliser la classe de base :</span><span class="sxs-lookup"><span data-stu-id="36b6c-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="36b6c-414">Mettez à `CreateModel.OnPostAsync` jour la méthode pour :</span><span class="sxs-lookup"><span data-stu-id="36b6c-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="36b6c-415">Ajoutez l’ID utilisateur au `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="36b6c-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="36b6c-416">Appelez le gestionnaire d’autorisations pour vérifier que l’utilisateur a l’autorisation de créer des contacts.</span><span class="sxs-lookup"><span data-stu-id="36b6c-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="36b6c-417">Mettre à jour IndexModel</span><span class="sxs-lookup"><span data-stu-id="36b6c-417">Update the IndexModel</span></span>

<span data-ttu-id="36b6c-418">Mettez à `OnGetAsync` jour la méthode afin que seuls les contacts approuvés soient affichés pour les utilisateurs généraux :</span><span class="sxs-lookup"><span data-stu-id="36b6c-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="36b6c-419">Mettre à jour EditModel</span><span class="sxs-lookup"><span data-stu-id="36b6c-419">Update the EditModel</span></span>

<span data-ttu-id="36b6c-420">Ajoutez un gestionnaire d’autorisations pour vérifier que l’utilisateur possède le contact.</span><span class="sxs-lookup"><span data-stu-id="36b6c-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="36b6c-421">Étant donné que l’autorisation des ressources est `[Authorize]` en cours de validation, l’attribut n’est pas suffisant.</span><span class="sxs-lookup"><span data-stu-id="36b6c-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="36b6c-422">L’application n’a pas accès à la ressource lors de l’évaluation des attributs.</span><span class="sxs-lookup"><span data-stu-id="36b6c-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="36b6c-423">L’autorisation basée sur les ressources doit être impérative.</span><span class="sxs-lookup"><span data-stu-id="36b6c-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="36b6c-424">Les vérifications doivent être effectuées une fois que l’application a accès à la ressource, soit en la chargeant dans le modèle de page, soit en la chargeant dans le gestionnaire lui-même.</span><span class="sxs-lookup"><span data-stu-id="36b6c-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="36b6c-425">Vous accédez fréquemment à la ressource en passant la clé de ressource.</span><span class="sxs-lookup"><span data-stu-id="36b6c-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="36b6c-426">Mettre à jour DeleteModel</span><span class="sxs-lookup"><span data-stu-id="36b6c-426">Update the DeleteModel</span></span>

<span data-ttu-id="36b6c-427">Mettez à jour le modèle de page de suppression pour utiliser le gestionnaire d’autorisations afin de vérifier que l’utilisateur dispose de l’autorisation de suppression sur le contact.</span><span class="sxs-lookup"><span data-stu-id="36b6c-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="36b6c-428">Injecter le service d’autorisation dans les vues</span><span class="sxs-lookup"><span data-stu-id="36b6c-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="36b6c-429">Actuellement, l’interface utilisateur affiche les liens modifier et supprimer pour les contacts que l’utilisateur ne peut pas modifier.</span><span class="sxs-lookup"><span data-stu-id="36b6c-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="36b6c-430">Injecter le service d’autorisation dans le fichier *views/_ViewImports. cshtml* afin qu’il soit disponible pour tous les affichages :</span><span class="sxs-lookup"><span data-stu-id="36b6c-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="36b6c-431">Le balisage précédent ajoute `using` plusieurs instructions.</span><span class="sxs-lookup"><span data-stu-id="36b6c-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="36b6c-432">Mettez à jour les liens **modifier** et **supprimer** dans *pages/contacts/index. cshtml* afin qu’ils soient affichés uniquement pour les utilisateurs disposant des autorisations appropriées :</span><span class="sxs-lookup"><span data-stu-id="36b6c-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="36b6c-433">Le masquage des liens des utilisateurs qui n’ont pas l’autorisation de modifier des données ne sécurise pas l’application.</span><span class="sxs-lookup"><span data-stu-id="36b6c-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="36b6c-434">Le masquage des liens rend l’application plus conviviale en affichant uniquement des liens valides.</span><span class="sxs-lookup"><span data-stu-id="36b6c-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="36b6c-435">Les utilisateurs peuvent pirater les URL générées pour appeler des opérations de modification et de suppression sur les données qu’ils ne possèdent pas.</span><span class="sxs-lookup"><span data-stu-id="36b6c-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="36b6c-436">La page ou le contrôleur Razor doit appliquer les vérifications d’accès pour sécuriser les données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="36b6c-437">Mettre à jour les détails</span><span class="sxs-lookup"><span data-stu-id="36b6c-437">Update Details</span></span>

<span data-ttu-id="36b6c-438">Mettez à jour la vue Détails pour permettre aux responsables d’approuver ou de rejeter les contacts :</span><span class="sxs-lookup"><span data-stu-id="36b6c-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="36b6c-439">Mettez à jour le modèle de page de détails :</span><span class="sxs-lookup"><span data-stu-id="36b6c-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="36b6c-440">Ajouter ou supprimer un utilisateur dans un rôle</span><span class="sxs-lookup"><span data-stu-id="36b6c-440">Add or remove a user to a role</span></span>

<span data-ttu-id="36b6c-441">Consultez [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/8502) pour plus d’informations sur :</span><span class="sxs-lookup"><span data-stu-id="36b6c-441">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="36b6c-442">Suppression des privilèges d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="36b6c-442">Removing privileges from a user.</span></span> <span data-ttu-id="36b6c-443">Par exemple, la désactivation d’un utilisateur dans une application de conversation.</span><span class="sxs-lookup"><span data-stu-id="36b6c-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="36b6c-444">Ajout de privilèges à un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="36b6c-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="36b6c-445">Tester l’application terminée</span><span class="sxs-lookup"><span data-stu-id="36b6c-445">Test the completed app</span></span>

<span data-ttu-id="36b6c-446">Si vous n’avez pas encore défini de mot de passe pour les comptes d’utilisateurs amorcés, utilisez l' [outil secret Manager](xref:security/app-secrets#secret-manager) pour définir un mot de passe :</span><span class="sxs-lookup"><span data-stu-id="36b6c-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="36b6c-447">Choisissez un mot de passe fort : utilisez au moins huit caractères et au moins un caractère majuscule, un chiffre et un symbole.</span><span class="sxs-lookup"><span data-stu-id="36b6c-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="36b6c-448">Par exemple, `Passw0rd!` répond aux exigences de mot de passe fort.</span><span class="sxs-lookup"><span data-stu-id="36b6c-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="36b6c-449">Exécutez la commande suivante à partir du dossier du projet, `<PW>` où est le mot de passe :</span><span class="sxs-lookup"><span data-stu-id="36b6c-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="36b6c-450">Supprimer et mettre à jour la base de données</span><span class="sxs-lookup"><span data-stu-id="36b6c-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="36b6c-451">Redémarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="36b6c-452">Un moyen simple de tester l’application terminée consiste à lancer trois navigateurs différents (ou des sessions incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="36b6c-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="36b6c-453">Dans un navigateur, inscrivez un nouvel utilisateur (par exemple, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="36b6c-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="36b6c-454">Connectez-vous à chaque navigateur avec un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="36b6c-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="36b6c-455">Vérifiez les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="36b6c-455">Verify the following operations:</span></span>

* <span data-ttu-id="36b6c-456">Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.</span><span class="sxs-lookup"><span data-stu-id="36b6c-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="36b6c-457">Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="36b6c-458">Les responsables peuvent approuver/rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="36b6c-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="36b6c-459">La `Details` vue affiche les boutons **approuver** et **rejeter** .</span><span class="sxs-lookup"><span data-stu-id="36b6c-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="36b6c-460">Les administrateurs peuvent approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="36b6c-461">Utilisateur</span><span class="sxs-lookup"><span data-stu-id="36b6c-461">User</span></span>                | <span data-ttu-id="36b6c-462">Amorcé par l’application</span><span class="sxs-lookup"><span data-stu-id="36b6c-462">Seeded by the app</span></span> | <span data-ttu-id="36b6c-463">Options</span><span class="sxs-lookup"><span data-stu-id="36b6c-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="36b6c-464">Non </span><span class="sxs-lookup"><span data-stu-id="36b6c-464">No</span></span>                | <span data-ttu-id="36b6c-465">Modifiez/supprimez les données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="36b6c-466">Oui</span><span class="sxs-lookup"><span data-stu-id="36b6c-466">Yes</span></span>               | <span data-ttu-id="36b6c-467">Approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="36b6c-468">Oui</span><span class="sxs-lookup"><span data-stu-id="36b6c-468">Yes</span></span>               | <span data-ttu-id="36b6c-469">Approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="36b6c-470">Créez un contact dans le navigateur de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="36b6c-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="36b6c-471">Copiez l’URL de la suppression et de la modification à partir du contact de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="36b6c-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="36b6c-472">Collez ces liens dans le navigateur de l’utilisateur de test pour vérifier que l’utilisateur de test ne peut pas effectuer ces opérations.</span><span class="sxs-lookup"><span data-stu-id="36b6c-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="36b6c-473">Créer l’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="36b6c-473">Create the starter app</span></span>

* <span data-ttu-id="36b6c-474">Créer une Razor application pages nommée « ContactManager »</span><span class="sxs-lookup"><span data-stu-id="36b6c-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="36b6c-475">Créez l’application avec des **comptes d’utilisateur individuels**.</span><span class="sxs-lookup"><span data-stu-id="36b6c-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="36b6c-476">Nommez-la « ContactManager » pour que l’espace de noms corresponde à l’espace de noms utilisé dans l’exemple.</span><span class="sxs-lookup"><span data-stu-id="36b6c-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="36b6c-477">`-uld`spécifie la base de données locale au lieu de SQLite</span><span class="sxs-lookup"><span data-stu-id="36b6c-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="36b6c-478">Ajouter des *modèles/contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="36b6c-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="36b6c-479">Structurez `Contact` le modèle.</span><span class="sxs-lookup"><span data-stu-id="36b6c-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="36b6c-480">Créez la migration initiale et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="36b6c-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="36b6c-481">Mettez à jour l’ancre **ContactManager** dans le fichier *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="36b6c-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="36b6c-482">Tester l’application en créant, en modifiant et en supprimant un contact</span><span class="sxs-lookup"><span data-stu-id="36b6c-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="36b6c-483">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="36b6c-483">Seed the database</span></span>

<span data-ttu-id="36b6c-484">Ajoutez la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) au dossier de *données* .</span><span class="sxs-lookup"><span data-stu-id="36b6c-484">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="36b6c-485">Appeler `SeedData.Initialize` à `Main`partir de :</span><span class="sxs-lookup"><span data-stu-id="36b6c-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="36b6c-486">Vérifiez que l’application a amorcé la base de données.</span><span class="sxs-lookup"><span data-stu-id="36b6c-486">Test that the app seeded the database.</span></span> <span data-ttu-id="36b6c-487">Si la base de coordonnées contient des lignes, la méthode Seed ne s’exécute pas.</span><span class="sxs-lookup"><span data-stu-id="36b6c-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="36b6c-488">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="36b6c-488">Additional resources</span></span>

* [<span data-ttu-id="36b6c-489">Créer une application Web .NET Core et SQL Database dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="36b6c-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="36b6c-490">[ASP.net Core laboratoire d’autorisation](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="36b6c-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="36b6c-491">Ce laboratoire aborde plus en détail les fonctionnalités de sécurité présentées dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="36b6c-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="36b6c-492">Autorisation basée sur une stratégie personnalisée</span><span class="sxs-lookup"><span data-stu-id="36b6c-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
