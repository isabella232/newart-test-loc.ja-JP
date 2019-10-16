---
title: SSL 証明書を作成する
description: 開発者向けサーバー用に手動で証明書を作成する場合の対処方法
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: c96489e6577f4887d2f22a9e81ea50f46cc9a5a3
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424276"
---
# <a name="create-an-ssl-certificate"></a>SSL 証明書を作成する

この記事では、SSL 証明書を作成する方法について説明します。

Windows 8 以降で PowerShell `New-SelfSignedCertificate` コマンドレットを使用して証明書を作成するには、次のコマンドを実行します。

```cmd
pbiviz --create-cert
```

ツールを使用するには、Windows 7 用の OpenSSL をインストールする必要があります。 OpenSSL ユーティリティはコマンド ラインから使用できる必要があります。

OpenSSL をインストールするには、[OpenSSL](https://www.openssl.org) サイトまたは [OpenSSL バイナリ](https://wiki.openssl.org/index.php/Binaries) サイトにアクセスします。



## <a name="create-a-certificate-mac-os-x"></a>証明書を作成する (Mac OS X)

通常、OpenSSL ユーティリティは、Linux または Mac OS X オペレーティング システムで使用できます。

次のいずれかのコマンドを実行して、ユーティリティをインストールすることもできます。
* *Brew* パッケージ マネージャーからの場合:

    ```cmd
    brew install openssl
    brew link openssl --force
    ```

* *MacPorts* を使用する場合:

    ```cmd
    sudo port install openssl
    ```

新しい証明書を生成するための OpenSSL ユーティリティをインストールしたら、次のコマンドを実行します。

```cmd
pbiviz --create-cert
```

## <a name="create-a-certificate-linux"></a>証明書を作成する (Linux)

ご利用の Linux オペレーティング システムで OpenSSL ユーティリティを使用できない場合は、次のコマンドのいずれか 1 つを使用してインストールできます。

* *APT* パッケージ マネージャーの場合:

    ```cmd
    sudo apt-get install openssl
    ```

* *Yellowdog アップデーター* の場合:

    ```cmd
    yum install openssl
    ```

* *Redhat パッケージ マネージャー* の場合:

    ```cmd
    rpm install openssl
    ```

ご利用のオペレーティング システムで OpenSSL ユーティリティが既に使用できるようになっている場合は、次のコマンドを実行して新しい証明書を作成します。

```cmd
pbiviz --create-cert
```

あるいは、[OpenSSL](https://www.openssl.org) サイトまたは [OpenSSL バイナリ](https://wiki.openssl.org/index.php/Binaries) サイトにアクセスすることで、OpenSSL ユーティリティを取得することもできます。

## <a name="generate-the-certificate-manually"></a>証明書を手動で作成する

任意のツールで証明書が生成されるように指定することができます。

ご利用のシステムに OpenSSL ユーティリティが既にインストールされている場合は、次のコマンドを実行して新しい証明書を作成します。

```cmd
openssl req -x509 -newkey rsa:4096 -keyout PowerBICustomVisualTest_private.key -out PowerBICustomVisualTest_public.crt -days 365
```

通常は、次のいずれか 1 つを実行することで、PowerBI-visuals-tools Web サーバー証明書を見つけることができます。

* ツールのグローバル インスタンスの場合:

    ```cmd
    %appdata%\npm\node_modules\PowerBI-visuals-tools\certs
    ```

* ツールのローカル インスタンスの場合:

    ```cmd
    <custom visual project root>\node_modules\PowerBI-visuals-tools\certs
    ```

PEM 形式を使用する場合は、証明書ファイルを *PowerBICustomVisualTest_public.crt* として保存し、privateKey を *PowerBICustomVisualTest_public.key* として保存します。

PFX 形式を使用する場合は、証明書ファイルを *PowerBICustomVisualTest_public.pfx* として保存します。

PFX 証明書ファイルにパスフレーズが必要な場合は、次の手順を行います。
1. 構成ファイルで、次のように指定します。

    ```cmd
    \PowerBI-visuals-tools\config.json
    ```

1. `server` セクションで、"*YOUR PASSPHRASE*" プレースホルダーを置き換えることで、パスフレーズを指定します。

    ```cmd
    "server":{
        "root":"webRoot",
        "assetsRoute":"/assets",
        "privateKey":"certs/PowerBICustomVisualTest_private.key",
        "certificate":"certs/PowerBICustomVisualTest_public.crt",
        "pfx":"certs/PowerBICustomVisualTest_public.pfx",
        "port":"8080",
        "passphrase":"YOUR PASSPHRASE"
    }
    ```
