# SOS - LAB02 - Analyse d’application mobile

Authors : Robin Cuénoud, Florian Mülhauser

## Question 1. Analyse statique

#### [a] Quels outils avez-vous utilisés pour « depacker » votre APK (détails de la manipulation) (2 pts)

Avec `apktool` en utilisant la commande `apktool d SOS-lab.apk`, cela va permettre de dépacker en déssassemblant notre APK. (Une alternative, qu'on trouve moins intéressante est de copier ce fichier en le renommant .zip, puis ensuite on le dépack en fasiant un unzip de ce dossier. Cela va le dépacker en partie, mais certains fichiers seront mal interprétées)

On peut utiliser `jadx-gui` pour depack et decompiler le code directement aussi.

#### [b] Lister les fichiers contenus dans l’APK. (1 pt)

![image-20200420151214561](image_sos/1b)

Dans `AndroidManifest.xml` il y’a tout ce qui est information lié à l’application, activité, permissions etc...

Dans `smali` il y’a tout le code, provennant de classes.dex, qui est maintenant desassemblé.



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

Une fois que l'on a désassemblé l'APK avec `apktool d SOS-Lab.apk`, le fichier `AndroidManifest.xml` apparait. On ouvre ce fichier et dedans il renseign le package entre autre `package="ch.heig.lab"` .


Une autre façon que de regarder dans le XML, est d'aller dans le dossier `smali` ensuite on a l'arborescence des fichiers utiliser. Le com/google est un import, mais par contre on peut deviner en suivant l'arborescence que `ch/heig/lab` est le nom de package, vu les fichiers qu'il contient

#### [e] Lister les permissions requises par l’application pour se lancer sur un appareil. (1 pt)

Dans le `AndroidManifest.xml` il n’y a pas de balise `<uses-permission …. />` donc aucune.

#### [f] Qu’utilise-t-on pour désassembler le fichier « classes.dex ». Qu’obtient-on après cette manipulation (détails de la manipulation) (3 pt)

apktool transforme le code assembleur de classes.dex en smali (il désassemble le fichier). On obtient donc à partir de l'APK ou de classes.dex un dossier smali, qui contient toutes les classes en .smali, cela reste en bas niveau (mais plus haut qu'assembleur). on fait donc `apktool d SOS-Lab.apk`, et ça va déssassembler le code de classes.dex qui était dedans.

#### [g] Qu’utilise-t-on pour décompiler le fichier « classes.dex ». Qu’obtient-on après cette manipulation (détails de la manipulation) (3 pt)

jadx (ou jadx-gui) quant à lui, permet de décompiler le fichier classes.dex et fait apparaitre directement les fichier java. On obtient donc le code haut niveau avec des features pour rechercher, aller aux jumps etc. On fait donc la commande `jadx -d out classes.dex`, out le nom de l'output directory

## Question 2. Reverse Engineering (15 pts)

#### [a] Expliquer en détail l’utilité de la méthode « a » de la classe Java « detection ». (3 pts)

```java
public static boolean a() {
        for (String file : System.getenv("PATH").split(":")) {
            if (new File(file, "su").exists()) {
                return true;
            }
        }
        return false;
    }
```

Elle récupère les variables d’environnement (l.2) et pour chaque variable (car `.split(“:”)` sépare chaque variable du PATH), elle essaie de créer un fichier portant le nom de la variable d’environnement. Si une de ces créations de fichiers réussit elle retourne vrai sinon faux.

Cette fonction sert à verifier si le device est root. En effet, si elle peut écrire des fichier à ces endroits, c’est une bonne indication qu’elle est root.  

#### [b] Expliquer en détail l’utilité de la méthode « b » de la classe « detection ». (3 pts)

```java
public static boolean b() {
        String str = Build.TAGS;
        return str != null && str.contains("test-keys");
    }
```

FROM : https://stackoverflow.com/questions/18808705/android-root-detection-using-build-tags

Vérifie si les tags de Build contient test-keys (si il arrive à les récuperer). Cela permet de voir si le kernel est signé par un developpeur officiel. Cela permet de verifier que le device n’est pas root.

#### [c] Expliquer en détail l’utilité de la méthode « c » de la classe « detection ». (3 pts)

```java
public static boolean c() {
        for (String file : new String[]{"/system/app/Superuser.apk", "/system/xbin/daemonsu", "/system/etc/init.d/99SuperSUDaemon", "/system/bin/.ext/.su", "/system/etc/.has_su_daemon", "/system/etc/.installed_su_daemon", "/dev/com.koushikdutta.superuser.daemon/"}) {
            if (new File(file).exists()) {
                return true;
            }
        }
        return false;
    }
```

Comme la méthode  `a()`, elle essaie d’écrire des fichiers à des endroits interdit sans être root, afin pour vérifier si le device est root ou non. C’est encore une façon de vérifier cela.

#### [d] Expliquer en détail l’utilité de la méthode « d » de la classe « detection ». (3 pts)

```java
public static boolean d(Context context) {
     return (context.getApplicationContext().getApplicationInfo().flags & 2) != 0;
}
```

L’application verifie que le flag 2 est activé, le flag 2 est celui la : https://developer.android.com/reference/android/content/pm/ApplicationInfo#FLAG_DEBUGGABLE le flag debug, donc elle vérifie que le débug est activé ou pas.

#### [e] Expliquer d’une manière précise ce que vous allez faire pour contourner ce mécanisme de protection de l’application. (code à modifier dans l’application) (3 pts)

On va juste modifier cette classe pour que toutes les méthodes renvoient faux, sinon `onCreate` de `MainActivity`  va appeler `droidDetect` qui va faire quitter l’application. On peut sinon aussi supprimer les lignes de test. (Lignes 2-7).

```java
public void onCreate(Bundle bundle) {
        if (detection.a() || detection.b() || detection.c()) {
            droiDetect("Root detected!");
        }
        if (detection.d(getApplicationContext())) {
            droiDetect("App is debuggable!");
        }
```

Ou sinon on peut enlever l’appel à `System.exit(0)` dans droidDetect.

Pour modifier dans le smallI j’ai enlevé les appels à droidDetect

![image-20200420164920230](image_sos/31)

## Question 3. Analyse Dynamique (23 pts)

#### [a] Installer l’application « SOS-Lab.apk » sur l’émulateur (détails de la manipulation) (2 pt)

Une fois le code modifié dans la classe de detection (on modifie le fichier smali), on va utiliser `apktool b <directory_modifie> -o SOS-test_lab.apk` afin de build notre APK modifié. Une fois modifié on doit signer ce nouvel APK avec le debug keystore du android SDK, pour cela on crée une clé et on signe.

Pour générer, on utilise keytool `keytool -genkey -v -keystore debug.jks -keyalg RSA -keysize 2048 -validity 10000 -alias android-debugkey` .

Pour signer on utilise apksigner `apksigner sign --ks debug.jks --out SOS-test-patch.apk SOS-test_lab.apk`

Voilà on a notre APK signé, on peut maintenant l'installer en utilisant la commande `adb install SOS-test_lab.apk`.'

#### [b] Challenge 1 : Changer la valeur de la variable 'value' de la classe chall01 à 1 (détailler la manipulation pour obtenir le résultat final). (1 pts)

On lance `frida-ps -U` on voit ch.heig.lab (le nom du package) , donc on peut commencer à debug remotely ce process avec frida grâce à `frida -U ch.heig.lab`

Comme snippet javascript j’ai écrit :

```javascript
Java.perform(function () {
  console.log("Starting hooks chall0...");
  var activity  = Java.use("ch.heig.lab.chall01");
  activity.getValue.overload().implementation = function() {
  return 1;
  }
  console.log("Hooks installed.");
});

```

Le code overload juste l’implémentation de la méthode getValue de chall0.



![image-20200420181345665](image_sos/chall0)

#### [c] Challenge 2 : Exécuter la méthode chall02() de la classe MainActivity (détailler la manipulation pour obtenir le résultat final). (3 pts)

Ici aussi facile,

il suffit de rejouter ce code dans celui d’avant :

```javascript
  var main;
        Java.choose('ch.heig.lab.MainActivity', {onMatch: function(instance)
        {
            main = instance;
        }
        ,onComplete: function() {}}
        );
        main.chall02();
```

![image-20200420182540954](image_sos/chall02)

#### [d] Challenge 3 : Faire en sorte que la méthode chall03() renvoie toujours « vrai » (détailler la manipulation pour obtenir le résultat final). (2 pts)

Ici comme pour le chall1 on overload la méthode de la class MainActivity.

Le code correspondant (avec la même variable main que le challenge d’avant).

```javascript
 main.chall03.overload().implementation = function () {
 return true;
 }
```

![image-20200420183544833](image_sos/3)

#### [e] Challenge 4 : Envoyer "HEIG" à la méthode chall03() (détailler la manipulation pour obtenir le résultat final). (2 pts)

il y’a une faute dans la question (c’est chall04 pas chall03).

Comme le chall2 on doit récuperer une instance donc on peut executer au même endroit grâce à cette ligne :

```java
main.chall04("HEIG");
```

![image-20200420183929669](image_sos/chall4)

#### [f] Challenge 5 : Envoyer « toujours » "SOS20" à la méthode chall05() de la classe MainActivity (détailler la manipulation pour obtenir le résultat final). (3 pts)

Ici il faut overload la méthode, la difficutlé est qu’il ne faut pas oublier le type du parametre (java.lang.String) et on peut rappeler la méthode pas overload grâce à this.chall05(str); ainsi on n’a pas besoin de récrire la méthode mais on peut juste modifier ses paramètres.

![image-20200420190057154](image_sos/chall05)

#### [g] Challenge 6 : Exécutez la méthode chall06() avec la bonne valeur pendant 10 secondes (détailler la manipulation pour obtenir le résultat final). (5 pts)

Ici pour solve ce challenge on attends dix secondes puis on envoie à confirmchall6 la valeur chall06 de la classe chall06, setTimeout sert à faire cela.

```javascript
setTimeout(function () {
		Java.perform(function () {
			var chall06 = Java.use('ch.heig.lab.chall06');
			chall06.addChall06.overload('int').implementation = function (v) {
				Java.choose('ch.heig.lab.MainActivity', {
		            onMatch: function(instance) {
						instance.chall06(chall06.chall06.value);
		            },
		            onComplete: function() {}
		        });
			}
		})
	}, 10000);
```

![image-20200420193148128](image_sos/chall06)

#### [h] Challenge 7 : Brute force la méthode checkPin(), puis confirme ta valeur avec l’appel à la méthode chall07() (détailler la manipulation pour obtenir le résultat final). (5 pts)



![image-20200420210711189](image_sos/7)

Nous voulons donc bruteforce la méthode checkPin. Nous savons que le pin contient 4 digits, donc nous devrons essayer toutes les combinaisons entre 9999 et 0000. Notre idée est de commencer à 0 et incrémenter jusqu à 9999. Cependant, pour tout les nombres < 1000, nous devrons ajouter des 0 à gauche afin de remplir les 4 digit. (par exemple, nous devons tester 0007 et non juste 7). C'est ce que nous faisons en calculant le nombre de 0 manquant et en faisant les .join(0) nécessaires avant d'ajouter le bloc de la tentative de password en elle meme.
Chacun des password ainsi créés va etre testé avec la méthode checkPin(password). En cas de succès on appelle la méthode main.chall07 avec comme argument ce password.

#### [i] Bonus Remplace le texte du bouton "Check" par "Pwned" (détails de la manipulation à réaliser). (5 pts)

On voit à la ligne 60 de MainActivity : 

```java
((Button) findViewById(R.id.check))
```

Cela va nous permettre de recuperer l’id du bouton check et ainsi modifier son text.

