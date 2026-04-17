# 🚀 Airflow Data Platform (Celery + Docker + RabbitMQ)

Este proyecto implementa una arquitectura escalable de **Apache Airflow** usando:

* Celery Executor
* RabbitMQ como broker
* PostgreSQL como base de datos
* Docker + Docker Compose
* Separación entre **master (scheduler/api)** y **workers**

---

# 🧱 Arquitectura

```
                ┌────────────────────┐
                │      MASTER        │
                │--------------------│
                │ Airflow Scheduler  │
                │ Airflow API        │
                │ RabbitMQ           │
                └─────────┬──────────┘
                          │
                          │ (Celery Queue)
                          ▼
        ┌──────────────────────────────┐
        │           WORKERS            │
        │------------------------------│
        │ Airflow Celery Workers       │
        │ Ejecutan tareas (DAGs)       │
        └──────────────────────────────┘
```

---

# 📁 Estructura del proyecto

```
data-platform/
│
├── airflow/
│   ├── dags/
│   ├── logs/
│   └── plugins/
│
├── master/
│   └── docker-compose.yml
│
├── worker/
│   └── docker-compose.yml
│
├── .env.example
├── .gitignore
└── README.md
```

---

# ⚙️ Requisitos

* Docker
* Docker Compose
* Git
* Servidor Linux (recomendado: Ubuntu)

---

# 🔐 Variables de entorno

Crea tu archivo `.env` basado en `.env.example`.

Ejemplo:

```
AIRFLOW__CORE__EXECUTOR=CeleryExecutor

AIRFLOW__DATABASE__SQL_ALCHEMY_CONN=postgresql+psycopg2://user:password@host:5432/db

AIRFLOW__CELERY__BROKER_URL=amqp://user:password@rabbitmq:5672/vhost

AIRFLOW__CELERY__RESULT_BACKEND=db+postgresql://user:password@host:5432/db

AIRFLOW__CORE__FERNET_KEY=your_fernet_key
AIRFLOW__CORE__LOAD_EXAMPLES=False

AIRFLOW_ADMIN_USER=admin
AIRFLOW_ADMIN_PASSWORD=admin123
AIRFLOW_ADMIN_EMAIL=admin@example.com
AIRFLOW_ADMIN_FIRSTNAME=Admin
AIRFLOW_ADMIN_LASTNAME=User
AIRFLOW_ADMIN_ROLE=Admin
```

---

# 🚀 Levantar el MASTER

```bash
cd master
docker compose up -d
```

---

# 🔍 Verificar servicios

```bash
docker ps
```

Ver logs:

```bash
docker logs airflow_scheduler
docker logs airflow_api
```

---

# 🌐 Acceder a Airflow

```
http://TU_IP:8080
```

---

# 👤 Crear usuario manual (opcional)

```bash
docker exec -it airflow_api airflow users create \
  --username admin \
  --password admin123 \
  --firstname Admin \
  --lastname User \
  --role Admin \
  --email admin@example.com
```

---

# ⚡ Workers

En cada worker:

```bash
git clone <repo>
cd worker
docker compose up -d
```

---

# 🔄 Actualizar DAGs

```bash
git pull
```

Recomendado:

* Automatizar con cron
* O usar CI/CD

---

# 🧠 Buenas prácticas

* No subir `.env` al repositorio
* Usar SSH para acceso a Git
* Mantener DAGs sincronizados entre master y workers
* Limitar recursos en Docker (`mem_limit`, `cpus`)

---

# 🛠️ Troubleshooting

## Error: usuario no se crea

* Revisar variables `AIRFLOW_ADMIN_*`
* Ejecutar `docker compose config`

## Error Celery

* Verificar conexión a RabbitMQ
* Revisar `BROKER_URL`

## DAGs no aparecen

* Verificar volumen `/opt/airflow/dags`
* Revisar scheduler

---

# 📌 Roadmap

* [ ] Auto deploy con CI/CD
* [ ] Monitoreo (Prometheus + Grafana)
* [ ] Logging centralizado
* [ ] Auto scaling de workers

---

# 👨‍💻 Autor

Proyecto diseñado para arquitectura escalable de datos con Airflow.
