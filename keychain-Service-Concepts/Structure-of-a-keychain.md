# キーチェーンの構造

一定の状況下では、キーチェーンは単純にファイルシステムに格納されているデータベースです。
デフォルトでは、macOSでは、それぞれのログインアカウントは一つずつキーチェーンを持っています。(macOS 10.3以降における新規アカウントに対しては、このキーチェーンは`login.keychain`と名付けられています)。ですが、ユーザーやアプリケーションは望む分だけキーチェーンを作成することができます。
それに対して、iOSにはすべてのアプリから利用可能な一つのキーチェーンがあります。又、ユーザーが端末からiCloudにログインした際、システムは論理的に異なったiCloudキーチェーンを提供します。

キーチェーンはいくつもの、データと属性から構成されるキーチェーン項目を保持しています。属性は、項目のクラスによってキーチェーンの項目と結びついています。作成日時やラベル
などのいくつかの属性は、すべての項目のクラスに共通しています。他は、クラス固有の属性になります。例えば、「ジェネラルパスワード」クラスは、サービスとアカウント名の属性を含んでいます。
一方で「インターネットパスワード項目」クラスは、サーバー名、セキュリティドメイン、プロトコルタイプやパスといったものを、アカウント名と同じように属性として含んでいます。

`KSecAttrSynchronizable`属性を用いて同期可能と示された項目は、iCloudキーチェーンに置かれ、ネットワークを通じて同じiCloudアカウントにログインしている全ての端末のiCloudキーチェーンに同期されます。

パスワードや秘密鍵(非対称暗号で使われる短い機密文字列)のように保護が必要な項目については、暗号化されキーチェーンによって保護されています。証明書のような保護の必要のない項目については暗号化されていません。暗号化されたデータは、キーチェーンがロックされているときアクセス不可能になります。
macOSで、キーチェーンがロックされている間に暗号化されたデータにアクセスしようとした場合、キーチェーンサービスは、キーチェーンパスワードを要求するダイアログを表示します。
iOSでは、デバイスのロックが解除された時にキーチェーンのロックも自動的に解除されるため、またロックされるまでの間はアプリケーションはいつでもそれぞれの持つキーチェーン項目にアクセスすることができます。

 --------------------------------------
*注意*: データとは異なり、項目の属性は機密とは考えられていないので暗号化されません。例えキーチェーンがロックされていたとしても、いつでも参照が可能です。
 --------------------------------------

macOSでは、iCloudで同期されていない項目は、保護されている各項目（とキーチェーン自身）はアクセス制御情報を追加で持っています。それらは「アクセスオブジェクト」と呼ばれるオパキューデータ構造をとります。
アクセスオブジェクトは、その項目についての糸つまたはそれ以上のアクセスコントロールリスト(ACL)エントリーを格納しています。各ACLエントリーは、その項目に対して行うことができる処理(復号化や認証のような)を特定する認証タグを一つまたは複数保持するリストを持っています。
それに加え各ACLエントリーは、ユーザーからの認証なしに指定された処理をおこなうことのできる信頼されたアプリケーションのリストも持っています。
ACLについて詳しくは、[Access Control Lists](https://github.com/Cj-bc/translation-keychain-doc/blob/master/keychain-Services-Concepts/about.md#L           )を参照してください。
**link未完成** **tagもね**

