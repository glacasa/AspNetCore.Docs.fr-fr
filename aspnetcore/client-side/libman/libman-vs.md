---
title: Utilisez LibMan avec ASP.NET Core dans Visual Studio
author: scottaddie
description: Apprenez à utiliser LibMan dans un projet ASP.NET Core avec Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658311"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="056be-103">Utilisez LibMan avec ASP.NET Core dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="056be-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="056be-104">Par [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="056be-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="056be-105">Visual Studio a intégré le soutien de [LibMan](xref:client-side/libman/index) dans ASP.NET projets de base, notamment :</span><span class="sxs-lookup"><span data-stu-id="056be-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="056be-106">Soutien à la configuration et à la gestion de LibMan restaurer les opérations sur la construction.</span><span class="sxs-lookup"><span data-stu-id="056be-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="056be-107">Éléments de menu pour déclencher LibMan restaurer et nettoyer les opérations.</span><span class="sxs-lookup"><span data-stu-id="056be-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="056be-108">Rechercher le dialogue pour trouver des bibliothèques et ajouter les fichiers à un projet.</span><span class="sxs-lookup"><span data-stu-id="056be-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="056be-109">Modification du support pour *libman.json*&mdash;le fichier manifeste LibMan.</span><span class="sxs-lookup"><span data-stu-id="056be-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="056be-110">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(comment télécharger)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="056be-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="056be-111">Prérequis</span><span class="sxs-lookup"><span data-stu-id="056be-111">Prerequisites</span></span>

* <span data-ttu-id="056be-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge **de travail ASP.NET et le développement web**</span><span class="sxs-lookup"><span data-stu-id="056be-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="056be-113">Ajouter des fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="056be-113">Add library files</span></span>

<span data-ttu-id="056be-114">Les dossiers de bibliothèque peuvent être ajoutés à un projet ASP.NET Core de deux façons différentes :</span><span class="sxs-lookup"><span data-stu-id="056be-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="056be-115">Utilisez le dialogue Add Client-Side Library</span><span class="sxs-lookup"><span data-stu-id="056be-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="056be-116">Configurer manuellement les entrées de fichiers manifestes LibMan</span><span class="sxs-lookup"><span data-stu-id="056be-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="056be-117">Utilisez le dialogue Add Client-Side Library</span><span class="sxs-lookup"><span data-stu-id="056be-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="056be-118">Suivez ces étapes pour installer une bibliothèque côté client :</span><span class="sxs-lookup"><span data-stu-id="056be-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="056be-119">Dans **Solution Explorer**, cliquez à droite sur le dossier de projet dans lequel les fichiers doivent être ajoutés.</span><span class="sxs-lookup"><span data-stu-id="056be-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="056be-120">Choisissez **Ajouter** > **la bibliothèque Client-Side**.</span><span class="sxs-lookup"><span data-stu-id="056be-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="056be-121">Le **dialogue Add Client-Side Library** apparaît :</span><span class="sxs-lookup"><span data-stu-id="056be-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Ajouter le dialogue de la bibliothèque client-côté](_static/add-library-dialog.png)

* <span data-ttu-id="056be-123">Sélectionnez le fournisseur de bibliothèque du **fournisseur** déposer.</span><span class="sxs-lookup"><span data-stu-id="056be-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="056be-124">CDNJS est le fournisseur par défaut.</span><span class="sxs-lookup"><span data-stu-id="056be-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="056be-125">Tapez le nom de la bibliothèque pour aller chercher dans la boîte à texte **de la Bibliothèque.**</span><span class="sxs-lookup"><span data-stu-id="056be-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="056be-126">IntelliSense fournit une liste de bibliothèques commençant par le texte fourni.</span><span class="sxs-lookup"><span data-stu-id="056be-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="056be-127">Sélectionnez la bibliothèque de la liste IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="056be-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="056be-128">Notez que le nom de `@` la bibliothèque est suffisant avec le symbole et la dernière version stable connue du fournisseur sélectionné.</span><span class="sxs-lookup"><span data-stu-id="056be-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="056be-129">Décider quels fichiers inclure :</span><span class="sxs-lookup"><span data-stu-id="056be-129">Decide which files to include:</span></span>
  * <span data-ttu-id="056be-130">Sélectionnez **le bouton Inclure tous les fichiers** de bibliothèque pour inclure tous les fichiers de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="056be-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="056be-131">Sélectionnez le bouton radio **De fichiers spécifiques Choisir** pour inclure un sous-ensemble des fichiers de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="056be-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="056be-132">Lorsque le bouton radio est sélectionné, l’arbre du sélecteur de fichiers est activé.</span><span class="sxs-lookup"><span data-stu-id="056be-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="056be-133">Cochez les cases à gauche des noms de fichiers à télécharger.</span><span class="sxs-lookup"><span data-stu-id="056be-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="056be-134">Spécifiez le dossier de projet pour stocker les fichiers dans la boîte de texte **Target Location.**</span><span class="sxs-lookup"><span data-stu-id="056be-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="056be-135">Par recommandation, entreposez chaque bibliothèque dans un dossier séparé.</span><span class="sxs-lookup"><span data-stu-id="056be-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="056be-136">Le dossier **Target Location** suggéré est basé sur l’emplacement à partir duquel le dialogue a été lancé :</span><span class="sxs-lookup"><span data-stu-id="056be-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="056be-137">Si elle est lancée à partir de la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="056be-137">If launched from the project root:</span></span>
    * <span data-ttu-id="056be-138">*wwwroot/lib* est utilisé si *wwwroot* existe.</span><span class="sxs-lookup"><span data-stu-id="056be-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="056be-139">*lib* est utilisé si *wwwroot* n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="056be-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="056be-140">Si elle est lancée à partir d’un dossier de projet, le nom correspondant du dossier est utilisé.</span><span class="sxs-lookup"><span data-stu-id="056be-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="056be-141">La suggestion de dossier est suffixe avec le nom de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="056be-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="056be-142">Le tableau suivant illustre les suggestions de dossier lors de l’installation de jQuery dans un projet Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="056be-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="056be-143">Emplacement de lancement</span><span class="sxs-lookup"><span data-stu-id="056be-143">Launch location</span></span>                           |<span data-ttu-id="056be-144">Dossier suggéré</span><span class="sxs-lookup"><span data-stu-id="056be-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="056be-145">racine du projet (si *wwwroot* existe)</span><span class="sxs-lookup"><span data-stu-id="056be-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="056be-146">*wwwroot/lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="056be-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="056be-147">racine du projet (si *wwwroot* n’existe pas)</span><span class="sxs-lookup"><span data-stu-id="056be-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="056be-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="056be-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="056be-149">*Dossier de pages* dans le projet</span><span class="sxs-lookup"><span data-stu-id="056be-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="056be-150">*Pages/jquery/*</span><span class="sxs-lookup"><span data-stu-id="056be-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="056be-151">Cliquez sur le bouton **Installer** pour télécharger les fichiers, par configuration dans *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="056be-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="056be-152">Examinez l’alimentation **du gestionnaire** de bibliothèque de la fenêtre de sortie pour obtenir **des** détails d’installation.</span><span class="sxs-lookup"><span data-stu-id="056be-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="056be-153">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="056be-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="056be-154">Configurer manuellement les entrées de fichiers manifestes LibMan</span><span class="sxs-lookup"><span data-stu-id="056be-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="056be-155">Toutes les opérations LibMan dans Visual Studio sont basées sur le contenu du manifeste LibMan de la racine du projet (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="056be-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="056be-156">Vous pouvez modifier manuellement *libman.json* pour configurer les fichiers de bibliothèque pour le projet.</span><span class="sxs-lookup"><span data-stu-id="056be-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="056be-157">Visual Studio restaure tous les fichiers de bibliothèque une fois *que libman.json* est enregistré.</span><span class="sxs-lookup"><span data-stu-id="056be-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="056be-158">Pour ouvrir *libman.json* pour l’édition, les options suivantes existent:</span><span class="sxs-lookup"><span data-stu-id="056be-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="056be-159">Double-cliquez sur le fichier *libman.json* dans **Solution Explorer**.</span><span class="sxs-lookup"><span data-stu-id="056be-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="056be-160">Cliquez à droite sur le projet dans **Solution Explorer** et **sélectionnez Gérer les bibliothèques client-côté**.</span><span class="sxs-lookup"><span data-stu-id="056be-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="056be-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="056be-161">**&#8224;**</span></span>
* <span data-ttu-id="056be-162">Sélectionnez **Gérer les bibliothèques client-côté** dans le menu Visual Studio **Project.**</span><span class="sxs-lookup"><span data-stu-id="056be-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="056be-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="056be-163">**&#8224;**</span></span>

<span data-ttu-id="056be-164">**&#8224;** Si le fichier *libman.json* n’existe pas déjà dans la racine du projet, il sera créé avec le contenu du modèle d’élément par défaut.</span><span class="sxs-lookup"><span data-stu-id="056be-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="056be-165">Visual Studio offre un support d’édition JSON riche tels que la colorisation, le formatage, IntelliSense, et la validation de schémas.</span><span class="sxs-lookup"><span data-stu-id="056be-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="056be-166">Le schéma JSON du manifeste LibMan [https://json.schemastore.org/libman](https://json.schemastore.org/libman)se trouve à .</span><span class="sxs-lookup"><span data-stu-id="056be-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="056be-167">Avec le fichier manifeste suivant, LibMan récupère les `libraries` fichiers par configuration définie dans la propriété.</span><span class="sxs-lookup"><span data-stu-id="056be-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="056be-168">Une explication des littérals `libraries` d’objet définis dans les suivants :</span><span class="sxs-lookup"><span data-stu-id="056be-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="056be-169">Un sous-ensemble de [version jQuery](https://jquery.com/) 3.3.1 est récupéré auprès du fournisseur CDNJS.</span><span class="sxs-lookup"><span data-stu-id="056be-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="056be-170">Le sous-ensemble est `files` &mdash;défini dans la propriété*jquery.min.js*, *jquery.js*, et *jquery.min.map*.</span><span class="sxs-lookup"><span data-stu-id="056be-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="056be-171">Les fichiers sont placés dans le dossier *wwwroot/lib/jquery* du projet.</span><span class="sxs-lookup"><span data-stu-id="056be-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="056be-172">L’intégralité de la version [Bootstrap](https://getbootstrap.com/) 4.1.3 est récupérée et placée dans un dossier *wwwroot/lib/bootstrap.*</span><span class="sxs-lookup"><span data-stu-id="056be-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="056be-173">La propriété littérale `provider` de l’objet `defaultProvider` l’emporte sur la valeur de la propriété.</span><span class="sxs-lookup"><span data-stu-id="056be-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="056be-174">LibMan récupère les fichiers Bootstrap du fournisseur de dépkg.</span><span class="sxs-lookup"><span data-stu-id="056be-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="056be-175">Un sous-ensemble de [Lodash](https://lodash.com/) a été approuvé par un organe directeur au sein de l’organisation.</span><span class="sxs-lookup"><span data-stu-id="056be-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="056be-176">Les fichiers *lodash.js* et *lodash.min.js* sont récupérés à partir du système de fichiers local à *C:\\temp\\lodash\\*.</span><span class="sxs-lookup"><span data-stu-id="056be-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="056be-177">Les fichiers sont copiés sur le dossier *wwwroot/lib/lodash* du projet.</span><span class="sxs-lookup"><span data-stu-id="056be-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="056be-178">LibMan ne prend en charge qu’une version de chaque bibliothèque de chaque fournisseur.</span><span class="sxs-lookup"><span data-stu-id="056be-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="056be-179">Le fichier *libman.json* échoue à la validation du schéma s’il contient deux bibliothèques avec le même nom de bibliothèque pour un fournisseur donné.</span><span class="sxs-lookup"><span data-stu-id="056be-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="056be-180">Restaurer les fichiers de la bibliothèque</span><span class="sxs-lookup"><span data-stu-id="056be-180">Restore library files</span></span>

<span data-ttu-id="056be-181">Pour restaurer les fichiers de bibliothèque à partir de Visual Studio, il doit y avoir un fichier *libman.json* valide dans la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="056be-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="056be-182">Les fichiers restaurés sont placés dans le projet à l’endroit spécifié pour chaque bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="056be-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="056be-183">Les dossiers de bibliothèque peuvent être restaurés dans le cadre d’un projet ASP.NET Core de deux façons :</span><span class="sxs-lookup"><span data-stu-id="056be-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="056be-184">Restaurer les fichiers pendant la construction</span><span class="sxs-lookup"><span data-stu-id="056be-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="056be-185">Restaurer manuellement les fichiers</span><span class="sxs-lookup"><span data-stu-id="056be-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="056be-186">Restaurer les fichiers pendant la construction</span><span class="sxs-lookup"><span data-stu-id="056be-186">Restore files during build</span></span>

<span data-ttu-id="056be-187">LibMan peut restaurer les fichiers de bibliothèque définis dans le cadre du processus de construction.</span><span class="sxs-lookup"><span data-stu-id="056be-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="056be-188">Par défaut, le comportement *de restauration sur la construction* est désactivé.</span><span class="sxs-lookup"><span data-stu-id="056be-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="056be-189">Pour activer et tester le comportement de restauration sur la construction :</span><span class="sxs-lookup"><span data-stu-id="056be-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="056be-190">Clic droit *libman.json* dans **Solution Explorer** et **sélectionnez Enable Restore Client-Side Libraries on Build** à partir du menu context.</span><span class="sxs-lookup"><span data-stu-id="056be-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="056be-191">Cliquez sur le bouton **Oui** lorsqu’on vous demande d’installer un forfait NuGet.</span><span class="sxs-lookup"><span data-stu-id="056be-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="056be-192">Le paquet [NuGet Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) est ajouté au projet :</span><span class="sxs-lookup"><span data-stu-id="056be-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="056be-193">Construire le projet pour confirmer la restauration du fichier LibMan se produit.</span><span class="sxs-lookup"><span data-stu-id="056be-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="056be-194">Le `Microsoft.Web.LibraryManager.Build` paquet injecte une cible MSBuild qui exécute LibMan pendant l’opération de construction du projet.</span><span class="sxs-lookup"><span data-stu-id="056be-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="056be-195">Passez en revue le flux **Build** de la fenêtre **de sortie** pour un journal d’activité LibMan :</span><span class="sxs-lookup"><span data-stu-id="056be-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="056be-196">Lorsque le comportement de restauration sur la construction est activé, le menu de contexte *libman.json* affiche une option **Disable Restore Client-Side Libraries on Build.**</span><span class="sxs-lookup"><span data-stu-id="056be-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="056be-197">La sélection de cette `Microsoft.Web.LibraryManager.Build` option supprime la référence du paquet du fichier de projet.</span><span class="sxs-lookup"><span data-stu-id="056be-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="056be-198">Par conséquent, les bibliothèques côté client ne sont plus restaurées sur chaque construction.</span><span class="sxs-lookup"><span data-stu-id="056be-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="056be-199">Indépendamment du réglage de restauration sur la construction, vous pouvez restaurer manuellement à tout moment à partir du menu de contexte *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="056be-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="056be-200">Pour plus d’informations, voir [Restaurer les fichiers manuellement](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="056be-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="056be-201">Restaurer manuellement les fichiers</span><span class="sxs-lookup"><span data-stu-id="056be-201">Restore files manually</span></span>

<span data-ttu-id="056be-202">Pour restaurer manuellement les fichiers de bibliothèque :</span><span class="sxs-lookup"><span data-stu-id="056be-202">To manually restore library files:</span></span>

* <span data-ttu-id="056be-203">Pour tous les projets de la solution :</span><span class="sxs-lookup"><span data-stu-id="056be-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="056be-204">Cliquez à droite sur le nom de la solution dans **Solution Explorer**.</span><span class="sxs-lookup"><span data-stu-id="056be-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="056be-205">Sélectionnez l’option Restaurer les **bibliothèques côté client.**</span><span class="sxs-lookup"><span data-stu-id="056be-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="056be-206">Pour un projet spécifique :</span><span class="sxs-lookup"><span data-stu-id="056be-206">For a specific project:</span></span>
  * <span data-ttu-id="056be-207">Cliquez à droite sur le fichier *libman.json* dans **Solution Explorer**.</span><span class="sxs-lookup"><span data-stu-id="056be-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="056be-208">Sélectionnez l’option Restaurer les **bibliothèques côté client.**</span><span class="sxs-lookup"><span data-stu-id="056be-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="056be-209">Pendant que l’opération de restauration est en cours d’exécution :</span><span class="sxs-lookup"><span data-stu-id="056be-209">While the restore operation is running:</span></span>

* <span data-ttu-id="056be-210">L’icône du Task Status Center (TSC) sur la barre d’état Visual Studio sera animée et lira *l’opération Restaurer commencée.*</span><span class="sxs-lookup"><span data-stu-id="056be-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="056be-211">En cliquant sur l’icône ouvre une boîte à outils énumérant les tâches d’arrière-plan connues.</span><span class="sxs-lookup"><span data-stu-id="056be-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="056be-212">Les messages seront envoyés à la barre d’état et au flux de la fenêtre **De sortie** du gestionnaire de **bibliothèque.**</span><span class="sxs-lookup"><span data-stu-id="056be-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="056be-213">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="056be-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="056be-214">Supprimer les fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="056be-214">Delete library files</span></span>

<span data-ttu-id="056be-215">Pour effectuer l’opération *propre,* qui supprime les fichiers de bibliothèque précédemment restaurés dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="056be-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="056be-216">Cliquez à droite sur le fichier *libman.json* dans **Solution Explorer**.</span><span class="sxs-lookup"><span data-stu-id="056be-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="056be-217">Sélectionnez l’option **Bibliothèques côté client propre.**</span><span class="sxs-lookup"><span data-stu-id="056be-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="056be-218">Pour empêcher la suppression involontaire de fichiers non-bibliothèque, l’opération propre ne supprime pas les répertoires entiers.</span><span class="sxs-lookup"><span data-stu-id="056be-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="056be-219">Il supprime uniquement les fichiers qui ont été inclus dans la restauration précédente.</span><span class="sxs-lookup"><span data-stu-id="056be-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="056be-220">Pendant que l’opération propre est en cours d’exécution:</span><span class="sxs-lookup"><span data-stu-id="056be-220">While the clean operation is running:</span></span>

* <span data-ttu-id="056be-221">L’icône TSC sur le bar d’état Visual Studio sera animée et lira *l’opération des bibliothèques clientes commencée.*</span><span class="sxs-lookup"><span data-stu-id="056be-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="056be-222">En cliquant sur l’icône ouvre une boîte à outils énumérant les tâches d’arrière-plan connues.</span><span class="sxs-lookup"><span data-stu-id="056be-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="056be-223">Les messages sont envoyés à la barre d’état et au flux **library Manager** de la fenêtre **De sortie.**</span><span class="sxs-lookup"><span data-stu-id="056be-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="056be-224">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="056be-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="056be-225">L’opération propre ne supprime que les fichiers du projet.</span><span class="sxs-lookup"><span data-stu-id="056be-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="056be-226">Les fichiers de bibliothèque restent dans le cache pour une récupération plus rapide sur les opérations de restauration futures.</span><span class="sxs-lookup"><span data-stu-id="056be-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="056be-227">Pour gérer les fichiers de bibliothèque stockés dans le cache de la machine locale, utilisez le [LibMan CLI](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="056be-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="056be-228">Désinstaller les fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="056be-228">Uninstall library files</span></span>

<span data-ttu-id="056be-229">Pour désinstaller les fichiers de bibliothèque :</span><span class="sxs-lookup"><span data-stu-id="056be-229">To uninstall library files:</span></span>

* <span data-ttu-id="056be-230">Ouvrez *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="056be-230">Open *libman.json*.</span></span>
* <span data-ttu-id="056be-231">Placez le caret `libraries` à l’intérieur de l’objet correspondant littéral.</span><span class="sxs-lookup"><span data-stu-id="056be-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="056be-232">Cliquez sur l’icône de l’ampoule qui apparaît dans la marge gauche, et **sélectionnez Uninstall \<library_name>\<library_version>**:</span><span class="sxs-lookup"><span data-stu-id="056be-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Désinstaller l’option de menu contextuelle de bibliothèque](_static/uninstall-menu-option.png)

<span data-ttu-id="056be-234">Alternativement, vous pouvez modifier manuellement et enregistrer le manifeste LibMan (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="056be-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="056be-235">[L’opération de restauration s’exécute](#restore-library-files) lorsque le fichier est enregistré.</span><span class="sxs-lookup"><span data-stu-id="056be-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="056be-236">Les fichiers de bibliothèque qui ne sont plus définis dans *libman.json* sont supprimés du projet.</span><span class="sxs-lookup"><span data-stu-id="056be-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="056be-237">Mettre à jour la version de la bibliothèque</span><span class="sxs-lookup"><span data-stu-id="056be-237">Update library version</span></span>

<span data-ttu-id="056be-238">Pour vérifier une version de bibliothèque mise à jour :</span><span class="sxs-lookup"><span data-stu-id="056be-238">To check for an updated library version:</span></span>

* <span data-ttu-id="056be-239">Ouvrez *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="056be-239">Open *libman.json*.</span></span>
* <span data-ttu-id="056be-240">Placez le caret `libraries` à l’intérieur de l’objet correspondant littéral.</span><span class="sxs-lookup"><span data-stu-id="056be-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="056be-241">Cliquez sur l’icône de l’ampoule qui apparaît dans la marge gauche.</span><span class="sxs-lookup"><span data-stu-id="056be-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="056be-242">Planer au-dessus **de vérifier pour les mises à jour**.</span><span class="sxs-lookup"><span data-stu-id="056be-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="056be-243">LibMan vérifie une version bibliothèque plus récente que la version installée.</span><span class="sxs-lookup"><span data-stu-id="056be-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="056be-244">Les résultats suivants peuvent se produire :</span><span class="sxs-lookup"><span data-stu-id="056be-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="056be-245">Un aucun message **trouvé de mise à jour** est affiché si la dernière version est déjà installée.</span><span class="sxs-lookup"><span data-stu-id="056be-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="056be-246">La dernière version stable est affichée si elle n’est pas déjà installée.</span><span class="sxs-lookup"><span data-stu-id="056be-246">The latest stable version is displayed if not already installed.</span></span>

  ![Vérifiez l’option de menu contextuelle mises à jour](_static/update-menu-option.png)

* <span data-ttu-id="056be-248">Si une version préalable plus récente que la version installée est disponible, la pré-version s’affiche.</span><span class="sxs-lookup"><span data-stu-id="056be-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="056be-249">Pour rétrograder à une ancienne version de bibliothèque, modifiez manuellement le fichier *libman.json.*</span><span class="sxs-lookup"><span data-stu-id="056be-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="056be-250">Lorsque le fichier est enregistré, le LibMan [restaurent l’opération](#restore-library-files):</span><span class="sxs-lookup"><span data-stu-id="056be-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="056be-251">Supprime les fichiers redondants de la version précédente.</span><span class="sxs-lookup"><span data-stu-id="056be-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="056be-252">Ajoute des fichiers nouveaux et mis à jour de la nouvelle version.</span><span class="sxs-lookup"><span data-stu-id="056be-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="056be-253">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="056be-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="056be-254">Dépôt GitHub LibMan</span><span class="sxs-lookup"><span data-stu-id="056be-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
