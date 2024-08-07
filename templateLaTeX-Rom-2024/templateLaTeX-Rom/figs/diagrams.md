# MQTT Architecture diagram

```plantuml
@startuml MQTT
left to right direction
actor Client1
actor Client2

Cloud Broker [

    MQTT    

    Broker  

]
node Senzor1 [
    Sensor1
]
node Senzor2 [
    Sensor2
]

Client1 ---> Broker: Publish
Client1 ---> Broker: Subscribe
Client2 ---> Broker: Publish
Client2 ---> Broker: Subscribe

Broker <--- Senzor1: Publish
Broker <--- Senzor1: Subscribe
Broker <--- Senzor2: Publish
Broker <--- Senzor2: Subscribe

@enduml
```

# RESTful structure

```plantuml
@startuml RESTful
skinparam linetype polyline
skinparam shadowing true 
left to right direction
actor Client
database "Database" as db
package "RESTful API" as rest {
    rectangle View {
        rectangle Routes [
            
            Routes

        ]   
    }
    rectangle Resources [
        

            Resource
            Manager     


    ]
    rectangle "Resources" as res
    rectangle Model [
        
        Model

    ]

    Client --> Routes : "HTTP Request"
    Routes --> Resources
    Resources --> Model
    Resources <-- Model
    Routes <-- Resources
    Model <--> db : "CRUD"
    Client <-- View : "HTTP Response"
    res <-- Resources
}
@enduml
```

# Retrofit REST API Client
```plantuml
@startuml
skinparam linetype polyline
skinparam shadowing true 

rectangle "\tAndroid\t\n\tActivity\t\t" as activity
package Retrofit as retrofit {
    cloud Call_Object as c1 #white;line.dashed
    cloud Java_Object as javaobj #white;line.dashed
    rectangle API as api
    rectangle RESTAdapter as restadapter
    rectangle OkHTTP as okhttp
    api --> c1
    c1 --> restadapter
    api <-- javaobj
    javaobj <-- restadapter
    
    restadapter --> okhttp
    restadapter <-- okhttp
}
rectangle "\nRESTful Server\n" as server
cloud Event #white;line.dashed
cloud Show #white;line.dashed
cloud HTTP_Req as HTTPReq #white;line.dashed
cloud HTTP_Rsp as HTTPRsp #white;line.dashed
activity --> Event
Event --> api
activity <-- Show
Show <-- api
okhttp --> HTTPReq
HTTPReq --> server
HTTPRsp <-- server
okhttp <-- HTTPRsp
@enduml
```

# SPI Master and Multiple Slaves
```plantuml
@startuml
skinparam linetype ortho
skinparam shadowing true
top to bottom direction

rectangle master as "\n\n\nSPI Master" {
    portout m2 as "<b>SCK"
    portout m3 as "<b>MOSI"
    portout m4 as "<b>MISO"
    portout m1 as "<b>SS2"
    portout m0 as "<b>SS1"
}
rectangle slave1 as "\n\n\nSPI Slave 1" {
    portin s1 as "<b>SS1"
    portin s2 as "<b>SCK"
    portin s3 as "<b>MOSI"
    portin s4 as "<b>MISO"
}

rectangle slave2 as "\n\n\nSPI Slave 2" {
    portin ss4 as "<b>MISO"
    portin ss1 as "<b>SS2"
    portin ss2 as "<b>SCK"
    portin ss3 as "<b>MOSI"
}

m0 ---> s1
m1 ---> ss1

m2 ---> s2 
m3 ---> s3 
m4 ---> s4

m2 ---> ss2
m3 ---> ss3
m4 ---> ss4
@enduml
```

# SPI Time diagram
```plantuml
@startuml
clock "NSCLK" as sclk with period 2 pulse 1 offset 3 
concise "MOSI" as mosi
concise "MISO" as miso
binary "SS" as ss

@0
ss is high
mosi is D0
miso is X

@2
ss is low

@4
mosi is D1

@6
mosi is D2

@8
mosi is D3

@10
mosi is D4

@12
mosi is D5

@14
mosi is D6

@16
mosi is D7

@18
mosi is D8
miso is S0
@20
mosi is D9
miso is S1
@22
mosi is D10
miso is S2
@24
mosi is D11
miso is S3
@26
mosi is D12
miso is S4
@28
mosi is D13
miso is S5
@30
mosi is D14
miso is S6
@32
mosi is D15
miso is S7
@34
mosi is {-}
miso is {-}
ss is high

@enduml
```

# Application general scheme
```plantuml
@startuml
skinparam shadowing true
left to right direction
!$ICONURL = "https://raw.githubusercontent.com/tupadr3/plantuml-icon-font-sprites/v3.0.0/icons"
!include $ICONURL/common.puml
!include $ICONURL/font-awesome-6/mobile_screen_button.puml
FA6_MOBILE_SCREEN_BUTTON(android, Mobile)
rectangle mqtt as "\nMQTT Broker\n"
!$ICONURL = "https://raw.githubusercontent.com/tupadr3/plantuml-icon-font-sprites/v3.0.0/icons"
!include $ICONURL/common.puml
!include $ICONURL/font-awesome-5/server.puml
FA5_SERVER(server, Server)
database mongodb as "\nMongoDB\nDatabase\n"
rectangle house as " " {
collections sensor as "\nSensors\n"
!$ICONURL = "https://raw.githubusercontent.com/tupadr3/plantuml-icon-font-sprites/v3.0.0/icons"
!include $ICONURL/common.puml
!include $ICONURL/material/router.puml
MATERIAL_ROUTER(router, Router)
}

android --> mqtt  
android <-- mqtt : MQTT
android --> server
android <-- server : HTTP
mqtt --> server
mqtt <-- server : HTTP
server --> mongodb
server <-- mongodb : "TCP/IP\nSockets"
mqtt ---> router
mqtt <--- router : MQTT
router --> sensor : Wi-Fi
router <-- sensor


@enduml
```

# Application general scheme
```plantuml
@startuml
skinparam shadowing true
skinparam linetype poly
skinparam classAttributeIconSize 0

class ActivityWelcome {
    -sharedPreferences : SharedPreferences
    -sensorArrayList : ArrayList<SensorListItem>
    -scheduler : ScheduledExecutorService
    +updateMetrics()
}
class ActivitySensor {
    -chartTemperature
    -chartHumidity
    +updateMetrics()
    +chartInitTemperatureData()
    +chartInitHumidityData()
}
class ScheduledDataAcquisition <<Runnable>> {
    -mqttClient : MQTTClient
    -sensorListItem : SensorListItem
    +run()
    +notifyListeners()
}
class UIThreadHandler {
    -listeners : Set<Listener>
    +addListener()
    +removeListener()
    +notifyListeners()
}
class MQTTClient {
    -mqttAndroidClient
    +connect()
    +subscribe()
    +unsubscribe()
    +publish()
    +disconnect()
}
class SensorListItem {
    -name
    -type
    -status
    -imageId
    -lastTemp
    -lastHumid
}
class DBAPIClient <<Retrofit>> {
    -retrofit : Retrofit
    +getClient()
}
class SensorReading {
    +sensorMAC
    +type
    +value
    +timestamp
}
class SensorReadingsList {
    +sensorReadings : List<SensorReading>
}


interface Listener {
    +updateMetrics()
}
interface DBAPIInterface {
    +doGetSensorReading()
    +doGetSensorReadingsList()
}


ActivityWelcome ..|> Listener
ActivityWelcome --|> ScheduledDataAcquisition
ActivityWelcome --|> SensorListItem

ActivitySensor ....|> Listener
ActivitySensor --|> DBAPIClient

DBAPIClient --|> DBAPIInterface
DBAPIInterface --|> SensorReadingsList
DBAPIInterface --|> SensorReading

ScheduledDataAcquisition --|> UIThreadHandler
ScheduledDataAcquisition --|> MQTTClient
ScheduledDataAcquisition --|> SensorListItem

UIThreadHandler --|> UIThreadHandler
UIThreadHandler o-- Listener

@enduml
```

# Flask Project Structure
```
	|-monitoring_system_bknd/
		|-src/
			|-__init__.py
			|-sensor_data_controller.py
            |-sensor_data_model.py
            |-sensor_data_routes.py
            |-sensor_data_view.py
		|-venv/
		|-.env
		|-requirements.txt
		|-run.py
```

# JSON data format from MQTT Broker to Android
MQTT message 1: {"temperature":28.380}
MQTT message 2: {"humidity":59.912}

# JSON data format from RESTful Server to Android
 {
     "list": [
         {
             "sensorMAC": "F8F005ADB2A9",
             "type": "humidity",
             "value": 61.846,
             "timestamp": "2024-07-08T15:00:03.877000Z"
         },
         {
             "sensorMAC": "F8F005ADB2A9",
             "type": "humidity",
             "value": 61.846,
             "timestamp": "2024-07-08T15:00:22.911000Z"
         },
         {
             "sensorMAC": "F8F005ADB2A9",
             "type": "humidity",
             "value": 61.846,
             "timestamp": "2024-07-08T15:00:42.256000Z"
         },
         {
             "sensorMAC": "F8F005ADB2A9",
             "type": "humidity",
             "value": 61.749,
             "timestamp": "2024-07-08T15:01:01.619000Z"
         },
         {
             "sensorMAC": "F8F005ADB2A9",
             "type": "humidity",
             "value": 61.547,
             "timestamp": "2024-07-08T15:01:20.949000Z"
         },
         {
             "sensorMAC": "F8F005ADB2A9",
             "type": "humidity",
             "value": 61.45,
             "timestamp": "2024-07-08T15:01:40.300000Z"
         },
         {
             "sensorMAC": "F8F005ADB2A9",
             "type": "humidity",
             "value": 61.322,
             "timestamp": "2024-07-08T15:01:59.647000Z"
         }
     ]
 }

 # MQTT Broker internal architecture

 ```plantuml
@startuml
skinparam shadowing true
left to right direction
rectangle mosquitto [
    MQTT Broker
    Mosquitto
]
rectangle subscriber [
    \tWildcard
    MQTT   Subscriber
]

mosquitto --> subscriber: Every publish
mosquitto <-- subscriber: Subscribe for #

@enduml
```

# PI MongoDB Document Example 
sensorReadings> db.readings.findOne()
{
    timestamp: ISODate('2024-06-05T12:54:03.085Z'),
    metadata: { sensorMAC: 'F8F005ADB2A9', type: 'temperature' },
    value: 27.514,
    _id: ObjectId('66605febb0abead3d2c8f28a')
}

# PI - Sensor Unit Diagram

 ```plantuml
@startuml
skinparam shadowing true
left to right direction
skinparam linetype ortho

component artyz7 as "Arty Z7" {
    !$ICONURL = "https://raw.githubusercontent.com/tupadr3/plantuml-icon-font-sprites/v3.0.0/icons"
    !include $ICONURL/common.puml
    !include $ICONURL/font-awesome-6/microchip.puml
    FA6_MICROCHIP(zynq7000, "Zynq 7000")
    
}
component wincxpro as "WINC1500-XPRO" {
    !$ICONURL = "https://raw.githubusercontent.com/tupadr3/plantuml-icon-font-sprites/v3.0.0/icons"
    !include $ICONURL/common.puml
    !include $ICONURL/font-awesome-6/wifi.puml
    FA6_WIFI(atwinc1500, ATWINC1500)
}

component hygro as "Pmod HYGRO" {
    !$ICONURL = "https://raw.githubusercontent.com/tupadr3/plantuml-icon-font-sprites/v3.0.0/icons"
    !include $ICONURL/common.puml
    !include $ICONURL/font-awesome-6/temperature_high.puml
    FA6_TEMPERATURE_HIGH(hdc1080, HDC1080)
}

wincxpro <----> artyz7 : SPI
wincxpro <---- artyz7 : RESET_N
wincxpro ----> artyz7 : IRQ
artyz7 <---> hygro : I2C


@enduml
```

# PI - Main State Machine
 ```plantuml
@startuml
skinparam shadowing true
skinparam ConditionEndStyle diamond
:Initialize all peripherals;
repeat :Search for AP information in memory;
if (Have saved AP info?) then (Yes)
    :Connect to router;
    :Initialize MQTT module;
    :Connect Socket;
    :Connect to MQTT Broker; 
    :Subscribe for Alarms;
    repeat
        if (Queue Empty?) then (No)
            :Prepare packet;
            :Send MQTT Publish;
        elseif (Publish time?) then (No)
        elseif (Data ready?) then (Yes)
            :Enqueue MQTT publish;
            :Compute Next publish;
        else
            :Start Acq;
        endif
    repeat while (Lost Router Connection?) is (No) not (Yes)
else (No)
    :Enter AP Mode;
    :Wait user connection;
    :Listen for Socket Conn;
    :Exchange router info;    
    :Save router info;
    :Close AP Mode;
endif
repeat while

@enduml
```