---
title: ASP.NET Core MVC でコントローラーで要求を処理する
author: ardalis
description: ''
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/actions
ms.openlocfilehash: 715a73863513870d1cbd522e75013d41830da1e7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653348"
---
# <a name="handle-requests-with-controllers-in-aspnet-core-mvc"></a>ASP.NET Core MVC でコントローラーで要求を処理する

作成者: [Steve Smith](https://ardalis.com/)、[Scott Addie](https://github.com/scottaddie)

コントローラー、アクション、アクションの結果は、開発者が ASP.NET Core MVC を利用してアプリをビルドするときの基本的な部分です。

## <a name="what-is-a-controller"></a>コントローラーとは何か。

コントローラーは一連のアクションを定義し、グループ化するために使用されます。 アクション (または*アクション メソッド*) は、要求を処理するコントローラーのメソッドです。 コントローラーは、同様のアクションを論理的にグループ化します。 アクションをこのように集めることで、ルーティング、キャッシュ、承認など、一連の共通ルールをまとめて適用できます。 要求は[ルーティング](xref:mvc/controllers/routing)を介してアクションにマッピングされます。

コントローラー クラスは慣例で

* プロジェクトのルートレベル *[コントローラー]* フォルダーに置かれます。
* `Microsoft.AspNetCore.Mvc.Controller` から継承します。

コントローラーはインスタンス化が可能なクラスであり、次の条件の少なくとも 1 つが当てはまります。

* クラス名に `Controller` というサフィックスが付く。
* クラスが名前に `Controller` というサフィックスが付くクラスから継承する。
* `[Controller]` 属性がクラスに適用される。

コントローラー クラスには、`[NonController]` 属性を関連付けないでください。

コントローラーは [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) に従う必要があります。 この原則を実装するための手法がいくつかあります。 複数のコントローラー アクションに同じサービスが必要な場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)で依存関係を要求する方法を検討してください。 サービスを必要とするアクション メソッドが 1 つだけの場合、[アクションの挿入](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)で依存関係を要求する方法を検討してください。

**M**odel-**V**iew-**C**ontroller パターン内では、コントローラーは要求の初回処理とモデルのインスタンス化を担います。 一般的に、ビジネスの決定はモデル内で実行するべきです。

コントローラーはモデルの処理の結果を受け取り (結果があれば)、適切なビューとそれに関連付けられるビュー データを返すか、API 呼び出しの結果を返します。 詳細については、「[Overview of ASP.NET Core MVC](xref:mvc/overview)」(ASP.NET Core MVC の概要) と「[ASP.NET Core MVC と Visual Studio の概要](xref:tutorials/first-mvc-app/start-mvc)」を参照してください。

コントローラーは *UI レベル*の抽象化です。 その役目は、要求データの有効性を確保することと返すビュー (あるいは、API の結果) を選択することです。 要素が十分に考慮されたアプリの場合、データ アクセスやビジネス ロジックは直接含まれません。 そうではなく、コントローラーはサービスにそのような役目を委任します。

## <a name="defining-actions"></a>アクションの定義

`[NonAction]` 属性が設定されているものを除き、コントローラーのパブリック メソッドはアクションです。 アクションのパラメーターは要求データにバインドされ、[モデル バインド](xref:mvc/models/model-binding)により検証されます。 モデル検証は、モデル バインドされているすべてに実行されます。 プロパティ値 `ModelState.IsValid` は、モデルのバインドと検証に成功したかどうかを示します。

アクション メソッドには、要求をビジネスにマッピングするためのロジックを含めてください。 ビジネスは通常、コントローラーが[依存関係挿入](xref:mvc/controllers/dependency-injection)を経由してアクセスするサービスとして表されます。 アクションはビジネス アクションの結果をアプリケーションの状態にマッピングします。

アクションは何でも返すことができますが、多くの場合、応答を生成する `IActionResult` のインスタンス (あるいは、非同期メソッドの `Task<IActionResult>`) を返します。 アクション メソッドは、*応答の種類*の選択を担います。 アクションの結果は*応答を行いません*。

### <a name="controller-helper-methods"></a>コントローラー ヘルパー メソッド

コントローラーは通常、[コントローラー](/dotnet/api/microsoft.aspnetcore.mvc.controller)から継承します。ただし、これは必須ではありません。 `Controller` から派生することで、次の 3 つのカテゴリのヘルパー メソッドにアクセスできます。

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1. 空の応答本文が生成されたメソッド

`Content-Type` HTTP 応答ヘッダーが含まれません。応答の本文に記述するコンテンツがないためです。

このカテゴリの中に、リダイレクトと HTTP 状態コードという 2 つの種類の結果があります。

* **HTTP 状態コード**

    この型は HTTP 状態コードを返します。 この種類のヘルパー メソッドには、`BadRequest`、`NotFound`、`Ok` などがあります。 たとえば、`return BadRequest();` の場合、実行時に 400 状態コードが生成されます。 `BadRequest`、`NotFound`、`Ok` などのメソッドがオーバーロードされると、HTTP ステータス コードのレスポンダーでなくなります。コンテンツ ネゴシエーションが発生するためです。

* **リダイレクト**

    この種類では、アクションまたは宛先にリダイレクトが返されます (`Redirect`、`LocalRedirect`、`RedirectToAction`、`RedirectToRoute` を利用して)。 たとえば、`return RedirectToAction("Complete", new {id = 123});` は `Complete` にリダイレクトし、匿名のオブジェクトを渡します。

    リダイレクトの結果の種類は HTTP ステータス コードの種類とは、`Location` HTTP 応答ヘッダーを追加するという点で主に異なります。

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2. 定義済みのコンテンツタイプを持つ空でない応答本文が生成されるメソッド

このカテゴリのヘルパー メソッドの多くには `ContentType` プロパティが含まれ、`Content-Type` 応答ヘッダーを設定し、応答本文を記述できます。

このカテゴリには 2 種類の結果があります。[ビュー](xref:mvc/views/overview)と[書式設定された応答](xref:web-api/advanced/formatting)です。

* **表示**

    この種類では、モデルを利用して HTML をレンダリングするビューが返されます。 たとえば、`return View(customer);` はデータ バインディングのビューにモデルを渡します。

* **書式設定された応答**

    この種類では、JSON または同様のデータ交換形式を返し、特定の手法でオブジェクトを表します。 たとえば、`return Json(customer);` の場合、与えられたオブジェクトを JSON 形式にシリアル化します。
    
    この種類の一般的なメソッドには他に `File`、`PhysicalFile` などがあります。 たとえば、`return PhysicalFile(customerFilePath, "text/xml");` は [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult) を返します。

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3. クライアントとネゴシエートされるコンテンツの種類で書式設定された、空でない応答本文が生成されるメソッド

このカテゴリは、**コンテンツ ネゴシエーション**として知られています。 [コンテンツ ネゴシエーション](xref:web-api/advanced/formatting#content-negotiation)は、アクションが [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) 型を返すか、[IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) 実装以外の何かを返すときに適用されます。 非 `IActionResult` の実装 (`object` など) を返すアクションは、書式設定された応答も返します。

この種類のヘルパー メソッドには `BadRequest`、`CreatedAtRoute`、`Ok` などがあります。 このようなメソッドの例として、それぞれ `return BadRequest(modelState);`、`return CreatedAtRoute("routename", values, newobject);`、`return Ok(value);` があります。 `BadRequest` と `Ok` は、値が渡されたときにのみ、コンテンツ ネゴシエーションを実行します。値が渡されなければ、代わりに HTTP ステータス コードという結果の種類としてサービスを提供します。 一方、`CreatedAtRoute` メソッドは常にコンテンツ ネゴシエーションを実行します。そのあらゆるオーバーロードにおいて、値を渡すことが必要になるためです。

### <a name="cross-cutting-concerns"></a>横断的な問題

アプリケーションは通常、ワークフローの一部を共有します。 たとえば、ショッピング カートにアクセスする際、認証を要求するアプリや一部のページでデータをキャッシュするアプリです。 アクション メソッドの前または後でロジックを実行するには、*フィルター*を使用します。 横断的な問題に対して[フィルター](xref:mvc/controllers/filters)を使うと、重複を減らすことができます。

`[Authorize]` など、フィルター属性の多くは、求められる詳細度に基づき、コントローラーまたはアクション レベルで適用できます。

エラー処理と応答キャッシュは多くの場合、横断的な問題です。
* [エラーを処理する](xref:mvc/controllers/filters#exception-filters)
* [応答キャッシュ](xref:performance/caching/response)

横断的な問題の多くはフィルターやカスタム [ミドルウェア](xref:fundamentals/middleware/index)の利用で処理できます。
