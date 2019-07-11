---
title: LibMan を Visual Studio で ASP.NET Core を使用します。
author: scottaddie
description: LibMan を Visual Studio を使用した ASP.NET Core プロジェクトで使用する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: ebfb405516d968bf5d5b8cff956a9892457027f2
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813457"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>LibMan を Visual Studio で ASP.NET Core を使用します。

作成者: [Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio はの組み込みサポート[LibMan](xref:client-side/libman/index)など、ASP.NET Core プロジェクトで。

* 構成とビルドで LibMan 復元操作の実行をサポートします。
* LibMan をトリガーするためのメニュー項目は、復元し、操作をクリーンアップします。
* ライブラリを検索して、ファイルをプロジェクトに追加する検索ダイアログ。
* 編集のサポート*libman.json*&mdash;LibMan マニフェスト ファイル。

[サンプル コードのダウンロードを表示または](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(ダウンロードする方法)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>必須コンポーネント

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード

## <a name="add-library-files"></a>ライブラリ ファイルを追加します。

ライブラリ ファイルは、2 つの方法で ASP.NET Core プロジェクトに追加できます。

1. [クライアント側ライブラリを追加 ダイアログを使用します。](#use-the-add-client-side-library-dialog)
1. [LibMan マニフェスト ファイルのエントリを手動で構成します。](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>クライアント側ライブラリを追加 ダイアログを使用します。

クライアント側ライブラリをインストールする手順に従います。

* **ソリューション エクスプ ローラー**ファイルを追加するプロジェクト フォルダーを右クリックします。 選択**追加** > **クライアント側ライブラリ**します。 **クライアント側ライブラリを追加**ダイアログが表示されます。

  ![クライアント側ライブラリを追加ダイアログ](_static/add-library-dialog.png)

* ライブラリ プロバイダーの選択、**プロバイダー**ドロップダウンします。 CDNJS は、既定のプロバイダーです。
* フェッチするライブラリの名前を入力、**ライブラリ**テキスト ボックス。 IntelliSense では、指定されたテキストで始まるライブラリの一覧を示します。
* IntelliSense の一覧からライブラリを選択します。 ライブラリ名が付くに注意してください、`@`記号と、選択したプロバイダーに既知の最新の安定バージョン。
* 含めるファイルを決定します。
  * 選択、**ライブラリのすべてのファイルを含める**ラジオ ボタンをすべてのライブラリのファイルを含めます。
  * 選択、**特定のファイルを選択**ラジオ ボタンは、ライブラリのファイルのサブセットが含まれます。 ラジオ ボタンを選択すると、ファイルのセレクターのツリーが有効になります。 ダウンロードするファイル名の左側にあるボックスを確認します。
* 内のファイルを格納するためのプロジェクト フォルダーを指定して、**ターゲットの場所**テキスト ボックス。 、推奨事項としては、別のフォルダーに各ライブラリを格納します。

  提案される**ターゲットの場所**フォルダーは、ダイアログの起動元の場所に基づきます。

  * 場合は、プロジェクトのルートから起動します。
    * *wwwroot/lib*場合に使用*wwwroot*存在します。
    * *lib*場合に使用*wwwroot*が存在しません。
  * プロジェクト フォルダーから起動する場合は、対応するフォルダー名が使用されます。

  フォルダーの提案は、ライブラリ名サフィックスとして付けられます。 次の表は、Razor ページ プロジェクトで jQuery をインストールするときに、フォルダーの推奨事項を示します。
  
  |場所を起動します。                           |推奨されるフォルダー      |
  |------------------------------------------|----------------------|
  |プロジェクトのルート (場合*wwwroot*が存在する)        |*wwwroot/lib/jquery/* |
  |プロジェクトのルート (場合*wwwroot*が存在しない) |*lib/jquery/*         |
  |*ページ*プロジェクト フォルダー                 |*Pages/jquery/*       |

* をクリックして、**インストール**ボタンで構成ごとのファイルをダウンロードする*libman.json*します。
* レビュー、**ライブラリ マネージャー**のフィード、**出力**インストールの詳細についてはウィンドウ。 例:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a>LibMan マニフェスト ファイルのエントリを手動で構成します。

Visual Studio でのすべての LibMan 操作は、プロジェクトのルートの LibMan マニフェストの内容に基づきます (*libman.json*)。 手動で編集できます*libman.json*プロジェクトのライブラリ ファイルを構成します。 Visual Studio は、すべてのライブラリ ファイルを 1 回復元*libman.json*保存されます。

開くには*libman.json*編集に関しては、次のオプションが存在します。

* ダブルクリックして、 *libman.json*ファイル**ソリューション エクスプ ローラー**します。
* プロジェクトを右クリックして**ソリューション エクスプ ローラー**選択**クライアント側ライブラリを管理**します。 **&#8224;**
* 選択**クライアント側ライブラリを管理**Visual Studio から**プロジェクト**メニュー。 **&#8224;**

**&#8224;** 場合、 *libman.json*プロジェクトのルートにファイルが存在しない、既定の項目テンプレートの内容で作成されます。

Visual Studio では、豊富な JSON 編集の色付けなどのサポート、書式設定、IntelliSense、およびスキーマ検証を提供します。 LibMan マニフェストの JSON スキーマで見つかった[ https://json.schemastore.org/libman](https://json.schemastore.org/libman)します。

LibMan がで定義されている構成ごとにファイルを取得する次のマニフェスト ファイルで、`libraries`プロパティ。 内で定義されているオブジェクト リテラルの説明`libraries`に従います。

* サブセットの[jQuery](https://jquery.com/)バージョン 3.3.1 が CDNJS プロバイダーから取得します。 サブセットが定義されている、`files`プロパティ&mdash;*jquery.min.js*、 *jquery.js*、および*jquery.min.map*します。 ファイルはプロジェクトの配置*wwwroot/ライブラリ/jquery*フォルダー。
* 全体[ブートス トラップ](https://getbootstrap.com/)4.1.3 のバージョンが取得され、配置、*ブートス トラップwwwroot/ライブラリ/* フォルダー。 オブジェクト リテラルの`provider`プロパティのオーバーライド、`defaultProvider`プロパティの値。 LibMan は、unpkg プロバイダーからブートス トラップ ファイルを取得します。
* サブセットの[Lodash](https://lodash.com/)組織内で管理本体によって承認されました。 *Lodash.js*と*lodash.min.js*からローカル ファイル システムのファイルが取得*c:\\temp\\lodash\\* します。 ファイルをプロジェクトのコピーは*wwwroot/ライブラリ/lodash*フォルダー。

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan は、各プロバイダーから各ライブラリの 1 つのバージョンのみをサポートします。 *Libman.json*ファイル指定されたプロバイダーの同じライブラリ名を持つ 2 つのライブラリが含まれているスキーマの検証は失敗します。

## <a name="restore-library-files"></a>ライブラリ ファイルを復元します。

Visual Studio 内からのライブラリ ファイルを復元する必要があります、有効な*libman.json*プロジェクト ルートにあるファイル。 復元されたファイルは、各ライブラリの指定された場所にプロジェクトに配置されます。

2 つの方法で ASP.NET Core プロジェクトでは、ライブラリ ファイルを復元できます。

1. [ビルド中にファイルを復元します。](#restore-files-during-build)
1. [ファイルを手動で復元します。](#restore-files-manually)

### <a name="restore-files-during-build"></a>ビルド中にファイルを復元します。

LibMan は、ビルド プロセスの一部として定義されているライブラリ ファイルを復元できます。 既定で、*ビルド時の復元*動作を無効にします。

有効にして、ビルド時の復元の動作をテストします。

* 右クリック*libman.json*で**ソリューション エクスプ ローラー**選択と**復元クライアント側のライブラリでビルドを有効にする**コンテキスト メニュー。
* をクリックして、**はい**NuGet パッケージをインストールするように求められたらボタンをクリックします。 [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet パッケージがプロジェクトに追加します。

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* LibMan ファイル復元が発生することを確認します。 プロジェクトをビルドします。 `Microsoft.Web.LibraryManager.Build`パッケージは、プロジェクトのビルド操作中に LibMan を実行する MSBuild ターゲットを挿入します。
* レビュー、**ビルド**のフィード、**出力**LibMan アクティビティ ログのウィンドウ。

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

ビルド時の復元動作が有効にすると、 *libman.json*コンテキスト メニューが表示されます、**復元クライアント側のライブラリでビルドを無効にする**オプション。 このオプションを選択すると削除、`Microsoft.Web.LibraryManager.Build`パッケージ、プロジェクト ファイルから参照します。 その結果、クライアント側ライブラリは不要になった各ビルドで復元されます。

ビルド時の復元の設定に関係なく手動で復元できますから、いつでも、 *libman.json*コンテキスト メニュー。 詳細については、次を参照してください。[ファイルを手動で復元](#restore-files-manually)します。

### <a name="restore-files-manually"></a>ファイルを手動で復元します。

ライブラリ ファイルを手動で復元するには。

* ソリューション内のすべてのプロジェクト。
  * ソリューション名を右クリックして**ソリューション エクスプ ローラー**します。
  * 選択、**クライアント側ライブラリを復元**オプション。
* 特定のプロジェクト。
  * 右クリックし、 *libman.json*ファイル**ソリューション エクスプ ローラー**します。
  * 選択、**クライアント側ライブラリを復元**オプション。

復元操作の実行: 中

* Visual Studio のステータス バーにタスク ステータス センター (TSC) アイコンがアニメーション化して、読み取りは*復元操作が開始された*します。 アイコンをクリックすると、既知のバック グラウンド タスクを一覧表示するツールヒントが開きます。
* メッセージは、ステータス バーに送信して、**ライブラリ マネージャー**のフィード、**出力**ウィンドウ。 例:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a>ライブラリ ファイルを削除します。

実行する、*クリーン*操作で、Visual Studio で以前に復元されたライブラリ ファイルを削除します。

* 右クリックし、 *libman.json*ファイル**ソリューション エクスプ ローラー**します。
* 選択、**クライアント側ライブラリをクリーン**オプション。

ライブラリ以外のファイルの意図しない削除を防ぐためには、クリーンの操作は、ディレクトリ全体を削除されません。 前の復元に含まれていたファイルを削除するだけです。

クリーン操作が実行されている場合: 中

* Visual Studio のステータス バーに TSC アイコンは、アニメーション化して、読み取りは*クライアント ライブラリの操作を開始*します。 アイコンをクリックすると、既知のバック グラウンド タスクを一覧表示するツールヒントが開きます。
* ステータス バーに送信されるメッセージと**ライブラリ マネージャー**のフィード、**出力**ウィンドウ。 例えば:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

クリーン操作では、ファイルがプロジェクトからのみ削除します。 ライブラリ ファイルは、将来の復元操作に高速な検索のキャッシュに保存します。 ローカル コンピューターのキャッシュに格納されているライブラリ ファイルを管理するには、使用、 [LibMan CLI](xref:client-side/libman/libman-cli)します。

## <a name="uninstall-library-files"></a>ライブラリ ファイルをアンインストールします。

ライブラリ ファイルをアンインストールするには。

* 開いている*libman.json*します。
* 対応の内側にキャレットを配置`libraries`オブジェクト リテラル。
* 左の余白に表示される電球アイコンをクリックし、選択**アンインストール\<library_name > @\<library_version >** :

  ![ライブラリのコンテキスト メニュー オプションをアンインストールします。](_static/uninstall-menu-option.png)

または、手動で編集して保存できます LibMan マニフェスト (*libman.json*)。 [復元操作に](#restore-library-files)ファイルを保存するときに実行されます。 ライブラリ ファイルで定義されている不要になった*libman.json*プロジェクトから削除されます。

## <a name="update-library-version"></a>ライブラリのバージョンを更新します。

ライブラリの更新バージョンを確認します。

* 開いている*libman.json*します。
* 対応の内側にキャレットを配置`libraries`オブジェクト リテラル。
* 左余白に表示される電球アイコンをクリックします。 ポインターを合わせる**更新プログラムの確認**します。

LibMan は、インストールされているバージョンより新しいバージョンのライブラリをチェックします。 結果は次が発生することができます。

* A**なしの更新が見つかりました**最新バージョンが既にインストールされている場合、メッセージが表示されます。
* 最新の安定バージョンが表示されていない場合は。

  ![コンテキスト メニュー オプションの更新プログラムを確認します。](_static/update-menu-option.png)

* インストールされているバージョンより新しいのプレリリース版を使用できる場合は、プレリリース版が表示されます。

ライブラリの以前のバージョンにダウン グレードする手動で編集、 *libman.json*ファイル。 保存すると、ファイルは、LibMan[復元操作に](#restore-library-files):

* 以前のバージョンからの余分なファイルを削除します。
* 新しいバージョンから、新規および更新されたファイルを追加します。

## <a name="additional-resources"></a>その他の技術情報

* <xref:client-side/libman/libman-cli>
* [LibMan の GitHub リポジトリ](https://github.com/aspnet/LibraryManager)
