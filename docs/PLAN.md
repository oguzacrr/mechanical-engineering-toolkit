# Proje Planı: AI-Assisted Strength of Materials Calculations for Machine Elements

> Durum: **TASLAK**. "Varsayım" ile işaretli maddeler henüz onaylanmadı.

## 0. Genel proje ve modüller

Ana proje: makine elemanları için mukavemet ve burulma hesaplarını Python ile yapan,
sonrasında yapay zekâ ile destekleyen açık kaynak bir araç seti.

| Modül | İçerik | Durum |
|---|---|---|
| 1. Kiriş (beam) | Eğilme, kesme, sehim | **Bu planın konusu (v1)** |
| 2. Mil (shaft) | Burulma, eğilme + burulma birleşik gerilme | Planlanıyor |
| 3. Diğer makine elemanları | Cıvata, yay, kama vb. mukavemet kontrolleri | Daha sonra |
| 4. Yapay zekâ katmanı | Optimizasyon, vekil model, LLM asistanı | Daha sonra |

Bu belgenin geri kalanı **Modül 1: Kiriş** içindir.

## 1. Amaç

Basit mesnetli bir kirişin mesnet tepkilerini, kesme kuvveti ve eğilme momenti diyagramlarını,
maksimum eğilme gerilmesini ve maksimum sehimini hesaplayan küçük bir Python kütüphanesi yazmak.

Asıl hedef aracın kendisi kadar **süreci öğrenmek**: problemi tanımlamak, elle çözmek,
tasarlamak, test etmek, belgelemek ve Git ile sürümlemek.

- **Varsayım:** Öncelik öğrenme ve portföy. Gerçek işte kullanım sonraki aşamada.

## 2. Kapsam

### v1 içinde
- Tek açıklıklı, **iki ucu basit mesnetli** kiriş
- **Tekil yük** (istenen konumda) ve **düzgün yayılı yük**
- Kesitler: **dikdörtgen** ve **dolu daire**
- Hesaplananlar:
  - Mesnet tepkileri
  - Kesme kuvveti V(x) ve eğilme momenti M(x) diyagramları
  - Maksimum eğilme gerilmesi
  - Maksimum sehim
- Çıktı: sayısal sonuçlar ve Matplotlib diyagramları
- Kullanım: Python fonksiyonları + Jupyter not defteri

### v1 dışında (sonraya)
- Ankastre, çıkmalı ve sürekli kirişler
- Birden fazla yük kombinasyonu, moment yükü
- Boru, I-profil ve diğer kesitler, malzeme kütüphanesi, güvenlik katsayısı
- Web arayüzü
- Yapay zekâ katmanı (bkz. bölüm 9)

## 3. Birimler ve işaret kuralı

- Tüm iç hesaplar **SI**: metre (m), Newton (N), Pascal (Pa), N·m.
- Aşağı yönlü yük **pozitif** girilir. Pozitif eğilme momenti kirişi alttan çeker (sagging).
- Kullanıcıya gösterirken MPa, mm gibi birimlere çevrilebilir. Dönüşüm tek bir yerde yapılır.

## 4. Mühendislik formülleri (elle doğrulama için)

Açıklık L, elastisite modülü E, kesit atalet momenti I, kesit merkezinden en uzak lif mesafesi c.

**Kesit özellikleri**
- Dikdörtgen (genişlik b, yükseklik h): I = b·h³ / 12, c = h / 2
- Dolu daire (çap d): I = π·d⁴ / 64, c = d / 2

**Tekil yük P, soldan a mesafesinde (b = L − a)**
- R_A = P·b / L, R_B = P·a / L
- Yük noktasında moment: M = P·a·b / L

**Düzgün yayılı yük w (N/m)**
- R_A = R_B = w·L / 2
- M_max = w·L² / 8 (orta noktada)
- Sehim (orta): δ_max = 5·w·L⁴ / (384·E·I)

**Orta noktada tekil yük P (kontrol vakası)**
- M_max = P·L / 4
- δ_max = P·L³ / (48·E·I)

**Eğilme gerilmesi**
- σ_max = M_max · c / I

Tekil yükün herhangi bir konumdaki sehim formülü uygulama aşamasında kaynaktan doğrulanacak.

## 5. Klasör yapısı (hedef)

```
mechanical-engineering-toolkit/
├── docs/
│   └── PLAN.md
├── src/beam/
│   ├── __init__.py
│   ├── sections.py     # kesit özellikleri (I, c)
│   ├── loads.py        # yük tanımları
│   ├── solver.py       # tepkiler, V(x), M(x), gerilme, sehim
│   └── plots.py        # diyagramlar
├── tests/
│   ├── test_sections.py
│   └── test_solver.py
├── notebooks/
│   └── 01_beam_demo.ipynb
├── README.md
└── requirements.txt
```

## 6. Tasarım

Küçük, tek işi yapan fonksiyonlar. Önce saf fonksiyonlar, sonra sınıf gerekirse.

| Modül | Fonksiyon / sınıf | Görev |
|---|---|---|
| sections | `rect_inertia(b, h)`, `circle_inertia(d)` | I ve c hesaplar |
| solver | `reactions(L, loads)` | R_A, R_B |
| solver | `shear_moment(L, loads, x)` | V(x), M(x) dizileri (NumPy) |
| solver | `max_bending_stress(M_max, I, c)` | σ_max |
| solver | `max_deflection(L, loads, E, I)` | δ_max |
| plots | `plot_diagrams(...)` | V ve M diyagramları |

## 7. Test planı

Her fonksiyon için **elle çözülmüş** bir vaka ile karşılaştırma (pytest):

1. Orta noktada P yükü: R = P/2, M_max = P·L/4, δ = P·L³/(48·E·I)
2. Düzgün yayılı yük: R = w·L/2, M_max = w·L²/8, δ = 5·w·L⁴/(384·E·I)
3. Simetri: yük ortada ise R_A = R_B
4. Denge: ΣF = 0 ve ΣM = 0
5. Kesit: bilinen b, h ve d değerleri için I
6. Hatalı girdi: L ≤ 0, a > L, negatif boyut → anlamlı hata

## 8. Çalışma adımları

| Adım | İş | Çıktı |
|---|---|---|
| 0 | Planı onaylamak | Bu dosyanın son hali |
| 1 | Kâğıt üzerinde bir örnek problemi elle çözmek | Referans sonuçlar |
| 2 | `sections.py` + testleri | İlk commit'li çalışan kod |
| 3 | Mesnet tepkileri + testleri | |
| 4 | V(x), M(x) | |
| 5 | Gerilme ve sehim | |
| 6 | Diyagramlar | Grafikler |
| 7 | Jupyter demo + README | Portföye hazır sürüm (v1) |

**Git düzeni:** Her adım bir veya daha fazla küçük commit. Anlamlı mesajlar.
Çalışan her adımdan sonra `git push`.

## 9. Sonraki sürümler ve yapay zekâ bağlantısı

- Farklı kesit ve malzemeler arasında **en hafif güvenli tasarımı** bulan optimizasyon
- Çok sayıda hesaptan veri seti üretip makine öğrenmesi ile **hızlı vekil model** eğitmek
- Doğal dilde tarif edilen kiriş problemini parametrelere çeviren bir **LLM asistanı**

## 10. Açık sorular

- [ ] Amaç öğrenme/portföy mü, gerçek kullanım mı?
- [ ] v1 kapsamı yeterli mi, fazla mı?
- [ ] Python ve kiriş teorisi bilgisi: nerelerde destek gerekir?
- [ ] Plan İngilizceye de çevrilsin mi (GitHub'da görünür olacağı için)?
