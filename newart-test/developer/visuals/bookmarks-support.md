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
# <a name="add-bookmark-support-for-power-bi-visuals"></a>Power BI ビジュアルのブックマーク サポートを追加する

Power BI レポートのブックマークを使用すると、レポート ページの構成済みビュー、選択の状態、およびビジュアルのフィルター処理の状態をキャプチャできます。 しかし、ブックマークをサポートし、変更に適切に対応するには、Power BI ビジュアル側からの追加のアクションが必要です。

ブックマークの詳細については、「[Power BI でブックマークを使用して分析情報を共有し、ストーリーを作成する](https://docs.microsoft.com/power-bi/desktop-bookmarks)」を参照してください。

## <a name="report-bookmarks-support-in-your-visual"></a>ビジュアルでのレポート ブックマークのサポート

ご自分のビジュアルで他のビジュアルとやりとりしたり、データ ポイントを選択したり、他のビジュアルのフィルター処理を行う場合は、プロパティから状態を復元する必要があります。

## <a name="add-report-bookmarks-support"></a>レポート ブックマーク サポートを追加する

1. 必要なユーティリティである [powerbi-visuals-utils-interactivityutils](https://github.com/Microsoft/PowerBI-visuals-utils-interactivityutils/) バージョン 3.0.0 以降をインストール (または更新) します。 これには、状態の選択またはフィルターで操作するための追加のクラスが含まれています。 これは、フィルター ビジュアルと `InteractivityService` を使用する任意のビジュアルに必要です。

2. `SelectionManager` のインスタンス内で `registerOnSelectCallback` 使用するように、ビジュアルの API をバージョン 1.11.0 に更新します。 これは、`InteractivityService` ではなく、プレーンの `SelectionManager` を使用する非フィルター ビジュアルに必要です。

### <a name="how-power-bi-visuals-interact-with-power-bi-in-report-bookmarks"></a>レポート ブックマークで Power BI ビジュアルと Power BI がやりとりする方法

次のシナリオについて考えてみましょう。レポート ページ上に複数のブックマークを作成し、各ブックマークでの選択状態をそれぞれ異なるものとします。

まず、ご利用のビジュアル内でデータ ポイントを選択します。 ビジュアルでは、ホストに選択を渡すことで、Power BI およびその他のビジュアルとやり取りします。 次に、 **[ブックマーク]** パネルで **[追加]** を選択します。Power BI によって新しいブックマークの現在の選択内容が保存されます。

選択内容を変更し、新しいブックマークを追加するときは、これが繰り返し行われます。 複数のブックマークを作成したら、それらを切り替えることができます。

ブックマークを選択すると、Power BI によって保存されたフィルターまたは選択状態が復元され、ビジュアルに渡されます。 他のビジュアルは、ブックマークに格納されている状態に応じて、強調表示されるか、フィルター処理されます。 Power BI ホストは、アクションを担当します。 ご利用のビジュアルでは、新しい選択状態 (レンダリングされるデータ ポイントの色を変更する場合など) を正しく反映する役割を果たします。

新しい選択状態は、`update` メソッドを介してビジュアルに伝達されます。 `options` 引数には、特殊なプロパティ `options.jsonFilters` が含まれます。 その JSONFilter プロパティには `Advanced Filter` および `Tuple Filter` を含めることができます。

ビジュアルでは、フィルター値を復元することで、選択されたブックマークに対応するビジュアルの状態が表示される必要があります。 または、ビジュアルで選択のみが使用される場合、ISelectionManager の `registerOnSelectCallback` メソッドとして登録されている特殊なコールバック関数呼び出しを使用します。

### <a name="visuals-with-selection"></a>選択を使用するビジュアル

ご利用のビジュアルが[選択](https://github.com/Microsoft/PowerBI-visuals/blob/master/Tutorial/Selection.md)を使用して他のビジュアルとやりとりする場合は、次の 2 つの方法のいずれかでブックマークを追加できます。

* [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md) がビジュアルでまだ使用されていない場合は、`FilterManager.restoreSelectionIds` メソッドを使用できます。

* 選択を管理するために [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md) がビジュアルで既に使用されている場合は、`InteractivityService` のインスタンス内で `applySelectionFromFilter` メソッドを使用する必要があります。

#### <a name="use-iselectionmanagerregisteronselectcallback"></a>ISelectionManager.registerOnSelectCallback を使用する

ブックマークを選択すると、Power BI によって、ビジュアルの `callback` メソッドが対応する選択と共に呼び出されます。 

```typescript
this.selectionManager.registerOnSelectCallback(
    (ids: ISelectionId[]) => {
        //called when a selection was set by Power BI
    });
);
```

[visualTransform](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/blob/master/src/barChart.ts#L74) メソッドで作成されたビジュアル内にデータ ポイントがあると仮定してみましょう。

`datapoints` は次のようになっています。

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

これで、ご自分のデータ ポイントとして `visualDataPoints` が用意され、`ids` 配列が `callback` 関数に渡されます。

この時点で、ビジュアルでは `ISelectionId[]` 配列と、ご利用の `visualDataPoints` 配列内の選択を比較し、対応するデータ ポイントを選択済みとしてマークする必要があります。

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

データ ポイントを更新したら、`filter` オブジェクトに格納されている現在の選択状態が反映されます。 これで、データ ポイントがレンダリングされると、カスタム ビジュアルの選択状態がブックマークの状態と一致するようになります。

### <a name="use-interactivityservice-for-control-selections-in-the-visual"></a>ビジュアルでのコントロールの選択に InteractivityService を使用する

ビジュアルで `InteractivityService` が使用されている場合、そのビジュアルでブックマークをサポートするための追加の操作は必要ありません。

ブックマークを選択すると、ビジュアルの選択状態がユーティリティによって処理されます。

### <a name="visuals-with-a-filter"></a>フィルターを使用するビジュアル

ビジュアルによって、データのフィルターが日付範囲で作成されるとします。 範囲の開始日と終了日として `startDate` と `endDate` があります。

関連条件でデータをフィルター処理する場合、ビジュアルでは高度なフィルターが作成され、ホスト メソッド `applyJsonFilter` が呼び出されます。

ターゲットは、フィルター処理に使用されるテーブルです。

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

ブックマークをクリックするたびに、カスタム ビジュアルによって `update` 呼び出しが取得されます。

カスタム ビジュアルでは、オブジェクト内のフィルターを確認する必要があります。

```typescript
const filter: IAdvancedFilter = FilterManager.restoreFilter(
    && options.jsonFilters
    && options.jsonFilters[0] as any
) as IAdvancedFilter;
```

`filter` オブジェクトが null でない場合、ビジュアルではオブジェクトからフィルター条件を復元する必要があります。

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

その後、ビジュアルでは、現在の条件を反映するように、内部状態を変更する必要があります。 内部状態には、データ ポイントと視覚エフェクト オブジェクト (線や四角形など) が含まれます。

> [!IMPORTANT]
> レポート ブックマークのシナリオでは、ビジュアルで `applyJsonFilter` を呼び出して他のビジュアルをフィルター処理する必要はありません。 これらは、既に Power BI によってフィルター処理されています。

タイムライン スライサー ビジュアルでは、範囲セレクターが、対応するデータ範囲に変更されます。

詳細については、[タイムライン スライサー リポジトリ](https://github.com/Microsoft/powerbi-visuals-timeline/commit/606f1152f59f82b5b5a367ff3b117372d129e597?diff=unified#diff-b6ef9a9ac3a3225f8bd0de84bee0a0df)に関するページを参照してください。

### <a name="filter-the-state-to-save-visual-properties-in-bookmarks"></a>状態をフィルター処理してビジュアル プロパティをブックマークに保存する

`filterState` プロパティでは、プロパティがフィルター処理の一部と見なされます。 ビジュアルでは、さまざまな値をブックマークに格納できます。

プロパティ値をフィルター状態として保存するには、*capabilities.json* ファイル内でオブジェクト プロパティを `"filterState": true` としてマークします。

たとえば、タイムライン スライサーでは、`Granularity` プロパティの値がフィルターに格納されます。 これにより、ブックマークを変更するときに、現在の粒度を変更できます。

詳細については、[タイムライン スライサー リポジトリ](https://github.com/microsoft/powerbi-visuals-timeline/commit/8b7d82dd23cd2bd71817f1bc5d1e1732347a185e#diff-290828b604cfa62f1cb310f2e90c52fdR334)に関するページを参照してください。
