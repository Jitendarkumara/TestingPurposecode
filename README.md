Level‑1 PLC (Machine Layer)

This is the actual plant equipment (machines, sensors)
Generates process data like:

Speed
Temperature
Thickness
Status signals



✅ Sends:

PROCESS DATA → OPC Server

✅ Receives:

SET VALUES (commands) from higher level


2. OPC Server (Communication Layer)

OPC = Open Platform Communication
Works as a bridge between PLC and Level‑2 system

🔄 Functions:

Collects real-time data from PLC
Sends SET VALUE back to PLC
Transfers data to/from database

✅ Key role:
👉 Translator + Interface between machine and IT system

3. Database (Level‑2 Core)

Central storage system (SQL / Oracle)

🔄 Functions:

Stores:

Process data
Coil information
Historical records


Performs:

Setpoint calculations
Data processing / logic



✅ This is the brain of Level‑2 system

4. MES (C3DIS) – Manufacturing Execution System

High-level system used for:

Production planning
Quality tracking
Order management



Data exchange:


PDI (Production Data Input)
→ MES sends order/coils data to database


PDO (Production Data Output)
→ Database sends results back to MES



🔄 Complete Data Flow (Step-by-Step)
Step 1: Machine → OPC

PLC sends real-time process data

➡️ OPC Server receives it

Step 2: OPC → Database

Data goes to database for storage & processing


Step 3: MES → Database (PDI)

MES sends:

Coil details
Order info
Quality specs




Step 4: Database Processing

System performs:

Setpoint Calculation (SETPOINT CALC)
Logic & validation




Step 5: Database → OPC

Sends calculated SET VALUES


Step 6: OPC → PLC

PLC receives commands and adjusts machine parameters


Step 7: Database → MES (PDO)

Sends:

Production results
Status
Reports




🔁 Simple Flow Summary
MES → Database → OPC → PLC (Machine)
        ↑              ↓
       Database ← OPC ← PLC
