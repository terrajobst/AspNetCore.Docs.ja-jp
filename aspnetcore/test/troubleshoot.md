---
title: ASP.NET Core プロジェクトのトラブルシューティングとデバッグ
author: Rick-Anderson
description: ASP.NET Core プロジェクトでの警告とエラーについて説明し、トラブルシューティングを行います。
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 345967f08cf99ef5f18d0c9bcd59ab29c74454f1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511510"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>ASP.NET Core プロジェクトのトラブルシューティングとデバッグ

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

次のリンクでは、トラブルシューティングのガイダンスが提供されています。

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC カンファレンス (2018 年、ロンドン): ASP.NET Core アプリケーションでの問題を診断する](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET ブログ: ASP.NET Core のパフォーマンスに関する問題のトラブルシューティング](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>.NET Core SDK の警告

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>32 ビットと 64 ビットの両方のバージョンの .NET Core SDK がインストールされています

ASP.NET Core の **[新しいプロジェクト]** ダイアログに、次の警告が表示される場合があります。

> 32 ビットと 64 ビットの両方のバージョンの .NET Core SDK がインストールされています。 'C:\\Program Files\\dotnet\\sdk\\' にインストールされている 64 ビット バージョンのテンプレートのみ表示されます。

この警告は、32 ビット (x86) と 64 ビット (x64) の両方のバージョンの [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) がインストールされている場合に表示されます。 両方のバージョンがインストールされる可能性のある一般的な理由は次のとおりです。

* 最初は、32 ビットのコンピューターを使用して .NET Core SDK インストーラーをダウンロードした後、それを 64 ビットのコンピューターにコピーしてインストールしました。
* 32 ビットの .NET Core SDK が別のアプリケーションによってインストールされました。
* 正しくないバージョンがダウンロードされ、インストールされました。

この警告を回避するには、32 ビットの .NET Core SDK をアンインストールします。 **[コントロール パネル]**  >  **[プログラムと機能]**  >  **[プログラムのアンインストールまたは変更]** からアンインストールします。 警告が発生する理由とその影響について理解している場合は、この警告を無視できます。

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>.NET Core SDK は、複数の場所にインストールされます

ASP.NET Core の **[新しいプロジェクト]** ダイアログに、次の警告が表示される場合があります。

> .NET Core SDK は、複数の場所にインストールされます。 'C:\\Program Files\\dotnet\\sdk\\' にインストールされた SDK からのテンプレートのみ表示されます。

このメッセージが表示されるのは、.NET Core SDK のインストールが少なくとも 1 つ、*C:\\Program Files\\dotnet\\sdk\\* の外部にあるディレクトリに存在する場合です。 通常、これは、MSI インストーラーではなく、コピーと貼り付けを使用して、.NET Core SDK がコンピューターに展開されている場合に発生します。

この警告を回避するには、32 ビットの .NET Core SDK とランタイムをすべてアンインストールします。 **[コントロール パネル]**  >  **[プログラムと機能]**  >  **[プログラムのアンインストールまたは変更]** からアンインストールします。 警告が発生する理由とその影響について理解している場合は、この警告を無視できます。

### <a name="no-net-core-sdks-were-detected"></a>.NET Core SDK が検出されませんでした

* Visual Studio の ASP.NET Core 用の **[新しいプロジェクト]** ダイアログに、次の警告が表示される場合があります。

  > .NET Core SDK が検出されませんでした。それらを確実に環境変数 `PATH` に含めてください。

* `dotnet` コマンドを実行すると、次のような警告が表示されます。

  > インストールされた dotnet SDK が見つかりませんでした。

これらの警告は、環境変数 `PATH` が、コンピューター上の .NET Core SDK をポイントしていない場合に表示されます。 これを解決するには、次の手順に従います。

* .NET Core SDK をインストールします。 [.NET ダウンロード](https://dotnet.microsoft.com/download)から最新のインストーラーを入手します。
* `PATH` 環境変数が、SDK がインストールされている場所 (64 ビット (x64) の場合は `C:\Program Files\dotnet\`、32 ビット (x86) の場合は `C:\Program Files (x86)\dotnet\`) をポイントしていることを確認します。 通常、SDK インストーラーによって `PATH` が設定されます。 同じコンピューターには、常に同じビット数の SDK とランタイムをインストールします。

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>.NET Core ホスティング バンドルのインストール後に SDK が見つからない

[.NET Core ホスティング バンドル](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)をインストールすると、.NET Core ランタイムがインストールされるときに、32 ビット (x86) バージョンの .NET Core (`C:\Program Files (x86)\dotnet\`) をポイントするように `PATH` が変更されます。 これにより、32 ビット (x86) .NET Core の `dotnet` コマンドが使用されているときに、SDK が見つからない可能性があります ([.NET Core SDK が検出されませんでした](#no-net-core-sdks-were-detected))。 この問題を解決するには、`PATH` の `C:\Program Files (x86)\dotnet\` の前の位置に `C:\Program Files\dotnet\` を移動します。

## <a name="obtain-data-from-an-app"></a>アプリからデータを取得する

アプリが要求に応答できる場合は、ミドルウェアを使用してアプリから次のデータを取得できます。

* 要求 &ndash; メソッド、スキーム、ホスト、パス ベース、パス、クエリ文字列、ヘッダー
* 接続 &ndash; リモート IP アドレス、リモート ポート、ローカル IP アドレス、ローカル ポート、クライアント証明書
* ID &ndash; 名前、表示名
* 構成設定
* 環境変数

次の [ミドルウェア](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) コードを `Startup.Configure` メソッドの要求処理パイプラインの先頭に配置します。 この開発環境でのみコードが確実に実行されるように、環境はミドルウェアが実行される前にチェックされます。

環境を取得するには、次のいずれかの方法を使用します。

* `Startup.Configure` メソッドに `IHostingEnvironment` を挿入し、ローカル変数を使用して環境をチェックします。 次のサンプル コードでは、この方法を示します。

* 環境を `Startup` クラスのプロパティに割り当てます。 プロパティを使用して環境をチェックします (例: `if (Environment.IsDevelopment())`)。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a>ASP.NET Core アプリをデバッグする

次のリンクでは、ASP.NET Core アプリのデバッグに関する情報を提供します。

* [Linux での ASP Core のデバッグ](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [SSH 経由の UNIX での .NET Core のデバッグ](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [クイック スタート:Visual Studio デバッガーを使用して ASP.NET をデバッグする](/visualstudio/debugger/quickstart-debug-aspnet)
* デバッグの詳細については、[こちらの GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/2960)に関するページを参照してください。
