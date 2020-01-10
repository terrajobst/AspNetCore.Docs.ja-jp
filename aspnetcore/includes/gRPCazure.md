## <a name="grpc-not-supported-on-azure-app-service"></a>gRPC は Azure App Service ではサポートされていません

> [!WARNING]
> 現在、[ASP.NET Core gRPC](xref:grpc/index) は Azure App Service または IIS 上でサポートされていません。 Http.Sys の HTTP/2 実装では、gRPC が依存する HTTP 応答の末尾のヘッダーがサポートされていません。 詳細については、次を参照してください。[この GitHub の問題](https://github.com/dotnet/AspNetCore/issues/9020)します。
