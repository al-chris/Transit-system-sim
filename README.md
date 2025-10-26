## Goal: Simulate a city bus tracking system

## Components:

gps-publisher: publishes live bus location to Kafka

route-aggregator: consumes GPS updates and computes ETA for stops

alert-service: publishes alerts if a bus is delayed or off route

dashboard: Websocket frontend for live visualisation.

---

Stack

Core: FastAPI (or barebones Python microservices)
Broker: Kafka, RabbitMQ
Storage: Postgres
Docker Compose for orchestration

---

```
smart-transit-system/
│
├── services/
│   ├── gps_publisher/
│   │   ├── app/
│   │   │   ├── core/
│   │   │   │   ├── config.py              # Kafka config, update intervals, etc.
│   │   │   │   ├── logging_config.py
│   │   │   │   └── __init__.py
│   │   │   │
│   │   │   ├── models/
│   │   │   │   ├── bus_position.py        # Data model for GPS updates
│   │   │   │   └── __init__.py
│   │   │   │
│   │   │   ├── services/
│   │   │   │   ├── gps_generator.py       # Simulate bus location updates
│   │   │   │   ├── kafka_producer.py      # Publishes bus location events
│   │   │   │   └── __init__.py
│   │   │   │
│   │   │   ├── main.py                    # Entry point (publishes GPS updates)
│   │   │   └── __init__.py
│   │   │
│   │   ├── tests/
│   │   │   ├── test_gps_generator.py
│   │   │   └── test_producer.py
│   │   │
│   │   ├── Dockerfile
│   │   ├── requirements.txt
│   │   └── README.md
│   │
│   ├── route_aggregator/
│   │   ├── app/
│   │   │   ├── api/
│   │   │   │   ├── v1/
│   │   │   │   │   ├── endpoints/
│   │   │   │   │   │   ├── routes.py      # For debugging or manual testing
│   │   │   │   │   │   └── health.py
│   │   │   │   │   └── __init__.py
│   │   │   │   └── __init__.py
│   │   │   │
│   │   │   ├── core/config.py
│   │   │   ├── models/
│   │   │   │   ├── route_state.py
│   │   │   │   └── __init__.py
│   │   │   ├── schemas/
│   │   │   │   ├── gps_event.py
│   │   │   │   └── eta_update.py
│   │   │   ├── services/
│   │   │   │   ├── kafka_consumer.py      # Consumes GPS updates
│   │   │   │   ├── eta_calculator.py      # Computes ETA per route/stop
│   │   │   │   ├── kafka_producer.py      # Publishes ETA updates
│   │   │   │   └── __init__.py
│   │   │   ├── main.py                    # FastAPI + background Kafka consumer
│   │   │   └── events.py
│   │   ├── tests/
│   │   │   └── test_eta_calculator.py
│   │   ├── Dockerfile
│   │   ├── requirements.txt
│   │   └── README.md
│   │
│   ├── alert_service/
│   │   ├── app/
│   │   │   ├── core/config.py
│   │   │   ├── schemas/alert_schema.py
│   │   │   ├── services/
│   │   │   │   ├── kafka_consumer.py      # Listens for ETA updates
│   │   │   │   ├── alert_logic.py         # Determines if delays/off-route occur
│   │   │   │   ├── kafka_producer.py      # Publishes alerts
│   │   │   │   └── __init__.py
│   │   │   ├── main.py
│   │   │   └── events.py
│   │   ├── tests/
│   │   │   └── test_alert_logic.py
│   │   ├── Dockerfile
│   │   ├── requirements.txt
│   │   └── README.md
│   │
│   └── dashboard/
│       ├── app/
│       │   ├── core/config.py
│       │   ├── web/
│       │   │   ├── static/
│       │   │   │   ├── css/
│       │   │   │   └── js/
│       │   │   └── templates/
│       │   │       └── index.html
│       │   ├── api/
│       │   │   ├── websocket_manager.py   # Handles WS connections
│       │   │   ├── routes.py              # WebSocket endpoints
│       │   │   └── __init__.py
│       │   ├── services/
│       │   │   ├── kafka_consumer.py      # Consumes alerts + ETA events
│       │   │   └── broadcaster.py         # Pushes data to connected clients
│       │   ├── main.py                    # FastAPI app serving WebSocket + HTML
│       │   └── __init__.py
│       ├── Dockerfile
│       ├── requirements.txt
│       └── README.md
│
├── common/
│   ├── event_bus/
│   │   ├── kafka_client.py                # Shared Kafka producer/consumer setup
│   │   ├── topics.py                      # Topic name constants
│   │   └── __init__.py
│   ├── schemas/
│   │   ├── gps_event.py
│   │   ├── eta_update.py
│   │   ├── alert_event.py
│   │   └── __init__.py
│   ├── utils/
│   │   ├── logger.py
│   │   ├── time_utils.py
│   │   └── __init__.py
│   └── __init__.py
│
├── docker/
│   ├── docker-compose.yml                 # Spins up Kafka, all 4 services
│   ├── kafka/
│   │   ├── setup-topics.sh
│   │   └── docker-compose.kafka.yml
│   ├── postgres/
│   │   ├── Dockerfile
│   │   └── init.sql
│   └── README.md
│
├── scripts/
│   ├── run_all.sh
│   ├── setup_env.sh
│   ├── create_topics.py
│   ├── generate_fake_routes.py
│   └── test_all.sh
│
├── .env
├── .gitignore
├── .python-version
├── pyproject.toml
├── requirements.txt
└── README.md
```