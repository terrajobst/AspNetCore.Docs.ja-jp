---
title: ASP.NET Core Blazor の概要
author: guardrex
description: ASP.NET Core アプリ内に .NET を使った対話型のクライアント側 Web UI を構築する方法である、ASP.NET Core Blazor について調べます。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 03/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/index
ms.openlocfilehash: 6d2e95cd2ec92f97a97cb558fb39e4540450c766
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405955"
---
# <a name="introduction-to-aspnet-core-opno-locblazor"></a><span data-ttu-id="75a65-103">ASP.NET Core Blazor の概要</span><span class="sxs-lookup"><span data-stu-id="75a65-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="75a65-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="75a65-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="75a65-105">*Blazor へようこそ。*</span><span class="sxs-lookup"><span data-stu-id="75a65-105">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="75a65-106"> は、.NET を使って対話型のクライアント側 Web UI を構築するためのフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="75a65-106"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="75a65-107">JavaScript の代わりに C# を使って、優れた対話型 UI を作成します。</span><span class="sxs-lookup"><span data-stu-id="75a65-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="75a65-108">.NET で記述された、サーバー側とクライアント側のアプリのロジックを共有します。</span><span class="sxs-lookup"><span data-stu-id="75a65-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="75a65-109">モバイル ブラウザーを含めた広範なブラウザーのサポートのために、HTML および CSS として UI をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="75a65-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="75a65-110">[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index) などの最新のホスティング プラットフォームと統合します。</span><span class="sxs-lookup"><span data-stu-id="75a65-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="75a65-111">クライアント側の Web 開発に .NET を使用すると、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="75a65-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="75a65-112">JavaScript ではなく C# でコードを記述します。</span><span class="sxs-lookup"><span data-stu-id="75a65-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="75a65-113">.NET ライブラリの既存の .NET エコシステムを活用します。</span><span class="sxs-lookup"><span data-stu-id="75a65-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="75a65-114">サーバーとクライアント全体でアプリ ロジックを共有します。</span><span class="sxs-lookup"><span data-stu-id="75a65-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="75a65-115">.NET のパフォーマンス、信頼性、およびセキュリティから利点が得られます。</span><span class="sxs-lookup"><span data-stu-id="75a65-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="75a65-116">Windows、Linux、macOS 上の Visual Studio を使って生産性を維持します。</span><span class="sxs-lookup"><span data-stu-id="75a65-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="75a65-117">多機能で使いやすい安定した言語、フレームワーク、およびツールの共通セットに基づいて構築します。</span><span class="sxs-lookup"><span data-stu-id="75a65-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="75a65-118">コンポーネント</span><span class="sxs-lookup"><span data-stu-id="75a65-118">Components</span></span>

Blazor<span data-ttu-id="75a65-119"> アプリは、"*コンポーネント*" に基づいています。</span><span class="sxs-lookup"><span data-stu-id="75a65-119"> apps are based on *components*.</span></span> <span data-ttu-id="75a65-120">Blazor のコンポーネントとは、ページ、ダイアログ、データ エントリ フォームなどの UI の要素です。</span><span class="sxs-lookup"><span data-stu-id="75a65-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="75a65-121">コンポーネントは、.NET アセンブリに組み込まれている次のような .NET クラスです。</span><span class="sxs-lookup"><span data-stu-id="75a65-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="75a65-122">柔軟な UI のレンダリング ロジックを定義する。</span><span class="sxs-lookup"><span data-stu-id="75a65-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="75a65-123">ユーザー イベントを処理する。</span><span class="sxs-lookup"><span data-stu-id="75a65-123">Handle user events.</span></span>
* <span data-ttu-id="75a65-124">入れ子にしたり再利用したりできる。</span><span class="sxs-lookup"><span data-stu-id="75a65-124">Can be nested and reused.</span></span>
* <span data-ttu-id="75a65-125">[Razor クラス ライブラリ](xref:razor-pages/ui-class)または [NuGet パッケージ](/nuget/what-is-nuget)として共有および配布できる。</span><span class="sxs-lookup"><span data-stu-id="75a65-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="75a65-126">コンポーネント クラスは通常、[Razor](xref:mvc/views/razor) マークアップ ページの形式で、 *.razor* ファイル拡張子で記述されます。</span><span class="sxs-lookup"><span data-stu-id="75a65-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="75a65-127">Blazor のコンポーネントは、正式には *Razor コンポーネント*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="75a65-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="75a65-128">Razor とは、開発者の生産性のために設計された、C# コードに HTML マークアップを結合するための構文です。</span><span class="sxs-lookup"><span data-stu-id="75a65-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="75a65-129">Razor を使用すると、[IntelliSense](/visualstudio/ide/using-intellisense) サポートを利用して、同一ファイル内で HTML マークアップと C# を切り替えることができます。</span><span class="sxs-lookup"><span data-stu-id="75a65-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="75a65-130">また、Razor Pages と MVC でも、Razor を使用します。</span><span class="sxs-lookup"><span data-stu-id="75a65-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="75a65-131">要求/応答モデルを中心に構築される Razor Pages や MVC とは異なり、コンポーネントは特にクライアント側の UI ロジックとコンポジションに対して使用されます。</span><span class="sxs-lookup"><span data-stu-id="75a65-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="75a65-132">次の Razor マークアップは、別のコンポーネント内で入れ子にできるコンポーネント (*Dialog.razor*) を示しています。</span><span class="sxs-lookup"><span data-stu-id="75a65-132">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

<span data-ttu-id="75a65-133">ダイアログの本文の内容 (`ChildContent`) とタイトル (`Title`) は、UI にこのコンポーネントを使用するコンポーネントによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="75a65-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="75a65-134">`OnYes` は、ボタンの `onclick` イベントによってトリガーされる C# メソッドです。</span><span class="sxs-lookup"><span data-stu-id="75a65-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="75a65-135"> では、UI コンポジションにとって自然な HTML タグを使用します。</span><span class="sxs-lookup"><span data-stu-id="75a65-135"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="75a65-136">HTML 要素によってコンポーネントを指定し、タグの属性によってコンポーネントのプロパティに値を渡します。</span><span class="sxs-lookup"><span data-stu-id="75a65-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="75a65-137">次の例では、`Index` コンポーネントで `Dialog` コンポーネントが使われています。</span><span class="sxs-lookup"><span data-stu-id="75a65-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="75a65-138">`ChildContent` と `Title` は、属性と `<Dialog>` 要素の内容によって設定されています。</span><span class="sxs-lookup"><span data-stu-id="75a65-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="75a65-139">*Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="75a65-139">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="75a65-140">ブラウザー内で親 (*Index.razor*) にアクセスすると、ダイアログがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="75a65-140">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![ブラウザーにレンダリングされた Dialog コンポーネント](index/_static/dialog.png)

<span data-ttu-id="75a65-142">このコンポーネントがアプリ内で使用されると、[Visual Studio](/visualstudio/ide/using-intellisense) および [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) では IntelliSense によって、構文およびパラメーター補完を利用して、開発を迅速化できます。</span><span class="sxs-lookup"><span data-stu-id="75a65-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="75a65-143">コンポーネントはレンダリングされると、"*レンダリング ツリー*" と呼ばれる、ブラウザーのドキュメント オブジェクト モデル (DOM) のメモリ内表現になります。これは、柔軟かつ効率的な方法で UI を更新するために使われます。</span><span class="sxs-lookup"><span data-stu-id="75a65-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="75a65-144"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="75a65-144"> WebAssembly</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="75a65-145"> WebAssembly は、.NET を使って対話型のクライアント側 Web アプリを構築するための、単一ページ アプリのフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="75a65-145"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="75a65-146"> WebAssembly は、プラグインやコードのトランスパイルを伴わずにオープン Web の標準を使用して、モバイル ブラウザーなど、最新のすべての Web ブラウザー上で機能します。</span><span class="sxs-lookup"><span data-stu-id="75a65-146"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="75a65-147">[WebAssembly](https://webassembly.org) (略称 *wasm*) によって、Web ブラウザー内で .NET コードを実行することが可能になります。</span><span class="sxs-lookup"><span data-stu-id="75a65-147">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="75a65-148">WebAssembly は、ダウンロードを高速化し実行速度を最大限に高めるために最適化されたコンパクトなバイトコード形式です。</span><span class="sxs-lookup"><span data-stu-id="75a65-148">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="75a65-149">WebAssembly はオープンな Web 標準であり、プラグインを使わずに Web ブラウザー内でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="75a65-149">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="75a65-150">WebAssembly コードを使用すると、JavaScript を介してブラウザーの全機能にアクセスでき、"*JavaScript の相互運用性*" (または、"*JavaScript 相互運用*") と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="75a65-150">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="75a65-151">ブラウザーの WebAssembly 経由で実行される .NET コードは、ブラウザーの JavaScript サンドボックス内で実行されます。その際、クライアント コンピューター上での悪意のある操作に対して、サンドボックスに備わった保護が適用されます。</span><span class="sxs-lookup"><span data-stu-id="75a65-151">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="75a65-152">![Blazor WebAssembly では WebAssembly を使用してブラウザーで .NET コードが実行されます。](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="75a65-152">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="75a65-153">Blazor WebAssembly アプリをビルドしてブラウザーで実行する場合:</span><span class="sxs-lookup"><span data-stu-id="75a65-153">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="75a65-154">C# コード ファイルと Razor ファイルが .NET アセンブリにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="75a65-154">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="75a65-155">そのアセンブリと .NET ランタイムがブラウザーにダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="75a65-155">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="75a65-156"> WebAssembly により .NET ランタイムがブートストラップされ、アプリのアセンブリを読み込むようにそのランタイムが構成されます。</span><span class="sxs-lookup"><span data-stu-id="75a65-156"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="75a65-157">Blazor WebAssembly ランタイムでは、JavaScript 相互運用を使って DOM 操作とブラウザー API の呼び出しが処理されます。</span><span class="sxs-lookup"><span data-stu-id="75a65-157">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="75a65-158">公開されているアプリのサイズである "*ペイロードのサイズ*" は、アプリの使用性に関する重要なパフォーマンス要因になります。</span><span class="sxs-lookup"><span data-stu-id="75a65-158">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="75a65-159">大規模なアプリは、ブラウザーへのダウンロードに比較的長い時間がかかり、ユーザー エクスペリエンスが損なわれます。</span><span class="sxs-lookup"><span data-stu-id="75a65-159">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="75a65-160"> WebAssembly では、ダウンロード時間を短縮するためにペイロードのサイズが最適化されます。</span><span class="sxs-lookup"><span data-stu-id="75a65-160"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="75a65-161">アプリが[中間言語 (IL) リンカー](xref:host-and-deploy/blazor/configure-linker)によって公開される場合、アプリから未使用コードが除去されます。</span><span class="sxs-lookup"><span data-stu-id="75a65-161">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="75a65-162">HTTP 応答が圧縮されます。</span><span class="sxs-lookup"><span data-stu-id="75a65-162">HTTP responses are compressed.</span></span>
* <span data-ttu-id="75a65-163">.NET ランタイムとアセンブリがブラウザーにキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="75a65-163">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="75a65-164"> サーバー</span><span class="sxs-lookup"><span data-stu-id="75a65-164"> Server</span></span>

Blazor<span data-ttu-id="75a65-165"> では、UI の更新プログラムを適用する方法からコンポーネントのレンダリング ロジックが分離されます。</span><span class="sxs-lookup"><span data-stu-id="75a65-165"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="75a65-166"> サーバーでは、ASP.NET Core アプリでサーバー上の Razor コンポーネントをホストするためのサポートが提供されます。</span><span class="sxs-lookup"><span data-stu-id="75a65-166"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="75a65-167">UI の更新は [SignalR](xref:signalr/introduction) 接続を介して処理されます。</span><span class="sxs-lookup"><span data-stu-id="75a65-167">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="75a65-168">ランタイムでは、ブラウザーからサーバーへの UI イベントの送信が処理されてから、コンポーネントの実行後に、サーバーからブラウザーへ返送された UI の更新が適用されます。</span><span class="sxs-lookup"><span data-stu-id="75a65-168">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="75a65-169">ブラウザーと通信するために Blazor サーバーで使われる接続は、JavaScript 相互運用の呼び出しを処理するためにも使われます。</span><span class="sxs-lookup"><span data-stu-id="75a65-169">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="75a65-170">![Blazor サーバーでは、サーバー上で .NET コードが実行され、SignalR 接続を介してクライアント上のドキュメント オブジェクト モデルとのやりとりが行われます](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="75a65-170">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="75a65-171">JavaScript 相互運用</span><span class="sxs-lookup"><span data-stu-id="75a65-171">JavaScript interop</span></span>

<span data-ttu-id="75a65-172">サード パーティ製の JavaScript ライブラリや、ブラウザーの API へのアクセスを必要とするアプリのために、コンポーネントは JavaScript と相互運用します。</span><span class="sxs-lookup"><span data-stu-id="75a65-172">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="75a65-173">コンポーネントでは、JavaScript で使用できるライブラリまたは API はいずれも使用することができます。</span><span class="sxs-lookup"><span data-stu-id="75a65-173">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="75a65-174">C# コードによる JavaScript コードの呼び出し、および JavaScript コードによる C# コードの呼び出しを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="75a65-174">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="75a65-175">詳細については、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="75a65-175">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="75a65-176">コードの共有と .NET Standard</span><span class="sxs-lookup"><span data-stu-id="75a65-176">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="75a65-177"> では [.NET Standard 2.0](/dotnet/standard/net-standard) が実装されます。</span><span class="sxs-lookup"><span data-stu-id="75a65-177"> implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="75a65-178">.NET Standard は、.NET 実装全体で共通した .NET API の標準仕様です。</span><span class="sxs-lookup"><span data-stu-id="75a65-178">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="75a65-179">.NET Standard のクラス ライブラリは、Blazor、.NET Framework、.NET Core、Xamarin、Mono、Unity など、さまざまな .NET プラットフォーム全体で共有することができます。</span><span class="sxs-lookup"><span data-stu-id="75a65-179">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="75a65-180">Web ブラウザー内で使用できない API (たとえば、ファイル システムへのアクセス、ソケットを開く機能、スレッドなど) からは、<xref:System.PlatformNotSupportedException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="75a65-180">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="75a65-181">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="75a65-181">Additional resources</span></span>

* [<span data-ttu-id="75a65-182">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="75a65-182">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="75a65-183">C# のガイド</span><span class="sxs-lookup"><span data-stu-id="75a65-183">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="75a65-184">HTML</span><span class="sxs-lookup"><span data-stu-id="75a65-184">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="75a65-185">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) コミュニティへのリンク</span><span class="sxs-lookup"><span data-stu-id="75a65-185">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
