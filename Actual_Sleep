## Sleep Mode


**RFM95 Slave Communication Behavior**

**Wake Period:**
- **MOSI (Master Out Slave In) Line:** Low state.
- **MISO (Master In Slave Out) Line:** Low state.
- **NSS (Slave Select) Pin:** On state (continuous).

During the wake period of the RFM95 slave:
- The MOSI line remains low.
- The MISO line remains low.
- The NSS pin stays in the on state continuously.

**Sleep Period:**
- **MOSI (Master Out Slave In) Line:** High state (sending sleep command).
- **MISO (Master In Slave Out) Line:** Low state.
- **NSS (Slave Select) Pin:** On state (continuous).

During sleep:
- The MOSI line goes high as the master sends a sleep command over SPI to the RFM95.
- The MISO line remains low.
- The NSS pin stays on continuously.

