---
title: ASP.NET Core で複数の環境を使用する
author: rick-anderson
description: ASP.NET Core アプリで複数の環境にわたりアプリの動作を制御する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: 30e2771c0a24fcbf6490d08c7028566314b6c011
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358722"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="ea9c0-103">ASP.NET Core で複数の環境を使用する</span><span class="sxs-lookup"><span data-stu-id="ea9c0-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ea9c0-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ea9c0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ea9c0-105">ASP.NET Core は、環境変数を利用し、ランタイム環境に基づいてアプリの動作を構成します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="ea9c0-106">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="ea9c0-107">環境</span><span class="sxs-lookup"><span data-stu-id="ea9c0-107">Environments</span></span>

<span data-ttu-id="ea9c0-108">ASP.NET Core はアプリの起動時に環境変数 `ASPNETCORE_ENVIRONMENT` を読み込み、その値を [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) に格納します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="ea9c0-109">`ASPNETCORE_ENVIRONMENT` は任意の値に設定できますが、次の 3 つの値がフレームワークによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="ea9c0-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (既定値)</span><span class="sxs-lookup"><span data-stu-id="ea9c0-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="ea9c0-111">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-111">The preceding code:</span></span>

* <span data-ttu-id="ea9c0-112">`ASPNETCORE_ENVIRONMENT` が `Development` に設定されているとき、[UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="ea9c0-113">`ASPNETCORE_ENVIRONMENT` の値が次のいずれかに設定されているとき、[UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="ea9c0-114">[環境タグ ヘルパー ](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) は `IHostingEnvironment.EnvironmentName` の値を使用し、要素にマークアップを追加するか、要素からマークアップを除外します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="ea9c0-115">Windows と macOS では、環境変数と値で大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="ea9c0-116">Linux では、環境変数と値について、既定で**大文字と小文字が区別されます**。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="ea9c0-117">開発</span><span class="sxs-lookup"><span data-stu-id="ea9c0-117">Development</span></span>

<span data-ttu-id="ea9c0-118">開発環境では、実稼働で公開すべきではない機能を有効にすることがあります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="ea9c0-119">たとえば、ASP.NET Core テンプレートは、開発環境で[開発者例外ページ](xref:fundamentals/error-handling#developer-exception-page)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="ea9c0-120">ローカル コンピューターの開発環境をプロジェクトの *Properties\launchSettings.json* ファイルで設定できます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="ea9c0-121">*launchSettings.json* に設定されている環境値で、システム環境に設定されている値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="ea9c0-122">次の JSON では、*launchSettings.json* ファイルの 3 つのプロファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="ea9c0-123">*launchSettings.json* の `applicationUrl` プロパティでは、サーバー URL の一覧を指定できます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="ea9c0-124">一覧の URL 間には、セミコロンを次のように使用します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-124">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="ea9c0-125">アプリが [dotnet run](/dotnet/core/tools/dotnet-run) で起動すると、`"commandName": "Project"` を含む最初のプロファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="ea9c0-126">`commandName` の値により、起動する Web サーバーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="ea9c0-127">`commandName` は次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="ea9c0-128">`Project` (Kestrel を起動する)</span><span class="sxs-lookup"><span data-stu-id="ea9c0-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="ea9c0-129">アプリが [dotnet run](/dotnet/core/tools/dotnet-run) で起動されるとき:</span><span class="sxs-lookup"><span data-stu-id="ea9c0-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="ea9c0-130">利用できる場合、*launchSettings.json* が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="ea9c0-131">*launchSettings.json* の `environmentVariables` 設定により環境変数がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="ea9c0-132">ホスティング環境が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="ea9c0-133">次の出力には、[dotnet run](/dotnet/core/tools/dotnet-run) で起動したアプリが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="ea9c0-134">Visual Studio のプロジェクト プロパティの **[デバッグ]** タブには、*launchSettings.json* ファイルを編集するための GUI があります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![プロジェクト プロパティ設定の環境変数](environments/_static/project-properties-debug.png)

<span data-ttu-id="ea9c0-136">プロジェクト プロファイルを変更した場合、Web サーバーを再起動するまで変更は適用されません。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="ea9c0-137">Kestrel がその環境に行われた変更を検出するには、先に再起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="ea9c0-138">*launchSettings.json* にはシークレットを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="ea9c0-139">ローカル開発のシークレットを格納するには、[シークレット マネージャー ツール](xref:security/app-secrets)を利用できます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="ea9c0-140">[Visual Studio Code](https://code.visualstudio.com/) を使用するとき、環境変数を *.vscode/launch.json* ファイルで設定できます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="ea9c0-141">次の例では、環境が `Development` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-141">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="ea9c0-142">*Properties/launchSettings.json* と同じように `dotnet run` でアプリを起動すると、プロジェクトの *.vscode/launch.json* ファイルは読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="ea9c0-143">*launchSettings.json* ファイルがない開発中アプリを起動するとき、環境変数で環境を設定するか、コマンドライン引数を `dotnet run` コマンドに設定します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="ea9c0-144">実稼働</span><span class="sxs-lookup"><span data-stu-id="ea9c0-144">Production</span></span>

<span data-ttu-id="ea9c0-145">実稼働環境は、セキュリティ、パフォーマンス、アプリの堅牢性が最大化されるように構成してください。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="ea9c0-146">開発とは異なる一般的な設定は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="ea9c0-147">キャッシュ。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-147">Caching.</span></span>
* <span data-ttu-id="ea9c0-148">クライアント側のリソースはバンドルされ、小さくされ、場合によっては CDN からサービスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="ea9c0-149">診断エラー ページが無効。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="ea9c0-150">フレンドリ エラー ページが有効。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="ea9c0-151">実稼働のログ記録と監視が有効。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="ea9c0-152">[Application Insights](/azure/application-insights/app-insights-asp-net-core) など。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="ea9c0-153">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="ea9c0-153">Set the environment</span></span>

<span data-ttu-id="ea9c0-154">環境変数またはプラットフォームの設定を使用してテスト用の特定の環境を設定すると、便利な場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-154">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="ea9c0-155">環境を設定しない場合、既定で `Production` に設定されます。ほとんどのデバッグ機能が無効になります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="ea9c0-156">環境を設定する手法は、オペレーティング システムによって異なります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-156">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="ea9c0-157">ホストが構築されると、アプリによって読み取られた最後の環境設定によってアプリの環境が決まります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-157">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="ea9c0-158">アプリの実行中にアプリの環境を変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-158">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="ea9c0-159">環境変数またはプラットフォームの設定</span><span class="sxs-lookup"><span data-stu-id="ea9c0-159">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="ea9c0-160">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ea9c0-160">Azure App Service</span></span>

<span data-ttu-id="ea9c0-161">[Azure App Service](https://azure.microsoft.com/services/app-service/) で環境を設定するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-161">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="ea9c0-162">**[App Services]** ブレードからアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-162">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="ea9c0-163">**[設定]** グループで、 **[構成]** ブレードを選択します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-163">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="ea9c0-164">**[アプリケーション設定]** タブで、 **[新しいアプリケーション設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-164">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="ea9c0-165">**[Add/Edit application setting]\(アプリケーション設定の追加/編集\)** ウィンドウで、 **[名前]** に `ASPNETCORE_ENVIRONMENT` を指定します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-165">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="ea9c0-166">**[値]** には、環境を指定します (`Staging` など)。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-166">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="ea9c0-167">デプロイ スロットがスワップされるときに、現在のスロットに環境設定を残したい場合は、 **[デプロイ スロットの設定]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-167">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="ea9c0-168">詳細については、Azure ドキュメントの「[Azure App Service でステージング環境を設定する](/azure/app-service/web-sites-staged-publishing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-168">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="ea9c0-169">**[OK]** を選択して、 **[Add/Edit application setting]\(アプリケーション設定の追加/編集\)** ウィンドウを閉じます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-169">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="ea9c0-170">**[構成]** ブレードの上部にある **[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-170">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="ea9c0-171">Azure App Service は、アプリ設定 (環境変数) が Azure Portal で追加、変更、削除された後、アプリを自動的に再起動します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-171">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="ea9c0-172">Windows</span><span class="sxs-lookup"><span data-stu-id="ea9c0-172">Windows</span></span>

<span data-ttu-id="ea9c0-173">[dotnet run](/dotnet/core/tools/dotnet-run) を使用してアプリを起動するときに現在のセッションに `ASPNETCORE_ENVIRONMENT` を設定するには、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-173">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="ea9c0-174">**コマンド プロンプト**</span><span class="sxs-lookup"><span data-stu-id="ea9c0-174">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="ea9c0-175">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="ea9c0-175">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="ea9c0-176">これらのコマンドは、現在のウィンドウでのみ有効になります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-176">These commands only take effect for the current window.</span></span> <span data-ttu-id="ea9c0-177">ウィンドウが閉じられると、`ASPNETCORE_ENVIRONMENT` 設定が初期設定またはコンピューター値に戻ります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-177">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="ea9c0-178">Windows でグローバルな値を設定するには、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-178">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="ea9c0-179">**[コントロール パネル]** > **[システム]** > **[システムの詳細設定]** の順に開き、`ASPNETCORE_ENVIRONMENT` 値を追加または編集します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-179">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![システムの詳細プロパティ](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 環境変数](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="ea9c0-182">管理コマンド プロンプトを開き、`setx` コマンドを使用するか、または管理用の PowerShell コマンド プロンプトを開いて、`[Environment]::SetEnvironmentVariable` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-182">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="ea9c0-183">**コマンド プロンプト**</span><span class="sxs-lookup"><span data-stu-id="ea9c0-183">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="ea9c0-184">`/M` スイッチは、システム レベルで環境変数を設定することを示します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-184">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="ea9c0-185">`/M` スイッチが使用されない場合、ユーザー アカウントに対する環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-185">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="ea9c0-186">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="ea9c0-186">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="ea9c0-187">`Machine` オプションの値は、システム レベルで環境変数を設定することを示します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-187">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="ea9c0-188">オプションの値が `User` に変更されると、ユーザー アカウントに対する環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-188">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="ea9c0-189">グローバルに設定されている `ASPNETCORE_ENVIRONMENT` の環境変数は、値の設定後に開いているすべてのコマンド ウィンドウで `dotnet run` に対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-189">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="ea9c0-190">**web.config**</span><span class="sxs-lookup"><span data-stu-id="ea9c0-190">**web.config**</span></span>

<span data-ttu-id="ea9c0-191">`ASPNETCORE_ENVIRONMENT` 環境変数を *web.config* で設定するには、<xref:host-and-deploy/aspnet-core-module#setting-environment-variables>の「*環境変数の設定*」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-191">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="ea9c0-192">**プロジェクト ファイルまたは発行プロファイル**</span><span class="sxs-lookup"><span data-stu-id="ea9c0-192">**Project file or publish profile**</span></span>

<span data-ttu-id="ea9c0-193">**Windows IIS の配置:** [発行プロファイル (.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-193">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="ea9c0-194">この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-194">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="ea9c0-195">**IIS アプリケーション プール**</span><span class="sxs-lookup"><span data-stu-id="ea9c0-195">**Per IIS Application Pool**</span></span>

<span data-ttu-id="ea9c0-196">分離されたアプリケーション プール (IIS 10.0 以降でサポートされています) で実行するアプリに対して `ASPNETCORE_ENVIRONMENT` 環境変数を設定するには、[環境変数 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある *AppCmd.exe コマンド*のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-196">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="ea9c0-197">`ASPNETCORE_ENVIRONMENT` 環境変数がアプリ プールで設定されている場合、その値は、システム レベルの設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-197">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ea9c0-198">IIS でアプリをホストして `ASPNETCORE_ENVIRONMENT` 環境変数を追加または変更するときは、次のいずれかの方法を使用してアプリで選択された新しい値を設定してください。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-198">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="ea9c0-199">コマンド プロンプトで `net stop was /y` を実行した後、`net start w3svc` を実行します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-199">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="ea9c0-200">サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-200">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="ea9c0-201">macOS</span><span class="sxs-lookup"><span data-stu-id="ea9c0-201">macOS</span></span>

<span data-ttu-id="ea9c0-202">macOS の現在の環境は、アプリ実行時にインラインで設定できます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-202">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="ea9c0-203">あるいは、アプリを実行する前に `export` で環境を設定します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-203">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="ea9c0-204">コンピューター レベルの環境変数は *.bashrc* または *.bash_profile* ファイルに設定されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-204">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="ea9c0-205">任意のテキスト エディターを利用し、ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-205">Edit the file using any text editor.</span></span> <span data-ttu-id="ea9c0-206">次のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-206">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="ea9c0-207">Linux</span><span class="sxs-lookup"><span data-stu-id="ea9c0-207">Linux</span></span>

<span data-ttu-id="ea9c0-208">Linux ディストリビューションの場合、セッション ベースの変数設定にはコマンド プロンプトで `export` コマンドを使用し、コンピューター レベルの環境設定には *bash_profile* ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-208">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="ea9c0-209">コードで環境を設定する</span><span class="sxs-lookup"><span data-stu-id="ea9c0-209">Set the environment in code</span></span>

<span data-ttu-id="ea9c0-210">ホストのビルド時に <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-210">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="ea9c0-211">以下を参照してください。<xref:fundamentals/host/generic-host#environmentname></span><span class="sxs-lookup"><span data-stu-id="ea9c0-211">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>


### <a name="configuration-by-environment"></a><span data-ttu-id="ea9c0-212">環境別の構成</span><span class="sxs-lookup"><span data-stu-id="ea9c0-212">Configuration by environment</span></span>

<span data-ttu-id="ea9c0-213">環境ごとに構成を読み込む場合の推奨事項は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-213">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="ea9c0-214">*appsettings* ファイル (*appsettings.{Environment}.json*)。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-214">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="ea9c0-215">以下を参照してください。<xref:fundamentals/configuration/index#json-configuration-provider></span><span class="sxs-lookup"><span data-stu-id="ea9c0-215">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="ea9c0-216">環境変数 (アプリがホストされている各システムで設定します)。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-216">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="ea9c0-217">「 <xref:fundamentals/host/generic-host#environmentname> 」および「 <xref:security/app-secrets#environment-variables>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-217">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="ea9c0-218">Secret Manager (開発環境の場合のみ)</span><span class="sxs-lookup"><span data-stu-id="ea9c0-218">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="ea9c0-219">以下を参照してください。<xref:security/app-secrets></span><span class="sxs-lookup"><span data-stu-id="ea9c0-219">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="ea9c0-220">環境別の起動のクラスとメソッド</span><span class="sxs-lookup"><span data-stu-id="ea9c0-220">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="ea9c0-221">IWebHostEnvironment を Startup.Configure に挿入する</span><span class="sxs-lookup"><span data-stu-id="ea9c0-221">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="ea9c0-222"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> を `Startup.Configure` に挿入します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-222">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="ea9c0-223">この方法は、環境ごとのコードの違いが最小限である少数の環境に対して、アプリが `Startup.Configure` の調整のみを必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-223">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="ea9c0-224">IWebHostEnvironment を Startup クラスに挿入する</span><span class="sxs-lookup"><span data-stu-id="ea9c0-224">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="ea9c0-225"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> を `Startup` コンストラクターに挿入します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-225">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="ea9c0-226">この方法は、環境ごとのコードの違いが最小限である少数の環境に対してのみ、アプリが `Startup` の構成を必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-226">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="ea9c0-227">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-227">In the following example:</span></span>

* <span data-ttu-id="ea9c0-228">環境は `_env` フィールドに保持されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-228">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="ea9c0-229">`_env` は、アプリの環境に基づいてスタートアップ構成を適用するために `ConfigureServices` および `Configure` で使用されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-229">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IWebHostEnvironment _env;

    public Startup(IWebHostEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```
### <a name="startup-class-conventions"></a><span data-ttu-id="ea9c0-230">Startup クラスの規約</span><span class="sxs-lookup"><span data-stu-id="ea9c0-230">Startup class conventions</span></span>

<span data-ttu-id="ea9c0-231">ASP.NET Core アプリの起動時、[Startup クラス](xref:fundamentals/startup)がアプリをブートストラップします。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-231">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="ea9c0-232">アプリでは、環境 (たとえば `StartupDevelopment`) ごとに個別の `Startup` クラスを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-232">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="ea9c0-233">実行時に適切な `Startup` クラスが選択されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-233">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="ea9c0-234">名前のサフィックスが現在の環境と一致するクラスが優先されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-234">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="ea9c0-235">一致する `Startup{EnvironmentName}` クラスが見つからない場合、`Startup` クラスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-235">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="ea9c0-236">この方法は、環境ごとのコードの違いが多いいくつかの環境に対して、アプリがスタートアップの構成を必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-236">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="ea9c0-237">環境ベースの `Startup` クラスを実装するには、使用中の各環境に対して `Startup{EnvironmentName}` クラスと、フォールバックの `Startup` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-237">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="ea9c0-238">アセンブリ名を受け入れる [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) オーバーロードを使用します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-238">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="ea9c0-239">Startup メソッドの規約</span><span class="sxs-lookup"><span data-stu-id="ea9c0-239">Startup method conventions</span></span>

<span data-ttu-id="ea9c0-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) と [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) は、フォーム `Configure<EnvironmentName>` と `Configure<EnvironmentName>Services` の環境固有バージョンに対応しています。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="ea9c0-241">この方法は、環境ごとのコードの違いが多いいくつかの環境に対して、アプリがスタートアップの構成を必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-241">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="ea9c0-242">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ea9c0-242">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ea9c0-243">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ea9c0-243">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ea9c0-244">ASP.NET Core は、環境変数を利用し、ランタイム環境に基づいてアプリの動作を構成します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-244">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="ea9c0-245">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-245">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="ea9c0-246">環境</span><span class="sxs-lookup"><span data-stu-id="ea9c0-246">Environments</span></span>

<span data-ttu-id="ea9c0-247">ASP.NET Core はアプリの起動時に環境変数 `ASPNETCORE_ENVIRONMENT` を読み込み、その値を [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName) に格納します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-247">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="ea9c0-248">`ASPNETCORE_ENVIRONMENT` は任意の値に設定できますが、次の 3 つの値がフレームワークによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-248">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="ea9c0-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (既定値)</span><span class="sxs-lookup"><span data-stu-id="ea9c0-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="ea9c0-250">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-250">The preceding code:</span></span>

* <span data-ttu-id="ea9c0-251">`ASPNETCORE_ENVIRONMENT` が `Development` に設定されているとき、[UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-251">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="ea9c0-252">`ASPNETCORE_ENVIRONMENT` の値が次のいずれかに設定されているとき、[UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-252">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="ea9c0-253">[環境タグ ヘルパー ](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) は `IHostingEnvironment.EnvironmentName` の値を使用し、要素にマークアップを追加するか、要素からマークアップを除外します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-253">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="ea9c0-254">Windows と macOS では、環境変数と値で大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-254">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="ea9c0-255">Linux では、環境変数と値について、既定で**大文字と小文字が区別されます**。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-255">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="ea9c0-256">開発</span><span class="sxs-lookup"><span data-stu-id="ea9c0-256">Development</span></span>

<span data-ttu-id="ea9c0-257">開発環境では、実稼働で公開すべきではない機能を有効にすることがあります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-257">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="ea9c0-258">たとえば、ASP.NET Core テンプレートは、開発環境で[開発者例外ページ](xref:fundamentals/error-handling#developer-exception-page)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-258">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="ea9c0-259">ローカル コンピューターの開発環境をプロジェクトの *Properties\launchSettings.json* ファイルで設定できます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-259">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="ea9c0-260">*launchSettings.json* に設定されている環境値で、システム環境に設定されている値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-260">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="ea9c0-261">次の JSON では、*launchSettings.json* ファイルの 3 つのプロファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-261">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="ea9c0-262">*launchSettings.json* の `applicationUrl` プロパティでは、サーバー URL の一覧を指定できます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-262">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="ea9c0-263">一覧の URL 間には、セミコロンを次のように使用します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-263">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="ea9c0-264">アプリが [dotnet run](/dotnet/core/tools/dotnet-run) で起動すると、`"commandName": "Project"` を含む最初のプロファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-264">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="ea9c0-265">`commandName` の値により、起動する Web サーバーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-265">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="ea9c0-266">`commandName` は次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-266">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="ea9c0-267">`Project` (Kestrel を起動する)</span><span class="sxs-lookup"><span data-stu-id="ea9c0-267">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="ea9c0-268">アプリが [dotnet run](/dotnet/core/tools/dotnet-run) で起動されるとき:</span><span class="sxs-lookup"><span data-stu-id="ea9c0-268">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="ea9c0-269">利用できる場合、*launchSettings.json* が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-269">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="ea9c0-270">*launchSettings.json* の `environmentVariables` 設定により環境変数がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-270">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="ea9c0-271">ホスティング環境が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-271">The hosting environment is displayed.</span></span>

<span data-ttu-id="ea9c0-272">次の出力には、[dotnet run](/dotnet/core/tools/dotnet-run) で起動したアプリが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-272">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="ea9c0-273">Visual Studio のプロジェクト プロパティの **[デバッグ]** タブには、*launchSettings.json* ファイルを編集するための GUI があります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-273">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![プロジェクト プロパティ設定の環境変数](environments/_static/project-properties-debug.png)

<span data-ttu-id="ea9c0-275">プロジェクト プロファイルを変更した場合、Web サーバーを再起動するまで変更は適用されません。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-275">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="ea9c0-276">Kestrel がその環境に行われた変更を検出するには、先に再起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-276">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="ea9c0-277">*launchSettings.json* にはシークレットを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-277">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="ea9c0-278">ローカル開発のシークレットを格納するには、[シークレット マネージャー ツール](xref:security/app-secrets)を利用できます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-278">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="ea9c0-279">[Visual Studio Code](https://code.visualstudio.com/) を使用するとき、環境変数を *.vscode/launch.json* ファイルで設定できます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-279">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="ea9c0-280">次の例では、環境が `Development` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-280">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="ea9c0-281">*Properties/launchSettings.json* と同じように `dotnet run` でアプリを起動すると、プロジェクトの *.vscode/launch.json* ファイルは読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-281">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="ea9c0-282">*launchSettings.json* ファイルがない開発中アプリを起動するとき、環境変数で環境を設定するか、コマンドライン引数を `dotnet run` コマンドに設定します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-282">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="ea9c0-283">実稼働</span><span class="sxs-lookup"><span data-stu-id="ea9c0-283">Production</span></span>

<span data-ttu-id="ea9c0-284">実稼働環境は、セキュリティ、パフォーマンス、アプリの堅牢性が最大化されるように構成してください。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-284">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="ea9c0-285">開発とは異なる一般的な設定は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-285">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="ea9c0-286">キャッシュ。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-286">Caching.</span></span>
* <span data-ttu-id="ea9c0-287">クライアント側のリソースはバンドルされ、小さくされ、場合によっては CDN からサービスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-287">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="ea9c0-288">診断エラー ページが無効。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-288">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="ea9c0-289">フレンドリ エラー ページが有効。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-289">Friendly error pages enabled.</span></span>
* <span data-ttu-id="ea9c0-290">実稼働のログ記録と監視が有効。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-290">Production logging and monitoring enabled.</span></span> <span data-ttu-id="ea9c0-291">[Application Insights](/azure/application-insights/app-insights-asp-net-core) など。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-291">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="ea9c0-292">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="ea9c0-292">Set the environment</span></span>

<span data-ttu-id="ea9c0-293">環境変数またはプラットフォームの設定を使用してテスト用の特定の環境を設定すると、便利な場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-293">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="ea9c0-294">環境を設定しない場合、既定で `Production` に設定されます。ほとんどのデバッグ機能が無効になります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-294">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="ea9c0-295">環境を設定する手法は、オペレーティング システムによって異なります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-295">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="ea9c0-296">ホストが構築されると、アプリによって読み取られた最後の環境設定によってアプリの環境が決まります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-296">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="ea9c0-297">アプリの実行中にアプリの環境を変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-297">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="ea9c0-298">環境変数またはプラットフォームの設定</span><span class="sxs-lookup"><span data-stu-id="ea9c0-298">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="ea9c0-299">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ea9c0-299">Azure App Service</span></span>

<span data-ttu-id="ea9c0-300">[Azure App Service](https://azure.microsoft.com/services/app-service/) で環境を設定するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-300">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="ea9c0-301">**[App Services]** ブレードからアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-301">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="ea9c0-302">**[設定]** グループで、 **[構成]** ブレードを選択します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-302">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="ea9c0-303">**[アプリケーション設定]** タブで、 **[新しいアプリケーション設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-303">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="ea9c0-304">**[Add/Edit application setting]\(アプリケーション設定の追加/編集\)** ウィンドウで、 **[名前]** に `ASPNETCORE_ENVIRONMENT` を指定します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-304">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="ea9c0-305">**[値]** には、環境を指定します (`Staging` など)。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-305">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="ea9c0-306">デプロイ スロットがスワップされるときに、現在のスロットに環境設定を残したい場合は、 **[デプロイ スロットの設定]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-306">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="ea9c0-307">詳細については、Azure ドキュメントの「[Azure App Service でステージング環境を設定する](/azure/app-service/web-sites-staged-publishing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-307">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="ea9c0-308">**[OK]** を選択して、 **[Add/Edit application setting]\(アプリケーション設定の追加/編集\)** ウィンドウを閉じます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-308">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="ea9c0-309">**[構成]** ブレードの上部にある **[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-309">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="ea9c0-310">Azure App Service は、アプリ設定 (環境変数) が Azure Portal で追加、変更、削除された後、アプリを自動的に再起動します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-310">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="ea9c0-311">Windows</span><span class="sxs-lookup"><span data-stu-id="ea9c0-311">Windows</span></span>

<span data-ttu-id="ea9c0-312">[dotnet run](/dotnet/core/tools/dotnet-run) を使用してアプリを起動するときに現在のセッションに `ASPNETCORE_ENVIRONMENT` を設定するには、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-312">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="ea9c0-313">**コマンド プロンプト**</span><span class="sxs-lookup"><span data-stu-id="ea9c0-313">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="ea9c0-314">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="ea9c0-314">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="ea9c0-315">これらのコマンドは、現在のウィンドウでのみ有効になります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-315">These commands only take effect for the current window.</span></span> <span data-ttu-id="ea9c0-316">ウィンドウが閉じられると、`ASPNETCORE_ENVIRONMENT` 設定が初期設定またはコンピューター値に戻ります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-316">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="ea9c0-317">Windows でグローバルな値を設定するには、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-317">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="ea9c0-318">**[コントロール パネル]** > **[システム]** > **[システムの詳細設定]** の順に開き、`ASPNETCORE_ENVIRONMENT` 値を追加または編集します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-318">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![システムの詳細プロパティ](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 環境変数](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="ea9c0-321">管理コマンド プロンプトを開き、`setx` コマンドを使用するか、または管理用の PowerShell コマンド プロンプトを開いて、`[Environment]::SetEnvironmentVariable` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-321">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="ea9c0-322">**コマンド プロンプト**</span><span class="sxs-lookup"><span data-stu-id="ea9c0-322">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="ea9c0-323">`/M` スイッチは、システム レベルで環境変数を設定することを示します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-323">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="ea9c0-324">`/M` スイッチが使用されない場合、ユーザー アカウントに対する環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-324">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="ea9c0-325">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="ea9c0-325">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="ea9c0-326">`Machine` オプションの値は、システム レベルで環境変数を設定することを示します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-326">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="ea9c0-327">オプションの値が `User` に変更されると、ユーザー アカウントに対する環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-327">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="ea9c0-328">グローバルに設定されている `ASPNETCORE_ENVIRONMENT` の環境変数は、値の設定後に開いているすべてのコマンド ウィンドウで `dotnet run` に対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-328">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="ea9c0-329">**web.config**</span><span class="sxs-lookup"><span data-stu-id="ea9c0-329">**web.config**</span></span>

<span data-ttu-id="ea9c0-330">`ASPNETCORE_ENVIRONMENT` 環境変数を *web.config* で設定するには、<xref:host-and-deploy/aspnet-core-module#setting-environment-variables>の「*環境変数の設定*」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-330">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="ea9c0-331">**プロジェクト ファイルまたは発行プロファイル**</span><span class="sxs-lookup"><span data-stu-id="ea9c0-331">**Project file or publish profile**</span></span>

<span data-ttu-id="ea9c0-332">**Windows IIS の配置:** [発行プロファイル (.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-332">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="ea9c0-333">この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-333">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="ea9c0-334">**IIS アプリケーション プール**</span><span class="sxs-lookup"><span data-stu-id="ea9c0-334">**Per IIS Application Pool**</span></span>

<span data-ttu-id="ea9c0-335">分離されたアプリケーション プール (IIS 10.0 以降でサポートされています) で実行するアプリに対して `ASPNETCORE_ENVIRONMENT` 環境変数を設定するには、[環境変数 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある *AppCmd.exe コマンド*のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-335">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="ea9c0-336">`ASPNETCORE_ENVIRONMENT` 環境変数がアプリ プールで設定されている場合、その値は、システム レベルの設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-336">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ea9c0-337">IIS でアプリをホストして `ASPNETCORE_ENVIRONMENT` 環境変数を追加または変更するときは、次のいずれかの方法を使用してアプリで選択された新しい値を設定してください。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-337">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="ea9c0-338">コマンド プロンプトで `net stop was /y` を実行した後、`net start w3svc` を実行します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-338">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="ea9c0-339">サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-339">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="ea9c0-340">macOS</span><span class="sxs-lookup"><span data-stu-id="ea9c0-340">macOS</span></span>

<span data-ttu-id="ea9c0-341">macOS の現在の環境は、アプリ実行時にインラインで設定できます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-341">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="ea9c0-342">あるいは、アプリを実行する前に `export` で環境を設定します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-342">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="ea9c0-343">コンピューター レベルの環境変数は *.bashrc* または *.bash_profile* ファイルに設定されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-343">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="ea9c0-344">任意のテキスト エディターを利用し、ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-344">Edit the file using any text editor.</span></span> <span data-ttu-id="ea9c0-345">次のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-345">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="ea9c0-346">Linux</span><span class="sxs-lookup"><span data-stu-id="ea9c0-346">Linux</span></span>

<span data-ttu-id="ea9c0-347">Linux ディストリビューションの場合、セッション ベースの変数設定にはコマンド プロンプトで `export` コマンドを使用し、コンピューター レベルの環境設定には *bash_profile* ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-347">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="ea9c0-348">コードで環境を設定する</span><span class="sxs-lookup"><span data-stu-id="ea9c0-348">Set the environment in code</span></span>

<span data-ttu-id="ea9c0-349">ホストのビルド時に <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-349">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="ea9c0-350">以下を参照してください。<xref:fundamentals/host/web-host#environment></span><span class="sxs-lookup"><span data-stu-id="ea9c0-350">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="ea9c0-351">環境別の構成</span><span class="sxs-lookup"><span data-stu-id="ea9c0-351">Configuration by environment</span></span>

<span data-ttu-id="ea9c0-352">環境ごとに構成を読み込む場合の推奨事項は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-352">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="ea9c0-353">*appsettings* ファイル (*appsettings.{Environment}.json*)。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-353">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="ea9c0-354">以下を参照してください。<xref:fundamentals/configuration/index#json-configuration-provider></span><span class="sxs-lookup"><span data-stu-id="ea9c0-354">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="ea9c0-355">環境変数 (アプリがホストされている各システムで設定します)。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-355">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="ea9c0-356">「 <xref:fundamentals/host/web-host#environment> 」および「 <xref:security/app-secrets#environment-variables>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-356">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="ea9c0-357">Secret Manager (開発環境の場合のみ)</span><span class="sxs-lookup"><span data-stu-id="ea9c0-357">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="ea9c0-358">以下を参照してください。<xref:security/app-secrets></span><span class="sxs-lookup"><span data-stu-id="ea9c0-358">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="ea9c0-359">環境別の起動のクラスとメソッド</span><span class="sxs-lookup"><span data-stu-id="ea9c0-359">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="ea9c0-360">IHostingEnvironment を Startup.Configure に挿入する</span><span class="sxs-lookup"><span data-stu-id="ea9c0-360">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="ea9c0-361"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> を `Startup.Configure` に挿入します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-361">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="ea9c0-362">この方法は、環境ごとのコードの違いが最小限である少数の環境に対してのみ、アプリが `Startup.Configure` の構成だけを必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-362">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="ea9c0-363">IHostingEnvironment を Startup クラスに挿入する</span><span class="sxs-lookup"><span data-stu-id="ea9c0-363">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="ea9c0-364">`Startup` コンストラクターに <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> を挿入し、`Startup` クラス全体で使用するフィールドにサービスを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-364">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="ea9c0-365">この方法は、環境ごとのコードの違いが最小限である少数の環境に対してのみ、アプリがスタートアップの構成を必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-365">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="ea9c0-366">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-366">In the following example:</span></span>

* <span data-ttu-id="ea9c0-367">環境は `_env` フィールドに保持されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-367">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="ea9c0-368">`_env` は、アプリの環境に基づいてスタートアップ構成を適用するために `ConfigureServices` および `Configure` で使用されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-368">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a><span data-ttu-id="ea9c0-369">Startup クラスの規約</span><span class="sxs-lookup"><span data-stu-id="ea9c0-369">Startup class conventions</span></span>

<span data-ttu-id="ea9c0-370">ASP.NET Core アプリの起動時、[Startup クラス](xref:fundamentals/startup)がアプリをブートストラップします。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-370">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="ea9c0-371">アプリでは、環境 (たとえば `StartupDevelopment`) ごとに個別の `Startup` クラスを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-371">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="ea9c0-372">実行時に適切な `Startup` クラスが選択されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-372">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="ea9c0-373">名前のサフィックスが現在の環境と一致するクラスが優先されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-373">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="ea9c0-374">一致する `Startup{EnvironmentName}` クラスが見つからない場合、`Startup` クラスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-374">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="ea9c0-375">この方法は、環境ごとのコードの違いが多いいくつかの環境に対して、アプリがスタートアップの構成を必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-375">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="ea9c0-376">環境ベースの `Startup` クラスを実装するには、使用中の各環境に対して `Startup{EnvironmentName}` クラスと、フォールバックの `Startup` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-376">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="ea9c0-377">アセンブリ名を受け入れる [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) オーバーロードを使用します。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-377">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="ea9c0-378">Startup メソッドの規約</span><span class="sxs-lookup"><span data-stu-id="ea9c0-378">Startup method conventions</span></span>

<span data-ttu-id="ea9c0-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) と [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) は、フォーム `Configure<EnvironmentName>` と `Configure<EnvironmentName>Services` の環境固有バージョンに対応しています。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="ea9c0-380">この方法は、環境ごとのコードの違いが多いいくつかの環境に対して、アプリがスタートアップの構成を必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="ea9c0-380">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="ea9c0-381">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ea9c0-381">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
