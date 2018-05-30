#  C - Socket

> Soketler iki yönlü haberleşme aygıtlarıdır. Aynı makinedeki farklı süreçler
arasında ya da uzak makinelerdeki süreçlerin haberleşmesinde kullanılabilir.
Uzak makinelerdeki süreçler arasında da haberleşmeyi olanaklı kılan tek
yöntemdir.

> Socket olusturulurken 3 parametre verilmesi gerekir.
- Haberlesme Stili
- Namespace
- Protocol

``Haberlesme stili`` socketin iletilen veriye nasil davranacagini soyler ve taraflar arasinda paket iletiminin nasil gerceklestirilecegini soyler.

``Namespace`` soket adreslerinin nasil yazilacagini soyler. Ornegin local namespace icin soket adresleri dosya isimleri iken, internet namespaceler icin soket adresleri, makinanin bagli oldugu agin **IP** adresi ve **PORT** numarasidir.

``Protocol`` Verinin nasil iletilecegini soyler.

* socket socket – soket oluşturur.
* close – soketi yok eder.
* connect – iki soket arasında bağlantı oluşturur.
* bind – sunucu soketini bir adres ile etiketler.
* listen – bir soketi durumları bekleyecek şekilde konfigüre eder.
* accept – bağlantıyı kabul eder ve bu bağlantı için yeni bir soket

> Namespace parametresi için ``PF_`` (Protocol Families’in kısaltılması)
öneki ile başlayan sabitler kullanılır. ``PF_LOCAL`` veya ``PF_UNIX`` lokal
namespace’i tanımlarken, PF_INET Internet namespace’inin
kullanılacağını gösterir.

> Haberleşme stili parametresi için ``SOCK_`` öneki ile başlayan sabitler
kullanılır. ``SOCK_STREAM`` *TCP tabanlı haberleşme stilini tanımlarken,
``SOCK_DGRAM`` *UDP tabanlı haberleşme stilini ifade eder

> Protokol ise veriyi iletme ve almak için düşük
seviyeli mekanizmayı belirtir. Genellikle 0 olarak kullanımı tercih edilir.

Soket çağrısının başarılı olması durumunda soket için olan bir *dosya
tanımlayıcısı* geri döner. **read**, **write** komutları kullanılarak sokete
yazılabilir ya da okunabilir. Soket ’in kullanımı sona erdiğinde ise *close*
komutu ile yok edilir.

### Local Soketler

Aynı bilgisayardaki süreçleri bağlayan soketler, **PF_LOCAL** ve
**PF_UNIX** sabitleri ile beraber lokal namespace’i kullanırlar

Soketin ismi **sockaddr_in** yapısında belirtilir.
Sun_family alanı **AF_LOCAL** olarak tanımlanır. Bu sabit değer soketin lokal olduğunu gösterir.

## Uygulama

### ``Client tarafı;``

**1- Soketin oluşturulması.** socket metodu kullanılarak oluşturulacaktır.
Namespace, haberleşme stili, ve protokol parametreleri kullanılarak çağrısı
yapılmalıdır. Lokal namespace,

```
socket (PF_LOCAL, SOCK_STREAM, 0);
```
**2- Soketin adres bilgisinin alınması.**

```
name.sun_family = AF_LOCAL;
strcpy (name.sun_path, socket_name);
```

**3-Soket bağlantısı kur. connect metodu kullanılarak bağlantı
sağlanacaktır.**

```
connect (socket_fd,(struct sockaddr *) &name, SUN_LEN
(&name));
```

**4- Komut satırındaki mesajı sokete yaz.**

```
write_text (socket_fd, message);
```
**5- Bağlantıyı sonlandır. close metodu kullanarak gerçekleştirilecektir.**

```
close (socket_fd);
```

### ``Server tarafı;``

**1-Soketin oluşturulması**

```
 socket_fd = socket (PF_LOCAL, SOCK_STREAM, 0);
```
**2-Soketin server soket olduğunun belirtilmesi.**

```
name.sun_family = AF_LOCAL;
strcpy (name.sun_path, socket_name);
bind (socket_fd,(struct sockaddr *) &name, SUN_LEN
(&name));
```

**3-Bağlantıların dinlenmesi**

```
 listen (socket_fd, 5);
```

**4-Bir Client dan “quit” mesajı gelene kadar serverın bağlantıları kabul
etmesi.**

```
struct sockaddr_un client_name;
socklen_t client_name_len;
int client_socket_fd;
int addrlen;
```
Tanımlarına göre
* Bağlantının kabul edilmesi

```
client_socket_fd = accept (socket_fd,(struct sockaddr
*) &client_name, &addrlen);
```

**5- Soketin kapatılması.**

```
close (socket_fd);
unlink (socket_name);
```

## NOT

Clinet da **connect** Server da **listen** ve **accept** komutlari ``SOCK_DGRAM`` 'de kullanilmasi zorunlu degildir.

**send** ve **sendto** veri göndermeye, **recv** ve **recvfrom** veri almaya yarayan fonksiyonlardır

send ve recv connect ile bağlanılan adreslerle çalışırlar. sendto ve recvfrom fonksiyonları parametre olarak adres de alırlar.

**SOCK_STREAM** ile send ve recv fonksiyonları kullanılır.
**SOCK_DGRAM** ile çalışılırken connect ile bağlatnı yapıldıysa send ve recv kullanılabilir, onun dışında sendto ve recvfrom kullanılarak adres belirtilmelidir.
