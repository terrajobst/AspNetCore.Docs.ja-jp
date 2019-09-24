---
title: デバッグ ASP.NET Core Blazor
author: guardrex
description: Blazor アプリをデバッグする方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/debug
ms.openlocfilehash: 3519479d8058f013de23cc9cfa0f5574cd158053
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207208"
---
# <a name="debug-aspnet-core-blazor"></a>デバッグ ASP.NET Core Blazor

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Chrome で Blazor Webassembly アプリをデバッグするための*早期*サポートが存在します。

デバッガーの機能は制限されています。 次のようなシナリオがあります。

* ブレークポイントを設定および削除します。
* コードを実行するか、コードの実行を再開`F8`() します。`F10`
* [ローカル *] 表示で*、、 `int` `string`、および`bool`型のすべてのローカル変数の値を確認します。
* 呼び出し履歴を参照してください。これには、JavaScript から .NET への呼び出しチェーンや、.NET から JavaScript までの呼び出し履歴が含まれます。

次のこと*はできません*。

* `int` 、`string`、または`bool`ではないローカルの値を確認します。
* クラスのプロパティまたはフィールドの値を確認します。
* 変数の上にマウスポインターを合わせると、値が表示されます。
* コンソールで式を評価します。
* 非同期呼び出し間でステップ実行します。
* その他の一般的なデバッグシナリオを実行します。

さらにデバッグを行うシナリオの開発は、エンジニアリングチームにとって重視されています。

## <a name="prerequisites"></a>必須コンポーネント

デバッグには、次のいずれかのブラウザーが必要です。

* Google Chrome (バージョン70以降)
* Microsoft Edge preview ([エッジ開発チャネル](https://www.microsoftedgeinsider.com))

## <a name="procedure"></a>プロシージャ

1. 構成で`Debug` Blazor webassembly を実行します。 [Dotnet run](/dotnet/core/tools/dotnet-run)コマンドに`--configuration Debug`オプションを渡します`dotnet run --configuration Debug`。
1. ブラウザーでアプリにアクセスします。
1. 開発者ツールパネルではなく、アプリにキーボードフォーカスを置きます。 [開発者ツール] パネルは、デバッグを開始したときに閉じることができます。
1. 次の Blazor に固有のキーボードショートカットを選択します。
   * `Shift+Alt+D`Windows/Linux の場合
   * `Shift+Cmd+D`macOS の場合
1. 画面に表示されている手順に従って、リモートデバッグが有効になっているブラウザーを再起動します。
1. 次の Blazor に固有のキーボードショートカットをもう一度選択して、デバッグセッションを開始します。
   * `Shift+Alt+D`Windows/Linux の場合
   * `Shift+Cmd+D`macOS の場合

## <a name="enable-remote-debugging"></a>リモートデバッグを有効にする

リモートデバッグが無効になっている場合は、[デバッグ可能な**ブラウザー] タブ**のエラーページが Chrome によって生成されます。 エラーページには、デバッグポートを開いた状態で Chrome を実行し、Blazor デバッグプロキシがアプリに接続できるようにするための手順が含まれています。 *Chrome インスタンスをすべて閉じ*、指示に従って chrome を再起動します。

## <a name="debug-the-app"></a>アプリをデバッグする

リモートデバッグが有効になっている状態で Chrome を実行すると、デバッグ キーボードショートカットによって新しい デバッガー タブが開きます。しばらくすると、**ソース** タブにアプリ内の .net アセンブリの一覧が表示されます。 各アセンブリを展開し、デバッグに使用できる *.cs*/*ソースファイルを見つけます。* ブレークポイントを設定し、アプリのタブに戻ります。ブレークポイントは、コードの実行時にヒットします。 ブレークポイントにヒットした後、コードを`F10`実行するか (`F8`)、コードの実行を正常に再開します。

Blazor は、 [Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、でプロトコルを補強するデバッグプロキシを提供します。NET 固有の情報。 デバッグキーボードショートカットを押すと、Blazor は、プロキシで Chrome DevTools をポイントします。 プロキシは、デバッグしようとしているブラウザーウィンドウに接続します (したがって、リモートデバッグを有効にする必要があります)。

## <a name="browser-source-maps"></a>ブラウザーソースマップ

ブラウザーソースマップを使用すると、ブラウザーはコンパイルされたファイルを元のソースファイルにマップでき、クライアント側のデバッグによく使用されます。 ただし、Blazor は現在、 C# JavaScript/wasm に直接マップされていません。 代わりに、Blazor はブラウザー内で IL を解釈するため、ソースマップは関係しません。

## <a name="troubleshooting-tip"></a>トラブルシューティングのヒント

エラーが発生した場合は、次のヒントを参考にしてください。

**[デバッガー]** タブで、ブラウザーで開発者ツールを開きます。 コンソールで、を実行`localStorage.clear()`してブレークポイントを削除します。
