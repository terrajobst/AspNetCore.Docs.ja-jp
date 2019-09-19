* <span data-ttu-id="73306-101">次のコマンドを実行し、HTTPS 開発証明書を信頼します。</span><span class="sxs-lookup"><span data-stu-id="73306-101">Trust the HTTPS development certificate by running the following command:</span></span>

    ```dotnetcli
    dotnet dev-certs https --trust
    ```

* <span data-ttu-id="73306-102">上記のコマンドでは、次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="73306-102">The preceding command displays the following output:</span></span>

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* <span data-ttu-id="73306-103">求められた場合は、管理者のユーザー名とパスワードを入力します。</span><span class="sxs-lookup"><span data-stu-id="73306-103">Enter the admin username and password if prompted.</span></span>  <span data-ttu-id="73306-104">証明書がインストールされて信頼されます。</span><span class="sxs-lookup"><span data-stu-id="73306-104">The certificate will now be installed and trusted.</span></span>

    <span data-ttu-id="73306-105">詳細については、[ASP.NET Core HTTPS 開発証明書の信頼](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)に関する記事をご覧ください</span><span class="sxs-lookup"><span data-stu-id="73306-105">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>