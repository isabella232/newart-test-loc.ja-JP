---
title: Power BI ビジュアルの高度な編集モード
description: この記事では、Power BI ビジュアルで高度な UI コントロールを設定する方法について説明します。
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: da72cf603027bc97060e7a00ed4a4e959a3a92e2
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424201"
---
# <a name="advanced-edit-mode-in-power-bi-visuals"></a><span data-ttu-id="233ad-103">Power BI ビジュアルの高度な編集モード</span><span class="sxs-lookup"><span data-stu-id="233ad-103">Advanced edit mode in Power BI visuals</span></span>

<span data-ttu-id="233ad-104">Power BI ビジュアルに高度な UI コントロールが必要な場合は、高度な編集モードを利用できます。</span><span class="sxs-lookup"><span data-stu-id="233ad-104">If you require advanced UI controls in your Power BI visual, you can take advantage of advanced edit mode.</span></span> <span data-ttu-id="233ad-105">レポート編集モードになっている場合は、 **[編集]** ボタンを選択して、編集モードを **[詳細]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="233ad-105">When you're in report editing mode, you select an **Edit** button to set the edit mode to **Advanced**.</span></span> <span data-ttu-id="233ad-106">ビジュアルでは `EditMode` フラグを使用して、この UI コントロールを表示するかどうかを決定できます。</span><span class="sxs-lookup"><span data-stu-id="233ad-106">The visual can use the `EditMode` flag to determine whether it should display this UI control.</span></span>

<span data-ttu-id="233ad-107">既定では、ビジュアルで高度な編集モードはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="233ad-107">By default, the visual doesn't support advanced edit mode.</span></span> <span data-ttu-id="233ad-108">別の動作が必要な場合は、`advancedEditModeSupport` プロパティを設定し、ビジュアルの *capabilities.json* ファイルでこれを明示的に示すことができます。</span><span class="sxs-lookup"><span data-stu-id="233ad-108">If a different behavior is required, you can explicitly state this in the visual's *capabilities.json* file by setting the `advancedEditModeSupport` property.</span></span>

<span data-ttu-id="233ad-109">使用可能な値は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="233ad-109">The possible values are:</span></span>

- <span data-ttu-id="233ad-110">`0` - NotSupported</span><span class="sxs-lookup"><span data-stu-id="233ad-110">`0` - NotSupported</span></span>

- <span data-ttu-id="233ad-111">`1` - SupportedNoAction</span><span class="sxs-lookup"><span data-stu-id="233ad-111">`1` - SupportedNoAction</span></span>

- <span data-ttu-id="233ad-112">`2` - SupportedInFocus</span><span class="sxs-lookup"><span data-stu-id="233ad-112">`2` - SupportedInFocus</span></span>

## <a name="enter-advanced-edit-mode"></a><span data-ttu-id="233ad-113">高度な編集モードを開始する</span><span class="sxs-lookup"><span data-stu-id="233ad-113">Enter advanced edit mode</span></span>

<span data-ttu-id="233ad-114">次の場合、 **[編集]** ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="233ad-114">An **Edit** button is displayed if:</span></span>

* <span data-ttu-id="233ad-115">*capabilities.json* ファイルで `advancedEditModeSupport` プロパティが `SupportedNoAction` または `SupportedInFocus` のいずれかに設定されている</span><span class="sxs-lookup"><span data-stu-id="233ad-115">The `advancedEditModeSupport` property is set in the *capabilities.json* file to either `SupportedNoAction` or `SupportedInFocus`.</span></span>

* <span data-ttu-id="233ad-116">ビジュアルがレポート編集モードで表示されている</span><span class="sxs-lookup"><span data-stu-id="233ad-116">The visual is viewed in report editing mode.</span></span>

<span data-ttu-id="233ad-117">`advancedEditModeSupport` プロパティが *capabilities.json* ファイルにない場合、または `NotSupported` に設定されている場合、 **[編集]** ボタンは表示されません。</span><span class="sxs-lookup"><span data-stu-id="233ad-117">If `advancedEditModeSupport` property is missing from the *capabilities.json* file or set to `NotSupported`, the **Edit** button is not displayed.</span></span>

![編集モードを開始する](./media/edit-mode.png)

<span data-ttu-id="233ad-119">**[編集]** を選択すると、ビジュアルでは、EditMode が `Advanced` に設定された update() 呼び出しを取得します。</span><span class="sxs-lookup"><span data-stu-id="233ad-119">When you select **Edit**, the visual gets an update() call with EditMode set to `Advanced`.</span></span> <span data-ttu-id="233ad-120">*capabilities.json* ファイルに設定されている値に応じて、次のアクションが実行されます。</span><span class="sxs-lookup"><span data-stu-id="233ad-120">Depending on the value that's set in the *capabilities.json* file, the following actions occur:</span></span>

* <span data-ttu-id="233ad-121">`SupportedNoAction`:ホストではこれ以上アクションは必要とされません。</span><span class="sxs-lookup"><span data-stu-id="233ad-121">`SupportedNoAction`: No further action is required by the host.</span></span>
* <span data-ttu-id="233ad-122">`SupportedInFocus`:ホストでは、ビジュアルがフォーカス モードでポップアウトされます。</span><span class="sxs-lookup"><span data-stu-id="233ad-122">`SupportedInFocus`: The host pops out the visual into in focus mode.</span></span>

## <a name="exit-advanced-edit-mode"></a><span data-ttu-id="233ad-123">高度な編集モードを終了する</span><span class="sxs-lookup"><span data-stu-id="233ad-123">Exit advanced edit mode</span></span>

<span data-ttu-id="233ad-124">次の場合、 **[レポートに戻る]** ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="233ad-124">The **Back to report** button is displayed if:</span></span>

* <span data-ttu-id="233ad-125">*capabilities.json* ファイルで `advancedEditModeSupport` プロパティが `SupportedInFocus` に設定されている</span><span class="sxs-lookup"><span data-stu-id="233ad-125">The `advancedEditModeSupport` property is set in the *capabilities.json* file to `SupportedInFocus`.</span></span>
