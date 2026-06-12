# Emmetteur récepteur basique LoRa

Matériel:

2 Module LoRa
2 Arduino nano
1 Bobine de SnPb
2 Tiges de cuivre de 17.3cm
1 Fer à souder
16 Câble Dupont

1:

souder 1 tige de cuivre par module LoRa en guise d'antenne
souder les pattes au Lora

2:

Connecter à l'aide des câbles Dupont Les Loras avec les Arduino dans cet ordre :

Lora            Arduino

3.3V            3.3V

GND             GND

SCK             D13

MISO            D12

MOSI            D11

NSS             D10

DIO0            D2

RST             D9


3:

ajouter la bonne bibliothèque à votre Arduino
Tool -->Manage libraries --> taper dans la barre de recherche LoRa by Sandeep Mistry

4:

entrer ce code pour votre émetteur :

#include <SPI.h>
#include <LoRa.h>

// Broches
#define LORA_CS   10
#define LORA_RST   9
#define LORA_IRQ   2

// Fréquence 433 MHz
#define FREQ 433E6

int compteur = 0;

void setup() {
  Serial.begin(9600);
  while (!Serial);

  Serial.println("Démarrage émetteur LoRa...");

  // Configuration des broches
  LoRa.setPins(LORA_CS, LORA_RST, LORA_IRQ);

  // Tentative d'initialisation
  if (!LoRa.begin(FREQ)) {
    Serial.println("ERREUR : Module LoRa non détecté !");
    Serial.println("Vérifiez le câblage SPI.");
    while (1);
  }

  // Paramètres radio
  LoRa.setSpreadingFactor(7);         // SF7 = rapide, courte portée
  LoRa.setSignalBandwidth(125E3);     // 125 kHz
  LoRa.setCodingRate4(5);             // 4/5
  LoRa.setTxPower(20);                // Puissance max

  Serial.println("Émetteur prêt !");
}

void loop() {
  Serial.print("Envoi du paquet #");
  Serial.println(compteur);

  // Début du paquet
  LoRa.beginPacket();
  LoRa.print("Paquet #");
  LoRa.print(compteur);
  LoRa.endPacket();  // Envoie le paquet

  Serial.println("Paquet envoyé !");
  compteur++;

  delay(2000);
}

5:

et ce code pour votre récepteur :

#include <SPI.h>
#include <LoRa.h>

#define LORA_CS   10
#define LORA_RST   9
#define LORA_IRQ   2

#define FREQ 433E6

void setup() {
  Serial.begin(9600);
  while (!Serial);

  Serial.println("Démarrage récepteur LoRa...");

  LoRa.setPins(LORA_CS, LORA_RST, LORA_IRQ);

  if (!LoRa.begin(FREQ)) {
    Serial.println("ERREUR : Module LoRa non détecté !");
    while (1);
  }

  LoRa.setSpreadingFactor(7);
  LoRa.setSignalBandwidth(125E3);
  LoRa.setCodingRate4(5);

  Serial.println("Récepteur prêt, en écoute...");
}

void loop() {
  int taille = LoRa.parsePacket();

  if (taille) {
    Serial.print("Paquet reçu (");
    Serial.print(taille);
    Serial.print(" octets) : ");

    while (LoRa.available()) {
      Serial.print((char)LoRa.read());
    }

    Serial.print(" | RSSI : ");
    Serial.print(LoRa.packetRssi());
    Serial.println(" dBm");
  }
}

6:

FIN
