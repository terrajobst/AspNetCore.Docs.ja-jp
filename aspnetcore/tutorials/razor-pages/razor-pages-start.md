---
title: 'チュートリアル: ASP.NET Core の Razor ページの概要'
author: rick-anderson
description: このチュートリアル シリーズでは、ASP.NET Core で Razor ページを使用する方法を示します。 モデルの作成、Razor ページのコードの生成、Entity Framework Core と SQL Server を使用したデータ アクセス、検索機能の追加、入力検証の追加、および移行を使用したモデルの更新の方法について説明します。
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 6e1d58ccd83f7d7c1083dc2bf9ce7476650812a1
ms.sourcegitcommit: da2fb2d78ce70accdba903ccbfdcfffdd0112123
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75723016"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>チュートリアル: ASP.NET Core の Razor ページの概要

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
これは、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明する、シリーズの最初のチュートリアルです。

[!INCLUDE[](~/includes/advancedRP.md)]

シリーズの最後には、映画のデータベースを管理できるアプリができあがります。  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * Razor Pages Web アプリを作成する。
> * アプリを実行します。
> * プロジェクト ファイルを確認する

このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>必須コンポーネント

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Razor ページ Web アプリを作成する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。
* 新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。
  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)
* プロジェクトに **RazorPagesMovie** という名前を付けます。 コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。
  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)

* ドロップダウンの **[ASP.NET Core 3.1]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/3/npx.png)

  次のスターター プロジェクトが作成されます。

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。

* プロジェクトを格納するディレクトリ (`cd`) に変更します。

* 次のコマンドを実行します。

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。
  * `code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。

* 状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、 **[はい]** を選択します。

  *launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **[ファイル]** > **[新しいソリューション]** の順に選択します。

![macOS の新しいソリューション](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* **[.NET Core]** > **[アプリ]** > **[Web アプリケーション]** > **[次へ]** の順に選択します。

  ![macOS の [新しいプロジェクト] ダイアログ](razor-pages-start/_static/webapp.png)

* **[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、 **[ターゲット フレームワーク]** を **[.NET Core 3.1]** に設定します。

  ![macOS .NET Core 3.1 の選択](razor-pages-start/_static/targetframework3.png)

* プロジェクトに **RazorPagesMovie** という名前を付けて、 **[作成]** を選択します。

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>アプリを実行する

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>プロジェクト ファイルを確認する

以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。

### <a name="pages-folder"></a>Pages フォルダー

Razor ページとサポート ファイルが格納されます。 各 Razor ページは、次のファイルのペアとなります。

* *.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。
* *.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。

サポート ファイルには、アンダー スコアで始まる名前が付けられます。 たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。 このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。 詳細については、「<xref:mvc/views/layout>」を参照してください。

### <a name="wwwroot-folder"></a>wwwroot フォルダー

HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。 詳細については、「<xref:fundamentals/static-files>」を参照してください。

### <a name="appsettingsjson"></a>appSettings.json

接続文字列などの構成データが保存されます。 詳細については、「<xref:fundamentals/configuration/index>」を参照してください。

### <a name="programcs"></a>Program.cs

プログラムのエントリ ポイントが保存されます。 詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。

### <a name="startupcs"></a>Startup.cs

アプリの動作を構成するコードが含まれています。 詳細については、「<xref:fundamentals/startup>」を参照してください。

## <a name="next-steps"></a>次の手順

このシリーズの次のチュートリアルに進んでください。

> [!div class="step-by-step"]
> [モデルの追加](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

これは、シリーズの最初のチュートリアルです。 [このシリーズ](xref:tutorials/razor-pages/index)では、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明します。

[!INCLUDE[](~/includes/advancedRP.md)]

シリーズの最後には、映画のデータベースを管理できるアプリができあがります。  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * Razor Pages Web アプリを作成する。
> * アプリを実行します。
> * プロジェクト ファイルを確認する

このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>必須コンポーネント

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Razor ページ Web アプリを作成する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。

* 新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)

* プロジェクトに **RazorPagesMovie** という名前を付けます。 コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)

* ドロップダウンの **[ASP.NET Core 2.2]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2_2.2.png)

  次のスターター プロジェクトが作成されます。

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。

* プロジェクトを格納するディレクトリ (`cd`) に変更します。

* 次のコマンドを実行します。

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。
  * `code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。

* 状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、 **[はい]** を選択します。

  *launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **[ファイル]** > **[新しいソリューション]** の順に選択します。

![macOS の新しいソリューション](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* **[.NET Core]** > **[アプリ]** > **[Web アプリケーション]** > **[次へ]** の順に選択します。

  ![macOS の [新しいプロジェクト] ダイアログ](razor-pages-start/_static/webapp.png)

* **[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、 **[ターゲット フレームワーク]** を **[.NET Core 3.1]** に設定します。

  ![macOS .NET Core 3.0 の選択](razor-pages-start/_static/targetframework3.png)

* プロジェクトに **RazorPagesMovie** という名前を付けて、 **[作成]** を選択します。

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>アプリを実行する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ctrl + F5 キーを押して、デバッガーなしで実行します。

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。 アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。 これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。 localhost では、ローカル コンピューターからの Web 要求のみが処理されます。 Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。

* アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。

  このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  次の図では、追跡に同意した後のアプリを示しています。

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* **Ctrl + F5** キーを押して、デバッガーなしで実行します。

  Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。 アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。 これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。 localhost では、ローカル コンピューターからの Web 要求のみが処理されます。

* アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。

  このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  次の図では、追跡に同意した後のアプリを示しています。

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* **Cmd - Opt - F5** キーを押して、デバッガーなしで実行します。

  Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。

* アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。

  このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2_safari.png)

  次の図では、追跡に同意した後のアプリを示しています。

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>プロジェクト ファイルを確認する

以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。

### <a name="pages-folder"></a>Pages フォルダー

Razor ページとサポート ファイルが格納されます。 各 Razor ページは、次のファイルのペアとなります。

* *.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。
* *.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。

サポート ファイルには、アンダー スコアで始まる名前が付けられます。 たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。 このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。 詳細については、「<xref:mvc/views/layout>」を参照してください。

### <a name="wwwroot-folder"></a>wwwroot フォルダー

HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。 詳細については、「<xref:fundamentals/static-files>」を参照してください。

### <a name="appsettingsjson"></a>appSettings.json

接続文字列などの構成データが保存されます。 詳細については、「<xref:fundamentals/configuration/index>」を参照してください。

### <a name="programcs"></a>Program.cs

プログラムのエントリ ポイントが保存されます。 詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。

### <a name="startupcs"></a>Startup.cs

cookie に対する同意が必要かどうかなど、アプリの動作を構成するコードが保存されます。 詳細については、「<xref:fundamentals/startup>」を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* [このチュートリアルの YouTube バージョン](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>次の手順

このシリーズの次のチュートリアルに進んでください。

> [!div class="step-by-step"]
> [モデルの追加](xref:tutorials/razor-pages/model)

::: moniker-end
