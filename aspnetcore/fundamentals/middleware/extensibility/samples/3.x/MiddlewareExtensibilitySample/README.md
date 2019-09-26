# <a name="aspnet-core-middleware-extensibility-sample"></a>ASP.NET Core のミドルウェアの拡張性の例

このサンプルでは、「[ASP.NET Core でのファクトリ ベースのミドルウェアのアクティブ化](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility)」で説明されているシナリオについて説明します。

このサンプル アプリでは、次でアクティブ化されるミドルウェアを示します。

* 規則。 従来のミドルウェアのアクティブ化については、「[ミドルウェア](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/)」のトピックを参照してください。
* [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) の実装。 既定の [IMiddlewareFactory クラス](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory)によってミドルウェアはアクティブ化されます。

ミドルウェアの実装は同一に機能し、クエリ文字列パラメーターで提供された値を記録します (`key`)。 ミドルウェアは、メモリ内データベースにクエリ文字列値を記録するのに、挿入されたデータベース コンテキスト (スコープ化されたサービス) を使用します。
