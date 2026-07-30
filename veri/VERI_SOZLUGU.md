# VERİ SÖZLÜĞÜ

Bu klasördeki dosyalar **türetilmiş veridir.** Ham Web of Science kayıtları tescillidir
ve paylaşılmamaktadır (bkz. `../README.md` §5).

---

## Ayıklanan alanlar ve gerekçesi

Aşağıdaki alanlar Clarivate'in tescilli içeriğidir ve bütün dosyalardan çıkarılmıştır:

| Alan | Ne olduğu | Neden çıkarıldı |
|---|---|---|
| `Abstract` | Yayıncının telifli özeti | Telif |
| `Keywords-Plus` | Clarivate'in ürettiği dizin terimleri | Tescilli üretim |
| `Cited-References` | Kayıt başına tam kaynakça dizgesi | Veri tabanı içeriği |
| `Affiliation` / `Affiliations` | Ham ve Clarivate-normalize adres alanları | Tescilli normalizasyon |
| `Web-of-Science-Categories`, `Research-Areas` | Clarivate sınıflandırması | Tescilli sınıflandırma |

Ayrıca 5.000'lik havuz dosyasından **başlık ve dergi adı da çıkarılmıştır.** Gerekçe:
5.000 kayıtlık başlık listesi veri tabanının kayda değer bir bölümünün yeniden
dağıtımı sayılabilir; tarama izinin tekrarlanabilirliği için DOI yeterlidir. Örneklem
dosyasında başlıklar korunmuştur, çünkü ilk 100 zaten makalede tablo olarak
yayımlanmaktadır.

**Korunan alanlar** künye niteliğindeki olgusal bilgilerdir (DOI, yıl, dergi, belge
türü, atıf sayısı) ve çalışmanın kendi ürettiği tarama çıktılarıdır.

---

## `top100.json` — nihai örneklem (n = 100)

| Alan | Açıklama |
|---|---|
| `sira` | Örneklem içi sıra (1–100), atıf azalan; eşitlikte eski yıl önce |
| `rank` | 5.000'lik havuzdaki sıra |
| `tc` | WoS atıf sayısı (tarama tarihi itibarıyla) |
| `year` | Yayın yılı |
| `doi` | DOI |
| `title` | Başlık |
| `journal` | Dergi |
| `type` | WoS belge türü |
| `ilk_yazar` | İlk yazar |
| `cat` | Teknoloji kategorisi (1–5), "asıl teknolojik katkı" kuralına göre |
| `tarama_gerekcesi` | Dahil etme gerekçesi; 72 kayıtta kural tabanlı, 28 kayıtta kayda özel |
| `verdict` | Tarama kararı (bu dosyada tamamı `IN`) |
| `bladder_t` | Başlıkta mesane/ürotelyal terimi var mı |
| `tech_t` / `tech_a` | Başlıkta / özette eşleşen teknoloji terimleri |
| `diag_s_t` / `diag_s_a` | Başlıkta / özette eşleşen güçlü tanısal çerçeve terimleri |
| `diag_w_t` | Başlıkta eşleşen zayıf tanısal çerçeve terimleri |
| `mech_t` / `mech_a` | Mekanizma/tümör biyolojisi terimleri (dışlama sinyali) |
| `treat_t` / `treat_a` | Tedavi terimleri (dışlama sinyali) |
| `tissue_t` / `tissue_a` | Doku/histopatoloji terimleri (dışlama sinyali) |
| `invas_t` | İnvaziv/endoskopik yöntem terimleri (dışlama sinyali) |
| `general_t` | Organ-spesifik olmayan genel kanser terimleri |
| `mrd` | Minimal rezidüel hastalık istisnası terimleri |

> `_t` = başlıkta eşleşen, `_a` = özette eşleşen terimler. Bunlar **özet metni değil**,
> `kod/thesaurus.py` içindeki denetimli terim listesiyle eşleşen ifadelerdir; tarama
> kararının yeniden üretilebilmesi için gereklidir.

**Not:** Önceki bir sürümde `cat_note` adlı bir alan vardı ve 100 kaydın tamamına tek
bir kaydın gerekçesini yazıyordu. Bu alan kaldırılmış, yerine denetim dosyasından
DOI eşleşmesiyle alınan `tarama_gerekcesi` konmuştur (100/100 eşleşme).

---

## `sampleB.json` — örtüşme analizinin B örneklemi (n = 100)

Sağlamlık analizi için kurulan ikinci örneklem: aynı havuz, önce genişletilmiş sorgunun
teknoloji terim listesiyle süzülmüş, sonra aynı ölçütlerle taranmıştır. Alan yapısı
`top100.json` ile aynıdır, `cat` ve `tarama_gerekcesi` alanlarını içermez.

A ve B örneklemlerinin örtüşmesi %98'dir.

---

## `havuz5000_tarama.json` — tarama izi (n = 5.000)

Hastalık bloğu sorgusundan atıfa göre azalan sırayla indirilen havuzun tamamı ve her
kayda verilen tarama kararı.

| Alan | Açıklama |
|---|---|
| `rank` | Havuz sırası (1 = en çok atıf alan) |
| `doi` | DOI |
| `year` | Yayın yılı |
| `tc` | WoS atıf sayısı |
| `type` | WoS belge türü |
| `verdict` | `IN` = ölçütleri karşılıyor · `OUT` = karşılamıyor |

Havuzun atıf aralığı 4.376–35'tir. Örneklemin alt sınırı 63 atıf olduğundan **emniyet
payı 28 atıftır**: havuz dışında kalan hiçbir uygun kaydın 35'ten fazla atfı olamayacağı
için örneklem kanıtlanabilir biçimde tamdır.

Havuzdaki 37 kayıt geri çekilmiş yayın (n = 33) veya endişe beyanı taşıyan yayındır
(n = 4); `type` alanından tanınabilirler. **Tamamı dışlanmıştır ve hiçbiri örnekleme
girmemiştir.** En yüksek atıflı olanı 458 atıflı, 2024 tarihli, geri çekilmiş bir
derlemedir.

---

## `uygun252_kimlik.json` — ölçütleri karşılayan kayıtlar (n = 252)

Ağ analizleri bu 252 kaydın tam metadatası üzerinden kurulmuştur. Tam metadata tescilli
alanlar içerdiği için paylaşılamamaktadır; burada yalnızca kimlik bilgisi verilmiştir.
Kendi WoS erişimi olan bir araştırmacı bu DOI listesiyle aynı kayıtları çekebilir.

Alanlar: `doi`, `year`, `tc`, `type`.

---

## `referans_kumesi.json` — duyarlılık testi referans kümesi (n = 148)

Arama dizgesinin duyarlılığı, örneklemden bağımsız ve önceden dondurulmuş bir referans
kümesine karşı ölçülmüştür. Küme, beş derlemenin kaynakçalarından çıkarılan 2016–2025
arası DOI'li 148 benzersiz referanstan oluşur.

| Alan | Açıklama |
|---|---|
| `dois` | 148 benzersiz DOI |
| `years` | DOI → yayın yılı eşlemesi |

Bu veri yayımlanmış derlemelerin kaynakçalarından türetilmiştir; WoS veri tabanı içeriği
değildir.

Payda düzeltmesi: 148 referansın 7'si WoS/SCI-EXPANDED'da bulunamamış, 5'i belge türü
ölçütünü karşılamamıştır → **yakalanabilir payda 136**.
