# PT Melbourne KB — Simple Calculator

Halaman live: **https://prod-at22.github.io/melbourne-kb/**

Repo ini ada dua fail yang penting:

| Fail | Apa dia | Boleh PO edit? |
|---|---|---|
| `calc-config.json` | **semua nombor dan itinerary kalkulator** — harga tier, kadar peak, kadar transport ikut kawasan, blok itinerary, add-on, surcaj | **Ya** — edit terus di sini |
| `index.html` | halaman KB penuh + enjin kalkulator | Tidak — perlu bina semula |

Halaman membaca `calc-config.json` **setiap kali dibuka**. Jadi ubah nombor dalam fail
itu, commit, refresh halaman — terus naik. Tak perlu bina semula `index.html`.

---

## Cara edit

1. Klik `calc-config.json` di atas.
2. Klik ikon pensel (**Edit this file**).
3. Ubah nombor yang perlu.
4. Scroll bawah → **Commit changes**.
5. Tunggu ~30 saat, refresh halaman KB.

### Jaring keselamatan

Kalau JSON tersalah tulis (koma tertinggal, kurungan tak tutup) atau bentuknya salah,
halaman **tidak** rosak. Ia guna balik config lama yang terbenam dalam `index.html`
dan papar notis merah di atas tab Simple Calculator. Kalau notis itu keluar, maksudnya
**suntingan tak terpakai** — betulkan JSON dan commit semula.

---

## ⚠ Kadar yang PERLU PO sahkan

Nombor di bawah **tiada sumber** dalam katalog PT Melbourne Basic/Standard 2026 mahupun
dalam `PT_MEL_RD_reformatted.xlsx`. Melbourne tiada ProdReq, jadi tiada dokumen ketiga
untuk dirujuk. Ia dikekalkan daripada versi kalkulator sebelum ini — **sahkan atau
betulkan** sebelum dipakai untuk quote sebenar.

| Kadar | Kunci dalam `calc-config.json` | Nilai sekarang |
|---|---|---|
| Airport transfer pick up tambahan | `airPick` | 1400 / 1700 / 2150 (2–3 / 4–6 / 7–9 pax) |
| Airport transfer drop off tambahan | `airDrop` | 1300 / 1600 / 2050 |
| Tolak airport transfer pick up | `airPickCut` | −1100 / −1300 / −1700 |
| Tolak airport transfer drop off | `airDropCut` | −1000 / −1200 / −1600 |
| Phillip Island 10 jam (hari extension) | `day` → `[Phillip Island]` | 2600 / 3000 / 3600 |
| Phillip Island + Penguin Parade 12 jam | `piPeng12` | 2950 / 3350 / 3900 |
| Free & Easy transport standby | `feStandby` | 500 / 650 / 1000 |
| Tolak Free & Easy transport standby | `feStandbyCut` | −300 / −500 / −700 |
| Tolak hari berpandu dari katalog | `dayDed` (ikut kawasan) | Basic: PI −2000/−2400/−2800, City −1800/−2100/−2600 · Std: PI −2300/−2600/−3100, GOR −2600/−3000/−3400 |
| Malam tambahan 3 bintang | `ext.night` | 300 normal / 450 peak (per pax/malam) |
| Malam tambahan 4 bintang | `hGrand`, `hPegasus` | 450 normal / 600 peak |
| Tolak malam pakej | `nightShort` | −200 per pax |
| Surcaj travel date 2027 | `extraSurcharge` | RM 100 per pax (1 Jan – 30 Jun 2027) |
| Lunch / dinner tambahan | `meals` | RM 100 per pax per hidangan |
| Hotel breakfast buffet (add-on) | `addons` | RM 125 per pax |
| Tolak tiket Maru / Churchill / Penguin | `addons` | −85 / −45 / −85 per pax |

Kadar **bersumber** (jangan ubah tanpa katalog baharu): tier semua varian, peak
RM 125/pax/malam × 4 tetingkap, single supplement RM 2,500, infant RM 1,000,
late booking RM 50, upgrade 4 bintang RM 125/pax/malam, dan semua kadar hari ikut
kawasan kecuali Phillip Island (lihat jadual di bawah).

---

## Di mana benda yang biasa diubah

### Harga katalog (tier per pax)

`variants[].tiers` — `a` = adult, `c` = CWB (adult − 200), `n` = CNB (adult − 500).

```json
{"from": 4, "to": 4, "a": 4197, "c": 3997, "n": 3697}
```

Varian: `basic` PT Melbourne Basic 5D4N · `std` PT Melbourne Standard 5D4N.

### Kadar peak season

`peak` — `mode: perNight`, RM 125 per pax per **malam** yang jatuh dalam tetingkap.

```json
"peak": {"mode": "perNight", "value": 125,
  "windows": [["2026-09-20","2026-09-30"],
              ["2026-10-30","2026-11-08"],
              ["2026-12-01","2027-01-31"],
              ["2027-03-01","2027-04-12"]]}
```

### Kadar transport ikut kawasan

`variants[].ext.rates.day` — kunci kawasan → band pax. Ini yang menentukan kos bila TC
tambah hari dan pilih satu blok itinerary. Semua kadar ini **per transport** (satu
kenderaan dikongsi group), diambil dari halaman *ADD-ON PRIVATE DAY TOUR* katalog p3.

| Kawasan | Kadar | Ada pada varian |
|---|---|---|
| `_default` (Private Day Tour generik) | 700 / 1000 / 1500 ikut kelas kenderaan | kedua-dua |
| `[Dandenong]` Puffing Billy 8 jam | 1500 | kedua-dua |
| `[Mornington]` 8 jam | 1600 | kedua-dua |
| `[Snow]` Winter Snow Trip 12 jam | 3200 | kedua-dua |
| `[Great Ocean Road]` 12 jam | 2800 | **Basic sahaja** (Std sudah ada GOR pada D3) |
| `[Melbourne City]` full-day 10 jam | 1500 | **Standard sahaja** (Basic sudah ada city pada D3) |
| `[Melbourne City Half]` half-day 5 jam | 800 | **Standard sahaja** |
| `[Phillip Island]` 10 jam | 2600 / 3000 / 3600 | kedua-dua — ⚠ tiada sumber |

**11 pax ke atas sengaja tiada kadar.** WAE hanya quote sehingga 10 pax, jadi kalkulator
papar cip merah `kadar?` dan bukan RM 0 — quote manual dengan WAE. Kalau WAE sudah bagi
kadar 11+, tukar `"normal": null` kepada nombornya.

### Blok itinerary

`variants[].library` — senarai blok yang muncul dalam dropdown **Itinerary** setiap hari.
`g` = tajuk kumpulan dalam dropdown, `region` = kunci kadar transport di atas.
Basic ada 11 blok, Standard ada 12 (senarai berbeza kerana add-on setiap katalog berbeza).

### Add-on tiket

`addons` — `["Nama", hargaAdult, hargaChild, "pax"]`. Kuantiti diisi sendiri ikut pax.
Tour per-transport **bukan** add-on lagi — ia kini kadar hari ikut kawasan di atas,
supaya tiada dua jalan untuk kos yang sama (elak double-count).

---

## Nota

Repo ini public dan KB mengandungi harga dalaman serta analisis pesaing.
