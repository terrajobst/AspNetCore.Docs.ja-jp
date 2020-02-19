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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="d1e7d-103">ASP.NET Core Blazor ホスティングモデルの構成</span><span class="sxs-lookup"><span data-stu-id="d1e7d-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="d1e7d-104">[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d1e7d-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="d1e7d-105">この記事では、ホスティングモデルの構成について説明します。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-105">This article covers hosting model configuration.</span></span>

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a><span data-ttu-id="d1e7d-106">Blazor サーバー</span><span class="sxs-lookup"><span data-stu-id="d1e7d-106">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="d1e7d-107">UI の接続状態を反映します。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-107">Reflect the connection state in the UI</span></span>

<span data-ttu-id="d1e7d-108">クライアントが接続が失われたことを検出すると、クライアントが再接続しようとしているときに、既定の UI がユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-108">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="d1e7d-109">再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-109">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="d1e7d-110">UI をカスタマイズするには、 *_Host*の `<body>` の `components-reconnect-modal` の `id` を持つ要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-110">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="d1e7d-111">次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-111">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="d1e7d-112">CSS クラス</span><span class="sxs-lookup"><span data-stu-id="d1e7d-112">CSS class</span></span>                       | <span data-ttu-id="d1e7d-113">&hellip; を示します。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-113">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="d1e7d-114">失われた接続。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-114">A lost connection.</span></span> <span data-ttu-id="d1e7d-115">クライアントが再接続しようとしています。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-115">The client is attempting to reconnect.</span></span> <span data-ttu-id="d1e7d-116">モーダルを表示します。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-116">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="d1e7d-117">サーバーへのアクティブな接続が再確立されます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-117">An active connection is re-established to the server.</span></span> <span data-ttu-id="d1e7d-118">モーダルを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-118">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="d1e7d-119">再接続に失敗しました。ネットワーク障害が原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-119">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="d1e7d-120">再接続を試みるには、`window.Blazor.reconnect()`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-120">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="d1e7d-121">再接続は拒否されました。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-121">Reconnection rejected.</span></span> <span data-ttu-id="d1e7d-122">サーバーに到達したが接続を拒否したため、サーバー上のユーザーの状態が失われています。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-122">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="d1e7d-123">アプリを再度読み込むには、`location.reload()`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-123">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="d1e7d-124">この接続状態は、次の場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-124">This connection state may result when:</span></span><ul><li><span data-ttu-id="d1e7d-125">サーバー側回線でクラッシュが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-125">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="d1e7d-126">サーバーがユーザーの状態を削除するのに十分な時間、クライアントが接続されていません。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-126">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="d1e7d-127">ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-127">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="d1e7d-128">サーバーが再起動されたか、アプリのワーカープロセスがリサイクルされています。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-128">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="d1e7d-129">表示モード</span><span class="sxs-lookup"><span data-stu-id="d1e7d-129">Render mode</span></span>

<span data-ttu-id="d1e7d-130">Blazor サーバーアプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI を事前に作成するために既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-130">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="d1e7d-131">これは *_Host. cshtml* Razor ページで設定されます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-131">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="d1e7d-132">コンポーネントの `RenderMode` を構成します。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-132">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="d1e7d-133">ページに prerendered ます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-133">Is prerendered into the page.</span></span>
* <span data-ttu-id="d1e7d-134">は、ページに静的 HTML として表示されるか、ユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-134">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="d1e7d-135">説明</span><span class="sxs-lookup"><span data-stu-id="d1e7d-135">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="d1e7d-136">コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-136">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="d1e7d-137">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-137">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="d1e7d-138">Blazor サーバーアプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-138">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="d1e7d-139">コンポーネントからの出力は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-139">Output from the component isn't included.</span></span> <span data-ttu-id="d1e7d-140">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-140">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="d1e7d-141">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-141">Renders the component into static HTML.</span></span> |

<span data-ttu-id="d1e7d-142">静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-142">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="d1e7d-143">Razor ページとビューからのステートフル対話型コンポーネントのレンダリング</span><span class="sxs-lookup"><span data-stu-id="d1e7d-143">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="d1e7d-144">ステートフル対話型コンポーネントは、Razor ページまたはビューに追加できます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-144">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="d1e7d-145">ページまたはビューが表示される場合:</span><span class="sxs-lookup"><span data-stu-id="d1e7d-145">When the page or view renders:</span></span>

* <span data-ttu-id="d1e7d-146">コンポーネントは、ページまたはビューと prerendered ます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-146">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="d1e7d-147">プリレンダリングに使用される初期コンポーネントの状態は失われます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-147">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="d1e7d-148">SignalR 接続が確立されると、新しいコンポーネントの状態が作成されます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-148">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="d1e7d-149">次の Razor ページでは、`Counter` コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-149">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="d1e7d-150">Razor ページとビューからの非対話型コンポーネントのレンダリング</span><span class="sxs-lookup"><span data-stu-id="d1e7d-150">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="d1e7d-151">次の Razor ページでは、フォームを使用して指定された初期値を使用して、`Counter` コンポーネントが静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-151">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="d1e7d-152">`MyComponent` は静的にレンダリングされるため、コンポーネントを対話形式にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-152">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="d1e7d-153">Blazor Server アプリ用に SignalR クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="d1e7d-153">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="d1e7d-154">場合によっては、Blazor サーバーアプリによって使用される SignalR クライアントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-154">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="d1e7d-155">たとえば、接続の問題を診断するために SignalR クライアントのログ記録を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-155">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="d1e7d-156">*Pages/_Host cshtml*ファイルで SignalR クライアントを構成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-156">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="d1e7d-157">`blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-157">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="d1e7d-158">`Blazor.start` を呼び出し、SignalR ビルダーを指定する構成オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="d1e7d-158">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
