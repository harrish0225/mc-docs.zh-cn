#### <a name="expressroute-limits"></a>ExpressRoute 限制

下列限制适用于每个订阅的 ExpressRoute 资源。

| 资源 | 默认/最大限制 |
| --- | --- |
| 每个订阅的 ExpressRoute 线路数 |10 个 |
| 每个订阅每个区域的 ExpressRoute 线路数（Azure 资源管理器） |10 个 |
| 具有 ExpressRoute Standard 的 Azure 私用对等互连的最大路由数 |4,000 |
| 具有 ExpressRoute Premium 附加设备的 Azure 私用对等互连的最大路由数 |10,000 |
| 具有 ExpressRoute Standard 的 Azure Microsoft 对等互连的最大路由数 |200 |
| 具有 ExpressRoute Premium 附加设备的 Azure Microsoft 对等互连的最大路由数 |200 |
| 链接到不同对等互连位置中相同虚拟网络的最大 ExpressRoute 线路数 |4 |
| 每个 ExpressRoute 线路允许的虚拟网络链接数 | 请参阅下表 |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>每个 ExpressRoute 线路的虚拟网络数

| **线路大小** | **针对 Standard 的 VNet 链接数** | **使用 Premium 附加设备时的 VNet 链接数** |
|---|---|---|
| 50 Mbps | 10 个 | 20 个 |
| 100 Mbps | 10 个 | 25 |
| 200 Mbps | 10 个 | 25 |
| 500 Mbps | 10 个 | 40 |
| 1 Gbps | 10 个 | 50 |
| 2 Gbps | 10 个 | 60 |
| 5 Gbps | 10 | 75 |
| 10 Gbps | 10 个 | 100 |