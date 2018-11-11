# REST UPA'larını Anlamak ve Kullanmak

Twitter veya GitHub gibi başka bir kaynaktan veri almayı düşündüğünüzde, "REST UPA" terimiyle karşılaşma ihtimaliniz çok yüksektir. Ama REST UPA nedir? Sizin için ne yapabilir? Nasıl kullanabilirsiniz?

Bu makalede, UPA belgelerini okuyabilmek ve UPA'ları etkili bir şekilde kullanabilmek için REST UPA'ları hakkında bilmeniz gereken her şeyi öğreneceksiniz.


## REST UPA Nedir

YouTube'da Batman ile ilgili videolar bulmaya çalıştığınızı varsayalım. Youtube'u açarsınız, bir arama alanına "Batman" yazarsınız, giriş tuşuna basarsınız ve Batman hakkındaki videoların bir listesini görürsünüz. Bir REST UPA'sı da benzer şekilde çalışır. Bir şeyler ararsınız ve bunu sorguladığınız servisten elde edilen sonuçların bir listesini alırsınız.

**UPA (API)**, bir uygulama programlama arabirimidir (application programming interface). Programların birbirleriyle konuşmasına izin veren bir dizi kuraldır. Geliştirici, UPA'yı sunucuda oluşturur ve istemcinin onunla konuşmasına izin verir.

**REST**, UPA'nın nasıl göründüğünü belirler. Representational State Transfer (Temsili Durum Transferi) anlamına gelir. Geliştiricilerin UPA'larını oluştururken izlemeleri gereken kurallar kümesidir. Bu kurallardan biri, belirli bir URL'ye bağlandığınızda bir veri parçasını (kaynak olarak adlandırılır) alabilmeniz gerektiğini belirtir.

Her URL'ye bir **talep (istek, request)** denir ve verilerin size geri gönderilmesine **yanıt (response)** denir.


## Bir Talebin Anatomisi

Bir talebin dört parçadan oluştuğunu bilmek önemlidir:

1. Bitiş noktası (uç nokta, endpoint)
2. Metod
3. Üst bilgiler
4. Veri (veya gövde)

**Bitiş noktası (veya yol, route)** istek yaptığınız URL'dir. Şu yapıyı takip eder:

```
kök-bitişnoktası/?
```

**kök-bitişnoktası (root-endpoint)**, istek yaptığınız UPA'nın başlangıç noktasıdır. [Github UPA'sının](https://developer.github.com/v3/) kök-bitişnoktası, [https://api.github.com](https://api.github.com) şeklindedir ve [Twitter UPA'sının](https://dev.twitter.com/rest/reference) kök-bitişnoktası, [https://api.twitter.com](https://api.twitter.com)'dur.

**Yol (path)**, talep ettiğiniz kaynağı belirler. Bunu bir servis için 1'e, başka bir servis için 2'ye, başka bir servis için de 3'e, vb. basmanızı isteyen bir otomatik çağrı yanıtlama sistemi gibi düşünün.

Bir web sitesinin bölümlerine gidebileceğiniz gibi yollara erişebilirsiniz. Örneğin, Smashing Magazine'de "JavaScript" ile etiketlenen tüm yayınların bir listesini almak için `https://www.smashingmagazine.com/tag/javascript/` adresine gidebilirsiniz. `https://www.smashingmagazine.com` adresi kök-bitişnoktası ve `/tag/javascript` yoldur.

Hangi yolları kullanabileceğinizi anlamak için UPA belgelerine göz atmanız gerekir. Örneğin, GitHub UPA'sı aracılığıyla belirli bir kullanıcının depo listesini almak istediğinizi varsayalım. [Belgeler](https://developer.github.com/v3/repos/#list-user-repositories), bunu yapmak için aşağıdaki yolu kullanmanız gerektiğini söyler:

```
/users/:username/repos
```

Bir yoldaki iki nokta işareti (`:`), bir değişkeni ifade eder. Talebinizi gönderirken bu değerleri gerçek değerlerle değiştirmeniz gerekir. Bu durumda, `:username`'i, aradığınız kullanıcının gerçek kullanıcı adıyla değiştirmelisiniz. Kendi GitHub hesabımı arıyorsam, `:username` değişkeninin yerine şunu yazarım: `maidis`.

Github'daki kaynak kod depolarımın listesini almak için bitiş noktası şudur:

```
https://api.github.com/users/maidis/repos
```

Bir bitiş noktasının son kısmı **sorgu parametreleridir (query parameters)**. Teknik olarak, sorgu parametreleri REST mimarisinin bir parçası değildir, ancak birçok UPA'nın bunları kullandığını göreceksiniz. Bu yüzden, UPA'ları nasıl okuyacağınızı ve kullanacağınızı tamamen anlamanıza yardımcı olmak için onlardan da bahsedeceğiz. Sorgu parametreleri, isteğinizi anahtar-değer (key-value) çiftleriyle tamlama seçeneği sunar. Her zaman bir soru işareti (`?`) ile başlarlar ve her parametre çifti, aşağıdaki gibi bir ampersan (`&`) ile ayrılır:

```
?query1=value1&query2=value2
```

Bir kullanıcının Github'daki depolarının bir listesini almaya çalıştığınızda, size verilen sonuçları değiştirmek için isteğinize üç olası parametre ekleyebilirsiniz:

*Kullanıcı depolarını listele*

*Belirtilen kullanıcı için genel depoları listeleyin.*

```
GET /users/:username/repos
```

*Parametreler*

| İsim           | Tür       | Açıklama |
|----------------|-----------|----------|
| `type`           | `string`    | `all`, `owner` ve `member`'dan biri olabilir. Öntanımlı: `owner` |
| `sort`           | `string`    | `created`, `updated`, `pushed` ve `full_name`'den biri olabilir. Öntanımlı: `full_name` |
| `direction`      | `string`    | `asc` veya `desc` olabilir. Öntanımlı: `full_name` kullanılırken: `asc`, diğer durumlarda `desc` |

Son zamanlara kod değişikliği yaptığım depoların listesini almak isterseniz, `sort`'u `push` olarak ayarlayabilirsiniz.

```
https://api.github.com/users/maidis/repos?sort=pushed
```


## curl ile Bitiş Noktalarını Test Etme

Herhangi bir programlama diliyle istek gönderebilirsiniz. JavaScript kullanıcıları [Fetch API](https://css-tricks.com/using-fetch/) jQuery'nin [Ajax metodu](http://api.jquery.com/jquery.ajax/) gibi yöntemleri kullanabilir; Ruby kullanıcıları Ruby'nin [Net::HTTP sınıfını](http://ruby-doc.org/stdlib-2.4.2/libdoc/net/http/rdoc/index.html) kullanabilir, Python kullanıcıları [Python Requests](http://docs.python-requests.org/en/master/)'i kullanabilir ve C++ kullanıcıları ne yapabilir ona sonra değineceğiz.

Şimdi [curl](https://curl.haxx.se/) adlı komut satırı aracını kullanacağız. UPA belgeleri genel olarak curl'e referans vererek yazıldığından curl kullanıyoruz. curl'ün nasıl kullanacağını biliyorsanız UPA belgelerini anlamada sorun yaşamazsınız. Sonrasında tercih ettiğiniz dille istekleri kolayca gerçekleştirebilirsiniz.

Devam etmeden önce, makinenize curl'ün yüklü olduğundan emin olmak istersiniz terminalinizi açın ve

```bash
curl --version
```

komutunu çalıştırın. Bu komut, sisteminizde yüklü olan curl sürümünü gösterir. curl yüklü değilse "komut bulunamadı" benzeri bir hata alırsınız. Bu durumda devam etmeden önce [curl'ü yüklemeniz](https://curl.haxx.se/download.html) gerekir, örneğin Fedora dağıtımı için şu komutu kullanabilirsiniz:

```bash
sudo dnf install curl
```

curl'ü kullanmak için, curl yazdıktan sonra istediğiniz bitiş noktasını yazabilirsiniz. Örneğin GitHub'ın kök bitiş noktasını almak için aşağıdakileri yazabilirsiniz:

```bash
curl https://api.github.com
```

Giriş tuşuna bastığınızda, GitHub'dan şöyle bir yanıt almalısınız:

```json
{
  "current_user_url": "https://api.github.com/user",
  "current_user_authorizations_html_url": "https://github.com/settings/connections/applications{/client_id}",
  "authorizations_url": "https://api.github.com/authorizations",
  "code_search_url": "https://api.github.com/search/code?q={query}{&page,per_page,sort,order}",
  "commit_search_url": "https://api.github.com/search/commits?q={query}{&page,per_page,sort,order}",
  "emails_url": "https://api.github.com/user/emails",
  "emojis_url": "https://api.github.com/emojis",
  "events_url": "https://api.github.com/events",
  "feeds_url": "https://api.github.com/feeds",
  "followers_url": "https://api.github.com/user/followers",
  "following_url": "https://api.github.com/user/following{/target}",
  "gists_url": "https://api.github.com/gists{/gist_id}",
  "hub_url": "https://api.github.com/hub",
  "issue_search_url": "https://api.github.com/search/issues?q={query}{&page,per_page,sort,order}",
  "issues_url": "https://api.github.com/issues",
  "keys_url": "https://api.github.com/user/keys",
  "notifications_url": "https://api.github.com/notifications",
  "organization_repositories_url": "https://api.github.com/orgs/{org}/repos{?type,page,per_page,sort}",
  "organization_url": "https://api.github.com/orgs/{org}",
  "public_gists_url": "https://api.github.com/gists/public",
  "rate_limit_url": "https://api.github.com/rate_limit",
  "repository_url": "https://api.github.com/repos/{owner}/{repo}",
  "repository_search_url": "https://api.github.com/search/repositories?q={query}{&page,per_page,sort,order}",
  "current_user_repositories_url": "https://api.github.com/user/repos{?type,page,per_page,sort}",
  "starred_url": "https://api.github.com/user/starred{/owner}{/repo}",
  "starred_gists_url": "https://api.github.com/gists/starred",
  "team_url": "https://api.github.com/teams",
  "user_url": "https://api.github.com/users/{user}",
  "user_organizations_url": "https://api.github.com/user/orgs",
  "user_repositories_url": "https://api.github.com/users/{user}/repos{?type,page,per_page,sort}",
  "user_search_url": "https://api.github.com/search/users?q={query}{&page,per_page,sort,order}"
}
```

Bir kullanıcının depolarının bir listesini almak için, bitiş noktasını daha önce gördüğümüz gibi uygun yola dönüştürebilirsiniz. Yani depolarımın bir listesini almak için şu komutu kullanabilirsiniz:

```bash
curl https://api.github.com/users/maidis/repos
```

Sorgu parametrelerini curl'e eklemek isterseniz `?` ve `=` karakterlerinden önce ters eğik çizgi (`\`) eklemeyi unutmayın. Bunun nedeni, `?` ve `=` karakterlerinin komut satırında özel karakterler olmasıdır. Onları normal karakter olarak yorumlamak için komut satırında önlerine `\` eklememiz gerekir:

```bash
curl https://api.github.com/users/maidis/repos\?sort\=pushed
```


## JSON
JSON (JavaScript Object Notation), bir REST UPA'sı aracılığıyla veri göndermek ve talep etmek için yaygın bir biçimdir. GitHub'ın size geri verdiği yanıt da JSON olarak biçimlendirilmiştir.

Bir JSON nesnesi, bir JavaScript Nesnesi gibi görünür. JSON'da, her bir özellik ve değer, aşağıdaki gibi çift tırnak işaretleri ile sarılmalıdır:

```json
{
  "özellik1": "değer1",
  "özellik2": "değer2",
  "özellik3": "değer3"
}
```

## Bir Talebin Anatomisine Geri Dönüş

Bir talebin dört parçadan oluştuğunu öğrenmiştik:

1. Bitiş noktası
2. Metod
3. Üst bilgiler
4. Veri

Şimdi, bir isteği oluşturan şeylerin geri kalanını inceleyelim.


### Metod
Metod, sunucuya gönderdiğiniz istek türüdür. Aşağıdaki beş tür arasından seçim yapabilirsiniz:

- GET
- POST
- PUT
- PATCH
- DELETE

Bu yöntemler, yaptığınız isteğin amacını belirtir. `Create` (Oluştur), `Read` (Oku), `Update` (Güncelle) ve `Delete` (Sil) olmak üzere dört olası eylemi (CRUD) gerçekleştirmek için kullanılırlar.

| Metod İsmi | İsteğin Anlamı |
|------------|----------------|
| `GET`      | Bu istek, bir sunucudan kaynak almak için kullanılır. Bir `GET` isteği yaparsanız sunucu talep ettiğiniz verileri arar ve size geri gönderir. Diğer bir deyişle, bir `GET` isteği `READ` işlemi gerçekleştirir. Bu öntanımlı istek yöntemidir. |
| `POST`     | Bu istek, sunucuda yeni bir kaynak oluşturmak için kullanılır. Bir `POST` isteği gerçekleştirirseniz sunucu, veritabanında yeni bir giriş oluşturur ve oluşturmanın başarılı olup olmadığını söyler. Diğer bir deyişle, bir `POST` isteği `CREATE` işlemi gerçekleştirir. |
| `PUT` ve `PATCH` | Bu iki istek, bir sunucudaki bir kaynağı güncellemek için kullanılır. Bir `PUT` veya `PATCH` isteği yaparsanız sunucu, veritabanındaki bir girişi günceller ve güncellemenin başarılı olup olmadığını söyler. Başka bir deyişle, bir `PUT` veya `PATCH` talebi `UPDATE` işlemi gerçekleştirir. |
| `DELETE` | Bu istek, bir sunucudan bir kaynağı silmek için kullanılır. Bir `DELETE` isteği gerçekleştirirseniz sunucu, veritabanında bir girişi siler ve silme işleminin başarılı olup olmadığını belirtir. Diğer bir deyişle, bir `DELETE` isteği bir `DELETE` işlemi gerçekleştirir. |

UPA, her bir istek için hangi istek yöntemini kullanmanız gerektiğini bilmenizi sağlar. Örneğin, bir kullanıcının depolarının bir listesini almak için bir `GET` isteğine ihtiyacınız vardır:

```
GET /users/:username/repos
```

[Yeni bir GitHub deposu oluşturmak](https://developer.github.com/v3/repos/#create) içinse bir POST isteğine ihtiyacınız vardır:

```
POST /user/repos
```

curl'de istek metodunu `-X` veya `--request` yazarak ve ardından istek yöntemini kullanarak ayarlayabilirsiniz. Aşağıdaki komut, curl aracılığıyla bir depo oluşturmayı dener:

```bash
curl -X POST https://api.github.com/user/repos
```

Bu isteği çalıştırdığınızda kimlik doğrulamanın gerekli olduğunu bildiren bir yanıt alırsınız:

```json
{
  "message": "Requires authentication",
  "documentation_url": "https://developer.github.com/v3/repos/#create"
}
```

Kimlik doğrulaması konusuna daha sonra değineceğiz.


### Üstbilgiler
Üstbilgiler, hem istemci hem de sunucuya bilgi sağlamak için kullanılır. Kimlik doğrulama ve gövde içeriği hakkında bilgi sağlama gibi birçok amaç için kullanılabilir. Geçerli üstbilgilerin listesini MDN’nin [HTTP Üstbilgileri Belgesi](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)'nden bulabilirsiniz.

HTTP Üstbilgileri, iki nokta işaretiyle ayrılan özellik-değer çiftleridir. Aşağıda, sunucunun JSON içeriği bekledeğini söyleyen bir üstbilgi örneği yer almaktadır:

```
"Content-Type: application/json". Missing the opening ".
```

curl'de HTTP üstbilgilerini `-H` veya `--header` seçeneğiyle gönderebilirsiniz. Yukarıdaki üstbilgiyi GitHub'ın UPA'sına göndermek için şu komutu kullanabilirsiniz:

```bash
curl -H "Content-Type: application/json" https://api.github.com
```

Content-Type üstbilgisi, GitHub UPA'sının çalışması için gerekli değildir. Yalnızca curl ile bir üstbilginin nasıl kullanılacağını gösteren bir örnek sunmak için verilmiştir.

Gönderdiğiniz üstbilgileri görüntülemek için, isteği gönderirken `-v` veya `--verbose` seçeneğini kullanabilirsiniz:

```bash
curl -H "Content-Type: application/json" https://api.github.com -v
```

```bash
* Rebuilt URL to: https://api.github.com/
*   Trying 192.30.253.116...
* TCP_NODELAY set
* Connected to api.github.com (192.30.253.116) port 443 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* successfully set certificate verify locations:
  CAfile: /etc/pki/tls/certs/ca-bundle.crt
  CApath: none
* TLSv1.2 (OUT), TLS handshake, Client hello (1):
* TLSv1.2 (IN), TLS handshake, Server hello (2):
* TLSv1.2 (IN), TLS handshake, Certificate (11):
* TLSv1.2 (IN), TLS handshake, Server key exchange (12):
* TLSv1.2 (IN), TLS handshake, Server finished (14):
* TLSv1.2 (OUT), TLS handshake, Client key exchange (16):
* TLSv1.2 (OUT), TLS change cipher, Client hello (1):
* TLSv1.2 (OUT), TLS handshake, Finished (20):
* TLSv1.2 (IN), TLS handshake, Finished (20):
* SSL connection using TLSv1.2 / ECDHE-RSA-AES128-GCM-SHA256
* ALPN, server accepted to use http/1.1
* Server certificate:
*  subject: C=US; ST=California; L=San Francisco; O=GitHub, Inc.; CN=*.github.com
*  start date: Jun 19 00:00:00 2018 GMT
*  expire date: Jul 10 12:00:00 2019 GMT
*  subjectAltName: host "api.github.com" matched cert's "*.github.com"
*  issuer: C=US; O=DigiCert Inc; OU=www.digicert.com; CN=DigiCert SHA2 High Assurance Server CA
*  SSL certificate verify ok.
> GET / HTTP/1.1
> Host: api.github.com
> User-Agent: curl/7.59.0
> Accept: */*
> Content-Type: application/json
> 
< HTTP/1.1 200 OK
< Server: GitHub.com
< Date: Fri, 09 Nov 2018 14:03:55 GMT
< Content-Type: application/json; charset=utf-8
< Content-Length: 2165
< Status: 200 OK
< X-RateLimit-Limit: 60
< X-RateLimit-Remaining: 58
< X-RateLimit-Reset: 1541774540
< Cache-Control: public, max-age=60, s-maxage=60
< Vary: Accept
< ETag: "7dc470913f1fe9bb6c7355b50a0737bc"
< X-GitHub-Media-Type: github.v3; format=json
< Access-Control-Expose-Headers: ETag, Link, Location, Retry-After, X-GitHub-OTP, X-RateLimit-Limit, X-RateLimit-Remaining, X-RateLimit-Reset, X-OAuth-Scopes, X-Accepted-OAuth-Scopes, X-Poll-Interval, X-GitHub-Media-Type
< Access-Control-Allow-Origin: *
< Strict-Transport-Security: max-age=31536000; includeSubdomains; preload
< X-Frame-Options: deny
< X-Content-Type-Options: nosniff
< X-XSS-Protection: 1; mode=block
< Referrer-Policy: origin-when-cross-origin, strict-origin-when-cross-origin
< Content-Security-Policy: default-src 'none'
< X-GitHub-Request-Id: C5CA:6A1F:15DBDF1:2B20805:5BE593CB
< 
{
  "current_user_url": "https://api.github.com/user",
  "current_user_authorizations_html_url": "https://github.com/settings/connections/applications{/client_id}",
  "authorizations_url": "https://api.github.com/authorizations",
  "code_search_url": "https://api.github.com/search/code?q={query}{&page,per_page,sort,order}",
  "commit_search_url": "https://api.github.com/search/commits?q={query}{&page,per_page,sort,order}",
  "emails_url": "https://api.github.com/user/emails",
  "emojis_url": "https://api.github.com/emojis",
  "events_url": "https://api.github.com/events",
  "feeds_url": "https://api.github.com/feeds",
  "followers_url": "https://api.github.com/user/followers",
  "following_url": "https://api.github.com/user/following{/target}",
  "gists_url": "https://api.github.com/gists{/gist_id}",
  "hub_url": "https://api.github.com/hub",
  "issue_search_url": "https://api.github.com/search/issues?q={query}{&page,per_page,sort,order}",
  "issues_url": "https://api.github.com/issues",
  "keys_url": "https://api.github.com/user/keys",
  "notifications_url": "https://api.github.com/notifications",
  "organization_repositories_url": "https://api.github.com/orgs/{org}/repos{?type,page,per_page,sort}",
  "organization_url": "https://api.github.com/orgs/{org}",
  "public_gists_url": "https://api.github.com/gists/public",
  "rate_limit_url": "https://api.github.com/rate_limit",
  "repository_url": "https://api.github.com/repos/{owner}/{repo}",
  "repository_search_url": "https://api.github.com/search/repositories?q={query}{&page,per_page,sort,order}",
  "current_user_repositories_url": "https://api.github.com/user/repos{?type,page,per_page,sort}",
  "starred_url": "https://api.github.com/user/starred{/owner}{/repo}",
  "starred_gists_url": "https://api.github.com/gists/starred",
  "team_url": "https://api.github.com/teams",
  "user_url": "https://api.github.com/users/{user}",
  "user_organizations_url": "https://api.github.com/user/orgs",
  "user_repositories_url": "https://api.github.com/users/{user}/repos{?type,page,per_page,sort}",
  "user_search_url": "https://api.github.com/search/users?q={query}{&page,per_page,sort,order}"
}
* Connection #0 to host api.github.com left intact
```

Buradaki `*`, curl tarafından sağlanan ek bilgileri ifade eder. `>`, istek üstbilgilerine işaret eder ve `<`, yanıt üstbilgilerine işaret eder.


### Veri (veya Gövde)

Veriler (bazen gövde veya mesaj olarak da adlandırılır) sunucuya göndermek istediğiniz bilgileri içerir. Bu seçenek sadece `POST`, `PUT`, `PATCH` veya `DELETE` istekleriyle kullanılır.

curl ile veri göndermek için `-d` veya `--data` seçeneğini kullanabilirsiniz:

```bash
curl -X POST <URL> -d property1=value1
```

Birden çok veri alanı göndermek için çoklu `-d` seçenekleri oluşturabilirsiniz:

```bash
curl -X POST <URL> -d property1=value1 -d property2=value2
```

Mantıklı görünüyorsa daha kolay okunabilmesi için isteğinizi birden çok satıra bölebilirsiniz:

```bash
curl -X POST <URL> \
  -d property1=value1 \
  -d property2=value2
```

Nasıl bir sunucu tasarlayacağınızı biliyorsanız bir UPA oluşturabilir ve kendi verilerinizi test edebilirsiniz. Bilmiyorsanız ama denemek için yeterince cesaretliyseniz Node, Express ve MongoDB ile nasıl sunucu oluşturulduğunu gösteren belgeleri takip edebilirsiniz.

Bir sunucu oluşturmakla uğraşmak istemiyorsanız [RequestBin](https://requestbin.fullcontact.com/), [PostBin](http://postb.in/) gibi ücretsiz çevrimiçi istek kutusu servislerinden birini deneyebilirsiniz. Bunu yapmak için, örneğin RequestBin sitesine gidin ve Create a RequestBin (Bir İstek Kutusu Oluştur) düğmesine basın.

[https://requestbin.fullcontact.com/1gtdeft1](https://requestbin.fullcontact.com/1gtdeft1) gibi istekleri test etmek için kullanabileceğiniz bir URL verilecektir. Bu istek kutusu, son yapılan 20 isteği saklayacak ve oluşturulduktan sonra 48 saat açık kalacaktır. Bu yüzden isteğinizi test etmek istiyorsanız kendi istek kutunuzu oluşturduğunuzdan emin olun. Bu belgeyi okuduğunuz zaman yukarıda oluşturulan istek kutusu çoktan silinmiş olacaktır.

İstek kutusu oluşturulduktan sonra açılan sayfada curl ile ve çeşitli programlama dilleriyle nasıl istek yapılabileceğini gösteren komut ve kod parçaları da gösteriliyor:

**cURL**

```bash
curl -X POST -d "fizz=buzz" http://requestbin.fullcontact.com/1gtdeft1
```

**Python (Requests ile)**

```python
import requests, time
r = requests.post('http://requestbin.fullcontact.com/1gtdeft1', data={"ts":time.time()})
print r.status_code
print r.content
```

**C# / .NET**

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

namespace RequestBinExample
{
  class Program
  {
    static void Main(string[] args)
    {
      MakeRequest();
    }

    private static async Task MakeRequest()
    {
      var httpClient = new HttpClient();
      var response = await httpClient.GetAsync(new Uri("http://requestbin.fullcontact.com/1gtdeft1"));
      var body = await response.Content.ReadAsStringAsync();
      Console.WriteLine(body);
    }
  }
}
```

Yukarıdaki curl komut satırını çalıştırdığımızda istek kutusuna gönderdiğimiz istek şu şekilde görüntülenir:

```bash
http://requestbin.fullcontact.com
POST /1gtdeft1

application/x-www-form-urlencoded
9 bytes

17s ago
From 88.238.99.57, 54.182.255.16

FORM/POST PARAMETERS
fizz: buzz

HEADERS
Via: 1.1 2f43ce8da1e384926701eb877d7472ba.cloudfront.net (CloudFront), 1.1 vegur
Cloudfront-Forwarded-Proto: http
Cloudfront-Is-Tablet-Viewer: false
Cloudfront-Viewer-Country: TR
Cloudfront-Is-Mobile-Viewer: false
Content-Length: 9
Accept: */*
Cloudfront-Is-Desktop-Viewer: true
X-Request-Id: 8632c0f3-0874-4c91-8b1a-f15cfb04264d
Connect-Time: 1
Connection: close
Cloudfront-Is-Smarttv-Viewer: false
Content-Type: application/x-www-form-urlencoded
X-Amz-Cf-Id: N_OYmWIX0o70XOR_Fv35mTM17SwLeNIJHH_xtNyAkekdleibcRyoxg==
Host: requestbin.fullcontact.com
Total-Route-Time: 0
User-Agent: curl/7.59.0

RAW BODY
fizz=buzz
```

Öntanımlı olarak, curl verileri bir sayfadaki "form alanları" aracılığıyla gönderilmiş gibi gönderir. JSON verilerini göndermek isterseniz `Content-Type`'ı `application/json` olarak ayarlamalısınız ve verilerinizi şu şekilde bir JSON nesnesi olarak biçimlendirmeniz gerekir:

```bash
curl -X POST http://requestbin.fullcontact.com/1gtdeft1 \
  -H "Content-Type: application/json" \
  -d '{
  "property1":"value1",
  "property2":"value2"
}'
```

JSON olarak gönderdiğimiz bu veri, istek kutusunda şu şekilde görüntülenir:

```bash
...
Content-Type: application/json
...

RAW BODY
{
  "property1":"value1",
  "property2":"value2"
}
```

Ve işte bu kadar! Bir isteğin yapısı hakkında bilmeniz gereken (hemen hemen) her şeyi öğrendiniz.

Şimdi GitHub UPA'sı üzerinden bir POST isteği göndermeye çalıştığınızda "Kimlik doğrulaması gerekiyor" yazan bir mesaj aldığınızı hatırlayın. Açıkça belli olduğu gibi bunun nedeni POST isteğini gerçekleştirme yetkinizin olmamasıdır.


## Kimlik Doğrulama
Kimsenin izniniz olmadan banka hesabınıza erişmesine izin vermezdiniz öyle değil mi? Aynı düşünce doğrultusunda geliştiriciler de yalnızca yetkilendirildiğiniz eylemleri gerçekleştirebilmeniz için gerekli önlemleri alır. Bu, başkalarının sizi taklit etmesini engeller.

`POST`, `PUT`, `PATCH` ve `DELETE` istekleri veritabanını değiştirdiğinden, geliştiriciler neredeyse her zaman bunları bir kimlik doğrulama duvarının arkasına yerleştirirler. Bazı durumlarda, bir `GET` isteği de kimlik doğrulaması gerektirir (örneğin, mevcut bakiyenizi kontrol etmek için banka hesabınıza eriştiğinizde olduğu gibi).

Web'de, kendinizi doğrulamak için iki temel yol vardır:

1. Bir kullanıcı adı ve parolayla (temel kimlik doğrulaması da denir)
2. Gizli bir belirteçle (dizgecik, jeton, token)

Gizli belirteç yöntemi, Github, Google, Twitter, Facebook gibi ağlarla kimliğinizi doğrulamanızı sağlayan [oAuth](https://oauth.net/)'u içerir.

Bu makalede yalnızca kullanıcı adı ve parolayla temel kimlik doğrulamayı kullanmayı öğreneceksiniz. Eğer oAuth ile kimlik doğrulama yapmak istiyorsanız konuyu internette araştırabilirsiniz.

curl ile temel bir kimlik doğrulaması yapmak için `-u` seçeneğini ve ardından kullanıcı adınızı ve parolanızı aşağıdaki gibi kullanabilirsiniz:

```bash
curl -X POST -u "username:password" https://api.github.com/user/repos
```

Yukarıdaki istekte kendi kullanıcı adınızı ve parolanızı kullanarak kimliğinizi doğrulamayı deneyin. Kimlik doğrulamayı başarılı bir şekilde tamamladıktan sonra aşağıdakine benzer bir hatayla karşılaşacaksınız artık:

```json
{
  "message": "Repository creation failed.",
  "errors": [
    {
      "resource": "Repository",
      "code": "missing_field",
      "field": "name"
    },
    {
      "resource": "Repository",
      "code": "custom",
      "field": "name",
      "message": "name is too short (minimum is 1 character)"
    }
  ],
  "documentation_url": "https://developer.github.com/v3/repos/#create"
}
```

Bunun nedeni, sunucuya henüz (tüm `POST`, `PUT`, `PATCH` ve `DELETE` isteklerinin gerektirdiği) herhangi bir veri vermemenizdir.

Şu ana kadar öğrendiğiniz bilgilerle, curl aracılığıyla bir GitHub deposu oluşturmak için yukarıdaki kodu düzenleyebilmeniz gerekir. Kendinizi sınamaktan çekinmeyin.

Şimdi, HTTP durum kodları ve hata mesajları hakkında konuşalım.


## HTTP Durum Kodları ve Hata Mesajları
Daha önce aldığınız mesajların bazıları, "Kimlik doğrulama gerektirir" ve "JSON ayrıştırma sorunları" şeklindeydi. Bunlar, yalnızca isteğinizle ilgili bir sorun olduğunda görünürler. HTTP durum kodları, yanıtın durumunu hızlı bir şekilde anlamanızı sağlar. 100+ ila 500+ arası olanlar genel olarak aşağıdaki kuralları takip eder:

- **200+**, isteğin **başarılı** olduğu anlamına gelir
- **300+**, isteğin başka bir URL'ye **yönlendirildiği** anlamına gelir
- **400+**, **istemciden** kaynaklanan bir **hata** demektir
- **500+**, **sunucudan** kaynaklanan bir **hata** oluştuğunu gösterir

Bir yanıtta, ayrıntılı çıktı seçeneğini (`-v` veya `--verbose`) veya üstbilgi seçeneğini (`-I` veya `--head`) kullanarak hata ayıklayabilirsiniz.

Örneğin, kullanıcı adınızı ve parolanızı girmeden bir `POST` isteğinde bulunurken `-I` eklerseniz, 401 durum kodu alırsınız (kimlik doğrulama gerektirir):

```bash
HTTP/1.1 401 Unauthorized
Server: GitHub.com
...
{
  "message": "Requires authentication",
  "documentation_url": "https://developer.github.com/v3/repos/#create"
}
```

Talebiniz, verileriniz yanlış veya eksik olduğu için geçersiz olduğunda da genellikle 400 durum kodu (Kötü İstek) alırsınız.

HTTP durum kodları hakkında daha çok bilgi almak için MDN'nin [HTTP Durum Belgesine](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) bakabilirsiniz.


## UPA Sürümleri
Geliştiriciler, UPA'larını zaman zaman günceller. Bazen sürüm yükseltmelerinde UPA, çok değişebilir. Böyle bir durumda uygulamanız çalışmaz duruma gelirse bunun nedeni genellikle kodunuzun artık eski bir UPA için olması, ancak isteğinizin daha yeni bir UPA'yı işaret etmesidir.

Belirli bir UPA sürümü için iki yolla talepte bulunabilirsiniz. Hangi yolu seçeceğiniz UPA'nın nasıl yazıldığına bağlıdır. Bu iki yol:

1. Doğrudan bitiş noktasında
2. Bir istek üstbilgisinde

Örneğin Twitter, ilk yöntemi kullanır. Yazım sırasında, Twitter’ın UPA'sı, bitiş noktasından anlaşılacağı üzere 1.1 sürümündedir:

```
https://api.twitter.com/1.1/account/settings.json
```

Diğer yandan GitHub, ikinci yöntemi kullanır. Yazım sırasında, GitHub UPA'sı üçüncü sürümdedir ve sürümü Accept üstbilgisiyle belirtebilirsiniz:

```bash
curl https://api.github.com -H Accept:application/vnd.github.v3+json
```

## C++'ın Sahneye Çıkışı

C++, o yolda ilerlese de şu an pilleri içinde bir programlama dili değildir. Bununla birlikte geliştirdiğiniz yazılıma en uygun enerji sistemini kolayca kullanmanıza izin verir. Bunun için genel olarak şu iki seçenekten birini tercih etmelisiniz:

- Qt ve benzeri, her şeyi bir arada sunan kütüphaneler
- Her bir tek iş için tasarlanmış ayrı ayrı kütüphaneler

### Qt Yolu

### Ayrı Ayrı kütüphaneler



## Özet
Bu makalede, bir REST UPA'sının ne olduğunu; `GET`, `POST`, `PUT`, `PATCH` ve `DELETE` yöntemleriyle istekte bulunmak için curl'ü nasıl kullanabileceğinizi öğrendiniz. Ayrıca, isteklerinizi `-u` seçeneğiyle nasıl kimlik doğrulamasıyla yapabileceğinizi ve HTTP durumlarının ne anlama geldiğini de öğrendiniz.

Umarım bu makale REST UPA'ları hakkında yeterince bilgi edinmenize yardımcı olur ve uygulamalarınızı oluştururken bunları akıcı bir şekilde kullanabilirsiniz.

## Kaynak
[Understanding And Using REST APIs - Smashing Magazine](https://www.smashingmagazine.com/2018/01/understanding-using-rest-api/)
