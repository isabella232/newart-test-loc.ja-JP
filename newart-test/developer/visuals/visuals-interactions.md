---
title: Power BI ビジュアルでのビジュアル対話
description: この記事では、Power BI ビジュアルでビジュアル対話を許可する必要があるかどうかを確認する方法について説明します。
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 0155f0fce1bc0fec5c96aef1c7e1dc9cf64b122f
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424641"
---
# <a name="visual-interactions-in-power-bi-visuals"></a><span data-ttu-id="47b43-103">Power BI ビジュアルでのビジュアル対話</span><span class="sxs-lookup"><span data-stu-id="47b43-103">Visual interactions in Power BI visuals</span></span>

<span data-ttu-id="47b43-104">ビジュアルでは、`allowInteractions` フラグの値のクエリを実行できます。このフラグでは、ビジュアルでビジュアル対話を許可する必要があるかどうかが示されています。</span><span class="sxs-lookup"><span data-stu-id="47b43-104">Visuals can query the value of the `allowInteractions` flag, which indicates whether the visual should allow visual interactions.</span></span> <span data-ttu-id="47b43-105">たとえば、ビジュアルはレポートの表示や編集の間は対話形式になりますが、ダッシュボードで表示されるときは対話形式ではありません。</span><span class="sxs-lookup"><span data-stu-id="47b43-105">For example, visuals are interactive during report viewing or editing, but not interactive when they're viewed in a dashboard.</span></span> <span data-ttu-id="47b43-106">このような操作には、"*クリック*"、"*パン*"、"*ズーム*"、"*選択*" などがあります。</span><span class="sxs-lookup"><span data-stu-id="47b43-106">These interactions are *click*, *pan*, *zoom*, *selection*, and others.</span></span> 

> [!NOTE]
> <span data-ttu-id="47b43-107">どのフラグが指定されているかに関係なく、すべてのシナリオでツールヒントを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="47b43-107">You should enable tooltips in all scenarios, regardless of which flag is indicated.</span></span>

<span data-ttu-id="47b43-108">`allowInteractions` フラグは、IVisualHost インターフェイスのメンバーとして、ビジュアルの初期化の間に、ブール値として渡されます。</span><span class="sxs-lookup"><span data-stu-id="47b43-108">The `allowInteractions` flag is passed as a Boolean during the initialization of the visual, as a member of the IVisualHost interface.</span></span>

<span data-ttu-id="47b43-109">ビジュアルが対話形式ではない必要がある Power BI のすべてのシナリオにおいて (ダッシュボード タイルなど)、`allowInteractions` フラグは `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="47b43-109">In any Power BI scenario that requires that the visuals not be interactive (for example, dashboard tiles), the `allowInteractions` flag is set to `false`.</span></span> <span data-ttu-id="47b43-110">それ以外の場合 (レポートなど)、`allowInteractions` は `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="47b43-110">Otherwise (for example, Report), `allowInteractions` is set to `true`.</span></span>

<span data-ttu-id="47b43-111">詳細については、[SampleBarChart ビジュアル リポジトリ](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/commit/59a47935d8f5272ce145fe804193599ddb7e2001)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="47b43-111">For more information, see the [SampleBarChart visual repository](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/commit/59a47935d8f5272ce145fe804193599ddb7e2001).</span></span>

```typescript
   ...
   let allowInteractions = options.host.allowInteractions;
   bars.on('click', function(d) {
       if (allowInteractions) {
           selectionManager.select(d.selectionId);
           ...
       }
   });
```
