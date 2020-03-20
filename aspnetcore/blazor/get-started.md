---
title: ASP.NET Core Blazor の概要
author: guardrex
description: 希望のツールで Blazor アプリを構築することで、Blazor の利用を開始してください。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/10/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 89c7529d2b8ec97db731f7c7268e19937c398115
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083244"
---
# <a name="get-started-with-aspnet-core-blazor"></a>ASP.NET Core Blazor の概要

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor の使用を開始します。

1. [.NET Core 3.1 SDK をインストールします](https://dotnet.microsoft.com/download/dotnet-core/3.1)。

1. オプションで、[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) テンプレートをインストールします。
   * [.NET Core 3.1 102 以降 (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) をインストールします。
   * コマンド シェルで次のコマンドを実行します。 Blazor WebAssembly がプレビュー段階にある間、[Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) パッケージにはプレビュー バージョンが用意されています。

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview2.20160.5
   ```

   > [!NOTE]
   > 3\.2 Preview 2 Blazor WebAssembly テンプレートを使用するには、.NET Core SDK バージョン 3.1.102 以降が**必要です**。 コマンド シェルで `dotnet --version` を実行して、インストールされている .NET Core SDK バージョンを確認します。

1. 使用するツールに向けたガイダンスに従ってください。

   # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

   1\. **ASP.NET および Web 開発**ワークロードを含む [Visual Studio 2019 バージョン 16.4 以降](https://visualstudio.microsoft.com/vs/preview/)をインストールします。

   2\. 新しいプロジェクトを作成します。

   3\. **[Blazor アプリ]** を選択します。 **[次へ]** を選択します。

   4\. **[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。 **[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。 **[作成]** を選択します。

   5\. Blazor WebAssembly エクスペリエンスについては、 **[Blazor WebAssembly アプリ]** テンプレートを選択します。 Blazor Server エクスペリエンスについては、 **[Blazor Server App] (Blazor Server アプリ)** テンプレートを選択します。 **[作成]** を選択します。 2つの Blazor ホスティング モデル、*Blazor Server*、*Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。 Blazor WebAssembly テンプレートが存在しない場合は、前のステップに戻り、テンプレートを再インストールします。

   6\. **Ctrl**+**F5** キーを押してアプリを実行します。

   > [!NOTE]
   > 以前のプレビュー リリースの ASP.NET Core Blazor (Preview 6 以前) 用に Blazor Visual Studio 拡張機能をインストールした場合は、その拡張機能をアンインストールできます。 Visual Studio でテンプレートを表示するには、コマンド シェルで Blazor テンプレートをインストールするだけで十分になりました。

   # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1\. [Visual Studio Code](https://code.visualstudio.com/) のインストール。

   2\. 最新の [C# for Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)をインストールします。

   3\. Blazor WebAssembly エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      Blazor Server エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      2つの Blazor ホスティング モデル、*Blazor Server*、*Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。

   4\. Visual Studio Code で *WebApplication1* フォルダーを開きます。

   5\. Blazor Server プロジェクトについては、プロジェクトをビルドおよびデバッグするためにアセットを追加するように、IDE によって要求されます。 **[はい]** を選択します。

   6\. Blazor Server アプリを使用する場合、Visual Studio Code デバッガーを使用してアプリを実行します。 Blazor WebAssembly アプリを使用する場合、アプリのプロジェクト フォルダーから `dotnet run` を実行します。

   7\. ブラウザーで、`https://localhost:5001` に移動します。

   # <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

   1\. [Visual Studio for Mac をインストールします](https://visualstudio.microsoft.com/vs/mac/)。

   2\. **[ファイル]**  >  **[新しいソリューション]** を選択するか、**新しいプロジェクト**を作成します。

   3\. サイドバーで、 **[.NET Core]**  >  **[アプリ]** の順に選択します。

   4\. **[Blazor Server App] (Blazor Server アプリ)** テンプレートを選択します。 現時点では、Visual Studio for Mac で使用できるのは Blazor Server テンプレートのみです。 Blazor WebAssembly については、 **[.NET Core CLI]** タブの指示に従ってください。Blazor Server テンプレートを選択したら、 **[次へ]** を選択します。 2つの Blazor ホスティング モデル、*Blazor Server*、*Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   5\. **[ターゲット フレームワーク]** を **[.NET Core 3.1]** に設定し、 **[次へ]** を選択します。

   6\. **[プロジェクト名]** フィールドで、アプリに `WebApplication1` という名前を付けます。 **[作成]** を選択します。

   7\. *デバッガーを使用せずに*アプリを実行するには、 **[実行]**  >  **[デバッグなしで実行する]** の順に選択します。 *デバッガーを使用して*アプリを実行するには、 **[デバッグの開始]** を選択してアプリを実行します。

   開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。

   # <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

   Blazor WebAssembly エクスペリエンスについては、コマンド シェルで以下のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Blazor Server エクスペリエンスについては、コマンド シェルで以下のコマンドを実行します。

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   2つの Blazor ホスティング モデル、*Blazor Server*、*Blazor WebAssembly* については、「<xref:blazor/hosting-models>」を参照してください。

   ブラウザーで、`https://localhost:5001` に移動します。

   ---

サイドバーのタブから複数のページを使用できます。

* Home
* カウンター
* Fetch data (データのフェッチ)

Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。 Web ページでカウンターをインクリメントするには、通常、JavaScript を記述することが必要ですが、Blazor では C# を使用できます。

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

ブラウザーで `/counter` の要求があると、上部の `@page` ディレクティブで指定されているように、`Counter` コンポーネントによってそのコンテンツがレンダリングされます。 コンポーネントは、レンダリング ツリーのメモリ内表現としてレンダリングされ、柔軟かつ効率的な方法で UI を更新するために利用できるようになります。

**[クリックしてください]** ボタンを選択するたびに:

* `onclick` イベントが発生します。
* `IncrementCount` メソッドが呼び出されます。
* `currentCount` がインクリメントされます。
* コンポーネントが再度レンダリングされます。

ランタイムで新しいコンテンツと前のコンテンツが比較され、変更されたコンテンツのみがドキュメント オブジェクト モデル (DOM) に適用されます。

HTML 構文を使用して、別のコンポーネントにコンポーネントを追加します。 たとえば、`Index` コンポーネントに `<Counter />` 要素を追加することで、アプリのホームページに `Counter` コンポーネントを追加します。

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

アプリを実行します。 ホームページには、`Counter` コンポーネントによって提供される独自のカウンターがあります。

コンポーネント パラメーターは、属性または [子コンテンツ](xref:blazor/components#child-content)を使用して指定されます。これにより、子コンポーネントのプロパティを設定できます。 `Counter` コンポーネントにパラメーターを追加するには、コンポーネントの `@code` ブロックを更新します。

* 属性 `[Parameter]` を使用して、`IncrementAmount` のためのパブリック プロパティを追加します。
* `currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

属性を使用して、`Index` コンポーネントの `<Counter>` 要素内に `IncrementAmount` を指定します。

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

アプリを実行します。 `Index` コンポーネントは、 **[クリックしてください]** ボタンが選択されるたびに 10 ずつインクリメントされる独自のカウンターを持っています。 `/counter` にある `Counter` コンポーネント (*Counter.razor*) は、引き続き 1 ずつインクリメントされます。

## <a name="next-steps"></a>次の手順

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/templates>
* <xref:signalr/introduction>
