---
title: ASP.NET Core のクラス ライブラリの再利用可能 Razor UI
author: Rick-Anderson
description: Razor を ASP.NET core クラス ライブラリの部分ビューを使用して再利用可能な UI を作成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/21/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 1b544e208be049f02d01e35daa6eb3bfba94265a
ms.sourcegitcommit: 04ce94b3c1b01d167f30eed60c1c95446dfe759d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/21/2019
ms.locfileid: "71176469"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>ASP.NET Core の Razor クラスライブラリプロジェクトを使用して、再利用可能な UI を作成する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Razor ビュー、ページ、コントローラー、ページモデル、 [razor コンポーネント](xref:blazor/class-libraries)、[ビューコンポーネント](xref:mvc/views/view-components)、およびデータモデルは、razor クラスライブラリ (rcl) に組み込むことができます。 RCL はパッケージ化し、再利用できます。 アプリケーションには RCL を含めることができます。また、それに含まれるビューやページをオーバーライドできます。 Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="create-a-class-library-containing-razor-ui"></a>Razor UI を含むクラス ライブラリを作成する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。
* **[ASP.NET Core Web アプリケーション]** を選択します。
* ライブラリに名前を付け ("RazorClassLib" など)、 **[OK]** を選択します。 生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。
* **ASP.NET Core 3.0**以降が選択されていることを確認します。
* [ **Razor クラスライブラリ** > **] [OK]** を選択します。

Razor クラスライブラリ (RCL) テンプレートは、既定で Razor コンポーネントの開発に既定で設定されています。 Visual Studio のテンプレートオプションを使用すると、ページとビューのテンプレートをサポートできます。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

コマンド ラインから `dotnet new razorclasslib` を実行します。 次に例を示します。

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Razor クラスライブラリ (RCL) テンプレートは、既定で Razor コンポーネントの開発に既定で設定されています。 オプション ( `-support-pages-and-views` `dotnet new razorclasslib -support-pages-and-views`) を渡して、ページとビューのサポートを提供します。

詳細については、「[dotnet new](/dotnet/core/tools/dotnet-new)」を参照してください。 生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。

---

Razor ファイルを RCL に追加します。

ASP.NET Core テンプレートは、RCL コンテンツが前提としています。、*領域*フォルダー。 「 [Rcl ページのレイアウト](#rcl-pages-layout)」を参照して、では`~/Pages`なく`~/Areas/Pages`、でコンテンツを公開する rcl を作成します。

## <a name="reference-rcl-content"></a>RCL コンテンツの参照

RCL は次によって参照できます。

* NuGet パッケージ。 「[Creating NuGet packages](/nuget/create-packages/creating-a-package)」 (NuGet パッケージの作成)、「[dotnet add package](/dotnet/core/tools/dotnet-add-package)」、「[Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)」 (NuGet パッケージの作成と公開) を参照してください。
* *{ProjectName}.csproj*. 「[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)」を参照してください。

## <a name="override-views-partial-views-and-pages"></a>ビュー、部分ビュー、ページのオーバーライド

Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。 たとえば、 *WebApp1/Areas/MyFeature/Pages/Page1. cshtml*を WebApp1 に追加すると、WebApp1 の PAGE1 が Rcl の page1 よりも優先されます。

サンプル ダウンロードの *WebApp1/Areas/MyFeature2* の名前を *WebApp1/Areas/MyFeature* に変更し、優先設定をテストします。

*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 部分ビューを *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* ビューにコピーします。 新しい場所を示すようにマークアップを更新します。 アプリをビルドして実行し、アプリの部分ビューが使用されていることを確認します。

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

## <a name="create-an-rcl-with-static-assets"></a>静的なアセットを含む RCL を作成する

RCL では、RCL の消費アプリから参照できる、関連する静的アセットが必要な場合があります。 ASP.NET Core では、使用中のアプリで利用可能な静的アセットを含む RCLs を作成できます。

RCL の一部としてコンパニオン資産を含めるには、クラスライブラリに*wwwroot*フォルダーを作成し、そのフォルダーに必要なファイルを含めます。

RCL をパッキングすると、 *wwwroot*フォルダー内のすべてのコンパニオン資産がパッケージに自動的に含まれます。

### <a name="exclude-static-assets"></a>静的アセットを除外する

静的アセットを除外するには、目的の除外パス`$(DefaultItemExcludes)`をプロジェクトファイルのプロパティグループに追加します。 エントリをセミコロン (`;`) で区切ります。

次の例では、 *wwwroot*フォルダーの *.lib*スタイルシートは静的アセットとは見なされず、公開された rcl には含まれていません。

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Typescript の統合

TypeScript ファイルを RCL に含めるには、次のようにします。

1. TypeScript ファイル (*ts*) を*wwwroot*フォルダーの外側に配置します。 たとえば、ファイルを*クライアント*フォルダーに配置します。

1. *Wwwroot*フォルダーの TypeScript ビルド出力を構成します。 プロジェクトファイル`TypescriptOutDir`の`PropertyGroup`内でプロパティを設定します。

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. プロジェクトファイルの`PropertyGroup`内に次のターゲットを`ResolveCurrentProjectStaticWebAssets`追加することにより、TypeScript ターゲットをターゲットの依存関係として含めます。

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     TypeScriptCompile;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>参照されている RCL からのコンテンツの使用

RCL の*wwwroot*フォルダーに含まれるファイルは、プレフィックス`_content/{LIBRARY NAME}/`の下にあるアプリに公開されます。 たとえば、という名前のライブラリを使用*すると、* の静的コンテンツ`_content/Razor.Class.Lib/`へのパスが生成されます。

使用中のアプリは`<script>` `<img>`、、 `<style>`、、およびその他の HTML タグを使用して、ライブラリによって提供される静的アセットを参照します。 使用中のアプリでは、次の方法`Startup.Configure`で[静的ファイルのサポート](xref:fundamentals/static-files)を有効にする必要があります。

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

ビルド出力 (`dotnet run`) から使用中のアプリを実行すると、開発環境では、静的な web アセットが既定で有効になります。 ビルド出力から実行するときに他の環境のアセットを`UseStaticWebAssets`サポートするには、 *Program.cs*のホストビルダーでを呼び出します。

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

発行`UseStaticWebAssets`された出力 (`dotnet publish`) からアプリを実行する場合、を呼び出す必要はありません。

### <a name="multi-project-development-flow"></a>複数プロジェクトの開発フロー

コンシューマーアプリの実行時:

* RCL のアセットは元のフォルダーにとどまります。 アセットは、消費側のアプリに移動されません。
* Rcl の*wwwroot*フォルダー内の変更は、rcl がリビルドされた後、使用中のアプリを再構築せずに、消費側アプリに反映されます。

RCL がビルドされると、静的な web 資産の場所を記述するマニフェストが生成されます。 コンシューマーアプリは、実行時にマニフェストを読み取り、参照されたプロジェクトとパッケージのアセットを使用します。 RCL に新しいアセットが追加されたときに、使用中のアプリが新しい資産にアクセスできるようにするには、そのマニフェストを更新するために RCL を再構築する必要があります。

### <a name="publish"></a>公開

アプリが発行されると、参照されているすべてのプロジェクトおよびパッケージの関連する資産が、で`_content/{LIBRARY NAME}/`発行されたアプリの wwwroot フォルダーにコピーされます。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor ビュー、ページ、コントローラー、ページモデル、 [razor コンポーネント](xref:blazor/class-libraries)、[ビューコンポーネント](xref:mvc/views/view-components)、およびデータモデルは、razor クラスライブラリ (rcl) に組み込むことができます。 RCL はパッケージ化し、再利用できます。 アプリケーションには RCL を含めることができます。また、それに含まれるビューやページをオーバーライドできます。 Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="create-a-class-library-containing-razor-ui"></a>Razor UI を含むクラス ライブラリを作成する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。
* **[ASP.NET Core Web アプリケーション]** を選択します。
* ライブラリに名前を付け ("RazorClassLib" など)、 **[OK]** を選択します。 生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。
* **ASP.NET Core 2.1** 以降が選択されていることを確認します。
* [ **Razor クラスライブラリ** > **] [OK]** を選択します。

RCL には、次のプロジェクトファイルがあります。

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

コマンド ラインから `dotnet new razorclasslib` を実行します。 例:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

詳細については、「[dotnet new](/dotnet/core/tools/dotnet-new)」を参照してください。 生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。

---

Razor ファイルを RCL に追加します。

ASP.NET Core テンプレートは、RCL コンテンツが前提としています。、*領域*フォルダー。 「 [Rcl ページのレイアウト](#rcl-pages-layout)」を参照して、では`~/Pages`なく`~/Areas/Pages`、でコンテンツを公開する rcl を作成します。

## <a name="reference-rcl-content"></a>RCL コンテンツの参照

RCL は次によって参照できます。

* NuGet パッケージ。 「[Creating NuGet packages](/nuget/create-packages/creating-a-package)」 (NuGet パッケージの作成)、「[dotnet add package](/dotnet/core/tools/dotnet-add-package)」、「[Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)」 (NuGet パッケージの作成と公開) を参照してください。
* *{ProjectName}.csproj*. 「[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)」を参照してください。

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>チュートリアル: RCL プロジェクトを作成し、Razor Pages プロジェクトから使用する

作成しなくても、[完全なプロジェクト](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)をダウンロードしてテストできます。 サンプル ダウンロードには、プロジェクトのテストを簡単にする追加のコードやリンクが含まれています。 GitHub の問題に関するフィードバックは[こちら](https://github.com/aspnet/AspNetCore.Docs/issues/6098)で扱っています。ダウンロード サンプルと段階的指示の違いについてコメントを投稿できます。

### <a name="test-the-download-app"></a>ダウンロード アプリをテストする

完全なアプリをダウンロードしておらず、チュートリアル プロジェクトを作成する場合、[次のセクション](#create-an-rcl)に進んでください。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio で *.sln* ファイルを開きます。 アプリを実行します。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

*cli* ディレクトリのコマンド プロンプトから、RCL と Web アプリをビルドします。

```dotnetcli
dotnet build
```

*WebApp1* ディレクトリに移動し、アプリを実行します。

```dotnetcli
dotnet run
```

---

[テスト WebApp1](#test-webapp1) の指示に従ってください。

## <a name="create-an-rcl"></a>RCL を作成する

このセクションでは、RCL を作成します。 Razor ファイルが RCL に追加されます。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

RCL プロジェクトの作成:

* Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。
* **[ASP.NET Core Web アプリケーション]** を選択します。
* アプリの名前を**RazorUIClassLib** > **OK**にします。
* **ASP.NET Core 2.1** 以降が選択されていることを確認します。
* [ **Razor クラスライブラリ** > **] [OK]** を選択します。
* *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* という名前が付いた Razor 部分ビュー ファイルを追加します。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

コマンド ラインから次を実行します。

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

上のコマンドでは以下の操作が行われます。

* Rcl `RazorUIClassLib`を作成します。
* Razor _Message ページが作成され、RCL に追加されます。 `-np` パラメーターによって、`PageModel` なしでページが作成されます。
* 作成、 [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view)ファイルを開き、RCL に追加します。

*_ViewStart.cshtml* (これは、次のセクションに追加されます) Razor ページ プロジェクトのレイアウトを使用するファイルが必要です。

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Razor ファイルおよびフォルダーをプロジェクトに追加します。

* *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* のマークアップを次のコードに変更します。

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* のマークアップを次のコードに変更します。

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` は部分ビュー (`<partial name="_Message" />`) を使用するために必要です。 `@addTagHelper` ディレクティブを含める代わりに、 *_ViewImports.cshtml* ファイルを追加できます。 例:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  詳細については *_ViewImports.cshtml*を参照してください[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)

* クラス ライブラリをビルドし、コンパイラ エラーがないことを確認します。

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

ビルド出力には、*RazorUIClassLib.dll* と *RazorUIClassLib.Views.dll* が含まれています。 *RazorUIClassLib.Views.dll* には、コンパイル済みの Razor コンテンツが含まれています。

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Razor ページ プロジェクトから Razor UI ライブラリを使用します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Razor ページ Web アプリの作成:

* **ソリューションエクスプローラー**で、ソリューションを右クリックし、[**新しいプロジェクト**の**追加** >] > ます。
* **[ASP.NET Core Web アプリケーション]** を選択します。
* アプリに **WebApp1** という名前を付けます。
* **ASP.NET Core 2.1** 以降が選択されていることを確認します。
* [ **Web アプリケーション** > **] [OK]** を選択します。

* **ソリューション エクスプローラー**で、**WebApp1** を右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。
* **ソリューションエクスプローラー**で、 **[WebApp1]** を右クリックし、[**ビルド依存関係** > **プロジェクトの依存関係**] を選択します。
* **WebApp1** の依存関係として **RazorUIClassLib** を選択します。
* **ソリューションエクスプローラー**から**WebApp1**を右クリックし、[**参照**の**追加** > ] を選択します。
* **[参照マネージャー]** ダイアログで、[ **RazorUIClassLib** > **OK]** を確認します。

アプリを実行します。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Razor Pages web アプリと、Razor Pages アプリと RCL を含むソリューションファイルを作成します。

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Web アプリをビルドし、実行します。

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>テスト WebApp1

に`/MyFeature/Page1`移動して、Razor UI クラスライブラリが使用されていることを確認します。

## <a name="override-views-partial-views-and-pages"></a>ビュー、部分ビュー、ページのオーバーライド

Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。 たとえば、 *WebApp1/Areas/MyFeature/Pages/Page1. cshtml*を WebApp1 に追加すると、WebApp1 の PAGE1 が Rcl の page1 よりも優先されます。

サンプル ダウンロードの *WebApp1/Areas/MyFeature2* の名前を *WebApp1/Areas/MyFeature* に変更し、優先設定をテストします。

*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 部分ビューを *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* ビューにコピーします。 新しい場所を示すようにマークアップを更新します。 アプリをビルドして実行し、アプリの部分ビューが使用されていることを確認します。

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

::: moniker-end
