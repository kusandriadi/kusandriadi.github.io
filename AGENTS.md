# AGENTS.md — kusandriadi.github.io

Panduan untuk AI coding agents (Claude Code, Cursor, Codex, dll.) yang membuka repository ini.

> **⚠️ BACA INI LEBIH DULU — REPO INI ADALAH BUILD ARTIFACT, BUKAN SOURCE.**
>
> Semua file di repository ini di-generate otomatis oleh Hugo dari repo source [`hugo-kusandriadi`](../hugo-kusandriadi/). Jangan edit langsung — perubahan akan tertimpa pada build berikutnya.

---

## 1. Ringkasan Repo

- **Nama:** `kusandriadi.github.io`
- **Tipe:** GitHub Pages static site (build output)
- **URL produksi:** https://kusandriadi.com (custom domain via `CNAME`)
- **Default GitHub Pages URL:** https://kusandriadi.github.io (redirected ke kusandriadi.com)
- **Branch yang di-serve:** `master`
- **Source repo:** `kusandriadi/hugo-kusandriadi` (private/personal — sibling directory di filesystem lokal user)
- **Generator:** Hugo `0.161.1` extended
- **Deploy mechanism:** GitHub Actions di repo source push commit ke sini via `peaceiris/actions-gh-pages@v4`

Author commit di branch ini secara normal adalah `github-actions[bot]` dengan pesan `Deploy from hugo-kusandriadi @ <sha>`.

---

## 2. Struktur Direktori

```
kusandriadi.github.io/
├── .nojekyll              # Beritahu GH Pages: skip Jekyll processing (file mulai dengan _ tetap di-serve)
├── CNAME                  # "kusandriadi.com" — custom domain GH Pages
├── favicon.png
├── robots.txt             # Allow all + sitemap pointer
├── sitemap.xml            # Generated Hugo sitemap
├── index.html             # Homepage (page 1)
├── index.xml              # RSS feed
├── about/                 # /about/ page
├── posts/                 # 24+ artikel — masing-masing folder dengan index.html + (kadang) index.xml
├── tags/                  # Halaman taxonomy tag
├── page/                  # Paginator: /page/2/, /page/3/, ...
├── css/                   # Compiled CSS (style.css, blog.css, fonts/)
├── js/                    # JavaScript assets (saat ini cuma .gitkeep)
├── images/                # Asset gambar
└── webfonts/              # Self-hosted PT Serif + Source Code Pro
```

Semua HTML di sini sudah di-minify oleh Hugo (`hugo --minify --gc`).

---

## 3. Cara Kerja Deploy (penting)

1. User commit ke `hugo-kusandriadi` (branch `master`/`main`).
2. GitHub Actions workflow `Deploy Hugo to kusandriadi.github.io` jalan.
3. Workflow checkout source, setup Hugo 0.161.1 extended, run `hugo --minify --gc`.
4. Hasil di `public/` di-push ke repo INI (`kusandriadi/kusandriadi.github.io`) branch `master` via `peaceiris/actions-gh-pages@v4`.
5. Workflow ini juga jalan via cron `15 */6 * * *` untuk refresh sidebar "Latest AI Articles" yang fetch remote JSON saat build-time.
6. GitHub Pages otomatis serve `master` branch dengan custom domain dari `CNAME`.

**Workflow ini menggunakan `deploy_key` (SSH) dengan secret `ACTIONS_DEPLOY_KEY` di repo source.** Public key terpasang sebagai deploy key di repo INI.

---

## 4. Untuk Agent: Do's & Don'ts

**Don't (paling penting):**
- ❌ **JANGAN edit `.html`, `.xml`, `.css`, atau apa pun di repo ini untuk memperbaiki tampilan/konten.** Edit akan ditimpa di deploy berikutnya (paling lama 6 jam karena cron).
- ❌ Jangan hapus `.nojekyll` — tanpa file ini, GH Pages akan jalankan Jekyll dan files yang mulai dengan `_` (mis. `_default/`) tidak akan di-serve.
- ❌ Jangan ganti isi `CNAME` kecuali user explicit ingin pindah domain. Kalau hilang/salah, custom domain GH Pages akan reset dan butuh konfigurasi ulang di Settings → Pages.
- ❌ Jangan commit langsung ke `master` repo ini — bot deploy harapkan branch dalam state yang konsisten. Manual commit bisa tertimpa atau bikin merge conflict di action.
- ❌ Jangan jalankan Hugo di sini — tidak ada source markdown, hanya output.
- ❌ Jangan tambahkan file source (`.md` content) ke repo ini.

**Do:**
- ✅ Kalau user minta perubahan tampilan/konten: arahkan ke repo source `hugo-kusandriadi` (sibling directory: `../hugo-kusandriadi/`).
- ✅ Kalau user minta "rebuild" manual tanpa nunggu cron, opsi:
  - Trigger `workflow_dispatch` di GitHub UI (repo source → Actions → Deploy Hugo → Run workflow).
  - Atau push commit kosong/trivial ke `hugo-kusandriadi`.
- ✅ Bantu read-only inspection (mis. user nanya "apa isi sitemap saat ini" atau "berapa post di-published") — itu boleh.
- ✅ Kalau benar-benar perlu hotfix manual (mis. takedown urgent satu post sebelum source repo bisa di-update), lakukan di branch terpisah + dokumentasikan, dan ingatkan user bahwa fix ini akan hilang di deploy berikutnya kalau source tidak di-update.

---

## 5. Verifikasi Sebelum "Memperbaiki"

Sebelum mengubah apa pun di sini, agent harus tanya/cek:
1. Apakah perubahan yang diminta user lebih tepat dilakukan di repo source? (Jawaban hampir selalu: **ya**.)
2. Apakah user tahu repo ini auto-generated?
3. Kalau memang harus manual: kapan deploy berikutnya akan jalan (cron / push baru di source)? User harus aware bahwa edit ini ephemeral.

---

## 6. File yang Bukan Auto-Generated

Beberapa file di repo ini bukan dihasilkan dari `hugo` build, dan **boleh disentuh manual** (atau dipertahankan saat deploy):
- `CNAME` — di-set ulang setiap deploy oleh action karena di workflow ada `cname: kusandriadi.com`. Jadi sebenarnya auto-managed juga.
- `.nojekyll` — historically committed manual, tapi `peaceiris/actions-gh-pages` biasanya menambahkannya otomatis. Aman dibiarkan.
- `AGENTS.md` (file ini) — **dipertahankan secara eksplisit** oleh workflow source repo via step `Preserve AGENTS.md from publish branch`: workflow `curl` file ini dari branch `master` repo ini lalu copy ke `./public/AGENTS.md` sebelum `peaceiris/actions-gh-pages` jalan, sehingga tidak ikut ter-clean saat replace. **Update file ini langsung di branch `master` repo ini** — perubahan akan persist di deploy berikutnya. Kalau file dihapus di repo ini, deploy berikutnya akan jalan tanpa preservation (step `continue-on-error: true`) dan AGENTS.md akan hilang permanen kecuali di-restore manual.

Selebihnya: anggap semua file di sini disposable dan akan di-replace.

---

## 7. Troubleshooting Cepat

| Gejala | Diagnosa |
|---|---|
| Perubahan di source repo belum muncul di kusandriadi.com | Cek tab Actions di repo source. Workflow gagal → fix di source. Workflow sukses tapi belum live → tunggu ~1 menit untuk GH Pages CDN purge |
| Custom domain tidak resolve | Cek `CNAME` masih berisi `kusandriadi.com`. Cek DNS record `kusandriadi.com` → `kusandriadi.github.io`. Cek Settings → Pages di repo ini |
| Halaman `/posts/foo/` 404 | (a) Title slug berubah di source → permalink baru. (b) Post belum di-deploy. (c) `draft: true` di source |
| Sidebar AI feed kosong | Build-time fetch ke `kusandriadi.com/ai-blog/data/posts.json` gagal saat build terakhir. Trigger rebuild |
| Theme rusak / CSS hilang | `.nojekyll` terhapus? Atau path CSS di-rewrite salah karena `baseURL` di source `config.toml` salah |

---

## 8. Konteks Cepat Saat Onboarding

Kalau agent baru pertama kali ke repo ini:
1. Lihat `CNAME` → domain produksi
2. Lihat `index.html` di header `<meta name=generator content="Hugo X.Y.Z">` → konfirmasi generator
3. Sibling directory `../hugo-kusandriadi/` = source repo. Buka `AGENTS.md` di sana untuk informasi development workflow.

Singkatnya: **kalau task ada hubungan dengan blog kusandriadi.com, 99% pekerjaan ada di repo source, bukan di sini.**
