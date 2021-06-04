# Welcome to FIWARE TRUE Connector (FTC in short)

The FTC Execution Core Container is the core component of an IDS Connector enabling:
* the data exchange between connectors, using several protocols like HTTP, HTTPS, WS over HTTPS, IDSCPv2
* the data (IDS Message header and payload) representation using multipart/mixed, multipart/form and http-header
* interaction with the several Identity Providers (Fraunhofer AISECv1, Fraunhofer AISECv2, Orbiter) and DAPS Service for requiring and validating a token
* communication with the ENG Clearing House for registering transactions
* the interaction with the IDS Metadata Broker 
* the integration of the Usage Control App in order to enforce the contract rules.


# Getting Started

The configuration should be performed customizing the following variables in the **docker-compose** file:
* **DATA_APP_ENDPOINT=192.168.56.1:8083/incoming-data-app/dataAppIncomingMessageReceiver** DataAPP endpoint for receiveing data (F endpoint in the above picture)
* **MULTIPART=mixed** DataAPP endpoint Content Type (choose mixed for Multipart/mixed or form for Multipart/form-data) 
* Edit external port if need (default values: **8086** for **web sockets over HTTPS**, **8090** for **http**, **8887** for **A endpoint** and  **8889** for **B endpoint**)
* Forward-To protocol validation can be changed by editing **application.validateProtocol**. Default value is *true* and Forward-To URL must be set like http(https,wss)://example.com, if you choose *false* Forward-To URL can be set like http(https,wss)://example.com or just example.com and the protocol chosen (from application.properties)will be automatically set (it will be overwritten! example: http://example.com will be wss://example if you chose wss in the properties). 

If you want to use your own certificate for the AISEC Fraunhofer DAPS server: 
* Put **DAPS certificates** into the cert folder and edit related settings (i.e., *application.keyStoreName*, *application.keyStorePassword*) into the *resources/application.properties* file

Finally, run the application:

*  Execute `docker-compose up &`
