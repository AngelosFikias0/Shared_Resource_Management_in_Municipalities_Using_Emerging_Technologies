# Smart City Integrated Management System (IMS)

## 📋 Περιγραφή Project

Πλήρες σύστημα διαχείρισης Smart City με real-time συλλογή και ανάλυση δεδομένων από IoT αισθητήρες, blockchain integration για data integrity, και modern web dashboard.

### 🏗️ Αρχιτεκτονική

```
IoT Sensors → Kafka → PySpark → PostgreSQL → Spring Boot API → Angular Dashboard
                                      ↓
                              Blockchain Layer
                                      ↓
                         Azure Monitor & Insights
```

---

## 🚀 Quick Start Guide

### Προαπαιτούμενα

- **Docker & Docker Compose** (v20+)
- **Python 3.9+**
- **Java 17+**
- **Node.js 18+** & npm
- **Maven 3.8+**
- **Azure CLI** (για production deployment)

---

## 📦 Βήμα 1: Εγκατάσταση Infrastructure

### 1.1 Εκκίνηση Docker Services

```bash
# Clone repository
git clone https://github.com/your-org/smart-city-ims.git
cd smart-city-ims

# Start infrastructure
docker-compose up -d

# Verify services
docker-compose ps
```

**Υπηρεσίες που ξεκινούν:**
- Zookeeper (port 2181)
- Kafka (port 9092)
- Kafka UI (port 8080) - http://localhost:8080
- PostgreSQL (port 5432)
- Redis (port 6379)

### 1.2 Έλεγχος Kafka

```bash
# Access Kafka UI
open http://localhost:8080

# Δημιουργία topic (auto-created αλλά μπορεί να γίνει manually)
docker exec -it smart-city-kafka kafka-topics --create \
  --bootstrap-server localhost:9092 \
  --topic smart-city-sensors \
  --partitions 3 \
  --replication-factor 1
```

---

## 📦 Βήμα 2: Εγκατάσταση Python Components

### 2.1 Setup Python Environment

```bash
# Δημιουργία virtual environment
python -m venv venv
source venv/bin/activate  # Linux/Mac
# ή venv\Scripts\activate  # Windows

# Εγκατάσταση dependencies
pip install -r requirements.txt

# Εγκατάσταση PySpark
pip install pyspark==3.5.0
```

### 2.2 Εκκίνηση IoT Sensor Simulator

```bash
# Ξεκινήστε το simulator
python iot_sensor_simulator.py

# Θα δείτε output όπως:
# 2025-10-22 10:15:30 - INFO - Έναρξη προσομοίωσης IoT αισθητήρων...
# 2025-10-22 10:15:30 - INFO - Αριθμός αισθητήρων: 7
# ✓ temperature: 22.45 °C
# ✓ air_quality: 48.20 AQI
```

### 2.3 Εκκίνηση PySpark Streaming

```bash
# Σε νέο terminal
python pyspark_streaming_processor.py

# Θα δείτε:
# INFO - Αρχικοποίηση Spark Session...
# INFO - Spark Session αρχικοποιήθηκε επιτυχώς
# INFO - Sensor readings stream ξεκίνησε
```

---

## 🔗 Βήμα 3: Blockchain Layer Setup

### 3.1 Local Blockchain (Ganache)

```bash
# Εγκατάσταση Ganache
npm install -g ganache

# Εκκίνηση local blockchain
ganache --port 8545 --accounts 10 --defaultBalanceEther 100

# Ή με Docker
docker run -d -p 8545:8545 trufflesuite/ganache-cli:latest
```

### 3.2 Εκκίνηση Blockchain Integration

```bash
# Test blockchain integration
python blockchain_integration.py

# Output:
# INFO - Σύνδεση με blockchain: 1337
# INFO - Account address: 0x...
# INFO - Balance: 100.0 ETH
# INFO - Recording sensor reading to blockchain...
# ✓ Successfully recorded: 0x...
```

---

## 🌱 Βήμα 4: Spring Boot Backend

### 4.1 Build Application

```bash
cd backend

# Build with Maven
mvn clean install

# Ή με Maven Wrapper
./mvnw clean install
```

### 4.2 Run Application

```bash
# Με Maven
mvn spring-boot:run

# Ή απευθείας το JAR
java -jar target/smart-city-ims-1.0.0.jar

# Application ξεκινά στο: http://localhost:8081
```

### 4.3 Έλεγχος REST APIs

```bash
# Health check
curl http://localhost:8081/actuator/health

# Latest readings
curl http://localhost:8081/api/v1/readings/latest

# Active alerts
curl http://localhost:8081/api/v1/alerts/active

# Anomalies (last 24h)
curl http://localhost:8081/api/v1/readings/anomalies?hours=24
```

---

## 🎨 Βήμα 5: Angular Frontend

### 5.1 Setup Frontend

```bash
cd frontend

# Εγκατάσταση dependencies
npm install

# Install Angular CLI (αν χρειάζεται)
npm install -g @angular/cli
```

### 5.2 Configuration

```typescript
// src/environments/environment.ts
export const environment = {
  production: false,
  apiUrl: 'http://localhost:8081'
};
```

### 5.3 Run Development Server

```bash
# Start development server
ng serve

# Ή με custom port
ng serve --port 4200

# Open browser: http://localhost:4200
```

### 5.4 Build for Production

```bash
# Production build
ng build --configuration production

# Output: dist/smart-city-frontend/
```

---

## 📊 Βήμα 6: Monitoring & Observability

### 6.1 Application Metrics

```bash
# Prometheus metrics
curl http://localhost:8081/actuator/prometheus

# Application info
curl http://localhost:8081/actuator/info

# Health details
curl http://localhost:8081/actuator/health
```

### 6.2 Database Monitoring

```bash
# Connect to PostgreSQL
docker exec -it smart-city-postgres psql -U smartcity_user -d smartcity

# Check sensor readings
SELECT COUNT(*) FROM sensor_readings;

# Check alerts
SELECT * FROM alerts WHERE resolved = false;

# View latest readings
SELECT * FROM latest_readings;

# City statistics
SELECT * FROM city_statistics;
```

### 6.3 Redis Monitoring

```bash
# Connect to Redis
docker exec -it smart-city-redis redis-cli -a RedisPass2024!

# Check cached data
KEYS *
GET sensorReadings::TEMP-001

# Monitor cache hits
INFO stats
```

---

## ☁️ Βήμα 7: Azure Deployment

### 7.1 Azure Prerequisites

```bash
# Login to Azure
az login

# Set subscription
az account set --subscription "Your-Subscription-ID"

# Create resource group
az group create \
  --name smart-city-rg \
  --location westeurope
```

### 7.2 Deploy Infrastructure (Bicep)

```bash
# Deploy with Bicep
az deployment group create \
  --resource-group smart-city-rg \
  --template-file infrastructure/main.bicep \
  --parameters environmentName=prod

# Get outputs
az deployment group show \
  --resource-group smart-city-rg \
  --name main \
  --query properties.outputs
```

### 7.3 Setup AKS Cluster

```bash
# Get AKS credentials
az aks get-credentials \
  --resource-group smart-city-rg \
  --name smart-city-aks

# Verify connection
kubectl get nodes

# Create namespace
kubectl create namespace smart-city

# Apply Kubernetes manifests
kubectl apply -f k8s/ -n smart-city
```

### 7.4 Deploy Application

```bash
# Build and push Docker image to ACR
az acr login --name smartcityacr

docker build -t smartcityacr.azurecr.io/smart-city-backend:latest .
docker push smartcityacr.azurecr.io/smart-city-backend:latest

# Deploy to AKS
kubectl apply -f k8s/deployment.yaml -n smart-city
kubectl apply -f k8s/service.yaml -n smart-city

# Check deployment
kubectl get pods -n smart-city
kubectl get services -n smart-city
```

### 7.5 Setup CI/CD with GitHub Actions

```bash
# Create Azure service principal
az ad sp create-for-rbac \
  --name "smart-city-github-actions" \
  --role contributor \
  --scopes /subscriptions/{subscription-id}/resourceGroups/smart-city-rg \
  --sdk-auth

# Add to GitHub Secrets:
# AZURE_CREDENTIALS = <output from above command>

# Push to main branch to trigger deployment
git add .
git commit -m "Initial deployment"
git push origin main
```

---

## 🧪 Testing

### Unit Tests

```bash
# Backend tests (Spring Boot)
cd backend
mvn test

# Frontend tests (Angular)
cd frontend
ng test

# Python tests
cd python
pytest tests/
```

### Integration Tests

```bash
# API integration tests
mvn verify

# End-to-end tests
cd frontend
ng e2e
```

### Load Testing

```bash
# Install k6
brew install k6  # Mac
# or download from https://k6.io

# Run load test
k6 run tests/load-test.js
```

---

## 📈 Performance Optimization

### Database Optimization

```sql
-- Create additional indexes
CREATE INDEX CONCURRENTLY idx_readings_timestamp 
ON sensor_readings(timestamp DESC);

-- Vacuum and analyze
VACUUM ANALYZE sensor_readings;

-- Check query performance
EXPLAIN ANALYZE 
SELECT * FROM sensor_readings 
WHERE sensor_id = 'TEMP-001' 
ORDER BY timestamp DESC LIMIT 100;
```

### Redis Caching Strategy

```java
// Configure cache TTL
@Cacheable(value = "sensorReadings", key = "#sensorId", 
           unless = "#result == null")
public List<SensorReading> getReadingsBySensor(String sensorId) {
    return readingRepository.findBySensorIdOrderByTimestampDesc(sensorId);
}
```

### API Response Time

- **Target:** < 200ms για cached queries
- **Target:** < 500ms για database queries
- **Target:** < 1s για complex aggregations

---

## 🔐 Security Best Practices

### 1. Environment Variables

```bash
# Never commit credentials!
# Use .env files locally

# .env.example
POSTGRES_PASSWORD=<change-me>
REDIS_PASSWORD=<change-me>
JWT_SECRET=<change-me>
AZURE_CLIENT_SECRET=<change-me>
```

### 2. Azure Key Vault Integration

```bash
# Store secrets in Key Vault
az keyvault secret set \
  --vault-name smart-city-kv \
  --name postgres-password \
  --value "SecurePassword123!"

# Reference in Kubernetes
kubectl create secret generic db-credentials \
  --from-literal=password="$(az keyvault secret show \
    --vault-name smart-city-kv \
    --name postgres-password \
    --query value -o tsv)"
```

### 3. Network Security

```bash
# Configure NSG rules
az network nsg rule create \
  --resource-group smart-city-rg \
  --nsg-name smart-city-nsg \
  --name AllowHTTPS \
  --priority 100 \
  --destination-port-ranges 443 \
  --access Allow \
  --protocol Tcp
```

---

## 📝 API Documentation

### Sensor Readings Endpoints

```http
GET /api/v1/readings/latest
GET /api/v1/readings/sensor/{sensorId}
GET /api/v1/readings/type/{type}?start=...&end=...
GET /api/v1/readings/anomalies?hours=24
GET /api/v1/readings/city/{city}?hours=24
```

### Alerts Endpoints

```http
GET /api/v1/alerts/active
GET /api/v1/alerts/sensor/{sensorId}
GET /api/v1/alerts/critical
GET /api/v1/alerts/count
PUT /api/v1/alerts/{id}/acknowledge
PUT /api/v1/alerts/{id}/resolve
```

### Example Responses

```json
// GET /api/v1/readings/latest
{
  "TEMP-001": 22.45,
  "AQI-001": 48.20,
  "PM25-001": 15.30
}

// GET /api/v1/alerts/active
[
  {
    "alertId": 1,
    "sensorId": "TEMP-001",
    "alertType": "threshold",
    "severity": "high",
    "message": "Temperature exceeded threshold",
    "actualValue": 42.5,
    "triggeredAt": "2025-10-22T10:30:00",
    "acknowledged": false,
    "resolved": false
  }
]
```

---

## 🐛 Troubleshooting

### Issue: Kafka Connection Failed

```bash
# Check Kafka status
docker logs smart-city-kafka

# Restart Kafka
docker-compose restart kafka

# Test connection
docker exec smart-city-kafka kafka-broker-api-versions \
  --bootstrap-server localhost:9092
```

### Issue: PostgreSQL Connection Refused

```bash
# Check PostgreSQL logs
docker logs smart-city-postgres

# Verify credentials
docker exec -it smart-city-postgres psql -U smartcity_user -d smartcity

# Reset password if needed
docker exec -it smart-city-postgres psql -U postgres
ALTER USER smartcity_user WITH PASSWORD 'NewPassword123!';
```

### Issue: PySpark Out of Memory

```bash
# Increase Spark memory
export SPARK_DRIVER_MEMORY=4g
export SPARK_EXECUTOR_MEMORY=4g

# Or in code:
spark = SparkSession.builder \
    .config("spark.driver.memory", "4g") \
    .config("spark.executor.memory", "4g") \
    .getOrCreate()
```

---

## 📚 Additional Resources

- **Kafka Documentation:** https://kafka.apache.org/documentation/
- **PySpark Guide:** https://spark.apache.org/docs/latest/api/python/
- **Spring Boot Docs:** https://spring.io/projects/spring-boot
- **Angular Guide:** https://angular.io/docs
- **Azure Kubernetes:** https://docs.microsoft.com/azure/aks/

---

## 👥 Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open Pull Request

---

## 📄 License

This project is licensed under the MIT License.

---

## 📞 Support

For issues and questions:
- GitHub Issues: https://github.com/your-org/smart-city-ims/issues
- Email: support@smartcity.gr
- Slack: #smart-city-dev

---

## ✅ Checklist Υλοποίησης

- [x] IoT Sensor Simulator με Kafka Producer
- [x] Docker Compose Infrastructure Setup
- [x] PostgreSQL Database Schema με TimescaleDB
- [x] PySpark Structured Streaming για επεξεργασία δεδομένων
- [x] Blockchain Integration Layer με Web3.py
- [x] Spring Boot REST API με Redis Caching
- [x] Angular Dashboard με Real-time Updates
- [x] Kubernetes Deployment Configuration
- [x] CI/CD Pipeline με GitHub Actions
- [x] Azure Infrastructure as Code (Bicep)
- [x] Monitoring με Azure Application Insights
- [x] Comprehensive Documentation

**Status:** ✅ Έτοιμο για Production Deployment 📋 Περιγραφή Project

Πλήρες σύστημα διαχείρισης Smart City με real-time συλλογή και ανάλυση δεδομένων από IoT αισθητήρες, blockchain integration για data integrity, και modern web dashboard.

### 🏗️ Αρχιτεκτονική

```
IoT Sensors → Kafka → PySpark → PostgreSQL → Spring Boot API → Angular Dashboard
                                      ↓
                              Blockchain Layer
                                      ↓
                         Azure Monitor & Insights
```

---

## 🚀 Quick Start Guide

### Προαπαιτούμενα

- **Docker & Docker Compose** (v20+)
- **Python 3.9+**
- **Java 17+**
- **Node.js 18+** & npm
- **Maven 3.8+**
- **Azure CLI** (για production deployment)

---

## 📦 Βήμα 1: Εγκατάσταση Infrastructure

### 1.1 Εκκίνηση Docker Services

```bash
# Clone repository
git clone https://github.com/your-org/smart-city-ims.git
cd smart-city-ims

# Start infrastructure
docker-compose up -d

# Verify services
docker-compose ps
```

**Υπηρεσίες που ξεκινούν:**
- Zookeeper (port 2181)
- Kafka (port 9092)
- Kafka UI (port 8080) - http://localhost:8080
- PostgreSQL (port 5432)
- Redis (port 6379)

### 1.2 Έλεγχος Kafka

```bash
# Access Kafka UI
open http://localhost:8080

# Δημιουργία topic (auto-created αλλά μπορεί να γίνει manually)
docker exec -it smart-city-kafka kafka-topics --create \
  --bootstrap-server localhost:9092 \
  --topic smart-city-sensors \
  --partitions 3 \
  --replication-factor 1
```

---

##
