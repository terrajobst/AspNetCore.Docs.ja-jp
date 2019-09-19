* <span data-ttu-id="fc0f4-101">次のコマンドを実行し、HTTPS 開発証明書を信頼します。</span><span class="sxs-lookup"><span data-stu-id="fc0f4-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="fc0f4-102">上記のコマンドは、Linux では機能しません。</span><span class="sxs-lookup"><span data-stu-id="fc0f4-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="fc0f4-103">証明書を信頼する方法については、Linux ディストリビューションのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fc0f4-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="fc0f4-104">上記のコマンドでは、次のダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="fc0f4-104">The preceding command displays the following dialog:</span></span>

  ![セキュリティ警告のダイアログ](~/getting-started/_static/cert.png)

* <span data-ttu-id="fc0f4-106">開発証明書を信頼することに同意する場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="fc0f4-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="fc0f4-107">詳細については、[ASP.NET Core HTTPS 開発証明書の信頼](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)に関する記事をご覧ください</span><span class="sxs-lookup"><span data-stu-id="fc0f4-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
