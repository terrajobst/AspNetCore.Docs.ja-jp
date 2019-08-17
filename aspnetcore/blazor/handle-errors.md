---
title: ASP.NET Core Blazor アプリでのエラーの処理
author: guardrex
description: Blazor が未処理の例外をどのように管理するか、およびエラーを検出して処理するアプリを開発する方法について、ASP.NET Core Blazor 方法を説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/06/2019
uid: blazor/handle-errors
ms.openlocfilehash: 52f55af99881b09c84d9cf88f5845efcb1ea76a1
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68948452"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a>ASP.NET Core Blazor アプリでのエラーの処理

作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)

この記事では、Blazor が未処理の例外を管理する方法と、エラーを検出して処理するアプリを開発する方法について説明します。

## <a name="how-the-blazor-framework-reacts-to-unhandled-exceptions"></a>Blazor フレームワークがハンドルされない例外にどのように反応するか

Blazor サーバー側は、ステートフルなフレームワークです。 ユーザーは、アプリを操作している間、*回線*と呼ばれるサーバーへの接続を維持します。 回線は、アクティブなコンポーネントインスタンスに加えて、次のような状態の他の多くの側面を保持します。

* コンポーネントの最新のレンダリング出力。
* クライアント側のイベントによってトリガーされる可能性がある、現在のイベント処理デリゲートのセット。

ユーザーが複数のブラウザータブでアプリを開いた場合、複数の独立した回路があります。

Blazor は、ほとんどのハンドルされない例外を、発生した回線にとって致命的なものとして扱います。 ハンドルされない例外によって回線が終了した場合、ユーザーは新しい回線を作成するためにページを再読み込みするだけで、アプリとの対話を続けることができます。 他のユーザーまたは他のブラウザータブの回線である、終了した回路以外の回路は影響を受けません。 このシナリオはデスクトップアプリに似ています&mdash;が、クラッシュしたアプリは再起動する必要がありますが、他のアプリは影響を受けません。

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

未処理の例外が発生した場合、例外は<xref:Microsoft.Extensions.Logging.ILogger>サービスコンテナーで構成されたインスタンスに記録されます。 既定では、Blazor apps はコンソールログプロバイダーを使用してコンソール出力にログを記録します。 ログサイズとログローテーションを管理するプロバイダーを使用して、より永続的な場所にログを記録することを検討してください。 詳細については、「 <xref:fundamentals/logging/index> 」を参照してください。

開発中、Blazor は通常、デバッグを支援するために、例外の完全な詳細をブラウザーのコンソールに送信します。 運用環境では、ブラウザーのコンソールでの詳細なエラーは既定で無効になっています。つまり、エラーはクライアントに送信されませんが、例外の完全な詳細は引き続きサーバー側でログに記録されます。 詳細については、「 <xref:fundamentals/error-handling> 」を参照してください。

ログに記録するインシデントと、ログに記録されるインシデントの重大度レベルを決定する必要があります。 悪意のあるユーザーは、意図的にエラーをトリガーできる可能性があります。 たとえば、製品の詳細を表示するコンポーネントの URL に不明`ProductId`なが指定されている場合は、エラーからインシデントをログに記録しないようにします。 すべてのエラーを、ログ記録の重要度の高いインシデントとして処理することはできません。

## <a name="places-where-errors-may-occur"></a>エラーが発生する可能性のある場所

フレームワークとアプリコードは、次のいずれかの場所でハンドルされない例外をトリガーすることがあります。

* [コンポーネントのインスタンス化](#component-instantiation)
* [ライフサイクルメソッド](#lifecycle-methods)
* [レンダリングロジック](#rendering-logic)
* [イベントハンドラー](#event-handlers)
* [コンポーネントの破棄](#component-disposal)
* [JavaScript の相互運用](#javascript-interop)
* [サーキットハンドラー](#circuit-handlers)
* [回線破棄](#circuit-disposal)
* [プリ](#prerendering)

上記のハンドルされない例外については、この記事の次のセクションで説明します。

### <a name="component-instantiation"></a>コンポーネントのインスタンス化

Blazor がコンポーネントのインスタンスを作成する場合:

* コンポーネントのコンストラクターが呼び出されます。
* [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component)ディレクティブまたは[[挿入]](xref:blazor/dependency-injection#request-a-service-in-a-component)属性を使用して、コンポーネントのコンストラクターに提供される非シングルトン DI サービスのコンストラクターが呼び出されます。 

任意`[Inject]`のプロパティに対して実行されるコンストラクターまたは setter がハンドルされない例外をスローすると、回線が失敗します。 フレームワークはコンポーネントをインスタンス化できないため、例外は fatal です。 コンストラクターのロジックによって例外がスローされる可能性がある場合、アプリでは、エラー処理とログ記録を含む [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントを使用して例外をトラップする必要があります。

### <a name="lifecycle-methods"></a>ライフサイクル メソッド

コンポーネントの有効期間中、Blazor はライフサイクルメソッドを呼び出します。

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

あるライフサイクルメソッドが例外を同期的または非同期的にスローする場合、この例外は回線にとって致命的です。 コンポーネントがライフサイクルメソッドのエラーに対処するには、エラー処理ロジックを追加します。

次の例`OnParametersSetAsync`では、がメソッドを呼び出して製品を取得します。

* `ProductRepository.GetProductByIdAsync`メソッドでスローされた例外は、 `try-catch`ステートメントによって処理されます。
* `catch`ブロックが実行されたとき:
  * `loadFailed`はに`true`設定されます。これは、ユーザーにエラーメッセージを表示するために使用されます。
  * エラーがログに記録されます。

[!code-cshtml[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>レンダリングロジック

`.razor`コンポーネントファイル内の宣言型マークアップは、とC#いう`BuildRenderTree`メソッドにコンパイルされます。 コンポーネントがレンダリングされる`BuildRenderTree`と、は、レンダリングされたコンポーネントの要素、テキスト、および子コンポーネントを記述するデータ構造を実行し、構築します。

レンダリングロジックは例外をスローすることがあります。 このシナリオの例は、が`@someObject.PropertyName` `@someObject`評価され、 `null`がである場合に発生します。 レンダリングロジックによってスローされた未処理の例外は、回線にとって致命的です。

レンダリングロジックで null 参照例外が発生しないようにする`null`には、そのメンバーにアクセスする前にオブジェクトを確認します。 次の例では`person.Address` 、がの場合`person.Address` 、 `null`プロパティはアクセスされません。

[!code-cshtml[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

上記のコードでは`person` 、 `null`がではないと想定しています。 多くの場合、コードの構造によって、コンポーネントのレンダリング時にオブジェクトが存在することが保証されます。 そのような場合は、レンダリングロジックでを`null`確認する必要はありません。 前の例では`person` 、はコンポーネントがインスタンス化`person`されるときにが作成されるため、が確実に存在することが保証されます。

### <a name="event-handlers"></a>イベント ハンドラー

クライアント側のコードは、次C#を使用してイベントハンドラーが作成されるときに、コードの呼び出しをトリガーします。

* `@onclick`
* `@onchange`
* その`@on...`他の属性
* `@bind`

これらのシナリオでは、イベントハンドラーコードによってハンドルされない例外がスローされることがあります。

イベントハンドラーがハンドルされない例外をスローした場合 (たとえば、データベースクエリが失敗した場合)、その例外は回線にとって致命的です。 アプリが外部の理由で失敗する可能性のあるコードを呼び出した場合は、エラー処理とログ記録を含む [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントを使用して例外をトラップします。

ユーザーコードによって例外がトラップされて処理されない場合は、フレームワークによって例外がログに記録され、回線が終了します。

### <a name="component-disposal"></a>コンポーネントの破棄

たとえば、ユーザーが別のページに移動したため、コンポーネントが UI から削除されることがあります。 を実装<xref:System.IDisposable?displayProperty=fullName>するコンポーネントが UI から削除されると、フレームワークはコンポーネントの<xref:System.IDisposable.Dispose*>メソッドを呼び出します。 

コンポーネントの`Dispose`メソッドがハンドルされない例外をスローした場合、この例外は回線にとって致命的です。 破棄ロジックによって例外がスローされる可能性がある場合、アプリでは、エラー処理とログ記録を含む [try-catch ](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントを使用して例外をトラップする必要があります。

コンポーネントの破棄の詳細について<xref:blazor/components#component-disposal-with-idisposable>は、「」を参照してください。

### <a name="javascript-interop"></a>JavaScript 相互運用

`IJSRuntime.InvokeAsync<T>`.NET コードで、ユーザーのブラウザーで JavaScript ランタイムへの非同期呼び出しを行うことができるようにします。

での`InvokeAsync<T>`エラー処理には、次の条件が適用されます。

* の呼び出し`InvokeAsync<T>`が同期的に失敗した場合、.net 例外が発生します。 たとえば、指定`InvokeAsync<T>`された引数をシリアル化できないため、の呼び出しは失敗します。 開発者コードは例外をキャッチする必要があります。 イベントハンドラーまたはコンポーネントのライフサイクルメソッドのアプリコードで例外が処理されない場合、結果として得られる例外は回線にとって致命的です。
* の呼び出し`InvokeAsync<T>`が非同期に失敗した場合<xref:System.Threading.Tasks.Task> 、.net は失敗します。 たとえば、JavaScript `InvokeAsync<T>`側のコードが例外をスローしたり、として`rejected`完了したを`Promise`返したりするために、の呼び出しが失敗することがあります。 開発者コードは例外をキャッチする必要があります。 [Await](/dotnet/csharp/language-reference/keywords/await)演算子を使用する場合は、エラー処理とログ記録を使用し[て、try-catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントでメソッド呼び出しをラップすることを検討してください。 それ以外の場合、失敗したコードは、回路にとって致命的な未処理の例外を発生させることになります。
* 既定では、の`InvokeAsync<T>`呼び出しは特定の期間内に完了する必要があります。そうでない場合は、呼び出しがタイムアウトします。既定のタイムアウト期間は1分です。 タイムアウトは、完了メッセージを返信しないネットワーク接続または JavaScript コードの損失からコードを保護します。 呼び出しがタイムアウトした場合、結果`Task` <xref:System.OperationCanceledException>としてが返されます。 ログ記録で例外をトラップして処理します。

同様に、JavaScript コードでは、 [[JSInvokable] 属性](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions)によって示される .net メソッドの呼び出しを開始できます。 これらの .NET メソッドでハンドルされない例外がスローされた場合:

* この例外は、回線にとって致命的な例外として扱われません。
* JavaScript 側`Promise`は拒否されます。

.NET 側またはメソッド呼び出しの JavaScript 側でエラー処理コードを使用するオプションがあります。

詳細については、「 <xref:blazor/javascript-interop> 」を参照してください。

### <a name="circuit-handlers"></a>サーキットハンドラー

Blazor を使用すると、コードで*サーキットハンドラー*を定義し、ユーザーの回線の状態が変化したときに通知を受け取ることができます。 次の状態が使用されます。

* `initialized`
* `connected`
* `disconnected`
* `disposed`

通知は、 `CircuitHandler`抽象基本クラスを継承する DI サービスを登録することによって管理されます。

カスタムサーキットハンドラーのメソッドがハンドルされない例外をスローした場合、この例外は回線にとって致命的です。 ハンドラーのコードまたはメソッドで例外が許容されるようにするには、エラー処理とログ記録を含む1つまたは複数の[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントでコードをラップします。

### <a name="circuit-disposal"></a>回線破棄

ユーザーが切断され、フレームワークが回線の状態をクリーンアップしているために回線が終了すると、フレームワークは回線の DI スコープを破棄します。 スコープを破棄すると、を実装<xref:System.IDisposable?displayProperty=fullName>するサーキットスコープの DI サービスが破棄されます。 破棄中にいずれかの DI サービスがハンドルされない例外をスローした場合、フレームワークは例外をログに記録します。

### <a name="prerendering"></a>プリ

Blazor コンポーネントは、レンダリングさ`Html.RenderComponentAsync`れた HTML マークアップがユーザーの初期 HTTP 要求の一部として返されるように、を使用して prerendered できます。 これは次のように機能します。

* 同じページに含まれるすべての prerendered コンポーネントを含む新しい回線を作成する。
* 初期 HTML を生成しています。
* ユーザーのブラウザーが`disconnected`同じサーバーに対して SignalR 接続を確立して回線で対話機能を再開するまで、回線をとして扱います。

たとえば、ライフサイクルメソッドやレンダリングロジックで、コンポーネントによって処理されない例外がスローされた場合は、次のようになります。

* この例外は、回線にとって致命的です。
* 例外は、呼び出しから`Html.RenderComponentAsync`の呼び出し履歴によってスローされます。 したがって、例外が開発者コードによって明示的にキャッチされない限り、HTTP 要求全体が失敗します。

通常の状況では、事前設定に失敗した場合に、コンポーネントのビルドとレンダリングを続行しても意味がありません。これは、作業コンポーネントをレンダリングできないためです。

プリレンダリング中に発生する可能性のあるエラーを許容するには、例外をスローする可能性のあるコンポーネント内にエラー処理ロジックを配置する必要があります。 エラー処理とログ記録では、 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメントを使用します。 ステートメントでの呼び出しをラップ`RenderComponentAsync`する代わりに、によって`RenderComponentAsync`表示されるコンポーネントにエラー処理ロジックを配置します。 `try-catch`

## <a name="advanced-scenarios"></a>高度なシナリオ

### <a name="recursive-rendering"></a>再帰的なレンダリング

コンポーネントは再帰的に入れ子にすることができます。 これは、再帰的なデータ構造を表す場合に便利です。 たとえば、コンポーネントは`TreeNode` 、ノードの子`TreeNode`ごとにより多くのコンポーネントをレンダリングできます。

再帰的にレンダリングする場合は、無限再帰になるようなコーディングパターンを避けます。

* 循環を含むデータ構造を再帰的に表示することは避けてください。 たとえば、子を含むツリーノードは表示しません。
* 循環を含むレイアウトのチェーンを作成しないでください。 たとえば、レイアウトがそれ自体であるレイアウトを作成しないようにします。
* 悪意のあるデータ入力または JavaScript の相互運用呼び出しによって、エンドユーザーが再帰不変 (ルール) に違反しないようにします。

レンダリング中の無限ループ:

* レンダリングプロセスを永久に続行します。
* は、終了しないループを作成するのと同じです。

これらのシナリオでは、影響を受ける回線がハングし、スレッドは通常次を試行します。

* オペレーティングシステムで許可されている CPU 時間を無期限に使用します。
* 無制限の数のサーバーメモリを消費します。 メモリを無制限に使用することは、反復処理のたびに終了しないループによってコレクションにエントリが追加されるシナリオと同じです。

無限の再帰パターンを回避するには、再帰的なレンダリングコードに適切な停止条件が含まれていることを確認します。

### <a name="custom-render-tree-logic"></a>カスタムレンダリングツリーのロジック

ほとんどの Blazor コンポーネントは、 `RenderTreeBuilder` razor ファイルとして実装され、出力をレンダリングするためにで動作するロジックを生成するためにコンパイルされ*ます。* 開発者は、手続き`RenderTreeBuilder` C#型コードを使用してロジックを手動で実装できます。 詳細については、「 <xref:blazor/components#manual-rendertreebuilder-logic> 」を参照してください。

> [!WARNING]
> 手動のレンダーツリービルダーロジックの使用は、高度で安全ではないシナリオと見なされます。一般的なコンポーネントの開発にはお勧めしません。

コード`RenderTreeBuilder`が記述されている場合、開発者はコードの正確性を保証する必要があります。 たとえば、開発者は次のことを確認する必要があります。

* およびへ`OpenElement`の`CloseElement`呼び出しは、正しくバランスが取れています。
* 属性は正しい場所にのみ追加されます。

手動のレンダーツリービルダーのロジックが正しくないと、クラッシュ、サーバーのハング、セキュリティの脆弱性など、任意の未定義の動作が発生する可能性があります。

アセンブリコードまたは MSIL 命令を手作業で記述するのと同じレベルの複雑さと同じレベルの*危険*を考慮して、手動のレンダリングツリービルダーロジックを検討してください。
