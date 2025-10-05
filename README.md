# delivery-config-server

## 🧭 About

The **Delivery Config Server** is a **Spring Cloud Config Server** that loads configuration properties from a **Git repository** (or optionally from the local filesystem).
Each microservice retrieves its configuration at startup — or refreshes it dynamically — through this server.

This architecture allows:

* Centralized management of all configuration files
* Environment-specific property management (`dev`, `test`, `prod`, etc.)
* Secure storage of secrets and credentials
* Dynamic reconfiguration with `@RefreshScope`

---

## 🚀 Features

* Acts as a centralized **Spring Cloud Config Server**
* Supports **Git-based configuration repository**
* Integration with **Spring Boot Actuator** for monitoring
* Supports multiple profiles (`dev`, `local`, `prod`)
* Works seamlessly with:

  * `gw-delivery` (API Gateway)
  * `ms-delivery-admin`
  * `ms-courier-order`
  * `ms-parcel-order`
  * `delivery-eureka-server`
* Easy setup with minimal configuration

---

## 🛠 Tech Stack

| Component                      | Description                   |
| ------------------------------ | ----------------------------- |
| **Java 11+**                   | Programming language          |
| **Spring Boot 2.6+**           | Framework                     |
| **Spring Cloud Config Server** | Centralized config management |
| **Gradle**                     | Build system                  |
| **Git / Local file store**     | Config source backend         |

---

## ⚙️ Configuration

### `application.yml`

An example base configuration for the Config Server:

```yaml
server:
  port: 8888

spring:
  application:
    name: delivery-config-server
  cloud:
    config:
      server:
        git:
          uri: https://github.com/your-org/delivery-config-repo.git
          search-paths: config
          default-label: main
      fail-fast: true

management:
  endpoints:
    web:
      exposure:
        include: health,info,env
```

> 💡 You can replace the Git URI with your private or organization repository URL containing the configuration files.

### Example File Structure in Git Repo

```
config-repo/
├── application.yml
├── gw-delivery.yml
├── ms-delivery-admin.yml
├── ms-courier-order.yml
├── ms-parcel-order.yml
└── delivery-eureka-server.yml
```

Each microservice fetches its config automatically based on its application name.

---

## 🧰 Build and Run

### Build

```bash
./gradlew clean build
```

### Run

```bash
./gradlew bootRun
```

or

```bash
java -jar build/libs/delivery-config-server-0.0.1-SNAPSHOT.jar
```

Once running, the Config Server will start on **port 8888** by default.

---

## 🧩 Verify Configuration Access

You can test if the server is working by calling the config endpoint:

### Example Request:

```bash
curl http://localhost:8888/ms-delivery-admin/default
```

### Example Response:

```json
{
  "name": "ms-delivery-admin",
  "profiles": ["default"],
  "label": "main",
  "propertySources": [
    {
      "name": "https://github.com/your-org/delivery-config-repo.git/config/ms-delivery-admin.yml",
      "source": {
        "server.port": "8081",
        "spring.datasource.url": "jdbc:postgresql://localhost:5432/delivery"
      }
    }
  ]
}
```

---

## 🌍 Integration with Other Services

Each microservice includes a `bootstrap.yml` (or `bootstrap.properties`) file to connect to this Config Server.

Example:

```yaml
spring:
  application:
    name: ms-delivery-admin
  cloud:
    config:
      uri: http://localhost:8888
      fail-fast: true
```

At startup, the service will fetch configuration from:

```
http://localhost:8888/ms-delivery-admin/default
```

---

## 🩺 Health Check

You can check if the Config Server is up and running via:

```
GET http://localhost:8888/actuator/health
```

Expected response:

```json
{
  "status": "UP"
}
```

---

## 🧠 Troubleshooting

| Issue                           | Cause                        | Solution                                          |
| ------------------------------- | ---------------------------- | ------------------------------------------------- |
| Config server not reachable     | Wrong URI or port            | Ensure `spring.cloud.config.uri` is correct       |
| Cannot fetch config             | Wrong Git URI or credentials | Check access rights to your config repo           |
| Microservice fails to start     | Missing `bootstrap.yml`      | Ensure it includes `spring.cloud.config.uri`      |
| Config not updating dynamically | Missing `@RefreshScope`      | Add annotation to beans that need runtime refresh |

---

## 🧪 Example Microservice Integration

In a dependent microservice like `gw-delivery`, you can test configuration retrieval with:

```bash
curl http://localhost:8888/gw-delivery/default
```

---

## 👥 Contributors

* **Eldar Novruzov** — Lead Developer
* Contributions and improvements are welcome! Please submit issues or PRs.

---

## 📄 License

This project is licensed under the **MIT License**.
See the [LICENSE](./LICENSE) file for more details.

---

Would you like me to **save this README** as a Markdown file (`README.md`) inside the extracted `delivery-config-server-master` folder so you can **download it directly**?
