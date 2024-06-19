---
lab:
  title: 使用预生成的文档智能模型
  module: Module 6 - Document Intelligence
---

# 使用预生成的文档智能模型

在本练习中，你将在 Azure 订阅中设置 Azure AI 文档智能资源。 你将使用 Azure AI 文档智能工作室和 C# 或 Python 将表单提交到该资源以供分析。

## 创建 Azure AI 文档智能资源

在调用 Azure AI 文档智能服务之前，必须创建资源以在 Azure 中托管该服务：

1. 在一个新的浏览器标签页中，打开 Azure 门户 ([https://portal.azure.com](https://portal.azure.com?azure-portal=true))，然后使用与 Azure 订阅关联的 Microsoft 帐户登录。
1. 在 Azure 门户主页上，导航到顶部搜索框并输入**文档智能**，然后按 **Enter**。
1. 在“**文档智能**”页中，选择“**创建**”。
1. 在**创建文档智能**页面上，使用以下内容配置资源：
    - **订阅**：Azure 订阅。
    - **资源组：** 选择或创建具有唯一名称（如 *DocIntelligenceResources*）的资源组。
    - **区域**：选择附近的区域。
    - **名称：** 输入全局唯一的名称。
    - **定价层级：** 选择**免费 F0**（如果没有可用的免费层级，请选择**标准 S0**）。
1. 然后选择“**查看 + 创建**”，选择“**创建**”。 等待 Azure 创建 Azure AI 文档智能资源。
1. 部署完成后，选择**转到资源**。 使此页面保持打开状态，以供本练习的其余部分使用。

## 使用“读取”模型

首先，使用 **Azure AI 文档智能工作室**和读取模型分析具有多种语言的文档。 将 Azure AI 文档智能工作室连接到刚刚创建的资源以执行分析：

1. 打开新的浏览器选项卡并转到 **Azure AI 文档智能工作室**[https://documentintelligence.ai.azure.com/studio](https://documentintelligence.ai.azure.com/studio)。
1. 在**文档分析**下，选择**读取**磁贴。
1. 如果系统要求你登录帐户，请使用 Azure 凭证。
1. 如果系统询问要使用哪个 Azure AI 文档智能资源，请选择创建 Azure AI 文档智能资源时使用的订阅和资源名称。
1. 在左侧文档列表中，选择 read-german.pdf****。

    ![显示 Azure AI 文档智能工作室中的“读取”页面的屏幕截图。](../media/read-german-sample.png#lightbox)

1. 在左上角，选择“分析选项”，在“分析选项”窗格中启用“语言”复选框（“可选检测”下），然后单击“保存”********************。 
1. 在左上角，选择**运行分析**。
1. 分析完成后，从图像中提取的文本将显示在**内容**选项卡中的右侧。查看此文本并将其与原始图像中的文本进行比较，以确保准确性。
1. 选择**结果**选项卡。此选项卡显示提取的 JSON 代码。 
1. 滚动到**结果**选项卡中 JSON 代码的底部。请注意，读取模型检测到每个跨度的语言。 大多数跨度采用德语（语言代码 `de`），但你可在跨度中找到其他语言代码（例如最后一个跨度中的英语 - 语言代码 `en`）。

    ![屏幕截图显示 Azure AI 文档智能工作室中读取模型中结果的两个范围的语言检测。](../media/language-detection.png#lightbox)

## 准备在 Visual Studio Code 中开发应用

现在，让我们探索使用 Azure 文档智能服务 SDK 的应用。 你将使用 Visual Studio Code 开发应用。 应用程序的代码文件已在 GitHub repo 中提供。

> **提示：** 如果您已经克隆了 **mslearn-ai-document-intelligence repo**，请在 Visual Studio Code 中打开它。 否则，请按照以下步骤将其克隆到开发环境中。

1. 启动 Visual Studio Code。
1. 打开面板 (SHIFT+CTRL+P) 并运行“**Git：克隆**”命令，以将 `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` 存储库克隆到本地文件夹（任意文件夹均可）。
1. 克隆存储库后，在 Visual Studio Code 中打开文件夹。
1. 等待其他文件安装完毕，以支持存储库中的 C# 代码项目。

    > **注意**：如果系统提示你添加生成和调试所需的资产，请选择**以后再说**。 如果系统提示*检测到文件夹中有 Azure 功能项目*，则可以放心地关闭该消息。

## 配置应用程序

已提供适用于 C# 和 Python 的应用程序，以及用于测试文档智能的 pdf 样本文件。 这两个应用具有相同的功能。 首先，你将使用 Azure 文档智能资源完成应用程序的一些关键部分以进行启用。

1. 检查以下发票，并记下某中某些字段和值。 这是代码将分析的发票。

    ![显示发票文档示例的截图。](../media/sample-invoice.png#lightbox)

1. 在 Visual Studio Code 的**资源管理器**窗格中，浏览到 **Labfiles/01-prebuild-models** 文件夹，并根据你的语言首选项展开 **C-Sharp** 文件夹或 **Python** 文件夹。 每个文件夹都包含要集成 Azure 文档智能功能的应用程序的特定语言文件。

1. 右键单击 **包含代码文件的 CSharp** 或 **Python** 文件夹，然后选择 **打开集成终端**。 然后根据语言偏好运行相应的命令，安装 Azure 表单识别器（文档智能的前身）SDK 包：

    **C#：**

    ```powershell
    dotnet add package Azure.AI.FormRecognizer --version 4.1.0
    ```

    **Python**：

    ```powershell
    pip install azure-ai-formrecognizer==3.3.0
    ```

## 添加代码以使用 Azure 文档智能服务

现在你可以使用 SDK 评估 pdf 文件了。

1. 切换到在 Azure 门户中显示 Azure AI 文档智能概述的浏览器选项卡。 在左侧窗格中的*资源管理*下，选择**密钥和终结点**。 在**终结点**值右侧，单击**复制到剪贴板**按钮。
1. 在**资源管理器窗格中的 **CSharp** 或 **Python** 文件夹中，打开首选语言的代码文件，并替换为`<Endpoint URL>`刚刚**复制的字符串：

    **C#**: ***Program.cs***

    ```csharp
    string endpoint = "<Endpoint URL>";
    ```

    **Python**： ***document-analysis.py***

    ```python
    endpoint = "<Endpoint URL>"
    ```

1. 切换到在 Azure 门户中显示 Azure AI 文档智能**密钥和终结点**的浏览器选项卡。 在**密钥 1**值右侧，单击*复制到剪贴板**按钮。
1. 在 Visual Studio Code 中的代码文件中，找到此行，并替换为 `<API Key>` 刚刚复制的字符串：

    **C#**

    ```csharp
    string apiKey = "<API Key>";
    ```

    **Python**

    ```python
    key = "<API Key>"
    ```

1. 找到注释 `Create the client`。 接下来，在新行上输入以下代码：

    **C#**

    ```csharp
    var cred = new AzureKeyCredential(apiKey);
    var client = new DocumentAnalysisClient(new Uri(endpoint), cred);
    ```

    **Python**

    ```python
    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )
    ```

1. 找到注释 `Analyze the invoice`。 接下来，在新行上输入以下代码：

    **C#**

    ```csharp
    AnalyzeDocumentOperation operation = await client.AnalyzeDocumentFromUriAsync(WaitUntil.Completed, "prebuilt-invoice", fileUri);
    ```

    **Python**

    ```python
    poller = document_analysis_client.begin_analyze_document_from_url(
        fileModelId, fileUri, locale=fileLocale
    )
    ```

1. 找到注释 `Display invoice information to the user`。 接下来，在新行上输入以下代码：

    **C#**

    ```csharp
    AnalyzeResult result = operation.Value;
    
    foreach (AnalyzedDocument invoice in result.Documents)
    {
        if (invoice.Fields.TryGetValue("VendorName", out DocumentField? vendorNameField))
        {
            if (vendorNameField.FieldType == DocumentFieldType.String)
            {
                string vendorName = vendorNameField.Value.AsString();
                Console.WriteLine($"Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}.");
            }
        }
    ```

    **Python**

    ```python
    receipts = poller.result()
    
    for idx, receipt in enumerate(receipts.documents):
    
        vendor_name = receipt.fields.get("VendorName")
        if vendor_name:
            print(f"\nVendor Name: {vendor_name.value}, with confidence {vendor_name.confidence}.")
    ```

    > [!NOTE]
    > 已添加代码以显示供应商名称。 初学者项目还包括用于显示*客户名称*和*发票总计*的代码。

1. 保存代码文件中的更改。

1. 在交互式终端窗格中，确保文件夹上下文是首选语言的文件夹。 然后，输入以下命令来创建应用程序。

1. ***如果仅使用 C#***，则输入以下命令来构建项目：

    **C#：**

    ```powershell
    dotnet build
    ```

1. 若要运行代码，请输入以下命令：

    **C#：**

    ```powershell
    dotnet run
    ```

    **Python**：

    ```powershell
    python document-analysis.py
    ```

该计划显示具有置信度级别的供应商名称、客户名称和发票总计。 将报告的值与在本部分开头呈现的示例发票进行比较。

## 清理

如果您已完成 Azure 资源的使用，请记住在 [Azure 门户](https://portal.azure.com/?azure-portal=true)中删除该资源，以避免进一步收费。

## 详细信息

有关文档智能服务的更多信息，请参阅[文档智能文档](https://learn.microsoft.com/azure/ai-services/document-intelligence/?azure-portal=true)。
