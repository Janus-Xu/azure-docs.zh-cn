---
title: 向地图添加多边形延伸层 |Microsoft Azure 映射
description: 如何将多边形延伸层添加到 Microsoft Azure Map Web SDK。
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334545"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>向地图添加多边形延伸层

本文介绍如何使用多边形延伸层将`Polygon`和`MultiPolygon`特征几何作为延伸形状。 Azure Maps Web SDK 支持按[扩展 GeoJSON 架构](extend-geojson.md#circle)中的定义呈现圆形几何。 在地图上呈现时，可以将这些圆转换为多边形。 用阿特拉斯包装时，所有功能几何都可以轻松更新[。Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)类。

## <a name="use-a-polygon-extrusion-layer"></a>使用多边形延伸层

将[多边形延伸层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)连接到数据源。 然后，将其加载到地图中。 多边形延伸层会将`Polygon`和`MultiPolygon`功能的区域呈现为延伸形状。 多边形`height`延伸`base`层的和属性定义了与延伸形状的地面和高度的基准距离（以米为**单位**）。 下面的代码演示如何创建一个多边形，如何将其添加到数据源中，以及如何使用多边形延伸层类进行呈现。

> [!Note]
> 多边形`base`延伸层中定义的值应小于或等于的值`height`。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="突出多边形" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
查看<a href='https://codepen.io'>CodePen</a>上的笔<a href='https://codepen.io/azuremaps/pen/wvvBpvE'>延伸多边形</a>Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>（）。</iframe>


## <a name="add-data-driven-polygons"></a>添加数据驱动多边形

可以使用多边形延伸层来呈现等值线图地图。 将延伸`height`层`fillColor`的和属性设置为`Polygon`和`MultiPolygon`特征几何中统计变量的度量值。 下面的代码示例显示了等值线图的拉伸的映射，该映射基于按状态测量人口密度。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="拉伸的等值线图映射" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）查看笔<a href='https://codepen.io/azuremaps/pen/eYYYNox'>拉伸的等值线图映射</a>。
</iframe>

## <a name="add-a-circle-to-the-map"></a>将圆添加到地图

Azure Maps 使用 GeoJSON 架构的扩展版本，它提供了圆的定义，如[此处](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)所述。 可以通过`point`创建一个具有`subType`属性的`Circle`功能，并使用表示以米为**单位**的半径的编号`Radius`属性，在地图上呈现延伸圆。 例如：

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Maps Web SDK 将这些`Point`功能转换为`Polygon`其功能的功能。 这些`Point`功能可以使用多边形延伸层在地图上呈现，如以下代码示例所示。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="无人机空域多边形" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的<a href='https://codepen.io/azuremaps/pen/zYYYrxo'>无人机空域多边形</a>Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>（）。
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>自定义多边形延伸层

多边形延伸层具有多个样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的<a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> by<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps （）。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [多边形延伸层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

其他资源：

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON 规范扩展](extend-geojson.md#circle)
