# android-permission

---
location permission in android

AndroidManifest.xml 기본설정

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="org.techtown.testgeolocation">
     <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
</manifest>    
```

MainActivity.java

```
public class MainActivity extends AppCompatActivity {

    private static final int MY_PERMISSIONS_STORAGE = 1111;  //권한값정의

    private static final String URL_DAUM_MAP = "http://www.google.com";
    private static final String URL_NAVER = "http://m.naver.com/";
    private static final String URL_GOOGLE_MAP = "https://www.google.com/maps";
    private static final String TAG = MainActivity.class.getSimpleName();
    private WebView webView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        checkPermission();
        webView = (WebView) findViewById(R.id.webview);
        initWebView();
    }

    private void initWebView(){
        webView.getSettings().setJavaScriptEnabled(true); // 자바스크립트 사용을 허용한다.
        webView.setWebViewClient(new WebViewClient());  // 새로운 창을 띄우지 않고 내부에서 웹뷰를 실행시킨다.
        webView.setWebChromeClient(new WebChromeClient(){
           @Override
            public void onGeolocationPermissionsShowPrompt(String origin, GeolocationPermissions.Callback callback) {
                super.onGeolocationPermissionsShowPrompt(origin, callback);
                callback.invoke(origin, true, false);
            }
        });
        webView.loadUrl("https://s3.amazonaws.com/lexwebuimobilehub-hosting-mobilehub-1032348699/index1.html");
    }

    private void checkPermission() {
        if (ContextCompat.checkSelfPermission(this,
                android.Manifest.permission.ACCESS_FINE_LOCATION)
                != PackageManager.PERMISSION_GRANTED) {
            //권한 확인이 안되었을때
            if (ActivityCompat.shouldShowRequestPermissionRationale(this,
                    android.Manifest.permission.READ_CONTACTS)) {
                new AlertDialog.Builder(this)
                        .setTitle("알림")
                        .setMessage("저장소 권한이 거부되었습니다.사용을 원하시면 설정에서 해당권한을 직접 허용하셔야 합니다.")
                        .setNeutralButton("설정", new DialogInterface.OnClickListener() {
                            @Override
                            public void onClick(DialogInterface dialogInterface, int i) {
                                Intent intent = new Intent(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
                                intent.setData(Uri.parse("package:" + getPackageName()));
                                startActivity(intent);
                            }
                        })
                        .setPositiveButton("확인", new DialogInterface.OnClickListener() {
                            @Override
                            public void onClick(DialogInterface dialogInterface, int i) {
                                finish();
                            }
                        })
                        .setCancelable(false)
                        .create()
                        .show();
            } else {
                // 권한 확인이 되었을 때
                Toast.makeText(this, "위치 권한을 활성화 하였습니다.",Toast.LENGTH_SHORT);
                ActivityCompat.requestPermissions(this,
                        new String[]{android.Manifest.permission.ACCESS_FINE_LOCATION, Manifest.permission.ACCESS_COARSE_LOCATION},
                        MY_PERMISSIONS_STORAGE);
                initWebView();
                //여기
            }
        }
    }


    public void onRequestPermissionsResult(int requestCode,
                                           @NonNull String[] permissions[], @NonNull int[] grantResults) {
        //사용자가 응답했을 때 이 매소드 호출로 재정의하기
        switch (requestCode) {
            case MY_PERMISSIONS_STORAGE: {
                for(int i = 0; i < grantResults.length; i++) {
                    //grantResults[] : 허용된 권한은 0, 거부한 권한은 -1
                    if (grantResults[i] < 0 ){
                        //권한 거부
                        Toast.makeText(this, "해당권한을 활성화 하셔야 합니다.",Toast.LENGTH_SHORT);
                        return;
                    } else {
                        initWebView();
                        //여기
                        Toast.makeText(this, "위치 권한을 활성화 하였습니다.",Toast.LENGTH_SHORT);
                    }
                }
                return;
            }
        }
    }
}
```

webview로 안드로이드에서 위치권한 
