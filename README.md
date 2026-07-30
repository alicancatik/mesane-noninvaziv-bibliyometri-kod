# Mesane kanserinin noninvaziv tanı ve takibinde en çok atıf alan 100 makale — analiz kodu ve türetilmiş veri

**Yazar:** Alican Çatık · Bakırköy Dr. Sadi Konuk Eğitim ve Araştırma Hastanesi
**Veri kaynağı:** Web of Science Core Collection, yalnızca SCI-EXPANDED
**Tarama tarihi:** 30.07.2026
**Sürüm:** `[DOLDURULACAK — ör. v1.0.0]`
**Lisans:** kod MIT · veri CC BY 4.0

> **English summary.** Analysis code and derived data for a bibliometric study of the
> 100 most-cited articles on non-invasive diagnosis and surveillance of bladder cancer
> (Web of Science Core Collection / SCI-EXPANDED, 2016–2025, searched 30 July 2026).
> The raw Web of Science records are proprietary and are **not** redistributed here;
> see "Ham veri" below. All scripts, the thesaurus, the manual screening decisions and
> the identifiers needed to regenerate the dataset from an institutional WoS
> subscription are included.

---

## 1. Bu pakette ne var

```
README.md               bu dosya
CITATION.cff            künye bilgisi (GitHub bunu otomatik okur)
kod/                    analiz betikleri + betik kılavuzu
veri/                   türetilmiş veri (tescilli WoS içeriği ayıklanmış)
  VERI_SOZLUGU.md       alan tanımları ve neyin neden çıkarıldığı
```

## 2. Çalıştırma sırası

```
bib_parse.py  →  screen2.py  →  analiz.py  →  analiz2.py  →  sekiller.py
```

- `bib_parse.py` — WoS `.bib` dışa aktarımlarını ayrıştırır (**kullanıcının kendi WoS
  erişimiyle indirdiği dosyalar gerekir**, bkz. §5)
- `screen2.py` — dahil/dışlama ölçütlerini kural tabanlı uygular
- `manual.py` — elle verilen kararların tamamını taşır (sıra → karar + gerekçe). Kural
  tabanlı motorun yanlış-pozitif ve yanlış-negatiflerini bu dosya düzeltir.
- `thesaurus.py` — eşanlamlı birleştirme tablosu (`THESAURUS`) ve ağdan çıkarılan genel
  terimler (`GENERIC`). Kılavuz kararı gereği açık dosya olarak paylaşılmaktadır.
- `analiz.py` — anahtar kelime, ülke ve yazar ağları
- `analiz2.py` — ortak-atıf ağı, öz-atıf oranı, Kleinberg atıf patlaması
- `sekiller.py` — 12 şekil (300 dpi PNG)
- `screen.py` — ilk geçiş taramasıdır, yalnızca uygunluk yoğunluğunu ölçmek için
  kullanılmıştır; **nihai örneklemde rolü yoktur**

## 3. Yazılım ve sürümler

Analizler tek bir Linux (Ubuntu 24) ortamında çalıştırılmıştır.

| | Sürüm | Not |
|---|---|---|
| Python | 3.12.3 | |
| networkx | 3.6.1 | betiklerde doğrudan içe aktarılır |
| matplotlib | 3.10.8 | betiklerde doğrudan içe aktarılır |
| numpy | 2.4.4 | doğrudan içe aktarılmaz; matplotlib bağımlılığı olarak ortamda bulunur |

**Kapsam notu:** Denetim ve duyarlılık tablolarını üreten `.xlsx` yazım kodu bu pakette
yer almamaktadır; ilgili çıktılar kayıt altına alınmamış yardımcı kodla üretilmiştir.
Tabloların içeriği `veri/` klasöründeki JSON dosyalarından yeniden türetilebilir.

## 4. Kilitli parametreler

Aşağıdakiler çalışma boyunca sabittir ve değiştirilmemiştir:

- **Yerleşim:** Fruchterman-Reingold (spring), `seed = 42`, `k = 0.6`, `iterations = 200`
- **Kümeleme:** Clauset-Newman-Moore açgözlü modülerlik, kenar ağırlıklı
- **Kleinberg atıf patlaması:** iki durumlu otomat, `s = 2`, `gamma = 1`, Viterbi çözümü;
  en az 3 yıl süren ve toplam en az 10 makalede geçen terimler.
  **Taban oran örneklemin yıllık makale sayısıyla orantılıdır** — düz taban oran
  örneklemin yıl dağılımının artefaktı olan patlamalar üretmektedir.
- **Ağ eşikleri:** anahtar kelime ≥ 4 makale · ortak-atıf ≥ 6 makale · ülke ≥ 3 makale ·
  yazar ≥ 4 makale
- **Şekillerde kenar süzmesi:** anahtar kelime ağında w ≥ 3, ortak-atıf ağında w ≥ 4.
  Bu yalnızca okunabilirlik içindir; **kümeleme ve modülerlik tam ağ üzerinden
  hesaplanmıştır.**
- **Örneklem sıralaması:** atıf azalan; eşit atıfta daha eski yayın yılı öncelenir
  → `(-atıf, yıl artan)`
- **Ülke sayımı:** tam sayım (çok adresli makalelerde her ülkeye bir kredi)

## 5. Ham veri — paylaşılamaz

Ham Web of Science kayıtları Clarivate'in tescilli içeriğidir ve bu arşivde
**yeniden dağıtılmamaktadır.** Bu, GLOBAL raporlama kılavuzunun veri erişilebilirliği
maddesi kapsamında açıkça beyan edilmiştir.

Aşağıdaki alanlar türetilmiş veri dosyalarından **kasıtlı olarak çıkarılmıştır:**
öz (`Abstract`), Clarivate dizin terimleri (`Keywords-Plus`), kaynakça dizgeleri
(`Cited-References`), ham ve normalize adres alanları (`Affiliation`, `Affiliations`),
WoS konu kategorileri ve araştırma alanları.

**Çalışmayı sıfırdan yeniden üretmek için** kendi kurumsal WoS aboneliğinizle
30.07.2026'daki üç sorguyu çalıştırıp Sorgu 3'ten atıfa göre azalan ilk 5.000 tam kaydı
(Full Record + Cited References) `.bib` olarak, ayrıca atıf raporlarını `.xlsx` olarak
indirmeniz ve `bib_parse.py`'den başlamanız gerekir. Sorgu metinleri makalenin Ek 1
bölümündedir. Veri tabanı sürekli güncellendiği için atıf sayıları farklı çıkacaktır;
`veri/` klasöründeki DOI listeleri hangi kayıtların kullanıldığını kesin olarak
belirlemenizi sağlar.

**Ara adımdan devam etmek için** ham veriye gerek yoktur: `veri/top100.json` ve
`veri/uygun252_kimlik.json` örneklemi ve uygun kayıt havuzunu kimlik düzeyinde
tanımlar.

### Tekrarlanabilirlik sınırı — açıkça

Bu paketle **doğrudan yeniden çalıştırılabilenler:** tarama mantığının denetimi
(`screen2.py` + `manual.py` + `thesaurus.py` okunarak her kaydın kararı izlenebilir),
örneklem seçimi ve eşitlik kuralı, betimsel istatistikler, duyarlılık testinin payda
hesabı, örtüşme analizi.

**Doğrudan yeniden çalıştırılamayanlar:** ağ analizleri, ortak-atıf, öz-atıf ve
Kleinberg patlaması. Bunlar `analiz.py`, `analiz2.py` ve `sekiller.py` içinde
`Affiliation`, `Keywords-Plus` ve `Cited-References` alanlarını okur; bu alanlar
tescilli olduğu için pakette yoktur. Kendi WoS erişiminizle `bib_parse.py`'yi
çalıştırıp `records_uygun252.json` dosyasını yeniden ürettiğinizde bu adımlar
çalışır. Hangi kayıtların gerektiği `veri/uygun252_kimlik.json` dosyasındadır.

**Ayrıca:** betiklerde `/home/claude/...` biçiminde sabit yollar vardır; başka bir
ortamda çalıştırmadan önce güncellenmelidir (bkz. `kod/README_kod.md`).

## 6. Bilinen tuzaklar

- WoS `Address` alanı **yayıncının** adresidir. Ülke bilgisi `Affiliation` (tekil)
  alanından çıkarılmalıdır; `Address` kullanılırsa bütün Elsevier dergileri Hollanda
  çıkar.
- `Author` alanı "Soyad, Ad", kaynakça ise "Soyad A" biçimindedir. Öz-atıf eşleşmesi
  için `analiz2.py` içindeki `ref_surname()` kullanılır.
- BibTeX kaçış karakterleri: DOI'lerde `\_` → `_` temizlenmelidir.
- "urothelial carcinoma-associated 1" (UCA1) bir gen adıdır; bu adı taşıyan başka organ
  çalışmaları hastalık bloğuna sızar ve elle elenmiştir.
- Kural tabanlı tarama motoru bir kayda başlığında invaziv terim geçmediği için
  ulaşamaz; ilgili dışlama `manual.py` içinde sabitlenmiştir.

## 7. Sınırlılık

Tarama motorunun duyarlılığı %100 değildir. Örneklemden bağımsız bir referans kümesine
karşı yapılan çapraz kontrolde, sözlük boşluğundan kaynaklanan yanlış-negatifler
saptanmıştır. Eşiğin üstünde saptanan bir yanlış-negatif örnekleme eklenmiş, eşitlik
kuralı gereği bir kayıt örneklemden çıkmıştır. Eşik altında kalan yanlış-negatifler
örneklemi etkilememektedir. Ayrıntı makalenin Sınırlılıklar bölümündedir.

---

## Lisans

- **kod/** — MIT (bkz. `LICENSE`)
- **veri/** — Creative Commons Attribution 4.0 International (CC BY 4.0)
- **Ham Web of Science kayıtları** — Clarivate'in tescilli içeriğidir, bu depoda yer
  almaz ve yukarıdaki lisansların kapsamı dışındadır.

## Bu paketin künyesi

Zenodo DOI alındıktan sonra buraya yazılacak. Makalenin "Veri ve kod erişilebilirliği"
bölümünde **sürüm DOI'si değil, tüm sürümleri temsil eden Concept DOI** kullanılmalıdır;
böylece kod sonradan düzeltilse bile makaledeki künye geçerli kalır.

```
Çatık A. [Paket adı]. Sürüm [x.y.z]. Zenodo; 2026. doi:[CONCEPT DOI]
```
