---
title: ASP.NET Core を使用した Azure App Service および IIS の一般的なエラーのリファレンス
author: guardrex
description: Azure Apps Service と IIS で ASP.NET Core アプリをホストするときに発生する一般的なエラーを解決する方法について助言を得ます。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/11/2019
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: f6afd6491181830f4d79486fa26a64423cd4a0ac
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70963674"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>ASP.NET Core を使用した Azure App Service および IIS の一般的なエラーのリファレンス

作成者: [Luke Latham](https://github.com/guardrex)

このトピックでは、一般的なエラーについて説明し、Azure Apps Service と IIS で ASP.NET Core アプリをホストするときに発生する固有のエラーを解決する方法を示します。

一般的なトラブルシューティング ガイダンスについては、「<xref:test/troubleshoot-azure-iis>」を参照してください。

次の情報を収集します。

* ブラウザーの動作 (ステータス コードとエラー メッセージ)
* アプリケーション イベント ログのエントリ
  * Azure App Service &ndash; (<xref:test/troubleshoot-azure-iis> 参照)。
  * IIS
    1. **[Windows]** メニューで **[スタート]** を選択し、「*Event Viewer*」と入力し、**Enter** を押します。
    1. **イベント ビューアー**が開いたら、サイドバーで **[Windows ログ]**、**[アプリケーション]** の順に展開します。
* ASP.NET Core モジュールの stdout ログ エントリと debug ログ エントリ
  * Azure App Service &ndash; (<xref:test/troubleshoot-azure-iis> 参照)。
  * IIS &ndash; ASP.NET Core モジュール トピックの「[ログの作成とリダイレクト](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)」セクションと「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」セクションの指示に従います。

エラー情報を次の一般的なエラーと比較します。 一致が見つかった場合は、トラブルシューティングのアドバイスに従います。

このトピックではすべてのエラーを網羅しているわけではありません。 ここに記載されていないエラーに遭遇した場合、このトピックの一番下にある **[コンテンツ フィードバック]** ボタンで新しい問題を登録してください。その際、エラーを再現する方法を詳しく教えてください。

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="installer-unable-to-obtain-vc-redistributable"></a>インストーラーが VC++ 再頒布可能パッケージを取得できない

* **インストーラー例外:** 0x80072efd **--または--** 0x80072f76 - 特定できないエラー

* **インストーラー ログ例外&#8224;:** エラー 0x80072efd **--または--** 0x80072f76:EXE パッケージを実行できませんでした

  &#8224;ログの場所は *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log* です。

トラブルシューティング:

[NET Core ホスティング バンドル](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)のインストール時にインストーラーがインターネットにアクセスできない場合、インストーラーは *Microsoft Visual C++ 2015 再頒布可能パッケージ*を取得できず、この例外が発生します。 [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53840)からインストーラーを入手します。 インストーラーが失敗する場合、[フレームワークに依存する展開 (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd) をホストするために必要な .NET Core ランタイムをサーバーが受け取っていない可能性があります。 FDD をホストしている場合は、**[プログラムと機能]** または **[アプリと機能]** でランタイムがインストールされていることを確認してください。 特定のランタイムが必要な場合、[.NET ダウンロード アーカイブ](https://dotnet.microsoft.com/download/archives)からダウンロードし、システムにインストールしてください。 ランタイムのインストール後、システムを再起動するか、コマンド プロンプトから **net stop was /y** に続けて **net start w3svc** を実行して IIS を再起動します。

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>OS のアップグレードによって 32 ビット ASP.NET Core モジュールが削除された

**アプリケーション ログ:** モジュール DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** を読み込めませんでした。 このデータはエラーです。

トラブルシューティング:

**C:\Windows\SysWOW64\inetsrv** ディレクトリにある OS ファイルでないファイルは、OS アップグレード時に保持されません。 OS アップグレードより前に ASP.NET Core モジュールをインストールしていた場合、OS アップグレード後に 32 ビット モードでアプリ プールを実行しようとすると、この問題が発生します。 OS アップグレード後に ASP.NET Core モジュールを修復してください。 「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」をご覧ください。 インストーラーを実行するときに **[修復]** を選択します。

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>サイト拡張機能の不足、32 ビット (x86) および 64 ビット (x64) サイト拡張機能がインストールされている、または間違ったプロセス ビットが設定されている

"*Azure App Services でホストしているアプリに適用されます。*"

* **ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス ハンドラーの読み込みエラー

* **アプリケーション ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。 インプロセス要求ハンドラーが見つかりませんでした。 hostfxr の呼び出し時にキャプチャされた出力:互換性のあるフレームワーク バージョンが見つかりませんでした。 指定したフレームワーク 'Microsoft.AspNetCore.App'、バージョン '{VERSION}-preview-\*' が見つかりませんでした。 アプリケーション '/LM/W3SVC/1416782824/ROOT' を起動できませんでした、エラー コード '0x8000ffff'。

* **ASP.NET Core モジュールの stdout ログ:** 互換性のあるフレームワーク バージョンが見つかりませんでした。 指定したフレームワーク 'Microsoft.AspNetCore.App'、バージョン '{VERSION}-preview-\*' が見つかりませんでした。

::: moniker range=">= aspnetcore-2.2"

* **ASP.NET Core モジュール デバッグ ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。 これが意味するところは、ほとんどの場合、アプリが正しく設定されていないということです。アプリケーションの対象であり、コンピューターにインストールされている Microsoft.NetCore.App と Microsoft.AspNetCore.App のバージョンを確認してください。 HRESULT が失敗し、次が返されました:0x8000ffff。 インプロセス要求ハンドラーが見つかりませんでした。 互換性のあるフレームワーク バージョンが見つかりませんでした。 指定したフレームワーク 'Microsoft.AspNetCore.App'、バージョン '{VERSION}-preview-\*' が見つかりませんでした。

::: moniker-end

トラブルシューティング:

* プレビュー ランタイムでアプリを実行している場合、アプリのビットとアプリのランタイム バージョンに一致する、32 ビット (x86) **または** 64 ビット (x64) のいずれかのサイト拡張機能をインストールします。 **両方の拡張機能や、拡張機能の複数のランタイム バージョンをインストールしないでください。**

  * ASP.NET Core {ランタイム バージョン} (x86) ランタイム
  * ASP.NET Core {ランタイム バージョン} (x64) ランタイム

  アプリを再起動します。 アプリが再起動するまで数秒待ちます。

* プレビュー ランタイムでアプリを実行していて、32 ビット (x86)、64 ビット (x64) 両方の[サイト拡張機能](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)がインストールされている場合、アプリのビットと一致しないサイト拡張機能をアンインストールします。 サイト拡張機能を削除した後、アプリを再起動します。 アプリが再起動するまで数秒待ちます。

* プレビュー ランタイムでアプリを実行していて、サイト拡張機能とアプリのビットが一致している場合、プレビュー サイト拡張機能の "*ランタイム バージョン*" がアプリのランタイム バージョンと一致していることを確認します。

* **[アプリケーション設定]** のアプリの **[プラットフォーム]** がアプリのビットと一致していることを確認します。

詳細については、<xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension> を参照してください。

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>x86 アプリが展開されますが、32 ビット アプリに対してアプリ プールは有効になりません。

* **ブラウザー:** HTTP エラー 500.30 - ANCM インプロセス起動失敗

* **アプリケーション ログ:** 物理ルートが '{PATH}' のアプリケーション '/LM/W3SVC/5/ROOT' に予想外のマネージド例外が発生しました、例外コード = '0xe0434352'。 詳細については、stderr ログを確認してください。 物理ルートが '{PATH}' のアプリケーション '/LM/W3SVC/5/ROOT' で clr とマネージド アプリケーションを読み込めませんでした。 CLR ワーカー スレッドが途中で終了しました

* **ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されましたが、空です。

::: moniker range=">= aspnetcore-2.2"

* **ASP.NET Core モジュール デバッグ ログ:** HRESULT が失敗し、次が返されました:0x8007023e

::: moniker-end

このシナリオは、自己完結型アプリの公開時、SDK によってトラップされます。 RID がプラットフォーム ターゲットに一致しない場合 (`win10-x64` RID とプロジェクト ファイルの `<PlatformTarget>x86</PlatformTarget>` など)、SDK からエラーが生成されます。

トラブルシューティング:

x86 フレームワークに依存する展開の場合 (`<PlatformTarget>x86</PlatformTarget>`)、32 ビット アプリに対して IIS アプリ プールを有効にします。 IIS Manager でアプリ プールの **[詳細設定]** を開き、**[32 ビット アプリケーションの有効化]** を **[True]** に設定します。

## <a name="platform-conflicts-with-rid"></a>プラットフォームが RID と競合している

* **ブラウザー:** HTTP エラー 502.5 - 処理エラー

* **アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' でプロセスを開始できませんでした、エラー コード = '0x80004005 : ff。

* **ASP.NET Core モジュールの stdout ログ:** 未処理の例外:System.BadImageFormatException:ファイルまたはアセンブリ '{ASSEMBLY}.dll' を読み込めませんでした。 正しくない形式のプログラムを読み込もうとしました。

トラブルシューティング:

* Kestrel でアプリをローカルに実行できることを確認します。 プロセスのエラーは、アプリ内の問題の結果である可能性があります。 詳細については、<xref:test/troubleshoot-azure-iis> を参照してください。

* Azure Apps 展開で、アプリケーションをアップグレードして新しいアセンブリを展開しようとしたときにこの例外が発生した場合は、以前の展開からすべてのファイルを手動で削除してください。 アップグレードしたアプリを展開するとき、互換性のないアセンブリが残っていると、`System.BadImageFormatException` 例外が発生します。

## <a name="uri-endpoint-wrong-or-stopped-website"></a>URI のエンドポイントが間違っているか、Web サイトが停止している

* **ブラウザー:** ERR_CONNECTION_REFUSED **--または--** 接続できません

* **アプリケーション ログ:** エントリなし

* **ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。

::: moniker range=">= aspnetcore-2.2"

* **ASP.NET Core モジュール デバッグ ログ:** ログ ファイルが作成されていません。

::: moniker-end

トラブルシューティング:

* アプリに対して正しい URI エンドポイントが使用されていることを確認します。 バインドを確認します。

* IIS Web サイトが *[停止]* 状態でないことを確認します。

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>CoreWebEngine または W3SVC サーバー機能が無効

**OS の例外:** ASP.NET Core モジュールを使用するには、IIS 7.0 CoreWebEngine および W3SVC の機能をインストールする必要があります。

トラブルシューティング:

適切な役割と機能が有効になっていることを確認します。 「[IIS 構成](xref:host-and-deploy/iis/index#iis-configuration)」を参照してください。

## <a name="incorrect-website-physical-path-or-app-missing"></a>Web サイト物理パスが間違っているか、アプリが見つからない

* **ブラウザー:** 403 許可されていません - アクセスが拒否されました **--または--** 403.14 許可されていません - Web サーバーは、このディレクトリの内容の一覧を表示しないように構成されています。

* **アプリケーション ログ:** エントリなし

* **ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。

::: moniker range=">= aspnetcore-2.2"

* **ASP.NET Core モジュール デバッグ ログ:** ログ ファイルが作成されていません。

::: moniker-end

トラブルシューティング:

IIS Web サイトの**基本設定**と物理アプリのフォルダーを確認します。 アプリが IIS Web サイトの**物理パス**にあるフォルダー内に配置されていることを確認します。

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>役割が正しくない、ASP.NET Core モジュールがインストールされていない、または不適切なアクセス許可

* **ブラウザー:** 500.19 内部サーバー エラー - ページに関連する構成データが無効であるため、要求されたページにアクセスできません。 **--または--** このページを表示できません

* **アプリケーション ログ:** エントリなし

* **ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。

::: moniker range=">= aspnetcore-2.2"

* **ASP.NET Core モジュール デバッグ ログ:** ログ ファイルが作成されていません。

::: moniker-end

トラブルシューティング:

* 適切な役割が有効になっていることを確認します。 「[IIS 構成](xref:host-and-deploy/iis/index#iis-configuration)」を参照してください。

* **[プログラムと機能]** または **[アプリと機能]** を開き、**[Windows Server Hosting]** がインストールされていることを確認します。 インストールされているプログラムの一覧に **[Windows Server Hosting]** がない場合、.NET Core ホスティング バンドルをダウンロードしてインストールします。

  [現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  詳細については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」をご覧ください。

* **[アプリケーション プール]** > **[プロセス モデル]** > **[ID]** が **ApplicationPoolIdentity** に設定されていることを確認します。または、アプリの展開フォルダーにアクセスするための正しいアクセス許可がカスタム ID に設定されていることを確認します。

* ASP.NET Core ホスティング バンドルをアンインストールし、以前のバージョンのホスティング バンドルをインストールした場合、*applicationHost.config* ファイルには ASP.NET Core モジュールのセクションが含まれません。 *applicationHost.config* で *%windir%/System32/inetsrv/config* を開き、`<configuration><configSections><sectionGroup name="system.webServer">` セクション グループを見つけます。 セクション グループに ASP.NET Core モジュールのセクションがない場合は、セクション要素を追加します。

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  または、ASP.NET Core ホスティング バンドルの最新バージョンをインストールします。 最新バージョンは、ポートされている ASP.NET Core アプリと下位互換性があります。

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>processPath の誤り、PATH 変数の欠如、ホスティング バンドルが未インストール、システムまたは IIS が再起動されていない、VC++ 再頒布可能パッケージが未インストール、dotnet.exe アクセス違反

::: moniker range=">= aspnetcore-2.2"

* **ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス ハンドラーの読み込みエラー

* **アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"{...}" ' でプロセスを開始できませんでした、エラー コード = '0x80070002 :0. アプリケーション '{PATH}' は開始できませんでした。 実行可能ファイルが '{PATH}' で見つかりませんでした。 アプリケーション '/LM/W3SVC/2/ROOT' を起動できませんでした、エラー コード '0x8007023e'。

* **ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。

* **ASP.NET Core モジュール デバッグ ログ:** イベント ログ:'アプリケーション '{PATH}' を起動できませんでした。 実行可能ファイルが '{PATH}' で見つかりませんでした。 HRESULT が失敗し、次が返されました:0x8007023e

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **ブラウザー:** HTTP エラー 502.5 - 処理エラー

* **アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"{...}" ' でプロセスを開始できませんでした、エラー コード = '0x80070002 :0.

* **ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されましたが、空です。

::: moniker-end

トラブルシューティング:

* Kestrel でアプリをローカルに実行できることを確認します。 プロセスのエラーは、アプリ内の問題の結果である可能性があります。 詳細については、<xref:test/troubleshoot-azure-iis> を参照してください。

* *web.config* で `<aspNetCore>` 要素の *processPath* 属性を調べ、フレームワークに依存する展開 (FDD) の場合はそれが `dotnet` であること、[自己完結型展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) の場合はそれが `.\{ASSEMBLY}.exe` であることを確認します。

* FDD の場合、PATH 設定で *dotnet.exe* にアクセスできていない可能性があります。 *C:\Program Files\dotnet\\* がシステムの PATH 設定に含まれていることを確認します。

* FDD では、アプリ プールのユーザー ID で *dotnet.exe* にアクセスできていない可能性があります。 アプリ プール ユーザー ID に、*C:\Program Files\dotnet* ディレクトリへのアクセス許可が設定されていることを確認します。 *C:\Program Files\dotnet* とアプリのディレクトリに、アプリ プール ユーザー ID に対する拒否ルールが構成されていないことを確認します。

* FDD を配置し、IIS を再起動せずに .NET Core をインストールした可能性があります。 サーバーを再起動するか、コマンド プロンプトから **net stop was /y** に続けて **net start w3svc** を実行して IIS を再起動します。

* ホスト システムで、 .NET Core ランタイムをインストールせずに、FDD を配置した可能性があります。 .NET Core ランタイムがインストールされていない場合は、システムで **.NET Core ホスティング バンドルのインストーラー**を実行します。

  [現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  詳細については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」をご覧ください。

  特定のランタイムが必要な場合、[.NET ダウンロード アーカイブ](https://dotnet.microsoft.com/download/archives)からダウンロードし、システムにインストールしてください。 インストールを完了するために、システムを再起動するか、コマンド プロンプトから **net stop was /y** に続けて **net start w3svc** を実行して IIS を再起動します。

* FDD を配置し、*Microsoft Visual C++ 2015 再頒布可能パッケージ (x64)* がシステムにインストールされていない可能性があります。 [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53840)からインストーラーを入手します。

## <a name="incorrect-arguments-of-aspnetcore-element"></a>\<aspNetCore> 要素の引数の誤り

::: moniker range=">= aspnetcore-2.2"

* **ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス ハンドラーの読み込みエラー

* **アプリケーション ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。 これが意味するところは、ほとんどの場合、アプリが正しく設定されていないということです。アプリケーションの対象であり、コンピューターにインストールされている Microsoft.NetCore.App と Microsoft.AspNetCore.App のバージョンを確認してください。 インプロセス要求ハンドラーが見つかりませんでした。 hostfxr の呼び出し時にキャプチャされた出力:dotnet SDK コマンドを実行しますか? dotnet SDK を次の場所からインストールしてください。https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 アプリケーション '/LM/W3SVC/3/ROOT' を起動できませんでした、エラー コード '0x8000ffff'。

* **ASP.NET Core モジュールの stdout ログ:** dotnet SDK コマンドを実行しますか? dotnet SDK を https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 からインストールしてください。

* **ASP.NET Core モジュール デバッグ ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。 これが意味するところは、ほとんどの場合、アプリが正しく設定されていないということです。アプリケーションの対象であり、コンピューターにインストールされている Microsoft.NetCore.App と Microsoft.AspNetCore.App のバージョンを確認してください。 HRESULT が失敗し、次が返されました:0x8000ffff インプロセス要求ハンドラーが見つかりませんでした。 hostfxr の呼び出し時にキャプチャされた出力:dotnet SDK コマンドを実行しますか? dotnet SDK を次の場所からインストールしてください。https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 HRESULT が失敗し、次が返されました:0x8000ffff

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **ブラウザー:** HTTP エラー 502.5 - 処理エラー

* **アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"dotnet" .\{ASSEMBLY}.dll' でプロセスを開始できませんでした、エラー コード = '0x80004005 : 80008081。

* **ASP.NET Core モジュールの stdout ログ:** 実行するアプリケーションが存在しません。'PATH\{ASSEMBLY}.dll'

::: moniker-end

トラブルシューティング:

* Kestrel でアプリをローカルに実行できることを確認します。 プロセスのエラーは、アプリ内の問題の結果である可能性があります。 詳細については、<xref:test/troubleshoot-azure-iis> を参照してください。

* *web.config* で `<aspNetCore>` 要素の *arguments* 属性を調べ、次のいずれかになっていることを確認します。(a) フレームワークに依存する展開 (FDD) の場合は `.\{ASSEMBLY}.dll`、または (b) 自己完結型の展開 (SCD) の場合は、未指定の空の文字列 (`arguments=""`) か、アプリの引数のリスト (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`)。

::: moniker range=">= aspnetcore-2.2"

## <a name="missing-net-core-shared-framework"></a>.NET Core 共有フレームワークがありません

* **ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス ハンドラーの読み込みエラー

* **アプリケーション ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。 これが意味するところは、ほとんどの場合、アプリが正しく設定されていないということです。アプリケーションの対象であり、コンピューターにインストールされている Microsoft.NetCore.App と Microsoft.AspNetCore.App のバージョンを確認してください。 インプロセス要求ハンドラーが見つかりませんでした。 hostfxr の呼び出し時にキャプチャされた出力:互換性のあるフレームワーク バージョンが見つかりませんでした。 指定のフレームワーク 'Microsoft.AspNetCore.App', version '{VERSION}' が見つかりませんでした。

アプリケーション '/LM/W3SVC/5/ROOT' を起動できませんでした、エラー コード '0x8000ffff'。

* **ASP.NET Core モジュールの stdout ログ:** 互換性のあるフレームワーク バージョンが見つかりませんでした。 指定のフレームワーク 'Microsoft.AspNetCore.App', version '{VERSION}' が見つかりませんでした。

* **ASP.NET Core モジュール デバッグ ログ:** HRESULT が失敗し、次が返されました:0x8000ffff

::: moniker-end

トラブルシューティング:

フレームワークに依存する展開 (FDD) では、正しいランタイムがシステムにインストールされていることを確認します。

## <a name="stopped-application-pool"></a>アプリケーション プールの停止

* **ブラウザー:** 503 サービスをご利用いただけません

* **アプリケーション ログ:** エントリなし

* **ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。

::: moniker range=">= aspnetcore-2.2"

* **ASP.NET Core モジュール デバッグ ログ:** ログ ファイルが作成されていません。

::: moniker-end

トラブルシューティング:

アプリケーション プールが *[停止]* 状態でないことを確認します。

## <a name="sub-application-includes-a-handlers-section"></a>サブアプリケーションに \<handlers> セクションが含まれている

* **ブラウザー:** HTTP エラー 500.19 - 内部サーバー エラー

* **アプリケーション ログ:** エントリなし

* **ASP.NET Core モジュールの stdout ログ:** ルート アプリのログ ファイルが作成され、通常の操作を示しています。 サブアプリのログ ファイルが作成されていません。

::: moniker range=">= aspnetcore-2.2"

* **ASP.NET Core モジュール デバッグ ログ:** ルート アプリのログ ファイルが作成され、通常の動作を示します。 サブアプリのログ ファイルが作成されません。

::: moniker-end

トラブルシューティング:

::: moniker range=">= aspnetcore-2.2"

サブアプリの *web.config* ファイルに `<handlers>` セクションがないか、サブアプリが親アプリのハンドラーを継承していないことを確認してください。

*web.config* の親アプリの `<system.webServer>` セクションが `<location>` 要素の中に置かれています。 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> プロパティは、`false` に設定されます。これは、[\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、親アプリのサブディレクトリにあるアプリによって継承されないことを示します。 詳細については、<xref:host-and-deploy/aspnet-core-module> を参照してください。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

サブアプリの *web.config* ファイルに `<handlers>` セクションが含まれていないことを確認します。

::: moniker-end

## <a name="stdout-log-path-incorrect"></a>stdout ログのパスが正しくありません

* **ブラウザー:** アプリは通常どおりに応答します。

::: moniker range=">= aspnetcore-2.2"

* **アプリケーション ログ:** C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll で stdout リダイレクトを開始できません。 例外メッセージ:HRESULT 0x80070005 が {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 で返されました。 C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll で stdout リダイレクトを停止できません。 例外メッセージ:HRESULT 0x80070002 が {PATH} で返されました。 {PATH}\aspnetcorev2_inprocess.dll で stdout リダイレクトを開始できません。

* **ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。

* **ASP.NET Core モジュール デバッグ ログ:** C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll で stdout リダイレクトを開始できません。 例外メッセージ:HRESULT 0x80070005 が {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 で返されました。 C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll で stdout リダイレクトを停止できません。 例外メッセージ:HRESULT 0x80070002 が {PATH} で返されました。 {PATH}\aspnetcorev2_inprocess.dll で stdout リダイレクトを開始できません。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **アプリケーション ログ:** 警告 :stdout ログ ファイル \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log を作成できません、エラー コード = -2147024893。

* **ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。

::: moniker-end

トラブルシューティング:

* *web.config* の `<aspNetCore>` 要素で指定された `stdoutLogFile` パスが存在しません。 詳細については、「[ASP.NET Core モジュール:ログの作成とリダイレクト](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)」を参照してください。

* アプリ プール ユーザーに stdout ログ パスに対する書き込みアクセス権がありません。

## <a name="application-configuration-general-issue"></a>アプリケーション構成の一般的な問題

::: moniker range=">= aspnetcore-2.2"

* **ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス要求ハンドラー失敗 **--または--** HTTP エラー 500.30 - ANCM インプロセス起動失敗

* **アプリケーション ログ:** 変数

* **ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されますが空です。あるいは作成され、通常のエントリが含まれますが、アプリのところでエラーが発生します。

* **ASP.NET Core モジュール デバッグ ログ:** 変数

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* **ブラウザー:** HTTP エラー 502.5 - 処理エラー

* **アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' でプロセスを作成しましたが、クラッシュしたか、応答しなかったか、所与のポート '{PORT}' で待機しませんでした、エラー コード = '{ERROR CODE}'

* **ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されましたが、空です。

::: moniker-end

トラブルシューティング:

このプロセスはおそらく、アプリの設定またはプログラミングに問題があり、開始できませんでした。

詳細については、次のトピックを参照してください。

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>
