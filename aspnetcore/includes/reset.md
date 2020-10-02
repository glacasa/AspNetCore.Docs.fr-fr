La réinitialisation permet au serveur de réinitialiser une requête HTTP/2 avec un code d’erreur spécifié. Une demande de réinitialisation est considérée comme abandonnée.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` dans l’exemple de code précédent, spécifie le `INTERNAL_ERROR` code d’erreur. Pour plus d’informations sur les codes d’erreur HTTP/2, consultez la [section Code d’erreur de la spécification http/2](https://tools.ietf.org/html/rfc7540#page-50).