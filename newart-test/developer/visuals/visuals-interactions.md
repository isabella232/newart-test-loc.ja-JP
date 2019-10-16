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
# <a name="visual-interactions-in-power-bi-visuals"></a>Power BI ビジュアルでのビジュアル対話

ビジュアルでは、`allowInteractions` フラグの値のクエリを実行できます。このフラグでは、ビジュアルでビジュアル対話を許可する必要があるかどうかが示されています。 たとえば、ビジュアルはレポートの表示や編集の間は対話形式になりますが、ダッシュボードで表示されるときは対話形式ではありません。 このような操作には、"*クリック*"、"*パン*"、"*ズーム*"、"*選択*" などがあります。 

> [!NOTE]
> どのフラグが指定されているかに関係なく、すべてのシナリオでツールヒントを有効にする必要があります。

`allowInteractions` フラグは、IVisualHost インターフェイスのメンバーとして、ビジュアルの初期化の間に、ブール値として渡されます。

ビジュアルが対話形式ではない必要がある Power BI のすべてのシナリオにおいて (ダッシュボード タイルなど)、`allowInteractions` フラグは `false` に設定されます。 それ以外の場合 (レポートなど)、`allowInteractions` は `true` に設定されます。

詳細については、[SampleBarChart ビジュアル リポジトリ](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/commit/59a47935d8f5272ce145fe804193599ddb7e2001)に関するページを参照してください。

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
