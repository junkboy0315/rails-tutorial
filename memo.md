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

### リソースを起点に設定してみる

#### コントローラの生成

```rb
rails generate controller Articles
# app/controllers/welcome_controller.rb

# アクションを指定して作成する場合
# たとえば、indexアクションを最初から作成したい場合などは下記のようにする。
# こうするとアクションとルーティングが自動的に作成される。
rails generate controller Welcome index
```

#### ルーティング

```rb
# config/routes.rb
Rails.application.routes.draw do
  # リソースで一括指定する場合
  resources :articles
  # `rails routes`をたたくと Resourceful なエンドポイントの一覧を確認することができる。
  # この 1 行だけでこれらのエンドポイントが生成される。

  # 以下、個別に指定する場合

  # localhost/welcome/index へのアクセスに
  # Welcomeコントローラーのindexアクションを使用する
  get 'welcome/index'

  # ルートへのアクセスをWelcomeコントローラのindexアクションにマップする
  root 'welcome#index'
end
```

### 新規作成ページ(new)の作成

#### アクション

```rb
# app/controllers/articles_controller.rb
class ArticlesController < ApplicationController
  # 下記を追加する
  def new
  end
end
```

#### テンプレート

`app/views/articles/new.html.erb`というテンプレートファイルを作成する。

```erb
<h1>Creating New Articles</h1>
```

この状態で`localhost/articles/new`にアクセスするとめでたくページが表示される。

#### フォーム

- フォームの設定は`form_with`というヘルパーを使って行う。
- `scope`に対象のリソースをシンボルで指定する。
- `url`に POST 先を指定する
  - `rails routes`したときの Prefix が`***_path`の`***`の部分（上記では`articles`）であるエンドポイントに POST される(下記参照)
- ブロックの中ではフォームビルダー`form`を使ってコンポーネントを配置していく。
- なお、デフォルトでは Ajax で POST されるので、下記ではわかりやすさのために`local: true`で Ajax を無効にしている。

```erb
<%= form_with scope: :article, url: articles_path, local: true do |form| %>
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

5.3 から
