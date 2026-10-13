# clinterplotation

钻孔数据三维空间插值 —— 多种插值方法的对比实验。

## 任务

以钻孔（点）采样数据为基础，插值出土壤重金属元素 **Cd、Pb、Cu、Ni** 在三维空间中的连续分布，
并对比不同方法的精度（R²、RMSE、MAE）。

## 数据

`data/data.xlsx`

- `usecols=[1..7]`：7 个特征；
- `usecols=[9..12]`：4 个目标元素（Cd / Pb / Cu / Ni）；
- 数据按每 4 行一个点位组织（同一坐标下的 4 个深度层），
  重组成 `[点位, 深度层, 特征]` / `[点位, 深度层, 元素]` 的三维结构后再送入模型。

## 方法

| 文件 | 方法 |
| --- | --- |
| `Interpolib.py` / `3DKriging.py` | 三维普通克里金（`pykrige.OrdinaryKriging3D`，球状 / 线性变异函数） |
| `Randforest.py` | `RandomForestRegressor`（`n_estimators=100, max_depth=10`），4 个元素分别建模 |
| `3DCNN.py` | Keras `Conv3D` + `Conv3DTranspose` 编解码网络 |
| `Interpolib.py` | 另含 SVR（`sklearn.svm.SVR`）作为对照 |
| `pearson.py` / `point.py` / `patch.py` | 相关性分析、点位处理、三维滑窗切片 |
| `draw.py` / `map.py` / `result_draw.py` | 损失曲线与结果成图 |
| `savedata.py` / `test.py` | 结果导出与测试 |

## 权重

`Model/` 下为四个元素各自的最佳 3D CNN 权重：`best_model_Cd.h5`、`best_model_Cu.h5`、
`best_model_Ni.h5`、`best_model_Pb.h5`。

## 环境

```
numpy
pandas
scikit-learn
pykrige
openpyxl
tensorflow / keras
matplotlib
```

## 使用

脚本内均使用相对路径，请在仓库根目录执行，例如：

```
python Interpolib.py    # 三维克里金
python Randforest.py    # 随机森林
python 3DCNN.py         # 3D CNN 推理与精度评估（R² / RMSE / MAE）
```

具体输出路径见各脚本内的 `to_excel(...)` / `savefig(...)`。

---

> 本 README 依据仓库现有代码整理，细节如与实现不符请以代码为准。
