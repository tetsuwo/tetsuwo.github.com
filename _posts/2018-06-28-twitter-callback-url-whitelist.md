---
layout: post
title: "Twitter: コールバック URL（Callback URL not approved for this client application ...）"
date: 2018-06-28 09:00:00 +0900
categories: Twitter
tags:
- Twitter
---

自分が管理しているサービスで Twitter ログインまわりで下記のアプリケーションエラーが発生していたため調べてみました。

```
<?xml version="1.0" encoding="UTF-8"?>
<errors>
    <error code="415">Callback URL not approved for this client application. Approved callback URLs can be adjusted in your application settings</error>
</errors>
```

どうやらこのエラー、調べてみると事前にお知らせがあった内容のようで、下の URL がその内容らしいです。

[Action REQUIRED - Sign in with Twitter users must whitelist callback URLs](https://twittercommunity.com/t/action-required-sign-in-with-twitter-users-must-whitelist-callback-urls/105342)

URL の中身をみてみます。

<!-- more -->

> ### Action REQUIRED - Sign in with Twitter users must whitelist callback URLs
> 
> A few weeks ago, we announced several upcoming changes to the developer platform in advance of new European Union data privacy regulations going into effect soon.
> 
> Today, we’d like to share an additional change for customers using Sign in with Twitter:
> 
> Sign in with Twitter allows developers to access Twitter content in order to make it easy for their users to sign in with just a few clicks. Developers use callback URLs as part of this integration in order to provide directions on where a user should go after signing in with their Twitter credentials.
> 
> As part of our continued effort to ensure safety and security in our developer platform, we’re announcing a new requirement that any developer using Sign in with Twitter must explicitly declare their callback URLs in a whitelist on apps.twitter.com.
> 
> In 30 days, we will begin enforcing the whitelist such that any URL not added to the whitelist will fail. This means that URLs can no longer be programmatically overridden in calls to the oauth/request_token endpoint. The callback_url parameter provided must match one of the whitelisted callback URLs. While we generally provide longer than a 30-day notice for changes like this, this timeline allows us to continue to provide a safe and secure experience for developers and our users.
> 
> You can add callback URLs to your whitelist on the applications settings page on apps.twitter.com 1.0k.
> - Enable the setting “Enable Callback Locking” to test that only URLs you have whitelisted are accepted.
> - Callback URLs will automatically be locked and the whitelist will be enforced starting on June 12th. The “Enable Callback Locking” setting will be removed on this date.
> - Check the documentation for more information.

かなり適当ですが翻訳してみました ↓

### 要対応 - ユーザーが Twitter でログインするにはコールバック URL にホワイトリスト登録する必要があり

数週間前、われわれは GDPR がすぐに発効されることを前提としたいくつかの変更点をお知らせしました。

今回、Twitter ログインを利用しているお客様に追加の変更を提案したいと思います。

Twitter ログインはユーザーが数クリックするだけで簡単にログインできるようにするために開発者に Twitter コンテンツへのアクセスを許可しています。
開発者はユーザーが Twitter の資格情報でログインしたあとどこにいくべきか方向づけするためにコールバック URL を使います。

30 日後には、ホワイトリストに登録されていない URL はすべて失敗するように適用を開始します。これは `oauth/request_token` エンドポイントをコールするときにプログラムで URL を上書きできないことを意味します。 `callback_url` パラメーターはホワイトリストに登録されたコールバック URL のいずれかに一致する必要があります。このような変更点は一般的に 30 日以上の告知期間を設けていますが、〜 （ここからよくわからない）


## コールバック URL のホワイトリストの登録方法

コールバック URL は Twitter Application のサイト（ https://apps.twitter.com ）から追加・変更が可能です。

コールバックを受ける先によって設定する内容が変わります。

というのも、ウェブサーバーで受けるかモバイルアプリ（iOS、Android アプリのクライアント側）でコールバック先の指定が違うのです。

### コールバック先が Web の場合

ウェブ側でコールバックを指定するときは、完全なリクエスト URI にする必要があります。

どういうことかというと指定するのはドメインではなく、コールバック先の URL ということです。

`https://twitter.t5o.me`でサイトを運営している場合に、Twitter ログインのコールバック先として `https://twitter.t5o.me/login/callback` で受け取るとしましょう。

この場合、コールバック先 URL として指定するのは `https://twitter.t5o.me/login/callback` です。

`https://twitter.t5o.me` ではないので注意しましょう。


### コールバック先がモバイルアプリの場合

モバイルアプリの場合、アプリケーション間の連携にはクライアント側のアプリケーションのみで画面遷移を完結させることが多いです。

そういった場合に用いられる手法として URL Scheme があります。

もちろんモバイルアプリの Twitter ログインを実装するときに利用するライブラリによって方針は違うと思いますが、ここでは iOS は TwitterKit、Android は twitter4pj を利用している前提とします。

さて iOS の TwitterKit の場合は、当該モバイルアプリに TwitterKit 用の URL スキームを設定する必要があります。

```
twitterkit-{コンシューマーキー}
```

これをクライアント側に

```

```


Twitter アプリケーションのコールバック URL に指定します。

続いて Android の場合は、当該モバイルアプリに設定されている URL スキームを使いまわせますので、既に設定されている URL スキームをコールバック URL に設定することで Twitter ログインを利用できます。



### 参考 URL
- [Action REQUIRED - Sign in with Twitter users must whitelist callback URLs](https://twittercommunity.com/t/action-required-sign-in-with-twitter-users-must-whitelist-callback-urls/105342)
- [Callback URL — Twitter Developers](https://developer.twitter.com/en/docs/basics/callback_url.html)
