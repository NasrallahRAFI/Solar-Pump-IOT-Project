HelioDrive IoT: Secure and Autonomous Solar Pump Management System
The HelioDrive IoT Project establishes a comprehensive, secure cyber-physical system for the remote monitoring and executive control of a Solar Photovoltaic (PV) powered Variable Frequency Drive (VFD) and its associated water pump. The architecture adheres to industry standards for real-time telemetry and command execution, leveraging specialized communication protocols across multiple integrated layers.

System Architecture and Protocol Stack
The system employs a three-tier architecture that spans the field device level, the cloud transport layer, and the human-machine interface (HMI).

1. Edge Device Layer (ESP32 Microcontroller)
The ESP32 unit functions as the IoT gateway or data acquisition unit (DAQ).

Industrial Communication: The ESP32 is integrated with the VFD via a serial interface utilizing the Modbus RTU protocol over RS485. This allows the reading of process variables from the VFD's Holding Registers (e.g., motor frequency, current, and temperature).

Hardware Interface: Communication is established using a dedicated UART interface (e.g., Serial2 on pins RX=16, TX=17) with an RS485 transceiver controlled by a Direction Enable pin (DE/RE, e.g., GPIO 15 or 4).

Internet Transport: The device achieves wide-area connectivity via Wi-Fi.

Security and Telemetry: Data is transmitted using MQTT (Message Queuing Telemetry Transport), secured by an encrypted transport layer via TLS/SSL (Transport Layer Security/Secure Sockets Layer). A dedicated Root CA Certificate ensures cryptographic verification of the broker.

2. Cloud Transport Layer (MQTT Broker)
Broker Platform: HiveMQ Cloud serves as the central message broker, providing a robust, fault-tolerant backbone for message queuing.

Bidirectional Messaging: This layer maintains a persistent connection for both publishing sensor telemetry and subscribing to control commands.

3. Application Layer (Web HMI)
Interface: A web-based Human-Machine Interface (HMI) application (client.html) is responsible for data visualization and remote command issuance.

Client Communication: The application employs the Paho MQTT JavaScript library to maintain a secure, real-time data link to the cloud broker.

Authentication: Access is controlled by an initial login module (index.html) which utilizes provided credentials for MQTT broker authentication, adhering to the principle of least privilege.

Core System Functionalities
Real-time Monitoring and Diagnostics
The system captures and processes critical operational parameters published across distinct MQTT topics, providing instantaneous visibility into the VFD's state:

Performance Metrics: Real-time motor temperature (vfd/temperature) and rotational speed/frequency (vfd/speed).

Power Quality: Monitoring of supply (Input Voltage/Current: vfd/tensionIN, vfd/courantIN) and load-side (Output Current: vfd/courantOUT) electrical data.

Environmental Status: Measurement of water storage levels (vfd/niveau_eau) for autonomous pump protection and resource management.

Condition Monitoring: Reporting of VFD fault codes and alarms (vfd/alertes) and network stability status (vfd/etat_reseau).

Executive Control and Automation
The HMI facilitates precise remote command execution and scheduling:

Discrete Control: Users can initiate Start (on), Stop (off), or transition the pump into Autonomous Mode (auto) via the vfd/status topic.

Proportional Control: A proportional setpoint for the VFD's operating frequency is issued via the vfd/Speedinput topic, allowing throttling of flow/pressure.

Time-Based Automation: A programmable schedule management module enables the configuration of periodic pump operation based on day-of-week and time-of-day criteria, synchronized with the ESP32 via the vfd/schedule topic.

Data Visualization and Alerting
The HMI presents data with advanced visual cues for efficient operational decision-making:

Visualization: Data is rendered in dedicated cards and collapsible panels, featuring progress gauges for levels and numerical displays.

Exception Highlighting: Operational parameters are subjected to conditional rendering, visually classifying data points as "normal," "warning," or "critical" to draw attention to threshold violations (e.g., low water level or overtemperature).

Logging: A persistent communication log provides an audit trail of all messages exchanged with the IoT device.
