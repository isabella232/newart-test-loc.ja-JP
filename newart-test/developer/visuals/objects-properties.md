---
title: Power BI ビジュアルのオブジェクトとプロパティ
description: この記事では、Power BI ビジュアルのカスタマイズ可能なプロパティについて説明します。
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b2043c6727e4cf8c5c46c4e277b01a9ea04a969b
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424121"
---
# <a name="objects-and-properties-of-power-bi-visuals"></a>Power BI ビジュアルのオブジェクトとプロパティ

オブジェクトでは、ビジュアルに関連付けられているカスタマイズ可能なプロパティが記述されています。 オブジェクトは複数のプロパティを持つことができ、各プロパティにはプロパティの内容を示す型が関連付けられています。 この記事では、オブジェクトとプロパティの型について説明します。

`myCustomObject` は、`dataView` および `enumerateObjectInstances` 内のオブジェクトを参照するために使用される内部名です

```json
"objects": {
    "myCustomObject": {
        "displayName": "My Object Name",
        "properties": { ... }
    }
}
```

## <a name="display-name"></a>表示名

`displayName` は、プロパティ ウィンドウに表示される名前です。

## <a name="properties"></a>プロパティ

`properties` は、開発者によって定義されるプロパティのマップです。

```json
"properties": {
    "myFirstProperty": {
        "displayName": "firstPropertyName",
        "type": ValueTypeDescriptor | StructuralTypeDescriptor
    }
}
```

> [!NOTE]
> `show` は、スイッチを使用してオブジェクトを切り替えることができる特殊なプロパティです。

例:

```json
"properties": {
    "show": {
        "displayName": "My Property Switch",
        "type": {"bool": true}
    }
}
```

### <a name="property-types"></a>プロパティの型

プロパティには、`ValueTypeDescriptor` と `StructuralTypeDescriptor` の 2 つの型があります。

#### <a name="value-type-descriptor"></a>値型記述子

`ValueTypeDescriptor` 型は、通常はプリミティブであり、通常は静的オブジェクトとして使用されます。

一般的な `ValueTypeDescriptor` 要素をいくつか次に示します。

```typescript
export interface ValueTypeDescriptor {
    text?: boolean;
    numeric?: boolean;
    integer?: boolean;
    bool?: boolean;
}
```

#### <a name="structural-type-descriptor"></a>構造型記述子

`StructuralTypeDescriptor` 型は、主に、データ バインドされたオブジェクトに使用されます。
最も一般的な `StructuralTypeDescriptor` 型は *fill* です。

```typescript
export interface StructuralTypeDescriptor {
    fill?: FillTypeDescriptor;
}
```

## <a name="gradient-property"></a>グラデーション プロパティ

グラデーション プロパティは、標準プロパティとして設定できないプロパティです。 代わりに、カラー ピッカー プロパティ (*fill* 型) の置換に関する規則を設定する必要があります。

次のコードで例を示します。

```json
"properties": {
    "showAllDataPoints": {
        "displayName": "Show all",
        "displayNameKey": "Visual_DataPoint_Show_All",
        "type": {
            "bool": true
        }
    },
    "fill": {
        "displayName": "Fill",
        "displayNameKey": "Visual_Fill",
        "type": {
            "fill": {
                "solid": {
                    "color": true
                }
            }
        }
    },
    "fillRule": {
        "displayName": "Color saturation",
        "displayNameKey": "Visual_ColorSaturation",
        "type": {
            "fillRule": {}
        },
        "rule": {
            "inputRole": "Gradient",
            "output": {
                "property": "fill",
                    "selector": [
                        "Category"
                    ]
            }
        }
    }
}
```

*fill* および *fillRule* プロパティには注意する必要があります。 前者は、カラー ピッカーです。後者は、グラデーションの置換規則であり、規則の条件が満たされると "*fill プロパティ*" `visually` を置き換えます。

*fill* プロパティと置換規則との間のこのリンクは、*fillRule* プロパティの `"rule"`>`"output"` セクションで設定されています。

`"Rule"`>`"InputRole"` プロパティでは、規則 (条件) をトリガーするデータ ロールを設定します。 この例では、データ ロール `"Gradient"` にデータが含まれている場合、`"fill"` プロパティに規則が適用されます。

次のコードでは、fill 規則 (`the last item`) をトリガーするデータ ロールの例を示します。

```json
{
    "dataRoles": [
            {
                "name": "Category",
                "kind": "Grouping",
                "displayName": "Details",
                "displayNameKey": "Role_DisplayName_Details"
            },
            {
                "name": "Series",
                "kind": "Grouping",
                "displayName": "Legend",
                "displayNameKey": "Role_DisplayName_Legend"
            },
            {
                "name": "Gradient",
                "kind": "Measure",
                "displayName": "Color saturation",
                "displayNameKey": "Role_DisplayName_Gradient"
            }
    ]
}
```

## <a name="the-enumerateobjectinstances-method"></a>enumerateObjectInstances メソッド

オブジェクトを効果的に使用するには、カスタム ビジュアルに `enumerateObjectInstances` という関数が必要です。 この関数では、プロパティ ウィンドウにオブジェクトを追加し、dataView 内でのオブジェクトのバインド先を決定します。  

一般的なセットアップは次のようになります。

```typescript
public enumerateObjectInstances(options: EnumerateVisualObjectInstancesOptions): VisualObjectInstanceEnumeration {
    let objectName: string = options.objectName;
    let objectEnumeration: VisualObjectInstance[] = [];

    switch( objectName ) {
        case 'myCustomObject':
            objectEnumeration.push({
                objectName: objectName,
                properties: { ... },
                selector: { ... }
            });
            break;
    };

    return objectEnumeration;
}
```

### <a name="properties"></a>プロパティ

`enumerateObjectInstances` のプロパティには、機能で定義したプロパティを反映します。 例については、この記事の末尾を参照してください。

### <a name="objects-selector"></a>オブジェクト セレクター

`enumerateObjectInstances` のセレクターでは、dataView 内でのオブジェクトのバインド先を決定します。 4 つの異なるオプションがあります。

#### <a name="static"></a>静的

このオブジェクトは、ここで示すようにメタデータ `dataviews[index].metadata.objects` にバインドされます。

```typescript
selector: null
```

#### <a name="columns"></a>列

このオブジェクトは、一致する `QueryName` を持つ列にバインドされます。

```typescript
selector: {
    metadata: 'QueryName'
}
```

#### <a name="selector"></a>セレクター

このオブジェクトは、作成した `selectionID` の対象である要素にバインドされます。 この例では、いくつかのデータ ポイントに対して `selectionID` を作成してあり、それらをループ処理するものとします。

```typescript
for (let dataPoint in dataPoints) {
    ...
    selector: dataPoint.selectionID.getSelector()
}
```

#### <a name="scope-identity"></a>スコープ ID

このオブジェクトは、グループの共通部分の特定の値にバインドされます。 たとえば、カテゴリ `["Jan", "Feb", "March", ...]` と系列 `["Small", "Medium", "Large"]` がある場合に、`Feb` と `Large` に一致する値の共通部分にあるオブジェクトを取得したいものとします。 これを実現するには、両方の列の `DataViewScopeIdentity` を取得し、それらを変数 `identities` にプッシュして、セレクターで次の構文を使用します。

```typescript
selector: {
    data: <DataViewScopeIdentity[]>identities
}
```

##### <a name="example"></a>例

次の例では、1 つのプロパティ *fill* を持つ customColor オブジェクトに対する 1 つの objectEnumeration がどのようなものになるかを示します。 次に示すように、このオブジェクトを `dataViews[index].metadata.objects` に静的にバインドします。

```typescript
objectEnumeration.push({
    objectName: "customColor",
    displayName: "Custom Color",
    properties: {
        fill: {
            solid: {
                color: dataPoint.color
            }
        }
    },
    selector: null
});
```
