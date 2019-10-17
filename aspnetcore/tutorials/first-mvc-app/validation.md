---
title: ASP.NET Core MVC アプリへの検証の追加
author: rick-anderson
description: ASP.NET Core アプリに検証を追加する方法
ms.author: riande
ms.date: 04/13/2017
uid: tutorials/first-mvc-app/validation
ms.openlocfilehash: 2bb4ed173d154e3b7457ce3f8009f0f9406e36c4
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72334072"
---
# <a name="add-validation-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="ffd2b-103">ASP.NET Core MVC アプリへの検証の追加</span><span class="sxs-lookup"><span data-stu-id="ffd2b-103">Add validation to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="ffd2b-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ffd2b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ffd2b-105">このセクションの内容:</span><span class="sxs-lookup"><span data-stu-id="ffd2b-105">In this section:</span></span>

* <span data-ttu-id="ffd2b-106">ここでは、`Movie` モデルに検証ロジックを追加します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-106">Validation logic is added to the `Movie` model.</span></span>
* <span data-ttu-id="ffd2b-107">検証規則は、ユーザーがムービーを作成または編集するときに、いつでも確実に適用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-107">You ensure that the validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="keeping-things-dry"></a><span data-ttu-id="ffd2b-108">DRY に維持する</span><span class="sxs-lookup"><span data-stu-id="ffd2b-108">Keeping things DRY</span></span>

<span data-ttu-id="ffd2b-109">MVC の設計の基本思想の 1 つは [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("Don't Repeat Yourself") です。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-109">One of the design tenets of MVC is [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("Don't Repeat Yourself").</span></span> <span data-ttu-id="ffd2b-110">ASP.NET Core MVC では、機能や動作を 1 回だけ指定した後、それをアプリ内のすべての場所に反映することが奨励されます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-110">ASP.NET Core MVC encourages you to specify functionality or behavior only once, and then have it be reflected everywhere in an app.</span></span> <span data-ttu-id="ffd2b-111">このようにすることで、記述する必要のあるコードの量が減り、作成したコードはエラーが発生しにくく、テストがしやすく、保守が容易になります。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-111">This reduces the amount of code you need to write and makes the code you do write less error prone, easier to test, and easier to maintain.</span></span>

<span data-ttu-id="ffd2b-112">MVC と Entity Framework Core Code First が提供している検証のサポートは、動作している DRY 原則の好例です。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-112">The validation support provided by MVC and Entity Framework Core Code First is a good example of the DRY principle in action.</span></span> <span data-ttu-id="ffd2b-113">検証規則は、1 つの場所 (モデル クラス内) で宣言的に指定でき、アプリのすべての場所で適用されます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-113">You can declaratively specify validation rules in one place (in the model class) and the rules are enforced everywhere in the app.</span></span>

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

## <a name="validation-error-ui"></a><span data-ttu-id="ffd2b-114">検証エラー UI</span><span class="sxs-lookup"><span data-stu-id="ffd2b-114">Validation Error UI</span></span>

<span data-ttu-id="ffd2b-115">アプリを実行し、Movies コントローラーに移動します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-115">Run the app and navigate to the Movies controller.</span></span>

<span data-ttu-id="ffd2b-116">**[Create New]\(新規作成\)** リンクをタップして、新しいムービーを追加します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-116">Tap the **Create New** link to add a new movie.</span></span> <span data-ttu-id="ffd2b-117">フォームに無効な値をいくつか入力します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-117">Fill out the form with some invalid values.</span></span> <span data-ttu-id="ffd2b-118">jQuery クライアント側の検証でエラーが検出されるとすぐに、エラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-118">As soon as jQuery client side validation detects the error, it displays an error message.</span></span>

![複数の jQuery クライアント側検証エラーのあるムービー ビュー フォーム](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="ffd2b-120">無効な値を含む各フィールドに、適切な検証エラー メッセージが自動的に表示されることがわかります。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-120">Notice how the form has automatically rendered an appropriate validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="ffd2b-121">エラーは、(JavaScript と jQuery を使用している) クライアント側とサーバー側 (ユーザーが JavaScript を無効にしている場合) の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-121">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (in case a user has JavaScript disabled).</span></span>

<span data-ttu-id="ffd2b-122">重要な利点は、この検証 UI を有効にするために、`MoviesController` クラスまたは *Create.cshtml* ビューのコードを 1 行も変更する必要がないことです。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-122">A significant benefit is that you didn't need to change a single line of code in the `MoviesController` class or in the *Create.cshtml* view in order to enable this validation UI.</span></span> <span data-ttu-id="ffd2b-123">このチュートリアルで前に作成したコントローラーとビューにより、`Movie` モデル クラスのプロパティで検証属性を使って指定した検証規則が自動的に取得されます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-123">The controller and views you created earlier in this tutorial automatically picked up the validation rules that you specified by using validation attributes on the properties of the `Movie` model class.</span></span> <span data-ttu-id="ffd2b-124">`Edit` アクション メソッドを使って検証をテストします。同じ検証が適用されます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-124">Test validation using the `Edit` action method, and the same validation is applied.</span></span>

<span data-ttu-id="ffd2b-125">クライアント側の検証エラーがなくなるまで、フォーム データはサーバーに送信されません。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-125">The form data isn't sent to the server until there are no client side validation errors.</span></span> <span data-ttu-id="ffd2b-126">このことは、[Fiddler ツール](https://www.telerik.com/fiddler) または [F12 開発者ツール](/microsoft-edge/devtools-guide)を使って `HTTP Post` メソッドにブレークポイントを設定することにより確認できます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-126">You can verify this by putting a break point in the `HTTP Post` method, by using the [Fiddler tool](https://www.telerik.com/fiddler) , or the [F12 Developer tools](/microsoft-edge/devtools-guide).</span></span>

## <a name="how-validation-works"></a><span data-ttu-id="ffd2b-127">検証の動作方法</span><span class="sxs-lookup"><span data-stu-id="ffd2b-127">How validation works</span></span>

<span data-ttu-id="ffd2b-128">コントローラーまたはビューのコードを更新しなくても検証 UI が生成する仕組みが気になるかもしれません。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-128">You might wonder how the validation UI was generated without any updates to the code in the controller or views.</span></span> <span data-ttu-id="ffd2b-129">次のコードでは、2 つの `Create` メソッドが示されています。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-129">The following code shows the two `Create` methods.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

<span data-ttu-id="ffd2b-130">最初の (HTTP GET の) `Create` アクション メソッドは、初期の作成フォームを表示します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-130">The first (HTTP GET) `Create` action method displays the initial Create form.</span></span> <span data-ttu-id="ffd2b-131">2 番目の (`[HttpPost]`) バージョンは、フォームの送信を処理します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-131">The second (`[HttpPost]`) version handles the form post.</span></span> <span data-ttu-id="ffd2b-132">2 番目の `Create` メソッド (`[HttpPost]` バージョン) は、`ModelState.IsValid` を呼び出してムービーに検証エラーがあるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-132">The second `Create` method (The `[HttpPost]` version) calls `ModelState.IsValid` to check whether the movie has any validation errors.</span></span> <span data-ttu-id="ffd2b-133">このメソッドを呼び出すと、オブジェクトに適用されているすべての検証属性が評価されます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-133">Calling this method evaluates any validation attributes that have been applied to the object.</span></span> <span data-ttu-id="ffd2b-134">オブジェクトに検証エラーがある場合、`Create` メソッドはフォームを再表示します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-134">If the object has validation errors, the `Create` method re-displays the form.</span></span> <span data-ttu-id="ffd2b-135">エラーがない場合、メソッドはデータベースに新しいムービーを保存します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-135">If there are no errors, the method saves the new movie in the database.</span></span> <span data-ttu-id="ffd2b-136">このムービーの例では、クライアント側で検証エラーが検出されると、フォームはサーバーに送信されません。クライアント側検証エラーがある場合、2 番目の `Create` メソッドは呼び出されません。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-136">In our movie example, the form isn't posted to the server when there are validation errors detected on the client side; the second `Create` method is never called when there are client side validation errors.</span></span> <span data-ttu-id="ffd2b-137">ブラウザーで JavaScript を無効にすると、クライアントの検証が無効になり、HTTP POST の `Create` メソッドの `ModelState.IsValid` での検証エラーの検出をテストできます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-137">If you disable JavaScript in your browser, client validation is disabled and you can test the HTTP POST `Create` method `ModelState.IsValid` detecting any validation errors.</span></span>

<span data-ttu-id="ffd2b-138">`[HttpPost] Create` メソッドにブレークポイントを設定し、メソッドが呼び出されないことを確認できます。検証エラーが検出された場合、クライアント側の検証はフォームのデータを送信しません。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-138">You can set a break point in the `[HttpPost] Create` method and verify the method is never called, client side validation won't submit the form data when validation errors are detected.</span></span> <span data-ttu-id="ffd2b-139">ブラウザーで JavaScript を無効にすると、エラーのあるフォームが送信され、ブレークポイントがヒットします。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-139">If you disable JavaScript in your browser, then submit the form with errors, the break point will be hit.</span></span> <span data-ttu-id="ffd2b-140">JavaScript がなくても完全な検証が行われます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-140">You still get full validation without JavaScript.</span></span> 

<span data-ttu-id="ffd2b-141">次の図では、FireFox ブラウザーで JavaScript を無効にする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-141">The following image shows how to disable JavaScript in the FireFox browser.</span></span>

![Firefox: [オプション] の [コンテンツ] タブで、[JavaScript を有効にする] チェック ボックスをオフにします。](~/tutorials/first-mvc-app/validation/_static/ff.png)

<span data-ttu-id="ffd2b-143">次の図では、Chrome ブラウザーで JavaScript を無効にする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-143">The following image shows how to disable JavaScript in the Chrome browser.</span></span>

![Google Chrome:[コンテンツの設定] の [JavaScript] セクションで、[Do not allow any site to run JavaScript]\(すべてのサイトで JavaScript の実行を許可しない\) をオンにします。](~/tutorials/first-mvc-app/validation/_static/chrome.png)

<span data-ttu-id="ffd2b-145">JavaScript を無効にした後、無効なデータを送信して、デバッガーをステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-145">After you disable JavaScript, post invalid data and step through the debugger.</span></span>

![無効なデータの送信のデバッグ中に、ModelState.IsValid の Intellisense で値が false であることが示されます。](~/tutorials/first-mvc-app/validation/_static/ms.png)

<span data-ttu-id="ffd2b-147">次のマークアップに *Create.cshtml* ビュー テンプレートの部分が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-147">The portion of the *Create.cshtml* view template is shown in the following markup:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/CreateRatingBrevity.html)]

<span data-ttu-id="ffd2b-148">上のマークアップは、アクション メソッドで、初期フォームの表示と、エラー発生時のフォームの再表示に使われます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-148">The preceding markup is used by the action methods to display the initial form and to redisplay it in the event of an error.</span></span>

<span data-ttu-id="ffd2b-149">[入力タグ ヘルパー](xref:mvc/views/working-with-forms)は [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性を使い、クライアント側での jQuery 検証に必要な HTML 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-149">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client side.</span></span> <span data-ttu-id="ffd2b-150">[検証タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-tag-helpers)には検証エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-150">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="ffd2b-151">詳しくは、[検証に関する記事](xref:mvc/models/validation)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-151">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="ffd2b-152">この方法の非常によい点は、コントローラーも `Create` ビュー テンプレートも、適用される実際の検証規則や、表示される特定のエラー メッセージについて、何も知らないことです。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-152">What's really nice about this approach is that neither the controller nor the `Create` view template knows anything about the actual validation rules being enforced or about the specific error messages displayed.</span></span> <span data-ttu-id="ffd2b-153">検証規則とエラー文字列は、`Movie` クラスでのみ指定されています。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-153">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="ffd2b-154">同じ検証規則が、`Edit` ビューおよびモデルを編集する他のユーザー作成のビュー テンプレートに、自動的に適用されます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-154">These same validation rules are automatically applied to the `Edit` view and any other views templates you might create that edit your model.</span></span>

<span data-ttu-id="ffd2b-155">検証ロジックを変更する必要があるときは、モデル (この例では `Movie` クラス) に検証属性を追加するだけで行うことができます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-155">When you need to change validation logic, you can do so in exactly one place by adding validation attributes to the model (in this example, the `Movie` class).</span></span> <span data-ttu-id="ffd2b-156">アプリケーションの異なる部分で規則の適用方法が一貫しない可能性を心配する必要はありません。すべての検証ロジックは 1 か所で定義され、すべての場所で使われます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-156">You won't have to worry about different parts of the application being inconsistent with how the rules are enforced — all validation logic will be defined in one place and used everywhere.</span></span> <span data-ttu-id="ffd2b-157">これにより、コードの簡潔さが保たれ、簡単に維持や更新できます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-157">This keeps the code very clean, and makes it easy to maintain and evolve.</span></span> <span data-ttu-id="ffd2b-158">また、これは DRY 原則に完全に従うことを意味します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-158">And it means that you'll be fully honoring the DRY principle.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="ffd2b-159">DataType 属性の使用</span><span class="sxs-lookup"><span data-stu-id="ffd2b-159">Using DataType Attributes</span></span>

<span data-ttu-id="ffd2b-160">*Movie.cs* ファイルを開き、`Movie` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-160">Open the *Movie.cs* file and examine the `Movie` class.</span></span> <span data-ttu-id="ffd2b-161">`System.ComponentModel.DataAnnotations` 名前空間には、組み込みの検証属性セットに加え、書式設定の属性もあります。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-161">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="ffd2b-162">リリース日と価格のフィールドには、`DataType` 列挙値が既に適用されています。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-162">We've already applied a `DataType` enumeration value to the release date and to the price fields.</span></span> <span data-ttu-id="ffd2b-163">次のコードでは、適切な `DataType` 属性が設定された `ReleaseDate` プロパティと `Price` プロパティを示します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-163">The following code shows the `ReleaseDate` and `Price` properties with the appropriate `DataType` attribute.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="ffd2b-164">`DataType` 属性は、ビュー エンジンに対して、データの書式設定のヒントのみを提供します (また、URL の場合に `<a>`、電子メールの場合に `<a href="mailto:EmailAddress.com">` などの要素/属性を提供します)。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-164">The `DataType` attributes only provide hints for the view engine to format the data (and supplies elements/attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email.</span></span> <span data-ttu-id="ffd2b-165">`RegularExpression` 属性を使って、データの書式を検証することができます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-165">You can use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="ffd2b-166">`DataType` 属性は、データベースの組み込み型よりも具体的なデータ型を指定するために使用されます。これらは検証属性ではありません。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-166">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type, they're not validation attributes.</span></span> <span data-ttu-id="ffd2b-167">この例では、追跡する必要があるのは日付のみであり、時刻は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-167">In this case we only want to keep track of the date, not the time.</span></span> <span data-ttu-id="ffd2b-168">`DataType` 列挙型は、Date、Time、PhoneNumber、Currency、EmailAddress など、多くの型のために用意されています。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-168">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress and more.</span></span> <span data-ttu-id="ffd2b-169">また、`DataType` 属性を使用して、アプリケーションで型固有の機能を自動的に提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-169">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="ffd2b-170">たとえば、`mailto:` リンクを `DataType.EmailAddress` に作成したり、HTML5 をサポートするブラウザーで `DataType.Date` に日付セレクターを提供したりできます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-170">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="ffd2b-171">`DataType` 属性は、HTML 5 ブラウザーが認識できる HTML 5 `data-` ("データ ダッシュ" と読みます) 属性を出力します。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-171">The `DataType` attributes emit HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="ffd2b-172">`DataType` 属性は、検証を**提供していません**。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-172">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="ffd2b-173">`DataType.Date` は、表示される日付の書式を指定しません。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-173">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="ffd2b-174">既定で、日付フィールドはサーバーの `CultureInfo` に基づき、既定の書式に従って表示されます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-174">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="ffd2b-175">`DisplayFormat` 属性は、日付の書式を明示的に指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-175">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="ffd2b-176">`ApplyFormatInEditMode` の設定では、編集用にテキスト ボックスに値を表示するときにも適用する必要がある書式設定を指定します</span><span class="sxs-lookup"><span data-stu-id="ffd2b-176">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="ffd2b-177">(フィールドによっては適用したくないこともあります。たとえば、通貨値の場合、おそらく編集用テキスト ボックスに通貨記号は必要ありません)。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-177">(You might not want that for some fields — for example, for currency values, you probably don't want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="ffd2b-178">`DisplayFormat` 属性を単独で使うことができますが、一般的に、`DataType` 属性を使うことが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-178">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="ffd2b-179">`DataType` 属性は、画面でのレンダリング方法とは異なり、データのセマンティクスを伝達します。また、DisplayFormat にはない、次の利点があります。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-179">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="ffd2b-180">ブラウザーは HTML5 機能を有効にすることができます (たとえば、カレンダー コントロール、ロケールに適した通貨記号、電子メール リンクを表示するときなど)。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-180">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>

* <span data-ttu-id="ffd2b-181">ブラウザーの既定では、ロケールに基づいて正しい書式を使ってデータがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-181">By default, the browser will render data using the correct format based on your locale.</span></span>

* <span data-ttu-id="ffd2b-182">`DataType` 属性により、MVC はデータを表示するために正しいフィールド テンプレートを選ぶことができます (`DisplayFormat` を単独で使う場合は、文字列テンプレートを使います)。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-182">The `DataType` attribute can enable MVC to choose the right field template to render the data (the `DisplayFormat` if used by itself uses the string template).</span></span>

> [!NOTE]
> <span data-ttu-id="ffd2b-183">jQuery の検証は、`Range` 属性と `DateTime` では機能しません。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-183">jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="ffd2b-184">たとえば、次のコードでは、指定した範囲内の日付であっても、クライアント側の検証エラーが常に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-184">For example, the following code will always display a client side validation error, even when the date is in the specified range:</span></span>
>
> `[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]`

<span data-ttu-id="ffd2b-185">`DateTime` で `Range` 属性を使うには、jQuery の日付検証を無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-185">You will need to disable jQuery date validation to use the `Range` attribute with `DateTime`.</span></span> <span data-ttu-id="ffd2b-186">一般的に、モデルに日付をハードコーディングしてコンパイルすることは推奨されません。そのため、`Range` 属性と `DateTime` の使用は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-186">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="ffd2b-187">次のコードは、1 行で複数の属性を組み合わせる例です。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-187">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="ffd2b-188">このシリーズの次のパートでは、アプリを確認し、自動的に生成される `Details` および `Delete` メソッドに対していくつかの改良を行います。</span><span class="sxs-lookup"><span data-stu-id="ffd2b-188">In the next part of the series, we review the app and make some improvements to the automatically generated `Details` and `Delete` methods.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ffd2b-189">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ffd2b-189">Additional resources</span></span>

* [<span data-ttu-id="ffd2b-190">フォームの操作</span><span class="sxs-lookup"><span data-stu-id="ffd2b-190">Working with Forms</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="ffd2b-191">グローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="ffd2b-191">Globalization and localization</span></span>](xref:fundamentals/localization)
* [<span data-ttu-id="ffd2b-192">Tag Helpers の概要</span><span class="sxs-lookup"><span data-stu-id="ffd2b-192">Introduction to Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="ffd2b-193">タグ ヘルパーの作成</span><span class="sxs-lookup"><span data-stu-id="ffd2b-193">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> <span data-ttu-id="ffd2b-194">[前へ](new-field.md)
> [次へ](details.md)</span><span class="sxs-lookup"><span data-stu-id="ffd2b-194">[Previous](new-field.md)
[Next](details.md)</span></span>  
