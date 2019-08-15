---
title: ASP.NET Core Razor ページに検証を追加する
author: rick-anderson
description: ASP.NET Core で Razor ページに検証を追加する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 5c5419eb6ccfbd9ddd8d6fadb24d688966d76c10
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022402"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="39b08-103">ASP.NET Core Razor ページに検証を追加する</span><span class="sxs-lookup"><span data-stu-id="39b08-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="39b08-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="39b08-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="39b08-105">ここでは、`Movie` モデルに検証ロジックを追加します。</span><span class="sxs-lookup"><span data-stu-id="39b08-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="39b08-106">検証規則は、ユーザーがムービーを作成または編集するときに、いつでも適用できます。</span><span class="sxs-lookup"><span data-stu-id="39b08-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="39b08-107">検証</span><span class="sxs-lookup"><span data-stu-id="39b08-107">Validation</span></span>

<span data-ttu-id="39b08-108">ソフトウェア開発には、[DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself" (繰り返しを避けること)) という重要な理念があります。</span><span class="sxs-lookup"><span data-stu-id="39b08-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="39b08-109">Razor ページでは、機能を一度規定したら、それをアプリ全体に反映する開発を推奨しています。</span><span class="sxs-lookup"><span data-stu-id="39b08-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="39b08-110">DRY は次の場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="39b08-110">DRY can help:</span></span>

* <span data-ttu-id="39b08-111">アプリのコード量を減らす。</span><span class="sxs-lookup"><span data-stu-id="39b08-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="39b08-112">コードのエラーが発生する可能性を低くし、テストと保守を簡単にする。</span><span class="sxs-lookup"><span data-stu-id="39b08-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="39b08-113">Razor ページと Entity Framework が提供している検証のサポートは、DRY 原則の好例です。</span><span class="sxs-lookup"><span data-stu-id="39b08-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="39b08-114">検証規則は、1 つの場所 (モデル クラス内) で宣言的に規定され、アプリの任意の場所で適用されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="39b08-115">検証規則をムービー モデルに追加する</span><span class="sxs-lookup"><span data-stu-id="39b08-115">Add validation rules to the movie model</span></span>

<span data-ttu-id="39b08-116">DataAnnotations 名前空間には、クラスまたはプロパティに宣言的に適用される一連の組み込みの検証属性があります。</span><span class="sxs-lookup"><span data-stu-id="39b08-116">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="39b08-117">また、DataAnnotations には、書式設定を支援し、どの検証を行わない `DataType` のような書式設定属性もあります。</span><span class="sxs-lookup"><span data-stu-id="39b08-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="39b08-118">組み込みの `Required`、`StringLength`、`RegularExpression`、および `Range` 検証属性を利用するように、`Movie` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="39b08-118">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="39b08-119">検証属性では、適用対象のモデル プロパティに適用する動作が指定されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-119">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="39b08-120">`Required` および `MinimumLength` 属性は、プロパティに値が必要であることを示します。ただし、この検証を満たすためにユーザーが空白を入力することは禁止されていません。</span><span class="sxs-lookup"><span data-stu-id="39b08-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="39b08-121">`RegularExpression` 属性は、入力できる文字を制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-121">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="39b08-122">上のコード "Genre" では:</span><span class="sxs-lookup"><span data-stu-id="39b08-122">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="39b08-123">文字のみを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="39b08-123">Must only use letters.</span></span>
  * <span data-ttu-id="39b08-124">最初の文字は大文字にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="39b08-124">The first letter is required to be uppercase.</span></span> <span data-ttu-id="39b08-125">空白、数字、特殊文字は使用できません。</span><span class="sxs-lookup"><span data-stu-id="39b08-125">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="39b08-126">`RegularExpression` "評価":</span><span class="sxs-lookup"><span data-stu-id="39b08-126">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="39b08-127">最初の文字が大文字である必要があります。</span><span class="sxs-lookup"><span data-stu-id="39b08-127">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="39b08-128">後続のスペースでは、特殊文字と数字が使用できます。</span><span class="sxs-lookup"><span data-stu-id="39b08-128">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="39b08-129">"PG-13" は評価に対して有効ですが、"Genre" に対しては失敗します。</span><span class="sxs-lookup"><span data-stu-id="39b08-129">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="39b08-130">`Range` 属性は、指定した範囲内に値を制限します。</span><span class="sxs-lookup"><span data-stu-id="39b08-130">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="39b08-131">`StringLength` 属性では、文字列プロパティの最大長を設定でき、オプションとして最小長も設定できます。</span><span class="sxs-lookup"><span data-stu-id="39b08-131">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="39b08-132">値の型 (`decimal`、`int`、`float`、`DateTime` など) は本質的に必須ではなく、`[Required]` 属性を必要としません。</span><span class="sxs-lookup"><span data-stu-id="39b08-132">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="39b08-133">ASP.NET Core によって検証規則が自動的に適用されるようにすると、アプリをより堅牢にできます。</span><span class="sxs-lookup"><span data-stu-id="39b08-133">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="39b08-134">また、ユーザーが何かを検証することを忘れてしまい、データベースに不適切なデータが誤って格納されることもなくなります。</span><span class="sxs-lookup"><span data-stu-id="39b08-134">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="39b08-135">Razor ページの検証エラー UI</span><span class="sxs-lookup"><span data-stu-id="39b08-135">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="39b08-136">アプリを実行し、[Pages/Movies]/(ページ/ムービー/) に移動します。</span><span class="sxs-lookup"><span data-stu-id="39b08-136">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="39b08-137">**[Create New]\(新規作成\)** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="39b08-137">Select the **Create New** link.</span></span> <span data-ttu-id="39b08-138">いくつか無効な値を入力してフォームを設定します。</span><span class="sxs-lookup"><span data-stu-id="39b08-138">Complete the form with some invalid values.</span></span> <span data-ttu-id="39b08-139">jQuery クライアント側検証がエラーを検出すると、エラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-139">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![複数 jQuery クライアント側検証エラーが表示されたムービー ビュー フォーム](validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

<span data-ttu-id="39b08-141">無効な値を含む各フィールドに、検証エラー メッセージが自動的に表示されることがわかります。</span><span class="sxs-lookup"><span data-stu-id="39b08-141">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="39b08-142">エラーは、(JavaScript と jQuery を使用している) クライアント側とサーバー側 (ユーザーが JavaScript を無効にしている場合) の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-142">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="39b08-143">大きな利点は、[Create]/(作成/) ページまたは [Edit]/(編集/) ページの変更が**必要ない**ことです。</span><span class="sxs-lookup"><span data-stu-id="39b08-143">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="39b08-144">一度 DataAnnotations がモデルに適用されると、検証 UI は有効化されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-144">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="39b08-145">このチュートリアルで作成した Razor ページは、(`Movie` モデル クラスのプロパティの検証属性を使用して) 検証規則を自動的に抽出します。</span><span class="sxs-lookup"><span data-stu-id="39b08-145">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="39b08-146">[Edit]/(編集/) ページを使用して検証をテストすると、同じ検証が適用されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-146">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="39b08-147">クライアント側の検証エラーがなくなるまで、フォーム データはサーバーにポストされません。</span><span class="sxs-lookup"><span data-stu-id="39b08-147">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="39b08-148">次のうち 1 つまたは複数の方法で、フォーム データがポストされていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="39b08-148">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="39b08-149">`OnPostAsync` メソッドにブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="39b08-149">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="39b08-150">フォームを送信します ( **[Create]/(作成/)** または **[Save]/(保存/)** を選択します)。</span><span class="sxs-lookup"><span data-stu-id="39b08-150">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="39b08-151">ブレークポイントがヒットすることはありません。</span><span class="sxs-lookup"><span data-stu-id="39b08-151">The break point is never hit.</span></span>
* <span data-ttu-id="39b08-152">[Fiddler ツール](https://www.telerik.com/fiddler)を使用します。</span><span class="sxs-lookup"><span data-stu-id="39b08-152">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="39b08-153">ブラウザー開発者向けツールを使用して、ネットワーク トラフィックを監視します。</span><span class="sxs-lookup"><span data-stu-id="39b08-153">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="39b08-154">サーバー側の検証</span><span class="sxs-lookup"><span data-stu-id="39b08-154">Server-side validation</span></span>

<span data-ttu-id="39b08-155">ブラウザーで JavaScript が無効にされている場合、エラーがあるフォームを送信すると、サーバーにポストされます。</span><span class="sxs-lookup"><span data-stu-id="39b08-155">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="39b08-156">必要に応じて、サーバー側の検証をテストします。</span><span class="sxs-lookup"><span data-stu-id="39b08-156">Optional, test server-side validation:</span></span>

* <span data-ttu-id="39b08-157">ブラウザーで JavaScript を無効にします。</span><span class="sxs-lookup"><span data-stu-id="39b08-157">Disable JavaScript in the browser.</span></span> <span data-ttu-id="39b08-158">ブラウザーの開発者ツールを使用して JavaScript を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="39b08-158">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="39b08-159">ブラウザーで JavaScript を無効にすることができない場合は、別のブラウザーを試してください。</span><span class="sxs-lookup"><span data-stu-id="39b08-159">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="39b08-160">[Create] または [Edit] ページの `OnPostAsync` メソッドにブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="39b08-160">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="39b08-161">無効なデータを含むフォームを送信します。</span><span class="sxs-lookup"><span data-stu-id="39b08-161">Submit a form with invalid data.</span></span>
* <span data-ttu-id="39b08-162">モデルの状態が無効であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="39b08-162">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

<span data-ttu-id="39b08-163">次のコードは、チュートリアルで前にスキャフォールディング処理した *Create.cshtml* ページの一部を示しています。</span><span class="sxs-lookup"><span data-stu-id="39b08-163">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="39b08-164">[Create] または [Edit] ページにおいて、最初のフォームの表示と、エラーイベント時におけるフォームの再表示のために使用されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-164">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="39b08-165">[入力タグ ヘルパー](xref:mvc/views/working-with-forms)は [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性を使用し、クライアント側で jQuery 検証に必要な HTML 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="39b08-165">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="39b08-166">[検証タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-tag-helpers)には検証エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-166">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="39b08-167">詳しくは、[検証に関する記事](xref:mvc/models/validation)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="39b08-167">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="39b08-168">[Create] ページと [Edit] ページ内に検証規則はありません。</span><span class="sxs-lookup"><span data-stu-id="39b08-168">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="39b08-169">検証規則とエラー文字列は、`Movie` クラスでのみ指定されています。</span><span class="sxs-lookup"><span data-stu-id="39b08-169">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="39b08-170">これらの検証規則は、`Movie` モデルを編集する Razor ページに自動的に適用されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-170">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="39b08-171">検証ロジックの変更が必要な場合は、モデルでのみ変更します。</span><span class="sxs-lookup"><span data-stu-id="39b08-171">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="39b08-172">検証は常にアプリケーション全体に適用されます (検証ロジックは 1 か所で定義されます)。</span><span class="sxs-lookup"><span data-stu-id="39b08-172">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="39b08-173">検証が 1 か所であることは、コードが整理された状態を保つことを助け、保守と更新を簡単にします。</span><span class="sxs-lookup"><span data-stu-id="39b08-173">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="39b08-174">DataType 属性の使用</span><span class="sxs-lookup"><span data-stu-id="39b08-174">Using DataType Attributes</span></span>

<span data-ttu-id="39b08-175">`Movie` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="39b08-175">Examine the `Movie` class.</span></span> <span data-ttu-id="39b08-176">`System.ComponentModel.DataAnnotations` 名前空間には、組み込みの検証属性セットに加え、書式設定の属性もあります。</span><span class="sxs-lookup"><span data-stu-id="39b08-176">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="39b08-177">`DataType` 属性は、`ReleaseDate` および `Price` プロパティに適用されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-177">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="39b08-178">`DataType` 属性は、ビュー エンジンに対して、データの書式設定のヒントのみを提供します (また、URL の場合に `<a>`、電子メールの場合に `<a href="mailto:EmailAddress.com">` などの属性を提供します)。</span><span class="sxs-lookup"><span data-stu-id="39b08-178">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="39b08-179">データの書式を検証するためには、`RegularExpression` 属性を使用してください。</span><span class="sxs-lookup"><span data-stu-id="39b08-179">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="39b08-180">`DataType` 属性は、データベースの組み込み型よりも具体的なデータ型を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-180">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="39b08-181">`DataType` 属性は、検証属性ではありません。</span><span class="sxs-lookup"><span data-stu-id="39b08-181">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="39b08-182">サンプル アプリケーションでは、日付のみが表示され、時刻は表示されません。</span><span class="sxs-lookup"><span data-stu-id="39b08-182">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="39b08-183">`DataType` 列挙型は、Date、Time、PhoneNumber、Currency、EmailAddress など、多くの型のために用意されています。</span><span class="sxs-lookup"><span data-stu-id="39b08-183">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="39b08-184">また、`DataType` 属性を使用して、アプリケーションで型固有の機能を自動的に提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="39b08-184">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="39b08-185">たとえば、`DataType.EmailAddress` に対して `mailto:` リンクを作成できます。</span><span class="sxs-lookup"><span data-stu-id="39b08-185">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="39b08-186">HTML 5 をサポートしているブラウザーでは、`DataType.Date` に日付セレクターを提供できます。</span><span class="sxs-lookup"><span data-stu-id="39b08-186">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="39b08-187">`DataType` 属性は、HTML 5 ブラウザーが使用する HTML 5 `data-` ("データ ダッシュ" と読みます) 属性を出力します。</span><span class="sxs-lookup"><span data-stu-id="39b08-187">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="39b08-188">`DataType` 属性は、検証を**提供していません**。</span><span class="sxs-lookup"><span data-stu-id="39b08-188">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="39b08-189">`DataType.Date` は、表示される日付の書式を指定しません。</span><span class="sxs-lookup"><span data-stu-id="39b08-189">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="39b08-190">既定で、日付フィールドはサーバーの `CultureInfo` に基づき、既定の書式に従って表示されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-190">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="39b08-191">`[Column(TypeName = "decimal(18, 2)")]` データ注釈は、Entity Framework Core がデータベースの通貨と `Price` を正しくマッピングできるようにするために必要です。</span><span class="sxs-lookup"><span data-stu-id="39b08-191">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="39b08-192">詳細については、「[Data Types](/ef/core/modeling/relational/data-types)」(データ型) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="39b08-192">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="39b08-193">`DisplayFormat` 属性は、日付の書式を明示的に指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-193">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="39b08-194">`ApplyFormatInEditMode` 設定では、編集対象の値を表示するときに適用する必要がある書式設定を指定します。</span><span class="sxs-lookup"><span data-stu-id="39b08-194">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="39b08-195">一部のフィールドでは、この動作が不要なことがあります。</span><span class="sxs-lookup"><span data-stu-id="39b08-195">You might not want that behavior for some fields.</span></span> <span data-ttu-id="39b08-196">たとえば、通貨値の場合、編集 UI で通貨記号が不要なことがあります。</span><span class="sxs-lookup"><span data-stu-id="39b08-196">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="39b08-197">`DisplayFormat` 属性を単独で使用できますが、一般的に、`DataType` 属性を使用することが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-197">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="39b08-198">`DataType` 属性は、画面でのレンダリング方法とは異なり、データのセマンティクスを伝達します。また、DisplayFormat にはない、次の利点があります。</span><span class="sxs-lookup"><span data-stu-id="39b08-198">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="39b08-199">ブラウザーは HTML5 機能を有効にすることができます (たとえば、カレンダー コントロール、ロケールに適した通貨記号、電子メール リンクを表示するときなど)。</span><span class="sxs-lookup"><span data-stu-id="39b08-199">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="39b08-200">ブラウザーの既定では、ロケールに基づいて正しい書式を使ってデータがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="39b08-200">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="39b08-201">`DataType` 属性を使用すると、ASP.NET Core フレームワークで、適切なフィールド テンプレートを選択し、データをレンダリングすることができます。</span><span class="sxs-lookup"><span data-stu-id="39b08-201">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="39b08-202">`DisplayFormat` を単独で使用する場合、文字列テンプレートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-202">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="39b08-203">注: jQuery の検証は、`Range` 属性と `DateTime` では機能しません。</span><span class="sxs-lookup"><span data-stu-id="39b08-203">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="39b08-204">たとえば、次のコードでは、指定した範囲内の日付であっても、クライアント側の検証エラーが常に表示されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-204">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="39b08-205">一般的に、モデルに日付をハードコーディングしてコンパイルすることは推奨されません。そのため、`Range` 属性と `DateTime` の使用は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="39b08-205">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="39b08-206">次のコードは、1 行で複数の属性を組み合わせる例です。</span><span class="sxs-lookup"><span data-stu-id="39b08-206">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="39b08-207">[Razor Pages と EF Core の概要](xref:data/ef-rp/intro)に関するページでは、Razor Pages での EF Core 操作についてより詳しく説明されています。</span><span class="sxs-lookup"><span data-stu-id="39b08-207">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="39b08-208">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="39b08-208">Apply migrations</span></span>

<span data-ttu-id="39b08-209">クラスに適用された DataAnnotations によって、スキーマが変更されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-209">The DataAnnotations applied to the class change the schema.</span></span> <span data-ttu-id="39b08-210">たとえば、`Title`フィールドに適用された DataAnnotations は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="39b08-210">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="39b08-211">文字数を 60 に制限します。</span><span class="sxs-lookup"><span data-stu-id="39b08-211">Limits the characters to 60.</span></span>
* <span data-ttu-id="39b08-212">`null` 値を許可しません。</span><span class="sxs-lookup"><span data-stu-id="39b08-212">Doesn't allow a `null` value.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="39b08-213">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39b08-213">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="39b08-214">`Movie` テーブルには現在、次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="39b08-214">The `Movie` table currently has the following schema:</span></span>

``` sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="39b08-215">上記のスキーマ変更では、EF によって例外がスローされることはありません。</span><span class="sxs-lookup"><span data-stu-id="39b08-215">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="39b08-216">ただし、スキーマがモデルと一致するように、移行を作成してください。</span><span class="sxs-lookup"><span data-stu-id="39b08-216">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="39b08-217">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="39b08-217">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="39b08-218">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="39b08-218">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="39b08-219">`Update-Database` では、`New_DataAnnotations` クラスの `Up` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-219">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="39b08-220">`Up` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="39b08-220">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="39b08-221">更新された `Movie` テーブルには、次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="39b08-221">The updated `Movie` table has the following schema:</span></span>

``` sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="39b08-222">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="39b08-222">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="39b08-223">SQLite では、移行は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="39b08-223">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="39b08-224">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="39b08-224">Publish to Azure</span></span>

<span data-ttu-id="39b08-225">Azure へのデプロイの詳細については、「[チュートリアル: SQL Database を使用して Azure に ASP.NET Core アプリを作成する](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="39b08-225">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="39b08-226">このたびは、この Razor ページの紹介を最後までお読みいただきありがとうございました。</span><span class="sxs-lookup"><span data-stu-id="39b08-226">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="39b08-227">このチュートリアルの後は、[Razor ページと EF Core の概要](xref:data/ef-rp/intro)に関するページにお進みいただくことが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="39b08-227">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39b08-228">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="39b08-228">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="39b08-229">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="39b08-229">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="39b08-230">前へ:新しいフィールドの追加</span><span class="sxs-lookup"><span data-stu-id="39b08-230">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)
