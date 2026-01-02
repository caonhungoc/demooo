# demooo
graph LR
    subgraph "IoT/Edge Layer"
        Device[Sensor/Device] -->|MQTT| Broker(MQTT Broker)
        Broker -->|Sub| Gateway[IoT Gateway Service<br/>(Go)]
        Gateway <-->|Cache/ACL| Redis[(Redis)]
        Gateway -->|Store Raw Data| Mongo[(MongoDB)]
    end

    Gateway -->|Pub Events| NATS((NATS Message Broker))

    subgraph "Backend Services Layer"
        NATS -->|Sub| Farm[Farm Management Service<br/>(Go)]
        Farm -->|Store Time-series| Influx[(InfluxDB)]
        
        NATS -->|Sub| Noti[Notification Service<br/>(NestJS)]
        Noti -->|Send| Email[Email/SMS Provider]
        
        NATS -->|Sub| Socket[Real-time Socket Service<br/>(NestJS)]
        Socket -->|WebSocket| FE[Frontend App<br/>(Web/Mobile)]
    end

    classDef go fill:#00ADD8,stroke:#333,stroke-width:2px,color:white;
    classDef nodejs fill:#E0234E,stroke:#333,stroke-width:2px,color:white;
    classDef db fill:#4DB33D,stroke:#333,stroke-width:2px,color:white;
    classDef nats fill:#27AAE1,stroke:#333,stroke-width:4px,color:white;
    
    class Gateway,Farm go;
    class Noti,Socket nodejs;
    class Redis,Mongo,Influx db;
    class NATS nats;
