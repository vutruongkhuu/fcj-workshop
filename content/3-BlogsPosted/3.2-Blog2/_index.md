---
title: "Blog 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# Physics on AWS: Optimizing wind turbine performance using OpenFAST in a digital twin

While reading the article “Physics on AWS: Optimizing wind turbine performance using OpenFAST in a digital twin”, the most notable point for me was the way AWS combines real-time IoT data with physical simulation to build a digital twin for wind turbines.

Instead of merely monitoring devices using sensor data, this architecture also uses OpenFAST — an open-source wind turbine simulation tool from the National Renewable Energy Laboratory (NREL) — to run simulations on demand. The simulation results are compared with real operating data, helping detect anomalies and improve operational performance of the wind farm.

**Why is a digital twin needed?**

- In this article, a digital twin is understood as an architecture that connects physical wind turbines to AWS Cloud through IoT devices to monitor performance while also adding knowledge from on-demand simulations.
- The goal of the digital twin is to compare real turbine data with simulation results from OpenFAST. When there is deviation or anomaly, the system can help evaluate the cause, examine the impact of controller changes, and support operational adjustment decisions.
- OpenFAST is an aeroelastic simulator commonly used in wind turbine design and certification according to standards such as IEC 61400-1 and IEC 61400-3. However, in real operation, environmental conditions can change in ways not fully captured during the initial design phase. For example, higher-than-expected turbulence intensity can accelerate equipment degradation.
- Therefore, reusing the same simulation tool used in design to test controller changes before deploying them to real devices is a valuable approach. It helps operators assess whether a change could reduce accumulated damage before applying it to the operating system.

## Overview of the digital twin architecture

The architecture in the original article combines real turbine data with simulation results to support controller software updates. The main processing flow consists of the following steps:

1. Wind turbine sensors are connected to AWS Cloud through AWS IoT Core.
2. An IoT rule forwards sensor data into Amazon Timestream, a database optimized for time-series data.
3. An AWS Lambda runs on a schedule to query data in Timestream and detect anomalies in the time series.
4. When an anomaly is detected, Amazon SNS sends a notification, and the preprocessor Lambda prepares input files for the OpenFAST simulation.
5. Simulations are run on demand. The latest OpenFAST version is pulled from Amazon Elastic Container Registry (Amazon ECR).
6. The simulation is dispatched through a RESTful API and runs on AWS Fargate.
7. Simulation results are uploaded to Amazon S3.
8. Time-series data from the simulation is processed by AWS Lambda. At this step, the system decides whether to update the controller software based on the detected anomalies.
9. The post-processor Lambda initiates the controller software update process for the wind turbine. Update information is sent back to the turbine through AWS IoT Core.
10. The results are visualized using Amazon Managed Grafana.

An example of an anomaly in the detection step is a controller overspeed alarm. A simple rule-based anomaly detection method can be used to detect values exceeding a threshold. In addition, more advanced anomaly detection methods using machine learning can be applied through Amazon SageMaker.

## Main components of the architecture

The digital twin architecture can be grouped into four important areas:

- Event-driven architecture workflow.
- Executing OpenFAST through RESTful APIs and containers.
- Running multiple OpenFAST simulations concurrently using Application Load Balancer and Amazon EC2 Auto Scaling.
- Visualizing physical assets and simulation results using Amazon Managed Grafana.

### Event-driven architecture

- Event-driven architecture allows services to communicate asynchronously and reduces direct dependency between them. The workflow is triggered automatically when an event occurs. The event may be an active warning from a turbine or an OpenFAST output file uploaded to Amazon S3.
- One advantage of this approach is that the number of turbines being monitored can grow from one to 100 or more without manually provisioning resources for each turbine.
- AWS Lambda supports rapid scaling to increase the number of OpenFAST simulation tasks. Meanwhile, AWS Fargate runs containers without requiring management of the underlying compute infrastructure.
- From an operational perspective, the event-driven architecture enables the engineering team to automate steps such as preparing input files, dispatching simulations, and post-processing results without manually provisioning resources.

### Containerization for OpenFAST

- OpenFAST is packaged as a container. Containerization makes it possible to deploy the application together with the libraries required to run simulations.
- In this architecture, Docker is used to create a container image containing the OpenFAST executable. FastAPI is also included in the container so the simulation can be dispatched through a RESTful API.

### RESTful API for OpenFAST with main command groups

- PUT /execute: run a simulation with initial conditions.
- POST /upload_to_s3: upload simulation results to Amazon S3.
- GET /status: check the simulation status.
- DELETE /simulation: delete simulation results.

This packaging approach helps engineering teams use an OpenFAST version suited to the wind turbine currently in operation without manually reconfiguring the runtime environment.

### Load balancing and auto scaling

The architecture uses Amazon EC2 Auto Scaling and Application Load Balancer (ALB) to handle changing simulation demand and allow multiple OpenFAST simulations to run at the same time.

EC2 Auto Scaling adjusts the number of OpenFAST containers based on the number of simulation requests. When demand increases, the system can scale out; when demand decreases, it can scale in to avoid idle resources.

When combined with ALB, simulation requests are distributed evenly across the OpenFAST containers. This design helps maintain performance and increase availability when many simulations are running simultaneously.

### Data visualization

- Amazon Timestream is used to collect and store real-time metrics from physical wind turbines. These metrics may include rotor speed, generator power, generator speed, generator torque, or wind turbine control system alarms.
- One notable feature of Timestream is scheduled queries. This feature can run periodic tasks automatically, such as calculating the average wind speed over 10 minutes or monitoring turbines with controller alarms.
- As a result, operations teams can view detailed data in real time or query historical data using SQL.
- Amazon Managed Grafana is connected to operating data in Timestream and OpenFAST simulation results stored in Amazon S3. This makes it possible to compare simulation data with real data and observe how the simulated components respond.
- For engineering teams, Grafana acts as an observability window: they can see how simulations respond to controller changes and then check whether the physical turbines respond as expected.

## Meaning of the architecture

The important point of this architecture is that it goes beyond simply monitoring wind turbines. The system creates a loop between:

- sensor data from real devices,
- anomaly detection,
- physical simulation using OpenFAST,
- analysis of simulation results,
- and controller software updates if needed.

This approach helps operators make decisions based on near-real-time data while also using physical models to improve analytical accuracy.

Instead of only reacting after damage has already occurred, the digital twin can help identify the root cause of alerts and apply operating strategies to reduce excessive wear before the damage becomes severe.

![Illustration](/images/3.jpg)

Reference article: https://aws.amazon.com/vi/blogs/architecture/physics-on-aws-optimizing-wind-turbine-performance-using-openfast-in-a-digital-twin/