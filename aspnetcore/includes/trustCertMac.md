* 次のコマンドを実行し、HTTPS 開発証明書を信頼します。

    ```console
    dotnet dev-certs https --trust
    ```

* 上記のコマンドでは、次の出力が表示されます。

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* 求められた場合は、管理者のユーザー名とパスワードを入力します。  証明書がインストールされて信頼されます。

    詳細については、[ASP.NET Core HTTPS 開発証明書の信頼](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)に関する記事をご覧ください