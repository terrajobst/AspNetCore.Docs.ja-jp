---
title: dotnet-grpc を使用して Protobuf 参照を管理する
author: juntaoluo
description: dotnet-grpc グローバル ツールを使用して Protobuf 参照の追加、更新、削除、および一覧表示を行うことについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78650900"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="e217b-103">dotnet-grpc を使用して Protobuf 参照を管理する</span><span class="sxs-lookup"><span data-stu-id="e217b-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="e217b-104">作成者: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="e217b-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="e217b-105">`dotnet-grpc` は、.NET gRPC プロジェクト内の [Protobuf ( *.proto*)](xref:grpc/basics#proto-file) 参照を管理するための .NET Core グローバル ツールです。</span><span class="sxs-lookup"><span data-stu-id="e217b-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="e217b-106">このツールを使用して、Protobuf 参照の追加、更新、削除、および一覧表示を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e217b-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="e217b-107">インストール</span><span class="sxs-lookup"><span data-stu-id="e217b-107">Installation</span></span>

<span data-ttu-id="e217b-108">`dotnet-grpc` [.NET Core グローバル ツール](/dotnet/core/tools/global-tools)をインストールするには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e217b-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="e217b-109">参照の追加</span><span class="sxs-lookup"><span data-stu-id="e217b-109">Add references</span></span>

<span data-ttu-id="e217b-110">`dotnet-grpc` を使用し、`<Protobuf />` 項目として Protobuf 参照を *.csproj* ファイルに追加できます。</span><span class="sxs-lookup"><span data-stu-id="e217b-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="e217b-111">Protobuf 参照は、C# クライアントやサーバーの資産を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="e217b-112">`dotnet-grpc` ツールは以下のことが可能です。</span><span class="sxs-lookup"><span data-stu-id="e217b-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="e217b-113">ディスク上のローカル ファイルから Protobuf 参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="e217b-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="e217b-114">URL で指定されたリモート ファイルから Protobuf 参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="e217b-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="e217b-115">プロジェクトに正しい gRPC パッケージの依存関係が追加されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e217b-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="e217b-116">たとえば、`Grpc.AspNetCore` パッケージは Web アプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="e217b-117">`Grpc.AspNetCore` には、gRPC のサーバーおよびクライアントのライブラリと、ツールのサポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e217b-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="e217b-118">または、gRPC クライアント ライブラリとツールのサポートのみが含まれる `Grpc.Net.Client`、`Grpc.Tools`、および `Google.Protobuf` パッケージは、コンソール アプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="e217b-119">ファイルの追加</span><span class="sxs-lookup"><span data-stu-id="e217b-119">Add file</span></span>

<span data-ttu-id="e217b-120">`add-file` コマンドは、Protobuf 参照としてディスクにローカル ファイルを追加するために使用します。</span><span class="sxs-lookup"><span data-stu-id="e217b-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="e217b-121">指定するファイル パス:</span><span class="sxs-lookup"><span data-stu-id="e217b-121">The file paths provided:</span></span>

* <span data-ttu-id="e217b-122">現在のディレクトリに対する相対パスでも絶対パスでもかまいません。</span><span class="sxs-lookup"><span data-stu-id="e217b-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="e217b-123">パターン ベースのファイル [glob](https://wikipedia.org/wiki/Glob_(programming)) のためにワイルド カードを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e217b-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="e217b-124">いずれかのファイルがプロジェクト ディレクトリの外部にある場合、Visual Studio で `Link` フォルダーの下にファイルを表示するために `Protos` 要素が追加されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="e217b-125">使用方法</span><span class="sxs-lookup"><span data-stu-id="e217b-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="e217b-126">引数</span><span class="sxs-lookup"><span data-stu-id="e217b-126">Arguments</span></span>

| <span data-ttu-id="e217b-127">引数</span><span class="sxs-lookup"><span data-stu-id="e217b-127">Argument</span></span> | <span data-ttu-id="e217b-128">説明</span><span class="sxs-lookup"><span data-stu-id="e217b-128">Description</span></span> |
|-|-|
| <span data-ttu-id="e217b-129">ファイル</span><span class="sxs-lookup"><span data-stu-id="e217b-129">files</span></span> | <span data-ttu-id="e217b-130">protobuf ファイルの参照。</span><span class="sxs-lookup"><span data-stu-id="e217b-130">The protobuf file references.</span></span> <span data-ttu-id="e217b-131">ローカル protobuf ファイルの場合は、glob へのパスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="e217b-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="e217b-132">オプション</span><span class="sxs-lookup"><span data-stu-id="e217b-132">Options</span></span>

| <span data-ttu-id="e217b-133">短いオプション</span><span class="sxs-lookup"><span data-stu-id="e217b-133">Short option</span></span> | <span data-ttu-id="e217b-134">長いオプション</span><span class="sxs-lookup"><span data-stu-id="e217b-134">Long option</span></span> | <span data-ttu-id="e217b-135">説明</span><span class="sxs-lookup"><span data-stu-id="e217b-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="e217b-136">-p</span><span class="sxs-lookup"><span data-stu-id="e217b-136">-p</span></span> | <span data-ttu-id="e217b-137">--project</span><span class="sxs-lookup"><span data-stu-id="e217b-137">--project</span></span> | <span data-ttu-id="e217b-138">操作するプロジェクト ファイルへのパス。</span><span class="sxs-lookup"><span data-stu-id="e217b-138">The path to the project file to operate on.</span></span> <span data-ttu-id="e217b-139">ファイルが指定されていない場合、コマンドを実行すると現在のディレクトリが検索されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="e217b-140">-S</span><span class="sxs-lookup"><span data-stu-id="e217b-140">-s</span></span> | <span data-ttu-id="e217b-141">--services</span><span class="sxs-lookup"><span data-stu-id="e217b-141">--services</span></span> | <span data-ttu-id="e217b-142">生成する必要がある gRPC サービスの種類。</span><span class="sxs-lookup"><span data-stu-id="e217b-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="e217b-143">`Default` が指定された場合、Web プロジェクトには `Both` が使用され、Web プロジェクト以外には `Client` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="e217b-144">指定できる値は、`Both`、`Client`、`Default`、`None`、`Server` です。</span><span class="sxs-lookup"><span data-stu-id="e217b-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="e217b-145">-i</span><span class="sxs-lookup"><span data-stu-id="e217b-145">-i</span></span> | <span data-ttu-id="e217b-146">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="e217b-146">--additional-import-dirs</span></span> | <span data-ttu-id="e217b-147">protobuf ファイルのインポートを解決するときに使用する追加のディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="e217b-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="e217b-148">これはセミコロンで区切られたパスの一覧です。</span><span class="sxs-lookup"><span data-stu-id="e217b-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="e217b-149">--access</span><span class="sxs-lookup"><span data-stu-id="e217b-149">--access</span></span> | <span data-ttu-id="e217b-150">生成される C# クラスに使用するアクセス修飾子。</span><span class="sxs-lookup"><span data-stu-id="e217b-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="e217b-151">既定値は `Public` です。</span><span class="sxs-lookup"><span data-stu-id="e217b-151">The default value is `Public`.</span></span> <span data-ttu-id="e217b-152">指定できる値は、`Internal` と `Public` です。</span><span class="sxs-lookup"><span data-stu-id="e217b-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="e217b-153">URL の追加</span><span class="sxs-lookup"><span data-stu-id="e217b-153">Add URL</span></span>

<span data-ttu-id="e217b-154">`add-url` コマンドは、ソース URL で指定されたリモート ファイルを Protobuf 参照として追加するために使用します。</span><span class="sxs-lookup"><span data-stu-id="e217b-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="e217b-155">ファイル パスを指定して、リモート ファイルをダウンロードする場所を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e217b-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="e217b-156">ファイル パスは、現在のディレクトリに対する相対パスでも絶対パスでもかまいません。</span><span class="sxs-lookup"><span data-stu-id="e217b-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="e217b-157">ファイル パスがプロジェクト ディレクトリの外部にある場合、Visual Studio で `Link` 仮想フォルダーの下にファイルを表示するために `Protos` 要素が追加されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="e217b-158">使用方法</span><span class="sxs-lookup"><span data-stu-id="e217b-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="e217b-159">引数</span><span class="sxs-lookup"><span data-stu-id="e217b-159">Arguments</span></span>

| <span data-ttu-id="e217b-160">引数</span><span class="sxs-lookup"><span data-stu-id="e217b-160">Argument</span></span> | <span data-ttu-id="e217b-161">説明</span><span class="sxs-lookup"><span data-stu-id="e217b-161">Description</span></span> |
|-|-|
| <span data-ttu-id="e217b-162">url</span><span class="sxs-lookup"><span data-stu-id="e217b-162">url</span></span> | <span data-ttu-id="e217b-163">リモート protobuf ファイルへの URL。</span><span class="sxs-lookup"><span data-stu-id="e217b-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="e217b-164">オプション</span><span class="sxs-lookup"><span data-stu-id="e217b-164">Options</span></span>

| <span data-ttu-id="e217b-165">短いオプション</span><span class="sxs-lookup"><span data-stu-id="e217b-165">Short option</span></span> | <span data-ttu-id="e217b-166">長いオプション</span><span class="sxs-lookup"><span data-stu-id="e217b-166">Long option</span></span> | <span data-ttu-id="e217b-167">説明</span><span class="sxs-lookup"><span data-stu-id="e217b-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="e217b-168">-o</span><span class="sxs-lookup"><span data-stu-id="e217b-168">-o</span></span> | <span data-ttu-id="e217b-169">--output</span><span class="sxs-lookup"><span data-stu-id="e217b-169">--output</span></span> | <span data-ttu-id="e217b-170">リモート protobuf ファイルのダウンロード パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="e217b-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="e217b-171">これは必須オプションです。</span><span class="sxs-lookup"><span data-stu-id="e217b-171">This is a required option.</span></span>
| <span data-ttu-id="e217b-172">-p</span><span class="sxs-lookup"><span data-stu-id="e217b-172">-p</span></span> | <span data-ttu-id="e217b-173">--project</span><span class="sxs-lookup"><span data-stu-id="e217b-173">--project</span></span> | <span data-ttu-id="e217b-174">操作するプロジェクト ファイルへのパス。</span><span class="sxs-lookup"><span data-stu-id="e217b-174">The path to the project file to operate on.</span></span> <span data-ttu-id="e217b-175">ファイルが指定されていない場合、コマンドを実行すると現在のディレクトリが検索されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="e217b-176">-S</span><span class="sxs-lookup"><span data-stu-id="e217b-176">-s</span></span> | <span data-ttu-id="e217b-177">--services</span><span class="sxs-lookup"><span data-stu-id="e217b-177">--services</span></span> | <span data-ttu-id="e217b-178">生成する必要がある gRPC サービスの種類。</span><span class="sxs-lookup"><span data-stu-id="e217b-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="e217b-179">`Default` が指定された場合、Web プロジェクトには `Both` が使用され、Web プロジェクト以外には `Client` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="e217b-180">指定できる値は、`Both`、`Client`、`Default`、`None`、`Server` です。</span><span class="sxs-lookup"><span data-stu-id="e217b-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="e217b-181">-i</span><span class="sxs-lookup"><span data-stu-id="e217b-181">-i</span></span> | <span data-ttu-id="e217b-182">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="e217b-182">--additional-import-dirs</span></span> | <span data-ttu-id="e217b-183">protobuf ファイルのインポートを解決するときに使用する追加のディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="e217b-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="e217b-184">これはセミコロンで区切られたパスの一覧です。</span><span class="sxs-lookup"><span data-stu-id="e217b-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="e217b-185">--access</span><span class="sxs-lookup"><span data-stu-id="e217b-185">--access</span></span> | <span data-ttu-id="e217b-186">生成される C# クラスに使用するアクセス修飾子。</span><span class="sxs-lookup"><span data-stu-id="e217b-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="e217b-187">既定値は `Public`にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e217b-187">Default value is `Public`.</span></span> <span data-ttu-id="e217b-188">指定できる値は、`Internal` と `Public` です。</span><span class="sxs-lookup"><span data-stu-id="e217b-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="e217b-189">削除</span><span class="sxs-lookup"><span data-stu-id="e217b-189">Remove</span></span>

<span data-ttu-id="e217b-190">`remove` コマンドは、 *.csproj* ファイルから Protobuf 参照を削除するために使用します。</span><span class="sxs-lookup"><span data-stu-id="e217b-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="e217b-191">このコマンドでは、引数としてパス引数とソース URL を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="e217b-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="e217b-192">ツール:</span><span class="sxs-lookup"><span data-stu-id="e217b-192">The tool:</span></span>

* <span data-ttu-id="e217b-193">Protobuf 参照のみが削除されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="e217b-194">元はリモート URL からダウンロードされた場合でも *.proto* ファイルは削除されません。</span><span class="sxs-lookup"><span data-stu-id="e217b-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="e217b-195">使用方法</span><span class="sxs-lookup"><span data-stu-id="e217b-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="e217b-196">引数</span><span class="sxs-lookup"><span data-stu-id="e217b-196">Arguments</span></span>

| <span data-ttu-id="e217b-197">引数</span><span class="sxs-lookup"><span data-stu-id="e217b-197">Argument</span></span> | <span data-ttu-id="e217b-198">説明</span><span class="sxs-lookup"><span data-stu-id="e217b-198">Description</span></span> |
|-|-|
| <span data-ttu-id="e217b-199">references</span><span class="sxs-lookup"><span data-stu-id="e217b-199">references</span></span> | <span data-ttu-id="e217b-200">削除する protobuf 参照の URL またはファイル パス。</span><span class="sxs-lookup"><span data-stu-id="e217b-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="e217b-201">オプション</span><span class="sxs-lookup"><span data-stu-id="e217b-201">Options</span></span>

| <span data-ttu-id="e217b-202">短いオプション</span><span class="sxs-lookup"><span data-stu-id="e217b-202">Short option</span></span> | <span data-ttu-id="e217b-203">長いオプション</span><span class="sxs-lookup"><span data-stu-id="e217b-203">Long option</span></span> | <span data-ttu-id="e217b-204">説明</span><span class="sxs-lookup"><span data-stu-id="e217b-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="e217b-205">-p</span><span class="sxs-lookup"><span data-stu-id="e217b-205">-p</span></span> | <span data-ttu-id="e217b-206">--project</span><span class="sxs-lookup"><span data-stu-id="e217b-206">--project</span></span> | <span data-ttu-id="e217b-207">操作するプロジェクト ファイルへのパス。</span><span class="sxs-lookup"><span data-stu-id="e217b-207">The path to the project file to operate on.</span></span> <span data-ttu-id="e217b-208">ファイルが指定されていない場合、コマンドを実行すると現在のディレクトリが検索されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="e217b-209">最新の情報に更新</span><span class="sxs-lookup"><span data-stu-id="e217b-209">Refresh</span></span>

<span data-ttu-id="e217b-210">`refresh` コマンドは、ソース URL の最新のコンテンツを使用してリモート参照を更新するために使用します。</span><span class="sxs-lookup"><span data-stu-id="e217b-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="e217b-211">ダウンロード ファイル パスとソース URL の両方を使用して、更新する参照を指定できます。</span><span class="sxs-lookup"><span data-stu-id="e217b-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="e217b-212">注:</span><span class="sxs-lookup"><span data-stu-id="e217b-212">Note:</span></span>

* <span data-ttu-id="e217b-213">ローカル ファイルを更新する必要があるかどうかを特定するために、ファイル コンテンツのハッシュが比較されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="e217b-214">タイムスタンプ情報は比較されません。</span><span class="sxs-lookup"><span data-stu-id="e217b-214">No timestamp information is compared.</span></span>

<span data-ttu-id="e217b-215">更新が必要な場合、ツールの実行により、常にローカル ファイルがリモート ファイルに置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="e217b-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="e217b-216">使用方法</span><span class="sxs-lookup"><span data-stu-id="e217b-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="e217b-217">引数</span><span class="sxs-lookup"><span data-stu-id="e217b-217">Arguments</span></span>

| <span data-ttu-id="e217b-218">引数</span><span class="sxs-lookup"><span data-stu-id="e217b-218">Argument</span></span> | <span data-ttu-id="e217b-219">説明</span><span class="sxs-lookup"><span data-stu-id="e217b-219">Description</span></span> |
|-|-|
| <span data-ttu-id="e217b-220">references</span><span class="sxs-lookup"><span data-stu-id="e217b-220">references</span></span> | <span data-ttu-id="e217b-221">更新する必要があるリモート protobuf 参照への URL またはファイル パス。</span><span class="sxs-lookup"><span data-stu-id="e217b-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="e217b-222">この引数を空のままにすると、すべてのリモート参照が更新されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="e217b-223">オプション</span><span class="sxs-lookup"><span data-stu-id="e217b-223">Options</span></span>

| <span data-ttu-id="e217b-224">短いオプション</span><span class="sxs-lookup"><span data-stu-id="e217b-224">Short option</span></span> | <span data-ttu-id="e217b-225">長いオプション</span><span class="sxs-lookup"><span data-stu-id="e217b-225">Long option</span></span> | <span data-ttu-id="e217b-226">説明</span><span class="sxs-lookup"><span data-stu-id="e217b-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="e217b-227">-p</span><span class="sxs-lookup"><span data-stu-id="e217b-227">-p</span></span> | <span data-ttu-id="e217b-228">--project</span><span class="sxs-lookup"><span data-stu-id="e217b-228">--project</span></span> | <span data-ttu-id="e217b-229">操作するプロジェクト ファイルへのパス。</span><span class="sxs-lookup"><span data-stu-id="e217b-229">The path to the project file to operate on.</span></span> <span data-ttu-id="e217b-230">ファイルが指定されていない場合、コマンドを実行すると現在のディレクトリが検索されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="e217b-231">--dry-run</span><span class="sxs-lookup"><span data-stu-id="e217b-231">--dry-run</span></span> | <span data-ttu-id="e217b-232">どの新しいコンテンツもダウンロードせずに更新されるファイルの一覧を出力します。</span><span class="sxs-lookup"><span data-stu-id="e217b-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="e217b-233">リスト</span><span class="sxs-lookup"><span data-stu-id="e217b-233">List</span></span>

<span data-ttu-id="e217b-234">`list` コマンドは、プロジェクト ファイル内のすべての Protobuf 参照を表示するために使用します。</span><span class="sxs-lookup"><span data-stu-id="e217b-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="e217b-235">列のすべての値が既定値であると、列が省略されることがあります。</span><span class="sxs-lookup"><span data-stu-id="e217b-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="e217b-236">使用方法</span><span class="sxs-lookup"><span data-stu-id="e217b-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="e217b-237">オプション</span><span class="sxs-lookup"><span data-stu-id="e217b-237">Options</span></span>

| <span data-ttu-id="e217b-238">短いオプション</span><span class="sxs-lookup"><span data-stu-id="e217b-238">Short option</span></span> | <span data-ttu-id="e217b-239">長いオプション</span><span class="sxs-lookup"><span data-stu-id="e217b-239">Long option</span></span> | <span data-ttu-id="e217b-240">説明</span><span class="sxs-lookup"><span data-stu-id="e217b-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="e217b-241">-p</span><span class="sxs-lookup"><span data-stu-id="e217b-241">-p</span></span> | <span data-ttu-id="e217b-242">--project</span><span class="sxs-lookup"><span data-stu-id="e217b-242">--project</span></span> | <span data-ttu-id="e217b-243">操作するプロジェクト ファイルへのパス。</span><span class="sxs-lookup"><span data-stu-id="e217b-243">The path to the project file to operate on.</span></span> <span data-ttu-id="e217b-244">ファイルが指定されていない場合、コマンドを実行すると現在のディレクトリが検索されます。</span><span class="sxs-lookup"><span data-stu-id="e217b-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e217b-245">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e217b-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
