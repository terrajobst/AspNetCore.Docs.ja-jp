---
title: ASP.NET Core の構成プロバイダーの Azure Key Vault
author: guardrex
description: Azure Key Vault 構成プロバイダーを使用して、実行時に読み込まれる名前と値のペアを使用してアプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/01/2019
uid: security/key-vault-configuration
ms.openlocfilehash: 0d0b6e20a1901d4a2630ce263b5fd0cd7bcca8fe
ms.sourcegitcommit: 4fe3ae892f54dc540859bff78741a28c2daa9a38
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2019
ms.locfileid: "68776651"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="adc65-103">ASP.NET Core の構成プロバイダーの Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="adc65-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="adc65-104">作成者 [Luke Latham](https://github.com/guardrex)および [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="adc65-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="adc65-105">このドキュメントでは、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)構成プロバイダーを使用して Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="adc65-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="adc65-106">Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。</span><span class="sxs-lookup"><span data-stu-id="adc65-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="adc65-107">ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="adc65-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="adc65-108">機密性の高い構成データへのアクセスを制御する。</span><span class="sxs-lookup"><span data-stu-id="adc65-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="adc65-109">構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。</span><span class="sxs-lookup"><span data-stu-id="adc65-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="adc65-110">このシナリオは ASP.NET Core 2.1 以降を対象とするアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="adc65-110">This scenario is available for apps that target ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="adc65-111">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="adc65-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="adc65-112">パッケージ</span><span class="sxs-lookup"><span data-stu-id="adc65-112">Packages</span></span>

<span data-ttu-id="adc65-113">Azure Key Vault 構成プロバイダーを使用するには、パッケージへの参照を[Microsoft. extension. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)パッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="adc65-113">To use the Azure Key Vault Configuration Provider, add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

<span data-ttu-id="adc65-114">[Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)のシナリオを採用するには、[Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) にパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="adc65-114">To adopt the [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) scenario, add a package reference to the [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) package.</span></span>

> [!NOTE]
> <span data-ttu-id="adc65-115">このドキュメントの作成時点では、の最新の`Microsoft.Azure.Services.AppAuthentication`安定し`1.0.3`たリリースであるバージョンで、[システムによって割り当てられたマネージド id](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="adc65-115">At the time of writing, the latest stable release of `Microsoft.Azure.Services.AppAuthentication`, version `1.0.3`, provides support for [system-assigned managed identities](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work).</span></span> <span data-ttu-id="adc65-116">*ユーザー割り当てのマネージド id*のサポートは、 `1.2.0-preview2`パッケージで利用できます。</span><span class="sxs-lookup"><span data-stu-id="adc65-116">Support for *user-assigned managed identities* is available in the `1.2.0-preview2` package.</span></span> <span data-ttu-id="adc65-117">このトピックでは、システム管理の id の使用方法を示します。提供さ`1.0.3`れるサンプル`Microsoft.Azure.Services.AppAuthentication`アプリでは、パッケージのバージョンを使用します。</span><span class="sxs-lookup"><span data-stu-id="adc65-117">This topic demonstrates the use of system-managed identities, and the provided sample app uses version `1.0.3` of the `Microsoft.Azure.Services.AppAuthentication` package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="adc65-118">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="adc65-118">Sample app</span></span>

<span data-ttu-id="adc65-119">サンプルアプリは、 `#define` *Program.cs*ファイルの先頭にあるステートメントによって決定される2つのモードのいずれかで実行されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-119">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="adc65-120">`Certificate`&ndash; Azure Key Vault に格納されているシークレットにアクセスするために Azure Key Vault クライアント ID と x.509 証明書を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="adc65-120">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="adc65-121">このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。</span><span class="sxs-lookup"><span data-stu-id="adc65-121">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="adc65-122">`Managed`アプリのコードまたは構成に資格情報が格納されていない Azure AD 認証を使用して Azure Key Vault するために、 [Azure リソースのマネージド id](/azure/active-directory/managed-identities-azure-resources/overview)を使用してアプリを認証する方法について説明します。 &ndash;</span><span class="sxs-lookup"><span data-stu-id="adc65-122">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="adc65-123">マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="adc65-123">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="adc65-124">サンプル`Managed`のバージョンは、Azure にデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="adc65-124">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="adc65-125">「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="adc65-125">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="adc65-126">プリプロセッサディレクティブ (`#define`) を使用してサンプルアプリケーションを構成する方法の詳細については、「」を参照<xref:index#preprocessor-directives-in-sample-code>してください。</span><span class="sxs-lookup"><span data-stu-id="adc65-126">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="adc65-127">開発環境でのシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="adc65-127">Secret storage in the Development environment</span></span>

<span data-ttu-id="adc65-128">[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="adc65-128">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="adc65-129">開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルシークレットマネージャーストアから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="adc65-129">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="adc65-130">Secret Manager ツールでは、 `<UserSecretsId>`アプリのプロジェクトファイルにプロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="adc65-130">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="adc65-131">プロパティ値 (`{GUID}`) を任意の一意の GUID に設定します。</span><span class="sxs-lookup"><span data-stu-id="adc65-131">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="adc65-132">シークレットは、名前と値のペアとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-132">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="adc65-133">階層値 (構成セクション) では`:` 、 [ASP.NET Core 構成](xref:fundamentals/configuration/index)キー名の区切り記号として (コロン) を使用します。</span><span class="sxs-lookup"><span data-stu-id="adc65-133">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="adc65-134">シークレットマネージャーは、プロジェクトのコンテンツルートに開かれたコマンドシェルから使用され`{SECRET NAME}`ます。ここで`{SECRET VALUE}` 、は名前、は値です。</span><span class="sxs-lookup"><span data-stu-id="adc65-134">The Secret Manager is used from a command shell opened to the project's content root, where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="adc65-135">プロジェクトのコンテンツルートからコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="adc65-135">Execute the following commands in a command shell from the project's content root to set the secrets for the sample app:</span></span>

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="adc65-136">これらのシークレットが Azure Key Vault セクションを[含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)の Azure Key Vault に格納されて`_dev`いる場合、サフィックス`_prod`はに変更されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-136">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="adc65-137">サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。</span><span class="sxs-lookup"><span data-stu-id="adc65-137">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="adc65-138">Azure Key Vault を使用した運用環境のシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="adc65-138">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="adc65-139">[クイックスタートで説明されている手順は次のとおりです。Azure CLI](/azure/key-vault/quick-create-cli)トピックを使用して Azure Key Vault からシークレットを設定および取得する方法については、「Azure Key Vault を作成する」および「サンプルアプリで使用するシークレットの保存」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="adc65-139">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="adc65-140">詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="adc65-140">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="adc65-141">[Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="adc65-141">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="adc65-142">コードブロックの右上隅にある **[試してみる]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="adc65-142">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="adc65-143">テキストボックス内の検索文字列 "Azure CLI" を使用します。</span><span class="sxs-lookup"><span data-stu-id="adc65-143">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="adc65-144">**[Cloud Shell の起動]** ボタンを使用して、ブラウザーで Cloud Shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="adc65-144">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="adc65-145">Azure portal の右上隅にあるメニューの **[Cloud Shell]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="adc65-145">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="adc65-146">詳細については、「 [Azure コマンドラインインターフェイス (CLI)](/cli/azure/) 」および「 [Azure Cloud Shell の概要](/azure/cloud-shell/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="adc65-146">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="adc65-147">まだ認証されていない場合は、 `az login`コマンドを使用してサインインします。</span><span class="sxs-lookup"><span data-stu-id="adc65-147">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="adc65-148">次のコマンド`{RESOURCE GROUP NAME}`を使用してリソースグループを作成し`{LOCATION}`ます。は、新しいリソースグループのリソースグループ名で、は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="adc65-148">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="adc65-149">次のコマンドを使用して、リソースグループにキーコンテナーを`{KEY VAULT NAME}`作成します。ここで、は新しい`{LOCATION}` key vault の名前、は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="adc65-149">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="adc65-150">名前と値のペアとして、キーコンテナーにシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="adc65-150">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="adc65-151">Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-151">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="adc65-152">階層値 (構成セクション) は`--` 、区切り記号として (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="adc65-152">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="adc65-153">[ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。</span><span class="sxs-lookup"><span data-stu-id="adc65-153">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="adc65-154">そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。</span><span class="sxs-lookup"><span data-stu-id="adc65-154">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="adc65-155">サンプルアプリで使用するシークレットは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="adc65-155">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="adc65-156">値には、 `_prod`開発環境で読み込まれた`_dev`サフィックス値とユーザーシークレットから区別するためのサフィックスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="adc65-156">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="adc65-157">は`{KEY VAULT NAME}` 、前の手順で作成したキーコンテナーの名前に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="adc65-157">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="adc65-158">Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する</span><span class="sxs-lookup"><span data-stu-id="adc65-158">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="adc65-159">**アプリが Azure の外部でホストされている場合**に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="adc65-159">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="adc65-160">詳細については、「[キー、シークレット、および証明書について](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="adc65-160">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="adc65-161">Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="adc65-161">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="adc65-162">マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="adc65-162">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="adc65-163">このサンプルアプリでは、 `#define` *Program.cs*ファイルの先頭にあるステートメントがに`Certificate`設定されている場合に、アプリケーション ID と x.509 証明書を使用します。</span><span class="sxs-lookup"><span data-stu-id="adc65-163">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="adc65-164">PKCS # 12 アーカイブ ( *.pfx*) 証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="adc65-164">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="adc65-165">証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。</span><span class="sxs-lookup"><span data-stu-id="adc65-165">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="adc65-166">現在のユーザーの個人証明書ストアに証明書をインストールします。</span><span class="sxs-lookup"><span data-stu-id="adc65-166">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="adc65-167">キーをエクスポート可能としてマークすることはオプションです。</span><span class="sxs-lookup"><span data-stu-id="adc65-167">Marking the key as exportable is optional.</span></span> <span data-ttu-id="adc65-168">このプロセスの後半で使用される証明書の拇印をメモしておきます。</span><span class="sxs-lookup"><span data-stu-id="adc65-168">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="adc65-169">PKCS # 12 アーカイブ ( *.pfx*) 証明書を DER でエンコードされた証明書 ( *.cer*) としてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="adc65-169">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="adc65-170">アプリを Azure AD (**アプリの登録**) に登録します。</span><span class="sxs-lookup"><span data-stu-id="adc65-170">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="adc65-171">DER でエンコードされた証明書 ( *.cer*) を Azure AD にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="adc65-171">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="adc65-172">Azure AD でアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="adc65-172">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="adc65-173">**[証明書 & シークレット]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="adc65-173">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="adc65-174">公開キーを含む証明書をアップロードするには、 **[証明書のアップロード]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="adc65-174">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="adc65-175">*.Cer*、 *pem*、または *.crt*証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="adc65-175">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="adc65-176">Key vault 名、アプリケーション ID、および証明書の拇印をアプリの*appsettings*ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="adc65-176">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="adc65-177">Azure portal の**キーコンテナー**に移動します。</span><span class="sxs-lookup"><span data-stu-id="adc65-177">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="adc65-178">Azure Key Vault セクションで、[運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)に作成したキーコンテナーを選択します。</span><span class="sxs-lookup"><span data-stu-id="adc65-178">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="adc65-179">**[アクセスポリシー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="adc65-179">Select **Access policies**.</span></span>
1. <span data-ttu-id="adc65-180">**[新規追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="adc65-180">Select **Add new**.</span></span>
1. <span data-ttu-id="adc65-181">**[プリンシパルの選択]** を選択し、名前で登録済みのアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="adc65-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="adc65-182">**[選択]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="adc65-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="adc65-183">**[シークレットのアクセス許可]** を開き、アプリに**Get**および**List**アクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="adc65-183">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="adc65-184">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="adc65-184">Select **OK**.</span></span>
1. <span data-ttu-id="adc65-185">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="adc65-185">Select **Save**.</span></span>
1. <span data-ttu-id="adc65-186">アプリをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="adc65-186">Deploy the app.</span></span>

<span data-ttu-id="adc65-187">サンプル`Certificate`アプリでは、シークレット名と`IConfigurationRoot`同じ名前を使用してから構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="adc65-187">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="adc65-188">非階層値:の`SecretName`値は、を使用`config["SecretName"]`して取得されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-188">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="adc65-189">階層値 (セクション):( `:`コロン) 表記`GetSection`または拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="adc65-189">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="adc65-190">次のいずれかの方法を使用して、構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="adc65-190">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="adc65-191">X.509 証明書は OS によって管理されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-191">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="adc65-192">アプリは、 `AddAzureKeyVault` *appsettings*ファイルによって指定された値を使用してを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="adc65-192">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="adc65-193">値の例:</span><span class="sxs-lookup"><span data-stu-id="adc65-193">Example values:</span></span>

* <span data-ttu-id="adc65-194">Key vault 名:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="adc65-194">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="adc65-195">アプリケーション ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="adc65-195">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="adc65-196">証明書の拇印:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="adc65-196">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="adc65-197">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="adc65-197">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="adc65-198">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-198">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="adc65-199">開発環境では、シークレット値は`_dev`サフィックス付きで読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="adc65-199">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="adc65-200">運用環境では、値はという`_prod`サフィックスで読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="adc65-200">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="adc65-201">Azure リソースの管理対象 id を使用する</span><span class="sxs-lookup"><span data-stu-id="adc65-201">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="adc65-202">**Azure にデプロイされたアプリ**は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、資格情報のない AZURE AD 認証 (アプリケーション ID とパスワード/クライアントシークレット) を使用して、アプリが Azure Key Vault で認証できるようになります。アプリに保存されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-202">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="adc65-203">このサンプルアプリでは、 *Program.cs*ファイルの先頭に`#define`あるステートメントがに`Managed`設定されている場合に、Azure リソースの管理対象 id を使用します。</span><span class="sxs-lookup"><span data-stu-id="adc65-203">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="adc65-204">アプリの*appsettings*ファイルにコンテナー名を入力します。</span><span class="sxs-lookup"><span data-stu-id="adc65-204">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="adc65-205">このサンプルアプリでは、 `Managed`バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません。そのため、これらの構成エントリは無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="adc65-205">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="adc65-206">アプリが Azure にデプロイされ、Azure は、 *appsettings*ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証します。</span><span class="sxs-lookup"><span data-stu-id="adc65-206">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="adc65-207">サンプルアプリを Azure App Service にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="adc65-207">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="adc65-208">Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-208">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="adc65-209">次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="adc65-209">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="adc65-210">オブジェクト ID は、App Service の **[id]** パネルの Azure portal に表示されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-210">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="adc65-211">Azure CLI とアプリのオブジェクト ID を使用して、キーコンテナー `list`に`get`アクセスするためのアクセス許可とアクセス許可をアプリに付与します。</span><span class="sxs-lookup"><span data-stu-id="adc65-211">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="adc65-212">Azure CLI、PowerShell、または Azure portal を使用して**アプリを再起動**します。</span><span class="sxs-lookup"><span data-stu-id="adc65-212">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="adc65-213">サンプルアプリ:</span><span class="sxs-lookup"><span data-stu-id="adc65-213">The sample app:</span></span>

* <span data-ttu-id="adc65-214">接続文字列を指定せ`AzureServiceTokenProvider`ずに、クラスのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="adc65-214">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="adc65-215">接続文字列が指定されていない場合、プロバイダーは Azure リソースの管理対象 id からアクセストークンを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="adc65-215">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="adc65-216">インスタンストークン`KeyVaultClient`のコールバックを使用して、新しいが作成されます。 `AzureServiceTokenProvider`</span><span class="sxs-lookup"><span data-stu-id="adc65-216">A new `KeyVaultClient` is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="adc65-217">インスタンスは、の既定の実装で使用`IKeyVaultSecretManager`されます。この実装では、すべての`--`シークレット値が読み`:`込まれ、二重ダッシュ () がキー名のコロン () に置き換えられます。 `KeyVaultClient`</span><span class="sxs-lookup"><span data-stu-id="adc65-217">The `KeyVaultClient` instance is used with a default implementation of `IKeyVaultSecretManager` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="adc65-218">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-218">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="adc65-219">開発環境では、シークレット値はユーザー `_dev`シークレットによって提供されるため、サフィックスが付いています。</span><span class="sxs-lookup"><span data-stu-id="adc65-219">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="adc65-220">運用環境では、値は Azure Key Vault によっ`_prod`て提供されるため、サフィックス付きで読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="adc65-220">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="adc65-221">`Access denied`エラーが発生した場合は、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="adc65-221">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="adc65-222">Azure でサービスを再起動したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="adc65-222">Confirm that you've restarted the service in Azure.</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="adc65-223">キー名のプレフィックスを使用する</span><span class="sxs-lookup"><span data-stu-id="adc65-223">Use a key name prefix</span></span>

<span data-ttu-id="adc65-224">`AddAzureKeyVault`の実装を受け入れるオーバーロードを提供し`IKeyVaultSecretManager`ます。これにより、キーコンテナーのシークレットを構成キーに変換する方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="adc65-224">`AddAzureKeyVault` provides an overload that accepts an implementation of `IKeyVaultSecretManager`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="adc65-225">たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。</span><span class="sxs-lookup"><span data-stu-id="adc65-225">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="adc65-226">これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="adc65-226">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="adc65-227">Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 (*開発*、*運用*シークレットなど) を同じコンテナーに配置したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="adc65-227">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="adc65-228">さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="adc65-228">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="adc65-229">次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) `5000-AppSecret`でシークレットが確立されています (キーコンテナーのシークレット名ではピリオドは使用できません)。</span><span class="sxs-lookup"><span data-stu-id="adc65-229">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="adc65-230">このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。</span><span class="sxs-lookup"><span data-stu-id="adc65-230">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="adc65-231">アプリの別のバージョンでは、5.1.0.0 は、キーコンテナーに (およびシークレットマネージャーツールを使用して) `5100-AppSecret`シークレットを追加します。</span><span class="sxs-lookup"><span data-stu-id="adc65-231">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="adc65-232">各アプリバージョンは、バージョン管理されたシークレット値`AppSecret`をとして構成に読み込みます。これにより、シークレットが読み込まれるときにバージョンが除去されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-232">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="adc65-233">`AddAzureKeyVault`は、カスタム`IKeyVaultSecretManager`のを使用して呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-233">`AddAzureKeyVault` is called with a custom `IKeyVaultSecretManager`:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?highlight=30-34)]

<span data-ttu-id="adc65-234">実装`IKeyVaultSecretManager`によって、シークレットのバージョンプレフィックスに反応し、適切なシークレットが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="adc65-234">The `IKeyVaultSecretManager` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

<span data-ttu-id="adc65-235">この`Load`メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを検索するプロバイダーアルゴリズムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-235">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="adc65-236">バージョンプレフィックスがで`Load`見つかった場合、アルゴリズムは、 `GetKey`メソッドを使用してシークレット名の構成名を返します。</span><span class="sxs-lookup"><span data-stu-id="adc65-236">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="adc65-237">シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。</span><span class="sxs-lookup"><span data-stu-id="adc65-237">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="adc65-238">このアプローチが実装されている場合:</span><span class="sxs-lookup"><span data-stu-id="adc65-238">When this approach is implemented:</span></span>

1. <span data-ttu-id="adc65-239">アプリのプロジェクトファイルで指定されているアプリのバージョン。</span><span class="sxs-lookup"><span data-stu-id="adc65-239">The app's version specified in the app's project file.</span></span> <span data-ttu-id="adc65-240">次の例では、アプリのバージョンがに`5.0.0.0`設定されています。</span><span class="sxs-lookup"><span data-stu-id="adc65-240">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="adc65-241">アプリケーションのプロジェクト`<UserSecretsId>`ファイルにプロパティが存在することを確認します`{GUID}` 。ここで、はユーザー指定の GUID です。</span><span class="sxs-lookup"><span data-stu-id="adc65-241">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="adc65-242">[シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。</span><span class="sxs-lookup"><span data-stu-id="adc65-242">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="adc65-243">シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-243">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="adc65-244">アプリを実行すると、key vault シークレットが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="adc65-244">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="adc65-245">の`5000-AppSecret`文字列シークレットは、アプリのプロジェクトファイル (`5.0.0.0`) で指定されているアプリのバージョンと一致します。</span><span class="sxs-lookup"><span data-stu-id="adc65-245">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="adc65-246">バージョン`5000` (ダッシュ付き) は、キー名から削除されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-246">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="adc65-247">アプリ全体で、キー `AppSecret`を使用して構成を読み取ると、シークレットの値が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="adc65-247">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="adc65-248">アプリのバージョンがプロジェクトファイルでに`5.1.0.0`変更され、アプリが再度実行された場合、返されるシークレット値は`5.1.0.0_secret_value_dev`開発環境および`5.1.0.0_secret_value_prod`運用環境にあります。</span><span class="sxs-lookup"><span data-stu-id="adc65-248">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="adc65-249">また、に`KeyVaultClient` `AddAzureKeyVault`独自の実装を提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="adc65-249">You can also provide your own `KeyVaultClient` implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="adc65-250">カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。</span><span class="sxs-lookup"><span data-stu-id="adc65-250">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="adc65-251">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="adc65-251">Bind an array to a class</span></span>

<span data-ttu-id="adc65-252">プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="adc65-252">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="adc65-253">キーにコロン (`:`) 区切り文字を含めることができる構成ソースから読み取る場合、配列を構成するキーを区別するために数値キーセグメント (`:0:`、 `:1:`、...</span><span class="sxs-lookup"><span data-stu-id="adc65-253">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="adc65-254">`:{n}:`)</span><span class="sxs-lookup"><span data-stu-id="adc65-254">`:{n}:`).</span></span> <span data-ttu-id="adc65-255">詳細については[、「構成:配列をクラス](xref:fundamentals/configuration/index#bind-an-array-to-a-class)にバインドします。</span><span class="sxs-lookup"><span data-stu-id="adc65-255">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="adc65-256">Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="adc65-256">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="adc65-257">このトピックで説明する方法では、階層`--`値 (セクション) の区切り記号として二重ダッシュ () を使用します。</span><span class="sxs-lookup"><span data-stu-id="adc65-257">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="adc65-258">配列キーは、2つのダッシュと数値のキーセグメント (`--0--`、 `--1--`、 &hellip; `--{n}--`) を使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-258">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="adc65-259">JSON ファイルによって提供される次の[Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="adc65-259">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="adc65-260">次の2つのオブジェクトリテラルが`WriteTo`配列に定義されています。この*シンク*には、ログ出力の宛先が記述されています。</span><span class="sxs-lookup"><span data-stu-id="adc65-260">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="adc65-261">前の JSON ファイルに示されている構成は、二重ダッシュ (`--`) 表記と数値セグメントを使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="adc65-261">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="adc65-262">キー</span><span class="sxs-lookup"><span data-stu-id="adc65-262">Key</span></span> | <span data-ttu-id="adc65-263">[値]</span><span class="sxs-lookup"><span data-stu-id="adc65-263">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="adc65-264">シークレットの再読み込み</span><span class="sxs-lookup"><span data-stu-id="adc65-264">Reload secrets</span></span>

<span data-ttu-id="adc65-265">シークレットは、が`IConfigurationRoot.Reload()`呼び出されるまでキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="adc65-265">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="adc65-266">が実行されるまで`Reload` 、key vault 内の有効期限切れ、無効、および更新されたシークレットは、アプリで尊重されません。</span><span class="sxs-lookup"><span data-stu-id="adc65-266">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="adc65-267">無効なシークレットと期限切れのシークレット</span><span class="sxs-lookup"><span data-stu-id="adc65-267">Disabled and expired secrets</span></span>

<span data-ttu-id="adc65-268">無効で有効期限が切れ`KeyVaultClientException`たシークレットは、実行時にをスローします。</span><span class="sxs-lookup"><span data-stu-id="adc65-268">Disabled and expired secrets throw a `KeyVaultClientException` at runtime.</span></span> <span data-ttu-id="adc65-269">アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。</span><span class="sxs-lookup"><span data-stu-id="adc65-269">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="adc65-270">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="adc65-270">Troubleshoot</span></span>

<span data-ttu-id="adc65-271">アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが[ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="adc65-271">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="adc65-272">次の状況では、構成を読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="adc65-272">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="adc65-273">Azure Active Directory で、アプリまたは証明書が正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="adc65-273">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="adc65-274">キーコンテナーは Azure Key Vault に存在しません。</span><span class="sxs-lookup"><span data-stu-id="adc65-274">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="adc65-275">アプリは、key vault にアクセスする権限がありません。</span><span class="sxs-lookup"><span data-stu-id="adc65-275">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="adc65-276">アクセスポリシーには、 `Get`と`List`のアクセス許可は含まれません。</span><span class="sxs-lookup"><span data-stu-id="adc65-276">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="adc65-277">Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。</span><span class="sxs-lookup"><span data-stu-id="adc65-277">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="adc65-278">アプリの key vault 名 (`KeyVaultName`)、Azure AD アプリケーション Id (`AzureADApplicationId`)、または Azure AD 証明書の拇印 (`AzureADCertThumbprint`) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="adc65-278">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="adc65-279">読み込みようとしている値の構成キー (名前) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="adc65-279">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="adc65-280">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="adc65-280">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="adc65-281">Microsoft Azure:Key Vault</span><span class="sxs-lookup"><span data-stu-id="adc65-281">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="adc65-282">Microsoft Azure:Key Vault のドキュメント</span><span class="sxs-lookup"><span data-stu-id="adc65-282">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="adc65-283">Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法</span><span class="sxs-lookup"><span data-stu-id="adc65-283">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="adc65-284">KeyVaultClient クラス</span><span class="sxs-lookup"><span data-stu-id="adc65-284">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="adc65-285">クイック スタート:.NET web アプリを使用して Azure Key Vault からシークレットを設定および取得する</span><span class="sxs-lookup"><span data-stu-id="adc65-285">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="adc65-286">チュートリアル: .NET で Azure Windows 仮想マシンで Azure Key Vault を使用する方法</span><span class="sxs-lookup"><span data-stu-id="adc65-286">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
