---
title: ASP.NET Core Security の概要
author: rick-anderson
description: ASP.NET Core での認証、承認、およびセキュリティの基本を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/index
ms.openlocfilehash: 0f8e96fb7d5246e746b95f8907745f849de60e24
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644030"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="5dbc5-103">ASP.NET Core Security の概要</span><span class="sxs-lookup"><span data-stu-id="5dbc5-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="5dbc5-104">ASP.NET Core を使用することで、開発者はアプリのセキュリティを簡単に構成して管理することができます。</span><span class="sxs-lookup"><span data-stu-id="5dbc5-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="5dbc5-105">ASP.NET Core には、認証、承認、データ保護、HTTPS の適用、アプリ シークレット、リクエスト フォージェリの対策保護、および CORS を管理するための機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="5dbc5-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, anti-request forgery protection, and CORS management.</span></span> <span data-ttu-id="5dbc5-106">これらのセキュリティ機能を使用すれば、堅牢かつセキュアな ASP.NET Core アプリを構築できます。</span><span class="sxs-lookup"><span data-stu-id="5dbc5-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="5dbc5-107">ASP.NET Core セキュリティ機能</span><span class="sxs-lookup"><span data-stu-id="5dbc5-107">ASP.NET Core security features</span></span>

<span data-ttu-id="5dbc5-108">ASP.NET Core では、組み込みの ID プロバイダーを含むアプリをセキュリティで保護するための多くのツールとライブラリが提供されますが、Facebook、Twitter、LinkedIn などのサードパーティの ID サービスを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="5dbc5-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in Identity providers but you can use 3rd party identity services such as Facebook, Twitter, or LinkedIn.</span></span> <span data-ttu-id="5dbc5-109">ASP.NET Core では、コードで公開せずに機密情報を格納して使用する方法である、アプリ シークレットを簡単に管理できます。</span><span class="sxs-lookup"><span data-stu-id="5dbc5-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="5dbc5-110">認証と承認</span><span class="sxs-lookup"><span data-stu-id="5dbc5-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="5dbc5-111">認証とは、ユーザーが提供した資格情報をオペレーティング システム、データベース、アプリまたはリソースに格納されているものと比較するプロセスのことです。</span><span class="sxs-lookup"><span data-stu-id="5dbc5-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="5dbc5-112">資格情報が一致した場合、ユーザーは正常に認証され、承認プロセス中に、承認されたアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="5dbc5-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="5dbc5-113">承認とは、ユーザーに許可する実行内容を決定するプロセスのことです。</span><span class="sxs-lookup"><span data-stu-id="5dbc5-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="5dbc5-114">また、認証はサーバー、データベース、アプリまたはリソースなどの領域に入る方法と見なすことができます。一方、承認はユーザーがその領域 (サーバー、データベース、またはアプリ) 内のいずかのオブジェクトに対して実行できるアクションです。</span><span class="sxs-lookup"><span data-stu-id="5dbc5-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="5dbc5-115">ソフトウェアの一般的な脆弱性</span><span class="sxs-lookup"><span data-stu-id="5dbc5-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="5dbc5-116">ASP.NET Core および EF には、アプリをセキュリティで保護し、セキュリティ違反を防止するのに役立つ機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="5dbc5-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="5dbc5-117">以下にリストされているリンクから、Web アプリの最も一般的なセキュリティの脆弱性を回避するための手法の詳細を示すドキュメントにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="5dbc5-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="5dbc5-118">クロスサイト スクリプト攻撃</span><span class="sxs-lookup"><span data-stu-id="5dbc5-118">Cross-site scripting attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="5dbc5-119">SQL インジェクション攻撃</span><span class="sxs-lookup"><span data-stu-id="5dbc5-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="5dbc5-120">クロスサイト リクエスト フォージェリ (CSRF)</span><span class="sxs-lookup"><span data-stu-id="5dbc5-120">Cross-Site Request Forgery (CSRF)</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="5dbc5-121">オープン リダイレクト攻撃</span><span class="sxs-lookup"><span data-stu-id="5dbc5-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="5dbc5-122">この他にも知っておく必要がある脆弱性はあります。</span><span class="sxs-lookup"><span data-stu-id="5dbc5-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="5dbc5-123">詳細については、目次の**セキュリティと ID** のセクションにある他の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dbc5-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
