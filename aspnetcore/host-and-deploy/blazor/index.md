---
title: ASP.NET Core Blazor のホストと展開
author: guardrex
description: Blazor アプリをホストおよび展開する方法を説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/23/2019
no-loc:
- Blazor
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 5c37c3d9f424f4c4b814e1955880623fd95179f2
ms.sourcegitcommit: 918d7000b48a2892750264b852bad9e96a1165a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2019
ms.locfileid: "74550374"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor"></a>ASP.NET Core Blazor のホストと展開

作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>アプリの発行

アプリは、リリース構成での展開のために発行されます。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **[ビルド]**  >  **[Publish {APPLICATION}]\({アプリケーション} を発行する\)** を選択します。
1. *[publish target]\(発行先\)* を選択します。 ローカルに発行するには、 **[フォルダー]** を選択します。
1. **[フォルダーの選択]** フィールド内で既定の場所を受け入れるか、または別の場所を指定します。 **[発行]** ボタンを選びます。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドを使用して、リリース構成によってアプリを発行します。

```dotnetcli
dotnet publish -c Release
```

---

アプリを発行すると、プロジェクトの依存関係の[復元](/dotnet/core/tools/dotnet-restore)がトリガーされ、展開されるアセットを作成する前にプロジェクトが[ビルド](/dotnet/core/tools/dotnet-build)されます。 ビルド プロセスの一環として、アプリのダウンロード サイズを縮小し読み込み時間を短縮するため、未使用のメソッドおよびアセンブリが削除されます。

Blazor WebAssembly アプリは、 */bin/Release/{ターゲット フレームワーク}/publish/{アセンブリ名}/dist* フォルダーに発行されます。 Blazor サーバー アプリは、 */bin/Release/{ターゲット フレームワーク}/publish* フォルダーに発行されます。

フォルダー内のアセットは、Web サーバーに展開されます。 展開のプロセスが手動であるか自動であるかは、ご使用の展開ツールによって異なります。

## <a name="app-base-path"></a>アプリのベース パス

*アプリのベース パス*とは、アプリのルート URL パスのことです。 次の ASP.NET Core アプリと Blazor サブアプリについて考えてみましょう。

* ASP.NET Core アプリは `MyApp` と命名します。
  * このアプリは、物理的に *d:/MyApp* にあります。
  * 要求は、`https://www.contoso.com/{MYAPP RESOURCE}` で受信されます。
* `CoolApp` という名前の Blazor アプリは `MyApp` のサブアプリです。
  * このサブアプリは、物理的に *d:/MyApp/CoolApp* にあります。
  * 要求は、`https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` で受信されます。

`CoolApp` に対して構成を追加指定しない場合、このシナリオではサブ アプリにはそれがサーバー上のどこの場所にあるかわかりません。 たとえば、相対 URL パス `/CoolApp/` にあることがわからない場合、アプリはそのリソースに対する正しい相対 URL を作成できません。

`<base>` タグの `href` 属性は、Blazor アプリのベース パスの `https://www.contoso.com/CoolApp/` に構成を指定するため、*Pages/_Host.cshtml* ファイル (Blazor サーバー) または *wwwroot/index.html* ファイル (Blazor WebAssembly) の相対ルート パスに設定されます。

```html
<base href="/CoolApp/">
```

Blazor サーバー アプリはさらに、`Startup.Configure` のアプリの要求パイプラインで <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> を呼び出すことによって、サーバー側のベース パスを設定します。

```csharp
app.UsePathBase("/CoolApp");
```

相対 URL を指定することにより、ルート ディレクトリに存在しないコンポーネントでアプリのルート パスへの相対 URL を構築できます。 ディレクトリ構造の別のレベルに存在するコンポーネントが、アプリ内のさまざまな場所にある他のリソースに対するリンクを構築できます。 アプリのベース パスは、リンクの `href` ターゲットがアプリのベース パス URI 空間内にある、選択されたハイパーリンクの阻止にも使用されます。 内部のナビゲーションは、Blazor ルーターによって処理されます。

多くのホスティング シナリオでは、アプリへの相対 URL パスは、アプリのルートです。 これらの場合、アプリの相対 URL ベース パスにフォワード スラッシュ (`<base href="/" />`) が付きます。これは、Blazor アプリの既定の構成です。 GitHub ページと IIS サブアプリなど、その他のホスティング シナリオの場合、アプリのベースパスは、アプリへのサーバーの相対 URL パスに設定する必要があります。

アプリのベース パスを設定するには、*Pages/_Host.cshtml* ファイル (Blazor サーバー) または *wwwroot/index.html* ファイル (Blazor WebAssembly) の `<head>` タグ要素内の `<base>` を更新します。 `href` 属性値を `/{RELATIVE URL PATH}/` (末尾にスラッシュが必要) に設定します。ここで、`{RELATIVE URL PATH}` は、アプリの完全な相対 URL パスです。

ルート以外の相対 URL パスが構成されている Blazor WebAssembly アプリの場合 (例: `<base href="/CoolApp/">`)、そのアプリは*ローカルで実行*されると自身のリソースを見つけることができません。 ローカルでの開発およびテスト中は、実行時の `<base>` タグの `href` 値と一致する*パス ベース*引数を指定することで、この問題を克服することができます。 末尾にはスラッシュを含めないでください。 アプリをローカルで実行しているときにパス ベースの引数を渡すには、アプリのディレクトリから `--pathbase` オプションを指定して `dotnet run` コマンドを実行します。

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

相対 URL パスが `/CoolApp/` (`<base href="/CoolApp/">`) の Blazor WebAssembly アプリについては、このコマンドは次のようになります。

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Blazor WebAssembly アプリは `http://localhost:port/CoolApp` でローカルで応答します。

## <a name="deployment"></a>配置

展開のガイダンスについては、次のトピックを参照してください。

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
