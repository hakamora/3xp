# 3XP Core

## Overview
* Designed to create standard easy-to-implement interfaces for various
peripherals
* Intended to be used with I<sup>2</sup>C
* Message-based

## Datatypes

### Integers
* Represented on the wire as ASCII
* Fixed width, must be preceded with leading zeroes as needed
* Documentation shorthand: i<n> = n-digit integer

### ASCII strings
* Prefixed by i2 containing the string's length (note that this implies a
string cannot be any longer than 99 characters)
* Followed by that many ASCII characters from 0x20 - 0x7E
* Documentation shorthand: s = ascii string

### Binary data
* Similar to ASCII string but can hold arbitrary values
* Prefixed by i2 containing the data's length (note that this implies a
binary data field can never hold more than 99 bytes)
* Followed by that many bytes (values 0x00 - 0xFF)
* Documentation shorthand: b = binary data

### List
* Prefixed by i2 containing the number of entries in the list
* Followed by that many entries
* Documentation shorthand: l[entry] = list of entry

## Messages
* All messages contain an i4 which denotes the message type
* There is a many-to-one mapping from message type to message layout (each
message type has a specific layout which may be shared with other message
types)
* Message types 0000-8999 are reserved for standard messages defined by the
3XP standard
* Message types 9000-9999 are for internal/private use. Implementers may choose
to define their own message layouts for any and all message types from this
range but should expect to conflict with other implementers.

## Interfaces
* An interface is defined as an abstract collection of request messages along
with a set of abstract actions that a device should perform in response
* Any device that claims to support an interface must support all request
messages defined in that interface
* New interfaces may reuse existing message types and even add additional
semantic meaning to existing fields in the message
* Interface types 0000-8999 are reserved for standard interfaces defined by the
3XP standard
* Interface types 9000-9999 are for internal/private use. Implementers may
choose to define their own interface types from this range but should expect
to conflict with other implementers.

## Message framing
* "XXXP" i4 i4 i4 <message body>
* Every message starts with the 4 bytes "XXXP"
* This 4 byte sequence is followed by an i4 containing the message type
* Followed by an i4 containing the interface address
* Followed by an i4 containing the message's length
* Followed by the message

## 3XP Core Messages

### Device Info Request
* Type 0000
* empty
* Requests a device to reply with a Device Info message

### Device Interface Request
* Type 0001
* empty
* Requests a device to reply with a Device Interface List message

### Device Info
* Type 0002
* s s s i2 i2
* Field 1: string: device name
* Field 2: string: manufacturer name
* Field 3: string: serial
* Field 4: int: version major
* Field 5: int: version minor

### Device Interface List
* Type 0003
* l[i4 i4]
* Field 1: list: pairs of interface address and interface type

## 3XP Core Interface
* Every 3XP device must implement this interface at address 0000

### Device Info Request
* A 3XP device must respond with a Device Info message
* Requestors that don't receive a response should assume the recipient device
is not a 3XP device and cease attempting to send 3XP requests to the device

### Device Interface Request
* A 3XP device must respond with a Device Interface message
* Requestors that don't receive a response should assume the recipient device
is not a 3XP device and cease attempting to send 3XP requests to the device