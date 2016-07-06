## Matchingood Laravel勉強会
### 1.Laravelフレームワークとは
稲森 高太

---

### 今どきなフレームワーク，Laravel
<img src="/img/laravel-logo.png">
* PHPでのWebアプリケーション開発には欠かせない環境になっている
* 多彩なルーティング，ミドルウェア，テンプレートエンジン，ORM，etc.

---

### そもそもフレームワークとは
* 直訳すれば「枠組み」．アプリケーションを作る土台となる機能を，開発者にわかりやすいレベルに抽象化して提供する．
* わかりやすい=機能が少ない ということはではない．ことWebアプリケーションの開発においては十分な機能を提供している．

---

### 現状のMGとの比較
* MG本体はSmartyというテンプレートエンジンと生のPHPで構成されている．
    + DBへの接続，sql文は直書き
    + すべてのphpファイルがルートディレクトリに置かれており，ルーティングがない(直接指定)．
        * 一応，ControllerとModelの区別は.classの有無でわかるが…
* これらはLaravelを使ってよりよく整備できる．

---

### Laravelを使うと 1
* DB周り
    + DBへのアクセスは，予めORMModelを書いておけば，オブジェクトのプロパティのように参照できる
    + ORMModelがなくても，ヘルパー関数によって可読性の高い記述が可能．

>>>

* ORMを用いたアクセス
```php
// $user->relationsはuserテーブルとuser_relationsテーブルの
// 一対多の関係をオブジェクトにマッピング
// 生SQLを使うと select文を実行->得られた配列にforeachという
// 過程を踏むことになり，コードが増える．
foreach ($user->relations as $relation) {
    try {
        $mghakenContacts[$relation->mg_url] = MghakenContactService::pull(
            $relation->mg_url
        );
    } catch (Exception $e) {
        return response()->json(['status' => 'error']);
    }
}
```

>>>

* DBクラスのヘルパー関数によるアクセス
```php
// leftJoinやwhereの条件ををメソッドチェーンで見やすく書き下せる．
// もちろん，各文字列にはエスケープがかかるので，SQLインジェクションの心配もない．
$client = DB::table('users')
    ->leftJoin('user_relations', 'users.id', '=', 'user_relations.user_id')
    ->leftJoin('user_roles', 'users.id', '=', 'user_roles.user_id')
    ->where('mg_url', '=', $clientInfo['url'])
    ->where('mg_id', '=', $clientInfo['id'])
    ->where('name', '=', 'client')
    ->first();
```

---

### Laravelを使うと 2
* ルーティング
    + Laravelのインストール時に，すでにディレクトリの階層構造ができている．(たとえば，コントローラは/App/Http/Controllerに配置する)
    + アクセスするPathとアクション(主にControllerのメソッド)を紐付けるには，/App/Http/route.phpを書けばOK！
    + 各PathにGET/POST等をリクエストした時のアクションを，直感的に指定できる．

>>>

```php:route.php
// シフトへのURLパターン
Route::get('/agenda', function () {
    $yearMonth = date('Y/m');
    return redirect("/agenda/{$yearMonth}");
});
Route::get('/agenda/{year}', function ($year) {
    $month = date('m');
    return redirect("/agenda/{$year}/{$month}");
})->where(['year' => '[0-9]{4}']);
Route::get('/agenda/{year}/{month}', "AgendaSheetController@index")
    ->where(['year' => '[0-9]{4}', 'month' => '[0-9][0-9]?']);
```

---

### おまけ:bladeテンプレート
* Smartyと同じテンプレートエンジン．制御構文等少し異なるが，使い方はおおよそ同じ
* Smartyには独自の構文が多くあったが(たとえば，phpの関数を使うには{$hoge|captalize}のように|を挟んで記述するとか)，bladeテンプレートはほぼphpと同様に書ける．
    + 拡張子も.blade.phpなので…
    + "{}"の中身は，もうphpの空間と思っても良い
* 制御構文はよりphpに近いものになっている->覚えることが少なくて済む．
* 詳しくはhaken_mypageレポジトリのresouces/viewsあたりのコードを読んでください．

---

### わからないことがあれば
* だいたいググれば出てきます
    - 英語の記事が多いですが(StackOverflowとか)
    - とはいえ，[http://readouble.com/](http://readouble.com/)とか[https://laravel10.wordpress.com/](https://laravel10.wordpress.com/)を見るとなんとかなります．
        + 後者は何していいかわからない時に，前者は何をするのかわかっている時に読むといいかも？
    - 僕や妹尾さんがいるときはぜひ聞いてください．


---

## おわり
もし，次回はここを掘り下げてほしい！ってところがあれば

是非お願いします
