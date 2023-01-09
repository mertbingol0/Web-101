## HTTP (Hyper Text Transfer Protokol) Nedir?</h2>
			
<p>
       HTTP, client tarafindan yapilan requesti, serverin anlayabilecegi sekilde parse edip, 
        duzenleyip ve paketleyip istenilen islemi server'a iletebilmemizi saglayan bir protokoldur. 
        Protkol ise kisaca bir iletisimdir diyebiliriz.
        Ornegin, benim suanda sizlere buradan bir seyler anlatiyor olusuma bir iletisim yani protokol diyebiliriz.
        Sizler de burada anlattiklarimi farkinda olmadan parse edip (parcalara ayirip) kavrayip, anliyorsunuz.
        Yani sizlerle "konusma" protokolunu kullanarak iletisimde bulunuyorum diyebiliriz.
        Web servisleri de bu protokolu kullanarak aralarinda iletisimde bulunurlar. Peki bu iletisimin teknik detaylari nelerdir?
</p

			
<p>HTTP protokolu, 
talep edilen istegin parse isleminin sonucunda olusturulan pakettir demistik. Burada ise bu parsing islemine deginecegiz,
ilk olarak parse edilen verilere bakalim.</p>
<p>HTTP Hader'i Request bilgileri'nin bulundugu kisimdir. Bu bilgi sayesinde client,
   nereye hangi metodu kullanarak request atilacagi bilgisini web servisine iletir ve arka tarafta calisan web servisi, bu bilgi uzerine islemlerini yapar ve request
sonucunu client'a doner. Bu islemede HTTP Response denir.</p>

<p>Ornek bir HTTP header yapisi su sekildedir: (Tabi programcinin yazis sekline gore bir cok ek bilgi daha olabilir, lakin suan da soz ettigim kisimlar kesinlikle bir http requesti icerisinde olmasi gereken kisimlardir.</p>
			
```
GET /index.html HTTP/1.1
Host: mertbingol.online
Accept: text/html
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36
```
Temel olarak bir HTTP Header'i 4 ana kisimdan olusur. Bu kisimlarin aciklamalarini asagida yazdim:
| Section | Description |
| --- | --- |
| Request (GET) | Bu kisim HTTP headeri'nin ilk satiridir ve burada nereye, ne sekilde request atilacagi bilgisi bulunur. Ornegin yukarida verdigim ornek de index.html'e bir GET requesti atilmis, (Bu Request methodlarina birazdan deginecegim) |
| Host: | Bu kisim, isteğin hangi sunucuya yönlendirileceğini belirtir. Yani domain'in karsilik geldigi ip adresini ogrenir. HTTP'nin bu bilgiye ihtiyaci vardir, cunku baska turlu ilgili web servisi ile iletisimde bulunamaz. |
| Accept | Bu kisim, requestimizin hangi türde veri talep ettigini belirtir. Örneğin, "application/json" veya "text/html" gibi. (Request yapilan, istenen veririnin tipi) |
| User-Agent: | Bu kisim, requestin yapildigi (uretildigi) browser, cihaz adi ve/veya sürümünü belirtir. Kisaca Request'in uretildigi cihaz hakkinda bir takim verilerin bulundugu kisimdir. |

<p>

</p>

### HTTP Status Codes:
<li>1xx: "Request henuz islenmeye devam." ediyor gibi bir tanim yapilabilir.</li>
<li>2xx: OK durumu, yani http protokolunun basarili bir sekilde request atilabildigini ifade eder. (requestin responseun'da bulunur). Ornek: <code>HTTP/1.1 200 OK</code> </li>
<li>3xx: Yoneldirme durumudur, request attiginiz adres farkli bir adresi temsil ediyor olabilir. Bu gibi durumlarda "yonlendime" dedigimiz durum yasanir ve requestiniz yonlendirilen adrese gider. (Ornegin, onebyte.io domaini 1byte.io'a yonlendirilmis.)</li>
<li>4xx: Requestinizde bir sorun oldugunda bu durum kodu ile karsilasirsiniz. Ornegin, bir sayfaya erisemediginizde "404 error" hatasi ile karsilarisiniz. Aslinda bu bir durum kodudur ve genelde client'in internet problemlerinde bu durumla karilasilir.</li>
<li>5xx: Bu ise 4xx error'dan farkli olarak, requestiniz dogru olsa dahi server tarafinda bir sorun oldugunda karsilasacaginiz bir HTTP durumudur. </li>
<br>
<p>Tum HTTP durum kodlarinin listesi: https://tr.wikipedia.org/wiki/HTTP_durum_kodlar%C4%B1</p>

