---
title: 強調表示
description: Power BI ビジュアルでのデータ ポイント選択項目の強調表示
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 4ff2187dc99d4e790b08c11f55a37e31e85693ad
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424281"
---
# <a name="highlight-data-points-in-power-bi-visuals"></a><span data-ttu-id="80880-103">Power BI ビジュアルでデータ ポイントを強調表示する</span><span class="sxs-lookup"><span data-stu-id="80880-103">Highlight data points in Power BI Visuals</span></span>

<span data-ttu-id="80880-104">既定では、要素が選択されるたびに、`dataView` オブジェクト内の `values` 配列は、選択された値のみを示すようにフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="80880-104">By default whenever an element is selected the `values` array in the `dataView` object will be filtered to just the selected values.</span></span> <span data-ttu-id="80880-105">これにより、ページ上の他のすべてのビジュアルには、選択したデータのみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="80880-105">It will cause all other visuals on the page to display just the selected data.</span></span>

![`dataview` の強調表示の既定の動作](./media/highlight-dataview.png)

<span data-ttu-id="80880-107">`capabilities.json` の `supportsHighlight` プロパティを `true` に設定すると、フィルター処理されていない完全な `values` 配列が `highlights` 配列とともに返されます。</span><span class="sxs-lookup"><span data-stu-id="80880-107">If you set the `supportsHighlight` property in your `capabilities.json` to `true`, you'll receive the full unfiltered `values` array along with a `highlights` array.</span></span> <span data-ttu-id="80880-108">`highlights` 配列は values 配列と同じ長さになり、選択されていない values はすべて `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="80880-108">The `highlights` array will be the same length as the values array and any non-selected values will be set to `null`.</span></span> <span data-ttu-id="80880-109">このプロパティを有効にすると、`values` 配列と `highlights` 配列を比較することによって適切なデータを強調表示する動作がビジュアル側で実行されます。</span><span class="sxs-lookup"><span data-stu-id="80880-109">With this property enabled it's the visual's responsibility to highlight the appropriate data by comparing the `values` array to the `highlights` array.</span></span>

![supportshighlight を指定したときの `dataview` の強調表示](./media/highlight-dataview-supports.png)

<span data-ttu-id="80880-111">この例では、選択された 1 つのバーがあることがわかります。</span><span class="sxs-lookup"><span data-stu-id="80880-111">In the example, you'll notice the 1 bar that is selected.</span></span> <span data-ttu-id="80880-112">これは highlights 配列の唯一の値です。</span><span class="sxs-lookup"><span data-stu-id="80880-112">And it's the only value in the highlights array.</span></span> <span data-ttu-id="80880-113">また、複数選択されたり部分的な強調表示が存在したりする場合もあることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="80880-113">It's also important to note there could be multiple selections and partial highlight.</span></span> <span data-ttu-id="80880-114">values には対応する数値があり、highlights 配列は存在しますが異なっています。</span><span class="sxs-lookup"><span data-stu-id="80880-114">There's the corresponding numeric value in the values and highlights arrays will be present but different.</span></span>
