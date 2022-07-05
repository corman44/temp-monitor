# temp-monitor

## DS18B20 Info
### 64-bit ROM Code [ 8-bit CRC | 48-bit Serial Number | 8-bit family code (28h) ]
###                  ^MSB   LSB^ ^MSB              LSB^ ^MSB                 LSB^
### Memory Map
### Byte 0: Temp LSB (50h)
### Byte 1: Temp MSB (05h) (combo for 85degC)
### Byte 2: T_H Reg or User Byte1 <-> Read from EEPROM
### Byte 3: T_L Reg or User Byte2 <-> Read from EEPROM
### Byte 4: Config Reg <-> Read from EEPROM
### Byte 5: Reserved
### Byte 6: Reserved
### Byte 7: Reserved
### Byte 8: CRC

### Config Register
#### Bit7: 0, Bit6: R1, Bit5: R2, Bit4: 1, Bit3: 1, Bit2: 1, Bit1: 1, Bit0: 1
#### setting R1=R2=1 will enable 12 resolution bits at 750ms

### CRC Generator
#### CRC = X^8 + X^5 + X^4 + 1

### 1-Wire Comm
#### DS18B20 is always in Slave mode. All data transmitted with LSB first
#### Transaction Sequence: Init, ROM Command, DS18B20 Function Command
#### Init: Master Tx Reset Pulse of > 480us, then release.
#### ROM Commands: 
#### - Search ROM [0xF0]: during init process, Master needs to identify each slave
#### - Read ROM [0x33]: used when 1 device is connected. Allows Master to read the 64-bit ROM code. (not to be used with multiple slaves on bus)
#### - Match ROM [0x55]: this command followed by a 64-bit ROM Code sequence allows the master to address a specific slave device on a multi/singledrop bus. Only the slave with the matching address will respond. (until another reset pulse is sent)
#### - Skip ROM [0xCC]: allows master to send a command to all slaves
#### - Alarm Search [0xEC]: allows Master to determine if any device has reached the alarm stage. Init sequence must be followed for further commands.
#### DS18B20 Commands:
#### - Convert T [0x44]: this command initiates a single temp conversion. Bus is pulled low by slave for duration of t_CONV.
#### - Write Scratchpad [0x4E]: this command allows the master to write 3 bytes of data to the scratchpad. T_H, T_L, Config reg is the order (Byte2, Byte3, Byte4). Data transmitted LSB first before Master issues a reset.
#### - Read Scratchpad [0xBE]: allows the master to read the contents of the scratchpad. Transfer starts with LSB of Byte0 and continues through Byte8's MSB. Master may terminate the transmission part way through if not all Bytes are necessary.
#### - Copy Scratchpad [0x48]: commands the device to copy the data Bytes 2,3,4 to EEPROM for later usage.
#### - Recall E^2 [0xB8]:

#testBIG
testbig
##testsmaller
testsmaller
###testsmallest
testsmallest