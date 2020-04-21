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
# <a name="upload-files-in-aspnet-core"></a>Télécharger des fichiers dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/) et [Rutger Storm](https://github.com/rutix)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core prend en charge le téléchargement d’un ou de plusieurs fichiers à l’aide de la liaison de modèle tampon pour les fichiers plus petits et du streaming inbuffered pour les fichiers plus grands.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Faites preuve de prudence lorsque vous offrez aux utilisateurs la possibilité de télécharger des fichiers sur un serveur. Les attaquants peuvent tenter de :

* Exécutez des attaques [par déni de service.](/windows-hardware/drivers/ifs/denial-of-service)
* Téléchargez des virus ou des logiciels malveillants.
* Compromettez les réseaux et les serveurs par d’autres moyens.

Les mesures de sécurité qui réduisent la probabilité d’une attaque réussie sont les suivantes :

* Téléchargez des fichiers dans une zone de téléchargement de fichiers dédiée, de préférence sur un lecteur non système. Un emplacement dédié facilite l’imposition de restrictions de sécurité sur les fichiers téléchargés. Désactiver les autorisations d’exécution sur l’emplacement de téléchargement de fichier.&dagger;
* Ne persistez **pas** les fichiers téléchargés dans le même arbre d’annuaire que l’application.&dagger;
* Utilisez un nom de fichier sécurisé déterminé par l’application. N’utilisez pas de nom de fichier fourni par l’utilisateur ou le nom de fichier non fiable du fichier téléchargé. &dagger; HTML code le nom de fichier non fiable lors de son affichage. Par exemple, l’enregistrement du nom du fichier ou l’affichage dans l’interface utilisateur (Razor code automatiquement LA sortie HTML).
* N’autorisez que les extensions de fichiers approuvées pour les spécifications de conception de l’application.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Vérifiez que des vérifications côté client sont effectuées sur le serveur. &dagger; Les contrôles côté client sont faciles à contourner.
* Vérifiez la taille d’un fichier téléchargé. Définissez une limite de taille maximale pour empêcher les gros téléchargements.&dagger;
* Lorsque les fichiers ne doivent pas être écrasés par un fichier téléchargé du même nom, vérifiez le nom du fichier contre la base de données ou le stockage physique avant de télécharger le fichier.
* **Exécutez un scanner virus/malware sur le contenu téléchargé avant que le fichier ne soit stocké.**

&dagger;L’application d’échantillon démontre une approche qui répond aux critères.

> [!WARNING]
> Le chargement d’un code malveillant sur un système est généralement la première étape de l’exécution de code capable de :
>
> * Prenez complètement le contrôle d’un système.
> * Surchargez un système avec le résultat que le système se bloque.
> * Compromettre les données utilisateur ou système.
> * Appliquer des graffitis à une interface utilisateur publique.
>
> Pour plus d’informations sur la réduction de la surface d’attaque quand vous acceptez des fichiers d’utilisateurs, consultez les ressources suivantes :
>
> * [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)
> * [Sécurité Azure : Vérifier que les contrôles appropriés sont en place quand vous acceptez des fichiers d’utilisateurs](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Pour plus d’informations sur la mise en œuvre des mesures de sécurité, y compris des exemples de l’application d’échantillon, consultez la section [Validation.](#validation)

## <a name="storage-scenarios"></a>Scénarios de stockage

Les options de stockage courantes pour les fichiers comprennent :

* Base de données

  * Pour les petits téléchargements de fichiers, une base de données est souvent plus rapide que les options de stockage physique (système de fichiers ou partage de réseau).
  * Une base de données est souvent plus pratique que les options de stockage physique, car la récupération d’un enregistrement de base de données pour les données utilisateur peut fournir simultanément le contenu du fichier (par exemple, une image avatar).
  * Une base de données est potentiellement moins coûteuse que l’utilisation d’un service de stockage de données.

* Stockage physique (système de fichiers ou partage réseau)

  * Pour les téléchargements de fichiers volumineux :
    * Les limites de base de données peuvent limiter la taille du téléchargement.
    * Le stockage physique est souvent moins économique que le stockage dans une base de données.
  * Le stockage physique est potentiellement moins coûteux que l’utilisation d’un service de stockage de données.
  * Le processus de l’application doit avoir lu et écrire des autorisations à l’emplacement de stockage. **N’accordez jamais la permission d’exécuter.**

* Service de stockage de données (par exemple, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * Les services offrent généralement une meilleure évolutivité et une facilité de résilience par endroits sur des solutions qui sont généralement sujettes à des points de défaillance uniques.
  * Les services sont potentiellement moins coûteux dans les grands scénarios d’infrastructure de stockage.

  Pour plus d’informations, voir [Quickstart: Use .NET pour créer un blob dans le stockage d’objets](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Scénarios de téléchargement de fichiers

Deux approches générales pour le téléchargement de fichiers sont la mise en mémoire tampon et le streaming.

**des réponses**

L’ensemble du fichier <xref:Microsoft.AspNetCore.Http.IFormFile>est lu dans un , qui est une représentation C du fichier utilisé pour traiter ou enregistrer le fichier.

Les ressources (disque, mémoire) utilisées par les téléchargements de fichiers dépendent du nombre et de la taille des téléchargements de fichiers simultanés. Si une application tente de tamponner trop de téléchargements, le site se bloque lorsqu’il est à court d’espace de mémoire ou de disque. Si la taille ou la fréquence des téléchargements de fichiers est épuisante ressources d’applications, utilisez le streaming.

> [!NOTE]
> Tout fichier tampon unique dépassant 64 KB est déplacé de la mémoire à un fichier d’ampoule sur le disque.

La mise en mémoire des petits fichiers est couverte dans les sections suivantes de ce sujet :

* [Stockage physique](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Sauvegarde de la base de données](#upload-small-files-with-buffered-model-binding-to-a-database)

**Diffusion en continu**

Le fichier est reçu à partir d’une demande multipartite et directement traité ou enregistré par l’application. Le streaming n’améliore pas les performances de manière significative. Le streaming réduit les demandes d’espace de mémoire ou de disque lors du téléchargement de fichiers.

Streaming fichiers volumineux est couvert dans les [fichiers Volumineux avec la](#upload-large-files-with-streaming) section streaming.

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Téléchargez de petits fichiers avec un modèle tampon liant au stockage physique

Pour télécharger de petits fichiers, utilisez un formulaire multipartite ou construisez une demande POST à l’aide de JavaScript.

L’exemple suivant montre l’utilisation d’un formulaire Razor Pages pour télécharger un seul fichier *(Pages/BufferedSingleFileUploadPhysical.cshtml* dans l’application d’échantillon) :

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

L’exemple suivant est analogue à l’exemple précédent, sauf que :

* JavaScript[(Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) est utilisé pour soumettre les données du formulaire.
* Il n’y a pas de validation.

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

Pour effectuer le formulaire POST dans JavaScript pour les clients qui [ne prennent pas en charge l’API Fetch](https://caniuse.com/#feat=fetch), utilisez l’une des approches suivantes :

* Utilisez un Fetch Polyfill (par exemple, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Utilisez `XMLHttpRequest`. Par exemple :

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

Afin de prendre en charge les téléchargements de`enctype`fichiers, `multipart/form-data`les formulaires HTML doivent spécifier un type d’encodage ( ) de .

Pour `files` un élément d’entrée à `multiple` l’appui `<input>` de télécharger plusieurs fichiers fournir l’attribut sur l’élément:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Les fichiers individuels téléchargés sur le serveur peuvent <xref:Microsoft.AspNetCore.Http.IFormFile>être consultés via La liaison de [modèle](xref:mvc/models/model-binding) utilisant . L’application d’échantillon démontre plusieurs téléchargements de fichiers tamponnés pour les scénarios de base de données et de stockage physique.

<a name="filename"></a>

> [!WARNING]
> **N’utilisez** `FileName` pas <xref:Microsoft.AspNetCore.Http.IFormFile> la propriété d’autres que pour l’affichage et l’enregistrement. Lors de l’affichage ou de la connexion, HTML code le nom du fichier. Un attaquant peut fournir un nom de fichier malveillant, y compris des chemins complets ou des chemins relatifs. Les demandes doivent :
>
> * Retirez le chemin du nom de fichier fourni par l’utilisateur.
> * Enregistrez le nom de fichier HTML-encoded, décrypté par voie pour l’interface utilisateur ou l’enregistrement.
> * Générer un nouveau nom de fichier aléatoire pour le stockage.
>
> Le code suivant supprime le chemin du nom du fichier :
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Les exemples fournis jusqu’à présent ne tiennent pas compte des considérations de sécurité. Des informations supplémentaires sont fournies par les sections suivantes et [l’application d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Considérations relatives à la sécurité](#security-considerations)
> * [Validation](#validation)

Lors du téléchargement de <xref:Microsoft.AspNetCore.Http.IFormFile>fichiers à l’aide de la liaison du modèle et , la méthode d’action peut accepter:

* Un <xref:Microsoft.AspNetCore.Http.IFormFile>single .
* N’importe laquelle des collections suivantes qui représentent plusieurs fichiers :
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Liste](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Correspondances contraignantes formulaire fichiers par nom. Par exemple, `name` la `<input type="file" name="formFile">` valeur HTML doit correspondre au`FormFile`paramètre/propriété CMD lié (). Pour plus d’informations, voir la [valeur d’attribut de nom De match au nom de paramètre de la section méthode POST.](#match-name-attribute-value-to-parameter-name-of-post-method)

L’exemple suivant :

* Boucles à travers un ou plusieurs fichiers téléchargés.
* Utilise [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour retourner un chemin complet pour un fichier, y compris le nom du fichier. 
* Enregistre les fichiers au système de fichiers local à l’aide d’un nom de fichier généré par l’application.
* Retourne le nombre total et la taille des fichiers téléchargés.

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

Utilisez `Path.GetRandomFileName` pour générer un nom de fichier sans chemin. Dans l’exemple suivant, le chemin est obtenu à partir de la configuration :

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

Le chemin passé <xref:System.IO.FileStream> au *doit* inclure le nom du fichier. Si le nom du fichier <xref:System.UnauthorizedAccessException> n’est pas fourni, un est jeté à l’heure de l’exécution.

Les fichiers téléchargés à l’aide de la <xref:Microsoft.AspNetCore.Http.IFormFile> technique sont tamponnés en mémoire ou sur disque sur le serveur avant le traitement. À l’intérieur <xref:Microsoft.AspNetCore.Http.IFormFile> de la méthode <xref:System.IO.Stream>d’action, le contenu est accessible en tant que . En plus du système de fichiers local, les fichiers peuvent être enregistrés sur une part de réseau ou sur un service de stockage de fichiers, comme [le stockage Azure Blob](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Pour un autre exemple qui boucle sur plusieurs fichiers pour télécharger et utilise des noms de fichiers sûrs, voir *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* dans l’application de l’échantillon.

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) lance <xref:System.IO.IOException> un si plus de 65 535 fichiers sont créés sans supprimer les fichiers temporaires précédents. La limite de 65 535 fichiers est une limite par serveur. Pour plus d’informations sur cette limite sur Windows OS, voir les remarques dans les sujets suivants:
>
> * [Fonction GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Téléchargez de petits fichiers avec une liaison de modèle tampon dans une base de données

Pour stocker les données de fichiers binaires <xref:System.Byte> dans une base de données à l’aide [d’Entity Framework](/ef/core/index), définissez une propriété de tableau sur l’entité :

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Spécifier un modèle de <xref:Microsoft.AspNetCore.Http.IFormFile>page pour la classe qui comprend un :

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
> <xref:Microsoft.AspNetCore.Http.IFormFile>peut être utilisé directement comme paramètre de méthode d’action ou comme propriété modèle liée. L’exemple précédent utilise une propriété modèle liée.

Le `FileUpload` est utilisé dans le formulaire Razor Pages:

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

Lorsque le formulaire est POSTed sur <xref:Microsoft.AspNetCore.Http.IFormFile> le serveur, copiez-le sur un flux et enregistrez-le comme un tableau d’endlète dans la base de données. Dans l’exemple `_dbContext` suivant, stocke le contexte de la base de données de l’application :

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

L’exemple précédent est similaire à un scénario démontré dans l’application de l’échantillon :

* *Pages/BufferedSingleFileUploadDb.cshtml*
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Soyez prudent quand vous stockez des données binaires dans des bases de données relationnelles, car cela peut avoir un impact négatif sur les performances.
>
> Ne vous fiez pas `FileName` à <xref:Microsoft.AspNetCore.Http.IFormFile> la propriété de sans validation ou ne faites pas confiance. La `FileName` propriété ne doit être utilisée qu’à des fins d’affichage et seulement après le codage HTML.
>
> Les exemples fournis ne tiennent pas compte des considérations de sécurité. Des informations supplémentaires sont fournies par les sections suivantes et [l’application d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Considérations relatives à la sécurité](#security-considerations)
> * [Validation](#validation)

### <a name="upload-large-files-with-streaming"></a>Téléchargez de gros fichiers avec diffusion en continu

L’exemple suivant montre comment utiliser JavaScript pour diffuser un fichier sur une action de contrôleur. Le jeton antiforgery du fichier est généré à l’aide d’un attribut de filtre personnalisé et transmis au client EN-têtes HTTP au lieu de dans le corps de demande. Étant donné que la méthode d’action traite directement les données téléchargées, la liaison du modèle de formulaire est désactivée par un autre filtre personnalisé. Dans l’action, le contenu du formulaire est lu en avec `MultipartReader`, qui lit chaque `MultipartSection` individuelle, traitant le fichier ou enregistrant le contenu selon ce qui est approprié. Une fois les sections multipartites lues, l’action exécute sa propre liaison de modèle.

La réponse initiale de la page charge le formulaire et enregistre un `GenerateAntiforgeryTokenCookieAttribute` jeton antiforgery dans un cookie (via l’attribut). L’attribut utilise ASP.NET [support antiforgery](xref:security/anti-request-forgery) intégré de Core pour définir un cookie avec un jeton de demande :

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Le `DisableFormValueModelBindingAttribute` est utilisé pour désactiver la liaison de modèle :

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

Dans l’application `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` d’échantillon, et sont appliqués `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` comme `Startup.ConfigureServices` filtres aux modèles d’application de page de et en utilisant les [conventions de Pages Razor](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

Étant donné que la liaison du modèle ne lit pas le formulaire, les paramètres qui sont liés à partir de la forme ne se lient pas (requête, itinéraire et en-tête continuent à fonctionner). La méthode d’action `Request` fonctionne directement avec la propriété. Un `MultipartReader` est utilisé pour lire chaque section. Les données de clé/valeur sont stockées dans un `KeyValueAccumulator`. Une fois que les sections multipartites sont lues, le contenu de la `KeyValueAccumulator` sont utilisés pour lier les données de formulaire à un type de modèle.

La `StreamingController.UploadDatabase` méthode complète pour le streaming vers une base de données avec EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Services publics/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

La `StreamingController.UploadPhysical` méthode complète pour le streaming à un emplacement physique:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

Dans l’application d’échantillon, `FileHelpers.ProcessStreamedFile`les contrôles de validation sont gérés par .

## <a name="validation"></a>Validation

La classe de `FileHelpers` l’application de l’échantillon montre plusieurs contrôles pour les téléchargements de fichiers tamponnés <xref:Microsoft.AspNetCore.Http.IFormFile> et en streaming. Pour <xref:Microsoft.AspNetCore.Http.IFormFile> le traitement des téléchargements de fichiers `ProcessFormFile` tamponnés dans l’application de l’échantillon, voir la méthode dans le fichier *Utilities/FileHelpers.cs.* Pour le traitement des `ProcessStreamedFile` fichiers en streaming, voir la méthode dans le même fichier.

> [!WARNING]
> Les méthodes de traitement de validation démontrées dans l’application de l’échantillon ne scannent pas le contenu des fichiers téléchargés. Dans la plupart des scénarios de production, une API de scanner de virus/malware est utilisée dans le fichier avant de mettre le fichier à la disposition des utilisateurs ou d’autres systèmes.
>
> Bien que l’échantillon de sujet fournisse un `FileHelpers` exemple de travail des techniques de validation, ne mettez pas en œuvre la classe dans une application de production à moins que vous ne :
>
> * Comprenez parfaitement la mise en œuvre.
> * Modifier la mise en œuvre le cas échéant pour l’environnement et les spécifications de l’application.
>
> **Ne jamais implémenter sans discernement le code de sécurité dans une application sans répondre à ces exigences.**

### <a name="content-validation"></a>Validation du contenu

**Utilisez un virus/malware tiers scannant l’API sur le contenu téléchargé.**

La numérisation des fichiers est exigeante sur les ressources du serveur dans des scénarios à volume élevé. Si les performances de traitement des demandes sont diminuées en raison de la numérisation des fichiers, envisagez de décharger le travail de numérisation à un [service d’arrière-plan,](xref:fundamentals/host/hosted-services)peut-être un service fonctionnant sur un serveur différent du serveur de l’application. En règle générale, les fichiers téléchargés sont conservés dans une zone de quarantaine jusqu’à ce que le scanner de virus d’arrière-plan les vérifie. Lorsqu’un fichier passe, le fichier est déplacé vers l’emplacement normal de stockage de fichiers. Ces étapes sont généralement effectuées en conjonction avec un enregistrement de base de données qui indique l’état de numérisation d’un fichier. En utilisant une telle approche, l’application et le serveur d’applications restent concentrés sur la réponse aux demandes.

### <a name="file-extension-validation"></a>Validation de la prolongation de fichier

L’extension du fichier téléchargé doit être vérifiée sur une liste d’extensions autorisées. Par exemple :

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validation de la signature du fichier

La signature d’un fichier est déterminée par les premiers octets au début d’un fichier. Ces octets peuvent être utilisés pour indiquer si l’extension correspond au contenu du fichier. L’application d’échantillon vérifie les signatures de fichiers pour quelques types de fichiers communs. Dans l’exemple suivant, la signature du fichier pour une image JPEG est vérifiée par rapport au fichier :

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

Pour obtenir des signatures de fichiers supplémentaires, consultez la [base de données des signatures de fichiers](https://www.filesignatures.net/) et les spécifications officielles des fichiers.

### <a name="file-name-security"></a>Sécurité du nom de fichier

N’utilisez jamais un nom de fichier fourni par le client pour enregistrer un fichier au stockage physique. Créez un nom de fichier sûr pour le fichier à l’aide [de Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour créer un chemin complet (y compris le nom de fichier) pour le stockage temporaire.

Razor code automatiquement les valeurs des propriétés pour l’affichage. Le code suivant est sûr à utiliser :

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

En dehors de <xref:System.Net.WebUtility.HtmlEncode*> Razor, toujours fichier le contenu du nom à partir de la demande d’un utilisateur.

De nombreuses implémentations doivent inclure une vérification de l’existence du fichier; autrement, le fichier est écrasé par un fichier du même nom. Fournissez une logique supplémentaire pour répondre aux spécifications de votre application.

### <a name="size-validation"></a>Validation de taille

Limitez la taille des fichiers téléchargés.

Dans l’application échantillon, la taille du fichier est limitée à 2 Mo (indiqué dans les octets). La limite est fournie via [Configuration](xref:fundamentals/configuration/index) à partir du fichier *appsettings.json* :

```json
{
  "FileSizeLimit": 2097152
}
```

Le `FileSizeLimit` est `PageModel` injecté dans les classes:

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

Lorsqu’une taille de fichier dépasse la limite, le fichier est rejeté :

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Valeur d’attribut de nom de match au nom de paramètre de la méthode POST

Dans les formulaires non-Razor que POST `FormData` forme des données ou utilisent directement `FormData` JavaScript, le nom spécifié dans l’élément du formulaire ou doit correspondre au nom du paramètre dans l’action du contrôleur.

Dans l’exemple suivant :

* Lors de `<input>` l’utilisation d’un élément, l’attribut `name` est défini à la valeur `battlePlans`:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Lors `FormData` de l’utilisation dans JavaScript, `battlePlans`le nom est réglé à la valeur :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Utilisez un nom correspondant pour le paramètre de la méthode C (`battlePlans`(

* Pour une méthode de `Upload`gestionnaire de page Razor Pages nommée :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Pour une méthode d’action de contrôleur MVC POST :

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Configuration du serveur et de l’application

### <a name="multipart-body-length-limit"></a>Limite de longueur du corps multipartite

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>fixe la limite pour la longueur de chaque corps multipartite. Les sections de formulaire <xref:System.IO.InvalidDataException> qui dépassent cette limite jettent une lorsqu’elles sont analysées. La valeur par défaut est de 134 217 728 (128 Mo). Personnalisez la <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> limite `Startup.ConfigureServices`en utilisant le paramètre dans :

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

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>est utilisé pour <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> définir la pour une seule page ou une action.

Dans une application Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) appliquez `Startup.ConfigureServices`le filtre avec une convention dans :

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

Dans une application Razor Pages ou une application MVC, appliquez le filtre sur le modèle de page ou la méthode d’action :

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel maximum demande la taille du corps

Pour les applications hébergées par Kestrel, la taille maximale de la demande par défaut est de 30 000 000 octets, soit environ 28,6 Mo. Personnalisez la limite à l’aide de l’option serveur [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel :

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

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>est utilisé pour définir le [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pour une seule page ou une action.

Dans une application Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) appliquez `Startup.ConfigureServices`le filtre avec une convention dans :

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

Dans une application De pages Razor ou une application MVC, appliquez le filtre sur la classe ou la méthode d’action du gestionnaire de page :

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

La `RequestSizeLimitAttribute` directive Razor peut [`@attribute`](xref:mvc/views/razor#attribute) également être appliquée à l’aide de la directive Razor :

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Autres limites de Kestrel

D’autres limites de Kestrel peuvent s’appliquer pour les applications hébergées par Kestrel :

* [Nombre maximale de connexions client](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Taux de données de demande et de réponse](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>Limite de longueur de contenu IIS

La limite de`maxAllowedContentLength`demande par défaut ( ) est de 30 000 000 octets, soit environ 28,6 Mo. Personnalisez la limite dans le fichier *web.config* :

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

Ce paramètre s’applique seulement à IIS. Par défaut, ce comportement ne se produit pas dans le cas d’un hébergement sur Kestrel. Pour plus d’informations, voir [demande limites \<de demandeLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Les limites du module de base ASP.NET ou la présence du module de filtrage de demande DE l’IIS peuvent limiter les téléchargements à 2 ou 4 Go. Pour plus d’informations, voir [Unable pour télécharger le fichier de plus de 2 Go de taille (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Dépanner

Voici certains problèmes courants rencontrés avec le chargement de fichiers et leurs solutions possibles.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Erreur non trouvée lorsqu’elle est déployée sur un serveur IIS

L’erreur suivante indique que le fichier téléchargé dépasse la longueur de contenu configurée du serveur :

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Pour plus d’informations sur l’augmentation de la limite, consultez la section [limite de durée du contenu DE l’IIS.](#iis-content-length-limit)

### <a name="connection-failure"></a>Échec de connexion

Une erreur de connexion et une connexion de serveur de réinitialisation indiquent probablement que le fichier téléchargé dépasse la taille maximale du corps de demande de Kestrel. Pour plus d’informations, consultez la section taille maximale de [la carrosserie de demande de Kestrel.](#kestrel-maximum-request-body-size) Les limites de connexion client de Kestrel peuvent également nécessiter un ajustement.

### <a name="null-reference-exception-with-iformfile"></a>Exception de référence null avec IFormFile

Si le contrôleur accepte les <xref:Microsoft.AspNetCore.Http.IFormFile> fichiers téléchargés `null`à l’aide, mais `enctype` la `multipart/form-data`valeur est , confirmer que le formulaire HTML spécifie une valeur de . Si cet attribut n’est `<form>` pas défini sur l’élément, <xref:Microsoft.AspNetCore.Http.IFormFile> le `null`téléchargement de fichier ne se produit pas et tous les arguments liés sont . Confirmez également que le [nom de téléchargement dans les données de forme correspond au nom de l’application](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Stream était trop long

Les exemples dans <xref:System.IO.MemoryStream> ce sujet s’appuient sur la tenue du contenu du fichier téléchargé. La limite de `MemoryStream` `int.MaxValue`taille d’un est . Si le scénario de téléchargement de fichiers de l’application nécessite la détention du contenu du `MemoryStream` fichier supérieur à 50 Mo, utilisez une approche alternative qui ne repose pas sur un seul pour la tenue du contenu d’un fichier téléchargé.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core prend en charge le téléchargement d’un ou de plusieurs fichiers à l’aide de la liaison de modèle tampon pour les fichiers plus petits et du streaming inbuffered pour les fichiers plus grands.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Faites preuve de prudence lorsque vous offrez aux utilisateurs la possibilité de télécharger des fichiers sur un serveur. Les attaquants peuvent tenter de :

* Exécutez des attaques [par déni de service.](/windows-hardware/drivers/ifs/denial-of-service)
* Téléchargez des virus ou des logiciels malveillants.
* Compromettez les réseaux et les serveurs par d’autres moyens.

Les mesures de sécurité qui réduisent la probabilité d’une attaque réussie sont les suivantes :

* Téléchargez des fichiers dans une zone de téléchargement de fichiers dédiée, de préférence sur un lecteur non système. Un emplacement dédié facilite l’imposition de restrictions de sécurité sur les fichiers téléchargés. Désactiver les autorisations d’exécution sur l’emplacement de téléchargement de fichier.&dagger;
* Ne persistez **pas** les fichiers téléchargés dans le même arbre d’annuaire que l’application.&dagger;
* Utilisez un nom de fichier sécurisé déterminé par l’application. N’utilisez pas de nom de fichier fourni par l’utilisateur ou le nom de fichier non fiable du fichier téléchargé. &dagger; HTML code le nom de fichier non fiable lors de son affichage. Par exemple, l’enregistrement du nom du fichier ou l’affichage dans l’interface utilisateur (Razor code automatiquement LA sortie HTML).
* N’autorisez que les extensions de fichiers approuvées pour les spécifications de conception de l’application.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Vérifiez que des vérifications côté client sont effectuées sur le serveur. &dagger; Les contrôles côté client sont faciles à contourner.
* Vérifiez la taille d’un fichier téléchargé. Définissez une limite de taille maximale pour empêcher les gros téléchargements.&dagger;
* Lorsque les fichiers ne doivent pas être écrasés par un fichier téléchargé du même nom, vérifiez le nom du fichier contre la base de données ou le stockage physique avant de télécharger le fichier.
* **Exécutez un scanner virus/malware sur le contenu téléchargé avant que le fichier ne soit stocké.**

&dagger;L’application d’échantillon démontre une approche qui répond aux critères.

> [!WARNING]
> Le chargement d’un code malveillant sur un système est généralement la première étape de l’exécution de code capable de :
>
> * Prenez complètement le contrôle d’un système.
> * Surchargez un système avec le résultat que le système se bloque.
> * Compromettre les données utilisateur ou système.
> * Appliquer des graffitis à une interface utilisateur publique.
>
> Pour plus d’informations sur la réduction de la surface d’attaque quand vous acceptez des fichiers d’utilisateurs, consultez les ressources suivantes :
>
> * [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)
> * [Sécurité Azure : Vérifier que les contrôles appropriés sont en place quand vous acceptez des fichiers d’utilisateurs](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Pour plus d’informations sur la mise en œuvre des mesures de sécurité, y compris des exemples de l’application d’échantillon, consultez la section [Validation.](#validation)

## <a name="storage-scenarios"></a>Scénarios de stockage

Les options de stockage courantes pour les fichiers comprennent :

* Base de données

  * Pour les petits téléchargements de fichiers, une base de données est souvent plus rapide que les options de stockage physique (système de fichiers ou partage de réseau).
  * Une base de données est souvent plus pratique que les options de stockage physique, car la récupération d’un enregistrement de base de données pour les données utilisateur peut fournir simultanément le contenu du fichier (par exemple, une image avatar).
  * Une base de données est potentiellement moins coûteuse que l’utilisation d’un service de stockage de données.

* Stockage physique (système de fichiers ou partage réseau)

  * Pour les téléchargements de fichiers volumineux :
    * Les limites de base de données peuvent limiter la taille du téléchargement.
    * Le stockage physique est souvent moins économique que le stockage dans une base de données.
  * Le stockage physique est potentiellement moins coûteux que l’utilisation d’un service de stockage de données.
  * Le processus de l’application doit avoir lu et écrire des autorisations à l’emplacement de stockage. **N’accordez jamais la permission d’exécuter.**

* Service de stockage de données (par exemple, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * Les services offrent généralement une meilleure évolutivité et une facilité de résilience par endroits sur des solutions qui sont généralement sujettes à des points de défaillance uniques.
  * Les services sont potentiellement moins coûteux dans les grands scénarios d’infrastructure de stockage.

  Pour plus d’informations, voir [Quickstart: Use .NET pour créer un blob dans le stockage d’objets](/azure/storage/blobs/storage-quickstart-blobs-dotnet). Le sujet <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>démontre <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> , mais peut <xref:System.IO.FileStream> être utilisé pour enregistrer <xref:System.IO.Stream>un stockage à blob lors du travail avec un .

## <a name="file-upload-scenarios"></a>Scénarios de téléchargement de fichiers

Deux approches générales pour le téléchargement de fichiers sont la mise en mémoire tampon et le streaming.

**des réponses**

L’ensemble du fichier <xref:Microsoft.AspNetCore.Http.IFormFile>est lu dans un , qui est une représentation C du fichier utilisé pour traiter ou enregistrer le fichier.

Les ressources (disque, mémoire) utilisées par les téléchargements de fichiers dépendent du nombre et de la taille des téléchargements de fichiers simultanés. Si une application tente de tamponner trop de téléchargements, le site se bloque lorsqu’il est à court d’espace de mémoire ou de disque. Si la taille ou la fréquence des téléchargements de fichiers est épuisante ressources d’applications, utilisez le streaming.

> [!NOTE]
> Tout fichier tampon unique dépassant 64 KB est déplacé de la mémoire à un fichier d’ampoule sur le disque.

La mise en mémoire des petits fichiers est couverte dans les sections suivantes de ce sujet :

* [Stockage physique](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Sauvegarde de la base de données](#upload-small-files-with-buffered-model-binding-to-a-database)

**Diffusion en continu**

Le fichier est reçu à partir d’une demande multipartite et directement traité ou enregistré par l’application. Le streaming n’améliore pas les performances de manière significative. Le streaming réduit les demandes d’espace de mémoire ou de disque lors du téléchargement de fichiers.

Streaming fichiers volumineux est couvert dans les [fichiers Volumineux avec la](#upload-large-files-with-streaming) section streaming.

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Téléchargez de petits fichiers avec un modèle tampon liant au stockage physique

Pour télécharger de petits fichiers, utilisez un formulaire multipartite ou construisez une demande POST à l’aide de JavaScript.

L’exemple suivant montre l’utilisation d’un formulaire Razor Pages pour télécharger un seul fichier *(Pages/BufferedSingleFileUploadPhysical.cshtml* dans l’application d’échantillon) :

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

L’exemple suivant est analogue à l’exemple précédent, sauf que :

* JavaScript[(Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) est utilisé pour soumettre les données du formulaire.
* Il n’y a pas de validation.

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

Pour effectuer le formulaire POST dans JavaScript pour les clients qui [ne prennent pas en charge l’API Fetch](https://caniuse.com/#feat=fetch), utilisez l’une des approches suivantes :

* Utilisez un Fetch Polyfill (par exemple, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Utilisez `XMLHttpRequest`. Par exemple :

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

Afin de prendre en charge les téléchargements de`enctype`fichiers, `multipart/form-data`les formulaires HTML doivent spécifier un type d’encodage ( ) de .

Pour `files` un élément d’entrée à `multiple` l’appui `<input>` de télécharger plusieurs fichiers fournir l’attribut sur l’élément:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Les fichiers individuels téléchargés sur le serveur peuvent <xref:Microsoft.AspNetCore.Http.IFormFile>être consultés via La liaison de [modèle](xref:mvc/models/model-binding) utilisant . L’application d’échantillon démontre plusieurs téléchargements de fichiers tamponnés pour les scénarios de base de données et de stockage physique.

<a name="filename2"></a>

> [!WARNING]
> **N’utilisez** `FileName` pas <xref:Microsoft.AspNetCore.Http.IFormFile> la propriété d’autres que pour l’affichage et l’enregistrement. Lors de l’affichage ou de la connexion, HTML code le nom du fichier. Un attaquant peut fournir un nom de fichier malveillant, y compris des chemins complets ou des chemins relatifs. Les demandes doivent :
>
> * Retirez le chemin du nom de fichier fourni par l’utilisateur.
> * Enregistrez le nom de fichier HTML-encoded, décrypté par voie pour l’interface utilisateur ou l’enregistrement.
> * Générer un nouveau nom de fichier aléatoire pour le stockage.
>
> Le code suivant supprime le chemin du nom du fichier :
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Les exemples fournis jusqu’à présent ne tiennent pas compte des considérations de sécurité. Des informations supplémentaires sont fournies par les sections suivantes et [l’application d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Considérations relatives à la sécurité](#security-considerations)
> * [Validation](#validation)

Lors du téléchargement de <xref:Microsoft.AspNetCore.Http.IFormFile>fichiers à l’aide de la liaison du modèle et , la méthode d’action peut accepter:

* Un <xref:Microsoft.AspNetCore.Http.IFormFile>single .
* N’importe laquelle des collections suivantes qui représentent plusieurs fichiers :
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Liste](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Correspondances contraignantes formulaire fichiers par nom. Par exemple, `name` la `<input type="file" name="formFile">` valeur HTML doit correspondre au`FormFile`paramètre/propriété CMD lié (). Pour plus d’informations, voir la [valeur d’attribut de nom De match au nom de paramètre de la section méthode POST.](#match-name-attribute-value-to-parameter-name-of-post-method)

L’exemple suivant :

* Boucles à travers un ou plusieurs fichiers téléchargés.
* Utilise [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour retourner un chemin complet pour un fichier, y compris le nom du fichier. 
* Enregistre les fichiers au système de fichiers local à l’aide d’un nom de fichier généré par l’application.
* Retourne le nombre total et la taille des fichiers téléchargés.

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

Utilisez `Path.GetRandomFileName` pour générer un nom de fichier sans chemin. Dans l’exemple suivant, le chemin est obtenu à partir de la configuration :

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

Le chemin passé <xref:System.IO.FileStream> au *doit* inclure le nom du fichier. Si le nom du fichier <xref:System.UnauthorizedAccessException> n’est pas fourni, un est jeté à l’heure de l’exécution.

Les fichiers téléchargés à l’aide de la <xref:Microsoft.AspNetCore.Http.IFormFile> technique sont tamponnés en mémoire ou sur disque sur le serveur avant le traitement. À l’intérieur <xref:Microsoft.AspNetCore.Http.IFormFile> de la méthode <xref:System.IO.Stream>d’action, le contenu est accessible en tant que . En plus du système de fichiers local, les fichiers peuvent être enregistrés sur une part de réseau ou sur un service de stockage de fichiers, comme [le stockage Azure Blob](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Pour un autre exemple qui boucle sur plusieurs fichiers pour télécharger et utilise des noms de fichiers sûrs, voir *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* dans l’application de l’échantillon.

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) lance <xref:System.IO.IOException> un si plus de 65 535 fichiers sont créés sans supprimer les fichiers temporaires précédents. La limite de 65 535 fichiers est une limite par serveur. Pour plus d’informations sur cette limite sur Windows OS, voir les remarques dans les sujets suivants:
>
> * [Fonction GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Téléchargez de petits fichiers avec une liaison de modèle tampon dans une base de données

Pour stocker les données de fichiers binaires <xref:System.Byte> dans une base de données à l’aide [d’Entity Framework](/ef/core/index), définissez une propriété de tableau sur l’entité :

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Spécifier un modèle de <xref:Microsoft.AspNetCore.Http.IFormFile>page pour la classe qui comprend un :

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
> <xref:Microsoft.AspNetCore.Http.IFormFile>peut être utilisé directement comme paramètre de méthode d’action ou comme propriété modèle liée. L’exemple précédent utilise une propriété modèle liée.

Le `FileUpload` est utilisé dans le formulaire Razor Pages:

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

Lorsque le formulaire est POSTed sur <xref:Microsoft.AspNetCore.Http.IFormFile> le serveur, copiez-le sur un flux et enregistrez-le comme un tableau d’endlète dans la base de données. Dans l’exemple `_dbContext` suivant, stocke le contexte de la base de données de l’application :

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

L’exemple précédent est similaire à un scénario démontré dans l’application de l’échantillon :

* *Pages/BufferedSingleFileUploadDb.cshtml*
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Soyez prudent quand vous stockez des données binaires dans des bases de données relationnelles, car cela peut avoir un impact négatif sur les performances.
>
> Ne vous fiez pas `FileName` à <xref:Microsoft.AspNetCore.Http.IFormFile> la propriété de sans validation ou ne faites pas confiance. La `FileName` propriété ne doit être utilisée qu’à des fins d’affichage et seulement après le codage HTML.
>
> Les exemples fournis ne tiennent pas compte des considérations de sécurité. Des informations supplémentaires sont fournies par les sections suivantes et [l’application d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Considérations relatives à la sécurité](#security-considerations)
> * [Validation](#validation)

### <a name="upload-large-files-with-streaming"></a>Téléchargez de gros fichiers avec diffusion en continu

L’exemple suivant montre comment utiliser JavaScript pour diffuser un fichier sur une action de contrôleur. Le jeton antiforgery du fichier est généré à l’aide d’un attribut de filtre personnalisé et transmis au client EN-têtes HTTP au lieu de dans le corps de demande. Étant donné que la méthode d’action traite directement les données téléchargées, la liaison du modèle de formulaire est désactivée par un autre filtre personnalisé. Dans l’action, le contenu du formulaire est lu en avec `MultipartReader`, qui lit chaque `MultipartSection` individuelle, traitant le fichier ou enregistrant le contenu selon ce qui est approprié. Une fois les sections multipartites lues, l’action exécute sa propre liaison de modèle.

La réponse initiale de la page charge le formulaire et enregistre un `GenerateAntiforgeryTokenCookieAttribute` jeton antiforgery dans un cookie (via l’attribut). L’attribut utilise ASP.NET [support antiforgery](xref:security/anti-request-forgery) intégré de Core pour définir un cookie avec un jeton de demande :

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Le `DisableFormValueModelBindingAttribute` est utilisé pour désactiver la liaison de modèle :

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

Dans l’application `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` d’échantillon, et sont appliqués `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` comme `Startup.ConfigureServices` filtres aux modèles d’application de page de et en utilisant les [conventions de Pages Razor](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Étant donné que la liaison du modèle ne lit pas le formulaire, les paramètres qui sont liés à partir de la forme ne se lient pas (requête, itinéraire et en-tête continuent à fonctionner). La méthode d’action `Request` fonctionne directement avec la propriété. Un `MultipartReader` est utilisé pour lire chaque section. Les données de clé/valeur sont stockées dans un `KeyValueAccumulator`. Une fois que les sections multipartites sont lues, le contenu de la `KeyValueAccumulator` sont utilisés pour lier les données de formulaire à un type de modèle.

La `StreamingController.UploadDatabase` méthode complète pour le streaming vers une base de données avec EF Core:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Services publics/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

La `StreamingController.UploadPhysical` méthode complète pour le streaming à un emplacement physique:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

Dans l’application d’échantillon, `FileHelpers.ProcessStreamedFile`les contrôles de validation sont gérés par .

## <a name="validation"></a>Validation

La classe de `FileHelpers` l’application de l’échantillon montre plusieurs contrôles pour les téléchargements de fichiers tamponnés <xref:Microsoft.AspNetCore.Http.IFormFile> et en streaming. Pour <xref:Microsoft.AspNetCore.Http.IFormFile> le traitement des téléchargements de fichiers `ProcessFormFile` tamponnés dans l’application de l’échantillon, voir la méthode dans le fichier *Utilities/FileHelpers.cs.* Pour le traitement des `ProcessStreamedFile` fichiers en streaming, voir la méthode dans le même fichier.

> [!WARNING]
> Les méthodes de traitement de validation démontrées dans l’application de l’échantillon ne scannent pas le contenu des fichiers téléchargés. Dans la plupart des scénarios de production, une API de scanner de virus/malware est utilisée dans le fichier avant de mettre le fichier à la disposition des utilisateurs ou d’autres systèmes.
>
> Bien que l’échantillon de sujet fournisse un `FileHelpers` exemple de travail des techniques de validation, ne mettez pas en œuvre la classe dans une application de production à moins que vous ne :
>
> * Comprenez parfaitement la mise en œuvre.
> * Modifier la mise en œuvre le cas échéant pour l’environnement et les spécifications de l’application.
>
> **Ne jamais implémenter sans discernement le code de sécurité dans une application sans répondre à ces exigences.**

### <a name="content-validation"></a>Validation du contenu

**Utilisez un virus/malware tiers scannant l’API sur le contenu téléchargé.**

La numérisation des fichiers est exigeante sur les ressources du serveur dans des scénarios à volume élevé. Si les performances de traitement des demandes sont diminuées en raison de la numérisation des fichiers, envisagez de décharger le travail de numérisation à un [service d’arrière-plan,](xref:fundamentals/host/hosted-services)peut-être un service fonctionnant sur un serveur différent du serveur de l’application. En règle générale, les fichiers téléchargés sont conservés dans une zone de quarantaine jusqu’à ce que le scanner de virus d’arrière-plan les vérifie. Lorsqu’un fichier passe, le fichier est déplacé vers l’emplacement normal de stockage de fichiers. Ces étapes sont généralement effectuées en conjonction avec un enregistrement de base de données qui indique l’état de numérisation d’un fichier. En utilisant une telle approche, l’application et le serveur d’applications restent concentrés sur la réponse aux demandes.

### <a name="file-extension-validation"></a>Validation de la prolongation de fichier

L’extension du fichier téléchargé doit être vérifiée sur une liste d’extensions autorisées. Par exemple :

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validation de la signature du fichier

La signature d’un fichier est déterminée par les premiers octets au début d’un fichier. Ces octets peuvent être utilisés pour indiquer si l’extension correspond au contenu du fichier. L’application d’échantillon vérifie les signatures de fichiers pour quelques types de fichiers communs. Dans l’exemple suivant, la signature du fichier pour une image JPEG est vérifiée par rapport au fichier :

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

Pour obtenir des signatures de fichiers supplémentaires, consultez la [base de données des signatures de fichiers](https://www.filesignatures.net/) et les spécifications officielles des fichiers.

### <a name="file-name-security"></a>Sécurité du nom de fichier

N’utilisez jamais un nom de fichier fourni par le client pour enregistrer un fichier au stockage physique. Créez un nom de fichier sûr pour le fichier à l’aide [de Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) pour créer un chemin complet (y compris le nom de fichier) pour le stockage temporaire.

Razor code automatiquement les valeurs des propriétés pour l’affichage. Le code suivant est sûr à utiliser :

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

En dehors de <xref:System.Net.WebUtility.HtmlEncode*> Razor, toujours fichier le contenu du nom à partir de la demande d’un utilisateur.

De nombreuses implémentations doivent inclure une vérification de l’existence du fichier; autrement, le fichier est écrasé par un fichier du même nom. Fournissez une logique supplémentaire pour répondre aux spécifications de votre application.

### <a name="size-validation"></a>Validation de taille

Limitez la taille des fichiers téléchargés.

Dans l’application échantillon, la taille du fichier est limitée à 2 Mo (indiqué dans les octets). La limite est fournie via [Configuration](xref:fundamentals/configuration/index) à partir du fichier *appsettings.json* :

```json
{
  "FileSizeLimit": 2097152
}
```

Le `FileSizeLimit` est `PageModel` injecté dans les classes:

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

Lorsqu’une taille de fichier dépasse la limite, le fichier est rejeté :

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Valeur d’attribut de nom de match au nom de paramètre de la méthode POST

Dans les formulaires non-Razor que POST `FormData` forme des données ou utilisent directement `FormData` JavaScript, le nom spécifié dans l’élément du formulaire ou doit correspondre au nom du paramètre dans l’action du contrôleur.

Dans l’exemple suivant :

* Lors de `<input>` l’utilisation d’un élément, l’attribut `name` est défini à la valeur `battlePlans`:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Lors `FormData` de l’utilisation dans JavaScript, `battlePlans`le nom est réglé à la valeur :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Utilisez un nom correspondant pour le paramètre de la méthode C (`battlePlans`(

* Pour une méthode de `Upload`gestionnaire de page Razor Pages nommée :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Pour une méthode d’action de contrôleur MVC POST :

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Configuration du serveur et de l’application

### <a name="multipart-body-length-limit"></a>Limite de longueur du corps multipartite

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>fixe la limite pour la longueur de chaque corps multipartite. Les sections de formulaire <xref:System.IO.InvalidDataException> qui dépassent cette limite jettent une lorsqu’elles sont analysées. La valeur par défaut est de 134 217 728 (128 Mo). Personnalisez la <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> limite `Startup.ConfigureServices`en utilisant le paramètre dans :

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

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>est utilisé pour <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> définir la pour une seule page ou une action.

Dans une application Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) appliquez `Startup.ConfigureServices`le filtre avec une convention dans :

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

Dans une application Razor Pages ou une application MVC, appliquez le filtre sur le modèle de page ou la méthode d’action :

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel maximum demande la taille du corps

Pour les applications hébergées par Kestrel, la taille maximale de la demande par défaut est de 30 000 000 octets, soit environ 28,6 Mo. Personnalisez la limite à l’aide de l’option serveur [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel :

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

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>est utilisé pour définir le [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pour une seule page ou une action.

Dans une application Razor Pages, [convention](xref:razor-pages/razor-pages-conventions) appliquez `Startup.ConfigureServices`le filtre avec une convention dans :

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

Dans une application De pages Razor ou une application MVC, appliquez le filtre sur la classe ou la méthode d’action du gestionnaire de page :

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>Autres limites de Kestrel

D’autres limites de Kestrel peuvent s’appliquer pour les applications hébergées par Kestrel :

* [Nombre maximale de connexions client](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Taux de données de demande et de réponse](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>Limite de longueur de contenu IIS

La limite de`maxAllowedContentLength`demande par défaut ( ) est de 30 000 000 octets, soit environ 28,6 Mo. Personnalisez la limite dans le fichier *web.config* :

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

Ce paramètre s’applique seulement à IIS. Par défaut, ce comportement ne se produit pas dans le cas d’un hébergement sur Kestrel. Pour plus d’informations, voir [demande limites \<de demandeLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Les limites du module de base ASP.NET ou la présence du module de filtrage de demande DE l’IIS peuvent limiter les téléchargements à 2 ou 4 Go. Pour plus d’informations, voir [Unable pour télécharger le fichier de plus de 2 Go de taille (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Dépanner

Voici certains problèmes courants rencontrés avec le chargement de fichiers et leurs solutions possibles.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Erreur non trouvée lorsqu’elle est déployée sur un serveur IIS

L’erreur suivante indique que le fichier téléchargé dépasse la longueur de contenu configurée du serveur :

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Pour plus d’informations sur l’augmentation de la limite, consultez la section [limite de durée du contenu DE l’IIS.](#iis-content-length-limit)

### <a name="connection-failure"></a>Échec de connexion

Une erreur de connexion et une connexion de serveur de réinitialisation indiquent probablement que le fichier téléchargé dépasse la taille maximale du corps de demande de Kestrel. Pour plus d’informations, consultez la section taille maximale de [la carrosserie de demande de Kestrel.](#kestrel-maximum-request-body-size) Les limites de connexion client de Kestrel peuvent également nécessiter un ajustement.

### <a name="null-reference-exception-with-iformfile"></a>Exception de référence null avec IFormFile

Si le contrôleur accepte les <xref:Microsoft.AspNetCore.Http.IFormFile> fichiers téléchargés `null`à l’aide, mais `enctype` la `multipart/form-data`valeur est , confirmer que le formulaire HTML spécifie une valeur de . Si cet attribut n’est `<form>` pas défini sur l’élément, <xref:Microsoft.AspNetCore.Http.IFormFile> le `null`téléchargement de fichier ne se produit pas et tous les arguments liés sont . Confirmez également que le [nom de téléchargement dans les données de forme correspond au nom de l’application](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Stream était trop long

Les exemples dans <xref:System.IO.MemoryStream> ce sujet s’appuient sur la tenue du contenu du fichier téléchargé. La limite de `MemoryStream` `int.MaxValue`taille d’un est . Si le scénario de téléchargement de fichiers de l’application nécessite la détention du contenu du `MemoryStream` fichier supérieur à 50 Mo, utilisez une approche alternative qui ne repose pas sur un seul pour la tenue du contenu d’un fichier téléchargé.

::: moniker-end


## <a name="additional-resources"></a>Ressources supplémentaires

* [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Chargement de fichiers illimité)
* [Sécurité Azure : cadre de sécurité : Validation des entrées Atténuation](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Modèles de conception de cloud Azure : modèle de clé de valet](/azure/architecture/patterns/valet-key)
