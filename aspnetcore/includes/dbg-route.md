## <a name="debug-diagnostics"></a>Diagnostics de débogage

Pour une sortie de diagnostic de routage `Logging:LogLevel:Microsoft` détaillée `Debug`, affectez à la valeur. Par exemple, dans l’environnement de développement, définissez *appSettings. Development. JSON*:

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