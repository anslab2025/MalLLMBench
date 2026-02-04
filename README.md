
# ansllab2025 - Malware Sample Dataset  
# ansllab2025 - 恶意软件样本数据集

This repository contains labeled datasets for mining malware (**miner**) and ransomware classification research. The labels are stored in CSV files, and corresponding static analysis reports are provided as HTML files to support feature extraction and behavioral analysis.

本仓库包含用于挖矿恶意软件（**miner**）和勒索软件（**ransomware**）分类研究的标注数据集。标签以 CSV 文件形式存储，并提供对应的静态分析 HTML 报告，便于特征提取与行为分析。

---

## 📂 Directory Structure / 目录结构

```
.
├── miner/                     # Mining malware dataset / 挖矿恶意软件数据集
│   └── *.csv                  # Labels: 0 = non-miner, 1 = miner / 标签：0=非挖矿，1=挖矿
├── miner_string/              # Static analysis reports (HTML) for miner samples / 挖矿样本的静态分析报告
│   └── VirusShare_*.html      # Detailed analysis pages / 详细分析页面
├── ransomware/                # Ransomware dataset / 勒索软件数据集
│   └── *.csv                  # Labels: 0 = non-ransomware, 1 = ransomware / 标签：0=非勒索，1=勒索
├── ransomware_string/         # Static analysis reports (HTML) for ransomware samples / 勒索软件样本的静态分析报告
│   └── VirusShare_*.html      # Same format as above / 格式同上
└── README.md
```

---

## 📊 Data Description / 数据说明

### 1. CSV Files (`miner/`, `ransomware/`)  
### 1. CSV 文件（`miner/`、`ransomware/`）

- Each row represents one malware sample.  
  每行代表一个恶意软件样本。
- Columns: `hash` (file hash), `label` (class label).  
  列包括：`hash`（文件哈希）、`label`（类别标签）。
- **Label Definition**:  
  **标签定义**：
  - `0`: Normal or non-target type (e.g., non-mining / non-ransomware)  
    `0`：正常或非目标类型（例如：非挖矿 / 非勒索）
  - `1`: Target malware (mining or ransomware)  
    `1`：目标恶意软件（挖矿或勒索）

> **Example / 示例**:  
> ```
> hash,label
> 0000ee89e9109377eb763c07aa0cdf9d,1
> a1b2c3d4e5f6...,0
> ```

---

### 2. HTML Analysis Reports (`*_string/`)  
### 2. HTML 分析报告（`*_string/`）

Each HTML file corresponds to a sample’s static analysis report. Filename format:

每个 HTML 文件对应一个样本的静态分析报告。文件命名规则如下：

```
VirusShare_{hash}_{YYYYMMDD}_{HHMMSS}_analysis.html
```

> **Example / 示例**:  
> `VirusShare_0000ee89e9109377eb763c07aa0cdf9d_20250516_062308_analysis.html`

#### Included Sections / 包含内容：

- **File Informations** – Basic metadata (size, timestamp, etc.)  
  **文件信息** – 基础元数据（大小、时间戳等）
- **File Version Info** – Version resource details  
  **文件版本信息** – 版本资源详情
- **File Checksums** – MD5, SHA1, SHA256 hashes  
  **文件校验和** – MD5、SHA1、SHA256 等
- **Sections** – PE section headers  
  **节区信息** – PE 文件节表
- **Strings** – Extracted printable strings  
  **字符串** – 提取的可打印字符串
- **Imports** – Imported functions (DLLs & APIs)  
  **导入表** – 导入的函数（DLL 与 API）
- **Suspicious APIs** – Potentially malicious API calls  
  **可疑 API** – 可能恶意的 API 调用
- **Exports** – Exported functions  
  **导出表** – 导出的函数

These reports are generated from automated reverse engineering or static analysis tools, and are suitable for large-scale feature engineering.

这些报告由自动化逆向工程或静态分析工具生成，适用于大规模特征工程。

---

## 💡 Usage Recommendations / 使用建议

- Combine `miner.csv` with HTML files in `miner_string/` to train mining detection models.  
  将 `miner.csv` 与 `miner_string/` 中的 HTML 文件结合，训练挖矿检测模型。
- Similarly, use `ransomware.csv` + `ransomware_string/` for ransomware classification.  
  类似地，使用 `ransomware.csv` 与 `ransomware_string/` 进行勒索软件分类。
- Parse key fields such as **Strings**, **Imports**, and **Suspicious APIs** to build feature vectors.  
  解析 **字符串**、**导入表** 和 **可疑 API** 等关键字段，构建特征向量。
- Recommended tools: Python with `pandas`, `BeautifulSoup`, or `lxml` for batch processing.  
  推荐工具：使用 Python 的 `pandas`、`BeautifulSoup` 或 `lxml` 批量处理数据。

---

## 🔍 Data Source / 数据来源

All samples originate from public threat intelligence platforms (e.g., VirusShare), cleaned and labeled for research purposes. Original file hashes are preserved for traceability.

所有样本均来源于公开威胁情报平台（如 VirusShare），经清洗与标注后用于研究。原始文件哈希值已保留，便于溯源。
