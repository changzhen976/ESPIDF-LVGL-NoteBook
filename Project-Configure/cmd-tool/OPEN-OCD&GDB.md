# OPEN-OCD & GDB-Debugger

----

> ESPIDF工具链中带有OpenOCD与Especially32相关的配置文件

## OpenOCD

### 安装位置

OpenOCD安装位置`idf_tools_path\Espressif\tools\openocd-esp32`

### 配置文件

OCD脚本配置文件(以esp32c3为例): `idf_tools_path\Espressif\tools\openocd-esp32\v0.12.0-esp32-20230921\openocd-esp32\share\openocd\scripts\board\esp32c3-builtin.cfg`

***esp32c3-builtin.cfg*** :

```shell
# SPDX-License-Identifier: GPL-2.0-or-later
#
# Example OpenOCD configuration file for ESP32-C3 connected via builtin USB-JTAG adapter.
#
# For example, OpenOCD can be started for ESP32-C3 debugging on
#
#   openocd -f board/esp32c3-builtin.cfg
#

# Source the JTAG interface configuration file
source [find interface/esp_usb_jtag.cfg]
# Source the ESP32-C3 configuration file
source [find target/esp32c3.cfg]
```

***interface/esp_usb_jtag.cfg*** :

```shell
# SPDX-License-Identifier: GPL-2.0-or-later
#
# ESP32-C3 builtin USB-JTAG adapter
#

adapter driver esp_usb_jtag

espusbjtag vid_pid 0x303a 0x1001
espusbjtag caps_descriptor 0x2000

#max speed
adapter speed 40000
```

***target/esp32c3.cfg*** :

```shell
# SPDX-License-Identifier: GPL-2.0-or-later
#

# Source the ESP common configuration file.
source [find target/esp_common.cfg]

# Target specific global variables
set _CHIPNAME 					"esp32c3"
set _CPUTAPID 					0x00005c25
set _ESP_ARCH					"riscv"
set _ONLYCPU					1
set _ESP_SMP_TARGET				0
set _ESP_SMP_BREAK 				0
set _ESP_EFUSE_MAC_ADDR_REG  	0x60008844

# Set workarea address and size
# stub flasher may need a lot of memory in case of compressed writes to flash (~107KB):
# - for apptrace: 2x16KB up buffers + 32KB down buffer
# - for uncompression: 32KB for unzip buffer size + 11KB for inflator data structs
# TODO: In general when up buffers are swapped apptrace copies `host->target` data from new up buffer to down buffer to free space for `target->host` data.
# In case of flash writes we use apptrace transfers in one direction only. So we can avoid copying and re-use up buffer instead of down one.
set _WA_ADDR				0x3FC80000
set _WA_SIZE				0x24000

# Target specific functions should be implemented for each riscv chips.
proc esp32c3_wdt_disable { } {
    # Halt event can occur during config phase (before "init" is done).
    # Ignore it since mww commands don't work at that time.
    if { [string compare [command mode] config] == 0 } {
        return
    }

    # Timer Group 0 & 1 WDTs
    mww 0x6001f064 0x50D83AA1
    mww 0x6001F048 0
    mww 0x60020064 0x50D83AA1
    mww 0x60020048 0
    # RTC WDT
    mww 0x600080a8 0x50D83AA1
    mww 0x60008090 0
    # SWD
    mww 0x600080b0 0x8F1D312A
    mww 0x600080ac 0x84B00000
}

proc esp32c3_soc_reset { } {
	global _RISCV_DMCONTROL

    # This procedure does "digital system reset", i.e. resets
    # all the peripherals except for the RTC block.
    # It is called from reset-assert-post target event callback,
    # after assert_reset procedure was called.
    # Since we need the hart to to execute a write to RTC_CNTL_SW_SYS_RST,
    # temporarily take it out of reset. Save the dmcontrol state before
    # doing so.
    riscv dmi_write $_RISCV_DMCONTROL 	0x80000001
    # Trigger the reset
    mww 0x60008000 0x9c00a000
    # Workaround for stuck in cpu start during calibration.
    # By writing zero to TIMG_RTCCALICFG_REG, we are disabling calibration
    mww 0x6001F068 0
    # Wait for the reset to happen
    sleep 10
    poll
    # Disable the watchdogs again
    esp32c3_wdt_disable

    # Here debugger reads allresumeack and allhalted bits as set (0x330a2)
    # We will clean allhalted state by resuming the core.
    riscv dmi_write $_RISCV_DMCONTROL 	0x40000001

    # Put the hart back into reset state. Note that we need to keep haltreq set.
    riscv dmi_write $_RISCV_DMCONTROL 	0x80000003
}

proc esp32c3_memprot_is_enabled { } {
    # IRAM0 PMS lock, SENSITIVE_CORE_X_IRAM0_PMS_CONSTRAIN_0_REG
    if { [get_mmr_bit 0x600C10A8 0] != 0 } {
        return 1
    }
    # DRAM0 PMS lock, SENSITIVE_CORE_X_DRAM0_PMS_CONSTRAIN_0_REG
    if { [get_mmr_bit 0x600C10C0 0] != 0 } {
        return 1
    }
    return 0
}

create_esp_target $_ESP_ARCH

$_TARGETNAME_0 riscv expose_csrs 2016=mpcer
$_TARGETNAME_0 riscv expose_csrs 2017=mpcmr
$_TARGETNAME_0 riscv expose_csrs 2018=mpccr
$_TARGETNAME_0 riscv expose_csrs 2051=cpu_gpio_oen
$_TARGETNAME_0 riscv expose_csrs 2052=cpu_gpio_in
$_TARGETNAME_0 riscv expose_csrs 2053=cpu_gpio_out

```
‍

‍
