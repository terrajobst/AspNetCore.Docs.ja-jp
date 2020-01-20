---
title: デバッグ ASP.NET Core Blazor
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
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159990"
---
# <a name="debug-aspnet-core-opno-locblazor"></a>デバッグ ASP.NET Core Blazor

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Chromium ベースのブラウザー (Chrome/Edge) でブラウザー開発ツールを使用することにより、Blazor デバッグのための*早期*サポートが導入されています。 次の作業が進行中です:

* Visual Studio でのデバッグを完全に有効にします。
* Visual Studio Code でデバッグを有効にします。

デバッガーの機能は制限されています。 次のようなシナリオがあります。

* ブレークポイントを設定および削除します。
* コードを使用するか、コードの実行を再開 (`F8`) する (`F10`)。
* *[ローカル] 表示で*、`int`、`string`、および `bool`型のローカル変数の値を確認します。
* 呼び出し履歴を参照してください。これには、JavaScript から .NET への呼び出しチェーンや、.NET から JavaScript までの呼び出し履歴が含まれます。

次のこと*はできません*。

* `int`、`string`、`bool`ではないローカルの値を確認します。
* クラスのプロパティまたはフィールドの値を確認します。
* 変数の上にマウスポインターを合わせると、値が表示されます。
* コンソールで式を評価します。
* 非同期呼び出し間でステップ実行します。
* その他の一般的なデバッグシナリオを実行します。

さらにデバッグを行うシナリオの開発は、エンジニアリングチームにとって重視されています。

## <a name="prerequisites"></a>Prerequisites

デバッグには、次のいずれかのブラウザーが必要です。

* Google Chrome (バージョン70以降)
* Microsoft Edge preview ([エッジ開発チャネル](https://www.microsoftedgeinsider.com))

## <a name="procedure"></a>プロシージャ

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!WARNING]
> Visual Studio でのデバッグのサポートは、開発の初期段階です。 **F5**のデバッグは現在サポートされていません。

1. デバッグせずに `Debug` の構成で Blazor WebAssembly を実行します ( **F5**キーではなく、Ctrl+**F5** **キーを押し**ます)。
1. アプリのデバッグプロパティ ( **[デバッグ]** メニューの最後のエントリ) を開き、HTTP**アプリの URL**をコピーします。 Chromium ベースのブラウザー (Edge ベータまたは Chrome) を使用して、アプリの HTTP アドレス (HTTPS アドレスではない) を参照します。
1. 開発者ツールパネルではなく、ブラウザーウィンドウでキーボードフォーカスをアプリに配置します。 この手順では、開発者ツールパネルを閉じたままにしておくことをお勧めします。 デバッグが開始されたら、[開発者ツール] パネルを再び開くことができます。
1. 次の Blazor固有のキーボードショートカットを選択します。

   * Windows での `Shift+Alt+D`
   * macOS の `Shift+Cmd+D`

   [デバッグ可能な**ブラウザーを見つけることができません] タブ**が表示される場合は、「[リモートデバッグの有効化](#enable-remote-debugging)」を参照してください。
   
   リモートデバッグを有効にした後:
   
   1\. 新しいブラウザー ウィンドウが開きます。 前のウィンドウを閉じます。

   2\. ブラウザーウィンドウで、キーボードフォーカスをアプリに配置します。

   3\. 新しいブラウザーウィンドウで Blazor固有のキーボードショートカットを選択します。 Windows の場合は `Shift+Alt+D`、macOS の場合は `Shift+Cmd+D` です。

   4\. **[Devtools]** タブがブラウザーに表示されます。 **ブラウザーウィンドウでアプリのタブを再度選択します。**

   アプリを Visual Studio にアタッチするには、「 [Visual studio でプロセスにアタッチ](#attach-to-process-in-visual-studio)する」セクションを参照してください。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. `--configuration Debug` オプションを[dotnet run](/dotnet/core/tools/dotnet-run)コマンドに渡すことによって `Debug` 構成で Blazor WebAssembly を実行します: `dotnet run --configuration Debug`。
1. シェルのウィンドウに表示されている HTTP URL でアプリに移動します。
1. 開発者ツールパネルではなく、アプリにキーボードフォーカスを置きます。 この手順では、開発者ツールパネルを閉じたままにしておくことをお勧めします。 デバッグが開始されたら、[開発者ツール] パネルを再び開くことができます。
1. 次の Blazor固有のキーボードショートカットを選択します。

   * Windows での `Shift+Alt+D`
   * macOS の `Shift+Cmd+D`

   [デバッグ可能な**ブラウザーを見つけることができません] タブ**が表示される場合は、「[リモートデバッグの有効化](#enable-remote-debugging)」を参照してください。
   
   リモートデバッグを有効にした後:
   
   1\. 新しいブラウザー ウィンドウが開きます。 前のウィンドウを閉じます。

   2\. 開発者ツールパネルではなく、ブラウザーウィンドウでキーボードフォーカスをアプリに配置します。

   3\. 新しいブラウザーウィンドウで Blazor固有のキーボードショートカットを選択します。 Windows の場合は `Shift+Alt+D`、macOS の場合は `Shift+Cmd+D` です。

---

## <a name="enable-remote-debugging"></a>リモート デバッグの有効化

リモートデバッグが無効になっている場合は、[デバッグ可能な**ブラウザー] タブ**のエラーページが Chrome によって生成されます。 このエラーページには、デバッグポートを開いた状態で Chrome を実行し、Blazor デバッグプロキシがアプリに接続できるようにするための手順が含まれています。 *Chrome インスタンスをすべて閉じ*、指示に従って chrome を再起動します。

## <a name="debug-the-app"></a>アプリをデバッグする

リモートデバッグが有効になっている状態で Chrome を実行すると、デバッグ キーボードショートカットによって新しい デバッガー タブが開きます。しばらくすると、**ソース** タブにアプリ内の .net アセンブリの一覧が表示されます。 各アセンブリを展開し、デバッグに使用できる *.cs*/のソースファイルを見つけ*ます。* ブレークポイントを設定し、アプリのタブに戻ります。ブレークポイントは、コードの実行時にヒットします。 ブレークポイントにヒットした後、コードを実行するか (`F8`) コードを正常に実行 (`F10`) します。

Blazor には、 [Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、でプロトコルを補強するデバッグプロキシが用意されています。NET 固有の情報。 デバッグ用のキーボードショートカットが押されると、Blazor は、プロキシで Chrome DevTools をポイントします。 プロキシは、デバッグしようとしているブラウザーウィンドウに接続します (したがって、リモートデバッグを有効にする必要があります)。

## <a name="attach-to-process-in-visual-studio"></a>Visual Studio でプロセスにアタッチ

Visual Studio でアプリのプロセスにアタッチすることは、 **F5 キーを押し**てデバッグを実行しているときに Blazor WebAssembly*一時的*なデバッグシナリオです。

実行中のアプリのプロセスを Visual Studio にアタッチするには、次の手順を実行します。

1. Visual Studio で、**デバッグ** > **プロセスにアタッチ** を選択します。
1. **[接続の種類]** で、 **[Chrome devtools protocol websocket (認証なし)]** を選択します。
1. **接続ターゲット**として、アプリの HTTP アドレス (HTTPS アドレスではない) を貼り付けます。
1. **[更新]** を選択して **[利用可能なプロセス]** のエントリを更新します。
1. デバッグするブラウザープロセスを選択し、 **[アタッチ]** を選択します。
1. **[コードの種類の選択**] ダイアログボックスで、アタッチしている特定のブラウザー (Edge または Chrome) のコードの種類を選択し、[ **OK]** を選択します。

## <a name="browser-source-maps"></a>ブラウザーソースマップ

ブラウザーソースマップを使用すると、ブラウザーはコンパイルされたファイルを元のソースファイルにマップでき、クライアント側のデバッグによく使用されます。 ただし、現在、Blazor はC# JavaScript/wasm に直接マップされていません。 代わりに、Blazor はブラウザー内で IL を解釈するため、ソースマップは関係ありません。

## <a name="troubleshoot"></a>トラブルシューティング

エラーが発生した場合は、次のヒントを参考にしてください。

**[デバッガー]** タブで、ブラウザーで開発者ツールを開きます。 コンソールで `localStorage.clear()` を実行して、ブレークポイントを削除します。
