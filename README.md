# Hyper-V Üzerinden Sanal Anahtar (vSwitch) Oluşturma

Bu rehber, Hyper-V üzerinde pfSense gibi sanal makineler için gerekli olan WAN (Dış Ağ) ve LAN (Özel Ağ) sanal anahtarlarının nasıl oluşturulacağını ve yapılandırılacağını açıklar.

---

### 1. Sanal Anahtar Yöneticisine Erişim

Hyper-V Manager’ı açın ve sağ panelde yer alan **Sanal Anahtar Yöneticisi** seçeneğine tıklayın.



### 2. WAN (Dış Ağ) Anahtarı Oluşturma

1. **Yeni sanal ağ anahtarı** seçeneğine tıklayın.
2. **Dış (External)** seçeneğini seçin.
3. **Sanal Anahtar Oluştur** butonuna tıklayın.

**Ayarlar:**

- **Ad:** WAN-Switch (İstediğiniz bir ad olabilir)
- **Bağlantı türü:** Dış ağ
- **Ağ bağlantısı:** Fiziksel Ethernet veya Wi-Fi adaptörünüz
- **Yönetim işletim sisteminin bu ağ bağdaştırıcısını paylaşmasına izin ver:**  
  - Eğer WAN üzerinden IP ataması gerçekleşmiyorsa bu seçeneği devre dışı bırakın.

---

### 3. LAN (Özel Ağ) Anahtarı Oluşturma

1. **Yeni sanal ağ anahtarı** seçeneğine tıklayın.
2. **Özel (Private)** seçeneğini seçin.
3. **Sanal Anahtar Oluştur** butonuna tıklayın.

**Ayarlar:**

- **Ad:** LAN-Switch (İstediğiniz bir ad olabilir)
- **Bağlantı türü:** Özel ağ (yalnızca sanal makineler arasında iletişim)



### 4. Sanal Anahtarları Sanal Makineye Bağlama (pfSense Örneği)

pfSense kurulu sanal makineye sağ tıklayın, **Ayarlar** bölümüne girin ve aşağıdaki adımları izleyin:

**WAN İçin:**
- **Ağ Bağdaştırıcısı > Sanal anahtar:** WAN-Switch

**LAN İçin:**
- Yeni bir **ağ bağdaştırıcısı** ekleyin.
- **Sanal anahtar:** LAN-Switch



### 5. Neden Bu Yapılandırma?

- **WAN:** Gerçek ağ ile iletişim için gereklidir (örneğin internet erişimi).
- **LAN:** İzole bir ağ ortamı sağlar, istemciler bu ağ üzerinden bağlanır.
- **pfSense:** Bu iki ağ arasında çalışan, yönlendirme ve güvenlik kontrollerini gerçekleştiren ağ geçididir.



### Notlar

- Her değişiklik sonrası sanal makineyi yeniden başlatmanız gerekebilir.
- WAN tarafı doğru yapılandırılmazsa pfSense dış dünyaya erişemez.

---

## PfSense Kurulumu

**pfSense**, FreeBSD tabanlı açık kaynaklı bir güvenlik duvarı ve yönlendirici (router) çözümüdür. Kurumsal düzeyde ağ güvenliği, trafik yönetimi ve bağlantı kontrolü sağlar. Hyper-V üzerinde sanal bir makineye kurulabilir ve hem WAN hem LAN sanal anahtarlarıyla çalıştırılabilir.

pfSense ile yapılabilecek başlıca işlemler:

- **Güvenlik duvarı kuralları (Firewall Rules):** Gelen ve giden trafiği kontrol etme
- **Port yönlendirme (Port Forwarding):** Belirli iç IP'lere gelen dış bağlantıları yönlendirme
- **DHCP sunucusu:** Yerel ağdaki istemcilere otomatik IP dağıtımı
- **DNS yönlendirme / çözümleme**
- **VPN sunucusu:** Uzak erişim ve güvenli bağlantılar için OpenVPN gibi çözümler
- **Trafik şekillendirme (Traffic Shaping):** Ağ bant genişliğini belirli uygulamalara göre önceliklendirme
- **VLAN desteği:** Fiziksel olarak aynı ağda sanal olarak ayrı ağlar tanımlama

---

### Hyper-V Üzerinde pfSense Kurulumu İçin Ön Hazırlık

1. Daha önce oluşturduğunuz **WAN-Switch** ve **LAN-Switch** sanal anahtarlarının hazır olduğundan emin olun.
2. [https://www.pfsense.org/download/](https://www.pfsense.org/download/) adresinden pfSense ISO dosyasını indirin.
3. Hyper-V’de yeni bir sanal makine oluşturun:
   - Generation: 1
   - RAM: En az 1 GB
   - Ağ Bağlantıları:
     - İlk ağ bağdaştırıcısı → WAN-Switch
     - İkinci ağ bağdaştırıcısı → LAN-Switch
   - ISO dosyasını sanal DVD sürücüsüne bağlayın.

---

### pfSense Kurulumu Adımları (Genel)

**ISO ile sanal makineyi başlatın.**
PfSense, ilk açılım

![resim1](https://github.com/user-attachments/assets/2192c375-dba0-4e54-8127-50750f48f61b)


**Klavye dilini şeçtikten sonra *Auto (ZFS)* seçiyoruz.**

![resim2](https://github.com/user-attachments/assets/2e3a2677-a175-4c05-a516-1bb39d195678)



**Bundan sonra 2 adım varsayılan ayarlar ile.**

![resim4](https://github.com/user-attachments/assets/c6e26b43-ba81-4245-a51b-af38a9d3dc31)

![resim5](https://github.com/user-attachments/assets/3fceff31-d1a4-46a3-8995-2719a7491fe2)


**Space'e basarak disk'i seçiyoruz.**

![resim6](https://github.com/user-attachments/assets/6cd021cc-ab17-4f04-bc11-22a2f2e76b0d)



Kurulumdan sonra yeniden başlatıyoruz.
Kurulumdan sonra otomatik giriş yapacaktır.

---

### Varsayılan Giriş Bilgileri:

- **Kullanıcı adı:** `admin`
- **Şifre:** `pfsense`

İlk girişte sizden yeni bir şifre belirlemeniz istenir.

---

### Kurulumdan Sonra Yapılabilecekler

- Web arayüzü üzerinden ağ kurallarını yapılandırma
- WAN IP’si alıp almadığını kontrol etme
- İç ağa bağlı istemciler için DHCP sunucusunu etkinleştirme
- Firewall kuralları yazarak dışa açık portları belirleme







Windows Server kurulu sunucuya:
- **Sağ tık > Ayarlar > Ağ Bağdaştırıcısı > (WAN AĞI)**

Windows Server'ı başlatıp http://192.168.1.1/ adresinden web arayüzüne girinebilir.


Yönetim panelin Local IP değiştirme:

Interfaces > LAN > IPv4 Address : {Belirlenecek IP adresi. Örnek: 192.168.10.1}

![resim7](https://github.com/user-attachments/assets/c3cb22ea-2448-478b-8f0d-7b03b5b50099)


# AD-DNS-DHCP

Sunucunun IPv4 adresini değiştirmemiz gerek. Bunun için:

Sistem tepsisindeki ağ simgesine sağ tık > "Open Network & Internet settings" > "Change adapter options" > Enternet sağ tık > "Properties" > (TCP/IPv4) : Diyerek IP'yi ayarlıyoruz.

![resim8](https://github.com/user-attachments/assets/58fdfa5a-31a1-470a-9f06-ab7bcbaf50e9)


Bağlantının gelmesi biraz sürebilir. "ipconfig" yaparak test ediniz.

Sunucu rollerini yükleme:

Server Manager > Add roles and features > Sunucuyu seçin(otomatik seçili gelir genellikle) > Aşağıdaki rolleri seçin:

- ✅ Active Directory Domain Services
- ✅ DNS Server
- ✅ DHCP Server

Sonrada Install diyerek yükleyiniz. Bazı eklentiler isteyebilir onlarıda yüklemeniz gerek. Yükleme bitince sunucuyu yeniden başlatın.

Domain Yapılandırma:

Yeniden başlatmadan sonra **Server Manager'dan uyarı gelecek**: "Promote this server to a domain controller" ona tıklayalım.

Add a new forest, **root domain**: "staj.local" > DSRM (Directory Services Restore Mode) için parola belirleyin. > 4 adımı "**Next** diyerek geçin > **Install**

Kendini otomatik yeniden başlatacaktır. Başlatıktan sonra eklediğiniz domain ile giriş yapacaktır. Administrator ve giriş şifresi girerek giriş yapılabilir.

DHCP Ayarlama:

Server Manager > Tools > DHCP

DHCP > (Sunucu) > IPv4 sağ tıkla > New Scope

Örnek Scope:
- İsim: İstediğiniz isim
- Açıklama: İstediğiniz açıklama
- Başlangıç IP: 192.168.10.51
- Bitiş IP: 192.168.10.200
- Alt Ağ Maskesi: 255.255.255.0
- Ağ Geçidi: 192.168.10.1 (pfsense)
- DNS Sunucusu: 192.168.10.2
- Domain: staj.local


Clientları ayarlama:

Client sunucuyu açtıktan sonra IP atamasında DNS Sunucusunu, önceki sunucunun IP'si olarak ayarlıyoruz.

![resim9](https://github.com/user-attachments/assets/d1d4b841-ec6e-47b9-b292-788600f6ed0d)


Ayarlar > Sistem > Hakkında > Yeniden adlandır > client1/client2

Sonra yeniden başlatın.

Domain'e katılma:

Denetim Masası > Sistem > Bilgisayar adı > Değiştir > alan adınız.

Sonra yeniden başlatın. Yeniden başlatıktan sonra ping atarak bağlantı kontrolü yapalım:

"ping {sunucunun adı}" Eğer sunucu adını bilmiyorsanız "hostname" komutu ile öğrenebilirsiniz.

# IIS (Web Server), SQL Server kurma ve dışarıdan bağlantı kurma

2. Sunucuya giriş yapıp "Active Directory" aktif edip.

Server Manager > Manage > Add Roles and Features > Web Server (IIS)

NOT: PfSense üzerinden HTTP portu açmayı unutmayın.

SQL Sunucusu Kurma:

SQL Server 2019 Express: https://www.microsoft.com/en-in/download/details.aspx?id=101064

Kurulum yaptıktan sonra SQL Server Management Studio'ı açınız.
"Windows Authentication" ile sunucu içinden giriş yapın.

Varsayılan olarak "Object Explorer" açılacak ancak açılmaz ise, F8 ya da View > Object Explorer ile açabilirsiniz.

Security > Logins > Sağ Tık > New Login

Login Name: İstenilen İsim
"SQL authentication" seçili olacak.
Password: İstenilen şifre (Güçlü şifre yapın, yoksa hata verip kabul etmez.)

NOT: "1433" Port'a izin verilmesi gerektiğini unutmayın.



