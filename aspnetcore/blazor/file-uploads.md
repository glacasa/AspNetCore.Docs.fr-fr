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
# <a name="aspnet-core-no-locblazor-file-uploads"></a>BlazorChargements de fichiers ASP.net Core

Par [Daniel Roth](https://github.com/danroth27) et [Pranav Krishnamoorthy](https://github.com/pranavkm)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Utilisez le `InputFile` composant pour lire les données du fichier browser dans le code .net, y compris pour les chargements de fichiers.

> [!WARNING]
> Suivez toujours les meilleures pratiques en matière de sécurité du chargement des fichiers. Pour plus d'informations, consultez <xref:mvc/models/file-uploads#security-considerations>.

## <a name="inputfile-component"></a>Composant `InputFile`

Le `InputFile` composant est rendu sous la forme d’une entrée HTML de type `file` .

Par défaut, l’utilisateur sélectionne des fichiers uniques. Ajoutez l' `multiple` attribut pour permettre à l’utilisateur de télécharger plusieurs fichiers à la fois. Quand un ou plusieurs fichiers sont sélectionnés par l’utilisateur, le `InputFile` composant déclenche un `OnChange` événement et transmet un `InputFileChangeEventArgs` qui fournit l’accès à la liste des fichiers sélectionnés et des détails sur chaque fichier.

Pour lire des données à partir d’un fichier sélectionné par l’utilisateur :

* Appelez `OpenReadStream` sur le fichier et lisez à partir du flux retourné. Pour plus d’informations, consultez la section [flux de fichier](#file-streams) .
* Utilisez `ReadAsync`. Par défaut, `ReadAsync` autorise uniquement la lecture d’un fichier d’une taille inférieure à 524 288 Ko (512 Ko). Cette limite est présente pour empêcher les développeurs de lire accidentellement des fichiers volumineux dans la mémoire. Spécifiez une approximation raisonnable pour la taille de fichier maximale attendue si les fichiers plus volumineux doivent être pris en charge. Évitez de lire le flux de fichier entrant directement dans la mémoire. Par exemple, ne copiez pas les octets du fichier dans un <xref:System.IO.MemoryStream> ou lisez-le en tant que tableau d’octets. Ces approches peuvent entraîner des problèmes de performances et de sécurité, en particulier dans Blazor Server . Au lieu de cela, envisagez de copier les octets de fichier dans un magasin externe, tel qu’un objet BLOB ou un fichier sur le disque.

Un composant qui reçoit un fichier image peut appeler la `RequestImageFileAsync` méthode pratique sur le fichier pour redimensionner les données de l’image dans le runtime JavaScript du navigateur avant que l’image ne soit diffusée dans l’application.

L’exemple suivant illustre le chargement de plusieurs fichiers image dans un composant. `InputFileChangeEventArgs.GetMultipleFiles` autorise la lecture de plusieurs fichiers. Spécifiez le nombre maximal de fichiers que vous prévoyez de lire pour empêcher un utilisateur malveillant de télécharger un plus grand nombre de fichiers que celle attendue par l’application. `InputFileChangeEventArgs.File` permet de lire le premier et le seul fichier uniquement si le téléchargement de fichier ne prend pas en charge plusieurs fichiers.

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

`IBrowserFile` retourne les métadonnées [exposées par le navigateur](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) en tant que propriétés. Ces métadonnées peuvent être utiles pour la validation préliminaire. Par exemple, consultez les [ `FileUpload.razor` `FilePreview.razor` exemples de composants et](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).

## <a name="file-streams"></a>Flux de fichiers

Dans une Blazor WebAssembly application, les données sont transmises directement dans le code .net au sein du navigateur.

Dans une Blazor Server application, les données de fichier sont diffusées en continu via la SignalR connexion dans le code .net sur le serveur lors de la lecture du fichier à partir du flux. [`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) autorise la configuration des caractéristiques de chargement de fichier pour Blazor Server .

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
