---
title: ASP.NET Core の認証サンプル
author: rick-anderson
description: ASP.NET Core リポジトリの認証サンプルへのリンクを提供します。
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: d49aef198e926d88f1a6727f84b06f0861c8812d
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187300"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET Core の認証サンプル

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[ASP.NET Core リポジトリ](https://github.com/aspnet/AspNetCore)の*AspNetCore/src/Security/samples*フォルダーには次の通り認証についてのサンプルが含まれています 。

* [要求の変換](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [Cookie 認証](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [カスタムポリシープロバイダー-IAuthorizationPolicyProvider](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [動的な認証スキームとオプション](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [外部クレーム](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [要求に基づくCookieと別の認証スキームの選択](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [静的ファイルへのアクセス制限](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>サンプルの実行

* [ブランチ](https://github.com/aspnet/AspNetCore)を選択します。 例、`Tag:v3.0.0`
* [ASP.NET Core リポジトリ](https://github.com/aspnet/AspNetCore)を複製またはダウンロードします。
* 複製したASP.NET Core リポジトリと一致するバージョンの[.NET Core SDK](https://www.microsoft.com/net/download/all)がインストールされていることを確認します。
* *AspNetCore/src/Security/samples*以下のサンプルに移動し、`dotnet run`でサンプルを実行します。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ASP.NET Core リポジトリ](https://github.com/aspnet/AspNetCore)の*AspNetCore/src/Security/samples*フォルダーには次の通り認証についてのサンプルが含まれています 。

* [要求の変換](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Cookie 認証](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [カスタムポリシープロバイダー-IAuthorizationPolicyProvider](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [動的な認証スキームとオプション](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [外部クレーム](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [要求に基づくCookieと別の認証スキームの選択](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [静的ファイルへのアクセス制限](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>サンプルの実行

* [ブランチ](https://github.com/aspnet/AspNetCore)を選択します。 例、`release/2.2`
* [ASP.NET Core リポジトリ](https://github.com/aspnet/AspNetCore)を複製またはダウンロードします。
* 複製したASP.NET Core リポジトリと一致するバージョンの[.NET Core SDK](https://www.microsoft.com/net/download/all)がインストールされていることを確認します。
* *AspNetCore/src/Security/samples*以下のサンプルに移動し、`dotnet run`でサンプルを実行します。

::: moniker-end
