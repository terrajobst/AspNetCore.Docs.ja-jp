---
title: ツールとダウンロード - ASP.NET Core および Azure を使用した DevOps
author: CamSoper
description: ツールおよび ASP.NET Core および Azure による開発運用に必要なダウンロード。
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 25ce311373b0aaddfa3bc2728c39e503acbca69d
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080635"
---
# <a name="tools-and-downloads"></a>ツールとダウンロード

Azure では、いくつかのインターフェイスのプロビジョニングおよびなどのリソースを管理、 [Azure portal](https://portal.azure.com)、 [Azure CLI](/cli/azure/)、 [Azure PowerShell](/powershell/azure/overview)、 [Azure クラウドシェル](https://shell.azure.com/bash)、および Visual Studio。 このガイドでは、必要最低限のアプローチを採用し、Azure Cloud Shell を減らすために必要な手順を実行可能な限りを使用します。 ただし、一部の Azure portal を使用する必要があります。

## <a name="prerequisites"></a>必須コンポーネント

次のサブスクリプションが必要です。

* Azure&mdash;アカウントを持っていない場合[無料試用版を入手](https://azure.microsoft.com/free/)します。
* Azure DevOps サービス&mdash;第 4 章で、DevOps の Azure サブスクリプションと組織を作成します。
* GitHub&mdash;アカウントを持っていない場合[無料でサインアップ](https://github.com/join)します。

次のツールが必要です。

* [Git](https://git-scm.com/downloads) &mdash;このガイドの Git の基本について理解をお勧めします。 レビュー、 [Git に関するドキュメント](https://git-scm.com/doc)、特に[git リモート](https://git-scm.com/docs/git-remote)と[git プッシュ](https://git-scm.com/docs/git-push)します。
* [.NET core SDK](https://www.microsoft.com/net/download/) &mdash; 2.1.300 のバージョンをビルドして、サンプル アプリの実行以降が必要です。 Visual Studio がインストールされている場合、 **.NET Core クロス プラットフォーム開発**ワークロードでは、.NET Core SDK が既にインストールされています。

    .NET Core SDK のインストールを確認します。 コマンド シェルを開き、次のコマンドを実行します。

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>推奨されるツール (Windows のみ)

* [Visual Studio](https://visualstudio.microsoft.com)堅牢な Azure ツール説明 GUI このガイドで説明されている機能のほとんどの。 無料の Visual Studio Community Edition を含む、Visual Studio の任意のエディションは機能します。 チュートリアルは、Visual Studio の有無にかかわらず、開発、デプロイ、DevOps のデモンストレーションに書き込まれます。

  Visual Studio が、次のことを確認します。[ワークロード](/visualstudio/install/modify-visual-studio)インストール。

  * ASP.NET と Web 開発
  * Azure の開発
  * .NET Core クロスプラットフォームの開発
