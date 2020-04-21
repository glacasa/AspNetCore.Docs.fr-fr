---
title: Télécharger des fichiers dans ASP.NET Core
author: rick-anderson
description: Comment utiliser la liaison de modèle et le streaming pour charger des fichiers dans ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2020
uid: mvc/models/file-uploads
ms.openlocfilehash: e25da0b3867181a16a4636768f36c148a152dd23
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661738"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="d8134-103">Télécharger des fichiers dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8134-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="d8134-104">Par [Steve Smith](https://ardalis.com/) et [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="d8134-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d8134-105">ASP.NET Core prend en charge le téléchargement d’un ou de plusieurs fichiers à l’aide de la liaison de modèle tampon pour les fichiers plus petits et du streaming inbuffered pour les fichiers plus grands.</span><span class="sxs-lookup"><span data-stu-id="d8134-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="d8134-106">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d8134-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="d8134-107">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="d8134-107">Security considerations</span></span>

<span data-ttu-id="d8134-108">Faites preuve de prudence lorsque vous offrez aux utilisateurs la possibilité de télécharger des fichiers sur un serveur.</span><span class="sxs-lookup"><span data-stu-id="d8134-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="d8134-109">Les attaquants peuvent tenter de :</span><span class="sxs-lookup"><span data-stu-id="d8134-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="d8134-110">Exécutez des attaques [par déni de service.](/windows-hardware/drivers/ifs/denial-of-service)</span><span class="sxs-lookup"><span data-stu-id="d8134-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="d8134-111">Téléchargez des virus ou des logiciels malveillants.</span><span class="sxs-lookup"><span data-stu-id="d8134-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="d8134-112">Compromettez les réseaux et les serveurs par d’autres moyens.</span><span class="sxs-lookup"><span data-stu-id="d8134-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="d8134-113">Les mesures de sécurité qui réduisent la probabilité d’une attaque réussie sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="d8134-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="d8134-114">Téléchargez des fichiers dans une zone de téléchargement de fichiers dédiée, de préférence sur un lecteur non système.</span><span class="sxs-lookup"><span data-stu-id="d8134-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="d8134-115">Un emplacement dédié facilite l’imposition de restrictions de sécurité sur les fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="d8134-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="d8134-116">Désactiver les autorisations d’exécution sur l’emplacement de téléchargement de fichier.&dagger;</span><span class="sxs-lookup"><span data-stu-id="d8134-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="d8134-117">Ne persistez **pas** les fichiers téléchargés dans le même arbre d’annuaire que l’application.&dagger;</span><span class="sxs-lookup"><span data-stu-id="d8134-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="d8134-118">Utilisez un nom de fichier sécurisé déterminé par l’application.</span><span class="sxs-lookup"><span data-stu-id="d8134-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="d8134-119">N’utilisez pas de nom de fichier fourni par l’utilisateur ou le nom de fichier non fiable du fichier téléchargé. &dagger; HTML code le nom de fichier non fiable lors de son affichage.</span><span class="sxs-lookup"><span data-stu-id="d8134-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="d8134-120">Par exemple, l’enregistrement du nom du fichier ou l’affichage dans l’interface utilisateur (Razor code automatiquement LA sortie HTML).</span><span class="sxs-lookup"><span data-stu-id="d8134-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="d8134-121">N’autorisez que les extensions de fichiers approuvées pour les spécifications de conception de l’application.&dagger;</span><span class="sxs-lookup"><span data-stu-id="d8134-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="d8134-122">Vérifiez que des vérifications côté client sont effectuées sur le serveur. &dagger; Les contrôles côté client sont faciles à contourner.</span><span class="sxs-lookup"><span data-stu-id="d8134-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="d8134-123">Vérifiez la taille d’un fichier téléchargé.</span><span class="sxs-lookup"><span data-stu-id="d8134-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="d8134-124">Définissez une limite de taille maximale pour empêcher les gros téléchargements.&dagger;</span><span class="sxs-lookup"><span data-stu-id="d8134-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="d8134-125">Lorsque les fichiers ne doivent pas être écrasés par un fichier téléchargé du même nom, vérifiez le nom du fichier contre la base de données ou le stockage physique avant de télécharger le fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="d8134-126">**Exécutez un scanner virus/malware sur le contenu téléchargé avant que le fichier ne soit stocké.**</span><span class="sxs-lookup"><span data-stu-id="d8134-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="d8134-127">&dagger;L’application d’échantillon démontre une approche qui répond aux critères.</span><span class="sxs-lookup"><span data-stu-id="d8134-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="d8134-128">Le chargement d’un code malveillant sur un système est généralement la première étape de l’exécution de code capable de :</span><span class="sxs-lookup"><span data-stu-id="d8134-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="d8134-129">Prenez complètement le contrôle d’un système.</span><span class="sxs-lookup"><span data-stu-id="d8134-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="d8134-130">Surchargez un système avec le résultat que le système se bloque.</span><span class="sxs-lookup"><span data-stu-id="d8134-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="d8134-131">Compromettre les données utilisateur ou système.</span><span class="sxs-lookup"><span data-stu-id="d8134-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="d8134-132">Appliquer des graffitis à une interface utilisateur publique.</span><span class="sxs-lookup"><span data-stu-id="d8134-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="d8134-133">Pour plus d’informations sur la réduction de la surface d’attaque quand vous acceptez des fichiers d’utilisateurs, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="d8134-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="d8134-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)</span><span class="sxs-lookup"><span data-stu-id="d8134-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="d8134-135">Sécurité Azure : Vérifier que les contrôles appropriés sont en place quand vous acceptez des fichiers d’utilisateurs</span><span class="sxs-lookup"><span data-stu-id="d8134-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="d8134-136">Pour plus d’informations sur la mise en œuvre des mesures de sécurité, y compris des exemples de l’application d’échantillon, consultez la section [Validation.](#validation)</span><span class="sxs-lookup"><span data-stu-id="d8134-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="d8134-137">Scénarios de stockage</span><span class="sxs-lookup"><span data-stu-id="d8134-137">Storage scenarios</span></span>

<span data-ttu-id="d8134-138">Les options de stockage courantes pour les fichiers comprennent :</span><span class="sxs-lookup"><span data-stu-id="d8134-138">Common storage options for files include:</span></span>

* <span data-ttu-id="d8134-139">Base de données</span><span class="sxs-lookup"><span data-stu-id="d8134-139">Database</span></span>

  * <span data-ttu-id="d8134-140">Pour les petits téléchargements de fichiers, une base de données est souvent plus rapide que les options de stockage physique (système de fichiers ou partage de réseau).</span><span class="sxs-lookup"><span data-stu-id="d8134-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="d8134-141">Une base de données est souvent plus pratique que les options de stockage physique, car la récupération d’un enregistrement de base de données pour les données utilisateur peut fournir simultanément le contenu du fichier (par exemple, une image avatar).</span><span class="sxs-lookup"><span data-stu-id="d8134-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="d8134-142">Une base de données est potentiellement moins coûteuse que l’utilisation d’un service de stockage de données.</span><span class="sxs-lookup"><span data-stu-id="d8134-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="d8134-143">Stockage physique (système de fichiers ou partage réseau)</span><span class="sxs-lookup"><span data-stu-id="d8134-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="d8134-144">Pour les téléchargements de fichiers volumineux :</span><span class="sxs-lookup"><span data-stu-id="d8134-144">For large file uploads:</span></span>
    * <span data-ttu-id="d8134-145">Les limites de base de données peuvent limiter la taille du téléchargement.</span><span class="sxs-lookup"><span data-stu-id="d8134-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="d8134-146">Le stockage physique est souvent moins économique que le stockage dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="d8134-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="d8134-147">Le stockage physique est potentiellement moins coûteux que l’utilisation d’un service de stockage de données.</span><span class="sxs-lookup"><span data-stu-id="d8134-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="d8134-148">Le processus de l’application doit avoir lu et écrire des autorisations à l’emplacement de stockage.</span><span class="sxs-lookup"><span data-stu-id="d8134-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="d8134-149">**N’accordez jamais la permission d’exécuter.**</span><span class="sxs-lookup"><span data-stu-id="d8134-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="d8134-150">Service de stockage de données (par exemple, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="d8134-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="d8134-151">Les services offrent généralement une meilleure évolutivité et une facilité de résilience par endroits sur des solutions qui sont généralement sujettes à des points de défaillance uniques.</span><span class="sxs-lookup"><span data-stu-id="d8134-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="d8134-152">Les services sont potentiellement moins coûteux dans les grands scénarios d’infrastructure de stockage.</span><span class="sxs-lookup"><span data-stu-id="d8134-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="d8134-153">Pour plus d’informations, voir [Quickstart: Use .NET pour créer un blob dans le stockage d’objets](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="d8134-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="d8134-154">Scénarios de téléchargement de fichiers</span><span class="sxs-lookup"><span data-stu-id="d8134-154">File upload scenarios</span></span>

<span data-ttu-id="d8134-155">Deux approches générales pour le téléchargement de fichiers sont la mise en mémoire tampon et le streaming.</span><span class="sxs-lookup"><span data-stu-id="d8134-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="d8134-156">**des réponses**</span><span class="sxs-lookup"><span data-stu-id="d8134-156">**Buffering**</span></span>

<span data-ttu-id="d8134-157">L’ensemble du fichier <xref:Microsoft.AspNetCore.Http.IFormFile>est lu dans un , qui est une représentation C du fichier utilisé pour traiter ou enregistrer le fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="d8134-158">Les ressources (disque, mémoire) utilisées par les téléchargements de fichiers dépendent du nombre et de la taille des téléchargements de fichiers simultanés.</span><span class="sxs-lookup"><span data-stu-id="d8134-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="d8134-159">Si une application tente de tamponner trop de téléchargements, le site se bloque lorsqu’il est à court d’espace de mémoire ou de disque.</span><span class="sxs-lookup"><span data-stu-id="d8134-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="d8134-160">Si la taille ou la fréquence des téléchargements de fichiers est épuisante ressources d’applications, utilisez le streaming.</span><span class="sxs-lookup"><span data-stu-id="d8134-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="d8134-161">Tout fichier tampon unique dépassant 64 KB est déplacé de la mémoire à un fichier d’ampoule sur le disque.</span><span class="sxs-lookup"><span data-stu-id="d8134-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="d8134-162">La mise en mémoire des petits fichiers est couverte dans les sections suivantes de ce sujet :</span><span class="sxs-lookup"><span data-stu-id="d8134-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="d8134-163">Stockage physique</span><span class="sxs-lookup"><span data-stu-id="d8134-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="d8134-164">Sauvegarde de la base de données</span><span class="sxs-lookup"><span data-stu-id="d8134-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="d8134-165">**Diffusion en continu**</span><span class="sxs-lookup"><span data-stu-id="d8134-165">**Streaming**</span></span>

<span data-ttu-id="d8134-166">Le fichier est reçu à partir d’une demande multipartite et directement traité ou enregistré par l’application.</span><span class="sxs-lookup"><span data-stu-id="d8134-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="d8134-167">Le streaming n’améliore pas les performances de manière significative.</span><span class="sxs-lookup"><span data-stu-id="d8134-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="d8134-168">Le streaming réduit les demandes d’espace de mémoire ou de disque lors du téléchargement de fichiers.</span><span class="sxs-lookup"><span data-stu-id="d8134-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="d8134-169">Streaming fichiers volumineux est couvert dans les [fichiers Volumineux avec la](#upload-large-files-with-streaming) section streaming.</span><span class="sxs-lookup"><span data-stu-id="d8134-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="d8134-170">Téléchargez de petits fichiers avec un modèle tampon liant au stockage physique</span><span class="sxs-lookup"><span data-stu-id="d8134-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="d8134-171">Pour télécharger de petits fichiers, utilisez un formulaire multipartite ou construisez une demande POST à l’aide de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d8134-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="d8134-172">L’exemple suivant montre l’utilisation d’un formulaire Razor Pages pour télécharger un seul fichier *(Pages/BufferedSingleFileUploadPhysical.cshtml* dans l’application d’échantillon) :</span><span class="sxs-lookup"><span data-stu-id="d8134-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="d8134-173">L’exemple suivant est analogue à l’exemple précédent, sauf que :</span><span class="sxs-lookup"><span data-stu-id="d8134-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="d8134-174">JavaScript[(Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) est utilisé pour soumettre les données du formulaire.</span><span class="sxs-lookup"><span data-stu-id="d8134-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="d8134-175">Il n’y a pas de validation.</span><span class="sxs-lookup"><span data-stu-id="d8134-175">There's no validation.</span></span>

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

<span data-ttu-id="d8134-176">Pour effectuer le formulaire POST dans JavaScript pour les clients qui [ne prennent pas en charge l’API Fetch](https://caniuse.com/#feat=fetch), utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="d8134-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="d8134-177">Utilisez un Fetch Polyfill (par exemple, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="d8134-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="d8134-178">Utilisez `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="d8134-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="d8134-179">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d8134-179">For example:</span></span>

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

<span data-ttu-id="d8134-180">Afin de prendre en charge les téléchargements de`enctype`fichiers, `multipart/form-data`les formulaires HTML doivent spécifier un type d’encodage ( ) de .</span><span class="sxs-lookup"><span data-stu-id="d8134-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="d8134-181">Pour `files` un élément d’entrée à `multiple` l’appui `<input>` de télécharger plusieurs fichiers fournir l’attribut sur l’élément:</span><span class="sxs-lookup"><span data-stu-id="d8134-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="d8134-182">Les fichiers individuels téléchargés sur le serveur peuvent <xref:Microsoft.AspNetCore.Http.IFormFile>être consultés via La liaison de [modèle](xref:mvc/models/model-binding) utilisant .</span><span class="sxs-lookup"><span data-stu-id="d8134-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="d8134-183">L’application d’échantillon démontre plusieurs téléchargements de fichiers tamponnés pour les scénarios de base de données et de stockage physique.</span><span class="sxs-lookup"><span data-stu-id="d8134-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="d8134-184">**N’utilisez** `FileName` pas <xref:Microsoft.AspNetCore.Http.IFormFile> la propriété d’autres que pour l’affichage et l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="d8134-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="d8134-185">Lors de l’affichage ou de la connexion, HTML code le nom du fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="d8134-186">Un attaquant peut fournir un nom de fichier malveillant, y compris des chemins complets ou des chemins relatifs.</span><span class="sxs-lookup"><span data-stu-id="d8134-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="d8134-187">Les demandes doivent :</span><span class="sxs-lookup"><span data-stu-id="d8134-187">Applications should:</span></span>
>
> * <span data-ttu-id="d8134-188">Retirez le chemin du nom de fichier fourni par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d8134-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="d8134-189">Enregistrez le nom de fichier HTML-encoded, décrypté par voie pour l’interface utilisateur ou l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="d8134-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="d8134-190">Générer un nouveau nom de fichier aléatoire pour le stockage.</span><span class="sxs-lookup"><span data-stu-id="d8134-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="d8134-191">Le code suivant supprime le chemin du nom du fichier :</span><span class="sxs-lookup"><span data-stu-id="d8134-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="d8134-192">Les exemples fournis jusqu’à présent ne tiennent pas compte des considérations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="d8134-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="d8134-193">Des informations supplémentaires sont fournies par les sections suivantes et [l’application d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="d8134-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="d8134-194">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="d8134-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="d8134-195">Validation</span><span class="sxs-lookup"><span data-stu-id="d8134-195">Validation</span></span>](#validation)

<span data-ttu-id="d8134-196">Lors du téléchargement de <xref:Microsoft.AspNetCore.Http.IFormFile>fichiers à l’aide de la liaison du modèle et , la méthode d’action peut accepter:</span><span class="sxs-lookup"><span data-stu-id="d8134-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="d8134-197">Un <xref:Microsoft.AspNetCore.Http.IFormFile>single .</span><span class="sxs-lookup"><span data-stu-id="d8134-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="d8134-198">N’importe laquelle des collections suivantes qui représentent plusieurs fichiers :</span><span class="sxs-lookup"><span data-stu-id="d8134-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="d8134-199">[Liste](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="d8134-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="d8134-200">Correspondances contraignantes formulaire fichiers par nom.</span><span class="sxs-lookup"><span data-stu-id="d8134-200">Binding matches form files by name.</span></span> <span data-ttu-id="d8134-201">Par exemple, `name` la `<input type="file" name="formFile">` valeur HTML doit correspondre au`FormFile`paramètre/propriété CMD lié ().</span><span class="sxs-lookup"><span data-stu-id="d8134-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="d8134-202">Pour plus d’informations, voir la [valeur d’attribut de nom De match au nom de paramètre de la section méthode POST.](#match-name-attribute-value-to-parameter-name-of-post-method)</span><span class="sxs-lookup"><span data-stu-id="d8134-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="d8134-203">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="d8134-203">The following example:</span></span>

* <span data-ttu-id="d8134-204">Boucles à travers un ou plusieurs fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="d8134-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="d8134-205">Utilise [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour retourner un chemin complet pour un fichier, y compris le nom du fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="d8134-206">Enregistre les fichiers au système de fichiers local à l’aide d’un nom de fichier généré par l’application.</span><span class="sxs-lookup"><span data-stu-id="d8134-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="d8134-207">Retourne le nombre total et la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="d8134-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="d8134-208">Utilisez `Path.GetRandomFileName` pour générer un nom de fichier sans chemin.</span><span class="sxs-lookup"><span data-stu-id="d8134-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="d8134-209">Dans l’exemple suivant, le chemin est obtenu à partir de la configuration :</span><span class="sxs-lookup"><span data-stu-id="d8134-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="d8134-210">Le chemin passé <xref:System.IO.FileStream> au *doit* inclure le nom du fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="d8134-211">Si le nom du fichier <xref:System.UnauthorizedAccessException> n’est pas fourni, un est jeté à l’heure de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="d8134-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="d8134-212">Les fichiers téléchargés à l’aide de la <xref:Microsoft.AspNetCore.Http.IFormFile> technique sont tamponnés en mémoire ou sur disque sur le serveur avant le traitement.</span><span class="sxs-lookup"><span data-stu-id="d8134-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="d8134-213">À l’intérieur <xref:Microsoft.AspNetCore.Http.IFormFile> de la méthode <xref:System.IO.Stream>d’action, le contenu est accessible en tant que .</span><span class="sxs-lookup"><span data-stu-id="d8134-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="d8134-214">En plus du système de fichiers local, les fichiers peuvent être enregistrés sur une part de réseau ou sur un service de stockage de fichiers, comme [le stockage Azure Blob](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="d8134-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="d8134-215">Pour un autre exemple qui boucle sur plusieurs fichiers pour télécharger et utilise des noms de fichiers sûrs, voir *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* dans l’application de l’échantillon.</span><span class="sxs-lookup"><span data-stu-id="d8134-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="d8134-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) lance <xref:System.IO.IOException> un si plus de 65 535 fichiers sont créés sans supprimer les fichiers temporaires précédents.</span><span class="sxs-lookup"><span data-stu-id="d8134-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="d8134-217">La limite de 65 535 fichiers est une limite par serveur.</span><span class="sxs-lookup"><span data-stu-id="d8134-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="d8134-218">Pour plus d’informations sur cette limite sur Windows OS, voir les remarques dans les sujets suivants:</span><span class="sxs-lookup"><span data-stu-id="d8134-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="d8134-219">Fonction GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="d8134-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="d8134-220">Téléchargez de petits fichiers avec une liaison de modèle tampon dans une base de données</span><span class="sxs-lookup"><span data-stu-id="d8134-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="d8134-221">Pour stocker les données de fichiers binaires <xref:System.Byte> dans une base de données à l’aide [d’Entity Framework](/ef/core/index), définissez une propriété de tableau sur l’entité :</span><span class="sxs-lookup"><span data-stu-id="d8134-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="d8134-222">Spécifier un modèle de <xref:Microsoft.AspNetCore.Http.IFormFile>page pour la classe qui comprend un :</span><span class="sxs-lookup"><span data-stu-id="d8134-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="d8134-223"><xref:Microsoft.AspNetCore.Http.IFormFile>peut être utilisé directement comme paramètre de méthode d’action ou comme propriété modèle liée.</span><span class="sxs-lookup"><span data-stu-id="d8134-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="d8134-224">L’exemple précédent utilise une propriété modèle liée.</span><span class="sxs-lookup"><span data-stu-id="d8134-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="d8134-225">Le `FileUpload` est utilisé dans le formulaire Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="d8134-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="d8134-226">Lorsque le formulaire est POSTed sur <xref:Microsoft.AspNetCore.Http.IFormFile> le serveur, copiez-le sur un flux et enregistrez-le comme un tableau d’endlète dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="d8134-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="d8134-227">Dans l’exemple `_dbContext` suivant, stocke le contexte de la base de données de l’application :</span><span class="sxs-lookup"><span data-stu-id="d8134-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="d8134-228">L’exemple précédent est similaire à un scénario démontré dans l’application de l’échantillon :</span><span class="sxs-lookup"><span data-stu-id="d8134-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="d8134-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d8134-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="d8134-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d8134-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="d8134-231">Soyez prudent quand vous stockez des données binaires dans des bases de données relationnelles, car cela peut avoir un impact négatif sur les performances.</span><span class="sxs-lookup"><span data-stu-id="d8134-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="d8134-232">Ne vous fiez pas `FileName` à <xref:Microsoft.AspNetCore.Http.IFormFile> la propriété de sans validation ou ne faites pas confiance.</span><span class="sxs-lookup"><span data-stu-id="d8134-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="d8134-233">La `FileName` propriété ne doit être utilisée qu’à des fins d’affichage et seulement après le codage HTML.</span><span class="sxs-lookup"><span data-stu-id="d8134-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="d8134-234">Les exemples fournis ne tiennent pas compte des considérations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="d8134-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="d8134-235">Des informations supplémentaires sont fournies par les sections suivantes et [l’application d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="d8134-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="d8134-236">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="d8134-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="d8134-237">Validation</span><span class="sxs-lookup"><span data-stu-id="d8134-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="d8134-238">Téléchargez de gros fichiers avec diffusion en continu</span><span class="sxs-lookup"><span data-stu-id="d8134-238">Upload large files with streaming</span></span>

<span data-ttu-id="d8134-239">L’exemple suivant montre comment utiliser JavaScript pour diffuser un fichier sur une action de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="d8134-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="d8134-240">Le jeton antiforgery du fichier est généré à l’aide d’un attribut de filtre personnalisé et transmis au client EN-têtes HTTP au lieu de dans le corps de demande.</span><span class="sxs-lookup"><span data-stu-id="d8134-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="d8134-241">Étant donné que la méthode d’action traite directement les données téléchargées, la liaison du modèle de formulaire est désactivée par un autre filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="d8134-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="d8134-242">Dans l’action, le contenu du formulaire est lu en avec `MultipartReader`, qui lit chaque `MultipartSection` individuelle, traitant le fichier ou enregistrant le contenu selon ce qui est approprié.</span><span class="sxs-lookup"><span data-stu-id="d8134-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="d8134-243">Une fois les sections multipartites lues, l’action exécute sa propre liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="d8134-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="d8134-244">La réponse initiale de la page charge le formulaire et enregistre un `GenerateAntiforgeryTokenCookieAttribute` jeton antiforgery dans un cookie (via l’attribut).</span><span class="sxs-lookup"><span data-stu-id="d8134-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="d8134-245">L’attribut utilise ASP.NET [support antiforgery](xref:security/anti-request-forgery) intégré de Core pour définir un cookie avec un jeton de demande :</span><span class="sxs-lookup"><span data-stu-id="d8134-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="d8134-246">Le `DisableFormValueModelBindingAttribute` est utilisé pour désactiver la liaison de modèle :</span><span class="sxs-lookup"><span data-stu-id="d8134-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="d8134-247">Dans l’application `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` d’échantillon, et sont appliqués `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` comme `Startup.ConfigureServices` filtres aux modèles d’application de page de et en utilisant les [conventions de Pages Razor](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="d8134-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="d8134-248">Étant donné que la liaison du modèle ne lit pas le formulaire, les paramètres qui sont liés à partir de la forme ne se lient pas (requête, itinéraire et en-tête continuent à fonctionner).</span><span class="sxs-lookup"><span data-stu-id="d8134-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="d8134-249">La méthode d’action `Request` fonctionne directement avec la propriété.</span><span class="sxs-lookup"><span data-stu-id="d8134-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="d8134-250">Un `MultipartReader` est utilisé pour lire chaque section.</span><span class="sxs-lookup"><span data-stu-id="d8134-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="d8134-251">Les données de clé/valeur sont stockées dans un `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="d8134-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="d8134-252">Une fois que les sections multipartites sont lues, le contenu de la `KeyValueAccumulator` sont utilisés pour lier les données de formulaire à un type de modèle.</span><span class="sxs-lookup"><span data-stu-id="d8134-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="d8134-253">La `StreamingController.UploadDatabase` méthode complète pour le streaming vers une base de données avec EF Core:</span><span class="sxs-lookup"><span data-stu-id="d8134-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="d8134-254">`MultipartRequestHelper`(*Services publics/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="d8134-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="d8134-255">La `StreamingController.UploadPhysical` méthode complète pour le streaming à un emplacement physique:</span><span class="sxs-lookup"><span data-stu-id="d8134-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="d8134-256">Dans l’application d’échantillon, `FileHelpers.ProcessStreamedFile`les contrôles de validation sont gérés par .</span><span class="sxs-lookup"><span data-stu-id="d8134-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="d8134-257">Validation</span><span class="sxs-lookup"><span data-stu-id="d8134-257">Validation</span></span>

<span data-ttu-id="d8134-258">La classe de `FileHelpers` l’application de l’échantillon montre plusieurs contrôles pour les téléchargements de fichiers tamponnés <xref:Microsoft.AspNetCore.Http.IFormFile> et en streaming.</span><span class="sxs-lookup"><span data-stu-id="d8134-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="d8134-259">Pour <xref:Microsoft.AspNetCore.Http.IFormFile> le traitement des téléchargements de fichiers `ProcessFormFile` tamponnés dans l’application de l’échantillon, voir la méthode dans le fichier *Utilities/FileHelpers.cs.*</span><span class="sxs-lookup"><span data-stu-id="d8134-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="d8134-260">Pour le traitement des `ProcessStreamedFile` fichiers en streaming, voir la méthode dans le même fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="d8134-261">Les méthodes de traitement de validation démontrées dans l’application de l’échantillon ne scannent pas le contenu des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="d8134-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="d8134-262">Dans la plupart des scénarios de production, une API de scanner de virus/malware est utilisée dans le fichier avant de mettre le fichier à la disposition des utilisateurs ou d’autres systèmes.</span><span class="sxs-lookup"><span data-stu-id="d8134-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="d8134-263">Bien que l’échantillon de sujet fournisse un `FileHelpers` exemple de travail des techniques de validation, ne mettez pas en œuvre la classe dans une application de production à moins que vous ne :</span><span class="sxs-lookup"><span data-stu-id="d8134-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="d8134-264">Comprenez parfaitement la mise en œuvre.</span><span class="sxs-lookup"><span data-stu-id="d8134-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="d8134-265">Modifier la mise en œuvre le cas échéant pour l’environnement et les spécifications de l’application.</span><span class="sxs-lookup"><span data-stu-id="d8134-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="d8134-266">**Ne jamais implémenter sans discernement le code de sécurité dans une application sans répondre à ces exigences.**</span><span class="sxs-lookup"><span data-stu-id="d8134-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="d8134-267">Validation du contenu</span><span class="sxs-lookup"><span data-stu-id="d8134-267">Content validation</span></span>

<span data-ttu-id="d8134-268">**Utilisez un virus/malware tiers scannant l’API sur le contenu téléchargé.**</span><span class="sxs-lookup"><span data-stu-id="d8134-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="d8134-269">La numérisation des fichiers est exigeante sur les ressources du serveur dans des scénarios à volume élevé.</span><span class="sxs-lookup"><span data-stu-id="d8134-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="d8134-270">Si les performances de traitement des demandes sont diminuées en raison de la numérisation des fichiers, envisagez de décharger le travail de numérisation à un [service d’arrière-plan,](xref:fundamentals/host/hosted-services)peut-être un service fonctionnant sur un serveur différent du serveur de l’application.</span><span class="sxs-lookup"><span data-stu-id="d8134-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="d8134-271">En règle générale, les fichiers téléchargés sont conservés dans une zone de quarantaine jusqu’à ce que le scanner de virus d’arrière-plan les vérifie.</span><span class="sxs-lookup"><span data-stu-id="d8134-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="d8134-272">Lorsqu’un fichier passe, le fichier est déplacé vers l’emplacement normal de stockage de fichiers.</span><span class="sxs-lookup"><span data-stu-id="d8134-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="d8134-273">Ces étapes sont généralement effectuées en conjonction avec un enregistrement de base de données qui indique l’état de numérisation d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="d8134-274">En utilisant une telle approche, l’application et le serveur d’applications restent concentrés sur la réponse aux demandes.</span><span class="sxs-lookup"><span data-stu-id="d8134-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="d8134-275">Validation de la prolongation de fichier</span><span class="sxs-lookup"><span data-stu-id="d8134-275">File extension validation</span></span>

<span data-ttu-id="d8134-276">L’extension du fichier téléchargé doit être vérifiée sur une liste d’extensions autorisées.</span><span class="sxs-lookup"><span data-stu-id="d8134-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="d8134-277">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d8134-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="d8134-278">Validation de la signature du fichier</span><span class="sxs-lookup"><span data-stu-id="d8134-278">File signature validation</span></span>

<span data-ttu-id="d8134-279">La signature d’un fichier est déterminée par les premiers octets au début d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="d8134-280">Ces octets peuvent être utilisés pour indiquer si l’extension correspond au contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="d8134-281">L’application d’échantillon vérifie les signatures de fichiers pour quelques types de fichiers communs.</span><span class="sxs-lookup"><span data-stu-id="d8134-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="d8134-282">Dans l’exemple suivant, la signature du fichier pour une image JPEG est vérifiée par rapport au fichier :</span><span class="sxs-lookup"><span data-stu-id="d8134-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="d8134-283">Pour obtenir des signatures de fichiers supplémentaires, consultez la [base de données des signatures de fichiers](https://www.filesignatures.net/) et les spécifications officielles des fichiers.</span><span class="sxs-lookup"><span data-stu-id="d8134-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="d8134-284">Sécurité du nom de fichier</span><span class="sxs-lookup"><span data-stu-id="d8134-284">File name security</span></span>

<span data-ttu-id="d8134-285">N’utilisez jamais un nom de fichier fourni par le client pour enregistrer un fichier au stockage physique.</span><span class="sxs-lookup"><span data-stu-id="d8134-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="d8134-286">Créez un nom de fichier sûr pour le fichier à l’aide [de Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour créer un chemin complet (y compris le nom de fichier) pour le stockage temporaire.</span><span class="sxs-lookup"><span data-stu-id="d8134-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="d8134-287">Razor code automatiquement les valeurs des propriétés pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="d8134-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="d8134-288">Le code suivant est sûr à utiliser :</span><span class="sxs-lookup"><span data-stu-id="d8134-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="d8134-289">En dehors de <xref:System.Net.WebUtility.HtmlEncode*> Razor, toujours fichier le contenu du nom à partir de la demande d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d8134-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="d8134-290">De nombreuses implémentations doivent inclure une vérification de l’existence du fichier; autrement, le fichier est écrasé par un fichier du même nom.</span><span class="sxs-lookup"><span data-stu-id="d8134-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="d8134-291">Fournissez une logique supplémentaire pour répondre aux spécifications de votre application.</span><span class="sxs-lookup"><span data-stu-id="d8134-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="d8134-292">Validation de taille</span><span class="sxs-lookup"><span data-stu-id="d8134-292">Size validation</span></span>

<span data-ttu-id="d8134-293">Limitez la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="d8134-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="d8134-294">Dans l’application échantillon, la taille du fichier est limitée à 2 Mo (indiqué dans les octets).</span><span class="sxs-lookup"><span data-stu-id="d8134-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="d8134-295">La limite est fournie via [Configuration](xref:fundamentals/configuration/index) à partir du fichier *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="d8134-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="d8134-296">Le `FileSizeLimit` est `PageModel` injecté dans les classes:</span><span class="sxs-lookup"><span data-stu-id="d8134-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="d8134-297">Lorsqu’une taille de fichier dépasse la limite, le fichier est rejeté :</span><span class="sxs-lookup"><span data-stu-id="d8134-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="d8134-298">Valeur d’attribut de nom de match au nom de paramètre de la méthode POST</span><span class="sxs-lookup"><span data-stu-id="d8134-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="d8134-299">Dans les formulaires non-Razor que POST `FormData` forme des données ou utilisent directement `FormData` JavaScript, le nom spécifié dans l’élément du formulaire ou doit correspondre au nom du paramètre dans l’action du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="d8134-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="d8134-300">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="d8134-300">In the following example:</span></span>

* <span data-ttu-id="d8134-301">Lors de `<input>` l’utilisation d’un élément, l’attribut `name` est défini à la valeur `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="d8134-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="d8134-302">Lors `FormData` de l’utilisation dans JavaScript, `battlePlans`le nom est réglé à la valeur :</span><span class="sxs-lookup"><span data-stu-id="d8134-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="d8134-303">Utilisez un nom correspondant pour le paramètre de la méthode C (`battlePlans`(</span><span class="sxs-lookup"><span data-stu-id="d8134-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="d8134-304">Pour une méthode de `Upload`gestionnaire de page Razor Pages nommée :</span><span class="sxs-lookup"><span data-stu-id="d8134-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="d8134-305">Pour une méthode d’action de contrôleur MVC POST :</span><span class="sxs-lookup"><span data-stu-id="d8134-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="d8134-306">Configuration du serveur et de l’application</span><span class="sxs-lookup"><span data-stu-id="d8134-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="d8134-307">Limite de longueur du corps multipartite</span><span class="sxs-lookup"><span data-stu-id="d8134-307">Multipart body length limit</span></span>

<span data-ttu-id="d8134-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>fixe la limite pour la longueur de chaque corps multipartite.</span><span class="sxs-lookup"><span data-stu-id="d8134-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="d8134-309">Les sections de formulaire <xref:System.IO.InvalidDataException> qui dépassent cette limite jettent une lorsqu’elles sont analysées.</span><span class="sxs-lookup"><span data-stu-id="d8134-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="d8134-310">La valeur par défaut est de 134 217 728 (128 Mo).</span><span class="sxs-lookup"><span data-stu-id="d8134-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="d8134-311">Personnalisez la <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> limite `Startup.ConfigureServices`en utilisant le paramètre dans :</span><span class="sxs-lookup"><span data-stu-id="d8134-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="d8134-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>est utilisé pour <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> définir la pour une seule page ou une action.</span><span class="sxs-lookup"><span data-stu-id="d8134-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="d8134-313">Dans une application Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) appliquez `Startup.ConfigureServices`le filtre avec une convention dans :</span><span class="sxs-lookup"><span data-stu-id="d8134-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="d8134-314">Dans une application Razor Pages ou une application MVC, appliquez le filtre sur le modèle de page ou la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="d8134-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="d8134-315">Kestrel maximum demande la taille du corps</span><span class="sxs-lookup"><span data-stu-id="d8134-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="d8134-316">Pour les applications hébergées par Kestrel, la taille maximale de la demande par défaut est de 30 000 000 octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="d8134-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="d8134-317">Personnalisez la limite à l’aide de l’option serveur [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel :</span><span class="sxs-lookup"><span data-stu-id="d8134-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d8134-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>est utilisé pour définir le [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pour une seule page ou une action.</span><span class="sxs-lookup"><span data-stu-id="d8134-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="d8134-319">Dans une application Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) appliquez `Startup.ConfigureServices`le filtre avec une convention dans :</span><span class="sxs-lookup"><span data-stu-id="d8134-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="d8134-320">Dans une application De pages Razor ou une application MVC, appliquez le filtre sur la classe ou la méthode d’action du gestionnaire de page :</span><span class="sxs-lookup"><span data-stu-id="d8134-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="d8134-321">La `RequestSizeLimitAttribute` directive Razor peut [`@attribute`](xref:mvc/views/razor#attribute) également être appliquée à l’aide de la directive Razor :</span><span class="sxs-lookup"><span data-stu-id="d8134-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="d8134-322">Autres limites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="d8134-322">Other Kestrel limits</span></span>

<span data-ttu-id="d8134-323">D’autres limites de Kestrel peuvent s’appliquer pour les applications hébergées par Kestrel :</span><span class="sxs-lookup"><span data-stu-id="d8134-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="d8134-324">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="d8134-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="d8134-325">Taux de données de demande et de réponse</span><span class="sxs-lookup"><span data-stu-id="d8134-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="d8134-326">Limite de longueur de contenu IIS</span><span class="sxs-lookup"><span data-stu-id="d8134-326">IIS content length limit</span></span>

<span data-ttu-id="d8134-327">La limite de`maxAllowedContentLength`demande par défaut ( ) est de 30 000 000 octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="d8134-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="d8134-328">Personnalisez la limite dans le fichier *web.config* :</span><span class="sxs-lookup"><span data-stu-id="d8134-328">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="d8134-329">Ce paramètre s’applique seulement à IIS.</span><span class="sxs-lookup"><span data-stu-id="d8134-329">This setting only applies to IIS.</span></span> <span data-ttu-id="d8134-330">Par défaut, ce comportement ne se produit pas dans le cas d’un hébergement sur Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d8134-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="d8134-331">Pour plus d’informations, voir [demande limites \<de demandeLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="d8134-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="d8134-332">Les limites du module de base ASP.NET ou la présence du module de filtrage de demande DE l’IIS peuvent limiter les téléchargements à 2 ou 4 Go.</span><span class="sxs-lookup"><span data-stu-id="d8134-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="d8134-333">Pour plus d’informations, voir [Unable pour télécharger le fichier de plus de 2 Go de taille (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="d8134-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d8134-334">Dépanner</span><span class="sxs-lookup"><span data-stu-id="d8134-334">Troubleshoot</span></span>

<span data-ttu-id="d8134-335">Voici certains problèmes courants rencontrés avec le chargement de fichiers et leurs solutions possibles.</span><span class="sxs-lookup"><span data-stu-id="d8134-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="d8134-336">Erreur non trouvée lorsqu’elle est déployée sur un serveur IIS</span><span class="sxs-lookup"><span data-stu-id="d8134-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="d8134-337">L’erreur suivante indique que le fichier téléchargé dépasse la longueur de contenu configurée du serveur :</span><span class="sxs-lookup"><span data-stu-id="d8134-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="d8134-338">Pour plus d’informations sur l’augmentation de la limite, consultez la section [limite de durée du contenu DE l’IIS.](#iis-content-length-limit)</span><span class="sxs-lookup"><span data-stu-id="d8134-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="d8134-339">Échec de connexion</span><span class="sxs-lookup"><span data-stu-id="d8134-339">Connection failure</span></span>

<span data-ttu-id="d8134-340">Une erreur de connexion et une connexion de serveur de réinitialisation indiquent probablement que le fichier téléchargé dépasse la taille maximale du corps de demande de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d8134-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="d8134-341">Pour plus d’informations, consultez la section taille maximale de [la carrosserie de demande de Kestrel.](#kestrel-maximum-request-body-size)</span><span class="sxs-lookup"><span data-stu-id="d8134-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="d8134-342">Les limites de connexion client de Kestrel peuvent également nécessiter un ajustement.</span><span class="sxs-lookup"><span data-stu-id="d8134-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="d8134-343">Exception de référence null avec IFormFile</span><span class="sxs-lookup"><span data-stu-id="d8134-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="d8134-344">Si le contrôleur accepte les <xref:Microsoft.AspNetCore.Http.IFormFile> fichiers téléchargés `null`à l’aide, mais `enctype` la `multipart/form-data`valeur est , confirmer que le formulaire HTML spécifie une valeur de .</span><span class="sxs-lookup"><span data-stu-id="d8134-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="d8134-345">Si cet attribut n’est `<form>` pas défini sur l’élément, <xref:Microsoft.AspNetCore.Http.IFormFile> le `null`téléchargement de fichier ne se produit pas et tous les arguments liés sont .</span><span class="sxs-lookup"><span data-stu-id="d8134-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="d8134-346">Confirmez également que le [nom de téléchargement dans les données de forme correspond au nom de l’application](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="d8134-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="d8134-347">Stream était trop long</span><span class="sxs-lookup"><span data-stu-id="d8134-347">Stream was too long</span></span>

<span data-ttu-id="d8134-348">Les exemples dans <xref:System.IO.MemoryStream> ce sujet s’appuient sur la tenue du contenu du fichier téléchargé.</span><span class="sxs-lookup"><span data-stu-id="d8134-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="d8134-349">La limite de `MemoryStream` `int.MaxValue`taille d’un est .</span><span class="sxs-lookup"><span data-stu-id="d8134-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="d8134-350">Si le scénario de téléchargement de fichiers de l’application nécessite la détention du contenu du `MemoryStream` fichier supérieur à 50 Mo, utilisez une approche alternative qui ne repose pas sur un seul pour la tenue du contenu d’un fichier téléchargé.</span><span class="sxs-lookup"><span data-stu-id="d8134-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d8134-351">ASP.NET Core prend en charge le téléchargement d’un ou de plusieurs fichiers à l’aide de la liaison de modèle tampon pour les fichiers plus petits et du streaming inbuffered pour les fichiers plus grands.</span><span class="sxs-lookup"><span data-stu-id="d8134-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="d8134-352">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d8134-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="d8134-353">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="d8134-353">Security considerations</span></span>

<span data-ttu-id="d8134-354">Faites preuve de prudence lorsque vous offrez aux utilisateurs la possibilité de télécharger des fichiers sur un serveur.</span><span class="sxs-lookup"><span data-stu-id="d8134-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="d8134-355">Les attaquants peuvent tenter de :</span><span class="sxs-lookup"><span data-stu-id="d8134-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="d8134-356">Exécutez des attaques [par déni de service.](/windows-hardware/drivers/ifs/denial-of-service)</span><span class="sxs-lookup"><span data-stu-id="d8134-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="d8134-357">Téléchargez des virus ou des logiciels malveillants.</span><span class="sxs-lookup"><span data-stu-id="d8134-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="d8134-358">Compromettez les réseaux et les serveurs par d’autres moyens.</span><span class="sxs-lookup"><span data-stu-id="d8134-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="d8134-359">Les mesures de sécurité qui réduisent la probabilité d’une attaque réussie sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="d8134-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="d8134-360">Téléchargez des fichiers dans une zone de téléchargement de fichiers dédiée, de préférence sur un lecteur non système.</span><span class="sxs-lookup"><span data-stu-id="d8134-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="d8134-361">Un emplacement dédié facilite l’imposition de restrictions de sécurité sur les fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="d8134-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="d8134-362">Désactiver les autorisations d’exécution sur l’emplacement de téléchargement de fichier.&dagger;</span><span class="sxs-lookup"><span data-stu-id="d8134-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="d8134-363">Ne persistez **pas** les fichiers téléchargés dans le même arbre d’annuaire que l’application.&dagger;</span><span class="sxs-lookup"><span data-stu-id="d8134-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="d8134-364">Utilisez un nom de fichier sécurisé déterminé par l’application.</span><span class="sxs-lookup"><span data-stu-id="d8134-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="d8134-365">N’utilisez pas de nom de fichier fourni par l’utilisateur ou le nom de fichier non fiable du fichier téléchargé. &dagger; HTML code le nom de fichier non fiable lors de son affichage.</span><span class="sxs-lookup"><span data-stu-id="d8134-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="d8134-366">Par exemple, l’enregistrement du nom du fichier ou l’affichage dans l’interface utilisateur (Razor code automatiquement LA sortie HTML).</span><span class="sxs-lookup"><span data-stu-id="d8134-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="d8134-367">N’autorisez que les extensions de fichiers approuvées pour les spécifications de conception de l’application.&dagger;</span><span class="sxs-lookup"><span data-stu-id="d8134-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="d8134-368">Vérifiez que des vérifications côté client sont effectuées sur le serveur. &dagger; Les contrôles côté client sont faciles à contourner.</span><span class="sxs-lookup"><span data-stu-id="d8134-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="d8134-369">Vérifiez la taille d’un fichier téléchargé.</span><span class="sxs-lookup"><span data-stu-id="d8134-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="d8134-370">Définissez une limite de taille maximale pour empêcher les gros téléchargements.&dagger;</span><span class="sxs-lookup"><span data-stu-id="d8134-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="d8134-371">Lorsque les fichiers ne doivent pas être écrasés par un fichier téléchargé du même nom, vérifiez le nom du fichier contre la base de données ou le stockage physique avant de télécharger le fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="d8134-372">**Exécutez un scanner virus/malware sur le contenu téléchargé avant que le fichier ne soit stocké.**</span><span class="sxs-lookup"><span data-stu-id="d8134-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="d8134-373">&dagger;L’application d’échantillon démontre une approche qui répond aux critères.</span><span class="sxs-lookup"><span data-stu-id="d8134-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="d8134-374">Le chargement d’un code malveillant sur un système est généralement la première étape de l’exécution de code capable de :</span><span class="sxs-lookup"><span data-stu-id="d8134-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="d8134-375">Prenez complètement le contrôle d’un système.</span><span class="sxs-lookup"><span data-stu-id="d8134-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="d8134-376">Surchargez un système avec le résultat que le système se bloque.</span><span class="sxs-lookup"><span data-stu-id="d8134-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="d8134-377">Compromettre les données utilisateur ou système.</span><span class="sxs-lookup"><span data-stu-id="d8134-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="d8134-378">Appliquer des graffitis à une interface utilisateur publique.</span><span class="sxs-lookup"><span data-stu-id="d8134-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="d8134-379">Pour plus d’informations sur la réduction de la surface d’attaque quand vous acceptez des fichiers d’utilisateurs, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="d8134-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="d8134-380">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)</span><span class="sxs-lookup"><span data-stu-id="d8134-380">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="d8134-381">Sécurité Azure : Vérifier que les contrôles appropriés sont en place quand vous acceptez des fichiers d’utilisateurs</span><span class="sxs-lookup"><span data-stu-id="d8134-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="d8134-382">Pour plus d’informations sur la mise en œuvre des mesures de sécurité, y compris des exemples de l’application d’échantillon, consultez la section [Validation.](#validation)</span><span class="sxs-lookup"><span data-stu-id="d8134-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="d8134-383">Scénarios de stockage</span><span class="sxs-lookup"><span data-stu-id="d8134-383">Storage scenarios</span></span>

<span data-ttu-id="d8134-384">Les options de stockage courantes pour les fichiers comprennent :</span><span class="sxs-lookup"><span data-stu-id="d8134-384">Common storage options for files include:</span></span>

* <span data-ttu-id="d8134-385">Base de données</span><span class="sxs-lookup"><span data-stu-id="d8134-385">Database</span></span>

  * <span data-ttu-id="d8134-386">Pour les petits téléchargements de fichiers, une base de données est souvent plus rapide que les options de stockage physique (système de fichiers ou partage de réseau).</span><span class="sxs-lookup"><span data-stu-id="d8134-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="d8134-387">Une base de données est souvent plus pratique que les options de stockage physique, car la récupération d’un enregistrement de base de données pour les données utilisateur peut fournir simultanément le contenu du fichier (par exemple, une image avatar).</span><span class="sxs-lookup"><span data-stu-id="d8134-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="d8134-388">Une base de données est potentiellement moins coûteuse que l’utilisation d’un service de stockage de données.</span><span class="sxs-lookup"><span data-stu-id="d8134-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="d8134-389">Stockage physique (système de fichiers ou partage réseau)</span><span class="sxs-lookup"><span data-stu-id="d8134-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="d8134-390">Pour les téléchargements de fichiers volumineux :</span><span class="sxs-lookup"><span data-stu-id="d8134-390">For large file uploads:</span></span>
    * <span data-ttu-id="d8134-391">Les limites de base de données peuvent limiter la taille du téléchargement.</span><span class="sxs-lookup"><span data-stu-id="d8134-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="d8134-392">Le stockage physique est souvent moins économique que le stockage dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="d8134-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="d8134-393">Le stockage physique est potentiellement moins coûteux que l’utilisation d’un service de stockage de données.</span><span class="sxs-lookup"><span data-stu-id="d8134-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="d8134-394">Le processus de l’application doit avoir lu et écrire des autorisations à l’emplacement de stockage.</span><span class="sxs-lookup"><span data-stu-id="d8134-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="d8134-395">**N’accordez jamais la permission d’exécuter.**</span><span class="sxs-lookup"><span data-stu-id="d8134-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="d8134-396">Service de stockage de données (par exemple, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="d8134-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="d8134-397">Les services offrent généralement une meilleure évolutivité et une facilité de résilience par endroits sur des solutions qui sont généralement sujettes à des points de défaillance uniques.</span><span class="sxs-lookup"><span data-stu-id="d8134-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="d8134-398">Les services sont potentiellement moins coûteux dans les grands scénarios d’infrastructure de stockage.</span><span class="sxs-lookup"><span data-stu-id="d8134-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="d8134-399">Pour plus d’informations, voir [Quickstart: Use .NET pour créer un blob dans le stockage d’objets](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="d8134-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="d8134-400">Le sujet <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>démontre <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> , mais peut <xref:System.IO.FileStream> être utilisé pour enregistrer <xref:System.IO.Stream>un stockage à blob lors du travail avec un .</span><span class="sxs-lookup"><span data-stu-id="d8134-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="d8134-401">Scénarios de téléchargement de fichiers</span><span class="sxs-lookup"><span data-stu-id="d8134-401">File upload scenarios</span></span>

<span data-ttu-id="d8134-402">Deux approches générales pour le téléchargement de fichiers sont la mise en mémoire tampon et le streaming.</span><span class="sxs-lookup"><span data-stu-id="d8134-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="d8134-403">**des réponses**</span><span class="sxs-lookup"><span data-stu-id="d8134-403">**Buffering**</span></span>

<span data-ttu-id="d8134-404">L’ensemble du fichier <xref:Microsoft.AspNetCore.Http.IFormFile>est lu dans un , qui est une représentation C du fichier utilisé pour traiter ou enregistrer le fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="d8134-405">Les ressources (disque, mémoire) utilisées par les téléchargements de fichiers dépendent du nombre et de la taille des téléchargements de fichiers simultanés.</span><span class="sxs-lookup"><span data-stu-id="d8134-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="d8134-406">Si une application tente de tamponner trop de téléchargements, le site se bloque lorsqu’il est à court d’espace de mémoire ou de disque.</span><span class="sxs-lookup"><span data-stu-id="d8134-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="d8134-407">Si la taille ou la fréquence des téléchargements de fichiers est épuisante ressources d’applications, utilisez le streaming.</span><span class="sxs-lookup"><span data-stu-id="d8134-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="d8134-408">Tout fichier tampon unique dépassant 64 KB est déplacé de la mémoire à un fichier d’ampoule sur le disque.</span><span class="sxs-lookup"><span data-stu-id="d8134-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="d8134-409">La mise en mémoire des petits fichiers est couverte dans les sections suivantes de ce sujet :</span><span class="sxs-lookup"><span data-stu-id="d8134-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="d8134-410">Stockage physique</span><span class="sxs-lookup"><span data-stu-id="d8134-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="d8134-411">Sauvegarde de la base de données</span><span class="sxs-lookup"><span data-stu-id="d8134-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="d8134-412">**Diffusion en continu**</span><span class="sxs-lookup"><span data-stu-id="d8134-412">**Streaming**</span></span>

<span data-ttu-id="d8134-413">Le fichier est reçu à partir d’une demande multipartite et directement traité ou enregistré par l’application.</span><span class="sxs-lookup"><span data-stu-id="d8134-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="d8134-414">Le streaming n’améliore pas les performances de manière significative.</span><span class="sxs-lookup"><span data-stu-id="d8134-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="d8134-415">Le streaming réduit les demandes d’espace de mémoire ou de disque lors du téléchargement de fichiers.</span><span class="sxs-lookup"><span data-stu-id="d8134-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="d8134-416">Streaming fichiers volumineux est couvert dans les [fichiers Volumineux avec la](#upload-large-files-with-streaming) section streaming.</span><span class="sxs-lookup"><span data-stu-id="d8134-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="d8134-417">Téléchargez de petits fichiers avec un modèle tampon liant au stockage physique</span><span class="sxs-lookup"><span data-stu-id="d8134-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="d8134-418">Pour télécharger de petits fichiers, utilisez un formulaire multipartite ou construisez une demande POST à l’aide de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d8134-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="d8134-419">L’exemple suivant montre l’utilisation d’un formulaire Razor Pages pour télécharger un seul fichier *(Pages/BufferedSingleFileUploadPhysical.cshtml* dans l’application d’échantillon) :</span><span class="sxs-lookup"><span data-stu-id="d8134-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="d8134-420">L’exemple suivant est analogue à l’exemple précédent, sauf que :</span><span class="sxs-lookup"><span data-stu-id="d8134-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="d8134-421">JavaScript[(Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) est utilisé pour soumettre les données du formulaire.</span><span class="sxs-lookup"><span data-stu-id="d8134-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="d8134-422">Il n’y a pas de validation.</span><span class="sxs-lookup"><span data-stu-id="d8134-422">There's no validation.</span></span>

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

<span data-ttu-id="d8134-423">Pour effectuer le formulaire POST dans JavaScript pour les clients qui [ne prennent pas en charge l’API Fetch](https://caniuse.com/#feat=fetch), utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="d8134-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="d8134-424">Utilisez un Fetch Polyfill (par exemple, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="d8134-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="d8134-425">Utilisez `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="d8134-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="d8134-426">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d8134-426">For example:</span></span>

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

<span data-ttu-id="d8134-427">Afin de prendre en charge les téléchargements de`enctype`fichiers, `multipart/form-data`les formulaires HTML doivent spécifier un type d’encodage ( ) de .</span><span class="sxs-lookup"><span data-stu-id="d8134-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="d8134-428">Pour `files` un élément d’entrée à `multiple` l’appui `<input>` de télécharger plusieurs fichiers fournir l’attribut sur l’élément:</span><span class="sxs-lookup"><span data-stu-id="d8134-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="d8134-429">Les fichiers individuels téléchargés sur le serveur peuvent <xref:Microsoft.AspNetCore.Http.IFormFile>être consultés via La liaison de [modèle](xref:mvc/models/model-binding) utilisant .</span><span class="sxs-lookup"><span data-stu-id="d8134-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="d8134-430">L’application d’échantillon démontre plusieurs téléchargements de fichiers tamponnés pour les scénarios de base de données et de stockage physique.</span><span class="sxs-lookup"><span data-stu-id="d8134-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="d8134-431">**N’utilisez** `FileName` pas <xref:Microsoft.AspNetCore.Http.IFormFile> la propriété d’autres que pour l’affichage et l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="d8134-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="d8134-432">Lors de l’affichage ou de la connexion, HTML code le nom du fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="d8134-433">Un attaquant peut fournir un nom de fichier malveillant, y compris des chemins complets ou des chemins relatifs.</span><span class="sxs-lookup"><span data-stu-id="d8134-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="d8134-434">Les demandes doivent :</span><span class="sxs-lookup"><span data-stu-id="d8134-434">Applications should:</span></span>
>
> * <span data-ttu-id="d8134-435">Retirez le chemin du nom de fichier fourni par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d8134-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="d8134-436">Enregistrez le nom de fichier HTML-encoded, décrypté par voie pour l’interface utilisateur ou l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="d8134-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="d8134-437">Générer un nouveau nom de fichier aléatoire pour le stockage.</span><span class="sxs-lookup"><span data-stu-id="d8134-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="d8134-438">Le code suivant supprime le chemin du nom du fichier :</span><span class="sxs-lookup"><span data-stu-id="d8134-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="d8134-439">Les exemples fournis jusqu’à présent ne tiennent pas compte des considérations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="d8134-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="d8134-440">Des informations supplémentaires sont fournies par les sections suivantes et [l’application d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="d8134-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="d8134-441">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="d8134-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="d8134-442">Validation</span><span class="sxs-lookup"><span data-stu-id="d8134-442">Validation</span></span>](#validation)

<span data-ttu-id="d8134-443">Lors du téléchargement de <xref:Microsoft.AspNetCore.Http.IFormFile>fichiers à l’aide de la liaison du modèle et , la méthode d’action peut accepter:</span><span class="sxs-lookup"><span data-stu-id="d8134-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="d8134-444">Un <xref:Microsoft.AspNetCore.Http.IFormFile>single .</span><span class="sxs-lookup"><span data-stu-id="d8134-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="d8134-445">N’importe laquelle des collections suivantes qui représentent plusieurs fichiers :</span><span class="sxs-lookup"><span data-stu-id="d8134-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="d8134-446">[Liste](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="d8134-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="d8134-447">Correspondances contraignantes formulaire fichiers par nom.</span><span class="sxs-lookup"><span data-stu-id="d8134-447">Binding matches form files by name.</span></span> <span data-ttu-id="d8134-448">Par exemple, `name` la `<input type="file" name="formFile">` valeur HTML doit correspondre au`FormFile`paramètre/propriété CMD lié ().</span><span class="sxs-lookup"><span data-stu-id="d8134-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="d8134-449">Pour plus d’informations, voir la [valeur d’attribut de nom De match au nom de paramètre de la section méthode POST.](#match-name-attribute-value-to-parameter-name-of-post-method)</span><span class="sxs-lookup"><span data-stu-id="d8134-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="d8134-450">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="d8134-450">The following example:</span></span>

* <span data-ttu-id="d8134-451">Boucles à travers un ou plusieurs fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="d8134-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="d8134-452">Utilise [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour retourner un chemin complet pour un fichier, y compris le nom du fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="d8134-453">Enregistre les fichiers au système de fichiers local à l’aide d’un nom de fichier généré par l’application.</span><span class="sxs-lookup"><span data-stu-id="d8134-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="d8134-454">Retourne le nombre total et la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="d8134-454">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="d8134-455">Utilisez `Path.GetRandomFileName` pour générer un nom de fichier sans chemin.</span><span class="sxs-lookup"><span data-stu-id="d8134-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="d8134-456">Dans l’exemple suivant, le chemin est obtenu à partir de la configuration :</span><span class="sxs-lookup"><span data-stu-id="d8134-456">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="d8134-457">Le chemin passé <xref:System.IO.FileStream> au *doit* inclure le nom du fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="d8134-458">Si le nom du fichier <xref:System.UnauthorizedAccessException> n’est pas fourni, un est jeté à l’heure de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="d8134-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="d8134-459">Les fichiers téléchargés à l’aide de la <xref:Microsoft.AspNetCore.Http.IFormFile> technique sont tamponnés en mémoire ou sur disque sur le serveur avant le traitement.</span><span class="sxs-lookup"><span data-stu-id="d8134-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="d8134-460">À l’intérieur <xref:Microsoft.AspNetCore.Http.IFormFile> de la méthode <xref:System.IO.Stream>d’action, le contenu est accessible en tant que .</span><span class="sxs-lookup"><span data-stu-id="d8134-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="d8134-461">En plus du système de fichiers local, les fichiers peuvent être enregistrés sur une part de réseau ou sur un service de stockage de fichiers, comme [le stockage Azure Blob](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="d8134-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="d8134-462">Pour un autre exemple qui boucle sur plusieurs fichiers pour télécharger et utilise des noms de fichiers sûrs, voir *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* dans l’application de l’échantillon.</span><span class="sxs-lookup"><span data-stu-id="d8134-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="d8134-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) lance <xref:System.IO.IOException> un si plus de 65 535 fichiers sont créés sans supprimer les fichiers temporaires précédents.</span><span class="sxs-lookup"><span data-stu-id="d8134-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="d8134-464">La limite de 65 535 fichiers est une limite par serveur.</span><span class="sxs-lookup"><span data-stu-id="d8134-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="d8134-465">Pour plus d’informations sur cette limite sur Windows OS, voir les remarques dans les sujets suivants:</span><span class="sxs-lookup"><span data-stu-id="d8134-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="d8134-466">Fonction GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="d8134-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="d8134-467">Téléchargez de petits fichiers avec une liaison de modèle tampon dans une base de données</span><span class="sxs-lookup"><span data-stu-id="d8134-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="d8134-468">Pour stocker les données de fichiers binaires <xref:System.Byte> dans une base de données à l’aide [d’Entity Framework](/ef/core/index), définissez une propriété de tableau sur l’entité :</span><span class="sxs-lookup"><span data-stu-id="d8134-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="d8134-469">Spécifier un modèle de <xref:Microsoft.AspNetCore.Http.IFormFile>page pour la classe qui comprend un :</span><span class="sxs-lookup"><span data-stu-id="d8134-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="d8134-470"><xref:Microsoft.AspNetCore.Http.IFormFile>peut être utilisé directement comme paramètre de méthode d’action ou comme propriété modèle liée.</span><span class="sxs-lookup"><span data-stu-id="d8134-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="d8134-471">L’exemple précédent utilise une propriété modèle liée.</span><span class="sxs-lookup"><span data-stu-id="d8134-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="d8134-472">Le `FileUpload` est utilisé dans le formulaire Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="d8134-472">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="d8134-473">Lorsque le formulaire est POSTed sur <xref:Microsoft.AspNetCore.Http.IFormFile> le serveur, copiez-le sur un flux et enregistrez-le comme un tableau d’endlète dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="d8134-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="d8134-474">Dans l’exemple `_dbContext` suivant, stocke le contexte de la base de données de l’application :</span><span class="sxs-lookup"><span data-stu-id="d8134-474">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="d8134-475">L’exemple précédent est similaire à un scénario démontré dans l’application de l’échantillon :</span><span class="sxs-lookup"><span data-stu-id="d8134-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="d8134-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d8134-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="d8134-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d8134-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="d8134-478">Soyez prudent quand vous stockez des données binaires dans des bases de données relationnelles, car cela peut avoir un impact négatif sur les performances.</span><span class="sxs-lookup"><span data-stu-id="d8134-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="d8134-479">Ne vous fiez pas `FileName` à <xref:Microsoft.AspNetCore.Http.IFormFile> la propriété de sans validation ou ne faites pas confiance.</span><span class="sxs-lookup"><span data-stu-id="d8134-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="d8134-480">La `FileName` propriété ne doit être utilisée qu’à des fins d’affichage et seulement après le codage HTML.</span><span class="sxs-lookup"><span data-stu-id="d8134-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="d8134-481">Les exemples fournis ne tiennent pas compte des considérations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="d8134-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="d8134-482">Des informations supplémentaires sont fournies par les sections suivantes et [l’application d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="d8134-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="d8134-483">Considérations relatives à la sécurité</span><span class="sxs-lookup"><span data-stu-id="d8134-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="d8134-484">Validation</span><span class="sxs-lookup"><span data-stu-id="d8134-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="d8134-485">Téléchargez de gros fichiers avec diffusion en continu</span><span class="sxs-lookup"><span data-stu-id="d8134-485">Upload large files with streaming</span></span>

<span data-ttu-id="d8134-486">L’exemple suivant montre comment utiliser JavaScript pour diffuser un fichier sur une action de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="d8134-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="d8134-487">Le jeton antiforgery du fichier est généré à l’aide d’un attribut de filtre personnalisé et transmis au client EN-têtes HTTP au lieu de dans le corps de demande.</span><span class="sxs-lookup"><span data-stu-id="d8134-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="d8134-488">Étant donné que la méthode d’action traite directement les données téléchargées, la liaison du modèle de formulaire est désactivée par un autre filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="d8134-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="d8134-489">Dans l’action, le contenu du formulaire est lu en avec `MultipartReader`, qui lit chaque `MultipartSection` individuelle, traitant le fichier ou enregistrant le contenu selon ce qui est approprié.</span><span class="sxs-lookup"><span data-stu-id="d8134-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="d8134-490">Une fois les sections multipartites lues, l’action exécute sa propre liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="d8134-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="d8134-491">La réponse initiale de la page charge le formulaire et enregistre un `GenerateAntiforgeryTokenCookieAttribute` jeton antiforgery dans un cookie (via l’attribut).</span><span class="sxs-lookup"><span data-stu-id="d8134-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="d8134-492">L’attribut utilise ASP.NET [support antiforgery](xref:security/anti-request-forgery) intégré de Core pour définir un cookie avec un jeton de demande :</span><span class="sxs-lookup"><span data-stu-id="d8134-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="d8134-493">Le `DisableFormValueModelBindingAttribute` est utilisé pour désactiver la liaison de modèle :</span><span class="sxs-lookup"><span data-stu-id="d8134-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="d8134-494">Dans l’application `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` d’échantillon, et sont appliqués `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` comme `Startup.ConfigureServices` filtres aux modèles d’application de page de et en utilisant les [conventions de Pages Razor](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="d8134-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="d8134-495">Étant donné que la liaison du modèle ne lit pas le formulaire, les paramètres qui sont liés à partir de la forme ne se lient pas (requête, itinéraire et en-tête continuent à fonctionner).</span><span class="sxs-lookup"><span data-stu-id="d8134-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="d8134-496">La méthode d’action `Request` fonctionne directement avec la propriété.</span><span class="sxs-lookup"><span data-stu-id="d8134-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="d8134-497">Un `MultipartReader` est utilisé pour lire chaque section.</span><span class="sxs-lookup"><span data-stu-id="d8134-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="d8134-498">Les données de clé/valeur sont stockées dans un `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="d8134-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="d8134-499">Une fois que les sections multipartites sont lues, le contenu de la `KeyValueAccumulator` sont utilisés pour lier les données de formulaire à un type de modèle.</span><span class="sxs-lookup"><span data-stu-id="d8134-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="d8134-500">La `StreamingController.UploadDatabase` méthode complète pour le streaming vers une base de données avec EF Core:</span><span class="sxs-lookup"><span data-stu-id="d8134-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="d8134-501">`MultipartRequestHelper`(*Services publics/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="d8134-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="d8134-502">La `StreamingController.UploadPhysical` méthode complète pour le streaming à un emplacement physique:</span><span class="sxs-lookup"><span data-stu-id="d8134-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="d8134-503">Dans l’application d’échantillon, `FileHelpers.ProcessStreamedFile`les contrôles de validation sont gérés par .</span><span class="sxs-lookup"><span data-stu-id="d8134-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="d8134-504">Validation</span><span class="sxs-lookup"><span data-stu-id="d8134-504">Validation</span></span>

<span data-ttu-id="d8134-505">La classe de `FileHelpers` l’application de l’échantillon montre plusieurs contrôles pour les téléchargements de fichiers tamponnés <xref:Microsoft.AspNetCore.Http.IFormFile> et en streaming.</span><span class="sxs-lookup"><span data-stu-id="d8134-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="d8134-506">Pour <xref:Microsoft.AspNetCore.Http.IFormFile> le traitement des téléchargements de fichiers `ProcessFormFile` tamponnés dans l’application de l’échantillon, voir la méthode dans le fichier *Utilities/FileHelpers.cs.*</span><span class="sxs-lookup"><span data-stu-id="d8134-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="d8134-507">Pour le traitement des `ProcessStreamedFile` fichiers en streaming, voir la méthode dans le même fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="d8134-508">Les méthodes de traitement de validation démontrées dans l’application de l’échantillon ne scannent pas le contenu des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="d8134-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="d8134-509">Dans la plupart des scénarios de production, une API de scanner de virus/malware est utilisée dans le fichier avant de mettre le fichier à la disposition des utilisateurs ou d’autres systèmes.</span><span class="sxs-lookup"><span data-stu-id="d8134-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="d8134-510">Bien que l’échantillon de sujet fournisse un `FileHelpers` exemple de travail des techniques de validation, ne mettez pas en œuvre la classe dans une application de production à moins que vous ne :</span><span class="sxs-lookup"><span data-stu-id="d8134-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="d8134-511">Comprenez parfaitement la mise en œuvre.</span><span class="sxs-lookup"><span data-stu-id="d8134-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="d8134-512">Modifier la mise en œuvre le cas échéant pour l’environnement et les spécifications de l’application.</span><span class="sxs-lookup"><span data-stu-id="d8134-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="d8134-513">**Ne jamais implémenter sans discernement le code de sécurité dans une application sans répondre à ces exigences.**</span><span class="sxs-lookup"><span data-stu-id="d8134-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="d8134-514">Validation du contenu</span><span class="sxs-lookup"><span data-stu-id="d8134-514">Content validation</span></span>

<span data-ttu-id="d8134-515">**Utilisez un virus/malware tiers scannant l’API sur le contenu téléchargé.**</span><span class="sxs-lookup"><span data-stu-id="d8134-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="d8134-516">La numérisation des fichiers est exigeante sur les ressources du serveur dans des scénarios à volume élevé.</span><span class="sxs-lookup"><span data-stu-id="d8134-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="d8134-517">Si les performances de traitement des demandes sont diminuées en raison de la numérisation des fichiers, envisagez de décharger le travail de numérisation à un [service d’arrière-plan,](xref:fundamentals/host/hosted-services)peut-être un service fonctionnant sur un serveur différent du serveur de l’application.</span><span class="sxs-lookup"><span data-stu-id="d8134-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="d8134-518">En règle générale, les fichiers téléchargés sont conservés dans une zone de quarantaine jusqu’à ce que le scanner de virus d’arrière-plan les vérifie.</span><span class="sxs-lookup"><span data-stu-id="d8134-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="d8134-519">Lorsqu’un fichier passe, le fichier est déplacé vers l’emplacement normal de stockage de fichiers.</span><span class="sxs-lookup"><span data-stu-id="d8134-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="d8134-520">Ces étapes sont généralement effectuées en conjonction avec un enregistrement de base de données qui indique l’état de numérisation d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="d8134-521">En utilisant une telle approche, l’application et le serveur d’applications restent concentrés sur la réponse aux demandes.</span><span class="sxs-lookup"><span data-stu-id="d8134-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="d8134-522">Validation de la prolongation de fichier</span><span class="sxs-lookup"><span data-stu-id="d8134-522">File extension validation</span></span>

<span data-ttu-id="d8134-523">L’extension du fichier téléchargé doit être vérifiée sur une liste d’extensions autorisées.</span><span class="sxs-lookup"><span data-stu-id="d8134-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="d8134-524">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d8134-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="d8134-525">Validation de la signature du fichier</span><span class="sxs-lookup"><span data-stu-id="d8134-525">File signature validation</span></span>

<span data-ttu-id="d8134-526">La signature d’un fichier est déterminée par les premiers octets au début d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="d8134-527">Ces octets peuvent être utilisés pour indiquer si l’extension correspond au contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="d8134-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="d8134-528">L’application d’échantillon vérifie les signatures de fichiers pour quelques types de fichiers communs.</span><span class="sxs-lookup"><span data-stu-id="d8134-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="d8134-529">Dans l’exemple suivant, la signature du fichier pour une image JPEG est vérifiée par rapport au fichier :</span><span class="sxs-lookup"><span data-stu-id="d8134-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="d8134-530">Pour obtenir des signatures de fichiers supplémentaires, consultez la [base de données des signatures de fichiers](https://www.filesignatures.net/) et les spécifications officielles des fichiers.</span><span class="sxs-lookup"><span data-stu-id="d8134-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="d8134-531">Sécurité du nom de fichier</span><span class="sxs-lookup"><span data-stu-id="d8134-531">File name security</span></span>

<span data-ttu-id="d8134-532">N’utilisez jamais un nom de fichier fourni par le client pour enregistrer un fichier au stockage physique.</span><span class="sxs-lookup"><span data-stu-id="d8134-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="d8134-533">Créez un nom de fichier sûr pour le fichier à l’aide [de Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour créer un chemin complet (y compris le nom de fichier) pour le stockage temporaire.</span><span class="sxs-lookup"><span data-stu-id="d8134-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="d8134-534">Razor code automatiquement les valeurs des propriétés pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="d8134-534">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="d8134-535">Le code suivant est sûr à utiliser :</span><span class="sxs-lookup"><span data-stu-id="d8134-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="d8134-536">En dehors de <xref:System.Net.WebUtility.HtmlEncode*> Razor, toujours fichier le contenu du nom à partir de la demande d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d8134-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="d8134-537">De nombreuses implémentations doivent inclure une vérification de l’existence du fichier; autrement, le fichier est écrasé par un fichier du même nom.</span><span class="sxs-lookup"><span data-stu-id="d8134-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="d8134-538">Fournissez une logique supplémentaire pour répondre aux spécifications de votre application.</span><span class="sxs-lookup"><span data-stu-id="d8134-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="d8134-539">Validation de taille</span><span class="sxs-lookup"><span data-stu-id="d8134-539">Size validation</span></span>

<span data-ttu-id="d8134-540">Limitez la taille des fichiers téléchargés.</span><span class="sxs-lookup"><span data-stu-id="d8134-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="d8134-541">Dans l’application échantillon, la taille du fichier est limitée à 2 Mo (indiqué dans les octets).</span><span class="sxs-lookup"><span data-stu-id="d8134-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="d8134-542">La limite est fournie via [Configuration](xref:fundamentals/configuration/index) à partir du fichier *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="d8134-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="d8134-543">Le `FileSizeLimit` est `PageModel` injecté dans les classes:</span><span class="sxs-lookup"><span data-stu-id="d8134-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="d8134-544">Lorsqu’une taille de fichier dépasse la limite, le fichier est rejeté :</span><span class="sxs-lookup"><span data-stu-id="d8134-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="d8134-545">Valeur d’attribut de nom de match au nom de paramètre de la méthode POST</span><span class="sxs-lookup"><span data-stu-id="d8134-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="d8134-546">Dans les formulaires non-Razor que POST `FormData` forme des données ou utilisent directement `FormData` JavaScript, le nom spécifié dans l’élément du formulaire ou doit correspondre au nom du paramètre dans l’action du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="d8134-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="d8134-547">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="d8134-547">In the following example:</span></span>

* <span data-ttu-id="d8134-548">Lors de `<input>` l’utilisation d’un élément, l’attribut `name` est défini à la valeur `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="d8134-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="d8134-549">Lors `FormData` de l’utilisation dans JavaScript, `battlePlans`le nom est réglé à la valeur :</span><span class="sxs-lookup"><span data-stu-id="d8134-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="d8134-550">Utilisez un nom correspondant pour le paramètre de la méthode C (`battlePlans`(</span><span class="sxs-lookup"><span data-stu-id="d8134-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="d8134-551">Pour une méthode de `Upload`gestionnaire de page Razor Pages nommée :</span><span class="sxs-lookup"><span data-stu-id="d8134-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="d8134-552">Pour une méthode d’action de contrôleur MVC POST :</span><span class="sxs-lookup"><span data-stu-id="d8134-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="d8134-553">Configuration du serveur et de l’application</span><span class="sxs-lookup"><span data-stu-id="d8134-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="d8134-554">Limite de longueur du corps multipartite</span><span class="sxs-lookup"><span data-stu-id="d8134-554">Multipart body length limit</span></span>

<span data-ttu-id="d8134-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>fixe la limite pour la longueur de chaque corps multipartite.</span><span class="sxs-lookup"><span data-stu-id="d8134-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="d8134-556">Les sections de formulaire <xref:System.IO.InvalidDataException> qui dépassent cette limite jettent une lorsqu’elles sont analysées.</span><span class="sxs-lookup"><span data-stu-id="d8134-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="d8134-557">La valeur par défaut est de 134 217 728 (128 Mo).</span><span class="sxs-lookup"><span data-stu-id="d8134-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="d8134-558">Personnalisez la <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> limite `Startup.ConfigureServices`en utilisant le paramètre dans :</span><span class="sxs-lookup"><span data-stu-id="d8134-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="d8134-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>est utilisé pour <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> définir la pour une seule page ou une action.</span><span class="sxs-lookup"><span data-stu-id="d8134-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="d8134-560">Dans une application Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) appliquez `Startup.ConfigureServices`le filtre avec une convention dans :</span><span class="sxs-lookup"><span data-stu-id="d8134-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="d8134-561">Dans une application Razor Pages ou une application MVC, appliquez le filtre sur le modèle de page ou la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="d8134-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="d8134-562">Kestrel maximum demande la taille du corps</span><span class="sxs-lookup"><span data-stu-id="d8134-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="d8134-563">Pour les applications hébergées par Kestrel, la taille maximale de la demande par défaut est de 30 000 000 octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="d8134-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="d8134-564">Personnalisez la limite à l’aide de l’option serveur [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel :</span><span class="sxs-lookup"><span data-stu-id="d8134-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="d8134-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>est utilisé pour définir le [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pour une seule page ou une action.</span><span class="sxs-lookup"><span data-stu-id="d8134-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="d8134-566">Dans une application Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) appliquez `Startup.ConfigureServices`le filtre avec une convention dans :</span><span class="sxs-lookup"><span data-stu-id="d8134-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="d8134-567">Dans une application De pages Razor ou une application MVC, appliquez le filtre sur la classe ou la méthode d’action du gestionnaire de page :</span><span class="sxs-lookup"><span data-stu-id="d8134-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="d8134-568">Autres limites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="d8134-568">Other Kestrel limits</span></span>

<span data-ttu-id="d8134-569">D’autres limites de Kestrel peuvent s’appliquer pour les applications hébergées par Kestrel :</span><span class="sxs-lookup"><span data-stu-id="d8134-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="d8134-570">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="d8134-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="d8134-571">Taux de données de demande et de réponse</span><span class="sxs-lookup"><span data-stu-id="d8134-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="d8134-572">Limite de longueur de contenu IIS</span><span class="sxs-lookup"><span data-stu-id="d8134-572">IIS content length limit</span></span>

<span data-ttu-id="d8134-573">La limite de`maxAllowedContentLength`demande par défaut ( ) est de 30 000 000 octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="d8134-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="d8134-574">Personnalisez la limite dans le fichier *web.config* :</span><span class="sxs-lookup"><span data-stu-id="d8134-574">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="d8134-575">Ce paramètre s’applique seulement à IIS.</span><span class="sxs-lookup"><span data-stu-id="d8134-575">This setting only applies to IIS.</span></span> <span data-ttu-id="d8134-576">Par défaut, ce comportement ne se produit pas dans le cas d’un hébergement sur Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d8134-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="d8134-577">Pour plus d’informations, voir [demande limites \<de demandeLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="d8134-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="d8134-578">Les limites du module de base ASP.NET ou la présence du module de filtrage de demande DE l’IIS peuvent limiter les téléchargements à 2 ou 4 Go.</span><span class="sxs-lookup"><span data-stu-id="d8134-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="d8134-579">Pour plus d’informations, voir [Unable pour télécharger le fichier de plus de 2 Go de taille (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="d8134-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d8134-580">Dépanner</span><span class="sxs-lookup"><span data-stu-id="d8134-580">Troubleshoot</span></span>

<span data-ttu-id="d8134-581">Voici certains problèmes courants rencontrés avec le chargement de fichiers et leurs solutions possibles.</span><span class="sxs-lookup"><span data-stu-id="d8134-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="d8134-582">Erreur non trouvée lorsqu’elle est déployée sur un serveur IIS</span><span class="sxs-lookup"><span data-stu-id="d8134-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="d8134-583">L’erreur suivante indique que le fichier téléchargé dépasse la longueur de contenu configurée du serveur :</span><span class="sxs-lookup"><span data-stu-id="d8134-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="d8134-584">Pour plus d’informations sur l’augmentation de la limite, consultez la section [limite de durée du contenu DE l’IIS.](#iis-content-length-limit)</span><span class="sxs-lookup"><span data-stu-id="d8134-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="d8134-585">Échec de connexion</span><span class="sxs-lookup"><span data-stu-id="d8134-585">Connection failure</span></span>

<span data-ttu-id="d8134-586">Une erreur de connexion et une connexion de serveur de réinitialisation indiquent probablement que le fichier téléchargé dépasse la taille maximale du corps de demande de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d8134-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="d8134-587">Pour plus d’informations, consultez la section taille maximale de [la carrosserie de demande de Kestrel.](#kestrel-maximum-request-body-size)</span><span class="sxs-lookup"><span data-stu-id="d8134-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="d8134-588">Les limites de connexion client de Kestrel peuvent également nécessiter un ajustement.</span><span class="sxs-lookup"><span data-stu-id="d8134-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="d8134-589">Exception de référence null avec IFormFile</span><span class="sxs-lookup"><span data-stu-id="d8134-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="d8134-590">Si le contrôleur accepte les <xref:Microsoft.AspNetCore.Http.IFormFile> fichiers téléchargés `null`à l’aide, mais `enctype` la `multipart/form-data`valeur est , confirmer que le formulaire HTML spécifie une valeur de .</span><span class="sxs-lookup"><span data-stu-id="d8134-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="d8134-591">Si cet attribut n’est `<form>` pas défini sur l’élément, <xref:Microsoft.AspNetCore.Http.IFormFile> le `null`téléchargement de fichier ne se produit pas et tous les arguments liés sont .</span><span class="sxs-lookup"><span data-stu-id="d8134-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="d8134-592">Confirmez également que le [nom de téléchargement dans les données de forme correspond au nom de l’application](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="d8134-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="d8134-593">Stream était trop long</span><span class="sxs-lookup"><span data-stu-id="d8134-593">Stream was too long</span></span>

<span data-ttu-id="d8134-594">Les exemples dans <xref:System.IO.MemoryStream> ce sujet s’appuient sur la tenue du contenu du fichier téléchargé.</span><span class="sxs-lookup"><span data-stu-id="d8134-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="d8134-595">La limite de `MemoryStream` `int.MaxValue`taille d’un est .</span><span class="sxs-lookup"><span data-stu-id="d8134-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="d8134-596">Si le scénario de téléchargement de fichiers de l’application nécessite la détention du contenu du `MemoryStream` fichier supérieur à 50 Mo, utilisez une approche alternative qui ne repose pas sur un seul pour la tenue du contenu d’un fichier téléchargé.</span><span class="sxs-lookup"><span data-stu-id="d8134-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="d8134-597">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d8134-597">Additional resources</span></span>

* <span data-ttu-id="d8134-598">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)</span><span class="sxs-lookup"><span data-stu-id="d8134-598">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
* [<span data-ttu-id="d8134-599">Sécurité Azure : cadre de sécurité : Validation des entrées Atténuation</span><span class="sxs-lookup"><span data-stu-id="d8134-599">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="d8134-600">Modèles de conception de cloud Azure : modèle de clé de valet</span><span class="sxs-lookup"><span data-stu-id="d8134-600">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
