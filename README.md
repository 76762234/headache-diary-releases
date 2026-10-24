# 头痛日记 · 发布产物

这个仓库**只放发布产物**，没有源码。

| 文件 | 说明 |
|---|---|
| `update-manifest.json` | 更新清单：最新版本号、更新说明、APK 下载地址、大小、sha256 |
| `headache-diary-<版本>.apk` | 各版本的 Android 安装包（归档副本） |

**源码仓库是私有的**（`76762234/headache-diary`），不在这里，也不从这里发布。
本仓库只承担一件事：让任何一台手机都能**匿名**读到「最新版本是多少、去哪下载」。

---

## 下载最新版

- 安装包：看下面的 [Releases](https://github.com/76762234/headache-diary-releases/releases)（推荐，走 CDN）
- 归档副本：本仓库根目录里的 `headache-diary-<版本>.apk`

> 两个文件是同一份字节。清单里的 `sha256` 对应两者，可自行校验。

## 给 App 用的更新清单地址

```
https://raw.githubusercontent.com/76762234/headache-diary-releases/main/update-manifest.json
```

App 的「设置 → 版本与更新 → 更新地址」填这个即可（App 里已预填，一般不用改）。

清单长这样：

```json
{
  "app": "headache-diary",
  "version": "1.4.1",
  "versionCode": 10401,
  "publishedAt": 1792800000000,
  "notes": "这次改了什么",
  "downloadUrl": "https://github.com/76762234/headache-diary-releases/releases/download/v1.4.1/headache-diary-1.4.1.apk",
  "sizeBytes": 19546211,
  "sha256": "…",
  "mandatory": false
}
```

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

# 2. 生成清单（downloadUrl 指向本仓库的 Release 目录）
powershell -File scripts\publish-update.ps1 `
  -DownloadBaseUrl "https://github.com/76762234/headache-diary-releases/releases/download/v<版本号>" `
  -ManifestOut "<本仓库目录>/update-manifest.json"

# 3. 把 APK 与清单提交到本仓库，并上传 Release 资产
```

> 建议 APK 以 **Release 资产**形式提供（走 CDN），
> 仓库里留一份归档副本即可 —— 否则每次发版都会给 git 历史增加约 20 MB。
