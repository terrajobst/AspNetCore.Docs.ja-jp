---
title: ASP.NET Core での開発におけるアプリシークレットの安全な保存
author: rick-anderson
description: ASP.NET Core アプリの開発中に、機密情報をアプリシークレットとして格納および取得する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: security/app-secrets
ms.openlocfilehash: 3323b7b614b7e8bc711b2c5acfb501b65b3d783b
ms.sourcegitcommit: 76d7fff62014c3db02564191ab768acea00f1b26
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852689"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="bb77c-103">ASP.NET Core での開発におけるアプリシークレットの安全な保存</span><span class="sxs-lookup"><span data-stu-id="bb77c-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

<span data-ttu-id="bb77c-104">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)、 [Scott addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="bb77c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="bb77c-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="bb77c-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bb77c-106">このドキュメントでは、ASP.NET Core アプリの開発時に機密データを格納および取得する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-106">This document explains techniques for storing and retrieving sensitive data during the development of an ASP.NET Core app.</span></span> <span data-ttu-id="bb77c-107">パスワードやその他の機密データをソースコードに格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="bb77c-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="bb77c-108">運用環境のシークレットは、開発またはテストには使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="bb77c-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="bb77c-109">シークレットはアプリと一緒にデプロイしないでください。</span><span class="sxs-lookup"><span data-stu-id="bb77c-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="bb77c-110">代わりに、環境変数、Azure Key Vault などの制御された方法を使用して、運用環境でシークレットを使用できるようにする必要があります。[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)を使用して、Azure テストおよび運用シークレットを格納し、保護することができます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="bb77c-111">環境変数</span><span class="sxs-lookup"><span data-stu-id="bb77c-111">Environment variables</span></span>

<span data-ttu-id="bb77c-112">環境変数は、コードまたはローカル構成ファイルにアプリシークレットを格納しないようにするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="bb77c-113">環境変数は、以前に指定したすべての構成ソースの構成値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="bb77c-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="bb77c-114">`Startup` コンストラクターで <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> を呼び出して、環境変数の値の読み取りを構成します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-114">Configure the reading of environment variable values by calling <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

<span data-ttu-id="bb77c-115">**個々のユーザーアカウント**のセキュリティが有効になっている ASP.NET Core web アプリを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="bb77c-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="bb77c-116">既定のデータベース接続文字列は、`DefaultConnection`キーを持つプロジェクトの*appsettings*ファイルに含まれています。</span><span class="sxs-lookup"><span data-stu-id="bb77c-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="bb77c-117">既定の接続文字列は LocalDB 用であり、ユーザーモードで実行され、パスワードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="bb77c-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="bb77c-118">アプリの展開中に、`DefaultConnection` キー値を環境変数の値でオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="bb77c-119">環境変数には、機密性の高い資格情報を持つ完全な接続文字列が格納される場合があります。</span><span class="sxs-lookup"><span data-stu-id="bb77c-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="bb77c-120">環境変数は通常、暗号化されていないプレーンテキストで格納されます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="bb77c-121">コンピューターまたはプロセスが侵害された場合、信頼されていない相手から環境変数にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="bb77c-122">ユーザーシークレットが漏えいしないようにするための追加の手段が必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="bb77c-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="bb77c-123">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="bb77c-123">Secret Manager</span></span>

<span data-ttu-id="bb77c-124">Secret Manager ツールは、ASP.NET Core プロジェクトの開発中に機微なデータを保存します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="bb77c-125">このコンテキストでは、機密データの一部がアプリシークレットになります。</span><span class="sxs-lookup"><span data-stu-id="bb77c-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="bb77c-126">アプリシークレットは、プロジェクトツリーとは別の場所に格納されます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="bb77c-127">アプリシークレットは、特定のプロジェクトに関連付けられているか、複数のプロジェクト間で共有されています。</span><span class="sxs-lookup"><span data-stu-id="bb77c-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="bb77c-128">アプリシークレットがソース管理にチェックインされていません。</span><span class="sxs-lookup"><span data-stu-id="bb77c-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="bb77c-129">シークレットマネージャーツールは、保存されているシークレットを暗号化せず、信頼されたストアとして扱わないでください。</span><span class="sxs-lookup"><span data-stu-id="bb77c-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="bb77c-130">開発目的でのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-130">It's for development purposes only.</span></span> <span data-ttu-id="bb77c-131">キーと値は、ユーザープロファイルディレクトリの JSON 構成ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="bb77c-132">Secret Manager ツールの動作</span><span class="sxs-lookup"><span data-stu-id="bb77c-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="bb77c-133">Secret Manager ツールは、値の格納場所や方法などの実装の詳細を抽象化します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-133">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="bb77c-134">このツールは、実装の詳細を把握していなくても使用できます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="bb77c-135">値は、ローカルコンピューター上のシステムで保護されたユーザープロファイルフォルダー内の JSON 構成ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-135">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="bb77c-136">Windows</span><span class="sxs-lookup"><span data-stu-id="bb77c-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="bb77c-137">ファイルシステムのパス:</span><span class="sxs-lookup"><span data-stu-id="bb77c-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="bb77c-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="bb77c-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="bb77c-139">ファイルシステムのパス:</span><span class="sxs-lookup"><span data-stu-id="bb77c-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="bb77c-140">上記のファイルパスで、`<user_secrets_id>` を *.csproj*ファイルで指定された `UserSecretsId` の値に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="bb77c-141">シークレットマネージャーツールで保存したデータの場所または形式に依存するコードは記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="bb77c-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="bb77c-142">これらの実装の詳細は変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bb77c-142">These implementation details may change.</span></span> <span data-ttu-id="bb77c-143">たとえば、シークレット値は暗号化されませんが、将来の可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bb77c-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a><span data-ttu-id="bb77c-144">シークレットマネージャーツールをインストールする</span><span class="sxs-lookup"><span data-stu-id="bb77c-144">Install the Secret Manager tool</span></span>

<span data-ttu-id="bb77c-145">Secret Manager ツールは、.NET Core SDK 2.1.300 以降の .NET Core CLI にバンドルされています。</span><span class="sxs-lookup"><span data-stu-id="bb77c-145">The Secret Manager tool is bundled with the .NET Core CLI in .NET Core SDK 2.1.300 or later.</span></span> <span data-ttu-id="bb77c-146">2\.1.300 の前にバージョンを .NET Core SDK には、ツールのインストールが必要です。</span><span class="sxs-lookup"><span data-stu-id="bb77c-146">For .NET Core SDK versions before 2.1.300, tool installation is necessary.</span></span>

> [!TIP]
> <span data-ttu-id="bb77c-147">コマンドシェルから `dotnet --version` を実行して、インストールされている .NET Core SDK バージョン番号を確認します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-147">Run `dotnet --version` from a command shell to see the installed .NET Core SDK version number.</span></span>

<span data-ttu-id="bb77c-148">使用されている .NET Core SDK にツールが含まれている場合は、次のような警告が表示されます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-148">A warning is displayed if the .NET Core SDK being used includes the tool:</span></span>

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

<span data-ttu-id="bb77c-149">ASP.NET Core プロジェクトに[Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="bb77c-149">Install the [Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) NuGet package in your ASP.NET Core project.</span></span> <span data-ttu-id="bb77c-150">例:</span><span class="sxs-lookup"><span data-stu-id="bb77c-150">For example:</span></span>

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

<span data-ttu-id="bb77c-151">コマンドシェルで次のコマンドを実行して、ツールのインストールを検証します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-151">Execute the following command in a command shell to validate the tool installation:</span></span>

```dotnetcli
dotnet user-secrets -h
```

<span data-ttu-id="bb77c-152">Secret Manager ツールには、使用法、オプション、およびコマンドヘルプのサンプルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-152">The Secret Manager tool displays sample usage, options, and command help:</span></span>

```console
Usage: dotnet user-secrets [options] [command]

Options:
  -?|-h|--help                        Show help information
  --version                           Show version information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id                                The user secret ID to use.

Commands:
  clear   Deletes all the application secrets
  list    Lists all the application secrets
  remove  Removes the specified user secret
  set     Sets the user secret to the specified value

Use "dotnet user-secrets [command] --help" for more information about a command.
```

> [!NOTE]
> <span data-ttu-id="bb77c-153">*.Csproj*ファイルの `DotNetCliToolReference` 要素で定義されているツールを実行するには、 *.csproj*ファイルと同じディレクトリに存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bb77c-153">You must be in the same directory as the *.csproj* file to run tools defined in the *.csproj* file's `DotNetCliToolReference` elements.</span></span>

::: moniker-end

## <a name="enable-secret-storage"></a><span data-ttu-id="bb77c-154">シークレットストレージを有効にする</span><span class="sxs-lookup"><span data-stu-id="bb77c-154">Enable secret storage</span></span>

<span data-ttu-id="bb77c-155">Secret Manager ツールは、ユーザープロファイルに格納されているプロジェクト固有の構成設定を操作します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-155">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bb77c-156">Secret Manager ツールには、.NET Core SDK 3.0.100 以降の `init` コマンドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="bb77c-156">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="bb77c-157">ユーザーシークレットを使用するには、プロジェクトディレクトリで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-157">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="bb77c-158">上記のコマンドは、 *.csproj*ファイルの `PropertyGroup` 内に `UserSecretsId` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-158">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="bb77c-159">既定では `UserSecretsId` の内部テキストは GUID です。</span><span class="sxs-lookup"><span data-stu-id="bb77c-159">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="bb77c-160">内部テキストは任意ですが、プロジェクトに固有です。</span><span class="sxs-lookup"><span data-stu-id="bb77c-160">The inner text is arbitrary, but is unique to the project.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="bb77c-161">ユーザーシークレットを使用するには、 *.csproj*ファイルの `PropertyGroup` 内に `UserSecretsId` 要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-161">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="bb77c-162">`UserSecretsId` の内部テキストは任意ですが、プロジェクトに固有です。</span><span class="sxs-lookup"><span data-stu-id="bb77c-162">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="bb77c-163">開発者は通常、`UserSecretsId`の GUID を生成します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-163">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> <span data-ttu-id="bb77c-164">Visual Studio で、ソリューションエクスプローラーでプロジェクトを右クリックし、コンテキストメニューから **[ユーザーシークレットの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-164">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="bb77c-165">このジェスチャは、GUID で設定された `UserSecretsId` 要素を .csproj ファイルに追加し*ます*。</span><span class="sxs-lookup"><span data-stu-id="bb77c-165">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="bb77c-166">シークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="bb77c-166">Set a secret</span></span>

<span data-ttu-id="bb77c-167">キーとその値で構成されるアプリシークレットを定義します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-167">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="bb77c-168">シークレットは、プロジェクトの `UserSecretsId` 値に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-168">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="bb77c-169">たとえば、 *.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-169">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="bb77c-170">前の例では、コロンは、`Movies` が `ServiceApiKey` プロパティを持つオブジェクトリテラルであることを示しています。</span><span class="sxs-lookup"><span data-stu-id="bb77c-170">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="bb77c-171">シークレットマネージャーツールは、他のディレクトリからも使用できます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-171">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="bb77c-172">`--project` オプションを使用して、 *.csproj*ファイルが存在するファイルシステムパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-172">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="bb77c-173">例:</span><span class="sxs-lookup"><span data-stu-id="bb77c-173">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="bb77c-174">Visual Studio での JSON 構造のフラット化</span><span class="sxs-lookup"><span data-stu-id="bb77c-174">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="bb77c-175">Visual Studio の **[ユーザーシークレットの管理]** ジェスチャは、テキストエディターで*シークレットの json*ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-175">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="bb77c-176">*シークレット*の内容を、格納されるキーと値のペアで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-176">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="bb77c-177">例:</span><span class="sxs-lookup"><span data-stu-id="bb77c-177">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="bb77c-178">JSON 構造体は、`dotnet user-secrets remove` または `dotnet user-secrets set`を使用した変更後にフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-178">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="bb77c-179">たとえば、`dotnet user-secrets remove "Movies:ConnectionString"` を実行すると、`Movies` オブジェクトリテラルが折りたたまれます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-179">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="bb77c-180">変更後のファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="bb77c-180">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="bb77c-181">複数のシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="bb77c-181">Set multiple secrets</span></span>

<span data-ttu-id="bb77c-182">シークレットのバッチは、JSON を `set` コマンドにパイプすることによって設定できます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-182">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="bb77c-183">次の例では、*入力の json*ファイルの内容をパイプ処理して `set` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-183">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="bb77c-184">Windows</span><span class="sxs-lookup"><span data-stu-id="bb77c-184">Windows</span></span>](#tab/windows)

<span data-ttu-id="bb77c-185">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-185">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="bb77c-186">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="bb77c-186">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="bb77c-187">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-187">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="bb77c-188">シークレットにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="bb77c-188">Access a secret</span></span>

<span data-ttu-id="bb77c-189">[ASP.NET Core 構成 API](xref:fundamentals/configuration/index)は、シークレットマネージャーのシークレットへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-189">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

<span data-ttu-id="bb77c-190">プロジェクトが .NET Framework 対象である場合は、 [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="bb77c-190">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="bb77c-191">ASP.NET Core 2.0 以降では、プロジェクトが <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出して、構成済みの既定値でホストの新しいインスタンスを初期化すると、開発モードでユーザーシークレットの構成ソースが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-191">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="bb77c-192"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> が <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>されている場合、<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> 呼び出し `CreateDefaultBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-192">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="bb77c-193">`CreateDefaultBuilder` が呼び出されない場合は、`Startup` コンストラクターで <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> を呼び出すことによって、ユーザーシークレット構成ソースを明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-193">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> in the `Startup` constructor.</span></span> <span data-ttu-id="bb77c-194">次の例に示すように、アプリが開発環境で実行されている場合にのみ `AddUserSecrets` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-194">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="bb77c-195">[Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="bb77c-195">Install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="bb77c-196">`Startup` コンストラクターで <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> の呼び出しを使用して、ユーザーシークレット構成ソースを追加します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-196">Add the user secrets configuration source with a call to <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

<span data-ttu-id="bb77c-197">ユーザーシークレットは、`Configuration` API を使用して取得できます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-197">User secrets can be retrieved via the `Configuration` API:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="bb77c-198">シークレットを POCO にマップする</span><span class="sxs-lookup"><span data-stu-id="bb77c-198">Map secrets to a POCO</span></span>

<span data-ttu-id="bb77c-199">オブジェクトリテラル全体を POCO (プロパティを持つ単純な .NET クラス) にマップすると、関連するプロパティを集計するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-199">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="bb77c-200">上記のシークレットを POCO にマップするには、`Configuration` API の[オブジェクトグラフバインド](xref:fundamentals/configuration/index#bind-to-an-object-graph)機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-200">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="bb77c-201">次のコードは、カスタム `MovieSettings` POCO にバインドし、`ServiceApiKey` プロパティ値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="bb77c-201">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

<span data-ttu-id="bb77c-202">`Movies:ConnectionString` と `Movies:ServiceApiKey` シークレットは `MovieSettings`の各プロパティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-202">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="bb77c-203">シークレットを使用した文字列の置換</span><span class="sxs-lookup"><span data-stu-id="bb77c-203">String replacement with secrets</span></span>

<span data-ttu-id="bb77c-204">プレーンテキストでのパスワードの保存は安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="bb77c-204">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="bb77c-205">たとえば、 *appsettings*に格納されているデータベース接続文字列には、指定されたユーザーのパスワードを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-205">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="bb77c-206">より安全な方法は、パスワードをシークレットとして保存することです。</span><span class="sxs-lookup"><span data-stu-id="bb77c-206">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="bb77c-207">例:</span><span class="sxs-lookup"><span data-stu-id="bb77c-207">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="bb77c-208">`Password` キーと値のペアを、 *appsettings*の接続文字列から削除します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-208">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="bb77c-209">例:</span><span class="sxs-lookup"><span data-stu-id="bb77c-209">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="bb77c-210">シークレットの値を <xref:System.Data.SqlClient.SqlConnectionStringBuilder> オブジェクトの <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password*> プロパティに設定して、接続文字列を完成させることができます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-210">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password*> property to complete the connection string:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a><span data-ttu-id="bb77c-211">シークレットを一覧表示する</span><span class="sxs-lookup"><span data-stu-id="bb77c-211">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="bb77c-212">*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-212">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="bb77c-213">次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-213">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="bb77c-214">前の例では、キー名のコロンは、 *json*内のオブジェクト階層を表しています。</span><span class="sxs-lookup"><span data-stu-id="bb77c-214">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="bb77c-215">1つのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="bb77c-215">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="bb77c-216">*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-216">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="bb77c-217">アプリの*シークレットの json*ファイルが変更され、`MoviesConnectionString` キーに関連付けられているキーと値のペアが削除されました。</span><span class="sxs-lookup"><span data-stu-id="bb77c-217">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="bb77c-218">`dotnet user-secrets list` を実行すると、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-218">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="bb77c-219">すべてのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="bb77c-219">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="bb77c-220">*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bb77c-220">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="bb77c-221">アプリのすべてのユーザーシークレットが、*シークレットの json*ファイルから削除されています。</span><span class="sxs-lookup"><span data-stu-id="bb77c-221">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="bb77c-222">`dotnet user-secrets list` を実行すると、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bb77c-222">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="bb77c-223">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="bb77c-223">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
