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
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="f69a0-103">ASP.NET Core での承認の概要</span><span class="sxs-lookup"><span data-stu-id="f69a0-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="f69a0-104">承認とは、ユーザーが何を実行できるかを決定するプロセスのことです。</span><span class="sxs-lookup"><span data-stu-id="f69a0-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="f69a0-105">例として、管理者ユーザーはドキュメントライブラリーの作成、ドキュメントの追加、ドキュメントの編集、およびそれらを削除することが許可されています。</span><span class="sxs-lookup"><span data-stu-id="f69a0-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="f69a0-106">管理者ではないユーザーは、ドキュメントを読む権限しかありません。</span><span class="sxs-lookup"><span data-stu-id="f69a0-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="f69a0-107">承認は直交性があり、認証から独立しています。</span><span class="sxs-lookup"><span data-stu-id="f69a0-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="f69a0-108">しかし、承認には認証のメカニズムが必須です。</span><span class="sxs-lookup"><span data-stu-id="f69a0-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="f69a0-109">認証は、ユーザーが誰であるかを確認するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="f69a0-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="f69a0-110">認証は、現在のユーザーに対して1つ以上のIDを作成します。</span><span class="sxs-lookup"><span data-stu-id="f69a0-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="f69a0-111">ASP.NET Core での認証の詳細については、「<xref:security/authentication/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f69a0-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="f69a0-112">承認の種類</span><span class="sxs-lookup"><span data-stu-id="f69a0-112">Authorization types</span></span>

<span data-ttu-id="f69a0-113">ASP.NET Core の承認は、シンプルで宣言的な[ロールベース](xref:security/authorization/roles)と、リッチな[ポリシーベース](xref:security/authorization/policies)のモデルが提供されています。</span><span class="sxs-lookup"><span data-stu-id="f69a0-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="f69a0-114">承認は、要件の中で表され、ハンドラーが要件に対するユーザーのクレームを評価します。</span><span class="sxs-lookup"><span data-stu-id="f69a0-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="f69a0-115">命令的なチェックはシンプルなポリシー、または、ユーザーのIDとユーザーがアクセスしようとするリソースのプロパティの両方を評価するポリシーに基づくことができます。</span><span class="sxs-lookup"><span data-stu-id="f69a0-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="f69a0-116">名前空間</span><span class="sxs-lookup"><span data-stu-id="f69a0-116">Namespaces</span></span>

<span data-ttu-id="f69a0-117">`AuthorizeAttribute` および `AllowAnonymousAttribute` 属性を含む承認のコンポーネントは、 `Microsoft.AspNetCore.Authorization` 名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="f69a0-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="f69a0-118">[シンプルな承認](xref:security/authorization/simple)をご参照ください。</span><span class="sxs-lookup"><span data-stu-id="f69a0-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
