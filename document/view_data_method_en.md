## 1. Download ParquetViewer from https://github.com/mukunku/ParquetViewer/releases
![parquetViewer_download.png](images/parquetViewer_download.png)

## 2. Double-click the downloaded executable to launch the interface
![parquetView_UI.png](images/parquetView_UI.png)

## 3. Drag the data file `xxxx.parquet` into the software interface and click "Done"; or click **File** in the menu bar, select **Open Folder**, choose the folder containing all the `.parquet` files, and then click "Done".
![drag_data_to_parquetView.png](images/drag_data_to_parquetView.png)  
![data_view.png](images/data_view.png)

## 4. Data Field Descriptions

| Field Name         | Type     | Meaning                   | Description                                                                 | Example Value         | 取值范围 / 可选值                                               |
|--------------------|----------|---------------------------|------------------------------------------------------------------------------|------------------------|------------------------------------------------------------------|
| `id`               | Long     | Sample ID                 | Unique identifier, auto-incremented from 0                                   | 42                     | 0 ~ numRows - 1                                                  |
| `dopant_elements`  | String   | Dopant elements & content | JSON string, keys are dopant elements, values are molar percentages (1–10%) | {"Y":5,"Sc":3}         | 1–3 elements from [Y, Sc, Yb, Dy, Bi], concentration: 1–10              |
| `sintering_temp`   | Integer  | Sintering Temperature (°C)| Sintering temperature during material preparation                           | 1350                   | 1200 ~ 1600                                                      |
| `sintering_time`   | Integer  | Sintering Time (h)        | Duration of high-temperature sintering process                              | 8                      | 1 ~ 10                                                           |
| `test_temp`        | Integer  | Test Temperature (°C)     | Temperature at which conductivity is measured                               | 800                    | 500 ~ 1000                                                       |
| `conductivity`     | String   | Conductivity (S/cm)       | Oxygen ion conductivity under high temperature                              | 0.001345678912345      | ~1e-6 to 0.009, high-precision small decimal string                                |
| `crystal_phase`    | String   | Crystal Phase             | Crystal structure formed after sintering                                    | "c+t"                  | "c", "t", "c+t", "m+t"                                           |
| `method`           | String   | Forming Method            | Pressing or deposition method of the material                               | "热压"                 | "干压", "热压", "等静压", "流延", "脉冲激光沉积"               |
| `material_source`  | String   | Material Source           | Source of raw material: lab-synthesized or commercial                       | "实验室自制"           | "实验室自制", "商品化"                                          |

## 5. Data Augmentation Strategy

To increase the quantity, enhance the diversity, and improve model robustness under complex processing conditions, a structured synthetic data augmentation strategy was developed. Based on the original fields, it uses rule-based definitions and physics-inspired models to generate representative new samples within valid bounds. All augmented data maintain realistic, statistically sound, and physically interpretable characteristics.

### 5.1 Logic of Augmented Field Generation

The following fields are key variables during augmentation. Each synthetic sample is generated independently with the logic below:

#### **1. `dopant_elements` (Dopant elements and concentrations)**

- **Element selection**: Randomly select 1–3 elements from [Y, Sc, Yb, Dy, Bi];
- **Concentration assignment**: Assign each selected element an integer between 1–10 (mol%);
- **JSON structure**: Combine into JSON string, e.g., `{"Y":6,"Sc":3}`.

#### **2. `sintering_temp` (Sintering temperature)**

- Uniformly sample an integer within **1200–1600°C**;
- Simulates microstructure evolution and ionic transport under varying thermal conditions.

#### **3. `sintering_time` (Sintering time)**

- Random integer between **1–10 hours**;
- Represents the impact of duration on density and conductivity.

#### **4. `test_temp` (Test temperature)**

- Uniformly sample integer between **500–1000°C**;
- Indicates oxygen ion mobility under high working temperatures.

#### **5. `conductivity` (Conductivity)**

This is the target variable and is estimated with the following method:

- **Base value**: Starting from $10^{-3}$ S/cm;
- **Dopant term**: Sum of dopant concentrations × 1e-4 (positive contribution);
- **Temperature term**: Add $1\times10^{-5}$ for each degree above 500°C;
- **Noise term**: Add Gaussian noise with std = 1e-4 to simulate measurement uncertainty;
- **Lower bound**: Final value is clipped at $10^{-6}$ to avoid outliers;
- **Output format**: High-precision string with ~15 significant digits (e.g., `"0.001345678912345"`).

> Final formula:
> \[
> \text{conductivity} = \max\left(10^{-6}, 10^{-3} + \text{dopant\_sum} \times 10^{-4} + (\text{test\_temp} - 500) \times 10^{-5} + \epsilon \right)
> \]
> where $\epsilon$ is the Gaussian noise term.

#### **6. `crystal_phase` (Crystal phase structure)**

- Randomly choose one from: `"c"`, `"t"`, `"c+t"`, `"m+t"`;
- Represents possible post-sintering phase combinations.

#### **7. `method` (Forming method)**

- Randomly select from: `"干压"`, `"热压"`, `"等静压"`, `"流延"`, `"脉冲激光沉积"` (in original Chinese);
- Models differences in sample preparation technique.

#### **8. `material_source` (Material source)**

- Randomly select between `"实验室自制"` and `"商品化"` (in original Chinese);
- Indicates the source of raw materials affecting reproducibility.

---

### 5.2 Sample Generation Workflow

Each augmented sample is generated by the following steps:

1. Randomly determine dopant element combinations and concentrations;
2. Set sintering temperature, time, and test temperature;
3. Calculate conductivity using physics-inspired formula;
4. Randomly select crystal phase, forming method, and material source;
5. Assemble into a full data record and write to dataset.

---

### 5.3 Advantages of Augmented Data

| Feature             | Description                                                                 |
|---------------------|-----------------------------------------------------------------------------|
| **Physical Plausibility** | All values are within engineering-valid ranges and follow ceramic behavior |
| **Structural Consistency** | Field structure is identical to original dataset, enabling direct merge   |
| **High Diversity**       | Rich sampling across combinations and ranges to expand parameter space      |
| **Reproducibility**      | Deterministic generation via random seed enables exact reproduction         |
| **Versatility**          | Usable for regression modeling, feature selection, generalization tests, etc. |

---
