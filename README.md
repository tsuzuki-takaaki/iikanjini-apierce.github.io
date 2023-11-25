# Isuconやることリスト

## 1. 全体像の把握
- 仕様の確認(長めに読む)
    - 何が得点になって、何が得点にならないのか、禁止事項は何か...etc
- cloudformation立ち上げの際にregionを間違えないように確認する
- benchmarkを回す度に、DBがDROP & CREATEされるのかを確認する
    - DBに直接入って行った作業が有効かどうか

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
    - bind_addressを変えた後にrestartするのを忘れないように
- DBサーバの分割は初期にやらなくてもいい
- git管理・自動化・ボトルネックの発見・issue管理を完璧にできるようにする
- 午前中にsetup + issue管理を終える

## Tips
- Query
    - DBに入って確認する -> schemaでas codeとして管理する(初期化時にdrop tableが走るはず)
    - 1回の質(index, ...etc)
    - 回数(N+1, ...etc)
- benchmarkを回すとき(initialize時)に毎回drop-tableをしている可能性が高いので、dbで直接indexを貼るのは試す時だけ
    - schemaのところに書けばいいはず
- create index
    - インデックス名は`idx_column`で:yosa:
```sql
create index index_name on table_name (column_name);
CREATE INDEX index_name ON table_name (column1, column2);
```
- create table時
```sql
CREATE TABLE `isu_condition` (
  `id` bigint AUTO_INCREMENT,
  `jia_isu_uuid` CHAR(36) NOT NULL,
  `timestamp` DATETIME NOT NULL,
  `is_sitting` TINYINT(1) NOT NULL,
  `condition` VARCHAR(255) NOT NULL,
  `message` VARCHAR(255) NOT NULL,
  `created_at` DATETIME(6) DEFAULT CURRENT_TIMESTAMP(6),
  INDEX idx_jiaisuuuid_timestamp (`jia_isu_uuid`, `timestamp`),
  INDEX idx_jiaisuuuid (`jia_isu_uuid`),
  PRIMARY KEY(`id`)
) ENGINE=InnoDB DEFAULT CHARACTER SET=utf8mb4;
```
- DBのリモート接続はDB_HOSTのipを変えただけではうまくいかない
    - https://isucon.net/archives/56082639.html
- tmux
    - ctrl + b(プレフィクス) -> "
        - 上下に分割できる
    - ctrl + b -> 9
        - 次のペインに移動できる
- 穴の確認
```sh
% sudo ss -lnpt
```
