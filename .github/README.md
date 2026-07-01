# GitHub Actions — Microtech Computers CRM

Two workflows are configured in `.github/workflows/`:

| File | Runs on | What it does |
|---|---|---|
| **`build-windows-exe.yml`** | `windows-latest` | Builds `MicrotechCRM.exe`, smoke-tests it, uploads it as an artifact, and attaches it to a GitHub Release when you push a tag. |
| **`ci.yml`** | `ubuntu-latest` | Fast sanity check — installs backend + frontend deps and builds them. Catches syntax/import errors early. |

---

## How to enable them

### 1. Push your code to GitHub

Use the **"Save to Github"** button in the Emergent chat toolbar (bottom-left of your chat input) — it will create/update a repo for you. The `.github/workflows/` folder is included automatically.

> If you'd rather do it manually:
> ```bash
> git remote add origin https://github.com/<you>/microtech-crm.git
> git push -u origin main
> ```

### 2. That's it

The moment your first push lands on `main`, GitHub Actions will start running both workflows. You'll see them under the **Actions** tab of your repo.

---

## Getting your `MicrotechCRM.exe`

### Every push (temporary artifact)

1. Go to **Actions** tab → click the latest `Build MicrotechCRM.exe (Windows)` run.
2. Scroll to **Artifacts** → download `MicrotechCRM-<sha>.zip`.
3. Unzip → double-click `MicrotechCRM.exe`. Done.
4. Artifacts are kept for 30 days.

### Official release (permanent, shareable link)

Tag a commit and push the tag:

```bash
git tag v1.0.0
git push origin v1.0.0
```

The workflow will build the exe, create a **GitHub Release** at `https://github.com/<you>/microtech-crm/releases`, and attach `MicrotechCRM.exe` to it. You can share the direct download link with anyone.

### Manual build without pushing code

Repo → **Actions** tab → **Build MicrotechCRM.exe (Windows)** on the left → click **Run workflow** button in the top-right of the run list → pick branch → Run. Done in ~5-8 minutes.

---

## What the Windows workflow actually does

```
checkout code
  → setup Python 3.11
  → setup Node 20
  → yarn install + yarn build   (with REACT_APP_BACKEND_URL empty for relative /api)
  → pip install requirements + pyinstaller
  → pyinstaller MicrotechCRM.spec
  → START the exe + curl /api/health + /api/auth/login → verify it works
  → upload dist\MicrotechCRM.exe as artifact
  → if pushed a tag: also attach it to the GitHub Release
```

It's literally an automated version of running `build_exe.bat` on your own Windows PC — but running on GitHub's free Windows runners.

---

## Cost

For **public** repos: **completely free**, unlimited minutes.
For **private** repos: 2,000 free Windows-minutes/month (Windows runners cost 2× minutes). A single build ≈ 6–10 minutes, so ~30–50 free builds/month.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Workflow doesn't appear | Make sure `.github/workflows/` was pushed. Check under repo → Actions → *"I understand my workflows, go ahead and enable them"*. |
| `pyinstaller` fails on Windows runner | Check the run logs — usually a missing hidden import. Add it to `hiddenimports` in `MicrotechCRM.spec`. |
| `yarn build` warning-as-error | Already handled — workflow sets `CI=false`. |
| Release step 403 | GitHub token permissions. Go to repo → Settings → Actions → General → Workflow permissions → *Read and write permissions*. |
| Windows Defender flags the .exe | Common with PyInstaller onefile builds. Either add an exclusion, or code-sign the exe (needs a paid cert). |

---

## Extending: also build Linux/macOS

You can add more matrix entries in `build-windows-exe.yml`:

```yaml
strategy:
  matrix:
    os: [windows-latest, ubuntu-latest, macos-latest]
runs-on: ${{ matrix.os }}
```

PyInstaller will then produce a Linux binary + a macOS `.app` alongside the Windows `.exe`.
