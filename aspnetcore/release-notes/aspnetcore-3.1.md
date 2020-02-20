---
title: ASP.NET Core 3.1 の新機能
author: rick-anderson
description: ASP.NET Core 3.1 の新機能について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: f375022ad3ebdea2990f626320ef295926f88c22
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447439"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="9b354-103">ASP.NET Core 3.1 の新機能</span><span class="sxs-lookup"><span data-stu-id="9b354-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="9b354-104">この記事では、ASP.NET Core 3.1 の最も大きな変更点について説明します。また、関連するドキュメントへのリンクも示します。</span><span class="sxs-lookup"><span data-stu-id="9b354-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="9b354-105">Razor コンポーネントの部分クラスのサポート</span><span class="sxs-lookup"><span data-stu-id="9b354-105">Partial class support for Razor components</span></span>

<span data-ttu-id="9b354-106">Razor コンポーネントが部分クラスとして生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="9b354-106">Razor components are now generated as partial classes.</span></span> <span data-ttu-id="9b354-107">Razor コンポーネントのコードは、単一ファイル内のコンポーネントのすべてのコードを定義するのではなく、部分クラスとして定義された分離コード ファイルを使用して記述できます。</span><span class="sxs-lookup"><span data-stu-id="9b354-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="9b354-108">詳細については、「[部分クラスのサポート](xref:blazor/components#partial-class-support)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b354-108">For more information, see [Partial class support](xref:blazor/components#partial-class-support).</span></span>

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor<span data-ttu-id="9b354-109"> コンポーネント タグ ヘルパーと最上位レベルのコンポーネントへのパラメーターの引き渡し</span><span class="sxs-lookup"><span data-stu-id="9b354-109"> Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="9b354-110">ASP.NET Core 3.0 がインストールされた Blazor では、コンポーネントは HTML ヘルパー (`Html.RenderComponentAsync`) を使用してページとビューにレンダリングされていました。</span><span class="sxs-lookup"><span data-stu-id="9b354-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="9b354-111">ASP.NET Core 3.1 では、新しいコンポーネント タグ ヘルパーを使用して、ページまたはビューからコンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="9b354-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="9b354-112">HTML ヘルパーは ASP.NET Core 3.1 でも引き続きサポートされていますが、コンポーネント タグ ヘルパーをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9b354-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

Blazor<span data-ttu-id="9b354-113"> 初回のレンダリング時に、サーバーアプリで最上位レベルのコンポーネントにパラメーターを渡すことができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="9b354-113"> Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="9b354-114">以前は、最上位レベルのコンポーネントにパラメーターを渡すには、[RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static) を使用するしかありませんでした。</span><span class="sxs-lookup"><span data-stu-id="9b354-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="9b354-115">このリリースでは、[RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) と [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) の両方がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9b354-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="9b354-116">指定されたパラメーター値はすべて JSON としてシリアル化され、初回の応答に含まれます。</span><span class="sxs-lookup"><span data-stu-id="9b354-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="9b354-117">たとえば、インクリメント量 (`IncrementAmount`) を使用して `Counter` コンポーネントを事前レンダリングする場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9b354-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="9b354-118">詳細については、「[コンポーネントを Razor Pages と MVC アプリに統合する](xref:blazor/integrate-components)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b354-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/integrate-components).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="9b354-119">Http.sys での共有キューのサポート</span><span class="sxs-lookup"><span data-stu-id="9b354-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="9b354-120">[Http.sys](xref:fundamentals/servers/httpsys) では、匿名の要求キューの作成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9b354-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="9b354-121">ASP.NET Core 3.1 では、既存の名前付き HTTP.sys 要求キューに作成またはアタッチする機能が追加されています。</span><span class="sxs-lookup"><span data-stu-id="9b354-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="9b354-122">既存の名前付き HTTP.sys 要求キューに作成またはアタッチすると、キューを所有する HTTP.sys コントローラー プロセスがリスナー プロセスから独立しているシナリオが可能になります。</span><span class="sxs-lookup"><span data-stu-id="9b354-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="9b354-123">このような独立によって、リスナー プロセスの再起動の間に既存の接続とエンキューされた要求を保持することができます。</span><span class="sxs-lookup"><span data-stu-id="9b354-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="9b354-124">SameSite Cookie の重大な変更</span><span class="sxs-lookup"><span data-stu-id="9b354-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="9b354-125">SameSite Cookie の動作が、今後のブラウザーの変更を反映するように変更されました。</span><span class="sxs-lookup"><span data-stu-id="9b354-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="9b354-126">これは、AzureAd、OpenIdConnect、WsFederation などの認証シナリオに影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9b354-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="9b354-127">詳細については、「<xref:security/samesite>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b354-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a><span data-ttu-id="9b354-128">Blazor アプリのイベントに対する既定のアクションを回避する</span><span class="sxs-lookup"><span data-stu-id="9b354-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="9b354-129">イベントに対する既定のアクションを回避するには、`@on{EVENT}:preventDefault` ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="9b354-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="9b354-130">次の例では、テキスト ボックスにキーの文字を表示する既定のアクションが回避されます。</span><span class="sxs-lookup"><span data-stu-id="9b354-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="9b354-131">詳細については、「[既定のアクションを禁止する](xref:blazor/event-handling#prevent-default-actions)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b354-131">For more information, see [Prevent default actions](xref:blazor/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a><span data-ttu-id="9b354-132">Blazor アプリでのイベント伝達を停止する</span><span class="sxs-lookup"><span data-stu-id="9b354-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="9b354-133">イベント伝達を停止するには、`@on{EVENT}:stopPropagation` ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="9b354-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="9b354-134">次の例では、チェック ボックスをオンにすると、子 `<div>` からのクリック イベントが親 `<div>` に伝達されなくなります。</span><span class="sxs-lookup"><span data-stu-id="9b354-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

<span data-ttu-id="9b354-135">詳細については、「[イベントの伝達の停止](xref:blazor/event-handling#stop-event-propagation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b354-135">For more information, see [Stop event propagation](xref:blazor/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-opno-locblazor-app-development"></a><span data-ttu-id="9b354-136">Blazor アプリの開発中の詳細なエラー</span><span class="sxs-lookup"><span data-stu-id="9b354-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="9b354-137">開発中に Blazor アプリが正常に機能していない場合、アプリからの詳細なエラー情報を受け取ることで、問題のトラブルシューティングと修正に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9b354-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="9b354-138">エラーが発生すると Blazor アプリによって画面の下部に金色のバーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9b354-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="9b354-139">開発中は、金色のバーによってブラウザー コンソールが表示され、そこで例外を確認できます。</span><span class="sxs-lookup"><span data-stu-id="9b354-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="9b354-140">実稼働環境では、金色のバーによって、エラーが発生したことがユーザーに通知され、ブラウザーの更新が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="9b354-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="9b354-141">詳細については、「[開発中の詳細なエラー](xref:blazor/handle-errors#detailed-errors-during-development)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b354-141">For more information, see [Detailed errors during development](xref:blazor/handle-errors#detailed-errors-during-development).</span></span>
