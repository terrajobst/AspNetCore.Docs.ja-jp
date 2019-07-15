---
title: ASP.NET Core での Bower でのクライアント側パッケージを管理します。
author: rick-anderson
description: Bower でのクライアント側パッケージの管理。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 08/09/2018
uid: client-side/bower
ms.openlocfilehash: 1292b75bdfe1cea56d9c8bc282b0dd90fc945a33
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813528"
---
# <a name="manage-client-side-packages-with-bower-in-aspnet-core"></a>ASP.NET Core での Bower でのクライアント側パッケージを管理します。

によって[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Noel Rice](https://twitter.com/noelrice1)、および[Scott Addie](https://scottaddie.com)

> [!IMPORTANT]
> Bower を保持したまま、別のソリューションを使用して、管理者がお勧めします。 [ライブラリ マネージャー](https://blogs.msdn.microsoft.com/webdev/2018/04/18/what-happened-to-bower/) (略して LibMan) は、Visual Studio の新しいクライアント側ライブラリ取得ツール (Visual Studio 15.8 またはそれ以降)。 詳細については、「 <xref:client-side/libman/index> 」を参照してください。 Bower は、Visual studio バージョン 15.5 でサポートされます。
>
> Webpack と yarn を 1 つの一般的な代替手段は、[移行手順](https://bower.io/blog/2017/how-to-migrate-away-from-bower/)利用できます。

[Bower](https://bower.io/) 「web 用のパッケージ マネージャー」自体を呼び出すことです。 、.NET エコシステム内で NuGet の静的コンテンツ ファイルを提供できないまま void が挿入されます。 ASP.NET Core プロジェクトでは、これらの静的ファイルはなどのクライアント側ライブラリを本質的な[jQuery](https://jquery.com/)と[ブートス トラップ](https://getbootstrap.com/)します。 .NET ライブラリを使用することも[NuGet](https://www.nuget.org/)パッケージ マネージャー。

設定するクライアント側の ASP.NET Core プロジェクト テンプレートで作成した新しいプロジェクトはビルド プロセスです。 [jQuery](https://jquery.com/)と[ブートス トラップ](https://getbootstrap.com/)がインストールされている場合、Bower はサポートされています。

クライアント側のパッケージに記載されて、 *bower.json*ファイル。 ASP.NET Core のプロジェクト テンプレートを構成します*bower.json* jQuery、jQuery の検証、およびブートス トラップを使用します。

このチュートリアルでは、サポートを追加します[Font Awesome](http://fontawesome.io)します。 Bower パッケージと共にインストールできる、 **Bower パッケージの管理**UI または手動で、 *bower.json*ファイル。

### <a name="installation-via-manage-bower-packages-ui"></a>Bower パッケージの管理 UI を使用してインストール

* 新しい ASP.NET Core Web アプリを作成、 **ASP.NET Core Web アプリケーション (.NET Core)** テンプレート。 選択**Web アプリケーション**と**認証なし**します。

* ソリューション エクスプ ローラーでプロジェクトを右クリックして**Bower パッケージの管理**(またはメイン メニューで、**プロジェクト** > **Bower パッケージの管理**).

* **Bower:\<プロジェクト名\>** ウィンドウで、[参照] タブをクリックし、入力して、パッケージ一覧をフィルター`font-awesome`検索ボックスで。

  ![bower パッケージを管理します。](bower/_static/manage-bower-packages.png)

* いることを確認、"に変更を保存*bower.json*"チェック ボックスがオンにします。 ドロップダウン リストからバージョンを選択し、**インストール**ボタンをクリックします。 **出力**ウィンドウには、インストールの詳細が表示されます。

### <a name="manual-installation-in-bowerjson"></a>Bower.json の手動インストール

開く、 *bower.json*ファイルを開き、依存関係に"フォント awesome"を追加します。 IntelliSense は、利用可能なパッケージを示しています。 パッケージを選択すると、使用可能なバージョンが表示されます。 次のイメージは古いし、表示される内容と一致しません。

![Bower パッケージ エクスプ ローラーの IntelliSense](bower/_static/add-package.png)

![bower バージョン IntelliSense](bower/_static/version-intelliSense.png)

Bower は[セマンティック バージョニング](https://semver.org/)依存関係を整理します。 セマンティック バージョニング、SemVer とも呼ばれる、パッケージを識別する番号付けスキームで\<メジャー >.\<マイナー >。\<パッチ >。 IntelliSense は、いくつかの一般的な選択肢のみを表示することによって、セマンティック バージョン管理を簡略化します。 IntelliSense の一覧 (上記の例では 4.6.3) の一番上の項目は、パッケージの最新の安定バージョンと見なされます。 キャレット (^) 記号が最新のメジャー バージョンと一致して、一致する最新のマイナー バージョンをティルダ (~)。

保存、 *bower.json*ファイル。 Visual Studio のウォッチ、 *bower.json*ファイルの変更。 保存時に、 *bower のインストール*コマンドを実行します。 出力ウィンドウの表示**bower/npm**正確に実行されるコマンドのビュー。

開く、 *.bowerrc*ファイル*bower.json*します。 `directory`プロパティに設定されて*wwwroot/lib*パッケージ資産をインストール、Bower の場所を示します。

```json
{
 "directory": "wwwroot/lib"
}
```

検索し、フォント awesome パッケージを表示するのには、ソリューション エクスプ ローラーで検索ボックスを使用できます。

開く、 *views \shared\_Layout.cshtml*ファイルを開き、フォント awesome の CSS ファイルを環境に追加[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)の`Development`します。 ソリューション エクスプ ローラーからドラッグ アンド ドロップ*フォント awesome.css*内で、`<environment names="Development">`要素。

[!code-html[](bower/sample/_Layout.cshtml?highlight=4&range=9-13)]

運用アプリでは追加*フォント awesome.min.css*の環境タグ ヘルパーを`Staging,Production`します。

内容を置き換える、 *Views\Home\About.cshtml*次のマークアップでの Razor ファイル。

[!code-html[](bower/sample/About.cshtml)]

アプリを実行し、フォント awesome パッケージの動作を検証する About ビューに移動します。

## <a name="exploring-the-client-side-build-process"></a>クライアント側のビルド プロセスを調べる

ほとんどの ASP.NET Core プロジェクト テンプレートは、Bower を使用して既に構成されます。 この次のチュートリアルでは、空の ASP.NET Core プロジェクトから開始して、プロジェクトでの Bower の使用方法の感覚を取得できるように各部分を手動で追加します。 プロジェクトの構造を出力の各構成の変更が加えられると、ランタイムの動作を確認できます。

Bower でクライアント側のビルド プロセスを使用する一般的な手順は次のとおりです。

* プロジェクトで使用されるパッケージを定義します。 <!-- once defined, you don't need to download them, VS does -->
* Web ページからパッケージを参照します。

### <a name="define-packages"></a>パッケージを定義します。

パッケージを一覧表示すると、 *bower.json*ファイル、Visual Studio がダウンロードされます。 次の例では、jQuery とブートス トラップを読み込むに Bower を使用して、 *wwwroot*フォルダー。

* 新しい ASP.NET Core Web アプリを作成、 **ASP.NET Core Web アプリケーション (.NET Core)** テンプレート。 選択、**空**プロジェクト テンプレートとクリック**OK**。

* ソリューション エクスプ ローラーでプロジェクトを右クリックして >**新しい項目の追加**選択**Bower 構成ファイル**します。 メモ:A *.bowerrc*ファイルも追加されます。

* 開いている*bower.json*、jquery を追加し、ブートス トラップ、`dependencies`セクション。 その結果、 *bower.json*ファイルは次の例のようになります。 バージョンは、時間の経過と共にが変更され、下の画像が一致しません。

[!code-json[](bower/sample/bower.json?highlight=5,6)]

* 保存、 *bower.json*ファイル。

  プロジェクトを含むことを確認、*ブートス トラップ*と*jQuery*ディレクトリ*wwwroot/lib*します。 Bower は、 *.bowerrc*で資産をインストールするファイル*wwwroot/lib*します。

  メモ:"Bower パッケージの管理 の UI では、手動のファイルを編集する代わりに提供します。

### <a name="enable-static-files"></a>静的ファイルを有効にします。

* 追加、`Microsoft.AspNetCore.StaticFiles`をプロジェクトに NuGet パッケージ。
* 静的ファイルで処理を有効にする、[静的ファイル ミドルウェア](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions)します。 呼び出しを追加して[UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions)を`Configure`メソッドの`Startup`します。

[!code-csharp[](bower/sample/Startup.cs?highlight=9)]

### <a name="reference-packages"></a>参照パッケージ

このセクションでは、配置したパッケージにアクセスできることを確認する HTML ページを作成します。

* という名前の新しい HTML ページを追加*Index.html*を*wwwroot*フォルダー。 メモ:HTML ファイルを追加する必要があります、 *wwwroot*フォルダー。 既定では、外部の静的コンテンツを提供できない*wwwroot*します。 参照してください[静的ファイル](xref:fundamentals/static-files)詳細についてはします。

  内容を置き換える*Index.html*を次のマークアップ。

[!code-html[](bower/sample/Index.html)]

* アプリを実行しに移動します`http://localhost:<port>/Index.html`します。 代わりに、 *Index.html*開かれると、キーを押して`Ctrl+Shift+W`します。 Jumbotron スタイルが適用される、jQuery コード、ボタンがクリックされたときの応答およびブートス トラップのボタンが状態を変更することを確認します。

  ![jumbotron スタイルの適用](bower/_static/jumbotron.png)
