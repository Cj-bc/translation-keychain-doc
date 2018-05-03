# キーチェーンサービスについて

キーチェーンサービスは、パスワード・鍵・証明書を安全に保管することができるストレージを提供します。複数人での使用も可能です。ユーザーは一つのパスワードでキーチェーンをアンロックでき、その後キーチェーンに対応したアプリケーションはパスワードを保存・取得することができます。このガイドには次のような内容が扱われています。
  * キーチェーンサービスの概要
  * 一番一般的に開発者に使われている、関数やデータ形式についての議論
  * 独自のアプリケーションの中でキーチェーンサービスを利用する為の例


# 簡単なまとめ

このドキュメントは、パスワードを保存・取得する方法について集中的に書かれています。あなたのアプリケーションがパスワードを以下のようなユーザーを対象として扱う必要があるのであれば、このドキュメントを読んでください:
  * 複数のユーザー -- (例: たくさんのユーザーを認証する必要のある、メール・スケジューリングサーバー)
  * 複数のサーバー -- (例: 一つ以上のセキュアなデータベースサーバーと情報を交換する必要のある銀行・保険アプリ)
  * パスワードを入力する必要のあるユーザー -- (例: 複数のセキュアなウェブサイトに必要なパスワードを保存するWebブラウザ)

このドキュメントを読むにあたり、認証の仕組みについての特別な知識は必要ありませんが、パスワードの保存・利用関連について実践的に精通している必要はあります。


## キーチェーンとキーチェンサービスAPIについて理解する

キーチェーンは、アプリやセキュアなサービスの代わりに小さなデータの塊を安全ん保存する、暗号化されたコンテナです。キーチェーンサービスAPIを利用してアクセスできます。

関連: [Keychain-Service-Concepts](Keychain-Service-Concepts/about.md)
      [Glossary](Glossary.md)

## キーチェーン項目とキーチェーンの取り扱い

キーチェーンサービスAPIを使うことで、キーチェーンの項目を探し、内容を読むことができます。又、新しく項目を追加・既存の項目を編集することもできます。macOSでは、キーチェーン自体の作成・削除・信用するアプリケーションの管理・APIを利用して他のキーチェーンの操作もできます。

関連: [iOS-Keychain-Service-Tasks](iOS-Keychain-Service-Tasks/about.md)
      [macOS-Keychain-Service-Tasks](macOS-Keychain-Service-Tasks/about.md)


# 参照

[Keychain Service Reference(外部リンク)](https://developer.apple.com/documentation/security/keychain_services)には、キーチェーンサービスAPIが提供する全ての機能と構造が記述されています。このドキュメントで使われているもののほか、キーチェーンアクセスAppのようなキーチェーン管理アプリケーションで主に使われているものも含まれています。

証明書や鍵の保存・取得について詳しくは、　[Certificate, Key, and Trust Services(外部リンク)](https://developer.apple.com/documentation/security/certificate_key_and_trust_services)

ソフトウェア開発におけるより広範囲なセキュリティに関する議論は、　[Security Overview(外部リンク)](https://developer.apple.com/library/content/documentation/Security/Conceptual/Security_Overview/Introduction/Introduction.html#//apple_ref/doc/uid/TP30000976)や [Security Coding Guide(外部リンク)](https://developer.apple.com/library/content/documentation/Security/Conceptual/SecureCodingGuide/Introduction.html#//apple_ref/doc/uid/TP40002415)を参照のこと


copyright (c) 2003,2016 Apple Inc.
