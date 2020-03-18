---
title: ASP.NET Core Blazor をデバッグする
author: guardrex
description: Blazor アプリをデバッグする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 1b0035af48b82807a6ae14835a41a1ecbef06bb6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648314"
---
# <a name="debug-aspnet-core-blazor"></a>ASP.NET Core Blazor をデバッグする

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Chromium ベースのブラウザー (Chrome/Edge) で、ブラウザー開発ツールを使用して Blazor WebAssembly をデバッグするために、*初期*のサポートが存在します。 以下のための作業が進行中です。

* Visual Studio でのデバッグを完全に可能にする。
* Visual Studio Code でのデバッグを可能にする。

デバッガーの機能は制限されています。 利用可能なシナリオには以下が含まれます。

* ブレークポイントの設定と削除を行う。
* コード全体でステップ実行する (`F10`)、コードの実行を再開 (`F8`) する。
* *Locals* の表示で、`int`、`string`、`bool` 型の任意のローカル変数の値を観察する。
* 呼び出し履歴を表示する。これには、JavaScript から .NET への呼び出しチェーンと、.NET から JavaScript への呼び出しチェーンが含まれます。

以下のことは*行えません*。

* `int`、`string`、`bool` ではないローカルの値を観察する。
* クラスのプロパティまたはフィールドの値を観察する。
* 変数をポイントしてその値を表示する。
* コンソール内で式を評価する。
* 複数の非同期呼び出しにわたってステップ実行する。
* その他の最も一般的なデバッグ シナリオを実行する。

エンジニアリング チームでは、これ以上のデバッグ シナリオの開発が引き続き重視されています。

## <a name="prerequisites"></a>必須コンポーネント

デバッグには、次のいずれかのブラウザーが必要です。

* Google Chrome (バージョン 70 以降)
* Microsoft Edge プレビュー ([Edge Dev Channel](https://www.microsoftedgeinsider.com))

## <a name="procedure"></a>プロシージャ

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!WARNING]
> Visual Studio でのデバッグのサポートは、開発の初期段階にあります。 **F5** でのデバッグは現在サポートされていません。

1. デバッグを行わずに `Debug` 構成で Blazor WebAssembly アプリを実行します (**F5** ではなく **Ctrl**+**F5**)。
1. アプリの [デバッグ] プロパティ ( **[デバッグ]** メニューの最後のエントリ) を開き、HTTP の **[アプリ URL]** をコピーします。 Chromium ベースのブラウザー (Edge ベータまたは Chrome) を使用して、アプリの HTTP アドレス (HTTPS アドレスではありません) を参照します。
1. 開発者ツール パネルではなく、ブラウザーのウィンドウで、キーボードのフォーカスをアプリに合わせます。 この手順では、開発者ツール パネルを閉じたままにしておくのが適切です。 デバッグが開始された後で、開発者ツール パネルを再び開くことができます。
1. 以下の Blazor 固有のキーボード ショートカットを選択します。

   * Windows の場合: `Shift+Alt+D`
   * macOS の場合: `Shift+Cmd+D`

   **[Unable to find debuggable browser tab] (デバッグ可能なブラウザー タブが見つかりません)** と表示される場合は、「[リモート デバッグの有効化](#enable-remote-debugging)」を参照してください。
   
   リモート デバッグの有効化後:
   
   1\. 新しいブラウザー ウィンドウが開きます。 前のウィンドウを閉じます。

   2\. ブラウザーのウィンドウで、キーボードのフォーカスをアプリに合わせます。

   3\. 新しいブラウザー ウィンドウで Blazor 固有のキーボード ショートカットを選択します。Windows の場合は `Shift+Alt+D`、macOS の場合は `Shift+Cmd+D` です。

   4\. ブラウザーに **[DevTools]** タブが表示されます。 **ブラウザー ウィンドウでアプリのタブを再度選択します。**

   アプリを Visual Studio にアタッチするには、「[Visual Studio でプロセスにアタッチする](#attach-to-process-in-visual-studio)」セクションを参照してください。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. `--configuration Debug` オプションを [dotnet run](/dotnet/core/tools/dotnet-run) コマンドに渡すことによって、`Debug` 構成で Blazor WebAssembly アプリを実行します: `dotnet run --configuration Debug`。
1. シェルのウィンドウに表示された HTTP URL にあるアプリに移動します。
1. 開発者ツール パネルではなく、アプリにキーボードのフォーカスを合わせます。 この手順では、開発者ツール パネルを閉じたままにしておくのが適切です。 デバッグが開始された後で、開発者ツール パネルを再び開くことができます。
1. 以下の Blazor 固有のキーボード ショートカットを選択します。

   * Windows の場合: `Shift+Alt+D`
   * macOS の場合: `Shift+Cmd+D`

   **[Unable to find debuggable browser tab] (デバッグ可能なブラウザー タブが見つかりません)** と表示される場合は、「[リモート デバッグの有効化](#enable-remote-debugging)」を参照してください。
   
   リモート デバッグの有効化後:
   
   1\. 新しいブラウザー ウィンドウが開きます。 前のウィンドウを閉じます。

   2\. 開発者ツール パネルではなく、ブラウザーのウィンドウで、キーボードのフォーカスをアプリに合わせます。

   3\. 新しいブラウザー ウィンドウで Blazor 固有のキーボード ショートカットを選択します。Windows の場合は `Shift+Alt+D`、macOS の場合は `Shift+Cmd+D` です。

---

## <a name="enable-remote-debugging"></a>リモート デバッグの有効化

リモートデバッグが無効になっている場合、 **[Unable to find debuggable browser tab] (デバッグ可能なブラウザー タブが見つかりません)** というエラー ページが Chrome によって生成されます。 エラー ページには、Blazor デバッグ プロキシがアプリに接続できるように、デバッグ ポートを開いた状態で Chrome を実行するための手順が記載されています。 *すべての Chrome インスタンスを閉じ*、指示のとおり Chrome を再起動します。

## <a name="debug-the-app"></a>アプリをデバッグする

リモート デバッグが有効な状態で Chrome を実行すると、デバッグ用のキーボード ショートカットによって新しいデバッガー タブが開きます。少しすると、 **[ソース]** タブに、アプリ内の .NET アセンブリの一覧が表示されます。 各アセンブリを展開して、デバッグに使用できる *.cs*/ *.razor* ソース ファイルを見つけます。 ブレークポイントを設定し、アプリのタブに戻ります。コードの実行時にブレークポイントにヒットします。 ブレークポイントにヒットした後、コード全体をステップ実行する (`F10`) か、コードの実行を普通に再開 (`F8`) します。

Blazor は、[Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、.NET 固有の情報によってプロトコルを拡張するデバッグ プロキシを備えています。 デバッグ用のキーボード ショートカットが押されると、Blazor はプロキシで Chrome DevTools を指し示します。 プロキシは、デバッグしようとしているブラウザー ウィンドウに接続します (そのためリモート デバッグを有効にする必要があります)。

## <a name="attach-to-process-in-visual-studio"></a>Visual Studio でプロセスにアタッチする

Visual Studio でのアプリのプロセスへのアタッチは、Blazor WebAssembly 用の*一時的*なデバッグ シナリオです。一方、**F5** によるデバッグは開発中です。

実行中のアプリのプロセスを Visual Studio にアタッチするには、次の手順を実行します。

1. Visual Studio で、 **[デバッグ]**  >  **[プロセスにアタッチ]** と選択します。
1. **[接続の種類]** で、 **[Chrome devtools protocol websocket (no authentication)] (Chrome DevTools Protocol Websocket (認証なし))** を選択します。
1. **[接続先]** には、アプリの HTTP アドレス (HTTPS アドレスではありません) を貼り付けます。
1. **[最新の情報に更新]** を選択し、 **[選択可能なプロセス]** のエントリを最新の情報に更新します。
1. デバッグするブラウザー プロセスを選択し、 **[アタッチ]** を選択します。
1. **[コードの種類の選択]** ダイアログで、アタッチ先の特定ブラウザーのコードの種類 (Edge または Chrome) を選択し、 **[OK]** を選択します。

## <a name="browser-source-maps"></a>ブラウザー ソース マップ

ブラウザー ソース マップを使用すると、ブラウザーのコンパイルされたファイルを元のソース ファイルにマップし直すことができ、これはクライアント側のデバッグによく使用されます。 ただし Blazor では現在、C# は JavaScript/WASM に直接マップされません。 その代わり、Blazor はブラウザー内で中間言語の解釈を行うため、ソース マップは関係がありません。

## <a name="troubleshoot"></a>トラブルシューティング

エラーが発生している場合は、次のヒントが役立つことがあります。

**[デバッガー]** タブで、ブラウザー内の開発者ツールを開きます。 コンソールで `localStorage.clear()` を実行して、任意のブレークポイントを削除します。
