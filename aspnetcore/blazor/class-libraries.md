---
title: Razor コンポーネントクラスライブラリの ASP.NET Core
author: guardrex
description: コンポーネントを外部コンポーネントライブラリから Blazor アプリに組み込む方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/class-libraries
ms.openlocfilehash: 6e93d48bbc684845952c3db8935ccc8b190044b7
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030344"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="f1564-103">Razor コンポーネントクラスライブラリの ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f1564-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="f1564-104">[Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="f1564-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="f1564-105">コンポーネントは、プロジェクト間で[Razor クラスライブラリ (RCL)](xref:razor-pages/ui-class)で共有できます。</span><span class="sxs-lookup"><span data-stu-id="f1564-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="f1564-106">*Razor コンポーネントクラスライブラリ*は、次のものからインクルードできます。</span><span class="sxs-lookup"><span data-stu-id="f1564-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="f1564-107">ソリューション内の別のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="f1564-107">Another project in the solution.</span></span>
* <span data-ttu-id="f1564-108">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="f1564-108">A NuGet package.</span></span>
* <span data-ttu-id="f1564-109">参照された .NET ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="f1564-109">A referenced .NET library.</span></span>

<span data-ttu-id="f1564-110">コンポーネントが通常の .NET 型であるのと同様に、RCL によって提供されるコンポーネントは通常の .NET アセンブリです。</span><span class="sxs-lookup"><span data-stu-id="f1564-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="f1564-111">RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="f1564-111">Create an RCL</span></span>

<span data-ttu-id="f1564-112"><xref:blazor/get-started>記事のガイダンスに従って、Blazor 用に環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="f1564-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f1564-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1564-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f1564-114">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f1564-114">Create a new project.</span></span>
1. <span data-ttu-id="f1564-115">[ **Razor クラスライブラリ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1564-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="f1564-116">          **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1564-116">Select **Next**.</span></span>
1. <span data-ttu-id="f1564-117">[**新しい Razor クラスライブラリを作成**します] ダイアログで、[**作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1564-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="f1564-118">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="f1564-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f1564-119">このトピックの例では、プロジェクト名`MyComponentLib1`を使用します。</span><span class="sxs-lookup"><span data-stu-id="f1564-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="f1564-120">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1564-120">Select **Create**.</span></span>
1. <span data-ttu-id="f1564-121">RCL をソリューションに追加します。</span><span class="sxs-lookup"><span data-stu-id="f1564-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="f1564-122">ソリューションを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="f1564-122">Right-click the solution.</span></span> <span data-ttu-id="f1564-123">[**既存のプロジェクト**の**追加** > ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1564-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="f1564-124">RCL のプロジェクトファイルに移動します。</span><span class="sxs-lookup"><span data-stu-id="f1564-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="f1564-125">RCL のプロジェクトファイル ( *.csproj*) を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1564-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="f1564-126">アプリから RCL の参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="f1564-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="f1564-127">アプリプロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="f1564-127">Right-click the app project.</span></span> <span data-ttu-id="f1564-128">[**参照**の**追加** > ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1564-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="f1564-129">RCL プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="f1564-129">Select the RCL project.</span></span> <span data-ttu-id="f1564-130">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1564-130">Select **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f1564-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f1564-131">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="f1564-132">コマンドシェルの[dotnet new](/dotnet/core/tools/dotnet-new)コマンドで`razorclasslib` **Razor クラスライブラリ**テンプレート () を使用します。</span><span class="sxs-lookup"><span data-stu-id="f1564-132">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="f1564-133">次の例では、という名前`MyComponentLib1`の rcl が作成されます。</span><span class="sxs-lookup"><span data-stu-id="f1564-133">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="f1564-134">を保持`MyComponentLib1`するフォルダーは、コマンドの実行時に自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="f1564-134">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="f1564-135">既存のプロジェクトにライブラリを追加するには、コマンドシェルで dotnet の [[参照の追加](/dotnet/core/tools/dotnet-add-reference)] コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="f1564-135">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="f1564-136">次の例では、RCL がアプリに追加されています。</span><span class="sxs-lookup"><span data-stu-id="f1564-136">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="f1564-137">ライブラリへのパスを使用して、アプリのプロジェクトフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1564-137">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="rcls-not-supported-for-client-side-apps"></a><span data-ttu-id="f1564-138">クライアント側アプリでは、RCLs はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="f1564-138">RCLs not supported for client-side apps</span></span>

<span data-ttu-id="f1564-139">現在の ASP.NET Core 3.0 プレビューでは、Razor クラスライブラリは Blazor クライアント側アプリと互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="f1564-139">In the current ASP.NET Core 3.0 Preview, Razor class libraries aren't compatible with Blazor client-side apps.</span></span> <span data-ttu-id="f1564-140">Blazor クライアント側アプリの場合は、コマンドシェルで`blazorlib`テンプレートによって作成された Blazor コンポーネントライブラリを使用します。</span><span class="sxs-lookup"><span data-stu-id="f1564-140">For Blazor client-side apps, use a Blazor component library created by the `blazorlib` template in a command shell:</span></span>

```console
dotnet new blazorlib -o MyComponentLib1
```

<span data-ttu-id="f1564-141">`blazorlib`テンプレートを使用するコンポーネントライブラリには、イメージ、JavaScript、スタイルシートなどの静的ファイルを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f1564-141">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="f1564-142">ビルド時には、静的ファイルがビルドされたアセンブリファイル ( *.dll*) に埋め込まれます。これにより、リソースを含める方法を気にすることなく、コンポーネントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1564-142">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="f1564-143">`content`ディレクトリに含まれるすべてのファイルは、埋め込みリソースとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="f1564-143">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="f1564-144">ライブラリコンポーネントの使用</span><span class="sxs-lookup"><span data-stu-id="f1564-144">Consume a library component</span></span>

<span data-ttu-id="f1564-145">別のプロジェクトのライブラリで定義されているコンポーネントを使用するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="f1564-145">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="f1564-146">名前空間で完全な型名を使用します。</span><span class="sxs-lookup"><span data-stu-id="f1564-146">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="f1564-147">Razor の[ \@using](xref:mvc/views/razor#using)ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="f1564-147">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="f1564-148">個々のコンポーネントを名前で追加することができます。</span><span class="sxs-lookup"><span data-stu-id="f1564-148">Individual components can be added by name.</span></span>

<span data-ttu-id="f1564-149">次の例では`MyComponentLib1` 、は`SalesReport`コンポーネントを含むコンポーネントライブラリです。</span><span class="sxs-lookup"><span data-stu-id="f1564-149">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="f1564-150">名前`SalesReport`空間を持つ完全な型名を使用して、コンポーネントを参照できます。</span><span class="sxs-lookup"><span data-stu-id="f1564-150">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="f1564-151">`@using`ディレクティブを使用してライブラリがスコープ内にある場合は、このコンポーネントを参照することもできます。</span><span class="sxs-lookup"><span data-stu-id="f1564-151">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="f1564-152">ライブラリの`@using MyComponentLib1`コンポーネントをプロジェクト全体で使用できるようにするには、トップレベルの*インポートの razor*ファイルにディレクティブを含めます。</span><span class="sxs-lookup"><span data-stu-id="f1564-152">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="f1564-153">ディレクティブを任意のレベルの*インポート*ファイルに追加して、名前空間をフォルダー内の1つのページまたは一連のページに適用します。</span><span class="sxs-lookup"><span data-stu-id="f1564-153">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="f1564-154">NuGet のビルド、パック、出荷</span><span class="sxs-lookup"><span data-stu-id="f1564-154">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="f1564-155">コンポーネントライブラリは標準の .NET ライブラリであるため、パッケージを NuGet にパッケージ化して配布することは、ライブラリをパッケージ化して NuGet に配布する場合と変わりはありません。</span><span class="sxs-lookup"><span data-stu-id="f1564-155">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="f1564-156">パッケージ化は、コマンドシェルで[dotnet pack](/dotnet/core/tools/dotnet-pack)コマンドを使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="f1564-156">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```console
dotnet pack
```

<span data-ttu-id="f1564-157">コマンドシェルで[dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push)コマンドを使用して、nuget にパッケージをアップロードします。</span><span class="sxs-lookup"><span data-stu-id="f1564-157">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command in a command shell:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="f1564-158">`blazorlib`テンプレートを使用すると、静的リソースが NuGet パッケージに含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1564-158">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="f1564-159">ライブラリコンシューマーは自動的にスクリプトとスタイルシートを受け取るため、リソースを手動でインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f1564-159">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="f1564-160">静的なアセットを含む Razor コンポーネントクラスライブラリを作成する</span><span class="sxs-lookup"><span data-stu-id="f1564-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="f1564-161">RCL には、静的なアセットを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f1564-161">An RCL can include static assets.</span></span> <span data-ttu-id="f1564-162">この静的アセットは、ライブラリを使用するすべてのアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1564-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="f1564-163">詳細については、「 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f1564-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f1564-164">その他の資料</span><span class="sxs-lookup"><span data-stu-id="f1564-164">Additional resources</span></span>

* <xref:razor-pages/ui-class>
