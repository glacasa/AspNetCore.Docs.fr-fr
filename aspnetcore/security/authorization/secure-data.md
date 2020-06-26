---
title: Créer une application ASP.NET Core avec les données utilisateur protégées par l’autorisation
author: rick-anderson
description: Découvrez comment créer une Razor application de pages avec des données utilisateur protégées par une autorisation. Comprend le protocole HTTPs, l’authentification, la sécurité ASP.NET Core Identity .
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: f50015af864a4a62abd5e2eab508aac915cb6370
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404715"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="c40ea-104">Créer une application ASP.NET Core avec les données utilisateur protégées par l’autorisation</span><span class="sxs-lookup"><span data-stu-id="c40ea-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="c40ea-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="c40ea-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="c40ea-106">Consultez [ce fichier PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) pour la version ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="c40ea-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="c40ea-107">Le ASP.NET Core version 1,1 de ce didacticiel se trouve dans [ce](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) dossier.</span><span class="sxs-lookup"><span data-stu-id="c40ea-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="c40ea-108">L’exemple 1,1 ASP.NET Core se trouve dans les [exemples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="c40ea-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="c40ea-109">Voir [ce PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="c40ea-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c40ea-110">Ce didacticiel montre comment créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="c40ea-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="c40ea-111">Il affiche la liste des contacts que les utilisateurs authentifiés (inscrits) ont créés.</span><span class="sxs-lookup"><span data-stu-id="c40ea-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="c40ea-112">Il existe trois groupes de sécurité :</span><span class="sxs-lookup"><span data-stu-id="c40ea-112">There are three security groups:</span></span>

* <span data-ttu-id="c40ea-113">Les **utilisateurs inscrits** peuvent afficher toutes les données approuvées et peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="c40ea-114">Les **responsables** peuvent approuver ou rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="c40ea-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="c40ea-115">Seuls les contacts approuvés sont visibles pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="c40ea-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="c40ea-116">**Les administrateurs** peuvent approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="c40ea-117">Les images de ce document ne correspondent pas exactement aux modèles les plus récents.</span><span class="sxs-lookup"><span data-stu-id="c40ea-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="c40ea-118">Dans l’image suivante, l’utilisateur Rick ( `rick@example.com` ) est connecté.</span><span class="sxs-lookup"><span data-stu-id="c40ea-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="c40ea-119">Rick peut uniquement afficher les contacts approuvés et **modifier** / **supprimer** / **créer** des liens pour ses contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="c40ea-120">Seul le dernier enregistrement créé par Rick affiche des liens **modifier** et **supprimer** .</span><span class="sxs-lookup"><span data-stu-id="c40ea-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="c40ea-121">Les autres utilisateurs ne verront pas le dernier enregistrement jusqu’à ce qu’un responsable ou un administrateur change l’État en « approuvé ».</span><span class="sxs-lookup"><span data-stu-id="c40ea-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Capture d’écran montrant que Rick est connecté](secure-data/_static/rick.png)

<span data-ttu-id="c40ea-123">Dans l’image suivante, `manager@contoso.com` est connecté et dans le rôle du responsable :</span><span class="sxs-lookup"><span data-stu-id="c40ea-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Capture d’écran montrant la manager@contoso.com connexion](secure-data/_static/manager1.png)

<span data-ttu-id="c40ea-125">L’illustration suivante montre la vue des détails du gestionnaire d’un contact :</span><span class="sxs-lookup"><span data-stu-id="c40ea-125">The following image shows the managers details view of a contact:</span></span>

![Vue du gestionnaire d’un contact](secure-data/_static/manager.png)

<span data-ttu-id="c40ea-127">Les boutons **approuver** et **rejeter** s’affichent uniquement pour les responsables et les administrateurs.</span><span class="sxs-lookup"><span data-stu-id="c40ea-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="c40ea-128">Dans l’image suivante, `admin@contoso.com` est connecté et dans le rôle de l’administrateur :</span><span class="sxs-lookup"><span data-stu-id="c40ea-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Capture d’écran montrant la admin@contoso.com connexion](secure-data/_static/admin.png)

<span data-ttu-id="c40ea-130">L’administrateur dispose de tous les privilèges.</span><span class="sxs-lookup"><span data-stu-id="c40ea-130">The administrator has all privileges.</span></span> <span data-ttu-id="c40ea-131">Elle peut lire/modifier/supprimer un contact et modifier l’état des contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="c40ea-132">L’application a été créée par la [génération](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) de modèles automatique du `Contact` modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="c40ea-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="c40ea-133">L’exemple contient les gestionnaires d’autorisations suivants :</span><span class="sxs-lookup"><span data-stu-id="c40ea-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="c40ea-134">`ContactIsOwnerAuthorizationHandler`: Garantit qu’un utilisateur peut uniquement modifier ses données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="c40ea-135">`ContactManagerAuthorizationHandler`: Permet aux gestionnaires d’approuver ou de rejeter les contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="c40ea-136">`ContactAdministratorsAuthorizationHandler`: Permet aux administrateurs d’approuver ou de refuser des contacts, ainsi que de modifier/supprimer des contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c40ea-137">Prérequis</span><span class="sxs-lookup"><span data-stu-id="c40ea-137">Prerequisites</span></span>

<span data-ttu-id="c40ea-138">Ce didacticiel est avancé.</span><span class="sxs-lookup"><span data-stu-id="c40ea-138">This tutorial is advanced.</span></span> <span data-ttu-id="c40ea-139">Vous devez connaître les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="c40ea-139">You should be familiar with:</span></span>

* [<span data-ttu-id="c40ea-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c40ea-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="c40ea-141">Authentification</span><span class="sxs-lookup"><span data-stu-id="c40ea-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="c40ea-142">Confirmation de compte et récupération de mot de passe</span><span class="sxs-lookup"><span data-stu-id="c40ea-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="c40ea-143">Autorisation</span><span class="sxs-lookup"><span data-stu-id="c40ea-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="c40ea-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c40ea-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="c40ea-145">L’application de démarrage et la fin de l’application</span><span class="sxs-lookup"><span data-stu-id="c40ea-145">The starter and completed app</span></span>

<span data-ttu-id="c40ea-146">[Téléchargez](xref:index#how-to-download-a-sample) l’application [terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="c40ea-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="c40ea-147">[Testez](#test-the-completed-app) l’application terminée afin de vous familiariser avec ses fonctionnalités de sécurité.</span><span class="sxs-lookup"><span data-stu-id="c40ea-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="c40ea-148">L’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="c40ea-148">The starter app</span></span>

<span data-ttu-id="c40ea-149">[Téléchargez](xref:index#how-to-download-a-sample) l’application de [démarrage](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="c40ea-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="c40ea-150">Exécutez l’application, appuyez sur le lien **ContactManager** et vérifiez que vous pouvez créer, modifier et supprimer un contact.</span><span class="sxs-lookup"><span data-stu-id="c40ea-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="c40ea-151">Sécuriser les données utilisateur</span><span class="sxs-lookup"><span data-stu-id="c40ea-151">Secure user data</span></span>

<span data-ttu-id="c40ea-152">Les sections suivantes présentent les principales étapes à suivre pour créer l’application de données utilisateur sécurisé.</span><span class="sxs-lookup"><span data-stu-id="c40ea-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="c40ea-153">Il peut s’avérer utile de faire référence au projet terminé.</span><span class="sxs-lookup"><span data-stu-id="c40ea-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="c40ea-154">Lier les données de contact à l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="c40ea-154">Tie the contact data to the user</span></span>

<span data-ttu-id="c40ea-155">Utilisez l' [Identity](xref:security/authentication/identity) ID d’utilisateur ASP.net pour vous assurer que les utilisateurs peuvent modifier leurs données, mais pas les données d’autres utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="c40ea-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="c40ea-156">Ajoutez `OwnerID` et `ContactStatus` au `Contact` modèle :</span><span class="sxs-lookup"><span data-stu-id="c40ea-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="c40ea-157">`OwnerID`ID de l’utilisateur de la `AspNetUser` table dans la [Identity](xref:security/authentication/identity) base de données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="c40ea-158">Le `Status` champ détermine si un contact est visible par les utilisateurs généraux.</span><span class="sxs-lookup"><span data-stu-id="c40ea-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="c40ea-159">Créez une nouvelle migration et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="c40ea-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="c40ea-160">Ajouter des services de rôle àIdentity</span><span class="sxs-lookup"><span data-stu-id="c40ea-160">Add Role services to Identity</span></span>

<span data-ttu-id="c40ea-161">Ajoutez [rôles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pour ajouter des services de rôle :</span><span class="sxs-lookup"><span data-stu-id="c40ea-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="c40ea-162">Exiger des utilisateurs authentifiés</span><span class="sxs-lookup"><span data-stu-id="c40ea-162">Require authenticated users</span></span>

<span data-ttu-id="c40ea-163">Définissez la stratégie d’authentification par défaut pour exiger que les utilisateurs soient authentifiés :</span><span class="sxs-lookup"><span data-stu-id="c40ea-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="c40ea-164">Vous pouvez refuser l’authentification au niveau de la Razor page, du contrôleur ou de la méthode d’action avec l' `[AllowAnonymous]` attribut.</span><span class="sxs-lookup"><span data-stu-id="c40ea-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="c40ea-165">La définition de la stratégie d’authentification par défaut pour exiger que les utilisateurs soient authentifiés protège les pages et les contrôleurs qui viennent d’être ajoutés Razor .</span><span class="sxs-lookup"><span data-stu-id="c40ea-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="c40ea-166">L’authentification requise par défaut est plus sécurisée que le fait de s’appuyer sur de nouveaux contrôleurs et Razor pages pour inclure l' `[Authorize]` attribut.</span><span class="sxs-lookup"><span data-stu-id="c40ea-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="c40ea-167">Ajoutez [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) aux pages d’index et de confidentialité afin que les utilisateurs anonymes puissent obtenir des informations sur le site avant de s’inscrire.</span><span class="sxs-lookup"><span data-stu-id="c40ea-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="c40ea-168">Configurer le compte de test</span><span class="sxs-lookup"><span data-stu-id="c40ea-168">Configure the test account</span></span>

<span data-ttu-id="c40ea-169">La `SeedData` classe crée deux comptes : administrateur et gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="c40ea-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="c40ea-170">Utilisez l' [outil secret Manager](xref:security/app-secrets) pour définir un mot de passe pour ces comptes.</span><span class="sxs-lookup"><span data-stu-id="c40ea-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="c40ea-171">Définissez le mot de passe à partir du répertoire du projet (répertoire contenant *Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="c40ea-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="c40ea-172">Si aucun mot de passe fort n’est spécifié, une exception est levée lorsque la `SeedData.Initialize` méthode est appelée.</span><span class="sxs-lookup"><span data-stu-id="c40ea-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="c40ea-173">Mettre à jour `Main` pour utiliser le mot de passe de test :</span><span class="sxs-lookup"><span data-stu-id="c40ea-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="c40ea-174">Créer les comptes de test et mettre à jour les contacts</span><span class="sxs-lookup"><span data-stu-id="c40ea-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="c40ea-175">Mettez à jour la `Initialize` méthode dans la `SeedData` classe pour créer les comptes de test :</span><span class="sxs-lookup"><span data-stu-id="c40ea-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="c40ea-176">Ajoutez l’ID d’utilisateur administrateur et `ContactStatus` les contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="c40ea-177">Faites de l’un des contacts « soumis » et un « rejeté ».</span><span class="sxs-lookup"><span data-stu-id="c40ea-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="c40ea-178">Ajoutez l’ID d’utilisateur et l’État à tous les contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="c40ea-179">Un seul contact est affiché :</span><span class="sxs-lookup"><span data-stu-id="c40ea-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="c40ea-180">Créer des gestionnaires d’autorisation propriétaire, responsable et administrateur</span><span class="sxs-lookup"><span data-stu-id="c40ea-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="c40ea-181">Créez une `ContactIsOwnerAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="c40ea-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c40ea-182">Le `ContactIsOwnerAuthorizationHandler` vérifie que l’utilisateur agissant sur une ressource possède la ressource.</span><span class="sxs-lookup"><span data-stu-id="c40ea-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="c40ea-183">`ContactIsOwnerAuthorizationHandler`Contexte des appels [. Réussie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si l’utilisateur authentifié actuel est le propriétaire du contact.</span><span class="sxs-lookup"><span data-stu-id="c40ea-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="c40ea-184">Les gestionnaires d’autorisations sont généralement :</span><span class="sxs-lookup"><span data-stu-id="c40ea-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="c40ea-185">Retourne `context.Succeed` lorsque les spécifications sont satisfaites.</span><span class="sxs-lookup"><span data-stu-id="c40ea-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="c40ea-186">Retourne `Task.CompletedTask` lorsque les spécifications ne sont pas remplies.</span><span class="sxs-lookup"><span data-stu-id="c40ea-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="c40ea-187">`Task.CompletedTask`n’est pas un succès ou un échec &mdash; . il permet l’exécution d’autres gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="c40ea-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="c40ea-188">Si vous devez faire échouer explicitement, retournez le [contexte. Échoue](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="c40ea-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="c40ea-189">L’application permet aux propriétaires de contacts de modifier/supprimer/créer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="c40ea-190">`ContactIsOwnerAuthorizationHandler`n’a pas besoin de vérifier l’opération passée dans le paramètre d’exigence.</span><span class="sxs-lookup"><span data-stu-id="c40ea-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="c40ea-191">Créer un gestionnaire d’autorisations de gestionnaire</span><span class="sxs-lookup"><span data-stu-id="c40ea-191">Create a manager authorization handler</span></span>

<span data-ttu-id="c40ea-192">Créez une `ContactManagerAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="c40ea-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c40ea-193">Le `ContactManagerAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un responsable.</span><span class="sxs-lookup"><span data-stu-id="c40ea-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="c40ea-194">Seuls les responsables peuvent approuver ou rejeter les modifications de contenu (nouveaux ou modifiés).</span><span class="sxs-lookup"><span data-stu-id="c40ea-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="c40ea-195">Créer un gestionnaire d’autorisations d’administrateur</span><span class="sxs-lookup"><span data-stu-id="c40ea-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="c40ea-196">Créez une `ContactAdministratorsAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="c40ea-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c40ea-197">Le `ContactAdministratorsAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un administrateur.</span><span class="sxs-lookup"><span data-stu-id="c40ea-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="c40ea-198">L’administrateur peut effectuer toutes les opérations.</span><span class="sxs-lookup"><span data-stu-id="c40ea-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="c40ea-199">Inscrire les gestionnaires d’autorisations</span><span class="sxs-lookup"><span data-stu-id="c40ea-199">Register the authorization handlers</span></span>

<span data-ttu-id="c40ea-200">Les services qui utilisent Entity Framework Core doivent être inscrits pour l' [injection de dépendances](xref:fundamentals/dependency-injection) à l’aide de [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="c40ea-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="c40ea-201">`ContactIsOwnerAuthorizationHandler`Utilise ASP.net Core [Identity](xref:security/authentication/identity) , qui repose sur Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="c40ea-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="c40ea-202">Inscrire les gestionnaires auprès de la collection de services afin qu’ils soient disponibles pour le `ContactsController` via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c40ea-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c40ea-203">Ajoutez le code suivant à la fin de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c40ea-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="c40ea-204">`ContactAdministratorsAuthorizationHandler`et `ContactManagerAuthorizationHandler` sont ajoutés en tant que singletons.</span><span class="sxs-lookup"><span data-stu-id="c40ea-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="c40ea-205">Il s’agit de singletons, car ils n’utilisent pas EF et toutes les informations nécessaires sont dans le `Context` paramètre de la `HandleRequirementAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="c40ea-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="c40ea-206">Autorisation du support</span><span class="sxs-lookup"><span data-stu-id="c40ea-206">Support authorization</span></span>

<span data-ttu-id="c40ea-207">Dans cette section, vous allez mettre à jour les Razor pages et ajouter une classe d’exigences d’opérations.</span><span class="sxs-lookup"><span data-stu-id="c40ea-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="c40ea-208">Examiner la classe des exigences relatives aux opérations de contact</span><span class="sxs-lookup"><span data-stu-id="c40ea-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="c40ea-209">Passez en revue la `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="c40ea-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="c40ea-210">Cette classe contient les spécifications prises en charge par l’application :</span><span class="sxs-lookup"><span data-stu-id="c40ea-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="c40ea-211">Créer une classe de base pour les Razor pages contacts</span><span class="sxs-lookup"><span data-stu-id="c40ea-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="c40ea-212">Créez une classe de base qui contient les services utilisés dans les Razor pages de contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="c40ea-213">La classe de base place le code d’initialisation à un emplacement :</span><span class="sxs-lookup"><span data-stu-id="c40ea-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="c40ea-214">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="c40ea-214">The preceding code:</span></span>

* <span data-ttu-id="c40ea-215">Ajoute le `IAuthorizationService` service pour accéder aux gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="c40ea-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="c40ea-216">Ajoute le Identity `UserManager` service.</span><span class="sxs-lookup"><span data-stu-id="c40ea-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="c40ea-217">Ajoutez la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="c40ea-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="c40ea-218">Mettre à jour CreateModel</span><span class="sxs-lookup"><span data-stu-id="c40ea-218">Update the CreateModel</span></span>

<span data-ttu-id="c40ea-219">Mettez à jour le constructeur de modèle de page de création pour utiliser la `DI_BasePageModel` classe de base :</span><span class="sxs-lookup"><span data-stu-id="c40ea-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="c40ea-220">Mettez à jour la `CreateModel.OnPostAsync` méthode pour :</span><span class="sxs-lookup"><span data-stu-id="c40ea-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="c40ea-221">Ajoutez l’ID utilisateur au `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="c40ea-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="c40ea-222">Appelez le gestionnaire d’autorisations pour vérifier que l’utilisateur a l’autorisation de créer des contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="c40ea-223">Mettre à jour IndexModel</span><span class="sxs-lookup"><span data-stu-id="c40ea-223">Update the IndexModel</span></span>

<span data-ttu-id="c40ea-224">Mettez à jour la `OnGetAsync` méthode afin que seuls les contacts approuvés soient affichés pour les utilisateurs généraux :</span><span class="sxs-lookup"><span data-stu-id="c40ea-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="c40ea-225">Mettre à jour EditModel</span><span class="sxs-lookup"><span data-stu-id="c40ea-225">Update the EditModel</span></span>

<span data-ttu-id="c40ea-226">Ajoutez un gestionnaire d’autorisations pour vérifier que l’utilisateur possède le contact.</span><span class="sxs-lookup"><span data-stu-id="c40ea-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="c40ea-227">Étant donné que l’autorisation des ressources est en cours de validation, l' `[Authorize]` attribut n’est pas suffisant.</span><span class="sxs-lookup"><span data-stu-id="c40ea-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="c40ea-228">L’application n’a pas accès à la ressource lors de l’évaluation des attributs.</span><span class="sxs-lookup"><span data-stu-id="c40ea-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="c40ea-229">L’autorisation basée sur les ressources doit être impérative.</span><span class="sxs-lookup"><span data-stu-id="c40ea-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="c40ea-230">Les vérifications doivent être effectuées une fois que l’application a accès à la ressource, soit en la chargeant dans le modèle de page, soit en la chargeant dans le gestionnaire lui-même.</span><span class="sxs-lookup"><span data-stu-id="c40ea-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="c40ea-231">Vous accédez fréquemment à la ressource en passant la clé de ressource.</span><span class="sxs-lookup"><span data-stu-id="c40ea-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="c40ea-232">Mettre à jour DeleteModel</span><span class="sxs-lookup"><span data-stu-id="c40ea-232">Update the DeleteModel</span></span>

<span data-ttu-id="c40ea-233">Mettez à jour le modèle de page de suppression pour utiliser le gestionnaire d’autorisations afin de vérifier que l’utilisateur dispose de l’autorisation de suppression sur le contact.</span><span class="sxs-lookup"><span data-stu-id="c40ea-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="c40ea-234">Injecter le service d’autorisation dans les vues</span><span class="sxs-lookup"><span data-stu-id="c40ea-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="c40ea-235">Actuellement, l’interface utilisateur affiche les liens modifier et supprimer pour les contacts que l’utilisateur ne peut pas modifier.</span><span class="sxs-lookup"><span data-stu-id="c40ea-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="c40ea-236">Injecter le service d’autorisation dans le fichier *pages/_ViewImports. cshtml* afin qu’il soit disponible pour tous les affichages :</span><span class="sxs-lookup"><span data-stu-id="c40ea-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="c40ea-237">Le balisage précédent ajoute plusieurs `using` instructions.</span><span class="sxs-lookup"><span data-stu-id="c40ea-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="c40ea-238">Mettez à jour les liens **modifier** et **supprimer** dans *pages/contacts/index. cshtml* afin qu’ils soient affichés uniquement pour les utilisateurs disposant des autorisations appropriées :</span><span class="sxs-lookup"><span data-stu-id="c40ea-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="c40ea-239">Le masquage des liens des utilisateurs qui n’ont pas l’autorisation de modifier des données ne sécurise pas l’application.</span><span class="sxs-lookup"><span data-stu-id="c40ea-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="c40ea-240">Le masquage des liens rend l’application plus conviviale en affichant uniquement des liens valides.</span><span class="sxs-lookup"><span data-stu-id="c40ea-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="c40ea-241">Les utilisateurs peuvent pirater les URL générées pour appeler des opérations de modification et de suppression sur les données qu’ils ne possèdent pas.</span><span class="sxs-lookup"><span data-stu-id="c40ea-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="c40ea-242">La Razor page ou le contrôleur doit appliquer les vérifications d’accès pour sécuriser les données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="c40ea-243">Mettre à jour les détails</span><span class="sxs-lookup"><span data-stu-id="c40ea-243">Update Details</span></span>

<span data-ttu-id="c40ea-244">Mettez à jour la vue Détails pour permettre aux responsables d’approuver ou de rejeter les contacts :</span><span class="sxs-lookup"><span data-stu-id="c40ea-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="c40ea-245">Mettez à jour le modèle de page de détails :</span><span class="sxs-lookup"><span data-stu-id="c40ea-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="c40ea-246">Ajouter ou supprimer un utilisateur dans un rôle</span><span class="sxs-lookup"><span data-stu-id="c40ea-246">Add or remove a user to a role</span></span>

<span data-ttu-id="c40ea-247">Consultez [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/8502) pour plus d’informations sur :</span><span class="sxs-lookup"><span data-stu-id="c40ea-247">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="c40ea-248">Suppression des privilèges d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c40ea-248">Removing privileges from a user.</span></span> <span data-ttu-id="c40ea-249">Par exemple, la désactivation d’un utilisateur dans une application de conversation.</span><span class="sxs-lookup"><span data-stu-id="c40ea-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="c40ea-250">Ajout de privilèges à un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c40ea-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="c40ea-251">Différences entre Challenge et interdire</span><span class="sxs-lookup"><span data-stu-id="c40ea-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="c40ea-252">Cette application définit la stratégie par défaut pour [exiger des utilisateurs authentifiés](#require-authenticated-users).</span><span class="sxs-lookup"><span data-stu-id="c40ea-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="c40ea-253">Le code suivant autorise les utilisateurs anonymes.</span><span class="sxs-lookup"><span data-stu-id="c40ea-253">The following code allows anonymous users.</span></span> <span data-ttu-id="c40ea-254">Les utilisateurs anonymes sont autorisés à afficher les différences entre la stimulation et l’interdiction.</span><span class="sxs-lookup"><span data-stu-id="c40ea-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="c40ea-255">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="c40ea-255">In the preceding code:</span></span>

* <span data-ttu-id="c40ea-256">Lorsque l’utilisateur n’est **pas** authentifié, un `ChallengeResult` est retourné.</span><span class="sxs-lookup"><span data-stu-id="c40ea-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="c40ea-257">Lorsqu’un `ChallengeResult` est retourné, l’utilisateur est redirigé vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="c40ea-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="c40ea-258">Lorsque l’utilisateur est authentifié, mais n’est pas autorisé, un `ForbidResult` est retourné.</span><span class="sxs-lookup"><span data-stu-id="c40ea-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="c40ea-259">Lorsqu’un `ForbidResult` est retourné, l’utilisateur est redirigé vers la page accès refusé.</span><span class="sxs-lookup"><span data-stu-id="c40ea-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="c40ea-260">Tester l’application terminée</span><span class="sxs-lookup"><span data-stu-id="c40ea-260">Test the completed app</span></span>

<span data-ttu-id="c40ea-261">Si vous n’avez pas encore défini de mot de passe pour les comptes d’utilisateurs amorcés, utilisez l' [outil secret Manager](xref:security/app-secrets#secret-manager) pour définir un mot de passe :</span><span class="sxs-lookup"><span data-stu-id="c40ea-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="c40ea-262">Choisissez un mot de passe fort : utilisez au moins huit caractères et au moins un caractère majuscule, un chiffre et un symbole.</span><span class="sxs-lookup"><span data-stu-id="c40ea-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="c40ea-263">Par exemple, `Passw0rd!` répond aux exigences de mot de passe fort.</span><span class="sxs-lookup"><span data-stu-id="c40ea-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="c40ea-264">Exécutez la commande suivante à partir du dossier du projet, où `<PW>` est le mot de passe :</span><span class="sxs-lookup"><span data-stu-id="c40ea-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="c40ea-265">Si l’application a des contacts :</span><span class="sxs-lookup"><span data-stu-id="c40ea-265">If the app has contacts:</span></span>

* <span data-ttu-id="c40ea-266">Supprimez tous les enregistrements de la `Contact` table.</span><span class="sxs-lookup"><span data-stu-id="c40ea-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="c40ea-267">Redémarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="c40ea-268">Un moyen simple de tester l’application terminée consiste à lancer trois navigateurs différents (ou des sessions incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="c40ea-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="c40ea-269">Dans un navigateur, inscrivez un nouvel utilisateur (par exemple, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="c40ea-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="c40ea-270">Connectez-vous à chaque navigateur avec un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c40ea-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="c40ea-271">Vérifiez les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="c40ea-271">Verify the following operations:</span></span>

* <span data-ttu-id="c40ea-272">Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.</span><span class="sxs-lookup"><span data-stu-id="c40ea-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="c40ea-273">Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="c40ea-274">Les responsables peuvent approuver/rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="c40ea-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="c40ea-275">La `Details` vue affiche les boutons **approuver** et **rejeter** .</span><span class="sxs-lookup"><span data-stu-id="c40ea-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="c40ea-276">Les administrateurs peuvent approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="c40ea-277">Utilisateur</span><span class="sxs-lookup"><span data-stu-id="c40ea-277">User</span></span>                | <span data-ttu-id="c40ea-278">Amorcé par l’application</span><span class="sxs-lookup"><span data-stu-id="c40ea-278">Seeded by the app</span></span> | <span data-ttu-id="c40ea-279">Options</span><span class="sxs-lookup"><span data-stu-id="c40ea-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="c40ea-280">Non</span><span class="sxs-lookup"><span data-stu-id="c40ea-280">No</span></span>                | <span data-ttu-id="c40ea-281">Modifiez/supprimez les données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="c40ea-282">Oui</span><span class="sxs-lookup"><span data-stu-id="c40ea-282">Yes</span></span>               | <span data-ttu-id="c40ea-283">Approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="c40ea-284">Oui</span><span class="sxs-lookup"><span data-stu-id="c40ea-284">Yes</span></span>               | <span data-ttu-id="c40ea-285">Approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="c40ea-286">Créez un contact dans le navigateur de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="c40ea-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="c40ea-287">Copiez l’URL de la suppression et de la modification à partir du contact de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="c40ea-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="c40ea-288">Collez ces liens dans le navigateur de l’utilisateur de test pour vérifier que l’utilisateur de test ne peut pas effectuer ces opérations.</span><span class="sxs-lookup"><span data-stu-id="c40ea-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="c40ea-289">Créer l’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="c40ea-289">Create the starter app</span></span>

* <span data-ttu-id="c40ea-290">Créer une Razor application pages nommée « ContactManager »</span><span class="sxs-lookup"><span data-stu-id="c40ea-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="c40ea-291">Créez l’application avec des **comptes d’utilisateur individuels**.</span><span class="sxs-lookup"><span data-stu-id="c40ea-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="c40ea-292">Nommez-la « ContactManager » pour que l’espace de noms corresponde à l’espace de noms utilisé dans l’exemple.</span><span class="sxs-lookup"><span data-stu-id="c40ea-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="c40ea-293">`-uld`spécifie la base de données locale au lieu de SQLite</span><span class="sxs-lookup"><span data-stu-id="c40ea-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="c40ea-294">Ajouter des *modèles/contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="c40ea-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="c40ea-295">Structurez le `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="c40ea-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="c40ea-296">Créez la migration initiale et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="c40ea-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="c40ea-297">Si vous rencontrez un bogue avec la `dotnet aspnet-codegenerator razorpage` commande, consultez [ce problème GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="c40ea-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="c40ea-298">Mettez à jour l’ancre **ContactManager** dans le fichier *pages/shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c40ea-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="c40ea-299">Tester l’application en créant, en modifiant et en supprimant un contact</span><span class="sxs-lookup"><span data-stu-id="c40ea-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="c40ea-300">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="c40ea-300">Seed the database</span></span>

<span data-ttu-id="c40ea-301">Ajoutez la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) au dossier *Data* :</span><span class="sxs-lookup"><span data-stu-id="c40ea-301">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="c40ea-302">Appeler `SeedData.Initialize` à partir de `Main` :</span><span class="sxs-lookup"><span data-stu-id="c40ea-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="c40ea-303">Vérifiez que l’application a amorcé la base de données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-303">Test that the app seeded the database.</span></span> <span data-ttu-id="c40ea-304">Si la base de coordonnées contient des lignes, la méthode Seed ne s’exécute pas.</span><span class="sxs-lookup"><span data-stu-id="c40ea-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="c40ea-305">Ce didacticiel montre comment créer une application Web ASP.NET Core avec les données utilisateur protégées par l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="c40ea-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="c40ea-306">Il affiche la liste des contacts que les utilisateurs authentifiés (inscrits) ont créés.</span><span class="sxs-lookup"><span data-stu-id="c40ea-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="c40ea-307">Il existe trois groupes de sécurité :</span><span class="sxs-lookup"><span data-stu-id="c40ea-307">There are three security groups:</span></span>

* <span data-ttu-id="c40ea-308">Les **utilisateurs inscrits** peuvent afficher toutes les données approuvées et peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="c40ea-309">Les **responsables** peuvent approuver ou rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="c40ea-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="c40ea-310">Seuls les contacts approuvés sont visibles pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="c40ea-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="c40ea-311">**Les administrateurs** peuvent approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="c40ea-312">Dans l’image suivante, l’utilisateur Rick ( `rick@example.com` ) est connecté.</span><span class="sxs-lookup"><span data-stu-id="c40ea-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="c40ea-313">Rick peut uniquement afficher les contacts approuvés et **modifier** / **supprimer** / **créer** des liens pour ses contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="c40ea-314">Seul le dernier enregistrement créé par Rick affiche des liens **modifier** et **supprimer** .</span><span class="sxs-lookup"><span data-stu-id="c40ea-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="c40ea-315">Les autres utilisateurs ne verront pas le dernier enregistrement jusqu’à ce qu’un responsable ou un administrateur change l’État en « approuvé ».</span><span class="sxs-lookup"><span data-stu-id="c40ea-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Capture d’écran montrant que Rick est connecté](secure-data/_static/rick.png)

<span data-ttu-id="c40ea-317">Dans l’image suivante, `manager@contoso.com` est connecté et dans le rôle du responsable :</span><span class="sxs-lookup"><span data-stu-id="c40ea-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Capture d’écran montrant la manager@contoso.com connexion](secure-data/_static/manager1.png)

<span data-ttu-id="c40ea-319">L’illustration suivante montre la vue des détails du gestionnaire d’un contact :</span><span class="sxs-lookup"><span data-stu-id="c40ea-319">The following image shows the managers details view of a contact:</span></span>

![Vue du gestionnaire d’un contact](secure-data/_static/manager.png)

<span data-ttu-id="c40ea-321">Les boutons **approuver** et **rejeter** s’affichent uniquement pour les responsables et les administrateurs.</span><span class="sxs-lookup"><span data-stu-id="c40ea-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="c40ea-322">Dans l’image suivante, `admin@contoso.com` est connecté et dans le rôle de l’administrateur :</span><span class="sxs-lookup"><span data-stu-id="c40ea-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Capture d’écran montrant la admin@contoso.com connexion](secure-data/_static/admin.png)

<span data-ttu-id="c40ea-324">L’administrateur dispose de tous les privilèges.</span><span class="sxs-lookup"><span data-stu-id="c40ea-324">The administrator has all privileges.</span></span> <span data-ttu-id="c40ea-325">Elle peut lire/modifier/supprimer un contact et modifier l’état des contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="c40ea-326">L’application a été créée par la [génération](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) de modèles automatique du `Contact` modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="c40ea-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="c40ea-327">L’exemple contient les gestionnaires d’autorisations suivants :</span><span class="sxs-lookup"><span data-stu-id="c40ea-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="c40ea-328">`ContactIsOwnerAuthorizationHandler`: Garantit qu’un utilisateur peut uniquement modifier ses données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="c40ea-329">`ContactManagerAuthorizationHandler`: Permet aux gestionnaires d’approuver ou de rejeter les contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="c40ea-330">`ContactAdministratorsAuthorizationHandler`: Permet aux administrateurs d’approuver ou de refuser des contacts, ainsi que de modifier/supprimer des contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c40ea-331">Prérequis</span><span class="sxs-lookup"><span data-stu-id="c40ea-331">Prerequisites</span></span>

<span data-ttu-id="c40ea-332">Ce didacticiel est avancé.</span><span class="sxs-lookup"><span data-stu-id="c40ea-332">This tutorial is advanced.</span></span> <span data-ttu-id="c40ea-333">Vous devez connaître les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="c40ea-333">You should be familiar with:</span></span>

* [<span data-ttu-id="c40ea-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c40ea-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="c40ea-335">Authentification</span><span class="sxs-lookup"><span data-stu-id="c40ea-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="c40ea-336">Confirmation de compte et récupération de mot de passe</span><span class="sxs-lookup"><span data-stu-id="c40ea-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="c40ea-337">Autorisation</span><span class="sxs-lookup"><span data-stu-id="c40ea-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="c40ea-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c40ea-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="c40ea-339">L’application de démarrage et la fin de l’application</span><span class="sxs-lookup"><span data-stu-id="c40ea-339">The starter and completed app</span></span>

<span data-ttu-id="c40ea-340">[Téléchargez](xref:index#how-to-download-a-sample) l’application [terminée](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="c40ea-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="c40ea-341">[Testez](#test-the-completed-app) l’application terminée afin de vous familiariser avec ses fonctionnalités de sécurité.</span><span class="sxs-lookup"><span data-stu-id="c40ea-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="c40ea-342">L’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="c40ea-342">The starter app</span></span>

<span data-ttu-id="c40ea-343">[Téléchargez](xref:index#how-to-download-a-sample) l’application de [démarrage](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="c40ea-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="c40ea-344">Exécutez l’application, appuyez sur le lien **ContactManager** et vérifiez que vous pouvez créer, modifier et supprimer un contact.</span><span class="sxs-lookup"><span data-stu-id="c40ea-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="c40ea-345">Sécuriser les données utilisateur</span><span class="sxs-lookup"><span data-stu-id="c40ea-345">Secure user data</span></span>

<span data-ttu-id="c40ea-346">Les sections suivantes présentent les principales étapes à suivre pour créer l’application de données utilisateur sécurisé.</span><span class="sxs-lookup"><span data-stu-id="c40ea-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="c40ea-347">Il peut s’avérer utile de faire référence au projet terminé.</span><span class="sxs-lookup"><span data-stu-id="c40ea-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="c40ea-348">Lier les données de contact à l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="c40ea-348">Tie the contact data to the user</span></span>

<span data-ttu-id="c40ea-349">Utilisez l' [Identity](xref:security/authentication/identity) ID d’utilisateur ASP.net pour vous assurer que les utilisateurs peuvent modifier leurs données, mais pas les données d’autres utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="c40ea-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="c40ea-350">Ajoutez `OwnerID` et `ContactStatus` au `Contact` modèle :</span><span class="sxs-lookup"><span data-stu-id="c40ea-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="c40ea-351">`OwnerID`ID de l’utilisateur de la `AspNetUser` table dans la [Identity](xref:security/authentication/identity) base de données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="c40ea-352">Le `Status` champ détermine si un contact est visible par les utilisateurs généraux.</span><span class="sxs-lookup"><span data-stu-id="c40ea-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="c40ea-353">Créez une nouvelle migration et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="c40ea-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="c40ea-354">Ajouter des services de rôle àIdentity</span><span class="sxs-lookup"><span data-stu-id="c40ea-354">Add Role services to Identity</span></span>

<span data-ttu-id="c40ea-355">Ajoutez [rôles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pour ajouter des services de rôle :</span><span class="sxs-lookup"><span data-stu-id="c40ea-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="c40ea-356">Exiger des utilisateurs authentifiés</span><span class="sxs-lookup"><span data-stu-id="c40ea-356">Require authenticated users</span></span>

<span data-ttu-id="c40ea-357">Définissez la stratégie d’authentification par défaut pour exiger que les utilisateurs soient authentifiés :</span><span class="sxs-lookup"><span data-stu-id="c40ea-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="c40ea-358">Vous pouvez refuser l’authentification au niveau de la Razor page, du contrôleur ou de la méthode d’action avec l' `[AllowAnonymous]` attribut.</span><span class="sxs-lookup"><span data-stu-id="c40ea-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="c40ea-359">La définition de la stratégie d’authentification par défaut pour exiger que les utilisateurs soient authentifiés protège les pages et les contrôleurs qui viennent d’être ajoutés Razor .</span><span class="sxs-lookup"><span data-stu-id="c40ea-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="c40ea-360">L’authentification requise par défaut est plus sécurisée que le fait de s’appuyer sur de nouveaux contrôleurs et Razor pages pour inclure l' `[Authorize]` attribut.</span><span class="sxs-lookup"><span data-stu-id="c40ea-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="c40ea-361">Ajoutez [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) aux pages d’index, à propos de et contact afin que les utilisateurs anonymes puissent obtenir des informations sur le site avant de s’inscrire.</span><span class="sxs-lookup"><span data-stu-id="c40ea-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="c40ea-362">Configurer le compte de test</span><span class="sxs-lookup"><span data-stu-id="c40ea-362">Configure the test account</span></span>

<span data-ttu-id="c40ea-363">La `SeedData` classe crée deux comptes : administrateur et gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="c40ea-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="c40ea-364">Utilisez l' [outil secret Manager](xref:security/app-secrets) pour définir un mot de passe pour ces comptes.</span><span class="sxs-lookup"><span data-stu-id="c40ea-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="c40ea-365">Définissez le mot de passe à partir du répertoire du projet (répertoire contenant *Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="c40ea-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="c40ea-366">Si aucun mot de passe fort n’est spécifié, une exception est levée lorsque la `SeedData.Initialize` méthode est appelée.</span><span class="sxs-lookup"><span data-stu-id="c40ea-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="c40ea-367">Mettre à jour `Main` pour utiliser le mot de passe de test :</span><span class="sxs-lookup"><span data-stu-id="c40ea-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="c40ea-368">Créer les comptes de test et mettre à jour les contacts</span><span class="sxs-lookup"><span data-stu-id="c40ea-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="c40ea-369">Mettez à jour la `Initialize` méthode dans la `SeedData` classe pour créer les comptes de test :</span><span class="sxs-lookup"><span data-stu-id="c40ea-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="c40ea-370">Ajoutez l’ID d’utilisateur administrateur et `ContactStatus` les contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="c40ea-371">Faites de l’un des contacts « soumis » et un « rejeté ».</span><span class="sxs-lookup"><span data-stu-id="c40ea-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="c40ea-372">Ajoutez l’ID d’utilisateur et l’État à tous les contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="c40ea-373">Un seul contact est affiché :</span><span class="sxs-lookup"><span data-stu-id="c40ea-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="c40ea-374">Créer des gestionnaires d’autorisation propriétaire, responsable et administrateur</span><span class="sxs-lookup"><span data-stu-id="c40ea-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="c40ea-375">Créez un dossier *authorization* et créez une `ContactIsOwnerAuthorizationHandler` classe dans celui-ci.</span><span class="sxs-lookup"><span data-stu-id="c40ea-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="c40ea-376">Le `ContactIsOwnerAuthorizationHandler` vérifie que l’utilisateur agissant sur une ressource possède la ressource.</span><span class="sxs-lookup"><span data-stu-id="c40ea-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="c40ea-377">`ContactIsOwnerAuthorizationHandler`Contexte des appels [. Réussie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si l’utilisateur authentifié actuel est le propriétaire du contact.</span><span class="sxs-lookup"><span data-stu-id="c40ea-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="c40ea-378">Les gestionnaires d’autorisations sont généralement :</span><span class="sxs-lookup"><span data-stu-id="c40ea-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="c40ea-379">Retourne `context.Succeed` lorsque les spécifications sont satisfaites.</span><span class="sxs-lookup"><span data-stu-id="c40ea-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="c40ea-380">Retourne `Task.CompletedTask` lorsque les spécifications ne sont pas remplies.</span><span class="sxs-lookup"><span data-stu-id="c40ea-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="c40ea-381">`Task.CompletedTask`n’est pas un succès ou un échec &mdash; . il permet l’exécution d’autres gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="c40ea-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="c40ea-382">Si vous devez faire échouer explicitement, retournez le [contexte. Échoue](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="c40ea-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="c40ea-383">L’application permet aux propriétaires de contacts de modifier/supprimer/créer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="c40ea-384">`ContactIsOwnerAuthorizationHandler`n’a pas besoin de vérifier l’opération passée dans le paramètre d’exigence.</span><span class="sxs-lookup"><span data-stu-id="c40ea-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="c40ea-385">Créer un gestionnaire d’autorisations de gestionnaire</span><span class="sxs-lookup"><span data-stu-id="c40ea-385">Create a manager authorization handler</span></span>

<span data-ttu-id="c40ea-386">Créez une `ContactManagerAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="c40ea-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c40ea-387">Le `ContactManagerAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un responsable.</span><span class="sxs-lookup"><span data-stu-id="c40ea-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="c40ea-388">Seuls les responsables peuvent approuver ou rejeter les modifications de contenu (nouveaux ou modifiés).</span><span class="sxs-lookup"><span data-stu-id="c40ea-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="c40ea-389">Créer un gestionnaire d’autorisations d’administrateur</span><span class="sxs-lookup"><span data-stu-id="c40ea-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="c40ea-390">Créez une `ContactAdministratorsAuthorizationHandler` classe dans le dossier *authorization* .</span><span class="sxs-lookup"><span data-stu-id="c40ea-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c40ea-391">Le `ContactAdministratorsAuthorizationHandler` vérifie que l’utilisateur agissant sur la ressource est un administrateur.</span><span class="sxs-lookup"><span data-stu-id="c40ea-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="c40ea-392">L’administrateur peut effectuer toutes les opérations.</span><span class="sxs-lookup"><span data-stu-id="c40ea-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="c40ea-393">Inscrire les gestionnaires d’autorisations</span><span class="sxs-lookup"><span data-stu-id="c40ea-393">Register the authorization handlers</span></span>

<span data-ttu-id="c40ea-394">Les services qui utilisent Entity Framework Core doivent être inscrits pour l' [injection de dépendances](xref:fundamentals/dependency-injection) à l’aide de [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="c40ea-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="c40ea-395">`ContactIsOwnerAuthorizationHandler`Utilise ASP.net Core [Identity](xref:security/authentication/identity) , qui repose sur Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="c40ea-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="c40ea-396">Inscrire les gestionnaires auprès de la collection de services afin qu’ils soient disponibles pour le `ContactsController` via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c40ea-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c40ea-397">Ajoutez le code suivant à la fin de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c40ea-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="c40ea-398">`ContactAdministratorsAuthorizationHandler`et `ContactManagerAuthorizationHandler` sont ajoutés en tant que singletons.</span><span class="sxs-lookup"><span data-stu-id="c40ea-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="c40ea-399">Il s’agit de singletons, car ils n’utilisent pas EF et toutes les informations nécessaires sont dans le `Context` paramètre de la `HandleRequirementAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="c40ea-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="c40ea-400">Autorisation du support</span><span class="sxs-lookup"><span data-stu-id="c40ea-400">Support authorization</span></span>

<span data-ttu-id="c40ea-401">Dans cette section, vous allez mettre à jour les Razor pages et ajouter une classe d’exigences d’opérations.</span><span class="sxs-lookup"><span data-stu-id="c40ea-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="c40ea-402">Examiner la classe des exigences relatives aux opérations de contact</span><span class="sxs-lookup"><span data-stu-id="c40ea-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="c40ea-403">Passez en revue la `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="c40ea-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="c40ea-404">Cette classe contient les spécifications prises en charge par l’application :</span><span class="sxs-lookup"><span data-stu-id="c40ea-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="c40ea-405">Créer une classe de base pour les Razor pages contacts</span><span class="sxs-lookup"><span data-stu-id="c40ea-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="c40ea-406">Créez une classe de base qui contient les services utilisés dans les Razor pages de contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="c40ea-407">La classe de base place le code d’initialisation à un emplacement :</span><span class="sxs-lookup"><span data-stu-id="c40ea-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="c40ea-408">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="c40ea-408">The preceding code:</span></span>

* <span data-ttu-id="c40ea-409">Ajoute le `IAuthorizationService` service pour accéder aux gestionnaires d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="c40ea-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="c40ea-410">Ajoute le Identity `UserManager` service.</span><span class="sxs-lookup"><span data-stu-id="c40ea-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="c40ea-411">Ajoutez la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="c40ea-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="c40ea-412">Mettre à jour CreateModel</span><span class="sxs-lookup"><span data-stu-id="c40ea-412">Update the CreateModel</span></span>

<span data-ttu-id="c40ea-413">Mettez à jour le constructeur de modèle de page de création pour utiliser la `DI_BasePageModel` classe de base :</span><span class="sxs-lookup"><span data-stu-id="c40ea-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="c40ea-414">Mettez à jour la `CreateModel.OnPostAsync` méthode pour :</span><span class="sxs-lookup"><span data-stu-id="c40ea-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="c40ea-415">Ajoutez l’ID utilisateur au `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="c40ea-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="c40ea-416">Appelez le gestionnaire d’autorisations pour vérifier que l’utilisateur a l’autorisation de créer des contacts.</span><span class="sxs-lookup"><span data-stu-id="c40ea-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="c40ea-417">Mettre à jour IndexModel</span><span class="sxs-lookup"><span data-stu-id="c40ea-417">Update the IndexModel</span></span>

<span data-ttu-id="c40ea-418">Mettez à jour la `OnGetAsync` méthode afin que seuls les contacts approuvés soient affichés pour les utilisateurs généraux :</span><span class="sxs-lookup"><span data-stu-id="c40ea-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="c40ea-419">Mettre à jour EditModel</span><span class="sxs-lookup"><span data-stu-id="c40ea-419">Update the EditModel</span></span>

<span data-ttu-id="c40ea-420">Ajoutez un gestionnaire d’autorisations pour vérifier que l’utilisateur possède le contact.</span><span class="sxs-lookup"><span data-stu-id="c40ea-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="c40ea-421">Étant donné que l’autorisation des ressources est en cours de validation, l' `[Authorize]` attribut n’est pas suffisant.</span><span class="sxs-lookup"><span data-stu-id="c40ea-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="c40ea-422">L’application n’a pas accès à la ressource lors de l’évaluation des attributs.</span><span class="sxs-lookup"><span data-stu-id="c40ea-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="c40ea-423">L’autorisation basée sur les ressources doit être impérative.</span><span class="sxs-lookup"><span data-stu-id="c40ea-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="c40ea-424">Les vérifications doivent être effectuées une fois que l’application a accès à la ressource, soit en la chargeant dans le modèle de page, soit en la chargeant dans le gestionnaire lui-même.</span><span class="sxs-lookup"><span data-stu-id="c40ea-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="c40ea-425">Vous accédez fréquemment à la ressource en passant la clé de ressource.</span><span class="sxs-lookup"><span data-stu-id="c40ea-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="c40ea-426">Mettre à jour DeleteModel</span><span class="sxs-lookup"><span data-stu-id="c40ea-426">Update the DeleteModel</span></span>

<span data-ttu-id="c40ea-427">Mettez à jour le modèle de page de suppression pour utiliser le gestionnaire d’autorisations afin de vérifier que l’utilisateur dispose de l’autorisation de suppression sur le contact.</span><span class="sxs-lookup"><span data-stu-id="c40ea-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="c40ea-428">Injecter le service d’autorisation dans les vues</span><span class="sxs-lookup"><span data-stu-id="c40ea-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="c40ea-429">Actuellement, l’interface utilisateur affiche les liens modifier et supprimer pour les contacts que l’utilisateur ne peut pas modifier.</span><span class="sxs-lookup"><span data-stu-id="c40ea-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="c40ea-430">Injecter le service d’autorisation dans le fichier *views/_ViewImports. cshtml* afin qu’il soit disponible pour tous les affichages :</span><span class="sxs-lookup"><span data-stu-id="c40ea-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="c40ea-431">Le balisage précédent ajoute plusieurs `using` instructions.</span><span class="sxs-lookup"><span data-stu-id="c40ea-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="c40ea-432">Mettez à jour les liens **modifier** et **supprimer** dans *pages/contacts/index. cshtml* afin qu’ils soient affichés uniquement pour les utilisateurs disposant des autorisations appropriées :</span><span class="sxs-lookup"><span data-stu-id="c40ea-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="c40ea-433">Le masquage des liens des utilisateurs qui n’ont pas l’autorisation de modifier des données ne sécurise pas l’application.</span><span class="sxs-lookup"><span data-stu-id="c40ea-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="c40ea-434">Le masquage des liens rend l’application plus conviviale en affichant uniquement des liens valides.</span><span class="sxs-lookup"><span data-stu-id="c40ea-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="c40ea-435">Les utilisateurs peuvent pirater les URL générées pour appeler des opérations de modification et de suppression sur les données qu’ils ne possèdent pas.</span><span class="sxs-lookup"><span data-stu-id="c40ea-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="c40ea-436">La Razor page ou le contrôleur doit appliquer les vérifications d’accès pour sécuriser les données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="c40ea-437">Mettre à jour les détails</span><span class="sxs-lookup"><span data-stu-id="c40ea-437">Update Details</span></span>

<span data-ttu-id="c40ea-438">Mettez à jour la vue Détails pour permettre aux responsables d’approuver ou de rejeter les contacts :</span><span class="sxs-lookup"><span data-stu-id="c40ea-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="c40ea-439">Mettez à jour le modèle de page de détails :</span><span class="sxs-lookup"><span data-stu-id="c40ea-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="c40ea-440">Ajouter ou supprimer un utilisateur dans un rôle</span><span class="sxs-lookup"><span data-stu-id="c40ea-440">Add or remove a user to a role</span></span>

<span data-ttu-id="c40ea-441">Consultez [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/8502) pour plus d’informations sur :</span><span class="sxs-lookup"><span data-stu-id="c40ea-441">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="c40ea-442">Suppression des privilèges d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c40ea-442">Removing privileges from a user.</span></span> <span data-ttu-id="c40ea-443">Par exemple, la désactivation d’un utilisateur dans une application de conversation.</span><span class="sxs-lookup"><span data-stu-id="c40ea-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="c40ea-444">Ajout de privilèges à un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c40ea-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="c40ea-445">Tester l’application terminée</span><span class="sxs-lookup"><span data-stu-id="c40ea-445">Test the completed app</span></span>

<span data-ttu-id="c40ea-446">Si vous n’avez pas encore défini de mot de passe pour les comptes d’utilisateurs amorcés, utilisez l' [outil secret Manager](xref:security/app-secrets#secret-manager) pour définir un mot de passe :</span><span class="sxs-lookup"><span data-stu-id="c40ea-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="c40ea-447">Choisissez un mot de passe fort : utilisez au moins huit caractères et au moins un caractère majuscule, un chiffre et un symbole.</span><span class="sxs-lookup"><span data-stu-id="c40ea-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="c40ea-448">Par exemple, `Passw0rd!` répond aux exigences de mot de passe fort.</span><span class="sxs-lookup"><span data-stu-id="c40ea-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="c40ea-449">Exécutez la commande suivante à partir du dossier du projet, où `<PW>` est le mot de passe :</span><span class="sxs-lookup"><span data-stu-id="c40ea-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="c40ea-450">Supprimer et mettre à jour la base de données</span><span class="sxs-lookup"><span data-stu-id="c40ea-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="c40ea-451">Redémarrez l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="c40ea-452">Un moyen simple de tester l’application terminée consiste à lancer trois navigateurs différents (ou des sessions incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="c40ea-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="c40ea-453">Dans un navigateur, inscrivez un nouvel utilisateur (par exemple, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="c40ea-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="c40ea-454">Connectez-vous à chaque navigateur avec un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="c40ea-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="c40ea-455">Vérifiez les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="c40ea-455">Verify the following operations:</span></span>

* <span data-ttu-id="c40ea-456">Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.</span><span class="sxs-lookup"><span data-stu-id="c40ea-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="c40ea-457">Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="c40ea-458">Les responsables peuvent approuver/rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="c40ea-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="c40ea-459">La `Details` vue affiche les boutons **approuver** et **rejeter** .</span><span class="sxs-lookup"><span data-stu-id="c40ea-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="c40ea-460">Les administrateurs peuvent approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="c40ea-461">Utilisateur</span><span class="sxs-lookup"><span data-stu-id="c40ea-461">User</span></span>                | <span data-ttu-id="c40ea-462">Amorcé par l’application</span><span class="sxs-lookup"><span data-stu-id="c40ea-462">Seeded by the app</span></span> | <span data-ttu-id="c40ea-463">Options</span><span class="sxs-lookup"><span data-stu-id="c40ea-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="c40ea-464">Non</span><span class="sxs-lookup"><span data-stu-id="c40ea-464">No</span></span>                | <span data-ttu-id="c40ea-465">Modifiez/supprimez les données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="c40ea-466">Oui</span><span class="sxs-lookup"><span data-stu-id="c40ea-466">Yes</span></span>               | <span data-ttu-id="c40ea-467">Approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="c40ea-468">Oui</span><span class="sxs-lookup"><span data-stu-id="c40ea-468">Yes</span></span>               | <span data-ttu-id="c40ea-469">Approuver/refuser et modifier/supprimer toutes les données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="c40ea-470">Créez un contact dans le navigateur de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="c40ea-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="c40ea-471">Copiez l’URL de la suppression et de la modification à partir du contact de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="c40ea-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="c40ea-472">Collez ces liens dans le navigateur de l’utilisateur de test pour vérifier que l’utilisateur de test ne peut pas effectuer ces opérations.</span><span class="sxs-lookup"><span data-stu-id="c40ea-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="c40ea-473">Créer l’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="c40ea-473">Create the starter app</span></span>

* <span data-ttu-id="c40ea-474">Créer une Razor application pages nommée « ContactManager »</span><span class="sxs-lookup"><span data-stu-id="c40ea-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="c40ea-475">Créez l’application avec des **comptes d’utilisateur individuels**.</span><span class="sxs-lookup"><span data-stu-id="c40ea-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="c40ea-476">Nommez-la « ContactManager » pour que l’espace de noms corresponde à l’espace de noms utilisé dans l’exemple.</span><span class="sxs-lookup"><span data-stu-id="c40ea-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="c40ea-477">`-uld`spécifie la base de données locale au lieu de SQLite</span><span class="sxs-lookup"><span data-stu-id="c40ea-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="c40ea-478">Ajouter des *modèles/contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="c40ea-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="c40ea-479">Structurez le `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="c40ea-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="c40ea-480">Créez la migration initiale et mettez à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="c40ea-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="c40ea-481">Mettez à jour l’ancre **ContactManager** dans le fichier *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c40ea-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="c40ea-482">Tester l’application en créant, en modifiant et en supprimant un contact</span><span class="sxs-lookup"><span data-stu-id="c40ea-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="c40ea-483">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="c40ea-483">Seed the database</span></span>

<span data-ttu-id="c40ea-484">Ajoutez la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) au dossier de *données* .</span><span class="sxs-lookup"><span data-stu-id="c40ea-484">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="c40ea-485">Appeler `SeedData.Initialize` à partir de `Main` :</span><span class="sxs-lookup"><span data-stu-id="c40ea-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="c40ea-486">Vérifiez que l’application a amorcé la base de données.</span><span class="sxs-lookup"><span data-stu-id="c40ea-486">Test that the app seeded the database.</span></span> <span data-ttu-id="c40ea-487">Si la base de coordonnées contient des lignes, la méthode Seed ne s’exécute pas.</span><span class="sxs-lookup"><span data-stu-id="c40ea-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="c40ea-488">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c40ea-488">Additional resources</span></span>

* [<span data-ttu-id="c40ea-489">Créer une application Web .NET Core et SQL Database dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c40ea-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="c40ea-490">[ASP.net Core laboratoire d’autorisation](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="c40ea-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="c40ea-491">Ce laboratoire aborde plus en détail les fonctionnalités de sécurité présentées dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="c40ea-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="c40ea-492">Autorisation basée sur une stratégie personnalisée</span><span class="sxs-lookup"><span data-stu-id="c40ea-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
