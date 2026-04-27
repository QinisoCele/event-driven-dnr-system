# 🧩 Event-Driven DNR Timer System

> An event-driven system for handling delayed “Delivery Not Received” resolution using Go, NATS, and concurrency patterns.

---

## 📌 Overview

The DNR Timer System is an event-driven backend component designed to handle delayed order resolution workflows. It listens to order events and triggers time-based actions when a delivery is not confirmed within a defined window.

This system ensures automated handling of “Delivery Not Received” scenarios, improving operational efficiency and reducing manual intervention.

---

## 🧠 Problem Statement

Design a system that automatically detects and resolves “Delivery Not Received” scenarios using time-based workflows, while handling asynchronous events reliably.

---

## ⚙️ Key Features

* ⏱️ Per-order asynchronous timers using goroutines
* 🔁 Event-driven architecture using NATS
* 🛑 Safe timer cancellation on delivery confirmation
* 📦 Automated resolution of DNR orders after timeout
* 🔐 Thread-safe timer management using mutexes
* 📡 Structured logging for observability and debugging

---

## 🏗️ Architecture

```mermaid
flowchart TD
    A[Order Events] --> B[NATS]
    B --> C[Order Controller]
    C --> D[DNR Timer Manager]
    D --> E[Goroutine Timers]

    E -->|Timer Expiry| F[Resolve DNR Order]
    F --> G[Update Order Status]
    F --> H[Send Notification]
    F --> I[Trigger Refund]

    C -->|Delivery Confirmed Event| D
    D -->|Cancel Timer| E
```

---

## 🔁 Event Flow

### 1. Delivery Not Received Event

* Order event received via NATS
* Timer started for the order

### 2. Delivery Confirmed Event

* Timer cancelled to prevent unnecessary processing

### 3. Timer Expiry

* Order automatically resolved as DNR
* Downstream actions triggered (status update, notifications, refunds)

```mermaid
sequenceDiagram
    participant OS as Order Service
    participant N as NATS
    participant OC as Order Controller
    participant TM as Timer Manager
    participant T as Timer

    OS->>N: Publish DNR Event
    N->>OC: Deliver Event
    OC->>TM: Start Timer
    TM->>T: Create Timer (goroutine)

    alt Delivery Confirmed
        OS->>N: Publish Confirm Event
        N->>OC: Deliver Event
        OC->>TM: Stop Timer
        TM->>T: Cancel Timer
    else Timer Expires
        T->>OC: Trigger Resolution
        OC->>OC: Resolve Order (DNR)
    end
```

---

## ⏱️ Timer Design

Each order is assigned a dedicated timer managed by a centralized timer manager.

* Uses `context.WithCancel` for safe cancellation
* Uses `sync.Mutex` for thread-safe state management
* Uses `time.NewTimer` for execution trigger
* Uses `time.NewTicker` for periodic progress logging

```mermaid
stateDiagram-v2
    [*] --> Started
    Started --> Running
    Running --> Cancelled: Stop() called
    Running --> Expired: Timer completes
    Cancelled --> [*]
    Expired --> Resolving
    Resolving --> [*]
```

---

## 🧠 Design Considerations

### Concurrency

* Non-blocking timers using goroutines
* Safe shared state using mutex locks

### Idempotency

* Prevents duplicate timers per order
* Ensures safe handling of repeated events

### Observability

* Logs timer lifecycle (start, stop, expiry)
* Tracks remaining time for debugging

---

## ⚠️ Failure Handling

```mermaid
flowchart TD
    A[Timer Fires] --> B{Order Already Resolved?}
    B -- Yes --> C[Ignore Event]
    B -- No --> D[Resolve Order]

    D --> E{Error Occurred?}
    E -- Yes --> F[Retry / Log Error]
    E -- No --> G[Success]

    F --> H[Dead Letter Queue - Future Improvement]
```

---

## ⚠️ Limitations

* Timers are stored in-memory
* Active timers are lost if the service restarts

---

## 🚀 Scaling Evolution

```mermaid
flowchart LR
    A[In-Memory Timers] --> B[Persistent Storage]
    B --> C[Distributed Scheduler]
    C --> D[Delayed Queue System]

    D --> E[High Scale & Fault Tolerance]
```

---

## 🚀 Future Improvements

* Persist timer state in a database for recovery on restart
* Introduce retry mechanisms for failed processing
* Implement dead-letter queues for failed events
* Migrate to a distributed delay queue for scalability
* Add metrics (latency, failures, active timers)

---

## 🛠️ Tech Stack

* Go (Golang)
* NATS (event streaming)
* Goroutines & Channels (concurrency)
* Context API (cancellation control)

---

## 🎯 Key Takeaways

This system demonstrates:

* Event-driven system design
* Asynchronous workflow management
* Concurrency control in Go
* Real-world reliability engineering
* Production-grade backend thinking

---

## 👨‍💻 Author

Qiniso Cele
Full-Stack Engineer | Backend & Systems Focus

---

