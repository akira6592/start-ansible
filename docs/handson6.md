# ハンズオン6: チャレンジ問題に挑戦

このハンズオンは、要件とヒントのみをもとにご自身で Playbook を書いて実行するハンズオンです。

ハンズオン6-1、6-2 からなります。順番に指定はありませんので、興味があるものに取り組んでください。


## ハンズオン6-1: スタティックルートの設定（難易度: 中）

### 要件
- ネットワーク `172.16.0.0/16` 宛てを、ネクストホップ `10.1.1.1` とするスタティックルートをルーターに設定する
- Playbook のファイル名は `handson6-1.yml` とする

### ヒント
- タスクは1つのみ
- スタティックルートの設定は [`ccisco.ios.ios_static_routes`](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_static_routes_module.html) モジュールを利用する


## ハンズオン6-2: ルーターから ping の実行（難易度: 中）

### 要件
- ルーターから `10.1.1.1` 宛てに ping を実行し、結果を画面に表示する
- Playbook のファイル名は `handson6-2.yml` とする

### ヒント
- タスクは2つ
- ping の実行は [`cisco.ios.ios_ping`](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_ping_module.html) モジュールを利用する
- 画面への表示は [`ansible.builtin.debug](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/debug_module.html) モジュールを利用する

## ハンズオン6-3: インターフェースの状態確認の自動化（難易度: 高）

### 要件
- ルーターの `GigabitEthernet3` の link status が `up` であること確認する
- Playbook のファイル名は `handson6-3.yml` とする

### ヒント
- タスクは2つ
- コマンドの実行には [`ansible.utils.cli_parse`](https://docs.ansible.com/ansible/latest/collections/ansible/utils/cli_parse_module.html) モジュールを利用する
  - show コマンドの実行結果から効率よく値を切り出すためにパーサーを利用。今回は `ansible.netcommon.ntc_templates` を利用するために以下のように指定する
    ```yaml
      #　(略)
      ansible.utils.cli_parse:
        command: # (略)
        parser:
          name: ansible.netcommon.ntc_templates
    ```
- 「～が～であること」という確認は [`ansible.builtin.assert`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/assert_module.html) モジュールを利用する
  - `that` オプションに条件を指定
  - showコマンド実行結果が変数 `res_show_interface` に入っている場合、以下の指定をすると `GigabitEthernet3` の link status が切り出せる
    ```yaml
    res_show_interfaces.parsed | selectattr("interface", "==", "GigabitEthernet3")).0.link_status
    ```
  - 起動している場合は `up` となる

---

# 🎉 GOAL 🎉

ハンズオンコンテンツはすべて完了です。お疲れさまでした。

なお、参考Playbook は [../playbooks](../playbooks) ディレクトリにあります。

スライド資料にお戻りください。講師の説明を再開します。



---

🏠 [`README.md` に戻る](../README.md)

