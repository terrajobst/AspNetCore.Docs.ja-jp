---
title: ASP.NET Core Blazor ホスティングモデルの構成
author: guardrex
description: Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティングモデルの構成について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: bd44643877e45c5b48b0972bcc2f637fbc5d98f2
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447036"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor ホスティングモデルの構成

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

この記事では、ホスティングモデルの構成について説明します。

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a>Blazor サーバー

### <a name="reflect-the-connection-state-in-the-ui"></a>UI の接続状態を反映します。

クライアントが接続が失われたことを検出すると、クライアントが再接続しようとしているときに、既定の UI がユーザーに表示されます。 再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。

UI をカスタマイズするには、 *_Host*の `<body>` の `components-reconnect-modal` の `id` を持つ要素を定義します。

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。

| CSS クラス                       | &hellip; を示します。 |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | 失われた接続。 クライアントが再接続しようとしています。 モーダルを表示します。 |
| `components-reconnect-hide`     | サーバーへのアクティブな接続が再確立されます。 モーダルを非表示にします。 |
| `components-reconnect-failed`   | 再接続に失敗しました。ネットワーク障害が原因である可能性があります。 再接続を試みるには、`window.Blazor.reconnect()`を呼び出します。 |
| `components-reconnect-rejected` | 再接続は拒否されました。 サーバーに到達したが接続を拒否したため、サーバー上のユーザーの状態が失われています。 アプリを再度読み込むには、`location.reload()`を呼び出します。 この接続状態は、次の場合に発生する可能性があります。<ul><li>サーバー側回線でクラッシュが発生した場合。</li><li>サーバーがユーザーの状態を削除するのに十分な時間、クライアントが接続されていません。 ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</li><li>サーバーが再起動されたか、アプリのワーカープロセスがリサイクルされています。</li></ul> |

### <a name="render-mode"></a>表示モード

Blazor サーバーアプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI を事前に作成するために既定で設定されます。 これは *_Host. cshtml* Razor ページで設定されます。

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

コンポーネントの `RenderMode` を構成します。

* ページに prerendered ます。
* は、ページに静的 HTML として表示されるか、ユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。

| `RenderMode`        | 説明 |
| ------------------- | ----------- |
| `ServerPrerendered` | コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 |
| `Server`            | Blazor サーバーアプリのマーカーをレンダリングします。 コンポーネントからの出力は含まれていません。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 |
| `Static`            | コンポーネントを静的 HTML にレンダリングします。 |

静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Razor ページとビューからのステートフル対話型コンポーネントのレンダリング

ステートフル対話型コンポーネントは、Razor ページまたはビューに追加できます。

ページまたはビューが表示される場合:

* コンポーネントは、ページまたはビューと prerendered ます。
* プリレンダリングに使用される初期コンポーネントの状態は失われます。
* SignalR 接続が確立されると、新しいコンポーネントの状態が作成されます。

次の Razor ページでは、`Counter` コンポーネントがレンダリングされます。

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Razor ページとビューからの非対話型コンポーネントのレンダリング

次の Razor ページでは、フォームを使用して指定された初期値を使用して、`Counter` コンポーネントが静的にレンダリングされます。

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

`MyComponent` は静的にレンダリングされるため、コンポーネントを対話形式にすることはできません。

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Blazor Server アプリ用に SignalR クライアントを構成する

場合によっては、Blazor サーバーアプリによって使用される SignalR クライアントを構成する必要があります。 たとえば、接続の問題を診断するために SignalR クライアントのログ記録を構成することができます。

*Pages/_Host cshtml*ファイルで SignalR クライアントを構成するには、次のようにします。

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
