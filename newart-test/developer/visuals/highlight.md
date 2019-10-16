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
# <a name="highlight-data-points-in-power-bi-visuals"></a>Power BI ビジュアルでデータ ポイントを強調表示する

既定では、要素が選択されるたびに、`dataView` オブジェクト内の `values` 配列は、選択された値のみを示すようにフィルター処理されます。 これにより、ページ上の他のすべてのビジュアルには、選択したデータのみが表示されます。

![`dataview` の強調表示の既定の動作](./media/highlight-dataview.png)

`capabilities.json` の `supportsHighlight` プロパティを `true` に設定すると、フィルター処理されていない完全な `values` 配列が `highlights` 配列とともに返されます。 `highlights` 配列は values 配列と同じ長さになり、選択されていない values はすべて `null` に設定されます。 このプロパティを有効にすると、`values` 配列と `highlights` 配列を比較することによって適切なデータを強調表示する動作がビジュアル側で実行されます。

![supportshighlight を指定したときの `dataview` の強調表示](./media/highlight-dataview-supports.png)

この例では、選択された 1 つのバーがあることがわかります。 これは highlights 配列の唯一の値です。 また、複数選択されたり部分的な強調表示が存在したりする場合もあることに注意してください。 values には対応する数値があり、highlights 配列は存在しますが異なっています。
