---
lab:
  title: 使用 Azure AI 内容理解分析内容
  module: Multimodal analysis with Content Understanding
---

# 使用 Azure AI 内容理解分析内容

在本练习中，你将使用 Azure AI Foundry 门户创建一个内容理解项目，该项目可以从发票中提取信息。 接着，你将在 Azure AI Foundry 门户中测试内容分析器，并通过内容理解 REST 接口使用它。

此练习大约需要 **30** 分钟。

## 创建 Azure AI Foundry 项目

让我们首先创建 Azure AI Foundry 项目。

1. 在 Web 浏览器中打开 [Azure AI Foundry 门户](https://ai.azure.com)，网址为：`https://ai.azure.com`，然后使用 Azure 凭据登录。 关闭首次登录时打开的任何使用技巧或快速入门窗格，如有必要，请使用左上角的 **Azure AI Foundry** 徽标导航到主页，如下图所示：

    ![Azure AI Foundry 门户的屏幕截图。](../media/ai-foundry-portal.png)

1. 在主页中，选择“**+ 创建项目**”。
1. 在“**创建项目**”向导中，输入项目的有效名称，如果建议使用现有中心，请选择新建中心的选项。 然后查看将自动创建的 Azure 资源以支持中心和项目。
1. 选择“**自定义**”并为中心指定以下设置：
    - **中心名称**：*中心的有效名称*
    - **订阅**：Azure 订阅
    - **资源组**：*创建或选择资源组*
    - **位置**：选择以下任一区域\*
        - 美国西部
        - 瑞典中部
        - 澳大利亚东部
    - **连接 Azure AI 服务或 Azure OpenAI**：*新建 AI 服务资源*
    - **连接 Azure AI 搜索**：*使用唯一名称创建新的 Azure AI 搜索资源*

    > \*在撰写本文时，Azure AI 内容理解仅在这些区域中提供。

1. 选择“**下一步**”查看配置。 然后，选择“**创建**”并等待该进程完成。
1. 创建项目后，关闭显示的所有使用技巧，并查看 Azure AI Foundry 门户中的项目页面，如下图所示：

    ![Azure AI Foundry 门户中 Azure AI 项目详细信息的屏幕截图。](../media/ai-foundry-project.png)

## 创建内容理解分析器

你将生成分析器，以便从发票中提取信息。 首先，你将根据示例发票定义架构。

1. 在新的浏览器标签页中，从 `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1234.pdf` 下载 [invoice-1234.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1234.pdf) 示例表单，并将其保存在本地文件夹中。
1. 返回到包含 Azure AI Foundry 项目主页的标签页，然后在左侧的导航窗格中选择“**内容理解**”。
1. 在“**内容理解**”页面，选择顶部的“**自定义分析器**”选项卡。
1. 在“内容理解自定义分析器”页面，选择“**+ 创建**”，并使用以下设置创建任务：
    - **任务名**：发票分析
    - **说明**：从发票中提取数据
    - **Azure AI 服务连接**：*Azure AI Foundry 中心的 Azure AI 服务资源*
    - **Azure Blob 存储帐户**：*Azure AI Foundry 中心的默认存储帐户*
1. 等待任务创建完成。

    > **提示**：如果访问存储时发生错误，请等待一分钟后再试。

1. 在“**定义架构**”页面，上传 你刚刚下载的 **invoice-1234.pdf** 文件。
1. 选择**文档分析**模板，然后选择“创建”****。

    *发票分析*模板包含发票中常见的字段。 你可以使用架构编辑器删除任何不需要的建议字段，并添加你需要的任何自定义字段。

1. 在建议字段列表中，选择 **BillingAddress**。 上传的发票格式不需要此字段，请使用出现的 **“删除”字段** （**&#128465;**）图标将其删除。
1. 现在，请删除以下不需要的建议字段：
    - BillingAddressRecipient
    - CustomerAddressRecipient
    - CustomerId
    - CustomerTaxId
    - DueDate
    - InvoiceTotal
    - PaymentTerm
    - PreviousUnpaidBalance
    - PurchaseOrder
    - RemittanceAddress
    - RemittanceAddressRecipient
    - ServiceAddress
    - ServiceAddressRecipient
    - ShippingAddress
    - ShippingAddressRecipient
    - TotalDiscount
    - VendorAddressRecipient
    - VendorTaxId
    - TaxDetails
1. 使用“**+ 添加新字段**”按钮添加以下字段：

    | 字段名称 | 字段说明 | 值类型 | 方法 |
    |--|--|--|--|
    | `VendorPhone` | `Vendor telephone number` | 字符串 | 提取 |
    | `ShippingFee` | `Fee for shipping` | Number | 提取 |

1. 验证已完成的架构是否如下所示，然后选择“保存”****。
    ![发票架构的屏幕截图。](../media/invoice-schema.png)

1. 在“**测试分析器**”页上，如果分析未自动开始，请选择“**运行分析**”。 然后，等待分析完成，并查看发票上标识为与架构字段匹配的文本值。
1. 查看分析结果，应类似于：

    ![分析器测试结果的屏幕截图。](../media/analysis-results.png)

1. 在“**字段**”窗格中查看已标识字段的详细信息，随后在“**结果**”选项卡中查看其 JSON 表示形式。

## 生成和测试分析器

现在你已训练了一个模型来提取发票字段，接下来可以构建一个分析器，以处理类似的表单。

1. 选择“**生成分析器**”页面，然后选择 **+ 生成分析器**，并生成具有以下属性的新分析器（按如下所示键入）：
    - **名称**：`contoso-invoice-analyzer`
    - **说明**：`Contoso invoice analyzer`
1. 等待新分析器准备就绪（使用“**刷新**”按钮进行检查）。
1. 从 `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1235.pdf`下载 [invoice-1235.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1235.pdf) 并将其保存在本地文件夹。
1. 返回到“**生成分析器**”页面，然后选择 **contoso-invoice-analyzer** 链接。 将显示分析器架构中定义的字段。
1. 在 “**contoso-invoice-analyzer**” 页面，选择“**测试**”选项卡。
1. 使用“**+ 上传测试文件**”按钮上传 **invoice-1235.pdf** 并运行分析，从测试表单中提取字段数据。
1. 查看测试结果，并验证分析器是否正确提取了测试发票中的字段。
1. 关闭 **contoso-invoice-analyzer*** 页面。

## 使用内容理解 REST API

创建分析器后，可以通过内容理解 REST API 从客户端应用程序使用它。

1. 在“**项目详细信息**”区域中，记下**项目连接字符串**。 你将使用此连接字符串连接到客户端应用程序中的项目。
1. 打开新的浏览器选项卡（使 Azure AI Foundry 门户在现有选项卡中保持打开状态）。 然后在新选项卡中，浏览到 [Azure 门户](https://portal.azure.com)，网址为：`https://portal.azure.com`；如果出现提示，请使用 Azure 凭据登录。

    关闭任何欢迎通知以查看 Azure 门户主页。

1. 使用页面顶部搜索栏右侧的 **[\>_]** 按钮在 Azure 门户中新建 Cloud Shell，选择订阅中不含存储的 ***PowerShell*** 环境。

    在 Azure 门户底部的窗格中，Cloud Shell 提供命令行接口。 可以调整此窗格的大小或最大化此窗格，以便更易于使用。

    > **备注**：如果以前创建了使用 *Bash* 环境的 Cloud Shell，请将其切换到 ***PowerShell***。

1. 在 Cloud Shell 工具栏的“**设置**”菜单中，选择“**转到经典版本**”（这是使用代码编辑器所必需的）。

    **<font color="red">在继续作之前，请确保已切换到 Cloud Shell 的经典版本。</font>**

1. 在 Cloud Shell 窗格中，输入以下命令以克隆包含此练习代码文件的 GitHub 存储库（键入命令，或将其复制到剪贴板后，在命令行中右键单击并粘贴为纯文本）：

    ```
   rm -r mslearn-ai-foundry -f
   git clone https://github.com/microsoftlearning/mslearn-ai-document-intelligence mslearn-ai-doc
    ```

    > **提示**：在 Cloudshell 中时输入命令时，输出可能会占用大量屏幕缓冲区。 可以通过输入 `cls` 命令来清除屏幕，以便更轻松地专注于每项任务。

1. 克隆存储库后，导航到包含你的应用代码文件的文件夹：

    ```
   cd mslearn-ai-doc/Labfiles/05-content-understanding/code
   ls -a -l
    ```

1. 在 Cloud Shell 命令行窗格中，输入以下命令以安装将要使用的库：

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install dotenv azure-identity azure-ai-projects
    ```

1. 输入以下命令以编辑已提供的配置文件：

    ```
   code .env
    ```

    该文件已在代码编辑器中打开。

1. 在代码文件中，将 **YOUR_PROJECT_CONNECTION_STRING** 占位符替换为项目的连接字符串（从 Azure AI Foundry 门户中的项目“**概述**”页面复制），并确保**分析器**设置为分配给分析器的名称（应为 *contoso-invoice-analyzer*）
1. 替换占位符后，使用 Ctrl+S**** 命令保存更改，然后使用 Ctrl+Q**** 命令关闭代码编辑器，同时保持 Cloud Shell 命令行打开。

1. 在 Cloud Shell 命令行中，输入以下命令以编辑提供的 **analyze_invoice.py** Python 代码文件：

    ```
    code analyze_invoice.py
    ```
    Python 代码文件在代码编辑器中打开：

1. 查看代码，内容包括：
    - 标识要分析的发票文件，默认值为 **invoice-1236.pdf**。
    - 从项目中检索 Azure AI 服务资源的终结点和密钥。
    - 向内容理解终结点提交 HTTP POST 请求，指示分析图像。
    - 检查 POST 操作的响应以检索分析操作的 ID。
    - 重复向内容理解服务提交 HTTP GET 请求，直到操作不再运行。
    - 如果操作成功，分析 JSON 响应并显示检索到的值。
1. 使用 **Ctrl+Q** 命令关闭代码编辑器，同时使 cloud shell 命令行保持打开状态。
1. 在 Cloud Shell 命令行窗格中，输入以下命令以运行 Python 代码：

    ```
    python analyze_invoice.py invoice-1236.pdf
    ```

1. 查看程序输出。
1. 使用以下命令使用不同发票的运行程序：

    ```
    python analyze_invoice.py invoice-1235.pdf
    ```

    > **提示**：代码文件夹中有三个可用的发票文件（invoice-1234.pdf、invoice-1235.pdf 和 invoice-1236.pdf） 

## 清理

如果已完成内容理解服务的使用，则应删除在本练习中创建的资源，以避免产生不必要的 Azure 成本。

1. 在 Azure AI Foundry 门户中，导航到“**旅行保险**”项目并将其删除。
1. 在 Azure 门户中，删除为这些练习创建的资源组。

