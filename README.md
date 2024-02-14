# Microcontroller Sensor Reader

This repository contains the source code and documentation for a project developed during the Microprocessors and Microcontrollers course. The project focuses on recording the characteristics of elements at different temperatures using a microcontroller (AT89S8253) and various components.

## Project Overview

The main goal is to measure the characteristics of electrical elements such as current or voltage at different temperatures. The system records these characteristics and correlates them with temperature using a digital thermometer. The project involves the use of a DS18B20 temperature sensor, A/D and D/A converters, and a MODBUS communication protocol.

## Tools and Technologies Used

- **Microcontroller:** AT89S8253
- **Components:**
  - 4 x 7-Segment Displays
  - A/D Converter
  - D/A Converter
  - Temperature Sensor: DS18B20
  - Resistance: 4.7 kΩ
- **Programming Languages:** Assembly for the microcontroller, C# for PC application
- **Programming Tools:**
  - MCU 8051 IDE
  - MIDE-51
  - 8051FLASH
  - Visual Studio 2015

## Project Functionality

### Microcontroller Board (easy8051a)

The project includes a menu displayed on four 7-segment displays with options for reading set voltage (D/A), reading voltage in A/D, reading temperature, and writing voltage value to D/A. The user can navigate through the menu using buttons.

### PC Application

The PC application, developed in C#, allows graphical representation of the relationship between input voltage from the board and output voltage in other parts of the circuit. The communication between the computer and microcontroller is established using the MODBUS protocol.

## Circuit Configuration

The circuit includes a BJT transistor, resistors, and a voltage supply. It is designed for illustration purposes and can be adapted as needed.

## Documentation

Detailed information on the project, including memory organization in DS18B20, MODBUS protocol details, and circuit configuration, is available in the project documentation.

**Note:** For a comprehensive understanding, please refer to the complete project documentation.

## How to Use

1. Clone the repository.
2. Set up the microcontroller environment using the specified tools.
3. Run the PC application on a computer with Visual Studio 2015 or later.
