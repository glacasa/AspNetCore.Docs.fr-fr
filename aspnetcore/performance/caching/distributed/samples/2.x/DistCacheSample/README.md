# <a name="aspnet-core-distributed-cache-sample"></a>Exemple de cache distribué ASP.NET Core

Cet exemple illustre l’utilisation d’un cache distribué. Cet exemple illustre le scénario décrit dans la rubrique [utilisation d’un cache distribué dans ASP.net Core](https://docs.microsoft.com/aspnet/core/performance/caching/distributed) .

Dans l’environnement de production, l’exemple d’application est configuré pour utiliser un cache de SQL Server distribué. Pour reconfigurer l’application afin qu’elle utilise un cache Redims distribué, modifiez la directive de préprocesseur en haut du fichier *Startup.cs* de façon à utiliser des ReDim ( `#define Redis // SQLServer` ). Pour plus d’informations, consultez [directives de préprocesseur dans l’exemple de code](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core#preprocessor-directives-in-sample-code).
