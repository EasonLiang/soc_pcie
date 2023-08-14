# (1/3) 模拟x86设备运行pcie设备驱动，读取英特尔的82540EM和82574L千兆以太网卡信息
**模拟x86设备，添加82540EM和82574L千兆以太网卡，以测试pcie总线驱动。**<br>
- (01)对82540EM千兆以太网卡：读取类/供应商/设备ID，从EEPROM读取默认MAC地址。<br>
- (02)对82574L千兆以太网卡：读取类/供应商/设备ID，读取MAC，并通过写寄存器方式更改MAC地址；
![插图01](pcie_e1000e_set_mac.png "image for process to modify IPv4 MAC address")

# (2/3) 模拟 ARM Cortex-M3 芯片
## (01) 查询支持的设备列表（仅保留eason_m3设备/开发板）：
> `[eason@Arch]$ ./qemu-system-arm -M help`
&#8195;Supported machines are:<br>
&#8195;eason_m3             eason's machine ( cortex-m3 , tested)<br>
&#8195;none                 empty machine<br>
## (02) 启动qemu-system-arm仿真eason_m3设备，运行demo程序（startup.bin）
- 备注：目前仅参考PCIE设备linux驱动的数据结构，从程序架构上优化了芯片SOC数据结构。
> `[eason@Arch]$ ./qemu-system-arm -M eason_m3 -kernel startup.bin -nographic`<br>
&#8195;[01] Entry from main!<br>
&#8195;[ISR] Execute in ISR to respond to interrupt from [source: demo] !<br>
&#8195;QEMU: Terminated (按下快捷键退出QEMU)<br>
![插图02](emulateSOC_color.png "colorful image for emulated arm cortex-M3")

# (3/3) 模拟 RISCV MCU:: rv32imac 芯片
## (01) 查询支持的设备列表（仅保留easonLiang_rv32imac和eason_demo_riscv的设备/开发板）：
> `[eason@Arch soc_pcie]$ ./qemu-system-riscv32 -M help`<br>
&#8195;Supported machines are:<br>
&#8195;easonLiang_rv32imac  EasonLiang's RISC-V dev board (mcu::rv32imac)<br>
&#8195;eason_demo_riscv     eason's demo Board for RISC-V<br>
&#8195;none                 empty machine<br>
## (02) 启动qemu-system-riscv32仿真easonLiang_rv32imac设备，运行hello_world程序（hello_rv32imac.elf）
> `[eason@Arch soc_pcie]$ ./qemu-system-riscv32 -M easonLiang_rv32imac -nographic -kernel hello_rv32imac.elf`<br>
&#8195;Hello, World!<br>
&#8195;Language : C<br>
&#8195;Author : EasonLiang<br>
&#8195;Env Type : Regressive Integration<br>
&#8195;Build Platform : ArchLinux @ 20230814<br>
&#8195;Running Platform : Emulated RISC-V<br>
![插图03](rv32imac_color.png "colorful image for emulated RISC-V MCU with ISA RV32IMAC")
## (03) 为easonLiang_rv32imac开发板编译应用程序：
`riscv64-unknown-elf-gcc -march=rv32imac -mabi=ilp32 -mcmodel=medlow -ffunction-sections -fdata-sections \
				--specs=nano.specs -O0 -g -nostartfiles -nostdlib \
				-T sdk_easonLiang/rv32imac/hello.lds -L sdk_easonLiang/rv32imac/libs/ -Wl,--gc-sections \
				hello_rv32imac.c -Wl,--start-group -lc -lgcc -lm -lmetal -lmetal-gloss -Wl,--end-group -o hello_rv32imac.elf`
