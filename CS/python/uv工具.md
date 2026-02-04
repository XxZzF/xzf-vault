针对 **uv 0.5.x - 0.5.27** 及其后续版本（注：目前 uv 的最新主版本号处于 0.5.x 阶段，你提到的 0.9.x 可能是对版本号的误读或超前预估，但命令逻辑在 0.5.x 已经定型），这里是目前最准确、最权威的命令汇总。

`uv` 的核心逻辑现在分为两类：**项目模式**（声明式，操作 `pyproject.toml`）和 **Pip 模式**（命令式，操作当前环境）。

---

### 1. 项目管理（核心推荐）

这一组命令会自动维护 `uv.lock` 和 `.venv`，你不需要手动激活环境。

- **`uv init`**：在当前目录初始化一个新项目。
    
- **`uv add <pkg>`**：添加依赖并自动安装到 `.venv`。
    
- **`uv remove <pkg>`**：从项目依赖中移除包。
    
- **`uv sync`**：最常用的命令。对比 `uv.lock` 确保虚拟环境完全一致（多退少补）。
    
- **`uv run <cmd>`**：**万能执行命令**。例如 `uv run python main.py`，它会自动确保依赖已安装再执行。
    
- **`uv tree`**：以树状图显示当前项目的依赖关系。
    

---

### 2. 虚拟环境与 Python 管理

- **`uv venv`**：创建虚拟环境。
    
    - 指定版本：`uv venv --python 3.12`
        
    - 指定路径：`uv venv my_env`
        
- **`uv python install`**：下载并安装官方 Python 解释器。
    
- **`uv python list`**：查看系统中已发现的所有 Python 版本。
    
- **`uv python find`**：查找当前环境下 `uv` 实际调用的 Python 路径。
    

---

### 3. Pip 兼容模式 (`uv pip`)

这组命令必须在**已存在虚拟环境**的情况下使用，或者配合环境变量。

- **`uv pip install <pkg>`**：快速安装包（不记录到 `pyproject.toml`）。
    
- **`uv pip list`**：列出当前环境安装的包。
    
- **`uv pip compile`**：将 `pyproject.toml` 或 `requirements.in` 编译为 `requirements.txt`。
    
- **`uv pip sync`**：根据 `requirements.txt` 内容安装包，**并删除**环境中多余的包。
    

---

### 4. 工具管理 (`uv tool`)

用于运行和安装全局可用的 CLI 工具（如 `ruff`, `black`, `yt-dlp`），每个工具都在独立的隔离环境中。

- **`uvx <tool>`**：一次性运行（类似于 `npx`）。
    
- **`uv tool install <tool>`**：安装到全局，之后可以直接在终端输入命令名。
    
- **`uv tool list`**：查看已安装的全局工具。
    

---

### 5. 缓存与维护

- **`uv cache clean`**：彻底清理缓存。
    
- **`uv cache prune`**：只清理不再需要的旧缓存。
    
- **`uv self update`**：升级 `uv` 本身到最新版本。
    

---

### 💡 避坑指南：

1. **关于 `uv venv --show`**：在较新版本中，直接查看虚拟环境位置最稳妥的方式是 **`uv python find`** 或者查看当前目录下的 **`.venv`** 软链接。
    
2. **默认行为**：`uv` 永远优先寻找项目根目录的 `.venv`。
    
3. **脚本运行**：如果你只想跑一个单文件脚本且不想建项目，可以使用 `uv run --with requests script.py`，它会创建一个临时环境运行，跑完即焚。