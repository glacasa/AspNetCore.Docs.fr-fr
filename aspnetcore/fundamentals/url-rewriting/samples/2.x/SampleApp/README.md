# <a name="aspnet-core-url-rewriting-sample"></a>Exemple de réécriture d’URL ASP.NET Core

Cet exemple illustre l’utilisation de l’intergiciel (middleware) de réécriture d’URL ASP.NET Core. L’application montre les options de redirection et de réécriture d’URL.

Quand vous exécutez l’exemple, des réponses autres que des réponses de fichier retournent l’URL réécrite ou redirigée quand une des règles est appliquée à une URL de requête. Pour les exemples de fichier XML et texte, le middleware de fichiers statiques délivre le fichier une fois qu’il a réécrit l’URL de requête.

## <a name="examples-in-this-sample"></a>Exemples dans cet exemple

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - Code d’état de réussite : 302 (Trouvé)
  - Exemple (redirection) : **/redirect-rule/{capture_group}** en **/redirected/{capture_group}**
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - Code d’état de réussite : 200 (OK)
  - Exemple (réécriture) : **/rewrite-rule/{capture_group_1}/{capture_group_2}** en **/rewritten?var1={capture_group_1}&var2={capture_group_2}**
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - Code d’état de réussite : 302 (Trouvé)
  - Exemple (redirection) : **/apache-mod-rules-redirect/{capture_group}** en **/redirected?id={capture_group}**
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - Code d’état de réussite : 200 (OK)
  - Exemple (réécriture) : **/iis-rules-rewrite/{capture_group}** en **/rewritten?id={capture_group}**
* `Add(RedirectXmlFileRequests)`
  - Code d’état de réussite : 301 (déplacé de façon permanente)
  - Exemple (redirection) : **/file.xml** en **/xmlfiles/file.xml**
* `Add(RewriteTextFileRequests)`
  - Code d’état de réussite : 200 (OK)
  - Exemple (réécriture) : **/some_file.txt** en **/file.txt**
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - Code d’état de réussite : 301 (déplacé de façon permanente)
  - Exemple (redirection) : **/image.png** en **/png-images/image.png**
  - Exemple (redirection) : **/image.jpg** en **/jpg-images/image.jpg**

## <a name="use-a-physicalfileprovider"></a>Utiliser un PhysicalFileProvider

Vous pouvez également obtenir un `IFileProvider` en créant un `PhysicalFileProvider` qui est ensuite passé aux méthodes `AddApacheModRewrite()` et `AddIISUrlRewrite()` :

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a>Extensions de redirection sécurisées

Pour faciliter l’exploration des méthodes de redirection sécurisées, cet exemple présente la configuration de `WebHostBuilder` pour que l’application utilise des URL (`https://localhost:5001`, `https://localhost`) et un certificat de test (*testCert.pfx*). Si le port 443 est déjà affecté ou utilisé sur le serveur, l’exemple `https://localhost` ne fonctionne pas &mdash; supprimez `ListenOptions` pour le port 443 dans la méthode `CreateWebHostBuilder` du fichier *Program.cs* ou annulez la liaison du port 443 sur le serveur, pour que Kestrel puisse utiliser le port.

| Méthode                           | Code d’état |    Port    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     301     | Null (465) |
| `.AddRedirectToHttps()`          |     302     | Null (465) |
| `.AddRedirectToHttps(301)`       |     301     | Null (465) |
| `.AddRedirectToHttps(301, 5001)` |     301     |    5001    |
