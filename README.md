# 撸货进销存（PWA 部署包）

手机、电脑都能用的撸货进销存记账系统，可安装到手机主屏幕、支持离线使用。

## 目录结构

```
luhuo-pwa/
├── index.html            # 应用本体（含全部界面与逻辑）
├── manifest.json         # PWA 清单（应用名、图标、全屏设置）
├── sw.js                 # Service Worker（离线缓存）
├── icon-192.png          # 应用图标
├── icon-512.png          # 应用图标（含 maskable）
└── apple-touch-icon.png  # iOS 主屏幕图标
```

## 一、部署到 GitHub Pages（免费，推荐）

1. 新建一个 GitHub 仓库（公开或私有均可，Pages 公开才免费）；
2. 把 `luhuo-pwa` 文件夹里的**全部文件**上传到仓库根目录；
3. 仓库 → **Settings → Pages** → Source 选 `main` 分支 / 根目录，保存；
4. 等一两分钟，得到网址，形如：`https://你的用户名.github.io/仓库名/`；
5. 电脑直接访问该网址；手机打开该网址，点「添加到主屏幕」即可当 App 用。

> 注意：仓库名会影响网址路径，但本包内所有引用都是**相对路径**，放根目录即可，无需改动。

## 二、部署到任意静态服务器

把 `luhuo-pwa` 里所有文件放到任意静态服务器目录（Nginx / 宝塔 / Vercel / Netlify / Cloudflare Pages 等）即可。只要通过 **HTTPS** 访问，就能安装 + 离线。

## 三、本地电脑直接使用（不部署）

也可以直接双击 `index.html` 在浏览器打开（此时 Service Worker 不生效，但功能完整可用）。

## 四、云端自动同步（多设备，可选）

想让手机和电脑数据**自动同步**，可以用 Supabase（免费）：

1. 到 [supabase.com](https://supabase.com) 注册并新建一个项目；
2. 进入 **SQL Editor**，执行下面这段建表 SQL：

```sql
create table if not exists luhuo_docs (
  id text primary key,
  content jsonb not null,
  updated_at timestamptz not null default now()
);
alter table luhuo_docs enable row level security;
create policy "public_all" on luhuo_docs for all using (true) with check (true);
```

3. 到 **Settings → API** 复制 **Project URL** 和 **anon public key**；
4. 在应用「设置 → 云端同步」里填这两个值，点「保存并连接」；
5. 手机和电脑都填**同一个项目**，之后任意一端记账，另一端会自动同步。

> 同步是「最后写入生效」；数据仍会保存在各设备本地，断网也能用，联网后自动补同步。

## 说明

- 数据默认保存在**本机浏览器 localStorage**；启用云端同步后同时会上传到 Supabase；
- 未启用云端同步时，手机与电脑之间需要手动「导出/导入」；
- 清除浏览器缓存前请先导出备份。
