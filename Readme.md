# O'Reilly Architecture Katas

This is a submission of Ronins team for O'Reilly [Architecture Katas](https://learning.oreilly.com/live-events/architectural-katas/0636920097101/0636920097100/).

Team Members:  
Arthur Shvetsov  
Dmytro Andreyev  
Gregory Hasyn  
Tetiana Husieva  
Vitalii Lyseiko  

## Contents
- [Introduction](#introduction)  
- [Architectural Drivers](#architectural-drivers)
    - [Functional Requirements](#functional-requirements)
    - [Quality Attribute Requirements](#quality-attribute-requirements)
    - [Architectural Assumptions](#architectural-assumptions)
- [Architecture](#architecture) 
    - [Event Storming](#event-storming)
    - [High-level architecture](#high-level-architecture)
- [Architecture Decision Records](#architecture-decision-records)


## Introduction

The Road Warrior is a new startup that aims to organize in one place all travel reservations, be it flights, hotels, or rental cars, effortlessly accessible at the fingertips. Whether the user prefers the convenience of a web browser or the mobility of a smartphone, the initiative ensures that travel plans are seamlessly grouped, effortlessly synchronized, and nicely presented on a dashboard. The Road Warrior integrates with existing travel systems, ensuring a hassle-free and streamlined experience for all travelers.  
The initiative also seamlessly integrates with standard social media platforms and offers the option to selectively share trip details with specific individuals. The application offers comprehensive end-of-year summary reports that provide valuable insights into travel usage.

## Architectural Drivers

### Functional Requirements

This section describes the functional requirements that will serve as the foundation of our platform. These requirements not only address the integration and interaction needs with external travel systems but also ensure an intuitive user experience, scalability, and adaptability in an ever-evolving travel landscape.

| Id | Description |
| ---------- | ---------- |
| FR1.1 | The system should be able to support a minimum of 2 million active users per week |
| FR1.2 | The system should accommodate and manage up to 15 million user accounts |
| FR2.1 | The system should automatically poll user email accounts for travel-related emails |
| FR2.2 | Provide the functionality to filter and whitelist certain email types or sources |
| FR3.1 | Integrate with the agency's existing airline, hotel, and car rental interface systems |
| FR3.2 | Retrieve and update travel details such as delays, cancellations, updates, gate changes, etc., from the integrated external systems |
| FR3.3 | Ensure travel details from external systems are updated in the app dashboard within a maximum of 5 minutes from their generation |
| FR4.1 | Users should be able to add new reservations to the system, update and delete existing |
| FR5.1 | Display reservations on the dashboard, organized/grouped by trips |
| FR5.2 | Automatically remove trips from the dashboard once those are completed |
| FR6.1 | Provide functionality for users to share their trip information on standard social media platforms |
| FR7.1 | Enable users to allow specific individuals to view their trip details |
| FR7.2 | Offer a rich user interface that is consistent across both web and mobile platforms |
| FR8.1 | Generate end-of-year summary reports for users. |
| FR8.2 | Provide metrics in the reports that cover different aspects of users' travel behavior and patterns |
| FR9.1 | Collect analytical data related to users' trips. |
| FR9.2 | Analyze travel trends, popular locations, preferred airlines and hotels, frequency of cancellations and updates, etc. |
| FR10.1 | Integrate seamlessly with systems like SABRE and APOLLO. |
| FR10.2 | Facilitate integration with a preferred travel agency to assist users with issues (quick problem resolution) |
| FR11.1 | Ensure the system works and is accessible internationally, accommodating different time zones and languages as necessary |

### Quality Attribute Requirements

This section describes the Quality Attribute Requirements that have a critical impact on the software solution's overall architecture. These requirements are of particular importance as they drive architectural decisions, influence the selection of design patterns, and determine the system's ability to meet its desired quality attributes.

| Id | Quality Attribute | Description |
| --------- | --------- | --------- |
| QA1 | Scalability | The system must be able to support 2 million active users per a week |
| QA2 | Scalability | The system must be able to store 15 million user accounts |
| QA3 | Performance | Updates must be presented in the application within 5 minutes of generation by the source |
| QA4 | Performance | The system must meet response time from the web of 800 ms and mobile first-contentful paint of under 1.4 sec |
| QA5 | Integrability | The system must be able to easily support new travel agency's interfaces integration |
| QA6 | Availability | The system must have 99.99% uptime, so the users can access the system at all times (max 5 minutes per month of unplanned downtime) |
| QA7 | Usability | The system must have richest user interface possible across all deployment platforms |
| QA8 | Usability | The system must work internationally, so be localized and globalized |
| QA9 | Security | Webhook and email processor must filter out malformed messages  |

### Architectural Assumptions

This section examines the Assumptions made during the design and development of the software solution's architecture. These assumptions represent beliefs or conditions that are considered true or likely to hold but without absolute certainty.

| Id | Description |
| ---------- | ---------- |
| A1 | Agency's APIs will provide rich REST / SOAP API for reservations management |
| A2 | Agency's APIs will provide webhooks to push updates about reservations |
| A3 | Agency's APIs won't be ratelimit Road Warrior platform requests |
| A4 | Agency's APIs will provide endpoints / services to support “help me!” functionality via WhatsApp / Viber bots |

## Architecture

### Event Storming
![event-storming](/Diagrams/event-storming.png)  
*Figure 1 Event Storming*

### High-level architecture
![high-level-architecture](/Diagrams/high-level-architecture.png)  
*Figure 2 High-level architecture*

## Architecture Decision Records

### **2023-09-15 ADR01 Use Microservice Architecture Style**
**Status**  
Accepted

**Context**  
Microservice architectures are common nowadays, but just using them because they're popular isn't enough. Below, we'll talk about the pros and cons and explain why we think a microservice architecture is the right choice for The Road Warrior.

**Decision**  
We're opting for the microservice architecture style for The Road Warrior backend platform.

**Consequences**  
- *Scalability*: Microservices allow us to independently scale individual components, ensuring efficient resource utilization and responsiveness to varying workloads.
- *Modularity*: Microservices promote a modular approach, enabling development teams to work on specific services independently.
- *Flexibility*: Each Microservice can be developed and deployed using technologies best suited to its specific requirements.
- *Decentralization*: Decentralized development and deployment reduce single points of failure and minimize the impact of service outages.
- *Complexity*: Managing a system composed of multiple Microservices introduces complexities related to inter-service communication, versioning, and monitoring.
- *Increased Development Effort*: Coordinating the development of multiple Microservices requires careful planning and coordination. 

In adopting the Microservices Architecture Style, we aim to achieve better scalability, flexibility, and modularity

### **2023-09-15 ADR02 Making certain parts of our architecture event-driven**
**Status**  
Accepted

**Context**  
Microservices can communicate either synchronously (with blocking) or asynchronously (without blocking). The Road Warrior platform needs to implement multiple processes involving both sequential and parallel steps.

**Decision**  
We employ asynchronous communication between services when the calling service can continue its operation without waiting for the outcome of the called service. Typically, this occurs when the handling of the event in progress can be deferred without disrupting the workflow or impacting the user experience.

**Consequences**  
The solution's architecture should include a mechanism for message queuing. Although this can be managed by event-driven services, we highly advise integrating a dedicated event-streaming platform like Kafka or Azure Event Hub. This approach fosters service decoupling, leading to improved system maintainability and scalability. It also establishes a robust foundation for potential future enhancements that might rely on event-driven communication.

- *Real-time Responsiveness*: it allows our system to respond to events and updates in real-time, improving the user experience.
- *Loose Coupling*: Microservices can communicate asynchronously through events, reducing tight coupling and making the system more flexible and maintainable.
- *Scalability*: it can be easily scaled horizontally, ensuring that the system can handle increased workloads efficiently.
- *Event Handling*: it promotes better event handling, allowing us to react to and process events as they occur.
- *Complexity*: it introduces a level of complexity in event management and event-driven communication that requires careful planning and design.

### **2023-09-15 ADR03 Adopting the Backend-for-Frontend Pattern**
**Status**  
Accepted

**Context**  
The Road Warrior platform involves a complex system comprising multiple Microservices that communicate synchronously through REST and asynchronously through Message Queues. This system serves a diverse user base, each interacting with our application through various frontends, including a mobile app, a web app, and third-party API clients in future. To meet our objectives, we must address the need for efficient communication between our Microservices and frontends.

**Decision**  
We have decided to implement the Backend-for-Frontend (BFF) Pattern as part of our system architecture. This entails creating dedicated BFFs for each frontend application.

**Consequences**  
- Frontend applications will no longer communicate directly with Microservices, reducing complexity and potential issues.
- Frontend developers will take responsibility for their respective BFFs, allowing them to tailor the backend to the specific needs of their frontend application.
- Uneven workloads from different frontends can be independently scaled, optimizing system performance.
- BFFs can be kept lightweight, streamlining development, deployment, and maintenance efforts. This choice aligns with our non-functional requirement for Feasibility.

By adopting the BFF pattern, we aim to enhance the efficiency, maintainability, and flexibility of our system, ensuring a better experience for our users across all frontends.

### **2023-09-15 ADR04 Adopting the CQRS Pattern**
**Status**  
Accepted

**Context**  
To enhance flexibility and enable independent configuration for scalability, we have opted for the CQRS (Command Query Responsibility Segregation) pattern. This pattern allows us to efficiently manage both write and read operations, each with distinct throughput and performance characteristics.

**Decision**  
We propose to adopt the CQRS architectural pattern. This approach involves separating the command operations (actions that modify data) from the query operations (actions that retrieve data), allowing for independent scalability, optimization, and maintenance of each operation type.

**Consequences**  
- *Optimized Read/Write Operations*: By segregating command and query operations, each can be optimized independently. This might involve different storage mechanisms, data structures, or even different database types for reads and writes.
- *Scalability*: As the demand for either command or query operations surges, CQRS allows for the independent scaling of each operation type, ensuring that the system remains responsive.
- *Flexibility in Data Storage*: Given that reads and writes are separated, there's the flexibility to employ specialized storage solutions for either operation, like employing fast-write databases for command operations and optimized-read databases for query operations.
- *Enhanced Security*: Commands and queries can have different security models. For instance, certain operations might be restricted to specific user roles, ensuring a more granular security approach.
- *Complexity*: Introducing CQRS can increase the overall complexity of the system architecture. It necessitates maintaining separate models for reading and writing and might require additional infrastructure.
- *Eventual Consistency*: Given the segregated nature of the architecture, there might be a delay between a command being executed and the updated data being available for queries, leading to eventual consistency.
- *Cost Implications*: Maintaining separate infrastructures for command and query operations might have associated cost implications, both in terms of initial setup and ongoing operational costs.

By embracing the CQRS architectural pattern, the Road Warrior platform is strategically positioned to handle the intricacies of high-volume command and query operations, ensuring optimized performance, scalability, and flexibility.

### **2023-09-15 ADR05 Adopting Cloud Provider Services over On-Premise Infrastructure**
**Status**  
Accepted

**Context**  
Our project involves the development of a complex system. We need to make a critical decision regarding our infrastructure: whether to rely on cloud provider services or maintain an on-premise infrastructure. This decision is vital due to its implications on scalability, cost-effectiveness, and operational efficiency.

**Decision**  
We propose adopting Cloud Provider Services as the infrastructure solution.

**Consequences**  
- *Scalability*: offers elastic scalability, allowing us to effortlessly adjust resources to meet varying workloads and growth demands.
- *Cost Efficiency*: often provides a pay-as-you-go model, minimizing upfront costs and offering cost savings through resource optimization.
- *Disaster Recovery*: typically offers robust disaster recovery options, ensuring data backup, redundancy, and high availability.
- *Global Reach*: has data centers distributed worldwide, enabling us to serve global users efficiently.
- *Security*: Leading cloud providers invest heavily in security measures, ensuring data protection and compliance with industry standards.
- *Flexibility*: provides a wide range of tools and services that can be easily integrated into our project architecture.
- *Maintenance Offload*: handles infrastructure maintenance, reducing the burden on our internal IT team.
- *Rapid Deployment*: allows for quick resource provisioning, enabling faster development and deployment cycles.
- *Accessibility*: can be accessed remotely, facilitating collaboration among geographically dispersed teams.
- *Up-to-Date Technology*: regularly updates their services with the latest technology, reducing the risk of obsolescence.

By choosing a cloud provider, we can harness these advantages to enhance our project's scalability, efficiency, and cost-effectiveness.

### **2023-09-15 ADR06 Implement Native iOS/Android Application and Separate SPA for Frontend**
**Status**  
Accepted

**Context**  
The Road Warrior platform aims to provide an unparalleled travel management experience for its users. With the diverse user base accessing the platform from multiple devices and varying network conditions, especially considering the 2 million active users per week, it is critical to offer a consistent, responsive, and engaging user experience. Moreover, as the demand for richer user interfaces and more interactive functionalities increases, we need to decide on the most efficient approach to our frontend development.

**Decision**  
We propose the implementation of native applications for iOS and Android to optimize the user experience for mobile users. Simultaneously, we will develop a separate Single Page Application (SPA) for the web frontend.

**Consequences**  
- *Optimized User Experience*: Native applications for iOS and Android will offer a more seamless, responsive, and tailored experience for mobile users. Native apps can tap directly into device features, offering faster performance and smoother animations.
- *Direct Access to Device Features*: Native apps allow for deeper integration with device capabilities, such as camera, GPS, and notifications, enhancing user interaction.
- *Offline Accessibility*: Native apps often provide offline mode, ensuring users can access their trip information even without an internet connection.
- *SPA Benefits*: Implementing a Single Page Application for the web frontend ensures a fast and fluid user experience, with reduced page reloads and instant feedback. It also provides a more modular and maintainable codebase.
- *Consistent Experience across Platforms*: While the mobile applications cater to device-specific features and nuances, the SPA ensures that users on desktops or other devices still receive a top-tier experience.
- *Scalability*: Both native apps and SPAs are scalable solutions. As our user base grows, we can efficiently handle increased loads and introduce more features.
- *Development Overhead*: With separate applications for iOS, Android, and the web, there's an increase in development and maintenance effort. However, this is offset by the superior user experience and performance benefits.

By opting for native applications and a separate SPA, we position the Road Warrior platform to provide an exceptional user experience across all devices, leveraging the strengths of each approach for optimal results.


### **2023-09-15 ADR07 Implementing a Data Lake for Analytics and Reporting**
**Status**  
Accepted

**Context**  
The Road Warrior platform generates vast amounts of data from its 15 million user accounts, encompassing travel trends, preferences, cancellations, updates, and other behaviors. Given this data influx, combined with the need for advanced analytics and reporting capabilities, there's an imperative to choose an optimal data storage and processing solution that can handle this volume, variety, and velocity of data while ensuring flexibility for future analytical endeavors.

**Decision**  
We propose the implementation of a Data Lake architecture to centralize our analytics and reporting requirements.

**Consequences**  
- *Scalability*: Data lakes are inherently scalable, allowing us to store petabytes of data without the traditional limitations posed by relational databases. As our user base and data volume grow, the Data Lake will adapt without performance degradation.
- *Variety of Data*: Data lakes can store structured, semi-structured, and unstructured data. This ensures that we can capture data in its raw form, from diverse sources like user logs, system metrics, and even unstructured feedback.
- *Advanced Analytics*: The Data Lake, in combination with analytical tools and frameworks, will enable sophisticated analytics like machine learning, trend analysis, and predictive modeling. This will provide insights into travel patterns, user preferences, and emerging trends.
- *Real-time Processing*: Data lakes support real-time data processing, enabling near-instant updates to the dashboard and generating real-time reports, crucial for reflecting travel updates and changes within the stipulated 5-minute window.
- *Cost Efficiency*: Data lakes, especially when implemented on cloud platforms, often come with a cost-effective storage solution. This allows us to store large volumes of data without incurring prohibitive costs.
- *Integration with Reporting Tools*: Data lakes seamlessly integrate with modern Business Intelligence and reporting tools, enabling us to generate the end-of-year summary reports with a wide range of metrics.
- *Flexibility*: As analytical requirements evolve, data lakes allow for schema-on-read, ensuring that we aren't locked into a fixed schema and can adapt our analytical strategies without major overhauls.
- *Security Concerns*: While data lakes offer robust data storage capabilities, they also necessitate stringent security measures. Proper access controls, data encryption, and regular audits will be paramount.

By choosing a Data Lake for our analytical and reporting backbone, the Road Warrior platform is positioned to harness the full potential of its vast data assets, driving insights and delivering value to its users.

### **2023-09-15 ADR08 Implementing Localization and Globalization Strategies**
**Status**  
Accepted

**Context**  
The Road Warrior platform aims to be an internationally accessible travel management solution, catering to users across diverse linguistic and cultural landscapes. With its intent to work seamlessly across borders, the system's design must ensure content and user experience are culturally appropriate and linguistically accurate. The decision surrounding localization and globalization is pivotal to achieving a truly global reach and providing an inclusive user experience.

**Decision**  
We propose a two-pronged approach: implementing Localization to tailor the application's content and UI to suit local languages and cultural nuances, and integrating Globalization to ensure the system's underlying architecture supports international functionalities and standards.

**Consequences**  
- *Cultural Appropriateness*: Localization ensures that all content, from text to visuals, is culturally relevant and sensitive, preventing unintended offenses or misunderstandings.
- *Language Support*: With Localization, the platform will support multiple languages, allowing users to interact with the system in their native or preferred language.
- *Date, Time, and Numeric Formats*: Globalization ensures standardized representation of dates, times, numbers, and currencies, accounting for regional differences.
- *Time Zone Handling*: Given the platform's emphasis on timely travel updates, effective handling of time zones through Globalization is crucial to display accurate timings regardless of user location.
- *Right-to-left Language Support*: Some languages (e.g., Arabic, Hebrew) require right-to-left display. Implementing L10n will ensure these languages are displayed correctly.
- *Expandability*: As the platform grows and enters new markets, adding support for additional languages and regions becomes simpler with an established L10n and G11n infrastructure.
- *Regulatory Compliance*: In some regions, there might be legal requirements concerning data privacy, content display, or other factors. A combined L10n and G11n approach will facilitate adherence to these regulations.
- *Performance Implications*: While localization and globalization enhance user experience, they may introduce additional layers of complexity, necessitating optimized coding practices and efficient resource management.

By adopting Localization and Globalization, the Road Warrior platform ensures a tailored and universally accessible user experience, paving the way for true international success.

### **2023-09-15 ADR09 Establishing a Notification Center for Push Notifications, SMS, and Webhooks**
**Status**  
Accepted

**Context**  
In today's digital age, users of the Road Warrior platform anticipate instantaneous and tailored alerts regarding their travel. Whether it's about gate changes, flight delays, or other pertinent updates, users must be informed swiftly and efficiently across a multitude of channels. As the platform's objective is to ensure travelers have seamless experiences, the implementation of a multifaceted notification mechanism becomes essential.

**Decision**  
We propose to introduce a comprehensive Notification Center that centralizes the management and delivery of Push Notifications, SMS alerts, and Webhook integrations, allowing the platform to communicate effectively with users and third-party systems.

**Consequences**  
- *Consistent User Communication*: With the Notification Center in place, users will receive coherent and timely alerts across all devices and platforms, enhancing the overall user experience.
- *Immediate Outreach with SMS*: Crucial updates, especially ones needing immediate attention, can be delivered through SMS, ensuring users are informed even when they're offline or not using the application.
- *Real-time App Updates with Push Notifications*: Push notifications will provide users with real-time updates directly on their devices, prompting them to check the application for detailed information, increasing app engagement.
- *Seamless Integration via Webhooks*: Webhooks pave the way for the platform to integrate in real-time with external services such as travel agencies, airlines, or other relevant partners. This ensures the platform always has up-to-the-minute data.
- *User Customization*: The Notification Center will empower users to customize their notification preferences, choosing which updates they'd like to receive and through which channels.
- *Improved Engagement*: With the diverse notification methods in play, user interaction with the platform is likely to increase, enhancing retention and satisfaction.
- *Data Security*: Given that the Notification Center will manage a plethora of user-sensitive information, it's imperative to have stringent security measures to safeguard user data, especially for channels like SMS.
- *Infrastructure Scalability*: As the platform expands its user base, the Notification Center must be designed to scale seamlessly, handling an ever-increasing volume of notifications efficiently.
- *Cost Management*: SMS notifications come with associated costs. It's crucial to optimize the frequency and nature of SMS alerts to balance user experience and operational costs.

By integrating a Notification Center designed for push notifications, SMS, and webhooks, the Road Warrior platform positions itself to deliver timely, relevant, and diverse alerts, ensuring users remain well-informed and engaged.