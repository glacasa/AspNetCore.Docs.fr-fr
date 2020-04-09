---
title: Gérer les références Protobuf avec dotnet-GRPC
author: juntaoluo
description: Renseignez-vous sur l’ajout, la mise à jour, la suppression et la liste des références Protobuf avec l’outil global dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667334"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="43f3b-103">Gérer les références Protobuf avec dotnet-GRPC</span><span class="sxs-lookup"><span data-stu-id="43f3b-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="43f3b-104">Par [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="43f3b-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="43f3b-105">`dotnet-grpc`est un outil mondial de base .NET pour gérer les références [Protobuf (*.proto)*](xref:grpc/basics#proto-file) au sein d’un projet .NET gRPC.</span><span class="sxs-lookup"><span data-stu-id="43f3b-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="43f3b-106">L’outil peut être utilisé pour ajouter, actualiser, supprimer et énumérer les références Protobuf.</span><span class="sxs-lookup"><span data-stu-id="43f3b-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="43f3b-107">Installation</span><span class="sxs-lookup"><span data-stu-id="43f3b-107">Installation</span></span>

<span data-ttu-id="43f3b-108">Pour installer `dotnet-grpc` l’outil [global de base .NET](/dotnet/core/tools/global-tools), exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="43f3b-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="43f3b-109">Ajouter des références</span><span class="sxs-lookup"><span data-stu-id="43f3b-109">Add references</span></span>

<span data-ttu-id="43f3b-110">`dotnet-grpc`peut être utilisé pour ajouter `<Protobuf />` des références Protobuf comme éléments du fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="43f3b-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="43f3b-111">Les références Protobuf sont utilisées pour générer les actifs du client et/ou du serveur C.</span><span class="sxs-lookup"><span data-stu-id="43f3b-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="43f3b-112">L’outil `dotnet-grpc` peut :</span><span class="sxs-lookup"><span data-stu-id="43f3b-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="43f3b-113">Créez une référence Protobuf à partir de fichiers locaux sur disque.</span><span class="sxs-lookup"><span data-stu-id="43f3b-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="43f3b-114">Créez une référence Protobuf à partir d’un fichier distant spécifié par une URL.</span><span class="sxs-lookup"><span data-stu-id="43f3b-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="43f3b-115">Assurez-vous que les bonnes dépendances de paquet de gRPC sont ajoutées au projet.</span><span class="sxs-lookup"><span data-stu-id="43f3b-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="43f3b-116">Par exemple, `Grpc.AspNetCore` le paquet est ajouté à une application web.</span><span class="sxs-lookup"><span data-stu-id="43f3b-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="43f3b-117">`Grpc.AspNetCore`contient des bibliothèques de serveurs et de clients gRPC et le support d’outillage.</span><span class="sxs-lookup"><span data-stu-id="43f3b-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="43f3b-118">Alternativement, `Grpc.Net.Client`le `Grpc.Tools` `Google.Protobuf` , et les paquets, qui ne contiennent que les bibliothèques de clients gRPC et le support d’outillage, sont ajoutés à une application Console.</span><span class="sxs-lookup"><span data-stu-id="43f3b-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="43f3b-119">Ajouter un fichier</span><span class="sxs-lookup"><span data-stu-id="43f3b-119">Add file</span></span>

<span data-ttu-id="43f3b-120">La `add-file` commande est utilisée pour ajouter des fichiers locaux sur disque comme références Protobuf.</span><span class="sxs-lookup"><span data-stu-id="43f3b-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="43f3b-121">Les trajectoires de fichiers fournies :</span><span class="sxs-lookup"><span data-stu-id="43f3b-121">The file paths provided:</span></span>

* <span data-ttu-id="43f3b-122">Peut être par rapport à l’annuaire actuel ou des chemins absolus.</span><span class="sxs-lookup"><span data-stu-id="43f3b-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="43f3b-123">Peut contenir des wild cards pour le [glissement](https://wikipedia.org/wiki/Glob_(programming))de fichier basé sur le modèle .</span><span class="sxs-lookup"><span data-stu-id="43f3b-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="43f3b-124">Si des fichiers sont en dehors `Link` de l’annuaire du projet, `Protos` un élément est ajouté pour afficher le fichier sous le dossier dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="43f3b-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="43f3b-125">Usage</span><span class="sxs-lookup"><span data-stu-id="43f3b-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="43f3b-126">Arguments</span><span class="sxs-lookup"><span data-stu-id="43f3b-126">Arguments</span></span>

| <span data-ttu-id="43f3b-127">Argument</span><span class="sxs-lookup"><span data-stu-id="43f3b-127">Argument</span></span> | <span data-ttu-id="43f3b-128">Description</span><span class="sxs-lookup"><span data-stu-id="43f3b-128">Description</span></span> |
|-|-|
| <span data-ttu-id="43f3b-129">files</span><span class="sxs-lookup"><span data-stu-id="43f3b-129">files</span></span> | <span data-ttu-id="43f3b-130">Les références de fichiers protobuf.</span><span class="sxs-lookup"><span data-stu-id="43f3b-130">The protobuf file references.</span></span> <span data-ttu-id="43f3b-131">Ceux-ci peuvent être un chemin à glob pour les fichiers protobuf locaux.</span><span class="sxs-lookup"><span data-stu-id="43f3b-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="43f3b-132">Options</span><span class="sxs-lookup"><span data-stu-id="43f3b-132">Options</span></span>

| <span data-ttu-id="43f3b-133">Option courte</span><span class="sxs-lookup"><span data-stu-id="43f3b-133">Short option</span></span> | <span data-ttu-id="43f3b-134">Option longue</span><span class="sxs-lookup"><span data-stu-id="43f3b-134">Long option</span></span> | <span data-ttu-id="43f3b-135">Description</span><span class="sxs-lookup"><span data-stu-id="43f3b-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="43f3b-136">-p</span><span class="sxs-lookup"><span data-stu-id="43f3b-136">-p</span></span> | <span data-ttu-id="43f3b-137">--projet</span><span class="sxs-lookup"><span data-stu-id="43f3b-137">--project</span></span> | <span data-ttu-id="43f3b-138">Le chemin vers le dossier du projet pour fonctionner.</span><span class="sxs-lookup"><span data-stu-id="43f3b-138">The path to the project file to operate on.</span></span> <span data-ttu-id="43f3b-139">Si un fichier n’est pas spécifié, la commande recherche un répertoire actuel.</span><span class="sxs-lookup"><span data-stu-id="43f3b-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="43f3b-140">-S</span><span class="sxs-lookup"><span data-stu-id="43f3b-140">-s</span></span> | <span data-ttu-id="43f3b-141">--services</span><span class="sxs-lookup"><span data-stu-id="43f3b-141">--services</span></span> | <span data-ttu-id="43f3b-142">Le type de services gRPC qui devraient être générés.</span><span class="sxs-lookup"><span data-stu-id="43f3b-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="43f3b-143">Si `Default` elle `Both` est spécifiée, `Client` est utilisée pour des projets Web et est utilisée pour des projets non-Web.</span><span class="sxs-lookup"><span data-stu-id="43f3b-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="43f3b-144">Les valeurs `Both` `Client`acceptées `None` `Server`sont , , `Default`, .</span><span class="sxs-lookup"><span data-stu-id="43f3b-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="43f3b-145">-i</span><span class="sxs-lookup"><span data-stu-id="43f3b-145">-i</span></span> | <span data-ttu-id="43f3b-146">--dirs supplémentaires-importation</span><span class="sxs-lookup"><span data-stu-id="43f3b-146">--additional-import-dirs</span></span> | <span data-ttu-id="43f3b-147">D’autres répertoires à utiliser lors de la résolution des importations pour les fichiers protobuf.</span><span class="sxs-lookup"><span data-stu-id="43f3b-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="43f3b-148">Il s’agit d’une liste de chemins séparés par le point-virgule.</span><span class="sxs-lookup"><span data-stu-id="43f3b-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="43f3b-149">--accès</span><span class="sxs-lookup"><span data-stu-id="43f3b-149">--access</span></span> | <span data-ttu-id="43f3b-150">Le modificateur d’accès à utiliser pour les classes C générées.</span><span class="sxs-lookup"><span data-stu-id="43f3b-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="43f3b-151">La valeur par défaut est `Public`.</span><span class="sxs-lookup"><span data-stu-id="43f3b-151">The default value is `Public`.</span></span> <span data-ttu-id="43f3b-152">Les valeurs acceptées sont `Internal` et `Public`.</span><span class="sxs-lookup"><span data-stu-id="43f3b-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="43f3b-153">Ajouter une URL</span><span class="sxs-lookup"><span data-stu-id="43f3b-153">Add URL</span></span>

<span data-ttu-id="43f3b-154">La `add-url` commande est utilisée pour ajouter un fichier distant spécifié par une URL source comme référence Protobuf.</span><span class="sxs-lookup"><span data-stu-id="43f3b-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="43f3b-155">Un chemin de fichier doit être fourni pour spécifier où télécharger le fichier à distance.</span><span class="sxs-lookup"><span data-stu-id="43f3b-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="43f3b-156">Le chemin de fichier peut être par rapport à l’annuaire actuel ou un chemin absolu.</span><span class="sxs-lookup"><span data-stu-id="43f3b-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="43f3b-157">Si le chemin de fichier est `Link` en dehors de l’annuaire du `Protos` projet, un élément est ajouté pour afficher le fichier sous le dossier virtuel dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="43f3b-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="43f3b-158">Usage</span><span class="sxs-lookup"><span data-stu-id="43f3b-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="43f3b-159">Arguments</span><span class="sxs-lookup"><span data-stu-id="43f3b-159">Arguments</span></span>

| <span data-ttu-id="43f3b-160">Argument</span><span class="sxs-lookup"><span data-stu-id="43f3b-160">Argument</span></span> | <span data-ttu-id="43f3b-161">Description</span><span class="sxs-lookup"><span data-stu-id="43f3b-161">Description</span></span> |
|-|-|
| <span data-ttu-id="43f3b-162">url</span><span class="sxs-lookup"><span data-stu-id="43f3b-162">url</span></span> | <span data-ttu-id="43f3b-163">L’URL d’un fichier protobuf distant.</span><span class="sxs-lookup"><span data-stu-id="43f3b-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="43f3b-164">Options</span><span class="sxs-lookup"><span data-stu-id="43f3b-164">Options</span></span>

| <span data-ttu-id="43f3b-165">Option courte</span><span class="sxs-lookup"><span data-stu-id="43f3b-165">Short option</span></span> | <span data-ttu-id="43f3b-166">Option longue</span><span class="sxs-lookup"><span data-stu-id="43f3b-166">Long option</span></span> | <span data-ttu-id="43f3b-167">Description</span><span class="sxs-lookup"><span data-stu-id="43f3b-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="43f3b-168">-o</span><span class="sxs-lookup"><span data-stu-id="43f3b-168">-o</span></span> | <span data-ttu-id="43f3b-169">--output</span><span class="sxs-lookup"><span data-stu-id="43f3b-169">--output</span></span> | <span data-ttu-id="43f3b-170">Spécifie le chemin de téléchargement du fichier protobuf distant.</span><span class="sxs-lookup"><span data-stu-id="43f3b-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="43f3b-171">C'est une option obligatoire.</span><span class="sxs-lookup"><span data-stu-id="43f3b-171">This is a required option.</span></span>
| <span data-ttu-id="43f3b-172">-p</span><span class="sxs-lookup"><span data-stu-id="43f3b-172">-p</span></span> | <span data-ttu-id="43f3b-173">--projet</span><span class="sxs-lookup"><span data-stu-id="43f3b-173">--project</span></span> | <span data-ttu-id="43f3b-174">Le chemin vers le dossier du projet pour fonctionner.</span><span class="sxs-lookup"><span data-stu-id="43f3b-174">The path to the project file to operate on.</span></span> <span data-ttu-id="43f3b-175">Si un fichier n’est pas spécifié, la commande recherche un répertoire actuel.</span><span class="sxs-lookup"><span data-stu-id="43f3b-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="43f3b-176">-S</span><span class="sxs-lookup"><span data-stu-id="43f3b-176">-s</span></span> | <span data-ttu-id="43f3b-177">--services</span><span class="sxs-lookup"><span data-stu-id="43f3b-177">--services</span></span> | <span data-ttu-id="43f3b-178">Le type de services gRPC qui devraient être générés.</span><span class="sxs-lookup"><span data-stu-id="43f3b-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="43f3b-179">Si `Default` elle `Both` est spécifiée, `Client` est utilisée pour des projets Web et est utilisée pour des projets non-Web.</span><span class="sxs-lookup"><span data-stu-id="43f3b-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="43f3b-180">Les valeurs `Both` `Client`acceptées `None` `Server`sont , , `Default`, .</span><span class="sxs-lookup"><span data-stu-id="43f3b-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="43f3b-181">-i</span><span class="sxs-lookup"><span data-stu-id="43f3b-181">-i</span></span> | <span data-ttu-id="43f3b-182">--dirs supplémentaires-importation</span><span class="sxs-lookup"><span data-stu-id="43f3b-182">--additional-import-dirs</span></span> | <span data-ttu-id="43f3b-183">D’autres répertoires à utiliser lors de la résolution des importations pour les fichiers protobuf.</span><span class="sxs-lookup"><span data-stu-id="43f3b-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="43f3b-184">Il s’agit d’une liste de chemins séparés par le point-virgule.</span><span class="sxs-lookup"><span data-stu-id="43f3b-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="43f3b-185">--accès</span><span class="sxs-lookup"><span data-stu-id="43f3b-185">--access</span></span> | <span data-ttu-id="43f3b-186">Le modificateur d’accès à utiliser pour les classes C générées.</span><span class="sxs-lookup"><span data-stu-id="43f3b-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="43f3b-187">La valeur par défaut est `Public`.</span><span class="sxs-lookup"><span data-stu-id="43f3b-187">Default value is `Public`.</span></span> <span data-ttu-id="43f3b-188">Les valeurs acceptées sont `Internal` et `Public`.</span><span class="sxs-lookup"><span data-stu-id="43f3b-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="43f3b-189">Supprimer</span><span class="sxs-lookup"><span data-stu-id="43f3b-189">Remove</span></span>

<span data-ttu-id="43f3b-190">La `remove` commande est utilisée pour supprimer les références Protobuf du fichier *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="43f3b-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="43f3b-191">La commande accepte les arguments de voie et les URL de source comme arguments.</span><span class="sxs-lookup"><span data-stu-id="43f3b-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="43f3b-192">L’outil :</span><span class="sxs-lookup"><span data-stu-id="43f3b-192">The tool:</span></span>

* <span data-ttu-id="43f3b-193">Supprime uniquement la référence Protobuf.</span><span class="sxs-lookup"><span data-stu-id="43f3b-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="43f3b-194">Ne supprime pas le fichier *.proto,* même s’il a été téléchargé à l’origine à partir d’une URL à distance.</span><span class="sxs-lookup"><span data-stu-id="43f3b-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="43f3b-195">Usage</span><span class="sxs-lookup"><span data-stu-id="43f3b-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="43f3b-196">Arguments</span><span class="sxs-lookup"><span data-stu-id="43f3b-196">Arguments</span></span>

| <span data-ttu-id="43f3b-197">Argument</span><span class="sxs-lookup"><span data-stu-id="43f3b-197">Argument</span></span> | <span data-ttu-id="43f3b-198">Description</span><span class="sxs-lookup"><span data-stu-id="43f3b-198">Description</span></span> |
|-|-|
| <span data-ttu-id="43f3b-199">references</span><span class="sxs-lookup"><span data-stu-id="43f3b-199">references</span></span> | <span data-ttu-id="43f3b-200">Les URL ou les trajectoires de fichier des références protobuf à supprimer.</span><span class="sxs-lookup"><span data-stu-id="43f3b-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="43f3b-201">Options</span><span class="sxs-lookup"><span data-stu-id="43f3b-201">Options</span></span>

| <span data-ttu-id="43f3b-202">Option courte</span><span class="sxs-lookup"><span data-stu-id="43f3b-202">Short option</span></span> | <span data-ttu-id="43f3b-203">Option longue</span><span class="sxs-lookup"><span data-stu-id="43f3b-203">Long option</span></span> | <span data-ttu-id="43f3b-204">Description</span><span class="sxs-lookup"><span data-stu-id="43f3b-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="43f3b-205">-p</span><span class="sxs-lookup"><span data-stu-id="43f3b-205">-p</span></span> | <span data-ttu-id="43f3b-206">--projet</span><span class="sxs-lookup"><span data-stu-id="43f3b-206">--project</span></span> | <span data-ttu-id="43f3b-207">Le chemin vers le dossier du projet pour fonctionner.</span><span class="sxs-lookup"><span data-stu-id="43f3b-207">The path to the project file to operate on.</span></span> <span data-ttu-id="43f3b-208">Si un fichier n’est pas spécifié, la commande recherche un répertoire actuel.</span><span class="sxs-lookup"><span data-stu-id="43f3b-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="43f3b-209">Actualiser</span><span class="sxs-lookup"><span data-stu-id="43f3b-209">Refresh</span></span>

<span data-ttu-id="43f3b-210">La `refresh` commande est utilisée pour mettre à jour une référence à distance avec le dernier contenu de l’URL source.</span><span class="sxs-lookup"><span data-stu-id="43f3b-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="43f3b-211">Le chemin du fichier de téléchargement et l’URL source peuvent être utilisés pour spécifier la référence à mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="43f3b-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="43f3b-212">Remarque :</span><span class="sxs-lookup"><span data-stu-id="43f3b-212">Note:</span></span>

* <span data-ttu-id="43f3b-213">Les hachages du contenu du fichier sont comparés pour déterminer si le fichier local doit être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="43f3b-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="43f3b-214">Aucune information sur l’allphe n’est comparée.</span><span class="sxs-lookup"><span data-stu-id="43f3b-214">No timestamp information is compared.</span></span>

<span data-ttu-id="43f3b-215">L’outil remplace toujours le fichier local par le fichier à distance si une mise à jour est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="43f3b-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="43f3b-216">Usage</span><span class="sxs-lookup"><span data-stu-id="43f3b-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="43f3b-217">Arguments</span><span class="sxs-lookup"><span data-stu-id="43f3b-217">Arguments</span></span>

| <span data-ttu-id="43f3b-218">Argument</span><span class="sxs-lookup"><span data-stu-id="43f3b-218">Argument</span></span> | <span data-ttu-id="43f3b-219">Description</span><span class="sxs-lookup"><span data-stu-id="43f3b-219">Description</span></span> |
|-|-|
| <span data-ttu-id="43f3b-220">references</span><span class="sxs-lookup"><span data-stu-id="43f3b-220">references</span></span> | <span data-ttu-id="43f3b-221">Les URL ou les trajectoires de fichiers vers des références protobuf à distance qui doivent être mises à jour.</span><span class="sxs-lookup"><span data-stu-id="43f3b-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="43f3b-222">Laissez cet argument vide pour rafraîchir toutes les références à distance.</span><span class="sxs-lookup"><span data-stu-id="43f3b-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="43f3b-223">Options</span><span class="sxs-lookup"><span data-stu-id="43f3b-223">Options</span></span>

| <span data-ttu-id="43f3b-224">Option courte</span><span class="sxs-lookup"><span data-stu-id="43f3b-224">Short option</span></span> | <span data-ttu-id="43f3b-225">Option longue</span><span class="sxs-lookup"><span data-stu-id="43f3b-225">Long option</span></span> | <span data-ttu-id="43f3b-226">Description</span><span class="sxs-lookup"><span data-stu-id="43f3b-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="43f3b-227">-p</span><span class="sxs-lookup"><span data-stu-id="43f3b-227">-p</span></span> | <span data-ttu-id="43f3b-228">--projet</span><span class="sxs-lookup"><span data-stu-id="43f3b-228">--project</span></span> | <span data-ttu-id="43f3b-229">Le chemin vers le dossier du projet pour fonctionner.</span><span class="sxs-lookup"><span data-stu-id="43f3b-229">The path to the project file to operate on.</span></span> <span data-ttu-id="43f3b-230">Si un fichier n’est pas spécifié, la commande recherche un répertoire actuel.</span><span class="sxs-lookup"><span data-stu-id="43f3b-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="43f3b-231">--à sec</span><span class="sxs-lookup"><span data-stu-id="43f3b-231">--dry-run</span></span> | <span data-ttu-id="43f3b-232">Sortie d’une liste de fichiers qui seraient mis à jour sans télécharger de nouveau contenu.</span><span class="sxs-lookup"><span data-stu-id="43f3b-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="43f3b-233">List</span><span class="sxs-lookup"><span data-stu-id="43f3b-233">List</span></span>

<span data-ttu-id="43f3b-234">La `list` commande est utilisée pour afficher toutes les références Protobuf dans le fichier du projet.</span><span class="sxs-lookup"><span data-stu-id="43f3b-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="43f3b-235">Si toutes les valeurs d’une colonne sont des valeurs par défaut, la colonne peut être omise.</span><span class="sxs-lookup"><span data-stu-id="43f3b-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="43f3b-236">Usage</span><span class="sxs-lookup"><span data-stu-id="43f3b-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="43f3b-237">Options</span><span class="sxs-lookup"><span data-stu-id="43f3b-237">Options</span></span>

| <span data-ttu-id="43f3b-238">Option courte</span><span class="sxs-lookup"><span data-stu-id="43f3b-238">Short option</span></span> | <span data-ttu-id="43f3b-239">Option longue</span><span class="sxs-lookup"><span data-stu-id="43f3b-239">Long option</span></span> | <span data-ttu-id="43f3b-240">Description</span><span class="sxs-lookup"><span data-stu-id="43f3b-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="43f3b-241">-p</span><span class="sxs-lookup"><span data-stu-id="43f3b-241">-p</span></span> | <span data-ttu-id="43f3b-242">--projet</span><span class="sxs-lookup"><span data-stu-id="43f3b-242">--project</span></span> | <span data-ttu-id="43f3b-243">Le chemin vers le dossier du projet pour fonctionner.</span><span class="sxs-lookup"><span data-stu-id="43f3b-243">The path to the project file to operate on.</span></span> <span data-ttu-id="43f3b-244">Si un fichier n’est pas spécifié, la commande recherche un répertoire actuel.</span><span class="sxs-lookup"><span data-stu-id="43f3b-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="43f3b-245">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="43f3b-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
