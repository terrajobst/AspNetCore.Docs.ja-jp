---
title: ASP.NET Core Web API における Json パッチ
author: tdykstra
description: ASP.NET Core Web API において JSON パッチ要求を処理する方法について説明します。
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/24/2019
uid: web-api/jsonpatch
ms.openlocfilehash: 97264903d85dbb397e85fdbf7b070e2aaae74bc8
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815549"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a>ASP.NET Core Web API における Json パッチ

著者: [Tom Dykstra](https://github.com/tdykstra)

この記事では、ASP.NET Core Web API において JSON パッチ要求を処理する方法について説明します。

## <a name="patch-http-request-method"></a>PATCH HTTP 要求メソッド

PUT および [PATCH](https://tools.ietf.org/html/rfc5789) メソッドは、既存のリソースを更新するために使用されます。 両者の違いは、PUT ではリソース全体を置き換えるのに対して、PATCH では変更箇所だけを指定することです。

## <a name="json-patch"></a>JSON パッチ

[JSON パッチ](https://tools.ietf.org/html/rfc6902)は、リソースに適用される更新を指定するための形式です。 JSON パッチ ドキュメントには、"*操作*" の配列が含まれます。 各操作では、配列要素の追加やプロパティ値の置換など、特定の種類の変更を識別します。

たとえば、次の JSON ドキュメントは、1 つのリソースとそのリソースに対応する JSON パッチ ドキュメント、およびパッチ操作を適用した結果を表しています。

### <a name="resource-example"></a>リソースの例

[!code-csharp[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>JSON パッチの例

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

上記の JSON では、次のように指定されています。

* `op` プロパティでは、操作の種類を指示します。
* `path` プロパティでは、更新する要素を指示します。
* `value` プロパティでは、新しい値を指定します。

### <a name="resource-after-patch"></a>パッチ後のリソース

上記の JSON パッチ ドキュメントを適用した後のリソースを、以下に示します。

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

JSON パッチ ドキュメントをリソースに適用することで行われる変更は、アトミックです。つまり、リスト内の任意の操作が失敗した場合は、リスト内のどの操作も適用されません。

## <a name="path-syntax"></a>パス構文

操作オブジェクトの [path](https://tools.ietf.org/html/rfc6901) プロパティでは、レベル間にスラッシュを保持します。 たとえば、`"/address/zipCode"` のようにします。

0 から始まるインデックスは、配列の要素を指定するために使用されます。 `addresses` 配列の最初の要素は、`/addresses/0` にあります。 配列の末尾への `add` では、インデックス番号ではなく、`/addresses/-` のようにハイフン (-) を使用します。

### <a name="operations"></a>オペレーション

次の表は、[JSON パッチの仕様](https://tools.ietf.org/html/rfc6902)に定義されている、サポートされる操作を示しています。

|操作  | メモ |
|-----------|--------------------------------|
| `add`     | プロパティまたは配列要素を追加します。 既存のプロパティの場合: 値を設定します。|
| `remove`  | プロパティまたは配列要素を削除します。 |
| `replace` | `remove` の後に、同じ場所で `add` が続く場合と同じです。 |
| `move`    | ソースからの `remove` の後に、ソースからの値を使用した宛先への `add` が続く場合と同じです。 |
| `copy`    | ソースからの値を使用した宛先への `add` と同じです。 |
| `test`    | `path` の値が指定された `value`と一致する場合に、成功の状態コードを返します。|

## <a name="jsonpatch-in-aspnet-core"></a>ASP.NET Core における JSON パッチ

JSON パッチの ASP.NET Core 実装は、[microsoft.aspnetcore.jsonpatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet パッケージ内に提供されています。 パッケージは、[Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) メタパッケージに含まれています。

## <a name="action-method-code"></a>アクション メソッド コード

API コントローラーにおける JSON パッチ用のアクション メソッド:

* `HttpPatch` 属性によって注釈されます。
* 通常は [FromBody] を利用して、`JsonPatchDocument<T>` を受け入れます。
* パッチ ドキュメント上の `ApplyTo` を呼び出して、変更を適用します。

次に例を示します。

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

サンプル アプリからのこのコードでは、以下の `Customer` モデルを利用します。

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

同じアクション メソッド:

* `Customer` を構築します。
* パッチを適用します
* 応答の本文内で結果を返します。

 実際のアプリでは、コードはデータベースなどの保存場所からデータを取得し、パッチを適用した後にデータベースを更新します。

### <a name="model-state"></a>モデルの状態

前のアクション メソッドの例では、パラメーターの 1 つとしてモデルの状態を取得する `ApplyTo` のオーバーロードを呼び出しています。 このオプションを利用すると、応答内にエラー メッセージを取得できます。 次の例では、`test` 操作に対する 400 Bad Request 応答の本文を示しています。

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>動的オブジェクト

次のアクション メソッドの例では、動的オブジェクトにパッチを適用する方法を示しています。

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>追加の操作

* `path` が配列要素を参照する場合: `path` によって指定された要素の前に新しい要素を挿入します。
* `path` がプロパティを参照する場合: プロパティ値を設定します。
* `path` が存在しない場所を参照する場合:
  * パッチへのリソースが動的オブジェクトの場合: プロパティを追加します。
  * パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。

次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Order` オブジェクトを `Orders` 配列の末尾に追加します。

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>削除の操作

* `path` が配列要素を参照する場合: 配列を削除します。
* `path` がプロパティを参照する場合:
  * パッチへのリソースが動的オブジェクトの場合: プロパティを削除します。
  * パッチへのリソースが静的オブジェクトの場合: 
    * プロパティが null 値を許容する場合: null を設定します。
    * プロパティが null 値を許容しない場合: `default<T>` を設定します。

次のサンプル パッチ ドキュメントでは、`CustomerName` に null を設定し、`Orders[0]` を削除します。

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>置換の操作

この操作は、`add` が後に続く `remove` と機能的に同じです。

次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Orders[0]` を新しい `Order` オブジェクトに置き換えます。

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>移動の操作

* `path` が配列要素を参照する場合: `from` 要素を `path` 要素の場所にコピーしてから、`from` 要素に対して `remove` 操作を実行します。
* `path` がプロパティを参照する場合: `from` プロパティの値を `path` プロパティにコピーしてから、`from` プロパティに対して `remove` 操作を実行します。
* `path` が存在しないプロパティを参照する場合:
  * パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。
  * パッチへのリソースが動的オブジェクトの場合: `from` プロパティを `path`によって指示された場所にコピーしてから、`from` プロパティに対して `remove` 操作を実行します。

次のサンプル パッチ ドキュメントでは、以下の操作を行います。

* `Orders[0].OrderName` の値を `CustomerName` にコピーします。
* `Orders[0].OrderName` に null を設定します。
* `Orders[1]` を `Orders[0]` の前に移動します。

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>コピー操作

この操作は、最後の `remove` 手順がない `move` 操作と機能的に同じです。

次のサンプル パッチ ドキュメントでは、以下の操作を行います。

* `Orders[0].OrderName` の値を `CustomerName` にコピーします。
* `Orders[1]` のコピーを `Orders[0]` の前に挿入します。

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>テストの操作

`path` によって指示された場所にある値が `value` に指定されている値と異なる場合、要求は失敗します。 その場合は、パッチ ドキュメントにあるその他すべての操作が成功したとしても、PATCH 要求全体が失敗します。

`test` 操作は、同時実行の競合がある場合に、一般的に更新を防ぐために使用されます。

`CustomerName` の初期値が "John" の場合、テストは失敗するため、次のサンプル パッチ ドキュメントは無効です。

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>コードを取得する

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)します。 ([ダウンロード方法](xref:index#how-to-download-a-sample))。

サンプルをテストするには、アプリを実行して、次の設定を使って HTTP 要求を送信します。

* URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* HTTP メソッド: `PATCH`
* ヘッダー: `Content-Type: application/json-patch+json`
* 本文:*JSON* プロジェクト フォルダーから JSON パッチ ドキュメント サンプルの 1 つをコピーして貼り付けます。

## <a name="additional-resources"></a>その他の技術情報

* [IETF RFC 5789 PATCH メソッドの仕様](https://tools.ietf.org/html/rfc5789)
* [IETF RFC 6902 JSON パッチの仕様](https://tools.ietf.org/html/rfc6902)
* [IETF RFC 6901 JSON パッチのパス形式の仕様](https://tools.ietf.org/html/rfc6901)
* [JSON パッチ ドキュメント](https://jsonpatch.com/)。 JSON パッチ ドキュメントを作成するためのリソースへのリンクを含みます。
* [ASP.NET Core JSON パッチのソース コード](https://github.com/aspnet/AspNetCore/tree/master/src/Features/JsonPatch/src)
