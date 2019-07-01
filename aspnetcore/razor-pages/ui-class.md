---
title: ASP.NET Core のクラス ライブラリの再利用可能 Razor UI
author: Rick-Anderson
description: Razor を ASP.NET core クラス ライブラリの部分ビューを使用して再利用可能な UI を作成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 06/28/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: d59f643a23b48ccbddf498ef534ee8432b010f40
ms.sourcegitcommit: 6d9cf728465cdb0de1037633a8b7df9a8989cccb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67463256"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>ASP.NET Core で Razor クラス ライブラリ プロジェクトを使用して再利用可能な UI を作成します。

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor ビュー、ページ、コント ローラー、ページ モデルでは、 [Razor コンポーネント](xref:blazor/class-libraries)、[ビュー コンポーネント](xref:mvc/views/view-components)、および Razor クラス ライブラリ (RCL) にデータ モデルをビルドすることができます。 RCL はパッケージ化し、再利用できます。 アプリケーションには RCL を含めることができます。また、それに含まれるビューやページをオーバーライドできます。 Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。

この機能が必要です。 [!INCLUDE[](~/includes/2.1-SDK.md)]

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="create-a-class-library-containing-razor-ui"></a>Razor UI を含むクラス ライブラリを作成する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。
* **[ASP.NET Core Web アプリケーション]** を選択します。
* ライブラリに名前を付け ("RazorClassLib" など)、 **[OK]** を選択します。 生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。
* **ASP.NET Core 2.1** 以降が選択されていることを確認します。
* **[Razor クラス ライブラリ]** 、 **[OK]** の順に選択します。

RCL では、次のプロジェクト ファイルがあります。

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

コマンド ラインから `dotnet new razorclasslib` を実行します。 例:

```console
dotnet new razorclasslib -o RazorUIClassLib
```

詳細については、「[dotnet new](/dotnet/core/tools/dotnet-new)」を参照してください。 生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。

---

Razor ファイルを RCL に追加します。

ASP.NET Core テンプレートは、RCL コンテンツが前提としています。、*領域*フォルダー。 参照してください[RCL ページ レイアウト](#afs)でコンテンツを公開する、RCL を作成する`~/Pages`なく`~/Areas/Pages`します。

## <a name="referencing-rcl-content"></a>RCL コンテンツを参照します。

RCL は次によって参照できます。

* NuGet パッケージ。 「[Creating NuGet packages](/nuget/create-packages/creating-a-package)」 (NuGet パッケージの作成)、「[dotnet add package](/dotnet/core/tools/dotnet-add-package)」、「[Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)」 (NuGet パッケージの作成と公開) を参照してください。
* *{ProjectName}.csproj*. 「[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)」を参照してください。

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>チュートリアル: RCL プロジェクトを作成し、Razor ページ プロジェクトからの使用

作成しなくても、[完全なプロジェクト](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)をダウンロードしてテストできます。 サンプル ダウンロードには、プロジェクトのテストを簡単にする追加のコードやリンクが含まれています。 GitHub の問題に関するフィードバックは[こちら](https://github.com/aspnet/AspNetCore.Docs/issues/6098)で扱っています。ダウンロード サンプルと段階的指示の違いについてコメントを投稿できます。

### <a name="test-the-download-app"></a>ダウンロード アプリをテストする

完全なアプリをダウンロードしておらず、チュートリアル プロジェクトを作成する場合、[次のセクション](#create-an-rcl)に進んでください。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio で *.sln* ファイルを開きます。 アプリを実行します。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

*cli* ディレクトリのコマンド プロンプトから、RCL と Web アプリをビルドします。

```console
dotnet build
```

*WebApp1* ディレクトリに移動し、アプリを実行します。

```console
dotnet run
```

---

[テスト WebApp1](#test) の指示に従ってください。

## <a name="create-an-rcl"></a>作成、RCL

このセクションで、RCL が作成されます。 Razor ファイルが RCL に追加されます。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

RCL プロジェクトの作成:

* Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。
* **[ASP.NET Core Web アプリケーション]** を選択します。
* アプリの名前を付けます**RazorUIClassLib** > **OK**します。
* **ASP.NET Core 2.1** 以降が選択されていることを確認します。
* **[Razor クラス ライブラリ]** 、 **[OK]** の順に選択します。
* *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* という名前が付いた Razor 部分ビュー ファイルを追加します。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

コマンド ラインから次を実行します。

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

上のコマンドでは以下の操作が行われます。

* 作成、 `RazorUIClassLib` RCL します。
* Razor _Message ページが作成され、RCL に追加されます。 `-np` パラメーターによって、`PageModel` なしでページが作成されます。
* 作成、 [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view)ファイルを開き、RCL に追加します。

*_ViewStart.cshtml* (これは、次のセクションに追加されます) Razor ページ プロジェクトのレイアウトを使用するファイルが必要です。

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Razor ファイルおよびフォルダーをプロジェクトに追加します。

* *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* のマークアップを次のコードに変更します。

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* のマークアップを次のコードに変更します。

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` は部分ビュー (`<partial name="_Message" />`) を使用するために必要です。 `@addTagHelper` ディレクティブを含める代わりに、 *_ViewImports.cshtml* ファイルを追加できます。 例:

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

詳細については *_ViewImports.cshtml*を参照してください[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)

* クラス ライブラリをビルドし、コンパイラ エラーがないことを確認します。

```console
dotnet build RazorUIClassLib
```

ビルド出力には、*RazorUIClassLib.dll* と *RazorUIClassLib.Views.dll* が含まれています。 *RazorUIClassLib.Views.dll* には、コンパイル済みの Razor コンテンツが含まれています。

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Razor ページ プロジェクトから Razor UI ライブラリを使用します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Razor ページ Web アプリの作成:

* **ソリューション エクスプローラー**で、ソリューションを右クリックし、 **[追加]** 、 **[新しいプロジェクト]** の順に選択します。
* **[ASP.NET Core Web アプリケーション]** を選択します。
* アプリに **WebApp1** という名前を付けます。
* **ASP.NET Core 2.1** 以降が選択されていることを確認します。
* **[Web アプリケーション]** 、 **[OK]** の順に選択します。

* **ソリューション エクスプローラー**で、**WebApp1** を右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。
* **ソリューション エクスプローラー**で、**WebApp1** を右クリックし、 **[ビルド依存関係]** 、 **[プロジェクトの依存関係]** の順に選択します。
* **WebApp1** の依存関係として **RazorUIClassLib** を選択します。
* **ソリューション エクスプローラー**で、**WebApp1** を右クリックし、 **[追加]** 、 **[参照]** の順に選択します。
* **[参照マネージャー]** ダイアログで、 **[RazorUIClassLib]** 、 **[OK]** の順に選択します。

アプリを実行します。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Razor ページ web アプリと Razor ページ アプリと、RCL を含むソリューション ファイルを作成します。

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Web アプリをビルドし、実行します。

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a>テスト WebApp1

使用されて、Razor の UI クラス ライブラリを確認します。

* `/MyFeature/Page1` を参照します。

## <a name="override-views-partial-views-and-pages"></a>ビュー、部分ビュー、ページのオーバーライド

Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。 たとえば、追加*WebApp1/Areas/MyFeature/Pages/Page1.cshtml* WebApp1 に、WebApp1 の Page1 は優先、RCL の Page1 とします。

サンプル ダウンロードの *WebApp1/Areas/MyFeature2* の名前を *WebApp1/Areas/MyFeature* に変更し、優先設定をテストします。

*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 部分ビューを *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* ビューにコピーします。 新しい場所を示すようにマークアップを更新します。 アプリをビルドして実行し、アプリの部分ビューが使用されていることを確認します。

<a name="afs"></a>

### <a name="rcl-pages-layout"></a>RCL ページ レイアウト

参照 RCL コンテンツの web アプリの一部である場合と同様に*ページ*フォルダー、RCL プロジェクト ファイルの構造を作成します。

* *RazorUIClassLib/ページ*
* *RazorUIClassLib/ページ/共有*

たとえば*RazorUIClassLib/ページ/共有*2 つの部分的なファイルが含まれています: *_Header.cshtml*と *_Footer.cshtml*します。 `<partial>`タグに追加できる *_Layout.cshtml*ファイル。

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker range=">= aspnetcore-3.0"

## <a name="create-an-rcl-with-static-assets"></a>静的なアセットを RCL を作成します。

RCL、RCL のアプリを使うで参照できる付属の静的なアセットを必要があります。 ASP.NET Core ではアプリを使うに使用できる静的なアセットを含む RCLs を作成できます。

コンパニオン資産は、RCL の一部として、作成、 *wwwroot*クラス ライブラリ内のフォルダーと、そのフォルダーに必要なファイルを含めます。

資産をすべてコンパニオン、RCL をパックするときに、 *wwwroot*フォルダー、パッケージに自動的に含まれ、パッケージを参照するアプリで使用できるようにします。

### <a name="consume-content-from-a-referenced-rcl"></a>参照先 RCL からコンテンツを使用します。

含まれるファイル、 *wwwroot* 、RCL のフォルダーは、プレフィックスの下でアプリを使うには公開`_content/{LIBRARY NAME}/`します。 `{LIBRARY NAME}` ライブラリのプロジェクト名に変換期間の小文字 (`.`) を削除します。 たとえば、という名前のライブラリ*Razor.Class.Lib*で静的なコンテンツへのパスになります`_content/razorclasslib/`します。

アプリを使うと、ライブラリによって提供される静的なアセットを参照して`<script>`、 `<style>`、 `<img>`、およびその他の HTML タグ。 アプリを使う必要があります[静的ファイルのサポート](xref:fundamentals/static-files)を有効にします。

### <a name="multi-project-development-flow"></a>複数プロジェクトの開発フロー

アプリを使うが実行されます。

* 元のフォルダー、RCL で資産を継続します。 資産は、アプリを使うに移動されません。
* RCL の内のすべての変更*wwwroot* RCL が再構築後のアプリを使うとアプリを使うを再構築せずにフォルダーが反映されます。

RCL のビルド時に静的 web 資産の場所を記述するマニフェストが生成されます。 アプリを使うでは、参照先のプロジェクトとパッケージから資産を使用する実行時にマニフェストを読み取ります。 RCL に新しい資産を追加するときに、RCL はアプリを使うことができます、新しいアセットへのアクセス前に、そのマニフェストを更新する再構築する必要があります。

### <a name="publish"></a>公開

参照されているすべてのプロジェクトとパッケージのコンパニオン アセットをコピー、アプリが発行されると、 *wwwroot*フォルダーの下で発行されたアプリの`_content/{LIBRARY NAME}/`します。

::: moniker-end
