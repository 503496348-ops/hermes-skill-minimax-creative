    "cfg_scale": 7.0,
})

if result.success:
    print(f"图片生成成功: {result.artifacts}")
```

### 支持的操作

| 操作 | 说明 | 需要参数 |
|------|------|---------|
| text_to_video | 文生视频 | prompt |
| image_to_video | 图生视频 | prompt, image_path |
| text_to_image | 文生图片 | prompt |
| image_to_image | 图片转换 | prompt, image_path |
| inpainting | 局部重绘 | prompt, image_path, mask_path |
| workflow_video | 自定义工作流 | workflow_path |
| workflow_image | 自定义工作流 | workflow_path |

### 系统要求

- NVIDIA GPU (6GB+ VRAM 推荐)
- PyTorch + CUDA
- ComfyUI 已安装

### 优势

- **无 API Key**：本地运行，无需网络
- **隐私保护**：数据不出本地
- **自定义工作流**：支持任意 ComfyUI 节点组合
- **批量生成**：支持 batch_size 参数

## 3D模型处理 (NEW)


- VRAM Manager 支持 `dtype="int8"` 加载模型，显存占用减半
- 适用于 LOW_VRAM / NO_VRAM 状态下的大模型推理
- INT8 tensor-wise 量化，质量损失极小（<1%）

```python
from modules.model_3d_processor import Model3DProcessor
import numpy as np

processor = Model3DProcessor()

# 准备数据
vertices = np.array([
    [0.0, 0.0, 0.0],
    [1.0, 0.0, 0.0],
    [0.0, 1.0, 0.0],
    [0.0, 0.0, 1.0],
], dtype=np.float32)

faces = np.array([
    [0, 1, 2],
    [0, 1, 3],
    [0, 2, 3],
    [1, 2, 3],
], dtype=np.int64)

# 保存GLB
result = processor.save_glb(
    vertices, faces, "output.glb",
    metadata={"name": "test_cube"},
)

# 加载GLB
load_result = processor.load_glb("output.glb")
if load_result.mesh_data:
    print(f"顶点数: {len(load_result.mesh_data.vertices)}")
    print(f"面数: {len(load_result.mesh_data.faces)}")
```

**支持格式**:
- GLB (二进制glTF)
- GLTF (JSON glTF)
- OBJ (Wavefront)
- FBX (Autodesk)

## 工作流

使用此技能时，按以下步骤执行：
- [ ] 1. 确认用户需求和使用场景
- [ ] 2. 加载相关代码和配置
- [ ] 3. 执行核心功能
- [ ] 4. 验证输出结果
- [ ] 5. 反馈给用户
## 2026-07-02 融合增强

- 破窗造视新增幻灯片级图片生成提示词包：强制真实可见文字、配色完整性、版式节奏与 manifest 证据。


## 2026-07-03 运行时增强

- 新增模型管线加载守卫：识别 flat/nested 仓库布局，并在注意力后端不兼容时要求 fallback。
- 验证：新增模块通过 py_compile 和定向 pytest，代码不依赖外部服务。

## 2026-07-03 产品收敛门禁

- 新增 `scripts/product_convergence_gate.py`：从远端干净 clone 后可运行 `python3 scripts/product_convergence_gate.py --json`，检查 SKILL/README、入口文件、smoke 目标、测试与外部融合引用是否自洽。
- 新增 `tests/test_product_convergence_gate.py`：确保门禁在产品仓库中真实可执行，避免后续增强只停留在孤岛模块。

## 一键开箱交付

本仓库提供标准一键入口：

- `install.sh`：用户的一条命令安装与冒烟入口。
- `scripts/setup.py`：安装声明依赖并串联 doctor。
- `scripts/doctor.py`：检查 README、SKILL、入口脚本、package scripts 与产品收敛门禁。
- `scripts/smoke.py`：运行 doctor、产品收敛门禁与 Python 编译级冒烟。
- `tests/test_one_click_open_box.py`：契约测试，防止 README 写了但脚本缺失。

## 快速开始

```bash
python3 scripts/cli.py --help
```
