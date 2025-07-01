# 快速游戏识别系统（含罗技鼠标宏自动写入）

## 项目简介

本项目实现了游戏内武器与配件的自动识别，并可一键写入罗技鼠标宏配置文件（weapon.lua），支持一键切换武器配置。适用于吃鸡等射击类游戏，极大提升自动压枪和配件适配效率。

---

## 运行环境与依赖

### 1. Python 独立环境包

- 推荐使用本项目自带的独立 Python 环境，无需全局安装，解压即用。
- 下载地址（任选其一）：
  - [PY.zip (推荐)](https://wwsm.lanzouo.com/iMB0O300bvhi)  
  - [完整环境包文件夹](https://wwsm.lanzouo.com/b0nzapn1i) 密码: xswl

### 2. 罗技鼠标驱动

- 必须安装 Logitech G HUB 或 Logitech Gaming Software。
- 下载地址：
  - [罗技驱动安装包.exe](https://wwsm.lanzouo.com/iveVZ300bwwj)

### 3. Python 代码包

- 包含所有识别主程序、模板、配置文件等。
- 下载地址：
  - [Python代码.zip](https://wwsm.lanzouo.com/i603d300c0sj)
  - [完整环境包文件夹](https://wwsm.lanzouo.com/b0nzapn1i) 密码: xswl

---

## 快速开始

1. **解压所有下载的压缩包**（建议放在英文路径下，如 D:\GameDetect）。
2. **安装罗技驱动**（如已安装可跳过）。
3. **无需全局安装Python，直接用自带环境运行**。

### 启动方法

- 进入 `Python代码` 文件夹，双击或命令行运行 `game.py`：
  ```bash
  # 进入Python环境
  cd Python代码
  # Windows下推荐
  ..\PY\python.exe game.py
  # 或直接双击 game.py 运行
  ```

- 首次运行如提示缺少依赖包，可在 `PY` 目录下用 pip 安装：
  ```bash
  ..\PY\python.exe -m pip install -r requirements.txt
  ```

---

## 功能说明

- **Tab**：识别当前屏幕武器与配件，自动写入 weapon.lua（默认写入第一把武器）。
- **1/2**：手动切换写入武器1/2的配置到 weapon.lua。
- **组合键（Ctrl+Tab、Alt+Tab）**：不会触发识别，防止误操作。
- **背包未打开时**：不会覆盖写入，终端会显示上一次的识别配置。

---

## 配置文件说明

### weapon.lua

- 自动生成，内容示例（图片名/英文名）：
  ```lua
  weapon_name = "M416"
  muzzles = "bc1"
  muzzle = "bc1"
  grips = "angle"
  scopes = "x4"
  stocks = "normal"
  shoot = "None"
  poses = "stand"
  scope_zoom = "1.0"
  bag = "None"
  car = "None"
  ```

- **字段说明**：
  - weapon_name：武器名
  - muzzles：枪口配件（图片名）
  - grips：握把配件（图片名）
  - scopes：瞄具配件（图片名）
  - stocks：枪托配件（图片名）
  - 其余字段可保持默认

---

## 常见问题

- **无法识别/写入？**  
  请确保游戏分辨率与模板一致，且已正确安装罗技驱动和Python环境。
- **依赖缺失？**  
  用自带PY环境运行，或用pip安装 requirements.txt 里的依赖。
- **罗技宏无反应？**  
  weapon.lua 路径需与宏脚本一致，且需重载宏配置。

---

## 国内高速下载

- [完整文件夹下载（含环境、代码、驱动）](https://wwsm.lanzouo.com/b0nzapn1i) 密码: xswl
- [PY.zip（独立Python环境）](https://wwsm.lanzouo.com/iMB0O300bvhi)
- [罗技驱动安装包.exe](https://wwsm.lanzouo.com/iveVZ300bwwj)
- [Python代码.zip](https://wwsm.lanzouo.com/i603d300c0sj)

---

如有问题请联系开发者或在终端查看详细报错信息。  
祝你游戏愉快，压枪无忧！ 
