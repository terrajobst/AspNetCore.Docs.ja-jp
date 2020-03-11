---
title: ASP.NET Core Web API のカスタム フォーマッタ
author: rick-anderson
description: ASP.NET Core で Web API のカスタム フォーマッタを作成して使用する方法を説明します。
ms.author: riande
ms.date: 02/08/2017
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: dd25cda460ba758cd07de094eaadd1f2d8c28657
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654956"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>ASP.NET Core Web API のカスタム フォーマッタ

著者: [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core MVC は、入力と出力のフォーマッタを使用した Web API でのデータ交換をサポートしています。 入力フォーマッタは、[モデル バインド](xref:mvc/models/model-binding)によって使用されます。 出力フォーマッタは、[応答の書式設定](xref:web-api/advanced/formatting)に使用されます。

フレームワークには、JSON および XML 用の組み込みの入力および出力フォーマッタが用意されています。 プレーン テキスト用の組み込みの出力フォーマッタが用意されていますが、プレーン テキスト用の入力フォーマッタは用意されていません。

この記事では、カスタム フォーマッタを作成して、追加形式のサポートを追加する方法を示します。 プレーン テキスト用のカスタムの入力フォーマッタの例については、GitHub の [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="when-to-use-custom-formatters"></a>カスタム フォーマッタを使用するタイミング

組み込みフォーマッタでサポートされていないコンテンツの種類を[コンテンツ ネゴシエーション](xref:web-api/advanced/formatting#content-negotiation) プロセスでサポートする場合は、カスタム フォーマッタを使用します。

たとえば、Web API の一部のクライアントが [Protobuf](https://github.com/google/protobuf) 形式を処理できる場合、より効率的であるため、それらのクライアントで Protobuf を使用することができます。 あるいは、Web API を使用して、[vCard](https://wikipedia.org/wiki/VCard) 形式 (連絡先データを交換する場合に一般的に使用される) で連絡先の名前とアドレスを送信することもできます。 この記事で提供するサンプル アプリでは単純な vCard フォーマッタを実装します。

## <a name="overview-of-how-to-use-a-custom-formatter"></a>カスタム フォーマッタの使用方法の概要

カスタム フォーマッタを作成して使用する手順を以下に示します。

* クライアントに送信するデータをシリアル化する場合は、出力フォーマッタ クラスを作成します。
* クライアントから受信したデータを逆シリアル化する場合は、入力フォーマッタ クラスを作成します。
* フォーマッタのインスタンスを `InputFormatters`MvcOptions`OutputFormatters` の [ および ](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions) コレクションに追加します。

次のセクションでは、これらの各手順のガイダンスとコード例を提供します。

## <a name="how-to-create-a-custom-formatter-class"></a>カスタム フォーマッタ クラスの作成方法

フォーマッタを作成する場合は、次のようにします。

* クラスを適切な基底クラスから派生させます。
* コンストラクターで有効なメディアの種類とエンコーディングを指定します。
* `CanReadType`/`CanWriteType` メソッドのオーバーライド
* `ReadRequestBodyAsync`/`WriteResponseBodyAsync` メソッドのオーバーライド
  
### <a name="derive-from-the-appropriate-base-class"></a>適切な基底クラスからの派生

メディアの種類がテキスト (vCard など) の場合は、[TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) または [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) 基底クラスから派生させます。

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

入力フォーマッタの例として、「[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)」を参照してください。

種類がバイナリである場合は、[InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) または [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) 基底クラスから派生させます。

### <a name="specify-valid-media-types-and-encodings"></a>有効なメディアの種類とエンコーディングの指定

コンストラクターで、`SupportedMediaTypes` および `SupportedEncodings` コレクションに追加して、有効なメディアの種類とエンコーディングを指定します。

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

入力フォーマッタの例として、「[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)」を参照してください。

> [!NOTE]
> フォーマッタ クラスでコンストラクターの依存関係の挿入を行うことはできません。 たとえば、コンストラクターにロガー パラメーターを追加して、ロガーを取得することはできません。 サービスにアクセスするには、メソッドに渡されるコンテキスト オブジェクトを使用する必要があります。 [以下](#read-write)のコード例でこの方法を示します。

### <a name="override-canreadtypecanwritetype"></a>CanReadType/CanWriteType のオーバーライド

`CanReadType` または `CanWriteType` メソッドをオーバーライドして、逆シリアル化またはシリアル化できる種類を指定します。 たとえば、vCard テキストを `Contact` の種類からのみ作成できます (その逆も可)。

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

入力フォーマッタの例として、「[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)」を参照してください。

#### <a name="the-canwriteresult-method"></a>CanWriteResult メソッド

一部のシナリオでは、`CanWriteResult` の代わりに `CanWriteType` をオーバーライドする必要があります。 次のすべての条件が満たされている場合は、`CanWriteResult` を使用します。

* アクション メソッドはモデル クラスを返します。
* 実行時に返される可能性がある派生クラスがあります。
* アクションが返した派生クラスを実行時に把握する必要があります。

たとえば、アクション メソッド シグネチャが `Person` の種類を返したとします。ただし、この場合、`Student` から派生した `Instructor` または `Person` の種類を返す可能性があります。 フォーマッタで `Student` オブジェクトのみを処理する場合は、[ メソッドに提供されたコンテキスト オブジェクトで ](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object)Object`CanWriteResult` の種類を確認します。 アクション メソッドが `CanWriteResult` を返す場合は、`IActionResult` を使用する必要がないことに注意してください。この場合、`CanWriteType` メソッドはランタイム型を受け取ります。

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a>ReadRequestBodyAsync/WriteResponseBodyAsync のオーバーライド

`ReadRequestBodyAsync` または `WriteResponseBodyAsync` で実際に逆シリアル化またはシリアル化を行います。 次の例で強調表示されている行は、依存関係の挿入コンテナーからサービスを取得する方法を示しています (コンストラクター パラメーターからは取得できません)。

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

入力フォーマッタの例として、「[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)」を参照してください。

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>カスタム フォーマッタを使用するように MVC を構成する方法

カスタム フォーマッタを使用するには、`InputFormatters` または `OutputFormatters` コレクションにフォーマッタ クラスのインスタンスを追加します。

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

フォーマッタは、挿入した順序で評価されます。 最初のものが優先されます。

## <a name="next-steps"></a>次のステップ:

* [このドキュメント用のサンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)。このアプリを使用して、シンプルな vCard の入力と出力フォーマッタを実装します。 アプリでは、次の例のように vCard の読み取りと書き込みを行います。

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

vCard の出力を表示するには、アプリケーションを実行し、Accept ヘッダー "text/vcard" を指定して Get 要求を `http://localhost:63313/api/contacts/` (Visual Studio から実行する場合) または `http://localhost:5000/api/contacts/` (コマンドラインから実行する場合) に送信します。

連絡先のメモリ内コレクションに vCard を追加するには、本文に vCard テキストを指定し、Content-Type ヘッダー"text/vcard" を指定して、同じ URL に Post 要求を送信します (書式は前述の例と同じ)。
