## <a name="debug-diagnostics"></a>Diagnostics de débogage

Pour une sortie de diagnostic de routage détaillée, affectez à la valeur `Logging:LogLevel:Microsoft` `Debug` . Dans l’environnement de développement, définissez le niveau de journalisation dans *appsettings.Development.jssur*:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
