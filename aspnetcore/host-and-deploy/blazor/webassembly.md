---
title: Hébergez et déployez ASP.NET WebAssembly Core Blazor
author: guardrex
description: Découvrez comment héberger Blazor et déployer une application à l’aide de ASP.NET Core, Content Delivery Networks (CDN), de serveurs de fichiers et de pages GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: f364d94085d175fde5596c222ef21852c0106ec1
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751121"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a>Hébergez et déployez ASP.NET WebAssembly Core Blazor

Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Avec le [ Blazor modèle d’hébergement WebAssembly](xref:blazor/hosting-models#blazor-webassembly):

* L’application, Blazor ses dépendances et le temps d’exécution .NET sont téléchargés sur le navigateur.
* L’application est exécutée directement sur le thread d’interface utilisateur du navigateur.

Les stratégies de déploiement suivantes sont prises en charge :

* L’application Blazor est desservie par une application ASP.NET Core. Cette stratégie est abordée dans la section [Déploiement hébergé avec ASP.NET Core](#hosted-deployment-with-aspnet-core).
* L’application Blazor est placée sur un serveur ou un service Web d’hébergement statique, où .NET n’est pas utilisé pour servir l’application. Blazor Cette stratégie est couverte dans la section de déploiement Blazor [Standalone,](#standalone-deployment) qui comprend des informations sur l’hébergement d’une application WebAssembly sous forme de sous-application IIS.

## <a name="rewrite-urls-for-correct-routing"></a>Réécriture d’URL pour un routage correct

Le routage des demandes Blazor de composants de page dans une application WebAssembly n’est pas aussi simple que les demandes de routage dans une Blazor application hébergée Server. Envisagez Blazor une application WebAssembly avec deux composants :

* *Main.razor* &ndash; Se charge à la racine de l’application et contient un lien vers le composant `About` (`href="About"`).
* Composant *About.Razor* &ndash; `About`.

Quand le document par défaut de l’application est demandé à l’aide de la barre d’adresses du navigateur (par exemple, `https://www.contoso.com/`) :

1. Le navigateur effectue une requête.
1. La page par défaut est retournée, généralement *index.html*.
1. *index.HTML* amorce l’application.
1. Blazor's routeur charges, et `Main` le composant Razor est rendu.

Dans la page Principale, la `About` sélection du lien vers Blazor le composant fonctionne sur le client `www.contoso.com` parce `About` que le `About` routeur empêche le navigateur de faire une demande sur Internet pour et sert le composant rendu lui-même. Toutes les demandes de points de terminaison internes *dans l’application Blazor WebAssembly* fonctionnent de la même manière : les demandes ne déclenchent pas de demandes basées sur le navigateur vers des ressources hébergées par le serveur sur Internet. Le routeur gère les requêtes en interne.

Si une requête pour `www.contoso.com/About` est effectuée à l’aide de la barre d’adresses du navigateur, elle échoue. Comme cette ressource n’existe pas sur l’hôte Internet de l’application, une réponse *404 – Non trouvé* est retournée.

Étant donné que les navigateurs envoient des requêtes aux hôtes basés sur Internet pour des pages côté client, les serveurs web et les services d’hébergement doivent réécrire toutes les requêtes pour les ressources qui ne se trouvent pas physiquement sur le serveur afin qu’elles pointent vers la page *index.html*. Lorsque *index.html* est retourné, Blazor le routeur de l’application prend le relais et répond avec la bonne ressource.

Lors du déploiement sur un serveur IIS, vous pouvez utiliser le module de réécriture d’URL avec le fichier *web.config* publié par l’application. Pour plus d’informations, consultez la section [IIS.](#iis)

## <a name="hosted-deployment-with-aspnet-core"></a>Déploiement hébergé avec ASP.NET Core

Un *déploiement hébergé* Blazor sert l’application WebAssembly aux navigateurs à partir d’une [application ASP.NET Core](xref:index) qui s’exécute sur un serveur Web.

L’application WebAssembly client Blazor est publiée dans le dossier */bin/Release/TARGET FRAMEWORK/publish/wwwroot* de l’application serveur, ainsi que tous les autres actifs Web statiques de l’application serveur. Les deux applications sont déployées ensemble. Un serveur web capable d’héberger une application ASP.NET Core est nécessaire. Pour un déploiement hébergé, Visual Studio inclut le modèle`blazorwasm` de`-ho|--hosted` `dotnet new` ** Blazor projet WebAssembly App** (modèle lors de l’utilisation de la nouvelle commande [dotnet)](/dotnet/core/tools/dotnet-new) avec **l’option Hébergée** sélectionnée (lors de l’utilisation de la commande).

Pour plus d’informations sur l’hébergement et le déploiement d’applications ASP.NET Core, consultez <xref:host-and-deploy/index>.

Pour plus d’informations concernant le déploiement sur Azure App Service, consultez <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="standalone-deployment"></a>Déploiement autonome

Un *déploiement autonome* sert Blazor l’application WebAssembly comme un ensemble de fichiers statiques qui sont demandés directement par les clients. N’importe quel serveur de Blazor fichiers statique est en mesure de servir l’application.

Les actifs de déploiement autonomes sont publiés dans le *dossier /bin/Release/TARGET FRAMEWORKMD/publish/wwwroot.*

### <a name="iis"></a>IIS

IIS est un serveur Blazor de fichiers statique capable pour les applications. Pour configurer IIS pour héberger, Blazorvoir Construire un site Statique sur [IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Les ressources publiées sont créées dans le dossier */bin/Release/{FRAMEWORK CIBLE}/publish*. Hébergez le contenu du dossier *publish* sur le serveur web ou le service d’hébergement.

#### <a name="webconfig"></a>web.config

Lorsqu’un Blazor projet est publié, un fichier *web.config* est créé avec la configuration IIS suivante :

* Les types MIME sont définis pour les extensions de fichiers suivantes :
  * *.dll* &ndash;`application/octet-stream`
  * *.json (json)* &ndash;`application/json`
  * *.wasm* &ndash;`application/wasm`
  * *.woff* &ndash;`application/font-woff`
  * *.woff2* &ndash;`application/font-woff`
* La compression HTTP est activée pour les types MIME suivants :
  * `application/octet-stream`
  * `application/wasm`
* Des règles du module de réécriture d’URL sont établies :
  * Servez le sous-répertoire où résident les actifs statiques de l’application *(wwwroot/'PATH REQUESTED').*
  * Créez un itinéraire de repli SPA de sorte que les demandes d’actifs autres que les fichiers soient redirigées vers le document par défaut de l’application dans son dossier d’actifs statiques *(wwwroot/index.html*).
  
#### <a name="use-a-custom-webconfig"></a>Utilisez un web.config personnalisé

Pour utiliser un fichier *web.config* personnalisé :

1. Placez le fichier *web.config* personnalisé à la racine du dossier de projet.
1. Ajoutez la cible suivante au fichier du projet (*.csproj*) :

   ```xml
   <Target Name="CopyWebConfigOnPublish" AfterTargets="Publish">
     <Copy SourceFiles="web.config" DestinationFolder="$(PublishDir)" />
   </Target>
   ```
   
> [!NOTE]
> L’utilisation de la `<IsWebConfigTransformDisabled>` propriété `true` MSBuild Blazor mis à n’est pas pris en charge dans les applications WebAssembly [comme il est pour ASP.NET applications Core déployées à IIS](xref:host-and-deploy/iis/index#webconfig-file). Pour plus d’informations, voir [La cible Copy requise pour fournir wasM web.config personnalisé Blazor (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).

#### <a name="install-the-url-rewrite-module"></a>Installer le module de réécriture d’URL

Le [module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite) est nécessaire pour réécrire les URL. Il n’est pas installé par défaut, et ne peut pas l’être en tant que fonctionnalité de service de rôle Serveur Web (IIS). Vous devez le télécharger à partir du site web IIS. Utilisez Web Platform Installer pour installer le module :

1. Localement, accédez à la [page des téléchargements du Module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Pour la version anglaise, sélectionnez **WebPI** pour télécharger le programme d’installation WebPI. Pour les autres langues, sélectionnez l’architecture appropriée pour le serveur (x86/x64) afin de télécharger le programme d’installation.
1. Copiez le programme d’installation sur le serveur. Exécutez le programme d’installation. Sélectionnez le bouton **Installer** et acceptez les termes du contrat de licence. Il n’est pas nécessaire de redémarrer le serveur après l’installation.

#### <a name="configure-the-website"></a>Configurer le site web

Affectez le dossier de l’application comme **chemin physique** du site web. Le dossier contient les éléments suivants :

* Le fichier *web.config* utilisé par IIS pour configurer le site web, notamment les règles de redirection nécessaires et les types de contenu de fichiers
* Le dossier de ressources statiques de l’application

#### <a name="host-as-an-iis-sub-app"></a>Hôte en tant que sous-application IIS

Si une application autonome est hébergée sous forme de sous-application IIS, effectuez l’une ou l’autre des applications suivantes :

* Désactiver le gestionnaire hérité ASP.NET module de base.

  Supprimer le gestionnaire Blazor dans le fichier *web.config* `<handlers>` publié par l’application en ajoutant une section au fichier :

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Désactiver l’héritage de la section `<system.webServer>` de l’application `inheritInChildApplications` racine `false`(parent) à l’aide d’un `<location>` élément avec ensemble à :

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

La suppression du gestionnaire ou l’héritage invalidant est effectuée en plus de [configurer le chemin de base de l’application](xref:host-and-deploy/blazor/index#app-base-path). Dans le fichier *index.html* de l’application, définissez le chemin de base de l’application sur l’alias IIS utilisé lors de la configuration de la sous-application dans IIS.

#### <a name="troubleshooting"></a>Dépannage

Si vous recevez un message *500 – Erreur interne du serveur* et que le Gestionnaire IIS lève des erreurs quand vous tentez d’accéder à la configuration du site web, vérifiez que le module de réécriture d’URL est installé. Quand le module n’est pas installé, le fichier *web.config* ne peut pas être analysé par IIS. Cela empêche le gestionnaire de l’IIS de charger Blazorla configuration du site Web et le site Web de servir les fichiers statiques de l’IIS.

Pour plus d’informations sur le dépannage des déploiements sur IIS, consultez <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Stockage Azure

[Azure Storage](/azure/storage/) static file Blazor hosting permet l’hébergement d’applications sans serveur. Les noms de domaine personnalisé, le réseau de distribution de contenu Azure (CDN) et HTTPS sont pris en charge.

Lorsque le service blob est activé pour l’hébergement de site Web statique sur un compte de stockage :

* Définissez le **nom du document d’index** sur `index.html`.
* Définissez le **chemin d’accès au document d’erreur** sur `index.html`. Les composants de Razor et autres points de terminaison non-fichier ne se trouvent sur les chemins d’accès physiques dans le contenu statique stocké par le service blob. Lorsqu’une demande pour l’une Blazor de ces ressources est reçue que le routeur doit gérer, l’erreur *404 - Non trouvé* générée par le service blob itinéraires de la demande à la **voie de document d’erreur**. Le blob *index.html* est Blazor retourné, et le routeur charge et traite le chemin.

Pour plus d’informations, consultez [Hébergement de sites web statiques dans le service Stockage Azure](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Le fichier *nginx.conf* suivant est simplifié pour montrer comment configurer Nginx pour envoyer le fichier *index.html* chaque fois qu’il ne peut pas trouver un fichier correspondant sur le disque.

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

Pour Blazor accueillir dans Docker en utilisant Nginx, configurez le Dockerfile pour utiliser l’image Nginx basée sur alpine. Mettez à jour le fichier Dockerfile pour copier le fichier *nginx.config* dans le conteneur.

Ajoutez une ligne au fichier Dockerfile, comme indiqué dans l’exemple suivant :

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Pour déployer Blazor une application WebAssembly sur CentOS 7 ou plus tard :

1. Créez le fichier de configuration Apache. L’exemple suivant est un fichier de configuration simplifié (*blazorapp.config*) :

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

1. Placez le fichier de `/etc/httpd/conf.d/` configuration Apache dans l’annuaire, qui est le répertoire de configuration Apache par défaut dans CentOS 7.

1. Placez les fichiers de `/var/www/blazorapp` l’application dans `DocumentRoot` l’annuaire (l’emplacement spécifié dans le fichier de configuration).

1. Redémarrer le service Apache.

Pour plus d’informations, voir [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) et [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>GitHub Pages

Pour gérer les réécritures d’URL, ajoutez un fichier *404.html* avec un script qui gère la redirection de la requête vers la page *index.html*. Pour obtenir un exemple d’implémentation fournie par la communauté, consultez [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (Applications à une seule page pour GitHub Pages) ([rafrex/spa-github-pages sur GitHub](https://github.com/rafrex/spa-github-pages#readme)). Vous pouvez obtenir un exemple d’utilisation de cette approche à l’adresse [blazor-demo/blazor-demo.github.io sur GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site actif](https://blazor-demo.github.io/)).

Quand vous utilisez un site de projet plutôt qu’un site d’entreprise, ajoutez ou mettez à jour la balise `<base>` dans *index.html*. Définissez la valeur d’attribut `href` sur le nom du dépôt GitHub avec une barre oblique (par exemple, `my-repository/`.

## <a name="host-configuration-values"></a>Valeurs de configuration de l’hôte

Les applications WebAssembly peuvent accepter les valeurs de configuration d’hôte suivantes comme arguments de ligne de commande au moment de l’exécution dans l’environnement de développement. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)

### <a name="content-root"></a>Racine de contenu

L’argument `--contentroot` définit le chemin absolu vers l’annuaire qui contient les fichiers de contenu de l’application[(racine](xref:fundamentals/index#content-root)de contenu ). Dans les exemples suivants, `/content-root-path` est le chemin racine du contenu de l’application.

* Passez l’argument lors de l’exécution de l’application localement à une invite de commandes. À partir du répertoire de l’application, exécutez :

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Ajoutez une entrée au fichier *launchSettings.json* de l’application dans le profil **IIS Express**. Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* Dans Visual Studio, spécifiez l’argument dans **Properties** > **Debug** > **Arguments .** Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Base du chemin

`--pathbase` L’argument définit le chemin de base de l’application pour une `<base>` `href` application exécutée localement `/` avec un chemin d’URL relatif non-root (l’étiquette est réglée sur un chemin autre que pour la mise en scène et la production). Dans les exemples suivants, `/relative-URL-path` est la base du chemin de l’application. Pour plus d’informations, voir [le chemin de base de l’application](xref:host-and-deploy/blazor/index#app-base-path).

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

* Dans Visual Studio, spécifiez l’argument dans **Properties** > **Debug** > **Arguments .** Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.

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

* Dans Visual Studio, spécifiez l’argument dans **Properties** > **Debug** > **Arguments .** Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Configurer l'éditeur de liens

Blazoreffectue des liens de langage intermédiaire (IL) sur chaque version de version pour supprimer l’IL inutile des assemblages de sortie. Pour plus d’informations, consultez <xref:host-and-deploy/blazor/configure-linker>.
