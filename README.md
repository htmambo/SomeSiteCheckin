# SomeSiteCheckin

一个基于 Python 和 Playwright 的自动签到工具，通过模拟浏览器自动登录网站并浏览帖子，实现全自动签到功能。

## ✨ 功能特点

- 🤖 **全自动化签到** - 使用 Playwright 模拟真实浏览器操作，无需手动干预
- 🔐 **安全登录** - 支持多种登录方式，保护账号安全
- 📝 **智能浏览** - 自动浏览帖子并完成签到任务
- ⏰ **定时执行** - 支持配置定时任务，按需自动执行
- 📊 **日志记录** - 详细记录签到过程和结果，便于追踪和调试
- 🎯 **多站点支持** - 灵活配置，支持多个站点的签到需求
- 🛡️ **异常处理** - 完善的错误处理机制，确保稳定运行

## 📋 系统要求

- Python 3.8 或更高版本
- 支持的操作系统：Windows、macOS、Linux

## 🚀 快速开始

### 安装

1. **克隆仓库**
```bash
git clone https://github.com/htmambo/SomeSiteCheckin.git
cd SomeSiteCheckin
```

2. **创建虚拟环境**（推荐）
```bash
python -m venv venv
# Windows
venv\Scripts\activate
# macOS/Linux
source venv/bin/activate
```

3. **安装依赖**
```bash
pip install -r requirements.txt
```

4. **安装 Playwright 浏览器**
```bash
playwright install chromium
```

### 配置

1. **复制配置文件模板**
```bash
cp config.example.json config.json
```

2. **编辑配置文件**

在 `config.json` 中填写您的配置信息：

```json
{
  "sites": [
    {
      "name": "网站名称",
      "url": "https://example.com",
      "username": "你的用户名",
      "password": "你的密码",
      "login_selector": "#login-button",
      "post_selector": ".post-link",
      "checkin_selector": "#checkin-button"
    }
  ],
  "schedule": {
    "enabled": true,
    "time": "09:00"
  },
  "browser": {
    "headless": true,
    "timeout": 30000
  },
  "logging": {
    "level": "INFO",
    "file": "checkin.log"
  }
}
```

**配置说明：**

- `sites`: 需要签到的网站列表
  - `name`: 网站名称（用于日志识别）
  - `url`: 网站登录页面地址
  - `username`: 登录用户名
  - `password`: 登录密码
  - `login_selector`: 登录按钮的 CSS 选择器
  - `post_selector`: 帖子链接的 CSS 选择器
  - `checkin_selector`: 签到按钮的 CSS 选择器
- `schedule`: 定时任务配置
  - `enabled`: 是否启用定时任务
  - `time`: 每天签到时间（24 小时制）
- `browser`: 浏览器配置
  - `headless`: 是否使用无头模式
  - `timeout`: 页面加载超时时间（毫秒）
- `logging`: 日志配置
  - `level`: 日志级别（DEBUG、INFO、WARNING、ERROR）
  - `file`: 日志文件路径

### 使用方法

#### 单次签到

```bash
python checkin.py
```

#### 定时签到

启用配置文件中的定时任务，或使用系统定时任务：

**使用 cron（Linux/macOS）**
```bash
# 编辑 crontab
crontab -e

# 添加每天 9:00 执行签到
0 9 * * * cd /path/to/SomeSiteCheckin && /path/to/python checkin.py
```

**使用 Windows 任务计划程序**
1. 打开任务计划程序
2. 创建基本任务
3. 设置触发器为每天指定时间
4. 操作选择"启动程序"，填写 Python 路径和脚本路径

#### 测试模式

使用测试模式查看浏览器操作过程（非无头模式）：

```bash
python checkin.py --test
```

## 🏗️ 技术架构

### 核心组件

```
SomeSiteCheckin/
├── checkin.py          # 主程序入口
├── core/
│   ├── browser.py      # 浏览器控制模块
│   ├── login.py        # 登录处理模块
│   ├── navigator.py    # 页面导航模块
│   └── scheduler.py    # 定时任务模块
├── utils/
│   ├── logger.py       # 日志工具
│   ├── config.py       # 配置管理
│   └── exceptions.py   # 自定义异常
├── config.json         # 配置文件（需自行创建）
├── config.example.json # 配置文件模板
├── requirements.txt    # Python 依赖
└── README.md          # 项目文档
```

### 工作流程

```
开始
  ↓
加载配置
  ↓
初始化 Playwright
  ↓
启动浏览器
  ↓
访问登录页面
  ↓
填写登录信息
  ↓
点击登录按钮
  ↓
等待登录成功
  ↓
导航到目标页面
  ↓
浏览帖子列表
  ↓
点击签到按钮
  ↓
验证签到结果
  ↓
记录日志
  ↓
关闭浏览器
  ↓
结束
```

### Playwright 核心功能

Playwright 是一个强大的浏览器自动化库，本项目主要使用以下功能：

- **浏览器启动**：支持 Chromium、Firefox、WebKit
- **页面操作**：导航、点击、输入、截图等
- **元素定位**：CSS 选择器、XPath、文本内容等
- **等待机制**：等待元素可见、网络空闲等
- **上下文管理**：管理 Cookie、Storage、Session 等

## 📚 使用示例

### 基础示例

```python
from playwright.sync_api import sync_playwright

def simple_checkin():
    with sync_playwright() as p:
        # 启动浏览器
        browser = p.chromium.launch(headless=False)
        page = browser.new_page()
        
        # 访问网站
        page.goto("https://example.com/login")
        
        # 填写登录信息
        page.fill("#username", "your_username")
        page.fill("#password", "your_password")
        
        # 点击登录
        page.click("#login-button")
        
        # 等待登录成功
        page.wait_for_selector(".user-profile")
        
        # 浏览帖子
        page.goto("https://example.com/posts")
        page.click(".post-link")
        
        # 签到
        page.click("#checkin-button")
        
        # 验证签到成功
        success = page.locator(".checkin-success").is_visible()
        
        print(f"签到{'成功' if success else '失败'}")
        
        # 关闭浏览器
        browser.close()

if __name__ == "__main__":
    simple_checkin()
```

### 高级示例 - 带错误处理

```python
from playwright.sync_api import sync_playwright, TimeoutError as PlaywrightTimeout
import logging

def advanced_checkin():
    logging.basicConfig(level=logging.INFO)
    logger = logging.getLogger(__name__)
    
    with sync_playwright() as p:
        try:
            # 启动浏览器
            browser = p.chromium.launch(
                headless=True,
                args=['--no-sandbox', '--disable-setuid-sandbox']
            )
            context = browser.new_context(
                viewport={'width': 1920, 'height': 1080},
                user_agent='Mozilla/5.0 (Windows NT 10.0; Win64; x64)'
            )
            page = context.new_page()
            
            # 设置超时时间
            page.set_default_timeout(30000)
            
            logger.info("开始访问登录页面")
            page.goto("https://example.com/login", wait_until="networkidle")
            
            # 检查是否已登录
            if page.locator(".user-profile").is_visible():
                logger.info("已登录，跳过登录步骤")
            else:
                logger.info("开始登录")
                page.fill("#username", "your_username")
                page.fill("#password", "your_password")
                page.click("#login-button")
                
                # 等待登录完成
                page.wait_for_selector(".user-profile", state="visible")
                logger.info("登录成功")
            
            # 浏览帖子
            logger.info("开始浏览帖子")
            page.goto("https://example.com/posts")
            
            # 获取帖子列表
            posts = page.locator(".post-link").all()
            logger.info(f"找到 {len(posts)} 个帖子")
            
            # 浏览第一个帖子
            if posts:
                posts[0].click()
                page.wait_for_load_state("networkidle")
            
            # 执行签到
            logger.info("开始签到")
            page.goto("https://example.com/checkin")
            
            checkin_button = page.locator("#checkin-button")
            if checkin_button.is_visible():
                checkin_button.click()
                page.wait_for_selector(".checkin-success", state="visible")
                logger.info("签到成功！")
            else:
                logger.warning("今日已签到或签到按钮不可见")
            
            # 截图保存
            page.screenshot(path="checkin_result.png")
            logger.info("已保存截图")
            
        except PlaywrightTimeout as e:
            logger.error(f"操作超时: {e}")
        except Exception as e:
            logger.error(f"发生错误: {e}")
        finally:
            browser.close()
            logger.info("浏览器已关闭")

if __name__ == "__main__":
    advanced_checkin()
```

## 🔧 故障排除

### 常见问题

**1. Playwright 浏览器未安装**

```
Error: Executable doesn't exist at ...
```

**解决方案**：
```bash
playwright install chromium
```

**2. 登录失败**

- 检查用户名和密码是否正确
- 检查网站是否有验证码
- 尝试使用非无头模式观察登录过程
- 检查 CSS 选择器是否正确

**3. 元素定位失败**

```
Error: Timeout 30000ms exceeded
```

**解决方案**：
- 使用浏览器开发者工具检查元素选择器
- 增加页面加载等待时间
- 使用更可靠的定位方式（如 `data-testid`）
- 等待页面完全加载（`wait_until="networkidle"`）

**4. 签到已完成**

如果网站显示"今日已签到"，这是正常情况，无需重复签到。

**5. 网络问题**

- 检查网络连接
- 尝试增加超时时间
- 使用代理（如需要）

### 调试技巧

1. **启用非无头模式**：在配置中设置 `headless: false` 查看浏览器操作
2. **查看日志**：检查 `checkin.log` 文件获取详细信息
3. **截图调试**：在关键步骤添加 `page.screenshot()` 保存截图
4. **使用 Playwright Inspector**：
```bash
PWDEBUG=1 python checkin.py
```

## 🔒 安全建议

1. **不要提交敏感信息**：
   - 将 `config.json` 添加到 `.gitignore`
   - 使用环境变量存储密码
   - 考虑使用加密存储凭证

2. **定期更新依赖**：
```bash
pip install --upgrade -r requirements.txt
```

3. **使用强密码**：确保账号使用强密码和双因素认证（如支持）

4. **限制访问权限**：确保配置文件只有必要的读取权限

## 🤝 贡献指南

欢迎贡献代码、报告问题或提出建议！

### 如何贡献

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

### 代码规范

- 遵循 PEP 8 Python 代码规范
- 添加适当的注释和文档字符串
- 编写单元测试
- 确保所有测试通过

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 📞 联系方式

- 项目主页：[https://github.com/htmambo/SomeSiteCheckin](https://github.com/htmambo/SomeSiteCheckin)
- 问题反馈：[Issues](https://github.com/htmambo/SomeSiteCheckin/issues)

## 🙏 致谢

- [Playwright](https://playwright.dev/) - 强大的浏览器自动化库
- 所有贡献者和使用者

## ⚠️ 免责声明

本工具仅供学习和研究使用。使用本工具时请遵守目标网站的服务条款和使用政策。对于因使用本工具而产生的任何问题，作者不承担任何责任。

---

**注意**：使用自动化工具前，请确保您的行为符合目标网站的使用条款，避免对网站服务器造成不必要的负担。建议合理设置签到频率，做一个负责任的用户。