# 《消失的那一天》GitHub Pages 部署说明（免命令行版）

这个版本保留原有的银白色复古论坛、8 个版块、搜索、帖子详情、回复、点赞、收藏、调查笔记和结局解锁。

## 先理解这个版本的数据怎么保存

- **GitHub Pages**：免费托管网页，给你一个公开网址。
- **Supabase Auth**：负责邮箱注册、登录和会话验证。
- **帖子、回复、点赞、收藏、调查进度**：仅写入当前浏览器的 `localStorage`，并按登录账号分开保存；不会上传到 Supabase 数据库，也不会在不同设备/浏览器间同步。
- 初始剧情帖子是网页自带的内容；你后来发布的帖子和操作属于本地记录。
- 如果清理浏览器网站数据、换浏览器或换设备，本地帖子和进度可能无法找回。建议不要把它当作重要资料的唯一备份。

> 注意：邮箱登录是真实的 Supabase 登录，所以注册邮箱会交给 Supabase 处理；但本项目不会把论坛帖子、回复或调查记录上传到 Supabase。

---

## 第一部分：创建 Supabase 登录服务（只需做一次）

### 1. 注册并创建项目

1. 打开 https://supabase.com/ ，注册/登录。
2. 点击创建新项目（New project）。
3. 选择一个组织，填写项目名称，例如 `vanished-day`。
4. 设置数据库密码并保存好（本网页不会使用这个数据库密码）。
5. 选择离你较近的区域，点击创建项目。等待项目准备完成。

Supabase 免费额度可能会调整；个人测试通常可以先从免费方案开始。注册和部署前请查看它当前的定价与使用限制。

### 2. 复制网页需要的两个值

1. 在 Supabase 项目控制台里打开 **Project Settings / 项目设置**。
2. 找到 **API** 或 **API Keys** 页面。
3. 复制项目 URL（Project URL）。
4. 复制公开客户端密钥（通常标记为 `anon` 或 `publishable`）。
5. **不要复制 `service_role`、secret key 或数据库密码到网页。** 网页代码会公开给访客，只能使用公开客户端密钥。

### 3. 把两个值填入网页

1. 在电脑上用文本编辑器打开 `index.html`。Mac 可以用 TextEdit，但要先在菜单里选择“格式 → 制作纯文本”；也可以使用 VS Code。
2. 搜索 `SUPABASE_URL`，把右侧引号中的“在这里填写你的 Supabase Project URL”替换为你复制的 URL。
3. 搜索 `SUPABASE_ANON_KEY`，把右侧引号中的提示文字替换为你复制的 `anon` 或 `publishable` 公钥。
4. 保留英文引号，不要多删逗号；保存文件。

示意（以下是占位符，不是可用的真实密钥）：

```js
const SUPABASE_URL = 'https://你的项目编号.supabase.co';
const SUPABASE_ANON_KEY = '你的公开客户端密钥';
```

### 4. 检查邮箱验证设置

1. 在 Supabase 控制台打开 **Authentication**。
2. 找到 **Sign In / Providers**（登录方式）里的 Email。
3. 默认通常会要求新用户验证邮箱。这样注册后，需要打开邮箱里的确认链接，再回到网站登录。
4. 如果只是自己测试，也可以在 Email 设置里关闭邮箱确认；但公开网站建议保留邮箱验证。

### 5. 配置登录跳转网址

先完成下面 GitHub Pages 部署，拿到网站网址后再回来设置：

1. Supabase 控制台打开 **Authentication → URL Configuration**。
2. 把 **Site URL** 设置成你实际的 GitHub Pages 网址。
3. 在 **Redirect URLs** 里添加同一个网址。
4. 保存设置。

Supabase 官方说明：https://supabase.com/docs/guides/auth/redirect-urls

---

## 第二部分：上传到 GitHub 并发布网页

### 1. 创建 GitHub 仓库

1. 打开 https://github.com/ 并登录。
2. 点击右上角 `+`，选择 **New repository**。
3. 仓库名填写 `vanished-day`（也可以起别的英文名）。
4. 如果使用 GitHub Free，建议选择 **Public**。
5. 勾选添加 README（可选），点击 **Create repository**。

提醒：GitHub Pages 网站是公开可访问的。仓库即使设为私有，发布出来的网站也可能是公开的；不要上传个人隐私、密码或私密文件。

### 2. 上传网页文件

1. 进入刚创建的仓库主页。
2. 点击 **Add file → Upload files**。
3. 把 `index.html` 和 `.nojekyll` 上传进去。`README-部署说明.md` 也可以一起上传。
4. 等待文件出现在列表中，点击 **Commit changes** 保存。

注意：必须上传名为 `index.html` 的网页文件，并放在仓库最外层（根目录），不要只上传 ZIP 压缩包。

### 3. 打开 GitHub Pages

1. 在仓库页面点击 **Settings**。
2. 左侧找到 **Pages**。
3. 在 **Build and deployment** 的 **Source** 里选择 **Deploy from a branch**。
4. Branch 选择 `main`，文件夹选择 `/(root)`。
5. 点击 **Save**。
6. 等待部署完成。回到 Pages 页面，点击 **Visit site** 获取网站地址。

官方说明：https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site

### 4. 把网站网址加回 Supabase

将上一步的真实网址粘贴到 Supabase 的 Site URL 和 Redirect URLs 中。保存后，再访问网站测试注册和登录。

---

## 第三部分：上线前测试清单

请按顺序检查：

- [ ] 打开网站时，先看到登录/注册入口。
- [ ] 能注册邮箱账号；如果开启邮箱验证，能收到确认邮件。
- [ ] 能成功登录并进入论坛首页。
- [ ] 能打开不同版块、搜索帖子、查看全文。
- [ ] 能发布帖子并在“最新”中看到。
- [ ] 能打开帖子并添加回复。
- [ ] 点赞、收藏后刷新网页，状态仍保留。
- [ ] 调查笔记、推理进度刷新后仍保留。
- [ ] 退出登录后，其他账号看不到此账号在本浏览器保存的自定义帖子与进度。

## 后续怎么更新网页

每次要改网页：

1. 在 GitHub 仓库里打开 `index.html`。
2. 点击铅笔图标（Edit），或在电脑上改完后重新上传。
3. 保存/Commit changes。
4. 等 GitHub Pages 自动重新发布，刷新网站查看。

如果你只改了网页代码，浏览器里保存的本地帖子通常不会被代码更新覆盖；但大幅调整数据结构时仍建议先导出/备份重要内容。

## 常见问题

**登录按钮显示“登录服务尚未配置”**
- 检查 `SUPABASE_URL` 和 `SUPABASE_ANON_KEY` 是否填入了正确的值。
- 确保只替换引号内部内容，保存后重新上传 `index.html`。

**注册后没法直接进入论坛**
- 很可能需要先打开邮箱里的验证链接。
- 检查 Supabase 的 Site URL / Redirect URLs 是否已经填入实际 GitHub Pages 网址。

**为什么我换电脑后看不到发过的帖子？**
- 这是本项目按你的要求采用的“本地个人数据”方案。帖子和进度存在原浏览器里，不会跟着账号同步。

**为什么其他访客看不到我发的帖子？**
- 这是预期行为：此版本不是共享社区。若将来想让所有访客互相看见帖子和回帖，需要把帖子与回复改为存储在数据库，并配置数据库权限与反垃圾措施。
