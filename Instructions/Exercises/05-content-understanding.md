---
lab:
  title: 使用 Azure AI 内容理解分析内容
  module: Multimodal analysis with Content Understanding
---

# 使用 Azure AI 内容理解分析内容

在本练习中，你会使用 Azure AI Foundry 门户创建内容理解项目，该项目可从旅行保单中提取信息。 接着，你将在 Azure AI Foundry 门户中测试内容分析器，并通过内容理解 REST 接口使用它。

此练习大约需要 **30** 分钟。

## 创建内容理解项目

首先，使用 Azure AI Foundry 门户创建内容理解项目。

1. 在 Web 浏览器中打开 [Azure AI Foundry 门户](https://ai.azure.com)，网址为：`https://ai.azure.com`，然后使用 Azure 凭据登录。

    Azure AI Foundry 门户的主页如下如所示：

    ![Azure AI Foundry 门户的屏幕截图。](../media/ai-foundry-portal.png)

1. 在主页“快速查找”**** 部分的底部，选择“内容理解”****。
1. 在“内容理解”**** 页面，选择“新建内容理解项目”**** 按钮。
1. 在“项目概述”**** 步骤中，为项目设置以下属性；然后选择“下一步”****：
    - **项目名称**：`travel-insurance`
    - **说明**：`Insurance policy data extraction`
    - **中心**：新建中心
1. 在“创建中心”**** 步骤中，设置以下属性，然后选择“下一步”****：
    - **Azure AI 中心资源**：`content-understanding-hub`
    - Azure 订阅：选择自己的 Azure订阅
    - **资源组**：创建具有适当名称的新资源组**
    - 位置****：选择任何可用位置**
    - **Azure AI 服务**：创建具有适当名称的新 Azure AI 服务资源**
1. 在“存储设置”**** 步骤中，指定新的 AI 中心存储帐户，然后选择“下一步”****。
1. 在“检查”**** 页上，选择“创建项目”****。 然后等待项目及其相关资源创建完成。

    项目准备就绪后，将在“定义架构”**** 页中打开。

    ![新的内容理解项目的屏幕截图。](../media/content-understanding-project.png)

## 检查 Azure 资源

创建 AI 中心和项目时，Azure 订阅中会创建各种资源以支持该项目。

1. 在新的浏览器标签页中打开 [Azure 门户](https://portal.azure.com)，网址为：`https://portal.azure.com`，然后使用 Azure 凭据登录。
1. 转到已为中心创建的资源组，并查看已创建的 Azure 资源。

    ![Azure 资源的屏幕截图。](../media/azure-resources.png)

## 定义自定义架构

你将生成分析器，以便从旅行保险表单中提取信息。 首先，基于示例表单定义架构。

1. 从 `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/train-form.pdf` 下载 [train-form.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/train-form.pdf) 示例表单并将其保存在本地文件夹中。
1. 返回到包含内容理解项目的浏览器标签页，然后在“定义架构”**** 页上传刚才下载的 **train-form.pdf** 文件。
1. 选择“文档分析”**** 模板，然后选择“创建”****。

    架构编辑器提供了定义要从表单中提取的数据字段的方法，如右图所示。 表单如下所示：

    ![示例保险表单的屏幕截图。](../media/train-form.png)

    表单的数据字段包括：
    
    - 与保险持有人相关的个人详细信息集合。
    - 与需要保险的旅行相关的详细信息集合。
    - 签名和日期

    首先，我们将添加一个字段，以表格的形式表示个人详细信息，然后在表格中定义个人详细信息的子字段。

1. 选择“+添加新字段”**** 以使用以下值创建新字段：
    - **字段名称**：`PersonalDetails`
    - **字段说明**：`Policyholder information`
    - **值类型**：表格
1. 选择“**保存更改**” (&#10004;)，并注意会自动创建新子字段。
1. 使用以下值配置新子字段：
    - **字段名称**：`PolicyholderName`
    - **字段说明**：`Policyholder name`
    - **值类型**：字符串
    - **方法**：提取
1. 使用“**+ 添加新子字段**”按钮添加以下附加子字段：

    | 字段名称 | 字段描述 | 值类型 | 方法 |
    |--|--|--|--|
    | `StreetAddress` | `Policyholder address` | 字符串 | 提取 |
    | `City` | `Policyholder city` | 字符串 | 提取 |
    | `PostalCode` | `Policyholder post code` | 字符串 | 提取 |
    | `CountryRegion` | `Policyholder country or region` | 字符串 | 提取 |
    | `DateOfBirth` | `Policyholder birth date` | 日期 | 提取 |

1. 添加所有个人信息子字段后，使用“**后退**”按钮返回到架构的顶层。
1. 添加名为 **`TripDetails`** 的新*表*字段，以表示投保行程的详细信息。 然后将下列子字段添加进去：

    | 字段名称 | 字段描述 | 值类型 | 方法 |
    |--|--|--|--|
    | `DestinationCity` | `Trip city` | 字符串 | 提取 |
    | `DestinationCountry` | `Trip country or region` | 字符串 | 提取 |
    | `DepartureDate` | `Date of departure` | 日期 | 提取 |
    | `ReturnDate` | `Date of return` | 日期 | 提取 |

1. 返回到架构顶层，并添加以下两个单独的字段：

    | 字段名称 | 字段描述 | 值类型 | 方法 |
    |--|--|--|--|
    | `Signature` | `Policyholder signature` | 字符串 | 提取 |
    | `Date` | `Date of signature` | 日期 | 提取 |

1. 验证已完成的架构是否如下所示，然后将其保存。

    ![文档架构的屏幕截图。](../media/completed-schema.png)

1. 在“**测试分析器**”页上，如果分析未自动开始，请选择“**运行分析**”。 然后等待分析完成并查看表单上标识为与架构中的字段匹配的文本值。

    ![分析器测试结果的屏幕截图。](../media/test-analyzer.png)

    内容理解服务应正确标识与架构中的字段对应的文本。 如果尚未这样做，则可以使用“**标签数据**”页上传另一个示例表单，并显式标识每个字段的正确文本。

## 生成和测试分析器

现在，你已训练了一个模型来从保险表单中提取字段，接下来可以生成一个分析器以用于类似的表单。

1. 在左侧导航窗格中，选择“**生成分析器**”页。
1. 选择“**+ 生成分析器**”并生成具有以下属性的新分析器（按如下所示键入）：
    - **名称**：`travel-insurance-analyzer`
    - **说明**：`Insurance form analyzer`
1. 等待新分析器准备就绪（使用“**刷新**”按钮进行检查）。
1. 从 `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/test-form.pdf` 下载 [test-form.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/test-form.pdf) 并将其保存在本地文件夹中。
1. 返回到“**生成分析器**”页，然后选择“**旅行保险分析器**”链接。 将显示分析器架构中定义的字段。
1. 在**旅行保险分析器**页中，选择“**测试**”。
1. 使用“**+ 上传测试文件**”按钮上传 **test-form.pdf** 并运行分析，从测试表单中提取字段数据。

    ![测试表单分析结果的屏幕截图。](../media/test-form-results.png)

1. 查看“**结果**”选项卡，查看分析器返回的 JSON 格式结果。 在下一个任务中，你将使用内容理解 REST API 将表单提交到分析器，并返回采用此格式的结果。
1. 关闭“**旅行保险分析器**”页面。

## 使用内容理解 REST API

创建分析器后，可以通过内容理解 REST API 从客户端应用程序使用它。

1. 切换到包含 Azure 门户的浏览器标签页（如果已关闭，要在新选项卡中打开 `https://portal.azure.com`）。
1. 在内容理解中心的资源组中，打开 **Azure AI 服务**资源。
1. 在“**概述**”页上的“**密钥和终结点**”部分中，查看“**内容理解**”标签页。

    ![内容理解的密钥和终结点的屏幕截图。](../media/keys-and-endpoint.png)

    需要内容理解终结点和其中一个密钥才能从客户端应用程序连接到分析器。

1. 使用页面顶部搜索栏右侧的 **[\>_]** 按钮在 Azure 门户中创建新的 Cloud Shell，选择 ***PowerShell*** 环境。 Cloud Shell 在 Azure 门户底部的窗格中提供命令行界面，如下所示：

    ![带有 Cloud Shell 窗格的 Azure 门户的屏幕截图。](../media/cloud-shell.png)

    > **备注**：如果以前创建了使用 *Bash* 环境的 Cloud Shell，请将其切换到 ***PowerShell***。

1. 请注意，可以通过拖动窗格顶部的分隔条来调整 Cloud Shell 的大小，或使用窗格右上角的 **&#8212;**、**&#10530;** 和 **X** 图标来最小化、最大化和关闭窗格。 有关如何使用 Azure Cloud Shell 的详细信息，请参阅 [Azure Cloud Shell 文档](https://docs.microsoft.com/azure/cloud-shell/overview)。
1. 在 Cloud Shell 工具栏的“**设置**”菜单中，选择“**转到经典版本**”（这是使用代码编辑器所必需的）。

1. 在 PowerShell 窗格中，输入以下命令以克隆包含此练习的 GitHub 存储库：

    ```
    rm -r mslearn-ai-doc -f
    git clone https://github.com/microsoftlearning/mslearn-ai-document-intelligence mslearn-ai-doc
    ```

1. 克隆存储库后，导航到 **mslearn-ai-doc/Labfiles/05-content-understanding/code** 文件夹：

    ```
    cd mslearn-ai-doc/Labfiles/05-content-understanding/code
    ```

1. 输入以下命令以编辑已提供的 **analyze_doc.py** Python 代码文件：

    ```
    code analyze_doc.py
    ```
    Python 代码文件在代码编辑器中打开：

    ![包含 Python 代码的代码编辑器的屏幕截图。](../media/code-editor.png)

1. 在代码文件中，将 **\<CONTENT_UNDERSTANDING_ENDPOINT\>** 占位符替换为内容理解终结点，并将 **\<CONTENT_UNDERSTANDING_KEY\>** 占位符替换为 Azure AI 服务资源的密钥之一。

    > **提示**：需要调整大小或最小化 Cloud Shell 窗口以从 Azure 门户中的 Azure AI 服务资源页复制端点和密钥 - 请注意不要*关闭* Cloud Shell（否则需要重复上述步骤）

1. 替换占位符后，使用 **Ctrl+S** 命令保存更改，然后查看已完成的代码，其中：
    - 向内容理解终结点提交 HTTP POST 请求，指示**旅行保险分析器**基于其 URL 分析表单。
    - 检查 POST 操作的响应以检索分析操作的 ID。
    - 重复向内容理解服务提交 HTTP GET 请求，直到操作不再运行。
    - 如果操作成功，则显示 JSON 响应。
1. 使用 **Ctrl+Q** 命令关闭代码编辑器，同时使 cloud shell 命令行保持打开状态。
1. 在 cloud shell 命令行窗格中，输入以下命令以安装 Python **请求**库（在代码中使用）：

    ```
    pip install requests
    ```

1. 安装库后，在 cloud shell 命令行窗格中，输入以下命令以运行 Python 代码：

    ```
    python analyze_doc.py
    ```

1. 查看程序的输出，其中包括文档分析的 JSON 结果。

    > **提示**：cloud shell 控制台中的屏幕缓冲区可能不够大，无法显示整个输出。 如果要查看整个输出，请使用命令 `python analyze_doc.py > output.txt` 运行程序。 然后，程序完成后，使用命令 `code output.txt` 在代码编辑器中打开输出。

## 清理

如果已完成内容理解服务的使用，则应删除在本练习中创建的资源，以避免产生不必要的 Azure 成本。

1. 在 Azure AI Foundry 门户中，导航到“**旅行保险**”项目并将其删除。
1. 在 Azure 门户中，删除为这些练习创建的资源组。

