---
title: ASP.NET Core Blazor ホスティング モデルの構成
author: guardrex
description: Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: bd44643877e45c5b48b0972bcc2f637fbc5d98f2
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646790"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor ホスティング モデルの構成

作成者: [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

この記事では、ホスティング モデルの構成について説明します。

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a>Blazor サーバー

### <a name="reflect-the-connection-state-in-the-ui"></a>UI に接続状態を反映する

接続が失われたことがクライアントで検出されると、クライアントによって再接続が試行される間、ユーザーに対して既定の UI が表示されます。 再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。

UI をカスタマイズするには、 *_Host.cshtml* Razor ページの `<body>` に、`components-reconnect-modal` の `id` を持つ要素を定義します。

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。

| CSS クラス                       | 示す内容&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | 接続が失われました。 クライアントによって再接続が試行されています。 モーダルを表示します。 |
| `components-reconnect-hide`     | サーバーへのアクティブな接続が再確立されます。 モーダルを非表示にします。 |
| `components-reconnect-failed`   | 再接続に失敗しました。ネットワーク障害が原因である可能性があります。 再接続を試みるには、`window.Blazor.reconnect()` を呼び出します。 |
| `components-reconnect-rejected` | 再接続が拒否されました。 サーバーに到達したが接続が拒否されたため、サーバー上のユーザーの状態が失われました。 アプリを再度読み込むには、`location.reload()` を呼び出します。 この接続状態は、次の場合に発生する可能性があります。<ul><li>サーバー側回線でクラッシュが発生した場合。</li><li>クライアントが長時間切断されているため、サーバーでユーザーの状態が削除された場合。 ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</li><li>サーバーが再起動されたか、アプリのワーカー プロセスがリサイクルされた場合。</li></ul> |

### <a name="render-mode"></a>表示モード

Blazor サーバー アプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI をプリレンダリングするよう既定で設定されます。 これは、 *_Host.cshtml* Razor ページで設定されます。

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode` により、コンポーネントに対して次の構成が行われます。

* ページにプリレンダリングするかどうか。
* ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報を含めるかどうか。

| `RenderMode`        | 説明 |
| ------------------- | ----------- |
| `ServerPrerendered` | コンポーネントを静的 HTML にレンダリングし、Blazor サーバー アプリのマーカーを含めます。 ユーザー エージェントの起動時に、Blazor アプリをブートストラップするためにこのマーカーが使用されます。 |
| `Server`            | Blazor サーバー アプリのマーカーをレンダリングします。 コンポーネントからの出力は含まれません。 ユーザー エージェントの起動時に、Blazor アプリをブートストラップするためにこのマーカーが使用されます。 |
| `Static`            | コンポーネントを静的 HTML にレンダリングします。 |

静的 HTML ページからのサーバー コンポーネントのレンダリングはサポートされていません。

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Razor ページおよびビューからステートフル対話型コンポーネントをレンダリングする

Razor ページまたはビューには、ステートフル対話型コンポーネントを追加できます。

ページまたはビューがレンダリングされると、次の処理が行われます。

* ページまたはビューと共にコンポーネントがプリレンダリングされます。
* プリレンダリングに使用された初期のコンポーネント状態は失われます。
* SignalR 接続が確立されると、新しいコンポーネント状態が作成されます。

次の Razor ページには、`Counter` コンポーネントがレンダリングされます。

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Razor ページおよびビューから非対話型コンポーネントをレンダリングする

次の Razor ページには、フォームを使用して指定された初期値を使用して、`Counter` コンポーネントが静的にレンダリングされます。

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

`MyComponent` が静的にレンダリングされるため、コンポーネントを対話型にすることはできません。

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Blazor サーバー アプリ用に SignalR クライアントを構成する

場合によっては、Blazor サーバー アプリによって使用される SignalR クライアントを構成する必要があります。 たとえば、接続の問題を診断するために SignalR クライアントのログ記録を構成できます。

*Pages/_Host* ファイルで SignalR クライアントを構成するには、次の手順に従います。

* `blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。
* `Blazor.start` を呼び出し、SignalR ビルダーを指定する構成オブジェクトを渡します。

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```
