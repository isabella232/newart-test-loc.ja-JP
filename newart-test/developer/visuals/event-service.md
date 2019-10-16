---
title: Power BI ビジュアルでイベントをレンダリングする
description: Power BI ビジュアルでは、PowerPoint または PDF にエクスポートする準備ができたことを Power BI に通知できます。
author: Yarovinsky
ms.author: alexyar
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b481ce94e5025045466a05d71e30a00f02be7ead
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424301"
---
# <a name="render-events-in-power-bi-visuals"></a><span data-ttu-id="413c6-103">Power BI ビジュアルでイベントをレンダリングする</span><span class="sxs-lookup"><span data-stu-id="413c6-103">Render events in Power BI visuals</span></span>

<span data-ttu-id="413c6-104">新しい API は、レンダリング中に呼び出す必要がある 3 つのメソッド (`started`、`finished`、`failed`) で構成されます。</span><span class="sxs-lookup"><span data-stu-id="413c6-104">The new API consists of three methods (`started`, `finished`, or `failed`) that should be called during rendering.</span></span>

<span data-ttu-id="413c6-105">レンダリングが開始されたら、Power BI ビジュアルのコードで `renderingStarted` メソッドを呼び出して、レンダリング プロセスが開始されたことを示します。</span><span class="sxs-lookup"><span data-stu-id="413c6-105">When rendering starts, the Power BI visual code calls the `renderingStarted` method to indicate that the rendering process has started.</span></span>

<span data-ttu-id="413c6-106">レンダリングが正常に完了した場合は、Power BI ビジュアルのコードで即座に `renderingFinished` メソッドを呼び出して、ビジュアルのイメージをエクスポートする準備ができたことを、リスナーに通知します (主に "*PDF へのエクスポート*" と "*PowerPoint へのエクスポート*")。</span><span class="sxs-lookup"><span data-stu-id="413c6-106">If rendering is completed successfully, the Power BI visual code immediately calls the `renderingFinished` method, notifying the listeners (primarily, *export to PDF* and *export to PowerPoint*) that the visual's image is ready for export.</span></span>

<span data-ttu-id="413c6-107">プロセスの間に問題が発生した場合、Power BI ビジュアルは正常にレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="413c6-107">If a problem occurs during the process, the Power BI visual is prevented from being rendered successfully.</span></span> <span data-ttu-id="413c6-108">レンダリング プロセスが完了していないことをリスナーに通知するには、Power BI ビジュアルのコードで `renderingFailed` メソッドを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="413c6-108">To notify the listeners that the rendering process hasn't been completed, the Power BI visual code should call the `renderingFailed` method.</span></span> <span data-ttu-id="413c6-109">また、このメソッドでは、障害の理由を示すオプションの文字列も提供します。</span><span class="sxs-lookup"><span data-stu-id="413c6-109">This method also provides an optional string to provide a reason for the failure.</span></span>

## <a name="usage"></a><span data-ttu-id="413c6-110">Usage</span><span class="sxs-lookup"><span data-stu-id="413c6-110">Usage</span></span>

```typescript
export interface IVisualHost extends extensibility.IVisualHost {
    eventService: IVisualEventService ;
}

/**
 * An interface for reporting rendering events
 */
export interface IVisualEventService {
    /**
     * Should be called just before the actual rendering starts, 
     * usually at the start of the update method
     *
     * @param options - the visual update options received as an update parameter
     */
    renderingStarted(options: VisualUpdateOptions): void;

    /**
     * Should be called immediately after rendering finishes successfully
     * 
     * @param options - the visual update options received as an update parameter
     */
    renderingFinished(options: VisualUpdateOptions): void;

    /**
     * Called when rendering fails, with an optional reason string
     * 
     * @param options - the visual update options received as an update parameter
     * @param reason - the optional failure reason string
     */
    renderingFailed(options: VisualUpdateOptions, reason?: string): void;
}
```

### <a name="sample-the-visual-displays-no-animations"></a><span data-ttu-id="413c6-111">サンプル: ビジュアルにアニメーションが表示されない</span><span class="sxs-lookup"><span data-stu-id="413c6-111">Sample: The visual displays no animations</span></span>

```typescript
    export class Visual implements IVisual {
        ...
        private events: IVisualEventService;
        ...

        constructor(options: VisualConstructorOptions) {
            ...
            this.events = options.host.eventService;
            ...
        }

        public update(options: VisualUpdateOptions) {
            this.events.renderingStarted(options);
            ...
            this.events.renderingFinished(options);
        }
```

### <a name="sample-the-visual-displays-animations"></a><span data-ttu-id="413c6-112">サンプル: ビジュアルにアニメーションが表示される</span><span class="sxs-lookup"><span data-stu-id="413c6-112">Sample: The visual displays animations</span></span>

<span data-ttu-id="413c6-113">ビジュアルにレンダリングするアニメーションまたは非同期関数がある場合は、アニメーションの後または非同期関数の内部で、`renderingFinished` メソッドを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="413c6-113">If the visual has animations or async functions for rendering, the `renderingFinished` method should be called after the animation or inside async function.</span></span>

```typescript
    export class Visual implements IVisual {
        ...
        private events: IVisualEventService;
        private element: HTMLElement;
        ...

        constructor(options: VisualConstructorOptions) {
            ...
            this.events = options.host.eventService;
            this.element = options.element;
            ...
        }

        public update(options: VisualUpdateOptions) {
            this.events.renderingStarted(options);
            ...
            // Learn more at https://github.com/d3/d3-transition/blob/master/README.md#transition_end
            d3.select(this.element).transition().duration(100).style("opacity","0").end().then(() => {
                // renderingFinished called after transition end
                this.events.renderingFinished(options);
            });
        }
```

## <a name="rendering-events-for-visual-certification"></a><span data-ttu-id="413c6-114">ビジュアル認定のためのレンダリング イベント</span><span class="sxs-lookup"><span data-stu-id="413c6-114">Rendering events for visual certification</span></span>

<span data-ttu-id="413c6-115">ビジュアル認定の要件の 1 つは、ビジュアルによるレンダリング イベントのサポートです。</span><span class="sxs-lookup"><span data-stu-id="413c6-115">One requirement of visuals certification is the support of rendering events by the visual.</span></span> <span data-ttu-id="413c6-116">詳細については、「[認定要件](https://docs.microsoft.com/power-bi/power-bi-custom-visuals-certified?#certification-requirements)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="413c6-116">For more information, see [certification requirements](https://docs.microsoft.com/power-bi/power-bi-custom-visuals-certified?#certification-requirements).</span></span>
