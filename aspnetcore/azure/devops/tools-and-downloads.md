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
# <a name="tools-and-downloads"></a>ツールとダウンロード

Azure には、[Azure portal](https://portal.azure.com)、[Azure CLI](/cli/azure/)、[Azure PowerShell](/powershell/azure/overview)、[Azure Cloud Shell](https://shell.azure.com/bash)、Visual Studio などのリソースをプロビジョニングおよび管理するためのインターフェイスがいくつか用意されています。 このガイドでは最小限のアプローチを採用しており、必要な手順を減らすために、可能な限り Azure Cloud Shell を使用します。 ただし、Azure portal を使用する必要がある部分もいくつかあります。

## <a name="prerequisites"></a>必須コンポーネント

次のサブスクリプションが必要です。

* Azure &mdash; アカウントをお持ちでない場合は、[無料試用版を入手](https://azure.microsoft.com/free/)してください。
* Azure DevOps Services &mdash; Azure DevOps サブスクリプションと組織は、第 4 章で作成されます。
* GitHub &mdash; アカウントをお持ちでない場合は、[無料でサインアップ](https://github.com/join)できます。

次のツールが必要です。

* [Git](https://git-scm.com/downloads) &mdash; このガイドを対象とした Git についての基本的な理解。 [Git のドキュメント](https://git-scm.com/doc) (具体的には、[git remote](https://git-scm.com/docs/git-remote) と [git push](https://git-scm.com/docs/git-push)) を確認してください。
* [.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; サンプル アプリをビルドして実行するには、バージョン 2.1.300 以降が必要です。 **.NET Core クロスプラットフォームの開発**ワークロードを使用して Visual Studio がインストールされている場合は、.NET Core SDK は既にインストールされています。

    .NET Core SDK のインストールを確認します。 コマンド シェルを開き、次のコマンドを実行します。

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>推奨されるツール (Windows のみ)

* [Visual Studio](https://visualstudio.microsoft.com) の堅牢な Azure ツールにより、このガイドで説明されているほとんどの機能に GUI が提供されます。 Visual Studio の任意のエディション (無償の Visual Studio Community Edition を含む) が動作します。 チュートリアルは、開発、デプロイ、および DevOps を示すため、Visual Studio を使用した場合としない場合の両方で書かれています。

  Visual Studio に次の[ワークロード](/visualstudio/install/modify-visual-studio)がインストールされていることを確認します。

  * ASP.NET と Web 開発
  * Azure の開発
  * .NET Core クロスプラットフォームの開発
