# 小红书自动搜索评论工具（MCP Server）

> 本项目基于 [JonaFly/RednoteMCP](https://github.com/JonaFly/RednoteMCP.git) 并结合我自己的使用经验，进行优化和改进（by windsurf）。在此向原作者表示衷心的感谢！

这是一款基于 Playwright 开发的小红书自动搜索和评论工具，作为 MCP Server，可通过特定配置接入 MCP Client（如Claude for Desktop），帮助用户自动完成登录小红书、搜索关键词、获取笔记内容及发布智能评论等操作。

### 注：[Redbook-Search-Comment-MCP2.0](https://github.com/chenningling/Redbook-Search-Comment-MCP2.0)已经发布！直接点击前往使用即可！
**2.0主要优化内容如下：**
- 优化了搜索笔记时，标题不显示的问题
- 新增了多类获取笔记的方法，确保能完整获取笔记内容
- 重构了评论功能，利用MCP客户端（如Claude）的AI能力生成更自然的评论
- 将功能模块化，分为笔记分析、评论生成和评论发布三个独立模块

## 一、功能特点

- **自动登录**：支持手动扫码登录方式，首次登录成功后会保存登录状态，后续使用无需重复扫码。
- **关键词搜索**：能依据用户输入的关键词搜索小红书笔记，并可指定返回结果的数量。
- **笔记内容获取**：输入笔记的 URL，即可获取该笔记的详细内容。
- **笔记评论获取**：通过笔记 URL 获取相应笔记的评论信息。
- **智能评论发布**：支持多种评论类型，包括引流（引导用户关注或私聊）、点赞（简单互动获取好感）、咨询（以问题形式增加互动）、专业（展示专业知识建立权威），可根据需求选择发布。

## 二、安装步骤

1. **Python 环境准备**：确保系统已安装 Python 3.8 或更高版本。若未安装，可从 Python 官方网站下载并安装。

2. **项目获取**：将本项目克隆或下载到本地。

3. **创建虚拟环境**：在项目目录下创建并激活虚拟环境（推荐）：
   ```bash
   # 创建虚拟环境
   python3 -m venv venv
   
   # 激活虚拟环境
   # Windows
   venv\Scripts\activate
   # macOS/Linux
   source venv/bin/activate
   ```

4. **安装依赖**：在激活的虚拟环境中安装所需依赖：
   ```bash
   pip install -r requirements.txt
   pip install fastmcp
   ```

5. **安装浏览器**：安装Playwright所需的浏览器：
   ```bash
   playwright install
   ```

## 三、MCP Server 配置

在 MCP Client（如Claude for Desktop）的配置文件中添加以下内容，将本工具配置为 MCP Server：

```json
{
    "mcpServers": {
        "xiaohongshu MCP": {
            "command": "/绝对路径/到/venv/bin/python3",
            "args": [
                "/绝对路径/到/xiaohongshu_mcp.py",
                "--stdio"
            ]
        }
    }
}
```

> **重要提示**：
> - 请使用虚拟环境中Python解释器的**完整绝对路径**
> - 例如：`/Users/username/Desktop/RedBook-Search-Comment-MCP/venv/bin/python3`
> - 同样，xiaohongshu_mcp.py也需要使用**完整绝对路径**

## 四、使用方法

### （一）启动服务器

1. **直接运行**：在项目目录下，激活虚拟环境后执行：
   ```bash
   python3 xiaohongshu_mcp.py
   ```

2. **通过 MCP Client 启动**：配置好MCP Client后，按照客户端的操作流程进行启动和连接。

### （二）主要功能操作

在MCP Client（如Claude for Desktop）中连接到服务器后，可以使用以下功能：

### 1. 登录小红书

**工具函数**：
```
mcp0_login()
```

**在MCP客户端中的使用方式**：
直接发送以下文本：
```
帮我登录小红书账号
```
或：
```
请登录小红书
```

**功能说明**：首次使用时会打开浏览器窗口，等待用户手动扫码登录。登录成功后，工具会保存登录状态。

### 2. 搜索笔记

**工具函数**：
```
mcp0_search_notes(keywords="关键词", limit=5)
```

**在MCP客户端中的使用方式**：
发送包含关键词的搜索请求：
```
帮我搜索小红书笔记，关键词为：美食
```
指定返回数量：
```
帮我搜索小红书笔记，关键词为旅游，返回10条结果
```

**功能说明**：根据关键词搜索小红书笔记，并返回指定数量的结果。默认返回5条结果。

### 3. 获取笔记内容

**工具函数**：
```
mcp0_get_note_content(url="笔记URL")
```

**在MCP客户端中的使用方式**：
发送包含笔记URL的请求：
```
帮我获取这个笔记的内容：https://www.xiaohongshu.com/search_result/xxxx
```
或：
```
请查看这个小红书笔记的内容：https://www.xiaohongshu.com/search_result/xxxx
```

**功能说明**：获取指定笔记URL的详细内容，包括标题、作者、发布时间和正文内容。

### 4. 获取笔记评论

**工具函数**：
```
mcp0_get_note_comments(url="笔记URL")
```

**在MCP客户端中的使用方式**：
发送包含笔记URL的评论请求：
```
帮我获取这个笔记的评论：https://www.xiaohongshu.com/search_result/xxxx
```
或：
```
请查看这个小红书笔记的评论区：https://www.xiaohongshu.com/search_result/xxxx
```

**功能说明**：获取指定笔记URL的评论信息，包括评论者、评论内容和评论时间。

### 5. 发布智能评论

**工具函数**：
```
mcp0_post_smart_comment(url="笔记URL", comment_type="评论类型")
```

**在MCP客户端中的使用方式**：
发送包含笔记URL和评论类型的请求：
```
帮我在这个笔记发布专业类型的评论：https://www.xiaohongshu.com/search_result/xxxx
```
或：
```
请在这个小红书笔记下发布一条引流评论：https://www.xiaohongshu.com/search_result/xxxx
```

**评论类型参数可选值**：
- `"引流"` (默认)：引导用户关注或私聊
- `"点赞"`：简单互动获取好感
- `"咨询"`：以问题形式增加互动
- `"专业"`：展示专业知识建立权威

**功能说明**：在指定笔记下发布智能评论，系统会根据笔记内容和指定的评论类型自动生成适合的评论内容。

## 五、代码结构

- **xiaohongshu_mcp.py**：实现主要功能的核心文件，包含登录、搜索、获取内容和评论、发布评论等功能的代码逻辑。
- **requirements.txt**：记录项目所需的依赖库。

## 六、常见问题与解决方案

1. **连接失败**：
   - 确保使用了虚拟环境中Python解释器的**完整绝对路径**
   - 确保MCP服务器正在运行
   - 尝试重启MCP服务器和客户端

2. **浏览器会话问题**：
   如果遇到`Page.goto: Target page, context or browser has been closed`错误：
   - 重启MCP服务器
   - 重新连接并登录

3. **依赖安装问题**：
   如果遇到`ModuleNotFoundError`错误：
   - 确保在虚拟环境中安装了所有依赖
   - 检查是否安装了fastmcp包

## 七、注意事项

- **浏览器模式**：工具使用 Playwright 的非隐藏模式运行，运行时会打开真实浏览器窗口。
- **登录方式**：首次登录需要手动扫码，后续使用若登录状态有效，则无需再次扫码。
- **平台规则**：使用过程中请严格遵守小红书平台的相关规定，避免进行过度操作，防止账号面临封禁等风险。
- **功能兼容性**：由于小红书平台可能会进行更新和调整，搜索结果和评论功能的可用性可能会受到影响。若出现异常，请及时关注项目更新或联系开发者。

## 八、免责声明

本工具仅用于学习和研究目的，使用者应严格遵守相关法律法规以及小红书平台的规定。因使用不当导致的任何问题，本项目开发者不承担任何责任。
