---
title: Power BI ビジュアルのブックマーク サポートを追加する
description: Power BI ビジュアルではブックマークの切り替えを処理できます
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: c19b67a59d0ecb4cbfbcf5ad8dd18886f440e164
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424226"
---
# <a name="add-bookmark-support-for-power-bi-visuals"></a><span data-ttu-id="f7061-103">Power BI ビジュアルのブックマーク サポートを追加する</span><span class="sxs-lookup"><span data-stu-id="f7061-103">Add bookmark support for Power BI visuals</span></span>

<span data-ttu-id="f7061-104">Power BI レポートのブックマークを使用すると、レポート ページの構成済みビュー、選択の状態、およびビジュアルのフィルター処理の状態をキャプチャできます。</span><span class="sxs-lookup"><span data-stu-id="f7061-104">With Power BI report bookmarks, you can capture a configured view of a report page, the selection state, and the filtering state of the visual.</span></span> <span data-ttu-id="f7061-105">しかし、ブックマークをサポートし、変更に適切に対応するには、Power BI ビジュアル側からの追加のアクションが必要です。</span><span class="sxs-lookup"><span data-stu-id="f7061-105">But it requires additional action from the Power BI visuals side to support the bookmark and react correctly to changes.</span></span>

<span data-ttu-id="f7061-106">ブックマークの詳細については、「[Power BI でブックマークを使用して分析情報を共有し、ストーリーを作成する](https://docs.microsoft.com/power-bi/desktop-bookmarks)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f7061-106">For more information about bookmarks, see [Use bookmarks to share insights and build stories in Power BI](https://docs.microsoft.com/power-bi/desktop-bookmarks).</span></span>

## <a name="report-bookmarks-support-in-your-visual"></a><span data-ttu-id="f7061-107">ビジュアルでのレポート ブックマークのサポート</span><span class="sxs-lookup"><span data-stu-id="f7061-107">Report bookmarks support in your visual</span></span>

<span data-ttu-id="f7061-108">ご自分のビジュアルで他のビジュアルとやりとりしたり、データ ポイントを選択したり、他のビジュアルのフィルター処理を行う場合は、プロパティから状態を復元する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f7061-108">If your visual interacts with other visuals, selects data points, or filters other visuals, you need to restore the state from properties.</span></span>

## <a name="add-report-bookmarks-support"></a><span data-ttu-id="f7061-109">レポート ブックマーク サポートを追加する</span><span class="sxs-lookup"><span data-stu-id="f7061-109">Add report bookmarks support</span></span>

1. <span data-ttu-id="f7061-110">必要なユーティリティである [powerbi-visuals-utils-interactivityutils](https://github.com/Microsoft/PowerBI-visuals-utils-interactivityutils/) バージョン 3.0.0 以降をインストール (または更新) します。</span><span class="sxs-lookup"><span data-stu-id="f7061-110">Install (or update) the required utility, [powerbi-visuals-utils-interactivityutils](https://github.com/Microsoft/PowerBI-visuals-utils-interactivityutils/) version 3.0.0 or later.</span></span> <span data-ttu-id="f7061-111">これには、状態の選択またはフィルターで操作するための追加のクラスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f7061-111">It contains additional classes to manipulate with the state selection or filter.</span></span> <span data-ttu-id="f7061-112">これは、フィルター ビジュアルと `InteractivityService` を使用する任意のビジュアルに必要です。</span><span class="sxs-lookup"><span data-stu-id="f7061-112">It's required for filter visuals and any visual that uses `InteractivityService`.</span></span>

2. <span data-ttu-id="f7061-113">`SelectionManager` のインスタンス内で `registerOnSelectCallback` 使用するように、ビジュアルの API をバージョン 1.11.0 に更新します。</span><span class="sxs-lookup"><span data-stu-id="f7061-113">Update the visual API to version 1.11.0 to use `registerOnSelectCallback` in an instance of `SelectionManager`.</span></span> <span data-ttu-id="f7061-114">これは、`InteractivityService` ではなく、プレーンの `SelectionManager` を使用する非フィルター ビジュアルに必要です。</span><span class="sxs-lookup"><span data-stu-id="f7061-114">It's required for non-filter visuals that use the plain `SelectionManager` rather than `InteractivityService`.</span></span>

### <a name="how-power-bi-visuals-interact-with-power-bi-in-report-bookmarks"></a><span data-ttu-id="f7061-115">レポート ブックマークで Power BI ビジュアルと Power BI がやりとりする方法</span><span class="sxs-lookup"><span data-stu-id="f7061-115">How Power BI visuals interact with Power BI in report bookmarks</span></span>

<span data-ttu-id="f7061-116">次のシナリオについて考えてみましょう。レポート ページ上に複数のブックマークを作成し、各ブックマークでの選択状態をそれぞれ異なるものとします。</span><span class="sxs-lookup"><span data-stu-id="f7061-116">Let's consider the following scenario: you want to create several bookmarks on the report page, with a different selection state in each bookmark.</span></span>

<span data-ttu-id="f7061-117">まず、ご利用のビジュアル内でデータ ポイントを選択します。</span><span class="sxs-lookup"><span data-stu-id="f7061-117">First, you select a data point in your visual.</span></span> <span data-ttu-id="f7061-118">ビジュアルでは、ホストに選択を渡すことで、Power BI およびその他のビジュアルとやり取りします。</span><span class="sxs-lookup"><span data-stu-id="f7061-118">The visual interacts with Power BI and other visuals by passing selections to the host.</span></span> <span data-ttu-id="f7061-119">次に、 **[ブックマーク]** パネルで **[追加]** を選択します。Power BI によって新しいブックマークの現在の選択内容が保存されます。</span><span class="sxs-lookup"><span data-stu-id="f7061-119">You then select **Add** in the **Bookmark** pane, and Power BI saves the current selections for the new bookmark.</span></span>

<span data-ttu-id="f7061-120">選択内容を変更し、新しいブックマークを追加するときは、これが繰り返し行われます。</span><span class="sxs-lookup"><span data-stu-id="f7061-120">It happens repeatedly as you change the selection and add new bookmarks.</span></span> <span data-ttu-id="f7061-121">複数のブックマークを作成したら、それらを切り替えることができます。</span><span class="sxs-lookup"><span data-stu-id="f7061-121">After you create the bookmarks, you can switch between them.</span></span>

<span data-ttu-id="f7061-122">ブックマークを選択すると、Power BI によって保存されたフィルターまたは選択状態が復元され、ビジュアルに渡されます。</span><span class="sxs-lookup"><span data-stu-id="f7061-122">When you select a bookmark, Power BI restores the saved filter or selection state and passes it to the visuals.</span></span> <span data-ttu-id="f7061-123">他のビジュアルは、ブックマークに格納されている状態に応じて、強調表示されるか、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="f7061-123">Other visuals are highlighted or filtered according to the state that's stored in the bookmark.</span></span> <span data-ttu-id="f7061-124">Power BI ホストは、アクションを担当します。</span><span class="sxs-lookup"><span data-stu-id="f7061-124">The Power BI host is responsible for the actions.</span></span> <span data-ttu-id="f7061-125">ご利用のビジュアルでは、新しい選択状態 (レンダリングされるデータ ポイントの色を変更する場合など) を正しく反映する役割を果たします。</span><span class="sxs-lookup"><span data-stu-id="f7061-125">Your visual is responsible for correctly reflecting the new selection state (for example, for changing the colors of rendered data points).</span></span>

<span data-ttu-id="f7061-126">新しい選択状態は、`update` メソッドを介してビジュアルに伝達されます。</span><span class="sxs-lookup"><span data-stu-id="f7061-126">The new selection state is communicated to the visual through the `update` method.</span></span> <span data-ttu-id="f7061-127">`options` 引数には、特殊なプロパティ `options.jsonFilters` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f7061-127">The `options` argument contains a special property, `options.jsonFilters`.</span></span> <span data-ttu-id="f7061-128">その JSONFilter プロパティには `Advanced Filter` および `Tuple Filter` を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f7061-128">Its JSONFilter property can contain `Advanced Filter` and `Tuple Filter`.</span></span>

<span data-ttu-id="f7061-129">ビジュアルでは、フィルター値を復元することで、選択されたブックマークに対応するビジュアルの状態が表示される必要があります。</span><span class="sxs-lookup"><span data-stu-id="f7061-129">The visual should restore the filter values to display the corresponding state of the visual for the selected bookmark.</span></span> <span data-ttu-id="f7061-130">または、ビジュアルで選択のみが使用される場合、ISelectionManager の `registerOnSelectCallback` メソッドとして登録されている特殊なコールバック関数呼び出しを使用します。</span><span class="sxs-lookup"><span data-stu-id="f7061-130">Or, if the visual uses selections only, you can use the special callback function call that's registered as the `registerOnSelectCallback` method of ISelectionManager.</span></span>

### <a name="visuals-with-selection"></a><span data-ttu-id="f7061-131">選択を使用するビジュアル</span><span class="sxs-lookup"><span data-stu-id="f7061-131">Visuals with Selection</span></span>

<span data-ttu-id="f7061-132">ご利用のビジュアルが[選択](https://github.com/Microsoft/PowerBI-visuals/blob/master/Tutorial/Selection.md)を使用して他のビジュアルとやりとりする場合は、次の 2 つの方法のいずれかでブックマークを追加できます。</span><span class="sxs-lookup"><span data-stu-id="f7061-132">If your visual interacts with other visuals by using [Selection](https://github.com/Microsoft/PowerBI-visuals/blob/master/Tutorial/Selection.md), you can add bookmarks in either of two ways:</span></span>

* <span data-ttu-id="f7061-133">[InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md) がビジュアルでまだ使用されていない場合は、`FilterManager.restoreSelectionIds` メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="f7061-133">If the visual hasn't already used [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md), you can use the `FilterManager.restoreSelectionIds` method.</span></span>

* <span data-ttu-id="f7061-134">選択を管理するために [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md) がビジュアルで既に使用されている場合は、`InteractivityService` のインスタンス内で `applySelectionFromFilter` メソッドを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f7061-134">If the visual already uses [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md) to manage selections, you should use the `applySelectionFromFilter` method in the instance of `InteractivityService`.</span></span>

#### <a name="use-iselectionmanagerregisteronselectcallback"></a><span data-ttu-id="f7061-135">ISelectionManager.registerOnSelectCallback を使用する</span><span class="sxs-lookup"><span data-stu-id="f7061-135">Use ISelectionManager.registerOnSelectCallback</span></span>

<span data-ttu-id="f7061-136">ブックマークを選択すると、Power BI によって、ビジュアルの `callback` メソッドが対応する選択と共に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f7061-136">When you select a bookmark, Power BI calls the `callback` method of the visual with the corresponding selections.</span></span> 

```typescript
this.selectionManager.registerOnSelectCallback(
    (ids: ISelectionId[]) => {
        //called when a selection was set by Power BI
    });
);
```

<span data-ttu-id="f7061-137">[visualTransform](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/blob/master/src/barChart.ts#L74) メソッドで作成されたビジュアル内にデータ ポイントがあると仮定してみましょう。</span><span class="sxs-lookup"><span data-stu-id="f7061-137">Let's assume that you have a data point in your visual that was created in the [visualTransform](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/blob/master/src/barChart.ts#L74) method.</span></span>

<span data-ttu-id="f7061-138">`datapoints` は次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="f7061-138">And `datapoints` looks like:</span></span>

```typescript
visualDataPoints.push({
    category: categorical.categories[0].values[i],
    color: getCategoricalObjectValue<Fill>(categorical.categories[0], i, 'colorSelector', 'fill', defaultColor).solid.color,
    selectionId: host.createSelectionIdBuilder()
        .withCategory(categorical.categories[0], i)
        .createSelectionId(),
    selected: false
});
```

<span data-ttu-id="f7061-139">これで、ご自分のデータ ポイントとして `visualDataPoints` が用意され、`ids` 配列が `callback` 関数に渡されます。</span><span class="sxs-lookup"><span data-stu-id="f7061-139">You now have `visualDataPoints` as your data points and the `ids` array passed to the `callback` function.</span></span>

<span data-ttu-id="f7061-140">この時点で、ビジュアルでは `ISelectionId[]` 配列と、ご利用の `visualDataPoints` 配列内の選択を比較し、対応するデータ ポイントを選択済みとしてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f7061-140">At this point, the visual should compare the `ISelectionId[]` array with the selections in your `visualDataPoints` array and mark the corresponding data points as selected.</span></span>

```typescript
this.selectionManager.registerOnSelectCallback(
    (ids: ISelectionId[]) => {
        visualDataPoints.forEach(dataPoint => {
            ids.forEach(bookmarkSelection => {
                if (bookmarkSelection.equals(dataPoint.selectionId)) {
                    dataPoint.selected = true;
                }
            });
        });
    });
);
```

<span data-ttu-id="f7061-141">データ ポイントを更新したら、`filter` オブジェクトに格納されている現在の選択状態が反映されます。</span><span class="sxs-lookup"><span data-stu-id="f7061-141">After you update the data points, they'll reflect the current selection state that's stored in the `filter` object.</span></span> <span data-ttu-id="f7061-142">これで、データ ポイントがレンダリングされると、カスタム ビジュアルの選択状態がブックマークの状態と一致するようになります。</span><span class="sxs-lookup"><span data-stu-id="f7061-142">Then, when the data points are rendered, the custom visual's selection state will match the state of the bookmark.</span></span>

### <a name="use-interactivityservice-for-control-selections-in-the-visual"></a><span data-ttu-id="f7061-143">ビジュアルでのコントロールの選択に InteractivityService を使用する</span><span class="sxs-lookup"><span data-stu-id="f7061-143">Use InteractivityService for control selections in the visual</span></span>

<span data-ttu-id="f7061-144">ビジュアルで `InteractivityService` が使用されている場合、そのビジュアルでブックマークをサポートするための追加の操作は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="f7061-144">If your visual uses `InteractivityService`, you don't need any additional actions to support the bookmarks in your visual.</span></span>

<span data-ttu-id="f7061-145">ブックマークを選択すると、ビジュアルの選択状態がユーティリティによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="f7061-145">When you select bookmarks, the utility handles the visual's selection state.</span></span>

### <a name="visuals-with-a-filter"></a><span data-ttu-id="f7061-146">フィルターを使用するビジュアル</span><span class="sxs-lookup"><span data-stu-id="f7061-146">Visuals with a filter</span></span>

<span data-ttu-id="f7061-147">ビジュアルによって、データのフィルターが日付範囲で作成されるとします。</span><span class="sxs-lookup"><span data-stu-id="f7061-147">Let's assume that the visual creates a filter of data by date range.</span></span> <span data-ttu-id="f7061-148">範囲の開始日と終了日として `startDate` と `endDate` があります。</span><span class="sxs-lookup"><span data-stu-id="f7061-148">You have `startDate` and `endDate` as the start and end dates of the range.</span></span>

<span data-ttu-id="f7061-149">関連条件でデータをフィルター処理する場合、ビジュアルでは高度なフィルターが作成され、ホスト メソッド `applyJsonFilter` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f7061-149">The visual creates an advanced filter and calls the host method `applyJsonFilter` to filter data by the relevant conditions.</span></span>

<span data-ttu-id="f7061-150">ターゲットは、フィルター処理に使用されるテーブルです。</span><span class="sxs-lookup"><span data-stu-id="f7061-150">The target is the table that's used for filtering.</span></span>

```typescript
import { AdvancedFilter } from "powerbi-models";

const filter: IAdvancedFilter = new AdvancedFilter(
    target,
    "And",
    {
        operator: "GreaterThanOrEqual",
        value: startDate
            ? startDate.toJSON()
            : null
    },
    {
        operator: "LessThanOrEqual",
        value: endDate
            ? endDate.toJSON()
            : null
    });

this.host.applyJsonFilter(
    filter,
    "general",
    "filter",
    (startDate && endDate)
        ? FilterAction.merge
        : FilterAction.remove
);
```

<span data-ttu-id="f7061-151">ブックマークをクリックするたびに、カスタム ビジュアルによって `update` 呼び出しが取得されます。</span><span class="sxs-lookup"><span data-stu-id="f7061-151">Each time you select a bookmark, the custom visual gets an `update` call.</span></span>

<span data-ttu-id="f7061-152">カスタム ビジュアルでは、オブジェクト内のフィルターを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f7061-152">The custom visual should check the filter in the object:</span></span>

```typescript
const filter: IAdvancedFilter = FilterManager.restoreFilter(
    && options.jsonFilters
    && options.jsonFilters[0] as any
) as IAdvancedFilter;
```

<span data-ttu-id="f7061-153">`filter` オブジェクトが null でない場合、ビジュアルではオブジェクトからフィルター条件を復元する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f7061-153">If the `filter` object isn't null, the visual should restore the filter conditions from the object:</span></span>

```typescript
const jsonFilters: AdvancedFilter = this.options.jsonFilters as AdvancedFilter[];

if (jsonFilters
    && jsonFilters[0]
    && jsonFilters[0].conditions
    && jsonFilters[0].conditions[0]
    && jsonFilters[0].conditions[1]
) {
    const startDate: Date = new Date(`${jsonFilters[0].conditions[0].value}`);
    const endDate: Date = new Date(`${jsonFilters[0].conditions[1].value}`);

    // apply restored conditions
} else {
    // apply default settings
}
```

<span data-ttu-id="f7061-154">その後、ビジュアルでは、現在の条件を反映するように、内部状態を変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f7061-154">After that, the visual should change its internal state to reflect the current conditions.</span></span> <span data-ttu-id="f7061-155">内部状態には、データ ポイントと視覚エフェクト オブジェクト (線や四角形など) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f7061-155">The internal state includes the data points and visualization objects (lines, rectangles, and so on).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f7061-156">レポート ブックマークのシナリオでは、ビジュアルで `applyJsonFilter` を呼び出して他のビジュアルをフィルター処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f7061-156">In the report bookmarks scenario, the visual shouldn't call `applyJsonFilter` to filter the other visuals.</span></span> <span data-ttu-id="f7061-157">これらは、既に Power BI によってフィルター処理されています。</span><span class="sxs-lookup"><span data-stu-id="f7061-157">They will already be filtered by Power BI.</span></span>

<span data-ttu-id="f7061-158">タイムライン スライサー ビジュアルでは、範囲セレクターが、対応するデータ範囲に変更されます。</span><span class="sxs-lookup"><span data-stu-id="f7061-158">The Timeline Slicer visual changes the range selector to the corresponding data ranges.</span></span>

<span data-ttu-id="f7061-159">詳細については、[タイムライン スライサー リポジトリ](https://github.com/Microsoft/powerbi-visuals-timeline/commit/606f1152f59f82b5b5a367ff3b117372d129e597?diff=unified#diff-b6ef9a9ac3a3225f8bd0de84bee0a0df)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f7061-159">For more information, see the [Timeline Slicer repository](https://github.com/Microsoft/powerbi-visuals-timeline/commit/606f1152f59f82b5b5a367ff3b117372d129e597?diff=unified#diff-b6ef9a9ac3a3225f8bd0de84bee0a0df).</span></span>

### <a name="filter-the-state-to-save-visual-properties-in-bookmarks"></a><span data-ttu-id="f7061-160">状態をフィルター処理してビジュアル プロパティをブックマークに保存する</span><span class="sxs-lookup"><span data-stu-id="f7061-160">Filter the state to save visual properties in bookmarks</span></span>

<span data-ttu-id="f7061-161">`filterState` プロパティでは、プロパティがフィルター処理の一部と見なされます。</span><span class="sxs-lookup"><span data-stu-id="f7061-161">The `filterState` property makes a property of a part of filtering.</span></span> <span data-ttu-id="f7061-162">ビジュアルでは、さまざまな値をブックマークに格納できます。</span><span class="sxs-lookup"><span data-stu-id="f7061-162">The visual can store a variety of values in bookmarks.</span></span>

<span data-ttu-id="f7061-163">プロパティ値をフィルター状態として保存するには、*capabilities.json* ファイル内でオブジェクト プロパティを `"filterState": true` としてマークします。</span><span class="sxs-lookup"><span data-stu-id="f7061-163">To save the property value as a filter state, mark the object property as `"filterState": true` in the *capabilities.json* file.</span></span>

<span data-ttu-id="f7061-164">たとえば、タイムライン スライサーでは、`Granularity` プロパティの値がフィルターに格納されます。</span><span class="sxs-lookup"><span data-stu-id="f7061-164">For example, the Timeline Slicer stores the `Granularity` property values in a filter.</span></span> <span data-ttu-id="f7061-165">これにより、ブックマークを変更するときに、現在の粒度を変更できます。</span><span class="sxs-lookup"><span data-stu-id="f7061-165">It allows the current granularity to change as you change the bookmarks.</span></span>

<span data-ttu-id="f7061-166">詳細については、[タイムライン スライサー リポジトリ](https://github.com/microsoft/powerbi-visuals-timeline/commit/8b7d82dd23cd2bd71817f1bc5d1e1732347a185e#diff-290828b604cfa62f1cb310f2e90c52fdR334)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f7061-166">For more information, see the [Timeline Slicer repository](https://github.com/microsoft/powerbi-visuals-timeline/commit/8b7d82dd23cd2bd71817f1bc5d1e1732347a185e#diff-290828b604cfa62f1cb310f2e90c52fdR334).</span></span>
