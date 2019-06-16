# Azure IoT 101 - Hands-on Labe

Introduction
============

This hands-on lab poses a set of challenges to get you started with hands-on experience to the Azure IoT stack.

### Prerequesits

- You need an Azure subscription. If you do not have an Azure Subscription yet, you can also do this lab with a [free trial subscription]( https://azure.microsoft.com/en-us/free/)
 - You should have a general idea about Azure. This lab does not include an Azure primer.
 - **Very important:** This training does not provide a step-by-step guide which you just need to click through or paste stuff around. Instead this lab requires you to figure solutions out on your own! If the instructions or hints mention concepts which you are not familiar with, please do not hesitate to either ask your buddies in the training or your favorite search engine :)

 The each step describes the desired outcome. Often there are multiple ways to achive this goal. Every way to solve it, is correct! To help you towards a solution, each step gives you a few hints and links to documentation. Do read those! Sometimes the hints also contain pointers to different alternative ways to solve a challenge.


 Scenario
============

The lab follows the following scenario: We want to measure certain readings from different environments on our factory shopfloor. As there are currently no sensors installed which would allow us to easily get their data for analysis, we decided to start with a quick prototype. For this we bought a few cheap [Raspberry Pi](https://en.wikipedia.org/wiki/Raspberry_Pi) boards with a [sensor-head](https://www.bosch-sensortec.com/bst/products/all_products/bme280) installed. The sensor can measure temperature, humidity and pressure. 

If our project proves some real value in getting and analysing this kind of data, we might later put some more ruggedized hardware in place.


Challenges
==================

## 1) Connect your Raspberry Pi to Azure

For this lab, we use a simulated version of the real Raspi board and the sensor-head. You can find it here: https://azure-samples.github.io/raspberry-pi-web-simulator/ The simulator is written in Javascript but you do not need knowledge of the programming language to use it. Although, if you do, feel free to extend the simulator later in the lab to send additional data etc.

The code is already all there, you do not need to do any more coding! The only thing it needs, is a connection string to an Azure IoT Hub. (Hint: The connection string goes into line 15).

So, first you probably need to provision an IoT Hub and then create a device identity for your simulated raspi.

### Goal of this challenge
You have data flowing from the simulated raspi into an Azure IoT Hub.

### Useful links
[What is the Azure IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/about-iot-hub)

[Create a new IoT Hub from the Azure Portal](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-through-portal)

[Understand device identites](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry)

*For this step there actually is a full step-by-step guide. Only refer to it if you are really stuck! https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started#register-a-new-device-in-the-iot-hub*

## 2) Visualize device telemetry in near-real time

To verify that our data is flowing into Azure and also to quickly see what the data looks like, we want to deploy a web-based frontend that can to used without much or any coding, schema definition etc.

### Goal of this challenge
Without the need to define a schema on your data, you have deployed a tool that allows you to see your data in a web-frontend and drill into it to see the raw telemetry events.

### Useful links
There is the Azure Time Series Insights service which could suite well for this purpose. But again, you are also free to try other tools as long as they fulfil the goal of the challenge.

[What is Time Series Insights](https://docs.microsoft.com/en-in/azure/time-series-insights/time-series-insights-update-overview)

[Connect IoT Hub to TSI](https://docs.microsoft.com/en-in/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)

## 3) Store data in cold storage

One key concept in working with IoT data is the differentiation between hot, warm and cold data layer. An architecture that builds on this principle is also known as a lamdba architecture.

There are various reasons for the cold layer but to name just two important ones here: 
1) Our processing pipeline might change at some point in the future. If we keep the historical data in its original, unprocessed form, we can do a replay of that data into our new pipeline.
2) Most analytics, and especially machine learning, happens in batch mode at some later point. For this we might not even know yet which data points exactly to keep. However, it might not be a very good idea from a cost perspective to load all the telemetry data in a fast but expensive storage such as an relational (SQL) database.

To start with this architecture, we now want to store our incoming data in an unprocessed form for long-term storage.

### Goal of this challenge
The unprocessed telemetry events are flowing into a storage that suits the needs of a cold storage. The data should be stored in a common text format that can be easily read by any tool (e.g. CSV or JSON).

### Useful links

[What is Azure Blob Storage](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction)

[What is Azure Stream Analytics(ASA)](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-introduction)

[Quick start with Stream Analytics](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-quick-create-portal)

[IoT Hub routing to storage](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-blob-storage)


## 4) Process and store data in the warm layer

Now that we solved our cold-storage requirements, let's take a look on where we store data for faster access than in a cold storage. This will be our warm layer. Later we want to build some visual dashboards based on this, so the storage that we choose here should be optimized for that.

To reduce the amount of data that we need to store here (think about our costs!), we want to pre-process the incoming data. Our senor is sending data in a high frequency (every 2 seconds). For now we don't need that data in such high resolution for our dashboard. Hence, we only want to store the average readings of the temperature and humidity in 30 second buckets (a.k.a windows).
### Goal of this challenge
Telemetry data that was averaged over 30 seconds is being transmitted into a storage service that can serve as a warm storage to which we will later connect a dashboard solution to.

### Useful links

[Introduction to Steam Analytics Windowing](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-window-functions)

[Understand outputs from ASA](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-define-outputs)

There are various services available in Azure which we could use as our warm layer. To name just two possible options here: Azure Cosmos DB or Azure SQL Database. While they will both work here, they are very different offerings. One is a No-SQL database, whereas the other one is a more traditional relational database.

[What is Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/introduction)

[What is Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-technical-overview)

[Create an Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-single-database-get-started)