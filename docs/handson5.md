# ハンズオン5: インターフェースの基本設定を自動化しよう

このハンズオンでは、インターフェースの基本設定をする専用モジュールを利用して、description の設定を自動化します。


ハンズオンの流れは以下の通りです。

- 5-1. Playbookの作成
- 5-2. Playbookの実行
- 5-3. 実行結果の確認

# 5-1. Playbookの作成

インターフェース `GigabitEthernet3` に、`changed_by_ansible` というdescriptionを設定するPlaybookを `handson5.yml` として作成してください。


以下のコンフィグが投入される想定です。
```
interface GigabitEthernet3
 description changed_by_ansible
```

本ハンズオンは未完成のPlaybookを完成させる形式です。以下に、利用するモジュールまでは掲載していますので、完成させてください。

```yaml
---
- hosts: ios            # 対象のグループ名（インベントリファイル内の定義）
  gather_facts: false   # システム情報収集機能の無効化（時間短縮のため）

  # ここからタスクの定義
  tasks:
    # タスク1: インターフェースへdescriptionの設定
    - name: config interface descption
      cisco.ios.ios_interfaces:
        config:   # この下からは未完成


```

## ヒント

タスク1は、[`cisco.ios.ios_interfaces` モジュール](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_interfaces_module.html)を利用して、Cisco IOS 機器のインターフェースの description を設定するタスクです。

インターフェース名を指定するパラメーターと、descriptionを指定するパラメータを、`cisco.ios.ios_interfaces` モジュールのドキュメントの [`Parameters`](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_interfaces_module.html#parameters)から探してください。

モジュールの使い方の雰囲気は、[`Examples`](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_interfaces_module.html#examples)を見るとつかめます。


# 5-2. Playbookの実行

次に、作成したPlaybookを実行します。以下のコマンドでPlaybookを実行してください。

コマンドの実行
```bash
ansible-playbook -i inventory.ini handson5.yml
```

以下は実行結果例です。

```bash
(ansible) [ansible@controller handson]$ ansible-playbook -i inventory.ini handson5.yml

PLAY [ios] *********************************************************************

TASK [config interface descption] **********************************************
changed: [ios01]

PLAY RECAP *********************************************************************
ios01 : ok=1  changed=1  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0   
```

`PLAY RECAP` の表示で、`ok=1`、`changed=1` になることを確認してください。

Playbookに誤りがある場合はエラーが表示されます。その場合は内容を確認し、修正して再実行します。エラーの解析が難しい場合があるので、分からなかったらお声がけください。


# 5-3. 実行結果の確認

Playbookの実行によって、ルーターに以下のコンフィグが投入されたはずです。

```
interface GigabitEthernet3
 description changed_by_ansible
```

Playbook内ではコンフィグそのものは指定していませんが、[`cisco.ios.ios_interfaces` モジュール](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_interfaces_module.html)に指定した各パラーメーターの値によって、内部的にコンフィグが生成され、機器に投入される仕組みです。

このコンフィグが入ったか確認しましょう。ハンズオン4と同様、コンフィグの表示自体もAnsibleで自動化してみましょう。

以下のコマンドを実行して、Ansible経由でコンフィグを表示してください。

```
ansible -i inventory.ini ios -m ios_command -a "commands='show running-config interface GigabitEthernet3'"
```

実行結果例は以下の通りです。`stdout_lines` 配下に目的のコンフィグがあることを確認してください。

```
(ansible) [ansible@controller handson]$ ansible -i inventory.ini ios -m ios_command -a "commands='show running-config interface GigabitEthernet3'"
ios01 | SUCCESS => {
    "stdout_lines": [
            ...(略)...
            "Building configuration...",
            "",
            "Current configuration : 256 bytes",
            "!",
            "interface GigabitEthernet3",         # 注目
            " description changed_by_ansible",    # 注目
            " ip address 10.1.3.254 255.255.255.0",
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

これで、ハンズオン5「インターフェースの基本設定を自動化しよう」は完了です。

---

# 🎉 GOAL 🎉

ハンズオンコンテンツはすべて完了です。お疲れさまでした。

スライド資料にお戻りください。講師の説明を再開します。

---

🏠 [`README.md` に戻る](../README.md)