---
title: Power BI ビジュアルでスライサーの同期機能を有効にする
description: この記事では、Power BI ビジュアルにスライサーの同期機能を追加する方法を説明します。
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 47f0148528d1ccfd451aa8e8ed87b4bec99d087e
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424311"
---
# <a name="sync-slicers-in-power-bi-visuals"></a><span data-ttu-id="af848-103">Power BI ビジュアルでのスライサーの同期</span><span class="sxs-lookup"><span data-stu-id="af848-103">Sync slicers in Power BI visuals</span></span>

<span data-ttu-id="af848-104">[スライサーの同期](https://docs.microsoft.com/power-bi/desktop-slicers)機能をサポートするには、カスタム スライサー ビジュアルで API バージョン 1.13 以降を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af848-104">To support the [Sync Slicers](https://docs.microsoft.com/power-bi/desktop-slicers) feature, your custom slicer visual must use API version 1.13 or later.</span></span>

<span data-ttu-id="af848-105">さらに、次のコードで示すように、*capabilities.json* ファイルでオプションを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="af848-105">Additionally, you need to enable the option in the *capabilities.json* file, as shown in the following code:</span></span>

```json
{
    ...
    "supportsHighlight": true,
    "suppressDefaultTitle": true,
    "supportsSynchronizingFilterState": true,
    "sorting": {
        "default": {}
    }
}
```

<span data-ttu-id="af848-106">*capabilities.json* ファイルを更新した後は、カスタム スライサー ビジュアルを選択したときに、 **[スライサーの同期]** オプション ウィンドウを表示できます。</span><span class="sxs-lookup"><span data-stu-id="af848-106">After you've updated the *capabilities.json* file, you can view the **Sync slicers** options pane when you select your custom slicer visual.</span></span>

> [!NOTE]
> <span data-ttu-id="af848-107">スライサーの同期機能では、複数のフィールドはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="af848-107">The Sync Slicers feature doesn't support more than one field.</span></span> <span data-ttu-id="af848-108">スライサーに複数のフィールド (**カテゴリ**または**メジャー**) がある場合、機能は無効になります。</span><span class="sxs-lookup"><span data-stu-id="af848-108">If your slicer has more than one field (**Category** or **Measure**), the feature is disabled.</span></span>

![[スライサーの同期] ウィンドウ](./media/sync-slicers-panel.png)

<span data-ttu-id="af848-110">**[スライサーの同期]** ウィンドウでは、スライサーの表示とそのフィルター処理を複数のレポート ページに適用できることがわかります。</span><span class="sxs-lookup"><span data-stu-id="af848-110">In the **Sync slicers** pane, you can see that your slicer visibility and its filtration can be applied to several report pages.</span></span>
