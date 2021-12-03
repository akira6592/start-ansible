# ハンズオン3: コンフィグ投入を自動化しよう

このハンズオンでは、ネットワーク機器への設定コンフィグの投入を自動化します。ここでは、参照先NTPサーバーの指定（`ntp server` コマンド）をします。

ハンズオンの流れは以下の通りです。

- 3-1. Playbookの作成
- 3-2. Playbookの実行
- 3-3. 実行結果の確認

# 3-1. Playbookの作成

以下の内容のPlaybookを `handson3.yml` として `vi` などのエディタで作成してください。インデントはタブではなく半角スペースです。縦のラインを間違えないようにご注意くだださい。難しければコピペでも構いません。

```yaml
---
- hosts: ios            # 対象のグループ名（インベントリファイル内の定義）
  gather_facts: false   # システム情報収集機能の無効化（時間短縮のため）

  # ここからタスクの定義
  tasks:
    # タスク1: 参照NTPサーバーの設定
    - name: config ntp
      cisco.ios.ios_config:
        lines:    # 投入したい設定コマンドを指定
          - ntp server 10.0.0.11
          - ntp server 10.0.0.12
```

## 解説

Playbook内のタスクについて解説します。

### タスク1: 参照NTPサーバーの設定

[`cisco.ios.ios_config` モジュール](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_config_module.html)を利用して、Cisco IOS 機器に設定コマンドを投入するタスクです。

[`lines` パラメーター](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_config_module.html#parameter-lines)で、投入したい設定コマンドをリストで指定します。

なお、グローバルコンフィギュレーションモードに移行する `configure terminal` コマンドは、モジュール内部で実行されます。そのため、Playbook側には指定不要です。

# 3-2. Playbookの実行

次に、作成したPlaybookを実行します。以下のコマンドでPlaybookを実行してください。

コマンドの実行
```bash
ansible-playbook -i inventory.ini handson3.yml
```

以下は実行結果例です。

```bash
(ansible) [ansible@controller handson]$ ansible-playbook -i inventory.ini handson3.yml

PLAY [ios] *********************************************************************

TASK [config ntp] **************************************************************
[WARNING]: To ensure idempotency and correct diff the input configuration lines should be similar to how they appear if present in the running configuration on device
changed: [ios01]

PLAY RECAP *********************************************************************
ios01 : ok=1  changed=1  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0   
```

`PLAY RECAP` の表示で、`ok=1`、`changed=1` になることを確認してください。（`[WARNING]` はモジュールの仕様によるものですので無視してください）

Playbookに誤りがある場合はエラーが表示されます。その場合は内容を確認し、修正して再実行します。エラーの解析が難しい場合があるので、分からなかったらお声がけください。


# 3-3. 実行結果の確認

Playbookの実行によって、ルーターに以下のコンフィグが投入されたはずです。

```
ntp server 10.0.0.11
ntp server 10.0.0.12
```

はじめての設定自動化ですので、機器側の設定を手動で確認してみましょう。

まず、Ansibleサーバーから、ルーターにsshでログインしてください。

```bash
# AnsibleサーバーからルーターへのSSH
ssh ansible@ios01    # パスワードはAnsibleサーバーと同じ
```

次に、ルーター上で特権モードに移行後、 `show running-config | inc ntp` コマンドを実行してください。
```bash
# ルーター上での確認
enable    # パスワードはログインと同じ
show running-config | inc ntp
```

結果を見て、設定が入っていることを確認してください。（`ntp server ntp.nict.jp` は元々設定済みのものです）

```bash
# 想定結果（順番は異なる場合があります）
ios01#show running-config | inc ntp
ntp server ntp.nict.jp
ntp server 10.0.0.11
ntp server 10.0.0.12
```

確認できたら、ルーター上で `logout`コマンドを実行し、Ansibleサーバーに戻ってください。
```
# ルーターからログアウト
logout
```

Ansibleサーバーに戻り、プロンプトがサーバーの以下のようなサーバーのものになったことを確認してください。
```
(ansible) [ansible@controller handson]$ 　　　　　　　# サーバー側のプロンプト
```

無事にAnsibleでコンフィグ投入の自動化を実現できました。

これで、ハンズオン3「コンフィグ投入を自動化しよう」は完了です。お疲れさまでした。

---

# 👉 NEXT（余裕がある人向け）

もしハンズオン時間に余裕がある人は、以下の次のハンズオンに進んでください。

以下の次のハンズオンに進んでください。

👉 [ハンズオン4: IPアドレス設定を自動化しよう](./handson4.md)

---

🏠 [`README.md` に戻る](../README.md)