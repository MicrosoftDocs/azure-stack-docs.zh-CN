---
title: Modular Data Center (MDC) 网络附录
description: MDC 网络的附录。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 12/30/2019
ms.lastreviewed: 12/30/2019
ms.openlocfilehash: b18f962ce2348a6a0f90a2ad48c1ea84e01fe921
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598634"
---
# <a name="mdc-network-appendix"></a>MDC 网络附录

该附录提供了 MDC 硬件的设备参数和标识信息。

## <a name="technical-device-parameters"></a>技术设备参数

### <a name="cisco-93360yc-fx2--cisco-9348gc-fxp"></a>Cisco 93360YC-FX2 和 Cisco 9348GC-FXP

| **功能** | Cisco Nexus 93360YC-FX2 | Cisco Nexus 9348GC-FXP |
|---|---|---|
| 端口 | 96 个 1/10/25-Gbps 端口和 12 个 40/100-Gbps QSFP28 端口 | 48 个 1-GBASE-T 端口、4 个 1/10/25-Gbps SFP28 端口和 2 个 40/100 QSFP28 端口 |
| 支持的速度 | 下行链路为 1/10/25-Gbps，上行链路为 40/100-Gbps，支持“分支”的端口 97-108：4x10/25G | 速度为 100-Mbps 和 1-Gbps |
| CPU | 四核 | 四核 |
| 系统内存 | 最大 24 GB | 24 GB |
| SSD 驱动器 | 128 GB | 128 GB |
| 系统缓冲区 | 40 MB | 40 MB |
| 管理端口 | 两个端口：一个 RJ-45 和一个 SFP+ | 两个端口：一个 RJ-45 和一个 SFP+ |
| USB 端口 | 一个 | 一个 |
| RS-232 串行端口 | 一个 | 一个 |
| 电源（最多两个） | 1200 W 交流电 (AC)，1200 W HVAC/HVDC | 350 W AC，440 W DC |
| 典型功率 (AC) | 404 W | 178 W |
| 最大功率 (AC) | 900 W | 287 W |
| 输入电压 (AC) | 100 V 到 240 V | 100 V 到 240 V |
| 输入电压（高压 AC [HVAC]） | 100 V 到 277 V | 90 V 到 305 V |
| 输入电压 (DC) | –40 V 到 –72 V | \-36 V 到 -72 V |
| 输入电压（高压 DC [HVAC]） | –240V 到 –380V | 192 V 到 400 V |
| 频率 (AC) | 50 Hz 到 60 Hz | 50 Hz 到 60 Hz |
| 风扇 | 三个风扇架 | 三级 |
| 气流 | 端口侧进气和排气 | 端口侧进气和排气 |
| 物理尺寸 | 3.38 x 17.41 x 24.14 英寸 （8.59 x 44.23 x 61.31 厘米） | 1.72 x 17.3 x 19.7 英寸 |
| （高 x 宽 x 深） | | （4.4 x 43.9 x 49.9 厘米） |
| 声音 | 风扇速度处于 40% 时为 76.7 dBA，风扇速度处于 70% 时为 88.7 dBA，风扇速度处于 100% 时为 97.4 dBA | 风扇速度处于 50% 时为 67.5 dBA，风扇速度处于 70% 时为 73.2 dBA，风扇速度处于 100% 时为 81.6 dBA |
| RoHS 符合性 | 是 | 是 |
| MTBF | 320,040 小时 | 257,860 小时 |
| 最低版本的 ACI 映像 | ACI-N9KDK9-14.1.2 | ACI-N9KDK9-13.0 |
| 最低版本的 NX-OS 映像 | NXOS-9.3(1) | NXOS-703I7.1 |

### <a name="juniper-mx204"></a>Juniper MX204

:::row:::
    :::column:::
        **布局**
    :::column-end:::
    :::column:::
        系统容量

        槽方向

        安装
    :::column-end:::
    :::column span="2":::
        3 Tbps

        NA

        前部或中部
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **物理规格**
    :::column-end:::
    :::column:::
        尺寸（宽 x 高 x 深）

        满载重量

        空载重量
    :::column-end:::
    :::column span="2":::
        17.45 英寸 x 8.71 英寸 x 24.5 英寸（44.3 x 22.1 x 62.2 厘米）

        130 磅/59 千克

        52 磅/23.6 千克
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **路由引擎**
    :::column-end:::
    :::column:::
        默认存储

        内核数
    :::column-end:::
    :::column span="2":::
        2x16 MB NOR 闪存；64 GB DDR4 RAM；2x50 GB SSD

        6 核
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **冗余**
    :::column-end:::
    :::column:::
        组件
    :::column-end:::
    :::column span="2":::
        电源、RE、风扇
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **环境**
    :::column-end:::
    :::column:::
        气流

        工作温度

        工作湿度

        工作海拔
    :::column-end:::
    :::column span="2":::
        边到边

        处于海平面时为 32° 到 115° F（0° 到 46° C）

        5% 到 90%

        10,000 英尺（3048米）
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **认证**
    :::column-end:::
    :::column:::
        NEBS
    :::column-end:::
    :::column span="2":::
        • GR-1089-Core EMC 和电气安全

        • 公共联接网 (CBN)

        • 国家电气规范 (NEC)

        • GR-63-Core 物理保护
    :::column-end:::
:::row-end:::

### <a name="dell-emc-s4148f-on"></a>Dell EMC S4148F-ON

功能：

- 48 个 10 千兆位以太网 SFP+ 端口
- 六个 40 千兆位以太网 SFP+ 端口
- 一个带有 RS232 信号的 RJ45 控制台/管理端口 
- 一个 RJ45 micro-USB-B 控制台端口 
- 一个 RJ45 10/100/1000Base-T 管理以太网端口 

规格：

- 大小： 
  - 一个 RU，1.75 英寸（高）x 17 英寸（宽）x 18 英寸（深）（4.4 厘米（高）x 43.1 厘米（宽）x 45.7 厘米（深）） 
  - S4112：1.7 英寸（高）x 8.28 英寸（宽）x 18 英寸（深）（4.125 厘米（高）x 20.9 厘米（宽）x 45 厘米（深）） 
- 电源：100–240 VAC 50/60 Hz
- 电源 (DC)，适用于 S4412：额定功率为 -40 VDC 到-72 VDC
- 每个系统的最大电流消耗：100/120V AC 时为 6A/5A；200/240V AC 时为 3A/2.5A
- S4112：100/120V AC 时为 2A/1.7A；200/240V AC 时为 1A/0.8A 
- S4112 (DC)：-40V/5A、-48V/4.2A、-72V/2.8A 

适用于工作环境的最大规格值：
- 工作温度：41° 到 104° F（5° 到 40° C）
- 工作湿度：5% - 85% (RH)，非冷凝 

适用于非工作环境的最大规格值：
- 存储温度：-40° 到 149°F（-40° C 到 65° C） 
- 存储湿度：5% - 95% (RH)，非冷凝


## <a name="identity"></a>标识

所有网络设备都设计为可与终端访问控制器访问控制系统 (TACACS) 配合使用。 TACACS 提供集中式标识体验。 TACACS 服务器必须作为初始配置的一部分提供。

请注意，在 TACACS 服务器不可用后，设备将回退到下列本地标识。 在正常操作期间会禁用那些帐户。


以下用户帐户在每个设备上执行退出：

| 设备 | 用户名 | 默认密码 |
|---------|--------------|----------------------|
| Edge 1 | root | 由客户分配 |
| Edge 2 | Root | 由客户分配 |
| Tor 1 | Root | 由客户分配 |
| Tor 2 | Root | 由客户分配 |
| BMC | Root | 由客户分配 |
| PDU 1 | root | 由客户分配 |
| PDU 2 | root | 由客户分配 |
| 串行 | Root | 由客户分配 |
| Avocent | Root 和 Admin | 由客户分配 |
