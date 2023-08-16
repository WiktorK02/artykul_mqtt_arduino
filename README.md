# Wprowadzenie do Sterowania Mikrokontrolerem Arduino poprzez MQTT i Node-RED

Współczesna era internetu rzeczy (IoT) otworzyła drzwi do niezliczonych możliwości w dziedzinie automatyzacji, monitoringu i zdalnego sterowania urządzeniami. Jednym z kluczowych elementów umożliwiających te zaawansowane funkcje jest protokół MQTT (Message Queuing Telemetry Transport), który umożliwia komunikację pomiędzy urządzeniami IoT w sposób wydajny i niskopoziomowy. W połączeniu z platformą programistyczną Node-RED oraz mikrokontrolerem Arduino, możemy stworzyć potężny system do sterowania i monitorowania urządzeń za pomocą sieci.
<br><br>
W tym artykule skupimy się na wprowadzeniu do sterowania mikrokontrolerem Arduino poprzez protokół MQTT oraz wykorzystaniu narzędzia Node-RED do tworzenia interaktywnych i zaawansowanych scenariuszy. Zaczniemy od prostych funkcji, aby zapewnić czytelne i przystępne dla początkujących podstawy, dające możliwości na rozbudowania projektu w bardziej zaawansowany system.

## Przygotowanie:
![Screenshot from 2023-08-15 12-09-21](https://github.com/WiktorK02/image-to-arduino/assets/123249470/89aa93f9-005b-4841-8d4c-cbc7e2c215c3)<br><br>
Arduino (w moim przypadku Arduino rev2 wifi)<br>

3x dioda LED<br>

3x rezystor 220 ohm<br>

## Niezbedna teoria o MQTT:

Protokół MQTT (Message Queuing Telemetry Transport) jest lekkim i efektywnym protokołem komunikacyjnym, który został zaprojektowany specjalnie dla urządzeń z ograniczonymi zasobami oraz aplikacji z niskim opóźnieniem i wysoką niezawodnością. MQTT jest szeroko wykorzystywany w dziedzinie IoT do komunikacji pomiędzy urządzeniami i serwerami, umożliwiając zdalny monitoring, sterowanie i zbieranie danych.
<bR><br>
Oto krótka teoria na temat protokołu MQTT:
<br><br>
**Struktura klient-serwer**:
<br>
Protokół MQTT opiera się na architekturze klient-serwer, gdzie urządzenie MQTT (klient) komunikuje się z serwerem MQTT (brokerem). Broker pełni rolę pośrednika, który przekazuje wiadomości pomiędzy klientami. Dzięki temu, urządzenia nie muszą znać bezpośrednio adresów IP ani pozostałych szczegółów swoich partnerów komunikacyjnych.
<br><br>
**Kanały komunikacji**:
<br>
W MQTT istnieją dwa główne typy kanałów komunikacji: tematy (topics) i kolejki (queues). Tematy są używane do kierowania wiadomości do konkretnych miejsc docelowych, a kolejki zapewniają gwarancję dostarczenia wiadomości do wszystkich subskrybentów.
<br><br>
**QoS (Quality of Service)**:
<br>
MQTT oferuje trzy poziomy jakości usług (QoS):
<br>

QoS 0 (At most once): Wiadomość jest wysyłana przez klienta do brokera przynajmniej raz, ale bez potwierdzenia dostarczenia. To najszybszy i najmniej niezawodny poziom.<br>

QoS 1 (At least once): Wiadomość jest dostarczana przynajmniej raz do brokera, który przekazuje ją odbiorcom. Klient odbiera potwierdzenie dostarczenia.<br>

QoS 2 (Exactly once): Najbardziej niezawodny poziom, zapewniający dostarczenie dokładnie jednej kopii wiadomości.
<br>

**Sesje i zachowanie klientów**:
<br>
Klient MQTT może nawiązać trwałą sesję z brokerem lub połączenie jednorazowe. W przypadku trwałej sesji, broker przechowuje informacje o subskrypcjach klienta nawet po rozłączeniu, co pozwala na dostarczanie mu wiadomości, które pojawiły się podczas nieobecności.
<br>

**Kontrola dostępu**:
<br>
MQTT obsługuje mechanizmy autentykacji i uprawnień, które pozwalają na kontrolę dostępu do poszczególnych tematów oraz operacji publikowania i subskrybowania.
<br>

**Zalety protokołu MQTT**:<br>

• Lekkość: Protokół ten jest niewymagający pod względem zasobów, co sprawia, że jest idealny do urządzeń o ograniczonej mocy obliczeniowej.<br>

• Niski opóźnienie: Wiadomości są przekazywane szybko, co jest kluczowe w aplikacjach czasu rzeczywistego.<br>

• Nieawaryjność: Dzięki różnym poziomom QoS, można dostosować niezawodność komunikacji do potrzeb konkretnego zastosowania.<br>

• Skalowalność: Broker może obsługiwać wielu klientów, co umożliwia łatwą skalowalność systemu<br>

## Arduino IDE i schemat połączenia<br>
### Schemat 
![Screenshot from 2023-08-15 12-04-48](https://github.com/WiktorK02/image-to-arduino/assets/123249470/c54a1f04-c3d4-4c7f-bd71-8b3d0d5901f4)<br>

**Dioda LED podłączona do**:<br>
• czerwonego kabla (PIN8); monitoruje podłączenie Arduino do sieci WIFI<br>
• żółtego kabla (PIN12); monitoruje podłączenie do brokera MQTT<Br>
• zielonego kabla (PIN13); prezentuje działanie programu poprzez ON/OFF<br>

<br>
Przed przystąpieniem do analizy kodu, upewnij się, że masz pobrane i zaatkualizowane środowisko Arduino IDE. Na temat instalacji odsyłam do strony producenta na której jest wszystko jasno wytłumaczone.
<br>
będziesz dodatkowo potrzebował dwóch głównych bibliotek: PubSubClient oraz WiFiNINA.
<br><br>

**PubSubClient**:
<br><br>
		Biblioteka PubSubClient umożliwia komunikację z serwerem MQTT. Możesz ją pobrać i zainstalować za pośrednictwem menedżera bibliotek w środowisku Arduino. Aby to zrobić, postępuj zgodnie z poniższymi krokami:<br>

• Otwórz Arduino IDE.<br>

• Przejdź do menu "Sketch" > "Include Library" > "Manage Libraries...".<br>

• Wyszukaj "PubSubClient".<br>

• Wybierz odpowiednią wersję PubSubClient (zazwyczaj najnowszą).<br>

• Kliknij "Install" (Zainstaluj).<br><br>

  **WiFiNINA**:<br>

  Biblioteka WiFiNINA obsługuje moduł Wi-Fi typu NINA. Aby ją pobrać i zainstalować, postępuj zgodnie z instrukcjami:<br>

• Otwórz Arduino IDE.<br>

• Przejdź do menu "Sketch" > "Include Library" > "Manage Libraries...".<br>

• Wyszukaj "WiFiNINA".<br>

• Wybierz odpowiednią wersję WiFiNINA (zazwyczaj najnowszą).<br>

• Kliknij "Install" (Zainstaluj).<br>
### Analiza kodu

**Definicje stałych i zmiennych globalnych**:
``` cpp
const char* ssid = "***";
const char* password = "***";
const char* mqttServer = "***";
const int mqttPort = 1883;
const char* mqttTopic = "led/control";
const char* mqttTopicStatus = "pin/status";
const char* mqttClientID = "arduino-client";
```
Te zmienne przechowują nazwę i hasło sieci Wi-Fi, adres serwera MQTT, port MQTT, tematy komunikacji oraz identyfikator klienta MQTT.
<br>
Należy zmienić:<br>
• SSID - nazwa sieci<br>
• Hasło do sieci<br>
• IP Brokera MQTT - w naszym przypadku będzie to IP komputera, bo używamy biblioteki Brokera MQTT postawionej na Node-Red(więcej informacji o tym w akapicie o Node-Red). Jeśli jednak używacie zewnetrznego brokera, IP należy zmienić na takie na którym jest postwiony broker.
<br><br>
**Definicje pinów i zmiennych**:
``` cpp
const int ledPin = 13;
const int ledPin12 = 12;
const int ledPin8 = 8;
int ledState = LOW;

```
Tutaj zdefiniowane są numery pinów dla diod LED oraz zmienne ledState i lastButtonState, które trzymają aktualny stan diod LED.<br><br>
**Inicjalizacja WiFiClient i PubSubClient**:
``` cpp
WiFiClient wifiClient;
PubSubClient mqttClient(wifiClient);
```
Tworzony jest obiekt wifiClient do obsługi połączenia Wi-Fi oraz obiekt mqttClient do obsługi komunikacji MQTT za pomocą tego połączenia.<br><br>
**Funkcja setup()**:
``` cpp
void setup() {
  pinMode(ledPin, OUTPUT);
  pinMode(ledPin12, OUTPUT);
  pinMode(ledPin8, OUTPUT);
  digitalWrite(ledPin, ledState);
  Serial.begin(9600);
  setupWiFi();
  mqttClient.setServer(mqttServer, mqttPort);
  mqttClient.setCallback(handleMqttMessage);
  reconnect();
}
```
W funkcji setup():
<br><br>
Ustawiane są piny diod LED jako wyjścia.<br>
Ustawiany jest początkowy stan diody LED na podstawie ledState.<br>
Inicjalizowana jest komunikacja szeregowa z prędkością 9600 bps.<br>
Wywoływana jest funkcja setupWiFi() do łączenia z siecią Wi-Fi.<br>
Ustawiany jest serwer i port MQTT dla klienta mqttClient.<br>
Ustawiany jest callback dla obsługi przychodzących wiadomości MQTT.<br>
Wywoływana jest funkcja reconnect() w celu nawiązania połączenia MQTT.<br><br>
**Pętla główna (funkcja loop())**:
``` cpp
void loop() {
  if (!mqttClient.connected()) {
    reconnect();
  }
  mqttClient.loop();
}
```
W pętli głównej (loop()):

Sprawdzane jest, czy klient MQTT jest połączony. Jeśli nie jest, wywoływana jest funkcja reconnect().<br>
Wywoływana jest metoda mqttClient.loop() do obsługi komunikacji MQTT w tle.<br><br>
**Funkcja setupWiFi()**:
``` cpp
void setupWiFi() {
  delay(10);
  Serial.print("Connecting to WiFi ");
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(500);
  }
  Serial.println();
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
  digitalWrite(ledPin12, HIGH);
}
```
Funkcja setupWiFi():<br>

Wyświetla informację o próbie łączenia z siecią Wi-Fi.<br>
Wywołuje WiFi.begin() w celu łączenia z siecią Wi-Fi i oczekuje na poprawne połączenie.<br>
Wyświetla informacje o połączeniu i lokalnym adresie IP.<br>
Zapala diodę podłączoną do ledPin12.<br><br>

**Funkcja reconnect()**:
``` cpp
void reconnect() {
  while (!mqttClient.connected()) {
    Serial.print("Attempting MQTT connection...");
    if (mqttClient.connect(mqttClientID)) {
      Serial.println("connected");
      mqttClient.subscribe(mqttTopic);
      digitalWrite(ledPin8, HIGH);
    } else {
      Serial.print("failed, rc=");
      Serial.print(mqttClient.state());
      Serial.println(" try again in 5 seconds");
      delay(5000);
    }
  }
}
```
Funkcja reconnect():
<br><br>
W pętli sprawdzane jest, czy klient MQTT nie jest połączony.<br>
Jeśli klient MQTT nie jest połączony, próbuje się połączyć z serwerem MQTT przy użyciu mqttClient.connect(mqttClientID).<br>
Jeśli połączenie się powiedzie, wyświetlany jest komunikat "connected" i klient subskrybuje temat MQTT.<br>
Dioda podłączona do ledPin8 zostaje zapalona po nawiązaniu połączenia.<br>
Jeśli nie uda się nawiązać połączenia, wyświetlany jest odpowiedni komunikat z kodem błędu i oczekiwaniem 5 sekund przed kolejną próbą.<br><br>
**Funkcja handleMqttMessage()**:
``` cpp
void handleMqttMessage(char* topic, byte* payload, unsigned int length) {
  String message = "";
  for (unsigned int i = 0; i < length; i++) {
    message += (char)payload[i];
  }
  Serial.print("Received MQTT message: ");
  Serial.println(message);

  if (message.equals("ON")) {
    ledState = HIGH;
    digitalWrite(ledPin, ledState);
    Serial.println("LED is ON");
  } else if (message.equals("OFF")) {
    ledState = LOW;
    digitalWrite(ledPin, ledState);
    Serial.println("LED is OFF");
  }
}
```
Funkcja handleMqttMessage():
<br><br>
Przetwarza otrzymaną wiadomość MQTT.<br>
Konwertuje payload (treść wiadomości) na string.<br>
Wyświetla otrzymaną wiadomość.<br>
Jeśli wiadomość jest "ON", ustawia stan diody na wysoki (HIGH), zapalając ją.<br>
Jeśli wiadomość jest "OFF", ustawia stan diody na niski (LOW), wyłączając ją.<br>

### Full code
``` cpp
#include <PubSubClient.h>
#include <WiFiNINA.h>

const char* ssid = "***"; // nalezy zmienic np. tp-link-1234
const char* password = "***"; // nalezy zmienic np. kotek1234
const char* mqttServer = "***"; // nalezy zmienic IP BROKERA MQTT np. 192.168.0.34
const int mqttPort = 1883;
const char* mqttTopic = "led/control";
const char* mqttTopicStatus = "pin/status";
const char* mqttClientID = "arduino-client";

const int ledPin = 13;
const int ledPin12 = 12;
const int ledPin8 = 8;
const int buttonPin = 2;
 
int ledState = LOW;     

WiFiClient wifiClient;
PubSubClient mqttClient(wifiClient);

void setup() {
  pinMode(ledPin, OUTPUT);
  pinMode(ledPin12, OUTPUT); 
  pinMode(ledPin8, OUTPUT); 

  pinMode(buttonPin, INPUT_PULLUP);
  
  digitalWrite(ledPin, ledState);
  
  Serial.begin(9600);
  setupWiFi();
  mqttClient.setServer(mqttServer, mqttPort);
  mqttClient.setCallback(handleMqttMessage);
  reconnect();
}

void loop() {
  
  if (!mqttClient.connected()) {
    reconnect();
  }
  mqttClient.loop();
}

void setupWiFi() {
  delay(10);
  Serial.print("Connecting to WiFi ");
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(500);
  }
  Serial.println();
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
  digitalWrite(ledPin12, HIGH);
}

void reconnect() {
  while (!mqttClient.connected()) {
    Serial.print("Attempting MQTT connection...");
    if (mqttClient.connect(mqttClientID)) {
      Serial.println("connected");
      mqttClient.subscribe(mqttTopic);
      digitalWrite(ledPin8, HIGH); 
    } else {
      Serial.print("failed, rc=");
      Serial.print(mqttClient.state());
      Serial.println(" try again in 5 seconds");
      delay(5000);
    }
  }
}

void handleMqttMessage(char* topic, byte* payload, unsigned int length) {
  String message = "";
  for (unsigned int i = 0; i < length; i++) {
    message += (char)payload[i];
  }
  Serial.print("Received MQTT message: ");
  Serial.println(message);

  if (message.equals("ON")) {
    ledState = HIGH;
    digitalWrite(ledPin, ledState);
    Serial.println("LED is ON");
  } else if (message.equals("OFF")) {
    ledState = LOW;
    digitalWrite(ledPin, ledState);
    Serial.println("LED is OFF");
  }
}
```
Po dostosowaniu kodu, należy go skompilować i wgrać na płytę Arduino.


## Node-Red 

Node-RED to otwarte oprogramowanie, które umożliwia tworzenie przepływów danych (flows) w sposób graficzny i intuicyjny. Oparte na przeglądarce internetowej, narzędzie to dostarcza wizualnego interfejsu, w którym użytkownik może łączyć węzły (nodes), reprezentujące czynności lub operacje, do wytwarzania złożonych scenariuszy i programów. Node-RED pozwala na integrację urządzeń i usług oraz na wykonywanie akcji w oparciu o dane napływające z różnych źródeł.
<br><br>
![Screenshot1](https://github.com/WiktorK02/arduino-wifi-and-mqtt/assets/123249470/1014dcbf-0f6c-4290-ac38-928838bf142e)
<br><br>
Wrzucam również zawartość pliku, która umożliwia zimportowanie do środowiska Node-RED tych bloków i połączeń. <br><br> 
**WAŻNE**: Jeżeli posiadacie broker MQTT na zewnętrznym urządzeniu, należy zmienić adres IP serwera z localhost na IP brokera w bloku MQTT (nie przejmujcie się wyskoczeniem komunikatu o braku odpowiednich bibliotek po imporcie poniższego pliku). W przeciwnym wypadku przed importem należy dodatkowo pobrać bibliotekę z brokerem:

```
node-red-contrib-aedes
```
### Import do Node-Red
```
[
    {
        "id": "2c6873d2.992abc",
        "type": "mqtt out",
        "z": "fcb6a21e80097fe7",
        "name": "",
        "topic": "led/control",
        "qos": "0",
        "retain": "false",
        "respTopic": "",
        "contentType": "",
        "userProps": "",
        "correl": "",
        "expiry": "",
        "broker": "407a01e4.6b637",
        "x": 650,
        "y": 260,
        "wires": []
    },
    {
        "id": "407a01e4.6b637",
        "type": "mqtt-broker",
        "name": "",
        "broker": "localhost",
        "port": "1883",
        "clientid": "",
        "autoConnect": true,
        "usetls": false,
        "protocolVersion": "4",
        "keepalive": "60",
        "cleansession": true,
        "birthTopic": "",
        "birthQos": "0",
        "birthPayload": "",
        "birthMsg": {},
        "closeTopic": "",
        "closePayload": "",
        "closeMsg": {},
        "willTopic": "",
        "willQos": "0",
        "willPayload": "",
        "willMsg": {},
        "userProps": "",
        "sessionExpiry": "",
        "credentials": {}
    }
]
```
Po dostosowaniu parametrów należy kliknąć 'Deploy' i sprawdzić działanie projektu poprzez naciśnięcie na przycisk 'ON/OFF'. Stan diody LED powinien się zmieniać wraz z naciskaniem na przycisk.
<br>
## Podsumowanie
Jeśli zainteresował cię ten temat, zachęcam do dokładniejszego zapoznania się z zawartą instrukcją oraz do zagłębiania się w dokumentację Arduino, MQTT i Node-Red. Link do repozytorium GitHub zawierającego projekt to cenna informacja, którą możesz wykorzystać do dalszych prac i udoskonaleń. Pamiętaj, że nawet od najprostszych pomysłów można zacząć budować coś znaczącego i ciekawego.
<br>
Link do repozytorium GitHub z projektem: https://github.com/WiktorK02/arduino-wifi-and-mqtt.git"
