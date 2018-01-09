---
title: '使用Webrtc构建移动平台P2P通讯方案：Android通讯'
date: 2018-01-02 16:38:49
tags:
  - code
---
### Java原生实现，从JCenter获取官方预编译库
[WebRTC > Native code > Android](https://webrtc.org/native-code/android/)<br>
Android Studio 3增加依赖：
```
implementation 'org.webrtc:google-webrtc:1.0.+'
```
Android Studio 2增加依赖：
```
compile 'org.webrtc:google-webrtc:1.0.+'
```

### Webview通讯实现（Javascript/Native interface）
[cordova-plugin-file](https://github.com/apache/cordova-plugin-file)<br>
**cordova-plugin-file写文件流程解析**<br>
从README.md了解，写文件函数为
```javascript
function writeFile(fileEntry, dataObj) {
    // Create a FileWriter object for our FileEntry (log.txt).
    fileEntry.createWriter(function (fileWriter) {

        fileWriter.onwriteend = function() {
            console.log("Successful file write...");
            readFile(fileEntry);
        };

        fileWriter.onerror = function (e) {
            console.log("Failed file write: " + e.toString());
        };

        // If data object is not passed in,
        // create a new Blob instead.
        if (!dataObj) {
            dataObj = new Blob(['some file data'], { type: 'text/plain' });
        }

        fileWriter.write(dataObj);
    });
}
```
<!-- more -->
```javascript
/**
 * Creates a new FileWriter associated with the file that this FileEntry represents.
 *
 * @param {Function} successCallback is called with the new FileWriter
 * @param {Function} errorCallback is called with a FileError
 */
FileEntry.prototype.createWriter = function (successCallback, errorCallback) {
    this.file(function (filePointer) {
        var writer = new FileWriter(filePointer);

        if (writer.localURL === null || writer.localURL === '') {
            if (errorCallback) {
                errorCallback(new FileError(FileError.INVALID_STATE_ERR));
            }
        } else {
            if (successCallback) {
                successCallback(writer);
            }
        }
    }, errorCallback);
};
```

FileWriter写文件函数为
```javascript
var FileWriter = function (file) {
...
    if (file) {
        this.localURL = file.localURL || file;
        this.length = file.size || 0;
    }
...
};

/**
 * Writes data to the file
 *
 * @param data text or blob to be written
 * @param isPendingBlobReadResult {Boolean} true if the data is the pending blob read operation result
 */
FileWriter.prototype.write = function (data, isPendingBlobReadResult) {

    var that = this;
    var supportsBinary = (typeof window.Blob !== 'undefined' && typeof window.ArrayBuffer !== 'undefined');
    /* eslint-disable no-undef */
    var isProxySupportBlobNatively = (cordova.platformId === 'windows8' || cordova.platformId === 'windows');
    var isBinary;

    // Check to see if the incoming data is a blob
    if (data instanceof File || (!isProxySupportBlobNatively && supportsBinary && data instanceof Blob)) {
        var fileReader = new FileReader();
        /* eslint-enable no-undef */
        fileReader.onload = function () {
            // Call this method again, with the arraybuffer as argument
            FileWriter.prototype.write.call(that, this.result, true /* isPendingBlobReadResult */);
        };
        fileReader.onerror = function () {
            // DONE state
            that.readyState = FileWriter.DONE;

            // Save error
            that.error = this.error;

            // If onerror callback
            if (typeof that.onerror === 'function') {
                that.onerror(new ProgressEvent('error', {'target': that}));
            }

            // If onwriteend callback
            if (typeof that.onwriteend === 'function') {
                that.onwriteend(new ProgressEvent('writeend', {'target': that}));
            }
        };

        // WRITING state
        this.readyState = FileWriter.WRITING;

        if (supportsBinary) {
            fileReader.readAsArrayBuffer(data);
        } else {
            fileReader.readAsText(data);
        }
        return;
    }

    // Mark data type for safer transport over the binary bridge
    isBinary = supportsBinary && (data instanceof ArrayBuffer);
    if (isBinary && cordova.platformId === 'windowsphone') { // eslint-disable-line no-undef
        // create a plain array, using the keys from the Uint8Array view so that we can serialize it
        data = Array.apply(null, new Uint8Array(data));
    }

    // Throw an exception if we are already writing a file
    if (this.readyState === FileWriter.WRITING && !isPendingBlobReadResult) {
        throw new FileError(FileError.INVALID_STATE_ERR);
    }

    // WRITING state
    this.readyState = FileWriter.WRITING;

    var me = this;

    // If onwritestart callback
    if (typeof me.onwritestart === 'function') {
        me.onwritestart(new ProgressEvent('writestart', {'target': me}));
    }

    // Write file
    exec(
        // Success callback
        function (r) {
            // If DONE (cancelled), then don't do anything
            if (me.readyState === FileWriter.DONE) {
                return;
            }

            // position always increases by bytes written because file would be extended
            me.position += r;
            // The length of the file is now where we are done writing.

            me.length = me.position;

            // DONE state
            me.readyState = FileWriter.DONE;

            // If onwrite callback
            if (typeof me.onwrite === 'function') {
                me.onwrite(new ProgressEvent('write', {'target': me}));
            }

            // If onwriteend callback
            if (typeof me.onwriteend === 'function') {
                me.onwriteend(new ProgressEvent('writeend', {'target': me}));
            }
        },
        // Error callback
        function (e) {
            // If DONE (cancelled), then don't do anything
            if (me.readyState === FileWriter.DONE) {
                return;
            }

            // DONE state
            me.readyState = FileWriter.DONE;

            // Save error
            me.error = new FileError(e);

            // If onerror callback
            if (typeof me.onerror === 'function') {
                me.onerror(new ProgressEvent('error', {'target': me}));
            }

            // If onwriteend callback
            if (typeof me.onwriteend === 'function') {
                me.onwriteend(new ProgressEvent('writeend', {'target': me}));
            }
        }, 'File', 'write', [this.localURL, data, this.position, isBinary]);
};
```
读取Blob对象，转化为二进制或Base64，使用File.write函数执行.

根据配置文件plugin.xml，Javascript的File类对应java的FileUtils类：
```xml
<config-file target="res/xml/config.xml" parent="/*">
    <feature name="File" >
        <param name="android-package" value="org.apache.cordova.file.FileUtils"/>
        <param name="onload" value="true" />
    </feature>
    <allow-navigation href="cdvfile:*" />
</config-file>
```

在FileUtils中查看write操作：
```java
public boolean execute(String action, final String rawArgs, final CallbackContext callbackContext) {
...
        else if (action.equals("write")) {
           threadhelper( new FileOp( ){
               public void run(JSONArray args) throws JSONException, FileNotFoundException, IOException, NoModificationAllowedException {
                   String fname=args.getString(0);
                   String nativeURL = resolveLocalFileSystemURI(fname).getString("nativeURL");
                   String data=args.getString(1);
                   int offset=args.getInt(2);
                   Boolean isBinary=args.getBoolean(3);

                   if(needPermission(nativeURL, WRITE)) {
                       getWritePermission(rawArgs, ACTION_WRITE, callbackContext);
                   }
                   else {
                       long fileSize = write(fname, data, offset, isBinary);
                       callbackContext.sendPluginResult(new PluginResult(PluginResult.Status.OK, fileSize));
                   }

               }
           }, rawArgs, callbackContext);
       }
...

/**
 * Write contents of file.
 *
 * @param data				The contents of the file.
 * @param offset			The position to begin writing the file.
 * @param isBinary          True if the file contents are base64-encoded binary data
 */
/**/
public long write(String srcURLstr, String data, int offset, boolean isBinary) throws FileNotFoundException, IOException, NoModificationAllowedException {
    try {
      LocalFilesystemURL inputURL = LocalFilesystemURL.parse(srcURLstr);
      Filesystem fs = this.filesystemForURL(inputURL);
      if (fs == null) {
        throw new MalformedURLException("No installed handlers for this URL");
      }

        long x = fs.writeToFileAtURL(inputURL, data, offset, isBinary); LOG.d("TEST",srcURLstr + ": "+x); return x;
    } catch (IllegalArgumentException e) {
        MalformedURLException mue = new MalformedURLException("Unrecognized filesystem URL");
        mue.initCause(e);
      throw mue;
    }

}

private Filesystem filesystemForURL(LocalFilesystemURL localURL) {
	if (localURL == null) return null;
	return filesystemForName(localURL.fsName);
}

private Filesystem filesystemForName(String name) {
	for (Filesystem fs:filesystems) {
		if (fs != null && fs.name != null && fs.name.equals(name)) {
			return fs;
		}
	}
	return null;
}
```

通过init函数看到，Filesystem有以下几种类型：
```java
@Override
public void initialize(CordovaInterface cordova, CordovaWebView webView) {
  ...
  this.registerFilesystem(new LocalFilesystem("temporary", webView.getContext(), webView.getResourceApi(), tmpRootFile));
	this.registerFilesystem(new LocalFilesystem("persistent", webView.getContext(), webView.getResourceApi(), persistentRootFile));
	this.registerFilesystem(new ContentFilesystem(webView.getContext(), webView.getResourceApi()));
  this.registerFilesystem(new AssetFilesystem(webView.getContext().getAssets(), webView.getResourceApi()));
  ...
}
```

看不出来是哪一种FileSystem，但是ContentFilesystem和AssetFilesystem的writeToFileAtURL函数都直接抛异常，直接看下LocalFilesystem的writeToFileAtURL函数：
```java
@Override
public long writeToFileAtURL(LocalFilesystemURL inputURL, String data,
		int offset, boolean isBinary) throws IOException, NoModificationAllowedException {

      boolean append = false;
      if (offset > 0) {
          this.truncateFileAtURL(inputURL, offset);
          append = true;
      }

      byte[] rawData;
      if (isBinary) {
          rawData = Base64.decode(data, Base64.DEFAULT);
      } else {
          rawData = data.getBytes(Charset.defaultCharset());
      }
      ByteArrayInputStream in = new ByteArrayInputStream(rawData);
      try
      {
      	byte buff[] = new byte[rawData.length];
          String absolutePath = filesystemPathForURL(inputURL);
          FileOutputStream out = new FileOutputStream(absolutePath, append);
          try {
          	in.read(buff, 0, buff.length);
          	out.write(buff, 0, rawData.length);
          	out.flush();
          } finally {
          	// Always close the output
          	out.close();
          }
          if (isPublicDirectory(absolutePath)) {
              broadcastNewFile(Uri.fromFile(new File(absolutePath)));
          }
      }
      catch (NullPointerException e)
      {
          // This is a bug in the Android implementation of the Java Stack
          NoModificationAllowedException realException = new NoModificationAllowedException(inputURL.toString());
          realException.initCause(e);
          throw realException;
      }

      return rawData.length;
}
```
至此一个Blob写流程已经完成，文件写入到某个文件。下面开始编码我们的Blob写入读出代码

##### 初始化
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

##### 写权限检查（ API 23+）
```java
// Storage Permissions
private static final int REQUEST_EXTERNAL_STORAGE = 1;
private static String[] PERMISSIONS_STORAGE = {
        Manifest.permission.READ_EXTERNAL_STORAGE,
        Manifest.permission.WRITE_EXTERNAL_STORAGE
};

public static void verifyStoragePermissions(Activity activity) {
    // Check if we have write permission
    int permission = ActivityCompat.checkSelfPermission(activity, Manifest.permission.WRITE_EXTERNAL_STORAGE);

    if (permission != PackageManager.PERMISSION_GRANTED) {
        // We don't have permission so prompt the user
        ActivityCompat.requestPermissions(
                activity,
                PERMISSIONS_STORAGE,
                REQUEST_EXTERNAL_STORAGE
        );
    }
}
```

##### WebView选择文件
```java
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

    // For Android 5.0+
    public boolean onShowFileChooser(WebView webView, ValueCallback<Uri[]> filePathCallback, FileChooserParams fileChooserParams) {
        mUploadMessageAboveL = filePathCallback;
        return handleFileChooser(filePathCallback);
    }

    private boolean handleFileChooser(final ValueCallback valueCallback) {
        mUploadMessage = valueCallback;
        Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
        intent.addCategory(Intent.CATEGORY_OPENABLE);
        intent.setType("*/*");
        ((Activity)context).startActivityForResult(
                Intent.createChooser(intent, "完成操作需要使用"),
                FILECHOOSER_RESULTCODE);
        return true;
    }
}

// Activity回调
public static final int FILECHOOSER_RESULTCODE = 10001;
private ValueCallback<Uri> mUploadMessage;
private ValueCallback<Uri[]> mUploadMessageAboveL;
public void handleFileUpload(int requestCode, int resultCode, Intent intent) {
    if (mUploadMessageAboveL != null) {
        Uri[] results = null;
        if (resultCode == Activity.RESULT_OK) {
            if (intent != null) {
                String dataString = intent.getDataString();
                ClipData clipData = intent.getClipData();
                if (clipData != null) {
                    results = new Uri[clipData.getItemCount()];
                    for (int i = 0; i < clipData.getItemCount(); i++) {
                        ClipData.Item item = clipData.getItemAt(i);
                        results[i] = item.getUri();
                    }
                }
                if (dataString != null)
                    results = new Uri[]{Uri.parse(dataString)};
            }
        }
        mUploadMessageAboveL.onReceiveValue(results);
        mUploadMessageAboveL = null;
    }
    else {
        Uri result = intent.getData();
        if(result == null) return;
        mUploadMessage.onReceiveValue( Uri.fromFile(new File(getRealFilePath(context, result))) );
        mUploadMessage = null;
    }

}
```

##### javascript接口
```java
public class JsInterface {
    private String filesystemPathForURL(String inputURL) {
        String file = "0/" + Md5Util.encrypt(inputURL, Md5Util.EncodeStrategy.ENCODE_HEX, Md5Util.CaseStrategy.LOWER_CASE) + "." + inputURL.replaceAll("^.*\\.","");
        try {
            File f = new File(Environment.getExternalStorageDirectory().getAbsoluteFile(), file);
            if(!f.exists()) f.createNewFile();
            return f.getAbsolutePath();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return "";
    }

    private long truncateFileAtURL(String inputURL, long size) throws IOException {
        File file = new File(filesystemPathForURL(inputURL));

        if (!file.exists()) {
            throw new FileNotFoundException("File at " + inputURL + " does not exist.");
        }

        RandomAccessFile raf = new RandomAccessFile(file.getAbsoluteFile(), "rw");
        try {
            if (raf.length() >= size) {
                FileChannel channel = raf.getChannel();
                channel.truncate(size);
                return size;
            }
            return raf.length();
        } finally {
            raf.close();
        }
    }

    @JavascriptInterface
    public long writeToFileAtURL(String inputURL, String data,
                                 int offset, boolean isBinary, String sucCallback, String failCallback) throws IOException {
        boolean append = false;
        if (offset > 0) {
            this.truncateFileAtURL(inputURL, offset);
            append = true;
        }

        byte[] rawData;
        if (isBinary) {
            rawData = Base64.decode(data, Base64.DEFAULT);
        } else {
            rawData = data.getBytes(Charset.defaultCharset());
        }
        ByteArrayInputStream in = new ByteArrayInputStream(rawData);
        try
        {
            byte buff[] = new byte[rawData.length];
            String absolutePath = filesystemPathForURL(inputURL);
            FileOutputStream out = new FileOutputStream(absolutePath, append);
            try {
                in.read(buff, 0, buff.length);
                out.write(buff, 0, rawData.length);
                out.flush();
            } finally {
                // Always close the output
                out.close();
            }
            final String jscall = "javascript:" + sucCallback + "(" + rawData.length + ")";
            mHandler.post(new Runnable() {
                @Override
                public void run() {
                    loadUrl(jscall);
                }
            });

            if (isPublicDirectory(absolutePath)) {
                broadcastNewFile(Uri.fromFile(new File(absolutePath)));
            }
        }
        catch (NullPointerException e)  {
            e.printStackTrace();

            final String jscall = "javascript:" + failCallback + "()";
            mHandler.post(new Runnable() {
                @Override
                public void run() {
                    loadUrl(jscall);
                }
            });
        }

        return rawData.length;
    }

    private boolean isPublicDirectory(String absolutePath) {
        // TODO: should expose a way to scan app's private files (maybe via a flag).
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
            // Lollipop has a bug where SD cards are null.
            for (File f : context.getExternalMediaDirs()) {
                if(f != null && absolutePath.startsWith(f.getAbsolutePath())) {
                    return true;
                }
            }
        }

        String extPath = Environment.getExternalStorageDirectory().getAbsolutePath();
        return absolutePath.startsWith(extPath);
    }

    /**
     * Send broadcast of new file so files appear over MTP
     */
    private void broadcastNewFile(Uri nativeUri) {
        Intent intent = new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE, nativeUri);
        context.sendBroadcast(intent);
    }
}
```
##### 业务实现
```HTML
<input id="file-input" type="file" name="name" style="display: none;" onChange="sendFileEvent(this.files)" accept="images/*"  />
<button onclick="javascript:document.getElementById('file-input').click()">导入到App</button><br>
```

```javascript
//  Functions for SendFile
var localURL;
var position = 0;
function sendFileEvent (files) {
  if (files && files[0]) {
    sendFile(files[0])
  }
}

function sendFile (file) {
  console.log(file.name)
  if (file) {
    localURL = file.localURL || file
    position = 0
    var reader = new window.FileReader()
    reader.onload = function (e) {
      write(e.target.result, true)
    }
    reader.readAsArrayBuffer(file)
  }
}

function write(data, isPendingBlobReadResult) {
   window.sync.writeToFileAtURL(localURL.name, base64ArrayBuffer(data), position, isPendingBlobReadResult, "success", "error")
}
```
```JavaScript
// Base64实现
// https://gist.githubusercontent.com/jonleighton/958841/raw/fb05a8632efb75d85d43deb593df04367ce48371/base64ArrayBuffer.js
function base64ArrayBuffer(arrayBuffer) {
  var base64    = ''
  var encodings = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/'

  var bytes         = new Uint8Array(arrayBuffer)
  var byteLength    = bytes.byteLength
  var byteRemainder = byteLength % 3
  var mainLength    = byteLength - byteRemainder

  var a, b, c, d
  var chunk

  // Main loop deals with bytes in chunks of 3
  for (var i = 0; i < mainLength; i = i + 3) {
    // Combine the three bytes into a single integer
    chunk = (bytes[i] << 16) | (bytes[i + 1] << 8) | bytes[i + 2]

    // Use bitmasks to extract 6-bit segments from the triplet
    a = (chunk & 16515072) >> 18 // 16515072 = (2^6 - 1) << 18
    b = (chunk & 258048)   >> 12 // 258048   = (2^6 - 1) << 12
    c = (chunk & 4032)     >>  6 // 4032     = (2^6 - 1) << 6
    d = chunk & 63               // 63       = 2^6 - 1

    // Convert the raw binary segments to the appropriate ASCII encoding
    base64 += encodings[a] + encodings[b] + encodings[c] + encodings[d]
  }

  // Deal with the remaining bytes and padding
  if (byteRemainder == 1) {
    chunk = bytes[mainLength]

    a = (chunk & 252) >> 2 // 252 = (2^6 - 1) << 2

    // Set the 4 least significant bits to zero
    b = (chunk & 3)   << 4 // 3   = 2^2 - 1

    base64 += encodings[a] + encodings[b] + '=='
  } else if (byteRemainder == 2) {
    chunk = (bytes[mainLength] << 8) | bytes[mainLength + 1]

    a = (chunk & 64512) >> 10 // 64512 = (2^6 - 1) << 10
    b = (chunk & 1008)  >>  4 // 1008  = (2^6 - 1) << 4

    // Set the 2 least significant bits to zero
    c = (chunk & 15)    <<  2 // 15    = 2^4 - 1

    base64 += encodings[a] + encodings[b] + encodings[c] + '='
  }

  return base64
}
```

##### 最终结果
```
C:\Users\Hamilton\Desktop>adb shell md5sum /sdcard/0/*.mp4
417ebe31a72dea3b6cefc8f02b8b3b6c  /sdcard/0/7fe1acf862f82442e4d545eef88cc88e.mp4
417ebe31a72dea3b6cefc8f02b8b3b6c  /sdcard/0/OLn6o-pxG6b38xu82z6xs3ck9OoAQmOp9C6eyw___0_1514976639.mp4
```
