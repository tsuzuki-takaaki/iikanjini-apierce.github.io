# Isuconやることリスト

## 1. 全体像の把握
- 仕様の確認(長めに読む)
    - 何が得点になって、何が得点にならないのか、禁止事項は何か...etc
- cloudformation立ち上げの際にregionを間違えないように確認する

********** nginx + mysql + ruby なる構成の練習しかしていないため、違った場合は終了です。お疲れ様でした！！ **********

## 2. SSH
- ipの確認
- isuconユーザでsshできるようにする
    - (おそらく)3台とも
```sh
% ssh -i sshkey_path ubuntu@ip_or_host
# when login successfully as ubuntu user
% cat ./.ssh/authorized_keys
% sudo -i -u isucon
% mkdir .ssh
% vim .ssh/authorized_keys  # ubuntu userの時に出力した公開鍵をコピーする
```
- `ssh config`を設定してsshしやすくする
```sh
% vim ~/.ssh/config
```

## 3. git init
- 今年は1台だけgit管理をする
    - 先に`/etc`配下のgit管理したいものを`etc/`にcpしておくと:yosa:
        - `/etc/nginx/nginx.cnf`, `/etc/nginx/sites-available/service_name.conf`
        - `/etc/mysql/**/*.cnf`
    - `service_name`配下でgit init
    - .gitignoreの設定
        - webapp配下全て入れてもOK
- sshでpushできるようにする
    - `ssh-keygen -t ed25519 -C "メアド@gmail.com"`
    - https://zenn.dev/v22uta/articles/038036c4b0c686
- localのgitconfigをcpしてcommitできるようにする

## 4. 言語実装の確認
- 初期の言語実装を確認する
- ※ `service_name`の部分を確認する
```sh
% systemctl status
% systemctl list-unit-files --type=service | grep service_name
```

## 5. ansibleで流すファイルの設定をする
- デプロイスクリプト
- `service_name`の変更

See https://github.com/tsuzuki-takaaki/isucon/tree/main/setup

## 6. ansible流す
```sh
% ansible-playbook -i ~/isucon/setup/ansible/hosts.yml ~/isucon/setup/ansible/playbook.yml
```

## 7. 初回benchmarkの実行
- 先に
    - nginxのformatの設定
    - mysqlのformatの設定
- scoreをmemoして置くところを決めておく

## 決まり事
- ロードバランシングを理解できなかったので今年はやらない
    - かなり試したけど、エラーの理由がわからなかった
- 1. nginx + app, 2. DB の2台構成にする
- DBサーバの分割は初期にやらなくてもいい
- git管理・自動化・ボトルネックの発見・issue管理を完璧にできるようにする
- 午前中にsetup + issue管理を終える

