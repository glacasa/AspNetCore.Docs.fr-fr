## <a name="grpc-not-supported-on-azure-app-service"></a>gRPC non pris en charge sur Azure App Service

> [!WARNING]
> [ASP.NET Core gRPC](xref:grpc/index) n’est pas actuellement pris en charge sur Azure App Service ou IIS. La mise en œuvre HTTP/2 de Http.Sys ne prend pas en charge les en-têtes de suivi de réponse HTTP sur lesquels gRPC s’appuie. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/9020).
