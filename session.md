# Oturum

- [Yapılandırma](#configuration)
- [Oturum Kullanımı](#session-usage)
- [Flaş Verisi](#flash-data)
- [Veritabanı Oturumları](#database-sessions)
- [Oturum Sürücüleri](#session-drivers)

<a name="configuration"></a>
## Yapılandırma

HTTP odaklı uygulamalar durum bilgisi taşımadıkları için, oturumlar istekler arasında kullanıcı hakkında bilgi saklamak için bir yol sağlar. Laravel temiz, tek bir API aracılığıyla kullanılabilen çeşitli oturum back-endleri ile birlikte gelir. İçerisinde [Memcached](http://memcached.org), [Redis](http://redis.io) ve veritabanları gibi popüler back-end desteği yer almaktadır.

Oturum yapılandırma ayarları `config/session.php` dosyasında bulunmaktadır. Bu belgede size sunulan iyi belgelenmiş seçenekleri gözden geçirmeyi unutmayın. Ön tanımlı olarak, Laravel `file` oturum sürücüsünü kullanmak üzere yapılandırılmıştır ve bu yapılandırma uygulamaların çoğunda iyi çalışacaktır.

Laravel ile Redis oturumlarını kullanmadan önce, Composer aracılığı ile `predis/predis` paketini(~1.0) yüklemeniz gerekir.

> **Not:** Eğer kayıtlı tüm oturum bilgilerini şifreli bir şekilde saklamanız gerekiyorsa yapılandırma dosyasındaki `encrypt` alanını `true` yapın.

#### Ayrılmış (Rezerve) Anahtarlar

Laravel framework dahili olarak `flash` session anahtarını kullanır, bu nedenle siz oturuma bu isimle bir öğe eklememelisiniz.

<a name="session-usage"></a>
## Oturum Kullanımı

#### Oturumda Bir Öğe Saklamak

	Session::put('anahtar', 'deger');

#### PDizi Oturum Değerine Bir Değer Eklemek

	Session::push('uyeler.takimlar', 'gelistiriciler');

#### Oturumdaki Bir Öğeyi Öğrenmek

	$value = Session::get('anahtar');

#### Bir Öğe Almak Veya Varsayılan Bir Değer Döndürmek

	$value = Session::get('anahtar', 'varsayilan');

	$value = Session::get('anahtar', function() { return 'varsayilan'; });

#### Bir Öğenin Elde Edilmesi ve Oturumdan Çıkartılması

	$value = Session::pull('anahtar', 'varsayilan');

#### Oturumdaki Tüm Verileri Almak

	$data = Session::all();

#### Oturumda Bir Öğenin Olup Olmadığını Tespit Etmek

	if (Session::has('uyeler'))
	{
		//
	}

#### Oturumdan Bir Öğeyi Çıkartmak

	Session::forget('anahtar');

#### Oturumdaki Tüm Öğeleri Çıkartmak

	Session::flush();

#### Tekrar Oturum ID Üretmek

	Session::regenerate();

<a name="flash-data"></a>
## Flaş Verisi

Bazen oturumda sadece sonraki istek için öğeler saklamak isteyebilirsiniz. Bunu `Session::flash` metodunu kullanarak gerçekleştirebilirsiniz:

	Session::flash('anahtar', 'deger');

#### Mevcut Flaş Verinin Bir Başka İstek İçin Yeniden Flaşlanması

	Session::reflash();

#### Flaş Verinin Sadece Bir Alt Kümesinin Yeniden Flaşlanması

	Session::keep(array('uyeadi', 'eposta'));

<a name="database-sessions"></a>
## Veritabanı Oturumları

`database` oturum sürücüsü kullanıyorken, oturum öğelerini taşıyan bir tablo kurulumu gerekecek. Aşağıda, bu tablo için örnek bir `Şema` deklarasyonu gösterilmektedir:

	Schema::create('sessions', function($table)
	{
		$table->string('id')->unique();
		$table->text('payload');
		$table->integer('last_activity');
	});

Tabii ki, bu migrasyonu üretmek için `session:table` Artisan komutunu kullanabilirsiniz!

	php artisan session:table

	composer dump-autoload

	php artisan migrate

<a name="session-drivers"></a>
## Oturum Sürücüleri

Oturum "driver'ı" her istek için oturum verisinin nerede saklanacağını tanımlamaktadır. Laravel çeşitli harika sürücülerle birlikte gelmektedir:

- `file` - oturumlar `app/storage/sessions` klasöründe saklanacaktır.
- `cookie` - oturumlar güvenli, kriptolanmış çerezlerde saklanacaktır.
- `database` - oturumlar kendi uygulamanızın kullandığı bir veritabanında saklanacaktır.
- `memcached` / redis - oturumlar bu hızlı, önbellekleme tabanlı depolardan birisinde saklanacaktır.
- `array` - oturumlar basit bir PHP dizisinde saklanacak ve istekler arasında sebat etmeyecektir.

> **Not:** Array sürücüsü tipik olarak [Unit Testler](/docs/master/testing) çalıştırmak için kullanılır, bu yüzden oturum verileri sürdürülmeyecektir.
