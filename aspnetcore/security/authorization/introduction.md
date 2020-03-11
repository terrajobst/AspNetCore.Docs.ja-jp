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
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="f288a-103">ASP.NET Core での承認の概要</span><span class="sxs-lookup"><span data-stu-id="f288a-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="f288a-104">承認とは、ユーザーが何を実行できるかを決定するプロセスのことです。</span><span class="sxs-lookup"><span data-stu-id="f288a-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="f288a-105">例として、管理者ユーザーはドキュメントライブラリーの作成、ドキュメントの追加、ドキュメントの編集、およびそれらを削除することが許可されています。</span><span class="sxs-lookup"><span data-stu-id="f288a-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="f288a-106">管理者ではないユーザーは、ドキュメントを読む権限しかありません。</span><span class="sxs-lookup"><span data-stu-id="f288a-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="f288a-107">承認は直交性があり、認証から独立しています。</span><span class="sxs-lookup"><span data-stu-id="f288a-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="f288a-108">しかし、承認には認証のメカニズムが必須です。</span><span class="sxs-lookup"><span data-stu-id="f288a-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="f288a-109">認証は、ユーザーが誰であるかを確認するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="f288a-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="f288a-110">承認では、現在のユーザーのために 1 つ以上の ID を作成する場合があります。</span><span class="sxs-lookup"><span data-stu-id="f288a-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="f288a-111">ASP.NET Core での認証の詳細については、「<xref:security/authentication/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f288a-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="f288a-112">承認の種類</span><span class="sxs-lookup"><span data-stu-id="f288a-112">Authorization types</span></span>

<span data-ttu-id="f288a-113">ASP.NET Core 承認は、単純な宣言型の[役割](xref:security/authorization/roles)と豊富な[ポリシーベース](xref:security/authorization/policies)のモデルを提供します。</span><span class="sxs-lookup"><span data-stu-id="f288a-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="f288a-114">承認は、要件の中で表され、ハンドラーが要件に対するユーザーのクレームを評価します。</span><span class="sxs-lookup"><span data-stu-id="f288a-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="f288a-115">命令的なチェックはシンプルなポリシー、または、ユーザーのIDとユーザーがアクセスしようとするリソースのプロパティの両方を評価するポリシーに基づくことができます。</span><span class="sxs-lookup"><span data-stu-id="f288a-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="f288a-116">名前空間</span><span class="sxs-lookup"><span data-stu-id="f288a-116">Namespaces</span></span>

<span data-ttu-id="f288a-117">`AuthorizeAttribute` 属性と `AllowAnonymousAttribute` 属性を含む承認コンポーネントは、`Microsoft.AspNetCore.Authorization` 名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="f288a-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="f288a-118">[単純な承認](xref:security/authorization/simple)に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f288a-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
