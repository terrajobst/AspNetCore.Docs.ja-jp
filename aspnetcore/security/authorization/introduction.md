---
title: ASP.NET Core での承認の概要
author: rick-anderson
description: 承認の基本と ASP.NET Core アプリでの承認のしくみについて説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/introduction
ms.openlocfilehash: b5e60b3c256941fff5e54e1a02e077c34c535902
ms.sourcegitcommit: 79850db9e79b1705b89f466c6f2c961ff15485de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75693870"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>ASP.NET Core での承認の概要

<a name="security-authorization-introduction"></a>

承認とは、ユーザーが何を実行できるかを決定するプロセスのことです。 例として、管理者ユーザーはドキュメントライブラリーの作成、ドキュメントの追加、ドキュメントの編集、およびそれらを削除することが許可されています。 管理者ではないユーザーは、ドキュメントを読む権限しかありません。

承認は直交性があり、認証から独立しています。 しかし、承認には認証のメカニズムが必須です。 認証は、ユーザーが誰であるかを確認するプロセスです。 認証は、現在のユーザーに対して1つ以上のIDを作成します。

ASP.NET Core での認証の詳細については、「<xref:security/authentication/index>」を参照してください。

## <a name="authorization-types"></a>承認の種類

ASP.NET Core の承認は、シンプルで宣言的な[ロールベース](xref:security/authorization/roles)と、リッチな[ポリシーベース](xref:security/authorization/policies)のモデルが提供されています。 承認は、要件の中で表され、ハンドラーが要件に対するユーザーのクレームを評価します。 命令的なチェックはシンプルなポリシー、または、ユーザーのIDとユーザーがアクセスしようとするリソースのプロパティの両方を評価するポリシーに基づくことができます。

## <a name="namespaces"></a>名前空間

`AuthorizeAttribute` および `AllowAnonymousAttribute` 属性を含む承認のコンポーネントは、 `Microsoft.AspNetCore.Authorization` 名前空間にあります。

[シンプルな承認](xref:security/authorization/simple)をご参照ください。
