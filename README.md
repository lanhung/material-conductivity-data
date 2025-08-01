## 📦 数据集说明（中文）

本项目提供两个不同规模的材料导电率数据集，均采用 7-Zip 工具进行分卷压缩：

- **`1kw/` 文件夹**：包含 **1,000 万条数据**
- **`1ww/` 文件夹**：包含 **1 亿条数据**

> 🔗 推荐使用 [7-Zip 工具](https://www.7-zip.org/a/7z2500-x64.exe) 进行解压。

数据采用 **Parquet 列式存储格式**，并使用 **Snappy 压缩算法**，适用于高效读取与处理。

详细内容（字段定义、数据浏览方法、数据增强策略等）请参考下方文档：

📄 中文文档地址：  
[view_data_method_zh.md](https://github.com/lanhung/material-conductivity-data/blob/master/document/view_data_method_zh.md)

---

## 📦 Dataset Description (English)

This project provides two large-scale material conductivity datasets, each compressed using the 7-Zip tool:

- **`1kw/` folder**: contains **10 million records**
- **`1ww/` folder**: contains **100 million records**

> 🔗 Please use [7-Zip](https://www.7-zip.org/a/7z2500-x64.exe) to extract the compressed files.

The datasets are stored in **Parquet columnar format** with **Snappy compression**, supporting efficient read and processing.

For more details (field definitions, data browsing instructions, augmentation strategy, etc.), please refer to the following documentation:

📄 English Documentation:  
[view_data_method_en.md](https://github.com/lanhung/material-conductivity-data/blob/master/document/view_data_method_en.md)
