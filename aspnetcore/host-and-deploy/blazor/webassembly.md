---
title: Héberger et déployer Blazor ASP.net Core webassembly
author: guardrex
description: Découvrez comment héberger et déployer une Blazor application à l’aide de ASP.net Core, de réseaux de distribution de contenu (CDN), de serveurs de fichiers et de pages github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: daaaab360e93de1cf10feec2db21d3acc25920bd
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110874"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a>Héberger et déployer Blazor ASP.net Core webassembly

Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27)et [Ben Adams](https://twitter.com/ben_a_adams).

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Avec le [ Blazor modèle d’hébergement webassembly](xref:blazor/hosting-models#blazor-webassembly):

* L' Blazor application, ses dépendances et le Runtime .net sont téléchargés dans le navigateur en parallèle.
* L’application est exécutée directement sur le thread d’interface utilisateur du navigateur.

Les stratégies de déploiement suivantes sont prises en charge :

* L' Blazor application est traitée par une application ASP.net core. Cette stratégie est abordée dans la section [Déploiement hébergé avec ASP.NET Core](#hosted-deployment-with-aspnet-core).
* L' Blazor application est placée sur un service ou un serveur Web d’hébergement statique, où .net n’est pas Blazor utilisé pour traiter l’application. Cette stratégie est traitée dans la section [Déploiement autonome](#standalone-deployment) , qui comprend des informations sur l' Blazor hébergement d’une application webassembly en tant que sous-application IIS.

## <a name="brotli-precompression"></a>Précompression Brotli

Lors de Blazor la publication d’une application webassembly, la sortie est précompressée à l’aide de l' [algorithme de compression Brotli](https://tools.ietf.org/html/rfc7932) au niveau le plus élevé afin de réduire la taille de l’application et de la nécessité de compresser le Runtime.

Pour la configuration de la compression IIS *Web. config* , consultez la section [IIS : Brotli et compression gzip](#brotli-and-gzip-compression) .

## <a name="rewrite-urls-for-correct-routing"></a>Réécriture d’URL pour un routage correct

Les requêtes de routage pour les composants Blazor de page dans une application webassembly ne sont pas Blazor aussi simples que les demandes de routage dans un serveur, une application hébergée. Prenons l' Blazor exemple d’une application webassembly avec deux composants :

* *Main.razor* &ndash; Se charge à la racine de l’application et contient un lien vers le composant `About` (`href="About"`).
* Composant *About.Razor* &ndash; `About`.

Quand le document par défaut de l’application est demandé à l’aide de la barre d’adresses du navigateur (par exemple, `https://www.contoso.com/`) :

1. Le navigateur effectue une requête.
1. La page par défaut est retournée, généralement *index.html*.
1. *index.HTML* amorce l’application.
1. Blazorle routeur est chargé et le composant `Main` Razor est rendu.

Dans la page principale, le fait de sélectionner le `About` lien vers le composant fonctionne sur le Blazor client car le routeur empêche le navigateur d’effectuer une requête sur `www.contoso.com` Internet `About` pour et sert le `About` composant rendu lui-même. Toutes les demandes pour les points de terminaison internes de *l' Blazor application webassembly* fonctionnent de la même façon : les demandes ne déclenchent pas de requêtes basées sur un navigateur vers des ressources hébergées sur le serveur sur Internet. Le routeur gère les requêtes en interne.

Si une requête pour `www.contoso.com/About` est effectuée à l’aide de la barre d’adresses du navigateur, elle échoue. Comme cette ressource n’existe pas sur l’hôte Internet de l’application, une réponse *404 – Non trouvé* est retournée.

Étant donné que les navigateurs envoient des requêtes aux hôtes basés sur Internet pour des pages côté client, les serveurs web et les services d’hébergement doivent réécrire toutes les requêtes pour les ressources qui ne se trouvent pas physiquement sur le serveur afin qu’elles pointent vers la page *index.html*. Quand *index. html* est retourné, le routeur de Blazor l’application prend le relais et répond avec la ressource correcte.

Lors du déploiement sur un serveur IIS, vous pouvez utiliser le module de réécriture d’URL avec le fichier *Web. config* publié de l’application. Pour plus d’informations, consultez la section [IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Déploiement hébergé avec ASP.NET Core

Un *déploiement hébergé* sert l' Blazor application webassembly aux navigateurs à partir d’une [application ASP.net Core](xref:index) qui s’exécute sur un serveur Web.

L’application Blazor webassembly cliente est publiée dans le dossier */bin/Release/{Target Framework}/Publish/wwwroot* de l’application serveur, ainsi que les autres ressources Web statiques de l’application serveur. Les deux applications sont déployées ensemble. Un serveur web capable d’héberger une application ASP.NET Core est nécessaire. Pour un déploiement hébergé, Visual Studio comprend`-ho|--hosted` le `dotnet new` `blazorwasm` [dotnet new](/dotnet/core/tools/dotnet-new) **Hosted** ** Blazor modèle de projet d’application webassembly** (modèle lors de l’utilisation de la commande dotnet New) avec l’option hébergée sélectionnée (lors de l’utilisation de la commande).

Pour plus d’informations sur l’hébergement et le déploiement d’applications ASP.NET Core, consultez <xref:host-and-deploy/index>.

Pour plus d’informations concernant le déploiement sur Azure App Service, consultez <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="standalone-deployment"></a>Déploiement autonome

Un *Déploiement autonome* sert l' Blazor application webassembly sous la forme d’un ensemble de fichiers statiques demandés directement par les clients. Tout serveur de fichiers statique peut traiter l' Blazor application.

Les ressources de déploiement autonomes sont publiées dans le dossier */bin/Release/{Target Framework}/Publish/wwwroot* .

### <a name="iis"></a>IIS

IIS est un serveur de fichiers statiques Blazor et puissant pour les applications. Pour configurer IIS pour héberger Blazor, consultez [créer un site Web statique sur IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Les ressources publiées sont créées dans le dossier */bin/Release/{FRAMEWORK CIBLE}/publish*. Hébergez le contenu du dossier *publish* sur le serveur web ou le service d’hébergement.

#### <a name="webconfig"></a>web.config

Lorsqu’un Blazor projet est publié, un fichier *Web. config* est créé avec la configuration IIS suivante :

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

  Supprimez le gestionnaire dans Blazor le fichier *Web. config* publié de l’application en `<handlers>` ajoutant une section au fichier :

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

IIS peut être configuré par le biais de *Web. config* pour servir les Blazor ressources compressées Brotli ou gzip. Pour obtenir un exemple de configuration, consultez [Web. config](webassembly/_samples/web.config?raw=true).

#### <a name="troubleshooting"></a>Dépannage

Si vous recevez un message *500 – Erreur interne du serveur* et que le Gestionnaire IIS lève des erreurs quand vous tentez d’accéder à la configuration du site web, vérifiez que le module de réécriture d’URL est installé. Quand le module n’est pas installé, le fichier *web.config* ne peut pas être analysé par IIS. Cela empêche le gestionnaire des services Internet de charger la configuration du site Web et le Blazorsite Web à partir des fichiers statiques de service.

Pour plus d’informations sur le dépannage des déploiements sur IIS, consultez <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Stockage Azure

L’hébergement de fichiers statiques [Azure Storage](/azure/storage/) permet l’hébergement d' Blazor applications sans serveur. Les noms de domaine personnalisé, le réseau de distribution de contenu Azure (CDN) et HTTPS sont pris en charge.

Lorsque le service blob est activé pour l’hébergement de site Web statique sur un compte de stockage :

* Définissez le **nom du document d’index** sur `index.html`.
* Définissez le **chemin d’accès au document d’erreur** sur `index.html`. Les composants de Razor et autres points de terminaison non-fichier ne se trouvent sur les chemins d’accès physiques dans le contenu statique stocké par le service blob. Lorsqu’une demande pour l’une de ces ressources est reçue que Blazor le routeur doit gérer, l’erreur *404-introuvable* générée par le service BLOB achemine la requête vers le **chemin du document d’erreur**. L’objet BLOB *index. html* est retourné et le Blazor routeur charge et traite le chemin d’accès.

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

Pour héberger Blazor dans l’ancrage à l’aide de nginx, configurez le fichier dockerfile pour utiliser l’image Nginx alpine. Mettez à jour le fichier Dockerfile pour copier le fichier *nginx.config* dans le conteneur.

Ajoutez une ligne au fichier Dockerfile, comme indiqué dans l’exemple suivant :

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Pour déployer une Blazor application webassembly sur CentOS 7 ou version ultérieure :

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

Les applications webassembly peuvent accepter les valeurs de configuration d’hôte suivantes comme arguments de ligne de commande au moment de l’exécution dans l’environnement de développement. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)

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

Blazoreffectue une liaison IL (Intermediate Language) sur chaque version de mise en production pour supprimer l’IL inutile des assemblys de sortie. Pour plus d’informations, consultez <xref:host-and-deploy/blazor/configure-linker>.
