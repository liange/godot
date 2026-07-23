# AGENTS.md

- 默认使用中文与用户沟通，除非用户明确要求使用其他语言。
- 文档和注释使用中文编写，存储到 `文档/`。

## 构建系统
- 仅使用 **SCons**，不支持 CMake/Make。命令格式：`scons platform=<platform>`。
- 依赖：Python 3.9+、SCons 4.4+。
- 常用目标：`editor`（默认）、`template_release`、`template_debug`。
- SCU 构建：`scons scu_build=yes`。
- 构建产物输出到 `bin/`。

## Pre-commit 钩子
- 使用 **`prek`**（而非 `pre-commit`）。CI 调用 `prek run --files ...`。
- 运行全部文件：`prek run --all`。
- clang-tidy 仅手动运行：`prek run --hook-stage manual clang-tidy --all`。
- Python：使用 **ruff** 格式化（非 black），规则定义在 `pyproject.toml`。
- C++：使用 **clang-format** 格式化（基于 LLVM 风格，Tab 缩进，4 空格宽度，`ColumnLimit: 0`）。

## 测试
- 框架：**doctest**（内置于 `thirdparty/doctest`）。
- 测试文件：`tests/**/test_*.cpp`。创建新测试文件：`python tests/create_test.py <Name> <path>`。
- 每个测试文件必须包含 `TEST_FORCE_LINK(test_name)` 宏以实现自动发现。
- 常用宏：`TEST_CASE`、`TEST_COND`、`TEST_FAIL_COND`、`TEST_CASE_PENDING`（跳过）、`TEST_CASE_MAY_FAIL`。
- 测试名称中的标签会触发对应的初始化：`[SceneTree]`、`[Editor]`、`[Audio]`。
- 运行测试：先构建，再运行 `./bin/<binary> --test <测试名称或筛选条件>`。运行被跳过的测试：`--test --no-skip`。
- 测试失败路径时，使用 `ERR_PRINT_OFF` / `ERR_PRINT_ON` 来屏蔽期望的错误输出。

## 代码规范
- **C++/GLSL**：Tab 缩进（参见 `.editorconfig` / `.clang-format`）。
- **Python/SConstruct/SCsub**：空格缩进（4 空格），由 Ruff 强制执行。
- **YAML/clang-format/clang-tidy**：2 空格缩进。
- 所有文件：LF 换行，UTF-8 编码，末尾保留换行，去除行尾空白。
- Include 排序由 clang-format 的 `Regroup` 规则控制：core/drivers/editor/main/scene/servers/tests（如 `"core/..."`）→ modules/platform → thirdparty → 系统头文件。
- Godot 命名规范：参数使用 `p_` 前缀（如 `p_name`），成员变量使用相应前缀。
- `.gen.cpp` 文件是自动生成的，不得包含在 glob 模式中，需显式加入构建。

## 模块与第三方库
- `modules/` — 可选功能模块，各自有独立的 `SCsub`，部分含 `doc_classes/`。
- `platform/` — 平台后端，各自有 `detect.py` 定义构建选项。
- `thirdparty/` — 内嵌的第三方依赖，排除在 lint、格式化和大部分静态检查之外。

## AI 贡献
- 如果 PR 由 AI 辅助编写，标题必须以 🤖 开头。
