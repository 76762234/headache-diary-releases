# 头痛日记 · 发布产物

这个仓库**只放发布产物**，没有源码。

| 文件 | 说明 |
|---|---|
| `update-manifest.json` | 更新清单：最新版本号、更新说明、APK 下载地址、**备用下载地址**、大小、sha256 |
| `headache-diary-<版本>.apk` | 各版本的 Android 安装包（归档副本） |

**源码仓库是私有的**（`76762234/headache-diary`），不在这里，也不从这里发布。
本仓库只承担一件事：让任何一台手机都能**匿名**读到「最新版本是多少、去哪下载」。

---

## 下载最新版

- 安装包：看下面的 [Releases](https://github.com/76762234/headache-diary-releases/releases)（推荐，走 CDN）
- 归档副本：本仓库根目录里的 `headache-diary-<版本>.apk`
- 直链：`https://raw.githubusercontent.com/76762234/headache-diary-releases/main/headache-diary-<版本>.apk`

> 三个入口是同一份字节。清单里的 `sha256` 对应它们，可自行校验。

## 给 App 用的更新清单地址

```
https://raw.githubusercontent.com/76762234/headache-diary-releases/main/update-manifest.json
```

App 的「设置 → 版本与更新 → 更新地址」填这个即可（App 里已预填，一般不用改）。

**国内网络提示**：`raw.githubusercontent.com` 在部分国内移动网络下连不上。
App 内置了**兜底地址**（同一个文件的 Gitee 镜像），会自动挨个试 ——
主地址连不上时改用镜像，用哪边由 App 自己判断，你不需要做任何设置。

清单长这样：

```json
{
  "app": "headache-diary",
  "version": "1.4.3",
  "versionCode": 10403,
  "publishedAt": 1792849732029,
  "notes": "更新链路加两道完整性闸门：只允许 https；下载后核对 SHA-256",
  "downloadUrl": "https://gitee.com/wch850412/headache-diary-releases/releases/download/v1.4.3/headache-diary-1.4.3.apk",
  "mirrorDownloadUrls": [
    "https://raw.githubusercontent.com/76762234/headache-diary-releases/main/headache-diary-1.4.3.apk",
    "https://github.com/76762234/headache-diary-releases/releases/download/v1.4.3/headache-diary-1.4.3.apk"
  ],
  "sizeBytes": 19613503,
  "sha256": "20496df0d8847067f92c694c06aea6353df1cca8e8470a9f88da66c816b03520",
  "mandatory": false
}
```

- `downloadUrl` 是**实测最快的那条**（Gitee Release 资产，国内约 2 MB/s）。
  之前用的是 raw 直链，实测只有约 52 KB/s —— 19 MB 要等 6 分钟，
  对手机流量用户等于「下载不了」。换主地址后实测约 10 秒。
- `mirrorDownloadUrls` 是**备用下载地址**（可选字段）：主地址连不上或校验不过时自动换，
  不用用户重试。raw 直链与 GitHub Release 资产都在里面。
- 清单里**只允许 https**：App 端凡是明文 `http` 一律拒绝（清单地址与所有下载地址）。
- 清单里的 `sha256` 会被 App 在**下载完成后实测比对**：对不上就当这个镜像失败、
  自动换下一个镜像，不会把坏包交给系统安装界面。
- `mirrorDownloadUrls` 是**备用下载地址**（可选字段）：写上以后，APK 下载主地址失败会自动换镜像，
  不用用户重试。老版本 App 不认识这个字段时会忽略它，不影响更新。

---

## 这个 App 是什么

**头痛日记** —— 一个纯本地的头痛记录工具：

- 所有记录只存在手机里，**没有账号、没有云端、不上传任何数据**
- 唯一会联网的动作是「检查更新」（读一次清单 + 下载一次安装包）
- 记录内容可一键导出成纯文本或 CSV，拿给医生看
- 备份导出成 JSON，换手机时导入即可

## 关于隐私

这个 App 里**没有任何**密钥、接口凭证、账号或后端服务，
安装包里也不含任何用户数据（用户数据在各自手机沙箱里，与安装包无关）。
发布到这里的只有安装包本身和版本元数据。

## 发布新版本的人看这里

用源码仓库里的脚本（**不要手工编辑清单**，脚本会算 sha256 并自检格式）：

```powershell
# 1. 构建 APK
powershell -File app\android\build-apk.ps1 -Task assembleRelease

# 2. 生成清单
#    DownloadBaseUrl = 主下载地址（直链，无跳转）
#    MirrorBaseUrl   = 备用下载地址（国内镜像 + Release 资产）
powershell -File scripts\publish-update.ps1 `
  -DownloadBaseUrl "https://raw.githubusercontent.com/76762234/headache-diary-releases/main" `
  -MirrorBaseUrl "https://gitee.com/wch850412/headache-diary-releases/releases/download/v<版本号>" `
  -MirrorBaseUrl "https://github.com/76762234/headache-diary-releases/releases/download/v<版本号>" `
  -ManifestOut "<本仓库目录>/update-manifest.json" `
  -Notes "这次改了什么"

# 3. 把 APK 与清单提交到本仓库，并上传 Release 资产
```

> 建议 APK 以 **Release 资产**形式提供（走 CDN），
> 仓库里留一份归档副本即可 —— 否则每次发版都会给 git 历史增加约 20 MB。
