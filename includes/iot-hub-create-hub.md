1. 登录到 [Azure 门户][lnk-portal]。
1. 选择“创建资源” > “物联网” > “IoT 中心”。
   
    ![屏幕截图显示 Azure 门户到 IoT 中心的导航](./media/iot-hub-create-hub/create-iot-hub1.png)

1. 在“IoT 中心”窗格中，输入 IoT 中心的以下信息：

   * **订阅**：选择需要将其用于创建此 IoT 中心的订阅。

   * **资源组**：创建用于托管 IoT 中心的资源组，或使用现有的资源组。 有关详细信息，请参阅[使用资源组管理 Azure 资源](../articles/azure-resource-manager/resource-group-portal.md)。

   * **区域**：选择最近的位置。

   * **名称**：创建 IoT 中心的名称。 如果输入的名称可用，会显示一个绿色复选标记。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![IoT 中心基本信息窗口](./media/iot-hub-create-hub/create-iot-hub2.png)

2. 选择“下一步: 大小和规模”，以便继续创建 IoT 中心。 

3. 选择“定价和缩放层”。 就本文来说，请选择“F1 - 免费”层（前提是此层在订阅上仍然可用）。 有关详细信息，请参阅[定价和缩放层][lnk-pricing]。

   ![IoT 中心大小和规模窗口](./media/iot-hub-create-hub/create-iot-hub3.png)

4. 选择“查看 + 创建”。

1. 查看 IoT 中心信息，然后单击“创建”。 创建 IoT 中心可能需要数分钟的时间。 可在“通知”窗格中监视进度。

<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png

<!-- Links -->
[lnk-portal]: https://portal.azure.n/
[lnk-pricing]: https://www.azure.cn/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md