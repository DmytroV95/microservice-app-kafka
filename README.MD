# Delivery Service App with microservice architecture

# Content

1. [Introduction](#introduction)
2. [Endpoints Description](#endpoints-description)
3. [Running the Application](#running-the-application)
4. [Testing with Postman](#testing-with-postman)

## Introduction

This project enhances service by adding centralized and reliable email sending capabilities,
ensuring messages are sent after cargo was created.

A Java microservice handles email notifications, receiving message details asynchronously via Kafka,
storing them in ElasticSearch, and sending them via SMTP using Spring's JavaMailSender. The service
updates the message status in the database based on the success or failure of the email delivery,
recording error messages if needed.

A retry mechanism runs every 5 minutes to reattempt sending emails with an "error" status.

Email notification logic has been integrated into an existing delivery service microservice to send notifications
when specific cargo was created.

The service is containerized with Docker, and a docker-compose file is provided to launch all
components, including Kafka, ElasticSearch, Kibana, and the email service.

The application uses a ***Eureka server*** to register microservices and an API Gateway server to manage
them. You can access all microservices using the API Gateway server to make request using same port. To check the running microservices,
visit the following URL: http://localhost:8072/actuator/gateway/routes.

## Endpoints Description

### Vehicle Controller Endpoints

#### (During the application's runtime, the project utilizes Liquibase scripts to manage database schema changes, and it also saves vehicle data to the database.)

| Method | Endpoint             | Description                                     |
|--------|----------------------|-------------------------------------------------|
| GET    | `/api/vehicles`      | Retrieve all vehicles in the system.            |
| POST   | `/api/vehicles`      | Create a new vehicle.                           |
| PUT    | `/api/vehicles/{id}` | Update details of a specific vehicle by its ID. |
| DELETE | `/api/vehicles/{id}` | Delete a specific vehicle by its ID.            |

### Cargo Controller Endpoints

| Method | Endpoint                  | Description                                                                                                                                                                                                                                                                                                         |
|--------|---------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| GET    | `/api/cargos`             | Retrieve all cargo items in the system.                                                                                                                                                                                                                                                                             |
| GET    | `/api/cargos/{id}`        | Retrieve details of a specific cargo item by its ID.                                                                                                                                                                                                                                                                |
| POST   | `/api/cargos`             | Create a new cargo item.                                                                                                                                                                                                                                                                                            |
| PUT    | `/api/cargos/{id}`        | Update details of a specific cargo item by its ID.                                                                                                                                                                                                                                                                  |
| DELETE | `/api/cargos/{id}`        | Delete a specific cargo item by its ID.                                                                                                                                                                                                                                                                             |
| GET    | `/api/cargos/_list`       | Get cargos by searching parameters.                                                                                                                                                                                                                                                                                 |
| GET    | `/api/cargos/file/upload` | Upload JSON file containing cargo data. <br/> You will find the JSON result of the operation in the project's root directory named 'data_processing_response'." <br/> Please copy the sample dataset from 'src/main/resources/json_data_set' to your local machine for testing the upload endpoint."                |                           |                                                                                                                                                                                                                                                                                                     |
| GET    | `/api/cargos/_report`     | Generate an Excel report for all cargo items. <br/> To test the '_report' endpoint, you can copy the URL from the Postman collection dataset (dependent on the port), which you can find in the root of the application, and enter this URL into a browser. Please note that this endpoint may not work in Postman. |

### Email Controller Endpoints

| Method | Endpoint           | Description                              |
|--------|--------------------|------------------------------------------| 
| POST   | `/api/emails/send` | Send message to recipient email address. |

## Running the Application

In this section, you will find out how the process of running application.

- [Back to application content](#content)


####

### To run using Docker:

***Run Docker before starting the application. It is necessary because integration
tests require running test containers
in Docker.***

***Run necessary containers*** Before build of each microservice go to docker-compose.yaml
and run command to create  next containers:
- zookeeper
- elasticsearch
- kafka

***Build application*** build each microservice before creating docker images.
- ***Use command:***
```bash 
mvn clean package
```

***Environment Variables:*** Create the **.env** file in the project root directory with the necessary environment variables. These variables should include your database connection details and any secret keys required by the application.

Use the `.env.sample` file from the application root directory as a sample data to connect with the Docker container using your custom properties.

## Email Notifications

To receive messages via email, you need to create a Google application password and add it to the `.env` file along with your username (real email).

### Steps to Create a Google Application Password

1. **Go to your Google Account**: Navigate to [Google Account](https://myaccount.google.com/).
2. **Security**: Click on the "Security" tab.
3. **App Passwords**: Under "Signing in to Google," select "App Passwords." You might need to sign in again.
4. **Select App and Device**: Choose the app and device you want to generate the app password for.
5. **Generate**: Click on "Generate."
6. **Copy the Password**: A 16-character password will appear. Copy this password (do not include any spaces).

### Add to .env File

Add the following lines to your `.env` file, replacing `your_email@example.com` with your actual email and `your_generated_password` with the generated application password:

```env
SPRING_MAIL_USERNAME=email@gmail.com (your real email)
SPRING_MAIL_PASSWORD=password (application password)

TO_EMAIL=to-email@gmail.com (email where you want to send the message)
```
####

- ***Docker Setup:*** Ensure that you have Docker installed on your system.

####

- ***Docker Compose:*** The application is configured to use Docker Compose for orchestrating containers. Make sure you
  have Docker Compose installed as well. You can check if it's installed by running:
    ```bash
    docker-compose --version
    ```

####

- ***Build Docker Image:*** In your project root directory, open a terminal and run the following command to build a
  Docker image of application:
    ```bash
    docker build -t image-name .
    ```

####

- ***Start Docker Containers:*** Once the image is built, you can start your Docker containers using Docker Compose by
  running:
    ```bash
    docker-compose up
    ```
  This command will start the application and any required services (e.g., the database) defined in
  docker-compose.yml file.

***Or just run this command (make build of each microservices if necessary)***
```bash 
mvn clean package
```

```bash
docker-compose up
```

***and images and container generates automatically***
P.S. If the application can't send a request to save cargo after running the docker-compose up
command, please wait about 3-5 minutes. This happens because some microservices need time to connect to
Kafka, or the gateway server needs time to retrieve information about running microservice
instances from the Eureka server.
####

## Access Using API Gateway Server

After the containers are up and running, you can access your microservices application at the specified endpoints.

### Using Postman

If the application is running, use Postman to access the microservices with the following ports, or just import the Postman collection from the application root directory:

- Delivery service: `http://localhost:8072/delivery-service/api/cargos`

To access the other microservices endpoints using the API Gateway server, use the following format:

*Note: Security will be added in the next project updates.*


####

# Testing with Postman

In this section, instructions are provided for importing sample data into application, which can be particularly useful
for testing and development purposes.

- [Back to application content](#content)

### Importing Sample Data as JSON in Postman

To import the Endpoints Collection into Postman for testing, follow these steps:

1. Open Postman.

2. Click on the "Import" button in the top left corner.

3. In the "Import" dialog, select the "File" tab.

4. Click on the "Upload Files" button and select the "PostmanCollection.json" file from root
   project directory.

5. Click the "Import" button to add the collection and environment variables to Postman.


## ***Enjoy Your Exploring!!!***
