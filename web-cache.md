<div class="post">
        <h1 class="post-title"><a href="/posts/how-do-work-web-cache?.html">Web Cache Nasil Calisir? ve Neden Boyle Bir Servise Ihtiyacimiz Var?</a></h1>
        <div class="post-meta">
<p>Bir web sitesine girildiğinde server o sitenin kaynak kodlarını alır ve o request'i atan client'a kaynak kodlarını render eder. Bu, birkaç kişi için bir sorun olmayabilir, ancak birkaç binlik requestlerde bu çok büyük bir sorun olabiliyor. Bu sebeple, web cache teknolojisini kullanarak bir web sitesine 10 kişi aynı anda request atarsa, backend tarafında bulunan kaynakları o 10 kişi için ayrı ayrı render etmek yerine, bilgisayarlarımız o verileri cacheden çeker ve browserımızda işlenir. (Bu verilere örnek olarak HTML, CSS ve JS verilebilir.) Böylece, backend tarafındaki yük epey bir hafifletilmiş olur çünkü ilgili kaynaklar iki kere veya daha fazla request-response işlemi ile işlenmez; onun yerine, HTML, CSS gibi kaynaklar bilgisayarınızda tutulur. </p>

<h3>Hangi Adreslerin ve Ne Gibi Verilerin Cacheleneceğine Kim Karar Veriyor ve Neden?</h3>
<p>Öncelikle, cache'lenebilir bir endpoint'e bir request atıldığında ne gibi durumlar oluyor, buna değinelim.</p>
<p>Önceden cache'lenmemiş bir endpoint'e request atıldığında, bir tür anahtar oluşturulur. Bu anahtar sayesinde, oluşturulan cache'in endpointine tekrar bir request atıldığında, verileri yeniden renderlamak yerine, request'in HTTP header'ına bakılır ve buna göre bir anahtar daha oluşturulur. Eğer o anahtar, cache'in db(memory)'sinde varsa, tekrar bir render işlemi gerçekleştirilmez, onun yerine, cache'lenmiş veri kullanıcıya cache'den return edilir.
</p>
<b>Örnek bir HTTP Response'unda Cachelenebilir/Cachelenemez bilgisinin verildiği kısım:</b>
<pre><code class="highlight language-js">age: 15
cache-control: max-age=600
date: Tue, 21 Feb 2023 17:02:37 GMT
etag: W/"63f36903-194d"
expires: Tue, 21 Feb 2023 16:23:53 GMT
vary: Accept-Encoding
via: 1.1 varnish
x-cache: HIT
x-cache-hits: 1
x-fastly-request-id: 9e1ebe8397080d97590d076e0bf46a0e5f3020d5
x-served-by: cache-sof1510058-SOF
x-timer: S1676998958.524868,VS0,VE1</code></pre>
<p>Dönen response'da yer alan <b>cache-control</b> ve <b>expires</b> başlıkları, response'un cache'lenip cache'lenemediğini belirler.</p>
<p><b>Cache-Control: max-age=600</b> header'ı, respons'un cach'de en fazla 600 saniye (10 dakika) tutulabileceğini belirtir. Bu süre sona erdikten sonra, cache'den alınan veriler, server'dan yeniden alınır.</p>
<p><b>Expires: Tue, 21 Feb 2023 16:23:53 GMT</b> header'ı da benzer bir bilgi verir. Bu header, response'un cache'de en fazla bu tarihe kadar (21 Şubat 2023, 16:23:53) tutulabileceğini belirtir.
    <p>Bu başlıkların olmaması ya da sıfır değerleriyle ayarlanmış olmaları durumunda, yanıt önbelleğe alınamaz. "Vary", "ETag" ve "X-Cache" gibi diğer başlıklar, cache'in davranışını etkileyen diğer bilgileri sağlarlar.</p>
<h3>Cache Key'i Neye Göre Oluşturuluyor?</h3>
<p>Cache key'i, HTTP request'inin içerisinde bulunan çok temel verilere bakarak bir key oluşturur. Peki, bu temel veriler nelerdir? Bu veriler protokol, domain, path ve qs (Query String)'dir. Tabii, böyle olmak zorunda değil. Sonundaki qs parametresi olmasa da veri, cachelenilebilir bir şekilde ayarlanabilir. Örneğin, key create işlemi sadece protokol, domain ve path bilgisine göre oluşturulabilir.</p>

<h3>Ne gibi Durumlarda Caching Yapılmaz?</h3>
<p>Bir verinin neye bakarak caching yaptığımızdan bahsettik, peki neye bakarak caching yapmıyoruz?
    Backend tarafından gönderilen response içerisindeki header kısmında verinin cachlenebilir ya da cachlenemez bilgisini veriyor.
    Böylece gelen veri, server'ın bize söylemiş olduğu cachelenebilirlik durumuna göre cache'liyor ya da cachlemiyoruz.</p>

 <h3>Peki Neden Verilerin Cachlenmemesini İstiyoruz? Her Şeyi Yüklenelim?</h3>
 <p>Bazı verileri cache'lemememiz gerekiyor. Örnek olarak, değişkenlik gösteren bir veriyi cache'lersek, o sayfayı yenilesek bile, yine veriyi cacheden almaya çalıştığımız için hala daha güncellenmemiş halini görürüz. Buna örnek olarak profil fotoğrafları verilebilir. Eğer her gönderilen veri cache'lenebilseydi, yenilediğiniz profil fotoğrafınızı görebilmek için cachelerinizi silmeniz ve yeni bir request atmanız gerekirdi.
 </p>

<h3>Cachlenmis Veriye 2. Bir Request Atılrsa Server Tarafında Ne Gibi Durumlar Olur?</h3>
<p>İlk örneğimize geri dönelim. Yukarıda, bir siteye ilk kez request atıldığında HTML, CSS ve JS gibi kaynakların bilgisayarlarımızda tutulmak üzere indirildiğinden bahsetmiştik. Şimdi ise ilk requestten sonra aynı endpoint'e 2. bir request atılırsa ne gibi durumların yaşanacağına değinelim.</p>

   
Aynı endpoint'e ikinci kez request atıldığında, ilk attığımız request üzerinden oluşturulan key'e bakılır
ve yeni atılan request'in key'i ile uyusup uyusmadığı testine sokulur. Ardından bu işlemin sonucu True ise backend tarafında
cachelenen verinin aynısı bu client'a gönderilir ve ilgili kaynaklar browser tarafından işlenir.
    </p>
          
    <p>Bu duruma aşağıda bir örnek verdim:</p>
    <b>Atilan bir request sonucunda server tarafinda bir cache key'i nasil olusturulur?</b>
    <pre><code class="highlight language-js">request = calculating_cash_key(http-response-parameters)</code></pre>
    <p>Böyle bir örnek düşünelim. Burada 'http-response-parameters' değişkeni içinde tutulan verinin 'HTTP 1.1/mertbingol0.github.io/index.html' olduğunu varsayalım. Key'in bu HTTP parametrelerine göre oluşturulduğundan söz etmiştim. Burada da aynı şekilde, eğer tekrar atılan request 'HTTP 1.1/mertbingol0.github.io/index.html' ise cache key hesaplanır ve hesaplanan key'in değeri, db(memory)'de var olup olmadığına bakılır. Eğer var ise, cache içerisinde bu endpoint için önceden cachelenmiş olan veriler return edilir.
    </p>
<h3>Örnek bir Caching Uygulaması:</h3>
          
```
from werkzeug.contrib.cache import SimpleCache
    
# Flask uygulamasını başlat
app = Flask(__name__)
    
# Bir cache nesnesi oluştur
cache = SimpleCache()
    
# Cache'lenecek endpoint için bir route belirle
@app.route('/cached-endpoint')
def cached_endpoint():
    # Yanıtın cache'de olup olmadığını kontrol et
    response = cache.get(request.path)
    if response is not None:
        # Yanıt cache'de ise, direkt olarak cache'den yanıtı döndür
        return response
    else:
        # Yanıt cache'de yok ise, yanıtı üret ve cache'e kaydet
        response = generate_response()
        cache.set(request.path, response, timeout=60)
        return response
    
# Bu fonksiyon cache'lenecek yanıtı üretir
def generate_response():
    # Bu örnekte, sadece bir string döndürüyoruz
    return "Bu cache'lenmiş bir veridir!"
    
if __name__ == '__main__':
    app.run()```
          
    <p>Flask ile yazdığım bu örnek cache uygulaması, HTTP isteklerine verilen yanıtların önbellekte (cache) saklanmasını sağlar. Bu şekilde, aynı isteği yapan birden fazla kullanıcı için, istek her seferinde yeniden işlenmeden önbellekten alınarak hızlı bir yanıt döndürülür. </p>

    <p>Yazımı okuduğunuz için teşekkür ederim. İyi çalışmalar.</p>
