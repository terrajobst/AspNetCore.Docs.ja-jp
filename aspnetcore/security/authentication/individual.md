---
title: 個々のユーザーアカウントで作成された ASP.NET Core プロジェクトに基づくアーティクル
author: rick-anderson
description: 個々のユーザーアカウントで作成された ASP.NET Core プロジェクトに基づいて、記事を発見します。
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: 91c5665dc50124b3ba09bdcfbf3ba501f684c604
ms.sourcegitcommit: 9e85c2562df5e108d7933635c830297f484bb775
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73463036"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="7637d-103">個々のユーザーアカウントで作成された ASP.NET Core プロジェクトに基づくアーティクル</span><span class="sxs-lookup"><span data-stu-id="7637d-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="7637d-104">ASP.NET Core Id は、Visual Studio の [個別のユーザーアカウント] オプションを使用してプロジェクトテンプレートに含まれています。</span><span class="sxs-lookup"><span data-stu-id="7637d-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="7637d-105">認証テンプレートは、`-au Individual`と共に .NET Core CLI で使用できます。</span><span class="sxs-lookup"><span data-stu-id="7637d-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="7637d-106">Web API 認証については、[この GitHub の問題](https://github.com/aspnet/AspNetCore/issues/5833)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7637d-106">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="7637d-107">認証なし</span><span class="sxs-lookup"><span data-stu-id="7637d-107">No Authentication</span></span>

<span data-ttu-id="7637d-108">認証は、.NET Core CLI で `-au` オプションと共に指定されます。</span><span class="sxs-lookup"><span data-stu-id="7637d-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="7637d-109">Visual Studio では、新しい web アプリケーションで **[認証の変更]** ダイアログを使用できます。</span><span class="sxs-lookup"><span data-stu-id="7637d-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="7637d-110">Visual Studio での新しい web アプリの既定値は、認証され**ません**。</span><span class="sxs-lookup"><span data-stu-id="7637d-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="7637d-111">認証なしで作成されたプロジェクト:</span><span class="sxs-lookup"><span data-stu-id="7637d-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="7637d-112">サインインおよびサインアウトするための web ページと UI を含めないでください。</span><span class="sxs-lookup"><span data-stu-id="7637d-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="7637d-113">認証コードを含めないでください。</span><span class="sxs-lookup"><span data-stu-id="7637d-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="7637d-114">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="7637d-114">Windows Authentication</span></span>

<span data-ttu-id="7637d-115">`-au Windows` オプションを使用して .NET Core CLI の新しい web アプリに対して Windows 認証が指定されています。</span><span class="sxs-lookup"><span data-stu-id="7637d-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="7637d-116">Visual Studio では、 **[認証の変更]** ダイアログボックスに**Windows 認証**オプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7637d-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="7637d-117">[Windows 認証] が選択されている場合、アプリは[Windows 認証 IIS モジュール](xref:host-and-deploy/iis/modules)を使用するように構成されます。</span><span class="sxs-lookup"><span data-stu-id="7637d-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="7637d-118">Windows 認証は、イントラネット web サイトを対象としています。</span><span class="sxs-lookup"><span data-stu-id="7637d-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7637d-119">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7637d-119">Additional resources</span></span>

<span data-ttu-id="7637d-120">次の記事では、個々のユーザーアカウントを使用する ASP.NET Core テンプレートで生成されたコードを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="7637d-120">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="7637d-121">ASP.NET Core でのアカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="7637d-121">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="7637d-122">承認によって保護されたユーザーデータを含む ASP.NET Core アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="7637d-122">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
