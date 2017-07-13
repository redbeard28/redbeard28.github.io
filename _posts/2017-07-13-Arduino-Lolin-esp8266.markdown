---
title:  "Arduino ESP8266 LoLin NodeMCU Getting Started"
date:   2017-07-13 22:00:00
categories: [Linux]
tags: [arduino,IoT,NodeMcu,domotique,esp8266]
tipue_search_active: true
---
Dans ce tuto, nous verrons comment connecter notre ESP8266 **LOLIN v3** NodeMCU sur l'interface Arduino IDE.
Les steps sont plutôt simple et vous devriez être prêt à utiliser votre matériel en moins de 5 minutes (10 min pour les novices)


## Matériels nécessaires

Veuillez vous procurer les éléments suivants:
 1. Lolin ESP8266 => [B06XJLNB8X](https://www.amazon.fr/s/ref=nb_sb_noss?__mk_fr_FR=%C3%85M%C3%85%C5%BD%C3%95%C3%91&url=search-alias%3Daps&field-keywords=B06XJLNB8X)
 2. Câble USB de très bonne qualité


## Partie software

Dirigez vous sur l'onglet préférences de l'IDE Arduino
![Preferences Menu](https://i0.wp.com/henrysbench.capnfatz.com/wp-content/uploads/2016/09/SelectPreferences.png?w=543)


Ajoutez l'URL ***http://arduino.esp8266.com/stable/package_esp8266com_index.json*** dans la partie Additional board manager URLs
![Additional board MAnager](https://i2.wp.com/henrysbench.capnfatz.com/wp-content/uploads/2016/09/AddURL.png?resize=768%2C664)


## Board Manager
Veuillez vous diriger versvers le Board Manager (Gestionnaire de carte)
![Gestionnaire de Carte](https://i2.wp.com/henrysbench.capnfatz.com/wp-content/uploads/2016/09/SelectBoardManager.png?w=696)

## Installer les fichiers de la Bibliothèque ESP8266
Sélectionnez sketch *![Install ESP8266 Files](https://i0.wp.com/henrysbench.capnfatz.com/wp-content/uploads/2016/09/InstallESP8266Files.png?resize=768%2C514)


## Sélectionnez la carte
Veuillez sélectionner la carte NodeMCU 1.0 dans la gestion des cartes
![NodeMCU Board(https://i0.wp.com/henrysbench.capnfatz.com/wp-content/uploads/2016/09/ChooseNodeMCUBoard.png?w=675)

## Veuillez connecter votre carte
Veuillez connecter la carte au port USB de votre PC. Si la carte n'est pas repérée par le système, veuillez installer les driver **CH340** 
![Connexion LOLIN](https://i0.wp.com/henrysbench.capnfatz.com/wp-content/uploads/2016/09/Connect-to-Computer.png?resize=768%2C569)


## Paramétrage communications Cartes
Veuillez paramétrer comme suis la partie Serial Commication
![Serial Com](https://i1.wp.com/henrysbench.capnfatz.com/wp-content/uploads/2016/09/SelectClockandCPU.png?w=589)


## Test de la carte
Copiez les programme suivant dans l'Arduino IDE, compilez transférez.
Ce programme permettra de scanner le wifi et de vous afficher dans le monitor serial les réseau wifi disponible autour de vous.

<pre class="prettyprint">
#include "ESP8266WiFi.h"

void setup() {
  Serial.begin(115200);
  // Set WiFi to station mode and disconnect from an AP if it was previously connected
  WiFi.mode(WIFI_STA);
  WiFi.disconnect();
  delay(2000);
  Serial.println("Setup done");
}

void loop() {
  Serial.println("scan start");

  
  int n = WiFi.scanNetworks();// WiFi.scanNetworks will return the number of networks found
  Serial.println("scan done");
  if (n == 0)
    Serial.println("no networks found");
  else
  {
    Serial.print(n);
    Serial.println(" networks found");
    for (int i = 0; i < n; ++i)
    {
      // Print SSID and RSSI for each network found
      Serial.print(i + 1);
      Serial.print(": ");
      Serial.print(WiFi.SSID(i));
      Serial.print(" (");
      Serial.print(WiFi.RSSI(i));
      Serial.print(")");
      Serial.println((WiFi.encryptionType(i) == ENC_TYPE_NONE)?" ":"*");
      delay(10);
    }
  }
  Serial.println("");

  // Wait a bit before scanning again
  delay(5000);
}
</pre>

## Source and credits
All the credit of this page is for ![henrysbench site](http://henrysbench.capnfatz.com/henrys-bench/arduino-projects-tips-and-more/arduino-esp8266-lolin-nodemcu-getting-started/)
