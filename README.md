# LSC_Smart-Connect_Indoor_Camera_Hack
Hack to enable the ONVIF (and RTSP) on the LSC Smart connect indoor camera from Action

## Tuto de base 
Sources du Hack:
https://github.com/guino/BazzDoorbell/issues/2

## Pré-requis
- une caméra :)
- une carte micro-SD entre 4Go et 128Go. elle devra rester dans la caméra, formatée en FAT32.
- un réseau WiFi (WPA1 ou WPA2) disponible. Paramètres à connaitre : SSID et mot de passe. Attention: le mot de passe et le SSID ne doivent pas contenir de ',' et de ';'.
- [ONVIF Device Manager](https://sourceforge.net/projects/onvifdm/) qui permet de trouver les caméras ONVIF sur un réseau local et de visualiser les images.
- un téléphone mobile ou une tablette avec l'app Tuya.
- un client telnet sur le PC.

## Préparation de la procédure
- sur une carte micro-SD de plus de 4Go formatée en FAT32, placer le fichier [ppsFactoryTool.txt](https://github.com/n3odym3/LSC_Smart-Connect_Indoor_Camera_Hack/blob/main/ToRoot).
- Éditer le fichier ppsFactoryTool.txt pour remplacer SSID et PASSWORD par ceux de votre réseau WiFi.
- Insérer la carte micro-SD dans la caméra.
- Lancer la caméra.
- Attendre que la lumière s'allume en rouge fixe (pour savoir que a caméra est connectée au WiFi).
- sur le PC, lancer [ONVIF Device Manager](https://sourceforge.net/projects/onvifdm/).
- Trouver l'adresse IP (CAMERA_IP) de la caméra avec ONVIF Device Manager.
- Réaliser les requêtes HTTP Suivantes, et copier les réponses obtenues dans un fichier texte sur le PC (pour sauvegarde):
  - http://admin:admin@(CAMERA_IP):8090/proc/cmdline => Sauvegarder la réponse (cela permet de rétablir l'état d'origine de la caméra au cas où).
  - http://admin:admin@(CAMERA_IP):8090/devices/deviceinfo
  - http://admin:admin@(CAMERA_IP):8090/proc/self/root/etc/init.d/S90PPStrong 
- Éteindre la caméra et récupérer la carte micro-SD.

## 'Rooter' (= lancer un programme non inclus d'origine) la caméra

La procédure dépends de la réponse à la requête http://admin:admin@(CAMERA_IP):8090/devices/deviceinfo

=> Si la réponse contient "softwareversion":"4.x.x"
- vérifier que dans la réponse à la requête http://admin:admin@(CAMERA_IP):8090/proc/self/root/etc/init.d/S90PPStrong, la ligne contenant "MTDNUM=5" ne commence pas par un '#'
- Ajouter les fichier suivant de [ToRoot](https://github.com/n3odym3/LSC_Smart-Connect_Indoor_Camera_Hack/blob/main/ToRoot) sur la carte micro-SD
  - env
  - initrun.sh
  - ppsMmcTool.txt
- Placer la carte micro-SD dans la caméra.
- Appuyer sur le bouton reset.
- Brancher la caméra, en gardant le bouton reset enfoncé.
- Maintenir le bouton enfoncé pendant ~5 secondes, jusqu'à ce que la lumière de la caméra clignote entre rouge et bleu.
- La caméra devrait ensuite booter.
- Attendre que la caméra ait fini de booter (~45 secondes).
- Tester si le hack a fonctionné avec la requête HTTP suivante 
  - http://admin:admin@CAMERA.IP:8090/proc/self/root/mnt/mmc01/hack => si la réponse est "done", tout s'est bien passé.

=> Si la réponse contient "softwareversion":"5.x.x"
- Ajouter les fichier suivant de [ToRoot](https://github.com/n3odym3/LSC_Smart-Connect_Indoor_Camera_Hack/blob/main/ToRoot) sur la carte micro-SD
  - env
  - initrun.sh
  - ppsMmcTool.txt
- Placer la carte micro-SD dans la caméra.
- Appuyer sur le bouton reset.
- Brancher la caméra, en gardant le bouton reset enfoncé.
- Maintenir le bouton enfoncé pendant ~5 secondes, jusqu'à ce que la lumière de la caméra clignote entre rouge et bleu.
- La caméra devrait ensuite booter.
- Attendre que la caméra ait fini de booter (~45 secondes).
- Tester si le hack a fonctionné avec la requête HTTP suivante 
  - http://admin:admin@CAMERA.IP:8090/proc/self/root/mnt/mmc01/hack => si la réponse est "done", tout s'est bien passé.


ATTENTION! à partir de maintenant, la caméra ne peut pas fonctionner sans carte micro-SD insérée, et celle-ci doit absolument contenir un fichier nommé "initrun.sh" (qui peut être vide, mais qui doit être présent).
 
## Hacker la caméra

La procédure dépends de la réponse à la requête http://admin:admin@(CAMERA_IP):8090/devices/deviceinfo

=> Si la réponse contient "softwareversion":"4.x.x"
- Ajouter les fichier de V4_ToHack sur la carte micro-SD
  - cgi-bin
  - busybox
  - custom.sh
  - dropbearmulti
  - httpd.conf
  - index.html
  - jpeg-arm
  - passwd
  - set
  - upload.html
- Copier le fichier ppsapp présent dans /home/app
- S'assurer que le md5 correspond à celui présent [ici](https://github.com/guino/ppsapp-rtsp/issues/1) (chercher ppstrong-a3-tuya2_lsc-4.0.6.20210311) 
- Aller sur https://www.marcrobledo.com/RomPatcher.js/
- Sélectionner le fichier ppsapp
- Sélectionner le patch téléchargé [ici](https://github.com/guino/ppsapp-rtsp/files/6880255/ppsapp-onvif.zip)
- Renommer le fichier ppsapp.txt téléchargé après le patch en ppsapp
- Placer le fichier ppsapp patché à la racine de la carte SD
- Placer la carte SD dans la caméra et brancher la caméra
- La caméra devrait booter deux fois !
- Bravo la caméra est patchée
- Essayer d'ouvrir le flux RTSP dans ONVIF Device Manager

=> Si la réponse contient "softwareversion":"5.x.x"
- Ajouter les fichiers de V5_ToHack sur la carte micro-SD
  - busybox
  - custom.sh
  - passwd
- insérer la carte micro-SD dans la caméra.
- redémarrer la caméra.
- connecter la caméra sur l'app Tuya
- si tout s'est bien passé, la caméra est maintenant accessible via telnet, avec comme utilisateur/mot de passe : user/user
- se connecter via telnet sur la caméra
- aller dans le répertoire /home/cfg/
- éditer le fichier tuya_config.json
  - changer le paramètre "onvif_enable":0 en "onvif_enable":1
  - changer le paramètre "onvif_pwd":"" en "onvif_pwd":"admin"
- redémarrer la caméra
- vérifier avec [ONVIF Device Manager] que le flux ONVIF est détecté et accessible (rappel: le mot de passe ONVIF configuré est "admin")


La caméra peut maitnenant être mise sur un réseau séparé et sans accès internet, la seule contrainte était de l'enregistrer sur la cloud Tuya pour l'activer. 
