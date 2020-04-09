## <a name="debug-diagnostics"></a><span data-ttu-id="f578d-101">Diagnostics de débbug</span><span class="sxs-lookup"><span data-stu-id="f578d-101">Debug diagnostics</span></span>

<span data-ttu-id="f578d-102">Pour la sortie diagnostique `Logging:LogLevel:Microsoft` détaillée `Debug`de routage, réglé à .</span><span class="sxs-lookup"><span data-stu-id="f578d-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="f578d-103">Par exemple, dans l’environnement de développement, *définissez des applications. Development.json*:</span><span class="sxs-lookup"><span data-stu-id="f578d-103">For example, in the development environment, set *appsettings.Development.json*:</span></span>

```JSON
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}