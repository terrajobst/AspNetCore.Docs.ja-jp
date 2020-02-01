---
title: ASP.NET Core Blazor アプリでのエラーの処理
author: guardrex
description: Blazor でハンドルされない例外を管理する方法、およびエラーを検出して処理するアプリを開発する方法を ASP.NET Core Blazor 方法を説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: b987513e5410e95ab632b9935d858b648838d94f
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928265"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a>ASP.NET Core Blazor アプリでのエラーの処理

作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)

この記事では、Blazor がハンドルされない例外を管理する方法と、エラーを検出して処理するアプリを開発する方法について説明します。

## <a name="detailed-errors-during-development"></a>開発中の詳細なエラー

開発中に Blazor アプリが正常に機能していない場合、アプリからの詳細なエラー情報を受け取ることで、問題のトラブルシューティングと修正に役立ちます。 エラーが発生すると Blazor アプリによって画面の下部に金色のバーが表示されます。

* 開発中は、金色のバーによってブラウザー コンソールが表示され、そこで例外を確認できます。
* 実稼働環境では、金色のバーによって、エラーが発生したことがユーザーに通知され、ブラウザーの更新が推奨されます。

このエラー処理エクスペリエンスの UI は、Blazor プロジェクトテンプレートの一部です。

Blazor WebAssembly で、 *wwwroot/index.html*ファイルのエクスペリエンスをカスタマイズします。

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

Blazor Server アプリで、 *Pages/_Host cshtml*ファイルのエクスペリエンスをカスタマイズします。

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

`blazor-error-ui` 要素は、Blazor テンプレートに含まれるスタイルによって非表示になり、エラーが発生したときに表示されます。

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a>Blazor サーバーアプリがハンドルされない例外にどのように反応するか

Blazor Server は、ステートフルなフレームワークです。 ユーザーは、アプリを操作している間、*回線*と呼ばれるサーバーへの接続を維持します。 回線は、アクティブなコンポーネントインスタンスに加えて、次のような状態の他の多くの側面を保持します。

* コンポーネントの最新のレンダリング出力。
* クライアント側のイベントによってトリガーされる可能性がある、現在のイベント処理デリゲートのセット。

ユーザーが複数のブラウザータブでアプリを開いた場合、複数の独立した回路があります。

Blazor は、ほとんどのハンドルされない例外を、発生した回線にとって致命的なものとして扱います。 ハンドルされない例外によって回線が終了した場合、ユーザーは新しい回線を作成するためにページを再読み込みするだけで、アプリとの対話を続けることができます。 他のユーザーまたは他のブラウザータブの回線である、終了した回路以外の回路は影響を受けません。 このシナリオは、クラッシュしたアプリを再起動する必要がある&mdash;クラッシュするデスクトップアプリに似ていますが、他のアプリは影響を受けません。

次の理由により、ハンドルされない例外が発生したときに回線が終了します。

* ハンドルされない例外が発生すると、回線が未定義の状態のままになることがよくあります。
* 未処理の例外が発生した後は、アプリの通常の動作を保証できません。
* 回線が継続している場合は、セキュリティの脆弱性がアプリに表示されることがあります。

## <a name="manage-unhandled-exceptions-in-developer-code"></a>開発者コードでハンドルされない例外を管理する

エラー発生後もアプリを続行するには、アプリにエラー処理ロジックが必要です。 この記事の後のセクションでは、未処理の例外の潜在的な原因について説明します。

実稼働環境では、フレームワークの例外メッセージやスタックトレースを UI に表示しません。 例外メッセージまたはスタックトレースを表示すると、次のようになります。

* エンドユーザーに機密情報を開示します。
* 悪意のあるユーザーがアプリ、サーバー、またはネットワークのセキュリティを侵害する可能性のある脆弱性を検出するのを支援します。

## <a name="log-errors-with-a-persistent-provider"></a>永続的なプロバイダーでエラーをログに記録する

未処理の例外が発生した場合、例外は、サービスコンテナーに構成されている <xref:Microsoft.Extensions.Logging.ILogger> インスタンスに記録されます。 既定では、Blazor アプリはコンソールログプロバイダーを使用してコンソール出力に記録されます。 ログサイズとログローテーションを管理するプロバイダーを使用して、より永続的な場所にログを記録することを検討してください。 詳細については、「 <xref:fundamentals/logging/index>」を参照してください。

開発中、Blazor は通常、デバッグを支援するために、ブラウザーのコンソールに例外の完全な詳細情報を送信します。 運用環境では、ブラウザーのコンソールでの詳細なエラーは既定で無効になっています。つまり、エラーはクライアントに送信されませんが、例外の完全な詳細は引き続きサーバー側でログに記録されます。 詳細については、「 <xref:fundamentals/error-handling>」を参照してください。

ログに記録するインシデントと、ログに記録されるインシデントの重大度レベルを決定する必要があります。 悪意のあるユーザーは、意図的にエラーをトリガーできる可能性があります。 たとえば、製品の詳細を表示するコンポーネントの URL に不明な `ProductId` が指定されている場合は、エラーからインシデントをログに記録しないようにします。 すべてのエラーを、ログ記録の重要度の高いインシデントとして処理することはできません。

## <a name="places-where-errors-may-occur"></a>エラーが発生する可能性のある場所

フレームワークとアプリコードは、次のいずれかの場所でハンドルされない例外をトリガーすることがあります。

* [コンポーネントのインスタンス化](#component-instantiation)
* [ライフサイクルメソッド](#lifecycle-methods)
* [レンダリングロジック](#rendering-logic)
* [イベントハンドラー](#event-handlers)
* [コンポーネントの破棄](#component-disposal)
* [JavaScript の相互運用](#javascript-interop)
* [Blazor サーバーのサーキットハンドラー](#blazor-server-circuit-handlers)
* [Blazor サーバー回線の破棄](#blazor-server-circuit-disposal)
* [Blazor サーバーの再度リリース](#blazor-server-prerendering)

上記のハンドルされない例外については、この記事の次のセクションで説明します。

### <a name="component-instantiation"></a>コンポーネントのインスタンス化

Blazor がコンポーネントのインスタンスを作成する場合:

* コンポーネントのコンストラクターが呼び出されます。
* [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component)ディレクティブまたは[`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component)属性を使用して、コンポーネントのコンストラクターに渡される非シングルトン DI サービスのコンストラクターが呼び出されます。

任意の `[Inject]` プロパティの実行コンストラクターまたは setter がハンドルされない例外をスローすると、Blazor サーバー回線が失敗します。 フレームワークはコンポーネントをインスタンス化できないため、例外は fatal です。 コンストラクターのロジックによって例外がスローされる可能性がある場合、アプリでは、エラー処理とログ記録を含む [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントを使用して例外をトラップする必要があります。

### <a name="lifecycle-methods"></a>ライフサイクル メソッド

コンポーネントの有効期間中、Blazor は次の[ライフサイクルメソッド](xref:blazor/lifecycle)を呼び出します。

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

いずれかのライフサイクルメソッドが、同期的または非同期的に例外をスローした場合、例外は Blazor サーバー回線にとって致命的です。 コンポーネントがライフサイクルメソッドのエラーに対処するには、エラー処理ロジックを追加します。

次の例では、`OnParametersSetAsync` がメソッドを呼び出して製品を取得します。

* `ProductRepository.GetProductByIdAsync` メソッドでスローされた例外は、`try-catch` ステートメントによって処理されます。
* `catch` ブロックを実行すると、次のようになります。
  * `loadFailed` は `true`に設定されます。これは、ユーザーにエラーメッセージを表示するために使用されます。
  * エラーがログに記録されます。

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>レンダリングロジック

`.razor` コンポーネントファイル内の宣言マークアップは、`BuildRenderTree`とC#呼ばれるメソッドにコンパイルされます。 コンポーネントがレンダリングされると、`BuildRenderTree` が実行され、レンダリングされたコンポーネントの要素、テキスト、および子コンポーネントを記述するデータ構造が構築されます。

レンダリングロジックは例外をスローすることがあります。 このシナリオの例は、`@someObject.PropertyName` が評価され、`@someObject` が `null`場合に発生します。 レンダリングロジックによってスローされた未処理の例外は、Blazor サーバー回線にとって致命的です。

レンダリングロジックで null 参照例外が発生しないようにするには、そのメンバーにアクセスする前に `null` オブジェクトを確認します。 次の例では、`person.Address` が `null`場合、`person.Address` のプロパティにはアクセスしません。

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

上記のコードでは、`person` が `null`でないことを前提としています。 多くの場合、コードの構造によって、コンポーネントのレンダリング時にオブジェクトが存在することが保証されます。 そのような場合は、表示ロジックで `null` を確認する必要はありません。 前の例では、コンポーネントがインスタンス化されるときに `person` が作成されるため、`person` が確実に存在することが保証されます。

### <a name="event-handlers"></a>イベント ハンドラー

クライアント側のコードは、次C#を使用してイベントハンドラーが作成されるときに、コードの呼び出しをトリガーします。

* `@onclick`
* `@onchange`
* その他の `@on...` 属性
* `@bind`

これらのシナリオでは、イベントハンドラーコードによってハンドルされない例外がスローされることがあります。

イベントハンドラーがハンドルされない例外をスローした場合 (たとえば、データベースクエリが失敗した場合)、例外は Blazor サーバー回線にとって致命的です。 アプリが外部の理由で失敗する可能性のあるコードを呼び出した場合は、エラー処理とログ記録を含む [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントを使用して例外をトラップします。

ユーザーコードによって例外がトラップされて処理されない場合は、フレームワークによって例外がログに記録され、回線が終了します。

### <a name="component-disposal"></a>コンポーネントの破棄

たとえば、ユーザーが別のページに移動したため、コンポーネントが UI から削除されることがあります。 <xref:System.IDisposable?displayProperty=fullName> を実装するコンポーネントが UI から削除されると、フレームワークはコンポーネントの <xref:System.IDisposable.Dispose*> メソッドを呼び出します。

コンポーネントの `Dispose` メソッドがハンドルされない例外をスローした場合、例外は Blazor サーバー回線にとって致命的です。 破棄ロジックによって例外がスローされる可能性がある場合、アプリでは、エラー処理とログ記録を含む [try-catch ](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントを使用して例外をトラップする必要があります。

コンポーネントの破棄の詳細については、「<xref:blazor/lifecycle#component-disposal-with-idisposable>」を参照してください。

### <a name="javascript-interop"></a>JavaScript 相互運用

`IJSRuntime.InvokeAsync<T>` を使用すると、.NET コードは、ユーザーのブラウザーで JavaScript ランタイムへの非同期呼び出しを行うことができます。

`InvokeAsync<T>`でのエラー処理には、次の条件が適用されます。

* `InvokeAsync<T>` の呼び出しが同期的に失敗した場合、.NET 例外が発生します。 たとえば、指定された引数をシリアル化できないため、`InvokeAsync<T>` を呼び出すことはできません。 開発者コードは例外をキャッチする必要があります。 イベントハンドラーまたはコンポーネントのライフサイクルメソッドのアプリコードで例外が処理されない場合、結果として得られる例外は Blazor サーバー回線にとって致命的です。
* `InvokeAsync<T>` の呼び出しが非同期に失敗した場合、.NET <xref:System.Threading.Tasks.Task> は失敗します。 たとえば、JavaScript 側のコードが例外をスローしたり、`rejected`として完了した `Promise` を返したりするために、`InvokeAsync<T>` の呼び出しが失敗することがあります。 開発者コードは例外をキャッチする必要があります。 [Await](/dotnet/csharp/language-reference/keywords/await)演算子を使用する場合は、エラー処理とログ記録を使用し[て、try-catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントでメソッド呼び出しをラップすることを検討してください。 それ以外の場合、失敗したコードは、Blazor サーバー回線にとって致命的な未処理の例外を発生させることになります。
* 既定では、`InvokeAsync<T>` の呼び出しは特定の期間内に完了する必要があります。そうでない場合は、呼び出しがタイムアウトします。既定のタイムアウト期間は1分です。 タイムアウトは、完了メッセージを返信しないネットワーク接続または JavaScript コードの損失からコードを保護します。 呼び出しがタイムアウトした場合、結果として得られる `Task` は <xref:System.OperationCanceledException>で失敗します。 ログ記録で例外をトラップして処理します。

同様に、JavaScript コードでは、 [`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions)属性によって示される .net メソッドの呼び出しを開始できます。 これらの .NET メソッドでハンドルされない例外がスローされた場合:

* この例外は、Blazor サーバー回線にとって致命的なものとして扱われません。
* JavaScript 側の `Promise` は拒否されます。

.NET 側またはメソッド呼び出しの JavaScript 側でエラー処理コードを使用するオプションがあります。

詳細については、「 <xref:blazor/javascript-interop>」を参照してください。

### <a name="opno-locblazor-server-circuit-handlers"></a>Blazor サーバーのサーキットハンドラー

Blazor Server を使用すると、コードで*サーキットハンドラー*を定義できます。これにより、ユーザーの回線の状態の変更時にコードを実行できます。 サーキットハンドラーを実装するには、`CircuitHandler` から派生させ、そのクラスをアプリのサービスコンテナーに登録します。 次のサーキットハンドラーの例では、開いている SignalR 接続を追跡します。

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

サーキットハンドラーは DI を使用して登録されます。 スコープ付きインスタンスは、回線のインスタンスごとに作成されます。 前の例の `TrackingCircuitHandler` を使用すると、すべての回線の状態を追跡する必要があるため、シングルトンサービスが作成されます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

カスタムサーキットハンドラーのメソッドがハンドルされない例外をスローした場合、例外は Blazor サーバー回線にとって致命的です。 ハンドラーのコードまたはメソッドで例外が許容されるようにするには、エラー処理とログ記録を含む1つまたは複数の[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントでコードをラップします。

### <a name="opno-locblazor-server-circuit-disposal"></a>Blazor サーバー回線の破棄

ユーザーが切断され、フレームワークが回線の状態をクリーンアップしているために回線が終了すると、フレームワークは回線の DI スコープを破棄します。 スコープを破棄すると、<xref:System.IDisposable?displayProperty=fullName>を実装するサーキットスコープの DI サービスが破棄されます。 破棄中にいずれかの DI サービスがハンドルされない例外をスローした場合、フレームワークは例外をログに記録します。

### <a name="opno-locblazor-server-prerendering"></a>Blazor サーバーのプリレンダリング

Blazor コンポーネントは、レンダリングされた HTML マークアップがユーザーの初期 HTTP 要求の一部として返されるように、`Component` タグヘルパーを使用して prerendered できます。 これは次のように機能します。

* 同じページに含まれるすべての prerendered コンポーネントに対して新しい回線を作成します。
* 初期 HTML を生成しています。
* ユーザーのブラウザーが同じサーバーへの SignalR 接続を確立するまで、回線を `disconnected` として扱います。 接続が確立されると、回線の対話機能が再開され、コンポーネントの HTML マークアップが更新されます。

たとえば、ライフサイクルメソッドやレンダリングロジックで、コンポーネントによって処理されない例外がスローされた場合は、次のようになります。

* この例外は、回線にとって致命的です。
* 例外は、`Component` タグヘルパーからの呼び出し履歴によってスローされます。 したがって、例外が開発者コードによって明示的にキャッチされない限り、HTTP 要求全体が失敗します。

通常の状況では、事前設定に失敗した場合に、コンポーネントのビルドとレンダリングを続行しても意味がありません。これは、作業コンポーネントをレンダリングできないためです。

プリレンダリング中に発生する可能性のあるエラーを許容するには、例外をスローする可能性のあるコンポーネント内にエラー処理ロジックを配置する必要があります。 エラー処理とログ記録では、 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントを使用します。 `try-catch` ステートメントで `Component` タグヘルパーをラップするのではなく、`Component` タグヘルパーによって表示されるコンポーネントにエラー処理ロジックを配置します。

## <a name="advanced-scenarios"></a>高度なシナリオ

### <a name="recursive-rendering"></a>再帰的なレンダリング

コンポーネントは再帰的に入れ子にすることができます。 これは、再帰的なデータ構造を表す場合に便利です。 たとえば、`TreeNode` コンポーネントでは、ノードの子ごとにより多くの `TreeNode` コンポーネントをレンダリングできます。

再帰的にレンダリングする場合は、無限再帰になるようなコーディングパターンを避けます。

* 循環を含むデータ構造を再帰的に表示することは避けてください。 たとえば、子を含むツリーノードは表示しません。
* 循環を含むレイアウトのチェーンを作成しないでください。 たとえば、レイアウトがそれ自体であるレイアウトを作成しないようにします。
* 悪意のあるデータ入力または JavaScript の相互運用呼び出しによって、エンドユーザーが再帰不変 (ルール) に違反しないようにします。

レンダリング中の無限ループ:

* レンダリングプロセスを永久に続行します。
* は、終了しないループを作成するのと同じです。

これらのシナリオでは、影響を受ける Blazor サーバー回線に障害が発生すると、通常、スレッドは次のことを試行します。

* オペレーティングシステムで許可されている CPU 時間を無期限に使用します。
* 無制限の数のサーバーメモリを消費します。 メモリを無制限に使用することは、反復処理のたびに終了しないループによってコレクションにエントリが追加されるシナリオと同じです。

無限の再帰パターンを回避するには、再帰的なレンダリングコードに適切な停止条件が含まれていることを確認します。

### <a name="custom-render-tree-logic"></a>カスタムレンダリングツリーのロジック

ほとんどの Blazor コンポーネントは、razor ファイルとして実装され、`RenderTreeBuilder` を操作して出力を表示するロジックを生成するためにコンパイルされ*ます。* 開発者は、手続きC#型コードを使用して `RenderTreeBuilder` ロジックを手動で実装できます。 詳細については、「 <xref:blazor/components#manual-rendertreebuilder-logic>」を参照してください。

> [!WARNING]
> 手動のレンダーツリービルダーロジックの使用は、高度で安全ではないシナリオと見なされます。一般的なコンポーネントの開発にはお勧めしません。

`RenderTreeBuilder` コードが記述されている場合、開発者はコードの正確性を保証する必要があります。 たとえば、開発者は次のことを確認する必要があります。

* `OpenElement` と `CloseElement` の呼び出しが正しく調整されています。
* 属性は正しい場所にのみ追加されます。

手動のレンダーツリービルダーのロジックが正しくないと、クラッシュ、サーバーのハング、セキュリティの脆弱性など、任意の未定義の動作が発生する可能性があります。

アセンブリコードまたは MSIL 命令を手作業で記述するのと同じレベルの複雑さと同じレベルの*危険*を考慮して、手動のレンダリングツリービルダーロジックを検討してください。
