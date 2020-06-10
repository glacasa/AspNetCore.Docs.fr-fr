---
title: Charger des fichiers dans ASP.NET Core
author: rick-anderson
description: Comment utiliser la liaison de modèle et le streaming pour charger des fichiers dans ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/03/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/file-uploads
ms.openlocfilehash: 632cc9fafc5daf2923997f0113adee52491acdcc
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "83838316"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="170a5-103">Charger des fichiers dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="170a5-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="170a5-104">Par [Steve Smith](https://ardalis.com/) et [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="170a5-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="170a5-105">ASP.NET Core prend en charge le chargement d’un ou plusieurs fichiers à l’aide d’une liaison de modèle mise en mémoire tampon pour les fichiers plus petits et la diffusion en continu sans mise en mémoire tampon pour les fichiers plus volumineux</span><span class="sxs-lookup"><span data-stu-id="170a5-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="170a5-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="170a5-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="170a5-107">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="170a5-107">Security considerations</span></span>

<span data-ttu-id="170a5-108">Soyez prudent lorsque vous fournissez aux utilisateurs la possibilité de charger des fichiers sur un serveur.</span><span class="sxs-lookup"><span data-stu-id="170a5-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="170a5-109">Les attaquants peuvent tenter d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="170a5-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="170a5-110">Exécuter [des attaques par déni de service](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="170a5-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="170a5-111">Télécharger des virus ou des programmes malveillants.</span><span class="sxs-lookup"><span data-stu-id="170a5-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="170a5-112">Compromettre les réseaux et les serveurs d’une autre manière.</span><span class="sxs-lookup"><span data-stu-id="170a5-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="170a5-113">Les étapes de sécurité qui réduisent la probabilité d’une attaque réussie sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="170a5-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="170a5-114">Chargez les fichiers dans une zone de chargement de fichier dédiée, de préférence sur un lecteur non-système.</span><span class="sxs-lookup"><span data-stu-id="170a5-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="170a5-115">Un emplacement dédié permet d’imposer des restrictions de sécurité plus faciles sur les fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="170a5-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="170a5-116">Désactivez les autorisations d’exécution sur l’emplacement de chargement du fichier.&dagger;</span><span class="sxs-lookup"><span data-stu-id="170a5-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="170a5-117">Ne conservez **pas** les fichiers téléchargés dans la même arborescence de répertoires que l’application.&dagger;</span><span class="sxs-lookup"><span data-stu-id="170a5-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="170a5-118">Utilisez un nom de fichier sécurisé déterminé par l’application.</span><span class="sxs-lookup"><span data-stu-id="170a5-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="170a5-119">N’utilisez pas un nom de fichier fourni par l’utilisateur ou le nom de fichier non approuvé du fichier téléchargé. &dagger; Code HTML encode le nom de fichier non fiable lors de son affichage.</span><span class="sxs-lookup"><span data-stu-id="170a5-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="170a5-120">Par exemple, la journalisation du nom de fichier ou l’affichage dans l’interface utilisateur ( Razor génère automatiquement le code html).</span><span class="sxs-lookup"><span data-stu-id="170a5-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="170a5-121">Autorisez uniquement les extensions de fichier approuvées pour la spécification de conception de l’application.&dagger;</span><span class="sxs-lookup"><span data-stu-id="170a5-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="170a5-122">Vérifiez que les vérifications côté client sont effectuées sur le serveur. &dagger; Les contrôles côté client sont faciles à contourner.</span><span class="sxs-lookup"><span data-stu-id="170a5-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="170a5-123">Vérifiez la taille d’un fichier téléchargé.</span><span class="sxs-lookup"><span data-stu-id="170a5-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="170a5-124">Définissez une limite de taille maximale pour empêcher les chargements volumineux.&dagger;</span><span class="sxs-lookup"><span data-stu-id="170a5-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="170a5-125">Lorsque les fichiers ne doivent pas être remplacés par un fichier téléchargé portant le même nom, vérifiez le nom du fichier par rapport à la base de données ou au stockage physique avant de charger le fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="170a5-126">**Exécutez un programme de détection de virus et de logiciels malveillants sur le contenu chargé avant que le fichier ne soit stocké.**</span><span class="sxs-lookup"><span data-stu-id="170a5-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="170a5-127">&dagger;L’exemple d’application illustre une approche qui répond aux critères.</span><span class="sxs-lookup"><span data-stu-id="170a5-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="170a5-128">Le chargement d’un code malveillant sur un système est généralement la première étape de l’exécution de code capable de :</span><span class="sxs-lookup"><span data-stu-id="170a5-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="170a5-129">Maîtrisez complètement un système.</span><span class="sxs-lookup"><span data-stu-id="170a5-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="170a5-130">Surchargez un système avec le résultat du blocage du système.</span><span class="sxs-lookup"><span data-stu-id="170a5-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="170a5-131">Compromettre les données utilisateur ou système.</span><span class="sxs-lookup"><span data-stu-id="170a5-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="170a5-132">Appliquez Graffiti à une interface utilisateur publique.</span><span class="sxs-lookup"><span data-stu-id="170a5-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="170a5-133">Pour plus d’informations sur la réduction de la surface d’attaque quand vous acceptez des fichiers d’utilisateurs, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="170a5-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="170a5-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)</span><span class="sxs-lookup"><span data-stu-id="170a5-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="170a5-135">Sécurité Azure : Vérifier que les contrôles appropriés sont en place quand vous acceptez des fichiers d’utilisateurs</span><span class="sxs-lookup"><span data-stu-id="170a5-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="170a5-136">Pour plus d’informations sur l’implémentation de mesures de sécurité, y compris des exemples de l’exemple d’application, consultez la section [validation](#validation) .</span><span class="sxs-lookup"><span data-stu-id="170a5-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="170a5-137">Scénarios de stockage</span><span class="sxs-lookup"><span data-stu-id="170a5-137">Storage scenarios</span></span>

<span data-ttu-id="170a5-138">Les options de stockage courantes pour les fichiers sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="170a5-138">Common storage options for files include:</span></span>

* <span data-ttu-id="170a5-139">Base de données</span><span class="sxs-lookup"><span data-stu-id="170a5-139">Database</span></span>

  * <span data-ttu-id="170a5-140">Pour les petits chargements de fichiers, une base de données est souvent plus rapide que les options de stockage physique (système de fichiers ou partage réseau).</span><span class="sxs-lookup"><span data-stu-id="170a5-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="170a5-141">Une base de données est souvent plus pratique que les options de stockage physique, car la récupération d’un enregistrement de base de données pour les données utilisateur peut fournir simultanément le contenu du fichier (par exemple, une image de l’avatar).</span><span class="sxs-lookup"><span data-stu-id="170a5-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="170a5-142">Une base de données est potentiellement moins coûteuse que l’utilisation d’un service de stockage de données.</span><span class="sxs-lookup"><span data-stu-id="170a5-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="170a5-143">Stockage physique (système de fichiers ou partage réseau)</span><span class="sxs-lookup"><span data-stu-id="170a5-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="170a5-144">Pour les chargements de fichiers volumineux :</span><span class="sxs-lookup"><span data-stu-id="170a5-144">For large file uploads:</span></span>
    * <span data-ttu-id="170a5-145">Les limites de base de données peuvent limiter la taille du téléchargement.</span><span class="sxs-lookup"><span data-stu-id="170a5-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="170a5-146">Le stockage physique est souvent moins économique que le stockage dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="170a5-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="170a5-147">Le stockage physique est potentiellement moins onéreux que l’utilisation d’un service de stockage de données.</span><span class="sxs-lookup"><span data-stu-id="170a5-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="170a5-148">Le processus de l’application doit disposer d’autorisations en lecture et en écriture sur l’emplacement de stockage.</span><span class="sxs-lookup"><span data-stu-id="170a5-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="170a5-149">**N’accordez jamais l’autorisation EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="170a5-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="170a5-150">Service de stockage de données (par exemple, [stockage d’objets BLOB Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="170a5-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="170a5-151">Les services offrent généralement une évolutivité et une résilience améliorées par rapport aux solutions locales qui sont généralement sujettes à des points de défaillance uniques.</span><span class="sxs-lookup"><span data-stu-id="170a5-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="170a5-152">Les services sont potentiellement moins coûteux dans les scénarios d’infrastructure de stockage volumineux.</span><span class="sxs-lookup"><span data-stu-id="170a5-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="170a5-153">Pour plus d’informations, consultez [démarrage rapide : utiliser .net pour créer un objet BLOB dans le stockage d’objets](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="170a5-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="170a5-154">Scénarios de chargement de fichiers</span><span class="sxs-lookup"><span data-stu-id="170a5-154">File upload scenarios</span></span>

<span data-ttu-id="170a5-155">La mise en mémoire tampon et la diffusion en continu sont deux approches générales pour le téléchargement de fichiers.</span><span class="sxs-lookup"><span data-stu-id="170a5-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="170a5-156">**des réponses**</span><span class="sxs-lookup"><span data-stu-id="170a5-156">**Buffering**</span></span>

<span data-ttu-id="170a5-157">La totalité du fichier est lue dans un <xref:Microsoft.AspNetCore.Http.IFormFile> , qui est une représentation C# du fichier utilisé pour traiter ou enregistrer le fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="170a5-158">Les ressources (disque, mémoire) utilisées par les chargements de fichiers dépendent du nombre et de la taille des chargements de fichiers simultanés.</span><span class="sxs-lookup"><span data-stu-id="170a5-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="170a5-159">Si une application tente de mettre en mémoire tampon un trop grand nombre de chargements, le site se bloque lorsqu’il manque de mémoire ou d’espace disque.</span><span class="sxs-lookup"><span data-stu-id="170a5-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="170a5-160">Si la taille ou la fréquence des chargements de fichiers épuisent les ressources d’application, utilisez la diffusion en continu.</span><span class="sxs-lookup"><span data-stu-id="170a5-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="170a5-161">Tout fichier mis en mémoire tampon dépassant 64 Ko est déplacé de la mémoire vers un fichier temporaire sur le disque.</span><span class="sxs-lookup"><span data-stu-id="170a5-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="170a5-162">La mise en mémoire tampon de petits fichiers est traitée dans les sections suivantes de cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="170a5-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="170a5-163">Stockage physique</span><span class="sxs-lookup"><span data-stu-id="170a5-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="170a5-164">Sauvegarde de la base de données</span><span class="sxs-lookup"><span data-stu-id="170a5-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="170a5-165">**Diffusion en continu**</span><span class="sxs-lookup"><span data-stu-id="170a5-165">**Streaming**</span></span>

<span data-ttu-id="170a5-166">Le fichier est reçu à partir d’une demande en plusieurs parties et est directement traité ou enregistré par l’application.</span><span class="sxs-lookup"><span data-stu-id="170a5-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="170a5-167">La diffusion en continu n’améliore pas les performances de manière significative.</span><span class="sxs-lookup"><span data-stu-id="170a5-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="170a5-168">La diffusion en continu réduit les demandes de mémoire ou d’espace disque lors du chargement de fichiers.</span><span class="sxs-lookup"><span data-stu-id="170a5-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="170a5-169">La diffusion en continu de fichiers volumineux est traitée dans la section [chargement de fichiers volumineux avec diffusion](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="170a5-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="170a5-170">Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers un stockage physique</span><span class="sxs-lookup"><span data-stu-id="170a5-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="170a5-171">Pour télécharger des fichiers de petite taille, utilisez un formulaire en plusieurs parties ou créez une requête de publication à l’aide de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="170a5-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="170a5-172">L’exemple suivant illustre l’utilisation d’un Razor formulaire de pages pour télécharger un seul fichier (*pages/BufferedSingleFileUploadPhysical. cshtml* dans l’exemple d’application) :</span><span class="sxs-lookup"><span data-stu-id="170a5-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="170a5-173">L’exemple suivant est similaire à l’exemple précédent, à l’exception de :</span><span class="sxs-lookup"><span data-stu-id="170a5-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="170a5-174">JavaScript ([API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API)) est utilisé pour envoyer les données du formulaire.</span><span class="sxs-lookup"><span data-stu-id="170a5-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="170a5-175">Il n’y a aucune validation.</span><span class="sxs-lookup"><span data-stu-id="170a5-175">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="170a5-176">Pour exécuter la publication de formulaire dans JavaScript pour les clients qui [ne prennent pas en charge l’API FETCH](https://caniuse.com/#feat=fetch), utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="170a5-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="170a5-177">Utilisez un Polyfill d’extraction (par exemple, [Window. Fetch Polyfill (GitHub/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="170a5-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="170a5-178">Utilisez `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="170a5-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="170a5-179">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="170a5-179">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="170a5-180">Afin de prendre en charge les chargements de fichiers, les formulaires HTML doivent spécifier un type d’encodage ( `enctype` ) de `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="170a5-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="170a5-181">Pour `files` qu’un élément INPUT prenne en charge le téléchargement de plusieurs fichiers, fournissez l' `multiple` attribut sur l' `<input>` élément :</span><span class="sxs-lookup"><span data-stu-id="170a5-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="170a5-182">Les fichiers individuels téléchargés sur le serveur sont accessibles via la [liaison de modèle](xref:mvc/models/model-binding) à l’aide de <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="170a5-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="170a5-183">L’exemple d’application illustre plusieurs chargements de fichiers mis en mémoire tampon pour les scénarios de base de données et de stockage physique.</span><span class="sxs-lookup"><span data-stu-id="170a5-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="170a5-184">N’utilisez **pas** la `FileName` propriété <xref:Microsoft.AspNetCore.Http.IFormFile> autre que pour l’affichage et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="170a5-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="170a5-185">Lors de l’affichage ou de la journalisation, le nom du fichier est encodé au format HTML.</span><span class="sxs-lookup"><span data-stu-id="170a5-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="170a5-186">Une personne malveillante peut fournir un nom de fichier malveillant, y compris les chemins d’accès complets ou les chemins d’accès relatifs.</span><span class="sxs-lookup"><span data-stu-id="170a5-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="170a5-187">Les applications doivent :</span><span class="sxs-lookup"><span data-stu-id="170a5-187">Applications should:</span></span>
>
> * <span data-ttu-id="170a5-188">Supprimez le chemin d’accès du nom de fichier fourni par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="170a5-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="170a5-189">Enregistrez le nom de fichier encodé au format HTML, avec chemin d’accès supprimé pour l’interface utilisateur ou la journalisation.</span><span class="sxs-lookup"><span data-stu-id="170a5-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="170a5-190">Générez un nouveau nom de fichier aléatoire pour le stockage.</span><span class="sxs-lookup"><span data-stu-id="170a5-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="170a5-191">Le code suivant supprime le chemin d’accès du nom de fichier :</span><span class="sxs-lookup"><span data-stu-id="170a5-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="170a5-192">Les exemples fournis jusqu’à présent ne prennent pas en compte les considérations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="170a5-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="170a5-193">Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="170a5-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="170a5-194">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="170a5-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="170a5-195">Validation</span><span class="sxs-lookup"><span data-stu-id="170a5-195">Validation</span></span>](#validation)

<span data-ttu-id="170a5-196">Lors du chargement de fichiers à l’aide d’une liaison de modèle et <xref:Microsoft.AspNetCore.Http.IFormFile> , la méthode d’action peut accepter les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="170a5-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="170a5-197">Une seule <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="170a5-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="170a5-198">L’un des regroupements suivants qui représentent plusieurs fichiers :</span><span class="sxs-lookup"><span data-stu-id="170a5-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="170a5-199">[Tarifs](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="170a5-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="170a5-200">La liaison correspond aux fichiers de formulaire par nom.</span><span class="sxs-lookup"><span data-stu-id="170a5-200">Binding matches form files by name.</span></span> <span data-ttu-id="170a5-201">Par exemple, la `name` valeur html dans `<input type="file" name="formFile">` doit correspondre au paramètre C#/lié à la propriété ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="170a5-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="170a5-202">Pour plus d’informations, consultez la section valeur de l' [attribut de nom de correspondance pour le nom de paramètre de la méthode de publication](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="170a5-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="170a5-203">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="170a5-203">The following example:</span></span>

* <span data-ttu-id="170a5-204">Effectue une boucle sur un ou plusieurs fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="170a5-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="170a5-205">Utilise [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour retourner le chemin d’accès complet d’un fichier, y compris le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="170a5-206">Enregistre les fichiers dans le système de fichiers local à l’aide d’un nom de fichier généré par l’application.</span><span class="sxs-lookup"><span data-stu-id="170a5-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="170a5-207">Retourne le nombre total et la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="170a5-207">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="170a5-208">Utilisez `Path.GetRandomFileName` pour générer un nom de fichier sans chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="170a5-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="170a5-209">Dans l’exemple suivant, le chemin d’accès est obtenu à partir de la configuration :</span><span class="sxs-lookup"><span data-stu-id="170a5-209">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="170a5-210">Le chemin d’accès passé à <xref:System.IO.FileStream> *doit* inclure le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="170a5-211">Si le nom de fichier n’est pas fourni, une <xref:System.UnauthorizedAccessException> exception est levée au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="170a5-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="170a5-212">Les fichiers chargés à l’aide de la <xref:Microsoft.AspNetCore.Http.IFormFile> technique sont mis en mémoire tampon ou sur disque sur le serveur avant le traitement.</span><span class="sxs-lookup"><span data-stu-id="170a5-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="170a5-213">À l’intérieur de la méthode d’action, le <xref:Microsoft.AspNetCore.Http.IFormFile> contenu est accessible en tant que <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="170a5-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="170a5-214">Outre le système de fichiers local, les fichiers peuvent être enregistrés sur un partage réseau ou un service de stockage de fichiers, tel que le [stockage d’objets BLOB Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="170a5-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="170a5-215">Pour obtenir un autre exemple qui effectue une boucle sur plusieurs fichiers pour le téléchargement et utilise des noms de fichiers sécurisés, consultez *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="170a5-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="170a5-216">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) lève une exception <xref:System.IO.IOException> si plus de 65 535 fichiers sont créés sans supprimer les fichiers temporaires précédents.</span><span class="sxs-lookup"><span data-stu-id="170a5-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="170a5-217">La limite de 65 535 fichiers est une limite par serveur.</span><span class="sxs-lookup"><span data-stu-id="170a5-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="170a5-218">Pour plus d’informations sur cette limite sur le système d’exploitation Windows, consultez les notes dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="170a5-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="170a5-219">GetTempFileNameA fonction)</span><span class="sxs-lookup"><span data-stu-id="170a5-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="170a5-220">Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers une base de données</span><span class="sxs-lookup"><span data-stu-id="170a5-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="170a5-221">Pour stocker des données de fichier binaires dans une base de données à l’aide de [Entity Framework](/ef/core/index), définissez une <xref:System.Byte> propriété de tableau sur l’entité :</span><span class="sxs-lookup"><span data-stu-id="170a5-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="170a5-222">Spécifiez une propriété de modèle de page pour la classe qui comprend <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="170a5-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="170a5-223"><xref:Microsoft.AspNetCore.Http.IFormFile>peut être utilisé directement comme paramètre de méthode d’action ou comme propriété de modèle liée.</span><span class="sxs-lookup"><span data-stu-id="170a5-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="170a5-224">L’exemple précédent utilise une propriété de modèle liée.</span><span class="sxs-lookup"><span data-stu-id="170a5-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="170a5-225">Le `FileUpload` est utilisé dans le Razor formulaire pages :</span><span class="sxs-lookup"><span data-stu-id="170a5-225">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="170a5-226">Lorsque le formulaire est publié sur le serveur, copiez <xref:Microsoft.AspNetCore.Http.IFormFile> -le dans un flux et enregistrez-le en tant que tableau d’octets dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="170a5-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="170a5-227">Dans l’exemple suivant, `_dbContext` stocke le contexte de base de données de l’application :</span><span class="sxs-lookup"><span data-stu-id="170a5-227">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="170a5-228">L’exemple précédent est semblable à un scénario illustré dans l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="170a5-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="170a5-229">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="170a5-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="170a5-230">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="170a5-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="170a5-231">Soyez prudent quand vous stockez des données binaires dans des bases de données relationnelles, car cela peut avoir un impact négatif sur les performances.</span><span class="sxs-lookup"><span data-stu-id="170a5-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="170a5-232">Ne vous fiez pas à la `FileName` propriété de sans validation ni à approuver celle-ci <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="170a5-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="170a5-233">La `FileName` propriété doit uniquement être utilisée à des fins d’affichage et uniquement après l’encodage HTML.</span><span class="sxs-lookup"><span data-stu-id="170a5-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="170a5-234">Les exemples fournis ne prennent pas en compte les considérations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="170a5-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="170a5-235">Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="170a5-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="170a5-236">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="170a5-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="170a5-237">Validation</span><span class="sxs-lookup"><span data-stu-id="170a5-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="170a5-238">Charger des fichiers volumineux avec streaming</span><span class="sxs-lookup"><span data-stu-id="170a5-238">Upload large files with streaming</span></span>

<span data-ttu-id="170a5-239">L’exemple suivant montre comment utiliser JavaScript pour diffuser un fichier vers une action de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="170a5-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="170a5-240">Le jeton anti-contrefaçon du fichier est généré à l’aide d’un attribut de filtre personnalisé et transmis aux en-têtes HTTP du client plutôt que dans le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="170a5-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="170a5-241">Étant donné que la méthode d’action traite directement les données chargées, la liaison de modèle de formulaire est désactivée par un autre filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="170a5-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="170a5-242">Dans l’action, le contenu du formulaire est lu en avec `MultipartReader`, qui lit chaque `MultipartSection` individuelle, traitant le fichier ou enregistrant le contenu selon ce qui est approprié.</span><span class="sxs-lookup"><span data-stu-id="170a5-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="170a5-243">Une fois les sections en plusieurs parties lues, l’action effectue sa propre liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="170a5-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="170a5-244">La réponse de page initiale charge le formulaire et enregistre un jeton anti-contrefaçon dans un cookie (via l' `GenerateAntiforgeryTokenCookieAttribute` attribut).</span><span class="sxs-lookup"><span data-stu-id="170a5-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="170a5-245">L’attribut utilise la [prise en charge de l’anticontrefaçon](xref:security/anti-request-forgery) intégrée de ASP.net Core pour définir un cookie avec un jeton de demande :</span><span class="sxs-lookup"><span data-stu-id="170a5-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="170a5-246">`DisableFormValueModelBindingAttribute`Est utilisé pour désactiver la liaison de modèle :</span><span class="sxs-lookup"><span data-stu-id="170a5-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="170a5-247">Dans l’exemple d’application, `GenerateAntiforgeryTokenCookieAttribute` et `DisableFormValueModelBindingAttribute` sont appliqués en tant que filtres aux modèles d’application de page de et à l' `/StreamedSingleFileUploadDb` aide des conventions de `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="170a5-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="170a5-248">Étant donné que la liaison de modèle ne lit pas le formulaire, les paramètres qui sont liés depuis le formulaire ne sont pas liés (la requête, l’itinéraire et l’en-tête continuent de fonctionner).</span><span class="sxs-lookup"><span data-stu-id="170a5-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="170a5-249">La méthode d’action fonctionne directement avec la `Request` propriété.</span><span class="sxs-lookup"><span data-stu-id="170a5-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="170a5-250">Un `MultipartReader` est utilisé pour lire chaque section.</span><span class="sxs-lookup"><span data-stu-id="170a5-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="170a5-251">Les données de clé/valeur sont stockées dans un `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="170a5-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="170a5-252">Une fois les sections en plusieurs parties lues, le contenu du `KeyValueAccumulator` est utilisé pour lier les données de formulaire à un type de modèle.</span><span class="sxs-lookup"><span data-stu-id="170a5-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="170a5-253">La `StreamingController.UploadDatabase` méthode complète pour la diffusion en continu vers une base de données avec EF Core :</span><span class="sxs-lookup"><span data-stu-id="170a5-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="170a5-254">`MultipartRequestHelper`(*Utilities/MultipartRequestHelper. cs*) :</span><span class="sxs-lookup"><span data-stu-id="170a5-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="170a5-255">Méthode complète `StreamingController.UploadPhysical` pour la diffusion en continu vers un emplacement physique :</span><span class="sxs-lookup"><span data-stu-id="170a5-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="170a5-256">Dans l’exemple d’application, les contrôles de validation sont gérés par `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="170a5-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="170a5-257">Validation</span><span class="sxs-lookup"><span data-stu-id="170a5-257">Validation</span></span>

<span data-ttu-id="170a5-258">La classe de l’exemple d’application `FileHelpers` illustre plusieurs vérifications des chargements de fichiers mis en mémoire tampon <xref:Microsoft.AspNetCore.Http.IFormFile> et diffusés en continu.</span><span class="sxs-lookup"><span data-stu-id="170a5-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="170a5-259">Pour traiter les <xref:Microsoft.AspNetCore.Http.IFormFile> chargements de fichiers mis en mémoire tampon dans l’exemple d’application, consultez la `ProcessFormFile` méthode dans le fichier *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="170a5-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="170a5-260">Pour le traitement de fichiers en continu, consultez la `ProcessStreamedFile` méthode dans le même fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="170a5-261">Les méthodes de traitement de validation présentées dans l’exemple d’application n’analysent pas le contenu des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="170a5-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="170a5-262">Dans la plupart des scénarios de production, une API de détection de virus et de logiciels malveillants est utilisée sur le fichier avant que le fichier soit mis à la disposition des utilisateurs ou d’autres systèmes.</span><span class="sxs-lookup"><span data-stu-id="170a5-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="170a5-263">Bien que l’exemple de rubrique fournit un exemple fonctionnel de techniques de validation, n’implémentez pas la `FileHelpers` classe dans une application de production, sauf si vous :</span><span class="sxs-lookup"><span data-stu-id="170a5-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="170a5-264">Comprenez pleinement l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="170a5-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="170a5-265">Modifiez l’implémentation en fonction de l’environnement et des spécifications de l’application.</span><span class="sxs-lookup"><span data-stu-id="170a5-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="170a5-266">**N’implémentez jamais non plus de code de sécurité dans une application sans répondre à ces exigences.**</span><span class="sxs-lookup"><span data-stu-id="170a5-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="170a5-267">Validation du contenu</span><span class="sxs-lookup"><span data-stu-id="170a5-267">Content validation</span></span>

<span data-ttu-id="170a5-268">**Utilisez une API d’analyse de virus/programme malveillant sur le contenu chargé.**</span><span class="sxs-lookup"><span data-stu-id="170a5-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="170a5-269">L’analyse des fichiers exige des ressources serveur dans des scénarios de volume élevé.</span><span class="sxs-lookup"><span data-stu-id="170a5-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="170a5-270">Si les performances de traitement des demandes sont réduites en raison de l’analyse de fichiers, envisagez de décharger le travail d’analyse vers un [service en arrière-plan](xref:fundamentals/host/hosted-services), éventuellement un service qui s’exécute sur un serveur différent du serveur de l’application.</span><span class="sxs-lookup"><span data-stu-id="170a5-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="170a5-271">En règle générale, les fichiers téléchargés sont conservés dans une zone en quarantaine jusqu’à ce que l’antivirus en arrière-plan les vérifie.</span><span class="sxs-lookup"><span data-stu-id="170a5-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="170a5-272">Lorsqu’un fichier réussit, le fichier est déplacé vers l’emplacement de stockage de fichiers normal.</span><span class="sxs-lookup"><span data-stu-id="170a5-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="170a5-273">Ces étapes sont généralement effectuées conjointement avec un enregistrement de base de données qui indique l’état d’analyse d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="170a5-274">À l’aide de cette approche, l’application et le serveur d’applications restent concentrés sur la réponse aux requêtes.</span><span class="sxs-lookup"><span data-stu-id="170a5-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="170a5-275">Validation de l’extension de fichier</span><span class="sxs-lookup"><span data-stu-id="170a5-275">File extension validation</span></span>

<span data-ttu-id="170a5-276">L’extension du fichier chargé doit être vérifiée par rapport à une liste d’extensions autorisées.</span><span class="sxs-lookup"><span data-stu-id="170a5-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="170a5-277">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="170a5-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="170a5-278">Validation de la signature de fichier</span><span class="sxs-lookup"><span data-stu-id="170a5-278">File signature validation</span></span>

<span data-ttu-id="170a5-279">La signature d’un fichier est déterminée par les premiers octets au début d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="170a5-280">Ces octets peuvent être utilisés pour indiquer si l’extension correspond au contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="170a5-281">L’exemple d’application vérifie les signatures de fichiers pour quelques types de fichiers courants.</span><span class="sxs-lookup"><span data-stu-id="170a5-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="170a5-282">Dans l’exemple suivant, la signature de fichier pour une image JPEG est vérifiée par rapport au fichier :</span><span class="sxs-lookup"><span data-stu-id="170a5-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="170a5-283">Pour obtenir des signatures de fichiers supplémentaires, consultez la [base de données signatures de fichiers](https://www.filesignatures.net/) et les spécifications de fichier officielles.</span><span class="sxs-lookup"><span data-stu-id="170a5-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="170a5-284">Sécurité des noms de fichiers</span><span class="sxs-lookup"><span data-stu-id="170a5-284">File name security</span></span>

<span data-ttu-id="170a5-285">N’utilisez jamais un nom de fichier fourni par le client pour enregistrer un fichier dans le stockage physique.</span><span class="sxs-lookup"><span data-stu-id="170a5-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="170a5-286">Créez un nom de fichier sécurisé pour le fichier à l’aide de [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour créer un chemin d’accès complet (y compris le nom de fichier) pour le stockage temporaire.</span><span class="sxs-lookup"><span data-stu-id="170a5-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="170a5-287">encode automatiquement les valeurs des propriétés pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="170a5-287"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="170a5-288">Le code suivant peut être utilisé en toute sécurité :</span><span class="sxs-lookup"><span data-stu-id="170a5-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="170a5-289">En dehors de Razor , toujours <xref:System.Net.WebUtility.HtmlEncode*> le contenu du nom de fichier à partir de la demande d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="170a5-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="170a5-290">De nombreuses implémentations doivent inclure une vérification de l’existence du fichier ; dans le cas contraire, le fichier est remplacé par un fichier du même nom.</span><span class="sxs-lookup"><span data-stu-id="170a5-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="170a5-291">Fournissez une logique supplémentaire pour répondre aux spécifications de votre application.</span><span class="sxs-lookup"><span data-stu-id="170a5-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="170a5-292">Validation de la taille</span><span class="sxs-lookup"><span data-stu-id="170a5-292">Size validation</span></span>

<span data-ttu-id="170a5-293">Limitez la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="170a5-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="170a5-294">Dans l’exemple d’application, la taille du fichier est limitée à 2 Mo (indiquée en octets).</span><span class="sxs-lookup"><span data-stu-id="170a5-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="170a5-295">La limite est fournie via la [configuration](xref:fundamentals/configuration/index) à partir du fichier *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="170a5-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="170a5-296">`FileSizeLimit`Est injecté dans des `PageModel` classes :</span><span class="sxs-lookup"><span data-stu-id="170a5-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="170a5-297">Quand une taille de fichier dépasse la limite, le fichier est rejeté :</span><span class="sxs-lookup"><span data-stu-id="170a5-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="170a5-298">Valeur de l’attribut de nom de correspondance avec le nom de paramètre de la méthode de publication</span><span class="sxs-lookup"><span data-stu-id="170a5-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="170a5-299">Dans les non- Razor formulaires qui publient des données de formulaire ou utilisent `FormData` directement JavaScript, le nom spécifié dans l’élément du formulaire ou `FormData` doit correspondre au nom du paramètre dans l’action du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="170a5-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="170a5-300">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="170a5-300">In the following example:</span></span>

* <span data-ttu-id="170a5-301">Lorsque vous utilisez un `<input>` élément, l' `name` attribut est défini sur la valeur `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="170a5-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="170a5-302">Lors de l’utilisation `FormData` de dans JavaScript, le nom est défini sur la valeur `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="170a5-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="170a5-303">Utilisez un nom correspondant pour le paramètre de la méthode C# ( `battlePlans` ) :</span><span class="sxs-lookup"><span data-stu-id="170a5-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="170a5-304">Pour une Razor méthode de gestionnaire de page pages nommée `Upload` :</span><span class="sxs-lookup"><span data-stu-id="170a5-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="170a5-305">Pour une méthode d’action du billet de contrôleur MVC :</span><span class="sxs-lookup"><span data-stu-id="170a5-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="170a5-306">Configuration du serveur et de l’application</span><span class="sxs-lookup"><span data-stu-id="170a5-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="170a5-307">Longueur limite du corps en plusieurs parties</span><span class="sxs-lookup"><span data-stu-id="170a5-307">Multipart body length limit</span></span>

<span data-ttu-id="170a5-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>définit la limite de la longueur de chaque corps en plusieurs parties.</span><span class="sxs-lookup"><span data-stu-id="170a5-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="170a5-309">Les sections de formulaire qui dépassent cette limite lèvent une lorsqu’elles sont <xref:System.IO.InvalidDataException> analysées.</span><span class="sxs-lookup"><span data-stu-id="170a5-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="170a5-310">La valeur par défaut est 134 217 728 (128 Mo).</span><span class="sxs-lookup"><span data-stu-id="170a5-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="170a5-311">Personnaliser la limite à l’aide du <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> paramètre dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="170a5-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="170a5-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>est utilisé pour définir le <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pour une seule page ou action.</span><span class="sxs-lookup"><span data-stu-id="170a5-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="170a5-313">Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="170a5-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    });
```

<span data-ttu-id="170a5-314">Dans une Razor application de pages ou une application MVC, appliquez le filtre au modèle de page ou à la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="170a5-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="170a5-315">Taille maximale du corps de la demande Kestrel</span><span class="sxs-lookup"><span data-stu-id="170a5-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="170a5-316">Pour les applications hébergées par Kestrel, la taille de corps de requête maximale par défaut est de 30 millions octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="170a5-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="170a5-317">Personnaliser la limite à l’aide de l’option de serveur [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel :</span><span class="sxs-lookup"><span data-stu-id="170a5-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="170a5-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>est utilisé pour définir le [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pour une seule page ou action.</span><span class="sxs-lookup"><span data-stu-id="170a5-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="170a5-319">Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="170a5-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    });
```

<span data-ttu-id="170a5-320">Dans une Razor application de pages ou une application MVC, appliquez le filtre à la classe du gestionnaire de pages ou à la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="170a5-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="170a5-321">`RequestSizeLimitAttribute`Peut également être appliqué à l’aide de la [`@attribute`](xref:mvc/views/razor#attribute) Razor directive :</span><span class="sxs-lookup"><span data-stu-id="170a5-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="170a5-322">Autres limites Kestrel</span><span class="sxs-lookup"><span data-stu-id="170a5-322">Other Kestrel limits</span></span>

<span data-ttu-id="170a5-323">D’autres limites Kestrel peuvent s’appliquer aux applications hébergées par Kestrel :</span><span class="sxs-lookup"><span data-stu-id="170a5-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="170a5-324">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="170a5-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="170a5-325">Taux de données de demande et de réponse</span><span class="sxs-lookup"><span data-stu-id="170a5-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="170a5-326">Limite de longueur du contenu IIS</span><span class="sxs-lookup"><span data-stu-id="170a5-326">IIS content length limit</span></span>

<span data-ttu-id="170a5-327">La limite de demandes par défaut ( `maxAllowedContentLength` ) est de 30 millions octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="170a5-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="170a5-328">Personnaliser la limite dans le fichier *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="170a5-328">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="170a5-329">Ce paramètre s’applique seulement à IIS.</span><span class="sxs-lookup"><span data-stu-id="170a5-329">This setting only applies to IIS.</span></span> <span data-ttu-id="170a5-330">Par défaut, ce comportement ne se produit pas dans le cas d’un hébergement sur Kestrel.</span><span class="sxs-lookup"><span data-stu-id="170a5-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="170a5-331">Pour plus d’informations, consultez [limites \<requestLimits> des demandes ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="170a5-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="170a5-332">Les limitations du module ASP.NET Core ou la présence du module de filtrage des demandes IIS peuvent limiter les chargements à 2 ou 4 Go.</span><span class="sxs-lookup"><span data-stu-id="170a5-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="170a5-333">Pour plus d’informations, consultez [Impossible de télécharger un fichier d’une taille supérieure à 2 Go (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="170a5-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="170a5-334">Dépanner</span><span class="sxs-lookup"><span data-stu-id="170a5-334">Troubleshoot</span></span>

<span data-ttu-id="170a5-335">Voici certains problèmes courants rencontrés avec le chargement de fichiers et leurs solutions possibles.</span><span class="sxs-lookup"><span data-stu-id="170a5-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="170a5-336">Erreur introuvable lors du déploiement sur un serveur IIS</span><span class="sxs-lookup"><span data-stu-id="170a5-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="170a5-337">L’erreur suivante indique que le fichier chargé dépasse la longueur du contenu configurée du serveur :</span><span class="sxs-lookup"><span data-stu-id="170a5-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="170a5-338">Pour plus d’informations sur l’amélioration de la limite, consultez la section [limite de longueur du contenu IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="170a5-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="170a5-339">Échec de connexion</span><span class="sxs-lookup"><span data-stu-id="170a5-339">Connection failure</span></span>

<span data-ttu-id="170a5-340">Une erreur de connexion et une connexion du serveur de réinitialisation indiquent probablement que le fichier téléchargé dépasse la taille maximale du corps de la demande de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="170a5-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="170a5-341">Pour plus d’informations, consultez la section [taille maximale du corps de la demande Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="170a5-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="170a5-342">Les limites de connexion du client Kestrel peuvent également nécessiter des ajustements.</span><span class="sxs-lookup"><span data-stu-id="170a5-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="170a5-343">Exception de référence null avec IFormFile</span><span class="sxs-lookup"><span data-stu-id="170a5-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="170a5-344">Si le contrôleur accepte les fichiers téléchargés à l’aide <xref:Microsoft.AspNetCore.Http.IFormFile> de mais que la valeur est `null` , vérifiez que le formulaire HTML spécifie la `enctype` valeur `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="170a5-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="170a5-345">Si cet attribut n’est pas défini sur l' `<form>` élément, le chargement du fichier ne se produit pas et tous les <xref:Microsoft.AspNetCore.Http.IFormFile> arguments liés sont `null` .</span><span class="sxs-lookup"><span data-stu-id="170a5-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="170a5-346">Vérifiez également que l' [appellation de chargement dans les données de formulaire correspond à celle de l’application](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="170a5-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="170a5-347">Le flux est trop long</span><span class="sxs-lookup"><span data-stu-id="170a5-347">Stream was too long</span></span>

<span data-ttu-id="170a5-348">Les exemples de cette rubrique reposent sur <xref:System.IO.MemoryStream> pour stocker le contenu du fichier chargé.</span><span class="sxs-lookup"><span data-stu-id="170a5-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="170a5-349">La limite de taille d’un `MemoryStream` est `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="170a5-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="170a5-350">Si le scénario de chargement de fichiers de l’application nécessite la conservation d’un contenu de fichier d’une taille supérieure à 50 Mo, utilisez une autre approche qui ne repose pas sur un seul `MemoryStream` pour conserver le contenu d’un fichier chargé.</span><span class="sxs-lookup"><span data-stu-id="170a5-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="170a5-351">ASP.NET Core prend en charge le chargement d’un ou plusieurs fichiers à l’aide d’une liaison de modèle mise en mémoire tampon pour les fichiers plus petits et la diffusion en continu sans mise en mémoire tampon pour les fichiers plus volumineux</span><span class="sxs-lookup"><span data-stu-id="170a5-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="170a5-352">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="170a5-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="170a5-353">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="170a5-353">Security considerations</span></span>

<span data-ttu-id="170a5-354">Soyez prudent lorsque vous fournissez aux utilisateurs la possibilité de charger des fichiers sur un serveur.</span><span class="sxs-lookup"><span data-stu-id="170a5-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="170a5-355">Les attaquants peuvent tenter d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="170a5-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="170a5-356">Exécuter [des attaques par déni de service](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="170a5-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="170a5-357">Télécharger des virus ou des programmes malveillants.</span><span class="sxs-lookup"><span data-stu-id="170a5-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="170a5-358">Compromettre les réseaux et les serveurs d’une autre manière.</span><span class="sxs-lookup"><span data-stu-id="170a5-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="170a5-359">Les étapes de sécurité qui réduisent la probabilité d’une attaque réussie sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="170a5-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="170a5-360">Chargez les fichiers dans une zone de chargement de fichier dédiée, de préférence sur un lecteur non-système.</span><span class="sxs-lookup"><span data-stu-id="170a5-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="170a5-361">Un emplacement dédié permet d’imposer des restrictions de sécurité plus faciles sur les fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="170a5-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="170a5-362">Désactivez les autorisations d’exécution sur l’emplacement de chargement du fichier.&dagger;</span><span class="sxs-lookup"><span data-stu-id="170a5-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="170a5-363">Ne conservez **pas** les fichiers téléchargés dans la même arborescence de répertoires que l’application.&dagger;</span><span class="sxs-lookup"><span data-stu-id="170a5-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="170a5-364">Utilisez un nom de fichier sécurisé déterminé par l’application.</span><span class="sxs-lookup"><span data-stu-id="170a5-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="170a5-365">N’utilisez pas un nom de fichier fourni par l’utilisateur ou le nom de fichier non approuvé du fichier téléchargé. &dagger; Code HTML encode le nom de fichier non fiable lors de son affichage.</span><span class="sxs-lookup"><span data-stu-id="170a5-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="170a5-366">Par exemple, la journalisation du nom de fichier ou l’affichage dans l’interface utilisateur ( Razor génère automatiquement le code html).</span><span class="sxs-lookup"><span data-stu-id="170a5-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="170a5-367">Autorisez uniquement les extensions de fichier approuvées pour la spécification de conception de l’application.&dagger;</span><span class="sxs-lookup"><span data-stu-id="170a5-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="170a5-368">Vérifiez que les vérifications côté client sont effectuées sur le serveur. &dagger; Les contrôles côté client sont faciles à contourner.</span><span class="sxs-lookup"><span data-stu-id="170a5-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="170a5-369">Vérifiez la taille d’un fichier téléchargé.</span><span class="sxs-lookup"><span data-stu-id="170a5-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="170a5-370">Définissez une limite de taille maximale pour empêcher les chargements volumineux.&dagger;</span><span class="sxs-lookup"><span data-stu-id="170a5-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="170a5-371">Lorsque les fichiers ne doivent pas être remplacés par un fichier téléchargé portant le même nom, vérifiez le nom du fichier par rapport à la base de données ou au stockage physique avant de charger le fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="170a5-372">**Exécutez un programme de détection de virus et de logiciels malveillants sur le contenu chargé avant que le fichier ne soit stocké.**</span><span class="sxs-lookup"><span data-stu-id="170a5-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="170a5-373">&dagger;L’exemple d’application illustre une approche qui répond aux critères.</span><span class="sxs-lookup"><span data-stu-id="170a5-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="170a5-374">Le chargement d’un code malveillant sur un système est généralement la première étape de l’exécution de code capable de :</span><span class="sxs-lookup"><span data-stu-id="170a5-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="170a5-375">Maîtrisez complètement un système.</span><span class="sxs-lookup"><span data-stu-id="170a5-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="170a5-376">Surchargez un système avec le résultat du blocage du système.</span><span class="sxs-lookup"><span data-stu-id="170a5-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="170a5-377">Compromettre les données utilisateur ou système.</span><span class="sxs-lookup"><span data-stu-id="170a5-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="170a5-378">Appliquez Graffiti à une interface utilisateur publique.</span><span class="sxs-lookup"><span data-stu-id="170a5-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="170a5-379">Pour plus d’informations sur la réduction de la surface d’attaque quand vous acceptez des fichiers d’utilisateurs, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="170a5-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="170a5-380">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)</span><span class="sxs-lookup"><span data-stu-id="170a5-380">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="170a5-381">Sécurité Azure : Vérifier que les contrôles appropriés sont en place quand vous acceptez des fichiers d’utilisateurs</span><span class="sxs-lookup"><span data-stu-id="170a5-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="170a5-382">Pour plus d’informations sur l’implémentation de mesures de sécurité, y compris des exemples de l’exemple d’application, consultez la section [validation](#validation) .</span><span class="sxs-lookup"><span data-stu-id="170a5-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="170a5-383">Scénarios de stockage</span><span class="sxs-lookup"><span data-stu-id="170a5-383">Storage scenarios</span></span>

<span data-ttu-id="170a5-384">Les options de stockage courantes pour les fichiers sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="170a5-384">Common storage options for files include:</span></span>

* <span data-ttu-id="170a5-385">Base de données</span><span class="sxs-lookup"><span data-stu-id="170a5-385">Database</span></span>

  * <span data-ttu-id="170a5-386">Pour les petits chargements de fichiers, une base de données est souvent plus rapide que les options de stockage physique (système de fichiers ou partage réseau).</span><span class="sxs-lookup"><span data-stu-id="170a5-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="170a5-387">Une base de données est souvent plus pratique que les options de stockage physique, car la récupération d’un enregistrement de base de données pour les données utilisateur peut fournir simultanément le contenu du fichier (par exemple, une image de l’avatar).</span><span class="sxs-lookup"><span data-stu-id="170a5-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="170a5-388">Une base de données est potentiellement moins coûteuse que l’utilisation d’un service de stockage de données.</span><span class="sxs-lookup"><span data-stu-id="170a5-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="170a5-389">Stockage physique (système de fichiers ou partage réseau)</span><span class="sxs-lookup"><span data-stu-id="170a5-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="170a5-390">Pour les chargements de fichiers volumineux :</span><span class="sxs-lookup"><span data-stu-id="170a5-390">For large file uploads:</span></span>
    * <span data-ttu-id="170a5-391">Les limites de base de données peuvent limiter la taille du téléchargement.</span><span class="sxs-lookup"><span data-stu-id="170a5-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="170a5-392">Le stockage physique est souvent moins économique que le stockage dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="170a5-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="170a5-393">Le stockage physique est potentiellement moins onéreux que l’utilisation d’un service de stockage de données.</span><span class="sxs-lookup"><span data-stu-id="170a5-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="170a5-394">Le processus de l’application doit disposer d’autorisations en lecture et en écriture sur l’emplacement de stockage.</span><span class="sxs-lookup"><span data-stu-id="170a5-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="170a5-395">**N’accordez jamais l’autorisation EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="170a5-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="170a5-396">Service de stockage de données (par exemple, [stockage d’objets BLOB Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="170a5-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="170a5-397">Les services offrent généralement une évolutivité et une résilience améliorées par rapport aux solutions locales qui sont généralement sujettes à des points de défaillance uniques.</span><span class="sxs-lookup"><span data-stu-id="170a5-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="170a5-398">Les services sont potentiellement moins coûteux dans les scénarios d’infrastructure de stockage volumineux.</span><span class="sxs-lookup"><span data-stu-id="170a5-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="170a5-399">Pour plus d’informations, consultez [démarrage rapide : utiliser .net pour créer un objet BLOB dans le stockage d’objets](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="170a5-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="170a5-400">La rubrique montre <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , mais <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> peut être utilisée pour enregistrer un <xref:System.IO.FileStream> dans le stockage d’objets BLOB quand vous utilisez un <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="170a5-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="170a5-401">Scénarios de chargement de fichiers</span><span class="sxs-lookup"><span data-stu-id="170a5-401">File upload scenarios</span></span>

<span data-ttu-id="170a5-402">La mise en mémoire tampon et la diffusion en continu sont deux approches générales pour le téléchargement de fichiers.</span><span class="sxs-lookup"><span data-stu-id="170a5-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="170a5-403">**des réponses**</span><span class="sxs-lookup"><span data-stu-id="170a5-403">**Buffering**</span></span>

<span data-ttu-id="170a5-404">La totalité du fichier est lue dans un <xref:Microsoft.AspNetCore.Http.IFormFile> , qui est une représentation C# du fichier utilisé pour traiter ou enregistrer le fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="170a5-405">Les ressources (disque, mémoire) utilisées par les chargements de fichiers dépendent du nombre et de la taille des chargements de fichiers simultanés.</span><span class="sxs-lookup"><span data-stu-id="170a5-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="170a5-406">Si une application tente de mettre en mémoire tampon un trop grand nombre de chargements, le site se bloque lorsqu’il manque de mémoire ou d’espace disque.</span><span class="sxs-lookup"><span data-stu-id="170a5-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="170a5-407">Si la taille ou la fréquence des chargements de fichiers épuisent les ressources d’application, utilisez la diffusion en continu.</span><span class="sxs-lookup"><span data-stu-id="170a5-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="170a5-408">Tout fichier mis en mémoire tampon dépassant 64 Ko est déplacé de la mémoire vers un fichier temporaire sur le disque.</span><span class="sxs-lookup"><span data-stu-id="170a5-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="170a5-409">La mise en mémoire tampon de petits fichiers est traitée dans les sections suivantes de cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="170a5-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="170a5-410">Stockage physique</span><span class="sxs-lookup"><span data-stu-id="170a5-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="170a5-411">Sauvegarde de la base de données</span><span class="sxs-lookup"><span data-stu-id="170a5-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="170a5-412">**Diffusion en continu**</span><span class="sxs-lookup"><span data-stu-id="170a5-412">**Streaming**</span></span>

<span data-ttu-id="170a5-413">Le fichier est reçu à partir d’une demande en plusieurs parties et est directement traité ou enregistré par l’application.</span><span class="sxs-lookup"><span data-stu-id="170a5-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="170a5-414">La diffusion en continu n’améliore pas les performances de manière significative.</span><span class="sxs-lookup"><span data-stu-id="170a5-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="170a5-415">La diffusion en continu réduit les demandes de mémoire ou d’espace disque lors du chargement de fichiers.</span><span class="sxs-lookup"><span data-stu-id="170a5-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="170a5-416">La diffusion en continu de fichiers volumineux est traitée dans la section [chargement de fichiers volumineux avec diffusion](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="170a5-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="170a5-417">Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers un stockage physique</span><span class="sxs-lookup"><span data-stu-id="170a5-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="170a5-418">Pour télécharger des fichiers de petite taille, utilisez un formulaire en plusieurs parties ou créez une requête de publication à l’aide de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="170a5-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="170a5-419">L’exemple suivant illustre l’utilisation d’un Razor formulaire de pages pour télécharger un seul fichier (*pages/BufferedSingleFileUploadPhysical. cshtml* dans l’exemple d’application) :</span><span class="sxs-lookup"><span data-stu-id="170a5-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="170a5-420">L’exemple suivant est similaire à l’exemple précédent, à l’exception de :</span><span class="sxs-lookup"><span data-stu-id="170a5-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="170a5-421">JavaScript ([API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API)) est utilisé pour envoyer les données du formulaire.</span><span class="sxs-lookup"><span data-stu-id="170a5-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="170a5-422">Il n’y a aucune validation.</span><span class="sxs-lookup"><span data-stu-id="170a5-422">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="170a5-423">Pour exécuter la publication de formulaire dans JavaScript pour les clients qui [ne prennent pas en charge l’API FETCH](https://caniuse.com/#feat=fetch), utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="170a5-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="170a5-424">Utilisez un Polyfill d’extraction (par exemple, [Window. Fetch Polyfill (GitHub/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="170a5-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="170a5-425">Utilisez `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="170a5-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="170a5-426">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="170a5-426">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="170a5-427">Afin de prendre en charge les chargements de fichiers, les formulaires HTML doivent spécifier un type d’encodage ( `enctype` ) de `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="170a5-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="170a5-428">Pour `files` qu’un élément INPUT prenne en charge le téléchargement de plusieurs fichiers, fournissez l' `multiple` attribut sur l' `<input>` élément :</span><span class="sxs-lookup"><span data-stu-id="170a5-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="170a5-429">Les fichiers individuels téléchargés sur le serveur sont accessibles via la [liaison de modèle](xref:mvc/models/model-binding) à l’aide de <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="170a5-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="170a5-430">L’exemple d’application illustre plusieurs chargements de fichiers mis en mémoire tampon pour les scénarios de base de données et de stockage physique.</span><span class="sxs-lookup"><span data-stu-id="170a5-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="170a5-431">N’utilisez **pas** la `FileName` propriété <xref:Microsoft.AspNetCore.Http.IFormFile> autre que pour l’affichage et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="170a5-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="170a5-432">Lors de l’affichage ou de la journalisation, le nom du fichier est encodé au format HTML.</span><span class="sxs-lookup"><span data-stu-id="170a5-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="170a5-433">Une personne malveillante peut fournir un nom de fichier malveillant, y compris les chemins d’accès complets ou les chemins d’accès relatifs.</span><span class="sxs-lookup"><span data-stu-id="170a5-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="170a5-434">Les applications doivent :</span><span class="sxs-lookup"><span data-stu-id="170a5-434">Applications should:</span></span>
>
> * <span data-ttu-id="170a5-435">Supprimez le chemin d’accès du nom de fichier fourni par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="170a5-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="170a5-436">Enregistrez le nom de fichier encodé au format HTML, avec chemin d’accès supprimé pour l’interface utilisateur ou la journalisation.</span><span class="sxs-lookup"><span data-stu-id="170a5-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="170a5-437">Générez un nouveau nom de fichier aléatoire pour le stockage.</span><span class="sxs-lookup"><span data-stu-id="170a5-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="170a5-438">Le code suivant supprime le chemin d’accès du nom de fichier :</span><span class="sxs-lookup"><span data-stu-id="170a5-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="170a5-439">Les exemples fournis jusqu’à présent ne prennent pas en compte les considérations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="170a5-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="170a5-440">Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="170a5-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="170a5-441">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="170a5-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="170a5-442">Validation</span><span class="sxs-lookup"><span data-stu-id="170a5-442">Validation</span></span>](#validation)

<span data-ttu-id="170a5-443">Lors du chargement de fichiers à l’aide d’une liaison de modèle et <xref:Microsoft.AspNetCore.Http.IFormFile> , la méthode d’action peut accepter les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="170a5-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="170a5-444">Une seule <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="170a5-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="170a5-445">L’un des regroupements suivants qui représentent plusieurs fichiers :</span><span class="sxs-lookup"><span data-stu-id="170a5-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="170a5-446">[Tarifs](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="170a5-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="170a5-447">La liaison correspond aux fichiers de formulaire par nom.</span><span class="sxs-lookup"><span data-stu-id="170a5-447">Binding matches form files by name.</span></span> <span data-ttu-id="170a5-448">Par exemple, la `name` valeur html dans `<input type="file" name="formFile">` doit correspondre au paramètre C#/lié à la propriété ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="170a5-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="170a5-449">Pour plus d’informations, consultez la section valeur de l' [attribut de nom de correspondance pour le nom de paramètre de la méthode de publication](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="170a5-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="170a5-450">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="170a5-450">The following example:</span></span>

* <span data-ttu-id="170a5-451">Effectue une boucle sur un ou plusieurs fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="170a5-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="170a5-452">Utilise [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour retourner le chemin d’accès complet d’un fichier, y compris le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="170a5-453">Enregistre les fichiers dans le système de fichiers local à l’aide d’un nom de fichier généré par l’application.</span><span class="sxs-lookup"><span data-stu-id="170a5-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="170a5-454">Retourne le nombre total et la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="170a5-454">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="170a5-455">Utilisez `Path.GetRandomFileName` pour générer un nom de fichier sans chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="170a5-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="170a5-456">Dans l’exemple suivant, le chemin d’accès est obtenu à partir de la configuration :</span><span class="sxs-lookup"><span data-stu-id="170a5-456">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="170a5-457">Le chemin d’accès passé à <xref:System.IO.FileStream> *doit* inclure le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="170a5-458">Si le nom de fichier n’est pas fourni, une <xref:System.UnauthorizedAccessException> exception est levée au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="170a5-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="170a5-459">Les fichiers chargés à l’aide de la <xref:Microsoft.AspNetCore.Http.IFormFile> technique sont mis en mémoire tampon ou sur disque sur le serveur avant le traitement.</span><span class="sxs-lookup"><span data-stu-id="170a5-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="170a5-460">À l’intérieur de la méthode d’action, le <xref:Microsoft.AspNetCore.Http.IFormFile> contenu est accessible en tant que <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="170a5-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="170a5-461">Outre le système de fichiers local, les fichiers peuvent être enregistrés sur un partage réseau ou un service de stockage de fichiers, tel que le [stockage d’objets BLOB Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="170a5-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="170a5-462">Pour obtenir un autre exemple qui effectue une boucle sur plusieurs fichiers pour le téléchargement et utilise des noms de fichiers sécurisés, consultez *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="170a5-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="170a5-463">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) lève une exception <xref:System.IO.IOException> si plus de 65 535 fichiers sont créés sans supprimer les fichiers temporaires précédents.</span><span class="sxs-lookup"><span data-stu-id="170a5-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="170a5-464">La limite de 65 535 fichiers est une limite par serveur.</span><span class="sxs-lookup"><span data-stu-id="170a5-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="170a5-465">Pour plus d’informations sur cette limite sur le système d’exploitation Windows, consultez les notes dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="170a5-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="170a5-466">GetTempFileNameA fonction)</span><span class="sxs-lookup"><span data-stu-id="170a5-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="170a5-467">Charger des fichiers de petite taille avec une liaison de modèle mise en mémoire tampon vers une base de données</span><span class="sxs-lookup"><span data-stu-id="170a5-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="170a5-468">Pour stocker des données de fichier binaires dans une base de données à l’aide de [Entity Framework](/ef/core/index), définissez une <xref:System.Byte> propriété de tableau sur l’entité :</span><span class="sxs-lookup"><span data-stu-id="170a5-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="170a5-469">Spécifiez une propriété de modèle de page pour la classe qui comprend <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="170a5-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="170a5-470"><xref:Microsoft.AspNetCore.Http.IFormFile>peut être utilisé directement comme paramètre de méthode d’action ou comme propriété de modèle liée.</span><span class="sxs-lookup"><span data-stu-id="170a5-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="170a5-471">L’exemple précédent utilise une propriété de modèle liée.</span><span class="sxs-lookup"><span data-stu-id="170a5-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="170a5-472">Le `FileUpload` est utilisé dans le Razor formulaire pages :</span><span class="sxs-lookup"><span data-stu-id="170a5-472">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="170a5-473">Lorsque le formulaire est publié sur le serveur, copiez <xref:Microsoft.AspNetCore.Http.IFormFile> -le dans un flux et enregistrez-le en tant que tableau d’octets dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="170a5-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="170a5-474">Dans l’exemple suivant, `_dbContext` stocke le contexte de base de données de l’application :</span><span class="sxs-lookup"><span data-stu-id="170a5-474">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="170a5-475">L’exemple précédent est semblable à un scénario illustré dans l’exemple d’application :</span><span class="sxs-lookup"><span data-stu-id="170a5-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="170a5-476">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="170a5-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="170a5-477">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="170a5-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="170a5-478">Soyez prudent quand vous stockez des données binaires dans des bases de données relationnelles, car cela peut avoir un impact négatif sur les performances.</span><span class="sxs-lookup"><span data-stu-id="170a5-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="170a5-479">Ne vous fiez pas à la `FileName` propriété de sans validation ni à approuver celle-ci <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="170a5-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="170a5-480">La `FileName` propriété doit uniquement être utilisée à des fins d’affichage et uniquement après l’encodage HTML.</span><span class="sxs-lookup"><span data-stu-id="170a5-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="170a5-481">Les exemples fournis ne prennent pas en compte les considérations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="170a5-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="170a5-482">Des informations supplémentaires sont fournies par les sections suivantes et l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="170a5-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="170a5-483">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="170a5-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="170a5-484">Validation</span><span class="sxs-lookup"><span data-stu-id="170a5-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="170a5-485">Charger des fichiers volumineux avec streaming</span><span class="sxs-lookup"><span data-stu-id="170a5-485">Upload large files with streaming</span></span>

<span data-ttu-id="170a5-486">L’exemple suivant montre comment utiliser JavaScript pour diffuser un fichier vers une action de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="170a5-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="170a5-487">Le jeton anti-contrefaçon du fichier est généré à l’aide d’un attribut de filtre personnalisé et transmis aux en-têtes HTTP du client plutôt que dans le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="170a5-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="170a5-488">Étant donné que la méthode d’action traite directement les données chargées, la liaison de modèle de formulaire est désactivée par un autre filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="170a5-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="170a5-489">Dans l’action, le contenu du formulaire est lu en avec `MultipartReader`, qui lit chaque `MultipartSection` individuelle, traitant le fichier ou enregistrant le contenu selon ce qui est approprié.</span><span class="sxs-lookup"><span data-stu-id="170a5-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="170a5-490">Une fois les sections en plusieurs parties lues, l’action effectue sa propre liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="170a5-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="170a5-491">La réponse de page initiale charge le formulaire et enregistre un jeton anti-contrefaçon dans un cookie (via l' `GenerateAntiforgeryTokenCookieAttribute` attribut).</span><span class="sxs-lookup"><span data-stu-id="170a5-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="170a5-492">L’attribut utilise la [prise en charge de l’anticontrefaçon](xref:security/anti-request-forgery) intégrée de ASP.net Core pour définir un cookie avec un jeton de demande :</span><span class="sxs-lookup"><span data-stu-id="170a5-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="170a5-493">`DisableFormValueModelBindingAttribute`Est utilisé pour désactiver la liaison de modèle :</span><span class="sxs-lookup"><span data-stu-id="170a5-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="170a5-494">Dans l’exemple d’application, `GenerateAntiforgeryTokenCookieAttribute` et `DisableFormValueModelBindingAttribute` sont appliqués en tant que filtres aux modèles d’application de page de et à l' `/StreamedSingleFileUploadDb` aide des conventions de `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor pages](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="170a5-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="170a5-495">Étant donné que la liaison de modèle ne lit pas le formulaire, les paramètres qui sont liés depuis le formulaire ne sont pas liés (la requête, l’itinéraire et l’en-tête continuent de fonctionner).</span><span class="sxs-lookup"><span data-stu-id="170a5-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="170a5-496">La méthode d’action fonctionne directement avec la `Request` propriété.</span><span class="sxs-lookup"><span data-stu-id="170a5-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="170a5-497">Un `MultipartReader` est utilisé pour lire chaque section.</span><span class="sxs-lookup"><span data-stu-id="170a5-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="170a5-498">Les données de clé/valeur sont stockées dans un `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="170a5-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="170a5-499">Une fois les sections en plusieurs parties lues, le contenu du `KeyValueAccumulator` est utilisé pour lier les données de formulaire à un type de modèle.</span><span class="sxs-lookup"><span data-stu-id="170a5-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="170a5-500">La `StreamingController.UploadDatabase` méthode complète pour la diffusion en continu vers une base de données avec EF Core :</span><span class="sxs-lookup"><span data-stu-id="170a5-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="170a5-501">`MultipartRequestHelper`(*Utilities/MultipartRequestHelper. cs*) :</span><span class="sxs-lookup"><span data-stu-id="170a5-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="170a5-502">Méthode complète `StreamingController.UploadPhysical` pour la diffusion en continu vers un emplacement physique :</span><span class="sxs-lookup"><span data-stu-id="170a5-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="170a5-503">Dans l’exemple d’application, les contrôles de validation sont gérés par `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="170a5-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="170a5-504">Validation</span><span class="sxs-lookup"><span data-stu-id="170a5-504">Validation</span></span>

<span data-ttu-id="170a5-505">La classe de l’exemple d’application `FileHelpers` illustre plusieurs vérifications des chargements de fichiers mis en mémoire tampon <xref:Microsoft.AspNetCore.Http.IFormFile> et diffusés en continu.</span><span class="sxs-lookup"><span data-stu-id="170a5-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="170a5-506">Pour traiter les <xref:Microsoft.AspNetCore.Http.IFormFile> chargements de fichiers mis en mémoire tampon dans l’exemple d’application, consultez la `ProcessFormFile` méthode dans le fichier *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="170a5-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="170a5-507">Pour le traitement de fichiers en continu, consultez la `ProcessStreamedFile` méthode dans le même fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="170a5-508">Les méthodes de traitement de validation présentées dans l’exemple d’application n’analysent pas le contenu des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="170a5-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="170a5-509">Dans la plupart des scénarios de production, une API de détection de virus et de logiciels malveillants est utilisée sur le fichier avant que le fichier soit mis à la disposition des utilisateurs ou d’autres systèmes.</span><span class="sxs-lookup"><span data-stu-id="170a5-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="170a5-510">Bien que l’exemple de rubrique fournit un exemple fonctionnel de techniques de validation, n’implémentez pas la `FileHelpers` classe dans une application de production, sauf si vous :</span><span class="sxs-lookup"><span data-stu-id="170a5-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="170a5-511">Comprenez pleinement l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="170a5-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="170a5-512">Modifiez l’implémentation en fonction de l’environnement et des spécifications de l’application.</span><span class="sxs-lookup"><span data-stu-id="170a5-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="170a5-513">**N’implémentez jamais non plus de code de sécurité dans une application sans répondre à ces exigences.**</span><span class="sxs-lookup"><span data-stu-id="170a5-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="170a5-514">Validation du contenu</span><span class="sxs-lookup"><span data-stu-id="170a5-514">Content validation</span></span>

<span data-ttu-id="170a5-515">**Utilisez une API d’analyse de virus/programme malveillant sur le contenu chargé.**</span><span class="sxs-lookup"><span data-stu-id="170a5-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="170a5-516">L’analyse des fichiers exige des ressources serveur dans des scénarios de volume élevé.</span><span class="sxs-lookup"><span data-stu-id="170a5-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="170a5-517">Si les performances de traitement des demandes sont réduites en raison de l’analyse de fichiers, envisagez de décharger le travail d’analyse vers un [service en arrière-plan](xref:fundamentals/host/hosted-services), éventuellement un service qui s’exécute sur un serveur différent du serveur de l’application.</span><span class="sxs-lookup"><span data-stu-id="170a5-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="170a5-518">En règle générale, les fichiers téléchargés sont conservés dans une zone en quarantaine jusqu’à ce que l’antivirus en arrière-plan les vérifie.</span><span class="sxs-lookup"><span data-stu-id="170a5-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="170a5-519">Lorsqu’un fichier réussit, le fichier est déplacé vers l’emplacement de stockage de fichiers normal.</span><span class="sxs-lookup"><span data-stu-id="170a5-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="170a5-520">Ces étapes sont généralement effectuées conjointement avec un enregistrement de base de données qui indique l’état d’analyse d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="170a5-521">À l’aide de cette approche, l’application et le serveur d’applications restent concentrés sur la réponse aux requêtes.</span><span class="sxs-lookup"><span data-stu-id="170a5-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="170a5-522">Validation de l’extension de fichier</span><span class="sxs-lookup"><span data-stu-id="170a5-522">File extension validation</span></span>

<span data-ttu-id="170a5-523">L’extension du fichier chargé doit être vérifiée par rapport à une liste d’extensions autorisées.</span><span class="sxs-lookup"><span data-stu-id="170a5-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="170a5-524">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="170a5-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="170a5-525">Validation de la signature de fichier</span><span class="sxs-lookup"><span data-stu-id="170a5-525">File signature validation</span></span>

<span data-ttu-id="170a5-526">La signature d’un fichier est déterminée par les premiers octets au début d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="170a5-527">Ces octets peuvent être utilisés pour indiquer si l’extension correspond au contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="170a5-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="170a5-528">L’exemple d’application vérifie les signatures de fichiers pour quelques types de fichiers courants.</span><span class="sxs-lookup"><span data-stu-id="170a5-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="170a5-529">Dans l’exemple suivant, la signature de fichier pour une image JPEG est vérifiée par rapport au fichier :</span><span class="sxs-lookup"><span data-stu-id="170a5-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="170a5-530">Pour obtenir des signatures de fichiers supplémentaires, consultez la [base de données signatures de fichiers](https://www.filesignatures.net/) et les spécifications de fichier officielles.</span><span class="sxs-lookup"><span data-stu-id="170a5-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="170a5-531">Sécurité des noms de fichiers</span><span class="sxs-lookup"><span data-stu-id="170a5-531">File name security</span></span>

<span data-ttu-id="170a5-532">N’utilisez jamais un nom de fichier fourni par le client pour enregistrer un fichier dans le stockage physique.</span><span class="sxs-lookup"><span data-stu-id="170a5-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="170a5-533">Créez un nom de fichier sécurisé pour le fichier à l’aide de [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour créer un chemin d’accès complet (y compris le nom de fichier) pour le stockage temporaire.</span><span class="sxs-lookup"><span data-stu-id="170a5-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="170a5-534">encode automatiquement les valeurs des propriétés pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="170a5-534"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="170a5-535">Le code suivant peut être utilisé en toute sécurité :</span><span class="sxs-lookup"><span data-stu-id="170a5-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="170a5-536">En dehors de Razor , toujours <xref:System.Net.WebUtility.HtmlEncode*> le contenu du nom de fichier à partir de la demande d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="170a5-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="170a5-537">De nombreuses implémentations doivent inclure une vérification de l’existence du fichier ; dans le cas contraire, le fichier est remplacé par un fichier du même nom.</span><span class="sxs-lookup"><span data-stu-id="170a5-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="170a5-538">Fournissez une logique supplémentaire pour répondre aux spécifications de votre application.</span><span class="sxs-lookup"><span data-stu-id="170a5-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="170a5-539">Validation de la taille</span><span class="sxs-lookup"><span data-stu-id="170a5-539">Size validation</span></span>

<span data-ttu-id="170a5-540">Limitez la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="170a5-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="170a5-541">Dans l’exemple d’application, la taille du fichier est limitée à 2 Mo (indiquée en octets).</span><span class="sxs-lookup"><span data-stu-id="170a5-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="170a5-542">La limite est fournie via la [configuration](xref:fundamentals/configuration/index) à partir du fichier *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="170a5-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="170a5-543">`FileSizeLimit`Est injecté dans des `PageModel` classes :</span><span class="sxs-lookup"><span data-stu-id="170a5-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="170a5-544">Quand une taille de fichier dépasse la limite, le fichier est rejeté :</span><span class="sxs-lookup"><span data-stu-id="170a5-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="170a5-545">Valeur de l’attribut de nom de correspondance avec le nom de paramètre de la méthode de publication</span><span class="sxs-lookup"><span data-stu-id="170a5-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="170a5-546">Dans les non- Razor formulaires qui publient des données de formulaire ou utilisent `FormData` directement JavaScript, le nom spécifié dans l’élément du formulaire ou `FormData` doit correspondre au nom du paramètre dans l’action du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="170a5-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="170a5-547">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="170a5-547">In the following example:</span></span>

* <span data-ttu-id="170a5-548">Lorsque vous utilisez un `<input>` élément, l' `name` attribut est défini sur la valeur `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="170a5-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="170a5-549">Lors de l’utilisation `FormData` de dans JavaScript, le nom est défini sur la valeur `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="170a5-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="170a5-550">Utilisez un nom correspondant pour le paramètre de la méthode C# ( `battlePlans` ) :</span><span class="sxs-lookup"><span data-stu-id="170a5-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="170a5-551">Pour une Razor méthode de gestionnaire de page pages nommée `Upload` :</span><span class="sxs-lookup"><span data-stu-id="170a5-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="170a5-552">Pour une méthode d’action du billet de contrôleur MVC :</span><span class="sxs-lookup"><span data-stu-id="170a5-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="170a5-553">Configuration du serveur et de l’application</span><span class="sxs-lookup"><span data-stu-id="170a5-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="170a5-554">Longueur limite du corps en plusieurs parties</span><span class="sxs-lookup"><span data-stu-id="170a5-554">Multipart body length limit</span></span>

<span data-ttu-id="170a5-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>définit la limite de la longueur de chaque corps en plusieurs parties.</span><span class="sxs-lookup"><span data-stu-id="170a5-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="170a5-556">Les sections de formulaire qui dépassent cette limite lèvent une lorsqu’elles sont <xref:System.IO.InvalidDataException> analysées.</span><span class="sxs-lookup"><span data-stu-id="170a5-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="170a5-557">La valeur par défaut est 134 217 728 (128 Mo).</span><span class="sxs-lookup"><span data-stu-id="170a5-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="170a5-558">Personnaliser la limite à l’aide du <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> paramètre dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="170a5-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="170a5-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>est utilisé pour définir le <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pour une seule page ou action.</span><span class="sxs-lookup"><span data-stu-id="170a5-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="170a5-560">Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="170a5-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="170a5-561">Dans une Razor application de pages ou une application MVC, appliquez le filtre au modèle de page ou à la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="170a5-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="170a5-562">Taille maximale du corps de la demande Kestrel</span><span class="sxs-lookup"><span data-stu-id="170a5-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="170a5-563">Pour les applications hébergées par Kestrel, la taille de corps de requête maximale par défaut est de 30 millions octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="170a5-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="170a5-564">Personnaliser la limite à l’aide de l’option de serveur [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel :</span><span class="sxs-lookup"><span data-stu-id="170a5-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<span data-ttu-id="170a5-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>est utilisé pour définir le [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pour une seule page ou action.</span><span class="sxs-lookup"><span data-stu-id="170a5-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="170a5-566">Dans une Razor application pages, appliquez le filtre avec une [Convention](xref:razor-pages/razor-pages-conventions) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="170a5-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="170a5-567">Dans une Razor application de pages ou une application MVC, appliquez le filtre à la classe du gestionnaire de pages ou à la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="170a5-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="170a5-568">Autres limites Kestrel</span><span class="sxs-lookup"><span data-stu-id="170a5-568">Other Kestrel limits</span></span>

<span data-ttu-id="170a5-569">D’autres limites Kestrel peuvent s’appliquer aux applications hébergées par Kestrel :</span><span class="sxs-lookup"><span data-stu-id="170a5-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="170a5-570">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="170a5-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="170a5-571">Taux de données de demande et de réponse</span><span class="sxs-lookup"><span data-stu-id="170a5-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="170a5-572">Limite de longueur du contenu IIS</span><span class="sxs-lookup"><span data-stu-id="170a5-572">IIS content length limit</span></span>

<span data-ttu-id="170a5-573">La limite de demandes par défaut ( `maxAllowedContentLength` ) est de 30 millions octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="170a5-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="170a5-574">Personnaliser la limite dans le fichier *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="170a5-574">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="170a5-575">Ce paramètre s’applique seulement à IIS.</span><span class="sxs-lookup"><span data-stu-id="170a5-575">This setting only applies to IIS.</span></span> <span data-ttu-id="170a5-576">Par défaut, ce comportement ne se produit pas dans le cas d’un hébergement sur Kestrel.</span><span class="sxs-lookup"><span data-stu-id="170a5-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="170a5-577">Pour plus d’informations, consultez [limites \<requestLimits> des demandes ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="170a5-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="170a5-578">Les limitations du module ASP.NET Core ou la présence du module de filtrage des demandes IIS peuvent limiter les chargements à 2 ou 4 Go.</span><span class="sxs-lookup"><span data-stu-id="170a5-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="170a5-579">Pour plus d’informations, consultez [Impossible de télécharger un fichier d’une taille supérieure à 2 Go (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="170a5-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="170a5-580">Dépanner</span><span class="sxs-lookup"><span data-stu-id="170a5-580">Troubleshoot</span></span>

<span data-ttu-id="170a5-581">Voici certains problèmes courants rencontrés avec le chargement de fichiers et leurs solutions possibles.</span><span class="sxs-lookup"><span data-stu-id="170a5-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="170a5-582">Erreur introuvable lors du déploiement sur un serveur IIS</span><span class="sxs-lookup"><span data-stu-id="170a5-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="170a5-583">L’erreur suivante indique que le fichier chargé dépasse la longueur du contenu configurée du serveur :</span><span class="sxs-lookup"><span data-stu-id="170a5-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="170a5-584">Pour plus d’informations sur l’amélioration de la limite, consultez la section [limite de longueur du contenu IIS](#iis-content-length-limit) .</span><span class="sxs-lookup"><span data-stu-id="170a5-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="170a5-585">Échec de connexion</span><span class="sxs-lookup"><span data-stu-id="170a5-585">Connection failure</span></span>

<span data-ttu-id="170a5-586">Une erreur de connexion et une connexion du serveur de réinitialisation indiquent probablement que le fichier téléchargé dépasse la taille maximale du corps de la demande de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="170a5-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="170a5-587">Pour plus d’informations, consultez la section [taille maximale du corps de la demande Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="170a5-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="170a5-588">Les limites de connexion du client Kestrel peuvent également nécessiter des ajustements.</span><span class="sxs-lookup"><span data-stu-id="170a5-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="170a5-589">Exception de référence null avec IFormFile</span><span class="sxs-lookup"><span data-stu-id="170a5-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="170a5-590">Si le contrôleur accepte les fichiers téléchargés à l’aide <xref:Microsoft.AspNetCore.Http.IFormFile> de mais que la valeur est `null` , vérifiez que le formulaire HTML spécifie la `enctype` valeur `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="170a5-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="170a5-591">Si cet attribut n’est pas défini sur l' `<form>` élément, le chargement du fichier ne se produit pas et tous les <xref:Microsoft.AspNetCore.Http.IFormFile> arguments liés sont `null` .</span><span class="sxs-lookup"><span data-stu-id="170a5-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="170a5-592">Vérifiez également que l' [appellation de chargement dans les données de formulaire correspond à celle de l’application](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="170a5-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="170a5-593">Le flux est trop long</span><span class="sxs-lookup"><span data-stu-id="170a5-593">Stream was too long</span></span>

<span data-ttu-id="170a5-594">Les exemples de cette rubrique reposent sur <xref:System.IO.MemoryStream> pour stocker le contenu du fichier chargé.</span><span class="sxs-lookup"><span data-stu-id="170a5-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="170a5-595">La limite de taille d’un `MemoryStream` est `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="170a5-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="170a5-596">Si le scénario de chargement de fichiers de l’application nécessite la conservation d’un contenu de fichier d’une taille supérieure à 50 Mo, utilisez une autre approche qui ne repose pas sur un seul `MemoryStream` pour conserver le contenu d’un fichier chargé.</span><span class="sxs-lookup"><span data-stu-id="170a5-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="170a5-597">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="170a5-597">Additional resources</span></span>

* [<span data-ttu-id="170a5-598">Vidange des demandes de connexion HTTP</span><span class="sxs-lookup"><span data-stu-id="170a5-598">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* <span data-ttu-id="170a5-599">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)</span><span class="sxs-lookup"><span data-stu-id="170a5-599">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
* [<span data-ttu-id="170a5-600">Sécurité Azure : frame de sécurité : validation des entrées | Atténuations</span><span class="sxs-lookup"><span data-stu-id="170a5-600">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="170a5-601">Modèles de conception de Cloud Azure : modèle de clé valet</span><span class="sxs-lookup"><span data-stu-id="170a5-601">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
