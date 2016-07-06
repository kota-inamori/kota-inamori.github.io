## Matchingood Laravel勉強会
### 2.ルーティングとミドルウェア
稲森 高太

---

### はじめに
この勉強会では，基本的にReadouble([https://readouble.com](https://readouble.com))の内容を，私が噛み砕いたものをお伝えします．

こちらのサイトもぜひご一読ください．

---

### ルーティングとは
* あるパスにアクセスした時に，何をするかを指定すること
* MGでは，ルーティングはすべてphpファイルの直接指定
* Laravelでは，直接(無名)関数を使ったり，あるコントローラ(php)のメソッドを指定して呼び出すことができる．

* 例:[Workingoodのルーティング](https://github.com/matchingood/haken_mypage/blob/master/app/Http/routes.php)

---

### ルーティングの設定
* /App/Http/routes.phpに記述する．
* 例えば，各パスへの各リクエストに対応した動作を指定するには，以下のように書く．

```php
Route::get('/', function () {
        return 'Hello World';
});

Route::post('foo/bar', function () {
        return 'Hello World';
});

Route::put('foo/bar', function () {
        //
});

Route::delete('foo/bar', function () {
        //
});
```

>>>

* また，第2引数にはコントローラのメソッドを直接指定することもできる

```php
Route::get('foo/bar', 'foobarController@index');
```

---

### パスからパラメータが欲しい時
* 以下のように書くと，パスからパラメータを取得できる

```php
// 関数の場合は，引数に渡される
Route::get('user/{id}', function($id) {
    //
});

// メソッドを指定した場合，そのメソッドの引数に渡される
Route::get('user/{id}', 'userController@getUser');
```

>>>

* また，パラメータを任意にすることもできる

```php
Route::get('user/{name?}', function($name = 'john') {
    //
});
```

---

### パラメータを限定する

* 正規表現を用いて，パラメータを限定することができる．

```php
Route::get('{role}/agenda', 'agendaController@index')
    ->where('role', 'staff|client');
```

---

### ミドルウェアとは
* HTTPリクエスト/レスポンスを，コントローラで処理する前後にフィルタリングする仕組み
* 例えば，認証済みユーザーはログインページをスキップ．
* 逆に未ログインのユーザーが認証が必要なページにアクセスしたらログイン画面に飛ばすなど
---

### ミドルウェアの作り方
* /App/Http/Middlewareにphpファイルを作り記述する．
* 手書きでもいいが，以下を端末で実行すると雛形を作ってくれるので便利．
* handleメソッドに実装を書く
```
php artisan make:middleware [middlewareName]
```


>>>

* 作ったミドルウェアは，/App/Http/Kernel.phpの$middlewareに登録すれば，グローバルに適用される

* 特定のルートに対して適用することもできる．

---

### リクエストを処理する
* next($request)を返すで，次のミドルウェアにリクエストを流している感じ

```php
public function handle($request, Closure $next)
{
    // アクションを取るコードをここに記述

    return $next($request);
}
```
例:[アクセスログ](https://github.com/matchingood/haken_mypage/blob/master/app/Http/Middleware/AccessLogger.php)

>>>

### レスポンスを処理する
* next($request)は，すべてのミドルウェアとアプリケーションを通ったあとの結果，すなわちレスポンスになっている．
* $responseを返すことで，クライアント側にレスポンスを渡す．

```php
public function handle($request, Closure $next)
{
    $response = $next($request);

    // アクションを取るコードをここに記述

    return $response;
}
```

例:[フィーチャーフォンのための文字コード変換](https://github.com/matchingood/haken_mypage/blob/master/app/Http/Middleware/SupportFeaturePhone.php)

---

## おわり
