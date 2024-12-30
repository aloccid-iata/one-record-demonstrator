# ONE Record Demonstrator

Welcome to the ONE Record Demonstrator, in this document you will find all the instructions to run a two NE:ONE server and how to setup pub/sub in ONE Record

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) installed
- [Docker Compose](https://docs.docker.com/compose/install/) installed (make sure you have compose V2)
- [Git](https://git-scm.com/downloads) installed

## Step by step guide

1) Clone the repository
   ```bash
   git clone https://github.com/IATA-Cargo/one-record-demo.git
   ```
2) Switch to the directory to docker-compose
   ```bash
   cd one-record-demo/docker-compose
   ```
   If you have Mac or Linux, please reset folder permissions 
   ```bash
   chmod -R 755 ./
   ```
4) Start all services with [docker compose](https://docs.docker.com/compose/)
   ```bash
   docker compose up -d
   ```
5) Wait until all containers are up and running:
   ```bash
   [+] Running 6/6
    ✔ Network docker-compose_default            Created 0.0s 
    ✔ Container docker-compose-graph-db-1       Healthy 0.0s 
    ✔ Container docker-compose-keycloak-1       Healthy 0.0s 
    ✔ Container docker-compose-ne-one-server-1  Started 0.0s 
    ✔ Container docker-compose-graph-db-setup-1 Started 0.0s
   ```
6) Try to access the ONE Record Server by  http://localhost:8080 or http://localhost:8081 using your favorite browser. 
   You should see a HTTP Error 401, because you did not authenticate yet. But this confirms that the ONE Record Server is up and running.

# Overview of services

| Name | Description | Base URL / Admin UI |
|-|-|-|
| ne-one-1 | [ne-one server](https://git.openlogisticsfoundation.org/wg-digitalaircargo/ne-one) | http://localhost:8080 |
| ne-one-2 | [ne-one server](https://git.openlogisticsfoundation.org/wg-digitalaircargo/ne-one) | http://localhost:8081 |
| ne-one play | [ne-one play](https://github.com/aloccid-iata/neoneplay) | http://localhost:3001 |
| ONExplorer one| ONE Record operational interface | http://localhost:4080 |
| ONExplorer two| ONE Record operational interface | http://localhost:5080 |
| ONExplorer three| ONE Record operational interface | http://localhost:6080 |
| ONExplorer server one| ONE Record operational interface node server used for notification and subscription| http://localhost:4081 |
| ONExplorer server two| ONE Record operational interface node server used for notification and subscription| http://localhost:5081 |
| ONExplorer server three| ONE Record operational interface node server used for notification and subscription| http://localhost:6081 |
| graphdb | GraphDB database as database backend for ne-one-1 and ne-one-2 on two separate repositories (neone and neone2) | http://localhost:7200 |
| keycloak | Identity provider for ne-one-1 and ne-one-2 servers to authenticate ONE Record clients and to obtain tokens for outgoing requests. <br/> **Preconfigured client_id:** neone-client<br/> **Preconfigured client_secret:** lx7ThS5aYggdsMm42BP3wMrVqKm9WpNY  | http://localhost:8989 <br/> (username/password: admin/admin)|
| mockserver | A mock server that displays all notification, subscription and action request and replies with specific patterns | http://localhost:1080/mockserver/dashboard |
IMPORTANT: 
- To simplify the setup, both NE:ONE servers are connected to a single Keycloak server, sharing the same user account.
- Each ops ui is directly connected with a ops ui server to stream notification.
- You can connect two ops ui to the two ne-one server and keep one as a standalone client. The ops ui server is be able to reply to subscription requests and receive direct notifications.


## Get a token

To get a token we have prepared a Postman collection. You will need to install Postman or a compatible software in order to use it.

1. [Download the Postman Collection here.](./assets/postman/Subscription.postman_collection.json) It will open a new github page, use the download button to get the file

2. [Download the Postman Environment here](./assets/postman/SubscriptionEnvironment.postman_environment.json). It will open a new github page, use the download button to get the file

3. Import the Environment in Postman

4. Import the Collection in Postman

5. In the Environments tab, select the subscription environment. 
There you will have server1, server2 and baseUrlKeyCloak

6. Select Collections on the right menu and open the Subscription collection already imported

7. Use the Token Request call to generate and access token

8. Copy the access token

## Connect ONExplorer to NE:ONE

We show here the process to connect the ONExplorer to a NE:ONE server

1. Connect to a ONExplorer, in this image you have three possibilities:
   - http://localhost:4080
   - http://localhost:5080
   - http://localhost:6080

2. Click on settings in the sidebar
3. Setup the Internal API Settings - Base URL with one of the two NE:ONE servers respectively
   - http://localhost:8080
   - http://localhost:8081
4. Use the token generated in the previous section for the token field.
5. Now the ONExplorer is linked with one of the two NE:ONE servers. You can repeat the steps for a second ONExplorer.

## Add external servers in the ONExplorer 

We show here how to add an external server to the ONExplorer.

1. Connect to a ONExplorer, in this image you have three possibilities:
   - http://localhost:4080
   - http://localhost:5080
   - http://localhost:6080

2. Click on settings in the sidebar
3. Click on ADD SERVER in the External Servers section
4. On the popup insert:
   - Server name
   - The base url of the server (to use the NE:ONE server of these image use http://localhost:8080 or 8081. To use the Ops UI server input http://localhost:4081 or 5081 or 6081)
   - A OIDC token (for NE:ONE you can use the get a token section while for Ops Ui server just put a random character)
6. Click on ADD 
7. Now the ONExplorer can interact with external servers

## Add NE:ONE server into NE:ONE Play

1. Connect to NE:ONE Play http://localhost:3001 

2. Click on the setting button in the top-right corner (cog icon)

3. Add your ne-one-1 server following this instruction:

    - Organization Name: <Choose a name (any string is accepted)>
    - Protocol: http
    - Host: http://localhost:8080  
    - Token : <Use the postman collection to generate a token and copy it here (follow the previous paragraph)>
    - Color : pick up a random color

4. Add your ne-one-1 server following this instruction:

    - Organization Name: <Choose a name (any string is accepted)>
    - Protocol: http
    - Host: http://localhost:8081  
    - Token : <Use the postman collection to generate a token and copy it here (follow the previous paragraph)>
    - Color : pick up a random color

5. Now you can start using NE:ONE Play. 

