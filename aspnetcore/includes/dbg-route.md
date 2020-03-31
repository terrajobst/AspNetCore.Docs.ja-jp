## <a name="debug-diagnostics"></a><span data-ttu-id="9eb6b-101">デバッグ診断</span><span class="sxs-lookup"><span data-stu-id="9eb6b-101">Debug diagnostics</span></span>

<span data-ttu-id="9eb6b-102">詳細なルーティング診断出力を行うには、`Logging:LogLevel:Microsoft` を `Debug` に設定してください。</span><span class="sxs-lookup"><span data-stu-id="9eb6b-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="9eb6b-103">たとえば、開発環境では、*appsettings.Development.json* を次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="9eb6b-103">For example, in the development environment, set *appsettings.Development.json*:</span></span>

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