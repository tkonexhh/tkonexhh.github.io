---

## title: RenderDocMcp

date: 2025-12-1 00:00:00 +0800
categories: 
tags: AI

---

所使用的GitHub库为 [https://github.com/halby24/RenderDocMCP](https://github.com/halby24/RenderDocMCP)

## 准备阶段

本地环境配置 安装python uv

> 关于uv的环境安装
> powershell -ExecutionPolicy ByPass -c "irm [https://astral.sh/uv/install.ps1](https://astral.sh/uv/install.ps1) | iex"

根据库中说明 执行

```
python scripts/install_extension.py
```

> 这个命令会安装RenderDoc的Extension

![RenderDoc截图](/assets/Image/RenderDocMcp的使用/RenderDocMcp的使用 图1.png)

点击Loaded并重启RenderDoc

在github目录执行uv

```
uv tool install .     # 这个很重要
uv tool update-shell  # PATHに追加
```

执行完毕才能真正的使用`renderdoc-mcp`命令

### Cursor配置

添加以下mcp配置

```
{
  "mcpServers": {
    "renderdoc": {
      "command": "renderdoc-mcp"
    }
  }
}
```

## mcp 使用方法

### 命令列表
```
| 命令 | 说明 |
|--------|------|
| `get_capture_status` | 检查rdc加载状态 |
| `get_draw_calls` | 获取绘制调用列表（层级结构） |
| `get_draw_call_details` | 获取特定绘制调用的详细信息 |
| `get_shader_info` | 获取着色器的源代码和常量缓冲区的值 |
| `get_buffer_contents` | 获取缓冲区内容（Base64）|
| `get_texture_info` | 获取纹理元数据 |
| `get_texture_data` | 获取纹理像素数据 (Base64) |
| `get_pipeline_state` | 获取管道状态 |
```

### 使用范例

获取绘制调用列表
```
get_draw_calls(include_children=true)
```

获取Shader信息
```
get_shader_info(event_id=123, stage="pixel")
```

获取管线状态
```
get_pipeline_state(event_id=123)
```

获取纹理数据
```
# 获取2D纹理的mip 0
get_texture_data(resource_id="ResourceId::123")

# 获取特定的mip级别
get_texture_data(resource_id="ResourceId::123", mip=2)

# 获取立方体贴图的特定面 (0=X+, 1=X-, 2=Y+, 3=Y-, 4=Z+, 5=Z-)
get_texture_data(resource_id="ResourceId::456", slice=3)

# 获取3D纹理的特定深度切片
get_texture_data(resource_id="ResourceId::789", depth_slice=5)

# 获取整个缓冲区
get_buffer_contents(resource_id="ResourceId::123")

# 从偏移量256获取512字节
get_buffer_contents(resource_id="ResourceId::123", offset=256, length=512)
```

一些使用结果
![RenderDoc截图](/assets/Image/RenderDocMcp的使用/RenderDocMcp的使用 图2.png)
![RenderDoc截图](/assets/Image/RenderDocMcp的使用/RenderDocMcp的使用 图3.png)
![RenderDoc截图](/assets/Image/RenderDocMcp的使用/RenderDocMcp的使用 图4.png)