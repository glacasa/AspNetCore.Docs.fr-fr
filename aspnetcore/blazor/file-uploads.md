---
title: BlazorChargements de fichiers ASP.net Core
author: guardrex
description: Découvrez comment charger des fichiers dans à Blazor l’aide du composant FichierEntrée.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/file-uploads
ms.openlocfilehash: 06d1464cb731a8008362fc911f463e4ff8a37b6b
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606651"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="2fa1d-103">BlazorChargements de fichiers ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="2fa1d-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="2fa1d-104">Par [Daniel Roth](https://github.com/danroth27) et [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="2fa1d-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="2fa1d-105">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2fa1d-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2fa1d-106">Utilisez le `InputFile` composant pour lire les données du fichier browser dans le code .net, y compris pour les chargements de fichiers.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="2fa1d-107">Suivez toujours les meilleures pratiques en matière de sécurité du chargement des fichiers.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="2fa1d-108">Pour plus d'informations, consultez <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="2fa1d-109">Composant `InputFile`</span><span class="sxs-lookup"><span data-stu-id="2fa1d-109">`InputFile` component</span></span>

<span data-ttu-id="2fa1d-110">Le `InputFile` composant est rendu sous la forme d’une entrée HTML de type `file` .</span><span class="sxs-lookup"><span data-stu-id="2fa1d-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="2fa1d-111">Par défaut, l’utilisateur sélectionne des fichiers uniques.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-111">By default, the user selects single files.</span></span> <span data-ttu-id="2fa1d-112">Ajoutez l' `multiple` attribut pour permettre à l’utilisateur de télécharger plusieurs fichiers à la fois.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="2fa1d-113">Quand un ou plusieurs fichiers sont sélectionnés par l’utilisateur, le `InputFile` composant déclenche un `OnChange` événement et transmet un `InputFileChangeEventArgs` qui fournit l’accès à la liste des fichiers sélectionnés et des détails sur chaque fichier.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="2fa1d-114">Pour lire des données à partir d’un fichier sélectionné par l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="2fa1d-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="2fa1d-115">Appelez `OpenReadStream` sur le fichier et lisez à partir du flux retourné.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-115">Call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="2fa1d-116">Pour plus d’informations, consultez la section [flux de fichier](#file-streams) .</span><span class="sxs-lookup"><span data-stu-id="2fa1d-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="2fa1d-117">Utilisez `ReadAsync`.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-117">Use `ReadAsync`.</span></span> <span data-ttu-id="2fa1d-118">Par défaut, `ReadAsync` autorise uniquement la lecture d’un fichier d’une taille inférieure à 524 288 Ko (512 Ko).</span><span class="sxs-lookup"><span data-stu-id="2fa1d-118">By default, `ReadAsync` only allows reading a file smaller than 524,288 KB (512 KB) in size.</span></span> <span data-ttu-id="2fa1d-119">Cette limite est présente pour empêcher les développeurs de lire accidentellement des fichiers volumineux dans la mémoire.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="2fa1d-120">Spécifiez une approximation raisonnable pour la taille de fichier maximale attendue si les fichiers plus volumineux doivent être pris en charge.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-120">Specify a reasonable approximation for the maximum expected file size if larger files must be supported.</span></span> <span data-ttu-id="2fa1d-121">Évitez de lire le flux de fichier entrant directement dans la mémoire.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="2fa1d-122">Par exemple, ne copiez pas les octets du fichier dans un <xref:System.IO.MemoryStream> ou lisez-le en tant que tableau d’octets.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="2fa1d-123">Ces approches peuvent entraîner des problèmes de performances et de sécurité, en particulier dans Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="2fa1d-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="2fa1d-124">Au lieu de cela, envisagez de copier les octets de fichier dans un magasin externe, tel qu’un objet BLOB ou un fichier sur le disque.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="2fa1d-125">Un composant qui reçoit un fichier image peut appeler la `RequestImageFileAsync` méthode pratique sur le fichier pour redimensionner les données de l’image dans le runtime JavaScript du navigateur avant que l’image ne soit diffusée dans l’application.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="2fa1d-126">L’exemple suivant illustre le chargement de plusieurs fichiers image dans un composant.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="2fa1d-127">`InputFileChangeEventArgs.GetMultipleFiles` autorise la lecture de plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="2fa1d-128">Spécifiez le nombre maximal de fichiers que vous prévoyez de lire pour empêcher un utilisateur malveillant de télécharger un plus grand nombre de fichiers que celle attendue par l’application.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="2fa1d-129">`InputFileChangeEventArgs.File` permet de lire le premier et le seul fichier uniquement si le téléchargement de fichier ne prend pas en charge plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

<span data-ttu-id="2fa1d-130">`IBrowserFile` retourne les métadonnées [exposées par le navigateur](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) en tant que propriétés.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="2fa1d-131">Ces métadonnées peuvent être utiles pour la validation préliminaire.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="2fa1d-132">Par exemple, consultez les [ `FileUpload.razor` `FilePreview.razor` exemples de composants et](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="2fa1d-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="2fa1d-133">Flux de fichiers</span><span class="sxs-lookup"><span data-stu-id="2fa1d-133">File streams</span></span>

<span data-ttu-id="2fa1d-134">Dans une Blazor WebAssembly application, les données sont transmises directement dans le code .net au sein du navigateur.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-134">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="2fa1d-135">Dans une Blazor Server application, les données de fichier sont diffusées en continu via la SignalR connexion dans le code .net sur le serveur lors de la lecture du fichier à partir du flux.</span><span class="sxs-lookup"><span data-stu-id="2fa1d-135">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="2fa1d-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) autorise la configuration des caractéristiques de chargement de fichier pour Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="2fa1d-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2fa1d-137">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2fa1d-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>