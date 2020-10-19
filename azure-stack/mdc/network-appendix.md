---
title: 模块化数据中心 (MDC) 网络附录
description: 适用于 MDC 网络的附录。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 12/30/2019
ms.lastreviewed: 12/30/2019
ms.openlocfilehash: 10de658f7e5f8621d44baaa16f2181f58f47811e
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182863"
---
# <a name="mdc-network-appendix"></a>MDC 网络附录

附录为 MDC 硬件提供设备参数和标识信息。

## <a name="technical-device-parameters"></a>技术设备参数

### <a name="cisco-93360yc-fx2--cisco-9348gc-fxp"></a>Cisco 93360YC-FX2 & Cisco 9348GC-FXP

| **功能** | **Cisco 结点 93360YC-FX2** | **Cisco 结点 9348GC-FXP** |
|---|---|---|
| 端口 | 96 x 1/10/25-Gbps 和 12 x 40/100 Gbps QSFP28 端口 | 48 x 1-GBASE 端口，4 x 1/10/25-Gbps SFP28 端口和 2 x 40/100 QSFP28 端口 |
| 支持的速度 | 1/10/25-downlinks 在上行上为 40/100-Gbps，分类支持的端口，97-108： 4x10/25G | 100-Mbps 和 1 Gbps 速度 |
| CPU | 四核 | 四核 |
| 系统内存 | 最高 24 GB | 24 GB |
| SSD 驱动器 | 128 GB | 128 GB |
| 系统缓冲区 | 40 MB | 40 MB |
| 管理端口 | 两个端口：一个 RJ-45 和一个 SFP + | 两个端口：一个 RJ-45 和一个 SFP + |
| USB 端口 | 一个 | 一个 |
| RS-232 串行端口 | 一个 | 一个 |
| 电源最多 (两个)  | 1200 w 交替当前 (AC) ，1200 W HVAC/HVDC | 350 W AC，440 W DC |
| 典型的 power (AC)  | 404 W | 178 W |
| 最大功率 (AC)  | 900 W | 287 W |
| 输入电压 (AC)  | 100 v 到 240 V | 100 v 到 240 V |
| 输入电压 (高压 AC [HVAC] )  | 100 v 到 277 V | 90 v 到 305 V |
| 输入电压 (DC)  | – 40 v 到– 72 V | \-36 v 到-72 V |
| 输入电压 (高压 DC [HVDC] )  | –380V – | 192 v 到 400 V |
|  (AC) 频率 | 50 Hz 到 60 Hz | 50 Hz 到 60 Hz |
| 转动 | 三个风扇托盘 | 三级 |
| 气流 | 端口端进气和排气 | 端口端进气和排气 |
| 物理维度 | 3.38 x 17.41 x 24.14。  (8.59 x 44.23 x 61.31 厘米)  | 1.72 x 17.3 x 19.7 in。 |
|  (H x W x D)  | |  (4.4 x 43.9 x 49.9 厘米)  |
| 噪声 | 76.7 dBA 处于40% 的风扇速度，以70% 的风扇速度和 97.4 dBA 速度88。7 | 67.5 dBA 处于50% 的风扇速度，以70% 的风扇速度 73.2 dBA，以81.6 速度提高100 |
| RoHS 相容性 | 是 | 是 |
| MTBF | 320040小时 | 257860小时 |
| 最小 ACI 映像 | ACI-N9KDK9-14.1。2 | ACI-N9KDK9-13。0 |
| 最小 NX-OS 映像 | NXOS-9.3 (1)  | NXOS-703I 7。1 |

### <a name="juniper-mx204"></a>刺柏 MX204

|  |  |  |
|--|--|--|
| 布局 | 系统容量 | 3 Tbps |
|        | 槽方向 | NA |
|        | 安装 | 正面或中心 |
| 物理规范 | 尺寸 (W x H x D)  | 17.45 "x 8.71" x 24.5 " (44.3 x 22.1 x 62.2 厘米)  |
|                        | 已完全加载权重 | 130磅/59 千克 |
|                        | 已卸载权重 | 52磅/23.6 公斤 |
| 路由引擎 | 默认内存 | 2x16 MB 或闪存;64 GB DDR4 RAM;2x50 GB SSD |
|                | 内核数 | 6核 |
| 冗余 | 组件 | 电源、分辨率、风扇 |
| 保护 | 空气流通 | 边到端 |
|               | 操作温度 | 32°到115° F (0 °到46° C) 海平面 |
|               | 操作湿度 | 5% 至90% |
|               | 操作海拔 | 10000 ft (3048)  |
| 认证 | NEBS | • GR-1089-核心 EMC 和电气安全 |
|                | | •常见结合网络 (CBN)  |
|                | | •国家电气代码 (NEC)  |
|                | | • GR-63 核心物理保护 |

### <a name="dell-emc-s4148f-on"></a>Dell EMC S4148F

功能：

- 48 10 千兆以太网 SFP + 端口
- 6 40 千兆以太网 QSFP + 端口
- 一个带有 RS232 信号的 RJ45 控制台/管理端口 
- 一个 RJ45 微 USB-B 控制台端口 
- 一个 RJ45 10/100/1000Base 管理以太网端口 

规格

- 大小： 
  - 1 RU，1.75 " (h) x 17" (w) x 18 " (d)  (4.4 厘米 (h) x 43.1 厘米 ()  (x 45.7 厘米) d # A13 
  - S4112： 1.7 " (h) x 8.28" (w) x 18 " (d)  (4.125 厘米 (h) x 20.9 厘米 (w) x 45 厘米 (d)  
- 电源：100– 240 VAC 50/60 Hz
- 电源 (DC) ，适用于 S4412：额定-40 VDC 到-72 VDC
- 每个系统的最大当前绘图： 100/120V AC 中的 6A/5A;3A/2.5 A at 200/电交流
- S4112： 2A/1.7 A at 100/120V AC;1A/0.8 A at 200/电交流。 
- S4112 (DC) ：-40V/5A、-48V/4.2 A、-72V/2.8 A 

最大操作规格：
- 工作温度：41°到104° F (5 °40° C) 
- 操作湿度：5% 至 85% (RH) ，非冷凝 

最大非操作规范：
- 存储温度：-40 到149° F (-40 ° C 到65° C)  
- 存储湿度：5% 至 95% (RH) ，非冷凝


## <a name="identity"></a>标识

所有网络设备都设计为与终端访问控制器 Access-Control 系统 (TACACS) 配合使用。 TACACS 提供集中的标识体验。 TACACS 服务器必须作为初始配置的一部分提供。

请注意，TACACS 服务器不可用后，设备将回退到下面列出的本地标识。 在正常操作期间，禁用了这些帐户。


以下用户帐户每个设备退出：

| 设备 | 用户名 | 默认密码 |
|---------|--------------|----------------------|
| 第1边缘 | root | 由客户分配 |
| 第2边缘 | Root | 由客户分配 |
| Tor 1 | Root | 由客户分配 |
| Tor 2 | Root | 由客户分配 |
| BMC | Root | 由客户分配 |
| PDU 1 | root | 由客户分配 |
| PDU 2 | root | 由客户分配 |
| 串行 | Root | 由客户分配 |
| 率先 | Root & 管理员 | 由客户分配 |
