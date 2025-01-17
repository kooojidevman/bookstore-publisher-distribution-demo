## 概要
* Rails7(MVC) × MySQL8の環境テンプレート

## 立ち上げ手順

```
// ファイル作成→編集
$ touch Dockerfile Gemfile entrypoint.sh docker-compose.yml 

// rails new(オプション忘れずに)
$ docker-compose run web rails new . --force --no-deps --skip-test --database=mysql 

// database.ymlの設定(developmentとtest)
database: データベース名
host: db 
root: root
password: パスワード

// DB作成
docker-compose run web rails db:create

// イメージの起動
docker-compose up

// 接続
http://localhost:3001
```

## テンプレートを流用する場合の手順

```
TODO
```


## pry-byebug Dockerでの操作方法

```
// コンテナにアタッチし、その後binding.pryを仕込んで処理を止める
docker atacch attach rails-learning-udemy_web_1

// 以下の手順でコンテナアタッチを抜ける
continue入力 →フォアグランドに戻る →「control」+ p → q
```

## タイムゾーン設定

```
// application.rbに以下追加
config.time_zone = 'Tokyo'

// config/initializers/time_formats.rbを作成し以下記述。
// view側でto_sの引数にキーを指定しフォーマットを変換する。
Time::DATE_FORMATS[:datetime_jp] = '%Y年%m月%d日 %H時%M分'

```

## Seedデータ作成

* db/seeds.rb作成

```ruby:db/seeds.rb
if Rails.env == 'developent'
  (1..50).each do |i|
    Board.create(name: "ユーザー#{i}", title: "タイトル#{i}", body: "本文#{i}")
  end
end
```

* rails db:seedを実行

## 日本語化設定

* config/application.rbに以下追加

```ruby
config.i18n.default_locale = :ja
```

* Gemfileに以下追加しインストール

```
gem 'rails-i18n'
```

* ja.ymlを作り日本語を定義する

```
// 例

ja:
  activerecord:
    attributes:
       board:
          name: 名前
          title: タイトル
          body: 本文
```

## テーブルの構造がわかるようにモデルに記述したい

* Gemfileのdevelopmentに以下追加しインストール

```
gem 'annotate'
```

* 以下実行

```
// 既存のモデルにスキーマ情報(アノテーション)書き出し
docker-compose exec web bundle exec annotate --models

// マイグレーション時に自動でモデルにスキーマ情報を書き込む設定
docker-compose exec web rails g annotate:install
```

## 認証(ログイン)関連の設定

* Gemfileのbcryptをインストール後、以下実行しユーザーモデル作成

```
rails g model user name:string password_digest:string
```

* マイグレーション後、コントローラ(sessions, home, users)作成

```
rails g controller sessions create destroy --skip-template-engine
rails g controller home index
rails g controller users new create me  // meはマイページ表示のためのアクション
```

## RSpecの導入

* Gemfileに以下追加

```
gem 'rspec-rails', '~> 3.8'
```

* コマンド実行

```
rails g rspec:install
```

* ジェネレータの使い方

```
// モデル
rails g rspec:model User

// コントローラ
rails g rspec:controller User
```
