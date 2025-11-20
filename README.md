# UNIX Distributed Message Broker (Pub/Sub System)

## Executive Summary
This project is a **terminal-based messaging middleware** engineered in **C** for Linux/UNIX environments. It functions as a lightweight **Publish-Subscribe (Pub/Sub)** system, designed to demonstrate mastery of **Inter-Process Communication (IPC)**, resource synchronization, and low-level system calls.

The system decouples message senders from receivers using a central broker architecture, allowing multiple concurrent clients to exchange data across dynamic topics in real-time, supported by a persistence layer for data recovery.

## System Architecture
The solution implements a classic Client-Server model using two primary executable components:



### 1. The Manager (Broker)
The central daemon responsible for the system's state. It handles:
* **Registry Management:** Tracking active users and subscriptions.
* **Message Routing:** Dispatching incoming payloads to the correct subscribers.
* **State Persistence:** Serializing messages to disk to survive process restarts (based on Time-To-Live logic).
* **Access Control:** Admin capabilities to lock topics or evict clients.

### 2. The Feed (Client)
A CLI endpoint for users. It allows:
* **Dynamic Subscription:** Users can subscribe/unsubscribe to data streams (topics) on the fly.
* **Broadcasting:** Sending ephemeral or persistent messages to specific topics.
* **Asynchronous Reception:** Listening for incoming messages via blocking/non-blocking reads.

---

## Technical Implementation

### Core Technologies
* **Language:** C (GCC Compiler).
* **IPC Mechanism:** **Named Pipes (FIFOs)** serve as the transport layer for bidirectional communication between the Manager and Feeds.
* **Concurrency:** UNIX **Signals** are utilized for event handling, interrupt management, and process synchronization.
* **File I/O:** Low-level system calls for managing the persistent storage of messages.
* **Build System:** GNU Make.

### Key Features
* **Topic-Based Routing:** Messages are strictly compartmentalized; clients only receive data from topics they explicitly follow.
* **Message Persistence (TTL):** Messages can be flagged with a duration. These are stored in a text file (defined by environment variables) and are automatically reloaded into memory if the server reboots within the validity window.
* **Concurrency Safety:** The system handles multiple simultaneous client connections without race conditions.
* **Graceful Teardown:** Custom signal handlers ensure that all FIFOs are unlinked and resources freed upon system exit (`SIGINT` or command), preventing "zombie" pipes.
* **Administrative Lock:** The Manager can "freeze" a topic, preventing new writes while maintaining read access for history.

---

## Command Reference

### Client Commands (`Feed`)
Users interact with the system via standard input:

* `topics` : Retrieves a list of all active topics.
* `msg <topic> <duration> <body>` : Publishes a message.
    * If `duration > 0`, the message persists for that many seconds.
* `subscribe <topic>` : Attaches the client to a topic stream.
* `unsubscribe <topic>` : Detaches the client from a topic.
* `exit` : Safely disconnects from the broker.

### Admin Commands (`Manager`)
The server console acts as an administrative terminal:

* `users` : Lists currently connected clients.
* `remove <username>` : Forcibly disconnects a specific user.
* `topics` : Displays topics and their current message backlog stats.
* `show <topic>` : Dumps the content/history of a specific topic.
* `lock <topic>` : Freezes a topic (rejects new messages).
* `unlock <topic>` : Re-opens a topic for writing.
* `close` : Initiates global system shutdown.

---

Academic Context
This project was developed as the final coursework for "Operating Systems" (Academic Year 2024/2025).

Team Size: 2 Members 
Final Grade: 9.2 / 10

## Configuration & Execution

### 1. Environment Setup
The system requires an environment variable to define the storage path for persistent messages:
```bash
export MSG_FICH=persistence_storage.txt


2. Compilation
Use the provided Makefile to build both binaries:

Bash

make all
3. Runtime
Step A: Start the Broker

Bash

./manager
Step B: Start Client(s) Open a new terminal window for each user:

Bash

./feed <username>


