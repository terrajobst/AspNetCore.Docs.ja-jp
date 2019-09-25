---
title: ASP.NET Core の概要
author: rick-anderson
description: ASP.NET Core を使用して基本的な Hello World アプリを作成し、実行する簡単なチュートリアルです。
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: getting-started
ms.openlocfilehash: 0f05ab120d64832a4bc2fd70921efc7238ee9eac
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187058"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="764da-103">チュートリアル: ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="764da-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="764da-104">このチュートリアルでは、.NET Core コマンド ライン インターフェイスを使用して ASP.NET Core Web アプリを作成して実行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="764da-104">This tutorial shows how to use the .NET Core command-line interface to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="764da-105">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="764da-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="764da-106">Web アプリ プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="764da-106">Create a web app project.</span></span>
> * <span data-ttu-id="764da-107">開発証明書を信頼します。</span><span class="sxs-lookup"><span data-stu-id="764da-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="764da-108">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="764da-108">Run the app.</span></span>
> * <span data-ttu-id="764da-109">Razor ページを編集する。</span><span class="sxs-lookup"><span data-stu-id="764da-109">Edit a Razor page.</span></span>

<span data-ttu-id="764da-110">最後に、作業用の Web アプリがご利用のローカル コンピューター上で実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="764da-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Web アプリのホーム ページ](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="764da-112">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="764da-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.0-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="764da-113">Web アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="764da-113">Create a web app project</span></span>

<span data-ttu-id="764da-114">コマンド シェルを開き、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="764da-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="764da-115">上記のコマンドにより、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="764da-115">The preceding command:</span></span>

* <span data-ttu-id="764da-116">新しい Web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="764da-116">Creates a new web app.</span></span>  
* <span data-ttu-id="764da-117">パラメーター `-o` によって、アプリのソース ファイルを含んだ *aspnetcoreapp* という名前のディレクトリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="764da-117">The `-o` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="764da-118">開発証明書を信頼する</span><span class="sxs-lookup"><span data-stu-id="764da-118">Trust the development certificate</span></span>

<span data-ttu-id="764da-119">HTTPS 開発証明書を信頼します。</span><span class="sxs-lookup"><span data-stu-id="764da-119">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="764da-120">Windows</span><span class="sxs-lookup"><span data-stu-id="764da-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="764da-121">上記のコマンドでは、次のダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="764da-121">The preceding command displays the following dialog:</span></span>

![セキュリティ警告のダイアログ](~/getting-started/_static/cert.png)

<span data-ttu-id="764da-123">開発証明書を信頼することに同意する場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="764da-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="764da-124">macOS</span><span class="sxs-lookup"><span data-stu-id="764da-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="764da-125">上記のコマンドでは、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="764da-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="764da-126">*HTTPS 開発証明書の信頼が要求されました。証明書がまだ信頼されていない場合は、次のコマンドを実行します:*  `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="764da-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="764da-127">このコマンドでは、システム キーチェーン上に証明書をインストールするためにご自分のパスワードを入力するよう求められる場合があります。</span><span class="sxs-lookup"><span data-stu-id="764da-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="764da-128">開発証明書を信頼することに同意する場合は、パスワードを入力します。</span><span class="sxs-lookup"><span data-stu-id="764da-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="764da-129">Linux</span><span class="sxs-lookup"><span data-stu-id="764da-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="764da-130">Windows Subsystem for Linux については、「[Trust HTTPS certificate from Windows Subsystem for Linux (Windows Subsystem for Linux からの HTTPS 証明書を信頼する)](xref:security/enforcing-ssl#wsl)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="764da-130">For Windows Subsystem for Linux, see [Trust HTTPS certificate from Windows Subsystem for Linux](xref:security/enforcing-ssl#wsl).</span></span>

<span data-ttu-id="764da-131">HTTPS 開発証明書を信頼する方法については、Linux ディストリビューションのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="764da-131">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="764da-132">詳細については、[ASP.NET Core HTTPS 開発証明書の信頼](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)に関する記事をご覧ください</span><span class="sxs-lookup"><span data-stu-id="764da-132">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="764da-133">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="764da-133">Run the app</span></span>

<span data-ttu-id="764da-134">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="764da-134">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="764da-135">コマンド シェルでアプリが開始したことが示されたら、[https://localhost:5001](https://localhost:5001) を参照します。</span><span class="sxs-lookup"><span data-stu-id="764da-135">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="764da-136">**[Accept]\(承認\)** をクリックして、プライバシーとクッキーのポリシーを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="764da-136">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="764da-137">このアプリで個人情報が保持されることはありません。</span><span class="sxs-lookup"><span data-stu-id="764da-137">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="764da-138">Razor ページを編集する</span><span class="sxs-lookup"><span data-stu-id="764da-138">Edit a Razor page</span></span>

<span data-ttu-id="764da-139">*Pages/Index.cshtml* を開き、次の強調表示されたマークアップでページを変更します。</span><span class="sxs-lookup"><span data-stu-id="764da-139">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="764da-140">[https://localhost:5001](https://localhost:5001) を参照して、変更が表示されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="764da-140">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="764da-141">次の手順</span><span class="sxs-lookup"><span data-stu-id="764da-141">Next steps</span></span>

<span data-ttu-id="764da-142">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="764da-142">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="764da-143">Web アプリ プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="764da-143">Create a web app project.</span></span>
> * <span data-ttu-id="764da-144">開発証明書を信頼します。</span><span class="sxs-lookup"><span data-stu-id="764da-144">Trust the development certificate.</span></span>
> * <span data-ttu-id="764da-145">プロジェクトを実行します。</span><span class="sxs-lookup"><span data-stu-id="764da-145">Run the project.</span></span>
> * <span data-ttu-id="764da-146">変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="764da-146">Make a change.</span></span>

<span data-ttu-id="764da-147">ASP.NET Core の詳細については、次の概要の推奨のラーニング パスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="764da-147">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
