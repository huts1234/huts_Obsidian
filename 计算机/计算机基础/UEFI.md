# UEFI（统一可扩展固件接口）深度解析

---

## 一、UEFI 核心概念
**UEFI（Unified Extensible Firmware Interface）** 是替代传统 BIOS 的现代固件标准，由 Intel 于 2005 年提出并成为行业规范。其核心改进包括：

| **对比维度**       | **传统 BIOS**                     | **UEFI**                          |
|--------------------|-----------------------------------|-----------------------------------|
| 启动模式           | 仅支持 Legacy（CSM）              | 支持 UEFI 和 Legacy 双模式         |
| 硬盘支持           | MBR 分区表（最大 2TB）             | GPT 分区表（最大 9.4ZB）           |
| 图形界面           | 文本界面（16色）                  | 图形化 GUI（支持鼠标操作）          |
| 启动速度           | 慢（需初始化所有硬件）             | 快（并行初始化 + 优化驱动加载）      |
| 安全机制           | 无                                | Secure Boot（阻止未签名代码运行）    |

---

## 二、UEFI 关键功能与配置

### 1. 进入 UEFI 设置的方法
| **操作系统**       | **操作步骤**                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| Windows 10/11     | 设置 → 更新与安全 → 恢复 → 高级启动 → 立即重新启动 → 疑难解答 → UEFI 固件设置 |
| Linux（Systemd）  | 终端执行 `systemctl reboot --firmware-setup`                                |
| 物理按键           | 开机时按 **Del/F2/F10/Esc**（不同品牌按键不同，如华硕按 Del，戴尔按 F2）     |

### 2. Secure Boot 安全启动
- **工作原理**：验证 EFI 可执行文件（如 `grubx64.efi`）的数字签名，阻止恶意引导程序加载  
- **兼容性问题**：  
  - 安装 Linux 需选择支持 Secure Boot 的发行版（如 Ubuntu）或手动导入签名密钥  
  - 双系统用户可能需要禁用 Secure Boot 以加载非 Windows 系统  

### 3. 启动模式选择
| **模式**          | **适用场景**                                | **配置方法**                                                                 |
|-------------------|-------------------------------------------|-----------------------------------------------------------------------------|
| UEFI 原生模式     | 新设备安装 Windows 10/11 或 Linux          | 禁用 CSM（兼容支持模块）                                                     |
| Legacy（CSM）     | 旧设备运行 DOS 或传统操作系统                | 启用 CSM，设置启动设备为 Legacy 优先                                         |
| UEFI + Legacy 混合 | 新旧设备兼容                                | 同时开启 UEFI 和 CSM，根据启动介质自动选择                                    |

---

## 三、UEFI 与硬盘分区

### 1. GPT vs MBR 分区表
| **特性**          | **MBR**                                  | **GPT（GUID 分区表）**                       |
|-------------------|------------------------------------------|---------------------------------------------|
| 最大硬盘容量      | 2TB                                      | 9.4ZB（理论无上限）                          |
| 分区数量          | 4个主分区（或3主分区+1扩展分区）           | 128个主分区                                  |
| 兼容性            | 所有系统                                  | 需 UEFI 支持（Windows 仅支持 64位系统）       |
| 数据安全          | 无备份                                    | 分区表头双重备份 + CRC32 校验                 |

**转换方法**：  
```bash
# 使用 gdisk 将 MBR 转为 GPT（数据会丢失！）
gdisk /dev/sda
x  # 专家模式
z  # 转换分区表
y  # 确认
```

### 2. 创建 UEFI 系统分区（ESP）
- **要求**：  
  - FAT32 格式（建议 300-500MB）  
  - 挂载点 `/boot/efi`（Linux）或隐藏分区（Windows）  
- **Linux 安装示例**：  
  ```bash
  mkfs.fat -F32 /dev/sda1  # 格式化 ESP 分区
  mount /dev/sda1 /boot/efi
  ```

---

## 四、UEFI 故障排查

### 1. 常见启动错误
| **错误提示**                  | **原因**                          | **解决方案**                                 |
|-------------------------------|-----------------------------------|--------------------------------------------|
| `No bootable device found`     | 启动顺序错误或硬盘未识别           | 检查 BIOS 启动顺序，确认硬盘连接正常          |
| `Secure Boot Violation`        | 加载了未签名的驱动或引导程序        | 禁用 Secure Boot 或导入自定义密钥            |
| `Invalid partition table`      | GPT/MBR 与启动模式不匹配            | 转换分区表或切换 Legacy/UEFI 模式             |
| `EFI stub: Load error`         | Linux 内核未启用 EFI stub 编译       | 重新编译内核或使用 GRUB2 作为引导加载程序      |

### 2. 修复 UEFI 引导（Windows）
```powershell
# 使用安装盘进入命令行
bootrec /fixboot      # 修复启动文件
bootrec /scanos       # 扫描已安装系统
bootrec /rebuildbcd   # 重建 BCD 存储
```

### 3. 修复 UEFI 引导（Linux）
```bash
# 重新安装 GRUB
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
update-grub
```

---

## 五、UEFI 固件更新

### 1. 更新步骤
1. 官网下载对应型号的固件文件（通常为 `.cap` 或 `.bio` 格式）  
2. 将文件复制到 FAT32 格式的 U 盘根目录  
3. 进入 UEFI 设置界面选择 `Flash BIOS` 或 `EZ Flash` 工具  
4. 选择文件并确认更新（切勿断电）  

### 2. 风险提示
- **验证文件完整性**：通过 SHA256 校验和比对  
- **回退机制**：部分厂商支持降级（如华硕 USB BIOS Flashback）  
- **紧急恢复**：若更新失败，使用编程器重写 SPI 闪存芯片  

---

## 六、UEFI 开发与扩展

### 1. UEFI 应用开发
- **工具链**：EDK II（EFI Development Kit II）  
- **示例代码（Hello World）**：  
  ```c
  #include <Uefi.h>
  EFI_STATUS EFIAPI UefiMain(EFI_HANDLE ImageHandle, EFI_SYSTEM_TABLE *SystemTable) {
    SystemTable->ConOut->OutputString(SystemTable->ConOut, L"Hello UEFI World!");
    return EFI_SUCCESS;
  }
  ```
- **编译命令**：  
  ```bash
  build -a X64 -p MyApp/MyApp.dsc -t GCC5
  ```

### 2. 安全研究
- **UEFI 漏洞案例**：  
  - **ThunderSpy**（CVE-2020-2593）：通过物理接口注入恶意固件  
  - **LogoFAIL**（2023年）：利用启动 Logo 图片触发缓冲区溢出  

---

通过深入理解 UEFI 架构，用户可优化系统启动流程、增强安全防护，并为高级开发与维护奠定基础。建议定期检查固件更新并备份关键配置。 🔧🔒