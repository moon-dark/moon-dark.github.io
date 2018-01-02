---
title: '使用Webrtc构建移动平台P2P通讯方案：Android通讯'
date: 2018-01-02 16:38:49
tags:
  - code
---
### 初始化
```java
protected void init() {
    super.init();
    this.addJavascriptInterface(new JsInterface(), "sync");
    this.setWebViewClient(new SyncWebViewClient());
    this.setWebChromeClient(new SyncWebChromeClient());
}

// 父类
private void init() {
    WebSettings settings = this.getSettings();
    settings.setJavaScriptEnabled(true);
    settings.setJavaScriptCanOpenWindowsAutomatically(true);
    settings.setGeolocationEnabled(true);
    String dir = this.getContext().getApplicationContext().getDir("database", Context.MODE_PRIVATE).getPath();
    settings.setGeolocationDatabasePath(dir);
    settings.setAppCacheEnabled(true);
    settings.setDatabaseEnabled(true);
    settings.setDomStorageEnabled(true);
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
        WebView.setWebContentsDebuggingEnabled(true);
    }
    this.setVerticalScrollBarEnabled(false);

    this.setWebChromeClient(new ExtWebChromeClient());
    this.setWebViewClient(new ExtWebViewClient());
}

```
<!-- more -->
### 导出导入
```java
// 文件选择
public class SyncWebChromeClient extends WebChromeClient {
    // For Android < 3.0
    public void openFileChooser(ValueCallback<Uri> valueCallback) {
        handleFileChooser(valueCallback);
    }

    // For Android  >= 3.0
    public void openFileChooser(ValueCallback valueCallback, String acceptType) {
        handleFileChooser(valueCallback);
    }

    //For Android  >= 4.1
    public void openFileChooser(ValueCallback<Uri> valueCallback, String acceptType, String capture) {
        handleFileChooser(valueCallback);
    }

    @Override
    public boolean onShowFileChooser(WebView webView, final ValueCallback<Uri[]> filePathCallback,
                                     FileChooserParams fileChooserParams) {
        Log.e("==>","handleFileChooser II , " + Uri.parse(vbzFilePath));
        if(TextUtils.isEmpty(vbzFilePath))  return true;
        Uri[] resultArray = new Uri[1];
        resultArray[0] = Uri.fromFile(new File(vbzFilePath));
        filePathCallback.onReceiveValue(resultArray);
        return true;
    }

    private boolean handleFileChooser(final ValueCallback valueCallback) {
        if(TextUtils.isEmpty(vbzFilePath))  return true;
        valueCallback.onReceiveValue( Uri.fromFile(new File(vbzFilePath)) );
        return true;
    }
}


public class JsInterface {
    private static final int MAX_SIZE = 64000;
    @JavascriptInterface
    public void importData(byte[] data) {
        if(mCallback == null ) return;
        try {
            File f = File.createTempFile("vbox", ".vbz", new File("/sdcard/0/"));
            FileOutputStream out = new FileOutputStream(f);
            out.write(data);
            out.close();
            Log.e("==>", f.getAbsolutePath() + ", " + f.length());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @JavascriptInterface
    public void exportData(String[] list) {
        if(mCallback == null ) return;
        if(list == null || list.length == 0) {
            vbzFilePath = mCallback.getAllRecord();
        } else {
            vbzFilePath = mCallback.getRecord(list);
        }

        jscall = "javascript:exportFileFromApp(\"" + vbzFilePath + "\")";
        mHandler.post(new Runnable() {
            @Override
            public void run() {
                loadUrl(jscall);
            }
        });
    }
}
```
