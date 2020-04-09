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
# <a name="introduction-to-aspnet-core-opno-locblazor"></a>ASP.NET Core Blazor の概要

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

*Blazor へようこそ。*

Blazor は、.NET を使って対話型のクライアント側 Web UI を構築するためのフレームワークです。

* JavaScript の代わりに C# を使って、優れた対話型 UI を作成します。
* .NET で記述された、サーバー側とクライアント側のアプリのロジックを共有します。
* モバイル ブラウザーを含めた広範なブラウザーのサポートのために、HTML および CSS として UI をレンダリングします。
* [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index) などの最新のホスティング プラットフォームと統合します。

クライアント側の Web 開発に .NET を使用すると、次のような利点があります。

* JavaScript ではなく C# でコードを記述します。
* .NET ライブラリの既存の .NET エコシステムを活用します。
* サーバーとクライアント全体でアプリ ロジックを共有します。
* .NET のパフォーマンス、信頼性、およびセキュリティから利点が得られます。
* Windows、Linux、macOS 上の Visual Studio を使って生産性を維持します。
* 多機能で使いやすい安定した言語、フレームワーク、およびツールの共通セットに基づいて構築します。

## <a name="components"></a>コンポーネント

Blazor アプリは、"*コンポーネント*" に基づいています。 Blazor のコンポーネントとは、ページ、ダイアログ、データ エントリ フォームなどの UI の要素です。

コンポーネントは、.NET アセンブリに組み込まれている次のような .NET クラスです。

* 柔軟な UI のレンダリング ロジックを定義する。
* ユーザー イベントを処理する。
* 入れ子にしたり再利用したりできる。
* [Razor クラス ライブラリ](xref:razor-pages/ui-class)または [NuGet パッケージ](/nuget/what-is-nuget)として共有および配布できる。

コンポーネント クラスは通常、[Razor](xref:mvc/views/razor) マークアップ ページの形式で、 *.razor* ファイル拡張子で記述されます。 Blazor のコンポーネントは、正式には *Razor コンポーネント*と呼ばれます。 Razor とは、開発者の生産性のために設計された、C# コードに HTML マークアップを結合するための構文です。 Razor を使用すると、[IntelliSense](/visualstudio/ide/using-intellisense) サポートを利用して、同一ファイル内で HTML マークアップと C# を切り替えることができます。 また、Razor Pages と MVC でも、Razor を使用します。 要求/応答モデルを中心に構築される Razor Pages や MVC とは異なり、コンポーネントは特にクライアント側の UI ロジックとコンポジションに対して使用されます。

次の Razor マークアップは、別のコンポーネント内で入れ子にできるコンポーネント (*Dialog.razor*) を示しています。

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

ダイアログの本文の内容 (`ChildContent`) とタイトル (`Title`) は、UI にこのコンポーネントを使用するコンポーネントによって提供されます。 `OnYes` は、ボタンの `onclick` イベントによってトリガーされる C# メソッドです。

Blazor では、UI コンポジションにとって自然な HTML タグを使用します。 HTML 要素によってコンポーネントを指定し、タグの属性によってコンポーネントのプロパティに値を渡します。

次の例では、`Index` コンポーネントで `Dialog` コンポーネントが使われています。 `ChildContent` と `Title` は、属性と `<Dialog>` 要素の内容によって設定されています。

*Index.razor*:

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

ブラウザー内で親 (*Index.razor*) にアクセスすると、ダイアログがレンダリングされます。

![ブラウザーにレンダリングされた Dialog コンポーネント](index/_static/dialog.png)

このコンポーネントがアプリ内で使用されると、[Visual Studio](/visualstudio/ide/using-intellisense) および [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) では IntelliSense によって、構文およびパラメーター補完を利用して、開発を迅速化できます。

コンポーネントはレンダリングされると、"*レンダリング ツリー*" と呼ばれる、ブラウザーのドキュメント オブジェクト モデル (DOM) のメモリ内表現になります。これは、柔軟かつ効率的な方法で UI を更新するために使われます。

## <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor WebAssembly は、.NET を使って対話型のクライアント側 Web アプリを構築するための、単一ページ アプリのフレームワークです。 Blazor WebAssembly は、プラグインやコードのトランスパイルを伴わずにオープン Web の標準を使用して、モバイル ブラウザーなど、最新のすべての Web ブラウザー上で機能します。

[WebAssembly](https://webassembly.org) (略称 *wasm*) によって、Web ブラウザー内で .NET コードを実行することが可能になります。 WebAssembly は、ダウンロードを高速化し実行速度を最大限に高めるために最適化されたコンパクトなバイトコード形式です。 WebAssembly はオープンな Web 標準であり、プラグインを使わずに Web ブラウザー内でサポートされます。

WebAssembly コードを使用すると、JavaScript を介してブラウザーの全機能にアクセスでき、"*JavaScript の相互運用性*" (または、"*JavaScript 相互運用*") と呼ばれています。 ブラウザーの WebAssembly 経由で実行される .NET コードは、ブラウザーの JavaScript サンドボックス内で実行されます。その際、クライアント コンピューター上での悪意のある操作に対して、サンドボックスに備わった保護が適用されます。

![Blazor WebAssembly では WebAssembly を使用してブラウザーで .NET コードが実行されます。](index/_static/blazor-webassembly.png)

Blazor WebAssembly アプリをビルドしてブラウザーで実行する場合:

* C# コード ファイルと Razor ファイルが .NET アセンブリにコンパイルされます。
* そのアセンブリと .NET ランタイムがブラウザーにダウンロードされます。
* Blazor WebAssembly により .NET ランタイムがブートストラップされ、アプリのアセンブリを読み込むようにそのランタイムが構成されます。 Blazor WebAssembly ランタイムでは、JavaScript 相互運用を使って DOM 操作とブラウザー API の呼び出しが処理されます。

公開されているアプリのサイズである "*ペイロードのサイズ*" は、アプリの使用性に関する重要なパフォーマンス要因になります。 大規模なアプリは、ブラウザーへのダウンロードに比較的長い時間がかかり、ユーザー エクスペリエンスが損なわれます。 Blazor WebAssembly では、ダウンロード時間を短縮するためにペイロードのサイズが最適化されます。

* アプリが[中間言語 (IL) リンカー](xref:host-and-deploy/blazor/configure-linker)によって公開される場合、アプリから未使用コードが除去されます。
* HTTP 応答が圧縮されます。
* .NET ランタイムとアセンブリがブラウザーにキャッシュされます。

## <a name="opno-locblazor-server"></a>Blazor サーバー

Blazor では、UI の更新プログラムを適用する方法からコンポーネントのレンダリング ロジックが分離されます。 Blazor サーバーでは、ASP.NET Core アプリでサーバー上の Razor コンポーネントをホストするためのサポートが提供されます。 UI の更新は [SignalR](xref:signalr/introduction) 接続を介して処理されます。

ランタイムでは、ブラウザーからサーバーへの UI イベントの送信が処理されてから、コンポーネントの実行後に、サーバーからブラウザーへ返送された UI の更新が適用されます。

ブラウザーと通信するために Blazor サーバーで使われる接続は、JavaScript 相互運用の呼び出しを処理するためにも使われます。

![Blazor サーバーでは、サーバー上で .NET コードが実行され、SignalR 接続を介してクライアント上のドキュメント オブジェクト モデルとのやりとりが行われます](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>JavaScript 相互運用

サード パーティ製の JavaScript ライブラリや、ブラウザーの API へのアクセスを必要とするアプリのために、コンポーネントは JavaScript と相互運用します。 コンポーネントでは、JavaScript で使用できるライブラリまたは API はいずれも使用することができます。 C# コードによる JavaScript コードの呼び出し、および JavaScript コードによる C# コードの呼び出しを行うことができます。 詳細については、次の記事を参照してください。

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a>コードの共有と .NET Standard

Blazor では [.NET Standard 2.0](/dotnet/standard/net-standard) が実装されます。 .NET Standard は、.NET 実装全体で共通した .NET API の標準仕様です。 .NET Standard のクラス ライブラリは、Blazor、.NET Framework、.NET Core、Xamarin、Mono、Unity など、さまざまな .NET プラットフォーム全体で共有することができます。

Web ブラウザー内で使用できない API (たとえば、ファイル システムへのアクセス、ソケットを開く機能、スレッドなど) からは、<xref:System.PlatformNotSupportedException> がスローされます。

## <a name="additional-resources"></a>その他の技術情報

* [WebAssembly](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [C# のガイド](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) コミュニティへのリンク
