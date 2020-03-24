---
layout: post
title: Pipe Kullanım Örneği
# subtitle:
blog: true
categories: [linux]
tags: [pipe]
# level:
# image: /img/hello_world.jpeg
# bigimg: /img/path.jpg
# tags: [books, shakespeare, test]
# gh-repo:
# gh-badge: [star, fork, follow]
comments: false
---

Linux'te veri akışları pipe kullanılarak manipüle edilebilirler. Pipe kullanımıyla ilgili internet üzerinde çok çeşitli bilgiler bulunduğu için pipe hakkında genel bilgiler vermeden direk olarak örnek üzerinde işlemler yapmaya çalıştım. Bu sebeple bir çok kaynaktan pipe hakkında daha detaylı bilgiler kontrol edilebilir.

[Mockaroo](https://mockaroo.com/) web sitesinden 1000 satırlı csv formatında dosya indirdim ve üzerinde pipe kullanarak örnekler yapmaya çalışacağım.


![mock data listesi](/resource/image/2020-03-10-pipe/image1.jpg){:class="img-responsive"}

Yapmaya çalışacağımız örnekler:
- Cinsiyeti erkek olanları listeleyecegiz.
- Listede bulunan erkeklerin ip adreslerinin lokasyonlarına göre sıralama yapacağız.
- ip adresi türkiye'de bulanan kişileri listeliyeceğiz.


grep ile dosyamızın içerinde Male geçen satırları alıyoruz. Burada dikkat etmemiz gereken grep komutuna girdiğimiz argumanın diğer bilgileri içerisinde bulunmaması gerekiyor . `grep Male MOCK_DATA.csv` olarak çalıştırdığımda soyadı Maleham olan bir bayanda listeleniyor. Bu sebeple `grep ';Male;' MOCK_DATA.csv` yaptığımız zaman diğer bilgilerde ;Male; olarak geçen bayan yoksa doğru listeleme yaparız. `awk '$5 ==Male'` olarak istediğimiz sutunda geçen filtrelemeyi daha doğru yapabiliriz. Sadece ip adresleri sütununu almak istersek `awk -F ";" '{ if($5 == "Male") print $6;}' MOCK_DATA.csv | head -n 5` şeklinde yazabiliriz.

![mock data listesi](/resource/image/2020-03-10-pipe/image2.jpg){:class="img-responsive"}

awk aracalığıyla ip adresi sütununu alıyoruz `-F ";"` (field sepearator) sütünlar arasındaki ayracın " ; " olduğunu belirtiyoruz. `awk -F ";" '{print $6}'`

![mock data listesi](/resource/image/2020-03-10-pipe/image3.jpg){:class="img-responsive"}

ip adreslerini sıralamak için sort kullanıyoruz. Field seperator olarak " . " kullanıyoruz. `-t.` Sütunları `-k --key` olarak belirtiyoruz. `k1,1n` 1. öncelik ve `n` sayı olarak sıralama yaptırıyoruz.

`sort -k1,1n -k2,2n -k3.3n -k4.4n -t.`

![mock data listesi](/resource/image/2020-03-10-pipe/image4.jpg){:class="img-responsive"}

Sıralamasını yaptığımız ip adrelerinin lokasyonlarına bakmak için **geoiplookup** kullanarak sorgulayacagız. Kullanım şekli `geoiplookup 195.175.39.39`'dir çıkan output **GeoIP Country Edition: TR, Turkey**.  Çıktının sadece ülke bölümünü almak istediğimiz için `cut -d: -f 2` noktalı virgülü field seperator olarak kullanıyoruz ve 2. bölümü alıyoruz. xargs gelen girdileri kendisinden sonraki programa argüman olarak vermesi için kullanıyoruz.

`awk '{print $1} {system("geoiplookup " $1 " | cut -d: -f 2 | xargs")}'`

![mock data listesi](/resource/image/2020-03-10-pipe/image5.jpg){:class="img-responsive"}

Ülkelere göre gruplandırarak hangi ülkeden kaç tane ip adresi bulundugunu listeliyoruz.

`grep ';Male;' MOCK_DATA.csv| awk -F ";" '{print $6}'| sort -k1,1n -k2,2n -k3.3n -k4.4n -t. | awk '{print $1} {system("geoiplookup " $1 " | cut -d: -f 2 | xargs")}' | grep '^[A-Z]' | sort | uniq -c | sort -n -r | head -n 5`

![mock data listesi](/resource/image/2020-03-10-pipe/image6.jpg){:class="img-responsive"}

yada

`awk -F ";" '{ if($5 == "Male") print $6;}' MOCK_DATA.csv| sort -k1,1n -k2,2n -k3.3n -k4.4n -t. | awk '{print $1} {system("geoiplookup " $1 " | cut -d: -f 2 | xargs")}' | grep '^[A-Z]' | sort | uniq -c | sort -n -r | head -n 5`

![mock data listesi](/resource/image/2020-03-10-pipe/image7.jpg){:class="img-responsive"}

Ip lokasyonu Türkiye olan kişileri listelemek için aşağıdaki komutu çalıştırıyoruz. MOCK_DATA.csv dosyası içerinde header bilgisini almamak için önce dosyanın satır sayısını buluyor ve tail komutuya header hariç bilgileri alıyoruz. Sonra geoiplookup ile lokasyonları buluyoruz ve grep ile TR olan satırları listeliyoruz.

`tail -n $(($(cat MOCK_DATA.csv| wc -l) -1)) MOCK_DATA.csv| awk -F ";" '{printf $2 " " $3 " " $6 " " } {system("geoiplookup " $6 " | cut -d: -f 2 | xargs")}' | grep "TR"`

![mock data listesi](/resource/image/2020-03-10-pipe/image8.jpg){:class="img-responsive"}


**Kaynaklar:**

Data dosyasını indirdiğim web sitesi [Mock Data](https://mockaroo.com/schemas/download)

Linux hakkında izlemesi çok keyifli sunumlara aşağıdaki linkler üzerinden ulaşabilirsiniz. Pipe konusunda güzel örnekler veriyor.

[İstanbul Coders - Linux ile İlgili Bilmemiz Gerekenler 1. Bolum ](https://www.youtube.com/watch?v=qV_k7nPtelE)

[İstanbul Coders - Linux ile İlgili Bilmemiz Gerekenler 2. Bolum](https://www.youtube.com/watch?v=eL1EeCBwEwM)

Linkte bulunan kitabın 4. bölümünde pipe hakkında konu anlatımı var ve email server üzerinde tutulan log kayıtlarından filtreleme yapmayla ilgili güzel bir örnek var.

[The Linux Philosophy for SysAdmins: And Everyone Who Wants To Be One](https://www.apress.com/gp/book/9781484237298)
