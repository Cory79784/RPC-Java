# RPC-Java

## Startup Guide

### 1. Install and Start ZooKeeper

Installation reference:

[Zookeeper Installation on Windows (Detailed Step-by-Step Guide, Ensures Success) - CSDN Blog](https://blog.csdn.net/tttzzzqqq2018/article/details/132093374?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522172149339116800211548359%2522%252C%2522scm%2522%253A%252220140713.130102334)

### 2. Run Instructions

* For the first four versions:

  * Run `TestServer` under the Server package.
  * Then run `TestClient` under the Client package.

* For later versions:

  * Run `ProviderTest` first.
  * Then run `ConsumerTest`.

### 3. Start via Command Line on Windows

Mac/Linux environments and common issues are detailed in the documentation on the Knowledge Planet platform.

#### Check Java Version

```bash
java -version
```

Ensure the output shows JDK 17 or higher. If not:

* Set `JAVA_HOME` to the JDK 17 installation directory (e.g., `D:\software\JDK17`).
* Add the JDK `bin` path to your system's `PATH` variable.

#### Check Maven Version

```bash
mvn -v
```

Ensure Maven version is 3.6 or above.

#### Check ZooKeeper Service (Windows)

1. Download and extract ZooKeeper.
2. Rename `zoo_sample.cfg` to `zoo.cfg`.
3. Start ZooKeeper:

```bash
bin\zkServer.cmd
```

## Project Build

### 1. Clean and Build

Navigate to the project directory:

```bash
cd D:\java_stduy\version5
mvn clean install -DskipTests
```

### 2. Verify Build Artifacts

Ensure the following JARs are generated:

* `krpc-api/target/krpc-api-1.0-SNAPSHOT.jar`
* `krpc-common/target/krpc-common-1.0-SNAPSHOT.jar`
* `krpc-core/target/krpc-core-1.0-SNAPSHOT.jar`
* `krpc-provider/target/krpc-provider-1.0-SNAPSHOT.jar`
* `krpc-consumer/target/krpc-consumer-1.0-SNAPSHOT.jar`

## Start Services

### 1. Start Service Provider (Provider)

```bash
cd D:\java_stduy\version5\krpc-provider
D:\software\JDK17\bin\java -cp "target\krpc-provider-1.0-SNAPSHOT.jar;target\lib\*" com.kama.provider.ProviderTest
```

### 2. Start Service Consumer (Consumer)

```bash
cd D:\java_stduy\version5\krpc-consumer
D:\software\JDK17\bin\java -cp "target\krpc-consumer-1.0-SNAPSHOT.jar;target\lib\*" com.kama.consumer.ConsumerTest
```

# RPC Concepts

## Definition

1. RPC (Remote Procedure Call Protocol) is a protocol for requesting services from remote systems over the network.
2. It abstracts the underlying network details.
3. It allows invoking methods on remote systems as if they were local.

## Popular RPC Technologies and Frameworks

* **Application-Level Frameworks:** Alibaba Dubbo/Dubbox, Google gRPC, Spring Boot/Spring Cloud
* **Communication Protocols:** RMI, Socket, SOAP (HTTP + XML), REST (HTTP + JSON)
* **Communication Libraries:** MINA, Netty

## Why Use RPC?

1. **Service Modularization:** Facilitates cross-platform communication in microservices.
2. **Distributed System Architecture:** Enables services on different machines to interact.
3. **Service Reusability:** Shared capabilities can be reused by multiple systems.
4. **System Integration:** When app A on server A needs to call methods from app B on server B, RPC handles the cross-memory-space invocation.

### Typical Scenarios

* **Large-scale websites:** With many subsystems and interfaces.
* **Service Discovery:** Registries like Nacos, Dubbo allow multiple instances of a service to be invoked without awareness of which instance is selected.
* **Security:** Limits resource exposure.
* **Governance:** Supports microservices and distributed management.

### Architecture Diagram

![Architecture](README.assets/655c04a02b08474e985ff4bf8a561d12.png)

### Core Functional Concepts

RPC implementations involve **Service Addressing**, **Serialization/Deserialization**, and **Network Transmission**.

#### Service Addressing

**Call ID Mapping:**

* **Local:** Direct function pointers are used.
* **Remote:** RPC assigns each method a unique Call ID across processes. Clients attach this ID to remote calls. Servers map the Call ID to actual methods using a hash table.

#### Serialization & Deserialization

* **Serialization:** Convert objects into binary for transmission.
* **Deserialization:** Convert binary back to objects.

**Why?**

* Since client and server are in separate memory spaces, they cannot share stack/heap directly. Data must be converted to a transferable format (e.g., byte stream) before sending.

**Benefits:**

* Binary data is easier to transmit.
* Enables cross-platform, cross-language interoperability (e.g., Python client, Java server).

#### Network Transmission

**Responsibilities:**

* Client sends Call ID and serialized parameters.
* Server sends back the serialized result.

**Protocols:** TCP, UDP, HTTP

**Using TCP:**

* Establish socket connection.
* Client sends interface name, method name, serialized params.
* Server deserializes and uses reflection to execute the method.

**Using HTTP:**

* Client sends REST requests.
* Server returns results in JSON or XML.

**Comparison:**

* **TCP:** Lower overhead, higher throughput, better for performance-intensive services. Harder to implement.
* **HTTP:** Higher-level protocol, easier to use, but introduces more overhead.

# Version 1

### Part 1

* Basic RPC invocation
* Client-side dynamic proxy
* Unified Request and Response definitions

### Part 2

* Introduce Netty for transmission
* Custom message formats

### Part 3

* Use ZooKeeper as service registry

![v1](README.assets/1720376325450.png)

# Version 2

### Part 1

* Netty-based encoder, decoder, and serializer

### Part 2

* Local service cache on client side
* Dynamic service cache update

![v2](README.assets/1720376518663.png)

# Version 3

### Part 1

* Client-side load balancing

### Part 2

* Client fault tolerance with retry on failure
* Service whitelisting

![v3](README.assets/1720376674829.png)

# Version 4

### Part 1

* Service rate limiting and degradation
* Circuit breaker implementation

![v4](README.assets/1720376759165.png)

# Version 5

* SPI mechanism
* Centralized configuration
* Support for multiple serialization formats (Kryo, Hessian, Protostuff)
* Improved shutdown handling

![v5](README.assets/image.png)

# Version 6

* Distributed log tracing
* Heartbeat detection
* Retry mechanism based on method whitelisting

![v6](README.assets/version6.jpg)

# Todo List

* [ ] Data compression, encryption, and authentication for transmission
* [ ] Service registration and consumption via annotations
* [ ] Automatically offline nodes with too many failures
* [ ] Proactively check offline node status and recover them
* [ ] Implement adaptive load balancing
