---
title: OpenAPI を使用した ASP.NET Core アプリの開発
author: ryanbrandenburg
description: Microsoft.dotnet-openapi ツールを使用して OpenAPI ファイルへの参照を追加する方法を説明します。
ms.author: rybrande
ms.date: 08/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: a9b38bb7e69744d72867bf69cecf1fa92d7c15b3
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187366"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="152fb-103">OpenAPI ツールを使用した ASP.NET Core アプリの開発</span><span class="sxs-lookup"><span data-stu-id="152fb-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="152fb-104">作成者: Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="152fb-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="152fb-105">`Microsoft.dotnet-openapi` は、プロジェクト内での [OpenAPI](https://github.com/OAI/OpenAPI-Specification) 参照を管理するための .NET Core グローバル ツールです。</span><span class="sxs-lookup"><span data-stu-id="152fb-105">`Microsoft.dotnet-openapi` is a .NET Core global tool for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="152fb-106">インストール</span><span class="sxs-lookup"><span data-stu-id="152fb-106">Installation</span></span>

<span data-ttu-id="152fb-107">`Microsoft.dotnet-openapi` をインストールするには次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="152fb-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-openapi
```

<span data-ttu-id="152fb-108">`Microsoft.dotnet-openapi` は [.NET Core グローバル ツール](/dotnet/core/tools/global-tools)です。</span><span class="sxs-lookup"><span data-stu-id="152fb-108">`Microsoft.dotnet-openapi` is a [.NET Core Global Tool](/dotnet/core/tools/global-tools).</span></span>

## <a name="add"></a><span data-ttu-id="152fb-109">追加</span><span class="sxs-lookup"><span data-stu-id="152fb-109">Add</span></span>

<span data-ttu-id="152fb-110">このページのコマンドのいずれかを使用して OpenAPI 参照を追加すると、次のような `<OpenApiReference />` 要素が *.csproj* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="152fb-110">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />`  element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="152fb-111">前の参照は、生成されるクライアント コードをアプリが呼び出すために必要です。</span><span class="sxs-lookup"><span data-stu-id="152fb-111">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="152fb-112">ファイルの追加</span><span class="sxs-lookup"><span data-stu-id="152fb-112">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="152fb-113">オプション</span><span class="sxs-lookup"><span data-stu-id="152fb-113">Options</span></span>

| <span data-ttu-id="152fb-114">短いオプション</span><span class="sxs-lookup"><span data-stu-id="152fb-114">Short option</span></span>| <span data-ttu-id="152fb-115">長いオプション</span><span class="sxs-lookup"><span data-stu-id="152fb-115">Long option</span></span>| <span data-ttu-id="152fb-116">説明</span><span class="sxs-lookup"><span data-stu-id="152fb-116">Description</span></span> | <span data-ttu-id="152fb-117">例</span><span class="sxs-lookup"><span data-stu-id="152fb-117">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="152fb-118">-v</span><span class="sxs-lookup"><span data-stu-id="152fb-118">-v</span></span>|<span data-ttu-id="152fb-119">--verbose</span><span class="sxs-lookup"><span data-stu-id="152fb-119">--verbose</span></span> | <span data-ttu-id="152fb-120">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="152fb-120">Show verbose output.</span></span> |<span data-ttu-id="152fb-121">dotnet openapi add file *-v* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="152fb-121">dotnet openapi add file *-v* .\OpenAPI.json</span></span> |
| <span data-ttu-id="152fb-122">-p</span><span class="sxs-lookup"><span data-stu-id="152fb-122">-p</span></span>|<span data-ttu-id="152fb-123">--updateProject</span><span class="sxs-lookup"><span data-stu-id="152fb-123">--updateProject</span></span> | <span data-ttu-id="152fb-124">操作対象のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="152fb-124">The project to operate on.</span></span> |<span data-ttu-id="152fb-125">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="152fb-125">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="152fb-126">-c</span><span class="sxs-lookup"><span data-stu-id="152fb-126">-c</span></span>|<span data-ttu-id="152fb-127">--code-generator</span><span class="sxs-lookup"><span data-stu-id="152fb-127">--code-generator</span></span>| <span data-ttu-id="152fb-128">参照に適用するコード ジェネレーター。</span><span class="sxs-lookup"><span data-stu-id="152fb-128">The code generator to apply to the reference.</span></span> <span data-ttu-id="152fb-129">オプションは `NSwagCSharp` と `NSwagTypeScript` です。</span><span class="sxs-lookup"><span data-stu-id="152fb-129">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="152fb-130">`--code-generator` を指定しない場合、ツールは既定で `NSwagCSharp` になります。</span><span class="sxs-lookup"><span data-stu-id="152fb-130">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="152fb-131">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="152fb-131">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="152fb-132">-h</span><span class="sxs-lookup"><span data-stu-id="152fb-132">-h</span></span>|<span data-ttu-id="152fb-133">--help</span><span class="sxs-lookup"><span data-stu-id="152fb-133">--help</span></span>|<span data-ttu-id="152fb-134">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="152fb-134">Show help information</span></span>|<span data-ttu-id="152fb-135">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="152fb-135">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="152fb-136">引数</span><span class="sxs-lookup"><span data-stu-id="152fb-136">Arguments</span></span>

|  <span data-ttu-id="152fb-137">引数</span><span class="sxs-lookup"><span data-stu-id="152fb-137">Argument</span></span>  | <span data-ttu-id="152fb-138">説明</span><span class="sxs-lookup"><span data-stu-id="152fb-138">Description</span></span> | <span data-ttu-id="152fb-139">例</span><span class="sxs-lookup"><span data-stu-id="152fb-139">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="152fb-140">source-file</span><span class="sxs-lookup"><span data-stu-id="152fb-140">source-file</span></span> | <span data-ttu-id="152fb-141">参照の作成元のソース。</span><span class="sxs-lookup"><span data-stu-id="152fb-141">The source to create a reference from.</span></span> <span data-ttu-id="152fb-142">OpenAPI ファイルである必要があります。</span><span class="sxs-lookup"><span data-stu-id="152fb-142">Must be an OpenAPI file.</span></span> |<span data-ttu-id="152fb-143">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="152fb-143">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="152fb-144">URL の追加</span><span class="sxs-lookup"><span data-stu-id="152fb-144">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="152fb-145">オプション</span><span class="sxs-lookup"><span data-stu-id="152fb-145">Options</span></span>

| <span data-ttu-id="152fb-146">短いオプション</span><span class="sxs-lookup"><span data-stu-id="152fb-146">Short option</span></span>| <span data-ttu-id="152fb-147">長いオプション</span><span class="sxs-lookup"><span data-stu-id="152fb-147">Long option</span></span>| <span data-ttu-id="152fb-148">説明</span><span class="sxs-lookup"><span data-stu-id="152fb-148">Description</span></span> | <span data-ttu-id="152fb-149">例</span><span class="sxs-lookup"><span data-stu-id="152fb-149">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="152fb-150">-v</span><span class="sxs-lookup"><span data-stu-id="152fb-150">-v</span></span>|<span data-ttu-id="152fb-151">--verbose</span><span class="sxs-lookup"><span data-stu-id="152fb-151">--verbose</span></span> | <span data-ttu-id="152fb-152">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="152fb-152">Show verbose output.</span></span> |<span data-ttu-id="152fb-153">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="152fb-153">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="152fb-154">-p</span><span class="sxs-lookup"><span data-stu-id="152fb-154">-p</span></span>|<span data-ttu-id="152fb-155">--updateProject</span><span class="sxs-lookup"><span data-stu-id="152fb-155">--updateProject</span></span> | <span data-ttu-id="152fb-156">操作対象のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="152fb-156">The project to operate on.</span></span> |<span data-ttu-id="152fb-157">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="152fb-157">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="152fb-158">-o</span><span class="sxs-lookup"><span data-stu-id="152fb-158">-o</span></span>|<span data-ttu-id="152fb-159">--output-file</span><span class="sxs-lookup"><span data-stu-id="152fb-159">--output-file</span></span> | <span data-ttu-id="152fb-160">OpenAPI ファイルのローカル コピーを配置する場所。</span><span class="sxs-lookup"><span data-stu-id="152fb-160">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="152fb-161">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="152fb-161">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="152fb-162">-c</span><span class="sxs-lookup"><span data-stu-id="152fb-162">-c</span></span>|<span data-ttu-id="152fb-163">--code-generator</span><span class="sxs-lookup"><span data-stu-id="152fb-163">--code-generator</span></span>| <span data-ttu-id="152fb-164">参照に適用するコード ジェネレーター。</span><span class="sxs-lookup"><span data-stu-id="152fb-164">The code generator to apply to the reference.</span></span> <span data-ttu-id="152fb-165">オプションは `NSwagCSharp` と `NSwagTypeScript` です。</span><span class="sxs-lookup"><span data-stu-id="152fb-165">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="152fb-166">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="152fb-166">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="152fb-167">-h</span><span class="sxs-lookup"><span data-stu-id="152fb-167">-h</span></span>|<span data-ttu-id="152fb-168">--help</span><span class="sxs-lookup"><span data-stu-id="152fb-168">--help</span></span>|<span data-ttu-id="152fb-169">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="152fb-169">Show help information</span></span>|<span data-ttu-id="152fb-170">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="152fb-170">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="152fb-171">引数</span><span class="sxs-lookup"><span data-stu-id="152fb-171">Arguments</span></span>

|  <span data-ttu-id="152fb-172">引数</span><span class="sxs-lookup"><span data-stu-id="152fb-172">Argument</span></span>  | <span data-ttu-id="152fb-173">説明</span><span class="sxs-lookup"><span data-stu-id="152fb-173">Description</span></span> | <span data-ttu-id="152fb-174">例</span><span class="sxs-lookup"><span data-stu-id="152fb-174">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="152fb-175">source-URL</span><span class="sxs-lookup"><span data-stu-id="152fb-175">source-URL</span></span> | <span data-ttu-id="152fb-176">参照の作成元のソース。</span><span class="sxs-lookup"><span data-stu-id="152fb-176">The source to create a reference from.</span></span> <span data-ttu-id="152fb-177">URL である必要があります。</span><span class="sxs-lookup"><span data-stu-id="152fb-177">Must be a URL.</span></span> |<span data-ttu-id="152fb-178">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="152fb-178">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="152fb-179">削除</span><span class="sxs-lookup"><span data-stu-id="152fb-179">Remove</span></span>

<span data-ttu-id="152fb-180">指定したファイル名と一致する OpenAPI 参照を *.csproj* ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="152fb-180">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="152fb-181">OpenAPI 参照が削除されると、クライアントは生成されません。</span><span class="sxs-lookup"><span data-stu-id="152fb-181">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="152fb-182">ローカルの *.json* および *.yaml* ファイルは削除されます。</span><span class="sxs-lookup"><span data-stu-id="152fb-182">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="152fb-183">オプション</span><span class="sxs-lookup"><span data-stu-id="152fb-183">Options</span></span>

| <span data-ttu-id="152fb-184">短いオプション</span><span class="sxs-lookup"><span data-stu-id="152fb-184">Short option</span></span>| <span data-ttu-id="152fb-185">長いオプション</span><span class="sxs-lookup"><span data-stu-id="152fb-185">Long option</span></span>| <span data-ttu-id="152fb-186">説明</span><span class="sxs-lookup"><span data-stu-id="152fb-186">Description</span></span>| <span data-ttu-id="152fb-187">例</span><span class="sxs-lookup"><span data-stu-id="152fb-187">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="152fb-188">-v</span><span class="sxs-lookup"><span data-stu-id="152fb-188">-v</span></span>|<span data-ttu-id="152fb-189">--verbose</span><span class="sxs-lookup"><span data-stu-id="152fb-189">--verbose</span></span> | <span data-ttu-id="152fb-190">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="152fb-190">Show verbose output.</span></span> |<span data-ttu-id="152fb-191">dotnet openapi remove *-v*</span><span class="sxs-lookup"><span data-stu-id="152fb-191">dotnet openapi remove *-v*</span></span>|
| <span data-ttu-id="152fb-192">-p</span><span class="sxs-lookup"><span data-stu-id="152fb-192">-p</span></span>|<span data-ttu-id="152fb-193">--updateProject</span><span class="sxs-lookup"><span data-stu-id="152fb-193">--updateProject</span></span> | <span data-ttu-id="152fb-194">操作対象のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="152fb-194">The project to operate on.</span></span> |<span data-ttu-id="152fb-195">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="152fb-195">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="152fb-196">-h</span><span class="sxs-lookup"><span data-stu-id="152fb-196">-h</span></span>|<span data-ttu-id="152fb-197">--help</span><span class="sxs-lookup"><span data-stu-id="152fb-197">--help</span></span>|<span data-ttu-id="152fb-198">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="152fb-198">Show help information</span></span>|<span data-ttu-id="152fb-199">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="152fb-199">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="152fb-200">引数</span><span class="sxs-lookup"><span data-stu-id="152fb-200">Arguments</span></span>

|  <span data-ttu-id="152fb-201">引数</span><span class="sxs-lookup"><span data-stu-id="152fb-201">Argument</span></span>  | <span data-ttu-id="152fb-202">説明</span><span class="sxs-lookup"><span data-stu-id="152fb-202">Description</span></span>| <span data-ttu-id="152fb-203">例</span><span class="sxs-lookup"><span data-stu-id="152fb-203">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="152fb-204">source-file</span><span class="sxs-lookup"><span data-stu-id="152fb-204">source-file</span></span> | <span data-ttu-id="152fb-205">削除する参照のソース。</span><span class="sxs-lookup"><span data-stu-id="152fb-205">The source to remove the reference to.</span></span> |<span data-ttu-id="152fb-206">dotnet openapi remove *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="152fb-206">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="152fb-207">最新の情報に更新</span><span class="sxs-lookup"><span data-stu-id="152fb-207">Refresh</span></span>

<span data-ttu-id="152fb-208">ダウンロード URL の最新のコンテンツを使用してダウンロードされたファイルのローカル バージョンを更新します。</span><span class="sxs-lookup"><span data-stu-id="152fb-208">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="152fb-209">オプション</span><span class="sxs-lookup"><span data-stu-id="152fb-209">Options</span></span>

| <span data-ttu-id="152fb-210">短いオプション</span><span class="sxs-lookup"><span data-stu-id="152fb-210">Short option</span></span>| <span data-ttu-id="152fb-211">長いオプション</span><span class="sxs-lookup"><span data-stu-id="152fb-211">Long option</span></span>| <span data-ttu-id="152fb-212">説明</span><span class="sxs-lookup"><span data-stu-id="152fb-212">Description</span></span> | <span data-ttu-id="152fb-213">例</span><span class="sxs-lookup"><span data-stu-id="152fb-213">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="152fb-214">-v</span><span class="sxs-lookup"><span data-stu-id="152fb-214">-v</span></span>|<span data-ttu-id="152fb-215">--verbose</span><span class="sxs-lookup"><span data-stu-id="152fb-215">--verbose</span></span> | <span data-ttu-id="152fb-216">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="152fb-216">Show verbose output.</span></span> | <span data-ttu-id="152fb-217">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="152fb-217">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="152fb-218">-p</span><span class="sxs-lookup"><span data-stu-id="152fb-218">-p</span></span>|<span data-ttu-id="152fb-219">--updateProject</span><span class="sxs-lookup"><span data-stu-id="152fb-219">--updateProject</span></span> | <span data-ttu-id="152fb-220">操作対象のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="152fb-220">The project to operate on.</span></span> | <span data-ttu-id="152fb-221">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="152fb-221">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="152fb-222">-h</span><span class="sxs-lookup"><span data-stu-id="152fb-222">-h</span></span>|<span data-ttu-id="152fb-223">--help</span><span class="sxs-lookup"><span data-stu-id="152fb-223">--help</span></span>|<span data-ttu-id="152fb-224">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="152fb-224">Show help information</span></span>|<span data-ttu-id="152fb-225">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="152fb-225">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="152fb-226">引数</span><span class="sxs-lookup"><span data-stu-id="152fb-226">Arguments</span></span>

|  <span data-ttu-id="152fb-227">引数</span><span class="sxs-lookup"><span data-stu-id="152fb-227">Argument</span></span>  | <span data-ttu-id="152fb-228">説明</span><span class="sxs-lookup"><span data-stu-id="152fb-228">Description</span></span> | <span data-ttu-id="152fb-229">例</span><span class="sxs-lookup"><span data-stu-id="152fb-229">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="152fb-230">source-URL</span><span class="sxs-lookup"><span data-stu-id="152fb-230">source-URL</span></span> | <span data-ttu-id="152fb-231">最新状態にする参照の URL。</span><span class="sxs-lookup"><span data-stu-id="152fb-231">The URL to refresh the reference from.</span></span> | <span data-ttu-id="152fb-232">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="152fb-232">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
