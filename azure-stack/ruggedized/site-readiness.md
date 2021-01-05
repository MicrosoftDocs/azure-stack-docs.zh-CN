---
title: Azure Stack 中心耐用站点准备情况 Azure Stack 中心 |Microsoft Docs
description: 了解 Azure Stack 中心耐用的站点准备情况规范。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: wamota
ms.reviewer: wamota
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 5a9eb7c73a30268eb4e53855e9ce277517019e4a
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874296"
---
# <a name="azure-stack-hub-ruggedized-site-readiness"></a>Azure Stack 中心耐用站点准备情况

本主题介绍 Azure Stack 中心耐用的环境和 PDU 电源删除要求。 

>[!NOTE]
>这些值仅用于设施规划目的，而且是大致的和保守的。 实际要求可能有所不同。

## <a name="environmental-requirements"></a>环境要求

下表列出了具有以下配置的 Azure Stack 集线器耐用解决方案的环境要求：

- 14 TB 缩放单位
- 200伏交流输入电压
- 35° C 最高环境温度

*表1。高/低配置环境要求*

| 对象                         | Azure Stack 中心耐用要求               |
|--------------------------------|--------------------------------|
|工作温度           | Azure Stack 中心耐用的操作温度 (，) ：-32 ° C (-25.6 ° F) 到43° C (109 ° F) 。    |
|湿度和湿度           | 存储：5% 到 95% RH，33° C (91 ° F) 最大 dew 点。 大气始终必须为非冷凝状态。 <br> 操作：5% 至 85% RH，29° C (84.2 ° F) 最大 dew 点。
|物理连接           | Azure Stack 集线器耐用可以通过以下方式进行物理连接： <br>4x10G SR SFP + <br>4x1000BASE-SX <br>4x 1000BASE-T
|电源输入                     | 最大 4.981 Kw，平均 4.391 KW<br> 输入连接器 C13/C14<br> 输入： 100-达到 50/60Hz

## <a name="pdu-power-drop-requirements"></a>PDU 电源丢弃要求

下表列出了 Azure Stack 集线器耐用所需的电源断开。

*表2：所需的功率丢弃次数*

| 配置  | 单阶段  | 三相增量 |三相 Wye |
|----------------|---------------|-------------------|----------------|
|高/低        | 2             | 2                 | 2              |

Azure Stack 集成系统使你能够使用不同的 PDU 连接器类型，以最大程度地集成到你的数据中心。 下表列出了连接器类型：

*表3：PDU 和连接器选项*

| 位置     | 单阶段                                | 三相增量                                   | 三相 Wye                                        |
|--------------|---------------------------------------------|-----------------------------------------------------|-----------------------------------------------------------|
|北美 |- L630P<br>-L7-30P<br>- Russellstoll 3750DP |-Hubbell Pro CS8365L<br>-Russellstoll 9P54U2T/1100 |- Hubbell C530P6S<br>- ABL Sursum S52S0A<br>-飞行线索 |


