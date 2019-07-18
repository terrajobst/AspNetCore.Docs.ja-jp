---
title: ASP.NET Core プロジェクトのトラブルシューティング
author: Rick-Anderson
description: ASP.NET Core プロジェクトでの警告とエラーについて説明し、トラブルシューティングを行います。
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: b434af2dd046045836d2f6f7f7b7b2d57699bedc
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308282"
---
# <a name="troubleshoot-aspnet-core-projects"></a><span data-ttu-id="a4249-103">ASP.NET Core プロジェクトのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="a4249-103">Troubleshoot ASP.NET Core projects</span></span>

<span data-ttu-id="a4249-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a4249-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a4249-105">次のリンクを使用すると、トラブルシューティングのガイダンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="a4249-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="a4249-106">NDC カンファレンス (ロンドン、2018):ASP.NET Core アプリケーションの問題の診断</span><span class="sxs-lookup"><span data-stu-id="a4249-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="a4249-107">ASP.NET ブログ:パフォーマンスに関する問題のトラブルシューティング ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4249-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="a4249-108">.NET Core SDK 警告</span><span class="sxs-lookup"><span data-stu-id="a4249-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="a4249-109">.NET Core SDK の32ビットバージョンと64ビットバージョンの両方がインストールされています</span><span class="sxs-lookup"><span data-stu-id="a4249-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="a4249-110">ASP.NET Core の **[新しいプロジェクト]** ダイアログで、次の警告が表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="a4249-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="a4249-111">.NET Core SDK の32ビットバージョンと64ビットバージョンの両方がインストールされています。</span><span class="sxs-lookup"><span data-stu-id="a4249-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="a4249-112">' C:\\Program Files\\dotnet\\sdk\\' にインストールされている64ビットバージョンのテンプレートのみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4249-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="a4249-113">この警告は、 [.NET Core SDK](https://www.microsoft.com/net/download/all)の32ビット (x86) バージョンと64ビット (x64) バージョンの両方がインストールされている場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4249-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="a4249-114">両方のバージョンがインストールされる可能性のある一般的な理由は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a4249-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="a4249-115">最初は、32ビットのコンピューターを使用して .NET Core SDK インストーラーをダウンロードした後、それを全体にコピーして64ビットのコンピューターにインストールしました。</span><span class="sxs-lookup"><span data-stu-id="a4249-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="a4249-116">32ビット .NET Core SDK が別のアプリケーションによってインストールされました。</span><span class="sxs-lookup"><span data-stu-id="a4249-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="a4249-117">間違ったバージョンがダウンロードされ、インストールされました。</span><span class="sxs-lookup"><span data-stu-id="a4249-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="a4249-118">この警告を回避するには、32ビットの .NET Core SDK をアンインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4249-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="a4249-119">アンインストール**コントロール パネルの** > **プログラムと機能** > **のアンインストールと変更プログラム**です。</span><span class="sxs-lookup"><span data-stu-id="a4249-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="a4249-120">警告が発生する理由とその影響について理解している場合は、警告を無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="a4249-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="a4249-121">.NET Core SDK が複数の場所にインストールされています</span><span class="sxs-lookup"><span data-stu-id="a4249-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="a4249-122">ASP.NET Core の **[新しいプロジェクト]** ダイアログで、次の警告が表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="a4249-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="a4249-123">.NET Core SDK が複数の場所にインストールされています。</span><span class="sxs-lookup"><span data-stu-id="a4249-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="a4249-124">' C:\\Program Files\\dotnet\\sdk\\' にインストールされている sdk のテンプレートのみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4249-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="a4249-125">.NET Core SDK の少なくとも 1 つのインストール ディレクトリの外部にある場合、このメッセージが表示 *c:\\Program Files\\dotnet\\sdk\\* します。</span><span class="sxs-lookup"><span data-stu-id="a4249-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="a4249-126">通常、このエラーは、MSI インストーラーではなくコピー/貼り付けを使用してコンピューターに .NET Core SDK が展開されている場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="a4249-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="a4249-127">この警告を回避するには、32ビットの .NET Core Sdk とランタイムをすべてアンインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4249-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="a4249-128">アンインストール**コントロール パネルの** > **プログラムと機能** > **のアンインストールと変更プログラム**です。</span><span class="sxs-lookup"><span data-stu-id="a4249-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="a4249-129">警告が発生する理由とその影響について理解している場合は、警告を無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="a4249-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="a4249-130">.NET Core Sdk が検出されませんでした</span><span class="sxs-lookup"><span data-stu-id="a4249-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="a4249-131">ASP.NET Core の Visual Studio の **[新しいプロジェクト]** ダイアログで、次の警告が表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="a4249-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="a4249-132">.NET Core Sdk が検出されませんでした。環境変数`PATH`に含まれていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="a4249-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="a4249-133">`dotnet`コマンドを実行すると、次のような警告が表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4249-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="a4249-134">インストールされている dotnet Sdk を見つけることができませんでした。</span><span class="sxs-lookup"><span data-stu-id="a4249-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="a4249-135">これらの警告は、環境変数`PATH`がコンピューター上の .net Core sdk を指していない場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4249-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="a4249-136">この問題を解決するには:</span><span class="sxs-lookup"><span data-stu-id="a4249-136">To resolve this problem:</span></span>

* <span data-ttu-id="a4249-137">.NET Core SDK をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4249-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="a4249-138">[.Net ダウンロード](https://dotnet.microsoft.com/download)から最新のインストーラーを入手します。</span><span class="sxs-lookup"><span data-stu-id="a4249-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="a4249-139">環境変数が、SDK がインストールされている場所を指して`C:\Program Files\dotnet\`いることを確認します ( `C:\Program Files (x86)\dotnet\` 64 ビット/x64 の場合は、32ビット/x86 の場合)。 `PATH`</span><span class="sxs-lookup"><span data-stu-id="a4249-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="a4249-140">SDK インストーラーは、通常、 `PATH`を設定します。</span><span class="sxs-lookup"><span data-stu-id="a4249-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="a4249-141">同じコンピューターには、常に同じビット Sdk とランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4249-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="a4249-142">.NET Core ホスティングバンドルのインストール後に SDK が見つからない</span><span class="sxs-lookup"><span data-stu-id="a4249-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="a4249-143">.Net core[ホスティングバンドル](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)をインストールすると`PATH` 、.net core ランタイムをインストールするときに、.net core の32ビット (x86) バージョン (`C:\Program Files (x86)\dotnet\`) を指すように変更されます。</span><span class="sxs-lookup"><span data-stu-id="a4249-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="a4249-144">これにより、32ビット (x86) .net core `dotnet`コマンドが使用されている場合 ([.net core sdk が検出されなかっ](#no-net-core-sdks-were-detected)た場合)、sdk が不足する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a4249-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="a4249-145">この問題を解決するに`C:\Program Files\dotnet\`は、の`PATH`前`C:\Program Files (x86)\dotnet\`の位置に移動します。</span><span class="sxs-lookup"><span data-stu-id="a4249-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="a4249-146">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="a4249-146">Obtain data from an app</span></span>

<span data-ttu-id="a4249-147">アプリが要求に応答できる場合は、ミドルウェアを使用してアプリから次のデータを取得できます。</span><span class="sxs-lookup"><span data-stu-id="a4249-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="a4249-148">Request &ndash;メソッド、scheme、host、pathbase、path、query string、headers</span><span class="sxs-lookup"><span data-stu-id="a4249-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="a4249-149">接続&ndash;リモート ip アドレス、リモートポート、ローカル IP アドレス、ローカルポート、クライアント証明書</span><span class="sxs-lookup"><span data-stu-id="a4249-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="a4249-150">Id &ndash;名、表示名</span><span class="sxs-lookup"><span data-stu-id="a4249-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="a4249-151">構成設定</span><span class="sxs-lookup"><span data-stu-id="a4249-151">Configuration settings</span></span>
* <span data-ttu-id="a4249-152">環境変数</span><span class="sxs-lookup"><span data-stu-id="a4249-152">Environment variables</span></span>

<span data-ttu-id="a4249-153">`Startup.Configure`メソッドの要求処理パイプラインの先頭に、次の[ミドルウェア](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)コードを配置します。</span><span class="sxs-lookup"><span data-stu-id="a4249-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="a4249-154">開発環境でのみコードが実行されるように、ミドルウェアが実行される前に環境がチェックされます。</span><span class="sxs-lookup"><span data-stu-id="a4249-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="a4249-155">環境を取得するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="a4249-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="a4249-156">`IHostingEnvironment`をメソッド`Startup.Configure`に挿入し、ローカル変数を使用して環境を確認します。</span><span class="sxs-lookup"><span data-stu-id="a4249-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="a4249-157">次のサンプルコードは、この方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="a4249-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="a4249-158">`Startup`クラスのプロパティに環境を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="a4249-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="a4249-159">プロパティを使用して環境を確認します`if (Environment.IsDevelopment())`(たとえば、)。</span><span class="sxs-lookup"><span data-stu-id="a4249-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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
