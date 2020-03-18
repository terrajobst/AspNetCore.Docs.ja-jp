---
title: ASP.NET Core アプリを配置するための Visual Studio 発行プロファイル (.pubxml)
author: rick-anderson
description: Visual Studio で発行プロファイルを作成し、それらを使用してさまざまなターゲットへの ASP.NET Core アプリの配置を管理する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 274dd2cd528d3766aa07f69aac3470a131c79ffe
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647348"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>ASP.NET Core アプリを配置するための Visual Studio 発行プロファイル (.pubxml)

作成者: [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このドキュメントでは、Visual Studio 2019 以降を使用して、発行プロファイルを作成および使用する方法に焦点を当てます。 Visual Studio を使用して作成した発行プロファイルは、MSBuild および Visual Studio で使用することができます。 Azure への発行の手順については、<xref:tutorials/publish-to-azure-webapp-using-vs> を参照してください。

`dotnet new mvc` コマンドでは、次の最上位レベルの [\<Project> 要素](/visualstudio/msbuild/project-element-msbuild)を含むプロジェクト ファイルが生成されます。

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

上の `<Project>` 要素の `Sdk` 属性では、MSBuild の[プロパティ](/visualstudio/msbuild/msbuild-properties)と[ターゲット](/visualstudio/msbuild/msbuild-targets)が、それぞれ *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* と *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* からインポートされます。 (Visual Studio 2019 Enterprise の場合) `$(MSBuildSDKsPath)` の既定の場所は、 *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* フォルダーです。

`Microsoft.NET.Sdk.Web` (Web SDK) は、`Microsoft.NET.Sdk` (.NET Core SDK) や `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)) などの他の SDK に依存します。 依存する各 SDK に関連付けられている MSBuild のプロパティとターゲットがインポートされます。 発行ターゲットでは、使われる発行方法に基づいて、適切なターゲットのセットがインポートされます。

MSBuild または Visual Studio がプロジェクトを読み込むと、次の高レベルのアクションが発生します。

* プロジェクトをビルドする
* 発行するファイルを計算する
* ターゲットにファイルを発行する

## <a name="compute-project-items"></a>プロジェクト項目を比較する

プロジェクトが読み込まれると、[MSBuild プロジェクト項目](/visualstudio/msbuild/common-msbuild-project-items) (ファイル) が計算されます。 項目の種類によって、ファイルの処理方法が決まります。 既定で、 *.cs* ファイルは `Compile` 項目一覧に含まれています。 `Compile` 項目一覧のファイルがコンパイルされます。

`Content` 項目一覧には、ビルドの出力に加え、発行されるファイルが含まれています。 既定では、`wwwroot\**`、`**\*.config`、`**\*.json` というパターンに一致するファイルが、`Content` の項目一覧に含まれます。 たとえば、`wwwroot\**` [glob パターン](https://gruntjs.com/configuring-tasks#globbing-patterns)は、*wwwroot* フォルダーとそのサブフォルダー内のすべてのファイルと一致します。

::: moniker range=">= aspnetcore-3.0"

Web SDK では、[Razor SDK](xref:razor-pages/sdk) がインポートされます。 その結果、`**\*.cshtml` および `**\*.razor` というパターンに一致するファイルが、`Content` の項目一覧に含まれます。

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Web SDK では、[Razor SDK](xref:razor-pages/sdk) がインポートされます。 その結果、`**\*.cshtml` というパターンに一致するファイルが、`Content` の項目一覧に含まれます。

::: moniker-end

発行一覧に明示的にファイルを追加するには、「[ファイルを含める](#include-files)」セクションで説明されているように、 *.csproj* ファイルに直接ファイルを追加します。

Visual Studio で **[発行]** ボタンを選択するか、コマンド ラインから発行すると、以下が実行されます。

* プロパティ/項目が計算されます (ビルドに必要なファイル)。
* **Visual Studio のみ**: NuGet パッケージが復元されます。 (CLI では、ユーザーが明示的に復元する必要があります)。
* プロジェクトがビルドされます。
* 発行項目が計算されます (発行に必要なファイル)。
* プロジェクトが発行されます (計算されたファイルが発行先にコピーされます)。

ASP.NET Core プロジェクトは、プロジェクト ファイルの `Microsoft.NET.Sdk.Web` を参照して、*app_offline.htm* ファイルを Web アプリのディレクトリのルートに配置します。 ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。 詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。

## <a name="basic-command-line-publishing"></a>基本的なコマンド ラインからの発行

コマンド ラインからの発行は、.NET Core をサポートするすべてのプラットフォームで機能し、Visual Studio は必要ありません。 次の例では、プロジェクト ディレクトリ ( *.csproj* ファイルが含まれているディレクトリ) から .NET Core CLI の [dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドが実行されます。 プロジェクト フォルダーが現在の作業ディレクトリでない場合は、プロジェクト ファイルのパスで明示的に渡します。 次に例を示します。

```dotnetcli
dotnet publish C:\Webs\Web1
```

Web アプリを作成して発行するには、次のコマンドを実行します。

```dotnetcli
dotnet new mvc
dotnet publish
```

`dotnet publish` コマンドでは次のような出力が生成されます。

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

既定の発行フォルダーの形式は *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\* です。 たとえば、*bin\Debug\netcoreapp2.2\publish\\* などです。

次のコマンドでは、`Release` ビルドと発行ディレクトリを指定します。

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

`dotnet publish` コマンドは、`Publish` ターゲットを呼び出す MSBuild を呼び出します。 `dotnet publish` に渡されたすべてのパラメーターが MSBuild に渡されます。 `-c` と `-o` のパラメーターは、それぞれ MSBuild の `Configuration` と `OutputPath` にマップします。

MSBuild のプロパティは、次のいずれかの形式を使用して渡すことができます。

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

たとえば、次のコマンドは、`Release` ビルドをネットワーク共有に発行します。 ネットワーク共有は、スラッシュを使用して指定します ( *//r8/* )。 .NET Core がサポートされるすべてのプラットフォームで使用できます。

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

配置用に発行したアプリが実行されていないことを確認します。 アプリが実行中は、*publish* フォルダー内のファイルがロックされます。 ロックされているファイルはコピーできないため、配置は行われません。

## <a name="publish-profiles"></a>プロファイルを発行する

このセクションでは、Visual Studio 2019 以降を使用して発行プロファイルが作成されます。 プロファイルが作成されると、Visual Studio またはコマンド ラインから発行できるようになります。 発行プロファイルは発行プロセスを簡略化でき、任意の数のプロファイルが存在できます。

次のいずれかの方法で、Visual Studio で発行プロファイルを作成します。

* **ソリューション エクスプローラー**で、プロジェクトを右クリックして **[発行]** をクリックします。
* **[ビルド]** メニューの **[{PROJECT NAME} の発行]** を選択します。

アプリ機能ページの **[発行]** タブが表示されます。 プロジェクトに発行プロファイルが含まれていない場合は、 **[発行先を選択]** のページが表示されます。 次の発行先のいずれかを選択するように求められます。

* Azure App Service
* Azure App Service on Linux
* Azure Virtual Machines
* フォルダー
* (任意の Web サーバーの) IIS、FTP、Web 配置
* インポート プロファイル

最も適切な発行先を決定するには、[自分に合った発行オプション](/visualstudio/ide/not-in-toc/web-publish-options)に関する記事を参照してください。

発行先を **[フォルダー]** に選択した場合は、発行された資産を保存するフォルダーのパスを指定します。 既定のフォルダー パスは *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\* です。 たとえば、*bin\Release\netcoreapp2.2\publish\\* などです。 **[プロファイルの作成]** ボタンを選択して完了します。

発行プロファイルが作成されると、 **[発行]** タブの内容が変化します。 新しく作成したプロファイルがドロップダウン リストに表示されます。 別の新しいプロファイルを作成するには、ドロップダウン リストから **[新しいプロファイルの作成]** を選択します。

Visual Studio の発行ツールでは、発行プロファイルについて説明する *Properties/PublishProfiles/{PROFILE NAME}.pubxml* という MSBuild ファイルが作成されます。 *.pubxml* ファイルは:

* 発行構成の設定を含み、発行プロセスによって使用されます。
* 変更して、ビルドと発行プロセスをカスタマイズできます。

Azure ターゲットに発行する場合、 *.pubxml* ファイルには、Azure サブスクリプション識別子が含まれます。 そのターゲットの種類では、このファイルをソース管理に追加することはお勧めしません。 Azure 以外のターゲットに発行する場合は、 *.pubxml* ファイルをチェックインするほうが安全です。

機微な情報 (発行パスワードなど) は、個々のユーザー/コンピューター レベルで暗号化されます。 それは、*Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* ファイルに格納されます。 このファイルには機微な情報が格納される可能性があるため、ソース コード管理にチェックインしないでください。

ASP.NET Core で Web アプリを発行する方法の概要については、<xref:host-and-deploy/index> を参照してください。 ASP.NET Core Web アプリを発行するために必要な MSBuild タスクとターゲットは、[aspnet/websdk リポジトリ](https://github.com/aspnet/websdk)のオープン ソースです。

以下のコマンドでは、フォルダー、MSDeploy、および [Kudu](https://github.com/projectkudu/kudu/wiki) 発行プロファイルを使用できます。 MSDeploy にはクロス プラットフォームのサポートがないため、次の MSDeploy オプションは、Windows でのみサポートされます。

**フォルダー (クロス プラットフォームで動作します):**

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

**MSDeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

**MSDeploy パッケージ:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

前の例では、

* `dotnet publish` と `dotnet build` により、任意のプラットフォームから Azure に発行する Kudu API がサポートされています。 Visual Studio の発行は、Kudu API をサポートしていますが、Azure へのクロスプラットフォームの発行は WebSDK がサポートしています。
* `dotnet publish` コマンドに `DeployOnBuild` を渡さないでください。

詳細については、「[Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish)」を参照してください。

次の内容の発行プロファイルを、プロジェクトの *Properties/PublishProfiles* フォルダーに追加します。

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a>フォルダーの発行の例

*FolderProfile* という名前のプロファイルを使用して発行する場合は、次のいずれかのコマンドを使用します。

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

.NET Core CLI の [dotnet build](/dotnet/core/tools/dotnet-build) コマンドにより `msbuild` が呼び出されて、ビルドと発行プロセスが実行されます。 `dotnet build` と `msbuild` のコマンドは、フォルダー プロファイルで渡す場合は同等です。 Windows で `msbuild` を直接呼び出すと、MSBuild の .NET Framework バージョンが使用されます。 フォルダー以外のプロファイルで `dotnet build` を呼び出すと:

* MSDeploy を使用する `msbuild` が呼び出されます。
* 失敗します (Windows で実行されている場合でも)。 フォルダー以外のプロファイルで発行するには、`msbuild` を直接呼び出します。

次のフォルダー発行プロファイルは、Visual Studio で作成され、ネットワーク共有に発行されます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

前の例の場合:

* `<ExcludeApp_Data>` プロパティは、XML スキーマの要件を満たすためだけに存在します。 プロジェクトのルートに *App_Data* フォルダーがある場合でも、`<ExcludeApp_Data>` プロパティが発行プロセスに影響することはありません。 *App_Data* フォルダーは ASP.NET 4.x プロジェクトのように特別な処理を受信しません。

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* `<LastUsedBuildConfiguration>` プロパティが `Release` に設定されている。 Visual Studio から発行すると、発行プロセスが開始されたときの値を使用して、`<LastUsedBuildConfiguration>` の値が設定されます。 `<LastUsedBuildConfiguration>` は特殊なので、インポートされる MSBuild ファイルでオーバーライドされないようにしてください。 ただし、このプロパティは次の方法のいずれかを使用してコマンドラインからオーバーライドすることができます。
  * .NET Core CLI の使用:

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * MSBuild の使用:

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  詳細については、「[MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx)」(MSBuild: 構成プロパティの設定方法) を参照してください。

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>コマンド ラインから MSDeploy エンドポイントに発行する

次の例では、Visual Studio によって作成された *AzureWebApp* という名前の ASP.NET Core Web アプリが使用されます。 Azure アプリ発行プロファイルは、Visual Studio を使用して追加されます。 プロファイルを作成する方法の詳細については、「[発行プロファイル](#publish-profiles)」セクションを参照してください。

発行プロファイルを使用してアプリを配置するには、Visual Studio の**開発者コマンド プロンプト**から `msbuild` コマンドを実行します。 コマンド プロンプトは、Windows タスク バー上の **[スタート]** メニューの *Visual Studio* フォルダーで利用できます。 簡単にアクセスできるようにするために、Visual Studio の **[ツール]** メニューにコマンド プロンプトを追加できます。 詳細については、「[Visual Studio 用開発者コマンド プロンプト](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio)」を参照してください。

MSBuild では次の構文が使用されます。

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* {PATH} &ndash; アプリのプロジェクト ファイルへのパス。
* {PROFILE} &ndash; 発行プロファイルの名前。
* {USERNAME} &ndash; MSDeploy ユーザー名。 {USERNAME} は発行プロファイルで確認できます。
* {PASSWORD} &ndash; MSDeploy パスワード。 *{PROFILE}.PublishSettings* ファイルから {PASSWORD} を取得します。 次のいずれかの方法で、 *.PublishSettings* ファイルをダウンロードします。
  * **ソリューション エクスプローラー**: **[ビュー]**  >  **[Cloud Explorer]** の順に選択します。 ご自分の Azure サブスクリプションを使用して接続します。 **App Services** を開きます。 アプリを右クリックします。 **[発行プロファイルのダウンロード]** を選択します。
  * Azure portal: Web アプリの **[概要]** ウィンドウで **[発行プロファイルの取得]** をクリックします。

次の例では、*AzureWebApp - Web Deploy* という名前の発行プロファイルが使用されます。

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Windows コマンド シェルからの .NET Core CLI の [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) コマンドで、発行プロファイルを使用することもできます。

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> `dotnet msbuild` コマンドはクロス プラットフォームのコマンドで、macOS および Linux 上で ASP.NET Core アプリをコンパイルすることができます。 ただし、macOS および Linux 上の MSBuild では、Azure またはその他の MSDeploy エンドポイントにアプリを配置することはできません。

## <a name="set-the-environment"></a>環境を設定する

発行プロファイル ( *.pubxml*) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを追加し、アプリの[環境](xref:fundamentals/environments)を設定します。

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

*web.config* の変換が必要な場合 (たとえば、構成、プロファイル、または環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。

## <a name="exclude-files"></a>ファイルを除外する

ASP.NET Core Web アプリを発行するときは、次の資産が含まれます。

* ビルド成果物
* 次の glob パターンと一致するフォルダーおよびファイル:
  * `**\*.config` (例: *web.config*)
  * `**\*.json` (例: *appsettings.json*)
  * `wwwroot\**`

MSBuild では、[glob パターン](https://gruntjs.com/configuring-tasks#globbing-patterns)がサポートされています。 たとえば、次の `<Content>` 要素では、*wwwroot/content* フォルダーとそのサブフォルダー内にあるテキスト ( *.txt*) ファイルのコピーが抑制されます。

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

上記のマークアップは、発行プロファイルまたは *.csproj* ファイルに追加できます。 *.csproj* ファイルに追加すると、プロジェクト内のすべての発行プロファイルにルールが追加されます。

次の `<MsDeploySkipRules>` 要素では、*wwwroot\content* フォルダーのすべてのファイルが除外されます。

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>` は、"*スキップされる*" ターゲットを配置サイトから削除しません。 `<Content>` のターゲットであるファイルとフォルダーは、配置サイトから削除されます。 たとえば、配置される Web アプリに次のファイルが含まれているとします。

* *Views/Home/About1.cshtml*
* *Views/Home/About2.cshtml*
* *Views/Home/About3.cshtml*

次の `<MsDeploySkipRules>` 要素を追加した場合、これらのファイルは配置サイトでは削除されません。

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

前述の `<MsDeploySkipRules>` 要素は、"*スキップされる*" ファイルが配置されないようにします。 それは、いったん配置されたファイルは削除しません。

次の `<Content>` 要素は、配置サイトのターゲット ファイルを削除します。

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

前述の `<Content>` 要素を使用してコマンド ライン配置を行うと、次のような出力が生成されます。

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a>インクルード ファイル

次のセクションでは、発行時のファイル インクルードのさまざまな方法を概説します。 「[一般的なファイル インクルード](#general-file-inclusion)」のセクションでは、Web SDK の発行先ファイルから提供される `DotNetPublishFiles` 項目を使用します。 「[選択的なファイル インクルード](#selective-file-inclusion)」のセクションでは、.NET Core SDK の発行先ファイルから提供される `ResolvedFileToPublish` 項目を使用します。 Web SDK は .NET Core SDK に依存するため、どちらの項目も ASP.NET Core プロジェクトで使用することができます。

### <a name="general-file-inclusion"></a>一般的なファイル インクルード

次の例の `<ItemGroup>` 要素は、プロジェクト ディレクトリの外部にあるフォルダーを発行済みサイトのフォルダーにコピーする方法を示しています。 次のマークアップの `<ItemGroup>` に追加されたすべてのファイルが既定で含まれます。

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

上のマークアップでは以下の操作が行われます。

* *.csproj* ファイルまたは発行プロファイルに追加できます。 *.csproj* ファイルに追加した場合は、プロジェクトの各発行プロファイルに含まれます。
* `Include` 属性の glob パターンに一致するファイルを格納するための `_CustomFiles` 項目を宣言します。 パターンで参照される*イメージ* フォルダーはプロジェクト ディレクトリの外部にあります。 `$(MSBuildProjectDirectory)` という名前の[予約済みプロパティ](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties)がプロジェクト ファイルの絶対パスを解決します。
* `DotNetPublishFiles` 項目にファイルの一覧を提供します。 既定では、項目の `<DestinationRelativePath>` 要素は空です。 既定値はマークアップでオーバーライドされ、`%(RecursiveDir)` などの[既知の項目メタデータ](/visualstudio/msbuild/msbuild-well-known-item-metadata)を使用します。 内部のテキストは、発行済みサイトの *wwwroot/images* フォルダーを表します。

### <a name="selective-file-inclusion"></a>選択的なファイル インクルード

次の例の強調表示されたマークアップは、次のことを示しています。

* プロジェクトの外部にあるファイルは、発行済みのサイトの *wwwroot* フォルダーにコピーされます。 *ReadMe2.md* のファイル名は維持されます。
* *wwwroot\Content* フォルダーは除外されます。
* *Views\Home\About2.cshtml* は除外されます。

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

上記の例では、既定の動作として `Include` 属性に提供されたファイルが常に発行済みサイトにコピーされる、`ResolvedFileToPublish` 項目を使用します。 既定の動作は、内部テキストが `Never` または `PreserveNewest` の子要素 `<CopyToPublishDirectory>` を含めるとオーバーライドされます。 次に例を示します。

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

他の配置例については、[Web SDK リポジトリの Readme](https://github.com/aspnet/websdk) を参照してください。

## <a name="run-a-target-before-or-after-publishing"></a>発行の前または後にターゲットを実行する

組み込みの `BeforePublish` と `AfterPublish` ターゲットは、ターゲットの発行前または後にターゲットを実行できます。 発行の前と後の両方でコンソール メッセージをログに記録するには、発行プロファイルに次の要素を追加します。

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>信頼されていない証明書を使用してサーバーに発行する

値が `True` の `<AllowUntrustedCertificate>` プロパティを発行プロファイルに追加します。

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Kudu サービス

Azure App Service での Web アプリのデプロイに含まれるファイルを表示するには、[Kudu サービス](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) を使用します。 `scm` トークンを Web アプリ名に追加します。 次に例を示します。

| URL                                    | 結果       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Web アプリ      |
| `http://mysite.scm.azurewebsites.net/` | Kudu サービス |

ファイルの表示、編集、削除、追加を行うには、[[デバッグ コンソール]](https://github.com/projectkudu/kudu/wiki/Kudu-console) メニュー項目を選択します。

## <a name="additional-resources"></a>その他の技術情報

* [Web 配置](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) は、IIS サーバーへの Web アプリと Web サイトの配置を簡略化します。
* [Web SDK GitHub リポジトリ](https://github.com/aspnet/websdk/issues):配置でのファイルの問題と要求機能。
* [Visual Studio から Azure VM に ASP.NET Web アプリを発行する](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
