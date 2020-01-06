---
title: ASP.NET Core プロジェクトのトラブルシューティングとデバッグ
author: Rick-Anderson
description: ASP.NET Core プロジェクトでの警告とエラーについて説明し、トラブルシューティングを行います。
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 73a73fb51571e5f7b706ff4b958217854750c1fb
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354708"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>ASP.NET Core プロジェクトのトラブルシューティングとデバッグ

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

次のリンクを使用すると、トラブルシューティングのガイダンスが得られます。

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC カンファレンス (ロンドン, 2018): ASP.NET Core アプリケーションの問題の診断](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET ブログ: パフォーマンスに関する問題のトラブルシューティング ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>.NET Core SDK 警告

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>.NET Core SDK の32ビットバージョンと64ビットバージョンの両方がインストールされています

ASP.NET Core の **[新しいプロジェクト]** ダイアログで、次の警告が表示される場合があります。

> .NET Core SDK の32ビットバージョンと64ビットバージョンの両方がインストールされています。 ' C:\\Program Files\\dotnet\\sdk\\' にインストールされている64ビットバージョンのテンプレートのみが表示されます。

この警告は、 [.NET Core SDK](https://www.microsoft.com/net/download/all)の32ビット (x86) バージョンと64ビット (x64) バージョンの両方がインストールされている場合に表示されます。 両方のバージョンがインストールされる可能性のある一般的な理由は次のとおりです。

* 最初は、32ビットのコンピューターを使用して .NET Core SDK インストーラーをダウンロードした後、それを全体にコピーして64ビットのコンピューターにインストールしました。
* 32ビット .NET Core SDK が別のアプリケーションによってインストールされました。
* 間違ったバージョンがダウンロードされ、インストールされました。

この警告を回避するには、32ビットの .NET Core SDK をアンインストールします。 アンインストール**コントロール パネルの** > **プログラムと機能** > **のアンインストールと変更プログラム**です。 警告が発生する理由とその影響について理解している場合は、警告を無視してもかまいません。

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>.NET Core SDK が複数の場所にインストールされています

ASP.NET Core の **[新しいプロジェクト]** ダイアログで、次の警告が表示される場合があります。

> .NET Core SDK が複数の場所にインストールされています。 ' C:\\Program Files\\dotnet\\sdk\\' にインストールされている Sdk のテンプレートのみが表示されます。

.NET Core SDK の少なくとも 1 つのインストール ディレクトリの外部にある場合、このメッセージが表示 *c:\\Program Files\\dotnet\\sdk\\* します。 通常、このエラーは、MSI インストーラーではなくコピー/貼り付けを使用してコンピューターに .NET Core SDK が展開されている場合に発生します。

この警告を回避するには、32ビットの .NET Core Sdk とランタイムをすべてアンインストールします。 アンインストール**コントロール パネルの** > **プログラムと機能** > **のアンインストールと変更プログラム**です。 警告が発生する理由とその影響について理解している場合は、警告を無視してもかまいません。

### <a name="no-net-core-sdks-were-detected"></a>.NET Core Sdk が検出されませんでした

* ASP.NET Core の Visual Studio の **[新しいプロジェクト]** ダイアログで、次の警告が表示される場合があります。

  > .NET Core Sdk が検出されませんでした。環境変数 `PATH`に含まれていることを確認してください。

* `dotnet` コマンドを実行すると、次のような警告が表示されます。

  > インストールされている dotnet Sdk を見つけることができませんでした。

これらの警告は、環境変数 `PATH` が、コンピューター上の .NET Core Sdk を指していない場合に表示されます。 この問題を解決するには、次の手順を実行します。

* .NET Core SDK をインストールします。 [.Net ダウンロード](https://dotnet.microsoft.com/download)から最新のインストーラーを入手します。
* `PATH` 環境変数が、SDK がインストールされている場所を指していることを確認します (64 ビット/x64 の場合は`C:\Program Files\dotnet\`、32ビット/x86 の場合は `C:\Program Files (x86)\dotnet\`)。 SDK インストーラーでは、通常、`PATH`が設定されます。 同じコンピューターには、常に同じビット Sdk とランタイムをインストールします。

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>.NET Core ホスティングバンドルのインストール後に SDK が見つからない

.Net core[ホスティングバンドル](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)をインストールすると、.net core ランタイムをインストールするときに、.net core の32ビット (x86) バージョン (`C:\Program Files (x86)\dotnet\`) を指すように `PATH` が変更されます。 これにより、32ビット (x86) .NET Core `dotnet` コマンドが使用されている場合 ([.Net Core sdk が検出されなかっ](#no-net-core-sdks-were-detected)た場合)、sdk が不足する可能性があります。 この問題を解決するには、`PATH`に `C:\Program Files (x86)\dotnet\` する前に `C:\Program Files\dotnet\` を位置に移動します。

## <a name="obtain-data-from-an-app"></a>アプリからデータを取得する

アプリが要求に応答できる場合は、ミドルウェアを使用してアプリから次のデータを取得できます。

* 要求 &ndash; メソッド、スキーム、ホスト、pathbase、path、クエリ文字列、ヘッダー
* 接続 &ndash; リモート IP アドレス、リモートポート、ローカル IP アドレス、ローカルポート、クライアント証明書
* Id &ndash; 名、表示名
* 構成設定
* 環境変数

`Startup.Configure` メソッドの要求処理パイプラインの先頭に、次の[ミドルウェア](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)コードを配置します。 開発環境でのみコードが実行されるように、ミドルウェアが実行される前に環境がチェックされます。

環境を取得するには、次のいずれかの方法を使用します。

* `Startup.Configure` メソッドに `IHostingEnvironment` を挿入し、ローカル変数を使用して環境を確認します。 次のサンプルコードは、この方法を示しています。

* 環境を `Startup` クラスのプロパティに割り当てます。 プロパティを使用して環境を確認します (たとえば、`if (Environment.IsDevelopment())`)。

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

## <a name="debug-aspnet-core-apps"></a>ASP.NET Core アプリのデバッグ

次のリンクは、ASP.NET Core アプリのデバッグに関する情報を提供します。

* [Linux での ASP Core のデバッグ](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [SSH 経由での Unix での .NET Core のデバッグ](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [クイックスタート: Visual Studio デバッガーを使用して ASP.NET をデバッグする](/visualstudio/debugger/quickstart-debug-aspnet)
* デバッグ情報の詳細については、[この GitHub の問題](https://github.com/aspnet/AspNetCore.Docs/issues/2960)を参照してください。
