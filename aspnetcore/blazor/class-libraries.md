---
title: Razor コンポーネントクラスライブラリの ASP.NET Core
author: guardrex
description: コンポーネントを外部コンポーネントライブラリから Blazor アプリに組み込む方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f8e8688cdb3d1aef0d470e0e2d8c3857140ef65f
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160029"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="cfa80-103">Razor コンポーネントクラスライブラリの ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cfa80-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="cfa80-104">[Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="cfa80-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="cfa80-105">コンポーネントは、プロジェクト間で[Razor クラスライブラリ (RCL)](xref:razor-pages/ui-class)で共有できます。</span><span class="sxs-lookup"><span data-stu-id="cfa80-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="cfa80-106">*Razor コンポーネントクラスライブラリ*は、次のものからインクルードできます。</span><span class="sxs-lookup"><span data-stu-id="cfa80-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="cfa80-107">ソリューション内の別のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="cfa80-107">Another project in the solution.</span></span>
* <span data-ttu-id="cfa80-108">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="cfa80-108">A NuGet package.</span></span>
* <span data-ttu-id="cfa80-109">参照された .NET ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="cfa80-109">A referenced .NET library.</span></span>

<span data-ttu-id="cfa80-110">コンポーネントが通常の .NET 型であるのと同様に、RCL によって提供されるコンポーネントは通常の .NET アセンブリです。</span><span class="sxs-lookup"><span data-stu-id="cfa80-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="cfa80-111">RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="cfa80-111">Create an RCL</span></span>

<span data-ttu-id="cfa80-112"><xref:blazor/get-started> の記事のガイダンスに従って、Blazor用に環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cfa80-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cfa80-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cfa80-114">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-114">Create a new project.</span></span>
1. <span data-ttu-id="cfa80-115">**[Razor クラスライブラリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="cfa80-116">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-116">Select **Next**.</span></span>
1. <span data-ttu-id="cfa80-117">**[新しい Razor クラスライブラリを作成]** します ダイアログで、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="cfa80-118">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="cfa80-119">このトピックの例では、`MyComponentLib1`プロジェクト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="cfa80-120">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-120">Select **Create**.</span></span>
1. <span data-ttu-id="cfa80-121">RCL をソリューションに追加します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="cfa80-122">ソリューションを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="cfa80-122">Right-click the solution.</span></span> <span data-ttu-id="cfa80-123">**[追加]**  >  **[既存のプロジェクト]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="cfa80-124">RCL のプロジェクトファイルに移動します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="cfa80-125">RCL のプロジェクトファイル ( *.csproj*) を選択します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="cfa80-126">アプリから RCL の参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="cfa80-127">アプリプロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="cfa80-127">Right-click the app project.</span></span> <span data-ttu-id="cfa80-128">[ > **参照**の**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="cfa80-129">RCL プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-129">Select the RCL project.</span></span> <span data-ttu-id="cfa80-130">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-130">Select **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="cfa80-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cfa80-131">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="cfa80-132">コマンドシェルで[dotnet new](/dotnet/core/tools/dotnet-new)コマンドを使用して、 **Razor クラスライブラリ**テンプレート (`razorclasslib`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-132">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="cfa80-133">次の例では、`MyComponentLib1`という名前の RCL が作成されます。</span><span class="sxs-lookup"><span data-stu-id="cfa80-133">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="cfa80-134">コマンドの実行時に、`MyComponentLib1` を保持するフォルダーが自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="cfa80-134">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="cfa80-135">既存のプロジェクトにライブラリを追加するには、コマンドシェルで dotnet の [[参照の追加](/dotnet/core/tools/dotnet-add-reference)] コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-135">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="cfa80-136">次の例では、RCL がアプリに追加されています。</span><span class="sxs-lookup"><span data-stu-id="cfa80-136">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="cfa80-137">ライブラリへのパスを使用して、アプリのプロジェクトフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-137">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="cfa80-138">ライブラリコンポーネントの使用</span><span class="sxs-lookup"><span data-stu-id="cfa80-138">Consume a library component</span></span>

<span data-ttu-id="cfa80-139">別のプロジェクトのライブラリで定義されているコンポーネントを使用するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-139">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="cfa80-140">名前空間で完全な型名を使用します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-140">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="cfa80-141">Razor の[\@using ディレクティブを](xref:mvc/views/razor#using)使用します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-141">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="cfa80-142">個々のコンポーネントを名前で追加することができます。</span><span class="sxs-lookup"><span data-stu-id="cfa80-142">Individual components can be added by name.</span></span>

<span data-ttu-id="cfa80-143">次の例では、`MyComponentLib1` は `SalesReport` コンポーネントを含むコンポーネントライブラリです。</span><span class="sxs-lookup"><span data-stu-id="cfa80-143">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="cfa80-144">名前空間を持つ完全な型名を使用して、`SalesReport` コンポーネントを参照できます。</span><span class="sxs-lookup"><span data-stu-id="cfa80-144">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="cfa80-145">また、ライブラリが `@using` ディレクティブを使用してスコープ内にある場合は、このコンポーネントを参照することもできます。</span><span class="sxs-lookup"><span data-stu-id="cfa80-145">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="cfa80-146">ライブラリのコンポーネントをプロジェクト全体で使用できるようにするには、トップレベルの *_Import razor*ファイルに `@using MyComponentLib1` ディレクティブを含めます。</span><span class="sxs-lookup"><span data-stu-id="cfa80-146">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="cfa80-147">ディレクティブを任意のレベルの *_Import の razor*ファイルに追加して、名前空間をフォルダー内の1つのページまたは一連のページに適用します。</span><span class="sxs-lookup"><span data-stu-id="cfa80-147">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="cfa80-148">NuGet のビルド、パック、出荷</span><span class="sxs-lookup"><span data-stu-id="cfa80-148">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="cfa80-149">コンポーネントライブラリは標準の .NET ライブラリであるため、パッケージを NuGet にパッケージ化して配布することは、ライブラリをパッケージ化して NuGet に配布する場合と変わりはありません。</span><span class="sxs-lookup"><span data-stu-id="cfa80-149">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="cfa80-150">パッケージ化は、コマンドシェルで[dotnet pack](/dotnet/core/tools/dotnet-pack)コマンドを使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="cfa80-150">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="cfa80-151">コマンドシェルで[dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push)コマンドを使用して、パッケージを nuget にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="cfa80-151">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="cfa80-152">静的なアセットを含む Razor コンポーネントクラスライブラリを作成する</span><span class="sxs-lookup"><span data-stu-id="cfa80-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="cfa80-153">RCL には、静的なアセットを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="cfa80-153">An RCL can include static assets.</span></span> <span data-ttu-id="cfa80-154">この静的アセットは、ライブラリを使用するすべてのアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cfa80-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="cfa80-155">詳細については、「 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cfa80-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cfa80-156">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="cfa80-156">Additional resources</span></span>

* <xref:razor-pages/ui-class>
