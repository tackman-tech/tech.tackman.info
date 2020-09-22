# Mastodon障害対応レポート

## 障害発生①

mastodon.tackman.info へアクセスすると、Cloudflareの504画面になる状態なのを確認

## 原因調査①

webインスタンス（フロントサーバー + sidekiqを一台で動かしている）のストレージが満杯になっていた

- Dockerコンテナ内のログローテーションがちゃんと回っていない模様

## 対応①

webインスタンス内の不要aptキャッシュを削除してストレージ容量確保
  
- そのうち問題が起きる可能性があるが、過去の利用状況から年単位で大丈夫と判断

## 障害発生②

ストレージ問題を解決したが、起動後一定時間でWebサーバが50xになる

## 原因調査②

挙動を調べるとsidekiqが起動→数秒でダウン→再起動を繰り返している。頭をひねった後に

```sh
docker-compose logs -f
```

でログを確認したところ一発で原因が確定、Redisへの接続に失敗していた。ログ読むのは大事

## 対応②

- Redis用インスタンスをf1-micro(1vCPU / 600MB)からg1-small(1vCPU / 1.7GB)にスケールアップ
- ついでにmastodonのバージョンを2.5 -> 3.2 へ更新

```sh
最新版をGitHubからgit clone
.env.production と docker-compose.yml を旧バージョンからコピー

$ docker-compose run --rm web rails db:migrate
$ docker-compose run --rm web rails assets:precompile

$ docker-compose up -d
```

## 結果

- 再起動から一週間、これで問題なく動いている
- Redisのインスタンスサイズをケチり過ぎたのがダメだった模様

## 課題

Redisインスタンスをスケールアップしたせいで、月の費用が500〜1000円程度これまでより余計にかかることになってしまった。なんとかしたい

## 参考文献

Dockerで雑にMastodonを起動する方法 https://qiita.com/zembutsu/items/fd52a504321dd5d6f0b8

- 公式であまり解説されていないDocker利用時のオペレーションがまとまっていました
