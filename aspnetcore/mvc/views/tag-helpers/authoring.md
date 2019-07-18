---
title: ASP.NET Core のタグ ヘルパー作成
author: rick-anderson
description: ASP.NET Core でのタグ ヘルパーの作成方法を説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/29/2019
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: c13e63725298975fc882aa45c4e75de53e1d66a8
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815158"
---
# <a name="author-tag-helpers-in-aspnet-core"></a><span data-ttu-id="da813-103">ASP.NET Core のタグ ヘルパー作成</span><span class="sxs-lookup"><span data-stu-id="da813-103">Author Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="da813-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="da813-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="da813-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="da813-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started-with-tag-helpers"></a><span data-ttu-id="da813-106">タグ ヘルパーの概要</span><span class="sxs-lookup"><span data-stu-id="da813-106">Get started with Tag Helpers</span></span>

<span data-ttu-id="da813-107">このチュートリアルでは、タグ ヘルパーのプログラミングの概要を提供します。</span><span class="sxs-lookup"><span data-stu-id="da813-107">This tutorial provides an introduction to programming Tag Helpers.</span></span> <span data-ttu-id="da813-108">[タグ ヘルパーの概要](intro.md)に関するページでは、タグ ヘルパーが提供する利点について説明しています。</span><span class="sxs-lookup"><span data-stu-id="da813-108">[Introduction to Tag Helpers](intro.md) describes the benefits that Tag Helpers provide.</span></span>

<span data-ttu-id="da813-109">タグ ヘルパーは、`ITagHelper` インターフェイス実装する任意のクラスです。</span><span class="sxs-lookup"><span data-stu-id="da813-109">A tag helper is any class that implements the `ITagHelper` interface.</span></span> <span data-ttu-id="da813-110">ただし、タグ ヘルパーを作成する場合は、通常は `TagHelper` から派生させます。こうすることで、`Process` メソッドにアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="da813-110">However, when you author a tag helper, you generally derive from `TagHelper`, doing so gives you access to the `Process` method.</span></span>

1. <span data-ttu-id="da813-111">**AuthoringTagHelpers** という新しい ASP.NET Core プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="da813-111">Create a new ASP.NET Core project called **AuthoringTagHelpers**.</span></span> <span data-ttu-id="da813-112">このプロジェクトに認証は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="da813-112">You won't need authentication for this project.</span></span>

1. <span data-ttu-id="da813-113">タグ ヘルパーを保持する *TagHelpers* というフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="da813-113">Create a folder to hold the Tag Helpers called *TagHelpers*.</span></span> <span data-ttu-id="da813-114">*TagHelpers* フォルダーは必須では*ありません*が、あると便利です。</span><span class="sxs-lookup"><span data-stu-id="da813-114">The *TagHelpers* folder is *not* required, but it's a reasonable convention.</span></span> <span data-ttu-id="da813-115">それでは、いくつか単純なタグ ヘルパーを記述してみましょう。</span><span class="sxs-lookup"><span data-stu-id="da813-115">Now let's get started writing some simple tag helpers.</span></span>

## <a name="a-minimal-tag-helper"></a><span data-ttu-id="da813-116">最小のタグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="da813-116">A minimal Tag Helper</span></span>

<span data-ttu-id="da813-117">このセクションでは、電子メール タグを更新するタグ ヘルパーを記述します。</span><span class="sxs-lookup"><span data-stu-id="da813-117">In this section, you write a tag helper that updates an email tag.</span></span> <span data-ttu-id="da813-118">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="da813-118">For example:</span></span>

```html
<email>Support</email>
```

<span data-ttu-id="da813-119">サーバーは、電子メール タグ ヘルパーを使用して、そのマークアップを以下に変換します。</span><span class="sxs-lookup"><span data-stu-id="da813-119">The server will use our email tag helper to convert that markup into the following:</span></span>

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

<span data-ttu-id="da813-120">つまり、これを電子メール リンクにするアンカー タグです。</span><span class="sxs-lookup"><span data-stu-id="da813-120">That is, an anchor tag that makes this an email link.</span></span> <span data-ttu-id="da813-121">ブログ エンジンを記述していて、マーケティング、サポート、およびその他の連絡先の電子メールをすべて同じドメインに送信するためにそれが必要な場合に、これを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="da813-121">You might want to do this if you are writing a blog engine and need it to send email for marketing, support, and other contacts, all to the same domain.</span></span>

1. <span data-ttu-id="da813-122">次の `EmailTagHelper` クラスを *TagHelpers* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="da813-122">Add the following `EmailTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * <span data-ttu-id="da813-123">タグ ヘルパーは、ルート クラス名の要素 (クラス名部分から *TagHelper* 部分を引いたもの) をターゲットとする名前付け規則です。</span><span class="sxs-lookup"><span data-stu-id="da813-123">Tag helpers use a naming convention that targets elements of the root class name (minus the *TagHelper* portion of the class name).</span></span> <span data-ttu-id="da813-124">この例では、**EmailTagHelper** のルート名は *email* となるため、`<email>` タグがターゲットとなります。</span><span class="sxs-lookup"><span data-stu-id="da813-124">In this example, the root name of **EmailTagHelper** is *email*, so the `<email>` tag will be targeted.</span></span> <span data-ttu-id="da813-125">この命名規則は、ほとんどのタグ ヘルパーで機能します。オーバーライドの方法については、後述します。</span><span class="sxs-lookup"><span data-stu-id="da813-125">This naming convention should work for most tag helpers, later on I'll show how to override it.</span></span>

   * <span data-ttu-id="da813-126">`EmailTagHelper` クラスは `TagHelper` から派生したものです。</span><span class="sxs-lookup"><span data-stu-id="da813-126">The `EmailTagHelper` class derives from `TagHelper`.</span></span> <span data-ttu-id="da813-127">`TagHelper` クラスはタグ ヘルパーを記述するためのメソッドとプロパティを提供します。</span><span class="sxs-lookup"><span data-stu-id="da813-127">The `TagHelper` class provides methods and properties for writing Tag Helpers.</span></span>

   * <span data-ttu-id="da813-128">オーバーライドされた `Process` メソッドは、実行時のタグ ヘルパーの動作を制御します。</span><span class="sxs-lookup"><span data-stu-id="da813-128">The overridden `Process` method controls what the tag helper does when executed.</span></span> <span data-ttu-id="da813-129">`TagHelper` クラスには、同じパラメーターを使用する非同期バージョン (`ProcessAsync`) も用意されています。</span><span class="sxs-lookup"><span data-stu-id="da813-129">The `TagHelper` class also provides an asynchronous version (`ProcessAsync`) with the same parameters.</span></span>

   * <span data-ttu-id="da813-130">`Process` (および `ProcessAsync`) のコンテキスト パラメーターには、現在の HTML タグの実行に関連付けられている情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="da813-130">The context parameter to `Process` (and `ProcessAsync`) contains information associated with the execution of the current HTML tag.</span></span>

   * <span data-ttu-id="da813-131">`Process` (および `ProcessAsync`) の出力パラメーターには、HTML タグとコンテンツの生成に使用された元のソースを代表するステートフル HTML 要素が含まれています。</span><span class="sxs-lookup"><span data-stu-id="da813-131">The output parameter to `Process` (and `ProcessAsync`) contains a stateful HTML element representative of the original source used to generate an HTML tag and content.</span></span>

   * <span data-ttu-id="da813-132">クラス名には **TagHelper** のサフィックスが付けられています。これは必須*ではありません*が、ベスト プラクティスです。</span><span class="sxs-lookup"><span data-stu-id="da813-132">Our class name has a suffix of **TagHelper**, which is *not* required, but it's considered a best practice convention.</span></span> <span data-ttu-id="da813-133">クラスは、次のように宣言できます。</span><span class="sxs-lookup"><span data-stu-id="da813-133">You could declare the class as:</span></span>

   ```csharp
   public class Email : TagHelper
   ```

1. <span data-ttu-id="da813-134">すべての Razor ビューで `EmailTagHelper` クラスを使用可能にするには、`addTagHelper` ディレクティブを *Views/_ViewImports.cshtml* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="da813-134">To make the `EmailTagHelper` class available to all our Razor views, add the `addTagHelper` directive to the *Views/_ViewImports.cshtml* file:</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   <span data-ttu-id="da813-135">上記のコードでは、ワイルドカードの構文を使用して、アセンブリ内のすべてのタグ ヘルパーが使用可能になるように指定しています。</span><span class="sxs-lookup"><span data-stu-id="da813-135">The code above uses the wildcard syntax to specify all the tag helpers in our assembly will be available.</span></span> <span data-ttu-id="da813-136">`@addTagHelper` の後の最初の文字列は、読み込むタグ ヘルパーを指定します (すべてのタグ ヘルパーを指定するには、"\*" を使用します)。2 番目の文字列 "AuthoringTagHelpers" は、タグ ヘルパーが存在するアセンブリを指定します。</span><span class="sxs-lookup"><span data-stu-id="da813-136">The first string after `@addTagHelper` specifies the tag helper to load (Use "\*" for all tag helpers), and the second string "AuthoringTagHelpers" specifies the assembly the tag helper is in.</span></span> <span data-ttu-id="da813-137">また、2 行目で、ワイルドカードの構文を使用して ASP.NET Core MVC タグ ヘルパーを取り込むことに注目してください (これらのヘルパーについては、[タグ ヘルパーの概要](intro.md)に関するページで説明されています)。Razor ビューでタグ ヘルパーを使用可能にするのが、`@addTagHelper` ディレクティブです。</span><span class="sxs-lookup"><span data-stu-id="da813-137">Also, note that the second line brings in the ASP.NET Core MVC tag helpers using the wildcard syntax (those helpers are discussed in [Introduction to Tag Helpers](intro.md).) It's the `@addTagHelper` directive that makes the tag helper available to the Razor view.</span></span> <span data-ttu-id="da813-138">または、次に示すように、タグ ヘルパーの完全修飾名 (FQN) を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="da813-138">Alternatively, you can provide the fully qualified name (FQN) of a tag helper as shown below:</span></span>

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

<span data-ttu-id="da813-139">FQN を使用してタグ ヘルパーをビューに追加するには、最初に FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) を追加してから、**アセンブリ名** (*AuthoringTagHelpers*。`namespace` は不要) を追加します。</span><span class="sxs-lookup"><span data-stu-id="da813-139">To add a tag helper to a view using a FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the **assembly name** (*AuthoringTagHelpers*, not necessarily the `namespace`).</span></span> <span data-ttu-id="da813-140">ほとんどの開発者は、ワイルドカードの構文を使用する方を選びます。</span><span class="sxs-lookup"><span data-stu-id="da813-140">Most developers will prefer to use the wildcard syntax.</span></span> <span data-ttu-id="da813-141">[タグ ヘルパーの概要](intro.md)に関するページでは、タグ ヘルパーの追加、削除、階層、およびワイルドカードの構文について詳しく説明されています。</span><span class="sxs-lookup"><span data-stu-id="da813-141">[Introduction to Tag Helpers](intro.md) goes into detail on tag helper adding, removing, hierarchy, and wildcard syntax.</span></span>

1. <span data-ttu-id="da813-142">これらの変更を加えて、*Views/Home/Contact.cshtml* ファイル内のマークアップを更新します。</span><span class="sxs-lookup"><span data-stu-id="da813-142">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="da813-143">アプリを実行して、お好みのブラウザーを使用して HTML ソースを表示すると、電子メール タグがアンカー マークアップ (`<a>Support</a>` など) に置き換えられていることが確認できます。</span><span class="sxs-lookup"><span data-stu-id="da813-143">Run the app and use your favorite browser to view the HTML source so you can verify that the email tags are replaced with anchor markup (For example, `<a>Support</a>`).</span></span> <span data-ttu-id="da813-144">*Support* と *Marketing* はリンクとして表示されますが、リンクを機能させる `href` 属性を持っていません。</span><span class="sxs-lookup"><span data-stu-id="da813-144">*Support* and *Marketing* are rendered as a links, but they don't have an `href` attribute to make them functional.</span></span> <span data-ttu-id="da813-145">次のセクションでこれを修正します。</span><span class="sxs-lookup"><span data-stu-id="da813-145">We'll fix that in the next section.</span></span>

## <a name="setattribute-and-setcontent"></a><span data-ttu-id="da813-146">SetAttribute と SetContent</span><span class="sxs-lookup"><span data-stu-id="da813-146">SetAttribute and SetContent</span></span>

<span data-ttu-id="da813-147">このセクションでは、`EmailTagHelper` を更新して、電子メール用の有効なアンカー タグが作成できるようにします。</span><span class="sxs-lookup"><span data-stu-id="da813-147">In this section, we'll update the `EmailTagHelper` so that it will create a valid anchor tag for email.</span></span> <span data-ttu-id="da813-148">これを更新して、Razor ビューから (`mail-to` 属性の形式で) 情報を取得し、それを使用してアンカーを生成します。</span><span class="sxs-lookup"><span data-stu-id="da813-148">We'll update it to take information from a Razor view (in the form of a `mail-to` attribute) and use that in generating the anchor.</span></span>

<span data-ttu-id="da813-149">次のように `EmailTagHelper` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="da813-149">Update the `EmailTagHelper` class with the following:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* <span data-ttu-id="da813-150">タグ ヘルパーのパスカルケースのクラス名とプロパティ名は、それぞれ[ケバブ ケース](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)に変換されます。</span><span class="sxs-lookup"><span data-stu-id="da813-150">Pascal-cased class and property names for tag helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="da813-151">そのため、`MailTo` 属性を使用するには、相当する `<email mail-to="value"/>` を使用します。</span><span class="sxs-lookup"><span data-stu-id="da813-151">Therefore, to use the `MailTo` attribute, you'll use `<email mail-to="value"/>` equivalent.</span></span>

* <span data-ttu-id="da813-152">最後の行は、最低限機能するタグ ヘルパーの完成したコンテンツを設定します。</span><span class="sxs-lookup"><span data-stu-id="da813-152">The last line sets the completed content for our minimally functional tag helper.</span></span>

* <span data-ttu-id="da813-153">強調表示されている行は、属性を追加するための構文を示しています。</span><span class="sxs-lookup"><span data-stu-id="da813-153">The highlighted line shows the syntax for adding attributes:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

<span data-ttu-id="da813-154">この方法は、属性 "href" が属性コレクションに現存していない場合に限り、属性 "href" に対して有効です。</span><span class="sxs-lookup"><span data-stu-id="da813-154">That approach works for the attribute "href" as long as it doesn't currently exist in the attributes collection.</span></span> <span data-ttu-id="da813-155">`output.Attributes.Add` メソッドを使用してタグ ヘルパー属性をタグ属性のコレクションの末尾に追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="da813-155">You can also use the `output.Attributes.Add` method to add a tag helper attribute to the end of the collection of tag attributes.</span></span>

1. <span data-ttu-id="da813-156">これらの変更を加えて、*Views/Home/Contact.cshtml* ファイル内のマークアップを更新します。</span><span class="sxs-lookup"><span data-stu-id="da813-156">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. <span data-ttu-id="da813-157">アプリを実行し、適切なリンクが生成されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="da813-157">Run the app and verify that it generates the correct links.</span></span>

<a name="self-closing"></a>

   > [!NOTE]
   > <span data-ttu-id="da813-158">自己終了の電子メール タグ (`<email mail-to="Rick" />`) を記述すると、最終的な出力も自己終了になります。</span><span class="sxs-lookup"><span data-stu-id="da813-158">If you were to write the email tag self-closing (`<email mail-to="Rick" />`), the final output would also be self-closing.</span></span> <span data-ttu-id="da813-159">開始タグのみを持つタグ (`<email mail-to="Rick">`) を記述する機能を有効にするには、次のようにクラスを装飾する必要があります。</span><span class="sxs-lookup"><span data-stu-id="da813-159">To enable the ability to write the tag with only a start tag (`<email mail-to="Rick">`) you must decorate the class with the following:</span></span>
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   <span data-ttu-id="da813-160">自己終了の電子メール タグ ヘルパーでは、出力は `<a href="mailto:Rick@contoso.com" />` になります。</span><span class="sxs-lookup"><span data-stu-id="da813-160">With a self-closing email tag helper, the output would be `<a href="mailto:Rick@contoso.com" />`.</span></span> <span data-ttu-id="da813-161">自己終了のアンカー タグは有効な HTML ではないため、作成することはないかもしれませんが、自己終了のタグ ヘルパーは必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="da813-161">Self-closing anchor tags are not valid HTML, so you wouldn't want to create one, but you might want to create a tag helper that's self-closing.</span></span> <span data-ttu-id="da813-162">タグ ヘルパーは、タグを読み取った後に、`TagMode` プロパティの型を設定します。</span><span class="sxs-lookup"><span data-stu-id="da813-162">Tag helpers set the type of the `TagMode` property after reading a tag.</span></span>

### <a name="processasync"></a><span data-ttu-id="da813-163">ProcessAsync</span><span class="sxs-lookup"><span data-stu-id="da813-163">ProcessAsync</span></span>

<span data-ttu-id="da813-164">このセクションでは非同期の電子メール ヘルパーを記述します。</span><span class="sxs-lookup"><span data-stu-id="da813-164">In this section, we'll write an asynchronous email helper.</span></span>

1. <span data-ttu-id="da813-165">`EmailTagHelper` クラスを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="da813-165">Replace the `EmailTagHelper` class with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   <span data-ttu-id="da813-166">**注:**</span><span class="sxs-lookup"><span data-stu-id="da813-166">**Notes:**</span></span>

   * <span data-ttu-id="da813-167">このバージョンでは、非同期の `ProcessAsync` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="da813-167">This version uses the asynchronous `ProcessAsync` method.</span></span> <span data-ttu-id="da813-168">非同期の `GetChildContentAsync` は `TagHelperContent` を含む `Task` を返します。</span><span class="sxs-lookup"><span data-stu-id="da813-168">The asynchronous `GetChildContentAsync` returns a `Task` containing the `TagHelperContent`.</span></span>

   * <span data-ttu-id="da813-169">`output` パラメーターを使用して、HTML 要素のコンテンツを取得します。</span><span class="sxs-lookup"><span data-stu-id="da813-169">Use the `output` parameter to get contents of the HTML element.</span></span>

1. <span data-ttu-id="da813-170">*Views/Home/Contact.cshtml* ファイルに次の変更を行って、タグ ヘルパーがターゲットの電子メールを取得できるようにします。</span><span class="sxs-lookup"><span data-stu-id="da813-170">Make the following change to the *Views/Home/Contact.cshtml* file so the tag helper can get the target email.</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="da813-171">アプリを実行し、有効な電子メール リンクが生成されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="da813-171">Run the app and verify that it generates valid email links.</span></span>

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a><span data-ttu-id="da813-172">RemoveAll、PreContent.SetHtmlContent、PostContent.SetHtmlContent</span><span class="sxs-lookup"><span data-stu-id="da813-172">RemoveAll, PreContent.SetHtmlContent and PostContent.SetHtmlContent</span></span>

1. <span data-ttu-id="da813-173">次の `BoldTagHelper` クラスを *TagHelpers* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="da813-173">Add the following `BoldTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * <span data-ttu-id="da813-174">`[HtmlTargetElement]` 属性は、"bold" という名前の HTML 属性を含むすべての HTML 要素を照合することを指定する属性パラメーターを渡し、クラス内で `Process` オーバーライド メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="da813-174">The `[HtmlTargetElement]` attribute passes an attribute parameter that specifies that any HTML element that contains an HTML attribute named "bold" will match, and the `Process` override method in the class will run.</span></span> <span data-ttu-id="da813-175">このサンプルでは、`Process` メソッドによって "bold" 属性が削除され、中にあるマークアップが `<strong></strong>` で囲まれます。</span><span class="sxs-lookup"><span data-stu-id="da813-175">In our sample, the `Process` method removes the "bold" attribute and surrounds the containing markup with `<strong></strong>`.</span></span>

   * <span data-ttu-id="da813-176">既存のタグ コンテンツは置き換えたくないので、`PreContent.SetHtmlContent` メソッドを使用して開始タグ `<strong>` を記述し、`PostContent.SetHtmlContent` メソッドを使用して終了タグ `</strong>` を記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="da813-176">Because you don't want to replace the existing tag content, you must write the opening `<strong>` tag with the `PreContent.SetHtmlContent` method and the closing `</strong>` tag with the `PostContent.SetHtmlContent` method.</span></span>

1. <span data-ttu-id="da813-177">`bold` 属性値を含めるように *About.cshtml* ビューを変更します。</span><span class="sxs-lookup"><span data-stu-id="da813-177">Modify the *About.cshtml* view to contain a `bold` attribute value.</span></span> <span data-ttu-id="da813-178">完成したコードを以下に示します。</span><span class="sxs-lookup"><span data-stu-id="da813-178">The completed code is shown below.</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. <span data-ttu-id="da813-179">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="da813-179">Run the app.</span></span> <span data-ttu-id="da813-180">お好みのブラウザーを使用して、ソースを検査し、マークアップを確認できます。</span><span class="sxs-lookup"><span data-stu-id="da813-180">You can use your favorite browser to inspect the source and verify the markup.</span></span>

   <span data-ttu-id="da813-181">上記の `[HtmlTargetElement]` 属性は、"bold" という属性名を提供する HTML マークアップのみをターゲットにしています。</span><span class="sxs-lookup"><span data-stu-id="da813-181">The `[HtmlTargetElement]` attribute above only targets HTML markup that provides an attribute name of "bold".</span></span> <span data-ttu-id="da813-182">`<bold>` 要素は、タグ ヘルパーによって変更されませんでした。</span><span class="sxs-lookup"><span data-stu-id="da813-182">The `<bold>` element wasn't modified by the tag helper.</span></span>

1. <span data-ttu-id="da813-183">`[HtmlTargetElement]` 属性の行をコメント アウトすると、既定の `<bold>` タグ (`<bold>` 形式の HTML マークアップ) が再度ターゲットになります。</span><span class="sxs-lookup"><span data-stu-id="da813-183">Comment out the `[HtmlTargetElement]` attribute line and it will default to targeting `<bold>` tags, that is, HTML markup of the form `<bold>`.</span></span> <span data-ttu-id="da813-184">既定の名前付け規則は、クラス名 **Bold**TagHelper を `<bold>` タグと一致させることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="da813-184">Remember, the default naming convention will match the class name **Bold**TagHelper to `<bold>` tags.</span></span>

1. <span data-ttu-id="da813-185">アプリを実行して、`<bold>` タグがタグ ヘルパーによって処理されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="da813-185">Run the app and verify that the `<bold>` tag is processed by the tag helper.</span></span>

<span data-ttu-id="da813-186">複数の `[HtmlTargetElement]` 属性でクラスを修飾すると、結果はターゲットの論理 OR になります。</span><span class="sxs-lookup"><span data-stu-id="da813-186">Decorating a class with multiple `[HtmlTargetElement]` attributes results in a logical-OR of the targets.</span></span> <span data-ttu-id="da813-187">たとえば、次のコードを使用すると、bold タグまたは bold 属性が一致します。</span><span class="sxs-lookup"><span data-stu-id="da813-187">For example, using the code below, a bold tag or a bold attribute will match.</span></span>

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

<span data-ttu-id="da813-188">同じステートメントに複数の属性を追加すると、ランタイムではそれらが論理 AND として扱われます。</span><span class="sxs-lookup"><span data-stu-id="da813-188">When multiple attributes are added to the same statement, the runtime treats them as a logical-AND.</span></span> <span data-ttu-id="da813-189">たとえば、次のコードでは、一致させるために、"bold" という名前の属性 (`<bold bold />`) を使って HTML 要素に "bold" と名前を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="da813-189">For example, in the code below, an HTML element must be named "bold" with an attribute named "bold" (`<bold bold />`) to match.</span></span>

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

<span data-ttu-id="da813-190">また、`[HtmlTargetElement]` を使用してターゲット要素の名前を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="da813-190">You can also use the `[HtmlTargetElement]` to change the name of the targeted element.</span></span> <span data-ttu-id="da813-191">たとえば、`BoldTagHelper` で `<MyBold>` タグをターゲットにするには、次の属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="da813-191">For example if you wanted the `BoldTagHelper` to target `<MyBold>` tags, you would use the following attribute:</span></span>

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a><span data-ttu-id="da813-192">タグ ヘルパーにモデルを渡す</span><span class="sxs-lookup"><span data-stu-id="da813-192">Pass a model to a Tag Helper</span></span>

1. <span data-ttu-id="da813-193">*Models* フォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="da813-193">Add a *Models* folder.</span></span>

1. <span data-ttu-id="da813-194">次の `WebsiteContext` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="da813-194">Add the following `WebsiteContext` class to the *Models* folder:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. <span data-ttu-id="da813-195">次の `WebsiteInformationTagHelper` クラスを *TagHelpers* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="da813-195">Add the following `WebsiteInformationTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * <span data-ttu-id="da813-196">前述したように、タグ ヘルパーは、パスカルケースの C# クラス名とタグ ヘルパーのプロパティを[ケバブ ケース](https://wiki.c2.com/?KebabCase)に変換します。</span><span class="sxs-lookup"><span data-stu-id="da813-196">As mentioned previously, tag helpers translates Pascal-cased C# class names and properties for tag helpers into [kebab case](https://wiki.c2.com/?KebabCase).</span></span> <span data-ttu-id="da813-197">そのため、`WebsiteInformationTagHelper` を Razor で使用するには、`<website-information />` を記述します。</span><span class="sxs-lookup"><span data-stu-id="da813-197">Therefore, to use the `WebsiteInformationTagHelper` in Razor, you'll write `<website-information />`.</span></span>

   * <span data-ttu-id="da813-198">`[HtmlTargetElement]` 属性を使用してターゲット要素を明示的に識別しないため、`website-information` の既定がターゲットになります。</span><span class="sxs-lookup"><span data-stu-id="da813-198">You are not explicitly identifying the target element with the `[HtmlTargetElement]` attribute, so the default of `website-information` will be targeted.</span></span> <span data-ttu-id="da813-199">次の属性を適用した場合 (ケバブ ケースではありませんが、クラス名が一致します):</span><span class="sxs-lookup"><span data-stu-id="da813-199">If you applied the following attribute (note it's not kebab case but matches the class name):</span></span>

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   <span data-ttu-id="da813-200">ケバブ ケース タグ `<website-information />` は一致しません。</span><span class="sxs-lookup"><span data-stu-id="da813-200">The kebab case tag `<website-information />` wouldn't match.</span></span> <span data-ttu-id="da813-201">`[HtmlTargetElement]` 属性を使用する場合は、次に示すようにケバブ ケースを使用します。</span><span class="sxs-lookup"><span data-stu-id="da813-201">If you want use the `[HtmlTargetElement]` attribute, you would use kebab case as shown below:</span></span>

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * <span data-ttu-id="da813-202">自己終了の要素にはコンテンツがありません。</span><span class="sxs-lookup"><span data-stu-id="da813-202">Elements that are self-closing have no content.</span></span> <span data-ttu-id="da813-203">この例では、Razor マークアップは自己終了タグを使用しますが、タグ ヘルパーは [section](https://www.w3.org/TR/html5/sections.html#the-section-element) 要素を作成します (これは自己終了ではなく、`section` 要素内のコンテンツを記述しています)。</span><span class="sxs-lookup"><span data-stu-id="da813-203">For this example, the Razor markup will use a self-closing tag, but the tag helper will be creating a [section](https://www.w3.org/TR/html5/sections.html#the-section-element) element (which isn't self-closing and you are writing content inside the `section` element).</span></span> <span data-ttu-id="da813-204">そのため、`TagMode` を `StartTagAndEndTag` に設定して出力を記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="da813-204">Therefore, you need to set `TagMode` to `StartTagAndEndTag` to write output.</span></span> <span data-ttu-id="da813-205">または、`TagMode` を設定する行をコメント アウトして、終了タグを使ってマークアップを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="da813-205">Alternatively, you can comment out the line setting `TagMode` and write markup with a closing tag.</span></span> <span data-ttu-id="da813-206">(サンプルのマークアップは、後ほどこのチュートリアルで提供します。)</span><span class="sxs-lookup"><span data-stu-id="da813-206">(Example markup is provided later in this tutorial.)</span></span>

   * <span data-ttu-id="da813-207">次の行の `$` (ドル記号) は、[挿入文字列](/dotnet/csharp/language-reference/keywords/interpolated-strings)を使用しています。</span><span class="sxs-lookup"><span data-stu-id="da813-207">The `$` (dollar sign) in the following line uses an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span></span>

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. <span data-ttu-id="da813-208">*About.cshtml* ビューに次のマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="da813-208">Add the following markup to the *About.cshtml* view.</span></span> <span data-ttu-id="da813-209">強調表示されたマークアップは、Web サイトの情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="da813-209">The highlighted markup displays the web site information.</span></span>

   [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > <span data-ttu-id="da813-210">Razor マークアップを次に示します。</span><span class="sxs-lookup"><span data-stu-id="da813-210">In the Razor markup shown below:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > <span data-ttu-id="da813-211">Razor は `info` 属性を文字列ではなくクラスとして認識し、ユーザーは C# コードを記述します。</span><span class="sxs-lookup"><span data-stu-id="da813-211">Razor knows the `info` attribute is a class, not a string, and you want to write C# code.</span></span> <span data-ttu-id="da813-212">文字列以外のタグ ヘルパー属性はすべて、`@` 文字を使わずに記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="da813-212">Any non-string tag helper attribute should be written without the `@` character.</span></span>

1. <span data-ttu-id="da813-213">アプリを実行し、[バージョン情報] ビューに移動して、Web サイトの情報を確認します。</span><span class="sxs-lookup"><span data-stu-id="da813-213">Run the app, and navigate to the About view to see the web site information.</span></span>

   > [!NOTE]
   > <span data-ttu-id="da813-214">終了タグを持つ次のマークアップを使用して、タグ ヘルパー内の `TagMode.StartTagAndEndTag` を持つ行を削除できます。</span><span class="sxs-lookup"><span data-stu-id="da813-214">You can use the following markup with a closing tag and remove the line with `TagMode.StartTagAndEndTag` in the tag helper:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a><span data-ttu-id="da813-215">条件タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="da813-215">Condition Tag Helper</span></span>

<span data-ttu-id="da813-216">条件タグ ヘルパーは、true 値が渡されたときに出力をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="da813-216">The condition tag helper renders output when passed a true value.</span></span>

1. <span data-ttu-id="da813-217">次の `ConditionTagHelper` クラスを *TagHelpers* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="da813-217">Add the following `ConditionTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. <span data-ttu-id="da813-218">*Views/Home/Index.cshtml* ファイルの内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="da813-218">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. <span data-ttu-id="da813-219">`Home` コントローラーの `Index` メソッドを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="da813-219">Replace the `Index` method in the `Home` controller with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. <span data-ttu-id="da813-220">アプリを実行して、ホーム ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="da813-220">Run the app and browse to the home page.</span></span> <span data-ttu-id="da813-221">条件付き `div` ではマークアップはレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="da813-221">The markup in the conditional `div` won't be rendered.</span></span> <span data-ttu-id="da813-222">クエリ文字列 `?approved=true` を URL に付加します (例: `http://localhost:1235/Home/Index?approved=true`)。</span><span class="sxs-lookup"><span data-stu-id="da813-222">Append the query string `?approved=true` to the URL (for example, `http://localhost:1235/Home/Index?approved=true`).</span></span> <span data-ttu-id="da813-223">`approved` は true に設定され、条件付きマークアップが表示されます。</span><span class="sxs-lookup"><span data-stu-id="da813-223">`approved` is set to true and the conditional markup will be displayed.</span></span>

> [!NOTE]
> <span data-ttu-id="da813-224">[nameof](/dotnet/csharp/language-reference/keywords/nameof) 演算子を使用して、太字タグ ヘルパーで行ったように文字列を指定するのではなく、ターゲットにする属性を指定します。</span><span class="sxs-lookup"><span data-stu-id="da813-224">Use the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator to specify the attribute to target rather than specifying a string as you did with the bold tag helper:</span></span>
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> <span data-ttu-id="da813-225">[nameof](/dotnet/csharp/language-reference/keywords/nameof) 演算子は、コードがリファクタリングされないように保護します (名前は `RedCondition` に変更できます)。</span><span class="sxs-lookup"><span data-stu-id="da813-225">The [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator will protect the code should it ever be refactored (we might want to change the name to `RedCondition`).</span></span>

### <a name="avoid-tag-helper-conflicts"></a><span data-ttu-id="da813-226">タグ ヘルパーの競合を回避する</span><span class="sxs-lookup"><span data-stu-id="da813-226">Avoid Tag Helper conflicts</span></span>

<span data-ttu-id="da813-227">このセクションでは、自動リンク タグ ヘルパーのペアを記述します。</span><span class="sxs-lookup"><span data-stu-id="da813-227">In this section, you write a pair of auto-linking tag helpers.</span></span> <span data-ttu-id="da813-228">1 つ目のヘルパーは HTTP で始まる URL を含むマークアップを、同じ URL を含む HTML アンカー タグに置き換えます (この URL へのリンクも生成されます)。</span><span class="sxs-lookup"><span data-stu-id="da813-228">The first will replace markup containing a URL starting with HTTP to an HTML anchor tag containing the same URL (and thus yielding a link to the URL).</span></span> <span data-ttu-id="da813-229">2 つ目のヘルパーは WWW で始まる URL に対して同じことを行います。</span><span class="sxs-lookup"><span data-stu-id="da813-229">The second will do the same for a URL starting with WWW.</span></span>

<span data-ttu-id="da813-230">これら 2 つのヘルパーは密接に関連しており、将来的にそれらをリファクターする可能性があるため、これらを同じファイルで保持します。</span><span class="sxs-lookup"><span data-stu-id="da813-230">Because these two helpers are closely related and you may refactor them in the future, we'll keep them in the same file.</span></span>

1. <span data-ttu-id="da813-231">次の `AutoLinkerHttpTagHelper` クラスを *TagHelpers* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="da813-231">Add the following `AutoLinkerHttpTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   ><span data-ttu-id="da813-232">`AutoLinkerHttpTagHelper` クラスは `p` 要素をターゲットとし、[Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) を使用してアンカーを作成します。</span><span class="sxs-lookup"><span data-stu-id="da813-232">The `AutoLinkerHttpTagHelper` class targets `p` elements and uses [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) to create the anchor.</span></span>

1. <span data-ttu-id="da813-233">次のマークアップを *Views/Home/Contact.cshtml* ファイルの末尾に追加します。</span><span class="sxs-lookup"><span data-stu-id="da813-233">Add the following markup to the end of the *Views/Home/Contact.cshtml* file:</span></span>

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. <span data-ttu-id="da813-234">アプリを実行し、タグ ヘルパーがアンカーを正しくレンダリングすることを確認します。</span><span class="sxs-lookup"><span data-stu-id="da813-234">Run the app and verify that the tag helper renders the anchor correctly.</span></span>

1. <span data-ttu-id="da813-235">`AutoLinker` クラスを更新して `AutoLinkerWwwTagHelper` を含めます。これは www テキストを元の www テキストも含むアンカー タグに変換します。</span><span class="sxs-lookup"><span data-stu-id="da813-235">Update the `AutoLinker` class to include the `AutoLinkerWwwTagHelper` which will convert www text to an anchor tag that also contains the original www text.</span></span> <span data-ttu-id="da813-236">以下では、更新されたコードが強調表示されています。</span><span class="sxs-lookup"><span data-stu-id="da813-236">The updated code is highlighted below:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. <span data-ttu-id="da813-237">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="da813-237">Run the app.</span></span> <span data-ttu-id="da813-238">www テキストはリンクとしてレンダリングされていますが、HTTP テキストはそのようになっていないことに注目してください。</span><span class="sxs-lookup"><span data-stu-id="da813-238">Notice the www text is rendered as a link but the HTTP text isn't.</span></span> <span data-ttu-id="da813-239">両方のクラスにブレークポイントを配置すると、HTTP タグ ヘルパー クラスが最初に実行されることがわかります。</span><span class="sxs-lookup"><span data-stu-id="da813-239">If you put a break point in both classes, you can see that the HTTP tag helper class runs first.</span></span> <span data-ttu-id="da813-240">問題は、タグ ヘルパーの出力がキャッシュされ、WWW タグ ヘルパーを実行するときに、WWW タグ ヘルパーが HTTP タグ ヘルパーからのキャッシュされた出力を上書きすることです。</span><span class="sxs-lookup"><span data-stu-id="da813-240">The problem is that the tag helper output is cached, and when the WWW tag helper is run, it overwrites the cached output from the HTTP tag helper.</span></span> <span data-ttu-id="da813-241">タグ ヘルパーの実行順序を制御する方法については、このチュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="da813-241">Later in the tutorial we'll see how to control the order that tag helpers run in.</span></span> <span data-ttu-id="da813-242">コードを次のように修正します。</span><span class="sxs-lookup"><span data-stu-id="da813-242">We'll fix the code with the following:</span></span>

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > <span data-ttu-id="da813-243">自動リンク タグ ヘルパーの最初のエディションで、次のコードを使用してターゲットのコンテンツを取得しました。</span><span class="sxs-lookup"><span data-stu-id="da813-243">In the first edition of the auto-linking tag helpers, you got the content of the target with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > <span data-ttu-id="da813-244">つまり、`ProcessAsync` メソッドに渡された `TagHelperOutput` を使用して `GetChildContentAsync` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="da813-244">That is, you call `GetChildContentAsync` using the `TagHelperOutput` passed into the `ProcessAsync` method.</span></span> <span data-ttu-id="da813-245">前述したように、出力はキャッシュされるため、最後に実行されたタグ ヘルパーによって上書きされます。</span><span class="sxs-lookup"><span data-stu-id="da813-245">As mentioned previously, because the output is cached, the last tag helper to run wins.</span></span> <span data-ttu-id="da813-246">次のコードを使用してこの問題を修正しました。</span><span class="sxs-lookup"><span data-stu-id="da813-246">You fixed that problem with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > <span data-ttu-id="da813-247">上記のコードは、コンテンツが変更されているかどうかをチェックし、変更されている場合は、出力バッファーからコンテンツを取得します。</span><span class="sxs-lookup"><span data-stu-id="da813-247">The code above checks to see if the content has been modified, and if it has, it gets the content from the output buffer.</span></span>

1. <span data-ttu-id="da813-248">アプリを実行して、2 つのリンクが期待どおりに動作することを確認します。</span><span class="sxs-lookup"><span data-stu-id="da813-248">Run the app and verify that the two links work as expected.</span></span> <span data-ttu-id="da813-249">自動リンカー タグ ヘルパーが正しく完全に機能しているように見えても、微妙な問題があります。</span><span class="sxs-lookup"><span data-stu-id="da813-249">While it might appear our auto linker tag helper is correct and complete, it has a subtle problem.</span></span> <span data-ttu-id="da813-250">WWW タグ ヘルパーを最初に実行すると、www リンクが不正確になります。</span><span class="sxs-lookup"><span data-stu-id="da813-250">If the WWW tag helper runs first, the www links won't be correct.</span></span> <span data-ttu-id="da813-251">`Order` オーバーロードを追加してコードを更新し、タグの実行順序を制御します。</span><span class="sxs-lookup"><span data-stu-id="da813-251">Update the code by adding the `Order` overload to control the order that the tag runs in.</span></span> <span data-ttu-id="da813-252">`Order` プロパティを、同じ要素をターゲットとする他のタグ ヘルパーと比べることで実行順序が決まります。</span><span class="sxs-lookup"><span data-stu-id="da813-252">The `Order` property determines the execution order relative to other tag helpers targeting the same element.</span></span> <span data-ttu-id="da813-253">既定の順序の値は 0 で、より低い値を持つインスタンスの方が先に実行されます。</span><span class="sxs-lookup"><span data-stu-id="da813-253">The default order value is zero and instances with lower values are executed first.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   <span data-ttu-id="da813-254">上記のコードにより、WWW タグ ヘルパーの前に、HTTP タグ ヘルパーが実行されることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="da813-254">The preceding code guarantees that the HTTP tag helper runs before the WWW tag helper.</span></span> <span data-ttu-id="da813-255">`Order` を `MaxValue` に変更し、WWW タグの生成されたマークアップが正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="da813-255">Change `Order` to `MaxValue` and verify that the markup generated for the WWW tag is incorrect.</span></span>

## <a name="inspect-and-retrieve-child-content"></a><span data-ttu-id="da813-256">子コンテンツを検査して取得する</span><span class="sxs-lookup"><span data-stu-id="da813-256">Inspect and retrieve child content</span></span>

<span data-ttu-id="da813-257">タグ ヘルパーには、コンテンツを取得するための複数のプロパティが用意されています。</span><span class="sxs-lookup"><span data-stu-id="da813-257">The tag helpers provide several properties to retrieve content.</span></span>

* <span data-ttu-id="da813-258">`GetChildContentAsync` の結果を `output.Content` に付加できます。</span><span class="sxs-lookup"><span data-stu-id="da813-258">The result of `GetChildContentAsync` can be appended to `output.Content`.</span></span>
* <span data-ttu-id="da813-259">`GetContent` を使用して `GetChildContentAsync` の結果を検査できます。</span><span class="sxs-lookup"><span data-stu-id="da813-259">You can inspect the result of `GetChildContentAsync` with `GetContent`.</span></span>
* <span data-ttu-id="da813-260">`output.Content` を変更すると、この自動リンカー サンプルのように、`GetChildContentAsync` を呼び出すまで、TagHelper ボディは実行またはレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="da813-260">If you modify `output.Content`, the TagHelper body won't be executed or rendered unless you call `GetChildContentAsync` as in our auto-linker sample:</span></span>

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* <span data-ttu-id="da813-261">`GetChildContentAsync` を複数回呼び出すと、同じ値が返され、キャッシュされた結果を使用しないことを示す false パラメーターを渡さない限り、`TagHelper` ボディは再実行されません。</span><span class="sxs-lookup"><span data-stu-id="da813-261">Multiple calls to `GetChildContentAsync` returns the same value and doesn't re-execute the `TagHelper` body unless you pass in a false parameter indicating not to use the cached result.</span></span>

## <a name="load-minified-partial-view-taghelper"></a><span data-ttu-id="da813-262">縮小された部分ビュー TagHelper を読み込む</span><span class="sxs-lookup"><span data-stu-id="da813-262">Load minified partial view TagHelper</span></span>

<span data-ttu-id="da813-263">運用環境では、縮小された部分ビューを読み込むことでパフォーマンスを向上できます。</span><span class="sxs-lookup"><span data-stu-id="da813-263">In production environments, performance can be improved by loading minified partial views.</span></span> <span data-ttu-id="da813-264">運用環境で縮小された部分ビューを活用するには:</span><span class="sxs-lookup"><span data-stu-id="da813-264">To take advantage of minified partial view in production:</span></span>

* <span data-ttu-id="da813-265">部分ビューを縮小するビルド前プロセスを作成/設定します。</span><span class="sxs-lookup"><span data-stu-id="da813-265">Create/set up a pre-build process that minifies partial views.</span></span>
* <span data-ttu-id="da813-266">次のコードを使って、非開発環境に縮小された部分ビューを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="da813-266">Use the following code to load  minified partial views in non-development environments.</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
