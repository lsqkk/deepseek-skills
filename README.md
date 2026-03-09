# 🚀 DeepSeek Skills - DeepSeek 本地文件操作增强插件

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Node.js](https://img.shields.io/badge/Node.js-22.x-green)
![Tampermonkey](https://img.shields.io/badge/Tampermonkey-兼容-ffbf00)
![Violentmonkey](https://img.shields.io/badge/Violentmonkey-兼容-orange)
![WebSocket](https://img.shields.io/badge/WebSocket-实时通信-yellow)
![安全性](https://img.shields.io/badge/安全性-用户确认-red)
![平台](https://img.shields.io/badge/平台-DeepSeek网页版-9cf)

本系统为 DeepSeek 网页版添加本地文件操作能力，模拟 SKILLS 的CODING功能。通过浏览器插件+本地服务的架构，在保障安全的前提下实现智能文件编辑。

![demo](demo.png)

## 🛠️ 系统架构
```
用户浏览器 (DeepSeek网页)
    ↓ (Tampermonkey脚本监控)
本地Node.js桥接服务 (localhost:8765)
    ↓ (安全文件操作)
用户本地文件系统
```

> **为DeepSeek网页版赋予本地文件操作能力，让AI助手直接读写您的项目文件**

## ✨ 项目亮点

- 🔧 **无缝集成** - 无需离开DeepSeek网页，直接操作本地文件
- 🛡️ **安全可靠** - 每次文件操作都需要用户手动确认，自动备份原文件
- 📋 **智能剪贴板** - 文件内容自动复制到剪贴板，一键粘贴到聊天框
- 🚀 **高效工作流** - 支持智能片段更新，最小化通信开销
- 🎯 **精准识别** - 智能命令检测，避免重复处理

## 🎮 核心功能

### 📖 文件操作能力
- **读取文件** - 获取本地文件内容，自动复制到剪贴板
- **写入文件** - 创建或覆盖文件内容
- **智能更新** - 仅修改文件中的特定片段，节省Token消耗
- **目录浏览** - 查看项目文件结构
- **自动备份** - 每次修改前自动创建备份文件

### 🔄 智能工作流
- **多命令连续处理** - 支持连续执行多个文件操作命令
- **智能片段定位** - 通过代码片段精确定位修改位置
- **JSON自动修复** - 智能修复AI输出的JSON格式错误
- **实时状态监控** - 浏览器右下角显示连接状态

### 🛡️ 安全特性
- **用户确认机制** - 每次写操作都必须手动确认
- **路径安全检查** - 防止目录遍历攻击
- **文件大小限制** - 默认10MB文件大小限制
- **本地连接限制** - 仅接受来自localhost的连接
- **操作日志记录** - 详细的操作审计日志

## 🚀 快速开始

### 系统要求
- **浏览器**：Chrome / Edge / Firefox（支持Tampermonkey或Violentmonkey）
- **Node.js**：v14.0.0 或更高版本
- **DeepSeek**：网页版访问权限（https://chat.deepseek.com）

### 安装步骤

#### 1. 安装浏览器扩展
1. 安装 **Tampermonkey** 扩展：
   - [Chrome Web Store](https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo)
   - [Microsoft Edge Add-ons](https://microsoftedge.microsoft.com/addons/detail/tampermonkey/iikmkjmpaadaobahmlepeloendndfphd)

2. 安装用户脚本：
   - 点击Tampermonkey图标 → "添加新脚本"
   - 复制 `deepseek_skills.js` 内容到编辑器中
   - 按 `Ctrl+S` 保存

#### 2. 设置本地桥接服务
```bash
# 克隆或下载项目
git clone https://github.com/lsqkk/deepseek-skills.git
cd deepseek-skills

# 安装依赖
npm install ws

# 启动桥接服务
node local_file_bridge.js
```
服务启动后，终端将显示：`✅ 桥接服务已启动`

#### 3. 配置DeepSeek AI
1. 打开 [DeepSeek网页版](https://chat.deepseek.com)
2. 在新对话中发送 `deepseek_skills.md` 中的提示词内容
3. AI将确认理解并准备就绪

### 验证安装
1. 刷新DeepSeek页面
2. 查看右下角状态指示器（🟢绿色表示已连接）
3. 尝试读取文件：`读取你的绝对路径文件（例如 /Users/me/test/example.txt 或 C:\Users\me\test\example.txt）`

## 📖 使用指南

### 基本文件操作

#### 读取文件
```
用户：请读取我的配置文件 /Users/me/project/config.json

AI：[[COMMAND={"action":"read_file","file_path":"/Users/me/project/config.json"}]]
→ 用户确认 → 内容自动复制到剪贴板 → 可Ctrl+V粘贴
```

#### 修改文件片段（推荐方式）
```json
[[COMMAND={
  "action": "update_section",
  "file_path": "/project/app.js",
  "old_content_start": "function calculate() {",
  "old_content_end": "}  // calculate结束",
  "new_content": "function calculate() {\n  // 优化后的算法\n  return result * 1.1;\n}"
}]]
```

#### 完整示例：修复代码bug
```
用户：修复utils.js中的calculateTotal函数，需要处理库存检查

AI：我先查看当前文件内容...
[[COMMAND={"action":"read_file","file_path":"/project/src/utils.js"}]]

（查看内容后）
AI：找到问题了，现在修复它...
[[COMMAND={
  "action": "update_section",
  "file_path": "/project/src/utils.js",
  "old_content_start": "  for (let item of items) {",
  "old_content_end": "  return total;",
  "new_content": "  for (let item of items) {\n    if (item.inStock) {\n      total += item.price * item.quantity;\n    }\n  }\n  return total;"
}]]
```

### 高级功能

#### 1. 批量文件操作
```json
// 创建项目基础结构
[[COMMAND={"action":"create_file","file_path":"/project/package.json","content":"{\"name\":\"my-app\",\"version\":\"1.0.0\"}"}]]
[[COMMAND={"action":"create_file","file_path":"/project/src/index.js","content":"console.log('Hello World');"}]]
[[COMMAND={"action":"create_file","file_path":"/project/README.md","content":"# My Project\\n\\n这是一个示例项目。"}]]
```

#### 2. 配置文件更新
```json
[[COMMAND={
  "action": "update_section",
  "file_path": "/project/config.json",
  "old_content_start": "  \"version\": \"1.0.0\",",
  "new_content": "  \"version\": \"2.0.0\",\n  \"author\": \"Your Name\",\n  \"updated\": \"2024-01-01\""
}]]
```

#### 3. 目录结构查看
```json
[[COMMAND={
  "action": "list_dir",
  "dir_path": "/project/src"
}]]
```

## 📁 项目结构

```
deepseek-skills/
├── deepseek_skills.js          # Tampermonkey用户脚本（浏览器端核心）
├── local_file_bridge.js        # Node.js桥接服务（本地文件操作）
├── deepseek_skills.md          # AI技能定义与提示词文件
├── README.md                   # 项目说明文档（本文档）
├── LICENSE                     # MIT开源许可证
└── node_modules/               # Node.js依赖（安装后生成）
```

## ⚙️ 配置选项

### 桥接服务配置 (`local_file_bridge.js`)
```javascript
const CONFIG = {
    PORT: 8765,                    // 服务端口（可自定义）
    ALLOWED_PATHS: [],             // 访问白名单；空数组=允许所有路径（不是工作目录）
    MAX_FILE_SIZE: 10 * 1024 * 1024, // 10MB文件大小限制
    BACKUP_DIR: '.deepseek_backups', // 备份目录名称
    DEBUG: true                    // 调试模式
};
```

> 路径说明：本项目不会把工作目录固定到某个盘符。文件读写以命令中的 `file_path`/`dir_path` 为准，建议始终使用绝对路径。

### 网页脚本配置 (`deepseek_skills.js`)
```javascript
const CONFIG = {
    BRIDGE_WS_URL: 'ws://localhost:8765', // 桥接服务地址
    COPY_TO_CLIPBOARD: true,              // 自动复制到剪贴板
    AUTO_SCAN_INTERVAL: 2000,             // AI响应检测间隔(ms)
    DEBUG_MODE: true                      // 调试日志
};
```

## 🔧 故障排除

### 常见问题

#### Q1: 状态指示器显示未连接（红色）
- ✅ 检查桥接服务是否运行：`node local_file_bridge.js`
- ✅ 检查端口是否占用：`netstat -ano | findstr :8765`（Windows）
- ✅ 重启服务并刷新 DeepSeek 页面

#### Q2: AI不输出命令格式
- ✅ 确保已发送完整的提示词给AI
- ✅ 检查Tampermonkey脚本是否启用（图标显示数字）
- ✅ 查看浏览器控制台是否有错误信息

#### Q3: 文件操作失败
- ✅ 检查文件路径是否正确（使用绝对路径）
- ✅ 确认文件权限（可读/可写）
- ✅ 查看桥接服务的错误日志

#### Q4: JSON解析错误
```
错误：Expected ',' or '}' after property value in JSON
解决方案：AI输出的代码包含未转义的特殊字符
```
- ✅ 让AI重新生成命令，确保代码正确转义
- ✅ 使用`update_section`代替`write_file`处理大文件
- ✅ 系统会自动尝试修复JSON格式

### 调试技巧

#### 查看日志
```bash
# 桥接服务日志（实时查看）
tail -f bridge_service.log

# 或直接在控制台查看
node local_file_bridge.js
```

#### 浏览器开发者工具
1. 按 `F12` 打开开发者工具
2. 切换到 `Console` 标签
3. 查看 `[FileSkill]` 开头的调试信息
4. 监控WebSocket连接状态

## 🎯 应用场景

### 开发工作流
- **代码审查与修复**：AI直接修改问题代码
- **项目初始化**：一键创建项目结构
- **配置管理**：批量更新配置文件
- **文档生成**：自动生成代码文档

### 学习与教育
- **编程练习**：实时修改练习代码
- **代码分析**：AI分析并改进现有代码
- **项目重构**：协助代码重构和优化

### 自动化任务
- **批量重命名**：正则表达式批量修改文件名
- **数据转换**：不同格式文件转换
- **模板生成**：根据模板生成代码文件

## 🏗️ 技术架构

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   DeepSeek网页  │    │  Tampermonkey脚本 │    │ Node.js桥接服务 │
│                 │◄──►│                  │◄──►│                 │
│ • 显示AI回复     │    │ • 监控AI输出      │    │ • WebSocket服务 │
│ • 用户输入       │    │ • 解析命令        │    │ • 文件系统操作  │
│                 │    │ • 安全确认        │    │ • 备份管理      │
└─────────────────┘    └──────────────────┘    └─────────────────┘
        │                        │                        │
        │                        │                        ▼
        │                        │               ┌─────────────────┐
        └────────────────────────┴───────────────┤   本地文件系统   │
                                                 │                 │
                                                 │ • 项目文件       │
                                                 │ • 配置文件       │
                                                 │ • 备份文件       │
                                                 └─────────────────┘
```

### 安全架构
1. **双重确认**：用户必须确认每个写操作
2. **路径白名单**：可配置允许访问的目录
3. **大小限制**：防止处理过大文件
4. **自动备份**：操作前自动备份原文件
5. **本地连接**：仅接受localhost连接

## 🤝 贡献指南

欢迎贡献代码、报告问题或提出新功能建议！

### 开发流程
1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

### 代码规范
- 使用ES6+ JavaScript语法
- 添加必要的注释和文档
- 确保向后兼容性
- 编写单元测试（计划中）

## 📄 许可证

本项目基于 MIT 许可证开源 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 🙏 致谢

感谢以下开源项目：
- [Tampermonkey](https://www.tampermonkey.net/) - 强大的用户脚本管理器
- [ws](https://github.com/websockets/ws) - Node.js WebSocket库
- [DeepSeek](https://www.deepseek.com/) - 优秀的AI助手平台

## 📞 支持与反馈

- **问题报告**：请使用GitHub Issues
- **功能建议**：通过Issues或讨论区提出
- **紧急问题**：查看故障排除章节

---

<div align="center">
  <p>让 AI 真正成为您的编程伙伴 ✨</p>
  <p>
    <sub>如果这个项目对您有帮助，请给个⭐️支持！</sub>
  </p>
</div>
