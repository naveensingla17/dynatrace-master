# Dynatrace Lab 2 – Monitor the EasyTravel Application

A hands-on lab that deploys the **EasyTravel** sample application in Docker and monitors it with Dynatrace.

## Lab steps

| # | Step | Description |
|---|------|-------------|
| 1 | **EasyTravel application** | The sample app to monitor |
| 2 | **Deploy on a Docker container** | Run EasyTravel in Docker |
| 3 | **Install the agent** | Install Dynatrace OneAgent on the Docker host |
| 4 | **Vitals of the frontend** | Check the EasyTravel frontend in **Experience Vitals** |
| 5 | **Enable user tag** | Identify users in sessions |
| 6 | **Conversion goal** | Define a goal, such as a completed booking, and track it |

> **Note:** OneAgent only full monitors processes that start after it is installed. If you install it after the containers are running (step 3 after step 2), restart the EasyTravel containers.

## Checklist

- [x] Set up the EasyTravel application
- [x] Deploy EasyTravel on Docker
- [x] Install OneAgent on the Docker host
- [x] Check frontend vitals in Experience Vitals
- [ ] Enable user tagging
- [ ] Create and track a conversion goal
