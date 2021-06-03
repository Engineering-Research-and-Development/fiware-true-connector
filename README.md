# TRUE CONNECTOR
[![License: AGPL](https://img.shields.io/github/license/Engineering-Research-and-Development/fiware-true-connector.svg)](https://opensource.org/licenses/AGPL-3.0)
<br/>
**TRUE** (**TRU**sted **E**ngineering) **Connector** for the IDS (International Data Space) ecosystem

The TRUE Connector is composed of three components:

* [Execution Core Container (ECC)](https://github.com/Engineering-Research-and-Development/true-connector-execution_core_container), open-source project designed by ENG. It is in charge of the data exchange through the IDS ecosystem representing data using the IDS Information Model and interacting with an external Identity Provider. It is also able to communicate with an IDS Broker for registering and querying information.
* [Fiware Data Application](https://github.com/Engineering-Research-and-Development/true-connector-fiware_data_app), open-source project designed by ENG. It represents a trivial data application for generating and consuming data on top of the ECC component.
* [Usage-Control (UC) Data Application](https://github.com/Engineering-Research-and-Development/true-connector-uc_data_app), a customized version of the Fraunhofer IESE base application for integrating the MyData Framework (a Usage Control Framework designed and implemented by Fraunhofer IESE) in a connector.

![TRUE Connector Architecture](docs/img/TRUE_Connector_Architecture.png?raw=true "TRUE Connector Architecture")

| :books: [Documentation](https://true-connector.rtfd.io) | :whale: [Docker Hub](https://hub.docker.com/) | :dart: [Roadmap](https://github.com/Engineering-Research-and-Development/fiware-true-connector/blob/master/roadmap.md) |
| ------------------------------------------------------- | --------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |

## How to Configurate and Run

The configuration should be performed customizing the following variables in the **.env** file:

* **DATA_APP_ENDPOINT=192.168.56.1:8084/data** DataAPP endpoint for receiveing data (F endpoint in the above picture)
* **MULTIPART_EDGE=mixed** DataAPP A-endpoint Content Type (choose *mixed* for Multipart/mixed or *form* for Multipart/form-data or *http-header* for Multipart/http-header) 
* **MULTIPART_ECC=mixed** Execution Core Container B-endpoint Content Type (choose *mixed* for Multipart/mixed or *form* for Multipart/form-data or *http-header* for Multipart/http-header) 
* Edit external ports if need (default values: **8086** for **WS over HTTPS**, **8090** for **http**, **8889** for **B endpoint**, **29292** for **IDSCP2**)
* Forward-To protocol validation can be changed by editing **application.validateProtocol**. Default value is *true* and Forward-To URL must be set like http(https,wss)://example.com, if you choose *false* Forward-To URL can be set like http(https,wss)://example.com or just example.com and the protocol chosen (from application.properties)will be automatically set (it will be overwritten! example: http://example.com will be wss://example if you chose wss in the properties).
* For websocket configuration, in DataApp resource folders, configure *config.properties* file, set following fields

```
server.ssl.key-password=changeit
server.ssl.key-store=/cert/ssl-server.jks
```
Or leave default values, if certificate and its password are correct.

### Supported Identity Providers

If it is needed to run the connector in developer mode please set the following properties as false:

```
CACHE_TOKEN=false
FETCH_TOKEN_ON_STARTUP=false
application.isEnabledDapsInteraction=false
```

This allow you to skip  the Daps interaction during the process in the development phase, otherwise you need to have a certificate provided from the CA offering Identity provider service.


The TRUE Connector is able to interact with the following Identity Providers:

* **AISECv1** put the certificate in the *cert* folder, edit related settings (i.e., *application.keyStoreName*, *application.keyStorePassword*) (in the *.env*) and set the *application.dapsVersion* (in the *resources/application-docker.properties*) to *v1*
* **AISECv2** put the certificate in the *cert* folder,edit related settings (i.e., *application.keyStoreName*, *application.keyStorePassword*) (in the *.env*) and set the *application.dapsVersion* (in the *resources/application-docker.properties*) to *v2*
* **ORBITER** put the certificate in the *cert* folder, edit related settings (i.e., *application.daps.orbiter.privateKey*, *application.daps.orbiter.password*) (in the *.env*) and set the *application.dapsVersion* (in the *resources/application-docker.properties*) to *orbiter*


The *application.dapsUrl* (in the *resources/application-docker.properties*) property must be set properly in order to address the right DAPS server.

Finally, run the application:

*  Execute `docker-compose up &`

## Endpoints
The TRUE Connector will use two protocols (http and https) as described by the Docker Compose File.
It will expose the following endpoints:

```
/proxy 
```
to receive data incomming request, and based on received request, forward request to Execution Core Connector (the P endpoint in the above picture)

``` 
/data 
```
to receive data (IDS Message) from a sender connector (the B endpoint in the above picture)
Furthermore, just for testing it will expose (http and https):

```
/about/version 
```
returns business logic version 

## Configuration
The ECC supports three different way to exchange data:

*  **REST endpoints** enabled if *WS_EDGE=false* and *WS_ECC=false*
*  **IDSCP2** enabled if *IDSCP2=true* and WS_ECC = false </br>For *WS_EDGE=true* (use websocket on the edge, false for REST on the edge) 
*  **Web Socket over HTTPS** enabled if *WS_EDGE=true* and *WS_ECC=true* and *IDSCP2=false* for configuration which uses web socket on the edge and between connectors.

For trusted data exchange define in *.env* the SSL settings:

*  KEYSTORE-NAME=changeit(JKS format)
*  KEY-PASSWORD=changeit
*  KEYSTORE-PASSWORD=changeit
*  ALIAS=changeit

## How to Test
The reachability could be verified using the following endpoints:

*  **http://{IP_ADDRESS}:{HTTP_PUBLIC_PORT}/about/version**

Keeping the provided docker-compose, for Data Provider URL will be:

*  **http://{IP_ADDRESS}:8090/about/version**

For Data Consumer, with provided docker-compose file:

*  **http://{IP_ADDRESS}:8091/about/version**


## How to Exchange Data

For details on request samples please check following link [Backend DataApp Usage](https://github.com/Engineering-Research-and-Development/market4.0-data_app_test_BE/blob/master/README.md)

Be sure to use correct configuration/ports for sender and receiver Data App and Execution Core Container (check .env file).

Default values:

```
DataApp URL: https://{IPADDRESS}:8084/proxy 
"Forward-To": "https://{RECEIVER_IP_ADDRESS}:8889/data",
```

For WSS flow:

```
DataApp URL: https://{IPADDRESS}:8084/proxy
"multipart": "wss",
"Forward-To": "wss://ecc-provider:8086/data",
"Forward-To-Internal": "wss://ecc-consumer:8887",
```

### WebSocket 

On the following link, information regarding WebSocket Message Streamer implementation can be found here [WebSocket Message Streamer library](https://github.com/Engineering-Research-and-Development/market4.0-websocket_message_streamer).

#### IDSCP2
Follow the REST endpoint or WS examples, put the server hostname/ip address in the Forward-To header (*wss/https://{RECEIVER_IP_ADDRESS/Hostname}:{WS_PUBLIC_PORT}*).
* **AISECv2** put the certificates (keyStore and trustStore) in the *cert* folder,edit related settings (*IDSCP2 AISEC DAPS settings* section in env file)


## Clearing House

The TRUE Connector supports is able to communicate with the ENG Clearing House for registering transactions. 

## Broker

The TRUE Connector integrates some endpoints for interacting with an IDS Broker described in [Broker](https://github.com/Engineering-Research-and-Development/fiware-true-connector/blob/master/docs/broker.md) section

## Usage Control
The TRUE Connector integrates the [Fraunhofer MyData Framework](https://www.mydata-control.de/) for implementing the Usage Control. Details about the PMP and PEP components can be found [here](docs/usage_control_rules.md). 

## Contract Negotiation - simple flow

For simple contract negotiation flow, with ContractAgreement read from file, please check following link
[Data App Contract Negotiation](https://github.com/Engineering-Research-and-Development/true-connector-basic_data_app/blob/master/README.md#markdown-header-Contract-Negotiation-simple-flow) 


## License

The TRUE Connector is licensed under [Affero General Public License (GPL) version 3](./LICENSE).

© 2020 Engineering Ingegneria Informatica S.p.A.


### Are there any legal issues with AGPL 3.0? Is it safe for me to use?

No problem in using a product licensed under AGPL 3.0. Issues with GPL (or AGPL) licenses are mostly related with the
fact that different people assign different interpretations on the meaning of the term “derivate work” used in these
licenses. Due to this, some people believe that there is a risk in just _using_ software under GPL or AGPL licenses
(even without _modifying_ it).

For the avoidance of doubt, the owners of this software licensed under an AGPL 3.0 license wish to make a clarifying
public statement as follows:

"Please note that software derived as a result of modifying the source code of this software in order to fix a bug or
incorporate enhancements is considered a derivative work of the product. Software that merely uses or aggregates (i.e.
links to) an otherwise unmodified version of existing software is not considered a derivative work, and therefore it
does not need to be released as under the same license, or even released as open source."
