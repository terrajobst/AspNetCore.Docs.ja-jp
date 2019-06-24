---
title: ASP.NET Core での承認の概要
author: rick-anderson
description: 承認と ASP.NET Core アプリでの承認のしくみの基礎について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/introduction
ms.openlocfilehash: 5465eb7875ebecd77b628376ef886db0ddd05025
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896979"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>ASP.NET Core での承認の概要

<a name="security-authorization-introduction"></a>

承認とは、ユーザーが何を実行できるかを決定するプロセスのことです。例として、管理者ユーザーはドキュメントライブラリーの作成、ドキュメントの追加、ドキュメントの編集、およびそれらを削除することが許可されています。管理者ではないユーザーは、ドキュメントを読む権限しかありません。

承認は直交性があり、認証から独立しています。しかし、承認には認証のメカニズムが必須です。認証は、ユーザーが誰であるかを確認するプロセスです。認証は、現在のユーザーに対して1つ以上のIDを作成します。

## <a name="authorization-types"></a>承認の種類

ASP.NET Core の承認は、単純で宣言的な[ロールベース](xref:security/authorization/roles)と、リッチな[ポリシーベース](xref:security/authorization/policies)のモデルが提供されています。承認は、要件の中で表され、ハンドラーが要件に対するユーザーのクレームを評価します。命令的なチェックはシンプルなポリシー、または、ユーザーのIDとユーザーがアクセスしようとするリソースのプロパティの両方を評価するポリシーに基づくことができます。

## <a name="namespaces"></a>名前空間

`AuthorizeAttribute` および `AllowAnonymousAttribute` 属性を含む承認のコンポーネントは、 `Microsoft.AspNetCore.Authorization` 名前空間にあります。

[単純な承認](xref:security/authorization/simple)のドキュメントをご参照ください。
