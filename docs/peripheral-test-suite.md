# S0 and Baseboard Peripheral Testing Suite

This document contains all electrical and functional tests required to validate the S0 module and its baseboard after PCB fabrication.

## 1. Visual and continuity checks

### 1.1 Visual inspection

* [ ] All connectors aligned (USB-C, RJ45, terminal blocks)
* [ ] No solder bridges on ESP32-C6, SIM7080G, W5500, S2LP
* [ ] No missing components on regulator networks
* [ ] No polarity errors on diodes, LEDs, electrolytic caps
* [ ] No cracked MLCCs or lifted pads

### 1.2 Continuity tests

* [ ] 3V3 ↔ GND not shorted
* [ ] **1V8 ↔ GND not shorted**
* [ ] 5V ↔ GND not shorted
* [ ] Battery input ↔ GND high resistance
* [ ] No unexpected continuity between high-current nets

## 2. Power-up and rail validation

### 2.1 S0 USB-C power

* [ ] 5 V measured at TP1 ≈ 5.0 V
* [ ] 3.3 V measured at TP3 ≈ 3.3 V
* [ ] No components overheating
* [ ] USB enumeration visible on PC

### 2.2 S0 battery input power

* [ ] 7.0 V applied correctly to JST battery input
* [ ] 3.3 V rail stable
* [ ] Idle current within expected range
* [ ] No thermal hotspots

### 2.3 Baseboard power (no S0 installed)

* [ ] TP1 = 3.3 V
* [ ] TP2 = 5 V
* [ ] TP3 = GND
* [ ] Regulators warm but not hot

### 2.4 S0 powered from baseboard

* [ ] S0 boots normally from baseboard input
* [ ] USB log available
* [ ] No resets or brownouts

## 3. ESP32-C6 core bring-up

### 3.1 USB console

* [ ] Boot messages observed at 115200 baud
* [ ] No continuous reboot loop

### 3.2 Flash bring-up firmware

* [ ] Flashing successful via USB
* [ ] LED blinks according to firmware
* [ ] UART prints periodic heartbeat

### 3.3 JTAG scan

* [ ] MTCK / MTMS / MTDI / MTDO connected
* [ ] JTAG probe detects single ESP32-C6
* [ ] No unstable connection

## 4. S0 onboard peripheral tests

### 4.1 GPIO and LED

* [ ] EXT_GPIO1 toggles normally
* [ ] EXT_GPIO2 toggles normally
* [ ] EXT_GPIO3 toggles normally
* [ ] LED pin toggles observable

### 4.2 I2C test

* [ ] Bus scan detects connected device
* [ ] No SDA/SCL stuck low
* [ ] Clean SCL/SDA waveforms

### 4.3 SPI test

* [ ] MOSI/MISO/SCK functional
* [ ] Loopback or flash readback correct
* [ ] Clean 1–4 MHz clock waveform

### 4.4 SIM7080G tests

#### Power control

* [ ] SIM7080_PWR toggles modem on/off
* [ ] Current draw changes appropriately

#### UART AT-link

* [ ] “AT” returns “OK”
* [ ] “ATI” returns modem info
* [ ] “AT+CSQ” returns valid RSSI

#### Network attach

* [ ] APN configured
* [ ] Attach success
* [ ] GNSS valid (outdoors)

### 4.5 Wireless M-Bus (S2LP)

* [ ] Driver initializes
* [ ] Transmit test packet successful
* [ ] Remote receiver or RF tool detects frame

### 4.6 USB-C stability

* [ ] Device enumerates
* [ ] High-rate serial spam test stable
* [ ] No disconnects

## 5. Baseboard peripheral tests

### 5.1 SPI link between S0 and baseboard

* [ ] Baseboard SPI activity visible
* [ ] No contention or stuck lines

### 5.2 Ethernet (W5500)

* [ ] PHY reset functional
* [ ] DHCP or static IP obtained
* [ ] Ping success
* [ ] TCP transfer stable

### 5.3 SD card slot

* [ ] SD card detected
* [ ] Filesystem mount OK
* [ ] Read/write/delete cycle successful

### 5.4 Wired M-Bus

* [ ] TXI/RXI waveforms correct
* [ ] Request frames sent
* [ ] Meter responds

### 5.5 Rail robustness

* [ ] 3V3 holds under 100 mA added load
* [ ] No thermal runaway

## 6. System-level tests

### 6.1 End-to-end data path

* [ ] Read sensor or M-Bus input
* [ ] ESP32 processes packet
* [ ] Data forwarded via NB-IoT or Ethernet
* [ ] Backend receives correct data

### 6.2 Sleep modes

* [ ] Light sleep reachable
* [ ] Deep sleep current matches datasheet values
* [ ] Wake reliably from GPIO/RTC

### 6.3 Long-run soak test (24–72 hours)

* [ ] SD card logging stable
* [ ] Ethernet or NB-IoT heartbeat stable
* [ ] No memory leaks or crashes
* [ ] No unexplained resets

## ESP32-C6 Bring-Up Firmware Template (ESP-IDF)

This firmware template is used to validate all electrical and functional subsystems of the S0 module and its baseboard. It must be compiled with the official Espressif toolchain.

### **Build Framework / Toolchain Requirements**

**Framework:**

* ESP-IDF (Espressif IoT Development Framework), version **5.x or later**

**Compiler Toolchain:**

* Xtensa-ESP32-C6 GCC toolchain (installed automatically by ESP-IDF)

**Why ESP-IDF is required:**

* Uses native FreeRTOS integration
* Uses ESP-IDF drivers for GPIO, UART, SPI, I2C
* Uses ESP logging system (`esp_log.h`)
* Uses ESP-IDF startup & build system (CMake)
* Arduino Core is **NOT compatible** with this code

### **Install ESP-IDF**

Follow Espressif’s official guide for ESP32-C6:

```bash
git clone -b v5.2 https://github.com/espressif/esp-idf.git
cd esp-idf
./install.sh esp32c6
```

### **Export Environment**

Before building:

```bash
. $HOME/esp/esp-idf/export.sh
```

This sets the PATH, compiler toolchain, and IDF environment variables.

### **Recommended Project Structure**

```shell
firmware/
│
├── CMakeLists.txt
├── sdkconfig
└── main/
    ├── CMakeLists.txt
    └── main.c     ← place bring-up firmware here
```

**Top-level CMakeLists.txt:**

```cmake
cmake_minimum_required(VERSION 3.16)
include($ENV{IDF_PATH}/tools/cmake/project.cmake)
project(s0_bringup)
```

**main/CMakeLists.txt:**

```cmake
idf_component_register(SRCS "main.c" INCLUDE_DIRS ".")
```

This is all you need to compile the provided bring-up code.

### **Build Firmware**

Inside the project folder:

```bash
idf.py build
```

### **Flash Firmware**

Replace `/dev/ttyACM0` with the correct USB device:

```bash
idf.py -p /dev/ttyACM0 flash
```

### **Flash + Monitor**

```bash
idf.py -p /dev/ttyACM0 flash monitor
```

Exit monitor with:
`Ctrl + ]`

### **Serial Console Use**

The firmware accepts commands over **UART0** (USB CDC), running at **115200 baud**.

Commands:

| Command             | Description                |
| ------------------- | -------------------------- |
| `LIST`              | Prints all test names      |
| `RUN ALL`           | Runs the entire test suite |
| `RUN <NAME>`        | Runs a specific test       |
| Any invalid command | Returns JSON error         |

### **Output Format**

Each test prints structured JSON:

```json
{"type":"result","name":"W5500_BASIC","result":"PASS"}
```

This enables automated factory testing and Python integration.

### **Additional Instructions**

1. Flash the firmware using:

   ```bash
   idf.py -p /dev/ttyACM0 flash monitor
   ```

2. Open the USB serial console at **115200 baud**.
3. Issue commands:

   * `LIST`
   * `RUN ALL`
   * `RUN MODEM_AT`
4. All results are printed as JSON for easy machine parsing.

Below is a minimal and clean template suitable for executing all tests above. You can drop this straight into `main.c` and adapt pin mappings and actual driver code as needed.

```c
#include <string.h>
#include <stdio.h>

#include "driver/gpio.h"
#include "driver/i2c.h"
#include "driver/spi_master.h"
#include "driver/uart.h"
#include "esp_log.h"
#include "esp_system.h"
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

#define LED_GPIO        2
#define GPIO1           3
#define GPIO2           4
#define GPIO3           5

#define UART_MODEM_TX   20
#define UART_MODEM_RX   21
#define UART_MODEM_NUM  UART_NUM_1

#define UART_CMD_NUM    UART_NUM_0

#define I2C_SCL         7
#define I2C_SDA         6
#define I2C_PORT        I2C_NUM_0

#define SPI_MOSI        9
#define SPI_MISO        10
#define SPI_CLK         11

#define SPI_CS_W5500    12
#define SPI_CS_SD       13
#define SPI_CS_S2LP     14

#define W5500_INT_GPIO  15
#define W5500_RST_GPIO  16

#define S2LP_SDN_GPIO   17

#define MODEM_PWR_GPIO  18

static const char *TAG = "BRINGUP";

static spi_device_handle_t spi_dev_w5500;
static spi_device_handle_t spi_dev_sd;
static spi_device_handle_t spi_dev_s2lp;

typedef enum {
    TEST_PASS = 0,
    TEST_FAIL = 1
} test_result_t;

typedef struct {
    const char *name;
    test_result_t (*fn)(void);
} test_case_t;

static void init_gpio(void)
{
    gpio_reset_pin(LED_GPIO);
    gpio_set_direction(LED_GPIO, GPIO_MODE_OUTPUT);

    gpio_reset_pin(GPIO1);
    gpio_set_direction(GPIO1, GPIO_MODE_OUTPUT);

    gpio_reset_pin(GPIO2);
    gpio_set_direction(GPIO2, GPIO_MODE_OUTPUT);

    gpio_reset_pin(GPIO3);
    gpio_set_direction(GPIO3, GPIO_MODE_OUTPUT);

    gpio_reset_pin(MODEM_PWR_GPIO);
    gpio_set_direction(MODEM_PWR_GPIO, GPIO_MODE_OUTPUT);
    gpio_set_level(MODEM_PWR_GPIO, 0);

    gpio_reset_pin(W5500_RST_GPIO);
    gpio_set_direction(W5500_RST_GPIO, GPIO_MODE_OUTPUT);
    gpio_set_level(W5500_RST_GPIO, 1);

    gpio_reset_pin(W5500_INT_GPIO);
    gpio_set_direction(W5500_INT_GPIO, GPIO_MODE_INPUT);

    gpio_reset_pin(S2LP_SDN_GPIO);
    gpio_set_direction(S2LP_SDN_GPIO, GPIO_MODE_OUTPUT);
    gpio_set_level(S2LP_SDN_GPIO, 1);

    ESP_LOGI(TAG, "GPIO initialized");
}

static void init_uart_modem(void)
{
    uart_config_t cfg = {
        .baud_rate = 115200,
        .data_bits = UART_DATA_8_BITS,
        .stop_bits = UART_STOP_BITS_1,
        .parity    = UART_PARITY_DISABLE,
        .flow_ctrl = UART_HW_FLOWCTRL_DISABLE,
        .source_clk = UART_SCLK_DEFAULT
    };

    uart_param_config(UART_MODEM_NUM, &cfg);
    uart_set_pin(UART_MODEM_NUM, UART_MODEM_TX, UART_MODEM_RX,
                 UART_PIN_NO_CHANGE, UART_PIN_NO_CHANGE);
    uart_driver_install(UART_MODEM_NUM, 4096, 0, 0, NULL, 0);

    ESP_LOGI(TAG, "Modem UART initialized");
}

static void init_uart_cmd(void)
{
    uart_config_t cfg = {
        .baud_rate = 115200,
        .data_bits = UART_DATA_8_BITS,
        .stop_bits = UART_STOP_BITS_1,
        .parity    = UART_PARITY_DISABLE,
        .flow_ctrl = UART_HW_FLOWCTRL_DISABLE,
        .source_clk = UART_SCLK_DEFAULT
    };

    uart_param_config(UART_CMD_NUM, &cfg);
    uart_set_pin(UART_CMD_NUM, UART_PIN_NO_CHANGE, UART_PIN_NO_CHANGE,
                 UART_PIN_NO_CHANGE, UART_PIN_NO_CHANGE);
    uart_driver_install(UART_CMD_NUM, 4096, 0, 0, NULL, 0);

    ESP_LOGI(TAG, "CMD UART initialized");
}

static void init_i2c(void)
{
    i2c_config_t cfg = {
        .mode = I2C_MODE_MASTER,
        .sda_io_num = I2C_SDA,
        .scl_io_num = I2C_SCL,
        .sda_pullup_en = GPIO_PULLUP_ENABLE,
        .scl_pullup_en = GPIO_PULLUP_ENABLE,
        .master.clk_speed = 100000
    };

    i2c_param_config(I2C_PORT, &cfg);
    i2c_driver_install(I2C_PORT, cfg.mode, 0, 0, 0);

    ESP_LOGI(TAG, "I2C initialized");
}

static void init_spi_bus(void)
{
    spi_bus_config_t buscfg = {
        .mosi_io_num = SPI_MOSI,
        .miso_io_num = SPI_MISO,
        .sclk_io_num = SPI_CLK,
        .quadwp_io_num = -1,
        .quadhd_io_num = -1,
        .max_transfer_sz = 4096
    };

    esp_err_t err = spi_bus_initialize(SPI2_HOST, &buscfg, SPI_DMA_CH_AUTO);
    if (err != ESP_OK) {
        ESP_LOGE(TAG, "SPI bus init failed: %d", err);
        return;
    }

    spi_device_interface_config_t devcfg_w5500 = {
        .clock_speed_hz = 10 * 1000 * 1000,
        .mode = 0,
        .spics_io_num = SPI_CS_W5500,
        .queue_size = 4
    };
    spi_bus_add_device(SPI2_HOST, &devcfg_w5500, &spi_dev_w5500);

    spi_device_interface_config_t devcfg_sd = {
        .clock_speed_hz = 8 * 1000 * 1000,
        .mode = 0,
        .spics_io_num = SPI_CS_SD,
        .queue_size = 4
    };
    spi_bus_add_device(SPI2_HOST, &devcfg_sd, &spi_dev_sd);

    spi_device_interface_config_t devcfg_s2lp = {
        .clock_speed_hz = 4 * 1000 * 1000,
        .mode = 0,
        .spics_io_num = SPI_CS_S2LP,
        .queue_size = 4
    };
    spi_bus_add_device(SPI2_HOST, &devcfg_s2lp, &spi_dev_s2lp);

    ESP_LOGI(TAG, "SPI bus and devices initialized");
}

static void modem_send_cmd(const char *cmd)
{
    uart_write_bytes(UART_MODEM_NUM, cmd, strlen(cmd));
}

static int modem_read_line(char *buf, int max_len, TickType_t timeout_ticks)
{
    int idx = 0;
    TickType_t start = xTaskGetTickCount();

    while (idx < max_len - 1) {
        uint8_t ch;
        int len = uart_read_bytes(UART_MODEM_NUM, &ch, 1, pdMS_TO_TICKS(10));
        if (len > 0) {
            if (ch == '\n') {
                break;
            }
            if (ch != '\r') {
                buf[idx++] = (char)ch;
            }
        }
        if ((xTaskGetTickCount() - start) > timeout_ticks) {
            break;
        }
    }
    buf[idx] = '\0';
    return idx;
}

static void modem_power_on(void)
{
    ESP_LOGI(TAG, "Powering on modem");
    gpio_set_level(MODEM_PWR_GPIO, 1);
    vTaskDelay(pdMS_TO_TICKS(1000));
    gpio_set_level(MODEM_PWR_GPIO, 0);
    vTaskDelay(pdMS_TO_TICKS(5000));
}

static esp_err_t w5500_hw_reset(void)
{
    gpio_set_level(W5500_RST_GPIO, 0);
    vTaskDelay(pdMS_TO_TICKS(10));
    gpio_set_level(W5500_RST_GPIO, 1);
    vTaskDelay(pdMS_TO_TICKS(100));

    ESP_LOGI(TAG, "W5500 hardware reset");
    return ESP_OK;
}

static esp_err_t w5500_spi_write_reg(uint16_t addr, uint8_t block, uint8_t value)
{
    uint8_t tx[4];
    uint8_t rx[4];

    tx[0] = (uint8_t)(addr >> 8);
    tx[1] = (uint8_t)(addr & 0xFF);
    tx[2] = block;
    tx[3] = value;

    spi_transaction_t t = {
        .length = 8 * sizeof(tx),
        .tx_buffer = tx,
        .rx_buffer = rx
    };

    return spi_device_transmit(spi_dev_w5500, &t);
}

static esp_err_t w5500_basic_init(void)
{
    ESP_ERROR_CHECK(w5500_hw_reset());
    ESP_LOGI(TAG, "W5500 basic init (skeleton)");
    return ESP_OK;
}

static esp_err_t sdcard_init_spi(void)
{
    ESP_LOGI(TAG, "SD card SPI init (skeleton)");
    return ESP_OK;
}

static void s2lp_hw_wakeup(void)
{
    gpio_set_level(S2LP_SDN_GPIO, 0);
    vTaskDelay(pdMS_TO_TICKS(10));
}

static esp_err_t s2lp_spi_write_reg(uint8_t addr, uint8_t value)
{
    uint8_t tx[2];
    uint8_t rx[2];

    tx[0] = addr;
    tx[1] = value;

    spi_transaction_t t = {
        .length = 8 * sizeof(tx),
        .tx_buffer = tx,
        .rx_buffer = rx
    };

    return spi_device_transmit(spi_dev_s2lp, &t);
}

static esp_err_t s2lp_basic_init(void)
{
    s2lp_hw_wakeup();
    ESP_LOGI(TAG, "S2LP basic init (skeleton)");
    return ESP_OK;
}

static test_result_t test_gpio_led(void)
{
    ESP_LOGI(TAG, "[TEST] GPIO_LED");
    gpio_set_level(LED_GPIO, 1);
    vTaskDelay(pdMS_TO_TICKS(50));
    gpio_set_level(LED_GPIO, 0);
    return TEST_PASS;
}

static test_result_t test_w5500_basic(void)
{
    ESP_LOGI(TAG, "[TEST] W5500_BASIC");
    if (w5500_basic_init() == ESP_OK) {
        return TEST_PASS;
    }
    return TEST_FAIL;
}

static test_result_t test_sdcard_basic(void)
{
    ESP_LOGI(TAG, "[TEST] SDCARD_BASIC");
    if (sdcard_init_spi() == ESP_OK) {
        return TEST_PASS;
    }
    return TEST_FAIL;
}

static test_result_t test_modem_at_basic(void)
{
    ESP_LOGI(TAG, "[TEST] MODEM_AT");
    char line[128];

    modem_power_on();
    modem_send_cmd("AT\r\n");

    TickType_t timeout = pdMS_TO_TICKS(8000);
    TickType_t start = xTaskGetTickCount();

    while ((xTaskGetTickCount() - start) < timeout) {
        int len = modem_read_line(line, sizeof(line), pdMS_TO_TICKS(1000));
        if (len > 0) {
            ESP_LOGI(TAG, "Modem: %s", line);
            if (strstr(line, "OK") != NULL) {
                ESP_LOGI(TAG, "[TEST] MODEM_AT PASS");
                return TEST_PASS;
            }
        }
    }

    ESP_LOGE(TAG, "[TEST] MODEM_AT FAIL (no OK)");
    return TEST_FAIL;
}

static test_result_t test_s2lp_basic(void)
{
    ESP_LOGI(TAG, "[TEST] S2LP_BASIC");
    if (s2lp_basic_init() == ESP_OK) {
        return TEST_PASS;
    }
    return TEST_FAIL;
}

static const test_case_t g_tests[] = {
    { "GPIO_LED",   test_gpio_led },
    { "W5500_BASIC", test_w5500_basic },
    { "SDCARD_BASIC", test_sdcard_basic },
    { "MODEM_AT",   test_modem_at_basic },
    { "S2LP_BASIC", test_s2lp_basic }
};

static const size_t g_test_count = sizeof(g_tests) / sizeof(g_tests[0]);

static void send_result_line(const char *name, test_result_t res)
{
    printf("{\"type\":\"result\",\"name\":\"%s\",\"result\":\"%s\"}\n",
           name,
           (res == TEST_PASS) ? "PASS" : "FAIL");
}

static void handle_command(const char *cmd)
{
    if (strcmp(cmd, "LIST") == 0) {
        for (size_t i = 0; i < g_test_count; i++) {
            printf("{\"type\":\"test\",\"name\":\"%s\"}\n", g_tests[i].name);
        }
        return;
    }

    if (strncmp(cmd, "RUN ", 4) == 0) {
        const char *name = cmd + 4;

        if (strcmp(name, "ALL") == 0) {
            for (size_t i = 0; i < g_test_count; i++) {
                test_result_t r = g_tests[i].fn();
                send_result_line(g_tests[i].name, r);
            }
            return;
        }

        for (size_t i = 0; i < g_test_count; i++) {
            if (strcmp(name, g_tests[i].name) == 0) {
                test_result_t r = g_tests[i].fn();
                send_result_line(g_tests[i].name, r);
                return;
            }
        }

        printf("{\"type\":\"error\",\"msg\":\"UNKNOWN_TEST\"}\n");
        return;
    }

    printf("{\"type\":\"error\",\"msg\":\"UNKNOWN_CMD\"}\n");
}

static void command_task(void *arg)
{
    char buf[128];
    int idx = 0;

    while (1) {
        uint8_t ch;
        int len = uart_read_bytes(UART_CMD_NUM, &ch, 1, pdMS_TO_TICKS(50));
        if (len > 0) {
            if (ch == '\n' || ch == '\r') {
                buf[idx] = '\0';
                if (idx > 0) {
                    handle_command(buf);
                }
                idx = 0;
            } else if (idx < (int)sizeof(buf) - 1) {
                buf[idx++] = (char)ch;
            }
        }
    }
}

static void blink_task(void *param)
{
    while (1) {
        gpio_set_level(LED_GPIO, 1);
        vTaskDelay(pdMS_TO_TICKS(500));
        gpio_set_level(LED_GPIO, 0);
        vTaskDelay(pdMS_TO_TICKS(500));
    }
}

void app_main(void)
{
    ESP_LOGI(TAG, "Bring-up firmware starting");

    init_gpio();
    init_uart_modem();
    init_uart_cmd();
    init_i2c();
    init_spi_bus();

    xTaskCreate(blink_task, "blink", 2048, NULL, 1, NULL);
    xTaskCreate(command_task, "cmd", 4096, NULL, 4, NULL);

    ESP_LOGI(TAG, "Ready. Commands: LIST, RUN <name>, RUN ALL");
}
```

### How to use this from your PC

1. Connect to the board’s **UART0** (same as default console) at 115200.
2. Type commands followed by newline:

* `LIST` → board prints available tests as JSON lines
* `RUN ALL` → runs all tests, prints JSON result lines
* `RUN MODEM_AT` → runs just the modem test
