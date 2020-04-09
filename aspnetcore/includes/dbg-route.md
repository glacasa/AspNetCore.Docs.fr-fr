## <a name="debug-diagnostics"></a>Diagnostics de débbug

Pour la sortie diagnostique `Logging:LogLevel:Microsoft` détaillée `Debug`de routage, réglé à . Par exemple, dans l’environnement de développement, *définissez des applications. Development.json*:

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