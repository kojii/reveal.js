## エンジニア勉強会
### 2014・10・24

> 共通レイアウト

*speaker Koji Saito*

## プロジェクト作成で

> さて、プロジェクト作成するか。

~~~
> rails new super_project
~~~

> モデルとか定義して、
画面になんか表示させたい...

> レイアウト考えるのめんどい

### よくよく考えると、
どのプロジェクトもレイアウトの構成ってかわらないよね。


### 共通化

~~~slim
/ layouts/application.html.slim

- content_for :head do
  = stylesheet_link_tag "application", :media => "all", "data-turbolinks-track" => true
  = javascript_include_tag "application", "data-turbolinks-track" => true
- content_for :content do
  = yield

= render :partial => "layouts/base"
~~~


~~~slim
/ layouts/dashboard.html.slim

- content_for :head do
  = stylesheet_link_tag "application", :media => "all", "data-turbolinks-track" => true
  = javascript_include_tag "application", "data-turbolinks-track" => true
- content_for :analytics do
  - if Rails.env == "production"
    javascript:
      (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
      (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
      m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
      })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

      ga('create', 'UA-53703104-2', 'auto');
      ga('require','displayfeatures');
      ga('send', 'pageview');

- content_for :content do
  = render "layouts/admin"
  /= yield
~~~

~~~slim
/ _admin.html.slim

#sidebar-left.col-sm-2
  .nav-collapse.sidebar-nav.collapse.navbar-collapse.bs-navbar-collapse
    ul.nav.main-menu
      - unless current_user.site.new_record?
        li.active
          = link_to dashboard_path do
            i.fa.fa-dashboard
            span ダッシュボード
        li
          a.dropmenu href="/design/analyse" onClick="setNavigationStatus('#report-menu')"
            i.fa.fa-bar-chart-o
            span レポート

#content.col-sm-10
  = yield
~~~

~~~slim
/ layouts/_base.html.slim

doctype html
html
  head
    meta charset="utf-8"
    meta http-equiv="X-UA-Compatible" content="IE=edge"
    meta name="viewport" content="width=device-width, initial-scale=1"
    meta name="author" content="#{yield :meta_author}"
    link rel="shortcut icon" href="/favicon.ico"
    title = content_for?(:title) ? yield(:title) : t("site_title")
    = favicon_link_tag
    = csrf_meta_tags
    = yield(:og)
    - if content_for?(:meta_keywords)
      meta(name="keywords" content="#{ yield :meta_keywords }")
    - if content_for?(:meta_description)
      meta(name="description" content="#{ yield :meta_description }")
    - if content_for?(:no_index)
      meta(name="robots" content="#{ yield :no_index }")
    = yield :external
    = yield :head
    = yield :analytics
  body
    = yield :affiliate_track_tag
    = yield :header
    = yield :content
    = yield :footer
    = yield :bottom_script
~~~

~~~slim
/ dashboard/index.html.slim

- content_for :header, render("layouts/header")
- content_for :footer, render("layouts/footer")
- content_for :bottom_script do
  javascript:
    var lp_graph = #{raw @lp_graph};
    var os_graph = #{raw @os_graph};
    var browser_graph = #{raw @browser_graph};
    var device_graph = #{raw @device_graph};
    var pref_graph = #{raw @pref_graph};
    var inflow_graph = #{raw @inflow_graph};
    ...

.edit.clearfix
  .navigation.clearfix
    ul.breadcrumbs
      li
        = link_to "ホーム", root_path
      li
        = link_to "ダッシュボード", dashboard_path

br
= render partial: "notice"

h2.section-title
  spann.glyphicon.glyphicon-calendar
  |&nbsp;サイトサマリー

.row
  .col-sm-12
    form.form-inline role="form"
      .form-group
        label.control-label for="daterange" 表示期間指定&nbsp;
        input#daterange.form-control type="text" value="#{@from_date} - #{@to_date}" style="width:220px;"
      .form-group
        button.btn.btn-primary type="button" onClick="change_date_dashboard('#{current_user.id}')" 適用
      .form-group
        .btn-group
          button.btn.btn-default type="button" onClick="change_time('過去3ヶ月')" 過去3ヶ月
          button.btn.btn-default type="button" onClick="change_time('先月')" 先月
          button.btn.btn-default type="button" onClick="change_time('先週')" 先週
          button.btn.btn-default type="button" onClick="change_time('昨日')" 昨日
          button.btn.btn-default type="button" onClick="change_time('今日')" 今日
          button.btn.btn-default type="button" onClick="change_time('今週')" 今週
          button.btn.btn-default type="button" onClick="change_time('今月')" 今月
~~~


## おすすめ

> 共通化したレイアウトを使うと
プロジェクトを新規で作成するときや
新らしいレイアウトを作成するときに
なやまずにコーディングを始められるよ。
