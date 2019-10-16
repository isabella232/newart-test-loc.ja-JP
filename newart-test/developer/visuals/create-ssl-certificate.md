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
# <a name="create-an-ssl-certificate"></a><span data-ttu-id="d08ea-103">SSL 証明書を作成する</span><span class="sxs-lookup"><span data-stu-id="d08ea-103">Create an SSL certificate</span></span>

<span data-ttu-id="d08ea-104">この記事では、SSL 証明書を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d08ea-104">This article describes how to create an SSL certificate.</span></span>

<span data-ttu-id="d08ea-105">Windows 8 以降で PowerShell `New-SelfSignedCertificate` コマンドレットを使用して証明書を作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d08ea-105">To generate the certificate by using the PowerShell `New-SelfSignedCertificate` cmdlet on Windows 8 or later, run the following command:</span></span>

```cmd
pbiviz --create-cert
```

<span data-ttu-id="d08ea-106">ツールを使用するには、Windows 7 用の OpenSSL をインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d08ea-106">The tool requires an OpenSSL installation for Windows 7.</span></span> <span data-ttu-id="d08ea-107">OpenSSL ユーティリティはコマンド ラインから使用できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="d08ea-107">The OpenSSL utility must be available from the command line.</span></span>

<span data-ttu-id="d08ea-108">OpenSSL をインストールするには、[OpenSSL](https://www.openssl.org) サイトまたは [OpenSSL バイナリ](https://wiki.openssl.org/index.php/Binaries) サイトにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="d08ea-108">To install OpenSSL, go to the [OpenSSL](https://www.openssl.org) or [OpenSSL Binaries](https://wiki.openssl.org/index.php/Binaries) site.</span></span>



## <a name="create-a-certificate-mac-os-x"></a><span data-ttu-id="d08ea-109">証明書を作成する (Mac OS X)</span><span class="sxs-lookup"><span data-stu-id="d08ea-109">Create a certificate (Mac OS X)</span></span>

<span data-ttu-id="d08ea-110">通常、OpenSSL ユーティリティは、Linux または Mac OS X オペレーティング システムで使用できます。</span><span class="sxs-lookup"><span data-stu-id="d08ea-110">Usually, the OpenSSL utility is available in the Linux or Mac OS X operating system.</span></span>

<span data-ttu-id="d08ea-111">次のいずれかのコマンドを実行して、ユーティリティをインストールすることもできます。</span><span class="sxs-lookup"><span data-stu-id="d08ea-111">You can also install the utility by running either of the following commands:</span></span>
* <span data-ttu-id="d08ea-112">*Brew* パッケージ マネージャーからの場合:</span><span class="sxs-lookup"><span data-stu-id="d08ea-112">From the *Brew* package manager:</span></span>

    ```cmd
    brew install openssl
    brew link openssl --force
    ```

* <span data-ttu-id="d08ea-113">*MacPorts* を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="d08ea-113">By using *MacPorts*:</span></span>

    ```cmd
    sudo port install openssl
    ```

<span data-ttu-id="d08ea-114">新しい証明書を生成するための OpenSSL ユーティリティをインストールしたら、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d08ea-114">After you install the OpenSSL utility for generating a new certificate, run the following command:</span></span>

```cmd
pbiviz --create-cert
```

## <a name="create-a-certificate-linux"></a><span data-ttu-id="d08ea-115">証明書を作成する (Linux)</span><span class="sxs-lookup"><span data-stu-id="d08ea-115">Create a certificate (Linux)</span></span>

<span data-ttu-id="d08ea-116">ご利用の Linux オペレーティング システムで OpenSSL ユーティリティを使用できない場合は、次のコマンドのいずれか 1 つを使用してインストールできます。</span><span class="sxs-lookup"><span data-stu-id="d08ea-116">If the OpenSSL utility isn't available in your Linux operating system, you can install it by using one of the following commands:</span></span>

* <span data-ttu-id="d08ea-117">*APT* パッケージ マネージャーの場合:</span><span class="sxs-lookup"><span data-stu-id="d08ea-117">For *APT* package manager:</span></span>

    ```cmd
    sudo apt-get install openssl
    ```

* <span data-ttu-id="d08ea-118">*Yellowdog アップデーター* の場合:</span><span class="sxs-lookup"><span data-stu-id="d08ea-118">For *Yellowdog Updater*:</span></span>

    ```cmd
    yum install openssl
    ```

* <span data-ttu-id="d08ea-119">*Redhat パッケージ マネージャー* の場合:</span><span class="sxs-lookup"><span data-stu-id="d08ea-119">For *Redhat Package Manager*:</span></span>

    ```cmd
    rpm install openssl
    ```

<span data-ttu-id="d08ea-120">ご利用のオペレーティング システムで OpenSSL ユーティリティが既に使用できるようになっている場合は、次のコマンドを実行して新しい証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="d08ea-120">If the OpenSSL utility is already available in your operating system, generate a new certificate by running the following command:</span></span>

```cmd
pbiviz --create-cert
```

<span data-ttu-id="d08ea-121">あるいは、[OpenSSL](https://www.openssl.org) サイトまたは [OpenSSL バイナリ](https://wiki.openssl.org/index.php/Binaries) サイトにアクセスすることで、OpenSSL ユーティリティを取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="d08ea-121">Or you can get the OpenSSL utility by going to the [OpenSSL](https://www.openssl.org) or [OpenSSL Binaries](https://wiki.openssl.org/index.php/Binaries) site.</span></span>

## <a name="generate-the-certificate-manually"></a><span data-ttu-id="d08ea-122">証明書を手動で作成する</span><span class="sxs-lookup"><span data-stu-id="d08ea-122">Generate the certificate manually</span></span>

<span data-ttu-id="d08ea-123">任意のツールで証明書が生成されるように指定することができます。</span><span class="sxs-lookup"><span data-stu-id="d08ea-123">You can specify that your certificates be generated by any tools.</span></span>

<span data-ttu-id="d08ea-124">ご利用のシステムに OpenSSL ユーティリティが既にインストールされている場合は、次のコマンドを実行して新しい証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="d08ea-124">If the OpenSSL utility is already installed in your system, generate a new certificate by running the following commands:</span></span>

```cmd
openssl req -x509 -newkey rsa:4096 -keyout PowerBICustomVisualTest_private.key -out PowerBICustomVisualTest_public.crt -days 365
```

<span data-ttu-id="d08ea-125">通常は、次のいずれか 1 つを実行することで、PowerBI-visuals-tools Web サーバー証明書を見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="d08ea-125">You can usually find the PowerBI-visuals-tools web server certificates by running one the following:</span></span>

* <span data-ttu-id="d08ea-126">ツールのグローバル インスタンスの場合:</span><span class="sxs-lookup"><span data-stu-id="d08ea-126">For the global instance of the tools:</span></span>

    ```cmd
    %appdata%\npm\node_modules\PowerBI-visuals-tools\certs
    ```

* <span data-ttu-id="d08ea-127">ツールのローカル インスタンスの場合:</span><span class="sxs-lookup"><span data-stu-id="d08ea-127">For the local instance of the tools:</span></span>

    ```cmd
    <custom visual project root>\node_modules\PowerBI-visuals-tools\certs
    ```

<span data-ttu-id="d08ea-128">PEM 形式を使用する場合は、証明書ファイルを *PowerBICustomVisualTest_public.crt* として保存し、privateKey を *PowerBICustomVisualTest_public.key* として保存します。</span><span class="sxs-lookup"><span data-stu-id="d08ea-128">If you use the PEM format, save the certificate file as *PowerBICustomVisualTest_public.crt*, and save privateKey as *PowerBICustomVisualTest_public.key*.</span></span>

<span data-ttu-id="d08ea-129">PFX 形式を使用する場合は、証明書ファイルを *PowerBICustomVisualTest_public.pfx* として保存します。</span><span class="sxs-lookup"><span data-stu-id="d08ea-129">If you use the PFX format, save the certificate file as *PowerBICustomVisualTest_public.pfx*.</span></span>

<span data-ttu-id="d08ea-130">PFX 証明書ファイルにパスフレーズが必要な場合は、次の手順を行います。</span><span class="sxs-lookup"><span data-stu-id="d08ea-130">If your PFX certificate file requires a passphrase, do the following:</span></span>
1. <span data-ttu-id="d08ea-131">構成ファイルで、次のように指定します。</span><span class="sxs-lookup"><span data-stu-id="d08ea-131">In the config file, specify:</span></span>

    ```cmd
    \PowerBI-visuals-tools\config.json
    ```

1. <span data-ttu-id="d08ea-132">`server` セクションで、"*YOUR PASSPHRASE*" プレースホルダーを置き換えることで、パスフレーズを指定します。</span><span class="sxs-lookup"><span data-stu-id="d08ea-132">In the `server` section, specify the passphrase by replacing the "*YOUR PASSPHRASE*" placeholder:</span></span>

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
