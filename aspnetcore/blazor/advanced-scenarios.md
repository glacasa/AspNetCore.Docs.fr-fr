---
title: ASP.NET scénarios Blazor avancés de base
author: guardrex
description: Découvrez les scénarios Blazoravancés dans , y compris comment intégrer la logique manuelle RenderTreeBuilder dans une application.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659452"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>ASP.NET scénarios avancés de Core Blazor

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

## <a name="blazor-server-circuit-handler"></a>Gestionnaire de circuit Blazor Server

Blazor Server permet au code de définir un *gestionnaire de circuit,* ce qui permet d’exécuter du code sur les modifications apportées à l’état du circuit d’un utilisateur. Un gestionnaire de circuit est `CircuitHandler` mis en œuvre en dérivant et en enregistrant la classe dans le conteneur de service de l’application. L’exemple suivant d’un gestionnaire de circuit suit les connexions SignalR ouvertes :

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

Les gestionnaires de circuits sont enregistrés à l’aide de DI. Les instances portées sont créées par exemple d’un circuit. À `TrackingCircuitHandler` l’aide de l’exemple précédent, un service singleton est créé parce que l’état de tous les circuits doit être suivi :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Si les méthodes d’un gestionnaire de circuit personnalisé jettent une exception non maniée, l’exception est fatale au circuit Blazor Server. Pour tolérer les exceptions dans le code d’un gestionnaire ou appelées méthodes, enveloppez le code dans une ou plusieurs instructions [d’essai-capture](/dotnet/csharp/language-reference/keywords/try-catch) avec la manipulation d’erreur et la connexion.

Lorsqu’un circuit se termine parce qu’un utilisateur s’est déconnecté et que le cadre nettoie l’état du circuit, le cadre dispose de la portée DI du circuit. L’élimination de la portée dispose de tous <xref:System.IDisposable?displayProperty=fullName>les services d' DI à portée de circuit qui mettent en œuvre . Si un service DI organise une exception non gérée lors de l’élimination, le cadre enregistre l’exception.

## <a name="manual-rendertreebuilder-logic"></a>Logique manuelle RenderTreeBuilder

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`fournit des méthodes pour manipuler les composants et les éléments, y compris les composants de construction manuellement dans le code C.

> [!NOTE]
> L’utilisation de `RenderTreeBuilder` composants pour créer est un scénario avancé. Un composant mal formé (par exemple, une étiquette de balisage non fermé) peut entraîner un comportement indéfini.

Considérez `PetDetails` le composant suivant, qui peut être intégré manuellement dans un autre composant :

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

Dans l’exemple suivant, `CreateComponent` la boucle `PetDetails` de la méthode génère trois composants. Lorsque `RenderTreeBuilder` vous appelez des méthodes`OpenComponent` `AddAttribute`pour créer les composants ( et ), les nombres de séquences sont des numéros de ligne de code source. L’algorithme de différence Blazor s’appuie sur les nombres de séquences correspondant à des lignes de code distinctes, et non sur des invocations d’appels distinctes. Lors de la `RenderTreeBuilder` création d’un composant avec des méthodes, codez dur les arguments pour les nombres de séquences. **L’utilisation d’un calcul ou d’un compteur pour générer le nombre de séquences peut entraîner de mauvaises performances.** Pour plus d’informations, voir les numéros de séquence se rapportent aux numéros de ligne de code et non à la section [de l’ordre d’exécution.](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)

`BuiltContent`Composant:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> Les types `Microsoft.AspNetCore.Components.RenderTree` dans le traitement des *résultats* des opérations de rendu. Ce sont des détails internes de la mise en œuvre du cadre Blazor. Ces types doivent être considérés comme *instables* et susceptibles d’être modifiés dans les versions futures.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Les numéros de séquence se rapportent aux numéros de ligne de code et non à l’ordre d’exécution

Les fichiers de composants de rasoir (*.razor*) sont toujours compilés. La compilation est un avantage potentiel par rapport au code d’interprétation parce que l’étape de compilation peut être utilisée pour injecter des informations qui améliorent les performances de l’application au moment de l’exécution.

Un exemple clé de ces améliorations concerne les *nombres de séquences*. Les nombres de séquences indiquent à l’heure d’exécution dont les sorties proviennent de quelles lignes de code distinctes et ordonnées. Le temps d’exécution utilise cette information pour générer des diffs d’arbre efficaces en temps linéaire, ce qui est beaucoup plus rapide que ce qui est normalement possible pour un algorithme général diff arbre.

Considérez le composant razor suivant (*.razor*) fichier:

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Le code précédent se compile à quelque chose comme ce qui suit:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Lorsque le code s’exécute pour `someFlag` `true`la première fois, le cas échéant, le constructeur reçoit :

| Séquence | Type      | Données   |
| :------: | --------- | :----: |
| 0        | Nœud de texte | Premier  |
| 1        | Nœud de texte | Seconde |

Imaginez `someFlag` `false`que devient , et la majoration est rendue à nouveau. Cette fois, le constructeur reçoit :

| Séquence | Type       | Données   |
| :------: | ---------- | :----: |
| 1        | Nœud de texte  | Seconde |

Lorsque le temps d’exécution effectue un diff, `0` il voit que l’élément à la séquence a été supprimé, de sorte qu’il génère le *script d’édition*trivial suivant:

* Retirez le premier nœud texte.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Le problème avec la génération de nombres de séquences programmatiquement

Imaginez plutôt que vous avez écrit la logique suivante de constructeur d’arbres de rendu:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Maintenant, la première sortie est:

| Séquence | Type      | Données   |
| :------: | --------- | :----: |
| 0        | Nœud de texte | Premier  |
| 1        | Nœud de texte | Seconde |

Ce résultat est identique au cas précédent, il n’existe donc aucun problème négatif. `someFlag`est `false` sur le deuxième rendu, et la sortie est:

| Séquence | Type      | Données   |
| :------: | --------- | ------ |
| 0        | Nœud de texte | Seconde |

Cette fois, l’algorithme diff voit que *deux* changements se sont produits, et l’algorithme génère le script d’édition suivant:

* Changer la valeur du premier `Second`nœud texte à .
* Retirez le deuxième nœud texte.

Générer les numéros de séquence a `if/else` perdu toutes les informations utiles sur l’endroit où les branches et les boucles étaient présentes dans le code d’origine. Il en résulte un diff **deux fois** plus longtemps qu’avant.

C’est un exemple anodin. Dans les cas plus réalistes avec des structures complexes et profondément imbriquées, et surtout avec les boucles, le coût de performance est généralement plus élevé. Au lieu d’identifier immédiatement les blocs de boucle ou les branches qui ont été insérés ou enlevés, l’algorithme diff doit se récurer profondément dans les arbres de rendu. Il en résulte généralement d’avoir à construire des scripts d’édition plus longs parce que l’algorithme diff est mal informé sur la façon dont les anciennes et les nouvelles structures se rapportent les unes aux autres.

### <a name="guidance-and-conclusions"></a>Orientations et conclusions

* Les performances de l’application souffrent si les nombres de séquences sont générés dynamiquement.
* Le cadre ne peut pas créer automatiquement ses propres numéros de séquence au moment de l’exécution parce que les informations nécessaires n’existent pas à moins qu’elles ne soient capturées au moment de la compilation.
* N’écrivez pas de longs `RenderTreeBuilder` blocs de logique appliquée manuellement. Préférez les fichiers *.razor* et permettez au compilateur de traiter les numéros de séquence. Si vous n’êtes `RenderTreeBuilder` pas en mesure d’éviter la `OpenRegion` / `CloseRegion` logique manuelle, divisez de longs blocs de code en petits morceaux enveloppés dans des appels. Chaque région dispose de son propre espace distinct de nombres de séquences, de sorte que vous pouvez redémarrer à partir de zéro (ou tout autre nombre arbitraire) à l’intérieur de chaque région.
* Si les nombres de séquences sont codés en dur, l’algorithme diff exige seulement que les nombres de séquences augmentent en valeur. La valeur initiale et les lacunes ne sont pas pertinentes. Une option légitime est d’utiliser le numéro de ligne de code comme numéro de séquence, ou de commencer à partir de zéro et d’augmenter par un ou des centaines (ou tout intervalle préféré). 
* Blazorutilise des nombres de séquences, tandis que d’autres cadres d’interface utilisateur qui diffent les arbres ne les utilisent pas. Diffing est beaucoup plus rapide lorsque Blazor les nombres de séquences sont utilisés, et a l’avantage d’une étape de compilation qui traite automatiquement des numéros de séquence pour les développeurs auteur de fichiers *.razor.*

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a>Effectuer de grands Blazor transferts de données dans les applications Server

Dans certains scénarios, de grandes quantités de Blazordonnées doivent être transférées entre JavaScript et . En règle générale, les transferts de données importants se produisent lorsque :

* Les API du système de fichiers de navigateur sont utilisés pour télécharger ou télécharger un fichier.
* Interop avec une bibliothèque tierce est nécessaire.

Dans Blazor Server, une limitation est en place pour empêcher de passer des messages simples et grands qui peuvent entraîner des problèmes de performances.

Considérez les directives suivantes lors de l’élaboration du code qui transfère des données entre JavaScript et Blazor:

* Trancher les données en petits morceaux et envoyer les segments de données en séquentiel jusqu’à ce que toutes les données soient reçues par le serveur.
* N’allouez pas de gros objets dans le code JavaScript et C.
* Ne bloquez pas le thread principal d’interface utilisateur pendant de longues périodes lors de l’envoi ou de la réception de données.
* Libérez toute mémoire consommée lorsque le processus est terminé ou annulé.
* Appliquer les exigences supplémentaires suivantes aux fins de la sécurité :
  * Déclarez la taille maximale du fichier ou des données qui peut être adoptée.
  * Déclarez le taux de téléchargement minimum du client au serveur.
* Une fois que les données sont reçues par le serveur, les données peuvent être :
  * Stockés temporairement dans un tampon de mémoire jusqu’à ce que tous les segments soient collectés.
  * Consommé immédiatement. Par exemple, les données peuvent être stockées immédiatement dans une base de données ou écrites sur disque au fur et à mesure que chaque segment est reçu.

La classe de téléchargement de fichiers suivante gère JS interop avec le client. La classe de téléchargement utilise JS interop pour :

* Sondage du client pour envoyer un segment de données.
* Annulez la transaction si les temps de vote sont annulés.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

Dans l'exemple précédent :

* Le `_maxBase64SegmentSize` est `8192`réglé à , `_maxBase64SegmentSize = _segmentSize * 4 / 3`qui est calculé à partir de .
* Les API de gestion de la mémoire de base de `_uploadedSegments`bas niveau .NET sont utilisées pour stocker les segments de mémoire sur le serveur dans .
* Une `ReceiveFile` méthode est utilisée pour gérer le téléchargement via JS interop :
  * La taille du fichier est déterminée dans `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`les octets par L’intermédiaire de JS interop avec .
  * Le nombre de segments à recevoir `numberOfSegments`sont calculés et stockés dans .
  * Les segments sont `for` demandés en boucle `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`par L’intermédiaire de JS interop avec . Tous les segments, sauf le dernier doivent être 8 192 octets avant de décoder. Le client est obligé d’envoyer les données d’une manière efficace.
  * Pour chaque segment reçu, les contrôles <xref:System.Convert.TryFromBase64String*>sont effectués avant de décoder avec .
  * Un flux avec les données <xref:System.IO.Stream> est`SegmentedStream`retourné comme un nouveau ( ) après le téléchargement est terminé.

La classe de flux segmentée expose la liste des segments <xref:System.IO.Stream>comme un readonly non-seekable :

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

Le code suivant implémente les fonctions JavaScript pour recevoir les données :

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
