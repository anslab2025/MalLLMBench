# MalLLMBench: A Dataset Benchmark for LLM-assisted Malware Analysis

This repository contains the datasets for **MalLLMBench**, a benchmark dataset designed for evaluating LLM-assisted malware analysis methods. The benchmark focuses on two malware categories: **cryptojacking (mining) malware** and **ransomware**, with samples organized using a temporal laboratory-to-wild separation to support non-i.i.d. evaluation.

本仓库包含 **MalLLMBench** 的数据集，这是一个用于评估 LLM 辅助恶意软件分析方法的基准数据集。基准聚焦于两类恶意软件：**挖矿恶意软件**与**勒索软件**，样本采用时间先后的 lab/wild 划分，支持非独立同分布 (non-i.i.d.) 评估。

---

## Data Access / 数据访问

**Access to the original PE binary files requires an application.** Please fill out the application form (`docs/binary_file_access_application.docx` for English, `docs/binary_file_access_application_cn.docx` for Chinese) and submit it as instructed in the form.

**获取原始 PE 二进制文件需要提交申请。** 请填写申请表（英文版 `docs/binary_file_access_application.docx`，中文版 `docs/binary_file_access_application_cn.docx`）并按要求提交。

> The static analysis HTML reports and CSV label files in this repository are publicly accessible without application.

> 本仓库中的静态分析 HTML 报告和 CSV 标签文件无需申请即可公开访问。

---

## Directory Structure / 目录结构

```
.
├── miner/                          # Cryptojacking (mining) malware dataset / 挖矿恶意软件数据集
│   ├── lab_label.csv              # Lab set labels (6,000 samples)
│   └── wild_label.csv             # Wild set labels (15,423 samples)
├── miner_string/                   # Static analysis HTML reports for miner samples
│   └── VirusShare_{hash}_{YYYYMMDD}_{HHMMSS}_analysis.html
├── ransomware/                     # Ransomware dataset
│   ├── lab_label.csv              # Lab set labels (6,000 samples)
│   └── wild_label.csv             # Wild set labels (13,318 samples)
├── ransomware_string/              # Static analysis HTML reports for ransomware samples
│   └── VirusShare_{hash}_{YYYYMMDD}_{HHMMSS}_analysis.html
├── docs/                           # Documentation & supplementary materials
│   ├── binary_file_access_application.docx       # English version
│   └── binary_file_access_application_cn.docx    # 中文版
├── reprset/                        # Representative subset for efficient LLM evaluation / 代表性子集
│   ├── Reprset_miner.csv          # Mining representative subset (2,000 samples)
│   └── Reprset_ransomware.csv  # Ransomware representative subset (2,000 samples)
├── LICENSE
├── CITATION.cff
└── README.md
```

---

## Data Description / 数据说明

### Sample Distribution / 样本分布

| Environment | Mining | Non-mining | Total | Ransomware | Non-ransomware | Total |
|:---|---:|---:|---:|---:|---:|---:|
| **Lab** | 2,000 | 4,000 | 6,000 | 2,000 | 4,000 | 6,000 |
| **Wild** | 6,000 | 12,000 | 18,000 | 5,141 | 10,282 | 15,423 |
| **Reprset** | 666 | 1,334 | 2,000 | 666 | 1,334 | 2,000 |
| **Total** | 8,000 | 16,000 | 24,000 | 7,141 | 14,282 | 21,423 |

> **Note**: The lab set was collected from February to August 2021, and the wild set from August to September 2021, with a temporal boundary at 2021-08-22.

### 1. CSV Label Files / CSV 标签文件

Each CSV file contains two columns:

| Column | Description |
|:---|:---|
| `filename` | Sample identifier in the format `VirusShare_{md5_hash}` |
| `label` | Class label: `0` = non-target (negative), `1` = target malware (positive) |

**Label definitions / 标签定义**:
- **miner/**: `1` = mining malware, `0` = non-mining (other malware families used as negative samples)
- **ransomware/**: `1` = ransomware, `0` = non-ransomware

> **Example**:
> ```csv
> filename,label
> VirusShare_0000ee89e9109377eb763c07aa0cdf9d,1
> VirusShare_2a01235356d4ddb0a49b678426b52836,0
> ```

### 2. HTML Analysis Reports / HTML 分析报告

Each CSV entry has a corresponding HTML file in the `*_string/` directory. The HTML filename embeds the hash plus a timestamp:

```
VirusShare_{hash}_{YYYYMMDD}_{HHMMSS}_analysis.html
```

**Important**: The `filename` column in the CSV contains only `VirusShare_{hash}` (without timestamp). To locate the corresponding HTML report, perform a prefix match:

```python
import glob
html_files = glob.glob(f"miner_string/VirusShare_{hash}_*_analysis.html")
```

#### Report Sections / 报告包含内容:

| Section | Description |
|:---|:---|
| File Informations | Basic metadata (size, type, compilation timestamp) |
| File Version Info | Version resource details |
| File Checksums | MD5, SHA1, SHA256, SHA512 hashes |
| Sections | PE section headers |
| Strings | Extracted printable strings |
| Imports | Imported functions (DLLs & APIs) |
| Suspicious APIs | Potentially malicious API calls |
| Exports | Exported functions |
| Resources | Resource entries |
| VirusTotal Results | Multi-engine detection (may be empty if rate-limited) |

> **Note**: Some sections may be empty or show error messages (e.g., VirusTotal `403 Forbidden`) if the analysis tool encountered rate limits or the sample lacked corresponding data.

### 3. Representative Subset (Reprset) / 代表性子集

The Reprset is a clustering-based representative subset designed to reduce LLM inference cost while preserving distributional coverage. Each CSV file contains two columns (`hash`, `label`), same format as the lab/wild labels.

| File | Task | Positive | Negative | Total |
|:---|:---|---:|---:|---:|
| `reprset/Reprset_miner.csv` | Cryptojacking | 666 | 1,334 | 2,000 |
| `reprset/Reprset_ransomware.csv` | Ransomware | 668 | 1,332 | 2,000 |

> The Reprset is derived from the Wild set via 1D-CNN feature extraction, PCA dimensionality reduction, and K-Means clustering with proportional sampling.

---

## Lab/Wild Temporal Split / Lab/Wild 时间划分

A core design principle of MalLLMBench is **temporal separation** to prevent information leakage and enable realistic evaluation under distribution shift:

- **Lab set**: Samples collected from February to August 2021 (controlled environment)
- **Wild set**: Samples collected from August to September 2021 (in-the-wild)
- **Temporal boundary**: 2021-08-22

This split ensures that wild-set samples are chronologically after lab-set samples, simulating real-world deployment where models must generalize to previously unseen malware variants.

---

## Download / 下载

Due to the large number of HTML analysis reports, cloning the full repository may be slow. We recommend:

```bash
# Sparse clone (metadata files only, skip large HTML directories):
git clone --filter=blob:none --sparse https://github.com/anslab2025/MalLLMBench.git
cd MalLLMBench
git sparse-checkout set miner ransomware README.md LICENSE CITATION.cff

# Or download specific files via the GitHub API:
curl -L https://github.com/anslab2025/MalLLMBench/raw/main/miner/lab_label.csv -o lab_label.csv
```

---

## Usage / 使用方法

### Loading labels / 加载标签

```python
import pandas as pd

# Load mining malware labels
lab_labels = pd.read_csv("miner/lab_label.csv")
wild_labels = pd.read_csv("miner/wild_label.csv")

print(f"Lab set: {len(lab_labels)} samples")
print(f"  Mining: {(lab_labels.label == 1).sum()}")
print(f"  Non-mining: {(lab_labels.label == 0).sum()}")
```

### Parsing HTML reports / 解析 HTML 报告

```python
from bs4 import BeautifulSoup
import glob

def load_html_report(hash_value, task="miner"):
    """Load and parse an HTML analysis report by sample hash."""
    pattern = f"{task}_string/VirusShare_{hash_value}_*_analysis.html"
    matches = glob.glob(pattern)
    if not matches:
        raise FileNotFoundError(f"No HTML report found for {hash_value}")
    with open(matches[0], "r", encoding="utf-8") as f:
        return BeautifulSoup(f.read(), "html.parser")

# Example: extract suspicious APIs
soup = load_html_report("0000ee89e9109377eb763c07aa0cdf9d", "miner")
suspicious_section = soup.find("a", id="suspicious")
# ... parse the section as needed
```

---

## Binary File Access / 二进制文件访问

Access to the original PE binary files requires an application. Please fill out the appropriate form below and submit it as instructed:

| Language | File |
|:---|:---|
| English | `docs/binary_file_access_application.docx` |
| 中文 | `docs/binary_file_access_application_cn.docx` |

原始 PE 二进制文件的访问需要申请，请填写对应的申请表并按要求提交。

---

## Reproducibility / 可复现性

All samples originate from public threat intelligence platforms (VirusShare.com). Labels were derived using VirusTotal multi-engine detection results aggregated via AVClass. Original file hashes are preserved for full traceability.

---

## License / 许可证

This dataset is released under the **Creative Commons Attribution 4.0 International (CC BY 4.0)** license. See [LICENSE](LICENSE) for details.

If you use this dataset in your research, please cite the accompanying paper.

---

## Citation / 引用

Citation information will be updated upon publication of the accompanying paper.

引用信息将在配套论文发表后更新。

---

## Contact / 联系

For questions or issues, please open a [GitHub Issue](https://github.com/anslab2025/MalLLMBench/issues).

All samples are sourced from VirusShare.com and labeled for research purposes. Original file hashes are preserved for traceability.

所有样本均来源于 VirusShare.com，经清洗与标注后用于研究。原始文件哈希值已保留，便于溯源。
