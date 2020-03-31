## <a name="debug-diagnostics"></a>デバッグ診断

詳細なルーティング診断出力を行うには、`Logging:LogLevel:Microsoft` を `Debug` に設定してください。 たとえば、開発環境では、*appsettings.Development.json* を次のように設定します。

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