# APB Slave Design and Verification

## Overview

This repository contains the **RTL design and UVM-based verification environment** for a simple **AMBA 3 APB Slave**.

The objective of this project is to verify correct **read/write functionality, APB protocol behavior, and error handling** of the APB slave module.

The design includes a **256-location internal memory** and supports basic **read and write transfers**.

---

# Design Description

The APB slave is implemented in the module:

```
apb_design_n
```

Key features of the design:

* 32-bit **data bus**
* 32-bit **address bus**
* **256 memory locations**
* **Read and write support**
* **Protocol state machine**
* **Error generation for invalid addresses**
* **PREADY generation during ACCESS phase**

---

# APB State Machine

The design uses a **finite state machine (FSM)** with three states.

### IDLE

Default state of the bus.

Conditions:

* `PSEL = 0`
* `PENABLE = 0`

The slave waits for a transfer request.

---

### SETUP

This state begins when the master selects the slave.

Signals:

* `PSEL = 1`
* `PENABLE = 0`

Address and control signals become valid in this phase.

---

### ACCESS

In this state the transfer is completed.

Signals:

* `PSEL = 1`
* `PENABLE = 1`

Operations performed:

* **Write:** `mem[paddr] <= pwdata`
* **Read:** `prdata <= mem[paddr]`

`PREADY` is asserted during this phase to indicate completion.

---

# Memory

The slave contains an internal memory:

```
mem [0:255]
```

* Each location is **32 bits wide**
* Used to store data during **write operations**
* Read operations return stored data

---

# Error Handling

The design generates an **error response** when an invalid address is accessed.

Condition:

```
addr_error = (paddr > DEPTH)
```

When an invalid address is detected:

* `PSLVERR = 1`
* Transfer completes with an error indication.

---

# Verification Environment

The verification environment is built using **SystemVerilog and UVM**.

Architecture:

```
Test
 └── Environment
      ├── Agent
      │    ├── Sequencer
      │    ├── Driver
      │    └── Monitor
      │
      ├── Scoreboard
      └── Subscriber
```

Components perform the following functions:

* **Driver** drives APB signals to the DUT
* **Monitor** observes bus activity
* **Scoreboard** checks read/write correctness
* **Subscriber** collects functional coverage

---

# Verification Features

The verification environment checks:

* APB **write transactions**
* APB **read transactions**
* **memory data integrity**
* **invalid address error response**
* **protocol compliance using assertions**

---

# Functional Coverage

Coverage points include:

* Read transfers
* Write transfers
* Error responses
* Address range access



# Assertions

SystemVerilog assertions verify protocol correctness including:

* Proper **IDLE state behavior**
* **SETUP to ACCESS transition**
* Correct **PENABLE usage**
* **Signal stability during ACCESS phase**

---

# Repository Structure

```
rtl/        - APB slave RTL
tb/         - UVM testbench
sequences/  - stimulus sequences
assertions/ - protocol assertions
coverage/   - functional coverage
README.md   - project documentation
```

---

# Tools

Simulation can be run using:

* Cadence Xcelium
* Synopsys VCS
* Siemens QuestaSim

---

# Conclusion

This project implements and verifies a **simple APB slave with internal memory and error handling**.
The UVM verification environment ensures correct protocol behavior, data integrity, and error reporting.
