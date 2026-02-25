# PyO3 + maturin 构建基于Rust的Python 扩展模块

## 一、使用 PyO3 编写 Rust Python 扩展

使用 PyO3 来编写 Python 扩展模块。

### 示例：`src/lib.rs`

```markdown
use pyo3::prelude::*;

#[pyfunction]
fn hello_from_bin() -> String {
    "Hello from rust!".to_string()
}

#[pymodule]
fn _core(m: &Bound<'_, PyModule>) -> PyResult<()> {
    m.add_function(wrap_pyfunction!(hello_from_bin, m)?)?;
    Ok(())
}
```

⚠ 注意：

*   `#[pymodule] fn _core`的函数名必须和 `Cargo.toml` 中 `[lib] name` 一致
*   否则 Python 无法 import

***

## 二、完整项目结构

```markdown
project_name/
├── Cargo.toml
├── pyproject.toml
└── src/
	├── project_name/
		└── __init__.py
		└── _core.pyi
    └── lib.rs
```

***

## 三、Cargo.toml 配置

```markdown
[package]
name = "project_name"
version = "0.1.0"
edition = "2024"

[lib]
name = "_core"
crate-type = ["cdylib"]

[dependencies]
pyo3 = { version = "0.27.1", features = ["extension-module", "abi3-py313"] }
```

***

## 四、pyproject.toml 配置

我们使用 maturin 来构建和发布。

```markdown
[project]
name = "project_name"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.13"
dependencies = [
    "maturin>=1.12.4",
]

[build-system]
requires = ["maturin>=1.0,<2.0"]
build-backend = "maturin"

[tool.maturin]
module-name = "project_name._core"
python-packages = ["project_name"]
python-source = "src"
```

***

## 五、安装 maturin

```shell
uv add maturin
```

***

## 六、构建并安装

在项目根目录执行：

```shell
uv run maturin develop
```

成功后即可在 Python 中使用：

```python
import project_name

print(project_name.hello_from_bin())
```

输出：

```shell
Hello from rust!
```

