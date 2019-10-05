---
title: OpenAPI を使用した ASP.NET Core アプリの開発
author: ryanbrandenburg
description: Microsoft.dotnet-openapi ツールを使用して OpenAPI ファイルへの参照を追加する方法を説明します。
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: f5eae9e871bc8efc30d500769adb845ff244a90c
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317772"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="bb0c1-103">OpenAPI ツールを使用した ASP.NET Core アプリの開発</span><span class="sxs-lookup"><span data-stu-id="bb0c1-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="bb0c1-104">作成者: Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="bb0c1-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="bb0c1-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) は、プロジェクト内での [OpenAPI](https://github.com/OAI/OpenAPI-Specification) 参照を管理するための [.NET Core グローバル ツール](/dotnet/core/tools/global-tools)です。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="bb0c1-106">インストール</span><span class="sxs-lookup"><span data-stu-id="bb0c1-106">Installation</span></span>

<span data-ttu-id="bb0c1-107">`Microsoft.dotnet-openapi` をインストールするには次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="bb0c1-108">追加</span><span class="sxs-lookup"><span data-stu-id="bb0c1-108">Add</span></span>

<span data-ttu-id="bb0c1-109">このページのコマンドのいずれかを使用して OpenAPI 参照を追加すると、次のような `<OpenApiReference />` 要素が *.csproj* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="bb0c1-110">前の参照は、生成されるクライアント コードをアプリが呼び出すために必要です。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/aspnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -v|--verbose | Show verbose output. |dotnet openapi add project *-v* ../Ref/ProjRef.csproj |
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="bb0c1-111">ファイルの追加</span><span class="sxs-lookup"><span data-stu-id="bb0c1-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="bb0c1-112">オプション</span><span class="sxs-lookup"><span data-stu-id="bb0c1-112">Options</span></span>

| <span data-ttu-id="bb0c1-113">短いオプション</span><span class="sxs-lookup"><span data-stu-id="bb0c1-113">Short option</span></span>| <span data-ttu-id="bb0c1-114">長いオプション</span><span class="sxs-lookup"><span data-stu-id="bb0c1-114">Long option</span></span>| <span data-ttu-id="bb0c1-115">説明</span><span class="sxs-lookup"><span data-stu-id="bb0c1-115">Description</span></span> | <span data-ttu-id="bb0c1-116">例</span><span class="sxs-lookup"><span data-stu-id="bb0c1-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="bb0c1-117">-v</span><span class="sxs-lookup"><span data-stu-id="bb0c1-117">-v</span></span>|<span data-ttu-id="bb0c1-118">--verbose</span><span class="sxs-lookup"><span data-stu-id="bb0c1-118">--verbose</span></span> | <span data-ttu-id="bb0c1-119">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-119">Show verbose output.</span></span> |<span data-ttu-id="bb0c1-120">dotnet openapi add file *-v* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="bb0c1-120">dotnet openapi add file *-v* .\OpenAPI.json</span></span> |
| <span data-ttu-id="bb0c1-121">-p</span><span class="sxs-lookup"><span data-stu-id="bb0c1-121">-p</span></span>|<span data-ttu-id="bb0c1-122">--updateProject</span><span class="sxs-lookup"><span data-stu-id="bb0c1-122">--updateProject</span></span> | <span data-ttu-id="bb0c1-123">操作対象のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-123">The project to operate on.</span></span> |<span data-ttu-id="bb0c1-124">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="bb0c1-124">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="bb0c1-125">-c</span><span class="sxs-lookup"><span data-stu-id="bb0c1-125">-c</span></span>|<span data-ttu-id="bb0c1-126">--code-generator</span><span class="sxs-lookup"><span data-stu-id="bb0c1-126">--code-generator</span></span>| <span data-ttu-id="bb0c1-127">参照に適用するコード ジェネレーター。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-127">The code generator to apply to the reference.</span></span> <span data-ttu-id="bb0c1-128">オプションは `NSwagCSharp` と `NSwagTypeScript` です。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-128">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="bb0c1-129">`--code-generator` を指定しない場合、ツールは既定で `NSwagCSharp` になります。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-129">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="bb0c1-130">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="bb0c1-130">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="bb0c1-131">-h</span><span class="sxs-lookup"><span data-stu-id="bb0c1-131">-h</span></span>|<span data-ttu-id="bb0c1-132">--help</span><span class="sxs-lookup"><span data-stu-id="bb0c1-132">--help</span></span>|<span data-ttu-id="bb0c1-133">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-133">Show help information</span></span>|<span data-ttu-id="bb0c1-134">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="bb0c1-134">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="bb0c1-135">引数</span><span class="sxs-lookup"><span data-stu-id="bb0c1-135">Arguments</span></span>

|  <span data-ttu-id="bb0c1-136">引数</span><span class="sxs-lookup"><span data-stu-id="bb0c1-136">Argument</span></span>  | <span data-ttu-id="bb0c1-137">説明</span><span class="sxs-lookup"><span data-stu-id="bb0c1-137">Description</span></span> | <span data-ttu-id="bb0c1-138">例</span><span class="sxs-lookup"><span data-stu-id="bb0c1-138">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="bb0c1-139">source-file</span><span class="sxs-lookup"><span data-stu-id="bb0c1-139">source-file</span></span> | <span data-ttu-id="bb0c1-140">参照の作成元のソース。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-140">The source to create a reference from.</span></span> <span data-ttu-id="bb0c1-141">OpenAPI ファイルである必要があります。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-141">Must be an OpenAPI file.</span></span> |<span data-ttu-id="bb0c1-142">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="bb0c1-142">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="bb0c1-143">URL の追加</span><span class="sxs-lookup"><span data-stu-id="bb0c1-143">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="bb0c1-144">オプション</span><span class="sxs-lookup"><span data-stu-id="bb0c1-144">Options</span></span>

| <span data-ttu-id="bb0c1-145">短いオプション</span><span class="sxs-lookup"><span data-stu-id="bb0c1-145">Short option</span></span>| <span data-ttu-id="bb0c1-146">長いオプション</span><span class="sxs-lookup"><span data-stu-id="bb0c1-146">Long option</span></span>| <span data-ttu-id="bb0c1-147">説明</span><span class="sxs-lookup"><span data-stu-id="bb0c1-147">Description</span></span> | <span data-ttu-id="bb0c1-148">例</span><span class="sxs-lookup"><span data-stu-id="bb0c1-148">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="bb0c1-149">-v</span><span class="sxs-lookup"><span data-stu-id="bb0c1-149">-v</span></span>|<span data-ttu-id="bb0c1-150">--verbose</span><span class="sxs-lookup"><span data-stu-id="bb0c1-150">--verbose</span></span> | <span data-ttu-id="bb0c1-151">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-151">Show verbose output.</span></span> |<span data-ttu-id="bb0c1-152">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="bb0c1-152">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="bb0c1-153">-p</span><span class="sxs-lookup"><span data-stu-id="bb0c1-153">-p</span></span>|<span data-ttu-id="bb0c1-154">--updateProject</span><span class="sxs-lookup"><span data-stu-id="bb0c1-154">--updateProject</span></span> | <span data-ttu-id="bb0c1-155">操作対象のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-155">The project to operate on.</span></span> |<span data-ttu-id="bb0c1-156">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="bb0c1-156">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="bb0c1-157">-o</span><span class="sxs-lookup"><span data-stu-id="bb0c1-157">-o</span></span>|<span data-ttu-id="bb0c1-158">--output-file</span><span class="sxs-lookup"><span data-stu-id="bb0c1-158">--output-file</span></span> | <span data-ttu-id="bb0c1-159">OpenAPI ファイルのローカル コピーを配置する場所。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-159">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="bb0c1-160">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="bb0c1-160">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="bb0c1-161">-c</span><span class="sxs-lookup"><span data-stu-id="bb0c1-161">-c</span></span>|<span data-ttu-id="bb0c1-162">--code-generator</span><span class="sxs-lookup"><span data-stu-id="bb0c1-162">--code-generator</span></span>| <span data-ttu-id="bb0c1-163">参照に適用するコード ジェネレーター。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-163">The code generator to apply to the reference.</span></span> <span data-ttu-id="bb0c1-164">オプションは `NSwagCSharp` と `NSwagTypeScript` です。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-164">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="bb0c1-165">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="bb0c1-165">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="bb0c1-166">-h</span><span class="sxs-lookup"><span data-stu-id="bb0c1-166">-h</span></span>|<span data-ttu-id="bb0c1-167">--help</span><span class="sxs-lookup"><span data-stu-id="bb0c1-167">--help</span></span>|<span data-ttu-id="bb0c1-168">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-168">Show help information</span></span>|<span data-ttu-id="bb0c1-169">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="bb0c1-169">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="bb0c1-170">引数</span><span class="sxs-lookup"><span data-stu-id="bb0c1-170">Arguments</span></span>

|  <span data-ttu-id="bb0c1-171">引数</span><span class="sxs-lookup"><span data-stu-id="bb0c1-171">Argument</span></span>  | <span data-ttu-id="bb0c1-172">説明</span><span class="sxs-lookup"><span data-stu-id="bb0c1-172">Description</span></span> | <span data-ttu-id="bb0c1-173">例</span><span class="sxs-lookup"><span data-stu-id="bb0c1-173">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="bb0c1-174">source-URL</span><span class="sxs-lookup"><span data-stu-id="bb0c1-174">source-URL</span></span> | <span data-ttu-id="bb0c1-175">参照の作成元のソース。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-175">The source to create a reference from.</span></span> <span data-ttu-id="bb0c1-176">URL である必要があります。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-176">Must be a URL.</span></span> |<span data-ttu-id="bb0c1-177">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="bb0c1-177">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="bb0c1-178">削除</span><span class="sxs-lookup"><span data-stu-id="bb0c1-178">Remove</span></span>

<span data-ttu-id="bb0c1-179">指定したファイル名と一致する OpenAPI 参照を *.csproj* ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-179">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="bb0c1-180">OpenAPI 参照が削除されると、クライアントは生成されません。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-180">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="bb0c1-181">ローカルの *.json* および *.yaml* ファイルは削除されます。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-181">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="bb0c1-182">オプション</span><span class="sxs-lookup"><span data-stu-id="bb0c1-182">Options</span></span>

| <span data-ttu-id="bb0c1-183">短いオプション</span><span class="sxs-lookup"><span data-stu-id="bb0c1-183">Short option</span></span>| <span data-ttu-id="bb0c1-184">長いオプション</span><span class="sxs-lookup"><span data-stu-id="bb0c1-184">Long option</span></span>| <span data-ttu-id="bb0c1-185">説明</span><span class="sxs-lookup"><span data-stu-id="bb0c1-185">Description</span></span>| <span data-ttu-id="bb0c1-186">例</span><span class="sxs-lookup"><span data-stu-id="bb0c1-186">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="bb0c1-187">-v</span><span class="sxs-lookup"><span data-stu-id="bb0c1-187">-v</span></span>|<span data-ttu-id="bb0c1-188">--verbose</span><span class="sxs-lookup"><span data-stu-id="bb0c1-188">--verbose</span></span> | <span data-ttu-id="bb0c1-189">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-189">Show verbose output.</span></span> |<span data-ttu-id="bb0c1-190">dotnet openapi remove *-v*</span><span class="sxs-lookup"><span data-stu-id="bb0c1-190">dotnet openapi remove *-v*</span></span>|
| <span data-ttu-id="bb0c1-191">-p</span><span class="sxs-lookup"><span data-stu-id="bb0c1-191">-p</span></span>|<span data-ttu-id="bb0c1-192">--updateProject</span><span class="sxs-lookup"><span data-stu-id="bb0c1-192">--updateProject</span></span> | <span data-ttu-id="bb0c1-193">操作対象のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-193">The project to operate on.</span></span> |<span data-ttu-id="bb0c1-194">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="bb0c1-194">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="bb0c1-195">-h</span><span class="sxs-lookup"><span data-stu-id="bb0c1-195">-h</span></span>|<span data-ttu-id="bb0c1-196">--help</span><span class="sxs-lookup"><span data-stu-id="bb0c1-196">--help</span></span>|<span data-ttu-id="bb0c1-197">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-197">Show help information</span></span>|<span data-ttu-id="bb0c1-198">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="bb0c1-198">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="bb0c1-199">引数</span><span class="sxs-lookup"><span data-stu-id="bb0c1-199">Arguments</span></span>

|  <span data-ttu-id="bb0c1-200">引数</span><span class="sxs-lookup"><span data-stu-id="bb0c1-200">Argument</span></span>  | <span data-ttu-id="bb0c1-201">説明</span><span class="sxs-lookup"><span data-stu-id="bb0c1-201">Description</span></span>| <span data-ttu-id="bb0c1-202">例</span><span class="sxs-lookup"><span data-stu-id="bb0c1-202">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="bb0c1-203">source-file</span><span class="sxs-lookup"><span data-stu-id="bb0c1-203">source-file</span></span> | <span data-ttu-id="bb0c1-204">削除する参照のソース。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-204">The source to remove the reference to.</span></span> |<span data-ttu-id="bb0c1-205">dotnet openapi remove *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="bb0c1-205">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="bb0c1-206">最新の情報に更新</span><span class="sxs-lookup"><span data-stu-id="bb0c1-206">Refresh</span></span>

<span data-ttu-id="bb0c1-207">ダウンロード URL の最新のコンテンツを使用してダウンロードされたファイルのローカル バージョンを更新します。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-207">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="bb0c1-208">オプション</span><span class="sxs-lookup"><span data-stu-id="bb0c1-208">Options</span></span>

| <span data-ttu-id="bb0c1-209">短いオプション</span><span class="sxs-lookup"><span data-stu-id="bb0c1-209">Short option</span></span>| <span data-ttu-id="bb0c1-210">長いオプション</span><span class="sxs-lookup"><span data-stu-id="bb0c1-210">Long option</span></span>| <span data-ttu-id="bb0c1-211">説明</span><span class="sxs-lookup"><span data-stu-id="bb0c1-211">Description</span></span> | <span data-ttu-id="bb0c1-212">例</span><span class="sxs-lookup"><span data-stu-id="bb0c1-212">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="bb0c1-213">-v</span><span class="sxs-lookup"><span data-stu-id="bb0c1-213">-v</span></span>|<span data-ttu-id="bb0c1-214">--verbose</span><span class="sxs-lookup"><span data-stu-id="bb0c1-214">--verbose</span></span> | <span data-ttu-id="bb0c1-215">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-215">Show verbose output.</span></span> | <span data-ttu-id="bb0c1-216">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="bb0c1-216">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="bb0c1-217">-p</span><span class="sxs-lookup"><span data-stu-id="bb0c1-217">-p</span></span>|<span data-ttu-id="bb0c1-218">--updateProject</span><span class="sxs-lookup"><span data-stu-id="bb0c1-218">--updateProject</span></span> | <span data-ttu-id="bb0c1-219">操作対象のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-219">The project to operate on.</span></span> | <span data-ttu-id="bb0c1-220">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="bb0c1-220">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="bb0c1-221">-h</span><span class="sxs-lookup"><span data-stu-id="bb0c1-221">-h</span></span>|<span data-ttu-id="bb0c1-222">--help</span><span class="sxs-lookup"><span data-stu-id="bb0c1-222">--help</span></span>|<span data-ttu-id="bb0c1-223">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-223">Show help information</span></span>|<span data-ttu-id="bb0c1-224">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="bb0c1-224">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="bb0c1-225">引数</span><span class="sxs-lookup"><span data-stu-id="bb0c1-225">Arguments</span></span>

|  <span data-ttu-id="bb0c1-226">引数</span><span class="sxs-lookup"><span data-stu-id="bb0c1-226">Argument</span></span>  | <span data-ttu-id="bb0c1-227">説明</span><span class="sxs-lookup"><span data-stu-id="bb0c1-227">Description</span></span> | <span data-ttu-id="bb0c1-228">例</span><span class="sxs-lookup"><span data-stu-id="bb0c1-228">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="bb0c1-229">source-URL</span><span class="sxs-lookup"><span data-stu-id="bb0c1-229">source-URL</span></span> | <span data-ttu-id="bb0c1-230">最新状態にする参照の URL。</span><span class="sxs-lookup"><span data-stu-id="bb0c1-230">The URL to refresh the reference from.</span></span> | <span data-ttu-id="bb0c1-231">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="bb0c1-231">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
