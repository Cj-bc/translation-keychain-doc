macOSでは、ログインキーチェーンのパスワードがユーザーのログインアカウントのパスワードと同じ時、ログインしている間は自動的にアンロックされます。初期状態では、ログインキーチェーンはデフォルトのキーチェーンでもあります。
デフォルトキーチェーンは、関数呼び出しの際に他のキーチェーンが指定されない限り、新しくできたキーチェーン項目を格納するために使われます。特定の他のキーチェーンサービス機能も、他のキーチェーンが指定されていない時デフォルトキーチェーンを使用します。
ユーザーはキーチェーンアクセスユーティリティを用いて、他のキーチェーンをデフォルトキーチェーンに指定することができます。但し、ログインキーチェーンは変わりません。

iOSでは、より簡単です。アプリケーションからアクセスできるキーチェーン（論理的に異なるiCloudキーチェーンを含む）が一つあるだけです。このキーチェーンはユーザーが端末をアンロックした時に自動的にアンロックされ、端末がロックされたときにロックされます。
アプリケーションは、それの所有するキーチェーン項目とアプリの所属するグループで共有されている項目にのみアクセスできます。


# 高レベル関数で基本的なキーチェーンアクセスをする

キーチェーンの構造はかなりの力と柔軟性を提供する一方で、ある程度の複雑さをもたらします。幸運なことに、キーチェーンサービスAPIはアプリが文字通り必要とする共通のキーチェーン処理を扱う高レベル関数をいくつか提供します。

 ----------------------
注意: iOSやmacOSのiCloudキーチェーンは、この節で説明されている関数を提供していません。iCloudキーチェーンにおいて同じタスクを行うには、一段階下がったAPIを使う必要があります。
詳しくはいかに出てくる|Use-Lower-Level-Functions-When-You-Need-More-Control|を参照してください。
 ----------------------

macOSでは、パスワード項目を作成してキーチェーンに追加する際、インターネットパスワードかそれ以外の種類のパスワードかによって二つの関数のうちどちらかを呼ぶ必要があります: `SecKeychainAddInternetPassword`か`SecKeychainAddGenericPassword`です。
例として、インターネットパスワードではない一般的なパスワードを追加するには、以下のようにします。

```
NSString* service = @"myService";
NSString* account = @"username";
NSString* password = @"somethingSecret";
const void* passwordData = [[password dataUsingEncoding:NSUTF8StringEncoding] bytes];
 
OSStatus status = SecKeychainAddGenericPassword(
                      NULL,        // Use default keychain
                      (UInt32)service.length,
                      [service UTF8String],
                      (UInt32)account.length,
                      [account UTF8String],
                      (UInt32)password.length,
                      passwordData,
                      NULL         // Uninterested in item reference
                  );
 
if (status != errSecSuccess) {     // Always check the status
    NSLog(@"Write failed: %@", SecCopyErrorMessageString(status, NULL));
}
```

`keychain reference`に`NULL`を渡した場合、その項目はデフォルトキーチェーンに追加されます。又、システムは最後の引数を使って新しく作成された項目への任意参照(?)を返します。もし項目への参照が必要ない場合は、上記の例にもあるように`NULL`を引数として渡してください。
`add`関数はアクセスオブジェクトを作成し、初期状態で信用されている唯一のアプリケーションとして呼び出し元のアプリケーションをリスト化します。

返り値を調べることで、処理が正常に実行されたかを確かめることができます。処理が失敗する理由として考えられるのは例えば、ユーザーがロックされたキーチェーンの解除に失敗することです。
ロックされたキーチェーンへのアクセスの呼び出しが入ったとき、もしキーチェーンがロックされていればシステムは自動的に、ユーザーにキーチェーンのパスワードを確認します([Figure 1-2](https://developer.apple.com/library/content/documentation/Security/Conceptual/keychainServConcepts/02concepts/concepts.html#//apple_ref/doc/uid/TP30000897-CH204-SW8))。しかし、もしユーザーがキャンセルしたりキーチェーンを解除できなかった場合、`add`オペレーションは適切なステータスとともに失敗します。

その後、格納された項目を読むためには、 `SecKeychainFindInternetPassword`又は` SecKeychainFindGenericPassword`を使用します。先ほどと同様に一般的なパスワードの場合:

```
UInt32 pwLength = 0;
void* pwData = NULL;
SecKeychainItemRef itemRef = NULL;
 
OSStatus status = SecKeychainFindGenericPassword(
                      NULL,         // Search default keychains
                      (UInt32)service.length,
                      [service UTF8String],
                      (UInt32)account.length,
                      [account UTF8String],
                      &pwLength,
                      &pwData,
                      &itemRef      // Get a reference this time
                  );
 
if (status == errSecSuccess) {
    NSData* data = [NSData dataWithBytes:pwData length:pwLength];
    NSString* password = [[NSString alloc] initWithData:data
                                               encoding:NSUTF8StringEncoding];
    NSLog(@"Read password %@", password);
} else {
    NSLog(@"Read failed: %@", SecCopyErrorMessageString(status, NULL));
}
 
if (pwData) SecKeychainItemFreeContent(NULL, pwData);  // Free memory
```

もし項目を作成したのと同じアプリからこの呼び出しを行ったのなら、その項目のACL内にアプリがあるため、キーチェーンサービスは自動的にアクセスを許可します。もし別のアプリからであれば、キーチェーンサービスはユーザーに項目へのアクセスの許可を求め流プロンプトを出します。ユーザーは拒否することも、一度のみ許可することも、永続的に許可することもできます。永続的な許可をする場合、その新しいアプリはその項目のACLに追加され、その後そのアプリからその項目へのアクセスについてユーザーからの許可を求めることはありません。


