```mermaid
graph LR
    subgraph "IoT/Edge Layer"
        Device["Sensor/Device"] <-->|MQTT Pub/Sub| Broker("MQTT Broker")
        Broker <-->|Pub/Sub| Gateway["IoT Gateway Service<br/>(Go)"]
        Gateway <-->|Cache/ACL| Redis[("Redis")]
        Gateway -->|Store Raw Data| Mongo[("MongoDB")]
    end

    %% Luồng dữ liệu Sensor đi lên
    Gateway ==>|Pub Sensor Data| NATS(("NATS Message Broker"))
    
    %% Luồng lệnh điều khiển đi xuống (Nét đứt)
    NATS -.->|Sub Cmd| Gateway

    subgraph "Backend Services Layer"
        NATS ==>|Sub Sensor Data| Farm["Farm Management Service<br/>(Go)"]
        Farm -.->|Pub Cmd| NATS
        Farm -->|Store Time-series| Influx[("InfluxDB")]
        
        NATS -->|Sub Events| Noti["Notification Service<br/>(NestJS)"]
        Noti -->|Send| Email["Email/SMS Provider"]
        
        NATS -->|Sub Events| Socket["Real-time Socket Service<br/>(NestJS)"]
        Socket -->|WebSocket| FE["Frontend App<br/>(Web/Mobile)"]
    end

    classDef go fill:#00ADD8,stroke:#333,stroke-width:2px,color:white;
    classDef nodejs fill:#E0234E,stroke:#333,stroke-width:2px,color:white;
    classDef db fill:#4DB33D,stroke:#333,stroke-width:2px,color:white;
    classDef nats fill:#27AAE1,stroke:#333,stroke-width:4px,color:white;
    
    class Gateway,Farm go;
    class Noti,Socket nodejs;
    class Redis,Mongo,Influx db;
    class NATS nats;
