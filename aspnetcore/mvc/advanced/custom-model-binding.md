---
title: ASP.NET Core でのカスタム モデル バインド
author: ardalis
description: モデル バインドにより ASP.NET Core のモデルの型を使用して、コントローラー アクションが直接動作する方法について説明します。
ms.author: riande
ms.date: 11/13/2018
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 91f42393ffee3249f9167e10eaea7b279a7cb70b
ms.sourcegitcommit: e7c56e8da5419bbc20b437c2dd531dedf9b0dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70878414"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="622f4-103">ASP.NET Core でのカスタム モデル バインド</span><span class="sxs-lookup"><span data-stu-id="622f4-103">Custom Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="622f4-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="622f4-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="622f4-105">モデル バインドにより、コントローラー アクションが HTTP 要求ではなく (メソッド引数として渡される) モデルの型を直接操作できるようになります。</span><span class="sxs-lookup"><span data-stu-id="622f4-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="622f4-106">受信要求データとアプリケーション モデルのマッピングは、モデル バインダーによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="622f4-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="622f4-107">開発者は、カスタム モデル バインダーを実装することで組み込みのモデル バインド機能を拡張することができます (ただし、通常は自分のプロバイダーを記述する必要はありません)。</span><span class="sxs-lookup"><span data-stu-id="622f4-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

[<span data-ttu-id="622f4-108">GitHub のサンプルを表示またはダウンロードする</span><span class="sxs-lookup"><span data-stu-id="622f4-108">View or download sample from GitHub</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/)

## <a name="default-model-binder-limitations"></a><span data-ttu-id="622f4-109">既定のモデル バインダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="622f4-109">Default model binder limitations</span></span>

<span data-ttu-id="622f4-110">既定のモデル バインダーは、一般的な .NET Core データ型の多くをサポートし、ほとんどの開発者のニーズを満たします。</span><span class="sxs-lookup"><span data-stu-id="622f4-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="622f4-111">要求からのテキストベースの入力をモデルの型に直接バインドすることが見込まれています。</span><span class="sxs-lookup"><span data-stu-id="622f4-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="622f4-112">入力は、バインドする前に変換しなければならないことがあります。</span><span class="sxs-lookup"><span data-stu-id="622f4-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="622f4-113">たとえば、モデル データの検索に使用できるキーがある場合などです。</span><span class="sxs-lookup"><span data-stu-id="622f4-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="622f4-114">カスタム モデル バインダーを使用すると、キーに基づいてデータをフェッチすることができます。</span><span class="sxs-lookup"><span data-stu-id="622f4-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="622f4-115">モデル バインドの確認</span><span class="sxs-lookup"><span data-stu-id="622f4-115">Model binding review</span></span>

<span data-ttu-id="622f4-116">モデル バインドは、操作の対象とする型に特定の定義を使用します。</span><span class="sxs-lookup"><span data-stu-id="622f4-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="622f4-117">*単純型*は、入力の 1 つの文字列から変換されます。</span><span class="sxs-lookup"><span data-stu-id="622f4-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="622f4-118">*複合型*は、複数の入力値から変換されます。</span><span class="sxs-lookup"><span data-stu-id="622f4-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="622f4-119">フレームワークは、`TypeConverter` の存在の有無によって違いを判断します。</span><span class="sxs-lookup"><span data-stu-id="622f4-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="622f4-120">外部リソースを必要としない `string` -> `SomeType` の単純なマッピングがある場合は型コンバーターを作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="622f4-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="622f4-121">独自のカスタム モデル バインダーを作成する前に、既存のモデル バインダーがどのように実装されているかを確認するとよいでしょう。</span><span class="sxs-lookup"><span data-stu-id="622f4-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="622f4-122">Base64 でエンコードされた文字列をバイト配列に変換できる、[ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) の使用を検討してください。</span><span class="sxs-lookup"><span data-stu-id="622f4-122">Consider the [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="622f4-123">バイト配列は多くの場合、ファイルまたはデータベース BLOB のフィールドとして格納されます。</span><span class="sxs-lookup"><span data-stu-id="622f4-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="622f4-124">ByteArrayModelBinder の操作</span><span class="sxs-lookup"><span data-stu-id="622f4-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="622f4-125">Base64 でエンコードされた文字列を使用してバイナリ データを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="622f4-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="622f4-126">たとえば、次のイメージは、文字列としてエンコードできます。</span><span class="sxs-lookup"><span data-stu-id="622f4-126">For example, the following image can be encoded as a string.</span></span>

<span data-ttu-id="622f4-127">![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")</span><span class="sxs-lookup"><span data-stu-id="622f4-127">![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")</span></span>

<span data-ttu-id="622f4-128">次のイメージは、エンコードされた文字列の一部を示したものです。</span><span class="sxs-lookup"><span data-stu-id="622f4-128">A small portion of the encoded string is shown in the following image:</span></span>

<span data-ttu-id="622f4-129">![エンコードされた dotnet bot](custom-model-binding/images/encoded-bot.png "エンコードされた dotnet bot")</span><span class="sxs-lookup"><span data-stu-id="622f4-129">![dotnet bot encoded](custom-model-binding/images/encoded-bot.png "dotnet bot encoded")</span></span>

<span data-ttu-id="622f4-130">[サンプルの README](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) の指示に従って、Base64 でエンコードされた文字列をファイルに変換します。</span><span class="sxs-lookup"><span data-stu-id="622f4-130">Follow the instructions in the [sample's README](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) to convert the base64-encoded string into a file.</span></span>

<span data-ttu-id="622f4-131">ASP.NET Core MVC では Base64 でエンコードされた文字列を取得し、`ByteArrayModelBinder` を使用してこれをバイト配列に変換できます。</span><span class="sxs-lookup"><span data-stu-id="622f4-131">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="622f4-132">[IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) を実装する [ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) は、次のようにして `byte[]` 引数を `ByteArrayModelBinder` にマップします。</span><span class="sxs-lookup"><span data-stu-id="622f4-132">The [ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) which implements [IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="622f4-133">独自のカスタム モデル バインダーを作成するときには、独自の `IModelBinderProvider` 型を実装するか、[ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute) を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="622f4-133">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute).</span></span>

<span data-ttu-id="622f4-134">次の例は、`ByteArrayModelBinder` を使用して Base64 でエンコードされた文字列を `byte[]` に変換し、結果をファイルに保存する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="622f4-134">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

<span data-ttu-id="622f4-135">[Postman](https://www.getpostman.com/) のようなツールを使用すると、この API メソッドに Base64 でエンコードされた文字列を POST することができます。</span><span class="sxs-lookup"><span data-stu-id="622f4-135">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="622f4-136">![postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="622f4-136">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="622f4-137">バインダーが要求データを適切に命名されたプロパティまたは引数にバインドできれば、モデル バインドは成功します。</span><span class="sxs-lookup"><span data-stu-id="622f4-137">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="622f4-138">ビュー モデルを指定して `ByteArrayModelBinder` を使用する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="622f4-138">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="622f4-139">カスタム モデル バインダーのサンプル</span><span class="sxs-lookup"><span data-stu-id="622f4-139">Custom model binder sample</span></span>

<span data-ttu-id="622f4-140">このセクションでは、次のようなカスタム モデル バインダーを実装します。</span><span class="sxs-lookup"><span data-stu-id="622f4-140">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="622f4-141">受信した要求データを厳密に型指定されたキーの引数に変換する。</span><span class="sxs-lookup"><span data-stu-id="622f4-141">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="622f4-142">Entity Framework Core を使用して関連するエンティティをフェッチする。</span><span class="sxs-lookup"><span data-stu-id="622f4-142">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="622f4-143">関連するエンティティを引数としてアクション メソッドに渡す。</span><span class="sxs-lookup"><span data-stu-id="622f4-143">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="622f4-144">次のサンプルでは、`Author` モデルの `ModelBinder` 属性を使用しています。</span><span class="sxs-lookup"><span data-stu-id="622f4-144">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

<span data-ttu-id="622f4-145">上記のコードでは、`Author` アクション パラメーターのバインドで使用される必要がある `IModelBinder` の型が `ModelBinder` 属性で指定されています。</span><span class="sxs-lookup"><span data-stu-id="622f4-145">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="622f4-146">次の `AuthorEntityBinder` クラスは、Entity Framework Core と `authorId` を使用してデータ ソースからエンティティをフェッチすることで `Author` パラメーターをバインドします。</span><span class="sxs-lookup"><span data-stu-id="622f4-146">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="622f4-147">前述の `AuthorEntityBinder` クラスは、カスタム モデル バインダーを示しています。</span><span class="sxs-lookup"><span data-stu-id="622f4-147">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="622f4-148">このクラスは、参照シナリオのベスト プラクティスを示すものではありません。</span><span class="sxs-lookup"><span data-stu-id="622f4-148">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="622f4-149">参照の場合は、`authorId` をバインドし、アクション メソッドでデータベースをクエリします。</span><span class="sxs-lookup"><span data-stu-id="622f4-149">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="622f4-150">この方法により、`NotFound` のケースからモデル バインドのエラーが分離されます。</span><span class="sxs-lookup"><span data-stu-id="622f4-150">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="622f4-151">アクション メソッドでの `AuthorEntityBinder` の使用方法を次のコードに示します。</span><span class="sxs-lookup"><span data-stu-id="622f4-151">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="622f4-152">`ModelBinder` 属性を使用すると、既定の規則を使用しないパラメーターに `AuthorEntityBinder` を適用できます。</span><span class="sxs-lookup"><span data-stu-id="622f4-152">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="622f4-153">この例では、引数の名前が既定の `authorId` ではないため、`ModelBinder` 属性を使用してパラメーターに指定されています。</span><span class="sxs-lookup"><span data-stu-id="622f4-153">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="622f4-154">アクション メソッドでエンティティを検索する場合と比較して、コントローラーとアクション メソッドの両方が簡略化されています。</span><span class="sxs-lookup"><span data-stu-id="622f4-154">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="622f4-155">Entity Framework Core を使用して作成者をフェッチするためのロジックは、モデル バインダーに移動しています。</span><span class="sxs-lookup"><span data-stu-id="622f4-155">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="622f4-156">これは、`Author` モデルにバインドするメソッドがいくつかある場合、大幅な簡略化になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="622f4-156">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="622f4-157">`ModelBinder` 属性を (ビューモデルなどの) 個々のモデル プロパティまたはアクション メソッド パラメーターに適用して、その型やアクションのみを対象とする特定のモデル バインダーまたはモデル名を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="622f4-157">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="622f4-158">ModelBinderProvider の実装</span><span class="sxs-lookup"><span data-stu-id="622f4-158">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="622f4-159">属性を適用する代わりに、`IModelBinderProvider` を実装することができます。</span><span class="sxs-lookup"><span data-stu-id="622f4-159">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="622f4-160">これは、組み込みフレームワーク バインダーの実装方法と同じです。</span><span class="sxs-lookup"><span data-stu-id="622f4-160">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="622f4-161">バインダーが動作する型を指定するときに、バインダーが受け入れる入力**ではなく**、生成される引数の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="622f4-161">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="622f4-162">次のバインダー プロバイダーは `AuthorEntityBinder` で動作します。</span><span class="sxs-lookup"><span data-stu-id="622f4-162">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="622f4-163">プロバイダーの MVC のコレクションに追加されるときに、`Author` または `Author` の型のパラメーターで `ModelBinder` 属性を使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="622f4-163">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="622f4-164">メモ:上のコードは `BinderTypeModelBinder` を返します。</span><span class="sxs-lookup"><span data-stu-id="622f4-164">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="622f4-165">`BinderTypeModelBinder` はモデル バインダーのファクトリとして機能し、依存関係の挿入 (DI) を提供します。</span><span class="sxs-lookup"><span data-stu-id="622f4-165">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="622f4-166">`AuthorEntityBinder` は DI に EF Core へのアクセスを求めます。</span><span class="sxs-lookup"><span data-stu-id="622f4-166">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="622f4-167">モデル バインダーが DI からのサービスを必要としている場合は、`BinderTypeModelBinder` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="622f4-167">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="622f4-168">カスタム モデル バインダー プロバイダーを使用する場合、次のようにしてこれを `ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="622f4-168">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

<span data-ttu-id="622f4-169">モデル バインダーを評価するときに、プロバイダーのコレクションが順序どおりにチェックされます。</span><span class="sxs-lookup"><span data-stu-id="622f4-169">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="622f4-170">バインダーを返す最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="622f4-170">The first provider that returns a binder is used.</span></span>

<span data-ttu-id="622f4-171">次のイメージは、デバッガーの既定のモデル バインダーを示します。</span><span class="sxs-lookup"><span data-stu-id="622f4-171">The following image shows the default model binders from the debugger.</span></span>

<span data-ttu-id="622f4-172">![既定のモデル バインダー](custom-model-binding/images/default-model-binders.png "既定のモデル バインダー")</span><span class="sxs-lookup"><span data-stu-id="622f4-172">![default model binders](custom-model-binding/images/default-model-binders.png "default model binders")</span></span>

<span data-ttu-id="622f4-173">コレクションの末尾にプロバイダーを追加すると、カスタム バインダーより前に組み込みのモデル バインダーが呼び出される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="622f4-173">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="622f4-174">この例では、カスタム プロバイダーが `Author` アクションの引数で使用されるように、コレクションの先頭に追加されています。</span><span class="sxs-lookup"><span data-stu-id="622f4-174">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

### <a name="polymorphic-model-binding"></a><span data-ttu-id="622f4-175">ポリモーフィック モデル バインド</span><span class="sxs-lookup"><span data-stu-id="622f4-175">Polymorphic model binding</span></span>

<span data-ttu-id="622f4-176">派生型の異なるモデルへのバインドは、ポリモーフィック モデル バインドと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="622f4-176">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="622f4-177">カスタム モデル バインドは、要求値を特定の派生モデル型にバインドする必要がある場合に必要です。</span><span class="sxs-lookup"><span data-stu-id="622f4-177">Custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="622f4-178">このアプローチが必要でない限り、ポリモーフィック モデル バインドを避けることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="622f4-178">Unless this approach is required, we recommend avoiding polymorphic model binding.</span></span> <span data-ttu-id="622f4-179">ポリモーフィック モデル バインドにより、バインドされているモデルに関する判断が困難になります。</span><span class="sxs-lookup"><span data-stu-id="622f4-179">Polymorphic model binding makes it difficult to reason about the bound models.</span></span> <span data-ttu-id="622f4-180">ただし、アプリにポリモーフィック モデル バインドが必要な場合、実装は次のコードのようになります。</span><span class="sxs-lookup"><span data-stu-id="622f4-180">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

<span data-ttu-id="622f4-181">派生型の異なるモデルへのバインドは、ポリモーフィック モデル バインドと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="622f4-181">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="622f4-182">カスタム モデル バインドは、要求値を特定の派生モデル型にバインドする必要がある場合に必要です。</span><span class="sxs-lookup"><span data-stu-id="622f4-182">Custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="622f4-183">ポリモーフィック モデル バインド:</span><span class="sxs-lookup"><span data-stu-id="622f4-183">Polymorphic model binding:</span></span>

* <span data-ttu-id="622f4-184">すべての言語と相互運用できるように設計された REST API では一般的ではありません。</span><span class="sxs-lookup"><span data-stu-id="622f4-184">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="622f4-185">バインドされたモデルに関する判断が困難になります。</span><span class="sxs-lookup"><span data-stu-id="622f4-185">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="622f4-186">ただし、アプリにポリモーフィック モデル バインドが必要な場合、実装は次のコードのようになります。</span><span class="sxs-lookup"><span data-stu-id="622f4-186">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/3.0sample/PolymorphicModelBinding/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="622f4-187">推奨事項とベスト プラクティス</span><span class="sxs-lookup"><span data-stu-id="622f4-187">Recommendations and best practices</span></span>

<span data-ttu-id="622f4-188">カスタム モデル バインダー:</span><span class="sxs-lookup"><span data-stu-id="622f4-188">Custom model binders:</span></span>

- <span data-ttu-id="622f4-189">状態コードの設定または結果 (たとえば 404 Not Found) のリターンを試行しないでください。</span><span class="sxs-lookup"><span data-stu-id="622f4-189">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="622f4-190">モデル バインドが失敗した場合、アクション メソッド自体の[アクション フィルター](xref:mvc/controllers/filters)またはロジックでエラーが処理される必要があります。</span><span class="sxs-lookup"><span data-stu-id="622f4-190">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="622f4-191">アクション メソッドから繰り返しのコードや横断的な問題を排除する場合に最も役立ちます。</span><span class="sxs-lookup"><span data-stu-id="622f4-191">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="622f4-192">通常、文字列をカスタムの型に変換する場合に使用すべきではありません。通常は、[`TypeConverter`](/dotnet/api/system.componentmodel.typeconverter) の方がオプションとして優れています。</span><span class="sxs-lookup"><span data-stu-id="622f4-192">Typically shouldn't be used to convert a string into a custom type, a [`TypeConverter`](/dotnet/api/system.componentmodel.typeconverter) is usually a better option.</span></span>
