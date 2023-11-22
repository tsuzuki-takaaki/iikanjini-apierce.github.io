# Isuconやることリスト

## 1. 全体像の把握
- 仕様の確認(長めに読む)
    - 何が得点になって、何が得点にならないのか、禁止事項は何か...etc

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
- TODO
    - See 5. ansibleで流すファイルの設定をする

## 4. 言語実装の確認
- 初期の言語実装を確認する
- ※ `service_name`の部分を確認する
```sh
% systemctl status
% systemctl list-unit-files --type=service | grep service_name
```

## 5. ansibleで流すファイルの設定をする
- デプロイスクリプト
- mysql
- nginx
    - log formatまで
- alp.yml
    - この時点でbenchmark流してないので、後で設定でも:yosa:
- `service_name`の変更

See https://github.com/tsuzuki-takaaki/isucon/tree/main/setup

## 6. ansible流す
```sh
% ansible-playbook -i ~/isucon/setup/ansible/hosts.yml ~/isucon/setup/ansible/playbook.yml
```

## 7. 初回benchmarkの実行
- scoreをmemoして置くところを決めておく

