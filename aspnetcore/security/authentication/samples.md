---
title: ASP.NET Core の認証サンプル
author: rick-anderson
description: ASP.NET Core リポジトリの認証サンプルへのリンクを提供します。
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b013d02a65e752bbb61a87a0bf502785125378a2
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511614"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET Core の認証サンプル

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)には、 *AspNetCore/src/Security/samples*フォルダーに次の認証サンプルが含まれています。

* [要求の変換](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [Cookie 認証](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [カスタムポリシープロバイダー-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [動的な認証スキームとオプション](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [外部要求](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [要求に基づいて cookie と別の認証スキームのどちらを選択するか](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [静的ファイルへのアクセスを制限する](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>サンプルを実行する

* [ブランチ](https://github.com/dotnet/AspNetCore)を選択してください。 たとえば、`Tag:v3.0.0` のように指定します。
* [ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)を複製またはダウンロードします。
* ASP.NET Core リポジトリの複製に一致する[.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)バージョンがインストールされていることを確認します。
* *AspNetCore/src/Security/samples*のサンプルに移動し、`dotnet run`でサンプルを実行します。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)には、 *AspNetCore/src/Security/samples*フォルダーに次の認証サンプルが含まれています。

* [要求の変換](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Cookie 認証](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [カスタムポリシープロバイダー-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [動的な認証スキームとオプション](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [外部要求](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [要求に基づいて cookie と別の認証スキームのどちらを選択するか](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [静的ファイルへのアクセスを制限する](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>サンプルを実行する

* [ブランチ](https://github.com/dotnet/AspNetCore)を選択してください。 たとえば、`release/2.2` のように指定します。
* [ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)を複製またはダウンロードします。
* ASP.NET Core リポジトリの複製に一致する[.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)バージョンがインストールされていることを確認します。
* *AspNetCore/src/Security/samples*のサンプルに移動し、`dotnet run`でサンプルを実行します。

::: moniker-end
