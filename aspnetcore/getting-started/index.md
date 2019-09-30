---
title: ASP.NET Core の概要
author: rick-anderson
description: ASP.NET Core を使用して基本的な Hello World アプリを作成し、実行する簡単なチュートリアルです。
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: getting-started
ms.openlocfilehash: c9cd5e05f52c8bdefa931adc654087dac91e2f05
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317761"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>チュートリアル: ASP.NET Core の概要

このチュートリアルでは、.NET Core コマンド ライン インターフェイスを使用して ASP.NET Core Web アプリを作成して実行する方法について説明します。

以下の方法について説明します。

> [!div class="checklist"]
> * Web アプリ プロジェクトを作成する。
> * 開発証明書を信頼します。
> * アプリを実行します。
> * Razor ページを編集する。

最後に、作業用の Web アプリがご利用のローカル コンピューター上で実行されるようにします。

![Web アプリのホーム ページ](_static/home-page.png)

## <a name="prerequisites"></a>必須コンポーネント

[!INCLUDE[](~/includes/3.0-SDK.md)]

## <a name="create-a-web-app-project"></a>Web アプリ プロジェクトを作成する

コマンド シェルを開き、次のコマンドを入力します。

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

上記のコマンドにより、次のことが行われます。

* 新しい Web アプリを作成します。  
* パラメーター `-o` によって、アプリのソース ファイルを含んだ *aspnetcoreapp* という名前のディレクトリが作成されます。

### <a name="trust-the-development-certificate"></a>開発証明書を信頼する

HTTPS 開発証明書を信頼します。

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

上記のコマンドでは、次のダイアログが表示されます。

![セキュリティ警告のダイアログ](~/getting-started/_static/cert.png)

開発証明書を信頼することに同意する場合は、 **[はい]** を選択します。

# <a name="macostabmacos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

上記のコマンドでは、次のメッセージが表示されます。

*HTTPS 開発証明書の信頼が要求されました。証明書がまだ信頼されていない場合は、次のコマンドを実行します:*  `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`

このコマンドでは、システム キーチェーン上に証明書をインストールするためにご自分のパスワードを入力するよう求められる場合があります。 開発証明書を信頼することに同意する場合は、パスワードを入力します。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Windows Subsystem for Linux については、「[Trust HTTPS certificate from Windows Subsystem for Linux (Windows Subsystem for Linux からの HTTPS 証明書を信頼する)](xref:security/enforcing-ssl#wsl)」をご覧ください。

HTTPS 開発証明書を信頼する方法については、Linux ディストリビューションのドキュメントを参照してください。

---

詳細については、[ASP.NET Core HTTPS 開発証明書の信頼](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)に関する記事をご覧ください

## <a name="run-the-app"></a>アプリを実行する

次のコマンドを実行します。

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

コマンド シェルでアプリが開始したことが示されたら、[https://localhost:5001](https://localhost:5001) を参照します。 **[Accept]\(承認\)** をクリックして、プライバシーとクッキーのポリシーを受け入れます。 このアプリで個人情報が保持されることはありません。

## <a name="edit-a-razor-page"></a>Razor ページを編集する

*Pages/Index.cshtml* を開き、次の強調表示されたマークアップを使ってページを変更し、保存します。

[!code-cshtml[](sample/index.cshtml?highlight=9)]

[https://localhost:5001](https://localhost:5001) に移動し、ページを更新して、変更が表示されていることを確認します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
> * Web アプリ プロジェクトを作成する。
> * 開発証明書を信頼します。
> * プロジェクトを実行します。
> * 変更を加えます。

ASP.NET Core の詳細については、次の概要の推奨のラーニング パスを参照してください。

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
