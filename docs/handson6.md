# ハンズオン6: チャレンジ問題に挑戦

このハンズオンは、要件とヒントのみをもとにご自身で Playbook を書いて実行するハンズオンです。

ハンズオン6-1、6-2 からなります。順番に指定はありませんので、興味があるものに取り組んでください。


## ハンズオン6-1: スタティックルートの設定（難易度: 中）

### 要件
- ネットワーク `172.16.0.0/16` 宛てを、ネクストホップ `10.1.1.1` とするスタティックルートをルーターに設定する
- Playbook のファイル名は `handson6-1.yml` とする

### ヒント
- スタティックルートの設定は [`ccisco.ios.ios_static_routes`](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_static_routes_module.html) モジュールを利用


## ハンズオン6-2: ルーターから ping の実行（難易度: 中）

### 要件
- ルーターから `10.1.1.1` 宛てに ping を実行し、結果を画面に表示する
- Playbook のファイル名は `handson6-2.yml` とする

### ヒント
- ping の実行は [`cisco.ios.ios_ping`](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_ping_module.html) モジュールを利用
- 画面への表示は [`ansible.builtin.debug](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/debug_module.html) モジュールを利用

---

# 🎉 GOAL 🎉

ハンズオンコンテンツはすべて完了です。お疲れさまでした。

なお、参考Playbook は [../playbooks](../playbooks) ディレクトリにあります。

スライド資料にお戻りください。講師の説明を再開します。



---

🏠 [`README.md` に戻る](../README.md)

