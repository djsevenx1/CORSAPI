# CORSAPI - API 代理转发服务

基于 Cloudflare Workers 的通用 API 中转代理服务，用于加速和转发 API 请求。

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/SzeMeng76/CORSAPI)

> **Credit**: 本项目二开自 [hafrey1/LunaTV-config](https://github.com/hafrey1/LunaTV-config)

---

## 功能特性

- ✅ 支持所有 HTTP 方法（GET、POST、PUT、DELETE 等）
- ✅ 自动转发请求头和请求体
- ✅ 完整的 CORS 支持
- ✅ 超时保护（9 秒）
- ✅ 为每个 API 源生成唯一路径，避免缓存冲突
- ✅ 自动提取并转发额外的 query 参数
- ✅ 支持 KV 缓存（可选）

---

## 快速部署

### 方式一：部署到 Cloudflare Workers

1. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com)
2. 进入 **Workers & Pages** → **创建应用程序** → **Workers**
3. 从 **Hello World!** 开始 → 项目命名 → **部署** → **编辑代码**
4. 将 `_worker.js` 文件内容复制到在线编辑器中
5. 点击 **保存并部署** 完成上线
6. （可选）绑定自定义域名：
   - 打开 Worker 设置 → **Triggers** → **Custom Domains**
   - 添加你的域名并保存

### 方式二：部署到 Cloudflare Pages

1. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com)
2. 下载本仓库中的 `_worker.js` 文件
3. 在本地新建一个空文件夹，将 `_worker.js` 放入其中
4. 前往 **Workers & Pages** → **创建应用程序** → **Pages**
5. **上传资产** → 项目命名 → **创建项目** → 从计算机中选择文件夹
6. 上传后点击 **部署站点**

---

## 使用方法

假设你的 Worker 部署在：`https://api.example.workers.dev`

### 基本用法

通过 `?url=` 参数转发任意 API 请求：

```
https://api.example.workers.dev/?url=https://example.com/api
```

### 高级用法：源专属路径

为每个 API 源使用唯一路径（推荐）：

```
https://api.example.workers.dev/p/source1?url=https://api1.com/vod
https://api.example.workers.dev/p/source2?url=https://api2.com/vod
```

这样可以：
- 避免不同源之间的缓存冲突
- 让客户端认为是不同的 API 地址
- 提高兼容性和稳定性

### 参数转发

所有额外的 query 参数都会自动转发到目标 API：

```
请求：https://api.example.workers.dev/?url=https://example.com/api&ac=list&pg=1
转发：https://example.com/api?ac=list&pg=1
```

---

## 健康检查

访问 `/health` 端点检查服务状态：

```
https://api.example.workers.dev/health
```

返回 `OK` 表示服务正常运行。

---

## 可选配置

### 启用 KV 缓存

如需使用 KV 缓存功能：

1. 在 Cloudflare Dashboard 中创建 KV 命名空间：
   - **存储和数据库** → **Workers KV** → **Create namespace**
   - 命名空间名称可自定义，例如：`MyKVNamespace`

2. 绑定 KV 命名空间到 Worker：
   - 在 Worker 设置中 → **绑定** → **添加绑定** → **KV 命名空间**
   - 变量名：`KV`
   - 选择刚才创建的 KV 命名空间
   - 点击 **添加绑定**

### 修改超时时间

在 `_worker.js` 中找到以下代码并修改超时毫秒数：

```javascript
const timeoutId = setTimeout(() => controller.abort(), 9000) // 默认 9 秒
```

---

## 注意事项

- **免费额度**：Cloudflare Workers 免费版每天有 10 万次请求额度
- **超时设置**：默认请求超时时间为 9 秒
- **CORS 支持**：已启用完整的 CORS 支持，可直接在前端应用中调用
- **防止递归**：自动检测并阻止递归调用自身

---

## 技术细节

### 源标识符提取

系统会自动从 API URL 中提取唯一标识符作为路径：

- `caiji.maotaizy.cc` → `/p/maotai`
- `iqiyizyapi.com` → `/p/iqiyi`
- `api.maoyanapi.top` → `/p/maoyan`

### 参数处理

- 自动提取 `url=` 参数作为目标地址
- 所有其他 query 参数自动转发到目标 API
- 支持 POST/PUT 请求的 body 转发

---

## 免责声明

本项目仅供学习和研究使用。使用本项目所产生的一切后果由使用者自行承担。

---

## 致谢

本项目基于 [hafrey1/LunaTV-config](https://github.com/hafrey1/LunaTV-config) 的 CORSAPI 部分进行二次开发和简化。

---

## 许可证

本项目采用与原项目相同的许可证。
