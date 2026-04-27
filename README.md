# event-driven-dnr-system
Delivery Not Received Timer Architechural Discussion

# 🧩 Delivery Not Received (DNR) Timer System

## 📌 Overview

The DNR Timer System is an event-driven backend component designed to handle delayed order resolution workflows. It listens to order events and triggers time-based actions when a delivery is not confirmed within a defined window.

This system ensures automated handling of “Delivery Not Received” scenarios, improving operational efficiency and reducing manual intervention.

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

```
        [Order Events]
              ↓
           [NATS]
              ↓
     [Order Controller]
              ↓
     [DNR Timer Manager]
              ↓
      [Goroutine Timers]
              ↓
   [Resolve / Refund / Notify]
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
* Downstream actions triggered (status update, notifications, etc.)

---

## ⏱️ Timer Design

* Each order is assigned a dedicated timer
* Timers are managed via a centralized manager using:

  * `context.WithCancel` for cancellation
  * `sync.Mutex` for thread safety
* Uses:

  * `time.NewTimer` → execution trigger
  * `time.NewTicker` → progress logging

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

## ⚠️ Limitations

* Timers are stored in-memory
* Active timers are lost if the service restarts

---

## 🚀 Future Improvements

* Persist timer state in a database for recovery on restart
* Introduce retry mechanisms for failed processing
* Implement dead-letter queues for failed events
* Migrate to a distributed delay queue for better scalability
* Add metrics (latency, failures, active timers) for monitoring

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
* Reliable handling of time-based business logic

---

## 👨‍💻 Author

Qiniso Cele
Full-Stack Engineer | Backend & Systems Focus

