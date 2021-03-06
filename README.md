# Foxit PDF SDK Flutter Wrapper

A new flutter for using Foxit PDF SDK to open a pdf document.
Now, only support Andorid platform.

## System Requirements

-  Foxit PDF SDK >= 6.2.1

-  Flutter >= 1.0.0

## Installation

### Android

1. Follow the Flutter getting started guides to [install](https://flutter.io/docs/get-started/install), [set up an editor](https://flutter.io/docs/get-started/editor), and [create a Flutter Project.](https://flutter.io/docs/get-started/test-drive?tab=terminal#create-app) , assume you create `testflutter` as your project.

2. Download [Foxit PDF SDK for Android](https://developers.foxitsoftware.com/pdf-sdk/android/) package, extract it, and copy the following files (libraries and licenses) in the "libs" folder of the extracted package to `testflutter\android\libs` directory:

		FoxitRDK.aar
		FoxitRDKUIExtensions.aar
		RMSSDK-4.2-release.aar
		rms-sdk-ui.aar
		rdk_key.txt
		rdk_sn.txt

3. Add dependency to your Flutter project in `testflutter/pubspec.yaml`. 

		dependencies:
	  		flutter:
	    		sdk: flutter
	
	  	+	flutter_foxitpdf:
	    +  		git:
	    +    		url: git://github.com/foxitsoftware/flutter-foxitpdf.git
	    
4. Adjust `testflutter/android/build.gradle` file.

		allprojects {
		    repositories {
		        google()
		        jcenter()
		+        flatDir {
		+            dirs project(':flutter_foxitpdf').file("$rootDir/libs")
		+       }
		    }
		}

5. Adjust `testflutter/android/app/src/main/AndroidManifest.xml` file

	    <application
	        android:name="io.flutter.app.FlutterApplication"
	        android:label="testflutter"
	        android:icon="@mipmap/ic_launcher"
	    +   tools:replace="android:label">
	    
6. Replace `lib/main.dart`, see [Usage](#usage)

7. Run, You can run this prject in `Android Studio` or by using `flutter run`

8. You may also clone the plugin and run `example` in the project. You need to copy the `libs` to `example/libs` directory.

## <span id="usage">Usage</span>

Replace `YOUR_RDK_SN` and `YOUR_RDK_KEY` with your own license (`rdk_key.txt, rdk_sn.txt`)

```dart

	import 'dart:typed_data';
	
	import 'package:flutter/material.dart';
	import 'dart:async';
	
	import 'package:flutter/services.dart';
	import 'package:flutter_foxitpdf/flutter_foxitpdf.dart';
	
	void main() => runApp(MyApp());
	
	class MyApp extends StatefulWidget {
	  @override
	  _MyAppState createState() => _MyAppState();
	}
	
	class _MyAppState extends State<MyApp> {
	  String _platformVersion = 'Unknown';
	  int _error = -1;
	
	  String _sn = 'YOUR_RDK_SN';
	  String _key = 'YOUR_RDK_KEY';
	  String _path = '/mnt/sdcard/FoxitSDK/complete_pdf_viewer_guide_android.pdf';
	
	  @override
	  void initState() {
	    super.initState();
	    initPlatformState();
	
	    init(_sn, _key);
	
	    openDocument(_path, null);
	  }
	
	  // Platform messages are asynchronous, so we initialize in an async method.
	  Future<void> initPlatformState() async {
	    String platformVersion;
	    // Platform messages may fail, so we use a try/catch PlatformException.
	    try {
	      platformVersion = await FlutterFoxitpdf.platformVersion;
	    } on PlatformException {
	      platformVersion = 'Failed to get platform version.';
	    }
	
	    // If the widget was removed from the tree while the asynchronous platform
	    // message was in flight, we want to discard the reply rather than calling
	    // setState to update our non-existent appearance.
	    if (!mounted) return;
	
	    setState(() {
	      _platformVersion = platformVersion;
	    });
	  }
	
	  @override
	  Widget build(BuildContext context) {
	    return MaterialApp(
	      home: Scaffold(
	        appBar: AppBar(
	          title: const Text('Plugin example app'),
	        ),
	        body: Center(
	          child: Text('Running on: $_platformVersion\n'),
	        ),
	      ),
	    );
	  }
	
	  Future<void> init(String sn, String key) async {
	    int error;
	    try {
	      error = await FlutterFoxitpdf.initialize(sn, key);
	    } on PlatformException {
	      error = -1;
	    }
	    setState(() {
	      _error = error;
	    });
	  }
	
	  Future<void> openDocument(String path, Uint8List password) async {
	    await FlutterFoxitpdf.openDocument(path, password);
	  }
	}
```

## Preview

![](https://i.imgur.com/HhIIRiq.jpg)


## API Reference
**Initialize Foxit PDF SDK**

	FlutterFoxitpdf.initialize(String, String);

**Open a pdf document**

	FlutterFoxitpdf.openDocument(String, Uint8List)