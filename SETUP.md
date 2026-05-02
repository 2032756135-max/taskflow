# TaskFlow 部署完整指南

> 数据存储在 GitHub · 每天早上9点邮件提醒 · 换电脑不丢数据

---

## 一、上传项目到 GitHub

### 前提：本地安装 Git
如果还没装，去 https://git-scm.com 下载安装。

### 步骤（在终端/命令行执行）

```bash
# 1. 在 GitHub 上创建私有仓库（名称：taskflow）
#    打开：https://github.com/new
#    Repository name: taskflow
#    选择 Private
#    不要勾选任何初始化选项，直接 Create

# 2. 进入项目文件夹（你下载 taskflow.html 等文件的地方）
cd /path/to/taskflow文件夹

# 3. 初始化 Git
git init

# 4. 添加所有文件
git add .

# 5. 第一次提交
git commit -m "feat: initial TaskFlow setup"

# 6. 绑定远程仓库（注意替换成你的仓库地址）
git remote add origin git@github.com:2032756135-max/taskflow.git

# 7. 推送
git push -u origin main
```

> ⚠️ 如果推送时提示 SSH Key 问题，改用 HTTPS：
> ```bash
> git remote set-url origin https://github.com/2032756135-max/taskflow.git
> git push -u origin main
> ```

---

## 二、开启 GitHub Pages（让网站可以直接访问）

1. 打开仓库页面 → **Settings** → **Pages**
2. Source 选择 **Deploy from a branch**
3. Branch 选 **main**，文件夹选 **/ (root)**
4. 点 **Save**
5. 等待1~2分钟，访问地址：

```
https://2032756135-max.github.io/taskflow/taskflow.html
```

> 收藏这个地址，在任何浏览器、任何电脑都可以打开！

---

## 三、创建 GitHub Personal Access Token（PAT）

网站需要 Token 才能把任务数据写入 GitHub。

1. 打开：https://github.com/settings/tokens/new
2. 填写：
   - **Note**：TaskFlow
   - **Expiration**：`No expiration`（永不过期）
   - **权限**：勾选 `repo`（整行全选）
3. 点 **Generate token**
4. ⚠️ **立即复制 Token！只显示一次！**（格式：`ghp_xxxxx...`）
5. 打开网站 → 点左下角 **⚙ GitHub 设置** → 粘贴 Token → 点连接

---

## 四、配置每日邮件提醒

### 4.1 获取 Gmail 应用专用密码

> 普通 Gmail 密码不行，必须用应用专用密码

1. 打开 Gmail → 右上角头像 → **管理 Google 帐号**
2. **安全性** → 找到 **两步验证**，确保已开启
3. 搜索 **应用专用密码** 或直接访问：https://myaccount.google.com/apppasswords
4. 应用名称填 `TaskFlow` → 点生成
5. 复制16位密码（格式：`xxxx xxxx xxxx xxxx`）

### 4.2 在 GitHub 添加 Secrets

1. 打开仓库 → **Settings** → **Secrets and variables** → **Actions**
2. 点 **New repository secret**，添加两个：

| Name | Value |
|------|-------|
| `GMAIL_USER` | `zxyy40011@gmail.com` |
| `GMAIL_APP_PASSWORD` | `刚才复制的16位密码` |

### 4.3 测试邮件发送

1. 打开仓库 → **Actions** → **📋 TaskFlow 每日任务提醒**
2. 点 **Run workflow** → 确认运行
3. 等30秒，查看邮箱是否收到邮件

---

## 五、日常使用

| 场景 | 操作 |
|------|------|
| 换了新电脑 | 打开网站地址 → 点 ⚙ 设置 → 粘贴 Token |
| Token 过期/失效 | 重新创建 Token → 点 ⚙ 设置 → 更新 |
| 手动触发提醒 | GitHub → Actions → 手动 Run workflow |
| 修改提醒时间 | 编辑 `.github/workflows/reminder.yml` 中的 cron 表达式 |
| 更新网站功能 | 修改 `taskflow.html` → `git add . && git commit -m "update" && git push` |

### Cron 时间参考
```
'0 1 * * *'   →  UTC 01:00 = 北京时间 09:00（现在的配置）
'0 0 * * *'   →  北京时间 08:00
'0 23 * * *'  →  北京时间 07:00（次日）
'0 2 * * 1'   →  每周一 北京时间 10:00
```

---

## 六、后续迁移到 MySQL（有服务器后）

当你有了服务器，只需：
1. 搭建一个简单的 Node.js / Python API
2. 提供 `/tasks` 的 GET / PUT 接口
3. 修改 `taskflow.html` 中的 `loadFromGitHub()` 和 `pushToGitHub()` 函数，替换为调用你的 API
4. Token 换成你服务器的 API Key

---

## 文件说明

```
taskflow/
├── taskflow.html              # 网站主文件（在浏览器中打开使用）
├── tasks.json                 # 任务数据（由网站自动维护，勿手动编辑）
├── .github/
│   └── workflows/
│       └── reminder.yml       # 每日邮件提醒自动化配置
└── SETUP.md                   # 本文档
```
