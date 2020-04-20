# SOS - LAB02 - Analyse d’application mobile

Authors : Robin Cuénoud, Florien Müllhauser

## Question 1. Analyse statique

### [a] Quels outils avez-vous utilisés pour « depacker » votre APK (détails de la manipulation) (2 pts)

Avec `apktool` en utilisant la commande `apktool d SOS-lab.apk`

On peut utiliser `jadx-gui` pour depack et decompiler le code directement aussi.

### [b] Lister les fichiers contenus dans l’APK. (1 pt)

![image-20200420151214561](image_sos/1b)

Dans `AndroidManifest.xml` il y’a tout ce qui est information lié à l’application, activité, permissions etc...

Dans `smali` il y’a tout le code qui est desassemblé.



### [c] Enumérer le point d’entrée (entrypoints) de l’application (détails de la manipulation). (1 pt)

Dans le `AndroidManifest.xml`

```xml
<?xml version="1.0" encoding="utf-8" standalone="no"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:compileSdkVersion="28" android:compileSdkVersionCodename="9" package="ch.heig.lab" platformBuildVersionCode="28" platformBuildVersionName="9">
    <application android:allowBackup="false" android:appComponentFactory="androidx.core.app.CoreComponentFactory" android:icon="@mipmap/ic_launcher" android:label="@string/app_name" android:roundIcon="@mipmap/ic_launcher_round" android:supportsRtl="true" android:theme="@style/AppTheme">
        <activity android:label="@string/app_name" android:name="ch.heig.lab.MainActivity" android:theme="@style/AppTheme.NoActionBar">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
    </application>
</manifest>
```

Il n’ya qu’une Activity `ch.heig.lab.MainActivity`

### [d]  Quel	nom	de	« package »	utilise	l’application	« SOS-Lab.apk ». Lister	deux	manières de	récupérer	cette	information. (2 pt)

> Une fois que l'on a désassemblé l'APK avec `apktool d SOS-Lab.apk`, le fichier `AndroidManifest.xml` apparait. On ouvre ce fichier et dedans il renseign le package entre autre `package="ch.heig.lab"` .


> Une autre façon que de regarder dans le XML, est d'aller dans le dossier `smali` ensuite on a l'arborescence des fichiers utiliser. Le com/google est un import, mais par contre on peut deviner en suivant l'arborescence que `ch/heig/lab` est le nom de package, vu les fichiers qu'il contient
