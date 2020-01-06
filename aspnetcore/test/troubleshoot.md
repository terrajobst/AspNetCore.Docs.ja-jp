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
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="6a2a0-103">ASP.NET Core プロジェクトのトラブルシューティングとデバッグ</span><span class="sxs-lookup"><span data-stu-id="6a2a0-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="6a2a0-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6a2a0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6a2a0-105">次のリンクを使用すると、トラブルシューティングのガイダンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="6a2a0-106">NDC カンファレンス (ロンドン, 2018): ASP.NET Core アプリケーションの問題の診断</span><span class="sxs-lookup"><span data-stu-id="6a2a0-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="6a2a0-107">ASP.NET ブログ: パフォーマンスに関する問題のトラブルシューティング ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a2a0-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="6a2a0-108">.NET Core SDK 警告</span><span class="sxs-lookup"><span data-stu-id="6a2a0-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="6a2a0-109">.NET Core SDK の32ビットバージョンと64ビットバージョンの両方がインストールされています</span><span class="sxs-lookup"><span data-stu-id="6a2a0-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="6a2a0-110">ASP.NET Core の **[新しいプロジェクト]** ダイアログで、次の警告が表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="6a2a0-111">.NET Core SDK の32ビットバージョンと64ビットバージョンの両方がインストールされています。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="6a2a0-112">' C:\\Program Files\\dotnet\\sdk\\' にインストールされている64ビットバージョンのテンプレートのみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="6a2a0-113">この警告は、 [.NET Core SDK](https://www.microsoft.com/net/download/all)の32ビット (x86) バージョンと64ビット (x64) バージョンの両方がインストールされている場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="6a2a0-114">両方のバージョンがインストールされる可能性のある一般的な理由は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="6a2a0-115">最初は、32ビットのコンピューターを使用して .NET Core SDK インストーラーをダウンロードした後、それを全体にコピーして64ビットのコンピューターにインストールしました。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="6a2a0-116">32ビット .NET Core SDK が別のアプリケーションによってインストールされました。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="6a2a0-117">間違ったバージョンがダウンロードされ、インストールされました。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="6a2a0-118">この警告を回避するには、32ビットの .NET Core SDK をアンインストールします。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="6a2a0-119">アンインストール**コントロール パネルの** > **プログラムと機能** > **のアンインストールと変更プログラム**です。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="6a2a0-120">警告が発生する理由とその影響について理解している場合は、警告を無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="6a2a0-121">.NET Core SDK が複数の場所にインストールされています</span><span class="sxs-lookup"><span data-stu-id="6a2a0-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="6a2a0-122">ASP.NET Core の **[新しいプロジェクト]** ダイアログで、次の警告が表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="6a2a0-123">.NET Core SDK が複数の場所にインストールされています。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="6a2a0-124">' C:\\Program Files\\dotnet\\sdk\\' にインストールされている Sdk のテンプレートのみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="6a2a0-125">.NET Core SDK の少なくとも 1 つのインストール ディレクトリの外部にある場合、このメッセージが表示 *c:\\Program Files\\dotnet\\sdk\\* します。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="6a2a0-126">通常、このエラーは、MSI インストーラーではなくコピー/貼り付けを使用してコンピューターに .NET Core SDK が展開されている場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="6a2a0-127">この警告を回避するには、32ビットの .NET Core Sdk とランタイムをすべてアンインストールします。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="6a2a0-128">アンインストール**コントロール パネルの** > **プログラムと機能** > **のアンインストールと変更プログラム**です。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="6a2a0-129">警告が発生する理由とその影響について理解している場合は、警告を無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="6a2a0-130">.NET Core Sdk が検出されませんでした</span><span class="sxs-lookup"><span data-stu-id="6a2a0-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="6a2a0-131">ASP.NET Core の Visual Studio の **[新しいプロジェクト]** ダイアログで、次の警告が表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="6a2a0-132">.NET Core Sdk が検出されませんでした。環境変数 `PATH`に含まれていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="6a2a0-133">`dotnet` コマンドを実行すると、次のような警告が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="6a2a0-134">インストールされている dotnet Sdk を見つけることができませんでした。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="6a2a0-135">これらの警告は、環境変数 `PATH` が、コンピューター上の .NET Core Sdk を指していない場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="6a2a0-136">この問題を解決するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-136">To resolve this problem:</span></span>

* <span data-ttu-id="6a2a0-137">.NET Core SDK をインストールします。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="6a2a0-138">[.Net ダウンロード](https://dotnet.microsoft.com/download)から最新のインストーラーを入手します。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="6a2a0-139">`PATH` 環境変数が、SDK がインストールされている場所を指していることを確認します (64 ビット/x64 の場合は`C:\Program Files\dotnet\`、32ビット/x86 の場合は `C:\Program Files (x86)\dotnet\`)。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="6a2a0-140">SDK インストーラーでは、通常、`PATH`が設定されます。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="6a2a0-141">同じコンピューターには、常に同じビット Sdk とランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="6a2a0-142">.NET Core ホスティングバンドルのインストール後に SDK が見つからない</span><span class="sxs-lookup"><span data-stu-id="6a2a0-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="6a2a0-143">.Net core[ホスティングバンドル](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)をインストールすると、.net core ランタイムをインストールするときに、.net core の32ビット (x86) バージョン (`C:\Program Files (x86)\dotnet\`) を指すように `PATH` が変更されます。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="6a2a0-144">これにより、32ビット (x86) .NET Core `dotnet` コマンドが使用されている場合 ([.Net Core sdk が検出されなかっ](#no-net-core-sdks-were-detected)た場合)、sdk が不足する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="6a2a0-145">この問題を解決するには、`PATH`に `C:\Program Files (x86)\dotnet\` する前に `C:\Program Files\dotnet\` を位置に移動します。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="6a2a0-146">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="6a2a0-146">Obtain data from an app</span></span>

<span data-ttu-id="6a2a0-147">アプリが要求に応答できる場合は、ミドルウェアを使用してアプリから次のデータを取得できます。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="6a2a0-148">要求 &ndash; メソッド、スキーム、ホスト、pathbase、path、クエリ文字列、ヘッダー</span><span class="sxs-lookup"><span data-stu-id="6a2a0-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="6a2a0-149">接続 &ndash; リモート IP アドレス、リモートポート、ローカル IP アドレス、ローカルポート、クライアント証明書</span><span class="sxs-lookup"><span data-stu-id="6a2a0-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="6a2a0-150">Id &ndash; 名、表示名</span><span class="sxs-lookup"><span data-stu-id="6a2a0-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="6a2a0-151">構成設定</span><span class="sxs-lookup"><span data-stu-id="6a2a0-151">Configuration settings</span></span>
* <span data-ttu-id="6a2a0-152">環境変数</span><span class="sxs-lookup"><span data-stu-id="6a2a0-152">Environment variables</span></span>

<span data-ttu-id="6a2a0-153">`Startup.Configure` メソッドの要求処理パイプラインの先頭に、次の[ミドルウェア](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)コードを配置します。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="6a2a0-154">開発環境でのみコードが実行されるように、ミドルウェアが実行される前に環境がチェックされます。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="6a2a0-155">環境を取得するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="6a2a0-156">`Startup.Configure` メソッドに `IHostingEnvironment` を挿入し、ローカル変数を使用して環境を確認します。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="6a2a0-157">次のサンプルコードは、この方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="6a2a0-158">環境を `Startup` クラスのプロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="6a2a0-159">プロパティを使用して環境を確認します (たとえば、`if (Environment.IsDevelopment())`)。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="6a2a0-160">ASP.NET Core アプリのデバッグ</span><span class="sxs-lookup"><span data-stu-id="6a2a0-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="6a2a0-161">次のリンクは、ASP.NET Core アプリのデバッグに関する情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="6a2a0-162">Linux での ASP Core のデバッグ</span><span class="sxs-lookup"><span data-stu-id="6a2a0-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="6a2a0-163">SSH 経由での Unix での .NET Core のデバッグ</span><span class="sxs-lookup"><span data-stu-id="6a2a0-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="6a2a0-164">クイックスタート: Visual Studio デバッガーを使用して ASP.NET をデバッグする</span><span class="sxs-lookup"><span data-stu-id="6a2a0-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="6a2a0-165">デバッグ情報の詳細については、[この GitHub の問題](https://github.com/aspnet/AspNetCore.Docs/issues/2960)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a2a0-165">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
