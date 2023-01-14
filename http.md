## HTTP (Hyper Text Transfer Protokol)</h2>
			
<p>
       HTTP, client tarafından yapılan requesti, serverin anlayabileceği şekilde parse edip, düzenleyip ve paketleyip istenilen işlemi server'a iletebilmemizi sağlayan bir protokoldur. Protokol, kısaca bir iletişimdir diyebiliriz. Örneğin, benim şuanda sizlere buradan bir şeyler anlatıyor olusuma bir iletişim yani protokol diyebiliriz. Sizler de burada anlattıklarımı farkında olmadan parse edip (parçalara ayırarak) kavrayıp, anlıyorsunuz. Yani sizlerle "konuşma" protokolunu kullanarak iletişimde bulunuyorum diyebiliriz. Web servisleri de bu protokolu kullanarak aralarında iletişimde bulunurlar. Peki bu iletişimin teknik detayları nelerdir?
</p

			
<p>HTTP protokolu, talep edilen isteğin parse işleminin sonucunda oluşturulan pakettir demiştik. Burada ise bu parsing işlemine değineceğiz. İlk olarak parse edilen verilere bakalım.</p>
<p>HTTP header, request bilgilerinin bulunduğu kısımdır. Bu bilgi sayesinde client, nereye hangi metodu kullanarak request atılacağı bilgisini web servisine iletir ve arka tarafta çalışan web servisi, bu bilgi üzerine işlemlerini yapar ve request sonucunu client'a döner. Bu işleme de HTTP response denir.</p>

<p>Ornek bir HTTP header yapısı şu şekildedir: Tabii programcının yazı şekline göre birçok ek bilgi daha olabilir, lakin şuan da söz ettiğim kısımlar kesinlikle bir http requesti içerisinde olması gereken kısımlardır.</p>
			
<pre>
GET /index.html HTTP/1.1
Host: mertbingol.online
Accept: text/html
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36
</pre>
Temel olarak bir HTTP header'i 4 ana kısımdan oluşur. Bu kısımların açıklamalarını aşağıda yazdım:
| Section | Description |
| --- | --- |
| Request (GET) | Bu kısım HTTP header'inin ilk satırıdır ve burada nereye, ne şekilde request atılacağı bilgisi bulunur. Örneğin yukarıda verdiğim örnekte index.html'e bir GET requesti atılmış (Bu request metodlarına birazdan değineceğim).|
| Host: | Bu kısım, isteğin hangi sunucuya yönlendirileceğini belirtir. Yani domain'in karşılık geldiği IP adresini öğrenir. HTTP'nin bu bilgiye ihtiyacı vardır çünkü başka türlü ilgili web servisi ile iletişimde bulunamaz. |
| Accept | Bu kısım, requestimizin hangi türde veri talep ettiğini belirtir. Örneğin, "application/json" veya "text/html" gibi. (Request yapılan, istenen verinin tipi) |
| User-Agent: | Bu kısım, requestin yapıldığı (üretildiği) browser, cihaz adı ve/veya sürümünü belirtir. Kısaca request'in üretildiği cihaz hakkında bir takım verilerin bulunduğu kısımdır. |

<p>

</p>
<hr>

### HTTP Status Codes:
<li>1xx: "Request henüz işlenmeye devam ediyor" gibi bir tanım yapılabilir.</li>
<li>2xx: OK durumu, yani http protokolünün başarılı bir şekilde request atılabildiğini ifade eder. (requestin response'unda bulunur). Örnek: <code>HTTP/1.1 200 OK</code></li>
<li>3xx: Yönlendirme durumudur, request attığınız adres farklı bir adresi temsil ediyor olabilir. Bu gibi durumlarda "yönlendirme" dediğimiz durum yaşanır ve requestiniz yönlendirilen adrese gider. (Örneğin, onebyte.io domaini 1byte.io'ya yönlendirilmiş.)</li>
<li>4xx: Requestinizde bir sorun olduğunda bu durum kodu ile karşılaşırsınız. Örneğin, bir sayfaya erişemediğinizde "404 error" hatası ile karşılaşırsınız. Aslında bu bir durum kodudur ve genelde client'in internet problemlerinde bu durumla karşılaşılır.</li>
<li>5xx: Bu ise 4xx error'dan farklı olarak, requestiniz doğru olsa dahi server tarafında bir sorun olduğunda karşılaşacağınız bir HTTP durumudur.</li>

<br>
<p>Tum HTTP durum kodlarinin listesi: https://tr.wikipedia.org/wiki/HTTP_durum_kodlar%C4%B1</p>
<hr>

### HTTP Request Methods

### GET (Client)
<p>GET methodu, server'dan bir veri isteğinde bulunduğumuzda kullanılan bir request methodudur. Örneğin herhangi bir sitede bir sayfaya tıklarsanız server'a "bana bu sayfayı getir" isteğinde bulunduğunuzdan dolayı HTTP protokolü GET methodunu kullanacaktır. Yani GET methodu server'dan herhangi bir veri talebinde bulunduğumuzda kullanılan bir methoddur. Ornek olarak asagida bloguma yapilan bir istegin headerini koydum. Bu requestte serverdan bir .html dosyasini bana sunmasini istedim bu sebeple GET methodu kullanildi.</p>
<img src="https://github.com/mertbingol0/Web-TEchLEarn/blob/main/github'a%20koy%20baba.png"></img>

### GET (Server)
Şimdi yukarıda verdiğim örnekteki request'in server tarafında nasıl işlendiğine bakalım. Örneklerimi herkesin anlayabileceği bir şekilde olması açısından ve Python'a olan hakimliğimden dolayı, Python programlama dili üzerinde Flask kütüphanesini kullanarak yapacağım.

```python
from flask import Flask, request, render_template

app = Flask(__name__)

@app.route('/blog', methods = ['GET'])
def example_request():
    return render_template('blog.html')


if __name__ == '__main__':
    app.run(debug=True)
```
Burada @app.route() yardımı ile URL üzerinde "/blog" sayfasına bir request atıldığında kullanıcıyı "/blog" sayfasına yönlendirmesini söylüyoruz. Bu işleme endpoint denir. Ardından methods = ['GET'] kısmında ise bu endpointimize yapılan request methodunun sadece GET olması gerektiğini söylüyoruz. Böylece bu sayfaya sadece GET requesti atıldığında kabul edecek ve işlemlerini yapacak. Tabii eğer istersek bir sayfaya iki farklı request methodu atılabilmesini sağlayabiliriz.

Örnek:

```python
@app.route('/blog', methods = ['GET', 'POST'])
```
Devam edelim. Kısaca client tarafından gelen request methodu GET ise example_request() fonksiyonumuz çalıştırılacak ve render_template('blog.html') ile birlikte "blog.html" dosyası "/blog" sayfasına requestte bulunan client'a sunulacak.

Not: Eğer developer bir sayfaya sadece post requesti atılabilmesini sağlarsa ve biz o sayfaya bir GET requesti atarsak "Method Not Allowed" hatası ile karşılaşırız. Ve hemen altında da "The method is not allowed for the requested URL." böyle bir bildiri yazar. (Bu adres için bu method'a izin verilmiyor gibi çevirilebilir)

<img src="https://github.com/mertbingol0/Web-TEchLEarn/blob/main/Screenshot%20from%202023-01-14%2001-18-01.png"></img>



