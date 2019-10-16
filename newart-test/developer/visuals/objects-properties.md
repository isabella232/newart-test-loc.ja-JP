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
# <a name="objects-and-properties-of-power-bi-visuals"></a><span data-ttu-id="8a4c2-103">Power BI ビジュアルのオブジェクトとプロパティ</span><span class="sxs-lookup"><span data-stu-id="8a4c2-103">Objects and properties of Power BI visuals</span></span>

<span data-ttu-id="8a4c2-104">オブジェクトでは、ビジュアルに関連付けられているカスタマイズ可能なプロパティが記述されています。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-104">Objects describe customizable properties that are associated with a visual.</span></span> <span data-ttu-id="8a4c2-105">オブジェクトは複数のプロパティを持つことができ、各プロパティにはプロパティの内容を示す型が関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-105">An object can have multiple properties, and each property has an associated type that describes what the property will be.</span></span> <span data-ttu-id="8a4c2-106">この記事では、オブジェクトとプロパティの型について説明します。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-106">This article provides information about objects and property types.</span></span>

<span data-ttu-id="8a4c2-107">`myCustomObject` は、`dataView` および `enumerateObjectInstances` 内のオブジェクトを参照するために使用される内部名です</span><span class="sxs-lookup"><span data-stu-id="8a4c2-107">`myCustomObject` is the internal name that's used to reference the object within `dataView` and `enumerateObjectInstances`.</span></span>

```json
"objects": {
    "myCustomObject": {
        "displayName": "My Object Name",
        "properties": { ... }
    }
}
```

## <a name="display-name"></a><span data-ttu-id="8a4c2-108">表示名</span><span class="sxs-lookup"><span data-stu-id="8a4c2-108">Display name</span></span>

<span data-ttu-id="8a4c2-109">`displayName` は、プロパティ ウィンドウに表示される名前です。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-109">`displayName` is the name that will be shown in the property pane.</span></span>

## <a name="properties"></a><span data-ttu-id="8a4c2-110">プロパティ</span><span class="sxs-lookup"><span data-stu-id="8a4c2-110">Properties</span></span>

<span data-ttu-id="8a4c2-111">`properties` は、開発者によって定義されるプロパティのマップです。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-111">`properties` is a map of properties that are defined by the developer.</span></span>

```json
"properties": {
    "myFirstProperty": {
        "displayName": "firstPropertyName",
        "type": ValueTypeDescriptor | StructuralTypeDescriptor
    }
}
```

> [!NOTE]
> <span data-ttu-id="8a4c2-112">`show` は、スイッチを使用してオブジェクトを切り替えることができる特殊なプロパティです。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-112">`show` is a special property that enables a switch to toggle the object.</span></span>

<span data-ttu-id="8a4c2-113">例:</span><span class="sxs-lookup"><span data-stu-id="8a4c2-113">Example:</span></span>

```json
"properties": {
    "show": {
        "displayName": "My Property Switch",
        "type": {"bool": true}
    }
}
```

### <a name="property-types"></a><span data-ttu-id="8a4c2-114">プロパティの型</span><span class="sxs-lookup"><span data-stu-id="8a4c2-114">Property types</span></span>

<span data-ttu-id="8a4c2-115">プロパティには、`ValueTypeDescriptor` と `StructuralTypeDescriptor` の 2 つの型があります。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-115">There are two property types: `ValueTypeDescriptor` and `StructuralTypeDescriptor`.</span></span>

#### <a name="value-type-descriptor"></a><span data-ttu-id="8a4c2-116">値型記述子</span><span class="sxs-lookup"><span data-stu-id="8a4c2-116">Value type descriptor</span></span>

<span data-ttu-id="8a4c2-117">`ValueTypeDescriptor` 型は、通常はプリミティブであり、通常は静的オブジェクトとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-117">`ValueTypeDescriptor` types are mostly primitive and are ordinarily used as a static object.</span></span>

<span data-ttu-id="8a4c2-118">一般的な `ValueTypeDescriptor` 要素をいくつか次に示します。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-118">Here are some of the common `ValueTypeDescriptor` elements:</span></span>

```typescript
export interface ValueTypeDescriptor {
    text?: boolean;
    numeric?: boolean;
    integer?: boolean;
    bool?: boolean;
}
```

#### <a name="structural-type-descriptor"></a><span data-ttu-id="8a4c2-119">構造型記述子</span><span class="sxs-lookup"><span data-stu-id="8a4c2-119">Structural type descriptor</span></span>

<span data-ttu-id="8a4c2-120">`StructuralTypeDescriptor` 型は、主に、データ バインドされたオブジェクトに使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-120">`StructuralTypeDescriptor` types are mostly used for data-bound objects.</span></span>
<span data-ttu-id="8a4c2-121">最も一般的な `StructuralTypeDescriptor` 型は *fill* です。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-121">The most common `StructuralTypeDescriptor` type is *fill*.</span></span>

```typescript
export interface StructuralTypeDescriptor {
    fill?: FillTypeDescriptor;
}
```

## <a name="gradient-property"></a><span data-ttu-id="8a4c2-122">グラデーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="8a4c2-122">Gradient property</span></span>

<span data-ttu-id="8a4c2-123">グラデーション プロパティは、標準プロパティとして設定できないプロパティです。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-123">The gradient property is a property that can't be set as a standard property.</span></span> <span data-ttu-id="8a4c2-124">代わりに、カラー ピッカー プロパティ (*fill* 型) の置換に関する規則を設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-124">Instead, you need to set a rule for the substitution of the color picker property (*fill* type).</span></span>

<span data-ttu-id="8a4c2-125">次のコードで例を示します。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-125">An example is shown in the following code:</span></span>

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

<span data-ttu-id="8a4c2-126">*fill* および *fillRule* プロパティには注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-126">Pay attention to the *fill* and *fillRule* properties.</span></span> <span data-ttu-id="8a4c2-127">前者は、カラー ピッカーです。後者は、グラデーションの置換規則であり、規則の条件が満たされると "*fill プロパティ*" `visually` を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-127">The first is the color picker, and the second is the substitution rule for the gradient that will replace the *fill property*, `visually`, when the rule conditions are met.</span></span>

<span data-ttu-id="8a4c2-128">*fill* プロパティと置換規則との間のこのリンクは、*fillRule* プロパティの `"rule"`>`"output"` セクションで設定されています。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-128">This link between the *fill* property and the substitution rule is set in the `"rule"`>`"output"` section of the *fillRule* property.</span></span>

<span data-ttu-id="8a4c2-129">`"Rule"`>`"InputRole"` プロパティでは、規則 (条件) をトリガーするデータ ロールを設定します。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-129">`"Rule"`>`"InputRole"` property sets which data role triggers the rule (condition).</span></span> <span data-ttu-id="8a4c2-130">この例では、データ ロール `"Gradient"` にデータが含まれている場合、`"fill"` プロパティに規則が適用されます。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-130">In this example, if data role `"Gradient"` contains data, the rule is applied for the `"fill"` property.</span></span>

<span data-ttu-id="8a4c2-131">次のコードでは、fill 規則 (`the last item`) をトリガーするデータ ロールの例を示します。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-131">An example of the data role that triggers the fill rule (`the last item`) is shown in the following code:</span></span>

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

## <a name="the-enumerateobjectinstances-method"></a><span data-ttu-id="8a4c2-132">enumerateObjectInstances メソッド</span><span class="sxs-lookup"><span data-stu-id="8a4c2-132">The enumerateObjectInstances method</span></span>

<span data-ttu-id="8a4c2-133">オブジェクトを効果的に使用するには、カスタム ビジュアルに `enumerateObjectInstances` という関数が必要です。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-133">To use objects effectively, you need a function in your custom visual called `enumerateObjectInstances`.</span></span> <span data-ttu-id="8a4c2-134">この関数では、プロパティ ウィンドウにオブジェクトを追加し、dataView 内でのオブジェクトのバインド先を決定します。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-134">This function populates the property pane with objects and also determines where your objects should be bound within the dataView.</span></span>  

<span data-ttu-id="8a4c2-135">一般的なセットアップは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-135">Here is what a typical setup looks like:</span></span>

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

### <a name="properties"></a><span data-ttu-id="8a4c2-136">プロパティ</span><span class="sxs-lookup"><span data-stu-id="8a4c2-136">Properties</span></span>

<span data-ttu-id="8a4c2-137">`enumerateObjectInstances` のプロパティには、機能で定義したプロパティを反映します。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-137">The properties in `enumerateObjectInstances` reflect the properties that you defined in your capabilities.</span></span> <span data-ttu-id="8a4c2-138">例については、この記事の末尾を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-138">For an example, go to the end of this article.</span></span>

### <a name="objects-selector"></a><span data-ttu-id="8a4c2-139">オブジェクト セレクター</span><span class="sxs-lookup"><span data-stu-id="8a4c2-139">Objects selector</span></span>

<span data-ttu-id="8a4c2-140">`enumerateObjectInstances` のセレクターでは、dataView 内でのオブジェクトのバインド先を決定します。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-140">The selector in `enumerateObjectInstances` determines where each object is bound in the dataView.</span></span> <span data-ttu-id="8a4c2-141">4 つの異なるオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-141">There are four distinct options.</span></span>

#### <a name="static"></a><span data-ttu-id="8a4c2-142">静的</span><span class="sxs-lookup"><span data-stu-id="8a4c2-142">static</span></span>

<span data-ttu-id="8a4c2-143">このオブジェクトは、ここで示すようにメタデータ `dataviews[index].metadata.objects` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-143">This object is bound to metadata `dataviews[index].metadata.objects`, as shown here.</span></span>

```typescript
selector: null
```

#### <a name="columns"></a><span data-ttu-id="8a4c2-144">列</span><span class="sxs-lookup"><span data-stu-id="8a4c2-144">columns</span></span>

<span data-ttu-id="8a4c2-145">このオブジェクトは、一致する `QueryName` を持つ列にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-145">This object is bound to columns with the matching `QueryName`.</span></span>

```typescript
selector: {
    metadata: 'QueryName'
}
```

#### <a name="selector"></a><span data-ttu-id="8a4c2-146">セレクター</span><span class="sxs-lookup"><span data-stu-id="8a4c2-146">selector</span></span>

<span data-ttu-id="8a4c2-147">このオブジェクトは、作成した `selectionID` の対象である要素にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-147">This object is bound to the element that you created a `selectionID` for.</span></span> <span data-ttu-id="8a4c2-148">この例では、いくつかのデータ ポイントに対して `selectionID` を作成してあり、それらをループ処理するものとします。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-148">In this example, let's assume that we've created `selectionID`s for some data points, and that we're looping through them.</span></span>

```typescript
for (let dataPoint in dataPoints) {
    ...
    selector: dataPoint.selectionID.getSelector()
}
```

#### <a name="scope-identity"></a><span data-ttu-id="8a4c2-149">スコープ ID</span><span class="sxs-lookup"><span data-stu-id="8a4c2-149">Scope identity</span></span>

<span data-ttu-id="8a4c2-150">このオブジェクトは、グループの共通部分の特定の値にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-150">This object is bound to particular values at the intersection of groups.</span></span> <span data-ttu-id="8a4c2-151">たとえば、カテゴリ `["Jan", "Feb", "March", ...]` と系列 `["Small", "Medium", "Large"]` がある場合に、`Feb` と `Large` に一致する値の共通部分にあるオブジェクトを取得したいものとします。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-151">For example, if you have categories `["Jan", "Feb", "March", ...]` and series `["Small", "Medium", "Large"]`, you might want to have an object at the intersection of values that match `Feb` and `Large`.</span></span> <span data-ttu-id="8a4c2-152">これを実現するには、両方の列の `DataViewScopeIdentity` を取得し、それらを変数 `identities` にプッシュして、セレクターで次の構文を使用します。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-152">To accomplish this, you could get the `DataViewScopeIdentity` of both columns, push them to variable `identities`, and use this syntax with the selector.</span></span>

```typescript
selector: {
    data: <DataViewScopeIdentity[]>identities
}
```

##### <a name="example"></a><span data-ttu-id="8a4c2-153">例</span><span class="sxs-lookup"><span data-stu-id="8a4c2-153">Example</span></span>

<span data-ttu-id="8a4c2-154">次の例では、1 つのプロパティ *fill* を持つ customColor オブジェクトに対する 1 つの objectEnumeration がどのようなものになるかを示します。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-154">The following example shows what one objectEnumeration would look like for a customColor object with one property, *fill*.</span></span> <span data-ttu-id="8a4c2-155">次に示すように、このオブジェクトを `dataViews[index].metadata.objects` に静的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="8a4c2-155">We want this object bound statically to `dataViews[index].metadata.objects`, as shown:</span></span>

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
