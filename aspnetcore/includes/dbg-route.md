## <a name="debug-diagnostics"></a><span data-ttu-id="59ada-101">Diagnostics de débogage</span><span class="sxs-lookup"><span data-stu-id="59ada-101">Debug diagnostics</span></span>

<span data-ttu-id="59ada-102">Pour une sortie de diagnostic de routage `Logging:LogLevel:Microsoft` détaillée `Debug`, affectez à la valeur.</span><span class="sxs-lookup"><span data-stu-id="59ada-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="59ada-103">Par exemple, dans l’environnement de développement, définissez *appSettings. Development. JSON*:</span><span class="sxs-lookup"><span data-stu-id="59ada-103">For example, in the development environment, set *appsettings.Development.json*:</span></span>

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