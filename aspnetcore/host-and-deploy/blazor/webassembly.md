---
title: Héberger et déployer Blazor ASP.net Core webassembly
author: guardrex
description: Découvrez comment héberger et déployer une Blazor application à l’aide de ASP.net Core, de réseaux de distribution de contenu (CDN), de serveurs de fichiers et de pages github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: e136a401beffe9cc7e29906b3631ab3f068b30fd
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967595"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a>Héberger et déployer ASP.NET Core éblouissant webassembly

Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)et [safia Abdalla](https://safia.rocks)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Avec le [modèle d’hébergement de Webassembly éblouissant](xref:blazor/hosting-models#blazor-webassembly):

* L’application éblouissant, ses dépendances et le Runtime .NET sont téléchargés dans le navigateur en parallèle.
* L’application est exécutée directement sur le thread d’interface utilisateur du navigateur.

Les stratégies de déploiement suivantes sont prises en charge :

* L’application Blazor est fournie par une application ASP.NET Core. Cette stratégie est abordée dans la section [Déploiement hébergé avec ASP.NET Core](#hosted-deployment-with-aspnet-core).
* L’application Blazor est placée sur un service ou un serveur web d’hébergement statique, où .NET n’est pas utilisé pour servir l’application Blazor. Cette stratégie est traitée dans la section [Déploiement autonome](#standalone-deployment) , qui comprend des informations sur l’hébergement d’une application de webassembly éblouissante en tant que sous-application IIS.

## <a name="brotli-precompression"></a>Précompression Brotli

Quand une application de webassembly éblouissant est publiée, la sortie est précompressée à l’aide de l' [algorithme de compression Brotli](https://tools.ietf.org/html/rfc7932) au niveau le plus élevé afin de réduire la taille de l’application et de la nécessité de compresser le Runtime.

Pour la configuration de la compression IIS *Web. config* , consultez la section [IIS : Brotli et compression gzip](#brotli-and-gzip-compression) .

## <a name="rewrite-urls-for-correct-routing"></a>Réécriture d’URL pour un routage correct

Le routage des requêtes pour les composants de page dans une application de webassembly éblouissante n’est pas aussi simple que le routage des demandes dans un serveur éblouissant, une application hébergée. Prenons l’exemple d’une application de webassembly éblouissant avec deux composants :

* *Main.razor* &ndash; Se charge à la racine de l’application et contient un lien vers le composant `About` (`href="About"`).
* Composant *About.Razor* &ndash; `About`.

Quand le document par défaut de l’application est demandé à l’aide de la barre d’adresses du navigateur (par exemple, `https://www.contoso.com/`) :

1. Le navigateur effectue une requête.
1. La page par défaut est retournée, généralement *index.html*.
1. *index.HTML* amorce l’application.
1. Le routeur de Blazor se charge et le composant `Main` Razor est rendu.

Dans la page principale, la sélection du composant `About` fonctionne sur le client, car le routeur Blazor empêche le navigateur d’effectuer une requête sur Internet à `www.contoso.com` pour `About` et fournit lui-même le composant `About` rendu. Toutes les requêtes pour les points de terminaison internes de *l’application* Web de l’outil éblouissant fonctionnent de la même façon : les demandes ne déclenchent pas de requêtes basées sur un navigateur vers des ressources hébergées sur le serveur sur Internet. Le routeur gère les requêtes en interne.

Si une requête pour `www.contoso.com/About` est effectuée à l’aide de la barre d’adresses du navigateur, elle échoue. Comme cette ressource n’existe pas sur l’hôte Internet de l’application, une réponse *404 – Non trouvé* est retournée.

Étant donné que les navigateurs envoient des requêtes aux hôtes basés sur Internet pour des pages côté client, les serveurs web et les services d’hébergement doivent réécrire toutes les requêtes pour les ressources qui ne se trouvent pas physiquement sur le serveur afin qu’elles pointent vers la page *index.html*. Quand *index. html* est retourné, le routeur éblouissant de l’application prend le relais et répond avec la ressource correcte.

Lors du déploiement sur un serveur IIS, vous pouvez utiliser le module de réécriture d’URL avec le fichier *Web. config* publié de l’application. Pour plus d’informations, consultez la section [IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Déploiement hébergé avec ASP.NET Core

Un *déploiement hébergé* sert l’application de webassembly éblouissant à des navigateurs à partir d’une [application ASP.net Core](xref:index) qui s’exécute sur un serveur Web.

L’application client éblouissant webassembly est publiée dans le dossier */bin/Release/{Target Framework}/Publish/wwwroot* de l’application serveur, ainsi que les autres ressources Web statiques de l’application serveur. Les deux applications sont déployées ensemble. Un serveur web capable d’héberger une application ASP.NET Core est nécessaire. Pour un déploiement hébergé, Visual Studio comprend le modèle de projet **application Webassembly éblouissant** (`blazorwasm` modèle lors de l’utilisation de la commande [dotnet New](/dotnet/core/tools/dotnet-new) ) avec l'`-ho|--hosted` option **hébergée** sélectionnée (lors de l’utilisation de la `dotnet new` commande).

Pour plus d’informations sur l’hébergement et le déploiement d’applications ASP.NET Core, consultez <xref:host-and-deploy/index>.

Pour plus d’informations concernant le déploiement sur Azure App Service, consultez <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="standalone-deployment"></a>Déploiement autonome

Un *Déploiement autonome* sert l’application de webassembly éblouissant sous la forme d’un ensemble de fichiers statiques demandés directement par les clients. N’importe quel serveur de fichiers statiques est capable de servir l’application Blazor.

Les ressources de déploiement autonomes sont publiées dans le dossier */bin/Release/{Target Framework}/Publish/wwwroot* .

### <a name="azure-app-service"></a>Azure App Service

Les applications webassembly éblouissantes peuvent être déployées sur Azure App services sur Windows, qui héberge l’application sur [IIS](#iis).

Le déploiement d’une application webassembly éblouissante autonome sur Azure App Service pour Linux n’est pas pris en charge actuellement. Une image de serveur Linux pour héberger l’application n’est pas disponible pour l’instant. Le travail est en cours pour activer ce scénario.

### <a name="iis"></a>IIS

IIS est un serveur de fichiers statiques compatible avec les applications Blazor. Pour configurer IIS afin d’héberger Blazor, consultez [Générer un site web statique sur IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Les ressources publiées sont créées dans le dossier */bin/Release/{FRAMEWORK CIBLE}/publish*. Hébergez le contenu du dossier *publish* sur le serveur web ou le service d’hébergement.

#### <a name="webconfig"></a>web.config

Quand un projet Blazor est publié, un fichier *web.config* est créé avec la configuration IIS suivante :

* Les types MIME sont définis pour les extensions de fichiers suivantes :
  * *. dll* &ndash;`application/octet-stream`
  * *. JSON* &ndash;`application/json`
  * *. WASM* &ndash;`application/wasm`
  * *. WOFF* &ndash;`application/font-woff`
  * *. woff2* &ndash;`application/font-woff`
* La compression HTTP est activée pour les types MIME suivants :
  * `application/octet-stream`
  * `application/wasm`
* Des règles du module de réécriture d’URL sont établies :
  * Servez-vous du sous-répertoire dans lequel résident les ressources statiques de l’application (*wwwroot/{chemin demandé}*).
  * Créez le routage de secours SPA afin que les demandes pour les ressources non-fichier soient redirigées vers le document par défaut de l’application dans son dossier des ressources statiques (*wwwroot/index.html*).
  
#### <a name="use-a-custom-webconfig"></a>Utiliser un fichier Web. config personnalisé

Pour utiliser un fichier *Web. config* personnalisé, placez le fichier *Web. config* personnalisé à la racine du dossier du projet et publiez le projet.

#### <a name="install-the-url-rewrite-module"></a>Installer le module de réécriture d’URL

Le [module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite) est nécessaire pour réécrire les URL. Il n’est pas installé par défaut, et ne peut pas l’être en tant que fonctionnalité de service de rôle Serveur Web (IIS). Vous devez le télécharger à partir du site web IIS. Utilisez Web Platform Installer pour installer le module :

1. Localement, accédez à la [page des téléchargements du Module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Pour la version anglaise, sélectionnez **WebPI** pour télécharger le programme d’installation WebPI. Pour les autres langues, sélectionnez l’architecture appropriée pour le serveur (x86/x64) afin de télécharger le programme d’installation.
1. Copiez le programme d’installation sur le serveur. Exécutez le programme d’installation. Sélectionnez le bouton **Installer** et acceptez les termes du contrat de licence. Il n’est pas nécessaire de redémarrer le serveur après l’installation.

#### <a name="configure-the-website"></a>Configurer le site web

Affectez le dossier de l’application comme **chemin physique** du site web. Le dossier contient les éléments suivants :

* Le fichier *web.config* utilisé par IIS pour configurer le site web, notamment les règles de redirection nécessaires et les types de contenu de fichiers
* Le dossier de ressources statiques de l’application

#### <a name="host-as-an-iis-sub-app"></a>Héberger en tant que sous-application IIS

Si une application autonome est hébergée en tant que sous-application IIS, effectuez l’une des opérations suivantes :

* Désactivez le gestionnaire de module ASP.NET Core hérité.

  Supprimez le gestionnaire dans le fichier *Web. config* publié de l’application éblouissant en ajoutant `<handlers>` une section au fichier :

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Désactivez l’héritage de la section de l' `<system.webServer>` application racine ( `<location>` parent) `inheritInChildApplications` à l' `false`aide d’un élément dont la valeur est :

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

La suppression du gestionnaire ou la désactivation de l’héritage est effectuée en plus de [la configuration du chemin d’accès de base de l’application](xref:host-and-deploy/blazor/index#app-base-path). Dans le fichier *index.html* de l’application, définissez le chemin de base de l’application sur l’alias IIS utilisé lors de la configuration de la sous-application dans IIS.

#### <a name="brotli-and-gzip-compression"></a>Compression Brotli et gzip

IIS peut être configuré par le biais de *Web. config* pour servir les ressources éblouissantes Brotli ou GZip compressées. Pour obtenir un exemple de configuration, consultez [Web. config](webassembly/_samples/web.config?raw=true).

#### <a name="troubleshooting"></a>Dépannage

Si vous recevez un message *500 – Erreur interne du serveur* et que le Gestionnaire IIS lève des erreurs quand vous tentez d’accéder à la configuration du site web, vérifiez que le module de réécriture d’URL est installé. Quand le module n’est pas installé, le fichier *web.config* ne peut pas être analysé par IIS. Cela empêche le Gestionnaire IIS de charger la configuration du site web et empêche le site web de fournir les fichiers statiques Blazor.

Pour plus d’informations sur le dépannage des déploiements sur IIS, consultez <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Stockage Azure

L’hébergement de fichiers statiques [Azure Storage](/azure/storage/) permet l’hébergement d’applications éblouissantes sans serveur. Les noms de domaine personnalisé, le réseau de distribution de contenu Azure (CDN) et HTTPS sont pris en charge.

Lorsque le service blob est activé pour l’hébergement de site Web statique sur un compte de stockage :

* Définissez le **nom du document d’index** sur `index.html`.
* Définissez le **chemin d’accès au document d’erreur** sur `index.html`. Les composants de Razor et autres points de terminaison non-fichier ne se trouvent sur les chemins d’accès physiques dans le contenu statique stocké par le service blob. Lors de la réception d’une requête pour l’une de ces ressources que le routeur Blazor doit gérer, l’erreur *404 - introuvable* générée par le service blob achemine la requête vers le **chemin d’accès au document d’erreur**. Le blob *index.html* est retourné et le routeur Blazor charge et traite le chemin d’accès.

Pour plus d’informations, consultez [Hébergement de sites web statiques dans le service Stockage Azure](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Le fichier *nginx. conf* suivant est simplifié pour montrer comment configurer Nginx pour envoyer le fichier *index. html* lorsqu’il ne peut pas trouver de fichier correspondant sur le disque.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Pour plus d’informations sur la configuration du serveur web Nginx de production, consultez [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Création de fichiers de configuration NGINX et NGINX Plus).

### <a name="nginx-in-docker"></a>Nginx dans Docker

Pour héberger Blazor dans Docker à l’aide de Nginx, configurez le fichier Dockerfile pour utiliser l’image Nginx basée sur Alpine. Mettez à jour le fichier Dockerfile pour copier le fichier *nginx.config* dans le conteneur.

Ajoutez une ligne au fichier Dockerfile, comme indiqué dans l’exemple suivant :

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Pour déployer une application de webassembly éblouissante sur CentOS 7 ou version ultérieure :

1. Créez le fichier de configuration Apache. L’exemple suivant est un fichier de configuration simplifié (*blazorapp. config*) :

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Placez le fichier de configuration Apache dans `/etc/httpd/conf.d/` le répertoire, qui est le répertoire de configuration Apache par défaut dans CentOS 7.

1. Placez les fichiers de l’application dans `/var/www/blazorapp` le répertoire (l’emplacement spécifié `DocumentRoot` dans le fichier de configuration).

1. Redémarrez le service Apache.

Pour plus d’informations, consultez [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) et [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>GitHub Pages

Pour gérer les réécritures d’URL, ajoutez un fichier *404.html* avec un script qui gère la redirection de la requête vers la page *index.html*. Pour obtenir un exemple d’implémentation fournie par la communauté, consultez [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (Applications à une seule page pour GitHub Pages) ([rafrex/spa-github-pages sur GitHub](https://github.com/rafrex/spa-github-pages#readme)). Vous pouvez obtenir un exemple d’utilisation de cette approche à l’adresse [blazor-demo/blazor-demo.github.io sur GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site actif](https://blazor-demo.github.io/)).

Quand vous utilisez un site de projet plutôt qu’un site d’entreprise, ajoutez ou mettez à jour la balise `<base>` dans *index.html*. Définissez la valeur d’attribut `href` sur le nom du dépôt GitHub avec une barre oblique (par exemple, `my-repository/`.

## <a name="host-configuration-values"></a>Valeurs de configuration de l’hôte

Les [applications Webassembly éblouissantes](xref:blazor/hosting-models#blazor-webassembly) peuvent accepter les valeurs de configuration d’hôte suivantes comme arguments de ligne de commande au moment de l’exécution dans l’environnement de développement.

### <a name="content-root"></a>Racine de contenu

L' `--contentroot` argument définit le chemin d’accès absolu au répertoire qui contient les fichiers de contenu de l’application ([racine du contenu](xref:fundamentals/index#content-root)). Dans les exemples suivants, `/content-root-path` est le chemin racine du contenu de l’application.

* Passez l’argument lors de l’exécution de l’application localement à une invite de commandes. À partir du répertoire de l’application, exécutez :

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Ajoutez une entrée au fichier *launchSettings.json* de l’application dans le profil **IIS Express**. Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* Dans Visual Studio, spécifiez l’argument dans **Propriétés** > **Déboguer** > les arguments de l'**application**. Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Base du chemin

L' `--pathbase` argument définit le chemin d’accès de base d’application pour une application exécutée localement avec un chemin d’URL `<base>` relatif `href` non racine (la balise est `/` définie sur un chemin d’accès autre que pour la mise en lots et la production). Dans les exemples suivants, `/relative-URL-path` est la base du chemin de l’application. Pour plus d’informations, consultez chemin de la base de l' [application](xref:host-and-deploy/blazor/index#app-base-path).

> [!IMPORTANT]
> Contrairement au chemin fourni au `href` de la balise `<base>`, n’incluez pas de barre oblique (`/`) quand vous passez la valeur d’argument `--pathbase`. Si vous spécifiez `<base href="/CoolApp/">` (inclut une barre oblique) comme chemin de base de l’application dans la balise `<base>`, passez `--pathbase=/CoolApp` (aucune barre oblique de fin) comme valeur d’argument de ligne de commande.

* Passez l’argument lors de l’exécution de l’application localement à une invite de commandes. À partir du répertoire de l’application, exécutez :

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Ajoutez une entrée au fichier *launchSettings.json* de l’application dans le profil **IIS Express**. Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* Dans Visual Studio, spécifiez l’argument dans **Propriétés** > **Déboguer** > les arguments de l'**application**. Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URLs

L’argument `--urls` définit les adresses IP ou les adresses d’hôtes avec les ports et protocoles sur lesquels il faut écouter les demandes.

* Passez l’argument lors de l’exécution de l’application localement à une invite de commandes. À partir du répertoire de l’application, exécutez :

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Ajoutez une entrée au fichier *launchSettings.json* de l’application dans le profil **IIS Express**. Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Dans Visual Studio, spécifiez l’argument dans **Propriétés** > **Déboguer** > les arguments de l'**application**. Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Configurer l'éditeur de liens

Éblouissant effectue une liaison IL (Intermediate Language) sur chaque version de mise en production pour supprimer l’IL inutile des assemblys de sortie. Pour plus d'informations, consultez <xref:host-and-deploy/blazor/configure-linker>.

## <a name="custom-boot-resource-loading"></a>Chargement des ressources de démarrage personnalisé

Une application de webassembly éblouissant peut être initialisée avec `loadBootResource` la fonction pour remplacer le mécanisme de chargement des ressources de démarrage intégré. Utilisez `loadBootResource` pour les scénarios suivants :

* Autorisez les utilisateurs à charger des ressources statiques, telles que des données de fuseau horaire ou *dotnet. WASM* à partir d’un CDN.
* Chargez les assemblys compressés à l’aide d’une requête HTTP et décompressez-les sur le client pour les hôtes qui ne prennent pas en charge l’extraction du contenu compressé à partir du serveur.
* Alias des ressources à un autre nom en redirigeant `fetch` chaque requête vers un nouveau nom.

`loadBootResource`les paramètres s’affichent dans le tableau suivant.

| Paramètre    | Description |
| ------------ | ----------- |
| `type`       | Type de la ressource. Types permissables : `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`,`timezonedata` |
| `name`       | Nom de la ressource. |
| `defaultUri` | URI relatif ou absolu de la ressource. |
| `integrity`  | Chaîne d’intégrité représentant le contenu attendu dans la réponse. |

`loadBootResource`retourne l’un des éléments suivants pour remplacer le processus de chargement :

* Chaîne d’URI. Dans l’exemple suivant (*wwwroot/index.html*), les fichiers suivants sont pris en charge à partir `https://my-awesome-cdn.com/`d’un CDN à l’adresse :

  * *dotnet. \*. js*
  * *dotnet. WASM*
  * Données de fuseau horaire

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* `Promise<Response>`. Transmettez `integrity` le paramètre dans un en-tête pour conserver le comportement de contrôle d’intégrité par défaut.

  L’exemple suivant (*wwwroot/index.html*) ajoute un en-tête HTTP personnalisé aux demandes sortantes et passe `integrity` le paramètre à l' `fetch` appel :
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* `null`/`undefined`, ce qui entraîne le comportement de chargement par défaut.

Les sources externes doivent retourner les en-têtes CORS requis pour les navigateurs afin d’autoriser le chargement des ressources Cross-Origin. Les CDN fournissent généralement les en-têtes requis par défaut.

Il vous suffit de spécifier des types pour les comportements personnalisés. Les types non spécifiés à sont chargés par le Framework en fonction de leurs comportements de `loadBootResource` chargement par défaut.

## <a name="change-the-filename-extension-of-dll-files"></a>Modifier l’extension de nom de fichier des fichiers DLL

Si vous avez besoin de modifier les extensions de nom de fichier des fichiers *. dll* publiés de l’application, suivez les instructions de cette section.

Après avoir publié l’application, utilisez un script d’interpréteur de commandes ou un pipeline de build DevOps pour renommer les fichiers *. dll* afin d’utiliser une extension de fichier différente. Ciblez les fichiers *. dll* dans le répertoire *wwwroot* de la sortie publiée de l’application (par exemple, *{root content}/bin/Release/netstandard2.1/Publish/wwwroot*).

Dans les exemples suivants, les fichiers *. dll* sont renommés pour utiliser l’extension de fichier *. bin* .

Sur Windows :

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

Sur Linux ou macOS :

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```
   
Pour utiliser une extension de fichier différente de *. bin*, remplacez *. bin* dans les commandes précédentes.

Pour adresser les fichiers *éblouissant. Boot. JSON. gz* et *blazor.Boot.JSON.br* compressés, adoptez l’une des approches suivantes :

* Supprimez les fichiers *éblouissant. Boot. JSON. gz* et *blazor.Boot.JSON.br* compressés. La compression est désactivée avec cette approche.
* Recompressez le fichier *éblouissant. Boot. JSON* mis à jour.

L’exemple Windows suivant utilise un script PowerShell placé à la racine du projet.

*ChangeDLLExtensions. ps1 :*:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Dans le fichier projet, le script est exécuté après la publication de l’application :

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

Pour nous faire part de vos commentaires, visitez [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).
