---
title: BlazorChargements de fichiers ASP.net Core
author: guardrex
description: Découvrez comment charger des fichiers dans à Blazor l’aide du composant FichierEntrée.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
ms.openlocfilehash: de4654f2efc401143e066628b096052efa65d7a0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722980"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="7b070-103">BlazorChargements de fichiers ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="7b070-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="7b070-104">Par [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="7b070-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="7b070-105">Utilisez le `InputFile` composant pour lire les données du fichier browser dans le code .net, y compris pour les chargements de fichiers.</span><span class="sxs-lookup"><span data-stu-id="7b070-105">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span> <span data-ttu-id="7b070-106">Le `InputFile` composant est rendu sous la forme d’une entrée HTML de type `file` .</span><span class="sxs-lookup"><span data-stu-id="7b070-106">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="7b070-107">Par défaut, l’utilisateur sélectionne des fichiers uniques.</span><span class="sxs-lookup"><span data-stu-id="7b070-107">By default, the user selects single files.</span></span> <span data-ttu-id="7b070-108">Ajoutez l' `multiple` attribut pour permettre à l’utilisateur de télécharger plusieurs fichiers à la fois.</span><span class="sxs-lookup"><span data-stu-id="7b070-108">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="7b070-109">Quand un ou plusieurs fichiers sont sélectionnés par l’utilisateur, le `InputFile` composant déclenche un `OnChange` événement et transmet un `InputFileChangeEventArgs` qui fournit l’accès à la liste des fichiers sélectionnés et des détails sur chaque fichier.</span><span class="sxs-lookup"><span data-stu-id="7b070-109">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="7b070-110">Un composant qui reçoit un fichier image peut appeler la `RequestImageFileAsync` méthode pratique sur le fichier pour redimensionner les données de l’image dans le runtime JavaScript du navigateur avant que l’image ne soit diffusée dans l’application.</span><span class="sxs-lookup"><span data-stu-id="7b070-110">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="7b070-111">L’exemple suivant illustre le chargement de plusieurs fichiers image dans un composant :</span><span class="sxs-lookup"><span data-stu-id="7b070-111">The following example demonstrates multiple image file upload in a component:</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h3>Images</h3>

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
        var imageFiles = e.GetMultipleFiles();
        var format = "image/png";

        foreach (var imageFile in imageFiles)
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

<span data-ttu-id="7b070-112">Pour lire des données à partir d’un fichier sélectionné par l’utilisateur, appelez `OpenReadStream` sur le fichier et lisez à partir du flux retourné.</span><span class="sxs-lookup"><span data-stu-id="7b070-112">To read data from a user-selected file, call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="7b070-113">Dans une Blazor WebAssembly application, les données sont transmises directement dans le code .net au sein du navigateur.</span><span class="sxs-lookup"><span data-stu-id="7b070-113">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span> <span data-ttu-id="7b070-114">Dans une Blazor Server application, les données de fichier sont transmises dans le code .net sur le serveur lors de la lecture du fichier à partir du flux.</span><span class="sxs-lookup"><span data-stu-id="7b070-114">In a Blazor Server app, the file data is streamed into .NET code on the server as the file is read from the stream.</span></span> 
