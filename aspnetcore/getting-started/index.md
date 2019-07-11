---
title: ASP.NET Core の概要
author: rick-anderson
description: ASP.NET Core を使用して基本的な Hello World アプリを作成し、実行する簡単なチュートリアルです。
ms.author: riande
ms.custom: mvc
ms.date: 05/15/2019
uid: getting-started
ms.openlocfilehash: c35251a0e49fbbffee7b8f5ea6905322b9042261
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814933"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="6e7ca-103">チュートリアル: ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="6e7ca-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="6e7ca-104">このチュートリアルでは、.NET Core コマンド ライン インターフェイスを使用して ASP.NET Core Web アプリを作成して実行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-104">This tutorial shows how to use the .NET Core command-line interface to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="6e7ca-105">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6e7ca-106">Web アプリ プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-106">Create a web app project.</span></span>
> * <span data-ttu-id="6e7ca-107">開発証明書を信頼します。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="6e7ca-108">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-108">Run the app.</span></span>
> * <span data-ttu-id="6e7ca-109">Razor ページを編集する。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-109">Edit a Razor page.</span></span>

<span data-ttu-id="6e7ca-110">最後に、作業用の Web アプリがご利用のローカル コンピューター上で実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Web アプリのホーム ページ](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="6e7ca-112">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6e7ca-112">Prerequisites</span></span>

* [<span data-ttu-id="6e7ca-113">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="6e7ca-113">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a><span data-ttu-id="6e7ca-114">Web アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="6e7ca-114">Create a web app project</span></span>

<span data-ttu-id="6e7ca-115">コマンド シェルを開き、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-115">Open a command shell, and enter the following command:</span></span>

```console
dotnet new webapp -o aspnetcoreapp
```

### <a name="trust-the-development-certificate"></a><span data-ttu-id="6e7ca-116">開発証明書を信頼する</span><span class="sxs-lookup"><span data-stu-id="6e7ca-116">Trust the development certificate</span></span>

<span data-ttu-id="6e7ca-117">HTTPS 開発証明書を信頼します。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-117">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="6e7ca-118">Windows</span><span class="sxs-lookup"><span data-stu-id="6e7ca-118">Windows</span></span>](#tab/windows)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="6e7ca-119">上記のコマンドでは、次のダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-119">The preceding command displays the following dialog:</span></span>

![セキュリティ警告のダイアログ](~/getting-started/_static/cert.png)

<span data-ttu-id="6e7ca-121">開発証明書を信頼することに同意する場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-121">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="6e7ca-122">macOS</span><span class="sxs-lookup"><span data-stu-id="6e7ca-122">macOS</span></span>](#tab/macos)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="6e7ca-123">上記のコマンドでは、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-123">The preceding command displays the following message:</span></span>

<span data-ttu-id="6e7ca-124">*HTTPS 開発証明書の信頼が要求されました。証明書がまだ信頼されていない場合は、次のコマンドを実行します:*  `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="6e7ca-124">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="6e7ca-125">このコマンドでは、システム キーチェーン上に証明書をインストールするためにご自分のパスワードを入力するよう求められる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-125">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="6e7ca-126">開発証明書を信頼することに同意する場合は、パスワードを入力します。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-126">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="6e7ca-127">Linux</span><span class="sxs-lookup"><span data-stu-id="6e7ca-127">Linux</span></span>](#tab/linux)

<span data-ttu-id="6e7ca-128">Windows Subsystem for Linux については、「[Trust HTTPS certificate from Windows Subsystem for Linux (Windows Subsystem for Linux からの HTTPS 証明書を信頼する)](xref:security/enforcing-ssl#wsl)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-128">For Windows Subsystem for Linux, see [Trust HTTPS certificate from Windows Subsystem for Linux](xref:security/enforcing-ssl#wsl).</span></span>

<span data-ttu-id="6e7ca-129">HTTPS 開発証明書を信頼する方法については、Linux ディストリビューションのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-129">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="6e7ca-130">詳細については、[ASP.NET Core HTTPS 開発証明書の信頼](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)に関する記事をご覧ください</span><span class="sxs-lookup"><span data-stu-id="6e7ca-130">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="6e7ca-131">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="6e7ca-131">Run the app</span></span>

<span data-ttu-id="6e7ca-132">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-132">Run the following commands:</span></span>

```console
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="6e7ca-133">コマンド シェルでアプリが開始したことが示されたら、[https://localhost:5001](https://localhost:5001) を参照します。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-133">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="6e7ca-134">**[Accept]\(承認\)** をクリックして、プライバシーとクッキーのポリシーを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-134">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="6e7ca-135">このアプリで個人情報が保持されることはありません。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-135">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="6e7ca-136">Razor ページを編集する</span><span class="sxs-lookup"><span data-stu-id="6e7ca-136">Edit a Razor page</span></span>

<span data-ttu-id="6e7ca-137">*Pages/Index.cshtml* を開き、次の強調表示されたマークアップでページを変更します。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-137">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="6e7ca-138">[https://localhost:5001](https://localhost:5001) を参照して、変更が表示されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-138">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6e7ca-139">次の手順</span><span class="sxs-lookup"><span data-stu-id="6e7ca-139">Next steps</span></span>

<span data-ttu-id="6e7ca-140">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-140">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6e7ca-141">Web アプリ プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-141">Create a web app project.</span></span>
> * <span data-ttu-id="6e7ca-142">開発証明書を信頼します。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-142">Trust the development certificate.</span></span>
> * <span data-ttu-id="6e7ca-143">プロジェクトを実行します。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-143">Run the project.</span></span>
> * <span data-ttu-id="6e7ca-144">変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-144">Make a change.</span></span>

<span data-ttu-id="6e7ca-145">ASP.NET Core の詳細については、次の概要の推奨のラーニング パスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e7ca-145">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
