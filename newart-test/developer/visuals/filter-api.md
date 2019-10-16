---
title: Power BI ビジュアルでの Visual Filters API
description: この記事では、Power BI ビジュアルで他のビジュアルをフィルター処理する方法を説明します。
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 98ebc87cf5a6b7bf8f0b8b88d4ff498edfd5bf9a
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424546"
---
# <a name="the-visual-filters-api-in-power-bi-visuals"></a>Power BI ビジュアルでの Visual Filters API

Visual Filters API を使用すると、Power BI ビジュアルでデータをフィルター処理できます。 他の選択肢との主な違いは、他のビジュアルでの強調表示のサポートに関係なく、他のビジュアルが任意の方法でフィルター処理されることです。

ビジュアルのフィルター処理を有効にするには、*capabilities.json* のコードの `general` セクションに `filter` オブジェクトが含まれている必要があります。

```json
"objects": {
        "general": {
            "displayName": "General",
            "displayNameKey": "formattingGeneral",
            "properties": {
                "filter": {
                    "type": {
                        "filter": true
                    }
                }
            }
        }
    }
```

Visual Filters API のインターフェイスは、[powerbi-models](https://www.npmjs.com/package/powerbi-models) パッケージで使用できます。 このパッケージには、フィルター インスタンスを作成するためのクラスも含まれています。

```cmd
npm install powerbi-models --save
```

ツールの古いバージョン (3.x.x より前) を使用する場合は、ビジュアル パッケージに `powerbi-models` を含める必要があります。 詳細については、簡単なガイド「[Advanced Filter API をカスタム ビジュアルに追加する](https://github.com/Microsoft/powerbi-visuals-sampleslicer/blob/master/doc/AddingAdvancedFilterAPI.md)」を参照してください。

次のコードで示すように、すべてのフィルターでは `IFilter` インターフェイスが拡張されています。

```typescript
export interface IFilter {
    $schema: string;
    target: IFilterTarget;
}
```
ここで:
* `target` は、データ ソースのテーブル列です。

## <a name="the-basic-filter-api"></a>Basic Filter API

基本的なフィルターのインターフェイスは次のコードで示すとおりです。

```typescript
export interface IBasicFilter extends IFilter {
    operator: BasicFilterOperators;
    values: (string | number | boolean)[];
}
```

ここで:
* `operator` は列挙型であり、値は *In*、*NotIn*、*All* です。
* `values` は、条件の値です。

基本フィルターの例:

```typescript
let basicFilter = {
    target: {
        column: "Col1"
    },
    operator: "In",
    values: [1,2,3]
}
```

このフィルターは、"`col1` が値 1、2、または 3 に等しいすべての行を表示する" という意味です。

対応する SQL は次のとおりです。

```sql
SELECT * FROM table WHERE col1 IN ( 1 , 2 , 3 )
```

フィルターを作成するには、`powerbi-models` の BasicFilter クラスを使用します。

古いバージョンのツールを使用している場合は、次のコードで示すように、`window['powerbi-models']` を使用してウィンドウ オブジェクト内のモデルのインスタンスを取得する必要があります。

```javascript
let categories: DataViewCategoricalColumn = this.dataView.categorical.categories[0];

let target: IFilterColumnTarget = {
    table: categories.source.queryName.substr(0, categories.source.queryName.indexOf('.')),
    column: categories.source.displayName
};

let values = [ 1, 2, 3 ];

let filter: IBasicFilter = new window['powerbi-models'].BasicFilter(target, "In", values);
```

ビジュアルでフィルターを呼び出すには、コンストラクターでビジュアルに対して提供されるホスト インターフェイス IVisualHost の applyJsonFilter() メソッドを使用します。

```typescript
visualHost.applyJsonFilter(filter, "general", "filter", FilterAction.merge);
```

## <a name="the-advanced-filter-api"></a>Advanced Filter API

[Advanced Filter API](https://github.com/Microsoft/powerbi-models) を使用すると、複数の条件 (*LessThan*、*Contains*、*Is*、*IsBlank* など) に基づく複雑なクロスビジュアルのデータポイント選択およびフィルター処理のクエリが可能になります。

このフィルターは、ビジュアル API 1.7.0 で導入されました。

Advanced Filter API には、`table` および `column` の名前を指定した `target` も必要です。 ただし、Advanced Filter API の演算子は *And* と *Or* です。 

さらに、フィルターでは、インターフェイスでの値ではなく条件が使用されます。

```typescript
interface IAdvancedFilterCondition {
    value: (string | number | boolean);
    operator: AdvancedFilterConditionOperators;
}
```

`operator` パラメーターの条件演算子は、*None*、*LessThan*、*LessThanOrEqual*、*GreaterThan*、*GreaterThanOrEqual*、*Contains*、*DoesNotContain*、*StartsWith*、*DoesNotStartWith*、*Is*、*IsNot*、*IsBlank*、"IsNotBlank" です。

```javascript
let categories: DataViewCategoricalColumn = this.dataView.categorical.categories[0];

let target: IFilterColumnTarget = {
    table: categories.source.queryName.substr(0, categories.source.queryName.indexOf('.')), // table
    column: categories.source.displayName // col1
};

let conditions: IAdvancedFilterCondition[] = [];

conditions.push({
    operator: "LessThan",
    value: 0
});

let filter: IAdvancedFilter = new window['powerbi-models'].AdvancedFilter(target, "And", conditions);

// invoke the filter
visualHost.applyJsonFilter(filter, "general", "filter", FilterAction.merge);
```

対応する SQL は次のとおりです。

```sql
SELECT * FROM table WHERE col1 < 0;
```

Advanced Filter API の使用に関する完全なサンプル コードについては、[Sampleslicer ビジュアル リポジトリ](https://github.com/Microsoft/powerbi-visuals-sampleslicer)を参照してください。

## <a name="the-tuple-filter-api-multi-column-filter"></a>Tuple Filter API (複数列フィルター)

Tuple Filter API は、Visuals API 2.3.0 で導入されました。 Basic Filter API に似ていますが、複数の列とテーブルに対して条件を定義することができます。

そのフィルター インターフェイスは次のコードで示すとおりです。 

```typescript
interface ITupleFilter extends IFilter {
    $schema: string;
    filterType: FilterType;
    operator: TupleFilterOperators;
    target: ITupleFilterTarget;
    values: TupleValueType[];
}
```

ここで:
* `target` は、テーブル名を持つ列の配列です。

    ```typescript
    declare type ITupleFilterTarget = IFilterTarget[];
    ```

  そのフィルターは、さまざまなテーブルの列に対応できます。

* `$schema` は http://powerbi.com/product/schema#tuple です。

* `filterType` は *FilterType.Tuple* です。

* `operator` は *In* 演算子でのみ使用できます。

* `values` は値のタプルの配列であり、各タプルはターゲット列の値の許可された 1 つの組み合わせを表します。 

```typescript
declare type TupleValueType = ITupleElementValue[];

interface ITupleElementValue {
    value: PrimitiveValueType
}
```

完全な例:

```typescript
let target: ITupleFilterTarget = [
    {
        table: "DataTable",
        column: "Team"
    },
    {
        table: "DataTable",
        column: "Value"
    }
];

let values = [
    [
        // the first column combination value (or the column tuple/vector value) that the filter will pass through
        {
            value: "Team1" // the value for the `Team` column of the `DataTable` table
        },
        {
            value: 5 // the value for the `Value` column of the `DataTable` table
        }
    ],
    [
        // the second column combination value (or the column tuple/vector value) that the filter will pass through
        {
            value: "Team2" // the value for `Team` column of `DataTable` table
        },
        {
            value: 6 // the value for `Value` column of `DataTable` table
        }
    ]
];

let filter: ITupleFilter = {
    $schema: "http://powerbi.com/product/schema#tuple",
    filterType: FilterType.Tuple,
    operator: "In",
    target: target,
    values: values
}

// invoke the filter
visualHost.applyJsonFilter(filter, "general", "filter", FilterAction.merge);
```

> [!IMPORTANT]
> 列名と条件値の順序が重要です。

対応する SQL は次のとおりです。

```sql
SELECT * FROM DataTable WHERE ( Team = "Team1" AND Value = 5 ) OR ( Team = "Team2" AND Value = 6 );
```  

## <a name="restore-the-json-filter-from-the-data-view"></a>データ ビューから JSON フィルターを復元する

API バージョン 2.2 以降では、次のコードで示すように、*VisualUpdateOptions* から JSON フィルターを復元できます。

```typescript
export interface VisualUpdateOptions extends extensibility.VisualUpdateOptions {
    viewport: IViewport;
    dataViews: DataView[];
    type: VisualUpdateType;
    viewMode?: ViewMode;
    editMode?: EditMode;
    operationKind?: VisualDataChangeOperationKind;
    jsonFilters?: IFilter[];
}
```

ブックマークを切り替えると、Power BI によってビジュアルの `update` メソッドが呼び出されて、ビジュアルは対応する `filter` オブジェクトを取得します。 詳細については、「[Power BI ビジュアルのブックマーク サポートを追加する](bookmarks-support.md)」を参照してください。

### <a name="sample-json-filter"></a>JSON フィルターのサンプル

次の図では、JSON フィルターのコードのサンプルをいくつか示します。

![JSON フィルターのコード](./media/json-filter.png)

### <a name="clear-the-json-filter"></a>JSON フィルターをクリアする

Filter API では、フィルターの `null` 値を *reset* または *clear* として受け入れます。

```typescript
// invoke the filter
visualHost.applyJsonFilter(null, "general", "filter", FilterAction.merge);
```
