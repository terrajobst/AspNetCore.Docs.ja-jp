---
title: ツールとダウンロード - ASP.NET Core および Azure を使用した DevOps
author: CamSoper
description: ASP.NET Core および Azure を使用した DevOps に必要なツールとダウンロード。
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 9c1042dd48b9167209b46e97a09e011b80e2609c
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511146"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="6b845-103">ツールとダウンロード</span><span class="sxs-lookup"><span data-stu-id="6b845-103">Tools and downloads</span></span>

<span data-ttu-id="6b845-104">Azure には、[Azure portal](https://portal.azure.com)、[Azure CLI](/cli/azure/)、[Azure PowerShell](/powershell/azure/overview)、[Azure Cloud Shell](https://shell.azure.com/bash)、Visual Studio などのリソースをプロビジョニングおよび管理するためのインターフェイスがいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="6b845-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="6b845-105">このガイドでは最小限のアプローチを採用しており、必要な手順を減らすために、可能な限り Azure Cloud Shell を使用します。</span><span class="sxs-lookup"><span data-stu-id="6b845-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="6b845-106">ただし、Azure portal を使用する必要がある部分もいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="6b845-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6b845-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6b845-107">Prerequisites</span></span>

<span data-ttu-id="6b845-108">次のサブスクリプションが必要です。</span><span class="sxs-lookup"><span data-stu-id="6b845-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="6b845-109">Azure &mdash; アカウントをお持ちでない場合は、[無料試用版を入手](https://azure.microsoft.com/free/)してください。</span><span class="sxs-lookup"><span data-stu-id="6b845-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="6b845-110">Azure DevOps Services &mdash; Azure DevOps サブスクリプションと組織は、第 4 章で作成されます。</span><span class="sxs-lookup"><span data-stu-id="6b845-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="6b845-111">GitHub &mdash; アカウントをお持ちでない場合は、[無料でサインアップ](https://github.com/join)できます。</span><span class="sxs-lookup"><span data-stu-id="6b845-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="6b845-112">次のツールが必要です。</span><span class="sxs-lookup"><span data-stu-id="6b845-112">The following tools are required:</span></span>

* <span data-ttu-id="6b845-113">[Git](https://git-scm.com/downloads) &mdash; このガイドを対象とした Git についての基本的な理解。</span><span class="sxs-lookup"><span data-stu-id="6b845-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="6b845-114">[Git のドキュメント](https://git-scm.com/doc) (具体的には、[git remote](https://git-scm.com/docs/git-remote) と [git push](https://git-scm.com/docs/git-push)) を確認してください。</span><span class="sxs-lookup"><span data-stu-id="6b845-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="6b845-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; サンプル アプリをビルドして実行するには、バージョン 2.1.300 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="6b845-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="6b845-116">**.NET Core クロスプラットフォームの開発**ワークロードを使用して Visual Studio がインストールされている場合は、.NET Core SDK は既にインストールされています。</span><span class="sxs-lookup"><span data-stu-id="6b845-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="6b845-117">.NET Core SDK のインストールを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b845-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="6b845-118">コマンド シェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b845-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="6b845-119">推奨されるツール (Windows のみ)</span><span class="sxs-lookup"><span data-stu-id="6b845-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="6b845-120">[Visual Studio](https://visualstudio.microsoft.com) の堅牢な Azure ツールにより、このガイドで説明されているほとんどの機能に GUI が提供されます。</span><span class="sxs-lookup"><span data-stu-id="6b845-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="6b845-121">Visual Studio の任意のエディション (無償の Visual Studio Community Edition を含む) が動作します。</span><span class="sxs-lookup"><span data-stu-id="6b845-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="6b845-122">チュートリアルは、開発、デプロイ、および DevOps を示すため、Visual Studio を使用した場合としない場合の両方で書かれています。</span><span class="sxs-lookup"><span data-stu-id="6b845-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="6b845-123">Visual Studio に次の[ワークロード](/visualstudio/install/modify-visual-studio)がインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b845-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="6b845-124">ASP.NET と Web 開発</span><span class="sxs-lookup"><span data-stu-id="6b845-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="6b845-125">Azure の開発</span><span class="sxs-lookup"><span data-stu-id="6b845-125">Azure development</span></span>
  * <span data-ttu-id="6b845-126">.NET Core クロスプラットフォームの開発</span><span class="sxs-lookup"><span data-stu-id="6b845-126">.NET Core cross-platform development</span></span>
