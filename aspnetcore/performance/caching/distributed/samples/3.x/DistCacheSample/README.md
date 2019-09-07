# <a name="aspnet-core-distributed-cache-sample"></a>分散キャッシュサンプルの ASP.NET Core

このサンプルでは、分散キャッシュの使用方法を示します。 このサンプルでは、 [ASP.NET Core トピックの「分散キャッシュの使用](https://docs.microsoft.com/aspnet/core/performance/caching/distributed)」で説明されているシナリオを示します。

運用環境では、サンプルアプリは分散 SQL Server キャッシュを使用するように構成されています。 分散 Redis cache を使用するようにアプリを再構成するには、 *Startup.cs*ファイルの先頭にあるプリプロセッサディレクティブを`#define Redis // SQLServer`、redis () を使用するように変更します。 詳細については、[サンプルコードの「プリプロセッサディレクティブ](https://docs.microsoft.com/aspnet/core/#preprocessor-directives-in-sample-code)」を参照してください。
