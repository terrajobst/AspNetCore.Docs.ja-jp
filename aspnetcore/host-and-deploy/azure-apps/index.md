---
title: Azure App Service に ASP.NET Core アプリを展開する
author: guardrex
description: この記事には、Azure のホストと展開リソースへのリンクが含まれます。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/07/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 5da32b5fd1026263f721db442b2676d45b239b8d
ms.sourcegitcommit: 2d4c1732c4866ed26b83da35f7bc2ad021a9c701
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815601"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a>Azure App Service に ASP.NET Core アプリを展開する

[Azure App Service](https://azure.microsoft.com/services/app-service/) は ASP.NET Core を含む Web アプリをホストするための [Microsoft クラウド コンピューティング プラットフォーム サービス](https://azure.microsoft.com/)です。

## <a name="useful-resources"></a>役に立つリソース

「[App Service のドキュメント](/azure/app-service/)」は、Azure アプリのドキュメント、チュートリアル、サンプル、ハウツー ガイド、その他のリソースのホームです。 ASP.NET Core アプリのホスティングに関連する次の 2 つのチュートリアルは特に重要です。

[Azure に ASP.NET Core Web アプリを作成する](/azure/app-service/app-service-web-get-started-dotnet)  
Visual Studio を使用して ASP.NET Core Web アプリを作成し、Windows の Azure App Service に配置します。

[App Service on Linux で ASP.NET Core アプリを作成する](/azure/app-service/containers/quickstart-dotnetcore)  
コマンド ラインを使用して ASP.NET Core Web アプリを作成し、Linux の Azure App Service に配置します。

ASP.NET Core のドキュメントでは、次の記事を参照できます。

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Visual Studio を使用して Azure App Service に ASP.NET Core アプリを発行する方法を説明します。

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
Visual Studio で ASP.NET Core Web アプリを作成し、それを Azure App Service に配置する方法について説明します。Git を利用し、継続的に配置します。

[最初のパイプラインを作成する](/azure/devops/pipelines/get-started-yaml)  
ASP.NET Core アプリ用に CI ビルドを設定し、Azure App Service に継続的配置リリースを作成します。

[Azure Web アプリのサンドボックス](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Azure アプリのプラットフォームで適用される Azure App Service ランタイム実行の制限事項について説明します。

<xref:test/troubleshoot>  
ASP.NET Core プロジェクトでの警告とエラーについて説明し、トラブルシューティングを行います。

## <a name="application-configuration"></a>アプリケーション構成

### <a name="platform"></a>プラットフォーム

::: moniker range=">= aspnetcore-2.2"

64 ビット (x64) と 32 ビット (x86) アプリ用のランタイムは、Azure App Service 上に存在します。 App Service で使用できる [.NET Core SDK](/dotnet/core/sdk) は 32 ビットですが、[Kudu](https://github.com/projectkudu/kudu/wiki) コンソールまたは Visual Studio の発行プロセスを使用すれば、ローカルでビルドされた 64 ビット アプリを展開できます。 詳細については、「[アプリを発行および配置する](#publish-and-deploy-the-app)」セクションを参照してください。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

ネイティブの依存関係を含むアプリのため、32 ビット (x86) アプリ用のランタイムが Azure App Service 上に存在します。 App Service で使用できる [.NET Core SDK](/dotnet/core/sdk) は 32 ビットです。

::: moniker-end

.Net Core ランタイムおよび .NET Core SDK に関する情報など、.NET Core フレームワーク コンポーネントおよび配布メソッドの詳細については、[.NET Core: コンポジション](/dotnet/core/about#composition)に関するページを参照してください。

### <a name="packages"></a>パッケージ

次の NuGet パッケージを、Azure App Service にデプロイされたアプリ用の自動ログ記録機能を提供するために含めます。

* [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) は [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) を使用して Azure App Service と ASP.NET Core の Light-Up 統合を提供します。 追加されるログ記録機能は `Microsoft.AspNetCore.AzureAppServicesIntegration` パッケージによって提供されます。
* [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) は [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) を実行して、`Microsoft.Extensions.Logging.AzureAppServices` パッケージに Azure App Service 診断ログ記録プロバイダーを追加します。
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) はロガー実装を提供することで、Azure App Service 診断ログとログ ストリーミング機能をサポートします。

上記のパッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)からは使用できません。 .NET Framework を対象とする、または `Microsoft.AspNetCore.App` メタパッケージを参照するアプリは、アプリのプロジェクト ファイル内の個々 のパッケージを明示的に参照する必要があります。

## <a name="override-app-configuration-using-the-azure-portal"></a>Azure Portal を使用してアプリの構成をオーバーライドする

Azure Portal のアプリの設定により、アプリの環境変数の設定が許可されます。 環境変数は、[環境変数構成プロバイダー](xref:fundamentals/configuration/index#environment-variables-configuration-provider)で使用できます。

Azure Portal でアプリの設定が作成または変更され、 **[保存]** ボタンが選択された場合、Azure アプリは再起動されます。 環境変数は、サービスが再起動された後にアプリに適用されます。

::: moniker range=">= aspnetcore-3.0"

アプリが[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合、環境変数は既定ではアプリの構成に読み込まれません。開発者が構成プロバイダーを追加する必要があります。 開発者は、構成プロバーダーを追加する際に環境変数のプレフィックスを決定します。 詳細については、<xref:fundamentals/host/generic-host> および「[Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider)」(環境変数構成プロバイダー) をご覧ください。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

アプリが [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) を使用してホストをビルドする場合、ホストを構成する環境変数では `ASPNETCORE_` プレフィックスが使用されます。 詳細については、<xref:fundamentals/host/web-host> および「[Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider)」(環境変数構成プロバイダー) をご覧ください。

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>プロキシ サーバーとロード バランサーのシナリオ

要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスを転送するために、[アウトプロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)をホストするときに Forwarded Headers Middleware を構成する [IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)と、ASP.NET Core モジュールが構成されます。 追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。 詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。

## <a name="monitoring-and-logging"></a>監視およびログ記録

Azure App Service には、ASP.NET Core アプリのログ統合を有効にする **ASP.NET Core のログ記録拡張機能**が用意されています。 この拡張機能を App Service に自動的に追加するには、**App Service** 発行プロファイルで Visual Studio の**発行**プロセスを使用します。 Visual Studio を使用してアプリをデプロイしていない場合は、App Service の **[開発ツール]**  >  **[拡張機能]** ダイアログを使用して、Azure portal に拡張機能を手動でインストールします。

監視、ログ記録、トラブルシューティングに関する情報は、次の記事を参照してください。

[Azure App Service でアプリを監視する](/azure/app-service/web-sites-monitor)  
アプリと App Service プランに関するクォータとメトリックを確認する方法を説明します。

[Azure App Service のアプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)  
HTTP 状態コード、失敗した要求、Web サーバー アクティビティの診断ログを有効にしてアクセスする方法を説明します。

<xref:fundamentals/error-handling>  
ASP.NET Core アプリでエラーを処理するための一般的な手法について理解します。

<xref:test/troubleshoot-azure-iis>  
ASP.NET Core アプリを使用した Azure App Service の配置に関する問題を診断する方法を説明します。

<xref:host-and-deploy/azure-iis-errors-reference>  
Azure App Service/IIS によってホストされるアプリの一般的な配置の構成エラーのトラブルシューティングに関するアドバイスを参照してください。

## <a name="data-protection-key-ring-and-deployment-slots"></a>データ保護キー リングとデプロイ スロット

[データ保護キー](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)は *%HOME%\ASP.NET\DataProtection-Keys* フォルダーに保存されます。 このフォルダーはネットワーク ストレージにバックアップされ、アプリをホストしているすべてのマシンで同期されています。 保存中のキーは保護されていません。 このフォルダーから、単一のデプロイ スロットのアプリのすべてのインスタンスにキー リングが提供されます。 ステージングや運用などの別のデプロイ スロットでは、キー リングが共有されません。

デプロイ スロットを切り替えると、データ保護を使用しているすべてのシステムが前のスロット内のキー リングを使用して格納されたデータを復号化できなくなります。 ASP.NET Cookie ミドルウェアは、その Cookie の保護にデータ保護を使用します。 これにより、ユーザーが標準の ASP.NET Cookie ミドルウェアを使用するアプリからサインアウトします。 スロットに依存しないキー リング ソリューションの場合、次のような外部キー リング プロバイダーを使用します。

* Azure Blob Storage
* Azure Key Vault
* SQL ストア
* Redis Cache

詳細については、<xref:security/data-protection/implementation/key-storage-providers> を参照してください。

## <a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>Azure App Service に ASP.NET Core プレビュー リリースを展開する

アプリが .NET Core のプレビュー リリースに依存している場合は、次のいずれかの方法を使用します。

* [プレビュー サイト拡張機能をインストールする](#install-the-preview-site-extension)
* [自己完結型のプレビュー アプリを展開する](#deploy-a-self-contained-preview-app)。
* [コンテナー用の Web アプリで Docker を使用する](#use-docker-with-web-apps-for-containers)

### <a name="install-the-preview-site-extension"></a>プレビュー サイト拡張機能をインストールする

プレビュー サイト拡張機能の使用に関する問題が発生した場合は、[aspnet/AspNetCore issue](https://github.com/aspnet/AspNetCore/issues) を作成してください。

1. Azure Portal から App Service に移動します。
1. Web アプリを選択します。
1. 検索ボックスに「拡張」と入力して "拡張機能" にフィルターするか、管理ツールの一覧をスクロール ダウンします。
1. **[拡張機能]** を選びます。
1. **[追加]** を選びます。
1. **[ASP.NET Core {X.Y} ({x64|x86}) ランタイム]** の拡張機能を一覧から選択します。`{X.Y}` は ASP.NET Core のプレビュー バージョンで、`{x64|x86}` はプラットフォームを指定します。
1. **[OK]** を選んで法的条項に同意します。
1. **[OK]** を選択し、拡張機能をインストールします。

操作が完了すると、最新の .NET Core プレビューがインストールされます。 次のようにしてインストールを検証します。

1. **[高度なツール]** を選択します。
1. **[高度なツール]** で **[移動]** を選択します。
1. **[デバッグ コンソール]**  >  **[PowerShell]** のメニュー項目を選択します。
1. PowerShell のプロンプトで次のコマンドを実行します。 コマンドの `{X.Y}` を ASP.NET Core ランタイム バージョンに、`{PLATFORM}` をプラットフォームに置き換えます。

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   x64 プレビューのランタイムがインストールされていると、コマンドで `True` が返されます。

> [!NOTE]
> App Services アプリのプラットフォーム アーキテクチャ (x86/x64) が、Azure Portal で A シリーズ計算、またはより優れたホスティング階層でホストされているアプリの設定に設定されます。 アプリがインプロセス モードで実行されていて、プラットフォーム アーキテクチャが 64 ビット (x64) 向けに構成されている場合は、ASP.NET Core モジュールで 64 ビット プレビュー ランタイム (ある場合) が使用されます。 **ASP.NET Core {X.Y} (x64) ランタイム**の拡張機能をインストールします。
>
> x64 プレビュー ランタイムがインストールされたら、Kudu PowerShell コマンド ウィンドウで次のコマンドを実行して、インストールを確認します。 コマンドの `{X.Y}` を ASP.NET Core ランタイム バージョンに置き換えます。
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> x64 プレビューのランタイムがインストールされていると、コマンドで `True` が返されます。

> [!NOTE]
> **ASP.NET Core 拡張機能**によって、たとえば Azure のログ記録など、Azure App Services での ASP.NET Core の追加機能が有効になります。 この拡張機能は、Visual Studio からデプロイするときに自動的にインストールされます。 拡張機能がインストールされていない場合は、アプリにインストールします。

**ARM テンプレートでプレビュー サイト拡張機能を使用する**

ARM テンプレートを使用してアプリを作成し、展開する場合は、リソースの種類として `siteextensions` を使用してサイト拡張機能を Web アプリに追加することができます。 次に例を示します。

[!code-json[](index/sample/arm.json?highlight=2)]

### <a name="deploy-a-self-contained-preview-app"></a>自己完結型のプレビュー アプリを展開する

プレビュー ランタイムを対象とする[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) では、展開でプレビュー ランタイムを保持します。

自己完結型アプリを展開する場合:

* Azure App Service のサイトには、[プレビュー サイト拡張機能](#install-the-preview-site-extension)は必要ありません。
* アプリは、[フレームワークに依存する展開 (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd) に発行するときとは異なる方法に従って、発行される必要があります。

「[自己完結型アプリを展開する](#deploy-the-app-self-contained)」セクションのガイダンスに従ってください。

### <a name="use-docker-with-web-apps-for-containers"></a>コンテナー用の Web アプリで Docker を使用する

[Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) には最新のプレビュー Docker イメージが含まれています。 イメージを基本イメージとして使用できます。 通常は、イメージを使用して、Web App for Containers に展開します。

## <a name="publish-and-deploy-the-app"></a>アプリを発行および配置する

### <a name="deploy-the-app-framework-dependent"></a>フレームワークに依存するアプリを展開する

::: moniker range=">= aspnetcore-2.2"

64 ビットの[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:

* 64 ビットのアプリをビルドするには、64 ビットの .NET Core SDK を使用します。
* App Service の **[構成]**  >  **[全般設定]** で、 **[プラットフォーム]** を **[64 ビット]** に設定します。 アプリでは、プラットフォームのビット数の選択を有効にするために、Basic 以上のサービス プランを使用する必要があります。

::: moniker-end

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Visual Studio ツールバーから **[ビルド]**  >  **[発行 {アプリケーション名}]** を選択するか、**ソリューション エクスプローラー**でプロジェクトを右クリックして、 **[発行]** を選択します。
1. **[発行先を選択]** ダイアログで、 **[App Service]** が選択されていることを確認します。
1. **[詳細]** を選択します。 **[発行]** ダイアログが開きます。
1. **[発行]** ダイアログで、次の操作を行います。
   * **[リリース]** の構成が選択されていることを確認します。
   * **[展開モード]** ドロップダウン リストを開き、 **[フレームワーク依存]** を選択します。
   * **[ターゲット ランタイム]** として **[ポータブル]** を選択します。
   * 展開時に追加のファイルを削除する場合、 **[ファイル発行オプション]** を開いて、転送先で追加のファイルを削除するチェック ボックスを選択します。
   * **[保存]** を選択します。
1. 発行ウィザードの残りのメッセージに従って、新しいサイトを作成するか、既存のサイトを更新します。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. プロジェクト ファイルで、[ランタイム識別子 (RID)](/dotnet/core/rid-catalog) を指定しないでください。

1. コマンド シェルから [dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドを使って、リリースの構成でアプリを発行します。 次の例では、アプリがフレームワークに依存するアプリとして公開されています。

   ```console
   dotnet publish --configuration Release
   ```

1. *bin/Release/{TARGET FRAMEWORK}/publish* ディレクトリのコンテンツを App Service のサイトに移動します。 *publush* フォルダーの内容をご利用のローカル ハード ドライブまたはネットワーク共有から直接 [Kudu](https://github.com/projectkudu/kudu/wiki) コンソールの App Service にドラッグする場合は、Kudu コンソールの `D:\home\site\wwwroot` フォルダーにファイルをドラッグします。

---

### <a name="deploy-the-app-self-contained"></a>自己完結型アプリを展開する

[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) には、Visual Studio またはコマンドライン インターフェイス (CLI) ツールを使用します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Visual Studio ツールバーから **[ビルド]**  >  **[発行 {アプリケーション名}]** を選択するか、**ソリューション エクスプローラー**でプロジェクトを右クリックして、 **[発行]** を選択します。
1. **[発行先を選択]** ダイアログで、 **[App Service]** が選択されていることを確認します。
1. **[詳細]** を選択します。 **[発行]** ダイアログが開きます。
1. **[発行]** ダイアログで、次の操作を行います。
   * **[リリース]** の構成が選択されていることを確認します。
   * **[展開モード]** ドロップダウン リストを開いて、 **[自己完結]** を選択します。
   * **[ターゲット ランタイム]** ドロップダウン リストからターゲット ランタイムを選択します。 既定値は、`win-x86` です。
   * 展開時に追加のファイルを削除する場合、 **[ファイル発行オプション]** を開いて、転送先で追加のファイルを削除するチェック ボックスを選択します。
   * **[保存]** を選択します。
1. 発行ウィザードの残りのメッセージに従って、新しいサイトを作成するか、既存のサイトを更新します。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. プロジェクト ファイルで、1 つまたは複数の[ランタイムの識別子 (RID)](/dotnet/core/rid-catalog) を指定します。 単一の RID に `<RuntimeIdentifier>` (単数形) を使用するか、`<RuntimeIdentifiers>` (複数形) を使用して RID のセミコロン区切りのリストを提供します。 次に例では、`win-x86` RID が指定されています。

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. コマンド シェルから [dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドを使って、ホストのランタイムに対するリリースの構成でアプリを発行します。 次の例では、アプリは `win-x86` RID に発行されます。 `--runtime` オプションに指定された RID は、プロジェクト ファイルの `<RuntimeIdentifier>` (`<RuntimeIdentifiers>`) プロパティで提供される必要があります。

   ```console
   dotnet publish --configuration Release --runtime win-x86
   ```

1. *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* ディレクトリのコンテンツを App Service のサイトに移動します。 *publush* フォルダーの内容をご利用のローカル ハード ドライブまたはネットワーク共有から直接 Kudu コンソールの App Service にドラッグする場合は、Kudu コンソールの `D:\home\site\wwwroot` フォルダーにファイルをドラッグします。

---

## <a name="protocol-settings-https"></a>プロトコル設定 (HTTPS)

セキュリティで保護されたプロトコル バインディングを使うと、HTTPS 経由で要求に応答するときに使用する証明書を指定できます。 バインディングには、特定のホスト名に向けて発行された有効なプライベート証明書 ( *.pfx*) が必要です。 詳しくは、「[チュートリアル: 既存のカスタム SSL 証明書を Azure App Service にバインドする](/azure/app-service/app-service-web-tutorial-custom-ssl)」をご覧ください。

## <a name="transform-webconfig"></a>web.config を変換する

発行時に *web.config* を変換する必要がある場合 (たとえば、構成、プロファイル、環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* [App Service の概要](/azure/app-service/app-service-web-overview)
* [Azure App Service: .NET アプリのホスティングに最適な場所 (55 分間の概要ビデオ)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Azure App Service の診断の概要](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

Windows Server の Azure App Service では[インターネット インフォメーション サービス (IIS)](https://www.iis.net/) が使用されます。 次のトピックは、基になる IIS テクノロジと関連しています。

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Windows Server - IT 管理者のコンテンツの現在と以前のリリース](/windows-server/windows-server-versions)
