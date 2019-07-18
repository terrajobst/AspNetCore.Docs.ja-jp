---
title: ASP.NET Core MVC アプリへの検証の追加
author: rick-anderson
description: ASP.NET Core アプリに検証を追加する方法
ms.author: riande
ms.date: 04/13/2017
uid: tutorials/first-mvc-app/validation
ms.openlocfilehash: f94d1003732b8ff04ec8aba3005f8c95a876cd67
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815119"
---
# <a name="add-validation-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC アプリへの検証の追加

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

このセクションの内容:

* ここでは、`Movie` モデルに検証ロジックを追加します。
* 検証規則は、ユーザーがムービーを作成または編集するときに、いつでも確実に適用できるようにします。

## <a name="keeping-things-dry"></a>DRY に維持する

MVC の設計の基本思想の 1 つは [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("Don't Repeat Yourself") です。 ASP.NET Core MVC では、機能や動作を 1 回だけ指定した後、それをアプリ内のすべての場所に反映することが奨励されます。 このようにすることで、記述する必要のあるコードの量が減り、作成したコードはエラーが発生しにくく、テストがしやすく、保守が容易になります。

MVC と Entity Framework Core Code First が提供している検証のサポートは、動作している DRY 原則の好例です。 検証規則は、1 つの場所 (モデル クラス内) で宣言的に指定でき、アプリのすべての場所で適用されます。

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

## <a name="validation-error-ui"></a>検証エラー UI

アプリを実行し、Movies コントローラーに移動します。

**[Create New]\(新規作成\)** リンクをタップして、新しいムービーを追加します。 フォームに無効な値をいくつか入力します。 jQuery クライアント側の検証でエラーが検出されるとすぐに、エラー メッセージが表示されます。

![複数の jQuery クライアント側検証エラーのあるムービー ビュー フォーム](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

無効な値を含む各フィールドに、適切な検証エラー メッセージが自動的に表示されることがわかります。 エラーは、(JavaScript と jQuery を使用している) クライアント側とサーバー側 (ユーザーが JavaScript を無効にしている場合) の両方に適用されます。

重要な利点は、この検証 UI を有効にするために、`MoviesController` クラスまたは *Create.cshtml* ビューのコードを 1 行も変更する必要がないことです。 このチュートリアルで前に作成したコントローラーとビューにより、`Movie` モデル クラスのプロパティで検証属性を使って指定した検証規則が自動的に取得されます。 `Edit` アクション メソッドを使って検証をテストします。同じ検証が適用されます。

クライアント側の検証エラーがなくなるまで、フォーム データはサーバーに送信されません。 このことは、[Fiddler ツール](https://www.telerik.com/fiddler) または [F12 開発者ツール](/microsoft-edge/devtools-guide)を使って `HTTP Post` メソッドにブレークポイントを設定することにより確認できます。

## <a name="how-validation-works"></a>検証の動作方法

コントローラーまたはビューのコードを更新しなくても検証 UI が生成する仕組みが気になるかもしれません。 次のコードでは、2 つの `Create` メソッドが示されています。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

最初の (HTTP GET の) `Create` アクション メソッドは、初期の作成フォームを表示します。 2 番目の (`[HttpPost]`) バージョンは、フォームの送信を処理します。 2 番目の `Create` メソッド (`[HttpPost]` バージョン) は、`ModelState.IsValid` を呼び出してムービーに検証エラーがあるかどうかを確認します。 このメソッドを呼び出すと、オブジェクトに適用されているすべての検証属性が評価されます。 オブジェクトに検証エラーがある場合、`Create` メソッドはフォームを再表示します。 エラーがない場合、メソッドはデータベースに新しいムービーを保存します。 このムービーの例では、クライアント側で検証エラーが検出されると、フォームはサーバーに送信されません。クライアント側検証エラーがある場合、2 番目の `Create` メソッドは呼び出されません。 ブラウザーで JavaScript を無効にすると、クライアントの検証が無効になり、HTTP POST の `Create` メソッドの `ModelState.IsValid` での検証エラーの検出をテストできます。

`[HttpPost] Create` メソッドにブレークポイントを設定し、メソッドが呼び出されないことを確認できます。検証エラーが検出された場合、クライアント側の検証はフォームのデータを送信しません。 ブラウザーで JavaScript を無効にすると、エラーのあるフォームが送信され、ブレークポイントがヒットします。 JavaScript がなくても完全な検証が行われます。 

次の図では、FireFox ブラウザーで JavaScript を無効にする方法を示します。

![Firefox: [オプション] の [コンテンツ] タブで、[JavaScript を有効にする] チェック ボックスをオフにします。](~/tutorials/first-mvc-app/validation/_static/ff.png)

次の図では、Chrome ブラウザーで JavaScript を無効にする方法を示します。

![Google Chrome:[コンテンツの設定] の [JavaScript] セクションで、[Do not allow any site to run JavaScript]\(すべてのサイトで JavaScript の実行を許可しない\) をオンにします。](~/tutorials/first-mvc-app/validation/_static/chrome.png)

JavaScript を無効にした後、無効なデータを送信して、デバッガーをステップ実行します。

![無効なデータの送信のデバッグ中に、ModelState.IsValid の Intellisense で値が false であることが示されます。](~/tutorials/first-mvc-app/validation/_static/ms.png)

次のマークアップに *Create.cshtml* ビュー テンプレートの部分が表示されます。

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/CreateRatingBrevity.html)]

上のマークアップは、アクション メソッドで、初期フォームの表示と、エラー発生時のフォームの再表示に使われます。

[入力タグ ヘルパー](xref:mvc/views/working-with-forms)は [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性を使い、クライアント側での jQuery 検証に必要な HTML 属性を生成します。 [検証タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-tag-helpers)には検証エラーが表示されます。 詳しくは、[検証に関する記事](xref:mvc/models/validation)をご覧ください。

この方法の非常によい点は、コントローラーも `Create` ビュー テンプレートも、適用される実際の検証規則や、表示される特定のエラー メッセージについて、何も知らないことです。 検証規則とエラー文字列は、`Movie` クラスでのみ指定されています。 同じ検証規則が、`Edit` ビューおよびモデルを編集する他のユーザー作成のビュー テンプレートに、自動的に適用されます。

検証ロジックを変更する必要があるときは、モデル (この例では `Movie` クラス) に検証属性を追加するだけで行うことができます。 アプリケーションの異なる部分で規則の適用方法が一貫しない可能性を心配する必要はありません。すべての検証ロジックは 1 か所で定義され、すべての場所で使われます。 これにより、コードの簡潔さが保たれ、簡単に維持や更新できます。 また、これは DRY 原則に完全に従うことを意味します。

## <a name="using-datatype-attributes"></a>DataType 属性の使用

*Movie.cs* ファイルを開き、`Movie` クラスを調べます。 `System.ComponentModel.DataAnnotations` 名前空間には、組み込みの検証属性セットに加え、書式設定の属性もあります。 リリース日と価格のフィールドには、`DataType` 列挙値が既に適用されています。 次のコードでは、適切な `DataType` 属性が設定された `ReleaseDate` プロパティと `Price` プロパティを示します。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`DataType` 属性は、ビュー エンジンに対して、データの書式設定のヒントのみを提供します (また、URL の場合に `<a>`、電子メールの場合に `<a href="mailto:EmailAddress.com">` などの要素/属性を提供します)。 `RegularExpression` 属性を使って、データの書式を検証することができます。 `DataType` 属性は、データベースの組み込み型よりも具体的なデータ型を指定するために使用されます。これらは検証属性ではありません。 この例では、追跡する必要があるのは日付のみであり、時刻は必要ありません。 `DataType` 列挙型は、Date、Time、PhoneNumber、Currency、EmailAddress など、多くの型のために用意されています。 また、`DataType` 属性を使用して、アプリケーションで型固有の機能を自動的に提供することもできます。 たとえば、`mailto:` リンクを `DataType.EmailAddress` に作成したり、HTML5 をサポートするブラウザーで `DataType.Date` に日付セレクターを提供したりできます。 `DataType` 属性は、HTML 5 ブラウザーが認識できる HTML 5 `data-` ("データ ダッシュ" と読みます) 属性を出力します。 `DataType` 属性は、検証を**提供していません**。

`DataType.Date` は、表示される日付の書式を指定しません。 既定で、日付フィールドはサーバーの `CultureInfo` に基づき、既定の書式に従って表示されます。

`DisplayFormat` 属性は、日付の書式を明示的に指定するために使用されます。

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode` の設定では、編集用にテキスト ボックスに値を表示するときにも適用する必要がある書式設定を指定します (フィールドによっては適用したくないこともあります。たとえば、通貨値の場合、おそらく編集用テキスト ボックスに通貨記号は必要ありません)。

`DisplayFormat` 属性を単独で使うことができますが、一般的に、`DataType` 属性を使うことが推奨されます。 `DataType` 属性は、画面でのレンダリング方法とは異なり、データのセマンティクスを伝達します。また、DisplayFormat にはない、次の利点があります。

* ブラウザーは HTML5 機能を有効にすることができます (たとえば、カレンダー コントロール、ロケールに適した通貨記号、電子メール リンクを表示するときなど)。

* ブラウザーの既定では、ロケールに基づいて正しい書式を使ってデータがレンダリングされます。

* `DataType` 属性により、MVC はデータを表示するために正しいフィールド テンプレートを選ぶことができます (`DisplayFormat` を単独で使う場合は、文字列テンプレートを使います)。

> [!NOTE]
> jQuery の検証は、`Range` 属性と `DateTime` では機能しません。 たとえば、次のコードでは、指定した範囲内の日付であっても、クライアント側の検証エラーが常に表示されます。
>
> `[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]`

`DateTime` で `Range` 属性を使うには、jQuery の日付検証を無効にする必要があります。 一般的に、モデルに日付をハードコーディングしてコンパイルすることは推奨されません。そのため、`Range` 属性と `DateTime` の使用は推奨されません。

次のコードは、1 行で複数の属性を組み合わせる例です。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

このシリーズの次のパートでは、アプリを確認し、自動的に生成される `Details` および `Delete` メソッドに対していくつかの改良を行います。

## <a name="additional-resources"></a>その他の技術情報

* [フォームの操作](xref:mvc/views/working-with-forms)
* [グローバライズとローカライズ](xref:fundamentals/localization)
* [Tag Helpers の概要](xref:mvc/views/tag-helpers/intro)
* [タグ ヘルパーの作成](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> [前へ](new-field.md)
> [次へ](details.md)  
