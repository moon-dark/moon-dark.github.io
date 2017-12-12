---
title: Android暗码启动APP
tags:
  - Android
  - code
date: 2017-05-12 18:22:27
---

暗码，比如*#06#查看手机IMEI号。*#06#就是一个暗码。

packages/apps/Dialer/src/com/android/dialer/SpecialCharSequenceMgr.java
<!--more-->
```Java
/**
 * Handles secret codes to launch arbitrary activities in the form of *#*#<code>#*#*.
 *
 * @param context the context to use
 * @param input the text to check for a secret code in
 * @return true if a secret code was encountered and handled
 */
static boolean handleSecretCode(Context context, String input) {
  // Secret codes are accessed by dialing *#*#<code>#*#*

  int len = input.length();
  if (len <= 8 || !input.startsWith("*#*#") || !input.endsWith("#*#*")) {
    return false;
  }
  String secretCode = input.substring(4, len - 4);
  TelephonyManagerCompat.handleSecretCode(context, secretCode);
  return true;
}
```

启动APP操作, AndroidManifest.xml里面注册静态广播：
```Java
<receiver android:name=".MainActivityReceiver" android:exported="true">
    <intent-filter>
         <action android:name="android.provider.Telephony.SECRET_CODE"/>
         <data android:host="6636" android:scheme="android_secret_code"/>
     </intent-filter>
 </receiver>
 ```

增加广播监听器：
```java
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.net.Uri;
import android.util.Log;

public class TestSecretReceiver extends BroadcastReceiver {

    private String TAG = "RuntimeSecretReceiver";
    private static final String SECRET_CODE_ACTION = "android.provider.Telephony.SECRET_CODE";
    private final Uri mUri = Uri.parse("android_secret_code://6636");

    @Override
    public void onReceive(Context context, Intent intent) {
        Log.d(TAG, "on receive secret code" + " action is " + intent.getAction());
        if (intent.getAction() == null) {
            Log.i(TAG, "Null action");
            return;
        }
        if (intent.getAction().equals(SECRET_CODE_ACTION)) {
            Uri uri = intent.getData();
            if (uri.equals(mUri)) {
                //注意启动自己需要的APP主界面即可
                Intent mIntent = new Intent(context, MainActivity.class);
                mIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                context.startActivity(mIntent);
            } else {
                Log.i(TAG, "No matched URI!");
            }
        } else {
            Log.i(TAG, "Not SECRET_CODE_ACTION!");
        }
    }
}
```
