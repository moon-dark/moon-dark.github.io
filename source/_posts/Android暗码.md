---
title: Android暗码
tags:
  - Android
  - code
date: 2017-05-12 18:22:27
---

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
