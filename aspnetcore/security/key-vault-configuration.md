---
title: ASP.NET Core での azure Key Vault 構成プロバイダー
author: guardrex
description: Azure Key Vault 構成プロバイダーを使用して、実行時に読み込まれる名前と値のペアを使用してアプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/13/2019
uid: security/key-vault-configuration
ms.openlocfilehash: be176ed612be0773c4a5b52607c023da3856ac14
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815332"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="10d48-103">ASP.NET Core での azure Key Vault 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="10d48-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="10d48-104">作成者 [Luke Latham](https://github.com/guardrex)および [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="10d48-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="10d48-105">このドキュメントは、使用する方法を説明します、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)構成プロバイダーを Azure Key Vault シークレットからアプリの構成値を読み込めません。</span><span class="sxs-lookup"><span data-stu-id="10d48-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="10d48-106">Azure Key Vault とは、暗号化キーとシークレットをアプリやサービスで使用される保護に役立つクラウド ベース サービスです。</span><span class="sxs-lookup"><span data-stu-id="10d48-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="10d48-107">ASP.NET Core アプリを Azure Key Vault を使用するための一般的なシナリオは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="10d48-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="10d48-108">機密性の高い構成データへのアクセスを制御します。</span><span class="sxs-lookup"><span data-stu-id="10d48-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="10d48-109">構成データを格納するときに、ハードウェア セキュリティ モジュール (HSM) の検証を 140-2 レベル 2 FIPS の要件を満たします。</span><span class="sxs-lookup"><span data-stu-id="10d48-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="10d48-110">このシナリオでは、ASP.NET Core 2.1 を対象とするアプリの使用可能なまたはそれ以降です。</span><span class="sxs-lookup"><span data-stu-id="10d48-110">This scenario is available for apps that target ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="10d48-111">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="10d48-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="10d48-112">パッケージ</span><span class="sxs-lookup"><span data-stu-id="10d48-112">Packages</span></span>

<span data-ttu-id="10d48-113">Azure Key Vault 構成プロバイダーを使用するへのパッケージ参照を追加、 [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="10d48-113">To use the Azure Key Vault Configuration Provider, add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

<span data-ttu-id="10d48-114">採用する、[の Azure リソースの id を管理](/azure/active-directory/managed-identities-azure-resources/overview)シナリオでは、パッケージ参照を追加、 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="10d48-114">To adopt the [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) scenario, add a package reference to the [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) package.</span></span>

> [!NOTE]
> <span data-ttu-id="10d48-115">最新の安定版リリースの書き込み時に`Microsoft.Azure.Services.AppAuthentication`、バージョン`1.0.3`、サポートを提供します[の id を管理システムによって割り当てられた](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)します。</span><span class="sxs-lookup"><span data-stu-id="10d48-115">At the time of writing, the latest stable release of `Microsoft.Azure.Services.AppAuthentication`, version `1.0.3`, provides support for [system-assigned managed identities](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work).</span></span> <span data-ttu-id="10d48-116">サポート*の id を管理ユーザーによって割り当てられた*で使用できるは、`1.2.0-preview2`パッケージ。</span><span class="sxs-lookup"><span data-stu-id="10d48-116">Support for *user-assigned managed identities* is available in the `1.2.0-preview2` package.</span></span> <span data-ttu-id="10d48-117">このトピックでは、システムで管理される id の使用を示します、提供されているサンプル アプリは、バージョンを使用して`1.0.3`の`Microsoft.Azure.Services.AppAuthentication`パッケージ。</span><span class="sxs-lookup"><span data-stu-id="10d48-117">This topic demonstrates the use of system-managed identities, and the provided sample app uses version `1.0.3` of the `Microsoft.Azure.Services.AppAuthentication` package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="10d48-118">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="10d48-118">Sample app</span></span>

<span data-ttu-id="10d48-119">サンプル アプリの実行によって 2 つのモードのいずれかで、`#define`の上部にあるステートメント、 *Program.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="10d48-119">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="10d48-120">`Certificate` &ndash; Azure Key Vault に格納されているアクセス シークレットを Azure Key Vault のクライアント ID と X.509 証明書の使用を示します。</span><span class="sxs-lookup"><span data-stu-id="10d48-120">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="10d48-121">このバージョンのサンプルは、Azure App Service または ASP.NET Core アプリのサービスを提供できる任意のホストに展開されている任意の場所から実行できます。</span><span class="sxs-lookup"><span data-stu-id="10d48-121">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="10d48-122">`Managed` &ndash; 使用する方法を示します[の Azure リソースの id を管理](/azure/active-directory/managed-identities-azure-resources/overview)アプリのコードまたは構成に格納されている資格情報のない Azure AD 認証を使用した Azure Key Vault にアプリを認証します。</span><span class="sxs-lookup"><span data-stu-id="10d48-122">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="10d48-123">管理対象 id 認証を使用する場合、Azure AD アプリケーション ID とパスワード (クライアント シークレット) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="10d48-123">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="10d48-124">`Managed`バージョンのサンプルを Azure にデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="10d48-124">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="10d48-125">ガイダンスに従って、 [Azure リソースの管理対象 id を使用して](#use-managed-identities-for-azure-resources)セクション。</span><span class="sxs-lookup"><span data-stu-id="10d48-125">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="10d48-126">プリプロセッサ ディレクティブを使用してサンプル アプリを構成する方法の詳細 (`#define`) を参照してください<xref:index#preprocessor-directives-in-sample-code>します。</span><span class="sxs-lookup"><span data-stu-id="10d48-126">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="10d48-127">開発環境でのシークレットのストレージ</span><span class="sxs-lookup"><span data-stu-id="10d48-127">Secret storage in the Development environment</span></span>

<span data-ttu-id="10d48-128">シークレットを使用してローカルの設定、 [Secret Manager ツール](xref:security/app-secrets)します。</span><span class="sxs-lookup"><span data-stu-id="10d48-128">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="10d48-129">機密情報が読み込まれているサンプル アプリを開発環境でローカル コンピューターで実行すると、ローカルの Secret Manager ストアから。</span><span class="sxs-lookup"><span data-stu-id="10d48-129">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="10d48-130">Secret Manager ツールが必要です、`<UserSecretsId>`アプリのプロジェクト ファイルのプロパティ。</span><span class="sxs-lookup"><span data-stu-id="10d48-130">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="10d48-131">プロパティ値を設定 (`{GUID}`) の一意の guid。</span><span class="sxs-lookup"><span data-stu-id="10d48-131">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="10d48-132">シークレットは、名前と値のペアとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="10d48-132">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="10d48-133">階層型の値 (構成セクション) を使用して、 `:` (コロン) で区切り記号として[ASP.NET Core 構成](xref:fundamentals/configuration/index)キー名。</span><span class="sxs-lookup"><span data-stu-id="10d48-133">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="10d48-134">Secret Manager は、プロジェクトのコンテンツのルートに開かれているコマンド シェルから使用場所`{SECRET NAME}`名前と`{SECRET VALUE}`値です。</span><span class="sxs-lookup"><span data-stu-id="10d48-134">The Secret Manager is used from a command shell opened to the project's content root, where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="10d48-135">サンプル アプリのシークレットを設定するプロジェクトのコンテンツのルートからコマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="10d48-135">Execute the following commands in a command shell from the project's content root to set the secrets for the sample app:</span></span>

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="10d48-136">これらのシークレットがで Azure Key Vault に格納されている場合、 [Azure Key Vault の実稼働環境でのシークレット ストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) セクションで、`_dev`にサフィックスが変更された`_prod`します。</span><span class="sxs-lookup"><span data-stu-id="10d48-136">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="10d48-137">サフィックスは、アプリの出力の構成値のソースを示す視覚的な合図を提供します。</span><span class="sxs-lookup"><span data-stu-id="10d48-137">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="10d48-138">Azure Key Vault の実稼働環境でのシークレットのストレージ</span><span class="sxs-lookup"><span data-stu-id="10d48-138">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="10d48-139">手順に従って、[クイック スタート。設定して、Azure CLI を使用して Azure Key Vault からシークレットを取得](/azure/key-vault/quick-create-cli)トピックでは、Azure Key Vault を作成およびサンプル アプリで使用されるシークレットを格納するためここで説明します。</span><span class="sxs-lookup"><span data-stu-id="10d48-139">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="10d48-140">詳細については、トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="10d48-140">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="10d48-141">Azure のクラウド シェルを開くには、次のメソッドのいずれかを使用して、 [Azure portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="10d48-141">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="10d48-142">選択**試して**コード ブロックの右上隅にします。</span><span class="sxs-lookup"><span data-stu-id="10d48-142">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="10d48-143">テキスト ボックスに検索文字列の"Azure CLI"を使用します。</span><span class="sxs-lookup"><span data-stu-id="10d48-143">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="10d48-144">ブラウザーで Cloud Shell を開き、 **Cloud Shell の起動**ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="10d48-144">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="10d48-145">選択、 **Cloud Shell** Azure portal の右上隅のメニュー ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="10d48-145">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="10d48-146">詳細については、次を参照してください。 [Azure コマンド ライン インターフェイス (CLI)](/cli/azure/)と[Azure Cloud Shell の概要](/azure/cloud-shell/overview)します。</span><span class="sxs-lookup"><span data-stu-id="10d48-146">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="10d48-147">既に認証されていない場合でサインイン、`az login`コマンド。</span><span class="sxs-lookup"><span data-stu-id="10d48-147">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="10d48-148">次のコマンドでリソース グループを作成、`{RESOURCE GROUP NAME}`は新しいリソース グループのリソース グループ名と`{LOCATION}`Azure リージョン (データ センター) には。</span><span class="sxs-lookup"><span data-stu-id="10d48-148">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="10d48-149">次のコマンドを使用して、リソース グループに key vault を作成場所`{KEY VAULT NAME}`、新しいキー コンテナーの名前を指定および`{LOCATION}`は Azure の地域 (データ センター)。</span><span class="sxs-lookup"><span data-stu-id="10d48-149">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="10d48-150">名前と値のペアとして、key vault にシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="10d48-150">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="10d48-151">Azure Key Vault シークレットの名前は、英数字とダッシュに制限されます。</span><span class="sxs-lookup"><span data-stu-id="10d48-151">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="10d48-152">階層型の値 (構成セクション) を使用して、 `--` (ダッシュを 2 つ) を区切り記号として。</span><span class="sxs-lookup"><span data-stu-id="10d48-152">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="10d48-153">サブキーのセクションを区切るために通常使用される、コロン[ASP.NET Core 構成](xref:fundamentals/configuration/index)、key vault のシークレット名では許可されません。</span><span class="sxs-lookup"><span data-stu-id="10d48-153">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="10d48-154">そのため、2 つのダッシュが使用され、シークレットは、アプリの構成に読み込まれるときに、コロンのスワップします。</span><span class="sxs-lookup"><span data-stu-id="10d48-154">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="10d48-155">次のシークレットは、サンプル アプリで使用するためです。</span><span class="sxs-lookup"><span data-stu-id="10d48-155">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="10d48-156">値を`_prod`サフィックスから区別するために、`_dev`ユーザー シークレットから開発環境に読み込まれた値のサフィックスします。</span><span class="sxs-lookup"><span data-stu-id="10d48-156">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="10d48-157">置換`{KEY VAULT NAME}`前の手順で作成した key vault の名前に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="10d48-157">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="10d48-158">アプリケーション ID と X.509 証明書を使用して、Azure でホストされているアプリ</span><span class="sxs-lookup"><span data-stu-id="10d48-158">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="10d48-159">Azure AD を構成、キー コンテナーへの認証に証明書を Azure Active Directory アプリケーション ID と X.509 を使用するには、Azure Key Vault とアプリ **、アプリが Azure の外部でホストされている**します。</span><span class="sxs-lookup"><span data-stu-id="10d48-159">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="10d48-160">詳細については、次を参照してください。[キー、シークレット、および証明書に関する](/azure/key-vault/about-keys-secrets-and-certificates)します。</span><span class="sxs-lookup"><span data-stu-id="10d48-160">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="10d48-161">使用が推奨アプリを Azure でホストされているは、アプリケーション ID と X.509 証明書を使用がサポートされています、[の Azure リソースの id を管理](#use-managed-identities-for-azure-resources)Azure でのアプリをホストする場合。</span><span class="sxs-lookup"><span data-stu-id="10d48-161">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="10d48-162">管理対象 id は、アプリや開発環境では、証明書を格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="10d48-162">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="10d48-163">アプリケーション ID と X.509 証明書の場合にサンプル アプリでは、`#define`の上部にあるステートメント、 *Program.cs*に設定されているファイル`Certificate`します。</span><span class="sxs-lookup"><span data-stu-id="10d48-163">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="10d48-164">PKCS #12 アーカイブを作成 ( *.pfx*) 証明書。</span><span class="sxs-lookup"><span data-stu-id="10d48-164">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="10d48-165">証明書を作成するためのオプションがあります。 [Windows で MakeCert](/windows/desktop/seccrypto/makecert)と[OpenSSL](https://www.openssl.org/)します。</span><span class="sxs-lookup"><span data-stu-id="10d48-165">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="10d48-166">現在のユーザーの個人証明書ストアに証明書をインストールします。</span><span class="sxs-lookup"><span data-stu-id="10d48-166">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="10d48-167">キーをマークするエクスポート可能とは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="10d48-167">Marking the key as exportable is optional.</span></span> <span data-ttu-id="10d48-168">このプロセスの後半で使用される証明書の拇印に注意してください。</span><span class="sxs-lookup"><span data-stu-id="10d48-168">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="10d48-169">PKCS #12 アーカイブのエクスポート ( *.pfx*) DER でエンコードされた証明書として証明書 ( *.cer*)。</span><span class="sxs-lookup"><span data-stu-id="10d48-169">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="10d48-170">Azure AD にアプリを登録 (**アプリの登録**)。</span><span class="sxs-lookup"><span data-stu-id="10d48-170">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="10d48-171">DER でエンコードされた証明書のアップロード ( *.cer*) Azure AD に。</span><span class="sxs-lookup"><span data-stu-id="10d48-171">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="10d48-172">Azure AD でアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="10d48-172">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="10d48-173">移動します**証明書およびシークレット**します。</span><span class="sxs-lookup"><span data-stu-id="10d48-173">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="10d48-174">選択**証明書のアップロード**パブリック キーを含む証明書をアップロードします。</span><span class="sxs-lookup"><span data-stu-id="10d48-174">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="10d48-175">A *.cer*、 *.pem*、または *.crt*証明書は許容されます。</span><span class="sxs-lookup"><span data-stu-id="10d48-175">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="10d48-176">アプリの key vault 名、アプリケーション ID、および証明書の拇印を格納する*appsettings.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="10d48-176">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="10d48-177">移動します**キー コンテナー** Azure portal でします。</span><span class="sxs-lookup"><span data-stu-id="10d48-177">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="10d48-178">作成した key vault の選択、 [Azure Key Vault の実稼働環境でのシークレット ストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)セクション。</span><span class="sxs-lookup"><span data-stu-id="10d48-178">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="10d48-179">選択**アクセス ポリシー**します。</span><span class="sxs-lookup"><span data-stu-id="10d48-179">Select **Access policies**.</span></span>
1. <span data-ttu-id="10d48-180">選択**新規追加**します。</span><span class="sxs-lookup"><span data-stu-id="10d48-180">Select **Add new**.</span></span>
1. <span data-ttu-id="10d48-181">選択**プリンシパルの選択**名前で登録済みのアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="10d48-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="10d48-182">選択、**選択**ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="10d48-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="10d48-183">開いている**シークレットのアクセス許可**を使用してアプリを提供し、**取得**と**一覧**アクセス許可。</span><span class="sxs-lookup"><span data-stu-id="10d48-183">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="10d48-184">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="10d48-184">Select **OK**.</span></span>
1. <span data-ttu-id="10d48-185">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="10d48-185">Select **Save**.</span></span>
1. <span data-ttu-id="10d48-186">アプリをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="10d48-186">Deploy the app.</span></span>

<span data-ttu-id="10d48-187">`Certificate`サンプル アプリからその構成値を取得する`IConfigurationRoot`シークレットの名前と同じ名前で。</span><span class="sxs-lookup"><span data-stu-id="10d48-187">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="10d48-188">非階層型の値:値は、`SecretName`では、行わ`config["SecretName"]`します。</span><span class="sxs-lookup"><span data-stu-id="10d48-188">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="10d48-189">階層型の値 (セクション)。使用`:`(コロン) 表記または`GetSection`拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="10d48-189">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="10d48-190">構成値を取得するのにには、これらの方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="10d48-190">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="10d48-191">X.509 証明書は、OS によって管理されます。</span><span class="sxs-lookup"><span data-stu-id="10d48-191">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="10d48-192">アプリによる呼び出し`AddAzureKeyVault`によって提供される値を持つ、 *appsettings.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="10d48-192">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="10d48-193">値の例:</span><span class="sxs-lookup"><span data-stu-id="10d48-193">Example values:</span></span>

* <span data-ttu-id="10d48-194">Key vault 名: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="10d48-194">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="10d48-195">アプリケーション ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="10d48-195">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="10d48-196">証明書の拇印: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="10d48-196">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="10d48-197">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="10d48-197">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="10d48-198">アプリを実行すると、web ページには、読み込まれた、シークレットの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="10d48-198">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="10d48-199">開発環境でシークレット値を読み込むと、`_dev`サフィックス。</span><span class="sxs-lookup"><span data-stu-id="10d48-199">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="10d48-200">運用環境で、値を読み込むと、`_prod`サフィックス。</span><span class="sxs-lookup"><span data-stu-id="10d48-200">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="10d48-201">Azure リソースの管理対象 id を使用してください。</span><span class="sxs-lookup"><span data-stu-id="10d48-201">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="10d48-202">**Azure にデプロイされたアプリ**活用できる[の Azure リソースの id を管理](/azure/active-directory/managed-identities-azure-resources/overview)、資格情報なしの Azure AD 認証を使用して Azure Key Vault での認証にアプリを許可する (アプリケーション ID とPassword/Client シークレット)、アプリに格納されています。</span><span class="sxs-lookup"><span data-stu-id="10d48-202">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="10d48-203">サンプル アプリは Azure リソースの管理対象 id を使用時に、`#define`の上部にあるステートメント、 *Program.cs*に設定されているファイル`Managed`します。</span><span class="sxs-lookup"><span data-stu-id="10d48-203">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="10d48-204">アプリの資格情報コンテナー名を入力します。 *appsettings.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="10d48-204">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="10d48-205">アプリケーション ID とパスワード (クライアント シークレット) に設定すると、サンプル アプリが必要としない、`Managed`バージョンについては、これらの構成エントリは無視できます。</span><span class="sxs-lookup"><span data-stu-id="10d48-205">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="10d48-206">アプリが Azure にデプロイし、Azure に格納されている場合のみ、コンテナー名を使用して Azure Key Vault にアクセスするアプリで認証される、 *appsettings.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="10d48-206">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="10d48-207">サンプル アプリを Azure App Service にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="10d48-207">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="10d48-208">Azure App Service にデプロイされたアプリは、自動的にサービスを作成するときに Azure AD に登録されます。</span><span class="sxs-lookup"><span data-stu-id="10d48-208">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="10d48-209">次のコマンドで使用するためのデプロイからのオブジェクト ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="10d48-209">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="10d48-210">オブジェクト ID が Azure portal に表示されます、 **Identity** App Service のパネルです。</span><span class="sxs-lookup"><span data-stu-id="10d48-210">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="10d48-211">使用してアプリを提供するアプリのオブジェクト ID では、Azure CLI を使用して`list`と`get`key vault へのアクセス許可。</span><span class="sxs-lookup"><span data-stu-id="10d48-211">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="10d48-212">**アプリの再起動**Azure CLI、PowerShell、または Azure portal を使用します。</span><span class="sxs-lookup"><span data-stu-id="10d48-212">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="10d48-213">サンプル アプリ:</span><span class="sxs-lookup"><span data-stu-id="10d48-213">The sample app:</span></span>

* <span data-ttu-id="10d48-214">インスタンスを作成、`AzureServiceTokenProvider`接続文字列を使用せずクラス。</span><span class="sxs-lookup"><span data-stu-id="10d48-214">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="10d48-215">接続文字列が指定されない場合、プロバイダーは、Azure リソースの管理対象 id からアクセス トークンを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="10d48-215">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="10d48-216">新しい`KeyVaultClient`で作成されたが、`AzureServiceTokenProvider`インスタンス トークンのコールバック。</span><span class="sxs-lookup"><span data-stu-id="10d48-216">A new `KeyVaultClient` is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="10d48-217">`KeyVaultClient`の既定の実装のインスタンスを使用`IKeyVaultSecretManager`をすべてのシークレット値を読み込むし、二重ダッシュに置き換えられます (`--`) にコロン (`:`) キー名。</span><span class="sxs-lookup"><span data-stu-id="10d48-217">The `KeyVaultClient` instance is used with a default implementation of `IKeyVaultSecretManager` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="10d48-218">アプリを実行すると、web ページには、読み込まれた、シークレットの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="10d48-218">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="10d48-219">シークレットの値がある、開発環境で、`_dev`サフィックスをユーザーの機密情報で提供しているためです。</span><span class="sxs-lookup"><span data-stu-id="10d48-219">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="10d48-220">運用環境で、値を読み込むと、`_prod`サフィックスを Azure Key Vault で提供しているためです。</span><span class="sxs-lookup"><span data-stu-id="10d48-220">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="10d48-221">表示された場合、`Access denied`エラー、アプリが Azure AD に登録され、key vault へのアクセスを提供することを確認します。</span><span class="sxs-lookup"><span data-stu-id="10d48-221">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="10d48-222">Azure でサービスを再起動したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="10d48-222">Confirm that you've restarted the service in Azure.</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="10d48-223">キー名のプレフィックスを使用します。</span><span class="sxs-lookup"><span data-stu-id="10d48-223">Use a key name prefix</span></span>

<span data-ttu-id="10d48-224">`AddAzureKeyVault` 実装を受け取るオーバー ロードを提供します。 `IKeyVaultSecretManager`、構成キーに変換されます主要 vault のシークレットを制御できます。</span><span class="sxs-lookup"><span data-stu-id="10d48-224">`AddAzureKeyVault` provides an overload that accepts an implementation of `IKeyVaultSecretManager`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="10d48-225">たとえば、アプリの起動時に指定したプレフィックス値に基づくシークレットの値を読み込むインターフェイスを実装できます。</span><span class="sxs-lookup"><span data-stu-id="10d48-225">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="10d48-226">これにより、たとえば、アプリのバージョンに基づくシークレットを読み込めませんできます。</span><span class="sxs-lookup"><span data-stu-id="10d48-226">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="10d48-227">同じ key vault にシークレットを複数のアプリを配置する環境のシークレットを配置するか、key vault のシークレットでプレフィックスを使用しないでください (たとえば、*開発*と*運用*シークレット) を同じ資格情報コンテナー。</span><span class="sxs-lookup"><span data-stu-id="10d48-227">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="10d48-228">別のアプリと開発/運用環境で最高レベルのセキュリティのアプリの環境を分離する個別の key vault を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="10d48-228">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="10d48-229">キーに次の例では、シークレットが確立されている資格情報コンテナー (と Secret Manager ツールを使用して、開発環境用) の`5000-AppSecret`(key vault のシークレット名で許可されていない期間)。</span><span class="sxs-lookup"><span data-stu-id="10d48-229">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="10d48-230">このシークレットは、アプリのバージョン 5.0.0.0 用アプリのシークレットを表します。</span><span class="sxs-lookup"><span data-stu-id="10d48-230">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="10d48-231">5\.1.0.0、アプリの別のバージョンのシークレットが、キーに追加コンテナー (と Secret Manager ツールを使用して) の`5100-AppSecret`します。</span><span class="sxs-lookup"><span data-stu-id="10d48-231">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="10d48-232">各アプリのバージョンでは、としては、その構成にそのバージョンのシークレット値を読み込みます`AppSecret`シークレットが読み込まれるバージョンを削除します。</span><span class="sxs-lookup"><span data-stu-id="10d48-232">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="10d48-233">`AddAzureKeyVault` カスタムを使用して呼び出した`IKeyVaultSecretManager`:</span><span class="sxs-lookup"><span data-stu-id="10d48-233">`AddAzureKeyVault` is called with a custom `IKeyVaultSecretManager`:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?highlight=30-34)]

<span data-ttu-id="10d48-234">`IKeyVaultSecretManager`実装が構成に適切なシークレットを読み込むのシークレットのバージョンのプレフィックスに反応します。</span><span class="sxs-lookup"><span data-stu-id="10d48-234">The `IKeyVaultSecretManager` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

<span data-ttu-id="10d48-235">`Load`メソッドが付いているバージョンのプレフィックスを検索する資格情報コンテナーのシークレットを反復処理するプロバイダーのアルゴリズムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="10d48-235">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="10d48-236">バージョンのプレフィックスがで検出されたときに`Load`、アルゴリズムを使用して、`GetKey`シークレット名の構成名を返すメソッド。</span><span class="sxs-lookup"><span data-stu-id="10d48-236">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="10d48-237">シークレットの名前からのバージョンのプレフィックスを削除し、アプリの構成に名前/値ペアの読み込みのシークレット名の残りの部分を返します。</span><span class="sxs-lookup"><span data-stu-id="10d48-237">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="10d48-238">このアプローチが実装されている場合。</span><span class="sxs-lookup"><span data-stu-id="10d48-238">When this approach is implemented:</span></span>

1. <span data-ttu-id="10d48-239">アプリのプロジェクト ファイルで指定されたアプリのバージョン。</span><span class="sxs-lookup"><span data-stu-id="10d48-239">The app's version specified in the app's project file.</span></span> <span data-ttu-id="10d48-240">次の例では、アプリのバージョンに設定されて`5.0.0.0`:</span><span class="sxs-lookup"><span data-stu-id="10d48-240">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="10d48-241">いることを確認、`<UserSecretsId>`プロパティは、アプリのプロジェクト ファイルに存在する場所`{GUID}`はユーザーが指定した GUID です。</span><span class="sxs-lookup"><span data-stu-id="10d48-241">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="10d48-242">ローカルで次のシークレットを保存、 [Secret Manager ツール](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="10d48-242">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="10d48-243">シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。</span><span class="sxs-lookup"><span data-stu-id="10d48-243">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="10d48-244">アプリを実行すると、key vault のシークレットが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="10d48-244">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="10d48-245">文字列のシークレットを`5000-AppSecret`がアプリのプロジェクト ファイルで指定されたアプリのバージョンに一致 (`5.0.0.0`)。</span><span class="sxs-lookup"><span data-stu-id="10d48-245">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="10d48-246">バージョン、 `5000` (dash) には、キー名から取り除かれます。</span><span class="sxs-lookup"><span data-stu-id="10d48-246">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="10d48-247">キーを使用して構成を読み取って、アプリ全体にわたって`AppSecret`シークレット値を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="10d48-247">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="10d48-248">プロジェクト ファイルで、アプリのバージョンが変更されるかどうか`5.1.0.0`シークレットに返される値は、アプリをもう一度実行`5.1.0.0_secret_value_dev`開発環境でと`5.1.0.0_secret_value_prod`実稼働環境でします。</span><span class="sxs-lookup"><span data-stu-id="10d48-248">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="10d48-249">独自に提供することも`KeyVaultClient`実装`AddAzureKeyVault`します。</span><span class="sxs-lookup"><span data-stu-id="10d48-249">You can also provide your own `KeyVaultClient` implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="10d48-250">カスタムのクライアントでは、アプリ間でのクライアントの 1 つのインスタンスの共有を許可します。</span><span class="sxs-lookup"><span data-stu-id="10d48-250">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="10d48-251">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="10d48-251">Bind an array to a class</span></span>

<span data-ttu-id="10d48-252">プロバイダーは、配列、POCO 配列にバインドするために構成値を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="10d48-252">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="10d48-253">キーをコロンを含めることができる構成ソースから読み取るときに (`:`) 配列を構成するキーを区別するために、数値キー セグメントの区切り記号が使用される (`:0:`、 `:1:`,…</span><span class="sxs-lookup"><span data-stu-id="10d48-253">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="10d48-254">`:{n}:`)</span><span class="sxs-lookup"><span data-stu-id="10d48-254">`:{n}:`).</span></span> <span data-ttu-id="10d48-255">詳細については、次を参照してください。[構成。クラスに配列をバインド](xref:fundamentals/configuration/index#bind-an-array-to-a-class)します。</span><span class="sxs-lookup"><span data-stu-id="10d48-255">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="10d48-256">Azure Key Vault のキーは、区切り記号としてコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="10d48-256">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="10d48-257">このトピックで説明されているアプローチは、二重ハイフンを使用 (`--`) (セクション) の階層値の区切り記号として。</span><span class="sxs-lookup"><span data-stu-id="10d48-257">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="10d48-258">ダッシュと数字キー セグメント倍精度浮動小数点で配列のキーが Azure Key Vault に格納されている (`--0--`、 `--1--`、 &hellip; `--{n}--`)。</span><span class="sxs-lookup"><span data-stu-id="10d48-258">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="10d48-259">次の確認[Serilog](https://serilog.net/)ログ プロバイダーの構成 JSON ファイルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="10d48-259">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="10d48-260">2 つのオブジェクトで定義されているリテラルは、`WriteTo`配列 2 つの Serilog を反映する*シンク*、ログ出力の変換先を記述します。</span><span class="sxs-lookup"><span data-stu-id="10d48-260">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="10d48-261">上記の JSON ファイルに示すように構成が二重ダッシュを使用して Azure Key Vault に格納されている (`--`) 表記と数値のセグメント。</span><span class="sxs-lookup"><span data-stu-id="10d48-261">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="10d48-262">キー</span><span class="sxs-lookup"><span data-stu-id="10d48-262">Key</span></span> | <span data-ttu-id="10d48-263">[値]</span><span class="sxs-lookup"><span data-stu-id="10d48-263">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="10d48-264">シークレットを再読み込み</span><span class="sxs-lookup"><span data-stu-id="10d48-264">Reload secrets</span></span>

<span data-ttu-id="10d48-265">シークレットはまでキャッシュ`IConfigurationRoot.Reload()`が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="10d48-265">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="10d48-266">期限切れ、無効にし、まで、アプリで、key vault にシークレットが更新されたが守られていない`Reload`を実行します。</span><span class="sxs-lookup"><span data-stu-id="10d48-266">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="10d48-267">無効になっており、期限切れのシークレット</span><span class="sxs-lookup"><span data-stu-id="10d48-267">Disabled and expired secrets</span></span>

<span data-ttu-id="10d48-268">無効になっており、期限切れのシークレットのスロー、`KeyVaultClientException`します。</span><span class="sxs-lookup"><span data-stu-id="10d48-268">Disabled and expired secrets throw a `KeyVaultClientException`.</span></span> <span data-ttu-id="10d48-269">アプリがスローされることを防ぐために、アプリの代わりにまたは、無効/有効期限切れのシークレットを更新します。</span><span class="sxs-lookup"><span data-stu-id="10d48-269">To prevent your app from throwing, replace your app or update the disabled/expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="10d48-270">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="10d48-270">Troubleshoot</span></span>

<span data-ttu-id="10d48-271">アプリは、プロバイダーを使用して構成の読み込みに失敗した場合、エラー メッセージが書き込む、 [ASP.NET Core のログ記録インフラストラクチャ](xref:fundamentals/logging/index)します。</span><span class="sxs-lookup"><span data-stu-id="10d48-271">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="10d48-272">次の条件は、読み込みを構成できないようにします。</span><span class="sxs-lookup"><span data-stu-id="10d48-272">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="10d48-273">アプリまたは証明書は Azure Active Directory で正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="10d48-273">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="10d48-274">Key vault は、Azure Key Vault に存在しません。</span><span class="sxs-lookup"><span data-stu-id="10d48-274">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="10d48-275">アプリは、キー コンテナーにアクセスする権限はありません。</span><span class="sxs-lookup"><span data-stu-id="10d48-275">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="10d48-276">アクセス ポリシーが含まれていない`Get`と`List`アクセス許可。</span><span class="sxs-lookup"><span data-stu-id="10d48-276">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="10d48-277">Key vault で構成データ (名前と値のペア) が正しくという名前のないがない、有効期限が切れたか、無効にします。</span><span class="sxs-lookup"><span data-stu-id="10d48-277">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="10d48-278">アプリが正しくない key vault の名前 (`KeyVaultName`)、Azure AD アプリケーション Id (`AzureADApplicationId`)、または Azure AD 証明書の拇印 (`AzureADCertThumbprint`)。</span><span class="sxs-lookup"><span data-stu-id="10d48-278">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="10d48-279">構成キー (名) は、ロードしようとしている値用のアプリで正しくないです。</span><span class="sxs-lookup"><span data-stu-id="10d48-279">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10d48-280">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="10d48-280">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="10d48-281">Microsoft Azure:Key Vault</span><span class="sxs-lookup"><span data-stu-id="10d48-281">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="10d48-282">Microsoft Azure:Key Vault のドキュメント</span><span class="sxs-lookup"><span data-stu-id="10d48-282">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="10d48-283">Azure Key Vault のキーを生成し、HSM で保護された転送する方法</span><span class="sxs-lookup"><span data-stu-id="10d48-283">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="10d48-284">KeyVaultClient クラス</span><span class="sxs-lookup"><span data-stu-id="10d48-284">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="10d48-285">クイック スタート:設定して、.NET web アプリを使用して Azure Key Vault からシークレットを取得</span><span class="sxs-lookup"><span data-stu-id="10d48-285">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="10d48-286">チュートリアル: Azure Windows 仮想マシンを使用した .NET で Azure Key Vault を使用する方法</span><span class="sxs-lookup"><span data-stu-id="10d48-286">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
