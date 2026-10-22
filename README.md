# Tree of Moments legal & support site / 一树时光 法律与支持网站

Static site published with GitHub Pages from the public repository
`adamqiu1001-cpu/tree-of-moments-legal` (branch `main`, folder `/ (root)`).

本目录是一个纯静态网站，通过公开仓库 `adamqiu1001-cpu/tree-of-moments-legal` 的 GitHub Pages（`main` 分支 / 根目录）发布。

## Files / 文件

| Path | Purpose / 用途 |
| --- | --- |
| `index.html` | Landing page linking all six documents / 首页，链接全部 6 个页面 |
| `styles.css` | Shared stylesheet (light, no external fonts or scripts) / 共用样式 |
| `en/privacy.html`, `zh/privacy.html` | Privacy Policy / 隐私政策（App Store Connect「隐私政策网址」） |
| `en/terms.html`, `zh/terms.html` | Terms of Use (Apple Standard EULA + product terms) / 使用条款 |
| `en/support.html`, `zh/support.html` | Support & FAQ / 支持与常见问题（App Store Connect「技术支持网址」） |

No JavaScript, no analytics, no cookies, no external requests. Keep it that way so the site itself does not need a privacy notice of its own.
网站不含 JavaScript、统计、Cookie 或外部请求，请保持这一点。

## Publish / 发布步骤

```bash
# 1. Clone the Pages repo next to this project (once) / 首次克隆 Pages 仓库
git clone https://github.com/adamqiu1001-cpu/tree-of-moments-legal.git ~/tree-of-moments-legal

# 2. Copy the CONTENTS of legal-site/ to the repo ROOT (not into a subfolder)
#    将 legal-site/ 的「内容」复制到仓库根目录（不要多套一层文件夹）
rsync -av --exclude README.md /path/to/Newtree/legal-site/ ~/tree-of-moments-legal/

# 3. Review and push / 检查后推送
cd ~/tree-of-moments-legal
git status && git diff --stat
git add -A
git commit -m "Legal & support pages for v1.0 (effective 2026-10-21)"
git push origin main
```

Notes / 说明：

- Existing URLs must keep working: `en/privacy.html`, `en/terms.html`, `zh/privacy.html`, `zh/terms.html` are already referenced by earlier builds and drafts. Do not rename or move them. 已有的 4 个 URL 不得改名或移动。
- `README.md` is excluded above so the repo can keep its own README; copy it too if you prefer.
- If the Pages repo contains a `.nojekyll` file, keep it. 若仓库已有 `.nojekyll`，请保留。
- GitHub Pages usually updates within 1-2 minutes; check the repo's **Actions** tab for the "pages build and deployment" run.

## Verify / 验证

All six URLs (plus the index) must return HTTP 200:

```bash
BASE=https://adamqiu1001-cpu.github.io/tree-of-moments-legal
for p in "" en/privacy.html en/terms.html en/support.html zh/privacy.html zh/terms.html zh/support.html; do
  printf '%-18s ' "${p:-index}"; curl -s -o /dev/null -w '%{http_code}\n' "$BASE/$p"
done
```

Then open each page on an iPhone (Safari) and check: effective date 2026-10-21, no mention of subscriptions, ads or QWeather, links between pages work, and the Apple Weather attribution link opens.
然后在 iPhone Safari 中逐页检查：生效日期为 2026-10-21；无订阅/广告/和风天气字样；页面间链接可用；Apple 天气归属链接可打开。

Optional local check of HTML well-formedness / 本地校验（可选）：

```bash
python3 -I - <<'PY'
from html.parser import HTMLParser
import pathlib
VOID = {'meta', 'link', 'br', 'img', 'hr', 'input'}
for f in sorted(pathlib.Path('legal-site').rglob('*.html')):
    stack = []
    class P(HTMLParser):
        def handle_starttag(self, t, a):
            if t not in VOID: stack.append(t)
        def handle_endtag(self, t):
            if t in VOID: return
            assert stack and stack[-1] == t, f'{f}: unexpected </{t}> at {self.getpos()}'
            stack.pop()
    P().feed(f.read_text(encoding='utf-8'))
    assert not stack, f'{f}: unclosed {stack}'
    print('OK', f)
PY
```

## App Store Connect URLs / 填写到 App Store Connect 的网址

| Field | en-US | zh-Hans |
| --- | --- | --- |
| Privacy Policy URL | `https://adamqiu1001-cpu.github.io/tree-of-moments-legal/en/privacy.html` | `https://adamqiu1001-cpu.github.io/tree-of-moments-legal/zh/privacy.html` |
| Support URL | `https://adamqiu1001-cpu.github.io/tree-of-moments-legal/en/support.html` | `https://adamqiu1001-cpu.github.io/tree-of-moments-legal/zh/support.html` |
| Marketing URL (optional) | `https://adamqiu1001-cpu.github.io/tree-of-moments-legal/` | same / 同左 |
| License Agreement | Apple Standard EULA (leave default) | 同左 |

## Updating later / 之后更新

When the app's data practices change (new framework, new permission, any SDK), update **both** privacy pages, bump the effective date, update the App Privacy answers in App Store Connect and `PrivacyInfo.xcprivacy` in the same release.
数据处理方式变化时，须在同一版本中同时更新中英文隐私政策（含生效日期）、App Store Connect 隐私问卷和 `PrivacyInfo.xcprivacy`。
