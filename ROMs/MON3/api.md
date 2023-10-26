# MON-3 API guide

## General conventions

C register - holds API Call.

Any other parameters are passed in the A, DE and/or HL registers

Executing a RST 10H opcode calls the API

```
        ld c,[API Call Number]
        rst 10H

    Some Examples:

        ;Produce a short Beep from the speaker
        ld c,3      ;beep call number
        rst 10H

        ;Display the letter 'G' on the LCD Screen
        ld c,14     ;charToLCD call number
        ld a,"G"    ;parameter
        rst 10H

        ;Wait for a period of time
        ld c,33     ;timeDelay call number
        ld hl,3000H ;parameter
        rst 10H
```
---
## API Calls list

| Routine | Call Number | Hex Number |
| ------- | ----------- | ---------- |
| [_softwareID](#_softwareid)    | 0 | 00H |
| [_versionID](#_versionid)      | 1 | 01H |
| [_preInit](#_preinit)          | 2 | 02H |
| [_beep](#_beep)                | 3 | 03H |
| [_convAToSeg](#_convatoseg)    | 4 | 04H |
| [_regAToASCII](#_regatoascii)      | 5 | 05H |
| [_ASCIItoSegment](#_asciitosegment)   | 6 | 06H |
| [_stringCompare](#_stringcompare)    | 7 | 07H |
| [_HLToString](#_hltostring)       | 8 | 08H |
| [_AToString](#_atostring)        | 9 | 09H |
| [_scanSegments](#_scansegements)     | 10 | 0AH |
| [_displayError](#_displayerror)     | 11 | 0BH |
| [_LCDBusy](#_lcdbusy)          | 12 | 0CH |
| [_stringToLCD](#_stringtolcd)      | 13 | 0DH |
| [_charToLCD](#_chartolcd)        | 14 | 0EH |
| [_commandToLCD](#_commandtolcd)     | 15 | 0FH |
| [_scanKeys](#_scankeys)         | 16 | 10H |
| [_scanKeysWait](#_scankeyswait)     | 17 | 11H |
| [_matrixScan](#_matrixscan)       | 18 | 12H |
| [_joystickScan](#_joystickscan)     | 19 | 13H |
| [_serialEnable](#_serialenable)     | 20 | 14H |
| [_serialDisable](#_serialdisable)    | 21 | 15H |
| [_txByte](#_txbyte)           | 22 | 16H |
| [_rxByte](#_rxbyte)           | 23 | 17H |
| [_intelHexLoad](#_intelhexload)     | 24 | 18H |
| [_sendToSerial](#_sendtoserial)     | 25 | 19H |
| [_receiveFromSerial](#_receivefromserial) | 26 | 1AH |
| [_sendAssembly](#_sendassembly)     | 27 | 1BH |
| [_sendHex](#_sendhex)          | 28 | 1CH |
| [_genDataDump](#_gendatadump)      | 29 | 1DH |
| [_checkStartEnd](#_checkstartend)    | 30 | 1EH |
| [_menuDriver](#_menudriver)       | 31 | 1FH |
| [_paramDriver](#_paramdriver)      | 32 | 20H |
| [_timeDelay](#_timedelay)        | 33 | 21H |
| [_playNote](#_playnote)         | 34 | 22H |
| [_playTune](#_playtune)         | 35 | 23H |
| [_playTuneMenu](#_playtunemenu)     | 36 | 24H |
| [_getCaps](#_getcaps)          | 37 | 25H |
| [_getShadow](#_getshadow)        | 38 | 26H |
| [_getProtect](#_getprotect)       | 39 | 27H |
| [_getExpand](#_getexpand)        | 40 | 28H |
| [_setCaps](#_setcaps)          | 41 | 29H |
| [_setShadow](#_setshadow)        | 42 | 2AH |
| [_setProtect](#_setprotect)       | 43 | 2BH |
| [_setExpand](#_setexpand)        | 44 | 2CH |
| [_toggleCaps](#_togglecaps)       | 45 | 2DH |
| [_toggleShadow](#_toggleshadow)     | 46 | 2EH |
| [_toggleProtect](#_toggleprotect)    | 47 | 2FH |
| [_toggleExpand](#_toggleexpand)     | 48 | 30H |

----
### _softwareID 
Get Software ID String
```
Input: nothing
Return: HL = Pointer to SOFTWARE ASCIIZ String
Destroy: none
```
### _versionID
Get Version Number and Version Straing
```
Input: nothing
Return: HL = Pointer to Release ASCIIZ String
        BC = Release major version number
        DE = Release minor version number
Destroys: none
```
### _preInit
Performs a hard boot, as if the TEC-1G had just been powered on. Returns to MON3 in default state.

### _beep
Makes a short beep tone
```
Input: nothing
Destroys: A
```
### _convAToSeg
Convert register A to Seven Segment
```
Inputs: A = byte to convert
        DE = address to store segment values (2 bytes)
Destroys: BC
```
### _regAToASCII
Convert register A to ASCII. IE: 2CH -> "2C"
```
Input: A = byte to convert
Output: HL = two byte ASCII string
Destroys: A
```
### _ASCIItoSegment
ASCII to Segment.  Converts an ASCII character to Seven Segment character
```
Input: A = ASCII character
Return: A = Segment character or 0 if out of range
Destroys: none
```
### _stringCompare
Compare two string
```
Input: HL = source pointer
       DE = target pointer
       B = #bytes to compare (up to 256)
Output: Zero Flag Set = compare match
Destroys: HL, DE, A, BC
```
### _HLToString
Convert HL to ASCII string. IE: 2CH -> "2C"
```
Input: HL = value to convert
       DE = address of string destination
Output: DE = address one after last ASCII entry
Destroys: A
```
### _AToString
Convert register A to ASCII string
```
Input: A = byte to convert
       DE = address of string destination
Output: DE = address one after last ASCII entry
Destroys: A
```
### _scanSegements
Multiplex the Seven Segment displays with the contents of DE
```
Inputs: DE = 6 byte location of segment data
Destroys: A, B
```
### _displayError
Display ERROR on the 7Segs and wait for key press
```
Input: none
Destroys: all
```
### _LCDBusy
LCD busy check.  Checks the LCD busy flag and loops until LCD isn't busy
```
Input: nothing
Destorys: none
```
### _stringToLCD
ASCII string to LCD.  Writes a string to the current cursor location on the LCD
```
Input: HL = ASCII string terminated with zero byte
Destroy: A, HL (moves to end of list)
```
### _charToLCD
ASCII character to LCD.  Writes one character to the LCD at the current cursor location
```
Input: A = ASCII character
Destroy: none
```
### _commandToLCD
Command to LCD.  Sends an LCD instruction to the LCD
```
Input: B = Instruction byte
Destroy: none
```
### _scanKeys
Universal Key input detection routine. Supports HexPad and Matrix. Does not wait for a key press - returns immediately.
```
Return: A = key value (if following are met)
    zero flag set if a key is pressed
    carry flag set if press detected of a new key
    carry flag not set for key pressed and held
Destroys: DE if using Matrix Keyboard
```
### _scanKeysWait
Generic Key input detection routine. Supports HexPad and Matrix. Waits until a key is pressed.
```
Return: A = key value (if following are met)
    zero flag set if key is pressed
Destroys: DE if using Matrix Keyboard
```
### _matrixScan
Key scan routine for the Matrix Keyboard.  This routine detects up to two key presses at the same time.  Key values stored in DE.  Routine must be called repetitively.
```
Input: None
Output: E = Key pressed between 00H-3FH (0-64)
        D = Second key, FF=no key, 00=shift, 01=Ctrl, 02=Fn
        zero flag set if a key is pressed or combination valid
```
### _joystickScan
Joystick port scan routines.  This routine will return a value based on the movement / button of the joystick or any combination: IE: UP+DOWN = 03H, Routine must be called repetitively.
```
Input: None
Output: A = Joystick return value between 00H-5FH (0-95)
      01H = Up        10H = Fire 2
      02H = Down      20H = Comm2 (Pin 9)
      04H = Left      40H = Fire 1
      08H = Right     80H = Fire 3
        zero flag set if no joystick value returned
Destroy: none
```
### _serialEnable
Activates the BitBang serial port for serial transfer.  Disco LED's glow blue to indicate ready status.
```
Input: none
Destroy: A
```
### _serialDisable
Deactivates the BitBang serial port for serial transfer.  Disco LEDs turn off.
```
Input: none
Destroy: A
```
### _txByte
Bit Bang FTDI USB transmit routine.  Transmit one byte via the FTDI USB serial connection.  It assumes a UART connection of 4800-8-N-2.
```
Input: A = byte to transmit
Output: nothing
Destroy: none
```
### _rxByte
Bit Bang FTDI USB receive routine.  Receive one byte via the FTDI USB serial connection.  It assumes a UART connection of 4800-8-N-2.  Note routine will wait until a bit is detected.
```
Input: nothing
Return: A = byte received
Destroy: none
```
### _intelHexLoad
Load an Intel Hex file via the FTDI USB serial connection.  Displays file progress on the segments and PASS or FAIL at the end of the load.  Intel Hex file format is a string of ASCII with the following parts:

MARK | LENGTH | ADDRESS | RECORD TYPE | DATA | CHECKSUM
:10200000210621CD7D20CD98203A00213C320021AF <- EXAMPLE LINE
MARK is a colon character, LENGTH is the number of bytes per line, ADDRESS  is the 2 byte address of where the data is to be stored.  RECORD TYPE is 00 for Data and 01 for EOF.  DATA is the bytes to be stored.  CHECKSUM is the addition of all bytes in the one line.
```
Input: nothing
Output: nothing
Destroy: HL,DE,BC,A
```
### _sendToSerial
SIO Binary Dump.  Transfer data on the TEC to a serial terminal.  From and To address data is needed and input.
```
Input: 2 byte from address = Stored in RAM at address 08C0H
       2 byte to address = Stored in RAM at address 08C2H
Destroys: A,HL,DE,BC
```
### _receiveFromSerial
SIO receive binary data.  Receive binary data from FTDI.  From and To address data is needed and input.
```
Input: 2 byte from address = Stored in RAM at address 08C0H
       2 byte to address = Stored in RAM at address 08C2H
Destroys: A,HL,DE,BC
```
### _sendAssembly
Send Assembly instructions to the serial port.  Print out code disassembly.  Receive binary data from FTDI.  From and To address data is needed and input.
```
Input: 2 byte from address = Stored in RAM at address 08C0H
       2 byte to address = Stored in RAM at address 08C2H
Destroys: A,HL,DE,BC
```
### _sendHex
Send a traditional HEX dump as text to the serial terminal.  Upto 16 bytes are displayed per line.    From and To address data is needed and input.
```
Input: 2 byte from address = Stored in RAM at address 08C0H
       2 byte to address = Stored in RAM at address 08C2H
Destroys: A,HL,DE,BC
```
### _genDataDump
Generate data dump in ASCII.  Print Address and then C number of bytes
```
Input: B = number of bytes to display
       HL = start address of data dump
       DE = address of string destination
Output: DE = zero terminated address one after last ASCII entry
          IE: "4000: 23 34 45 56 78 9A BC DE",0
Destroys: A, HL (moves to next address after last byte)
```
### _checkStartEnd
Check start and end address difference.
```
Input: HL = address location of START value
       HL+2 = address location of END value
Output: HL = start address
        BC = length of end-start
        Carry = set if end is less than start
Destroys: DE
```
### _menuDriver
Menu driver for external applications.  Creates a selectable custom menu.  Keys: Go = Select menu item, AD = Exit Menu, +/- = Navigate menu
```
Input: HL = Pointer to Menu configuration. (See Note)
Destroys: A, HL

Note: Menu configuration is as follows.  All strings are ZERO terminated!
<Menu Entries>, <Menu Text Title>, [<Menu Text Label>, <Menu Routine Address>]+
  EG: .db 2                 ;Two menu items
      .db "Games",0         ;Menu title
      .db "TEC Invaders",0  ;Text and Routine
      .dw invaders
      .db "TEC Maze",0      ;Text and routine
      .dw maze
```
### _paramDriver
Parameter data entry driver.  Creates a list of editable two byte parameters.  Keys: Go = Continue, AD = Exit, +/- = Navigate, 0-F = enter values
```
Input: HL = Pointer to Parameter configuration. (See Note)
Note: Parameter configuration is as follows.  All strings are ZERO terminated!  Parameter text can be no longer than 14 characters
<No. of Entries>, <Parameter Title Text>, [<Param Text Label>, <Param RAM Address>]+
  EG: .db 3                         ;Three parameters
      .db "= Enter Parameters =",0  ;Parameter title
      .db "Start Address:",0        ;Text and Address
      .dw COPY_START
      .db "End Address:",0          ;Text and Address
      .dw COPY_END
      .db "Dest. Address:",0        ;Text and Address
      .dw COPY_DEST
```
### _timeDelay
A 16 bit delay routine.
```
Input: HL = delay amount
Destroys: none
``` 
### _playNote
Play a note.  Play a note with a given frequency and wavelength
```
Input: HL = frequency (01-7F)
       B = wavelength (00-FF)
Destroys: HL, BC, A
```
### _playTune
Play a series of notes.  To play a note use a reference between 01H and 18H.  Where 01H is the lowest and 18h is the highest.  Use 00H to make a pause and any value greater then 18H to exit.

Note reference table is as follows:
+-----------+
|  G  | 01H |
|  G# | 02H |
|  A  | 03H |
|  A# | 04H |
|  B  | 05H |
|  C  | 06H |
|  C# | 07H |
|  D  | 08H |
|  D# | 09H |
|  E  | 0AH |
|  F  | 0BH |
|  F# | 0CH |
|  G  | 0DH |
|  G# | 0EH |
|  A  | 0FH |
|  A# | 10H |
|  B  | 11H |
|  C  | 12H |
|  C# | 13H |
|  D  | 14H |
|  D# | 15H |
|  E  | 16H |
|  F  | 17H |
|  F# | 18H |
+-----------+
```
Input: DE = Address of first note
Destroy: A,B,DE,HL
```
### _playTuneMenu
Play tune routine above but address of notes is set via a parameter menu.
### _getCaps
Get Caps lock state
```
Input: none
Output: A = caps lock state; 0 = off, CAPSLOCK = on
```
### _getShadow
Get SHADOW state
```
Input: none
Output: A = shadow state; 0 = off, SHADOW = on
```
### _getProtect
Get PROTECT state
```
Input: none
Output: A = protect state; 0 = off
```
### _getExpand
Get EXPAND state
```
Input: none
Output: A = expand state; 0 = off
```
### _setCaps
Set Caps lock state
```
Input: A = Desired caps lock state; 0 = off, 1 = on
Destroy: A
```
### _setShadow
Set Shadow state
```
Input: A = Desired shadow state; 0 = off, 1 = on
Destroy: A
```
### _setProtect
Set Protect state
```
Input: A = Desired protect state; 0 = off, 1 = on
Destroy: A
```
### _setExpand
Set Expand state
```
Input: A = Desired expand state; 0 = off, 1 = on
Destroy: A
```
### _toggleCaps
Toggle Caps Lock state
```
Input: none
Destroy: A
```
### _toggleShadow
Toggle Shadow state
```
Input: none
Destroy: A
```
### _toggleProtect
Toggle Protect state
```
Input: none
Destroy: A
```
### _toggleExpand
Toggle Expand state
```
Input: none
Destroy: A
```

---