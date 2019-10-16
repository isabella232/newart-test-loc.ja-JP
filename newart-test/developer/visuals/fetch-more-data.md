---
title: より多くのデータを Power BI からフェッチする
description: この記事では、Power BI ビジュアルに対する大きいデータセットのセグメント化されたフェッチを有効にする方法を説明します。
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b67977abd93b3aa605430dd2d7fb516ca33bd51c
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424176"
---
# <a name="fetch-more-data-from-power-bi"></a><span data-ttu-id="22ad1-103">より多くのデータを Power BI からフェッチする</span><span class="sxs-lookup"><span data-stu-id="22ad1-103">Fetch more data from Power BI</span></span>

<span data-ttu-id="22ad1-104">この記事では、30 KB のデータ ポイントのハード制限を回避して、より多くのデータを読み込む方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="22ad1-104">This article discusses how to load more data to bypass the hard limit of a 30-KB data point.</span></span> <span data-ttu-id="22ad1-105">この方法では、データがチャンクで提供されます。</span><span class="sxs-lookup"><span data-stu-id="22ad1-105">This approach provides data in chunks.</span></span> <span data-ttu-id="22ad1-106">パフォーマンスを向上させるために、ユース ケースに合わせてチャンク サイズを構成できます。</span><span class="sxs-lookup"><span data-stu-id="22ad1-106">To improve performance, you can configure the chunk size to accommodate your use case.</span></span>  

## <a name="enable-a-segmented-fetch-of-large-datasets"></a><span data-ttu-id="22ad1-107">大きいデータセットのセグメント化されたフェッチを有効にする</span><span class="sxs-lookup"><span data-stu-id="22ad1-107">Enable a segmented fetch of large datasets</span></span>

<span data-ttu-id="22ad1-108">`dataview` セグメント モードでは、必要な dataViewMapping に対する dataReductionAlgorithm のウィンドウ サイズを、ビジュアルの *capabilities.json* ファイルで定義します。</span><span class="sxs-lookup"><span data-stu-id="22ad1-108">For the `dataview` segment mode, you define a window size for dataReductionAlgorithm in the visual's *capabilities.json* file for the required dataViewMapping.</span></span> <span data-ttu-id="22ad1-109">`count` によってウィンドウ サイズが決定され、更新ごとに `dataview` に追加できる新しいデータ行の数が制限されます。</span><span class="sxs-lookup"><span data-stu-id="22ad1-109">The `count` determines the window size, which limits the number of new data rows that can be appended to the `dataview` in each update.</span></span>

<span data-ttu-id="22ad1-110">次のコードを *capabilities.json* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="22ad1-110">Add the following code in the *capabilities.json* file:</span></span>

```typescript
"dataViewMappings": [
    {
        "table": {
            "rows": {
                "for": {
                    "in": "values"
                },
                "dataReductionAlgorithm": {
                    "window": {
                        "count": 100
                    }
                }
            }
    }
]
```

<span data-ttu-id="22ad1-111">新しいセグメントが既存の `dataview` に追加され、`update` 呼び出しとしてビジュアルに提供されます。</span><span class="sxs-lookup"><span data-stu-id="22ad1-111">New segments are appended to the existing `dataview` and provided to the visual as an `update` call.</span></span>

## <a name="usage-in-the-power-bi-visual"></a><span data-ttu-id="22ad1-112">Power BI ビジュアルでの使用</span><span class="sxs-lookup"><span data-stu-id="22ad1-112">Usage in the Power BI visual</span></span>

<span data-ttu-id="22ad1-113">`dataView.metadata.segment` の存在を調べることによって、データが存在するかどうかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="22ad1-113">You can determine whether data exists by checking the existence of `dataView.metadata.segment`:</span></span>

```typescript
    public update(options: VisualUpdateOptions) {
        const dataView = options.dataViews[0];
        console.log(dataView.metadata.segment);
        // output: __proto__: Object
    }
```

<span data-ttu-id="22ad1-114">また、`options.operationKind` を調べることによって、それが最初の更新か、それとも 2 回目以降の更新かを確認することもできます。</span><span class="sxs-lookup"><span data-stu-id="22ad1-114">You can also check to see whether it's the first update or a subsequent update by checking `options.operationKind`.</span></span> <span data-ttu-id="22ad1-115">次のコードで、`VisualDataChangeOperationKind.Create` では最初のセグメントが参照され、`VisualDataChangeOperationKind.Append` では後続のセグメントが参照されます。</span><span class="sxs-lookup"><span data-stu-id="22ad1-115">In the following code, `VisualDataChangeOperationKind.Create` refers to the first segment, and `VisualDataChangeOperationKind.Append` refers to subsequent segments.</span></span>

<span data-ttu-id="22ad1-116">実装のサンプルについては、次のコード スニペットを参照してください。</span><span class="sxs-lookup"><span data-stu-id="22ad1-116">For a sample implementation, see the following code snippet:</span></span>

```typescript
// CV update implementation
public update(options: VisualUpdateOptions) {
    // indicates this is the first segment of new data.
    if (options.operationKind == VisualDataChangeOperationKind.Create) {

    }

    // on second or subsequent segments:
    if (options.operationKind == VisualDataChangeOperationKind.Append) {

    }

    // complete update implementation
}
```

<span data-ttu-id="22ad1-117">次に示すように、UI イベント ハンドラーから `fetchMoreData` メソッドを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="22ad1-117">You can also invoke the `fetchMoreData` method from a UI event handler, as shown here:</span></span>

```typescript
btn_click(){
{
    // check if more data is expected for the current data view
    if (dataView.metadata.segment) {
        //request for more data if available; as a response, Power BI will call update method
        let request_accepted: bool = this.host.fetchMoreData();
        // handle rejection
        if (!request_accepted) {
            // for example, when the 100 MB limit has been reached
        }
    }
}
```

<span data-ttu-id="22ad1-118">`this.host.fetchMoreData` メソッドの呼び出しに対する応答として、Power BI では新しいデータ セグメントを使用してビジュアルの `update` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="22ad1-118">As a response to calling the `this.host.fetchMoreData` method, Power BI calls the `update` method of the visual with a new segment of data.</span></span>

> [!NOTE]
> <span data-ttu-id="22ad1-119">クライアントのメモリ制約を回避するため、Power BI では現在、フェッチされるデータの合計が 100 MB に制限されています。</span><span class="sxs-lookup"><span data-stu-id="22ad1-119">To avoid client memory constraints, Power BI currently limits the fetched data total to 100 MB.</span></span> <span data-ttu-id="22ad1-120">fetchMoreData() から `false` が返されることで、制限に達したことがわかります。</span><span class="sxs-lookup"><span data-stu-id="22ad1-120">You can see that the limit has been reached when fetchMoreData() returns `false`.</span></span>
