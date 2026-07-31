# DayoTag 第三方许可证与声明

这份清单基于当前本地 App 与固件候选版本中已经识别到的依赖和内置模型资产整理。由于正式签名 APK 和稳定固件尚未确定，它不是最终 Release 的完整清单。

正式发布时必须：

1. 以最终构建的依赖锁定文件、APK 内容和固件构建清单重新生成清单。
2. 核对实际打包版本、上游地址、许可证和版权声明。
3. 将需要随二进制分发的许可证全文与 NOTICE 一并放入 APK、固件压缩包或 Release 附件。
4. 删除最终产物中没有实际包含的组件，补充本清单尚未覆盖的传递依赖和厂商 SDK。

## App：直接依赖

| 组件 | 当前版本 | 许可证 | 上游 |
|---|---:|---|---|
| React | 19.2.3 | MIT | <https://github.com/facebook/react> |
| React Native | 0.85.0 | MIT | <https://github.com/facebook/react-native> |
| Expo | 56.0.9 | MIT | <https://github.com/expo/expo> |
| Expo Modules Core | 56.0.17 | MIT | <https://github.com/expo/expo> |
| Async Storage | 2.2.0 | MIT | <https://github.com/react-native-async-storage/async-storage> |
| React Native SVG | 15.15.4 | MIT | <https://github.com/software-mansion/react-native-svg> |

以上只是当前 `package.json` 中的直接运行时依赖。最终 APK 还需要从锁定文件和 Android 依赖图生成完整传递依赖清单。

## App：内置运行时与模型资产

| 组件或资产 | 当前标识 | 许可证 | 上游与说明 |
|---|---|---|---|
| sherpa-onnx Android runtime | 1.13.4（项目自建） | Apache-2.0 | <https://github.com/k2-fsa/sherpa-onnx>；版权归 sherpa-onnx contributors |
| CAM++ speaker embedding model | 当前候选模型 | Apache-2.0 | <https://modelscope.cn/models/iic/speech_campplus_sv_zh-cn_16k-common> |
| CAM++ ONNX conversion | 当前候选转换版本 | Apache-2.0 | <https://huggingface.co/Alkd/campplus-zh-cn-common-200k-onnx> |
| X-ASR 2026 Offline INT8 | 当前候选模型 | Apache-2.0 | <https://github.com/Gilgamesh-J/X-ASR>；分发来源见 sherpa-onnx 模型 Release |
| Silero VAD | 当前候选模型 | MIT | <https://github.com/snakers4/silero-vad>；语音活动检测模型 |
| pyannote segmentation-3.0 | 当前候选转换版本 | MIT | <https://github.com/pyannote/pyannote-audio>；重叠语音检测模型 |
| ONNX Runtime | 1.20.0 | MIT | <https://github.com/microsoft/onnxruntime>；模型推理运行时 |
| sqlite-vec | 0.1.9 | Apache-2.0 或 MIT（按 MIT 使用） | <https://github.com/asg017/sqlite-vec>；端上向量检索扩展 |
| Opus | 1.5.2（内置源码编译） | BSD-3-Clause | <https://opus-codec.org/>；音频解码 |
| Fresco / Hermes | 随 React Native 版本 | MIT | Meta 开源组件，随 RN 运行时进入 APK |
| androidx.work 等 AndroidX 组件 | 随最终构建锁定 | Apache-2.0 | Google AndroidX，后台任务调度等 |

APK 不内置任何字体：界面全部使用设备系统字体，因此不涉及字体许可义务。

当前 CAM++ 候选文件记录的 SHA-256：

`5c8574f7930ddde1abe72dac31ee5ff7fb7fd30cc8fbf0302881c4cb32f4a4ad`

## 固件：当前已识别组件

| 组件 | 当前标识 | 许可证 | 版权或上游 |
|---|---|---|---|
| RNNoise | 当前构建内置 | BSD-3-Clause（二进制分发须随附声明全文） | Mozilla / Jean-Marc Valin / Xiph.Org 等；<https://github.com/xiph/rnnoise> |
| Opus | 1.4 | BSD-3-Clause（同上，另附专利许可说明） | Copyright 2001–2011 Xiph.Org、Skype Limited、Octasic 及其他列名贡献者；<https://opus-codec.org/> |
| FlashDB | 2.1.1 | Apache-2.0 | Armink 等贡献者；<https://github.com/armink/FlashDB> |
| FAL | FlashDB 随附版本 | Apache-2.0 | Flash Abstraction Layer，随 FlashDB 许可文件分发 |
| RT-Thread FAL 移植层 | 随 SDK 版本 | Apache-2.0 | RT-Thread 社区 |
| SiFli SDK BSP / 例程（含 bootloader、DFU 与 LCPU BLE 镜像来源） | 随 SDK 版本 | Apache-2.0 | 思澈科技；<https://github.com/OpenSiFli/SiFli-SDK> |
| GNU Arm 工具链运行时（newlib-nano / libgcc） | 14.2.1 | BSD 系声明 / GPL-3.0 含 GCC Runtime Library Exception | 静态链接进各固件镜像；Exception 明确允许二进制随本项目条款分发 |

RNNoise 与 Opus 的 BSD-3 条款要求版权声明、条件列表和免责声明**随二进制分发的材料一并复现**，仅给链接不满足义务——固件发布包必须携带随固件源码维护的 `THIRD-PARTY-NOTICES.txt` 全文。

固件分发形态两种：OTA 包（内置于 APK）与串口完整烧录包。串口包内的四个分区文件均已核清来源（2026-07-31 对照实际构建逐文件核查）：bootloader 与 DFU 由项目构建自 SDK 的 Apache-2.0 例程源码现场编译，分区表由本项目板级配置生成，主固件内嵌的 LCPU BLE 镜像同样来自 SDK 的 Apache-2.0 源码树；蓝牙控制器本体在芯片 ROM 中，不属于任何分发文件。固件正式发布前仍须记录所用 SDK 与工具链的具体版本，使上述署名可追溯。

## 许可证全文

当前 APK 工程在 `assets/licenses/` 下按组件目录随包保存许可全文与 NOTICE（sherpa-onnx、CAM++、X-ASR、Silero VAD、pyannote、ONNX Runtime、sqlite-vec，及共享的 Apache-2.0 全文），固件侧的 BSD-3 声明全文随固件源码维护并须随固件二进制一并分发。正式 Release 需要继续保留这些文件。

MIT、Apache-2.0、BSD-3 和其他实际使用组件的许可证全文，应以最终构建中对应上游版本的原文为准，不能只用本页摘要替代。
