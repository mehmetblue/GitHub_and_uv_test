# GitHub Çalışma Akışı Checklist (VS Code + uv)

> Amaç: Repoyu klonla → ortamı kur → **feature branch**’te çalış → kalite kontrolleri → commit/push → PR → merge → branch temizliği.

---

## ✅ 1) Repo’yu klonla ve doğru klasörü aç

- [ ] Bilgisayarında yeni bir çalışma klasörü oluştur.
- [ ] VS Code’u bu klasör içinde aç.
- [ ] Terminalde repoyu klonla:

```bash
git clone https://github.com/ErsinOzturk10/AgenticCyberSense.git
```

* [ ] Doğru klon linkini GitHub’dan al:

  * [ ] Repo sayfasında **Code** sekmesine git.
  * [ ] Branch’in **main** olduğundan emin ol (değilse **main** seç).
  * [ ] **Code → HTTPS** linkini kopyala.

* [ ] Klon başarılı olunca VS Code’un sol tarafında repo dosyaları görünür.

* [ ] **Önemli:** `git branch` boş görünüyorsa muhtemelen **yanlış klasörde**sindir.

  * [ ] VS Code’da **File → Open Folder** ile klonlanan proje klasörünü aç:

    * Örn: `.../oluşturduğun_klasör/AgenticCyberSense`
  * [ ] Sol üstte klasör adının proje adıyla aynı olduğunu kontrol et.

---

## ✅ 2) Ortamı kur (uv + venv)

* [ ] Bağımlılıkları senkronize et:

```bash
uv sync
```

* [ ] `.venv/` klasörü oluştuğunu kontrol et.
* [ ] Sanal ortamı aktif et:

**Windows**

```bash
.\.venv\Scripts\activate
```

**macOS / Linux**

```bash
source .venv/bin/activate
```

* [ ] Terminalde başta `(… )` şeklinde venv adı göründü mü kontrol et.
* [ ] Aktif branch’i doğrula:

```bash
git branch
```

Beklenen: `* main`

---

## ✅ 3) Feature branch oluştur (main üzerinde çalışma)

* [ ] **main üzerinde geliştirme yapma.** Her task için **main’den yeni branch** oluştur.
* [ ] Örnek: RAG taskı için branch oluştur:

```bash
git checkout -b feat/rag
```

* [ ] Kontrol et:

```bash
git branch
```

Beklenen:

* `* feat/rag`
* `  main`

---

## ✅ 4) Kod değişikliklerini takip et (VS Code)

* [ ] VS Code’da **Source Control**’a gir:

  * Kısayol: `Ctrl + Shift + G`
* [ ] **Changes** altında değişen dosyaları gör.
* [ ] Bir dosyaya tıklayınca VS Code diff ekranında:

  * Sol: `main`
  * Sağ: `feat/...`
    farkları gösterir.

---

## ✅ 5) Stage et (push öncesi)

* [ ] Tüm dosyaları stage etmek için:

```bash
git add .
```

* [ ] Tek dosya stage etmek için:

```bash
git add dosya_adi.ext
```

* [ ] Alternatif (VS Code):

  * [ ] **Changes** yanındaki `+` → tüm değişiklikleri stage et
  * [ ] Dosya yanındaki `+` → sadece o dosyayı stage et

---

## ✅ 6) Kalite kontrolleri (push öncesi)

> Not: Bu adımlar, GitHub Actions’taki `build.yml` ile yapılan kontrollerin **lokalde** önceden yakalanmasını sağlar.

* [ ] Stage edilmiş dosyalar için pre-commit kontrollerini çalıştır:

```bash
uvx pre-commit run --all-files
```

* [ ] **Önemli:** Bu komut **sadece stage edilmiş dosyaları** kontrol eder (stage edilmemiş dosyaları kontrol etmez).
* [ ] Bazı hatalar otomatik düzeltilebilir. Sonra tekrar çalıştır:

```bash
uvx pre-commit run --all-files
```

* [ ] `ruff check` ve `ruff format` için `Passed` görmeden devam etme.
* [ ] İstisnai olarak bir uyarıyı yok saymak gerekirse satır sonuna ekle:

```python
# noqa: T201
```

> `T201` yerine terminalde görünen uyarı kodunu yaz. (Takım içinde konuşulup istisnai kullanılmalı.)

* [ ] Tip kontrolü (mypy):

```bash
uv run mypy src --strict
```

Beklenen (hata yoksa): `Success: no issues found ...`

---

## ✅ 7) Commit + Push (Publish / Sync)

* [ ] Commit mesajını kısa ve açıklayıcı yaz.
* [ ] Commit at:

```bash
git commit -m "feat: rag pipeline skeleton"
```

> VS Code UI ile de commit atabilirsin (Source Control’daki kutu + Commit).

* [ ] Branch ilk kez push edilecekse:

```bash
git push -u origin feat/rag
```

> VS Code’da bu genelde **Publish Branch** olarak görünür.
> Daha önce push ettiysen **Sync** görürsün.

---

## ✅ 8) GitHub’da Pull Request aç

* [ ] Repo → **Pull requests** sekmesine git.

* [ ] `Compare & pull request` butonunu kullan.

* [ ] Actions’ta **Build** koşuyor mu kontrol et:

  * Repo → **Actions** → workflow runs

* [ ] Build hatasız tamamlanınca PR’ı oluştur.

* [ ] Sağ üstten **Reviewers** ekle.

* [ ] `Create a pull request` ile PR’ı aç.

* [ ] Reviewer değişiklik isterse:

  * [ ] Localde düzelt → stage → kontroller → commit → push
  * [ ] PR otomatik güncellenir.

* [ ] En az 1 onay sonrası:

  * [ ] `Merge pull request` ile `main`’e merge et
  * [ ] Merge sonrası `Delete branch` ile feature branch’i GitHub’dan sil

---

## ✅ 9) Merge sonrası local main’i güncelle ve yeni task’e geç

* [ ] Localde `main`’e dön:

```bash
git checkout main
```

* [ ] Remote’daki güncel main’i çek:

```bash
git pull origin main
```

* [ ] Yeni task için yeni branch oluştur:

```bash
git checkout -b feat/mcp
```

* [ ] Kontrol:

```bash
git branch
```

Beklenen: `* feat/mcp`

---

## ✅ 10) Merge conflict notu

* [ ] Aynı satırlarda farklı değişiklik yapıldıysa **merge conflict** çıkabilir.
* [ ] Çözüm: İlgili kişiler birlikte karar verir:

  * [ ] Hangi değişiklik kalacak?
  * [ ] İkisi de tutulacak mı?
  * [ ] Test/CI sonuçlarına göre doğrulama yapılır.

```
```


