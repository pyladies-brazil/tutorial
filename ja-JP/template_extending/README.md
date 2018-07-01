# テンプレートを拡張しよう

Djangoが持つさらに素敵なことは**テンプレート拡張**です。これは何を意味してるでしょうか？それはHTMLの共通部分をウェブサイトの別なページで使えるということです。

テンプレートは同じ情報やレイアウトを一つ以上の場所で利用したいときに役立ちます。 各ファイル内で自分で繰り返す必要はありません。 さらにもし何か変更したい場合、各テンプレートを変更する必要はなく、1回変更すればいいだけです！

## 基本テンプレートを作成する

基本テンプレートはあなたのウェブサイトの各ページを拡張するための最も基本的なテンプレートです。

`blog/templates/blog/`以下に`base.html`ファイルを作ってみましょう。

    blog
    └───templates
        └───blog
                base.html
                post_list.html
    

それから、以下のように`post_list.html`から`base.html`ファイルにすべてコピーしましょう。

{% filename %}blog/templates/blog/base.html{% endfilename %}

```html
{% load staticfiles %}
<html>
    <head>
        <title>Django Girls blog</title>
        <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
        <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css">
        <link href='//fonts.googleapis.com/css?family=Lobster&subset=latin,latin-ext' rel='stylesheet' type='text/css'>
        <link rel="stylesheet" href="{% static 'css/blog.css' %}">
    </head>
    <body>
        <div class="page-header">
            <h1><a href="/">Django Girls Blog</a></h1>
        </div>

        <div class="content container">
            <div class="row">
                <div class="col-md-8">
                {% for post in posts %}
                    <div class="post">
                        <div class="date">
                            {{ post.published_date }}
                        </div>
                        <h1><a href="">{{ post.title }}</a></h1>
                        <p>{{ post.text|linebreaksbr }}</p>
                    </div>
                {% endfor %}
                </div>
            </div>
        </div>
    </body>
</html>
```

それから`base.html`内の`<body>`(`<body>`と`</body>`の間のすべて)を次で置き換えます。

{% filename %}blog/templates/blog/base.html{% endfilename %}

```html
<body>
    <div class="page-header">
        <h1><a href="/">Django Girls Blog</a></h1>
    </div>
    <div class="content container">
        <div class="row">
            <div class="col-md-8">
            {% block content %}
            {% endblock %}
            </div>
        </div>
    </div>
</body>
```

{% raw %}`{% for post in posts %}` から `{% endfor %}` が以下のように置き換えられたことに気づくかもしれません。 {% endraw %}

{% filename %}blog/templates/blog/base.html{% endfilename %}

```html
{% block content %}
{% endblock %}
```

でも何のために？ あなたはただ`block`を作っただけです！ `{% block %}` タグを、これからHTMLを挿入しようとする場所に使いました。 そのHTMLはこのテンプレート (`base.html`) を拡張した別のテンプレートからやってきます。 どうやって行うかはすぐに示します。

`base.html` を保存し、もう一度 `blog/templates/blog/post_list.html` を開きます。 {% raw %} `{% for post in posts %}` の上と `{% endfor %}` の下をすべてを削除しましょう。 それが終わったら以下のようになっていると思います。{% endraw %}

{% filename %}blog/templates/blog/post_list.html{% endfilename %}

```html
{% for post in posts %}
    <div class="post">
        <div class="date">
            {{ post.published_date }}
        </div>
        <h1><a href="">{{ post.title }}</a></h1>
        <p>{{ post.text|linebreaksbr }}</p>
    </div>
{% endfor %}
```

これをテンプレートのコンテンツブロックのパーツとして使いたいです。このファイルにblockタグを追加する時です！

{% raw %}You want your block tag to match the tag in your `base.html` file. You also want it to include all the code that belongs in your content blocks. To do that, put everything between `{% block content %}` and `{% endblock %}`. Like this:{% endraw %}

{% filename %}blog/templates/blog/post_list.html{% endfilename %}

```html
{% block content %}
    {% for post in posts %}
        <div class="post">
            <div class="date">
                {{ post.published_date }}
            </div>
            <h1><a href="">{{ post.title }}</a></h1>
            <p>{{ post.text|linebreaksbr }}</p>
        </div>
    {% endfor %}
{% endblock %}
```

Only one thing left. We need to connect these two templates together. This is what extending templates is all about! We'll do this by adding an extends tag to the beginning of the file. Like this:

{% filename %}blog/templates/blog/post_list.html{% endfilename %}

```html
{% extends 'blog/base.html' %}

{% block content %}
    {% for post in posts %}
        <div class="post">
            <div class="date">
                {{ post.published_date }}
            </div>
            <h1><a href="">{{ post.title }}</a></h1>
            <p>{{ post.text|linebreaksbr }}</p>
        </div>
    {% endfor %}
{% endblock %}
```

That's it! Check if your website is still working properly. :)

> If you get the error `TemplateDoesNotExist`, that means that there is no `blog/base.html` file and you have `runserver` running in the console. Try to stop it (by pressing Ctrl+C – the Control and C keys together) and restart it by running a `python manage.py runserver` command.