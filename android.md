# Sécurité des applications Android
## APKTOOL
Les fichiers APK sont des archives, on peut les décompresser avec apktool
```bash
apktool d monapp.apk
```
Ensuite on peut voir les permissions que demande l'application dans le fichier AndroidManifest.xml

## ADB
Modification du PATH pour adb
```bash
export PATH=${PATH}:/home/phozon/Android/Sdk/tools:/home/phozon/Android/Sdk/platform-tools
```
Lister les devices avec adb
```bash
adb devices
```
Télécharger et installer un apk avec ADB

http://payatu.com/wp-content/uploads/2016/01/diva-beta.tar.gz
```bash    
adb install /home/phozon/Downloads/diva-beta.apk 
```
Rentrer dans le shell de la VM android
```bash
adb shell
```
Si on veut spécifier une target:
```bash	
adb -s 192.168.0.1:5555 shell
```	
Déposer un fichier dans la carte mémoire (une image)
```bash
adb push fsociety.jpg /sdcard/Pictures/fsociety.jpg
```
Récuperer le fichier APK d'une application déjà installée
Liste les applications déjà installées:
```bash
adb shell pm list packages
```
Afficher le chemin du fichier APK
```bash
adb shell pm path jakhar.aseem.diva
```
On fait pull pour récuperer le fichier APK et on lui indique le chemin distant
```bash
adb pull /data/app/jakhar.aseem.diva-1/base.apk ./diva_recup.apk
```
## Convertir un APK en JAR
Softs pour obtenir le code source d'un APK
Dex2jar
https://github.com/pxb1988/dex2jar
JD-Gui
http://jd.benow.ca/

Envoyer le APK vers la kali :
```bash
scp -P 2222 diva-beta.apk vagrant@127.0.0.1:/home/vagrant
```
Pour faire un jar a partir d'un APK:
```bash
d2j-dex2jar diva-beta.apk
```
On renvoit le .jar vers la machine host:
```bash
scp diva-beta-dex2jar.jar phozon@172.16.97.155:/home/phozon/Shared
```
## JD-GUI pour lire le code JAVA 
Commande pour lancer jd-gui.jar
```bash	
java --add-opens java.base/jdk.internal.loader=ALL-UNNAMED --add-opens jdk.zipfs/jdk.nio.zipfs=ALL-UNNAMED -jar /home/phozon/Documents/0\ -\ Cours/secu\ android/jd-gui-1.4.0.jar
```
Ensuite on ouvre le fichier .jar dans jd-gui

## Envoyer le certificat burp avec ADB
Push certificat avec ADB
```bash
adb push cacert.crt /mnt/sdcard/
```
## Capture du traffic avec TCPDUMP
capture tcpdump de tout le traffic de l'android
```bash
tcpdump -i eth1 -w /tmp/capture.pcap	
```
## Vulnérabilités
Shared prefs

Android peut utiliser des bases de données de type SQLite pour stocker des informations relatives à une application
Aucun chiffrement n'est prévu par défaut pour les données présentes en base

Afficher les logs d'une application spéficique 
```bash
adb logcat | grep -F "`adb shell ps | grep jakhar.aseem.diva | cut -c10-15`"
```
Le cache du keyboard
```bash
/data/data/com.android.providers.userdictionary/databases/user_dict.db
```
Récuperer l'utilisateur de l'appli
```bash
adb shell ps
```
Récuperer le cotenu du presse papier d'une appli
```bash
adb shell su u0_a60 service call clipboard 2 s16 jakhar.aseem.diva
```
2 pour get les infos
s16 pour récuperer les strings

Allow backup permission si l'application n'est pas sur false dans le AndroidManifest, alors allowBackup est à true par défaut

faire une backup via ABD
On spécifie le nom de l'archive et le nom du paquet a backuper
```bash
adb backup -f jakhar.aseem.diva.ab -apk jakhar.aseem.diva
```
Conversion du  backup .ab en .tar pour l'extraire.
```bash
dd if=jakhar.aseem.diva.ab bs=1 skip=24 | python -c "import zlib,sys;sys.stdout.write(zlib.decompress(sys.stdin.read()))" > backup.tar
```
Extraction de l'archive .tar, puis on a un dossier app/
```bash
tar xvf backup.tar
```
dans apps/jakhar.aseem.diva/sp/jakhar.aseem.diva_preferences.xml on retrouve mes préférences, mon login et password et le code pin que j'ai définit dans l'appli

Convertir le .ab en .tar avec le tool Android Backup Extractor
```bash
java -jar abe.jar unpack jakhar.aseem.diva.ab backup.tar
```
Activity exportables dans le fichier AndroidManifest.xml
```bash
android:exported="true"
```
On peut aussi les reconnaitres car elles disposent des balises intent filter 

Commande pour lancer directement une activity avec ADB:
	
```bash
adb shell am start -n jakhar.aseem.diva/.APICreds2Activity

```
Rentrer en mode débug dans une appli
```bash
adb shell ps
adb forward tcp:3445 jdwp:2636
jdb -attach localhost:3445
```
##Décompiler et recompiler un APK puis le signer 
Décompiler l'apk
```bash
apktool d my-app.apk
```
Recompiler le nouveau apk
```bash
apktool b new-app.apk my-app/
```
Générer un nouveau certificat
```bash
keytool -genkey -v -keystore resign.keystore -alias NEW_APP -keyalg RSA 2048 -validity 10000
```
Ajouter le certificat sur le nouveau apk
```bash
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore resign.keystore new-app.apk NEW_APP
```
Afficher le cert avec keytool
```bash
keytool -printcert -file CERT.RSA
```
