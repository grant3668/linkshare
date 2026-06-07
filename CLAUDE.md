# CLAUDE.md — linkshare（ESP32-S3 固件网页安装）

本仓库用于**网页一键安装 ESP32-S3 固件**(ESP Web Tools + GitHub Pages)。
维护本仓库时**必须遵守以下规则**。

## 1. 分支策略
- **`main`**：保持**空**(只一行 README 或空提交)。是默认分支,别人打开仓库看不到内容。
- **`dev`**：内容分支,**GitHub Pages 从 `dev` 分支 `/(root)` 发布**。
- 所有内容只放 `dev`。

## 2. dev 只放这三样,其它一律不放
```
index.html        ← 安装页（见第 4 条：只有两个按钮）
manifest.json     ← ESP Web Tools 清单，指向 firmware 里的 bin，offset 0，chipFamily ESP32-S3
firmware/<bin>    ← 合并好的单文件固件（刷到 0x0）
```
不要放源码、文档、脚本等任何其它东西(本 CLAUDE.md 除外)。

## 3. 历史策略：单提交 + 强推，不保留历史
- **`dev` 永远只有 1 个 commit**;每次更新用 `git commit --amend --no-edit` 改写那一个 commit。
- **每次都用 `git push -f origin dev` 强推**,丢弃历史(public 仓,不暴露过程)。

## 4. 网页只保留两个按钮
`index.html` 只包含：
- **「连接 / 烧录」**按钮(`<esp-web-install-button>`)
- **「读取 ID」**按钮(WebSerial 发 `serial` 命令,解析 `esp32=<12hex>` 显示 tester_id)

不加任何说明文字/步骤/警告。仅桌面版 Chrome/Edge 支持。

## 5. 更新固件的标准流程
```bash
# 在 linkshare/（dev 分支）
cp 新固件.bin firmware/<bin>          # 换固件（必要时同步改 manifest.json 的 version/path）
git add -A
git commit --amend --no-edit          # 保持单提交
git push -f origin dev                 # 强推，无历史

# 回 tester 根仓 bump 子模块指针（linkshare 是 tester 的子模块，跟踪 dev）
cd .. && git add linkshare && git commit -m "bump linkshare" && git push origin main
```

## 6. 与 tester 的关系
- linkshare 是 **tester 仓的子模块**,`.gitmodules` 里 `branch = dev`。
- 固件源码在 `pixelhid/firmware/esp32_hid_bridge/`;发布用的合并 bin 由那边 build 出来后拷到这里的 `firmware/`。

## 7. GitHub Pages
- 地址:`https://grant3668.github.io/linkshare/`
- Source: Deploy from a branch → `dev` /(root)。
- HTTPS 自带(WebSerial 必需)。
