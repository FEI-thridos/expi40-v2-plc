[[_TOC_]]

# Production with physical PLC & TIA Portal V19

**TBD**

# Production with PLCSIM & TIA Portal V19 Upd5

## Prerequisities

- TIA Portal V19.0 Upd5 (**requires license**)
- PLCSIM Advanced V6.0 Upd1 (**requires license**)
- **expi40-service-network docker compose running**
- MQTT Explorer (*optional*)

---

## Firewall and Network setup

Setup **Siemens PLCSIM Virtual Ethernet Adapter**:
- check Npcap,
- check Siemens PLCSIM Virtual Switch,
- for TCP/IPv4 set:
   - IP: 192.168.0.100
   - Subnet Mask: 255.255.255.0
   - Default Gateway: *leave empty*
   - Preferred and Alternative DNS Servers: *leave empty*

**Remove Siemens PLCSIM Virtual Switch check from other adapters.**

*Firewall setup does not apply.*

---

## mosquitto host bridge

1. Download official x64 .exe from https://mosquitto.org/download/
   
2. Generate passwordfile for *plcUser* using **PowerShell in Administrator mode**
   ```
   ./mosquitto_passwd -c -b ./passwordfile "plcUser" "plcP4ssword."
   ```
   
3. Create aclfile
   ```
   user plcUser
   topic write plc
   ```

4. Update mosquitto.conf
   ```
   # Set listener to PLCSIM Virtual Ethernet Adapter (IPv4:1884)
   listener 1884 192.168.0.100
 
   # Connection auth settings
   allow_anonymous false
   password_file ./passwordfile
   acl_file ./aclfile

   # Set connection bridge to docker's mosquitto
   connection bridge_to_docker
   # Forward messages from PLCSIM Virtual Eth Adapter
   # to Loopback:1883
   address 127.0.0.1:1883
   # Set credentials
   username plcUser
   password plcP4ssword.
   # Forward messages from all topics
   topic #
   ```

5. Run mosquitto local instance using CMD `.\mosquitto.exe -c .\mosquitto.conf -v`

---

## PLCSIM Advanced V6.0 Upd1

1. Set Online Access to TCP/IP Single Adapter

2. Select TCP/IP communication with Ethernet NIC

3. Start simulated PLC with properties:
   - Instance name: *any*
   - IP address [X1]: 192.168.0.101 (PLCSIM Virtual Eth Adapter IPv4 - last octet + 1)
   - Subnet mask: 255.255.255.0
   - Default gateway: *leave empty*
   - PLC family: **ET 200SP**

---

## TIA Portal V19 Upd5

1. Open project provided in /src/expi40prod

2. In Program blocks/LOpcUa/PubSub MQTT JSON/publisher_LOpcUa_typeConnParamMqtt DB block check:
   - Broker IP Address: 192.168.0.100 (**Siemens PLCSIM Virtual Ethernet Adapter**)
   - Broker Port: 1884 (**mosquitto host bridge listener port**)
   - MQTT Username: plcUser
   - MQTT Password: plcP4ssword.
   - MQTT Topic: plc

3. In Device configuration check IP address in the project:
   - IP address: 192.168.0.101 (**IP address [X1] of Virtual S7-1500 PLC**)
   - Subnet mask: 255.255.255.0

4. Compile -> Download to device -> Go online

5. Set Program blocks/LOpcUa/PubSub MQTT JSON/publisher_control.enable to **true**

6. Check:
   - mosquitto host bridge log in CMD for incoming PLC messages,
   - MQTT Explorer connected to mqtt://localhost:1883 for incoming messages to docker-compose's mosquitto.

*Documentation provided in /docu/109814033_OPC_UA_PubSub_MQTT_JSON_DOC_V1_0_en.pdf.*

---

# OPC UA PubSub Test with PLCSIM & TIA Portal V17 Upd8

## Prerequisities

- TIA Portal V17.0 Upd8 (**requires license**)
- PLCSIM Advanced V6.0 Upd1 (**requires license**)
- **expi40-service-network docker compose running**
- MQTT Explorer (*optional*)

---

## Firewall and Network setup

Setup **Siemens PLCSIM Virtual Ethernet Adapter**:
- check Npcap,
- check Siemens PLCSIM Virtual Switch,
- for TCP/IPv4 set:
   - IP: 192.168.0.100
   - Subnet Mask: 255.255.255.0
   - Default Gateway: *leave empty*
   - Preferred and Alternative DNS Servers: *leave empty*

Remove Siemens PLCSIM Virtual Switch check from other adapters.

*Firewall setup does not apply.*

---

## mosquitto host bridge

1. Download official x64 .exe from https://mosquitto.org/download/
   
2. Generate passwordfile for *plcUser* using **PowerShell in Administrator mode**
   ```
   ./mosquitto_passwd -c -b ./passwordfile "plcUser" "plcP4ssword."
   ```
   
3. Create aclfile
   ```
   user plcUser
   topic write plc
   ```

4. Update mosquitto.conf
   ```
   # Set listener to PLCSIM Virtual Ethernet Adapter (IPv4:1884)
   listener 1884 192.168.0.100
 
   # Connection auth settings
   allow_anonymous false
   password_file ./passwordfile
   acl_file ./aclfile

   # Set connection bridge to docker's mosquitto
   connection bridge_to_docker
   # Forward messages from PLCSIM Virtual Eth Adapter
   # to Loopback:1883
   address 127.0.0.1:1883
   # Set credentials
   username plcUser
   password plcP4ssword.
   # Forward messages from all topics
   topic #
   ```

5. Run mosquitto local instance using CMD `.\mosquitto.exe -c .\mosquitto.conf -v`

---

## PLCSIM Advanced V6.0 Upd1

1. Set Online Access to TCP/IP Single Adapter

2. Select TCP/IP communication with Ethernet NIC

3. Start simulated PLC with properties:
   - Instance name: *any*
   - IP address [X1]: 192.168.0.101 (PLCSIM Virtual Eth Adapter IPv4 - last octet + 1)
   - Subnet mask: 255.255.255.0
   - Default gateway: *leave empty*
   - PLC family: **S7-1500**

---

## TIA Portal V17 Upd8

1. Open project provided in /src/109814033_OPC_UA_PubSub_MQTT_JSON_PROJ_V1_0/109814033_OPC_UA_PubSub_MQTT_JSON

2. In Program blocks/ControllingPubMqtt DB set:
   - Broker IP Address: 192.168.0.100 (**Siemens PLCSIM Virtual Ethernet Adapter**)
   - Broker Port: 1884 (**mosquitto host bridge listener port**)
   - MQTT Username: plcUser
   - MQTT Password: plcP4ssword.
   - MQTT Topic: plc

3. In Device configuration set IP address in the project:
   - IP address: 192.168.0.101 (**IP address [X1] of Virtual S7-1500 PLC**)
   - Subnet mask: 255.255.255.0

4. Compile -> Download to device -> Go online

5. Set ControllingPubMqtt.enable to **true** in 'Controlling' Watch Table

6. Check:
   - mosquitto host bridge log in CMD for incoming PLC messages,
   - MQTT Explorer connected to mqtt://localhost:1883 for incoming messages to docker-compose's mosquitto.

*Documentation provided in /docu/109814033_OPC_UA_PubSub_MQTT_JSON_DOC_V1_0_en.pdf.*