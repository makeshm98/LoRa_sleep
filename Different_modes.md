## Different Modes 
## Register: RegIrqFlags1 (0x3E)

### Description
This register is used to indicate various interrupt conditions and status flags related to the operation modes of the RFM95 module.

### Bits
1. **ModeReady**: Set when the operation mode requested in the `Mode` register is ready. It indicates different operating modes such as Sleep, Standby, Rx (Receiving), and Tx (Transmitting).
2. **RxReady**: Indicates that the module is in Rx (Receiving) mode and is ready to receive data.
3. **TxReady**: Indicates that the module is in Tx (Transmitting) mode and is ready to transmit data.
4. **PllLock**: Indicates whether the PLL (Phase-Locked Loop) is locked, typically used in FS, Rx, or Tx modes.
5. **Rssi**: Set in Rx mode when the Received Signal Strength Indicator (RSSI) exceeds a specified threshold.
6. **Timeout**: Set when a timeout occurs during an operation.
7. **PreambleDetect**: Set when the Preamble Detector has found a valid Preamble.
8. **SyncAddressMatch**: Set when Sync and Address (if enabled) are detected.

These bits provide information about the module's current state and the occurrence of various events such as readiness for operation, reception of data, transmission readiness, PLL lock status, RSSI threshold crossing, timeouts, preamble detection, and sync/address matching.
