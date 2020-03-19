---
title: ASP.NET Core プロジェクトのトラブルシューティングとデバッグ
author: Rick-Anderson
description: ASP.NET Core プロジェクトでの警告とエラーについて説明し、トラブルシューティングを行います。
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 345967f08cf99ef5f18d0c9bcd59ab29c74454f1
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511510"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="daead-103">ASP.NET Core プロジェクトのトラブルシューティングとデバッグ</span><span class="sxs-lookup"><span data-stu-id="daead-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="daead-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="daead-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="daead-105">次のリンクでは、トラブルシューティングのガイダンスが提供されています。</span><span class="sxs-lookup"><span data-stu-id="daead-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="daead-106">NDC カンファレンス (2018 年、ロンドン): ASP.NET Core アプリケーションでの問題を診断する</span><span class="sxs-lookup"><span data-stu-id="daead-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="daead-107">ASP.NET ブログ: ASP.NET Core のパフォーマンスに関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="daead-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="daead-108">.NET Core SDK の警告</span><span class="sxs-lookup"><span data-stu-id="daead-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="daead-109">32 ビットと 64 ビットの両方のバージョンの .NET Core SDK がインストールされています</span><span class="sxs-lookup"><span data-stu-id="daead-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="daead-110">ASP.NET Core の **[新しいプロジェクト]** ダイアログに、次の警告が表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="daead-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="daead-111">32 ビットと 64 ビットの両方のバージョンの .NET Core SDK がインストールされています。</span><span class="sxs-lookup"><span data-stu-id="daead-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="daead-112">'C:\\Program Files\\dotnet\\sdk\\' にインストールされている 64 ビット バージョンのテンプレートのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="daead-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="daead-113">この警告は、32 ビット (x86) と 64 ビット (x64) の両方のバージョンの [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) がインストールされている場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="daead-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="daead-114">両方のバージョンがインストールされる可能性のある一般的な理由は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="daead-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="daead-115">最初は、32 ビットのコンピューターを使用して .NET Core SDK インストーラーをダウンロードした後、それを 64 ビットのコンピューターにコピーしてインストールしました。</span><span class="sxs-lookup"><span data-stu-id="daead-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="daead-116">32 ビットの .NET Core SDK が別のアプリケーションによってインストールされました。</span><span class="sxs-lookup"><span data-stu-id="daead-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="daead-117">正しくないバージョンがダウンロードされ、インストールされました。</span><span class="sxs-lookup"><span data-stu-id="daead-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="daead-118">この警告を回避するには、32 ビットの .NET Core SDK をアンインストールします。</span><span class="sxs-lookup"><span data-stu-id="daead-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="daead-119">**[コントロール パネル]**  >  **[プログラムと機能]**  >  **[プログラムのアンインストールまたは変更]** からアンインストールします。</span><span class="sxs-lookup"><span data-stu-id="daead-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="daead-120">警告が発生する理由とその影響について理解している場合は、この警告を無視できます。</span><span class="sxs-lookup"><span data-stu-id="daead-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="daead-121">.NET Core SDK は、複数の場所にインストールされます</span><span class="sxs-lookup"><span data-stu-id="daead-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="daead-122">ASP.NET Core の **[新しいプロジェクト]** ダイアログに、次の警告が表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="daead-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="daead-123">.NET Core SDK は、複数の場所にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="daead-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="daead-124">'C:\\Program Files\\dotnet\\sdk\\' にインストールされた SDK からのテンプレートのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="daead-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="daead-125">このメッセージが表示されるのは、.NET Core SDK のインストールが少なくとも 1 つ、*C:\\Program Files\\dotnet\\sdk\\* の外部にあるディレクトリに存在する場合です。</span><span class="sxs-lookup"><span data-stu-id="daead-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="daead-126">通常、これは、MSI インストーラーではなく、コピーと貼り付けを使用して、.NET Core SDK がコンピューターに展開されている場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="daead-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="daead-127">この警告を回避するには、32 ビットの .NET Core SDK とランタイムをすべてアンインストールします。</span><span class="sxs-lookup"><span data-stu-id="daead-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="daead-128">**[コントロール パネル]**  >  **[プログラムと機能]**  >  **[プログラムのアンインストールまたは変更]** からアンインストールします。</span><span class="sxs-lookup"><span data-stu-id="daead-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="daead-129">警告が発生する理由とその影響について理解している場合は、この警告を無視できます。</span><span class="sxs-lookup"><span data-stu-id="daead-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="daead-130">.NET Core SDK が検出されませんでした</span><span class="sxs-lookup"><span data-stu-id="daead-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="daead-131">Visual Studio の ASP.NET Core 用の **[新しいプロジェクト]** ダイアログに、次の警告が表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="daead-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="daead-132">.NET Core SDK が検出されませんでした。それらを確実に環境変数 `PATH` に含めてください。</span><span class="sxs-lookup"><span data-stu-id="daead-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="daead-133">`dotnet` コマンドを実行すると、次のような警告が表示されます。</span><span class="sxs-lookup"><span data-stu-id="daead-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="daead-134">インストールされた dotnet SDK が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="daead-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="daead-135">これらの警告は、環境変数 `PATH` が、コンピューター上の .NET Core SDK をポイントしていない場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="daead-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="daead-136">これを解決するには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="daead-136">To resolve this problem:</span></span>

* <span data-ttu-id="daead-137">.NET Core SDK をインストールします。</span><span class="sxs-lookup"><span data-stu-id="daead-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="daead-138">[.NET ダウンロード](https://dotnet.microsoft.com/download)から最新のインストーラーを入手します。</span><span class="sxs-lookup"><span data-stu-id="daead-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="daead-139">`PATH` 環境変数が、SDK がインストールされている場所 (64 ビット (x64) の場合は `C:\Program Files\dotnet\`、32 ビット (x86) の場合は `C:\Program Files (x86)\dotnet\`) をポイントしていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="daead-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="daead-140">通常、SDK インストーラーによって `PATH` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="daead-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="daead-141">同じコンピューターには、常に同じビット数の SDK とランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="daead-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="daead-142">.NET Core ホスティング バンドルのインストール後に SDK が見つからない</span><span class="sxs-lookup"><span data-stu-id="daead-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="daead-143">[.NET Core ホスティング バンドル](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)をインストールすると、.NET Core ランタイムがインストールされるときに、32 ビット (x86) バージョンの .NET Core (`C:\Program Files (x86)\dotnet\`) をポイントするように `PATH` が変更されます。</span><span class="sxs-lookup"><span data-stu-id="daead-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="daead-144">これにより、32 ビット (x86) .NET Core の `dotnet` コマンドが使用されているときに、SDK が見つからない可能性があります ([.NET Core SDK が検出されませんでした](#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="daead-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="daead-145">この問題を解決するには、`PATH` の `C:\Program Files (x86)\dotnet\` の前の位置に `C:\Program Files\dotnet\` を移動します。</span><span class="sxs-lookup"><span data-stu-id="daead-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="daead-146">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="daead-146">Obtain data from an app</span></span>

<span data-ttu-id="daead-147">アプリが要求に応答できる場合は、ミドルウェアを使用してアプリから次のデータを取得できます。</span><span class="sxs-lookup"><span data-stu-id="daead-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="daead-148">要求 &ndash; メソッド、スキーム、ホスト、パス ベース、パス、クエリ文字列、ヘッダー</span><span class="sxs-lookup"><span data-stu-id="daead-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="daead-149">接続 &ndash; リモート IP アドレス、リモート ポート、ローカル IP アドレス、ローカル ポート、クライアント証明書</span><span class="sxs-lookup"><span data-stu-id="daead-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="daead-150">ID &ndash; 名前、表示名</span><span class="sxs-lookup"><span data-stu-id="daead-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="daead-151">構成設定</span><span class="sxs-lookup"><span data-stu-id="daead-151">Configuration settings</span></span>
* <span data-ttu-id="daead-152">環境変数</span><span class="sxs-lookup"><span data-stu-id="daead-152">Environment variables</span></span>

<span data-ttu-id="daead-153">次の [ミドルウェア](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) コードを `Startup.Configure` メソッドの要求処理パイプラインの先頭に配置します。</span><span class="sxs-lookup"><span data-stu-id="daead-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="daead-154">この開発環境でのみコードが確実に実行されるように、環境はミドルウェアが実行される前にチェックされます。</span><span class="sxs-lookup"><span data-stu-id="daead-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="daead-155">環境を取得するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="daead-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="daead-156">`Startup.Configure` メソッドに `IHostingEnvironment` を挿入し、ローカル変数を使用して環境をチェックします。</span><span class="sxs-lookup"><span data-stu-id="daead-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="daead-157">次のサンプル コードでは、この方法を示します。</span><span class="sxs-lookup"><span data-stu-id="daead-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="daead-158">環境を `Startup` クラスのプロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="daead-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="daead-159">プロパティを使用して環境をチェックします (例: `if (Environment.IsDevelopment())`)。</span><span class="sxs-lookup"><span data-stu-id="daead-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="daead-160">ASP.NET Core アプリをデバッグする</span><span class="sxs-lookup"><span data-stu-id="daead-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="daead-161">次のリンクでは、ASP.NET Core アプリのデバッグに関する情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="daead-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="daead-162">Linux での ASP Core のデバッグ</span><span class="sxs-lookup"><span data-stu-id="daead-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="daead-163">SSH 経由の UNIX での .NET Core のデバッグ</span><span class="sxs-lookup"><span data-stu-id="daead-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="daead-164">クイック スタート:Visual Studio デバッガーを使用して ASP.NET をデバッグする</span><span class="sxs-lookup"><span data-stu-id="daead-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="daead-165">デバッグの詳細については、[こちらの GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/2960)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="daead-165">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
