# ハンズオン4: IPアドレス設定を自動化しよう

先ほどのハンズオン3では、コンフィグを直接そのままPlaybook内に指定していました。Ansibleにはコンフィグをそのまま指定するモジュールだけなく、もっと高機能なモジュールもあります。

このハンズオンでは、L3レベルのインターフェース設定をする専用モジュールを利用して、IPアドレス設定を自動化します。

ハンズオンの流れは以下の通りです。

- 4-1. Playbookの作成
- 4-2. Playbookの実行
- 4-3. 実行結果の確認

# 4-1. Playbookの作成

以下の内容のPlaybookを `handson4.yml` として `vi` などのエディタで作成してください。インデントはタブではなく半角スペースです。縦のラインを間違えないようにご注意くだださい。難しければコピペでも構いません。

```yaml
---
- hosts: ios            # 対象のグループ名（インベントリファイル内の定義）
  gather_facts: false   # システム情報収集機能の無効化（時間短縮のため）

  # ここからタスクの定義
  tasks:
    # タスク1: インターフェースへIPアドレスの設定
    - name: config IP address
      cisco.ios.ios_l3_interfaces:
        config:
          - name: GigabitEthernet3
            ipv4:
              - address: 10.1.3.254/24
```

## 解説

Playbook内のタスクについて解説します。

### タスク1: インターフェースへIPアドレスの設定

[`cisco.ios.ios_l3_interfaces` モジュール](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_l3_interfaces_module.html)を利用して、Cisco IOS 機器のインターフェースにIPアドレスを設定するタスクです。

[`config` パラメーター](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_l3_interfaces_module.html#parameter-config)で、設定内容をリストで指定します。

[`name` パラメーター](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_l3_interfaces_module.html#parameter-config/name)で、インターフェース名を、`ipv4` 配下の [`address` パラメーター](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_l3_interfaces_module.html#parameter-config/ipv4/address)で、IPv4 アドレスを指定します。


# 4-2. Playbookの実行

次に、作成したPlaybookを実行します。以下のコマンドでPlaybookを実行してください。

コマンドの実行
```bash
ansible-playbook -i inventory.ini handson4.yml
```

以下は実行結果例です。

```bash
(ansible) [ansible@controller handson]$ ansible-playbook -i inventory.ini handson4.yml

PLAY [ios] *********************************************************************

TASK [config IP address] *******************************************************
changed: [ios01]

PLAY RECAP *********************************************************************
ios01 : ok=1  changed=1 unreachable=0  failed=0  skipped=0  rescued=0  ignored=0   
```

`PLAY RECAP` の表示で、`ok=1`、`changed=1` になることを確認してください。

Playbookに誤りがある場合はエラーが表示されます。その場合は内容を確認し、修正して再実行します。エラーの解析が難しい場合があるので、分からなかったらお声がけください。


# 4-3. 実行結果の確認

Playbookの実行によって、ルーターに以下のコンフィグが投入されたはずです。

```
interface GigabitEthernet3
 ip address 10.1.3.254 255.255.255.0
```

Playbook内では上記のようなコンフィグそのものは指定していませんが、[`cisco.ios.ios_l3_interfaces` モジュール](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_l3_interfaces_module.html)に指定した各パラーメーターの値によって、内部的にコンフィグが生成され、機器に投入される仕組みです。

このコンフィグが入ったか確認しましょう。

ハンズオン3では手動で設定設定後のコンフィグを確認しましたが、今回はコンフィグの表示自体もAnsibleで自動化してみましょう。
コンフィグ表示ような単一の簡単な処理であれば、Playbookを作成せずに、[`ansible` アドホックコマンド](https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html)のみで実現できます。

以下のコマンドを実行して、Ansible経由でコンフィグを表示してください。

```
ansible -i inventory.ini ios -m ios_command -a "commands='show running-config interface GigabitEthernet3'"
```

実行結果例は以下の通りです。`stdout_lines` 配下に目的のコンフィグがあることを確認してください。

```
(ansible) [ansible@controller handson]$ ansible -i inventory.ini ios -m ios_command -a "commands='show running-config interface GigabitEthernet3'"
ios01 | SUCCESS => {
    ...(略)...
    "stdout_lines": [
        [
            "Building configuration...",
            "",
            "Current configuration : 238 bytes",
            "!",
            "interface GigabitEthernet3",               # 注目
            " ip address 10.1.3.254 255.255.255.0",     # 注目
            " ip ospf network non-broadcast",
            " ip ospf dead-interval 40",
            " ip ospf hello-interval 10",
            " ip ospf 1 area 0",
            " ip ospf cost 50",
            " negotiation auto",
            " no mop enabled",
            " no mop sysid",
            "end"
        ]
    ]
}
```


無事にAnsibleで、インターフェースへのIPアドレス設定の自動化を実現できました。

これで、ハンズオン4「IPアドレス設定を自動化しよう」は完了です。

---

# 👉 NEXT（余裕がある人向け）

もしハンズオン時間に余裕がある人は、以下の次のハンズオンに進んでください。

👉 [ハンズオン5: インターフェースの基本設定を自動化しよう](./handson5.md)

時間がちょうど良い方はスライド資料にお戻りください。講師の説明を再開します。

---

🏠 [`README.md` に戻る](../README.md)