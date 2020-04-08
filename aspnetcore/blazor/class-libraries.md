---
title: ASP.NET Core Razor コンポーネント クラス ライブラリ
author: guardrex
description: 外部コンポーネント ライブラリから、コンポーネントを Blazor アプリに含める方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218767"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="70697-103">ASP.NET Core Razor コンポーネント クラス ライブラリ</span><span class="sxs-lookup"><span data-stu-id="70697-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="70697-104">作成者: [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="70697-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="70697-105">コンポーネントは、[Razor クラス ライブラリ (RCL)](xref:razor-pages/ui-class) 内でプロジェクト間で共有できます。</span><span class="sxs-lookup"><span data-stu-id="70697-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="70697-106">*Razor コンポーネント クラス ライブラリ*は、次から含めることができます。</span><span class="sxs-lookup"><span data-stu-id="70697-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="70697-107">ソリューションの別のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="70697-107">Another project in the solution.</span></span>
* <span data-ttu-id="70697-108">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="70697-108">A NuGet package.</span></span>
* <span data-ttu-id="70697-109">参照されている .NET ライブラリ。</span><span class="sxs-lookup"><span data-stu-id="70697-109">A referenced .NET library.</span></span>

<span data-ttu-id="70697-110">コンポーネントが通常の .NET 型であるのと同様に、RCL によって提供されるコンポーネントは通常の .NET アセンブリです。</span><span class="sxs-lookup"><span data-stu-id="70697-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="70697-111">RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="70697-111">Create an RCL</span></span>

<span data-ttu-id="70697-112"><xref:blazor/get-started> の記事のガイダンスに従って、Blazor 用の環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="70697-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="70697-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70697-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="70697-114">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="70697-114">Create a new project.</span></span>
1. <span data-ttu-id="70697-115">**[Razor クラス ライブラリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="70697-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="70697-116">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="70697-116">Select **Next**.</span></span>
1. <span data-ttu-id="70697-117">**[Create a new Razor class library]** (新しい Razor クラスライブラリの作成) ダイアログで、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="70697-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="70697-118">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="70697-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="70697-119">このトピックの例では、プロジェクト名 `MyComponentLib1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="70697-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="70697-120">**作成** を選択します。</span><span class="sxs-lookup"><span data-stu-id="70697-120">Select **Create**.</span></span>
1. <span data-ttu-id="70697-121">RCL をソリューションに追加します。</span><span class="sxs-lookup"><span data-stu-id="70697-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="70697-122">ソリューションを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="70697-122">Right-click the solution.</span></span> <span data-ttu-id="70697-123">**[追加]**  >  **[既存のプロジェクト]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="70697-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="70697-124">RCL のプロジェクト ファイルに移動します。</span><span class="sxs-lookup"><span data-stu-id="70697-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="70697-125">RCL のプロジェクト ファイル ( *.csproj*) を選択します。</span><span class="sxs-lookup"><span data-stu-id="70697-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="70697-126">アプリから RCL の参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="70697-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="70697-127">アプリ プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="70697-127">Right-click the app project.</span></span> <span data-ttu-id="70697-128">**[追加]**  >  **[参照]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="70697-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="70697-129">RCL プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="70697-129">Select the RCL project.</span></span> <span data-ttu-id="70697-130">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="70697-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="70697-131">テンプレートから RCL を生成するときに **[ページとビューのサポート]** チェック ボックスがオンになっている場合は、次のコンテンツで生成されたプロジェクトのルートに、 *_Imports.razor* ファイルも追加して、Razor コンポーネントの作成を有効にします。</span><span class="sxs-lookup"><span data-stu-id="70697-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="70697-132">生成されたプロジェクトのルートにファイルを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="70697-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="70697-133">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="70697-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="70697-134">コマンド シェルで **dotnet new** コマンドを使用して、`razorclasslib`Razor クラス ライブラリ[ テンプレート (](/dotnet/core/tools/dotnet-new)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="70697-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="70697-135">次の例では、`MyComponentLib1` という名前の RCL が作成されます。</span><span class="sxs-lookup"><span data-stu-id="70697-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="70697-136">コマンドの実行時に、`MyComponentLib1` を保持するフォルダーが自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="70697-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="70697-137">テンプレートから RCL を生成するときに、`-s|--support-pages-and-views` スイッチが使用されている場合、次のコンテンツで生成されたプロジェクトのルートに、 *_Imports.razor* ファイルも追加して、Razor コンポーネントの作成を有効にします。</span><span class="sxs-lookup"><span data-stu-id="70697-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="70697-138">生成されたプロジェクトのルートにファイルを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="70697-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="70697-139">既存のプロジェクトにライブラリを追加するには、コマンド シェルで [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="70697-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="70697-140">次の例では、RCL がアプリに追加されています。</span><span class="sxs-lookup"><span data-stu-id="70697-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="70697-141">ライブラリへのパスを使用して、アプリのプロジェクト フォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="70697-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="70697-142">ライブラリ コンポーネントの使用</span><span class="sxs-lookup"><span data-stu-id="70697-142">Consume a library component</span></span>

<span data-ttu-id="70697-143">別のプロジェクトのライブラリに定義されているコンポーネントを使用するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="70697-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="70697-144">名前空間と完全な型名を使用します。</span><span class="sxs-lookup"><span data-stu-id="70697-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="70697-145">Razor の [\@using](xref:mvc/views/razor#using) ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="70697-145">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="70697-146">個々のコンポーネントを名前で追加することができます。</span><span class="sxs-lookup"><span data-stu-id="70697-146">Individual components can be added by name.</span></span>

<span data-ttu-id="70697-147">次の例で、`MyComponentLib1` は `SalesReport` コンポーネントを含むコンポーネント ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="70697-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="70697-148">名前空間と完全な型名を使用して、`SalesReport` コンポーネントを参照できます。</span><span class="sxs-lookup"><span data-stu-id="70697-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="70697-149">また、ライブラリが `@using` ディレクティブを使用して、スコープ内に取り込まれている場合も、このコンポーネントを参照できます。</span><span class="sxs-lookup"><span data-stu-id="70697-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="70697-150">プロジェクト全体でライブラリのコンポーネントを使用できるようにするには、最上位の `@using MyComponentLib1`_Import.razor*ファイルに* ディレクティブを含めます。</span><span class="sxs-lookup"><span data-stu-id="70697-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="70697-151">ディレクティブを任意のレベルの *_Import.razor* ファイルに追加して、名前空間をフォルダー内の 1 つまたは複数のページに適用します。</span><span class="sxs-lookup"><span data-stu-id="70697-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="70697-152">静的アセットを含む Razor コンポーネント クラスライブラリを作成する</span><span class="sxs-lookup"><span data-stu-id="70697-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="70697-153">RCL には、静的アセットを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="70697-153">An RCL can include static assets.</span></span> <span data-ttu-id="70697-154">静的アセットは、ライブラリを使用するすべてのアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="70697-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="70697-155">詳細については、「<xref:razor-pages/ui-class#create-an-rcl-with-static-assets>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="70697-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="70697-156">ビルド、パック、NuGet への配布</span><span class="sxs-lookup"><span data-stu-id="70697-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="70697-157">コンポーネント ライブラリは標準 .NET ライブラリであるため、それらをパッケージ化して NuGet に配布することは、ライブラリをパッケージ化して NuGet に配布する場合と変わりはありません。</span><span class="sxs-lookup"><span data-stu-id="70697-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="70697-158">パッケージ化は、コマンド シェルで [dotnet pack](/dotnet/core/tools/dotnet-pack) コマンドを使用して実行します。</span><span class="sxs-lookup"><span data-stu-id="70697-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="70697-159">コマンド シェルで [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) コマンドを使用して、パッケージを NuGet にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="70697-159">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="70697-160">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="70697-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="70697-161">XML リンカー構成ファイルをライブラリに追加する</span><span class="sxs-lookup"><span data-stu-id="70697-161">Add an XML linker configuration file to a library</span></span>](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
