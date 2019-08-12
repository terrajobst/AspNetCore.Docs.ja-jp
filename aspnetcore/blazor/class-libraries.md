---
title: Razor コンポーネントクラスライブラリの ASP.NET Core
author: guardrex
description: コンポーネントを外部コンポーネントライブラリから Blazor アプリに組み込む方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/class-libraries
ms.openlocfilehash: 402b7b072554f63f85e7cf5e55336104d235a071
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68948442"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="7df30-103">Razor コンポーネントクラスライブラリの ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7df30-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="7df30-104">[Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="7df30-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="7df30-105">コンポーネントは、プロジェクト間で[Razor クラスライブラリ (RCL)](xref:razor-pages/ui-class)で共有できます。</span><span class="sxs-lookup"><span data-stu-id="7df30-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="7df30-106">*Razor コンポーネントクラスライブラリ*は、次のものからインクルードできます。</span><span class="sxs-lookup"><span data-stu-id="7df30-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="7df30-107">ソリューション内の別のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="7df30-107">Another project in the solution.</span></span>
* <span data-ttu-id="7df30-108">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="7df30-108">A NuGet package.</span></span>
* <span data-ttu-id="7df30-109">参照された .NET ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="7df30-109">A referenced .NET library.</span></span>

<span data-ttu-id="7df30-110">コンポーネントが通常の .NET 型であるのと同様に、RCL によって提供されるコンポーネントは通常の .NET アセンブリです。</span><span class="sxs-lookup"><span data-stu-id="7df30-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="7df30-111">RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="7df30-111">Create an RCL</span></span>

<span data-ttu-id="7df30-112"><xref:blazor/get-started>記事のガイダンスに従って、Blazor 用に環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="7df30-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7df30-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7df30-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7df30-114">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="7df30-114">Create a new project.</span></span>
1. <span data-ttu-id="7df30-115">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7df30-115">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="7df30-116">          **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7df30-116">Select **Next**.</span></span>
1. <span data-ttu-id="7df30-117">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="7df30-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="7df30-118">このトピックの例では、プロジェクト名`MyComponentLib1`を使用します。</span><span class="sxs-lookup"><span data-stu-id="7df30-118">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="7df30-119">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7df30-119">Select **Create**.</span></span>
1. <span data-ttu-id="7df30-120">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.0]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7df30-120">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="7df30-121">**Razor クラスライブラリ**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="7df30-121">Select the **Razor Class Library** template.</span></span> <span data-ttu-id="7df30-122">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7df30-122">Select **Create**.</span></span>
1. <span data-ttu-id="7df30-123">RCL をソリューションに追加します。</span><span class="sxs-lookup"><span data-stu-id="7df30-123">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="7df30-124">ソリューションを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="7df30-124">Right-click the solution.</span></span> <span data-ttu-id="7df30-125">[**既存のプロジェクト**の**追加** > ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="7df30-125">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="7df30-126">RCL のプロジェクトファイルに移動します。</span><span class="sxs-lookup"><span data-stu-id="7df30-126">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="7df30-127">RCL のプロジェクトファイル ( *.csproj*) を選択します。</span><span class="sxs-lookup"><span data-stu-id="7df30-127">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="7df30-128">アプリから RCL の参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="7df30-128">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="7df30-129">アプリプロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="7df30-129">Right-click the app project.</span></span> <span data-ttu-id="7df30-130">[**参照**の**追加** > ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="7df30-130">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="7df30-131">RCL プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="7df30-131">Select the RCL project.</span></span> <span data-ttu-id="7df30-132">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7df30-132">Select **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7df30-133">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7df30-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="7df30-134">コマンドシェルの[dotnet new](/dotnet/core/tools/dotnet-new)コマンドで`razorclasslib` **Razor クラスライブラリ**テンプレート () を使用します。</span><span class="sxs-lookup"><span data-stu-id="7df30-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="7df30-135">次の例では、という名前`MyComponentLib1`の rcl が作成されます。</span><span class="sxs-lookup"><span data-stu-id="7df30-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="7df30-136">を保持`MyComponentLib1`するフォルダーは、コマンドの実行時に自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="7df30-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="7df30-137">既存のプロジェクトにライブラリを追加するには、コマンドシェルで dotnet の [[参照の追加](/dotnet/core/tools/dotnet-add-reference)] コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="7df30-137">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="7df30-138">次の例では、RCL がアプリに追加されています。</span><span class="sxs-lookup"><span data-stu-id="7df30-138">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="7df30-139">ライブラリへのパスを使用して、アプリのプロジェクトフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7df30-139">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="rcls-not-supported-for-client-side-apps"></a><span data-ttu-id="7df30-140">クライアント側アプリでは、RCLs はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="7df30-140">RCLs not supported for client-side apps</span></span>

<span data-ttu-id="7df30-141">現在の ASP.NET Core 3.0 プレビューでは、Razor クラスライブラリは Blazor クライアント側アプリと互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="7df30-141">In the current ASP.NET Core 3.0 Preview, Razor class libraries aren't compatible with Blazor client-side apps.</span></span> <span data-ttu-id="7df30-142">Blazor クライアント側アプリの場合は、コマンドシェルで`blazorlib`テンプレートによって作成された Blazor コンポーネントライブラリを使用します。</span><span class="sxs-lookup"><span data-stu-id="7df30-142">For Blazor client-side apps, use a Blazor component library created by the `blazorlib` template in a command shell:</span></span>

```console
dotnet new blazorlib -o MyComponentLib1
```

<span data-ttu-id="7df30-143">`blazorlib`テンプレートを使用するコンポーネントライブラリには、イメージ、JavaScript、スタイルシートなどの静的ファイルを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="7df30-143">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="7df30-144">ビルド時には、静的ファイルがビルドされたアセンブリファイル ( *.dll*) に埋め込まれます。これにより、リソースを含める方法を気にすることなく、コンポーネントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="7df30-144">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="7df30-145">`content`ディレクトリに含まれるすべてのファイルは、埋め込みリソースとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="7df30-145">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="7df30-146">ライブラリコンポーネントの使用</span><span class="sxs-lookup"><span data-stu-id="7df30-146">Consume a library component</span></span>

<span data-ttu-id="7df30-147">別のプロジェクトのライブラリで定義されているコンポーネントを使用するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="7df30-147">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="7df30-148">名前空間で完全な型名を使用します。</span><span class="sxs-lookup"><span data-stu-id="7df30-148">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="7df30-149">Razor の[ \@using](xref:mvc/views/razor#using)ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="7df30-149">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="7df30-150">個々のコンポーネントを名前で追加することができます。</span><span class="sxs-lookup"><span data-stu-id="7df30-150">Individual components can be added by name.</span></span>

<span data-ttu-id="7df30-151">次の例では`MyComponentLib1` 、は`SalesReport`コンポーネントを含むコンポーネントライブラリです。</span><span class="sxs-lookup"><span data-stu-id="7df30-151">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="7df30-152">名前`SalesReport`空間を持つ完全な型名を使用して、コンポーネントを参照できます。</span><span class="sxs-lookup"><span data-stu-id="7df30-152">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="7df30-153">`@using`ディレクティブを使用してライブラリがスコープ内にある場合は、このコンポーネントを参照することもできます。</span><span class="sxs-lookup"><span data-stu-id="7df30-153">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="7df30-154">ライブラリの`@using MyComponentLib1`コンポーネントをプロジェクト全体で使用できるようにするには、トップレベルの*インポートの razor*ファイルにディレクティブを含めます。</span><span class="sxs-lookup"><span data-stu-id="7df30-154">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="7df30-155">ディレクティブを任意のレベルの*インポート*ファイルに追加して、名前空間をフォルダー内の1つのページまたは一連のページに適用します。</span><span class="sxs-lookup"><span data-stu-id="7df30-155">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="7df30-156">NuGet のビルド、パック、出荷</span><span class="sxs-lookup"><span data-stu-id="7df30-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="7df30-157">コンポーネントライブラリは標準の .NET ライブラリであるため、パッケージを NuGet にパッケージ化して配布することは、ライブラリをパッケージ化して NuGet に配布する場合と変わりはありません。</span><span class="sxs-lookup"><span data-stu-id="7df30-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="7df30-158">パッケージ化は、コマンドシェルで[dotnet pack](/dotnet/core/tools/dotnet-pack)コマンドを使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="7df30-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```console
dotnet pack
```

<span data-ttu-id="7df30-159">コマンドシェルで[dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push)コマンドを使用して、nuget にパッケージをアップロードします。</span><span class="sxs-lookup"><span data-stu-id="7df30-159">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command in a command shell:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="7df30-160">`blazorlib`テンプレートを使用すると、静的リソースが NuGet パッケージに含まれます。</span><span class="sxs-lookup"><span data-stu-id="7df30-160">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="7df30-161">ライブラリコンシューマーは自動的にスクリプトとスタイルシートを受け取るため、リソースを手動でインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7df30-161">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="7df30-162">静的なアセットを含む Razor コンポーネントクラスライブラリを作成する</span><span class="sxs-lookup"><span data-stu-id="7df30-162">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="7df30-163">RCL には、静的なアセットを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="7df30-163">An RCL can include static assets.</span></span> <span data-ttu-id="7df30-164">この静的アセットは、ライブラリを使用するすべてのアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="7df30-164">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="7df30-165">詳細については、「 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7df30-165">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7df30-166">その他の資料</span><span class="sxs-lookup"><span data-stu-id="7df30-166">Additional resources</span></span>

* <xref:razor-pages/ui-class>
