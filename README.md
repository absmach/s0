# 🔌 S0 Gateway

**S0** is a modular **IoT gateway** designed for **smart metering** and **secure IoT connectivity**. It bridges Wireless and Wired M-Bus networks with modern IoT platforms, featuring multiple radio interfaces, NB-IoT/LTE-M, and optional Ethernet for flexible deployments in industrial and smart energy environments.

<p align="center">
  <img src="docs/img/s0.png" alt="S0 Board" width="300"/>
</p>

## 🌟 Features

- 📡 **Multi-Protocol Support**: Communicates with meters using **Wireless M-Bus (868 MHz)** and **Wired M-Bus** standards.
- 🌐 **Multiple Connectivity Options**: Uplink via **NB-IoT/LTE-M**, **Ethernet**, or **LoRa** for reliable data transmission.
- ⚡ **Low-Power Operation**: Optimized for battery-powered deployments with energy-efficient components.
- 🛠️ **Modular Design**: Combine the **S0 Core Board** with the **Baseboard** for extended connectivity and power options.
- 💾 **Local Storage**: Optional **microSD card** for logging and offline data buffering.
- 🔒 **Secure Communication**: Supports TLS/DTLS for encrypted MQTT connections to the **Magistrala** IoT platform.

## 🛠️ How It Works

1. **Collect Meter Data**: Gather readings from smart meters via **Wireless or Wired M-Bus**.
2. **Process & Encode**: The ESP32-C6 processes and formats data for transmission.
3. **Transmit Securely**: Send data to the **Magistrala** platform over **NB-IoT**, **Ethernet**, or **LoRa**.
4. **Monitor & Manage**: Enable remote firmware updates, monitoring, and diagnostics via the cloud.

## 📖 Documentation

For setup instructions, pinouts, and integration guides, see the full documentation:  
🔗 [S0 Documentation](https://github.com/absmach/s0-docs)

## 💡 Use Cases

- 🏢 **Smart Buildings**: Connect heat and water meters via Wireless M-Bus for real-time monitoring.
- 🏭 **Industrial IoT**: Collect and transmit sensor data in secure and bandwidth-constrained environments.
- 🌍 **Smart Cities**: Enable large-scale metering infrastructure with NB-IoT and LoRa connectivity.
- 🔐 **Secure Data Acquisition**: Ensure encrypted communication from edge devices to the cloud.

## 🔧 Hardware Highlights

- **Core Board**:
  - MCU: **ESP32-C6** (RISC-V, Wi-Fi 6, BLE 5)
  - Radios: **RC-S2LP** (Wireless M-Bus), **RAK3172** (LoRa), **SIM7080G** (NB-IoT/LTE-M + GNSS)
  - Power: **USB-C**, 7.4 V battery, or Baseboard supply

- **Baseboard**:
  - Ethernet: **W5500 controller**
  - Wired M-Bus: **TSS721A transceiver**
  - Storage: **microSD card slot**
  - Power: **24 V DC input**, battery charger

## 🤝 Contributing

Contributions are welcome! Please check the [CONTRIBUTING.md](#) for details on how to get started.

## 📜 License

S0 is licensed under the **Apache-2.0 License**. See the [LICENSE](LICENSE) file for more details.


> 🇪🇺 This work has been partially supported by the [ELASTIC project](https://elasticproject.eu/) and [CONFIDENTIAL6G project](https://confidential6g.eu/), which received funding from the [Smart Networks and Services Joint Undertaking](https://smart-networks.europa.eu/) (SNS JU) under the European Union’s [Horizon Europe](https://research-and-innovation.ec.europa.eu/funding/funding-opportunities/funding-programmes-and-open-calls/horizon-europe_en) research and innovation programme under [Grant Agreement No. 101139067](https://cordis.europa.eu/project/id/101139067). Views and opinions expressed are those of the author(s) only and do not necessarily reflect those of the European Union. Neither the European Union nor the granting authority can be held responsible for them.
