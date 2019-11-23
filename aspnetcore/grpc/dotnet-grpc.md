---
title: dotnet-grpc を使用して Protobuf 参照を管理する
author: juntaoluo
description: Dotnet-grpc グローバルツールを使用した Protobuf 参照の追加、更新、削除、および一覧表示について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519038"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="d6b73-103">dotnet-grpc を使用して Protobuf 参照を管理する</span><span class="sxs-lookup"><span data-stu-id="d6b73-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="d6b73-104">作成者: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="d6b73-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="d6b73-105">`dotnet-grpc` は、.NET gRPC プロジェクト内の[Protobuf (*proto*)](xref:grpc/basics#proto-file)参照を管理するための .net Core グローバルツールです。</span><span class="sxs-lookup"><span data-stu-id="d6b73-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="d6b73-106">このツールを使用すると、Protobuf 参照の追加、更新、削除、および一覧表示を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="d6b73-107">インストール</span><span class="sxs-lookup"><span data-stu-id="d6b73-107">Installation</span></span>

<span data-ttu-id="d6b73-108">`dotnet-grpc` [.Net Core グローバルツール](/dotnet/core/tools/global-tools)をインストールするには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d6b73-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="d6b73-109">参照の追加</span><span class="sxs-lookup"><span data-stu-id="d6b73-109">Add references</span></span>

<span data-ttu-id="d6b73-110">`dotnet-grpc` を使用すると、 *.csproj*ファイルに `<Protobuf />` 項目として Protobuf 参照を追加できます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="d6b73-111">Protobuf 参照は、 C#クライアントまたはサーバーの資産を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="d6b73-112">`dotnet-grpc` ツールでは次のことができます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="d6b73-113">ディスク上のローカルファイルから Protobuf 参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="d6b73-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="d6b73-114">URL で指定されたリモートファイルから Protobuf 参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="d6b73-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="d6b73-115">正しい gRPC パッケージの依存関係がプロジェクトに追加されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d6b73-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="d6b73-116">たとえば、`Grpc.AspNetCore` パッケージは web アプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="d6b73-117">`Grpc.AspNetCore` には、gRPC サーバーとクライアントライブラリ、およびツールのサポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d6b73-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="d6b73-118">また、gRPC クライアントライブラリとツールのサポートのみを含む、`Grpc.Net.Client`、`Grpc.Tools`、および `Google.Protobuf` パッケージがコンソールアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="d6b73-119">ファイルの追加</span><span class="sxs-lookup"><span data-stu-id="d6b73-119">Add file</span></span>

<span data-ttu-id="d6b73-120">`add-file` コマンドは、Protobuf 参照としてディスクにローカルファイルを追加するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="d6b73-121">指定されたファイルパス:</span><span class="sxs-lookup"><span data-stu-id="d6b73-121">The file paths provided:</span></span>

* <span data-ttu-id="d6b73-122">現在のディレクトリまたは絶対パスを基準とした相対パスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="d6b73-123">パターンベースのファイル[グロビング](https://wikipedia.org/wiki/Glob_(programming))のワイルドカードを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="d6b73-124">ファイルがプロジェクトディレクトリの外部にある場合は、Visual Studio の `Protos` フォルダーにファイルを表示するために `Link` 要素が追加されます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="d6b73-125">使用法</span><span class="sxs-lookup"><span data-stu-id="d6b73-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="d6b73-126">引数</span><span class="sxs-lookup"><span data-stu-id="d6b73-126">Arguments</span></span>

| <span data-ttu-id="d6b73-127">引数</span><span class="sxs-lookup"><span data-stu-id="d6b73-127">Argument</span></span> | <span data-ttu-id="d6b73-128">説明</span><span class="sxs-lookup"><span data-stu-id="d6b73-128">Description</span></span> |
|-|-|
| <span data-ttu-id="d6b73-129">files</span><span class="sxs-lookup"><span data-stu-id="d6b73-129">files</span></span> | <span data-ttu-id="d6b73-130">Protobuf ファイル参照。</span><span class="sxs-lookup"><span data-stu-id="d6b73-130">The protobuf file references.</span></span> <span data-ttu-id="d6b73-131">ローカル protobuf ファイルの場合は、glob のパスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="d6b73-132">オプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-132">Options</span></span>

| <span data-ttu-id="d6b73-133">短いオプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-133">Short option</span></span> | <span data-ttu-id="d6b73-134">長いオプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-134">Long option</span></span> | <span data-ttu-id="d6b73-135">説明</span><span class="sxs-lookup"><span data-stu-id="d6b73-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="d6b73-136">-p</span><span class="sxs-lookup"><span data-stu-id="d6b73-136">-p</span></span> | <span data-ttu-id="d6b73-137">--project</span><span class="sxs-lookup"><span data-stu-id="d6b73-137">--project</span></span> | <span data-ttu-id="d6b73-138">操作するプロジェクトファイルのパス。</span><span class="sxs-lookup"><span data-stu-id="d6b73-138">The path to the project file to operate on.</span></span> <span data-ttu-id="d6b73-139">ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。</span><span class="sxs-lookup"><span data-stu-id="d6b73-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="d6b73-140">-s</span><span class="sxs-lookup"><span data-stu-id="d6b73-140">-s</span></span> | <span data-ttu-id="d6b73-141">--サービス</span><span class="sxs-lookup"><span data-stu-id="d6b73-141">--services</span></span> | <span data-ttu-id="d6b73-142">生成する必要がある gRPC サービスの種類。</span><span class="sxs-lookup"><span data-stu-id="d6b73-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="d6b73-143">`Default` が指定されている場合、Web プロジェクトには `Both` が使用され、Web プロジェクト以外では `Client` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="d6b73-144">許容される値は、`Both`、`Client`、`Default`、`None`、`Server`です。</span><span class="sxs-lookup"><span data-stu-id="d6b73-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="d6b73-145">-i</span><span class="sxs-lookup"><span data-stu-id="d6b73-145">-i</span></span> | <span data-ttu-id="d6b73-146">--追加-インポート-ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="d6b73-146">--additional-import-dirs</span></span> | <span data-ttu-id="d6b73-147">Protobuf ファイルのインポートを解決するときに使用する追加のディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="d6b73-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="d6b73-148">これは、セミコロンで区切られたパスの一覧です。</span><span class="sxs-lookup"><span data-stu-id="d6b73-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="d6b73-149">--アクセス</span><span class="sxs-lookup"><span data-stu-id="d6b73-149">--access</span></span> | <span data-ttu-id="d6b73-150">生成されC#たクラスに使用するアクセス修飾子。</span><span class="sxs-lookup"><span data-stu-id="d6b73-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="d6b73-151">既定値は `Public` です。</span><span class="sxs-lookup"><span data-stu-id="d6b73-151">The default value is `Public`.</span></span> <span data-ttu-id="d6b73-152">許容される値は `Internal` と `Public`です。</span><span class="sxs-lookup"><span data-stu-id="d6b73-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="d6b73-153">URL の追加</span><span class="sxs-lookup"><span data-stu-id="d6b73-153">Add URL</span></span>

<span data-ttu-id="d6b73-154">`add-url` コマンドは、ソース URL で指定されたリモートファイルを Protobuf reference として追加するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="d6b73-155">リモートファイルをダウンロードする場所を指定するには、ファイルパスを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6b73-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="d6b73-156">ファイルパスは、現在のディレクトリまたは絶対パスを基準とした相対パスにすることができます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="d6b73-157">ファイルパスがプロジェクトディレクトリの外部にある場合は、Visual Studio の `Protos` 仮想フォルダーの下にファイルを表示するために `Link` 要素が追加されます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="d6b73-158">使用法</span><span class="sxs-lookup"><span data-stu-id="d6b73-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="d6b73-159">引数</span><span class="sxs-lookup"><span data-stu-id="d6b73-159">Arguments</span></span>

| <span data-ttu-id="d6b73-160">引数</span><span class="sxs-lookup"><span data-stu-id="d6b73-160">Argument</span></span> | <span data-ttu-id="d6b73-161">説明</span><span class="sxs-lookup"><span data-stu-id="d6b73-161">Description</span></span> |
|-|-|
| <span data-ttu-id="d6b73-162">url</span><span class="sxs-lookup"><span data-stu-id="d6b73-162">url</span></span> | <span data-ttu-id="d6b73-163">リモート protobuf ファイルの URL。</span><span class="sxs-lookup"><span data-stu-id="d6b73-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="d6b73-164">オプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-164">Options</span></span>

| <span data-ttu-id="d6b73-165">短いオプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-165">Short option</span></span> | <span data-ttu-id="d6b73-166">長いオプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-166">Long option</span></span> | <span data-ttu-id="d6b73-167">説明</span><span class="sxs-lookup"><span data-stu-id="d6b73-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="d6b73-168">-o</span><span class="sxs-lookup"><span data-stu-id="d6b73-168">-o</span></span> | <span data-ttu-id="d6b73-169">--出力</span><span class="sxs-lookup"><span data-stu-id="d6b73-169">--output</span></span> | <span data-ttu-id="d6b73-170">リモート protobuf ファイルのダウンロードパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="d6b73-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="d6b73-171">これは必須オプションです。</span><span class="sxs-lookup"><span data-stu-id="d6b73-171">This is a required option.</span></span>
| <span data-ttu-id="d6b73-172">-p</span><span class="sxs-lookup"><span data-stu-id="d6b73-172">-p</span></span> | <span data-ttu-id="d6b73-173">--project</span><span class="sxs-lookup"><span data-stu-id="d6b73-173">--project</span></span> | <span data-ttu-id="d6b73-174">操作するプロジェクトファイルのパス。</span><span class="sxs-lookup"><span data-stu-id="d6b73-174">The path to the project file to operate on.</span></span> <span data-ttu-id="d6b73-175">ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。</span><span class="sxs-lookup"><span data-stu-id="d6b73-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="d6b73-176">-s</span><span class="sxs-lookup"><span data-stu-id="d6b73-176">-s</span></span> | <span data-ttu-id="d6b73-177">--サービス</span><span class="sxs-lookup"><span data-stu-id="d6b73-177">--services</span></span> | <span data-ttu-id="d6b73-178">生成する必要がある gRPC サービスの種類。</span><span class="sxs-lookup"><span data-stu-id="d6b73-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="d6b73-179">`Default` が指定されている場合、Web プロジェクトには `Both` が使用され、Web プロジェクト以外では `Client` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="d6b73-180">許容される値は、`Both`、`Client`、`Default`、`None`、`Server`です。</span><span class="sxs-lookup"><span data-stu-id="d6b73-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="d6b73-181">-i</span><span class="sxs-lookup"><span data-stu-id="d6b73-181">-i</span></span> | <span data-ttu-id="d6b73-182">--追加-インポート-ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="d6b73-182">--additional-import-dirs</span></span> | <span data-ttu-id="d6b73-183">Protobuf ファイルのインポートを解決するときに使用する追加のディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="d6b73-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="d6b73-184">これは、セミコロンで区切られたパスの一覧です。</span><span class="sxs-lookup"><span data-stu-id="d6b73-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="d6b73-185">--アクセス</span><span class="sxs-lookup"><span data-stu-id="d6b73-185">--access</span></span> | <span data-ttu-id="d6b73-186">生成されC#たクラスに使用するアクセス修飾子。</span><span class="sxs-lookup"><span data-stu-id="d6b73-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="d6b73-187">既定値は `Public` です。</span><span class="sxs-lookup"><span data-stu-id="d6b73-187">Default value is `Public`.</span></span> <span data-ttu-id="d6b73-188">許容される値は `Internal` と `Public`です。</span><span class="sxs-lookup"><span data-stu-id="d6b73-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="d6b73-189">削除</span><span class="sxs-lookup"><span data-stu-id="d6b73-189">Remove</span></span>

<span data-ttu-id="d6b73-190">`remove` コマンドは、 *.csproj*ファイルから Protobuf 参照を削除するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="d6b73-191">コマンドは、引数としてパス引数とソース Url を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d6b73-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="d6b73-192">ツール:</span><span class="sxs-lookup"><span data-stu-id="d6b73-192">The tool:</span></span>

* <span data-ttu-id="d6b73-193">Protobuf 参照のみを削除します。</span><span class="sxs-lookup"><span data-stu-id="d6b73-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="d6b73-194">は、最初にリモート URL からダウンロードされた場合でも、*プロトコル*ファイルを削除しません。</span><span class="sxs-lookup"><span data-stu-id="d6b73-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="d6b73-195">使用法</span><span class="sxs-lookup"><span data-stu-id="d6b73-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="d6b73-196">引数</span><span class="sxs-lookup"><span data-stu-id="d6b73-196">Arguments</span></span>

| <span data-ttu-id="d6b73-197">引数</span><span class="sxs-lookup"><span data-stu-id="d6b73-197">Argument</span></span> | <span data-ttu-id="d6b73-198">説明</span><span class="sxs-lookup"><span data-stu-id="d6b73-198">Description</span></span> |
|-|-|
| <span data-ttu-id="d6b73-199">参照</span><span class="sxs-lookup"><span data-stu-id="d6b73-199">references</span></span> | <span data-ttu-id="d6b73-200">削除する protobuf 参照の Url またはファイルパス。</span><span class="sxs-lookup"><span data-stu-id="d6b73-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="d6b73-201">オプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-201">Options</span></span>

| <span data-ttu-id="d6b73-202">短いオプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-202">Short option</span></span> | <span data-ttu-id="d6b73-203">長いオプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-203">Long option</span></span> | <span data-ttu-id="d6b73-204">説明</span><span class="sxs-lookup"><span data-stu-id="d6b73-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="d6b73-205">-p</span><span class="sxs-lookup"><span data-stu-id="d6b73-205">-p</span></span> | <span data-ttu-id="d6b73-206">--project</span><span class="sxs-lookup"><span data-stu-id="d6b73-206">--project</span></span> | <span data-ttu-id="d6b73-207">操作するプロジェクトファイルのパス。</span><span class="sxs-lookup"><span data-stu-id="d6b73-207">The path to the project file to operate on.</span></span> <span data-ttu-id="d6b73-208">ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。</span><span class="sxs-lookup"><span data-stu-id="d6b73-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="d6b73-209">更新</span><span class="sxs-lookup"><span data-stu-id="d6b73-209">Refresh</span></span>

<span data-ttu-id="d6b73-210">`refresh` コマンドは、ソース URL の最新のコンテンツを使用してリモート参照を更新するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="d6b73-211">ダウンロードファイルパスとソース URL の両方を使用して、更新する参照を指定できます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="d6b73-212">注意:</span><span class="sxs-lookup"><span data-stu-id="d6b73-212">Note:</span></span>

* <span data-ttu-id="d6b73-213">ファイルの内容のハッシュは、ローカルファイルを更新する必要があるかどうかを判断するために比較されます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="d6b73-214">タイムスタンプ情報は比較されません。</span><span class="sxs-lookup"><span data-stu-id="d6b73-214">No timestamp information is compared.</span></span>

<span data-ttu-id="d6b73-215">更新が必要な場合、ツールは常にローカルファイルをリモートファイルに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="d6b73-216">使用法</span><span class="sxs-lookup"><span data-stu-id="d6b73-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="d6b73-217">引数</span><span class="sxs-lookup"><span data-stu-id="d6b73-217">Arguments</span></span>

| <span data-ttu-id="d6b73-218">引数</span><span class="sxs-lookup"><span data-stu-id="d6b73-218">Argument</span></span> | <span data-ttu-id="d6b73-219">説明</span><span class="sxs-lookup"><span data-stu-id="d6b73-219">Description</span></span> |
|-|-|
| <span data-ttu-id="d6b73-220">参照</span><span class="sxs-lookup"><span data-stu-id="d6b73-220">references</span></span> | <span data-ttu-id="d6b73-221">更新する必要があるリモート protobuf 参照への Url またはファイルパス。</span><span class="sxs-lookup"><span data-stu-id="d6b73-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="d6b73-222">すべてのリモート参照を更新するには、この引数を空のままにします。</span><span class="sxs-lookup"><span data-stu-id="d6b73-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="d6b73-223">オプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-223">Options</span></span>

| <span data-ttu-id="d6b73-224">短いオプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-224">Short option</span></span> | <span data-ttu-id="d6b73-225">長いオプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-225">Long option</span></span> | <span data-ttu-id="d6b73-226">説明</span><span class="sxs-lookup"><span data-stu-id="d6b73-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="d6b73-227">-p</span><span class="sxs-lookup"><span data-stu-id="d6b73-227">-p</span></span> | <span data-ttu-id="d6b73-228">--project</span><span class="sxs-lookup"><span data-stu-id="d6b73-228">--project</span></span> | <span data-ttu-id="d6b73-229">操作するプロジェクトファイルのパス。</span><span class="sxs-lookup"><span data-stu-id="d6b73-229">The path to the project file to operate on.</span></span> <span data-ttu-id="d6b73-230">ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。</span><span class="sxs-lookup"><span data-stu-id="d6b73-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="d6b73-231">--ドライ-実行</span><span class="sxs-lookup"><span data-stu-id="d6b73-231">--dry-run</span></span> | <span data-ttu-id="d6b73-232">新しいコンテンツをダウンロードせずに更新されるファイルの一覧を出力します。</span><span class="sxs-lookup"><span data-stu-id="d6b73-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="d6b73-233">一覧</span><span class="sxs-lookup"><span data-stu-id="d6b73-233">List</span></span>

<span data-ttu-id="d6b73-234">`list` コマンドは、プロジェクトファイル内のすべての Protobuf 参照を表示するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d6b73-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="d6b73-235">列のすべての値が既定値の場合、列は省略される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d6b73-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="d6b73-236">使用法</span><span class="sxs-lookup"><span data-stu-id="d6b73-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="d6b73-237">オプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-237">Options</span></span>

| <span data-ttu-id="d6b73-238">短いオプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-238">Short option</span></span> | <span data-ttu-id="d6b73-239">長いオプション</span><span class="sxs-lookup"><span data-stu-id="d6b73-239">Long option</span></span> | <span data-ttu-id="d6b73-240">説明</span><span class="sxs-lookup"><span data-stu-id="d6b73-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="d6b73-241">-p</span><span class="sxs-lookup"><span data-stu-id="d6b73-241">-p</span></span> | <span data-ttu-id="d6b73-242">--project</span><span class="sxs-lookup"><span data-stu-id="d6b73-242">--project</span></span> | <span data-ttu-id="d6b73-243">操作するプロジェクトファイルのパス。</span><span class="sxs-lookup"><span data-stu-id="d6b73-243">The path to the project file to operate on.</span></span> <span data-ttu-id="d6b73-244">ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。</span><span class="sxs-lookup"><span data-stu-id="d6b73-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d6b73-245">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="d6b73-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
