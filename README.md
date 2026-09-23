# 婚礼请柬 · 站点 2 / 站点 3 发布说明

## 架构（两站完全独立，改一个不影响另一个）

| | 站点 2：回门宴 / 九江 | 站点 3：婚宴 / 黄陂 |
|---|---|---|
| 访问地址 | https://lzz66.github.io/wedding-invitation2/ | https://lzz66.github.io/wedding-invitation3/ |
| GitHub 仓库 | `lzz66/wedding-invitation2` | `lzz66/wedding-invitation3` |
| 本地目录 | `C:\Users\Administrator\Downloads\wedding3\wedding-repo\` | `C:\Users\Administrator\Downloads\wedding3\wedding-repo3\` |
| 当前内容 | 回门宴、十里幸福茂艺术中心、江西九江 | 婚宴、黄陂合禧宴会艺术中心、武汉市黄陂区 |

> 站点 2 已回滚为原始“回门宴 / 九江”配置；刚才的“婚宴 / 黄陂”修改只保留在站点 3。

## 核心规矩：永久缓存时代，换内容必须换文件名

图片和音乐当前采用“同源优先、jsDelivr CDN 兜底”的方式；CDN URL 固定到历史提交哈希。
**哈希地址会被 CDN 长期缓存**——这是打开速度快的根源，但代价是：

1. **替换图片/音乐时必须用新文件名**（如 `art_s78.jpg` → `art_s78b.jpg` → `art_s78c.jpg`），否则访客可能一直看到旧文件。
2. 同名文件改了内容，purge 缓存也未必可靠——所以直接用新文件名，新名字没有历史缓存，推上去就是新的。
3. URL 里的哈希**不需要是最新提交**，只要那个提交里包含这个文件就行，平时不要动它。
4. 只有新增了一批文件、想统一重新固定时，才把 URL 哈希整体改成包含这些文件的某个提交。
5. 修改站点 3 的页面元信息时，必须保持 `og:image`、`og:url`、CDN 地址都指向 `wedding-invitation3`，不要混用站点 2 地址。

## 站点 2 修改发布流程

站点 2 可能有人在 GitHub 网页上直接改，**动手前先同步**：

```bash
cd C:\Users\Administrator\Downloads\wedding3\wedding-repo
git pull --rebase mirror2 main
# 改完后
git add -A && git commit -m "说明" && git push mirror2 main
# 等约 40 秒 GitHub Pages 部署，验证 https://lzz66.github.io/wedding-invitation2/
```

## 站点 3 修改发布流程

站点 3 可能有人在 GitHub 网页上直接改，**动手前先同步**：

```bash
cd C:\Users\Administrator\Downloads\wedding3\wedding-repo3
git pull --rebase origin main
# 改完后
git add -A && git commit -m "说明" && git push origin main
# 等约 40 秒 GitHub Pages 部署，验证 https://lzz66.github.io/wedding-invitation3/
```

## 图片制作惯例

- 海报规格：900×1500（3:5）、q82 渐进式 JPEG，约 150–370 KB/张。
- 生成图左下角的「AI生成」水印必须抹掉：从本目录执行 `python ..\patch_watermark.py`（纸纹采样修补，改图后记得先裁剪缩放再抹）。
- 版式：手机端通栏叠放，桌面端并排；封面 100vw×100vh。

## 验证工具

- 页面内容：分别访问上述两个线上地址，必要时强制刷新。
- CDN 指纹：`md5sum` 对比 `curl -sL <图片URL>` 与本地文件。

## 音乐

- 走 jsDelivr 哈希固定地址 + 12 秒超时回退同源；播放中绝不切源（防卡顿重启）。
- 更换音乐：新文件名或更新 `<source>` 里的哈希；码率 80–128k、体积 <1.5 MB 为宜。
