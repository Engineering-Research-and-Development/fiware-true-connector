## How to Configure and Run

The configuration should be performed customizing the following variables in the **.env** file. 

There are general properties for connector and specific properties for PROVIDER Configuration and CONSUMER Configuration, separated with corresponding labels. We tried to name properties as clear as possible, so that names are self descriptive, and that user can know, what this property does just by its name.

* **MULTIPART_ECC=mixed** Execution Core Container B-endpoint Content Type (choose *mixed* for Multipart/mixed or *form* for Multipart/form-data or *http-header* for Multipart/http-header), make sure to disable WS_ECC and IDSCP2.
* **WS_ECC=false** Execution Core Container B-endpoint - by default it is set to false, change this one to true for enabling wss communication between Connectors. There are two additional condition that needs to be set for WSS configuration - MULTIPART_ECC=mixed and IDSCP2=false
* **IDSCP2=false** Execution Core Container B-endpoint - by default it is set to false, change this one to true for enabling IDSCPv2 protocol communication between Connectors. Be sure to disable WS_ECC, since connectors communicate using one of protocols - REST, WSS or IDSCPv2.

* **Forward-To protocol validation** can be changed by editing **application.validateProtocol**. Default value is *true* and Forward-To URL must be set like http(https,wss)://example.com, if you choose *false* Forward-To URL can be set like http(https,wss)://example.com or just example.com and the protocol chosen (from application.properties)will be automatically set (it will be overwritten! example: http://example.com will be wss://example if you chose wss in the properties).
* Edit external ports if need (default values: **8086** for **WS over HTTPS**, **8090** for **http**, **8889** for **B endpoint**, **29292** for **IDSCP2**)

For trusted data exchange define in *.env* the SSL settings (default values):

*  KEYSTORE-NAME=ssl-server.jks
*  KEY-PASSWORD=changeit
*  KEYSTORE-PASSWORD=changeit
*  ALIAS=execution-core-container

If you have your own certificate, self signed or other, be sure to change those properties to match with other certificate. Keystore should be in jks format.

Provider/Consumer configuration properties
* **DATA_APP_ENDPOINT=https://ecc-provider:8083/data** DataAPP endpoint for receiveing data (F endpoint in the above picture)
* **MULTIPART_EDGE=mixed** DataAPP A-endpoint Content Type (choose *mixed* for Multipart/mixed or *form* for Multipart/form-data or *http-header* for Multipart/http-header)
* For websocket configuration, in DataApp resource folders, configure *config.properties* file, set following fields

## Connector reachability - endpoints
The TRUE Connector will use two protocols (http and https) as described by the Docker Compose File.
Once Connector is started, the reachability could be verified using the following endpoints:

*  **http://{IP_ADDRESS}:{HTTP_PUBLIC_PORT}/about/version**

Keeping the provided docker-compose, for Data Provider URL will be:

*  **http://{IP_ADDRESS}:8090/about/version**

For Data Consumer, with provided docker-compose file:

*  **http://{IP_ADDRESS}:8091/about/version**

For sending proxy requests:
*  **http://{IP_ADDRESS}:8084/proxy**

For receiving incoming requests on B-endpoint:
*  **http://{IP_ADDRESS}:8889/data**


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
