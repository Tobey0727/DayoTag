# DayoTag 固件串口烧录指南

本指南面向希望自行完成首次写入、故障恢复或 DIY 复刻的用户。普通升级仍建议优先使用 DayoTag App 内的 OTA。

DayoTag 使用思澈科技 [SF32LB52x](https://www.sifli.com/zh-hans/sf32lb52x) 系列芯片。DayoTag 的串口完整烧录包内附带**官方按已验证流程封装的下载脚本**——它调用思澈 legacy UART 下载器（ImgDownUart）以 `--compare --verify` 方式写入并核对日志中的 `FINAL_PASS`。请使用烧录包内附带的脚本，不要改用 SDK 环境自行生成的下载脚本：后者在部分 SDK 环境下会改走其他下载工具链，该路径未在 DayoTag 硬件上验证。

> **平台说明：** DayoTag 的烧录流程目前只在 **Windows** 上完整验证过。macOS / Linux 的思澈官方脚本未经 DayoTag 验证，如自行尝试请先做好完整备份，并欢迎通过 Issue 反馈结果。

> **DayoTag 硬件特别说明：** 当前版本的 Type-C 接口只用于引出 UART 串口信号，板上没有集成 USB 转串口芯片，也不是可直接连接电脑的原生 USB 下载口。普通 Type-C 数据线直连电脑不会出现可用烧录串口。烧录时必须根据[嘉立创原理图](https://oshwhub.com/eda_kqtkoxdx/project_bpeoomns)使用外置 **3.3V USB-to-UART** 转接器连接下载串口。

## 1. 准备

需要准备：

- 与当前硬件版本、芯片型号和屏幕类型匹配的 DayoTag 串口完整烧录包。
- 外置 3.3V USB-to-UART 转接器及连接线。
- 稳定的设备供电。
- Windows、macOS 或 Linux 电脑。
- 当前设备固件及重要数据的备份。

从 [GitHub Releases](https://github.com/Tobey0727/DayoTag/releases) 下载对应版本的 `DayoTag-firmware-<version>-serial.zip`，并核对 Release 页面公布的 SHA-256。不要将其他板型、其他屏幕版本或来源不明的固件写入设备。

完整烧录包应包含分区文件、配置及 DayoTag 官方封装的下载脚本。不要拿单个 `.bin` 文件自行猜测地址烧录。

## 2. 连接串口

断电后，按照嘉立创原理图标注连接：

- USB-to-UART `GND` → DayoTag `GND`
- USB-to-UART `TX` → DayoTag `RX`
- USB-to-UART `RX` → DayoTag `TX`

注意：

- 串口逻辑电平必须为 **3.3V**，不要把 5V 串口电平接入芯片。
- 建议让 DayoTag 按正常方式供电，并与转接器共地；不要随意使用转接器的 5V 引脚给设备供电。
- 如果通过 Type-C 形态的转接板接入，也必须确认它按 DayoTag 原理图把对应触点转换成 3.3V UART；普通 Type-C 线不能代替 USB-to-UART 转接器。

连接成功后，电脑通常会显示：

- Windows：`COMx`
- macOS：`/dev/cu.usbserial-*`，不要选择 `/dev/tty.*`
- Linux：`/dev/ttyUSB*` 或 `/dev/ttyACM*`

## 3. 运行下载脚本

解压串口完整烧录包，在脚本所在目录运行包内附带的下载脚本（以烧录包内 README 标注的入口为准，Windows 下为 PowerShell 脚本，需要指定串口号）：

```powershell
.\flash.ps1 -Port COM<你的串口号>
```

随后：

1. 脚本会调用已验证的 UART 下载器开始写入。在它等待设备响应时复位 DayoTag，并在复位后的约 2 秒内进入下载。
2. 等待脚本依次写入固件分区。烧录期间不要断电、拔线或关闭终端。
3. 脚本会核对下载日志：**必须出现 `FINAL_PASS` 才算烧录成功**，脚本在未见到该标记时会直接报错。
4. 烧录完成后让设备重新启动，检查屏幕、蓝牙广播、App 连接和固件版本。

DayoTag 已验证流程的 UART 波特率不应超过 `1000000`。不要自行修改下载脚本里的分区地址、文件列表和时序。

## 4. 常见问题

### 电脑没有出现串口

- 确认使用的是外置 USB-to-UART 转接器，而不是普通 Type-C 数据线。
- 确认转接器驱动已经安装。
- 检查 TX / RX 是否交叉连接，GND 是否共地。
- 检查设备供电和转接器的 3.3V / 5V 档位。

### 一直无法进入下载

SF32LB52x 没有普通 BOOT MODE 引脚，下载工具需要在芯片启动后的短时间窗口内通过 UART 进入下载模式。重新运行脚本，在它等待端口确认时复位设备，并在约 2 秒内开始下载。

### 下载中断或 Verify 失败

- 停止继续写入，保存完整终端输出。
- 检查供电、串口线和波特率后重试。
- 不要执行“全片擦除”，以免清除设备身份、校准值或其他保留数据。
- 如果仍然失败，使用烧录前备份恢复，或通过 GitHub Issue 提供硬件版本、固件版本、串口工具型号和脱敏后的日志。

## 5. 官方资料

- [SF32LB52x 芯片与文档](https://www.sifli.com/zh-hans/sf32lb52x)
- [SiFli-SDK 快速入门](https://docs.sifli.com/projects/sdk/latest/sf32lb52x/quickstart/index.html)
- [SiFli-SDK 环境安装](https://docs.sifli.com/projects/sdk/latest/sf32lb52x/quickstart/install/index.html)
- [SiFli-SDK 编译与下载](https://docs.sifli.com/projects/sdk/latest/en/sf32lb52x/quickstart/build.html)
- [DayoTag 嘉立创硬件资料](https://oshwhub.com/eda_kqtkoxdx/project_bpeoomns)

本仓库发布经过验证的 DayoTag 固件二进制及烧录包。希望基于 SF32LB52x 开发自有固件的用户，请以思澈官方 SDK 和文档为准。
