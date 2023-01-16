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

#### GET on Client Side
<p>GET methodu, server'dan bir veri isteğinde bulunduğumuzda kullanılan bir request methodudur. Örneğin herhangi bir sitede bir sayfaya tıklarsanız server'a "bana bu sayfayı getir" isteğinde bulunduğunuzdan dolayı HTTP protokolü GET methodunu kullanacaktır. Yani GET methodu server'dan herhangi bir veri talebinde bulunduğumuzda kullanılan bir methoddur. Ornek olarak asagida bloguma yapilan bir istegin headerini koydum. Bu requestte serverdan bir .html dosyasini bana sunmasini istedim bu sebeple GET methodu kullanildi.</p>
<img src="https://github.com/mertbingol0/Web-TEchLEarn/blob/main/github'a%20koy%20baba.png"></img>

#### GET on Server Side
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
Burada ```@app.route()``` yardımı ile URL üzerinde "/blog" sayfasına bir request atıldığında kullanıcıyı "/blog" sayfasına yönlendirmesini söylüyoruz. Bu işleme endpoint denir. Ardından ```methods = ['GET']``` kısmında ise bu endpointimize yapılan request methodunun sadece GET olması gerektiğini söylüyoruz. Böylece bu sayfaya sadece GET requesti atıldığında kabul edecek ve işlemlerini yapacak. Tabii eğer istersek bir sayfaya iki farklı request methodu atılabilmesini sağlayabiliriz.

Örnek:

```python
@app.route('/blog', methods = ['GET', 'POST'])
```
Devam edelim. Kısaca client tarafından gelen request methodu GET ise ```example_request()``` fonksiyonumuz çalıştırılacak ve ```render_template('blog.html')``` ile birlikte "blog.html" dosyası "/blog" sayfasına requestte bulunan client'a sunulacak.

Not: Eğer developer bir sayfaya sadece post requesti atılabilmesini sağlarsa ve biz o sayfaya bir GET requesti atarsak "Method Not Allowed" hatası ile karşılaşırız. Ve hemen altında da "The method is not allowed for the requested URL." böyle bir bildiri yazar. (Bu adres için bu method'a izin verilmiyor gibi çevirilebilir)

<img src="https://github.com/mertbingol0/Web-TEchLEarn/blob/main/Screenshot%20from%202023-01-14%2001-18-01.png"></img>

<hr>

#### POST on Client Side

POST request'i, server'a bir veri göndermek istediğimizde kullandığımız request methodudur. Örneğin bir siteye fotoğraf yüklemek istersek, bu fotoğraf yükleme işlemi POST request'i ile yapılır. Sizler fotoğrafınızı yüklediğinizde, server'a "Sana bir fotoğraf gönderiyorum" diyorsunuz ve server'da o fotoğrafınızı alıp, gereken işlemi gerçekleştiriyor. Örneğin, verdiğiniz fotoğraf bir profil fotoğrafı olabilir ve bu profil fotoğrafını profilinize eklemek için bir takım işlemler gerçekleştirir. Tabii ki, post methodu sadece fotoğraf yükleme gibi basit işler haricinde, çok büyük şeyler içinde kullanılabilir. 

Söz ettiğim fotoğraf yükleme işlemi için Instagram üzerinden bir örnek:

<img src="https://github.com/mertbingol0/Web-TEchLEarn/blob/main/Screenshot%20from%202023-01-16%2001-06-53.png"></img>

Büyük işlere örnek olarak, bir kullanıcı sitenizin kayıt bölümündeki formları doldurduktan sonra girdiği bilgileri size gönderdiği anda, o veriler frontend tarafında JSON verilerine dönüştürülerek backend'e gönderilir. Ve backend bu verileri alıp kullanıcıyı veritabanına kaydeder. Yani yukarıda da dediğim gibi, server'a bir veri göndermemiz gerektiği durumlarda POST methodunu kullanmamız gerekir. Aşağıda örnek bir POST requesti verdim, ancak front-end bilgim olmadığından, client tarafını terminal üzerinden uygulayacağım. Bu işin asli frontend developar'i tarafindan hallediliyor (<a href="https://stackoverflow.com/questions/29775797/fetch-post-json-data">https://stackoverflow.com/questions/29775797/fetch-post-json-data</a>).

```
curl -X POST -H "Content-Type: application/json" -d '{"name":"Mert","surname":"Bingol"}' http://194.233.166.90:8889/users/create
```

Bu komut, "Content-Type" header'ını "application/json" olarak ayarlayarak, "name" ve "surname" alanlarını içeren bir JSON verisi gönderir. "Content-Type" header'ını "application/json" olarak ayarlar çünkü göndereceğimiz veri JSON türündedir. (https://blog.turhost.com/json-nedir-ve-ne-icin-kullanilir/) Backend tarafında yazdığım kod sayesinde burada gönderdiğimiz veriyi belirttiğim adreste görüntüleyebilmemizi sağlıyor. Aşağıya görseli ekledim:

<img src="https://github.com/mertbingol0/Web-TEchLEarn/blob/main/Screenshot%20from%202023-01-16%2001-02-56.png"></img>

#### POST on Server Side

Gelelim işin backend kısmına. Burada, yukarıda yaptığımız işlemlerin arka tarafta ne gibi süreçlerden geçtiğine değineceğiz. İlk olarak bir JSON verisinin frontend tarafından nasıl alındığına değinelim. Fakat öncesinde tüm kodu vererek açıklamalarını yapayım ve ardından işlenen süreçlere adım adım devam ederiz.

```python
import json
from flask import Flask, jsonify, request
app = Flask(__name__)

@app.route("/users/create", methods=["POST"])
def create_users():
    data = request.get_json()
    with open("users.json", "w") as f:
        json.dump(data, f)
    return "Data received and saved"
    
@app.route("/list-users", methods=["GET"])
def list_users():
    with open('users.json') as json_file:
        data = json.load(json_file)
    return jsonify(data)

if __name__ == '__main__':
    app.run(debug=True)
```

Klasik işlemleri atlayarak, asıl kısımlara değiniyorum. @app.route("/users/create", methods=["POST"]) Bu kısımda "/users/create" adında bir sayfa oluşturdum ve bu sayfaya sadece POST request'i atılabilmesini sağladım. Ardından "data" adında bir değişken oluşturup yukarıdaki örneğimizde bize gelen .json verisini backend tarafında yakalayıp "data" adlı değişkenime değer olarak atadım ve yakaladığım .json verisini "users.json" adlı dosyaya yazmasını söyledim. Son olarakta return ile "Data received and saved" mesajını bize gönderilen request'e response olarak döndürdüm.


```@app.route("/list-users", methods=["GET"])``` Bu kısımda ise dikkat ederseniz ```/list-users``` adında bir sayfa oluşturdum ve bu sayfaya GET methodu ile request atılabilmesini sağladım. Hmm, "list-users" diyor... ve GET methodu kullanıyor... demek ki... ben burada kullanıcıların bir listesini görmek istemişim ve server'dan bir veri almak istediğim için GET methodunu kullanmışım. Sonrasında ```with open('users.json') as json_file:``` satırında "users.json" adında bir json dosyası oluşturdum ve ```as``` parametresi yardımıyla "users.json" dosyasını "json_file" olarak temsil etmesini söyledim. Son olarakda "users.json" dosyasını ```return jsonify(data)``` diyerek client tarafından gelen POST requestinin sonucunu client'a return ettim.

#### PUT, DELETE and PATCH

PUT metodu, veri üzerinde değişiklik yaparken kullandığımız bir metoddur. Bu sayede yaptığımız değişiklikleri sunucuya bildirebiliyoruz. Ancak çoğu zaman PUT yerine POST kullanılabilir. PATCH metodu içinde aynı şey geçerlidir. Örnek olarak PUT requesti atmayi istedim ancak Twitter, Instagram gibi uygulamalar üzerinde profilimde değişiklik yaptığımda PUT isteğini göremedim. Daha önce PUT metodu ile ilgili bir deneyimim olmadığı için burada herhangi bir örnek veremiyorum, ancak bir kaynak bırakabilirim:
<a href="https://devcamp.com/trails/python-api-development-with-flask/campsites/hello-flask/guides/guide-building-update-action-put-request-flask">https://devcamp.com/trails/python-api-development-with-flask/campsites/hello-flask/guides/guide-building-update-action-put-request-flask</a>

PATCH metodu, PUT gibi çalışır. Tek farkı, PUT gibi genel bir veriyi değil, veri içindeki küçük bir kısmı için değişiklikler yapabiliyor olmasıdır. Örneğin büyük bir veri tutulduğunu ve o veri içerisinde yazım hatası olduğunu düşünün, o yazım hatasını düzeltmek için komple tüm veriyi güncelleme talebinde bulunmamıza gerek yoktur. Sadece küçük bir veri eklemek istediğimizden PATCH metodu kullanılabilir. Yine bu konuda bir örnek veremem, ancak bir kaynak bırakabilirim:
<a href="https://pythonbasics.org/flask-rest-api/">https://pythonbasics.org/flask-rest-api/</a>

DELETE metodu, adından da anlaşılacağı gibi, var olan verileri silmek için kullandığımız bir metoddur. Örneğin, sosyal medya hesaplarınızda paylaştığınız fotoğrafları sildiğinizde, sunucuya "ben bu fotoğrafı silmek istiyorum" diyebilmenizi sağlayan metod DELETE metodudur diyebiliriz. Aşağıda örnek bir DELETE isteğinin header'ını bıraktım.

Evet birakamadim. Çoğu uygulamada her işlem için POST metodu kullanılmış illa biz yazacağı kendi kodumuzu. Bunun sebebini araştırırken şöyle bir şey buldum: <a href="https://www.buraksenyurt.com/post/Post-Gorunumlu-Put">https://www.buraksenyurt.com/post/Post-Gorunumlu-Put</a> burada anlatılanlara göre bazı durumlarda Firewall'lar sadece GET ve POST metodu ile yapılan istekleri kabul ediyorlar. Bu nadiren olsa da, Instagram gibi bir uygulamada da olabiliyor.

PUT, DELETE ve PATCH methodlarini pek aciklayabildigimi dusunmuyorum eger daha iyi bir sekilde aciklayabileceginizi dusunuyorsaniz lutfen bana ulasin ve bu kaynaga eklemelerimizi yapalim. 

