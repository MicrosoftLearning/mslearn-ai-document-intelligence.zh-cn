---
lab:
  title: 创建组合型文档智能模型
  module: Module 6 - Document Intelligence
---

# 创建组合型文档智能模型

在本练习中，你将创建并训练两个用于分析不同税务表的自定义模型。 然后，你将创建一个包含这两个自定义模型的组合模型。 你将通过提交一个表单来测试模型，并检查它是否正确识别文档类型和标记字段。

## 设置资源

我们将使用脚本创建 Azure AI 文档智能资源、包含示例表单的存储帐户和资源组：

1. 启动 Visual Studio Code。
1. 打开面板 (SHIFT+CTRL+P) 并运行“**Git：克隆**”命令，以将 `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` 存储库克隆到本地文件夹（任意文件夹均可）。
1. 克隆存储库后，在 Visual Studio Code 中打开文件夹。
1. 等待其他文件安装完毕，以支持存储库中的 C# 代码项目。

    > **注意**：如果系统提示你添加生成和调试所需的资产，请选择**以后再说**。

1. 右键单击“03-composed-model”目录，在集成终端中打开，并执行安装脚本：****

   ``` bash
   bash setup.sh
   ```

## 创建 1040 表单自定义模型

若要创建组合模型，必须先创建两个或多个自定义模型。 若要创建首个自定义模型，请执行以下操作：

1. 在新的浏览器选项卡中，启动[Azure AI 文档智能工作室](https://formrecognizer.appliedai.azure.com/studio)。
1. 向下滚动，然后在“自定义模型”下选择“自定义模型”********。
1. 如果系统要求你登录帐户，请使用 Azure 凭据。
1. 如果系统询问要使用哪个 Azure AI 文档智能资源，请选择创建 Azure AI 文档智能资源时使用的订阅和资源名称。
1. 在“我的项目”**** 下，选择“+ 创建项目”****。
1. 在“项目名称”文本框中，键入“1040 表单”，然后选择“继续”************。
1. 在“配置服务资源”页面的“订阅”下拉列表中，选择你的 Azure 订阅********。
1. 在“**资源组**”下拉列表中，选择“**DocumentIntelligenceResources**”。
1. 在“**Azure AI 文档智能或 Azure AI 服务资源**”下拉列表中，选择“**DocumentIntelligence**”
1. 在“API 版本”下拉列表中，确保已选中“2022-06-30-preview”，然后选择“继续”************。

    :::image type="content" source="../media/4-configure-service-resources.png" alt-text="屏幕截图显示了 Azure AI 文档智能工作室自定义模型向导中的“配置服务资源”页。" lightbox="../media/4-configure-service-resources.png":::

1. 在“配置训练数据源”页面的“订阅”下拉列表中，选择你的 Azure 订阅********。
1. 在“**资源组**”下拉列表中，选择“**DocumentIntelligenceResources**”。
1. 在“存储帐户”下拉列表中，选择列出的唯一存储帐户****。
1. 在“Blob 容器”下拉列表中，选择“1040examples”，然后选择“继续”************。

    :::image type="content" source="../media/4-connect-training-data-source.png" alt-text="屏幕截图显示了 Azure AI 文档智能工作室自定义模型向导中的“连接训练数据源”页。" lightbox="../media/4-connect-training-data-source.png":::

1. 在“查看和创建”页上选择“创建项目”********。

## 标记 1040 表单自定义模型

现在，让我们标记示例表单中的字段：

1. 在“标签数据”页的右上角，选择 **+**，然后选择“字段”********。

    :::image type="content" source="../media/4-add-label.png" alt-text="屏幕截图显示了如何在 Azure AI 文档智能工作室中新增标签。" lightbox="../media/4-add-label.png":::

1. 键入“FirstName”，然后按 <kbd>Enter</kbd>****。
1. 在文档中，选择“John”，然后选择“FirstName”********。

    :::image type="content" source="../media/4-label-first-name.png" alt-text="屏幕截图显示了如何在 Azure AI 文档智能工作室中完成新标签。" lightbox="../media/4-label-first-name.png":::

1. 在页面右上角，选择 **+**，然后选择“字段”****。
1. 键入“LastName”，然后按 <kbd>Enter</kbd>****。
1. 在文档中，选择“Doe”，然后选择“LastName”********。
1. 在页面右上角，选择 **+**，然后选择“字段”****。
1. 键入“City”，然后按 <kbd>Enter</kbd>****。
1. 在文档中，选择“Los Angeles”，然后选择“City”********。
1. 在页面右上角，选择 **+**，然后选择“字段”****。
1. 键入“State”，然后按 <kbd>Enter</kbd>****。
1. 在文档中，选择“CA”，然后选择“State”********。
1. 对左侧列表中的其余表单重复此标记过程。 标记相同的四个字段：FirstName、LastName、City 和 State********。

> [!IMPORTANT]
> 就本练习而言，我们仅使用五个示例表单并仅标记四个字段。 在真实模型中，应使用尽可能多的样本来最大限度地提高预测的准确性和置信度。 还应标记表单中的所有可用字段，而不仅仅是这四个字段。

## 训练 1040 表单自定义模型

既然已经标记好了示例表单，我们就可以训练第一个自定义模型了：

1. 在 Azure AI 文档智能工作室中，选择“**训练**”。
1. 在“训练新模型”对话框的“模型 ID”文本框中，键入“1040FormsModel”************。
1. 在“生成模型”下拉列表中，选择“模板”，然后选择“训练”************。 
1. 在“正在进行训练”对话框中，选择“转到模型”********。

## 创建 1099 表单自定义模型

现在，必须创建第二个模型，你将使用示例 1099 税务表对其进行训练：

1. 在 Azure AI 文档智能工作室中，选择“**自定义模型**”。
1. 在“我的项目”**** 下，选择“+ 创建项目”****。
1. 在“项目名称”文本框中，键入“1099 表单”，然后选择“继续”************。
1. 在“配置服务资源”页面的“订阅”下拉列表中，选择你的 Azure 订阅********。
1. 在“**资源组**”下拉列表中，选择“**DocumentIntelligenceResources**”。
1. 在“**Azure AI 文档智能或 Azure AI 服务资源**”下拉列表中，选择“**DocumentIntelligence**”
1. 在“API 版本”下拉列表中，确保已选中“2022-06-30-preview”，然后选择“继续”************。

    :::image type="content" source="../media/4-configure-service-resources.png" alt-text="屏幕截图显示了 Azure AI 文档智能工作室自定义模型向导中的“配置服务资源”页。" lightbox="../media/4-configure-service-resources.png":::

1. 在“配置训练数据源”页面的“订阅”下拉列表中，选择你的 Azure 订阅********。
1. 在“**资源组**”下拉列表中，选择“**DocumentIntelligenceResources**”。
1. 在“存储帐户”下拉列表中，选择列出的唯一存储帐户****。
1. 在“Blob 容器”下拉列表中，选择“1099examples”，然后选择“继续”************。
1. 在“查看和创建”页上选择“创建项目”********。

## 标记 1099 表单自定义模型

现在，使用一些字段标记示例表单：

1. 在“标签数据”页的右上角，选择 **+**，然后选择“字段”********。
1. 键入“FirstName”，然后按 <kbd>Enter</kbd>****。
1. 在文档中，选择“John”，然后选择“FirstName”********。
1. 在页面右上角，选择 **+**，然后选择“字段”****。
1. 键入“LastName”，然后按 <kbd>Enter</kbd>****。
1. 在文档中，选择“Doe”，然后选择“LastName”********。
1. 在页面右上角，选择 **+**，然后选择“字段”****。
1. 键入“City”，然后按 <kbd>Enter</kbd>****。
1. 在文档中，选择“New Haven”，然后选择“City”********。
1. 在页面右上角，选择 **+**，然后选择“字段”****。
1. 键入“State”，然后按 <kbd>Enter</kbd>****。
1. 在文档中，选择“CT”，然后选择“State”********。
1. 对左侧列表中的其余表单重复此标记过程。 标记相同的四个字段：FirstName、LastName、City 和 State********。

## 训练 1099 表单自定义模型

现在可以训练第二个自定义模型：

1. 在 Azure AI 文档智能工作室中，选择“**训练**”。
1. 在“训练新模型”对话框的“模型 ID”文本框中，键入“1099FormsModel”************。
1. 在“生成模型”下拉列表中，选择“模板”，然后选择“训练”************。 
1. 在“正在进行训练”对话框中，选择“转到模型”********。
1. 训练过程可能需要数分钟。 请时而刷新浏览器，直到两个模型都显示“已成功”状态****。

## 创建和组装组合模型

分析 1040 和 1099 税务表的两个自定义模型现已完成。 可以继续创建组合模型：

1. 在 Azure AI 文档智能模型页中，选择“**1040FormsModel**”和“**1099FormsModel**”。
1. 在模型列表顶部，选择“组合”****。

    :::image type="content" source="../media/4-start-compose-model.png" alt-text="屏幕截图显示了如何在 Azure AI 文档智能工作室中开始组合模型。" lightbox="../media/4-start-compose-model.png":::

1. 在“组合新模型”对话框的“模型 ID”文本框中，键入“TaxFormsModel”，然后选择“组合”****************。 Azure AI 文档智能创建组合模型，并将其显示在自定义模型列表中：

## 使用组合模型

现在，组合模型已完成，让我们使用示例形式对其进行测试：

1. 在 [Azure 门户](https://portal.azure.com/learn.docs.microsoft.com?azure-portal=true)中，选择“所有资源”，然后选择“formsrecstorage&lt;xxxxx&gt;”存储帐户，其中 &lt;xxxxx&gt; 是一个随机数********。
1. 在“数据存储”下选择“容器”，然后选择“TestDoc”************。
1. 在“f1040_7.pdf”的右侧，选择“...”，然后选择“下载”************。
1. 将 PDF 文档保存到本地计算机并记下保存的位置。
1. 在 Azure AI 文档智能工作室中，依次选择“**TaxFormsModel**”、“**测试**”。
1. 选择“+ 添加”，然后浏览到 PDF 文档的保存位置****。
1. 选择“f1040_7.pdf”，然后选择“打开”********。
1. 选择“分析”。 Azure AI 文档智能使用组合模型分析表单。

    :::image type="content" source="../media/4-composed-model-analysis.png" alt-text="屏幕截图显示来如何在 Azure AI 文档智能工作室中使用组合模型。" lightbox="../media/4-composed-model-analysis.png":::

1. 你分析的文档是 1040 税务表的一个示例。 检查 DocType 属性以查看是否使用了正确的自定义模型****。 还要检查模型标识的 FirstName、LastName、City 和 State 值****************。

## 清理练习资源

现在你已经了解了组合模型的工作原理，让我们删除你在 Azure 订阅中创建的资源。

1. 在 [Azure 门户](https://portal.azure.com/learn.docs.microsoft.com?azure-portal=true)中，选择“资源组”。
1. 在“**资源组**”列表中，依次选择“**DocumentIntelligenceResources**”、“**删除资源组**”。 
1. 在“**键入资源组名称**”文本框中，键入“**DocumentIntelligenceResources**”，然后选择“**删除**”以删除文档智能资源和存储帐户。

## 了解详细信息

- [组合自定义模型](/azure/ai-services/document-intelligence/concept-composed-models)
- [为自定义模型生成训练数据集](/azure/applied-ai-services/form-recognizer/how-to-guides/build-custom-model-v3)
