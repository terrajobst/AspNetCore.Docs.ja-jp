---
title: ASP.NET MVC から ASP.NET Core MVC への移行
author: ardalis
description: ASP.NET MVC プロジェクトの ASP.NET Core MVC への移行を開始する方法について説明します。
ms.author: riande
ms.date: 04/06/2019
uid: migration/mvc
ms.openlocfilehash: 6c9449fb43960d05db8aa6dcba64d3d830834cdb
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652550"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>ASP.NET MVC から ASP.NET Core MVC への移行

[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)、[上田 Smith](https://ardalis.com/)、 [Scott addie](https://scottaddie.com)

この記事では、ASP.NET MVC プロジェクトの[ASP.NET CORE mvc](../mvc/overview.md)への移行を開始する方法について説明します。 このプロセスでは、ASP.NET MVC から変更されたものの多くを強調表示します。 ASP.NET MVC からの移行は、複数の手順から構成されるプロセスです。この記事では、初期セットアップ、基本的なコントローラーとビュー、静的コンテンツ、およびクライアント側の依存関係について説明します。 その他の記事では、多くの ASP.NET MVC プロジェクトで検出された構成と id コードの移行について説明します。

> [!NOTE]
> サンプルのバージョン番号が最新ではない可能性があります。 必要に応じて、プロジェクトを更新する必要があります。

## <a name="create-the-starter-aspnet-mvc-project"></a>Starter ASP.NET MVC プロジェクトを作成する

アップグレードを示すために、まず ASP.NET MVC アプリを作成します。 名前空間が次の手順で作成する ASP.NET Core プロジェクトと一致するように、 *WebApp1*という名前を付けて作成します。

![Visual Studio の [新しいプロジェクト] ダイアログ](mvc/_static/new-project.png)

![新しい Web アプリケーションダイアログ: ASP.NET templates パネルで選択された MVC プロジェクトテンプレート](mvc/_static/new-project-select-mvc-template.png)

*省略可能:* ソリューションの名前を*WebApp1*から*Mvc5*に変更します。 Visual Studio に新しいソリューション名 (*Mvc5*) が表示されます。これにより、次のプロジェクトからこのプロジェクトを簡単に指定できます。

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core プロジェクトを作成する

2つのプロジェクトの名前空間が一致するように、前のプロジェクト (*WebApp1*) と同じ名前の新しい*空*の ASP.NET Core web アプリを作成します。 同じ名前空間を使用すると、2つのプロジェクト間でコードを簡単にコピーできます。 このプロジェクトは、同じ名前を使用する前のプロジェクトとは別のディレクトリに作成する必要があります。

![[新しいプロジェクト] ダイアログ](mvc/_static/new_core.png)

![New ASP.NET Web Application dialog: ASP.NET Core テンプレートパネルで空のプロジェクトテンプレートが選択されました](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *省略可能:* *Web アプリケーション*プロジェクトテンプレートを使用して、新しい ASP.NET Core アプリを作成します。 プロジェクトに*WebApp1*という名前を設定し、**個々のユーザーアカウント**の認証オプションを選択します。 このアプリの名前を*FullAspNetCore*に変更します。 このプロジェクトを作成すると、変換にかかる時間が短縮されます。 テンプレートで生成されたコードを参照して、最終的な結果を確認したり、変換プロジェクトにコードをコピーしたりできます。 また、テンプレートで生成されたプロジェクトと比較するために変換手順でスタックしている場合にも役立ちます。

## <a name="configure-the-site-to-use-mvc"></a>MVC を使用するようにサイトを構成する

::: moniker range=">= aspnetcore-2.1"

* .NET Core を対象とする場合、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)は既定で参照されます。 このパッケージには、MVC アプリで一般的に使用されるパッケージが含まれています。 .NET Framework を対象とする場合は、パッケージ参照をプロジェクトファイルに個別に一覧表示する必要があります。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* .NET Core を対象とする場合、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage)は既定で参照されます。 このパッケージには、MVC アプリによって一般的に使用されるパッケージが含まれています。 .NET Framework を対象とする場合は、パッケージ参照をプロジェクトファイルに個別に一覧表示する必要があります。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* .NET Core または .NET Framework を対象とする場合、MVC アプリによって頻繁に使用されるパッケージは、プロジェクトファイルに個別に一覧表示されます。

::: moniker-end

`Microsoft.AspNetCore.Mvc` は ASP.NET Core MVC フレームワークです。 `Microsoft.AspNetCore.StaticFiles` は静的ファイルハンドラーです。 ASP.NET Core ランタイムはモジュール形式であり、静的ファイルの提供を明示的に選択する必要があります (「[静的ファイル](xref:fundamentals/static-files)」を参照してください)。

* *Startup.cs*ファイルを開き、次のコードに一致するようにコードを変更します。

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

`UseStaticFiles` 拡張メソッドは、静的ファイルハンドラーを追加します。 前述のように、ASP.NET ランタイムはモジュール形式であるため、静的ファイルの提供を明示的に選択する必要があります。 `UseMvc` 拡張メソッドは、ルーティングを追加します。 詳細については、「[アプリケーションの起動](xref:fundamentals/startup)と[ルーティング](xref:fundamentals/routing)」を参照してください。

## <a name="add-a-controller-and-view"></a>コントローラーとビューを追加する

このセクションでは、最小コントローラーとビューを追加して、次のセクションで移行する ASP.NET MVC コントローラーとビューのプレースホルダーとして機能します。

* *Controllers*フォルダーを追加します。

* *HomeController.cs*という名前の**コントローラークラス**を*Controllers*フォルダーに追加します。

![[新しい項目の追加] ダイアログ](mvc/_static/add_mvc_ctl.png)

* *Views*フォルダーを追加します。

* *ビュー/ホーム*フォルダーを追加します。

* *Views/Home*フォルダーに、 *Index. cshtml*という名前の**Razor ビュー**を追加します。

![[新しい項目の追加] ダイアログ](mvc/_static/view.png)

プロジェクトの構造は次のようになります。

![WebApp1 のファイルとフォルダーを示すソリューションエクスプローラー](mvc/_static/project-structure-controller-view.png)

*Views/Home/Index. cshtml*ファイルの内容を次のように置き換えます。

```html
<h1>Hello world!</h1>
```

アプリケーションを実行します。

![Microsoft Edge で開いている Web アプリ](mvc/_static/hello-world.png)

詳細については、「[コントローラー](xref:mvc/controllers/actions)と[ビュー](xref:mvc/views/overview) 」を参照してください。

最小限の作業 ASP.NET Core プロジェクトを作成したので、ASP.NET MVC プロジェクトから機能の移行を開始できます。 次のものを移動する必要があります。

* クライアント側コンテンツ (CSS、フォント、およびスクリプト)

* controllers

* 表示

* モデル

* まとめる

* フィルタ

* ログイン/送信、Id (これは次のチュートリアルで行います)。

## <a name="controllers-and-views"></a>コントローラーとビュー

* ASP.NET MVC `HomeController` の各メソッドを新しい `HomeController`にコピーします。 ASP.NET MVC では、組み込みテンプレートのコントローラーアクションメソッドの戻り値の型は[Actionresult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); であることに注意してください。ASP.NET Core MVC では、アクションメソッドは代わりに `IActionResult` を返します。 `ActionResult` は `IActionResult`を実装するので、アクションメソッドの戻り値の型を変更する必要はありません。

* ASP.NET MVC プロジェクトの*About. cshtml*、 *Contact.* cshtml、および*Index. cshtml* view ファイルを ASP.NET Core プロジェクトにコピーします。

* ASP.NET Core アプリを実行し、各メソッドをテストします。 レイアウトファイルをまだ移行していないため、表示されているビューにはビューファイルのコンテンツのみが含まれます。 `About` ビューと `Contact` ビューに対してレイアウトファイルが生成されたリンクがないため、ブラウザーからそれらを呼び出す必要があります ( **4492**はプロジェクトで使用されているポート番号に置き換えてください)。

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![連絡先ページ](mvc/_static/contact-page.png)

スタイル設定とメニュー項目がないことに注意してください。 次のセクションでこれを修正します。

## <a name="static-content"></a>静的コンテンツ

以前のバージョンの ASP.NET MVC では、静的コンテンツは web プロジェクトのルートからホストされており、サーバー側ファイルと混在していました。 ASP.NET Core では、静的なコンテンツは*wwwroot*フォルダーでホストされます。 古い ASP.NET MVC アプリの静的コンテンツを ASP.NET Core プロジェクトの*wwwroot*フォルダーにコピーします。 このサンプルの変換の例を次に示します。

* 古い MVC プロジェクトの*favicon*ファイルを ASP.NET Core プロジェクトの*wwwroot*フォルダーにコピーします。

Old ASP.NET MVC プロジェクトでは、そのスタイルに[ブートストラップ](https://getbootstrap.com/)を使用して、ブートストラップファイルを*Content*フォルダーおよび*Scripts*フォルダーに格納します。 古い ASP.NET MVC プロジェクトを生成したテンプレートは、レイアウトファイル (*Views/Shared/_Layout*) のブートストラップを参照します。 ASP.NET MVC プロジェクトの*ブートストラップ*ファイルと*ブートストラップ*ファイルを、新しいプロジェクトの*wwwroot*フォルダーにコピーできます。 代わりに、次のセクションでは、CDNs を使用してブートストラップ (およびその他のクライアント側ライブラリ) のサポートを追加します。

## <a name="migrate-the-layout-file"></a>レイアウトファイルを移行する

* 古い ASP.NET MVC プロジェクトの*views*フォルダーの *_ViewStart*ファイルを ASP.NET Core プロジェクトの*views*フォルダーにコピーします。 *_ViewStart* ASP.NET Core MVC では、このファイルは変更されていません。

* *ビュー/共有*フォルダーを作成します。

* *省略可能:* *FullAspNetCore* MVC プロジェクトの*views*フォルダーから ASP.NET Core プロジェクトの*views*フォルダーに *_ViewImports*をコピーします。 *_ViewImports*ファイル内の名前空間宣言を削除します。 *_ViewImports*のファイルは、すべてのビューファイルの名前空間を提供し、[タグヘルパー](xref:mvc/views/tag-helpers/intro)を取り込みます。 タグヘルパーは、新しいレイアウトファイルで使用されます。 *_ViewImports* 、ASP.NET Core の新しいファイルです。

* 古い ASP.NET MVC プロジェクトの*views/shared*フォルダーの *_Layout*ファイルを ASP.NET Core プロジェクトの*views/shared*フォルダーにコピーします。

*_Layout*ファイルを開き、次のように変更します (完成したコードは次のようになります)。

* `@Styles.Render("~/Content/css")` を `<link>` 要素に置き換えて、*ブートストラップ*を読み込みます (下記参照)。

* `@Scripts.Render("~/bundles/modernizr")`を削除します。

* `@Html.Partial("_LoginPartial")` 行をコメントアウトします (行を `@*...*@`で囲みます)。 詳細については[、「ASP.NET Core への認証と id の移行](xref:migration/identity)」を参照してください。

* `@Scripts.Render("~/bundles/jquery")` を `<script>` の要素に置き換えます (下記参照)。

* `@Scripts.Render("~/bundles/bootstrap")` を `<script>` の要素に置き換えます (下記参照)。

ブートストラップ CSS インクルードの置換マークアップ:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

JQuery および Bootstrap JavaScript インクルードの置換マークアップ:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

更新された *_Layout*ファイルは次のようになります。

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

ブラウザーでサイトを表示します。 これで、必要なスタイルが適切に読み込まれます。

* *省略可能:* 新しいレイアウトファイルを使用することをお勧めします。 このプロジェクトでは、 *FullAspNetCore*プロジェクトからレイアウトファイルをコピーできます。 新しいレイアウトファイルは[タグヘルパー](xref:mvc/views/tag-helpers/intro)を使用し、その他の機能強化が行われています。

## <a name="configure-bundling-and-minification"></a>バンドルと縮小の構成

バンドルと縮小を構成する方法の詳細については、「[バンドルと縮小](../client-side/bundling-and-minification.md)」を参照してください。

## <a name="solve-http-500-errors"></a>HTTP 500 エラーを解決する

問題の原因としては、問題の原因に関する情報が含まれていない HTTP 500 のエラーメッセージが表示されることがあります。 たとえば、 *Views/_ViewImports cshtml*ファイルにプロジェクト内に存在しない名前空間が含まれている場合、HTTP 500 エラーが発生します。 ASP.NET Core アプリの既定では、`UseDeveloperExceptionPage` 拡張機能が `IApplicationBuilder` に追加され、構成が*開発*されるときに実行されます。 詳細については、次のコードを参考にしてください。

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

ASP.NET Core は、web アプリ内の未処理の例外を HTTP 500 エラー応答に変換します。 通常、サーバーに関する機密情報が漏えいするのを防ぐために、エラーの詳細はこれらの応答に含まれていません。 詳細については、「[エラーの処理](../fundamentals/error-handling.md)」の「**開発者の例外ページの使用**」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
