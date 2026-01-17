### Omni Core v1 Bootstrap
# oc Programlama Dili Referans Kılavuzu

**Sürüm:** 1.0 (Nihai Referans) 
**Tarih:** 11.12.2025

Bu belge, oc (OmniCore) programlama dilinin **eksiksiz** başvuru kaynağıdır. Dilin sunduğu tüm veri tipleri, sözdizimi varyantları, derleyici kuralları ve standart kütüphane fonksiyonları en ince ayrıntısına kadar açıklanmıştır.

---

## İçindekiler

1.  [Giriş ve Felsefe](#1-giriş-ve-felsefe)
2.  [Temel Sözdizimi](#2-temel-sözdizimi)
3.  [Değişkenler ve Veri Tipleri](#3-değişkenler-ve-veri-tipleri)
4.  [Veri Yapıları ve Koleksiyonlar](#4-veri-yapıları-ve-koleksiyonlar)
5.  [Operatörler](#5-operatörler)
6.  [Kontrol Akışı](#6-kontrol-akışı)
7.  [Fonksiyonlar ve Fonksiyonel Programlama](#7-fonksiyonlar-ve-fonksiyonel-programlama)
8.  [Modüler Programlama ve Kütüphaneler](#8-modüler-programlama-ve-kütüphaneler)
9.  [Nesne Yönelimli Programlama (Struct & Group)](#9-nesne-yönelimli-programlama-struct--group)
10. [Bellek ve Sistem Erişimi](#10-bellek-ve-sistem-erişimi)
11. [Standart Kütüphane Referansı](#11-standart-kütüphane-referansı)
12. [Modüllerden Bağımsız Yardımcı Fonksiyonlar](#12-modüllerden-bağımsız-yardımcı-fonksiyonlar-built-ins)
13. [Derleyici Hedefleri ve Optimizasyon](#13-derleyici-hedefleri-ve-optimizasyon-targets)

---

## 1. Giriş ve Felsefe

OmniCore, kontrolü geliştiriciye veren ancak modern kolaylıkları da sunan hibrit bir dildir.
*   **Tam Kontrol:** Bellek (`ptr`), CPU registerları (`cpu`) ve GPU çekirdeklerine (`gpu`) doğrudan erişim.
*   **Esneklik:** Fonksiyonel (zincirleme, lambda, UFCS) ve OOP (struct-group) paradigması.
*   **Netlik:** Açık modül önek tanımları (`:onek;`) ve okunabilir sözdizimi.

---

## 2. Temel Sözdizimi

### 2.1 Bloklar ve İfadeler
*   **Sonlandırma:** Noktalı virgül (`;`) her ifadenin sonunda zorunludur.
*   **Bloklar:** `{ ... }` süslü parantezleri ile kapsam (scope) oluşturulur.

### 2.2 Yorumlar

```
oc
// Bu bir tek satır yorumdur.

/* 
   Bu bir
   çok satırlı (blok) yorumdur.
*/
```

---

## 3. Değişkenler ve Veri Tipleri

OmniCore, statik tipli bir dildir ancak `any` tipi ile dinamikliği destekler.

### 3.1 Tanımlayıcılar (`var`, `let`, `const`, `must`)


*   **`var_name`**: Değiştirilebilir (mutable) değişken.

```oc
    sayac: i32 = 0;
    sayac = 1; // Geçerli
```
    
*   **`let`**: Değiştirilemez (immutable) sabit. (Varsayılan olarak).
```oc
    let pi: f32 = 3.14;
    // pi = 3.15; // HATA!
```
*   **`mut let`**: Değiştirilebilir `let`.

*   **`const`**: Derleme zamanı sabiti (Compile-time constant).
```oc
    const SURUM = "1.0.0";
```
    
*   **`must`**: Derleme zamanında (compile-time) kesinlikle hesaplanması veya mevcut olması gereken değerler için (Assertion gibi).
```oc
    must  config = load_config(); // Yüklenemezse derleme durur.
```

### 3.2 Temel Tamsayı Tipleri (Integers)

| Tip   | Bit Genişliği | İşaretli mi? | Değer Aralığı | Yaygın Kullanım |
|---    |---            |---           |---            |---             |
| `i8`  | 8-bit         | Evet         | -128 .. 127   | ASCII Karakter, İşaretli Byte |
| `u8`  | 8-bit         | Hayır        | 0 .. 255      | Ham Veri, Piksel, Byte |
| `i16` | 16-bit        | Evet         | -32,768 .. 32,767 | Ses Örnekleri, Sensör Verisi |
| `u16` | 16-bit        | Hayır        | 0 .. 65,535   | Port Numaraları, Sayaçlar |
| `i32` | 32-bit        | Evet         | -2.14 Milyar .. +2.14 Milyar | Varsayılan Tamsayı |
| `u32` | 32-bit        | Hayır        | 0 .. 4.29 Milyar | Boyutlar, İndeksler |
| `i64` | 64-bit        | Evet         | Çok Geniş | Zaman Damgası (Timestamp), Dosya Boyutu |
| `u64` | 64-bit        | Hayır        | 0 .. 18 Kentilyon | Bellek Adresleri (Pointer) |
| `i128`| 128-bit       | Evet         | Astronomik | Kriptografi, Bilimsel Hesaplama |
| `u128`| 128-bit       | Hayır        | Maksimum | Hash Değerleri (UUID, GUID) |

### 3.3 Ondalıklı Sayılar (Floating Point)

*   `f32`: Tek hassasiyetli (Single precision, 7 basamak). Grafik ve oyun fiziği için.
*   `f64`: Çift hassasiyetli (Double precision, 15 basamak). Varsayılan ondalık tip.
*   `f128`: Dört hassasiyetli (Quad precision). Bilimsel simülasyonlar.
*   Ayrıca `float` tipler `print()` veya `echo()` yaparken formatlama için `val.2`  ifade edilebilir, bu ifade ondalık basamaktan sadece ilk 2 rakamın gösterileceği / dikkate alınacağı anlamına gelir.
### 3.4 Finansal/Sabit Noktalı Sayılar (Fixed Point)

#Para birimi hesaplamalarında yuvarlama hatası olmaması için kullanılır.

*   `d64`: Standart finansal değer.
*   `d128`: Yüksek hassasiyetli finansal değer.

### 3.5 Özel Donanım ve Sistem Tipleri
*   **`bit`**: Sadece 1-bit alan kaplar (`0` veya `1`). Boolean yerine donanım bayrakları için.
*   **`byte`**: `u8` için bir takma addır (alias).
*   **`hex`**: Onaltılık (Hexadecimal) veri literali. ` adres: hex = 0xFF1A;`
*   **`ptr`**: Tipi belirsiz ham bellek işaretçisi (`void*` karşılığı).
*   **`any`**: Dinamik tip. Çalışma zamanında her türlü veriyi tutabilir.

*   **`char`**: 'A' tek karakterlik tip
*   **`str`**:  "string" string tanımlama (tüm stringler UTF8 dir.) Ayrıca str[30] veya `str/30` ilk 30 karakterin dikkate alınacağını bildirir.

---

## 4. Veri Yapıları ve Koleksiyonlar

### 4.1 Diziler (Arrays)

OmniCore'da diziler, tür güvenliği ve esneklik sağlayan çeşitli yollarla tanımlanabilir.

**Statik (Sabit Boyutlu) Diziler:**
Stack belleğinde tutulur. Boyut derleme zamanında bilinmelidir.

```oc
// 1. Tip Çıkarımı ile (Inference):
var sayilar = [1, 2, 3, 4, 5]; // Tipi: i32[5]

// 2. Açık Tip Tanımı ile:
var notlar[3]: f64 = [10.5, 20.0, 30.2];

// 3. Karmaşık Tipli Dizi (Heterogeneous):
var karisik: arr = [1, "iki", 3.14]; // Tipi: arr (Generic Array)

// 4. Çok Boyutlu Dizi (Henüz tam desteklenmiyor, düzleştirilmiş kullanılabilir):
// var matris[9]: i32 = [1,0,0, 0,1,0, 0,0,1];
```

**Dinamik (Büyüyebilir) Diziler:**
Heap belleğinde tutulur. `d[]` sözdizimi kullanılır.

```oc
liste[]: i32 = [10, 20, 30];
liste.push(40); // Sona ekle
```

**Fonksiyonlar:** (Bkz: `array` Standart Modülü)
*   `push`, `pop`, `count`, `clear`, `find`, `sort`, `reverse`.

### 4.2 Haritalar (Maps / Dictionaries)

Anahtar-Değer (Key-Value) çiftlerini tutar. `map<AnahtarTipi, DegerTipi>`

```oc
notlar: map<str, i32>;
notlar["Ali"] = 90;
notlar["Veli"] = 85;

 notu: i32 = notlar["Ali"]; // 90
```

### 4.3 Matematiksel Vektörler
Oyun ve grafik programlama için SIMD destekli tipler.

*   `Vec2`: `[x, y]`
*   `Vec3`: `[x, y, z]`
*   `Vec4`: `[x, y, z, w]`

```oc
 v1 = Vec3[1.0, 2.0, 3.0];
 v2 = Vec3[4.0, 5.0, 6.0];
 sonuc = v1 + v2; // [5.0, 7.0, 9.0] otomatik toplanır.
```

---

## 5. Operatörler

*   **Aritmetik:** `+`, `-`, `*`, `/` (bölme), `%` (mod), `++` (artırma), `--` (azaltma).
*   **Mantıksal:** `&&` (VE), `||` (VEYA), `!` (DEĞİL).
*   **Bitwise (Bit Düzeyi):** `&` (VE), `|` (VEYA), `^` (XOR), `~` (TÜMLEYEN), `<<` (SOLA KAYDIR), `>>` (SAĞA KAYDIR).
*   **Karşılaştırma:**
    *   `==`: Değer eşitliği.
    *   `===`: Kimlik (Referans/Adres) eşitliği.
    *   `!=`: Eşit değil.
    *   `!==`: Aynı nesne değil.
    *   `<`, `>`, `<=`, `>=`.
*   **Erişim:**
    *   `.`: Üye erişimi (`insan.ad`).
    *   `.`: Statik/Modül erişimi (`network.http.get`). //group fonksiyonun yapısı gereği üye erişimi . ile yapılır.

*   **İkili Operatörler (Binary Operators):**
    *   `.=` : String birleştirme operatörü. Mevcut string'e yeni string ekler.
```
    a: str = "";
    a .= "Bugün hava";
    a .= " bulutlu";
    a .= " sıcaklık 16 derece.";
    // a = "Bugün hava bulutlu sıcaklık 16 derece."; 
```
    *   Diğer operatörler (aritmetik, mantıksal, bitwise) yukarıda belirtilmiştir.
            
---

## 6. Kontrol Akışı

### 6.1 `if - elseif - else`

* tek satır kod if
```oc
x = 10;
if (x > 100) print("Büyük");
```

* tek satır kod if else
```oc
x = 10;
if (x > 100) println("Büyük");
else println("Küçük");
```

```oc
x = 10;
if (x > 100) {
    print("Büyük");
} elseif (x == 10) {
    print("Eşit");
} else {
    print("Küçük");
}
```

---

### 6.2 `match` (Desen Eşleştirme)

OmniCore'ın en güçlü özelliklerinden biridir. 4 farklı kullanım varyantı vardır.

**1. Basit Eşleşme:**
```oc
match (durum) {
    1 => {print("Aktif")},
    0 => {print("Pasif")},
    def => {print("Bilinmiyor")} // Varsayılan (default)
}
```

**2. Çoklu Seçim:**
Birden fazla değeri tek satırda eşleştirme.
```oc
match (sayi) {
    1, 3, 5, 7, 9 => {print("Tek Sayı")},
    2, 4, 6, 8, 0 => {print("Çift Sayı")}
}
```

**3. İfade Olarak (Expression Match):**
Sonucu bir değişkene atama. **Not:** Expression match kullanımında sonundaki noktalı virgül isteğe bağlıdır.
```oc
 mesaj = match (kod) {
    200 => "Başarılı",
    404 => "Bulunamadı",
    500 => "Sunucu Hatası",
    def => "Bilinmeyen Hata"
}
```
**4. Result, Option kontrolü.
```
match file.open("test.txt", 0) {
    Ok(h) => { ... },
    Err(code) => { ... }
}
```
---

### 6.3 Döngüler

**1. C-Tarzı For Döngüsü:**
Geleneksel, virgül ile ayrılmış yapı.
i =1 yapılmamışsa, i otomatik 0 dır. 
```oc
for (i, i<10, i++) {
    echo(i);
}
```

**2. Aralık (Range) Döngüsü:**
`baslangic..bitis` (bitiş dahil).
```oc
// i burada otomatik "i:i32=0" olarak tanımlanır. 
// range (A..Z), (a..z),(0..999) gibi tanımlanabilir.
for (i in 0..10) {
    echo(i); // 0, 1, ..., 10
}
```

**3. Koleksiyon Döngüsü (Foreach):**
```oc
var isimler:arr = ["Ali", "Veli"];
for (isim in isimler) {
    echo(isim);
}
```

**4. While Döngüsü:**
```oc
while (kosul) {
    // ...
}
```
```
var i: i32 = 0;
    while (i < 10) {
        if (i == 5) {
            break;
        }
        echo("{i} ");
        i = i + 1;
    }
```

**5. Sonsuz Döngü (`loop`):**
```oc
loop {
    if (koşul) break;
}
```
---

### 6.4 `rolling` (Hata Toleranslı Blok)

## Bir kod bloğunun hata vermesi durumunda, belirli bir sayıda tekrar denenmesini sağlayan mekanizmadır.
 `$rolling` özel değişkeni mevcut deneme sayısını tutar.

```oc
// Blok adı BAGLANTI:
BAGLANTI:{
    // $rolling değişkeni otomatik tanımlanır ve 0'dan başlar.
    
    echo("Bağlanmaya çalışılıyor... Deneme: {$rolling}");
    
    conn = network.connect("sunucu.com");
    
    if (conn.hata && $rolling <= 4) {
        time.sleep(1000); // 1 saniye bekle
        rolling:BAGLANTI; // Bloğu başa sar (goto gibi ama güvenli)
    }
    if($rolling == 5){
        echo("Bağlantı başarısız.");
        exit(1);
    }
}
```

---

## 7. Fonksiyonlar ve Fonksiyonel Programlama

### 7.1 Tanımlama Biçimleri

**Standart Tanım:**
```
fn topla(a: i32, b: i32): i32 {
    return a + b;
}
```

**Void Fonksiyon (Prosedür):**
Dönüş tipi belirtilmezse `void` kabul edilir.
```
fn selamla(isim: str) {
    print("Merhaba {isim}");
}
```

**Expression Body (Tek Satır):**
Süslü parantez yerine `->` kullanılır.
```
fn kare(x: i32) -> x * x;
```

**Lamda:**
anonim fonksiyon isimsiz fonksiyonlar:  fn (...) -> ...;
```
var kare:i32 = fn (x: i32) -> x * x;
```
---


### 7.2 Parametre Özellikleri

*   **Varsayılan Değer:** `fn topla(a: i32, b: i32 = 0)`
*   **İsimli Çağrı:** `topla(a: 5, b: 10)` veya `topla(b: 10, a: 5)`
*   **Değişken Sayıda Parametre (Varargs):** `...`
```oc
    fn log(mesaj: str, ...) { ... }
    log("Hata", "Dosya", "Yok");
```

### 7.3 UFCS (Uniform Function Call Syntax) work flow
Fonksiyonların, ilk parametrelerinin bir metoduymuş gibi çağrılabilmesini sağlar. Bu, zincirleme (chaining) yapmayı kolaylaştırır.

```oc
// Standart çağrı:
str.to_upper(str.trim("  oc  "));

// UFCS ile çağrı (Okunabilir):
"  oc  "->trim()->to_upper();
```

### 7.4 Higher-Order Functions (HOF)
Fonksiyonları parametre olarak alabilen veya döndürebilen fonksiyonlar.

```oc
fn islem_yap(dizi[]: i32, operasyon: fn(i32): i32) {
    for (i in dizi) {
        operasyon(i);
    }
}
```

---

## 8. Modüler Programlama ve Kütüphaneler

### 8.1 Modül Tanımlama (`:onek;`)
Her modül dosyasının (`.oc`) en başında, o modülün diğer dosyalarda hangi önek (prefix) ile çağrılacağını belirleyen özel bir tanımlama bulunur.

**Örnek Dosya: `network.oc`**
```oc
:net; /* ön ek olarak kullanır.
:;  veya hiçbir önek almadan kullanılır. bu şekilde sadece group (http) adını kullanır. */
export group http { ... }
```

### 8.2 Erişim Belirteçleri (`export`)
Varsayılan olarak her şey **gizlidir (private)**. Dışarı açmak için `export` veya `pub` kullanılır.

```oc
gizli: i32 = 0;
export  acik: i32 = 1;

export fn genel_fonk() { ... }
```

### 8.3 Kullanım (`use`)
```oc
use network;       // network.oc dosyasını yükle.
// Dosya başında :net; tanımlı olduğu için:
net.http.get(...); 

use math as m;     // math modülünü 'm' olarak takma adla yükle.
m.sin(30);
```

---

## 9. Nesne Yönelimli Programlama (Struct & Group)

OmniCore, veriyi (`struct`) ve davranışı (`group`) birbirinden kesin olarak ayırır.

### 9.1 Struct (Veri Yapısı)
Sadece alanları (field) tutar. Metot içermez.

```oc
struct Oyuncu {
    ad: str;
    puan: i32;
    aktif: bool;
}
```


### 9.2 Group (Davranış Grubu)
Fonksiyonları (metotları) gruplar. 

**KURAL:** Bir `group`, bir `struct`'a metot ekleyecekse, isimleri **BİREBİR AYNI** olmalıdır.

```oc
group Oyuncu {
    /* struct group içerisinde tanımlanır ise dışarıdan direkt olarak erişilemez 
        sadece group metotları kullanılarak ulaşılabilir */
    struct Oyuncu {  
        ad: str;
        puan: i32;
        aktif: bool;
    }

    // Yapıcı (Constructor) - Statik Fonksiyon
    yeni => fn(isim: str): Oyuncu {
        return Oyuncu { 
            ad: isim, 
            puan: 0, 
            aktif: true 
        };
    }

    // Nesne Metodu
    // İlk parametre 'self' olmak zorundadır, tüm yapıları tek tek yazmaktan kurtarır. Tipi (Oyuncu) belirtilmez (infer edilir).
    puan_arttir => fn(self, miktar: i32) {
        self.puan = self.puan + miktar;
    }
    
    // Tek satır metot
    isim_getir => fn(self) -> self.ad;
}

// Kullanım:
o1 = Oyuncu.yeni("Ahmet");
o1.puan_arttir(10);
print(o1.isim_getir()); // Ahmet
```

---


### 10.2 `asm` ve `fastexec`:
fastexec, asm kodları için kapsayıcı, değişken geçişlerinin otomatik olarak yapıldığı ve optimize edici bir bloktur.
Inline Assembly kodları için CPU register kontrolü.
* Bu geliştirmelerle artık asm bloklarında şunları yapabilirsiniz:
* Clobber List: Bloğun başında # clobber: rax, rbx gibi bir satır ekleyerek, bu register'ların blok öncesinde push ve sonrasında pop edilmesini sağlayabilirsiniz.
* Register Mapping: %degisken:reg sözdizimini kullanarak (örneğin %sayac:rcx), değişkenin blok başında otomatik olarak o register'a yüklenmesini ve blok sonunda register'daki değerin değişkene geri yazılmasını sağlayabilirsiniz.

```oc

fn main() {
    fastexec {
        // Standart değişken tanımlama
        var a: i32 = 10;
        var b: i32 = 20;
        var total: i32 = 0;
        
        asm: CRITICAL_ADD { 
            mov rax, %a      // %a -> [rbp - 8] gibi bir adrese dönüşür
            add rax, %b      // %b -> [rbp - 16]
            mov %total, rax  // %total -> [rbp - 24]
        }
        
        echo(total); // Çıktı: 30
    }

    //# En kolay yöntem bir fonksiyon tanımlayıp içerisine eklemektir, return yapabilir. asmcall,asmjmp return yapmaz. 
    // Üretilen kodlar içerisinden call yapar. Normal fonksiyon gibi asm bloklarınızı kullanabilirsiniz.
    asmcall(CRITICAL_ADD);
    
    // Veya oraya zıpla (dikkat: Ne yaptığınızı bilmiyor iseniz asm bloklarını kullanmayın.)
    // Eğer kodunuzda ret veya başka bir jmp yoksa hiç geri dönmeyebilir, ve uygulama çöker.
    asmjmp("MY_ASM_BLOCK");    
    
}


```

### 12. Modüllerden Bağımsız Yardımcı Fonksiyonlar (Built-ins)

Bu fonksiyonlar herhangi bir `use` bildirimi gerektirmeden her yerden doğrudan çağrılabilir. Dilin çekirdeğine gömülüdürler.

#### 12.1 Temel Giriş/Çıkış
*   **`echo(args...)`**: Çok yönlü ekrana yazdırma fonksiyonu. Otomatik tip çıkarımı yapar ve süslü parantez `{}` ile string interpolation destekler. `print` ve `println` fonksiyonlarının daha yetenekli ve pratik halidir.
```oc
    echo("Merhaba Dünya\n");           // Düz metin
    echo(12345);                       // Sayısal değer
    echo("Sonuç: {10 + 20}");          // İfade interpolasyonu
    echo("Değer: {degisken}");         // Değişken interpolasyonu
    echo(fonksiyon_cagrisi());         // Fonksiyon sonucu
```
*   **`input(prompt)`**: Kullanıcıdan veri almak için kullanılır. Opsiyonel bir mesaj (prompt) görüntüleyebilir. Her zaman `str` (metin) döndürür.
```oc
     ad:str = input("Adınız nedir?: ");
```

#### 12.2 Bellek ve Tip Bilgisi
*   **`sizeof(T)`**: Bir tipin veya değişkenin bellekte kapladığı alanı (byte cinsinden) döndürür.
*   **`typeof(val)`**: Değişkenin çalışma zamanındaki tipinin adını (`str` olarak) döndürür.
*   **`default(T)`**: Belirtilen tipin varsayılan değerini (sıfır, false, null vb.) döndürür.

#### 12.3 Güvenlik ve Hata Ayıklama
*   **`assert(kosul, mesaj)`**: Koşul `false` ise programı belirtilen mesajla ve hata koduyla durdurur. Sadece geliştirme (debug) modunda çalışır.
*   **`panic(mesaj)`**: Kurtarılamaz bir hata durumunda programı derhal sonlandırır ve yığın izini (stack trace) ekrana basar.
*   **`todo(mesaj)`**: Henüz implemente edilmemiş kod blokları için yer tutucu. Çalıştırılırsa "Not Implemented" hatası verir.

#### 12.4 Akış Kontrolü ve İşlem Yönetimi
*   **`exit(code)`**: Programı belirtilen çıkış koduyla (`0`: Başarılı, `1` ve üzeri: Hata) derhal sonlandırır. `os.exit` ile aynıdır ancak modül gerektirmez.
*   **`defer(blok)`**: İçinde bulunduğu blok (scope) tamamlandığında çalıştırılacak kodu sıraya ekler. Kaynak temizliği (dosya kapatma, bellek serbest bırakma) için idealdir.
```oc

fn dosya_oku() {
    f = file.open("test.txt");
    defer { 
        file.close(f); 
        print("Dosya kapatıldı."); 
    }
    
    // Dosya işlemleri...
    if (hata) {
        return; // Burada defer otomatik çalışır ve dosyayı kapatır.
    }
    // Fonksiyon bitiminde de defer çalışır.
}

```

#### 12.5 Matematik ve Mantık Yardımcıları
*   **`min(a, b)`**: İki değerden küçük olanı döndürür.
*   **`max(a, b)`**: İki değerden büyük olanı döndürür.
*   **`clamp(val, min, max)`**: Değeri belirtilen aralığa sınırlar (`val < min` ise `min`, `val > max` ise `max` döner).
*   **`swap(a, b)`**: İki değişkenin değerini güvenli ve hızlı bir şekilde yer değiştirir.

#### 12.6 Evrensel Tip Dönüşümleri
Modül kullanmadan hızlıca tip dönüşümü (casting) yapmak için kısa yollar.

*   **`_int(val)` / `stri32(val)`**: Verilen değeri (özellikle string'i) tamsayıya çevirir. `input`'tan gelen veriyi matematikte kullanmak için gereklidir.
```oc
     yas = int(input("Yaşınız: ")); // veya stri32(giris)
```
*   **`float(val)`**: Değeri ondalıklı sayıya (`f64`) çevirir.
*   **`str(val)`**: Herhangi bir değeri metne çevirir.
*   **`bool(val)`**: Değeri mantıksal (`true/false`) tipe çevirir.


#### 12.7 Sonuç Yönetim Metotları (Result & Option)
* OmniCore'da hata veya boş değer dönebilen fonksiyonlar `Result` veya `Option` tipi döndürür. 


### Sonuç Yönetim Metotları: Operatör Önceliği ve Akış Kuralları

** `Result` ve `Option` Tipleri:** Hata yönetiminde kullanılır.

Örnek:
```
        match (file.open("not.txt", file.w)) {
            Ok(h) => {
                file.write(h, "Test verisi");
                file.close(h);
            },
            Err(e) =>
        }

```
    
*   **`val.is_ok()` / `val.is_err()`**: İşlemin başarılı mı başarısız mı olduğunu `bool` olarak söyler.
*   **`val.is_some()` / `val.is_none()` / `val.is_null()`**: Değer  mı yok mu (`Option` için) kontrol eder.
---

## 10. Bellek ve Sistem Erişimi

### 10.1 `memory` Modülü: Manuel Yönetim
OmniCore normalde otomatik bellek yönetimi kullanır. Ancak `memory` modülü ile C-tarzı manuel yönetim mümkündür. İleri düzey kullanıcılar içindir.

> **Önemli Not:** Derleyici, `memory` modülü kullanıldığında bile herhangi bir hatayı önlemek için ayrılan hafızayı ve veri tiplerini arka planda kontrol eder. 
Aşırı uyumsuzluk veya taşma durumunda derleme zamanında veya çalışma zamanında hata üretir. Bu sayede manuel yönetimde bile bellek sızıntılarının ve kritik hataların mümkün olduğunca önüne geçilir.

```oc
use memory;

// 1024 byte yer ayır
 ptr = memory.alloc(1024);

// Belleği kullan (unsafe)
memory.set(ptr, 0, 1024); // Sıfırla

// Serbest bırak
memory.free(ptr);

// Veri Okuma/Yazma (Dereferencing)
memory.write<i32>(ptr, 0, 100);      // ptr[0] = 100
 val = memory.read<i32>(ptr, 0);  // val = ptr[0]

// Blok İşlemleri
memory.copy(src_ptr, dest_ptr, 1024); // memcpy
memory.move(src_ptr, dest_ptr, 1024); // memmove (güvenli)
```

**Temel Fonksiyonlar:**
*   `alloc(size) -> ptr`
*   `realloc(ptr, new_size) -> ptr`
*   `free(ptr)`
*   `set(ptr, val_u8, size)`: Belirtilen alanı bayt ile doldurur (memset).
*   `read<T>(ptr, offset)`: Adresten değer okur.
*   `write<T>(ptr, offset, val)`: Adrese değer yazar.
*   `copy(src_ptr, dest_ptr, 1024)`: memcpy
*   `move(src_ptr, dest_ptr, 1024)`: memmove (güvenli)

 

### 10.3 `cpu` Modülü: İşlemci Kontrolü
**Gereksinim:** `cpu` modülü fonksiyonları **sadece `fastexec` bloğu içerisinde** çalışır. Bu kapsam dışında çağrılırlarsa derleyici hata üretir.

Bu modül, çekirdekler, registerlar (kaydediciler) ve düşük seviyeli işlemci komutları ile doğrudan etkileşim kurmayı sağlar.

*   `core_count() -> i32`: Sistemdeki mantıksal çekirdek sayısını döndürür.
*   `reg_get(reg_id) -> u64`: Belirtilen register'ın (örn: `cpu.RAX`) değerini okur.
*   `reg_set(reg_id, val)`: Register'a değer yazar. **Dikkatli kullanılmalıdır.**
*   `pause()`: İşlemciye "duraklama" (spin-loop hint) sinyali gönderir. Enerji tasarrufu ve HT performansı için döngülerde kullanılır.
*   `flush(ptr)`: Belirtilen adresi CPU önbelleğinden (cache) temizler.
*   `rdtsc() -> u64`: Time-Stamp Counter değerini okur (Çok hassas zamanlama için).
*   `get_freq() -> i32`: Anlık CPU frekansını (MHz) döndürür.

**Kullanım Örneği:**

```oc
fn LowLevelOp() {
    fastexec {
        // Çekirdek sayısını al
         cores = cpu.core_count();
        
        // Hassas zaman ölçümü (Cycle cinsinden)
         start = cpu.rdtsc();
        
        asm: ISLEM {
            mov rax, 100
            // ...
        }
        
         end = cpu.rdtsc();
         cycles = end - start;
        
        // Spin-wait döngüsü örneği
        while (mesgul_mu) {
            cpu.pause(); // CPU'yu rahatlat
        }
    }
    // cpu.rdtsc(); // HATA! fastexec dışında kullanılamaz.
}
```

---

## 11. Standart Kütüphane Referansı

Bu bölüm, OmniCore standart kütüphanesindeki modüllerin tam listesidir.

### 11.1 `io` Modülü
**Açıklama:** Standart Giriş/Çıkış işlemleri.

*   `fn print(val: any,style opsiyonel): void` - Ekrana yazar (yeni satır yok). yerel fonksiyon olarak eklendi.
*   `fn println(val: any,style opsiyonel): void` - Ekrana yazar ve satır atlar. yerel fonksiyon olarak eklendi.
*   `fn eprint(val: any): void` - Hata akışına (stderr) yazar.  yerel fonksiyon olarak eklendi.
*   `fn prompt(msg: str): str` - Mesaj gösterip girdi ister.
*   `fn input(): str` - Klavyeden veri okur.                    yerel fonksiyon olarak eklendi.
*   `fn flush(): void` - Çıktı tamponunu temizler.

**Örnek:**
```oc
ad = io.prompt("Adınız: ");
println("Merhaba {ad}");

// Styling System (Renkli Çıktı)
// 1. Stil Tanımlama (Global Kapsamda yapılmalıdır)
style Dikkat = "\033[31;1m"; // Kırmızı ve Kalın
style Bilgi  = "\033[36m";    // Cyan

// 2. Kullanım (İkinci parametre olarak stil adı veya ANSI kodu verilir)
println("Bu kritik bir hatadır!", "Dikkat");
println("İşlem tamamlandı.", "Bilgi");
println("Doğrudan ANSI kodu.", "\033[32m"); // Yeşil
print("Normal yazı.");

// Yerleşik Stiller: "error", "warn", "info", "success"
print("Başarılı!", "success");
```

### 11.2 `string` Modülü
**Açıklama:** Metin işleme fonksiyonları.
// :str; 

*   `len(s)`: Karakter sayısı. /str.len() / modülden bağımsız strlen(string) ile aynı işi yapar.
*   `is_empty(s)`: Boş kontrolü.
*   `to_upper(s)`, `to_lower(s)`: Dönüşüm.
*   `trim(s)`: Boşlukları temizle.
*   `split(s, ayirici)`: Böl ve dizi döndür.
*   `join(dizi, birlestirici)`: Birleştir.
*   `contains(s, aranan)`, `starts_with`, `ends_with`.
*   `replace(s, eski, yeni)`.
*   `substring(s, bas, uzunluk)`.

**Örnek:**
```oc
 s = "  merhaba dünya  ";
 temiz = to_upper(trim(s)); // "MERHABA DÜNYA"
if temiz.contains("DÜNYA") { ... }
```

### 11.3 `array` Modülü önek `arr`
**Açıklama:** Dinamik dizi araçları.

*   `count(arr)`: Eleman sayısı.
*   `capacity(arr)`: Ayrılan bellek kapasitesi.
*   `push(arr, val)`: Sona ekle.
*   `pop(arr)`: Sondan çıkar (`Result` döner).
*   `insert(arr, index, val)`: Araya ekle.
*   `remove(arr, index)`: Sil.
*   `find(arr, val)`: Ara (`Option<index>` döner).
*   `sort(arr)`: Sırala.
*   `reverse(arr)`: Ters çevir.
*   `clear(arr)`: Temizle.
*   `newcap(size)`: Optimize dizi oluştur.

**Örnek:**
```oc
sayilar[]: i32 = [3, 1, 4];
sayilar.sort(); // [1, 3, 4]
sayilar.push(2);
sayilar.reverse(); // [2, 4, 3, 1]
sayilar.sort(); // [1, 2, 3, 4]
sayilar.clear(); // []
sayilar.newcap(10); // [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
```

### 11.4 `math` Modülü
**Açıklama:** Matematiksel fonksiyonlar ve sabitler (`f64` üzerinde çalışır).

*   **Sabitler:** `PI`, `E`, `INF`, `NAN`.
*   **Temel:** `abs`, `pow(x, y)`, `sqrt`.
*   **Logaritmik:** `exp`, `log` (ln), `log10`.
*   **Trigonometrik:** `sin`, `cos`, `tan`, `atan2`.
*   **Dönüşüm:** `deg_to_rad`, `rad_to_deg`.
*   **Yuvarlama:** `round`, `floor`, `ceil`, `trunc`.

**Örnek:**
```oc
 yaricap = 5.0;
 alan = math.PI * math.pow(yaricap, 2.0);
 aci_rad = math.deg_to_rad(45.0);
```

### 11.5 `file` Modülü
**Açıklama:** Dosya sistemi işlemleri.

*   `open(path, mode) -> Result<Handle, Error>`
*   Modlar: `READ` `r`, `WRITE` `w`, `APPEND` `a`, `READ_WRITE` `rw`.
*   `readall(handle) -> str`
*   `write(handle, data)`
*   `close(handle)`
*   `exists(path) -> bool`
*   `delete(path)`
*   `copy(src, dest)`

**Örnek:**
```oc
    (h)<-file.open("not.txt", file.w) ?-> eprint("Hata: {e}")
    (h)-> {
        file.write(h, "Test verisi");
        file.close(h);
    }

```

### 11.6 `network` Modülü
**Önek:** `:net;` (Kullanımda `net` olarak çağrılır.)

Bu modül, tüm ağ operasyonlarını üç ana grup altında toplar.

#### ** Grup: `net.http` (Web İstemci ve Sunucu)**

*   `get(url) -> Result<Response, Error>`
*   `post(url, body) -> Result<Response, Error>`
*   `server(port, handler_fn)`: Basit HTTP sunucusu başlatır.

**Örnek (İstemci):**
```oc
 cevap = net.http.get("https://api.ornek.com/veri");
if cevap.is_ok() {
    echo(cevap.unwrap().body);
}
```

**Örnek (Sunucu):**
```oc
// 8080 portunda sunucu başlat
net.http.server(8080, fn(req) {
    if (req.path == "/selam") return "Merhaba Dünya";
    return 404; // Not Found
});
```

#### ** Grup: `net.socket` (Düşük Seviye Soket)**

*   `create(type)`: `TCP` veya `UDP` soketi oluşturur.
*   `bind(sock, addr, port)`
*   `listen(sock)`
*   `accept(sock)`
*   `connect(sock, addr, port)`
*   `send(sock, data)`
*   `recv(sock, size)`
*   `close(sock)`

**Örnek (Raw Socket):**
```oc
 s = net.socket.create(net.TCP);
net.socket.connect(s, "127.0.0.1", 9000);
net.socket.send(s, "Ping");
```

#### ** Grup: `net.tcp` (TCP Yardımcıları)**

*   `connect(host, port) -> Stream`: Hızlı bağlantı.
*   `listen(port, handler)`: Hızlı sunucu.

```oc
 stream = net.tcp.connect("google.com", 80);
stream.write("GET / HTTP/1.1\r\n\r\n");
```

### 11.7 `database` Modülü
**Önek:** `:db;`

Veritabanı işlemleri için `sqlite` ve `nosql` gruplarını içerir.

#### ** Grup: `db.sqlite` (İlişkisel Veritabanı)**

*   `connect(path) -> Connection`
*   `exec(conn, sql)`: SQL komutu çalıştır (CREATE, INSERT).
*   `query(conn, sql) -> Rows`: Veri sorgula (SELECT).
*   `close(conn)`

**Örnek:**
```oc
 con = db.sqlite.connect("kullanicilar.db");

// Tablo oluştur
db.sqlite.exec(con, "CREATE TABLE IF NOT EXISTS user (id INT, name TEXT)");

// Veri ekle
db.sqlite.exec(con, "INSERT INTO user VALUES (1, 'Ahmet')");

// Sorgula
 satirlar = db.sqlite.query(con, "SELECT * FROM user");
for (satir in satirlar) {
    echo("Kullanıcı: {satir['name']}");
}

db.sqlite.close(con);
```

#### ** Grup: `db.nosql` (Anahtar-Değer Deposu)**

Basit, gömülü (embedded) bir KV (Key-Value) veritabanı.

*   `open(path) -> Store`
*   `set(store, key, value)`
*   `get(store, key) -> Option<val>`
*   `del(store, key)`

**Örnek:**
```oc
 store = db.nosql.open("cache.dat");

db.nosql.set(store, "session_123", "{user_id: 5}");

 val = db.nosql.get(store, "session_123");
if val.is_some() {
    echo("Oturum Verisi: {val.unwrap()}");
}
```

### 11.8 `os` Modülü
**Açıklama:** İşletim sistemine özgü işlemler.

*   `args()`: Komut satırı argümanlarını dizi olarak verir.
*   `get_env(key)`, `set_env(key, val)`.
*   `cwd()`: Mevcut çalışma dizini.
*   `chdir(path)`, `mkdir(path)`, `rmdir(path)`.
*   `execute(cmd)`: Sistem komutu çalıştır.
*   `exit(code)`: Programdan çık.

**Örnek:**
```oc
 dosyalar = os.execute("ls -la");
 kullanici = os.get_env("USER").unwrap_or("Misafir");
```

### 11.9 `crypto` Modülü
**Açıklama:** Kriptografik işlemler.

*   `hash(algo, data)`: SHA256, MD5 vb.
*   `hmac(key, data)`.
*   `generate_key()`.
*   `encrypt(algo, key, data)`.
*   `decrypt(algo, key, data)`.

**Örnek:**
```oc
 sifre_hash = crypto.hash(crypto.SHA256, "gizlisifre123");
print("Hash: {sifre_hash}");
```

### 11.10 `time` Modülü
**Açıklama:** Zaman ve tarih.

*   `now()`: Unix timestamp.
*   `sleep(ms)`: Beklet.
*   `utc_now()`, `local_now()`: DateTime yapısı döner.
*   `format(dt, fmt_str)`.
*   `start()`, `end()`: Performans ölçümü.

**Örnek:**
```oc
 basla = time.start();
time.sleep(500); // 500ms uyu
 sure = time.end(basla);
echo("Geçen süre: {sure}");
```

### 11.11 `rand` Modülü
**Açıklama:** Rastgele sayı üretimi.

*   `new_rng()`: Standart üreteç.
*   `new_secure()`: Kriptografik güvenli üreteç.
*   `range_i32(rng, min, max)`.
*   `f64(rng)`: 0.0 - 1.0 arası.
*   `choice(rng, dizi)`.

**Örnek:**
```oc
 rng = rand.new_rng();
 zar = rand.range_i32(rng, 1, 7); // 1-6
```

### 11.12 `regex` Modülü
**Açıklama:** Düzenli ifadeler.

*   `compile(pattern)`.
*   `match(regex, text)`, `find(regex, text)`.
*   `replace(regex, text, new)`.
*   `Match` yapısı: `group(i)`, `start()`, `end()`.

**Örnek:**
```oc
 re = regex.compile("\\d+"); // Sayıları bul
 sonuc = re.find("Sipariş No: 12345");
if sonuc.is_some() {
    echo("Numara: {sonuc.text()}"); // 12345
}
```

### 11.13 `json` Modülü
**Açıklama:** JSON ayrıştırma ve oluşturma.

*   `parse(str) -> JsonValue`.
*   `stringify(val) -> str`.
*   `JsonValue` metotları: `get(key)`, `at(index)`, `as_str()`, `as_i32()`.

**Örnek:**
```oc
 veri = json.parse("{\"id\": 1, \"aktif\": true}").unwrap();
 id = veri.get("id").as_i32().unwrap();
```

### 11.14 `log` Modülü
**Açıklama:** Loglama sistemi.

*   Seviyeler: `DEBUG`, `INFO`, `WARN`, `ERROR`, `FATAL`.
*   Fonksiyonlar: `log.info(msg)`, `log.error(msg)` vb.
*   `set_level(lvl)`, `add_target(file)`.

**Örnek:**
```oc
log.set_level(log.WARN);
log.info("Bu görünmez");
log.error("Bu görünür");
```

### 11.15 `gpu` Modülü
**Açıklama:** GPU programlama arayüzü.

*   `select_device(id)`.
*   `compile_kernel(fn)`.
*   `alloc_array(dev, size)`, `to_gpu(arr)`, `from_gpu(arr)`.
*   `launch(kernel, grid, block, args...)`.
*   `sync(dev)`.

**GPU Kernel Fonksiyonu (`gpu`):**
Ekran kartı üzerinde paralel çalışacak fonksiyonlar.
```oc

gpu fn piksel_is(veri: u8[]) {
    // GPU kodu...
     id:u8 = 0;
    select_device(id);
}
```

### 11.16 `thread` Modülü
**Açıklama:** İş parçacığı yönetimi.

*   `spawn(fn)`: Yeni thread.
*   `join(handle)`: Bekle.
*   `Mutex`, `Semaphore`, `Channel` (Mesajlaşma kanalları).

**Örnek:**
```oc
thread.spawn(fn() {
    print("Arka planda çalışıyor...");
});
```

### 11.17 `ffi` Modülü (Foreign Function Interface)
**Açıklama:** C/C++ kütüphaneleriyle bağlantı.

*   `extern fn`: Dış fonksiyon tanımlama.
*   `#[link(name="lib")]`: Kütüphane bağlama.

**Örnek:**
```oc
#[link(name="m")]
extern fn sqrt(x: f64): f64;
// Kullanım: sqrt(16.0);
```

### 11.18 `error` Modülü
**Açıklama:** Hata türleri ve yönetimi. `Result` ve `Option` temel yapıtaşlarıdır.

*   `panic(msg)`: Programı durdur.
*   `unwrap()`, `unwrap_or(default)`.

### 11.19 `types` Modülü
**Açıklama:** İleri seviye tip dönüşümü, tip kontrolü ve güvenli/güvensiz dönüşüm araçları. Yerleşik `int()`/`float()` fonksiyonlarından daha detaylı kontrol sağlar.

*   **Güvenli Dönüşümler (Checked Casts):**
    *   `to_i32(val) -> Result<i32, Error>`: Hata kontrollü dönüşüm.
    *   `to_f64(val) -> Result<f64, Error>`
    *   `to_str(val) -> str`
    *   `parse<T>(str) -> Result<T, Error>`: Genel ayrıştırma.
*   **Tip Kontrolü (Runtime Type Info):**
    *   `is_type<T>(val) -> bool`: Değişkenin tipi o mu?
    *   `name_of(val) -> str`: Tip adını döndürür.
    *   `size_of<T>() -> i32`: Tipin boyutu.
*   **Güvensiz Dönüşümler (Unsafe Casts):**
    *   `as_type<T>(val)`: Zorla dönüştür (Veri kaybı olabilir).
    *   `cast<T>(val)`: Bitwise Reinterpret Cast (Ham bellek yorumlaması).

**Örnek:**
```oc
 s:str = "123";
if types.is_type<str>(s) {
     num = types.parse<i32>(s).unwrap();
}

// Bitwise dönüşüm (float -> int bitleri)
 f:f32 = 1.0;
 bits = types.cast<i32>(f);
```

### 11.20 `generics` (Jenerikler)
Dilin çekirdek özelliğidir. `<T>` sözdizimi ile kullanılır.

```oc
fn kutu_yap<T>(deger: T) { ... }
```

### 11.21 `vector` Modülü (Lineer Cebir & Vektör)
**Açıklama:** Oyun, fizik ve grafik programlama için Vektör ve Matris aritmetiği. `Vec2`, `Vec3`, `Vec4` tipleri üzerinde çalışır.

*   `dot(v1, v2)`: Nokta çarpımı (Skaler sonuç).
*   `cross(v1, v2)`: Çapraz çarpım (`Vec3`).
*   `normalize(v)`: Birim vektör yapar (Uzunluk = 1).
*   `access(v)`: Uzunluk (magnitude) hesaplar.
*   `distance(v1, v2)`: İki nokta arası mesafe.
*   `lerp(v1, v2, t)`: Lineer enterpolasyon (t: 0.0 - 1.0).
*   `rotate(v, axis, angle)`: Vektörü döndürür.

**Örnek:**
```oc
 v = Vec3[0, 1, 0];
 yon = vector.rotate(v, Vec3[0, 0, 1], 90.0);
```


### 11.23 `path` Modülü (Dosya Yolu İşlemleri)( bunu file modülü ile birleştir.)
**Açıklama:** Dosya yollarını işletim sisteminden bağımsız (Windows/Linux) yönetir.

*   `join(parts...)`: Yolları birleştirir (`/` veya `\` otomatik).
*   `ext(path)`: Dosya uzantısını verir.
*   `base(path)`: Dosya adını verir.
*   `dir(path)`: Klasör yolunu verir.
*   `abs(path)`: Mutlak (absolute) yolu verir.

**Örnek:**
```oc
 tam_yol = path.join(os.cwd(), "veri", "resim.png");
```


### 11.25 `encoding` Modülü
**Açıklama:** Veri kodlama ve sıkıştırma.

*   `base64_encode(data)`, `base64_decode(str)`.
*   `hex_encode(data)`, `hex_decode(str)`.
*   `compress(data, algo)`, `decompress(data, algo)`: (Gzip, Zlib).

### 11.26 `test` Modülü (Birim Test)
**Açıklama:** Yazılım kalitesi için test çerçevesi.

*   `assert_eq(a, b)`, `assert_ne(a, b)`.
*   `assert_true(cond)`.
*   `run_tests()`: Tüm testleri çalıştırır.

```oc
fn test_toplama() {
    test.assert_eq(2 + 2, 4);
}
```

---
**OmniCore Referans Dokümanı Sonu.**
