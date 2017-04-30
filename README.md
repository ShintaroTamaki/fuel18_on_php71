# 本リポジトリ
MacBookProに　PHP 7.1.4 FuelPHP 1.8 をインストールし、<br>
oil test ができるまでを検証したメモと結果のソースです、<br>
これをご参考にされて不具合等が発生しても責任は持ちません。

## PHPインストール
```
$ brew install homebrew/php/php71
$ brew install homebrew/php/php71-xdebug
$ php -v
PHP 7.1.4 (cli) (built: Apr 14 2017 15:02:18) ( NTS )
Copyright (c) 1997-2017 The PHP Group
Zend Engine v3.1.0, Copyright (c) 1998-2017 Zend Technologies
    with Xdebug v2.5.3, Copyright (c) 2002-2017, by Derick Rethans
```
## FuelPHP 1.8 インストール
1. GitHub のブランチをZIPダウンロードして解凍（チェックアウトしても良かった）<br>
https://github.com/fuel/fuel/tree/1.8/master
1. 必要なファイルのみコピー
```
$ ls -al
total 3160
drwxr-xr-x  11 shintaro.tamaki  staff      374  4 29 00:19 .
drwxr-xr-x   5 shintaro.tamaki  staff      170  4 28 23:53 ..
-rw-r--r--@  1 shintaro.tamaki  staff     6148  4 29 00:19 .DS_Store
drwxr-xr-x  11 shintaro.tamaki  staff      374  4 28 23:53 .git
-rw-r--r--   1 shintaro.tamaki  staff      648  4 28 23:53 .gitignore
-rw-r--r--   1 shintaro.tamaki  staff      387  4 29 00:01 README.md
-rwxr-xr-x@  1 shintaro.tamaki  staff     1496  1 15 09:50 composer.json
-rwxr-xr-x@  1 shintaro.tamaki  staff  1593071  1 15 09:50 composer.phar
drwxr-xr-x@  6 shintaro.tamaki  staff      204  4 29 00:09 fuel
-rwxr-xr-x@  1 shintaro.tamaki  staff     1832  1 15 09:50 oil
drwxr-xr-x@  7 shintaro.tamaki  staff      238  1 15 09:50 public
```
1. composer で必要なパッケージを取得
```
$ ./composer.phar self-update
$ ./composer.phar update
```
## PHPUnit パッケージ追加
デフォルトのままでは composer.json に PHPUnit が入ってないので追加する<br>
PHPUnitの最新版（2017/04/29時点）は 6.1 だが、<br>
http://tomomik.hatenablog.com/entry/2017/03/12/074003 <br>
こんな記述もあったので、5.7を使うことにする
```
$ ./composer.phar require --dev phpunit/phpunit ^5.7
$ ./composer.phar require --dev phpunit/php-invoker
$ ./composer.phar require --dev phpunit/dbunit 2.0.*
```
## PHPUnitへのPath設定
oil.php で Path を設定する。
```
$ cp fuel/packages/oil/config/oil.php fuel/app/config/.
```
```
fuel/app/config/oil.php
  // Path を設定
  'autoload_path' => '' ,
  'binary_path'   => APPPATH.'../vendor/bin/phpunit' ,
```
## oil test
やってみる、でもエラー出てる・・・
```
$ php oil test
Tests Running...This may take a few moments.
PHPUnit 5.7.19 by Sebastian Bergmann and contributors.

...............................................................  63 / 420 ( 15%)
............................................................... 126 / 420 ( 30%)
...............FFF............................................. 189 / 420 ( 45%)
............................................................... 252 / 420 ( 60%)
............................................................... 315 / 420 ( 75%)
............................................................... 378 / 420 ( 90%)
..........................................                      420 / 420 (100%)

Time: 912 ms, Memory: 14.00MB

There were 3 failures:
```
<br>
ググるとforumにあり、<br>
https://fuelphp.com/forums/discussion/14087/test-failures-in-fresh-project<br>
fuel/core にこんな修正が・・・<br>
https://github.com/fuel/core/commit/c28a83a2e1cfa32a88ed5c1a93a79de3547a873e<br>
手動でマージ！テスト<br>

```
$ php oil test
Tests Running...This may take a few moments.
PHPUnit 5.7.19 by Sebastian Bergmann and contributors.

...............................................................  63 / 420 ( 15%)
............................................................... 126 / 420 ( 30%)
............................................................... 189 / 420 ( 45%)
............................................................... 252 / 420 ( 60%)
............................................................... 315 / 420 ( 75%)
............................................................... 378 / 420 ( 90%)
..........................................                      420 / 420 (100%)

Time: 894 ms, Memory: 14.00MB

OK (420 tests, 516 assertions)
```
通った！<br>
以上です。


# 参考
[PHP7でFuelPHPを使うぞ！！](http://tech-blog.shucream.net/entry/2016/02/01/210559)<br>
[FuelPHP でユニットテスト + カバレッジ取得](http://qiita.com/hira3/items/f14bbc64b6a01d575e57)<br>
[FuelPHPでPHPUnitを使用するときに'PHPUnit_Framework_TestCase' not found って出た](http://tomomik.hatenablog.com/entry/2017/03/12/074003)
