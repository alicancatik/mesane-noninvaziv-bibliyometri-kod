# Kod — çalıştırma sırası

Gerekli paketler: `networkx` 3.6.1 · `matplotlib` 3.10.8 · `numpy` 2.4.4 · `openpyxl`

| Sıra | Betik | Girdi | Çıktı |
|---|---|---|---|
| 1 | `bib_parse.py` | 5 adet WoS .bib (`/mnt/user-data/uploads`) | `records.json` |
| 2 | `screen2.py` | `records.json` | `screen2.json` (özellik bayrakları) |
| 3 | — | `screen2.json` + `manual.py` | `merged.json`, `top100.json` |
| 4 | `analiz.py` | `top100.json`, `records.json` | anahtar kelime / ülke / yazar ağları |
| 5 | `analiz2.py` | aynı | ortak-atıf, öz-atıf, Kleinberg |
| 6 | `sekiller.py` | aynı | 12 şekil (300 dpi PNG) |

`screen.py` ilk geçiş (liberal/katı sınır) taramasıdır; yalnızca uygunluk yoğunluğunu
ölçmek için kullanıldı, nihai örneklemde rolü yoktur.

`manual.py` elle verilen kararların tamamını taşır (rank → karar + gerekçe). Kural tabanlı
motorun yanlış-pozitif ve yanlış-negatiflerini bu dosya düzeltir. **Shkolyar 2019 dışlaması
burada sabitlenmiştir** — otomatik filtre onu yakalayamıyor.

`thesaurus.py` iki şey içerir: `THESAURUS` (eşanlamlı birleştirme, §5 karar 7) ve `GENERIC`
(korpusun tanımı gereği ayırt edici olmayan terimler, K6 ile ağdan çıkarılır).

## Kilitli parametreler

- Yerleşim: Fruchterman-Reingold, `seed=42`, `k=0.6`, `iterations=200`
- Kümeleme: Clauset-Newman-Moore açgözlü modülerlik, kenar ağırlıklı
- Kleinberg: iki durumlu otomat, `s=2`, `gamma=1`, Viterbi; en az 3 yıl, toplam ≥10 makale.
  **Taban oran örneklemin yıllık makale sayısıyla orantılıdır** — düz taban oran artefakt üretir.
- Ağ eşikleri: anahtar kelime ≥4 · ortak-atıf ≥6 · ülke ≥3 · yazar ≥4
- Şekillerde görüntüleme kenar süzmesi: anahtar kelime w≥3, ortak-atıf w≥4.
  Kümeleme ve modülerlik **tam ağ** üzerinden hesaplanır.

## Bilinen tuzaklar

- WoS `Address` alanı **yayıncının** adresidir. Ülke bilgisi `Affiliation` (tekil) alanından
  çıkarılır. `Affiliations` (çoğul) normalize kurum adlarıdır, ülke içermez.
- `Author` alanı "Soyad, Ad", kaynakça "Soyad A" biçimindedir. Öz-atıf eşleşmesi için
  `analiz2.py` içindeki `ref_surname()` kullanılır.
- BibTeX kaçış karakterleri: DOI'lerde `\_` → `_` temizlenmelidir.
- "urothelial carcinoma-associated 1" (UCA1) bir gen adıdır; bu adı taşıyan başka organ
  çalışmaları hastalık bloğuna sızar.

## Ham veri

Ham WoS kayıtları tescillidir ve paylaşılmaz (GLOBAL 6.2 kapsamında beyan edilir).
Bu pakette türetilmiş dosyalar vardır:
- `veri/records_uygun252.json` — 252 uygun kaydın tam metadatası (ağlar bundan kurulur)
- `veri/havuz5000_dizin.json` — 5.000 kaydın hafif dizini + tarama verdiktleri
- `veri/top100.json` · `veri/sampleB.json` · `veri/refset.json`

Tam yeniden tarama gerekirse kullanıcıdan beş .bib dosyası istenip 1. adımdan başlanır.

## Temiz kurulum (yeni oturumda)

```bash
cd kod
python3 veri_yukle.py ../records.json   # 5000 sıralı listeyi türetilmiş veriden kurar
cd ..
cp veri/top100.json veri/sampleB.json .
cd kod
sed -i "s|/home/claude/|../|g" analiz.py analiz2.py sekiller.py
python3 analiz.py && python3 analiz2.py && python3 sekiller.py
```

Bu dizilim test edildi ve 4. oturumdaki sayıların birebir aynısını üretir:
anahtar kelime 35/271/3 küme/Q=0,166 · ortak-atıf 89/1982/2/Q=0,345 ·
yazar 23/60/6/Q=0,523 · öz-atıf %5,70 ve %6,62 · Kleinberg 3 patlama.
