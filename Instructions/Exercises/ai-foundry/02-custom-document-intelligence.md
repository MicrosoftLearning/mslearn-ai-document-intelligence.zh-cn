---
lab:
  title: 从表单中提取数据
  module: Module 6 - Document Intelligence
---

# 从表单中提取数据

假设公司当前要求员工手动购买订单表并将数据输入到数据库中。 他们希望利用 AI 服务改进数据输入过程。 你决定构建一个机器学习模型，该模型将读取表单并生成可用于自动更新数据库的结构化数据。

**Azure AI** 文档智能是一项 Azure AI 服务，可帮助用户构建自动数据处理软件。 此软件可使用光学字符识别 (OCR) 从表单中提取文字、密钥对和表。 Azure AI 文档智能预置了用于识别发票、收据和名片的模型。 该服务还提供训练自定义模式的功能。 在本练习中，我们将重点关注生成自定义模型。

## 准备在 Visual Studio Code 中开发应用程序

现在，让我们使用服务 SDK，用 Visual Studio Code 开发一个应用程序。 GitHub repo 中提供了应用程序的代码文件。

> **提示：** 如果您已经克隆了 **mslearn-ai-document-intelligence repo**，请在 Visual Studio Code 中打开它。 否则，请按照以下步骤将其克隆到开发环境中。

1. 启动 Visual Studio Code。
1. 打开面板 (SHIFT+CTRL+P) 并运行“**Git：Clone**”命令，以将 `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` 存储库克隆到本地文件夹（任意文件夹均可）。
1. 克隆存储库后，在 Visual Studio Code 中打开文件夹。

    > **注意**：如果 Visual Studio Code 显示一条弹出消息，提示你信任打开的代码，请单击弹出窗口中的“是，我信任该作者”选项。

1. 等待其他文件安装完毕，以支持存储库中的 C# 代码项目。

    > **注意**：如果系统提示你添加生成和调试所需的资产，请选择**以后再说**。 如果系统提示*检测到文件夹中有 Azure 功能项目*，则可以放心地关闭该消息。

## 创建 Azure AI 文档智能资源

要使用 Azure AI 文档智能服务，需要在 Azure 订阅中添加 Azure AI 文档智能或 Azure AI 服务资源。 使用 Azure 门户创建资源。

1. 在一个浏览器标签页中，打开 Azure 门户 (`https://portal.azure.com`)，然后使用与 Azure 订阅关联的 Microsoft 帐户登录。
1. 在 Azure 门户主页上，导航到顶部搜索框并输入**文档智能**，然后按 **Enter**。
1. 在“**文档智能**”页中，选择“**创建**”。
1. 在**创建文档智能**页面上，使用以下内容配置资源：
    - **订阅**：Azure 订阅。
    - **资源组：** 选择或创建具有唯一名称（如 *DocIntelligenceResources*）的资源组。
    - **区域**：选择附近的区域。
    - **名称：** 输入全局唯一的名称。
    - **定价层级：** 选择**免费 F0**（如果没有可用的免费层级，请选择**标准 S0**）。
1. 然后选择“**查看 + 创建**”，选择“**创建**”。 等待 Azure 创建 Azure AI 文档智能资源。
1. 当部署完成后，选择“**转到资源**”，查看资源的“**概览**”页面。 

## 收集用于训练的文档

您将使用像这样的示例表单来训练和测试模型： 

![本项目中使用的发票图像。](../media/Form_1.jpg)

1. 返回到 **Visual Studio Code**。 在*浏览器*窗格中，打开 **Labfiles/02-custom-document-intelligence** 文件夹并展开 **sample-forms** 文件夹。 注意该文件夹中有以 **.json** 和 **.jpg** 结尾的文件 。

    你将使用 **.jpg** 文件来训练模型。  

    已生成 **.json** 文件，其中包含标签信息。 文件将连同表单一起上传到 blob 存储容器中。

    在 Visual Studio Code 中选择*sample-forms*文件夹中的图片，即可查看这些图片。

1. 返回 **Azure 门户**，如果尚未导航到资源的**概述**页面，则导航到该页面。 在*基本要素*部分下，查看创建文档智能资源的**资源组**。

1. 记下资源组“**概述**”页上的“**订阅 ID**”和“**位置**”。 后续步骤中需要这些值和**资源组**名称。

    ![资源组页面的示例。](../media/resource_group_variables.png)

1. 在 Visual Studio Code 中，在*浏览器*窗格中浏览到 **Labfiles/02-custom-document-intelligence** 文件夹，然后根据语言首选项展开 **CSharp** 或 **Python** 文件夹。 每个文件夹都包含要集成 Azure OpenAI 功能的应用程序的特定语言文件。

1. 右键单击包含代码文件的 **CSharp** 或 **Python** 文件夹，选择**打开集成终端**。 

1. 在终端中运行以下命令登录 Azure。 **az 登录**命令将打开 Microsoft Edge 浏览器，请使用创建 Azure AI 文档智能资源时使用的同一帐户登录。 登录后，关闭浏览器窗口。

    ```powershell
    az login
    ```

1. 返回到 Visual Studio Code。 在终端窗格中，运行以下命令以列出 Azure 位置。

    ```powershell
    az account list-locations -o table
    ```

1. 在输出中，找到与资源组位置相对应的“**名称**”值（例如，对于*美国东部*，对应的名称是 *eastus*） 。

    > **重要说明**：记录该“**名称**”值并在步骤 11 中使用它。

1. 在 Visual Studio Code 的 **Labfiles/02-custom-document-intelligence** 文件夹中，选择 **setup.cmd**。 你将使用此处理脚本来运行创建需要的其他 Azure 资源所需的 Azure 命令行接口 (CLI) 命令。

1. 在 **setup.cmd** 脚本中，查看该命令。 程序将会执行以下操作：
    - 在 Azure 资源组中创建存储帐户
    - 将本地 *sampleforms* 文件夹中的文件上传到存储帐户中名为 *sampleforms* 的容器
    - 打印共享访问签名 URI

1. 使用你部署文档智能资源时使用的订阅、资源组和位置名称对应的值，修改 **subscription_id**、**resource_group** 和 **location** 变量声明。
然后**保存**更改。

    为练习保留 **expiry_date** 变量的默认值。 此变量在生成共享访问签名 (SAS) URI 时使用。 实际操作时，应为 SAS 设置适当的到期日期。 可以在[此处](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works)详细了解 SAS。  

1. 在 **Labfiles/02-custom-document-intelligence** 文件夹的终端中，输入以下命令运行脚本：

    ```PowerShell
    $currentdir=(Get-Item .).FullName
    cd ..
    ./setup.cmd
    cd $currentdir

    ```

1. 脚本完成后，查看显示的输出。

1. 在 Azure 门户中，刷新资源组并验证它包含刚刚创建的 Azure 存储帐户。 打开存储帐户，并在左侧窗格中选择“**存储浏览器**”。 然后在存储浏览器中，展开“**BLOB 容器**”并选择“**sampleforms**”容器以验证已从本地 **02-custom-form/sample-forms** 文件夹上传文件  。

## 使用文档智能工作室训练模型

现在，你将使用上传到存储帐户的文件来训练模型。

1. 在浏览器中，导航到 Document Intelligence Studio，网址是 `https://documentintelligence.ai.azure.com/studio`。
1. 向下滚动到**自定义模型**部分，然后选择**自定义提取模型**磁贴。
1. 如果系统要求你登录帐户，请使用 Azure 凭证。
1. 如果系统询问要使用哪个 Azure AI 文档智能资源，请选择创建 Azure AI 文档智能资源时使用的订阅和资源名称。
1. 在“**我的项目**”下，选择“**创建项目**”。 使用以下配置：

    - **项目名称：** 输入一个唯一的名称。
        - 选择“*继续*”。
    - **配置服务资源：** 选择之前在本实验室中创建的订阅、资源组和文档智能资源。 选中*设置为默认*复选框。 保留默认 API 版本。 
        - 选择“*继续*”。
    - **连接训练数据源：** 选择设置脚本创建的订阅、资源组和存储帐户。 选中*设置为默认*复选框。 选择 `sampleforms`blob 容器，文件夹路径留空。
        - 选择“*继续*”。
    - 选择“*创建项目*”

1. 创建项目后，选择屏幕右上角的“**训练**”以训练模型。 使用以下配置：
    - **模型 ID：***提供一个全局唯一的名称（下一步将需要模型 ID 名称）。* 
    - **构建模式：** 模板。
1. 选择“**转到模型**”。
1. 训练可能需要一些时间。 完成后，你将看到通知。

## 测试自定义文档智能模型

1. 返回到 Visual Studio Code。 在终端中，根据你的语言偏好运行相应的命令来安装 Document Intelligence 软件包：

    **C#：**

    ```powershell
    dotnet add package Azure.AI.FormRecognizer --version 4.1.0
    ```

    **Python**：

    ```powershell
    pip install azure-ai-formrecognizer==3.3.3
    ```

1. 在 Visual Studio Code 中，在 **Labfiles/02-custom-document-intelligence** 文件夹中，选择你正在使用的语言。 用以下值编辑配置文件（**appsettings.json** 或 **.env**，取决于你的语言偏好）：
    - 你的文档智能终结点。
    - 文档智能密钥。
    - 你在训练模型时提供的模型 ID。 你可以在 Document Intelligence Studio 的**模型**页面上找到它。 **保存**所做更改。

1. 在 Visual Studio Code 中，打开客户端应用程序的代码文件（C# 为 *Program.cs*，Python 为 *test-model.py*），查看其中包含的代码，尤其是 URL 中的图片是否指向网上 GitHub 仓库中的文件。

1. 返回终端，输入以下命令运行程序：

    **C#**

    ```powershell
    dotnet build
    dotnet run
    ```

    **Python**

    ```powershell
    python test-model.py
    ```

1. 查看输出结果，观察模型的输出结果是如何提供字段名称的，如 `Merchant` 和 `CompanyPhoneNumber`。

## 清理

如果您已完成 Azure 资源的使用，请记住在 [Azure 门户](https://portal.azure.com/?azure-portal=true)中删除该资源，以避免进一步收费。

## 详细信息

有关文档智能服务的更多信息，请参阅[文档智能文档](https://learn.microsoft.com/azure/ai-services/document-intelligence/?azure-portal=true)。
