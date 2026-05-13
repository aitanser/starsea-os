# 星海量子通用操作系统 - 项目文档

**版本**：1.0.0  
**开发者**：蓝域星河有限公司集团  

---

## 一、项目概述

星海量子通用操作系统配置终端是一个多用户、多语言、高安全性的量子操作系统管理工具。它提供了系统初始化、用户管理、终端命令交互、虚拟文件系统、定时任务、升级维护、安全审计等完整功能，并支持通过 GitHub 远程更新。

系统采用 **Fernet 加密** 保护所有配置文件，用户密码使用 SHA-256 加盐存储，系统密钥采用 bcrypt 验证并支持暴力锁定。内置 **admin / user / developer** 三级权限体系，开发者拥有最高运维权限（需独立激活密钥）。

> **注意事项**  
>
> - 本系统为专有软件，未经授权不得用于商业用途。  
> - 默认产品密钥仅用于开发测试，正式部署前必须更换高强度产品密钥及开发者激活密钥。  
> - 所有敏感操作均会被记录在审计日志中，请勿泄露管理员或开发者凭据。

---

## 二、系统架构

```
项目根目录/
├── 组件/                     # 系统运行目录
│   ├── main.py               # 主入口
│   ├── requirements.txt      # Python依赖
│   ├── README.md             # 说明文件
│   ├── 关于/                 # 产品文档
│   │   ├── 版本号.txt
│   │   ├── 关于.txt
│   │   ├── 系统教程.txt
│   │   ├── 系统框架.txt
│   │   └── 制作名单.txt
│   ├── 支付码/               # 赞赏码（空白占位）
│   ├── 示例文档/             # 按格式分类的空白模板文件（16类）
│   ├── 数据集/               # 参考数据（字符集、表情集、示例表格、测试文本）
│   ├── 应用/                 # 软件安装目录（空）
│   └── system/               # 核心系统模块
│       ├── config.py         # 配置与多语言
│       ├── utils.py          # 加密、文件、用户管理
│       ├── init_handler.py   # 初始化流程
│       ├── login_handler.py  # 登录验证
│       ├── terminal.py       # 终端交互核心
│       ├── ... (共40+模块)
└── G_D_蓝域星河的数据储存库/ # 用户数据仓库（名称可自定义，安装包自带）
```

> **注意事项**  
>
> - `G_D_蓝域星河的数据储存库` 的名称应根据终端类型和序号进行定制（如 `K_A_蓝域星河的数据储存库`）。系统首次启动时会根据配置自动检测或引导创建，请勿随意重命名。  
> - `system/` 目录下的核心模块严禁手动修改，升级时请使用系统提供的升级工具。

---

## 三、核心功能

### 1. 系统初始化

- 验证系统产品密钥（格式 XXXXX-XXXXX-XXXXX-XXXXX-XXXXX）
- 设置管理员账户、终端类型（FWQ/KZ/GL/DB）及序号
- 自动创建加密配置文件 `.lyxh.enc`
- 设置安全问题（用于密码重置）

### 2. 用户与权限管理

- 三级角色：user（普通）、admin（管理员）、developer（开发者）
- 管理员可增删用户、修改密码、变更角色
- 开发者拥有所有管理员权限 + 修改系统根密钥、查看原始配置等
- 角色变更至 developer 需独立激活密钥

### 3. 量子终端

- 超过 80 个内置命令（系统管理、文件操作、网络、监控、虚拟文件系统等）
- Tab 命令补全、历史记录、别名、收藏夹
- 多会话管理（独立虚拟文件系统和真实目录）
- 颜色主题切换（7 种 ANSI 主题）
- 性能分析（`time` 命令）、沙盒执行
- 实时时钟显示、消息广播

### 4. 安全机制

- 配置文件全程加密（AES-128 CBC + HMAC）
- 系统密钥 bcrypt 验证，错误 5 次锁定 15 分钟
- 用户密码 SHA-256 哈希存储，支持密码策略强制
- 双因素认证（TOTP）可选
- 硬件绑定许可证（可选）
- 安全审计日志记录所有敏感操作

### 5. 升级与维护

- 本地版本迁移（`upgrade` 命令）
- GitHub Release 自动检测、下载、应用升级包
- 系统还原点管理（创建 / 恢复快照）
- 文件完整性检查、临时文件清理、虚拟文件系统修复
- 自动备份（每 10 分钟）与定时报告（每小时）
- 自愈守护进程（监控调度器、日志自动压缩）

### 6. 扩展性

- 插件系统：`plugins/` 目录下的 Python 模块可通过 `plugin` 命令动态加载和执行
- 脚本引擎：支持执行 `.ssea` 批处理脚本
- Cron 任务：支持标准 cron 表达式添加定时任务

### 7. 多语言支持

内置 8 种语言：中文、English、日本語、Français、Deutsch、Español、Русский、العربية  
所有界面提示均根据所选语言显示，配置文件按语言独立存储。

> **注意事项**  
>
> - **密钥安全**：系统产品密钥和开发者激活密钥是最高机密，请通过安全渠道分发，严禁明文存储在可公开访问的位置。  
> - **密码策略**：建议在生产环境中启用密码复杂度策略（通过 `password_policy.json` 配置），并定期强制用户更换密码。  
> - **双因素认证**：对于管理员和开发者账户，强烈建议启用 TOTP 双因素认证，避免因密码泄露导致越权操作。  
> - **硬件绑定**：启用硬件绑定许可证后，配置文件将无法在其他机器上解密，迁移前务必先解除绑定或联系开发者。  
> - **审计日志**：审计日志文件 `audit.log` 应定期备份并妥善保管，它是追溯安全事件的关键依据。  
> - **定时备份**：系统默认每 10 分钟自动备份加密配置，备份文件为 `.lyxh.enc.bak`，请勿手动删除近期的备份文件。  
> - **升级风险**：无论是本地升级还是 GitHub 远程升级，升级前系统都会自动创建备份。如果升级失败，可使用维护工具中的“恢复备份”功能回滚。  
> - **多语言环境**：不同语言的配置文件独立存储，切换语言登录不会影响其他语言的配置。初始化时每个语言系统都需单独设置管理员。

---

## 四、安装与运行

### 环境要求

- Python 3.8+
- 依赖库：`bcrypt`, `cryptography`, `requests`, `psutil`, `pyotp`, `paramiko`, `croniter` 等

### 安装步骤

1. 将项目文件夹“组件”放置在任意目录。
2. 进入“组件”目录，执行：

   ```bash
   pip install -r requirements.txt
   ```

3. 运行：

   ```bash
   python main.py
   ```

4. 首次运行需进行系统初始化（按提示输入系统产品密钥、创建管理员账户等）。

> **注意事项**  
>
> - 某些功能（如邮件通知、SCP 文件传输、TOTP 二维码显示）依赖第三方库，请确保 `requirements.txt` 中的包全部安装成功。  
> - 如果 `psutil` 安装失败，系统监控功能将回退到模拟数据，不影响核心运行。  
> - 系统运行期间请勿手动关闭终端窗口，推荐使用 `exit` 命令正常退出，以保证定时任务和守护进程安全停止。

---

## 五、使用说明

启动后显示主菜单：

```
===== 星海量子通用操作系统 =====
1 - 系统初始化
2 - 登录系统
3 - 日常检测
4 - 系统升级（本地/离线）
5 - GitHub 自动更新
6 - 生成系统报告
7 - 系统维护
0 - 退出
```

登录后进入量子终端，输入 `help` 查看可用命令，输入 `help <关键词>` 获取分类帮助。  
示例命令：

- `status`  查看系统状态
- `backup`  手动备份配置
- `vls`     虚拟文件列表
- `monitor` 系统资源监控
- `session new work` 创建新会话
- `theme blue` 切换蓝色主题

详细教程可查看 `关于/系统教程.txt`。

> **注意事项**  
>
> - **命令补全**：在终端中可以使用 Tab 键自动补全命令，如果补全不生效，请确认终端支持 `readline`。  
> - **脚本执行**：`.ssea` 脚本默认拥有当前用户权限，请勿运行不可信来源的脚本。建议在沙盒模式（`sandbox <命令>`）中测试未知命令。  
> - **多会话**：每个会话拥有独立的虚拟文件系统和真实工作目录，退出会话不会影响其他会话，但主会话不可关闭。  
> - **数据储存库**：用户另存为的文件建议保存在 `G_D_蓝域星河的数据储存库` 中，具体路径和命名规则请参考系统教程。

---

## 六、配置说明

### 系统产品密钥

默认密钥（开发示例）：`STAR1-SEA2O-S3QU4-NTUM5-OS123`  
其 bcrypt 哈希硬编码在 `system/config.py` 的 `VERSION_KEY_HASHES` 中。  
密钥哈希可通过开发者命令 `devkeychange` 更新（存储在外部文件 `version_keys.json`）。

### 开发者激活密钥

开发者角色的提升需要独立的激活密钥，其哈希定义在 `config.py` 的 `DEVELOPER_KEY_HASH`。  
正式发布时应使用高强度密钥并妥善保管。

### SMTP 邮件配置

若要使用邮件通知功能，需在 `mail_config.json` 中配置 SMTP 服务器信息。

> **注意事项**  
>
> - 修改密钥哈希或安全配置后，建议立即重启系统并验证新配置是否生效。  
> - `mail_config.json` 中包含邮箱密码，请务必设置文件权限为 600（仅 Linux/macOS），避免凭据泄露。  
> - 更改系统产品密钥后，所有已加密的配置文件依然可用（只要加密密钥一致），但新初始化的系统将使用新密钥。

---

## 七、开发与贡献

本项目由 **蓝域星河有限公司集团** 开发，欢迎社区通过 GitHub 提交 Issue 或 Pull Request。  
插件开发请参阅本文档的 **[插件开发指南](#八插件开发指南)**。

> **注意事项**  
>
> - 提交代码前请确保所有现有功能正常，尤其是加密解密和权限验证逻辑。  
> - 新增模块应放置在 `system/` 目录下，并遵循现有的命名和导入规范。

---

## 八、插件开发指南

### 8.1 插件系统概述

星海量子操作系统内置插件管理器，允许用户通过编写 Python 脚本动态扩展终端功能。  
插件存放在 `组件/plugins/` 目录下，每个插件是一个独立的 `.py` 文件，系统启动后可通过 `plugin list` 查看，通过 `plugin run <插件名> [参数]` 执行。

### 8.2 插件开发要求

- **文件命名**：插件文件需以 `.py` 结尾，文件名即为插件名称（不包含扩展名）。  
  例如：`hello.py` 对应插件名 `hello`。
- **必须实现的接口**：每个插件必须定义一个 `run` 函数，其函数签名如下：

  ```python
  def run(args, lang, lang_code, current_user):
      """
      args: list[str] —— 命令行参数列表（不包含插件名本身）
      lang: dict —— 当前语言配置字典 (从 system.config.LANGUAGES 获取)
      lang_code: int —— 当前语言代码 (1~8)
      current_user: dict —— 当前登录用户信息字典，包含 username, role 等字段
      """
  ```

- **可用资源**：插件运行环境拥有对 `system` 包的完全访问权限，可调用系统提供的任何公共 API。  
  常用导入示例：

  ```python
  from system.utils import log_action, load_config, save_config
  from system.config import LANGUAGES
  ```

  **注意**：请勿在插件中执行破坏性操作，除非您清楚自己的权限。普通用户无法使用需要高权限的功能，但插件本身无权限限制，编写时应自行判断。
- **返回值**：`run` 函数不需要返回特定值，打印输出会显示在终端中。

### 8.3 示例插件

#### 示例1：Hello World

文件：`components/plugins/hello.py`

```python
# 一个简单的问候插件
def run(args, lang, lang_code, current_user):
    name = args[0] if args else current_user['username']
    print(f"你好，{name}！欢迎使用星海量子插件系统。")
    print(f"当前语言：{lang['name']}")
```

使用方法：在终端输入 `plugin run hello` 或 `plugin run hello 鸿渚`。

#### 示例2：磁盘空间检查

文件：`components/plugins/disk_check.py`

```python
# 检查当前目录磁盘使用情况
import os
from system.utils import log_action

def run(args, lang, lang_code, current_user):
    path = args[0] if args else '.'
    total, used, free = 0, 0, 0
    if hasattr(os, 'statvfs'):
        st = os.statvfs(path)
        total = st.f_blocks * st.f_frsize
        free = st.f_bavail * st.f_frsize
        used = total - free
        print(f"路径: {path}")
        print(f"总计: {total//1024//1024} MB")
        print(f"已用: {used//1024//1024} MB")
        print(f"可用: {free//1024//1024} MB")
    else:
        print("当前操作系统不支持 statvfs。")
    log_action(f"插件 disk_check 由 {current_user['username']} 执行")
```

#### 示例3：访问系统配置（需谨慎）

文件：`components/plugins/show_config.py`

```python
# 显示当前语言的加密配置（需要管理员角色）
from system.utils import load_config, is_privileged
from system.config import LANGUAGES

def run(args, lang, lang_code, current_user):
    if not is_privileged(current_user['role']):
        print("权限不足，需要管理员或开发者角色。")
        return
    config = load_config(lang_code)
    if config:
        # 屏蔽密码哈希，只显示基本信息
        safe_config = config.copy()
        if 'users' in safe_config:
            for u in safe_config['users']:
                u['password_hash'] = '***'
        import json
        print(json.dumps(safe_config, indent=2, ensure_ascii=False))
    else:
        print("配置未加载。")
```

### 8.4 插件安装与使用

1. 将编写好的 `.py` 文件放入 `组件/plugins/` 目录。
2. 重新登录量子终端，或无需重启，直接在终端输入：
   - `plugin list` —— 查看所有可用插件
   - `plugin load <插件名>` —— 加载插件（检查语法）
   - `plugin run <插件名> [参数...]` —— 执行插件
3. 插件执行过程中发生的错误会输出到终端，同时记录在 `init.log` 中（若插件内调用了 `log_action`）。

### 8.5 注意事项

- 插件运行在主线程中，若包含无限循环或长时间阻塞，将导致终端无响应。如有需要，请使用 `threading` 创建后台线程。
- 插件可访问所有系统模块，因此请勿安装来源不明的插件。
- 系统升级可能会覆盖 `plugins/` 目录，建议提前备份自己的插件。
- **权限风险**：插件代码可以调用 `save_config` 等关键函数，务必审核代码逻辑，防止意外或恶意篡改配置。

### 8.6 未来扩展

未来系统可能支持：

- 插件依赖管理
- 插件自动加载与卸载
- 插件权限声明

欢迎开发者贡献更多强大插件，丰富星海量子生态！

---

## 九、许可证

本项目采用专有许可证，仅供授权使用。  
版权所有 © 蓝域星河有限公司集团。