---
title: ASP.NET Core のアプリケーション モデルの使用
author: ardalis
description: アプリケーションを読み、操作し、ASP.NET Core での MVC 要素の動作を変更する方法について説明します。
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/application-model
ms.openlocfilehash: 4b6c978e5752eb320412a1c204df8e3d288fe4a1
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654554"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a><span data-ttu-id="3cd39-103">ASP.NET Core のアプリケーション モデルの使用</span><span class="sxs-lookup"><span data-stu-id="3cd39-103">Work with the application model in ASP.NET Core</span></span>

<span data-ttu-id="3cd39-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3cd39-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3cd39-105">ASP.NET Core MVC では、MVC アプリのコンポーネントを表す*アプリケーション モデル*を定義できます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-105">ASP.NET Core MVC defines an *application model* representing the components of an MVC app.</span></span> <span data-ttu-id="3cd39-106">このモデルを読み、操作し、MVC 要素の動作を変更することができます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-106">You can read and manipulate this model to modify how MVC elements behave.</span></span> <span data-ttu-id="3cd39-107">既定で、MVC には、どのクラスがコントローラーとして考慮されるか、それらのクラスのどのメソッドがアクションであるか、パラメーターおよびルーティングがどのように動作するかについて、特定の規則があります。</span><span class="sxs-lookup"><span data-stu-id="3cd39-107">By default, MVC follows certain conventions to determine which classes are considered to be controllers, which methods on those classes are actions, and how parameters and routing behave.</span></span> <span data-ttu-id="3cd39-108">この動作をアプリのニーズに合うようにカスタマイズして、独自の規則を作成し、それらをグローバルにまたは属性として適用することができます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-108">You can customize this behavior to suit your app's needs by creating your own conventions and applying them globally or as attributes.</span></span>

## <a name="models-and-providers"></a><span data-ttu-id="3cd39-109">モデルおよびプロバイダー</span><span class="sxs-lookup"><span data-stu-id="3cd39-109">Models and Providers</span></span>

<span data-ttu-id="3cd39-110">ASP.NET Core MVC アプリケーション モデルには、MVC アプリケーションを表現する、抽象インターフェイスと具象実装クラスの両方が含まれています。</span><span class="sxs-lookup"><span data-stu-id="3cd39-110">The ASP.NET Core MVC application model include both abstract interfaces and concrete implementation classes that describe an MVC application.</span></span> <span data-ttu-id="3cd39-111">このモデルは、既定の規則に従ってアプリのコントローラー、アクション、アクション パラメーター、ルート、およびフィルターを検出する MVC の結果です。</span><span class="sxs-lookup"><span data-stu-id="3cd39-111">This model is the result of MVC discovering the app's controllers, actions, action parameters, routes, and filters according to default conventions.</span></span> <span data-ttu-id="3cd39-112">アプリケーション モデルを使用すると、既定の MVC の動作とは異なる規則を使用するようアプリを変更することができます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-112">By working with the application model, you can modify your app to follow different conventions from the default MVC behavior.</span></span> <span data-ttu-id="3cd39-113">パラメーター、名前、ルート、およびフィルターは、すべてアクションおよびコントローラーの構成データとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-113">The parameters, names, routes, and filters are all used as configuration data for actions and controllers.</span></span>

<span data-ttu-id="3cd39-114">ASP.NET Core MVC アプリケーション モデルの構造は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="3cd39-114">The ASP.NET Core MVC Application Model has the following structure:</span></span>

* <span data-ttu-id="3cd39-115">ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="3cd39-115">ApplicationModel</span></span>
  * <span data-ttu-id="3cd39-116">コントローラー (ControllerModel)</span><span class="sxs-lookup"><span data-stu-id="3cd39-116">Controllers (ControllerModel)</span></span>
    * <span data-ttu-id="3cd39-117">アクション (ActionModel)</span><span class="sxs-lookup"><span data-stu-id="3cd39-117">Actions (ActionModel)</span></span>
      * <span data-ttu-id="3cd39-118">パラメーター (ParameterModel)</span><span class="sxs-lookup"><span data-stu-id="3cd39-118">Parameters (ParameterModel)</span></span>

<span data-ttu-id="3cd39-119">このモデルでは、各レベルで、共通の `Properties` コレクションにアクセスできます。下位レベルでは、階層の上位レベルで設定されたプロパティ値にアクセスしたり上書きしたりできます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-119">Each level of the model has access to a common `Properties` collection, and lower levels can access and overwrite property values set by higher levels in the hierarchy.</span></span> <span data-ttu-id="3cd39-120">このプロパティは、アクションの作成時、`ActionDescriptor.Properties` に保存されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-120">The properties are persisted to the `ActionDescriptor.Properties` when the actions are created.</span></span> <span data-ttu-id="3cd39-121">そして要求の処理時に、規則によって追加または変更されたすべてのプロパティに、`ActionContext.ActionDescriptor.Properties` を介してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-121">Then when a request is being handled, any properties a convention added or modified can be accessed through `ActionContext.ActionDescriptor.Properties`.</span></span> <span data-ttu-id="3cd39-122">フィルターやモデル バインダーなどをアクションごとに構成するのに、プロパティを使用するのはよい方法です。</span><span class="sxs-lookup"><span data-stu-id="3cd39-122">Using properties is a great way to configure your filters, model binders, etc. on a per-action basis.</span></span>

> [!NOTE]
> <span data-ttu-id="3cd39-123">アプリのスタートアップが完了している場合、`ActionDescriptor.Properties` コレクションは (書き込みに) スレッド セーフではありません。</span><span class="sxs-lookup"><span data-stu-id="3cd39-123">The `ActionDescriptor.Properties` collection isn't thread safe (for writes) once app startup has finished.</span></span> <span data-ttu-id="3cd39-124">このコレクションにデータを安全に追加するには、規則が最善の方法です。</span><span class="sxs-lookup"><span data-stu-id="3cd39-124">Conventions are the best way to safely add data to this collection.</span></span>

### <a name="iapplicationmodelprovider"></a><span data-ttu-id="3cd39-125">IApplicationModelProvider</span><span class="sxs-lookup"><span data-stu-id="3cd39-125">IApplicationModelProvider</span></span>

<span data-ttu-id="3cd39-126">ASP.NET Core MVC は、[IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) インターフェイスによって定義されるプロバイダー パターンを使用して、アプリケーション モデルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-126">ASP.NET Core MVC loads the application model using a provider pattern, defined by the [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) interface.</span></span> <span data-ttu-id="3cd39-127">このセクションでは、このプロバイダーがどのように機能するかについての、いくつかの内部実装に関する詳細を説明します。</span><span class="sxs-lookup"><span data-stu-id="3cd39-127">This section covers some of the internal implementation details of how this provider functions.</span></span> <span data-ttu-id="3cd39-128">これは高度なトピックです。アプリケーション モデルを活用するアプリのほとんどでは、規則を使用してアプリケーション モデルを活用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3cd39-128">This is an advanced topic - most apps that leverage the application model should do so by working with conventions.</span></span>

<span data-ttu-id="3cd39-129">`IApplicationModelProvider` インターフェイスの実装は、その `OnProvidersExecuting` プロパティに応じて、昇順で `Order` を呼び出して互いを "ラップ" します。</span><span class="sxs-lookup"><span data-stu-id="3cd39-129">Implementations of the `IApplicationModelProvider` interface "wrap" one another, with each implementation calling `OnProvidersExecuting` in ascending order based on its `Order` property.</span></span> <span data-ttu-id="3cd39-130">次いで、`OnProvidersExecuted` メソッドが逆順で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-130">The `OnProvidersExecuted` method is then called in reverse order.</span></span> <span data-ttu-id="3cd39-131">このフレームワークでは、次のいくつかのプロバイダーが定義されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-131">The framework defines several providers:</span></span>

<span data-ttu-id="3cd39-132">1 番目 (`Order=-1000`):</span><span class="sxs-lookup"><span data-stu-id="3cd39-132">First (`Order=-1000`):</span></span>

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

<span data-ttu-id="3cd39-133">次 (`Order=-990`):</span><span class="sxs-lookup"><span data-stu-id="3cd39-133">Then (`Order=-990`):</span></span>

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> <span data-ttu-id="3cd39-134">2 つのプロバイダーの `Order` の値が同じである場合、順序は定義されていないため、これには依存しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="3cd39-134">The order in which two providers with the same value for `Order` are called is undefined, and therefore shouldn't be relied upon.</span></span>

> [!NOTE]
> <span data-ttu-id="3cd39-135">`IApplicationModelProvider` は、フレームワークの作成者が拡張する高度な概念です。</span><span class="sxs-lookup"><span data-stu-id="3cd39-135">`IApplicationModelProvider` is an advanced concept for framework authors to extend.</span></span> <span data-ttu-id="3cd39-136">一般に、規則やフレームワークを使う必要があるアプリは、プロバイダーを使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="3cd39-136">In general, apps should use conventions and frameworks should use providers.</span></span> <span data-ttu-id="3cd39-137">重要な違いは、プロバイダーは常に規則の前に実行されるということです。</span><span class="sxs-lookup"><span data-stu-id="3cd39-137">The key distinction is that providers always run before conventions.</span></span>

<span data-ttu-id="3cd39-138">`DefaultApplicationModelProvider` は ASP.NET Core MVC で使用される多数の既定の動作を確立します。</span><span class="sxs-lookup"><span data-stu-id="3cd39-138">The `DefaultApplicationModelProvider` establishes many of the default behaviors used by ASP.NET Core MVC.</span></span> <span data-ttu-id="3cd39-139">次の役割があります。</span><span class="sxs-lookup"><span data-stu-id="3cd39-139">Its responsibilities include:</span></span>

* <span data-ttu-id="3cd39-140">コンテキストにグローバル フィルターを追加する</span><span class="sxs-lookup"><span data-stu-id="3cd39-140">Adding global filters to the context</span></span>
* <span data-ttu-id="3cd39-141">コンテキストにコントローラーを追加する</span><span class="sxs-lookup"><span data-stu-id="3cd39-141">Adding controllers to the context</span></span>
* <span data-ttu-id="3cd39-142">アクションとしてパブリック コントローラー メソッドを追加する</span><span class="sxs-lookup"><span data-stu-id="3cd39-142">Adding public controller methods as actions</span></span>
* <span data-ttu-id="3cd39-143">コンテキストにアクション メソッド パラメーターを追加する</span><span class="sxs-lookup"><span data-stu-id="3cd39-143">Adding action method parameters to the context</span></span>
* <span data-ttu-id="3cd39-144">ルートおよびその他の属性を適用する</span><span class="sxs-lookup"><span data-stu-id="3cd39-144">Applying route and other attributes</span></span>

<span data-ttu-id="3cd39-145">いくつかの組み込みの動作は、`DefaultApplicationModelProvider` によって実装されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-145">Some built-in behaviors are implemented by the `DefaultApplicationModelProvider`.</span></span> <span data-ttu-id="3cd39-146">このプロバイダーは、[`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel)、[`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel)、および [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel) インスタンスを代わりに参照する、[`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) を構築する役割があります。</span><span class="sxs-lookup"><span data-stu-id="3cd39-146">This provider is responsible for constructing the [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), which in turn references [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel), [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), and [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) instances.</span></span> <span data-ttu-id="3cd39-147">`DefaultApplicationModelProvider` クラスは、今後変更する可能性がある変更される、内部フレームワークの実装についての詳細です。</span><span class="sxs-lookup"><span data-stu-id="3cd39-147">The `DefaultApplicationModelProvider` class is an internal framework implementation detail that can and will change in the future.</span></span> 

<span data-ttu-id="3cd39-148">`AuthorizationApplicationModelProvider` は、`AuthorizeFilter` 属性および `AllowAnonymousFilter` 属性に関連付けられた動作を適用します。</span><span class="sxs-lookup"><span data-stu-id="3cd39-148">The `AuthorizationApplicationModelProvider` is responsible for applying the behavior associated with the `AuthorizeFilter` and `AllowAnonymousFilter` attributes.</span></span> <span data-ttu-id="3cd39-149">[これらの属性については、こちらを参照してください](xref:security/authorization/simple)。</span><span class="sxs-lookup"><span data-stu-id="3cd39-149">[Learn more about these attributes](xref:security/authorization/simple).</span></span>

<span data-ttu-id="3cd39-150">`CorsApplicationModelProvider` は、`IEnableCorsAttribute` および `IDisableCorsAttribute` および `DisableCorsAuthorizationFilter` に関連付けられた動作を実装します。</span><span class="sxs-lookup"><span data-stu-id="3cd39-150">The `CorsApplicationModelProvider` implements behavior associated with the `IEnableCorsAttribute` and `IDisableCorsAttribute`, and the `DisableCorsAuthorizationFilter`.</span></span> <span data-ttu-id="3cd39-151">[CORS の詳細については、こちらを参照してください](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="3cd39-151">[Learn more about CORS](xref:security/cors).</span></span>

## <a name="conventions"></a><span data-ttu-id="3cd39-152">規則</span><span class="sxs-lookup"><span data-stu-id="3cd39-152">Conventions</span></span>

<span data-ttu-id="3cd39-153">このアプリケーション モデルでは、モデルまたはプロバイダー全体をオーバーライドするよりも簡単に、モデルの動作をカスタマイズできる、規則の抽象化を定義できます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-153">The application model defines convention abstractions that provide a simpler way to customize the behavior of the models than overriding the entire model or provider.</span></span> <span data-ttu-id="3cd39-154">これらの抽象化は、アプリの動作の変更に推奨されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-154">These abstractions are the recommended way to modify your app's behavior.</span></span> <span data-ttu-id="3cd39-155">規則では、動的にカスタマイズすることが可能なコードを記述することができます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-155">Conventions provide a way for you to write code that will dynamically apply customizations.</span></span> <span data-ttu-id="3cd39-156">[フィルター](xref:mvc/controllers/filters)では、フレームワークの動作を変更できるのに対して、カスタマイズではアプリ全体がどのように連携するかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-156">While [filters](xref:mvc/controllers/filters) provide a means of modifying the framework's behavior, customizations let you control how the whole app works together.</span></span>

<span data-ttu-id="3cd39-157">次の規則があります。</span><span class="sxs-lookup"><span data-stu-id="3cd39-157">The following conventions are available:</span></span>

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

<span data-ttu-id="3cd39-158">規則は、規則を MVC オプションを追加したり、`Attribute` を実装してそれらをコントローラー、アクション、またはアクション パラメーターに適用したりすることによって適用します ([`Filters`](xref:mvc/controllers/filters) と類似しています)。</span><span class="sxs-lookup"><span data-stu-id="3cd39-158">Conventions are applied by adding them to MVC options or by implementing `Attribute`s and applying them to controllers, actions, or action parameters (similar to [`Filters`](xref:mvc/controllers/filters)).</span></span> <span data-ttu-id="3cd39-159">フィルターとは異なり、規則は、各要求の一部としてではなく、アプリの起動時にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-159">Unlike filters, conventions are only executed when the app is starting, not as part of each request.</span></span>

### <a name="sample-modifying-the-applicationmodel"></a><span data-ttu-id="3cd39-160">サンプル: ApplicationModel を変更する</span><span class="sxs-lookup"><span data-stu-id="3cd39-160">Sample: Modifying the ApplicationModel</span></span>

<span data-ttu-id="3cd39-161">次の規則は、アプリケーション モデルにプロパティを追加するために使用します。</span><span class="sxs-lookup"><span data-stu-id="3cd39-161">The following convention is used to add a property to the application model.</span></span> 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

<span data-ttu-id="3cd39-162">アプリケーション モデルの規則は、MVC が `ConfigureServices` の `Startup` に追加されるときに、オプションとしてが適用されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-162">Application model conventions are applied as options when MVC is added in `ConfigureServices` in `Startup`.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

<span data-ttu-id="3cd39-163">プロパティは、コントローラー アクション内の `ActionDescriptor` プロパティ コレクションからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-163">Properties are accessible from the `ActionDescriptor` properties collection within controller actions:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a><span data-ttu-id="3cd39-164">サンプル: ControllerModel の説明を変更する</span><span class="sxs-lookup"><span data-stu-id="3cd39-164">Sample: Modifying the ControllerModel Description</span></span>

<span data-ttu-id="3cd39-165">前の例のように、コントローラー モデルを変更して、カスタム プロパティを含めることもできます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-165">As in the previous example, the controller model can also be modified to include custom properties.</span></span> <span data-ttu-id="3cd39-166">これらは、アプリケーション モデルで指定した同じ名前の既存のプロパティをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="3cd39-166">These will override existing properties with the same name specified in the application model.</span></span> <span data-ttu-id="3cd39-167">次の規則属性では、コントローラー レベルで説明が追加されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-167">The following convention attribute adds a description at the controller level:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

<span data-ttu-id="3cd39-168">この規則は、コントローラーの属性として適用されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-168">This convention is applied as an attribute on a controller.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

<span data-ttu-id="3cd39-169">"description" プロパティは、前の例と同じ方法でアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-169">The "description" property is accessed in the same manner as in previous examples.</span></span>

### <a name="sample-modifying-the-actionmodel-description"></a><span data-ttu-id="3cd39-170">サンプル: ActionModel の説明を変更する</span><span class="sxs-lookup"><span data-stu-id="3cd39-170">Sample: Modifying the ActionModel Description</span></span>

<span data-ttu-id="3cd39-171">既にアプリケーションまたはコントローラー レベルに適用されている動作をオーバーライドして、個別のアクションに別の属性規則を適用することができます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-171">A separate attribute convention can be applied to individual actions, overriding behavior already applied at the application or controller level.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

<span data-ttu-id="3cd39-172">コントローラーのアクションにこれを適用している前の例は、コントローラー レベルで規則をオーバーライドする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3cd39-172">Applying this to an action within the previous example's controller demonstrates how it overrides the controller-level convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a><span data-ttu-id="3cd39-173">例: ParameterModel を変更する</span><span class="sxs-lookup"><span data-stu-id="3cd39-173">Sample: Modifying the ParameterModel</span></span>

<span data-ttu-id="3cd39-174">次の規則をアクション パラメーターに適用して、`BindingInfo` を変更することができます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-174">The following convention can be applied to action parameters to modify their `BindingInfo`.</span></span> <span data-ttu-id="3cd39-175">次の規則では、パラメーターはルート パラメーターである必要があります。その他のバインディング ソースとなる可能性のあるものは (クエリ文字列の値など) 無視されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-175">The following convention requires that the parameter be a route parameter; other potential binding sources (such as query string values) are ignored.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

<span data-ttu-id="3cd39-176">この属性は、任意のアクション パラメーターに適用できます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-176">The attribute may be applied to any action parameter:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a><span data-ttu-id="3cd39-177">Sample: ActionModel 名を変更する</span><span class="sxs-lookup"><span data-stu-id="3cd39-177">Sample: Modifying the ActionModel Name</span></span>

<span data-ttu-id="3cd39-178">次の規則は、`ActionModel` が適用されるアクションの*名前*を更新してそれを変更します。</span><span class="sxs-lookup"><span data-stu-id="3cd39-178">The following convention modifies the `ActionModel` to update the *name* of the action to which it's applied.</span></span> <span data-ttu-id="3cd39-179">この新しい名前は、属性にパラメーターとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-179">The new name is provided as a parameter to the attribute.</span></span> <span data-ttu-id="3cd39-180">この新しい名前はルーティングによって使用されるので、このアクション メソッドに到達するのに使用されるルートに影響します。</span><span class="sxs-lookup"><span data-stu-id="3cd39-180">This new name is used by routing, so it will affect the route used to reach this action method.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

<span data-ttu-id="3cd39-181">この属性は、`HomeController` のアクション メソッドに適用されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-181">This attribute is applied to an action method in the `HomeController`:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

<span data-ttu-id="3cd39-182">メソッド名は `SomeName` ですが、このメソッド名を使用する MVC 規則をこの属性はオーバーライドし、アクション名を `MyCoolAction` に置換します。</span><span class="sxs-lookup"><span data-stu-id="3cd39-182">Even though the method name is `SomeName`, the attribute overrides the MVC convention of using the method name and replaces the action name with `MyCoolAction`.</span></span> <span data-ttu-id="3cd39-183">したがって、このアクションに到達するのに使用されるルートは、`/Home/MyCoolAction` です。</span><span class="sxs-lookup"><span data-stu-id="3cd39-183">Thus, the route used to reach this action is `/Home/MyCoolAction`.</span></span>

> [!NOTE]
> <span data-ttu-id="3cd39-184">この例は、基本的に、組み込みの [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) 属性を使用するのと同じです。</span><span class="sxs-lookup"><span data-stu-id="3cd39-184">This example is essentially the same as using the built-in [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) attribute.</span></span>

### <a name="sample-custom-routing-convention"></a><span data-ttu-id="3cd39-185">サンプル: カスタム ルーティング規則</span><span class="sxs-lookup"><span data-stu-id="3cd39-185">Sample: Custom Routing Convention</span></span>

<span data-ttu-id="3cd39-186">`IApplicationModelConvention` を使用して、ルーティングの動作をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-186">You can use an `IApplicationModelConvention` to customize how routing works.</span></span> <span data-ttu-id="3cd39-187">たとえば、次の規則は、名前空間の `.` をルートの `/` に置き換えてコントローラーの名前空間をそのルートに組み込みます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-187">For example, the following convention will incorporate Controllers' namespaces into their routes, replacing `.` in the namespace with `/` in the route:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

<span data-ttu-id="3cd39-188">この規則は、スタートアップのオプションとして追加されています。</span><span class="sxs-lookup"><span data-stu-id="3cd39-188">The convention is added as an option in Startup.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> <span data-ttu-id="3cd39-189">[ を使用し、](xref:fundamentals/middleware/index) にアクセスして、`MvcOptions`ミドルウェア`services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`に規則を追加できます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-189">You can add conventions to your [middleware](xref:fundamentals/middleware/index) by accessing `MvcOptions` using `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span></span>

<span data-ttu-id="3cd39-190">このサンプルでは、コントローラー名に "Namespace" が含まれる、属性のルーティングを使用していないルートにこの規則は適用されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-190">This sample applies this convention to routes that are not using attribute routing where the controller has  "Namespace" in its name.</span></span> <span data-ttu-id="3cd39-191">この規則は、次のコントローラーによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-191">The following controller demonstrates this convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a><span data-ttu-id="3cd39-192">WebApiCompatShim でのアプリケーション モデルの使用法</span><span class="sxs-lookup"><span data-stu-id="3cd39-192">Application Model Usage in WebApiCompatShim</span></span>

<span data-ttu-id="3cd39-193">ASP.NET Core MVC と ASP.NET Web API 2 とでは使用する規則のセットが異なります。</span><span class="sxs-lookup"><span data-stu-id="3cd39-193">ASP.NET Core MVC uses a different set of conventions from ASP.NET Web API 2.</span></span> <span data-ttu-id="3cd39-194">カスタム規則を使用すると、Web API アプリの動作と一致するように、ASP.NET Core MVC アプリの動作を変更できます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-194">Using custom conventions, you can modify an ASP.NET Core MVC app's behavior to be consistent with that of a Web API app.</span></span> <span data-ttu-id="3cd39-195">Microsoft では、この目的専用に [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) を出荷しています。</span><span class="sxs-lookup"><span data-stu-id="3cd39-195">Microsoft ships the [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) specifically for this purpose.</span></span>

> [!NOTE]
> <span data-ttu-id="3cd39-196">[ASP.NET Web API からの移行](xref:migration/webapi)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3cd39-196">Learn more about [migration from ASP.NET Web API](xref:migration/webapi).</span></span>

<span data-ttu-id="3cd39-197">Web API Compatibility Shim を使用するには、プロジェクトにパッケージを追加し、`AddWebApiConventions` の `Startup` を呼び出して MVC に規則を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3cd39-197">To use the Web API Compatibility Shim, you need to add the package to your project and then add the conventions to MVC by calling `AddWebApiConventions` in `Startup`:</span></span>

```csharp
services.AddMvc().AddWebApiConventions();
```

<span data-ttu-id="3cd39-198">Shim が提供するこの規則は、それに特定の属性が適用されたアプリの一部にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-198">The conventions provided by the shim are only applied to parts of the app that have had certain attributes applied to them.</span></span> <span data-ttu-id="3cd39-199">次の 4 つの属性は、Shim の規則でどのコントローラーが規則を変更する必要があるかを制御するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-199">The following four attributes are used to control which controllers should have their conventions modified by the shim's conventions:</span></span>

* [<span data-ttu-id="3cd39-200">UseWebApiActionConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="3cd39-200">UseWebApiActionConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [<span data-ttu-id="3cd39-201">UseWebApiOverloadingAttribute</span><span class="sxs-lookup"><span data-stu-id="3cd39-201">UseWebApiOverloadingAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [<span data-ttu-id="3cd39-202">UseWebApiParameterConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="3cd39-202">UseWebApiParameterConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [<span data-ttu-id="3cd39-203">UseWebApiRoutesAttribute</span><span class="sxs-lookup"><span data-stu-id="3cd39-203">UseWebApiRoutesAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a><span data-ttu-id="3cd39-204">アクション規則</span><span class="sxs-lookup"><span data-stu-id="3cd39-204">Action Conventions</span></span>

<span data-ttu-id="3cd39-205">`UseWebApiActionConventionsAttribute` は、名に基づいてアクションに HTTP メソッドをマップするために使用されます (たとえば、`Get` は `HttpGet` にマップされます)。</span><span class="sxs-lookup"><span data-stu-id="3cd39-205">The `UseWebApiActionConventionsAttribute` is used to map the HTTP method to actions based on their name (for instance, `Get` would map to `HttpGet`).</span></span> <span data-ttu-id="3cd39-206">これは、属性のルーティングを使用しないアクションにのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-206">It only applies to actions that don't use attribute routing.</span></span>

### <a name="overloading"></a><span data-ttu-id="3cd39-207">オーバーロード</span><span class="sxs-lookup"><span data-stu-id="3cd39-207">Overloading</span></span>

<span data-ttu-id="3cd39-208">`UseWebApiOverloadingAttribute` は、`WebApiOverloadingApplicationModelConvention` 規則を適用するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-208">The `UseWebApiOverloadingAttribute` is used to apply the `WebApiOverloadingApplicationModelConvention` convention.</span></span> <span data-ttu-id="3cd39-209">この規則は、アクションの選択プロセスに `OverloadActionConstraint` を追加します。これによって、候補のアクションは、要求で省略可能でないすべてのパラメーターが満たされるものに制限されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-209">This convention adds an `OverloadActionConstraint` to the action selection process, which limits candidate actions to those for which the request satisfies all non-optional parameters.</span></span>

### <a name="parameter-conventions"></a><span data-ttu-id="3cd39-210">パラメーター規則</span><span class="sxs-lookup"><span data-stu-id="3cd39-210">Parameter Conventions</span></span>

<span data-ttu-id="3cd39-211">`UseWebApiParameterConventionsAttribute` は、`WebApiParameterConventionsApplicationModelConvention` アクション規則を適用するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-211">The `UseWebApiParameterConventionsAttribute` is used to apply the `WebApiParameterConventionsApplicationModelConvention` action convention.</span></span> <span data-ttu-id="3cd39-212">この規則では、アクション パラメーターとして使用される単純な型は、要求本文からバインドされる複雑な型に対し、既定で URI からバインドされることが指定されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-212">This convention specifies that simple types used as action parameters are bound from the URI by default, while complex types are bound from the request body.</span></span>

### <a name="routes"></a><span data-ttu-id="3cd39-213">ルート</span><span class="sxs-lookup"><span data-stu-id="3cd39-213">Routes</span></span>

<span data-ttu-id="3cd39-214">`UseWebApiRoutesAttribute` は、`WebApiApplicationModelConvention` コントローラー規則が適用されるかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="3cd39-214">The `UseWebApiRoutesAttribute` controls whether the `WebApiApplicationModelConvention` controller convention is applied.</span></span> <span data-ttu-id="3cd39-215">これが有効な場合、この規則はルートに[領域](xref:mvc/controllers/areas)のサポートを追加するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-215">When enabled, this convention is used to add support for [areas](xref:mvc/controllers/areas) to the route.</span></span>

<span data-ttu-id="3cd39-216">互換パッケージには、規則のセットに加え、Web API によって提供されるものの代わりとなる `System.Web.Http.ApiController` 基底クラスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-216">In addition to a set of conventions, the compatibility package includes a `System.Web.Http.ApiController` base class that replaces the one provided by Web API.</span></span> <span data-ttu-id="3cd39-217">これにより、Web API 用に記述され、その `ApiController` から継承されたコントローラーが、ASP.NET Core MVC での実行中に、設計どおりに動作するようにすることを可能にします。</span><span class="sxs-lookup"><span data-stu-id="3cd39-217">This allows your controllers written for Web API and inheriting from its `ApiController` to work as they were designed, while running on ASP.NET Core MVC.</span></span> <span data-ttu-id="3cd39-218">前述のすべての `UseWebApi*` 属性は、基本コントローラー クラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-218">All of the `UseWebApi*` attributes listed earlier are applied to the base controller class.</span></span> <span data-ttu-id="3cd39-219">`ApiController` では、Web API にあるものと互換性のあるプロパティ、メソッド、および結果の型が公開されます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-219">The `ApiController` exposes properties, methods, and result types that are compatible with those found in Web API.</span></span>

## <a name="using-apiexplorer-to-document-your-app"></a><span data-ttu-id="3cd39-220">アプリをドキュメント化するための ApiExplorer の使用</span><span class="sxs-lookup"><span data-stu-id="3cd39-220">Using ApiExplorer to Document Your App</span></span>

<span data-ttu-id="3cd39-221">このアプリケーション モデルは、アプリの構造をスキャンするために使用できる [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) プロパティを各レベルで公開します。</span><span class="sxs-lookup"><span data-stu-id="3cd39-221">The application model exposes an [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) property at each level that can be used to traverse the app's structure.</span></span> <span data-ttu-id="3cd39-222">これは、[Swagger などのツールを使用して、Web API のヘルプ ページを生成する](xref:tutorials/web-api-help-pages-using-swagger)ために使用できます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-222">This can be used to [generate help pages for your Web APIs using tools like Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="3cd39-223">`ApiExplorer` プロパティは、アプリのモデルのどの部分を公開するか指定するために設定できる `IsVisible` プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="3cd39-223">The `ApiExplorer` property exposes an `IsVisible` property that can be set to specify which parts of your app's model should be exposed.</span></span> <span data-ttu-id="3cd39-224">この設定は、次の規則を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-224">You can configure this setting using a convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

<span data-ttu-id="3cd39-225">このアプローチを使用すると (必要に応じて追加の規則が必要)、アプリ内で任意のレベルで API の可視性を有効または無効にできます。</span><span class="sxs-lookup"><span data-stu-id="3cd39-225">Using this approach (and additional conventions if required), you can enable or disable API visibility at any level within your app.</span></span> 
