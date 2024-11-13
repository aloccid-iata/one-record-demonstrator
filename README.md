# ONE Record Two Nodes

Welcome to the ONE Record Two Nodes, in this document you will find all the instructions to run a two NE:ONE server and how to setup pub/sub in ONE Record

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) installed
- [Docker Compose](https://docs.docker.com/compose/install/) installed (make sure you have compose V2)
- [Git](https://git-scm.com/downloads) installed

## Step by step guide

1) Clone the repository
   ```bash
   git clone https://github.com/IATA-Cargo/one-record-two-nodes.git
   ```
2) Switch to the directory to docker-compose
   ```bash
   cd one-record-two-nodes/docker-compose
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
| ne-one view | [ne-one view](https://git.openlogisticsfoundation.org/wg-digitalaircargo/ne-one-view) | http://localhost:3000 |
| ne-one play | [ne-one play](https://github.com/aloccid-iata/neoneplay) | http://localhost:3001 |
| graphdb | GraphDB database as database backend for ne-one-1 and ne-one-2 on two separate repositories (neone and neone2) | http://localhost:7200 |
| keycloak | Identity provider for ne-one-1 and ne-one-2 servers to authenticate ONE Record clients and to obtain tokens for outgoing requests. <br/> **Preconfigured client_id:** neone-client<br/> **Preconfigured client_secret:** lx7ThS5aYggdsMm42BP3wMrVqKm9WpNY  | http://localhost:8989 <br/> (username/password: admin/admin)|
| mockserver | A mock server that displays all notification, subscription and action request and replies with specific patterns | http://localhost:1080/mockserver/dashboard |

IMPORTANT: To simplify the setup, both NE:ONE servers are connected to a single Keycloak server, sharing the same user account.

## Postman Collection

To setup a pub/sub we have prepared a Postman collection. You will need to install Postman or a compatible software in order to use it.

1. [Download the Postman Collection here.](./assets/postman/Subscription.postman_collection.json) It will open a new github page, use the download button to get the file

2. [Download the Postman Environment here](./assets/postman/SubscriptionEnvironment.postman_environment.json). It will open a new github page, use the download button to get the file

3. Import the Environment in Postman

4. Import the Collection in Postman

5. In the Environments tab, select the subscription environment. 
There you will have server1, server2 and baseUrlKeyCloak

6. Select Collections on the right menu and open the Subscription collection already imported

7. Use the Token Request call to generate and access token

8. Copy the access token (it might be a long string, please copy the full content) in the Authorization tab of the collection folder (Subscription). Now all API calls will in the folder will use the same bearer token. Alternatively you can copy the token to the Authorization tab of each API call.

10. Run the call named "Subscription S1 to S2 Product" to have the ne-one-1 server subscribing to all Product logistics object created on ne-one-2

11. Approve the subscription on ne-one-2 with the call "Approve subscription"

12. Generate a new Product on ne-one-2 using the call "Create Product". Looking at the log of ne-one-1 you should receive a notification.

IMPORTANT: In the current setup, *ne-one-1* will receive a notification and send a ping to the mock server. To modify this behavior, update the 'QUARKUS_REST_CLIENT_NOTIFICATION_CLIENT_URL' property in the *ne-one-1* configuration within the Docker Compose file to point to your server. *Ne-one-1* will then forward the notification to *(your-host)/notifications*.

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

