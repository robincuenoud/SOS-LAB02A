# SOS - LAB02 - Analyse d’application mobile

Authors : Robin Cuénoud, Florien Müllhauser

## Question 1. Analyse statique

#### [a] Quels outils avez-vous utilisés pour « depacker » votre APK (détails de la manipulation) (2 pts)

Avec `apktool` en utilisant la commande `apktool d SOS-lab.apk`

On peut utiliser `jadx-gui` pour depack et decompiler le code directement aussi.

#### [b] Lister les fichiers contenus dans l’APK. (1 pt)

![image-20200420151214561](image_sos/1b)

Dans `AndroidManifest.xml` il y’a tout ce qui est information lié à l’application, activité, permissions etc...

Dans `smali` il y’a tout le code qui est desassemblé.



#### [c] Enumérer le point d’entrée (entrypoints) de l’application (détails de la manipulation). (1 pt)

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

#### [d]  Quel	nom	de	« package »	utilise	l’application	« SOS-Lab.apk ». Lister	deux	manières de	récupérer	cette	information. (2 pt)

> Une fois que l'on a désassemblé l'APK avec `apktool d SOS-Lab.apk`, le fichier `AndroidManifest.xml` apparait. On ouvre ce fichier et dedans il renseign le package entre autre `package="ch.heig.lab"` .


> Une autre façon que de regarder dans le XML, est d'aller dans le dossier `smali` ensuite on a l'arborescence des fichiers utiliser. Le com/google est un import, mais par contre on peut deviner en suivant l'arborescence que `ch/heig/lab` est le nom de package, vu les fichiers qu'il contient

#### [e] Lister les permissions requises par l’application pour se lancer sur un appareil. (1 pt)

Dans le `AndroidManifest.xml` il n’y a pas de balise `<uses-permission …. />` donc aucune. 

#### [f] Qu’utilise-t-on pour désassembler le fichier « classes.dex ». Qu’obtient-on après cette manipulation (détails de la manipulation) (3 pt)

apktool transforme le code assembleur en smali (il désassemble le fichier). On obtient du smali. 

#### [g] Qu’utilise-t-on pour décompiler le fichier « classes.dex ». Qu’obtient-on après cette manipulation (détails de la manipulation) (3 pt)

jadx-gui décompile le fichier .dex et fait des fichier java. 

## Question 2. Reverse Engineering (15 pts)

#### [a] Expliquer en détail l’utilité de la méthode « a » de la classe Java « detection ». (3 pts)

#### [b] Expliquer en détail l’utilité de la méthode « b » de la classe « detection ». (3 pts)

#### [c] Expliquer en détail l’utilité de la méthode « c » de la classe « detection ». (3 pts)

#### [d] Expliquer en détail l’utilité de la méthode « d » de la classe « detection ». (3 pts)

#### [e] Expliquer d’une manière précise ce que vous allez faire pour contourner ce mécanisme de protection de l’application. (code à modifier dans l’application) (3 pts)

## Question 3. Analyse Dynamique (23 pts)

#### [a] Installer l’application « SOS-Lab.apk » sur l’émulateur (détails de la manipulation) (2 pt)

#### [b] Challenge 1 : Changer la valeur de la variable 'value' de la classe chall01 à 1 (détailler la manipulation pour obtenir le résultat final). 1 pts)

#### [c] Challenge 2 : Exécuter la méthode chall02() de la classe MainActivity (détailler la manipulation pour obtenir le résultat final). (3 pts)

#### [d] Challenge 3 : Faire en sorte que la méthode chall03() renvoie toujours « vrai » (détailler la manipulation pour obtenir le résultat final). (2 pts)

#### [e] Challenge 4 : Envoyer "HEIG" à la méthode chall03() (détailler la manipulation pour obtenir le résultat final). (2 pts)

#### [f] Challenge 5 : Envoyer « toujours » "SOS20" à la méthode chall05() de la classe MainActivity (détailler la manipulation pour obtenir le résultat final). (3 pts)

#### [g] Challenge 6 : Exécutez la méthode chall06() avec la bonne valeur pendant 10 secondes (détailler la manipulation pour obtenir le résultat final). (5 pts)

#### [h] Challenge 7 : Brute force la méthode checkPin(), puis confirme ta valeur avec l’appel à la méthode chall07() (détailler la manipulation pour obtenir le résultat final). (5 pts)

#### [i] Bonus Remplace le texte du bouton "Check" par "Pwned" (détails de la manipulation à réaliser). (5 pts)