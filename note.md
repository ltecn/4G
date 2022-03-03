Trusted Non-3GPP Access Point Function (TNAP)

Trusted Non-3GPP Gateway Function (TNGF)

Trusted WLAN Interworking Function (TWIF)

Wireline Access Gateway Function (W-AGF)

[5G: focus on N3IWF, TNGF, TWIF and W-5GAN](https://romars.tech/pubblicazioni/n3iwf/)  


# IMS

[Understanding the IMS Charging Architecture](https://www.oracle.com/technical-resources/articles/enterprise-architecture/imc-charging-architecture-part1.html)

[3GPP TS 32.260 IP Multimedia Subsystem (IMS) charging](https://www.etsi.org/deliver/etsi_ts/132200_132299/132260/16.03.00_60/ts_132260v160300p.pdf)


# BGP

边界网关协议 (BGP)  
内部边界网关协议 (IBGP)   
外部边界网关协议 (EBGP)  

BGP协议通常应用于大型和复杂的网络，用于在自治系统之间传递路由信息。 




```
 192.168.10.0/24                                                               192.168.20.0/24
+-------------------------------------------+                                 +----------------+
| AS 65001                                  |                                 | AS 65002       |
|    +-----+192.168.10.2                    |                                 |                |
|    | RT2 |-------------+                  |                                 |                |
|    +-----+LOOPBACK:    |              LAN1|                                 |                |
|             10.0.0.2   |                  |                                 |                |
|                        |   192.168.10.1+-----+172.16.1.1       172.16.1.2+-----+             |
|                   IBGP +---------------| RT1 |---------------------------| RT4 |             |
|                        |   LOOPBACK:   +-----+LAN2        EBGP           +-----+             |
|    +-----+192.168.10.3 |     10.0.0.1     |                                 |                |
|    | RT3 |-------------+                  |                                 |                |
|    +-----+LOOPBACK:                       |                                 |                |
|             10.0.0.3                      |                                 |                |
+-------------------------------------------+                                 +----------------+
```







## 定义

边界网关协议BGP（Border Gateway Protocol）是一种实现自治系统AS（Autonomous System）之间的路由可达，并选择最佳路由的距离矢量路由协议。早期发布的三个版本分别是BGP-1（RFC1105）、BGP-2（RFC1163）和BGP-3（RFC1267），1994年开始使用BGP-4（RFC1771），2006年之后单播IPv4网络使用的版本是BGP-4（RFC4271），其他网络（如IPv6等）使用的版本是[MP-BGP](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564?section=j0e1)（RFC4760）。

MP-BGP是对BGP-4进行了扩展，来达到在不同网络中应用的目的，BGP-4原有的消息机制和路由机制并没有改变。MP-BGP在IPv6单播网络上的应用称为BGP4+，在IPv4组播网络上的应用称为MBGP（Multicast BGP）。



## 目的

为方便管理规模不断扩大的网络，网络被分成了不同的自治系统。1982年，外部网关协议EGP（Exterior Gateway Protocol）被用于实现在AS之间动态交换路由信息。但是EGP设计得比较简单，只发布网络可达的路由信息，而不对路由信息进行优选，同时也没有考虑环路避免等问题，很快就无法满足网络管理的要求。

BGP是为取代最初的EGP而设计的另一种外部网关协议。不同于最初的EGP，BGP能够进行路由优选、避免路由环路、更高效率的传递路由和维护大量的路由信息。

虽然BGP用于在AS之间传递路由信息，但并不是所有AS之间传递路由信息都需要运行BGP。比如在数据中心上行的连入Internet的出口上，为了避免Internet海量路由对数据中心内部网络的影响，设备采用静态路由代替BGP与外部网络通信。



## 受益

BGP从多方面保证了网络的安全性、灵活性、稳定性、可靠性和高效性：

- BGP采用认证和GTSM的方式，保证了[网络的安全性](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564?section=j0dp)。
- BGP提供了丰富的路由策略，能够灵活的进行[路由选路](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564?section=j0dq)。
- BGP提供了[路由聚合](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564?section=j0dt)和[路由衰减](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564?section=j0du)功能用于防止路由振荡，有效提高了网络的稳定性。
- BGP使用TCP作为其传输层协议（端口号为179），并支持[BGP与BFD联动](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564?section=j0dw)、[BGP Auto FRR](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564?section=j0dx)和[BGP GR和NSR](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564?section=j0dy)，提高了网络的可靠性。



## BGP的基本概念



本章节介绍了BGP的基本概念，理解这些基本概念后，您可以更好的理解BGP的其它功能。



### 自治系统AS（Autonomous System）

AS是指在一个实体管辖下的拥有相同选路策略的IP网络。BGP网络中的每个AS都被分配一个唯一的AS号，用于区分不同的AS。AS号分为2字节AS号和4字节AS号，其中2字节AS号的范围为1至65535，4字节AS号的范围为1至4294967295。支持4字节AS号的设备能够与支持2字节AS号的设备兼容。

### BGP分类

如[图9-1](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564/2defbe2d#fig_dc_feature_bgp_000301)所示，BGP按照运行方式分为EBGP（External/Exterior BGP）和IBGP（Internal/Interior BGP）。

**图9-1** BGP的运行方式
![img](https://download.huawei.com/mdl/image/download?uuid=bf32e8bfc3d245b8904d71989e519a84)

- EBGP：运行于不同AS之间的BGP称为EBGP。为了防止AS间产生环路，当BGP设备接收EBGP对等体发送的路由时，会将带有本地AS号的路由丢弃。

- IBGP：运行于同一AS内部的BGP称为IBGP。为了防止AS内产生环路，BGP设备不将从IBGP对等体学到的路由通告给其他IBGP对等体，并与所有IBGP对等体建立全连接。为了解决IBGP对等体的连接数量太多的问题，BGP设计了[路由反射器](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564?section=j0dr)和[BGP联盟](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564?section=j0ds)。

  ![img](https://download.huawei.com/mdl/image/download?uuid=3023c65de85e48aa809c666f1f56987f)

  如果在AS内一台BGP设备收到EBGP邻居发送的路由后，需要通过另一台BGP设备将该路由传输给其他AS，此时推荐使用IBGP。



### BGP报文交互中的角色

BGP报文交互中分为Speaker和Peer两种角色。

- Speaker：发送BGP报文的设备称为BGP发言者（Speaker），它接收或产生新的报文信息，并发布（Advertise）给其它BGP Speaker。
- Peer：相互交换报文的Speaker之间互称对等体（Peer）。若干相关的对等体可以构成对等体组（Peer Group）。



### BGP的路由器号（Router ID）

BGP的Router ID是一个用于标识BGP设备的32位值，通常是IPv4地址的形式，在BGP会话建立时发送的Open报文中携带。对等体之间建立BGP会话时，每个BGP设备都必须有唯一的Router ID，否则对等体之间不能建立BGP连接。

BGP的Router ID在BGP网络中必须是唯一的，可以采用手工配置，也可以让设备自动选取。缺省情况下，BGP选择设备上的Loopback接口的IPv4地址作为BGP的Router ID。如果设备上没有配置Loopback接口，系统会选择接口中最大的IPv4地址作为BGP的Router ID。一旦选出Router ID，除非发生接口地址删除等事件，否则即使配置了更大的地址，也保持原来的Router ID。



## BGP工作原理



BGP对等体的建立、更新和删除等交互过程主要有5种报文、6种状态机和5个原则。



### BGP的报文

BGP对等体间通过以下5种报文进行交互，其中Keepalive报文为周期性发送，其余报文为触发式发送：

- Open报文：用于建立BGP对等体连接。
- Update报文：用于在对等体之间交换路由信息。
- Notification报文：用于中断BGP连接。
- Keepalive报文：用于保持BGP连接。
- Route-refresh报文：用于在改变路由策略后请求对等体重新发送路由信息。只有支持路由刷新（Route-refresh）能力的BGP设备会发送和响应此报文。



### BGP状态机

如[图9-2](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564/b38b2b8e#fig_dc_feature_bgp_000401)所示，BGP对等体的交互过程中存在6种状态机：空闲（Idle）、连接（Connect）、活跃（Active）、Open报文已发送（OpenSent）、Open报文已确认（OpenConfirm）和连接已建立（Established）。在BGP对等体建立的过程中，通常可见的3个状态是：Idle、Active和Established。

**图9-2** BGP对等体交互过程
![img](https://download.huawei.com/mdl/image/download?uuid=ddde2b0cfb284606bf46116c85e05572)

1. Idle状态是BGP初始状态。在Idle状态下，BGP拒绝邻居发送的连接请求。只有在收到本设备的Start事件后，BGP才开始尝试和其它BGP对等体进行TCP连接，并转至Connect状态。

   ![img](https://download.huawei.com/mdl/image/download?uuid=e5b767ae6d3845d89920a82cfe5e34b7)

   - Start事件是由一个操作者配置一个BGP过程，或者重置一个已经存在的过程或者路由器软件重置BGP过程引起的。
   - 任何状态中收到Notification报文或TCP拆链通知等Error事件后，BGP都会转至Idle状态。

2. 在Connect状态下，BGP启动连接重传定时器（Connect Retry），等待TCP完成连接。

   - 如果TCP连接成功，那么BGP向对等体发送Open报文，并转至OpenSent状态。
   - 如果TCP连接失败，那么BGP转至Active状态。
   - 如果连接重传定时器超时，BGP仍没有收到BGP对等体的响应，那么BGP继续尝试和其它BGP对等体进行TCP连接，停留在Connect状态。

3. 在Active状态下，BGP总是在试图建立TCP连接。

   - 如果TCP连接成功，那么BGP向对等体发送Open报文，关闭连接重传定时器，并转至OpenSent状态。
   - 如果TCP连接失败，那么BGP停留在Active状态。
   - 如果连接重传定时器超时，BGP仍没有收到BGP对等体的响应，那么BGP转至Connect状态。

4. 在OpenSent状态下，BGP等待对等体的Open报文，并对收到的Open报文中的AS号、版本号、认证码等进行检查。

   - 如果收到的Open报文正确，那么BGP发送Keepalive报文，并转至OpenConfirm状态。
   - 如果发现收到的Open报文有错误，那么BGP发送Notification报文给对等体，并转至Idle状态。

5. 在OpenConfirm状态下，BGP等待Keepalive或Notification报文。如果收到Keepalive报文，则转至Established状态，如果收到Notification报文，则转至Idle状态。

6. 在Established状态下，BGP可以和对等体交换Update、Keepalive、Route-refresh报文和Notification报文。

   - 如果收到正确的Update或Keepalive报文，那么BGP就认为对端处于正常运行状态，将保持BGP连接。
   - 如果收到错误的Update或Keepalive报文，那么BGP发送Notification报文通知对端，并转至Idle状态。
   - Route-refresh报文不会改变BGP状态。
   - 如果收到Notification报文，那么BGP转至Idle状态。
   - 如果收到TCP拆链通知，那么BGP断开连接，转至Idle状态。



### BGP对等体之间的交互原则

BGP设备将最优路由加入BGP路由表，形成BGP路由。BGP设备与对等体建立邻居关系后，采取以下交互原则：

- 从IBGP对等体获得的BGP路由，BGP设备只发布给它的EBGP对等体。
- 从EBGP对等体获得的BGP路由，BGP设备发布给它所有EBGP和IBGP对等体。
- 当存在多条到达同一目的地址的有效路由时，BGP设备只将最优路由发布给对等体。
- 路由更新时，BGP设备只发送更新的BGP路由。
- 所有对等体发送的路由，BGP设备都会接收。



## BGP与IGP交互



BGP与IGP在设备中使用不同的路由表，为了实现不同AS间相互通讯，BGP需要与IGP进行交互，即BGP路由表和IGP路由表相互引入。

### BGP引入IGP路由

BGP协议本身不发现路由，因此需要将其他路由引入到BGP路由表，实现AS间的路由互通。当一个AS需要将路由发布给其他AS时，AS边缘路由器会在BGP路由表中引入IGP的路由。为了更好的规划网络，BGP在引入IGP的路由时，可以使用路由策略进行路由过滤和路由属性设置，也可以设置MED值指导EBGP对等体判断流量进入AS时选路。

BGP引入路由时支持Import和Network两种方式：

- Import方式是按协议类型，将RIP、OSPF、ISIS等协议的路由引入到BGP路由表中。为了保证引入的IGP路由的有效性，Import方式还可以引入静态路由和直连路由。
- Network方式是逐条将IP路由表中已经存在的路由引入到BGP路由表中，比Import方式更精确。

### IGP引入BGP路由

当一个AS需要引入其他AS的路由时，AS边缘路由器会在IGP路由表中引入BGP的路由。为了避免大量BGP路由对AS内设备造成影响，当IGP引入BGP路由时，可以使用路由策略，进行路由过滤和路由属性设置。



## BGP的路由优选规则和负载分担



在BGP路由表中，到达同一目的地可能存在多条路由。此时BGP会选择其中一条路由作为最佳路由，并只把此路由发送给其对等体。BGP为了选出最佳路由，会根据BGP的路由优选规则依次比较这些路由的BGP属性。

### BGP属性

路由属性是对路由的特定描述，所有的BGP路由属性都可以分为以下4类，常见BGP属性类型如[表9-1](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564/2a827a34#tab_dc_feature_bgp_000701)所示：

- 公认必须遵循（Well-known mandatory）：所有BGP设备都可以识别此类属性，且必须存在于Update报文中。如果缺少这类属性，路由信息就会出错。
- 公认任意（Well-known discretionary）：所有BGP设备都可以识别此类属性，但不要求必须存在于Update报文中，即就算缺少这类属性，路由信息也不会出错。
- 可选过渡（Optional transitive）：BGP设备可以不识别此类属性，如果BGP设备不识别此类属性，但它仍然会接收这类属性，并通告给其他对等体。
- 可选非过渡（Optional non-transitive）：BGP设备可以不识别此类属性，如果BGP设备不识别此类属性，则会被忽略该属性，且不会通告给其他对等体。

**表9-1** BGP常见属性类型

| 属性名            | 类型         |
| ----------------- | ------------ |
| Origin属性        | 公认必须遵循 |
| AS_Path属性       | 公认必须遵循 |
| Next_Hop属性      | 公认必须遵循 |
| Local_Pref属性    | 公认任意     |
| MED属性           | 可选非过渡   |
| 团体属性          | 可选过渡     |
| Originator_ID属性 | 可选非过渡   |
| Cluster_List属性  | 可选非过渡   |

下面介绍几种常用的BGP路由属性：

- **Origin属性**

  Origin属性用来定义路径信息的来源，标记一条路由是怎么成为BGP路由的。它有以下3种类型：

  - IGP：具有最高的优先级。通过**network**命令注入到BGP路由表的路由，其Origin属性为IGP。
  - EGP：优先级次之。通过EGP得到的路由信息，其Origin属性为EGP。
  - Incomplete：优先级最低。通过其他方式学习到的路由信息。比如BGP通过**import-route**命令引入的路由，其Origin属性为Incomplete。

- **AS_Path属性**

  AS_Path属性按矢量顺序记录了某条路由从本地到目的地址所要经过的所有AS编号。在接收路由时，设备如果发现AS_Path列表中有本AS号，则不接收该路由，从而避免了AS间的路由环路。

  当BGP Speaker传播自身引入的路由时：

  - 当BGP Speaker将这条路由通告到EBGP对等体时，便会在Update报文中创建一个携带本地AS号的AS_Path列表。
  - 当BGP Speaker将这条路由通告给IBGP对等体时，便会在Update报文中创建一个空的AS_Path列表。

  当BGP Speaker传播从其他BGP Speaker的Update报文中学习到的路由时：

  - 当BGP Speaker将这条路由通告给EBGP对等体时，便会把本地AS编号添加在AS_Path列表的最前面（最左面）。收到此路由的BGP设备根据AS_Path属性就可以知道去目的地址所要经过的AS。离本地AS最近的相邻AS号排在前面，其他AS号按顺序依次排列。
  - 当BGP Speaker将这条路由通告给IBGP对等体时，不会改变这条路由相关的AS_Path属性。

- **Next_Hop属性**

  Next_Hop属性记录了路由的下一跳信息。BGP的下一跳属性和IGP的有所不同，不一定就是邻居设备的IP地址。通常情况下，Next_Hop属性遵循下面的规则：

  - BGP Speaker在向EBGP对等体发布某条路由时，会把该路由信息的下一跳属性设置为本地与对端建立BGP邻居关系的接口地址。
  - BGP Speaker将本地始发路由发布给IBGP对等体时，会把该路由信息的下一跳属性设置为本地与对端建立BGP邻居关系的接口地址。
  - BGP Speaker在向IBGP对等体发布从EBGP对等体学来的路由时，并不改变该路由信息的下一跳属性。

- **Local_Pref属性**

  Local_Pref属性表明路由器的BGP优先级，用于判断流量离开AS时的最佳路由。当BGP的设备通过不同的IBGP对等体得到目的地址相同但下一跳不同的多条路由时，将优先选择Local_Pref属性值较高的路由。Local_Pref属性仅在IBGP对等体之间有效，不通告给其他AS。Local_Pref属性可以手动配置，如果路由没有配置Local_Pref属性，BGP选路时将该路由的Local_Pref值按缺省值100来处理。

- **MED属性**

  MED（Multi-Exit Discriminator）属性用于判断流量进入AS时的最佳路由，当一个运行BGP的设备通过不同的EBGP对等体得到目的地址相同但下一跳不同的多条路由时，在其它条件相同的情况下，将优先选择MED值较小者作为最佳路由。

  MED属性仅在相邻两个AS之间传递，收到此属性的AS一方不会再将其通告给任何其他第三方AS。MED属性可以手动配置，如果路由没有配置MED属性，BGP选路时将该路由的MED值按缺省值0来处理。

- **团体属性**

  团体属性（Community）用于标识具有相同特征的BGP路由，使路由策略的应用更加灵活，同时降低了维护管理的难度。

  团体属性分为自定义团体属性和公认团体属性。公认团体属性如[表9-2](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564/2a827a34#tab_dc_feature_bgp_000702)所示。

  **表9-2** 公认团体属性

  | 团体属性名称        | 团体属性号               | 说明                                                         |
  | ------------------- | ------------------------ | ------------------------------------------------------------ |
  | Internet            | 0（0x00000000）          | 设备在收到具有此属性的路由后，可以向任何BGP对等体发送该路由。 |
  | No_Advertise        | 4294967042（0xFFFFFF02） | 设备收到具有此属性的路由后，将不向任何BGP对等体发送该路由。  |
  | No_Export           | 4294967041（0xFFFFFF01） | 设备收到具有此属性的路由后，将不向AS外发送该路由。           |
  | No_Export_Subconfed | 4294967043（0xFFFFFF03） | 设备收到具有此属性的路由后，将不向AS外发送该路由，也不向AS内其他子AS发布此路由。 |

- **Originator_ID属性和Cluster_List属性**

  Originator_ID属性和Cluster_List属性用于解决路由反射器场景中的环路问题，详细描述请参见[路由反射器](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564?section=j0dr)。

### BGP选择路由的策略

当到达同一目的地存在多条路由时，BGP依次对比下列属性来选择路由：

1. 优选协议首选值（PrefVal）最高的路由。

   协议首选值（PrefVal）是华为设备的特有属性，该属性仅在本地有效。

2. 优选本地优先级（Local_Pref）最高的路由。

   如果路由没有本地优先级，BGP选路时将该路由按缺省的本地优先级100来处理。

3. 依次优选手动聚合路由、自动聚合路由、**network**命令引入的路由、**import-route**命令引入的路由、从对等体学习的路由。

4. 优选AS路径（AS_Path）最短的路由。

5. 依次优选Origin类型为IGP、EGP、Incomplete的路由。

6. 对于来自同一AS的路由，优选MED值最低的路由。

7. 依次优选本地VPN路由、LocalCross路由、RemoteCross路由

   ![img](https://download.huawei.com/mdl/image/download?uuid=eceaf39c267f43619daaa3dfb239ab5f)

   LocalCross路由指本地VPN交叉路由或公私网互引路由。

   PE上某个VPN实例的VPNv4路由的ERT匹配其他VPN实例的IRT后复制到该VPN实例，称为LocalCross；从远端PE学习到的VPNv4路由的ERT匹配某个VPN实例的IRT后复制到该VPN实例，称为RemoteCross。

8. 优选从EBGP邻居学来的路由（EBGP路由优先级高于IBGP路由）。

9. 优选到BGP下一跳IGP度量值（metric）最小的路由。

   ![img](https://download.huawei.com/mdl/image/download?uuid=eceaf39c267f43619daaa3dfb239ab5f)

   在IGP中，对到达同一目的地址的不同路由，IGP根据本身的路由算法计算路由的度量值。

10. 优选Cluster_List最短的路由。

11. 优选Router ID最小的设备发布的路由。

    如果路由携带Originator_ID属性，选路过程中将比较Originator_ID的大小（不再比较Router ID），并优选Originator_ID最小的路由。

12. 优选从具有最小IP Address的对等体学来的路由。

### BGP负载分担

当到达同一目的地址存在多条等价路由时，可以通过BGP等价负载分担实现均衡流量的目的。形成BGP等价负载分担的条件是“BGP选择路由的策略”的1至8条规则中需要比较的属性完全相同。



路由聚合



在大规模的网络中，BGP路由表十分庞大，给设备造成了很大的负担，同时使发生路由振荡的几率也大大增加，影响网络的稳定性。

路由聚合是将多条路由合并的机制，它通过只向对等体发送聚合后的路由而不发送所有的具体路由的方法，减小路由表的规模。并且被聚合的路由如果发生路由振荡，也不再对网络造成影响，从而提高了网络的稳定性。

BGP在IPv4网络中支持自动聚合和手动聚合两种方式，而IPv6网络中仅支持手动聚合方式：

- 自动聚合：对BGP引入的路由进行聚合。配置自动聚合后，BGP将按照自然网段聚合路由（例如非自然网段A类地址10.1.1.1/24和10.2.1.1/24将聚合为自然网段A类地址10.0.0.0/8），并且BGP向对等体只发送聚合后的路由。
- 手动聚合：对BGP本地路由表中存在的路由进行聚合。手动聚合可以控制聚合路由的属性，以及决定是否发布具体路由。

为了避免路由聚合可能引起的路由环路，BGP设计了AS_Set属性。AS_Set属性是一种无序的AS_Path属性，标明聚合路由所经过的AS号。当聚合路由重新进入AS_Set属性中列出的任何一个AS时，BGP将会检测到自己的AS号在聚合路由的AS_Set属性中，于是会丢弃该聚合路由，从而避免了路由环路的形成。






[BGP配置 -- HW ](https://support.huawei.com/enterprise/zh/doc/EDOC1100075564/b5abc35a)



