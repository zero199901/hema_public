# 河马APP 2.12.2 逆向分析与Frida测试项目

## 🎯 项目简介

本项目对河马APP（com.dz.hmjc）进行逆向分析，重点研究其网络请求加密机制，并使用Frida动态Hook技术实现加密功能的关闭，为安全研究和漏洞分析提供技术支持。

## 📁 项目结构

```
hema/
├── README.md                           # 项目总览
├── analysis/                           # Python分析脚本
│   ├── decrypt_analysis.py            # 解密逻辑分析
│   ├── decrypt_from_log.py            # 从日志解密
│   ├── decrypt_response.py             # 响应解密
│   └── requirements.txt                # Python依赖
├── frida_scripts/                      # Frida测试脚本
│   ├── complete_test.js                # 综合测试脚本
│   ├── simple_disable_encryption.js    # 简化版加密关闭
│   ├── test_encryption_hook.js         # Hook验证测试
│   ├── monitor_network_requests.js     # 网络请求监控
│   ├── disable_encryption.js           # 完整版加密关闭
│   ├── README_加密关闭.md              # 加密关闭说明
│   └── 测试指南.md                     # 详细测试指南
├── md/                                 # 分析文档
│   ├── URL调用分析.md                  # URL调用分析报告
│   ├── 解密逻辑分析.md                 # 解密逻辑分析报告
│   └── Frida加密关闭测试报告.md        # Frida测试报告
├── sources/                            # 反编译源码
│   └── com/dz/                         # 河马APP源码
└── resources/                          # 应用资源文件
```

## 🔍 主要发现

### 加密机制分析

1. **加密开关控制**：
   - 通过 `com.dz.foundation.base.*.s.*.d()` 方法控制
   - 网络请求类 `com.dz.business.*.*.c` 中的 `R()` 方法判断是否加密

2. **加密实现**：
   - **算法**: AES/CBC/PKCS5Padding
   - **密钥**: `"d*****m"`
   - **IV**: `"a*****t"`
   - **编码**: Base64

3. **加密范围**：
   - 请求头 `datas` 字段
   - 请求体内容
   - 响应数据解密

### Frida Hook策略

1. **Hook加密开关**：强制返回 `false` 关闭加密
2. **Hook加密方法**：直接返回原始数据，跳过加密
3. **网络监控**：实时监控网络请求和响应

## 🚀 快速开始

### 环境要求

- Android设备（已Root）
- Frida 16.7.19+
- ADB工具
- Python 3.x（用于分析脚本）

### 安装依赖

```bash
# Python依赖
pip install -r analysis/requirements.txt

# Frida（如果未安装）
pip install frida-tools
```

### 快速测试

```bash
# 1. 确保设备连接
adb devices

# 2. 启动Frida Server（在设备上）
adb shell
su
./data/local/tmp/frida-server &

# 3. 运行综合测试脚本
frida -U -f com.dz.hmjc -l frida_scripts/complete_test.js --no-pause
```

## 📊 测试结果

### 成功标志

- ✅ 加密开关状态显示为 `false`
- ✅ 请求头 `datas` 字段显示明文JSON
- ✅ 请求体显示明文JSON
- ✅ 响应数据不再解密

### 测试日志示例

```
============================================================
🚀 河马APP加密关闭 + 网络监控综合测试
============================================================

[1] 关闭加密开关...
✅ Hook加密开关方法成功
✅ Hook网络加密开关成功
✅ Hook加密/解密方法成功

[2] 启动网络请求监控...
✅ Hook OkHttp Request.Builder成功
✅ Hook河马APP网络方法成功

[3] 执行测试验证...
🔧 当前加密开关状态: false
✅ 加密开关已成功关闭

📡 网络请求: POST https://api.example.com/search
🔍 datas请求头:
   名称: datas
   值: {"version":"1.0","packageName":"com.dz.hmjc",...}
   ✅ 检测到明文数据
```

## 🛠️ 工具和脚本

### Python分析脚本

- `decrypt_analysis.py`: 分析加密逻辑和解密算法
- `decrypt_from_log.py`: 从Frida日志中提取和解密数据
- `decrypt_response.py`: 解密API响应数据

### Frida测试脚本

- `complete_test.js`: 综合测试脚本（推荐使用）
- `simple_disable_encryption.js`: 简化版加密关闭
- `test_encryption_hook.js`: Hook验证测试
- `monitor_network_requests.js`: 网络请求监控

## 📚 文档说明

- `md/URL调用分析.md`: 详细的URL调用分析报告
- `md/解密逻辑分析.md`: 加密解密逻辑的深入分析
- `md/Frida加密关闭测试报告.md`: 完整的Frida测试报告
- `frida_scripts/测试指南.md`: 详细的测试操作指南

## ⚠️ 重要声明

1. **仅用于学习研究目的**
2. **不要在生产环境使用**
3. **遵守相关法律法规**
4. **注意保护用户隐私**
5. **测试完成后及时清理**

## 🤝 贡献指南

1. Fork本项目
2. 创建特性分支
3. 提交更改
4. 发起Pull Request

## 📄 许可证

本项目仅供学习和研究使用，请勿用于商业用途。

## 🔄 更新日志

- **v1.0**: 完成基础逆向分析
- **v1.1**: 实现Frida Hook脚本
- **v1.2**: 添加网络监控功能
- **v1.3**: 完善测试脚本和文档
- **v1.4**: 优化Hook策略，提高稳定性

---

**免责声明**: 本项目仅用于安全研究和学习目的，请勿用于非法用途。使用者需自行承担相关责任。



![6163523eb9b54ba256422d8f2c4ff0d6](https://github.com/user-attachments/assets/24a80507-3b18-45ea-bf9d-1383d30ad247)
