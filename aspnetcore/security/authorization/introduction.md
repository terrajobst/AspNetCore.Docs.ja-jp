---
title: ASP.NET Core での承認の概要
author: rick-anderson
description: 承認の基本と ASP.NET Core アプリでの承認のしくみについて説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/introduction
ms.openlocfilehash: b5e60b3c256941fff5e54e1a02e077c34c535902
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652334"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>ASP.NET Core での承認の概要

<a name="security-authorization-introduction"></a>

承認とは、ユーザーが何を実行できるかを決定するプロセスのことです。 例として、管理者ユーザーはドキュメントライブラリーの作成、ドキュメントの追加、ドキュメントの編集、およびそれらを削除することが許可されています。 管理者ではないユーザーは、ドキュメントを読む権限しかありません。

承認は直交性があり、認証から独立しています。 しかし、承認には認証のメカニズムが必須です。 認証は、ユーザーが誰であるかを確認するプロセスです。 承認では、現在のユーザーのために 1 つ以上の ID を作成する場合があります。

ASP.NET Core での認証の詳細については、「<xref:security/authentication/index>」を参照してください。

## <a name="authorization-types"></a>承認の種類

ASP.NET Core 承認は、単純な宣言型の[役割](xref:security/authorization/roles)と豊富な[ポリシーベース](xref:security/authorization/policies)のモデルを提供します。 承認は、要件の中で表され、ハンドラーが要件に対するユーザーのクレームを評価します。 命令的なチェックはシンプルなポリシー、または、ユーザーのIDとユーザーがアクセスしようとするリソースのプロパティの両方を評価するポリシーに基づくことができます。

## <a name="namespaces"></a>名前空間

`AuthorizeAttribute` 属性と `AllowAnonymousAttribute` 属性を含む承認コンポーネントは、`Microsoft.AspNetCore.Authorization` 名前空間にあります。

[単純な承認](xref:security/authorization/simple)に関するドキュメントを参照してください。
