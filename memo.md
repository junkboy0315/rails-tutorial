# Ruby on rails

## チュートリアル

### セットアップ

```sh
gem install rails
rails new PROJECT_NAME
cd PROJECT_NAME
# 起動
rails server
```

### コントローラー

Welcome というコントローラーを、index というアクションとともに作成

```sh
rails generate controller Welcome index

# app/controllers/welcome_controller.rb
# app/views/welcome/index.html.erb
# などが生成される
```

### ルーティング

`config/routes.rb`に記載する

```rb
Rails.application.routes.draw do
  # localhost/welcome/index へのアクセスを
  # Welcomeコントローラーのindexアクションが使用される
  get 'welcome/index'

  # ルートへのアクセスをWelcomeコントローラのindexアクションにマップする
  root 'welcome#index'
end
```

### リソースを起点に設定してみる

まずリソースをルーティングファイルに記載する

```rb
Rails.application.routes.draw do
  resources :articles
end
```

`rails routes`をたたくと Resourceful なエンドポイントの一覧を確認することができる。
この 1 行だけでこれらのエンドポイントが生成される。

次にそのリソースを扱うコントローラを作成する。

```rb
rails generate controller Articles
```

生成された`app/controllers/articles_controller.rb`を下記にようにする

```rb
class ArticlesController < ApplicationController
  # 下記を追加する
  def new
  end
end
```

`app/views/articles/new.html.erb`というテンプレートファイルを作成する。

```erb
<h1>Creating New Articles</h1>
```

この状態で`localhost/articles/new`にアクセスするとめでたくページが表示される。

#### フォーム

フォームの設定は`form_with`というヘルパーを使って行う。

```erb
<%= form_with scope: :article, local: true do |form| %>
  <p>
    <%= form.label :title %><br>
    <%= form.text_field :title %>
  </p>

  <p>
    <%= form.label :text %><br>
    <%= form.text_area :text %>
  </p>

  <p>
    <%= form.submit %>
  </p>
<% end %>
```

`scope:`に対象のリソースをシンボルで指定する。
ブロックの中ではフォームビルダー`form`を使ってコンポーネントを配置していく。

上記の状態では POST 先が不正なので、`form_with`の`:url`オプションに設定を行う。

```erb
<%= form_with scope: :article, url: articles_path, local: true do |form| %>
```

上記のように設定すると、`rails routes`したときの Prefix が`***_path`の`***`の部分（上記では`articles`）であるエンドポイントに POST される。

```txt
      Prefix Verb   URI Pattern                  Controller#Action
welcome_index GET    /welcome/index(.:format)     welcome#index
     articles GET    /articles(.:format)          articles#index
              POST   /articles(.:format)          articles#create <=コレ
  new_article GET    /articles/new(.:format)      articles#new
 edit_article GET    /articles/:id/edit(.:format) articles#edit
      article GET    /articles/:id(.:format)      articles#show
              PATCH  /articles/:id(.:format)      articles#update
              PUT    /articles/:id(.:format)      articles#update
              DELETE /articles/:id(.:format)      articles#destroy
         root GET    /                            welcome#index
```

なお、デフォルトでは Ajax で POST されるので、上記ではわかりやすさのために`local: true`で Ajax を無効にしている。

5.3 から
