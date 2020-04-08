---
title: ASP.NET Core で複数の環境を使用する
author: rick-anderson
description: ASP.NET Core アプリで複数の環境にわたりアプリの動作を制御する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: b0218b2c77c283c0849dca9491046534b88c5a77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78645356"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>ASP.NET Core で複数の環境を使用する

::: moniker range=">= aspnetcore-3.0"

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core は、環境変数を利用し、ランタイム環境に基づいてアプリの動作を構成します。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="environments"></a>環境

ASP.NET Core はアプリの起動時に環境変数 `ASPNETCORE_ENVIRONMENT` を読み込み、その値を [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) に格納します。 `ASPNETCORE_ENVIRONMENT` は任意の値に設定できますが、次の 3 つの値がフレームワークによって提供されます。

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production> (既定値)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

上記のコードでは次の操作が行われます。

* `ASPNETCORE_ENVIRONMENT` が `Development` に設定されているとき、[UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) を呼び出します。
* `ASPNETCORE_ENVIRONMENT` の値が次のいずれかに設定されているとき、[UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) を呼び出します。

  * `Staging`
  * `Production`
  * `Staging_2`

[環境タグ ヘルパー ](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) は `IHostingEnvironment.EnvironmentName` の値を使用し、要素にマークアップを追加するか、要素からマークアップを除外します。

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

Windows と macOS では、環境変数と値で大文字と小文字が区別されません。 Linux では、環境変数と値について、既定で**大文字と小文字が区別されます**。

### <a name="development"></a>開発

開発環境では、実稼働で公開すべきではない機能を有効にすることがあります。 たとえば、ASP.NET Core テンプレートは、開発環境で[開発者例外ページ](xref:fundamentals/error-handling#developer-exception-page)を有効にします。

ローカル コンピューターの開発環境をプロジェクトの *Properties\launchSettings.json* ファイルで設定できます。 *launchSettings.json* に設定されている環境値で、システム環境に設定されている値がオーバーライドされます。

次の JSON では、*launchSettings.json* ファイルの 3 つのプロファイルが表示されます。

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
> *launchSettings.json* の `applicationUrl` プロパティでは、サーバー URL の一覧を指定できます。 一覧の URL 間には、セミコロンを次のように使用します。
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

アプリが [dotnet run](/dotnet/core/tools/dotnet-run) で起動すると、`"commandName": "Project"` を含む最初のプロファイルが使用されます。 `commandName` の値により、起動する Web サーバーが指定されます。 `commandName` は次のいずれかになります。

* `IISExpress`
* `IIS`
* `Project` (Kestrel を起動する)

アプリが [dotnet run](/dotnet/core/tools/dotnet-run) で起動されるとき:

* 利用できる場合、*launchSettings.json* が読み込まれます。 *launchSettings.json* の `environmentVariables` 設定により環境変数がオーバーライドされます。
* ホスティング環境が表示されます。

次の出力には、[dotnet run](/dotnet/core/tools/dotnet-run) で起動したアプリが表示されます。

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Visual Studio のプロジェクト プロパティの **[デバッグ]** タブには、*launchSettings.json* ファイルを編集するための GUI があります。

![プロジェクト プロパティ設定の環境変数](environments/_static/project-properties-debug.png)

プロジェクト プロファイルを変更した場合、Web サーバーを再起動するまで変更は適用されません。 Kestrel がその環境に行われた変更を検出するには、先に再起動する必要があります。

> [!WARNING]
> *launchSettings.json* にはシークレットを格納しないでください。 ローカル開発のシークレットを格納するには、[シークレット マネージャー ツール](xref:security/app-secrets)を利用できます。

[Visual Studio Code](https://code.visualstudio.com/) を使用するとき、環境変数を *.vscode/launch.json* ファイルで設定できます。 次の例では、環境が `Development` に設定されます。

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

*Properties/launchSettings.json* と同じように `dotnet run` でアプリを起動すると、プロジェクトの *.vscode/launch.json* ファイルは読み込まれません。 *launchSettings.json* ファイルがない開発中アプリを起動するとき、環境変数で環境を設定するか、コマンドライン引数を `dotnet run` コマンドに設定します。

### <a name="production"></a>実稼働

実稼働環境は、セキュリティ、パフォーマンス、アプリの堅牢性が最大化されるように構成してください。 開発とは異なる一般的な設定は次のとおりです。

* キャッシュ。
* クライアント側のリソースはバンドルされ、小さくされ、場合によっては CDN からサービスが提供されます。
* 診断エラー ページが無効。
* フレンドリ エラー ページが有効。
* 実稼働のログ記録と監視が有効。 [Application Insights](/azure/application-insights/app-insights-asp-net-core) など。

## <a name="set-the-environment"></a>環境を設定する

環境変数またはプラットフォームの設定を使用してテスト用の特定の環境を設定すると、便利な場合がよくあります。 環境を設定しない場合、既定で `Production` に設定されます。ほとんどのデバッグ機能が無効になります。 環境を設定する手法は、オペレーティング システムによって異なります。

ホストが構築されると、アプリによって読み取られた最後の環境設定によってアプリの環境が決まります。 アプリの実行中にアプリの環境を変更することはできません。

### <a name="environment-variable-or-platform-setting"></a>環境変数またはプラットフォームの設定

#### <a name="azure-app-service"></a>Azure App Service

[Azure App Service](https://azure.microsoft.com/services/app-service/) で環境を設定するには、次の手順を実行します。

1. **[App Services]** ブレードからアプリを選択します。
1. **[設定]** グループで、 **[構成]** ブレードを選択します。
1. **[アプリケーション設定]** タブで、 **[新しいアプリケーション設定]** を選択します。
1. **[Add/Edit application setting]\(アプリケーション設定の追加/編集\)** ウィンドウで、 **[名前]** に `ASPNETCORE_ENVIRONMENT` を指定します。 **[値]** には、環境を指定します (`Staging` など)。
1. デプロイ スロットがスワップされるときに、現在のスロットに環境設定を残したい場合は、 **[デプロイ スロットの設定]** チェック ボックスをオンにします。 詳細については、Azure ドキュメントの「[Azure App Service でステージング環境を設定する](/azure/app-service/web-sites-staged-publishing)」をご覧ください。
1. **[OK]** を選択して、 **[Add/Edit application setting]\(アプリケーション設定の追加/編集\)** ウィンドウを閉じます。
1. **[構成]** ブレードの上部にある **[保存]** を選択します。

Azure App Service は、アプリ設定 (環境変数) が Azure Portal で追加、変更、削除された後、アプリを自動的に再起動します。

#### <a name="windows"></a>Windows

[dotnet run](/dotnet/core/tools/dotnet-run) を使用してアプリを起動するときに現在のセッションに `ASPNETCORE_ENVIRONMENT` を設定するには、次のコマンドを使用します。

**コマンド プロンプト**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

これらのコマンドは、現在のウィンドウでのみ有効になります。 ウィンドウが閉じられると、`ASPNETCORE_ENVIRONMENT` 設定が初期設定またはコンピューター値に戻ります。

Windows でグローバルな値を設定するには、次の方法のいずれかを使用します。

* **[コントロール パネル]** > **[システム]** > **[システムの詳細設定]** の順に開き、`ASPNETCORE_ENVIRONMENT` 値を追加または編集します。

  ![システムの詳細プロパティ](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 環境変数](environments/_static/windows_aspnetcore_environment.png)

* 管理コマンド プロンプトを開き、`setx` コマンドを使用するか、または管理用の PowerShell コマンド プロンプトを開いて、`[Environment]::SetEnvironmentVariable` を使用します。

  **コマンド プロンプト**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  `/M` スイッチは、システム レベルで環境変数を設定することを示します。 `/M` スイッチが使用されない場合、ユーザー アカウントに対する環境変数が設定されます。

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  `Machine` オプションの値は、システム レベルで環境変数を設定することを示します。 オプションの値が `User` に変更されると、ユーザー アカウントに対する環境変数が設定されます。

グローバルに設定されている `ASPNETCORE_ENVIRONMENT` の環境変数は、値の設定後に開いているすべてのコマンド ウィンドウで `dotnet run` に対して有効になります。

**web.config**

`ASPNETCORE_ENVIRONMENT` 環境変数を *web.config* で設定するには、<xref:host-and-deploy/aspnet-core-module#setting-environment-variables>の「*環境変数の設定*」のセクションを参照してください。

**プロジェクト ファイルまたは発行プロファイル**

**Windows IIS の配置:** [発行プロファイル (.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを追加します。 この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**IIS アプリケーション プール**

分離されたアプリケーション プール (IIS 10.0 以降でサポートされています) で実行するアプリに対して `ASPNETCORE_ENVIRONMENT` 環境変数を設定するには、[環境変数 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある *AppCmd.exe コマンド*のセクションを参照してください。 `ASPNETCORE_ENVIRONMENT` 環境変数がアプリ プールで設定されている場合、その値は、システム レベルの設定をオーバーライドします。

> [!IMPORTANT]
> IIS でアプリをホストして `ASPNETCORE_ENVIRONMENT` 環境変数を追加または変更するときは、次のいずれかの方法を使用してアプリで選択された新しい値を設定してください。
>
> * コマンド プロンプトで `net stop was /y` を実行した後、`net start w3svc` を実行します。
> * サーバーを再起動します。

#### <a name="macos"></a>macOS

macOS の現在の環境は、アプリ実行時にインラインで設定できます。

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

あるいは、アプリを実行する前に `export` で環境を設定します。

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

コンピューター レベルの環境変数は *.bashrc* または *.bash_profile* ファイルに設定されます。 任意のテキスト エディターを利用し、ファイルを編集します。 次のステートメントを追加します。

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Linux ディストリビューションの場合、セッション ベースの変数設定にはコマンド プロンプトで `export` コマンドを使用し、コンピューター レベルの環境設定には *bash_profile* ファイルを使用します。

### <a name="set-the-environment-in-code"></a>コードで環境を設定する

ホストのビルド時に <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> を呼び出します。 以下を参照してください。<xref:fundamentals/host/generic-host#environmentname>


### <a name="configuration-by-environment"></a>環境別の構成

環境ごとに構成を読み込む場合の推奨事項は次のとおりです。

* *appsettings* ファイル (*appsettings.{Environment}.json*)。 以下を参照してください。<xref:fundamentals/configuration/index#json-configuration-provider>
* 環境変数 (アプリがホストされている各システムで設定します)。 「 <xref:fundamentals/host/generic-host#environmentname> 」および「 <xref:security/app-secrets#environment-variables>」を参照してください。
* Secret Manager (開発環境の場合のみ) 以下を参照してください。<xref:security/app-secrets>

## <a name="environment-based-startup-class-and-methods"></a>環境別の起動のクラスとメソッド

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a>IWebHostEnvironment を Startup.Configure に挿入する

<xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> を `Startup.Configure` に挿入します。 この方法は、環境ごとのコードの違いが最小限である少数の環境に対して、アプリが `Startup.Configure` の調整のみを必要とする場合に便利です。

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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>IWebHostEnvironment を Startup クラスに挿入する

<xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> を `Startup` コンストラクターに挿入します。 この方法は、環境ごとのコードの違いが最小限である少数の環境に対してのみ、アプリが `Startup` の構成を必要とする場合に便利です。

次に例を示します。

* 環境は `_env` フィールドに保持されます。
* `_env` は、アプリの環境に基づいてスタートアップ構成を適用するために `ConfigureServices` および `Configure` で使用されます。

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
### <a name="startup-class-conventions"></a>Startup クラスの規約

ASP.NET Core アプリの起動時、[Startup クラス](xref:fundamentals/startup)がアプリをブートストラップします。 アプリでは、環境 (たとえば `StartupDevelopment`) ごとに個別の `Startup` クラスを定義することができます。 実行時に適切な `Startup` クラスが選択されます。 名前のサフィックスが現在の環境と一致するクラスが優先されます。 一致する `Startup{EnvironmentName}` クラスが見つからない場合、`Startup` クラスが使用されます。 この方法は、環境ごとのコードの違いが多いいくつかの環境に対して、アプリがスタートアップの構成を必要とする場合に便利です。

環境ベースの `Startup` クラスを実装するには、使用中の各環境に対して `Startup{EnvironmentName}` クラスと、フォールバックの `Startup` クラスを作成します。

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

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

アセンブリ名を受け入れる [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) オーバーロードを使用します。

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

### <a name="startup-method-conventions"></a>Startup メソッドの規約

[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) と [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) は、フォーム `Configure<EnvironmentName>` と `Configure<EnvironmentName>Services` の環境固有バージョンに対応しています。 この方法は、環境ごとのコードの違いが多いいくつかの環境に対して、アプリがスタートアップの構成を必要とする場合に便利です。

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core は、環境変数を利用し、ランタイム環境に基づいてアプリの動作を構成します。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="environments"></a>環境

ASP.NET Core はアプリの起動時に環境変数 `ASPNETCORE_ENVIRONMENT` を読み込み、その値を [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName) に格納します。 `ASPNETCORE_ENVIRONMENT` は任意の値に設定できますが、次の 3 つの値がフレームワークによって提供されます。

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (既定値)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

上記のコードでは次の操作が行われます。

* `ASPNETCORE_ENVIRONMENT` が `Development` に設定されているとき、[UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) を呼び出します。
* `ASPNETCORE_ENVIRONMENT` の値が次のいずれかに設定されているとき、[UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) を呼び出します。

  * `Staging`
  * `Production`
  * `Staging_2`

[環境タグ ヘルパー ](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) は `IHostingEnvironment.EnvironmentName` の値を使用し、要素にマークアップを追加するか、要素からマークアップを除外します。

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

Windows と macOS では、環境変数と値で大文字と小文字が区別されません。 Linux では、環境変数と値について、既定で**大文字と小文字が区別されます**。

### <a name="development"></a>開発

開発環境では、実稼働で公開すべきではない機能を有効にすることがあります。 たとえば、ASP.NET Core テンプレートは、開発環境で[開発者例外ページ](xref:fundamentals/error-handling#developer-exception-page)を有効にします。

ローカル コンピューターの開発環境をプロジェクトの *Properties\launchSettings.json* ファイルで設定できます。 *launchSettings.json* に設定されている環境値で、システム環境に設定されている値がオーバーライドされます。

次の JSON では、*launchSettings.json* ファイルの 3 つのプロファイルが表示されます。

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
> *launchSettings.json* の `applicationUrl` プロパティでは、サーバー URL の一覧を指定できます。 一覧の URL 間には、セミコロンを次のように使用します。
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

アプリが [dotnet run](/dotnet/core/tools/dotnet-run) で起動すると、`"commandName": "Project"` を含む最初のプロファイルが使用されます。 `commandName` の値により、起動する Web サーバーが指定されます。 `commandName` は次のいずれかになります。

* `IISExpress`
* `IIS`
* `Project` (Kestrel を起動する)

アプリが [dotnet run](/dotnet/core/tools/dotnet-run) で起動されるとき:

* 利用できる場合、*launchSettings.json* が読み込まれます。 *launchSettings.json* の `environmentVariables` 設定により環境変数がオーバーライドされます。
* ホスティング環境が表示されます。

次の出力には、[dotnet run](/dotnet/core/tools/dotnet-run) で起動したアプリが表示されます。

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Visual Studio のプロジェクト プロパティの **[デバッグ]** タブには、*launchSettings.json* ファイルを編集するための GUI があります。

![プロジェクト プロパティ設定の環境変数](environments/_static/project-properties-debug.png)

プロジェクト プロファイルを変更した場合、Web サーバーを再起動するまで変更は適用されません。 Kestrel がその環境に行われた変更を検出するには、先に再起動する必要があります。

> [!WARNING]
> *launchSettings.json* にはシークレットを格納しないでください。 ローカル開発のシークレットを格納するには、[シークレット マネージャー ツール](xref:security/app-secrets)を利用できます。

[Visual Studio Code](https://code.visualstudio.com/) を使用するとき、環境変数を *.vscode/launch.json* ファイルで設定できます。 次の例では、環境が `Development` に設定されます。

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

*Properties/launchSettings.json* と同じように `dotnet run` でアプリを起動すると、プロジェクトの *.vscode/launch.json* ファイルは読み込まれません。 *launchSettings.json* ファイルがない開発中アプリを起動するとき、環境変数で環境を設定するか、コマンドライン引数を `dotnet run` コマンドに設定します。

### <a name="production"></a>実稼働

実稼働環境は、セキュリティ、パフォーマンス、アプリの堅牢性が最大化されるように構成してください。 開発とは異なる一般的な設定は次のとおりです。

* キャッシュ。
* クライアント側のリソースはバンドルされ、小さくされ、場合によっては CDN からサービスが提供されます。
* 診断エラー ページが無効。
* フレンドリ エラー ページが有効。
* 実稼働のログ記録と監視が有効。 [Application Insights](/azure/application-insights/app-insights-asp-net-core) など。

## <a name="set-the-environment"></a>環境を設定する

環境変数またはプラットフォームの設定を使用してテスト用の特定の環境を設定すると、便利な場合がよくあります。 環境を設定しない場合、既定で `Production` に設定されます。ほとんどのデバッグ機能が無効になります。 環境を設定する手法は、オペレーティング システムによって異なります。

ホストが構築されると、アプリによって読み取られた最後の環境設定によってアプリの環境が決まります。 アプリの実行中にアプリの環境を変更することはできません。

### <a name="environment-variable-or-platform-setting"></a>環境変数またはプラットフォームの設定

#### <a name="azure-app-service"></a>Azure App Service

[Azure App Service](https://azure.microsoft.com/services/app-service/) で環境を設定するには、次の手順を実行します。

1. **[App Services]** ブレードからアプリを選択します。
1. **[設定]** グループで、 **[構成]** ブレードを選択します。
1. **[アプリケーション設定]** タブで、 **[新しいアプリケーション設定]** を選択します。
1. **[Add/Edit application setting]\(アプリケーション設定の追加/編集\)** ウィンドウで、 **[名前]** に `ASPNETCORE_ENVIRONMENT` を指定します。 **[値]** には、環境を指定します (`Staging` など)。
1. デプロイ スロットがスワップされるときに、現在のスロットに環境設定を残したい場合は、 **[デプロイ スロットの設定]** チェック ボックスをオンにします。 詳細については、Azure ドキュメントの「[Azure App Service でステージング環境を設定する](/azure/app-service/web-sites-staged-publishing)」をご覧ください。
1. **[OK]** を選択して、 **[Add/Edit application setting]\(アプリケーション設定の追加/編集\)** ウィンドウを閉じます。
1. **[構成]** ブレードの上部にある **[保存]** を選択します。

Azure App Service は、アプリ設定 (環境変数) が Azure Portal で追加、変更、削除された後、アプリを自動的に再起動します。

#### <a name="windows"></a>Windows

[dotnet run](/dotnet/core/tools/dotnet-run) を使用してアプリを起動するときに現在のセッションに `ASPNETCORE_ENVIRONMENT` を設定するには、次のコマンドを使用します。

**コマンド プロンプト**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

これらのコマンドは、現在のウィンドウでのみ有効になります。 ウィンドウが閉じられると、`ASPNETCORE_ENVIRONMENT` 設定が初期設定またはコンピューター値に戻ります。

Windows でグローバルな値を設定するには、次の方法のいずれかを使用します。

* **[コントロール パネル]** > **[システム]** > **[システムの詳細設定]** の順に開き、`ASPNETCORE_ENVIRONMENT` 値を追加または編集します。

  ![システムの詳細プロパティ](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 環境変数](environments/_static/windows_aspnetcore_environment.png)

* 管理コマンド プロンプトを開き、`setx` コマンドを使用するか、または管理用の PowerShell コマンド プロンプトを開いて、`[Environment]::SetEnvironmentVariable` を使用します。

  **コマンド プロンプト**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  `/M` スイッチは、システム レベルで環境変数を設定することを示します。 `/M` スイッチが使用されない場合、ユーザー アカウントに対する環境変数が設定されます。

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  `Machine` オプションの値は、システム レベルで環境変数を設定することを示します。 オプションの値が `User` に変更されると、ユーザー アカウントに対する環境変数が設定されます。

グローバルに設定されている `ASPNETCORE_ENVIRONMENT` の環境変数は、値の設定後に開いているすべてのコマンド ウィンドウで `dotnet run` に対して有効になります。

**web.config**

`ASPNETCORE_ENVIRONMENT` 環境変数を *web.config* で設定するには、<xref:host-and-deploy/aspnet-core-module#setting-environment-variables>の「*環境変数の設定*」のセクションを参照してください。

**プロジェクト ファイルまたは発行プロファイル**

**Windows IIS の配置:** [発行プロファイル (.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを追加します。 この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**IIS アプリケーション プール**

分離されたアプリケーション プール (IIS 10.0 以降でサポートされています) で実行するアプリに対して `ASPNETCORE_ENVIRONMENT` 環境変数を設定するには、[環境変数 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある *AppCmd.exe コマンド*のセクションを参照してください。 `ASPNETCORE_ENVIRONMENT` 環境変数がアプリ プールで設定されている場合、その値は、システム レベルの設定をオーバーライドします。

> [!IMPORTANT]
> IIS でアプリをホストして `ASPNETCORE_ENVIRONMENT` 環境変数を追加または変更するときは、次のいずれかの方法を使用してアプリで選択された新しい値を設定してください。
>
> * コマンド プロンプトで `net stop was /y` を実行した後、`net start w3svc` を実行します。
> * サーバーを再起動します。

#### <a name="macos"></a>macOS

macOS の現在の環境は、アプリ実行時にインラインで設定できます。

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

あるいは、アプリを実行する前に `export` で環境を設定します。

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

コンピューター レベルの環境変数は *.bashrc* または *.bash_profile* ファイルに設定されます。 任意のテキスト エディターを利用し、ファイルを編集します。 次のステートメントを追加します。

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Linux ディストリビューションの場合、セッション ベースの変数設定にはコマンド プロンプトで `export` コマンドを使用し、コンピューター レベルの環境設定には *bash_profile* ファイルを使用します。

### <a name="set-the-environment-in-code"></a>コードで環境を設定する

ホストのビルド時に <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> を呼び出します。 以下を参照してください。<xref:fundamentals/host/web-host#environment>

### <a name="configuration-by-environment"></a>環境別の構成

環境ごとに構成を読み込む場合の推奨事項は次のとおりです。

* *appsettings* ファイル (*appsettings.{Environment}.json*)。 以下を参照してください。<xref:fundamentals/configuration/index#json-configuration-provider>
* 環境変数 (アプリがホストされている各システムで設定します)。 「 <xref:fundamentals/host/web-host#environment> 」および「 <xref:security/app-secrets#environment-variables>」を参照してください。
* Secret Manager (開発環境の場合のみ) 以下を参照してください。<xref:security/app-secrets>

## <a name="environment-based-startup-class-and-methods"></a>環境別の起動のクラスとメソッド

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>IHostingEnvironment を Startup.Configure に挿入する

<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> を `Startup.Configure` に挿入します。 この方法は、環境ごとのコードの違いが最小限である少数の環境に対してのみ、アプリが `Startup.Configure` の構成だけを必要とする場合に便利です。

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>IHostingEnvironment を Startup クラスに挿入する

`Startup` コンストラクターに <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> を挿入し、`Startup` クラス全体で使用するフィールドにサービスを割り当てます。 この方法は、環境ごとのコードの違いが最小限である少数の環境に対してのみ、アプリがスタートアップの構成を必要とする場合に便利です。

次に例を示します。

* 環境は `_env` フィールドに保持されます。
* `_env` は、アプリの環境に基づいてスタートアップ構成を適用するために `ConfigureServices` および `Configure` で使用されます。

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

### <a name="startup-class-conventions"></a>Startup クラスの規約

ASP.NET Core アプリの起動時、[Startup クラス](xref:fundamentals/startup)がアプリをブートストラップします。 アプリでは、環境 (たとえば `StartupDevelopment`) ごとに個別の `Startup` クラスを定義することができます。 実行時に適切な `Startup` クラスが選択されます。 名前のサフィックスが現在の環境と一致するクラスが優先されます。 一致する `Startup{EnvironmentName}` クラスが見つからない場合、`Startup` クラスが使用されます。 この方法は、環境ごとのコードの違いが多いいくつかの環境に対して、アプリがスタートアップの構成を必要とする場合に便利です。

環境ベースの `Startup` クラスを実装するには、使用中の各環境に対して `Startup{EnvironmentName}` クラスと、フォールバックの `Startup` クラスを作成します。

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

アセンブリ名を受け入れる [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) オーバーロードを使用します。

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

### <a name="startup-method-conventions"></a>Startup メソッドの規約

[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) と [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) は、フォーム `Configure<EnvironmentName>` と `Configure<EnvironmentName>Services` の環境固有バージョンに対応しています。 この方法は、環境ごとのコードの違いが多いいくつかの環境に対して、アプリがスタートアップの構成を必要とする場合に便利です。

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
