# 个人导航首页

这是一个简洁、美观、功能实用的个人浏览器起始页 / 导航页。它集成了**多引擎搜索**、**常用网站快速链接**以及**常用工具快捷入口**，并针对移动端和桌面端进行了响应式优化。

## ✨ 功能特点

- **🔍 聚合搜索** – 支持 Google、百度、GitHub 等多个搜索引擎，一键切换。
- **📌 常用链接网格** – 以卡片形式展示你常用的网站，点击即可跳转。
- **🚀 顶部工具栏** – 快速访问时间日历、备忘录、汽车定位、网盘入口等外部服务。
- **💾 本地存储** – 自动记住你上次选择的搜索引擎，刷新页面依然保留。
- **⌨️ 键盘快捷键** – `Ctrl + Enter` 快速提交搜索，提升效率。
- **📱 响应式设计** – 完美适配手机、平板和桌面显示器。
- **🎨 毛玻璃效果** – 现代化的 UI 设计，背景渐变与模糊玻璃质感。

## 🛠 技术栈

- **HTML5** – 页面结构
- **CSS3** – Flex/Grid 布局、动画、媒体查询、毛玻璃效果
- **JavaScript (ES6)** – 交互逻辑、本地存储、事件处理
- **[Font Awesome 6](https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css)** – 图标库

## 🚀 部署指南（使用 Nginx Proxy Manager）

本页面为纯静态文件，你可以轻松地通过 **Nginx Proxy Manager (NPM)** 将其部署到你的服务器上。

### 前提条件

- 已安装 Docker 和 Docker Compose
- 已部署 Nginx Proxy Manager（参考[官方文档](https://nginxproxymanager.com/guide/)）
- 拥有一个指向你服务器 IP 的域名（例如 `nav.example.com`）

### 步骤

1. **准备静态文件**
   将本 `index.html` 文件以及可能用到的其他资源（如图片、CSS、JS）放到服务器上的一个目录中，例如：

   text

   ```
   /opt/nav/index.html
   ```

   

2. **确保 NPM 容器能访问该目录**
   如果你使用 Docker 部署的 NPM，需要在 `docker-compose.yml` 中将该目录挂载到容器内。编辑 NPM 的 `docker-compose.yml`，在 `volumes` 部分添加一行映射：

   yaml

   ```
   volumes:
     - ./data:/data
     - ./letsencrypt:/etc/letsencrypt
     - /opt/nav:/www/nav        # 将宿主机目录映射到容器内的 /www/nav
   ```

   

   然后重启 NPM 容器：

   bash

   ```
   docker-compose down && docker-compose up -d
   ```

   

3. **在 NPM 后台添加代理主机**

   - 登录 NPM 管理后台（默认端口 `81`）

   - 点击 **Hosts → Proxy Hosts → Add Proxy Host**

   - **Domain Names**：填写你的域名（如 `nav.example.com`）

   - **Scheme**：`http`

   - **Forward Hostname / IP**：`127.0.0.1`

   - **Forward Port**：`80`

   - 切换到 **SSL** 选项卡，申请 Let's Encrypt 证书并开启 **Force SSL**（可选但推荐）

   - 切换到 **Advanced** 选项卡，在 **Custom Nginx Configuration** 中输入以下配置：

     nginx

     ```
     location / {
         root   /www/nav;          # 与容器内挂载路径一致
         try_files $uri $uri/ /index.html;
         index  index.html index.htm;
     }
     ```

     

   - 点击 **Save** 保存。

4. **验证**
   访问你的域名（如 `https://nav.example.com`），即可看到本导航页。

## 🎨 自定义指南

### 修改常用链接

编辑 HTML 中 `class="links-grid"` 的 `div` 内的每个 `<a>` 标签，修改 `href` 和图标类名即可。

html

```
<a href="https://你的链接" class="link-item" target="_blank">
    <i class="fas fa-图标名 link-icon"></i>
    链接名称
</a>
```



图标名称请参考 [Font Awesome 图标库](https://fontawesome.com/icons)。

### 修改搜索引擎选项

在 `<select id="engineSelect">` 中添加或删除 `<option>` 即可。
每个 `option` 的 `value` 应为搜索引擎的搜索 URL（将查询词部分用 `q=` 等参数占位，实际查询词会被 JavaScript 自动拼接）。

### 调整样式

所有样式均写在 `<style>` 标签内，你可以随意修改颜色、间距、字体等。
主要配色由 `background: linear-gradient(135deg, #6a11cb, #2575fc)` 控制。

### 修改顶部工具栏链接

工具栏的每个 `.tool` 链接位于 `class="toolbar"` 的 `div` 中，修改 `href` 和图标即可。

## ⚠️ 注意事项

- 本页面使用了外部 CDN 资源（Font Awesome），请确保你的网络能够访问 `cdnjs.cloudflare.com`。
- 所有外部链接（如 `https://1lemon.cn/1` 等）仅为示例，请替换为你自己的实际服务地址。
- 本地存储仅保存搜索引擎偏好，不会收集任何个人信息。
- 如果你不需要某些功能（如工具栏），可直接删除对应的 HTML 代码块。

## 📄 许可证

本项目基于 MIT 许可证开源，你可以自由使用、修改和分发。
