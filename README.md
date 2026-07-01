# 阈值处理软件

[![GitHub stars](https://img.shields.io/github/stars/Co-STEM-Lab/threshold_processor_gui?style=flat-square)](https://github.com/Co-STEM-Lab/threshold_processor_gui/stargazers)
[![GitHub release](https://img.shields.io/github/v/release/Co-STEM-Lab/threshold_processor_gui?style=flat-square)](https://github.com/Co-STEM-Lab/threshold_processor_gui/releases)
[![GitHub downloads](https://img.shields.io/github/downloads/Co-STEM-Lab/threshold_processor_gui/total?style=flat-square)](https://github.com/Co-STEM-Lab/threshold_processor_gui/releases)
[![License](https://img.shields.io/github/license/Co-STEM-Lab/threshold_processor_gui?style=flat-square)](./LICENSE)

基于 PyQt6 的图像阈值分割交互式调参工具。支持多种阈值策略与梯度分水岭算法，后台线程处理保证界面流畅，适用于材料显微图像（如合金晶粒）的自动分割与尺寸统计。

## 功能特性

- **10 种阈值策略**：Otsu、Otsu Strict、Otsu Very Strict、Yen、Triangle、Li、Isodata、Mean、Minimum、Multi-Otsu
- **梯度分水岭**：先 Otsu 二值化，再用分水岭算法分离粘连区域
- **实时调参**：高斯模糊 σ、阈值系数、开运算迭代次数、最小面积过滤、腐蚀/膨胀
- **三视图同步**：原始图 / 二值蒙版 / 分割结果，同步缩放平移
- **右键框选放大**：右键拖拽选区即可放大到感兴趣区域
- **晶粒统计**：面积、等效直径、长短轴，含标准差
- **分布图**：内置 Matplotlib 柱状图展示晶粒尺寸分布（含均值 ± 标准差）
- **后台线程**：图像处理在 Worker 线程运行，UI 不卡顿
- **多格式保存**：支持 PNG / TIFF / JPEG / BMP / SVG，可分别保存二值蒙版和分割结果

## 界面截图

```
┌──────────────────────────────────────────────────┐
│  📂 Open  💾 Save  📊 Plot  [Method ▼]  ▶ Run   │
├──────────────────┬─────────────┬─────────────────┤
│    Original      │ Binary Mask │ Segmentation    │
│                  │             │   Result        │
│  (同步缩放/平移)   │             │                 │
├──────────────────┴─────────────┴─────────────────┤
│  Parameters: Sigma | TH Coef | Open Iter | ...   │
├──────────────────────────────────────────────────┤
│  Log                                             │
└──────────────────────────────────────────────────┘
```

## 依赖

| 依赖 | 版本 |
|------|------|
| Python | ≥ 3.10 |
| PyQt6 | ≥ 6.5 |
| NumPy | ≥ 1.24 |
| scikit-image | ≥ 0.22 |
| SciPy | ≥ 1.10 |
| Pillow | ≥ 10.0 |
| Matplotlib | ≥ 3.7 |

## 安装与运行

### 方式一：Python 源码运行

```bash
# 克隆仓库
git clone https://github.com/Co-STEM-Lab/threshold_processor_gui.git
cd threshold_processor_gui

# 创建虚拟环境
python -m venv venv
venv\Scripts\activate   # Windows
# source venv/bin/activate  # macOS/Linux

# 安装依赖
pip install PyQt6 numpy scikit-image scipy Pillow matplotlib

# 运行
python threshold_processor.py
```

### 方式二：打包为 exe（Windows）

```bash
pip install pyinstaller
pyinstaller 阈值处理软件.spec
# 输出在 dist\阈值处理软件.exe
```

## 使用说明

### 基本操作

| 操作 | 说明 |
|------|------|
| `Ctrl+O` | 打开图像（支持 PNG / JPG / TIF / BMP） |
| `Ctrl+S` | 保存结果 |
| `▶ Run` | 执行分割处理 |
| 滚轮 | 缩放图像 |
| 左键拖拽 | 平移图像 |
| 右键拖拽 | 框选放大 |

### 阈值方法

| 方法 | 说明 |
|------|------|
| **Otsu** | 自动计算最佳全局阈值，适合双峰分布图像 |
| **Otsu Strict (×1.10)** | Otsu 阈值 × 1.10，只保留更亮区域 |
| **Otsu Very Strict (×1.20)** | Otsu 阈值 × 1.20，大幅减少检测区域 |
| **Yen** | 基于熵最大化，对较暗图像效果好 |
| **Triangle** | 基于直方图三角测量，适合单峰分布 |
| **Li** | 基于最小交叉熵迭代求解 |
| **Isodata** | 聚类阈值法，迭代分割背景和前景 |
| **Mean** | 使用像素均值作为阈值 |
| **Minimum** | 寻找直方图双峰之间的谷底 |
| **Multi-Otsu** | 多级 Otsu（classes=2） |
| **Gradient Watershed** | Otsu + 分水岭，适合粘连严重的块 |

### 参数说明

| 参数 | 范围 | 默认值 | 说明 |
|------|------|--------|------|
| **Sigma** | 0 ~ 3.0 | 0.8 | 高斯模糊 σ，越大越去噪但越模糊 |
| **TH Coef** | 0.5 ~ 1.5 | 1.0 | 阈值系数，>1 更严格，<1 更宽松 |
| **Open Iter** | 0 ~ 10 | 1 | 开运算迭代次数，切断块之间细薄连接 |
| **Min Area** | 1 ~ 500 | 30 | 最小块面积（像素），过滤噪点 |
| **Expand** | 0 ~ 0.5 | 0.10 | 腐蚀/膨胀强度，>0 腐蚀（块缩小） |
| **Dist Sigma** | 0.5 ~ 10.0 | 3.0 | 仅分水岭有效，距离变换平滑 σ |

### 保存结果

点击 **Save** 后弹出对话框可选择：

- **格式**：PNG / TIFF / JPEG / BMP / SVG
- **内容**：Binary Mask（二值蒙版）/ Segmentation Result（分割结果）
- **保存路径**和文件名前缀

保存时自动生成：
- `{前缀}_binary_mask.{格式}` — 二值蒙版
- `{前缀}_segmentation.{格式}` — 分割着色图

## 文件结构

```
threshold_processor_gui/
├── threshold_processor.py       # 主程序（GUI + 处理逻辑）
├── 阈值处理软件.spec             # PyInstaller 打包配置
├── README.md
├── .gitignore
└── venv/                        # 虚拟环境（本地，不入库）
```

## 引用

如果您使用了本软件，请引用以下文章：

> *文章信息待补充*

## 作者

陈桂森 — 2025–2026

## 许可

MIT License
