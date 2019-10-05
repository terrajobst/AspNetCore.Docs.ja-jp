---
title: ASP.NET Core 3.0 の新機能
author: rick-anderson
description: ASP.NET Core 3.0 の新機能について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: aspnetcore-3.0
ms.openlocfilehash: ec3de5b35883752b7b3dbefceccec55da3986f39
ms.sourcegitcommit: dc96d76f6b231de59586fcbb989a7fb5106d26a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703675"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="628a9-103">ASP.NET Core 3.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="628a9-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="628a9-104">この記事では、ASP.NET Core 3.0 の最も大きな変更点について説明します。また、その変更点のドキュメントへのリンクも示します。</span><span class="sxs-lookup"><span data-stu-id="628a9-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## <a name="blazor"></a><span data-ttu-id="628a9-105">Blazor</span><span class="sxs-lookup"><span data-stu-id="628a9-105">Blazor</span></span>

<span data-ttu-id="628a9-106">Blazor は、.NET を使って対話型のクライアント側 Web UI を構築するための ASP.NET Core の新しいフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="628a9-106">Blazor is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="628a9-107">JavaScript の代わりに C# を使って、優れた対話型 UI を作成します。</span><span class="sxs-lookup"><span data-stu-id="628a9-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="628a9-108">.NET で記述された、サーバー側とクライアント側のアプリのロジックを共有します。</span><span class="sxs-lookup"><span data-stu-id="628a9-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="628a9-109">モバイル ブラウザーを含めた広範なブラウザーのサポートのために、HTML および CSS として UI をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="628a9-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="628a9-110">Blazor フレームワークでサポートされるシナリオ:</span><span class="sxs-lookup"><span data-stu-id="628a9-110">Blazor framework supported scenarios:</span></span>

* <span data-ttu-id="628a9-111">再利用可能な UI コンポーネント (Razor コンポーネント)</span><span class="sxs-lookup"><span data-stu-id="628a9-111">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="628a9-112">クライアント側のルーティング</span><span class="sxs-lookup"><span data-stu-id="628a9-112">Client-side routing</span></span>
* <span data-ttu-id="628a9-113">コンポーネントのレイアウト</span><span class="sxs-lookup"><span data-stu-id="628a9-113">Component layouts</span></span>
* <span data-ttu-id="628a9-114">依存関係の挿入のサポート</span><span class="sxs-lookup"><span data-stu-id="628a9-114">Support for dependency injection</span></span>
* <span data-ttu-id="628a9-115">フォームと検証</span><span class="sxs-lookup"><span data-stu-id="628a9-115">Forms and validation</span></span>
* <span data-ttu-id="628a9-116">Razor クラス ライブラリを使用したコンポーネント ライブラリの構築</span><span class="sxs-lookup"><span data-stu-id="628a9-116">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="628a9-117">JavaScript 相互運用</span><span class="sxs-lookup"><span data-stu-id="628a9-117">JavaScript interop</span></span>

<span data-ttu-id="628a9-118">詳細については、<xref:blazor/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-118">For more information, see <xref:blazor/index>.</span></span>

### <a name="blazor-server"></a><span data-ttu-id="628a9-119">Blazor サーバー</span><span class="sxs-lookup"><span data-stu-id="628a9-119">Blazor Server</span></span>

<span data-ttu-id="628a9-120">Blazor では、UI の更新プログラムを適用する方法からコンポーネントのレンダリング ロジックが分離されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-120">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="628a9-121">Blazor サーバーでは、ASP.NET Core アプリでサーバー上の Razor コンポーネントをホストするためのサポートが提供されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-121">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="628a9-122">UI の更新は SignalR 接続を介して処理されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-122">UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="628a9-123">Blazor サーバーは ASP.NET Core 3.0 でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="628a9-123">Blazor Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="blazor-webassembly-preview"></a><span data-ttu-id="628a9-124">Blazor WebAssembly (プレビュー)</span><span class="sxs-lookup"><span data-stu-id="628a9-124">Blazor WebAssembly (Preview)</span></span>

<span data-ttu-id="628a9-125">Blazor アプリは、WebAssembly ベースの .NET ランタイムを使用してブラウザーで直接実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="628a9-125">Blazor apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> <span data-ttu-id="628a9-126">Blazor WebAssembly はプレビュー段階であり、ASP.NET Core 3.0 ではサポートされて "*いません*"。</span><span class="sxs-lookup"><span data-stu-id="628a9-126">Blazor WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> <span data-ttu-id="628a9-127">Blazor WebAssembly は、ASP.NET Core の今後のリリースでサポートされる予定です。</span><span class="sxs-lookup"><span data-stu-id="628a9-127">Blazor WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="628a9-128">Razor のコンポーネント</span><span class="sxs-lookup"><span data-stu-id="628a9-128">Razor components</span></span>

<span data-ttu-id="628a9-129">Blazor アプリはコンポーネントから構築されています。</span><span class="sxs-lookup"><span data-stu-id="628a9-129">Blazor apps are built from components.</span></span> <span data-ttu-id="628a9-130">コンポーネントは、ページ、ダイアログ、フォームなどのユーザー インターフェイス (UI) の自己完結型チャンクです。</span><span class="sxs-lookup"><span data-stu-id="628a9-130">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="628a9-131">コンポーネントは、UI レンダリング ロジックとクライアント側のイベント ハンドラーを定義する通常の .NET クラスです。</span><span class="sxs-lookup"><span data-stu-id="628a9-131">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="628a9-132">JavaScript を使用せずに、機能豊富な対話型 Web アプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-132">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="628a9-133">通常、Blazor のコンポーネントは、HTML と C# が自然に融合している Razor 構文を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-133">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="628a9-134">Razor コンポーネントは、両方とも Razor を使用するという点で Razor Pages および MVC ビューに似ています。</span><span class="sxs-lookup"><span data-stu-id="628a9-134">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="628a9-135">要求 - 応答モデルに基づくページやビューとは異なり、コンポーネントは特に UI コンポジションを処理するために使われます。</span><span class="sxs-lookup"><span data-stu-id="628a9-135">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="628a9-136">gRPC</span><span class="sxs-lookup"><span data-stu-id="628a9-136">gRPC</span></span>

<span data-ttu-id="628a9-137">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="628a9-137">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="628a9-138">広く普及している高パフォーマンス RPC (リモート プロシージャ コール) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="628a9-138">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="628a9-139">API 開発に対して独特のコントラクト優先のアプローチを提供します。</span><span class="sxs-lookup"><span data-stu-id="628a9-139">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="628a9-140">次のような最新テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="628a9-140">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="628a9-141">HTTP/2 (転送用)。</span><span class="sxs-lookup"><span data-stu-id="628a9-141">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="628a9-142">プロトコル バッファー (インターフェイス記述言語として)。</span><span class="sxs-lookup"><span data-stu-id="628a9-142">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="628a9-143">バイナリ シリアル化形式。</span><span class="sxs-lookup"><span data-stu-id="628a9-143">Binary serialization format.</span></span>
* <span data-ttu-id="628a9-144">次のような機能があります。</span><span class="sxs-lookup"><span data-stu-id="628a9-144">Provides features such as:</span></span>

  * <span data-ttu-id="628a9-145">認証</span><span class="sxs-lookup"><span data-stu-id="628a9-145">Authentication</span></span>
  * <span data-ttu-id="628a9-146">双方向のストリーミングおよびフロー制御。</span><span class="sxs-lookup"><span data-stu-id="628a9-146">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="628a9-147">キャンセルおよびタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="628a9-147">Cancellation and timeouts.</span></span>

<span data-ttu-id="628a9-148">ASP.NET Core 3.0 の gRPC 機能には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="628a9-148">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="628a9-149">[Grpc AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash;grpc サービスをホストするための ASP.NET Core フレームワーク。</span><span class="sxs-lookup"><span data-stu-id="628a9-149">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="628a9-150">ASP.NET Core 上の gRPC は、ログ記録、依存関係の挿入 (DI)、認証、承認など、標準の ASP.NET Core 機能と完全に統合されています。</span><span class="sxs-lookup"><span data-stu-id="628a9-150">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication and authorization.</span></span>
* <span data-ttu-id="628a9-151">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; 使い慣れた `HttpClient` に基づいて構築される .NET Core 用の gRPC クライアント。</span><span class="sxs-lookup"><span data-stu-id="628a9-151">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="628a9-152">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; `HttpClientFactory` と gRPC クライアントの統合。</span><span class="sxs-lookup"><span data-stu-id="628a9-152">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="628a9-153">詳細については、<xref:grpc/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-153">For more information, see <xref:grpc/index>.</span></span>

## <a name="signalr"></a><span data-ttu-id="628a9-154">SignalR</span><span class="sxs-lookup"><span data-stu-id="628a9-154">SignalR</span></span>

<span data-ttu-id="628a9-155">移行の手順については、「[SignalR コードの更新](xref:migration/22-to-30#signalr)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-155">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> <span data-ttu-id="628a9-156">現在、SignalR は `System.Text.Json` を使用して JSON メッセージのシリアル化および逆シリアル化を行います。</span><span class="sxs-lookup"><span data-stu-id="628a9-156">SignalR now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="628a9-157">`Newtonsoft.Json` ベースのシリアライザーを復元する手順については、「[Newtonsoft.Json に切り替える](xref:migration/22-to-30#switch-to-newtonsoftjson)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-157">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="628a9-158">SignalR 対応の JavaScript および .NET クライアント には、自動再接続のサポートが追加されました。</span><span class="sxs-lookup"><span data-stu-id="628a9-158">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="628a9-159">既定では、クライアントはすぐに再接続を試行し、必要に応じて 2 秒後、10 秒後、30 秒後に再試行します。</span><span class="sxs-lookup"><span data-stu-id="628a9-159">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="628a9-160">クライアントが正常に再接続すると、新しい接続 ID を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="628a9-160">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="628a9-161">自動再接続はオプトインです。</span><span class="sxs-lookup"><span data-stu-id="628a9-161">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="628a9-162">再接続の間隔は、ミリ秒単位の間隔を配列で渡すことによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-162">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="628a9-163">カスタム実装を渡すと、再接続間隔を完全に制御することができます。</span><span class="sxs-lookup"><span data-stu-id="628a9-163">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="628a9-164">最後の再接続間隔の後で再接続に失敗した場合:</span><span class="sxs-lookup"><span data-stu-id="628a9-164">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="628a9-165">クライアントは接続がオフラインであると見なします。</span><span class="sxs-lookup"><span data-stu-id="628a9-165">The client considers the connection is offline.</span></span>
* <span data-ttu-id="628a9-166">クライアントは再接続の試行を停止します。</span><span class="sxs-lookup"><span data-stu-id="628a9-166">The client stops trying to reconnect.</span></span>

<span data-ttu-id="628a9-167">再接続の試行中に、再接続が試行されていることをユーザーに通知するようにアプリ UI を更新します。</span><span class="sxs-lookup"><span data-stu-id="628a9-167">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="628a9-168">接続が中断されたときに UI にフィードバックを提供するため、次のイベント ハンドラーを含むように SignalR クライアント API が拡張されました。</span><span class="sxs-lookup"><span data-stu-id="628a9-168">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="628a9-169">`onreconnecting`:これによって、開発者が UI を無効にしたり、アプリがオフラインであることをユーザーに知らせたりすることができます。</span><span class="sxs-lookup"><span data-stu-id="628a9-169">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="628a9-170">`onreconnected`:これによって、接続が再確立したときに開発者が UI を更新できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-170">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="628a9-171">次のコードでは `onreconnecting` を使用して、接続の試行中に UI を更新します。</span><span class="sxs-lookup"><span data-stu-id="628a9-171">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="628a9-172">次のコードでは `onreconnected` を使用して、接続時に UI を更新します。</span><span class="sxs-lookup"><span data-stu-id="628a9-172">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="628a9-173">SignalR 3.0 以降では、ハブ メソッドが承認を必要とする場合に、承認ハンドラーにカスタム リソースが提供されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-173">SignalR 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="628a9-174">リソースは `HubInvocationContext` のインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="628a9-174">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="628a9-175">`HubInvocationContext` には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="628a9-175">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="628a9-176">呼び出されているハブ メソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="628a9-176">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="628a9-177">ハブ メソッドに対する引数。</span><span class="sxs-lookup"><span data-stu-id="628a9-177">Arguments to the hub method.</span></span>

<span data-ttu-id="628a9-178">複数の組織が Azure Active Directory を使用してサインインできるチャット ルーム アプリの例について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="628a9-178">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="628a9-179">Microsoft アカウントを持つユーザーは誰でもサインインしてチャットできますが、ユーザーを利用禁止にしたりユーザーのチャット履歴を表示したりできるのは所有している組織のメンバーのみです。</span><span class="sxs-lookup"><span data-stu-id="628a9-179">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users’ chat histories.</span></span> <span data-ttu-id="628a9-180">アプリを使用すると、特定のユーザーに対して特定の機能を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="628a9-180">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="628a9-181">上記のコードでは、`DomainRestrictedRequirement` がカスタムの `IAuthorizationRequirement` として機能します。</span><span class="sxs-lookup"><span data-stu-id="628a9-181">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="628a9-182">`HubInvocationContext` リソース パラメーターが渡されているため、内部ロジックで以下を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="628a9-182">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="628a9-183">ハブが呼び出されているコンテキストを調べます。</span><span class="sxs-lookup"><span data-stu-id="628a9-183">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="628a9-184">個々のハブ メソッドの実行をユーザーに許可するかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="628a9-184">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="628a9-185">個々のハブ メソッドは、コードが実行時にチェックするポリシーの名前で修飾できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-185">Individual Hub methods can be decorated with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="628a9-186">クライアントが個々のハブ メソッドを呼び出そうとすると、`DomainRestrictedRequirement` ハンドラーが実行され、メソッドへのアクセスを制御します。</span><span class="sxs-lookup"><span data-stu-id="628a9-186">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="628a9-187">この方法に基づいて、`DomainRestrictedRequirement` が次のようにアクセスを制御します。</span><span class="sxs-lookup"><span data-stu-id="628a9-187">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="628a9-188">ログインしているすべてのユーザーが `SendMessage` メソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="628a9-188">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="628a9-189">`@jabbr.net` 電子メール アドレスを使用してログインしたユーザーのみが、ユーザーの履歴を表示できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-189">Only users who have logged in with a `@jabbr.net` email address can view users’ histories.</span></span>
* <span data-ttu-id="628a9-190">`bob42@jabbr.net` のみが、ユーザーをチャット ルーム利用禁止にすることができます。</span><span class="sxs-lookup"><span data-stu-id="628a9-190">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

<span data-ttu-id="628a9-191">`DomainRestricted` ポリシーの作成には以下が含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="628a9-191">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="628a9-192">*Startup.cs* に新しいポリシーを追加します。</span><span class="sxs-lookup"><span data-stu-id="628a9-192">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="628a9-193">カスタムの `DomainRestrictedRequirement` 要件をパラメーターとして指定します。</span><span class="sxs-lookup"><span data-stu-id="628a9-193">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="628a9-194">承認ミドルウェアを使用して `DomainRestricted` を登録します。</span><span class="sxs-lookup"><span data-stu-id="628a9-194">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

<span data-ttu-id="628a9-195">SignalR ハブは[エンドポイント ルーティング](xref:fundamentals/routing)を使用します。</span><span class="sxs-lookup"><span data-stu-id="628a9-195">SignalR hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="628a9-196">SignalR ハブ接続は以前は明示的に実行されました。</span><span class="sxs-lookup"><span data-stu-id="628a9-196">SignalR hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="628a9-197">以前のバージョンでは、開発者はさまざまな場所でコントローラー、Razor ページ、およびハブを接続する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="628a9-197">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of different places.</span></span> <span data-ttu-id="628a9-198">明示的な接続によって、ほぼ同一のルーティング セグメントがいくつも生成されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-198">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

<span data-ttu-id="628a9-199">SignalR 3.0 ハブは、エンドポイント ルーティングを介してルーティングできます。</span><span class="sxs-lookup"><span data-stu-id="628a9-199">SignalR 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="628a9-200">エンドポイント ルーティングでは、通常、すべてのルーティングを `UseRouting` に構成できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-200">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="628a9-201">ASP.NET Core 3.0 SignalR によって以下が追加されました。</span><span class="sxs-lookup"><span data-stu-id="628a9-201">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="628a9-202">クライアントとサーバー間のストリーミング。</span><span class="sxs-lookup"><span data-stu-id="628a9-202">Client-to-server streaming.</span></span> <span data-ttu-id="628a9-203">クライアントとサーバー間のストリーミングでは、サーバー側メソッドが `IAsyncEnumerable<T>` または `ChannelReader<T>` のインスタンスを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="628a9-203">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="628a9-204">次 C# の例では、ハブの `UploadStream` メソッドがクライアントから文字列のストリームを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="628a9-204">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="628a9-205">.NET クライアント アプリは、`IAsyncEnumerable<T>` または `ChannelReader<T>` インスタンスを、上記の `UploadStream` Hub メソッドの `stream` 引数として渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="628a9-205">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="628a9-206">`for` ループが完了し、ローカル関数が終了した後で、ストリームの完了が送信されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-206">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="628a9-207">JavaScript クライアント アプリは、上記の `UploadStream` ハブの `stream`引数のために SignalR `Subject` (または [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="628a9-207">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="628a9-208">JavaScript コードで `subject.next` メソッドを使用すると、文字列がキャプチャされてからサーバーへの送信準備が整うように文字列を処理できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-208">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="628a9-209">直前の 2 つのスニペットのようなコードを使用して、リアルタイム ストリーミング エクスペリエンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-209">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

### <a name="new-json-serialization"></a><span data-ttu-id="628a9-210">新しい JSON シリアル化</span><span class="sxs-lookup"><span data-stu-id="628a9-210">New JSON serialization</span></span>

<span data-ttu-id="628a9-211">現在、ASP.NET Core 3.0 では、JSON シリアル化のために既定で <xref:System.Text.Json> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-211">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="628a9-212">非同期で JSON の読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="628a9-212">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="628a9-213">UTF-8 テキスト用に最適化されています。</span><span class="sxs-lookup"><span data-stu-id="628a9-213">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="628a9-214">通常、`Newtonsoft.Json` よりパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="628a9-214">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="628a9-215">Json.NET を ASP.NET Core 3.0 に追加するには、「[Newtonsoft.Json ベースの JSON 形式のサポートを追加する](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-215">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="628a9-216">新しい Razor ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="628a9-216">New Razor directives</span></span>

<span data-ttu-id="628a9-217">次の一覧には、新しい Razor ディレクティブが含まれます。</span><span class="sxs-lookup"><span data-stu-id="628a9-217">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="628a9-218">[@attribute](xref:mvc/views/razor#attribute) &ndash; `@attribute` ディレクティブでは、指定された属性が生成されたページまたはビューのクラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-218">[@attribute](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="628a9-219">たとえば、`@attribute [Authorize]` のようにします。</span><span class="sxs-lookup"><span data-stu-id="628a9-219">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="628a9-220">[@implements](xref:mvc/views/razor#implements) &ndash; `@implements` ディレクティブでは、生成されたクラスのインターフェイスが実装されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-220">[@implements](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="628a9-221">たとえば、`@implements IDisposable` のようにします。</span><span class="sxs-lookup"><span data-stu-id="628a9-221">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="628a9-222">IdentityServer4 では、Web API と SPA の認証と承認がサポートされています</span><span class="sxs-lookup"><span data-stu-id="628a9-222">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="628a9-223">[IdentityServer4](https://identityserver.io) は、ASP.NET Core 3.0 用の OpenID Connect および OAuth 2.0 フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="628a9-223">[IdentityServer4](https://identityserver.io) is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="628a9-224">IdentityServer4 により、次のセキュリティ機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="628a9-224">IdentityServer4 enables the following security features:</span></span>

* <span data-ttu-id="628a9-225">サービスとしての認証 (AaaS)</span><span class="sxs-lookup"><span data-stu-id="628a9-225">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="628a9-226">複数のアプリケーションの種類でのシングル サインオン/オフ (SSO)</span><span class="sxs-lookup"><span data-stu-id="628a9-226">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="628a9-227">API のアクセス制御</span><span class="sxs-lookup"><span data-stu-id="628a9-227">Access control for APIs</span></span>
* <span data-ttu-id="628a9-228">Federation Gateway</span><span class="sxs-lookup"><span data-stu-id="628a9-228">Federation Gateway</span></span>

<span data-ttu-id="628a9-229">詳細については、「[ようこそ! IdentityServer4](http://docs.identityserver.io/en/latest/index.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-229">For more information, see [Welcome to IdentityServer4](http://docs.identityserver.io/en/latest/index.html).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="628a9-230">証明書および Kerberos 認証</span><span class="sxs-lookup"><span data-stu-id="628a9-230">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="628a9-231">証明書認証には以下が必要です。</span><span class="sxs-lookup"><span data-stu-id="628a9-231">Certificate authentication requires:</span></span>

* <span data-ttu-id="628a9-232">証明書を受け入れるようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="628a9-232">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="628a9-233">`Startup.Configure` に認証ミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="628a9-233">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="628a9-234">`Startup.ConfigureServices` に証明書認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="628a9-234">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="628a9-235">証明書認証のオプションには次の機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="628a9-235">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="628a9-236">自己署名された証明書を受け入れる。</span><span class="sxs-lookup"><span data-stu-id="628a9-236">Accept self-signed certificates.</span></span>
* <span data-ttu-id="628a9-237">証明書の失効を確認する。</span><span class="sxs-lookup"><span data-stu-id="628a9-237">Check for certificate revocation.</span></span>
* <span data-ttu-id="628a9-238">提供された証明書に適切な使用フラグが含まれていることを確認する。</span><span class="sxs-lookup"><span data-stu-id="628a9-238">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="628a9-239">既定のユーザー プリンシパルは、証明書のプロパティで構成されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-239">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="628a9-240">ユーザー プリンシパルには、プリンシパルの補完または置換を可能にするイベントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="628a9-240">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="628a9-241">詳細については、<xref:security/authentication/certauth> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-241">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="628a9-242">[Windows 認証](/windows-server/security/windows-authentication/windows-authentication-overview)は、Linux および macOS に拡張されています。</span><span class="sxs-lookup"><span data-stu-id="628a9-242">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="628a9-243">以前のバージョンでは、Windows 認証は [IIS](xref:host-and-deploy/iis/index) と [HttpSys](xref:fundamentals/servers/httpsys) に限定されていました。</span><span class="sxs-lookup"><span data-stu-id="628a9-243">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="628a9-244">ASP.NET Core 3.0 では、[Kestrel](xref:fundamentals/servers/kestrel) は、Windows、Linux、および macOS 上で、Windows ドメインに参加しているホストに対して Negotiate、[Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)、および [NTLM](/windows-server/security/kerberos/ntlm-overview) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="628a9-245">これらの認証スキームの Kestrel サポートは、[Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-245">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="628a9-246">他の認証サービスと同様に、認証アプリ全体を構成してからサービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="628a9-246">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="628a9-247">ホストの要件:</span><span class="sxs-lookup"><span data-stu-id="628a9-247">Host requirements:</span></span>

* <span data-ttu-id="628a9-248">Windows ホストでは、アプリをホストするユーザー アカウントに[サービス プリンシパル名](/windows/win32/ad/service-principal-names) (SPN) を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="628a9-248">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="628a9-249">Linux および macOS マシンは、ドメインに参加している必要があります。</span><span class="sxs-lookup"><span data-stu-id="628a9-249">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="628a9-250">Web プロセス用に SPN を作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="628a9-250">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="628a9-251">[キータブ ファイル](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/)をホスト マシン上で生成して構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="628a9-251">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="628a9-252">詳細については、<xref:security/authentication/windowsauth> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-252">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="628a9-253">テンプレートの変更</span><span class="sxs-lookup"><span data-stu-id="628a9-253">Template changes</span></span>

<span data-ttu-id="628a9-254">Web UI テンプレート (Razor Pages、コントローラーとビューを含む MVC) では、以下が削除されています。</span><span class="sxs-lookup"><span data-stu-id="628a9-254">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="628a9-255">Cookie 同意 UI は含まれなくなりました。</span><span class="sxs-lookup"><span data-stu-id="628a9-255">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="628a9-256">ASP.NET Core 3.0 テンプレートで生成されるアプリで Cookie 同意機能を有効にするには、<xref:security/gdpr> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-256">To enable the cookie consent feature in an ASP.NET Core 3.0 template generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="628a9-257">スクリプトと関連する静的アセットは、CDN を使用する代わりに、ローカル ファイルとして参照されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="628a9-257">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="628a9-258">詳細については、「[現在、スクリプトと関連する静的アセットは、現在の環境に基づいた CDN を使用する代わりに、ローカル ファイルとして参照される (aspnet/AspNetCore.Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-258">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="628a9-259">Angular テンプレートは、Angular 8 を使用するように更新されました。</span><span class="sxs-lookup"><span data-stu-id="628a9-259">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="628a9-260">Razor クラス ライブラリ (RCL) テンプレートは Razor コンポーネント開発での既定です。</span><span class="sxs-lookup"><span data-stu-id="628a9-260">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="628a9-261">Visual Studio の新しいテンプレート オプションによって、ページとビューのテンプレート サポートが提供されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-261">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="628a9-262">コマンド シェルでテンプレートから RCL を作成するときは、`-support-pages-and-views` オプション (`dotnet new razorclasslib -support-pages-and-views`) を渡します。</span><span class="sxs-lookup"><span data-stu-id="628a9-262">When creating an RCL from the template in a command shell, pass the `-support-pages-and-views` option (`dotnet new razorclasslib -support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="628a9-263">汎用ホスト</span><span class="sxs-lookup"><span data-stu-id="628a9-263">Generic Host</span></span>

<span data-ttu-id="628a9-264">ASP.NET Core 3.0 テンプレートでは <xref:fundamentals/host/generic-host> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-264">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="628a9-265">以前のバージョンでは <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="628a9-265">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="628a9-266">.NET Core 汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) を使用すると、ASP.NET Core アプリと、Web 固有ではない他のサーバー シナリオの統合が強化されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-266">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that are not web specific.</span></span> <span data-ttu-id="628a9-267">詳細については、「[HostBuilder による WebHostBuilder の置き換え](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-267">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="628a9-268">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="628a9-268">Host configuration</span></span>

<span data-ttu-id="628a9-269">ASP.NET Core 3.0 リリースよりも前には、`ASPNETCORE_` というプレフィックスが付いた環境変数が Web ホストのホスト構成用に読み込まれました。</span><span class="sxs-lookup"><span data-stu-id="628a9-269">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="628a9-270">3\.0 では、`AddEnvironmentVariables` が使用され、`DOTNET_` というプレフィックスが付いた環境変数が `CreateDefaultBuilder` でのホスト構成用に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="628a9-270">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-contructor-injection"></a><span data-ttu-id="628a9-271">Startup コンストラクター挿入の変更</span><span class="sxs-lookup"><span data-stu-id="628a9-271">Changes to Startup contructor injection</span></span>

<span data-ttu-id="628a9-272">汎用ホストでは、`Startup` コンストラクター挿入で次の型しかサポートされません。</span><span class="sxs-lookup"><span data-stu-id="628a9-272">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="628a9-273">すべてのサービスを `Startup.Configure` メソッドへの引数として直接挿入することは引き続きできます。</span><span class="sxs-lookup"><span data-stu-id="628a9-273">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="628a9-274">詳細については、「[汎用ホストによる Startup コンストラクター挿入の制限 (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-274">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="628a9-275">Kestrel</span><span class="sxs-lookup"><span data-stu-id="628a9-275">Kestrel</span></span>

* <span data-ttu-id="628a9-276">Kestrel 構成は、汎用ホストへの移行に対応するように更新されました。</span><span class="sxs-lookup"><span data-stu-id="628a9-276">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="628a9-277">3\.0 では、Kestrel は `ConfigureWebHostDefaults` によって提供される Web ホスト ビルダー上で構成されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-277">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="628a9-278">接続アダプターは Kestrel から削除され、接続ミドルウェアで置き換えられました。これは ASP.NET Core パイプラインの HTTP ミドルウェアに似ていますが下位レベルの接続用です。</span><span class="sxs-lookup"><span data-stu-id="628a9-278">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="628a9-279">Kestrel トランスポート層は、`Connections.Abstractions` のパブリック インターフェイスとして公開されています。</span><span class="sxs-lookup"><span data-stu-id="628a9-279">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="628a9-280">ヘッダーとトレーラーのあいまいさは、末尾のヘッダーを新しいコレクションに移動することによって解決されました。</span><span class="sxs-lookup"><span data-stu-id="628a9-280">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="628a9-281">同期 IO の API (`HttpRequest.Body.Read` など) は、アプリのクラッシュにつながるスレッド スタベーションの一般的な原因です。</span><span class="sxs-lookup"><span data-stu-id="628a9-281">Synchronous IO APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="628a9-282">3\.0 では、`AllowSynchronousIO` は既定で無効になっています。</span><span class="sxs-lookup"><span data-stu-id="628a9-282">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="628a9-283">詳細については、<xref:migration/22-to-30#kestrel> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-283">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="628a9-284">既定で有効な HTTP/2</span><span class="sxs-lookup"><span data-stu-id="628a9-284">HTTP/2 enabled by default</span></span>

<span data-ttu-id="628a9-285">HTTP/2 は、Kestrel では HTTPS エンドポイントに対して既定で有効です。</span><span class="sxs-lookup"><span data-stu-id="628a9-285">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="628a9-286">IIS または HTTP.sys での HTTP/2 サポートは、オペレーティング システムでサポートされる場合に有効です。</span><span class="sxs-lookup"><span data-stu-id="628a9-286">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="628a9-287">要求時の EventCounter</span><span class="sxs-lookup"><span data-stu-id="628a9-287">EventCounters on request</span></span>

<span data-ttu-id="628a9-288">ホスティング EventSource `Microsoft.AspNetCore.Hosting` は、受信要求に関連する次の新しい種類の <xref:System.Diagnostics.Tracing.EventCounter> を出力します。</span><span class="sxs-lookup"><span data-stu-id="628a9-288">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="628a9-289">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="628a9-289">Endpoint routing</span></span>

<span data-ttu-id="628a9-290">エンドポイント ルーティングによってフレームワーク (MVC など) がミドルウェアとうまく連携できるようになりますが、これが次のように強化されています。</span><span class="sxs-lookup"><span data-stu-id="628a9-290">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="628a9-291">ミドルウェアとエンドポイントの順序を `Startup.Configure` の要求処理パイプラインで構成できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-291">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="628a9-292">エンドポイントとミドルウェアは、正常性チェックなどの他の ASP.NET Core ベースのテクノロジに対して適切に構成できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-292">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="628a9-293">エンドポイントは、ミドルウェアと MVC の両方に、CORS や承認などのポリシーを実装できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-293">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="628a9-294">フィルターおよび属性をコントローラーのメソッドに設定できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-294">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="628a9-295">詳細については、<xref:fundamentals/routing#routing-basics> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-295">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="628a9-296">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="628a9-296">Health Checks</span></span>

<span data-ttu-id="628a9-297">正常性チェックでは、汎用ホストとのエンドポイント ルーティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="628a9-297">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="628a9-298">`Startup.Configure` で、エンドポイントの URL または相対パスを使用して、エンドポイント ビルダーで `MapHealthChecks` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="628a9-298">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="628a9-299">正常性チェックのエンドポイントは以下を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="628a9-299">Health Checks endpoints can:</span></span>

* <span data-ttu-id="628a9-300">1 つまたは複数の許可されたホスト/ポートを指定する。</span><span class="sxs-lookup"><span data-stu-id="628a9-300">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="628a9-301">承認を必要とする。</span><span class="sxs-lookup"><span data-stu-id="628a9-301">Require authorization.</span></span>
* <span data-ttu-id="628a9-302">CORS を必要とする。</span><span class="sxs-lookup"><span data-stu-id="628a9-302">Require CORS.</span></span>

<span data-ttu-id="628a9-303">詳細については、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-303">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="628a9-304">HttpContext のパイプ</span><span class="sxs-lookup"><span data-stu-id="628a9-304">Pipes on HttpContext</span></span>

<span data-ttu-id="628a9-305">現在、<xref:System.IO.Pipelines> API を使用して、要求本文の読み取りと応答本文の書き込みを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="628a9-305">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="628a9-306">次に、</span><span class="sxs-lookup"><span data-stu-id="628a9-306">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="628a9-307">`HttpRequest.BodyReader` プロパティは、要求本文を読み取るために使用できる <xref:System.IO.Pipelines.PipeReader> を提供します。</span><span class="sxs-lookup"><span data-stu-id="628a9-307">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="628a9-308">次に、</span><span class="sxs-lookup"><span data-stu-id="628a9-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="628a9-309">`HttpResponse.BodyWriter` プロパティは、応答本文を書き込むために使用できる <xref:System.IO.Pipelines.PipeWriter> を提供します。</span><span class="sxs-lookup"><span data-stu-id="628a9-309">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="628a9-310">`HttpRequest.BodyReader` は、`HttpRequest.Body` ストリームに類似しています。</span><span class="sxs-lookup"><span data-stu-id="628a9-310">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="628a9-311">`HttpResponse.BodyWriter` は `HttpResponse.Body` ストリームに類似しています。</span><span class="sxs-lookup"><span data-stu-id="628a9-311">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="628a9-312">IIS でのエラー報告の向上</span><span class="sxs-lookup"><span data-stu-id="628a9-312">Improved error reporting in IIS</span></span>

<span data-ttu-id="628a9-313">IIS で ASP.NET Core アプリをホストする際の起動エラーで生成される診断データが豊富になりました。</span><span class="sxs-lookup"><span data-stu-id="628a9-313">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="628a9-314">これらのエラーは、該当する場合は常にスタック トレースと共に Windows イベント ログに報告されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-314">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="628a9-315">さらに、すべての警告、エラー、および未処理の例外が、Windows イベント ログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-315">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="628a9-316">ワーカー サービスとワーカー SDK</span><span class="sxs-lookup"><span data-stu-id="628a9-316">Worker Service and Worker SDK</span></span>

<span data-ttu-id="628a9-317">.NET Core 3.0 では、新しいワーカー サービス アプリ テンプレートが導入されました。</span><span class="sxs-lookup"><span data-stu-id="628a9-317">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="628a9-318">このテンプレートは、.NET Core で長期サービスを作成する場合の出発点として利用できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-318">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="628a9-319">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="628a9-319">For more information, see:</span></span>

* [<span data-ttu-id="628a9-320">Windows サービスとしての .NET Core ワーカー</span><span class="sxs-lookup"><span data-stu-id="628a9-320">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="628a9-321">Forwarded Headers Middleware の機能強化</span><span class="sxs-lookup"><span data-stu-id="628a9-321">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="628a9-322">ASP.NET Core の以前のバージョンでは、Azure Linux にデプロイした場合、または IIS 以外のリバース プロキシの背後にデプロイした場合に、<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> と <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> の呼び出しで問題が発生していました。</span><span class="sxs-lookup"><span data-stu-id="628a9-322">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="628a9-323">以前のバージョンの修正方法は、「[Linux および非 IIS のリバース プロキシのスキームを転送する](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies)」に記載されてます。</span><span class="sxs-lookup"><span data-stu-id="628a9-323">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="628a9-324">このシナリオは ASP.NET Core 3.0 では修正されています。</span><span class="sxs-lookup"><span data-stu-id="628a9-324">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="628a9-325">ホストによって [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) が有効化されるのは、`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されているときです。</span><span class="sxs-lookup"><span data-stu-id="628a9-325">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="628a9-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` はコンテナー イメージで `true` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="628a9-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="628a9-327">パフォーマンスの向上</span><span class="sxs-lookup"><span data-stu-id="628a9-327">Performance improvements</span></span>

<span data-ttu-id="628a9-328">ASP.NET Core 3.0 には、メモリ使用量を減らしてスループットを向上させる多くの機能強化が含まれています。</span><span class="sxs-lookup"><span data-stu-id="628a9-328">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="628a9-329">スコープ サービスで組み込み依存関係挿入コンテナーを使用する場合のメモリ使用量の削減。</span><span class="sxs-lookup"><span data-stu-id="628a9-329">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="628a9-330">ミドルウェアのシナリオやルーティングを含む、フレームワーク全体での割り当ての削減。</span><span class="sxs-lookup"><span data-stu-id="628a9-330">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="628a9-331">WebSocket 接続のメモリ使用量の削減。</span><span class="sxs-lookup"><span data-stu-id="628a9-331">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="628a9-332">HTTPS 接続でのメモリの削減とスループットの向上。</span><span class="sxs-lookup"><span data-stu-id="628a9-332">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="628a9-333">最適化された完全非同期の新 JSON シリアライザー。</span><span class="sxs-lookup"><span data-stu-id="628a9-333">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="628a9-334">フォーム解析でのメモリ使用量の削減とスループットの向上。</span><span class="sxs-lookup"><span data-stu-id="628a9-334">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="628a9-335">.NET Core 3.0 のみで実行する ASP.NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="628a9-335">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="628a9-336">ASP.NET Core 3.0 以降、.NET Framework はサポートされるターゲット フレームワークではなくなりました。</span><span class="sxs-lookup"><span data-stu-id="628a9-336">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="628a9-337">.NET Framework をターゲットとするプロジェクトは、[.NET Core 2.1 LTS リリース](https://www.microsoft.com/net/download/dotnet-core/2.1)を使用して完全にサポートされた状態で続行できます。</span><span class="sxs-lookup"><span data-stu-id="628a9-337">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://www.microsoft.com/net/download/dotnet-core/2.1).</span></span> <span data-ttu-id="628a9-338">ほとんどの ASP.NET Core 2.1.x 関連パッケージは、.NET Core 2.1 の 3 年の LTS 期間が終わっても無期限にサポートされます。</span><span class="sxs-lookup"><span data-stu-id="628a9-338">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the 3 year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="628a9-339">移行の詳細については、「[.NET Framework から .NET Core にコードを移植する](/dotnet/core/porting/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-339">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="628a9-340">ASP.NET Core 共有フレームワークの使用</span><span class="sxs-lookup"><span data-stu-id="628a9-340">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="628a9-341">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれる ASP.NET Core 3.0 共有フレームワークでは、プロジェクト ファイル内の明示的な `<PackageReference />` 要素を必要としなくなりました。</span><span class="sxs-lookup"><span data-stu-id="628a9-341">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="628a9-342">プロジェクト ファイル内で `Microsoft.NET.Sdk.Web` SDK を使用すると、共有フレームワークが自動的に参照されます。</span><span class="sxs-lookup"><span data-stu-id="628a9-342">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="628a9-343">ASP.NET Core 共有フレームワークから削除されたアセンブリ</span><span class="sxs-lookup"><span data-stu-id="628a9-343">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="628a9-344">ASP.NET Core 3.0 共有フレームワークから削除された重要なアセンブリは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="628a9-344">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="628a9-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET)。</span><span class="sxs-lookup"><span data-stu-id="628a9-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="628a9-346">Json.NET を ASP.NET Core 3.0 に追加するには、「[Newtonsoft.Json ベースの JSON 形式のサポートを追加する](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-346">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="628a9-347">ASP.NET Core 3.0 では JSON の読み取りと書き込みのために `System.Text.Json` が導入されています。</span><span class="sxs-lookup"><span data-stu-id="628a9-347">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="628a9-348">詳細については、このドキュメントの「[新しい JSON シリアル化](#new-json-serialization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-348">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="628a9-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="628a9-349">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="628a9-350">共有フレームワークから削除されたすべてのアセンブリの一覧については、「[Microsoft.AspNetCore.App 3.0 から削除されるアセンブリ](https://github.com/aspnet/AspNetCore/issues/3755)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-350">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/aspnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="628a9-351">この変更の動機については、「[Microsoft.AspNetCore.App 3.0 に対する重大な変更](https://github.com/aspnet/Announcements/issues/325)」および「[ASP.NET Core 3.0 の変更点を初めて見て](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="628a9-351">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
