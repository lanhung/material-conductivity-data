# ParquetViewer Instruction & Data Description

## 1. Load Data with ParquetViewer

### 1.1 Download ParquetViewer

Download from [https://github.com/mukunku/ParquetViewer/releases](https://github.com/mukunku/ParquetViewer/releases)

![parquetViewer_download.png](images/parquetViewer_download.png)

### 1.2 Launch the Software

Double-click the downloaded executable to open the main interface:

![parquetView_UI.png](images/parquetView_UI.png)

### 1.3 Load Parquet Data

- **Method 1**: Drag your `xxxx.parquet` file directly into the viewer interface;
- **Method 2**: Go to **File** → **Open Folder**, select the folder containing `.parquet` files;
- Click **Done** to load the data.

![drag_data_to_parquetView.png](images/drag_data_to_parquetView.png)  
![data_view.png](images/data_view.png)

---

## 2. Data Field Descriptions

| Field Name         | Type     | Meaning                   | Description                                                                 | Example Value         | Value Range / Options                                              |
|--------------------|----------|---------------------------|------------------------------------------------------------------------------|------------------------|---------------------------------------------------------------------|
| `id`               | Long     | Sample ID                 | Unique identifier, auto-incremented from 0                                   | 42                     | 0 ~ numRows - 1                                                     |
| `dopant_elements`  | String   | Dopant elements & content | JSON string, keys are dopant elements, values are molar percentages (1–10%) | {"Y":5,"Sc":3}         | 1–3 elements from [Y, Sc, Yb, Dy, Bi], concentration: 1–10 mol%     |
| `sintering_temp`   | Integer  | Sintering Temperature (°C)| Sintering temperature during material preparation                           | 1350                   | 1200 ~ 1600                                                         |
| `sintering_time`   | Integer  | Sintering Time (h)        | Duration of high-temperature sintering process                              | 8                      | 1 ~ 10                                                              |
| `test_temp`        | Integer  | Test Temperature (°C)     | Temperature at which conductivity is measured                               | 800                    | 500 ~ 1000                                                          |
| `conductivity`     | String   | Conductivity (S/cm)       | Oxygen ion conductivity under high temperature                              | 0.001345678912345      | ~1e-6 to 0.009, high-precision floating number as string            |
| `crystal_phase`    | String   | Crystal Phase             | Crystal structure formed after sintering                                    | "c+t"                  | "c", "t", "c+t", "m+t"                                              |
| `method`           | String   | Forming Method            | Pressing or deposition method of the material                               | "热压"                 | "干压", "热压", "等静压", "流延", "脉冲激光沉积" (in Chinese)    |
| `material_source`  | String   | Material Source           | Source of raw material: lab-synthesized or commercial                       | "实验室自制"           | "实验室自制", "商品化" (in Chinese)                                 |

---

## 3. Data Augmentation Strategy

To improve data quantity, diversity, and robustness under complex process conditions, a structured synthetic data augmentation strategy was implemented. Based on the original field schema, the augmented data are generated using rule-based logic and physics-inspired estimation, maintaining physical plausibility and statistical validity.

### 3.1 Field-wise Augmentation Logic

| Field               | Augmentation Logic                                                                                     |
|---------------------|--------------------------------------------------------------------------------------------------------|
| **`dopant_elements`** | Randomly choose 1–3 elements from [Y, Sc, Yb, Dy, Bi]; assign each an integer 1–10; store as JSON string |
| **`sintering_temp`**  | Uniformly sample integer between 1200 and 1600                                                        |
| **`sintering_time`**  | Random integer between 1 and 10 hours                                                                 |
| **`test_temp`**       | Uniform integer between 500 and 1000                                                                  |
| **`conductivity`**    | Estimated using a formula with base + dopant + temperature + noise terms; string formatted to ~15 digits |
| **`crystal_phase`**   | Randomly choose from {"c", "t", "c+t", "m+t"}                                                         |
| **`method`**          | Randomly select from five Chinese-formatted forming methods                                           |
| **`material_source`** | Randomly select from "实验室自制" and "商品化" (Chinese: lab-made or commercial)                        |

> Conductivity is estimated using:  
> \[
> \text{conductivity} = \max\left(10^{-6}, 10^{-3} + \text{dopant\_sum} \times 10^{-4} + (\text{test\_temp} - 500) \times 10^{-5} + \epsilon \right)
> \]  
> where $\epsilon$ is Gaussian noise with standard deviation $1\times10^{-4}$.

---

### 3.2 Sample Generation Workflow

1. Sample dopant elements and assign concentrations;
2. Sample sintering and test temperatures;
3. Compute conductivity based on physical model;
4. Randomly assign crystal phase, forming method, and material source;
5. Package into a complete data record.

---

### 3.3 Advantages of Augmented Data

| Feature                | Description                                                                 |
|------------------------|-----------------------------------------------------------------------------|
| **Physical Plausibility** | All values conform to realistic ceramic material conditions                  |
| **Field Consistency**     | Matches structure of original dataset for seamless integration             |
| **High Diversity**        | Broad sampling of parameter space across multiple variables                |
| **Reproducibility**       | Deterministic generation enabled via fixed random seed                     |
| **Flexible Application**  | Useful for regression tasks, feature engineering, robustness evaluation     |

---
