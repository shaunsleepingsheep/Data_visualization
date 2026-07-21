# Tổng hợp kiến thức — Phân tích dữ liệu với Python (Python for Data Analysis)

> Tổng hợp từ toàn bộ slide bài giảng (`02_Slides_Bai_giang`): Bài TH01, Bài 01 → Bài 07.
> Dùng để ôn tập và tự kiểm tra nhanh trước khi thi/làm bài tập.

## Mục lục
0. [Bài TH01 — Ôn tập Python cơ bản cho DA](#0-bài-th01--ôn-tập-python-cơ-bản-cho-da)
1. [Bài 01 — Giới thiệu PTDL, Nhập/Xuất dữ liệu](#1-bài-01--giới-thiệu-ptdl-nhậpxuất-dữ-liệu)
2. [Bài 02 — Sắp xếp/Làm sạch dữ liệu (Data Wrangling)](#2-bài-02--sắp-xếplàm-sạch-dữ-liệu-data-wrangling)
3. [Bài 03 — Trực quan hóa dữ liệu (lý thuyết)](#3-bài-03--trực-quan-hóa-dữ-liệu-lý-thuyết)
4. [Bài 04 — Công cụ trực quan hóa (matplotlib/pandas/plotly)](#4-bài-04--công-cụ-trực-quan-hóa-matplotlibpandasplotly)
5. [Bài 05 — Phân tích thăm dò dữ liệu (EDA)](#5-bài-05--phân-tích-thăm-dò-dữ-liệu-eda)
6. [Bài 06 — Mô hình hóa (Regression Modeling)](#6-bài-06--mô-hình-hóa-regression-modeling)
7. [Bài 07 — Đánh giá & tinh chỉnh mô hình](#7-bài-07--đánh-giá--tinh-chỉnh-mô-hình)
8. [Bảng tra nhanh (Cheat sheet tổng hợp)](#8-bảng-tra-nhanh-cheat-sheet-tổng-hợp)

---

## 0. Bài TH01 — Ôn tập Python cơ bản cho DA

### Kiểu dữ liệu
- **Number**: `int`, `float`, `complex`. `_` phân tách hàng nghìn: `5_600_000`. Hàm: `max/min/sum/sorted/round/pow`, `math.sqrt()`.
- **String**: `'...'`/`"..."`, index dương (0→len-1) & âm (-1→-len). `split()`, `rsplit()`, `replace()`, f-string.
- **List** `[ ]`: **mutable** (đổi được phần tử qua index). `append/extend/insert/pop/remove/clear/sort`.
- **Tuple** `( )`: **immutable** — không sửa được sau khi tạo.
- **Dict** `{key: value}`: truy cập qua **key** không qua index. `keys()/values()/items()/get()/setdefault()`. Là nền tảng của `DataFrame`.
- **Set** `{ }`: phần tử duy nhất, không thứ tự. `add/update/remove(lỗi nếu ko có)/discard(không lỗi)/pop`. Dùng loại bỏ trùng lặp, giao tập hợp `&`.
- **Bool**: `0`, rỗng, `None` → falsy; còn lại → truthy.
- **None**: đại diện thiếu giá trị/missing data; kiểm tra bằng `is None` (không dùng `==`).

⚠️ Dễ nhầm: `any()` chỉ cần 1 phần tử True; `all()` cần toàn bộ True.

### Toán tử — độ ưu tiên
`Số học (+ - * / % // **) > So sánh (> < >= <= == !=) > Logic (and/or/not) > Gán (=, +=, ...)`
Toán tử thành viên: `in`, `not in`.

### Cấu trúc điều khiển
- `if / elif / else`
- `for <biến> in <iterable>:` — dùng `range(start, stop, step)` để lặp n lần.
- **Comprehension**: List `[expr for item in it if cond]`, Dict `{k:v for ...}`, Set `{expr for ...}`.
- `while <điều kiện>:` — kiểm tra điều kiện trước, chạy sau.
- `break` (thoát vòng lặp) / `continue` (bỏ qua lần lặp hiện tại).

### Hàm
```python
def func(x, y=1, *args, **kwargs) -> tuple:
    """docstring"""
    return x, y, args, kwargs
```
- `x`: bắt buộc; `y=1`: mặc định; `*args`: nhiều tham số vị trí (tuple); `**kwargs`: tham số key=value (dict).
- **Lambda**: `lambda params: expr` — dùng nhiều trong `filter()`, `map()`, `apply()`, `groupby()`, `sort_values()`.

### Pandas (nhập môn)
- **Series**: mảng 1 chiều có index. **DataFrame**: bảng 2 chiều (rows × columns), mỗi cột là 1 Series.
- Kiểu dữ liệu pandas: `int64/float64`, `object`/`string`, `bool`, `datetime64[ns]`, `timedelta[ns]`, `Period`, `Categorical`.
- ⚠️ Trong Series, `x in s` mặc định kiểm tra theo **index**; muốn kiểm tra theo **giá trị** phải dùng `x in s.values`.
- Toán tử logic trên Series/array: dùng `&`, `|`, `~` (không dùng `and/or/not`).

---

## 1. Bài 01 — Giới thiệu PTDL, Nhập/Xuất dữ liệu

### Quy trình phân tích dữ liệu
Đặt vấn đề (câu hỏi cần trả lời) → Xác định dữ liệu cần có → Hiểu dữ liệu (ý nghĩa từng cột, kiểu dữ liệu, phạm vi giá trị) → Xác định **Target/Label** (biến mục tiêu) → Chọn thư viện → Nhập dữ liệu → Xuất dữ liệu → Phân tích cơ bản.

- **Attribute = Cột = Biến = Feature** (nhiều tên gọi, cùng một khái niệm) — dễ gây nhầm khi đọc đề.
- Ví dụ xuyên suốt: bộ dữ liệu ô tô UCI (Automobile, 205 dòng × 26 cột) — target là **price**.

### Thư viện chính
| Nhóm | Thư viện | Vai trò |
|---|---|---|
| Xử lý dữ liệu | pandas, NumPy | DataFrame/Series; mảng đa chiều |
| Trực quan | matplotlib, seaborn | Vẽ đồ thị cơ bản/nâng cao |
| Thuật toán | scikit-learn, statsmodels | ML (Classification/Regression/Clustering), thống kê |

### Đọc/Ghi dữ liệu (nhớ cặp đối xứng)
| Định dạng | Đọc | Ghi |
|---|---|---|
| csv | `pd.read_csv()` | `df.to_csv()` |
| excel | `pd.read_excel()` | `df.to_excel()` |
| sql | `pd.read_sql()` | `df.to_sql()` |
| hdf | `pd.read_hdf()` | `df.to_hdf()` |
| json | `pd.read_json()` | `df.to_json()` |

```python
df = pd.read_csv(path, names=headers)   # names: đặt tên cột khi file thô không có header
```

### Khám phá dữ liệu ban đầu
- `df.head()/df.tail()` — xem n dòng đầu/cuối.
- `df.dtypes` — kiểm tra kiểu dữ liệu (str→object, int→int64, float→float64, datetime→datetime64).
- `df.info()` — tóm tắt: số dòng/cột, kiểu dữ liệu, số non-null, bộ nhớ.
- `df.describe()` — thống kê mô tả (count/mean/std/min/25%/50%/75%/max), **chỉ áp dụng cột số**, bỏ qua NaN.
- `df.describe(include="all")` — mở rộng cho cả cột object (thêm unique/top/freq).

⚠️ Cột tưởng là số nhưng có ký tự lạ (`?`) → pandas đọc thành `object` — phải kiểm tra kỹ trước khi tính toán.

---

## 2. Bài 02 — Sắp xếp/Làm sạch dữ liệu (Data Wrangling)

- **Data Preprocessing = Data Cleaning = Data Wrangling**. Thống kê: Data Scientist tốn **80% thời gian** làm sạch/tổ chức dữ liệu, chỉ 20% để phân tích.

### Xử lý Missing Values
- Dạng biểu diễn: `?`, `.`, `NaN`, `NaT`, `N/A`, ô trống...
- Cách xử lý: (1) loại bỏ cột/dòng chứa khuyết, (2) thay bằng mean (số)/mode (phân loại), (3) dùng thuật toán (`KNNImputer`).

```python
df.dropna(axis=0, how='any', subset=None, inplace=False)
# axis=0 → xóa DÒNG; axis=1 → xóa CỘT (dễ nhầm ngược)
# how='any' (có ≥1 NA thì bỏ) / 'all' (toàn bộ NA mới bỏ)
df.dropna(subset=["price"], axis=0, inplace=True)

mean = df["horsepower"].mean()
df["horsepower"].replace(to_replace=np.nan, value=mean)
```
⚠️ Không có `inplace=True` → phải gán lại `df = df.dropna(...)`.

### Định dạng dữ liệu (Data Formatting)
- Chuẩn hóa cách biểu diễn (VD tên thành phố ghi nhiều kiểu khác nhau → 1 chuẩn).
- Đổi đơn vị: `df['city-L/100km'] = 235/df['city-mpg']`.
- Đổi kiểu dữ liệu: `df['price'] = df['price'].astype('int')`.

### Chuẩn hóa dữ liệu (Normalization) — 3 công thức
| Phương pháp | Công thức | Miền |
|---|---|---|
| Scale | x_new = x_old / x_max | [0,1] |
| Min-Max | x_new = (x_old − min)/(max − min) | [0,1] |
| Z-score | x_new = (x_old − mean)/std | mean=0, std=1 |

```python
from sklearn.preprocessing import MinMaxScaler, StandardScaler
MinMaxScaler().fit_transform(df[['length']])
StandardScaler().fit_transform(df[['length']])
scipy.stats.zscore(a)
```
Lý do chuẩn hóa: phục vụ thuật toán ML, giảm ảnh hưởng outlier, dễ so sánh, Gradient Descent hội tụ tốt hơn.

### Binning (số → phân loại)
```python
bins = np.linspace(min(df["price"]), max(df["price"]), 4)   # 4 mốc = 3 bin
group_names = ['Thấp', 'Vừa', 'Cao']
df['price-bin'] = pd.cut(df['price'], bins, labels=group_names, include_lowest=True)
```
⚠️ `include_lowest=True` để không bỏ sót giá trị nhỏ nhất.

### Encoding (phân loại → số)
```python
pd.get_dummies(df['fuel-type'])   # one-hot: mỗi giá trị → 1 cột 0/1
```
Các loại khác (nêu tên): Integer Encoding, Frequency Encoding, **Ordinal Encoding** (dùng khi biến có thứ tự), Mapping.

### Pandas cheat sheet bổ sung
`pd.melt(df)` (wide→long), `df.pivot()` (long→wide), `pd.concat([...], axis=0/1)` (nối dòng/cột).

---

## 3. Bài 03 — Trực quan hóa dữ liệu (lý thuyết)

### Vai trò
EDA hỗ trợ → truyền đạt dữ liệu rõ ràng → chia sẻ độ chính xác → hỗ trợ ra quyết định.
Tiêu chí một trực quan tốt: **Effective (hiệu quả) – Attractive (hấp dẫn) – Impactive (tác động)**.

### Phân loại biểu đồ theo MỤC ĐÍCH (không phải theo kiểu dữ liệu)
| Mục đích | Biểu đồ tiêu biểu |
|---|---|
| Độ phân bố | Histogram, Density, Boxplot, Violin, Ridgeline |
| Tương quan (2 biến) | Scatterplot, Bubble plot, Heatmap, 2D density |
| So sánh nhóm | Barplot, Boxplot, Lollipop, Radar |
| So sánh với tổng thể | Stacked bar, Pie, Doughnut, Treemap, Venn |
| Tiến triển theo thời gian | Line, Area, Streamgraph, Timeseries |
| Địa lý | Map, Choropleth, Bubble map |
| Luồng dịch chuyển | Sankey, Chord, Network, Arc diagram |

### Kiến trúc Matplotlib (3 tầng — hay hỏi thi)
1. **Backend layer**: FigureCanvas (vùng vẽ), Renderer (cách vẽ), Event (sự kiện).
2. **Artist layer**: mọi thứ vẽ ra là **Artist**. **Primitive Artist** (Line2D, Rectangle, Text...) vs **Container Artist** (Figure, Axes, Axis, Tick) — container có thể lồng nhau (Figure→Axes→Line2D/Axis→Tick).
3. **Scripting layer (pyplot)**: giao diện dễ dùng, giống MATLAB — cách viết phổ biến nhất (`import matplotlib.pyplot as plt`).

### `plot()` cơ bản
- `fmt` string = `'[color][marker][line]'` (VD `'ro'`=đỏ+tròn, `'g-'`=xanh lá+nét liền).
- `Series.plot(kind=...)`: line(mặc định)/bar/barh/pie/area/hist/box/kde(density).
- `DataFrame.plot(kind=...)`: thêm 2 loại chỉ DataFrame mới có: **scatter**, **hexbin**.

⚠️ Đọc Excel có header/footer mô tả thừa → dùng `skiprows`/`skipfooter`:
```python
pd.read_excel('Canada.xlsx', sheet_name='...', skiprows=range(20), skipfooter=2)
```

---

## 4. Bài 04 — Công cụ trực quan hóa (matplotlib/pandas/plotly)

### Quy trình 5 bước vẽ biểu đồ
Câu hỏi cần trả lời → Hiểu dữ liệu → Xác định thông điệp chính → Chọn loại chart → Dùng màu/kích thước/nhãn để làm nổi bật thông điệp.

### Xử lý dữ liệu hay dùng trước khi vẽ
`set_index()`, `df.loc[...]`, `df.sum(axis=1)`, `sort_values()`, `.transpose()`, `groupby().sum()`.

### Các loại biểu đồ & code mẫu
```python
# Line — thể hiện xu hướng theo thời gian
df.loc['Haiti', 1990:2013].plot(kind='line')

# Area — tổng tích lũy theo thời gian
df.plot(kind='area', stacked=True)   # chồng dồn; stacked=False: chồng trong suốt

# Histogram — phân phối tần suất 1 biến liên tục
count, bin_edges = np.histogram(df_can[2010])
df_can[2010].plot(kind='hist', xticks=bin_edges)

# Bar — so sánh giá trị giữa danh mục
df.plot(kind='bar')     # dọc
df.plot(kind='barh')    # ngang
df.plot(kind='bar', stacked=True)  # cột chồng

# Pie — tỉ lệ % thành phần
plt.pie(value, labels=labels, autopct='%1.1f%%')
plt.axis('equal')   # bắt buộc để không méo thành elip

# Box — phân bố (Min, Q1, Median, Q3, Max, Outlier); IQR = Q3−Q1
df.plot(kind='box')

# Scatter — quan hệ giữa 2 biến
df.plot(kind='scatter', x='year', y='total')
```
⚠️ `autopct='%1.1f%%'`: `%%` in ra ký tự `%`; `1.1f` = số thực 1 chữ số thập phân.

### Dashboard nhiều biểu đồ (hay hỏi thi)
```python
fig, axs = plt.subplots(2, 3, figsize=(15, 10))
axs[0,0].scatter(x, y); axs[0,0].set_title('Scatter')
axs[1,2].hist(y, bins=15); axs[1,2].set_title('Histogram')
plt.tight_layout()
```
- Công cụ dashboard: **Dash by Plotly** (web tương tác), **Streamlit**, **Jupyter Dash**, matplotlib `subplots`.

### Waffle chart & Word cloud
- Waffle: lưới ô vuông thể hiện tỉ lệ/tiến độ mục tiêu.
- Word cloud: chữ càng to = từ xuất hiện càng nhiều; cần làm sạch text (stop-word) trước khi tạo.

---

## 5. Bài 05 — Phân tích thăm dò dữ liệu (EDA)

### Quy trình EDA
Tóm tắt đặc tính chính → hiểu dữ liệu → tìm mối quan hệ giữa các biến → chọn ra biến quan trọng ảnh hưởng target.

### Thống kê mô tả — 4 nhóm
**1) Central Tendency**
- Mean (nhạy outlier) > Median (bền với outlier, 50% dữ liệu nhỏ hơn) > Mode (giá trị xuất hiện nhiều nhất).
- Mean=Median=Mode → đối xứng; Mode<Median<Mean → lệch phải; ngược lại → lệch trái.

**2) Dispersion (độ phân tán)**
- Range = Max − Min (nhạy outlier).
- Quartile: Q1, Q2=Median, Q3; **IQR = Q3 − Q1**.
- Variance: s² = Σ(Xᵢ−X̄)²/(n−1) (dùng n−1, không phải n).
- Std = √variance. CV = σ/μ × 100%.
- Boxplot: whisker trên = Q3+1.5×IQR, whisker dưới = Q1−1.5×IQR → ngoài khoảng này là **outlier**.

**3) Shape**
- Skewness (độ lệch): lệch trái/cân đối/lệch phải (dựa vào mean).
- Kurtosis (độ nhọn): Platykurtic (<0, thấp) / Mesokurtic (=0, chuẩn) / Leptokurtic (>0, nhọn — nhiều outlier).

**4) Correlation**
⚠️ **Tương quan ≠ nhân quả**, và không có tính bắc cầu.
- Hệ số Pearson (r): gần ±1 → tương quan mạnh; gần 0 → không có quan hệ.
```python
Pearson_coef, p_value = stats.pearsonr(df['horsepower'], df['price'])
```
- Bảng mức độ mạnh/yếu: |r| 0.8–1 mạnh, 0.5–0.8 vừa, 0.3–0.5 yếu, 0–0.3 không.
- P-value: <0.001 Strong, <0.05 Moderate, <0.1 Weak, >0.1 No (không có ý nghĩa thống kê).
- `sns.regplot()` — trực quan tương quan; `sns.heatmap()` — ma trận tương quan nhiều biến.

### Gom nhóm (Grouping)
```python
df_grp = df[['drive-wheels','body-style','price']].groupby(['drive-wheels','body-style'], as_index=False).mean()
df_pivot = df_grp.pivot(index='drive-wheels', columns='body-style')
plt.pcolor(df_pivot, cmap='RdBu'); plt.colorbar()
df['drive-wheels'].value_counts()
```

### ANOVA (Analysis of Variance)
- Dùng khi biến phân loại có **>2 nhóm** (t-test dùng cho 2 nhóm/biến binary).
- F-test = biến động giữa nhóm / biến động trong nhóm. F lớn → ảnh hưởng mạnh.
```python
fvalue, pvalue = stats.f_oneway(groupA, groupB, groupC)
# p_value < 0.05 → có sự khác biệt đáng kể giữa các nhóm
```

⚠️ Quy tắc chọn biến quan trọng: biến số → dùng |r| (loại nếu -0.3 đến 0.3); biến phân loại → dùng ANOVA (giữ nếu p<0.05).

---

## 6. Bài 06 — Mô hình hóa (Regression Modeling)

### Simple Linear Regression (SLR)
`y = b0 + b1*x` (b0=intercept, b1=slope).
```python
from sklearn.linear_model import LinearRegression
lm = LinearRegression()
X = df[['highway-mpg']]   # PHẢI là DataFrame 2D, không phải Series
Y = df['price']
lm.fit(X, Y)
Yhat = lm.predict(X)
lm.intercept_   # b0
lm.coef_        # b1
```

### Multiple Linear Regression (MLR)
`Y = b0 + b1x1 + b2x2 + ... `
```python
Z = df[['horsepower','curb-weight','engine-size','highway-mpg']]
lm.fit(Z, df['price'])
```

### Đánh giá bằng trực quan
- **Regression plot** (`sns.regplot`): tương quan + đường hồi quy.
- **Residual plot** (`sns.residplot`): residual = y thực − ŷ dự đoán.
  - Trải ngẫu nhiên quanh 0 → mô hình tuyến tính phù hợp.
  - Dạng cong (U) → dữ liệu phi tuyến, cần đổi mô hình.
  - Hình phễu (phương sai không đều) → mô hình không chính xác.
- **Distribution plot** (`sns.distplot`): so sánh phân phối Yhat vs Y thực — dùng khi có nhiều biến (MLR).

### Polynomial Regression
```python
# đơn biến
f = np.polyfit(x, y, 3); p = np.poly1d(f)

# đa biến (2 bước)
from sklearn.preprocessing import PolynomialFeatures
pr = PolynomialFeatures(degree=2, include_bias=False)
X_poly = pr.fit_transform(df[['horsepower','curb-weight']])
lm.fit(X_poly, df['price'])
```
⚠️ `include_bias=False` bỏ cột hằng số 1 (LinearRegression tự tính intercept). Bậc càng cao → dễ overfit.

Best practice thứ tự: **Chuẩn hóa (StandardScaler) → PolynomialFeatures → LinearRegression**.

### Pipeline
```python
from sklearn.pipeline import Pipeline
Input = [('scale', StandardScaler()), ('polynomial', PolynomialFeatures(include_bias=False)), ('model', LinearRegression())]
pipe = Pipeline(Input)
pipe.fit(Z, y); ypipe = pipe.predict(Z)
```

### Thang đo: MSE & R²
```python
from sklearn.metrics import mean_squared_error
mean_squared_error(df['price'], Yhat)   # càng nhỏ càng tốt

lm.score(X, y)   # R², càng gần 1 càng tốt
```
R² = 1 − (MSE của mô hình / MSE của đường trung bình Ȳ).

⚠️ **Điểm dễ nhầm quan trọng**: MSE của MLR/Polynomial luôn ≤ MSE của SLR vì càng nhiều tham số càng khớp dữ liệu **training** tốt hơn → không có nghĩa là mô hình tốt hơn khi dự đoán dữ liệu mới (nguy cơ overfitting) → cần đánh giá thêm bằng train/test split (Bài 07).

---

## 7. Bài 07 — Đánh giá & tinh chỉnh mô hình

### Train/Test Split
```python
from sklearn.model_selection import train_test_split
x_train, x_test, y_train, y_test = train_test_split(x_data, y_data, test_size=0.3, random_state=0)
```
- Train (~70%) để xây mô hình; Test (~30%) để đánh giá (dữ liệu mô hình "chưa thấy").
- Sau khi chọn được mô hình/tham số tốt → train lại trên **toàn bộ** dữ liệu để triển khai.

### Cross-Validation (k-Fold)
- Chia k phần; mỗi vòng lấy 1 phần làm test, k-1 phần làm train; lặp k lần rồi lấy trung bình → dùng dữ liệu hiệu quả hơn train/test split.
```python
from sklearn.model_selection import cross_val_score, cross_val_predict
scores = cross_val_score(lr, x_data, y_data, cv=3); np.mean(scores)   # trả về SCORE mỗi fold
yhat = cross_val_predict(lr, x_data, y_data, cv=3)                     # trả về GIÁ TRỊ DỰ ĐOÁN
```
⚠️ Dễ nhầm: `cross_val_score` → điểm số; `cross_val_predict` → giá trị dự đoán.

### Overfitting vs Underfitting
- **Overfitting**: tốt ở train, kém ở test (mô hình học cả nhiễu, đường hồi quy "vặn vẹo").
- **Underfitting**: kém cả ở train lẫn test (mô hình quá đơn giản, hoặc thiếu dữ liệu).
- Đồ thị chọn mô hình: trục hoành = bậc đa thức (order), trục tung = error.
  - Train error luôn giảm khi tăng bậc.
  - Test error giảm rồi tăng (hình chữ U) → **điểm thấp nhất = bậc tối ưu**. Trái = underfit, phải = overfit.

```python
Rsqu_test = []
for n in [1,2,3,4]:
    pr = PolynomialFeatures(degree=n)
    x_train_pr = pr.fit_transform(x_train[['horsepower']])
    x_test_pr = pr.fit_transform(x_test[['horsepower']])
    lr.fit(x_train_pr, y_train)
    Rsqu_test.append(lr.score(x_test_pr, y_test))
```

### Ridge Regression (Regularization)
- Hồi quy tuyến tính mở rộng, dùng khi: cần chống overfitting, hoặc biến độc lập đa cộng tuyến.
- **alpha (α)**: tham số điều chuẩn — phạt độ lớn hệ số hồi quy.
  - α=0 → giống Linear Regression thường (dễ overfit).
  - α tăng dần → đường mô hình mượt hơn, giảm ảnh hưởng outlier/nhiễu.
  - α quá lớn → underfitting.
```python
from sklearn.linear_model import Ridge
RidgeModel = Ridge(alpha=0.1)
RidgeModel.fit(X, y)
```
- Chọn alpha tối ưu bằng cross-validation trên 1 dãy alpha (`np.logspace(-3, 4, 60)`) → bản chất là **Grid Search**.

### Các chỉ số đánh giá khác
- **RMSE** = √(Σ(p−a)²/n)
- **MAE** = Σ|p−a|/n
- **RSE**, **RAE**: dạng tương đối so với đường trung bình.

⚠️ R² tính riêng trên tập train **sẽ luôn tăng** theo độ phức tạp mô hình → không dùng để chọn mô hình; phải đánh giá trên **test/cross-validation**.

---

## 8. Bảng tra nhanh (Cheat sheet tổng hợp)

### Đọc/ghi dữ liệu
| Đọc | Ghi |
|---|---|
| `pd.read_csv()` | `df.to_csv()` |
| `pd.read_excel()` | `df.to_excel()` |
| `pd.read_sql()` | `df.to_sql()` |
| `pd.read_json()` | `df.to_json()` |

### Khám phá & làm sạch
`df.dtypes`, `df.info()`, `df.describe()`, `df.isna()`, `df.dropna()`, `df.fillna()/replace()`, `df.astype()`, `pd.cut()`, `pd.get_dummies()`.

### Chuẩn hóa
Scale (÷max) · Min-Max ((x-min)/(max-min)) · Z-score ((x-mean)/std).

### Thống kê & tương quan
Mean/Median/Mode · Range/IQR/Variance/Std · Skewness/Kurtosis · Pearson r + p-value · ANOVA (F, p-value) · `groupby()`/`pivot()`.

### Biểu đồ (matplotlib/pandas `kind=`)
`line, bar, barh, hist, box, kde/density, area, pie, scatter, hexbin` — `sns.regplot/residplot/distplot/heatmap/boxplot`.

### Mô hình (sklearn)
`LinearRegression`, `PolynomialFeatures`, `Pipeline`, `Ridge` · `train_test_split`, `cross_val_score/predict`, `GridSearchCV` (α tối ưu) · `mean_squared_error`, `.score()` (R²).

### Các "bẫy" dễ nhầm cần nhớ
1. `dropna(axis=0)` xóa **dòng**, `axis=1` xóa **cột**.
2. `X` truyền vào `.fit()/.predict()` của sklearn phải là **2D** (`df[['col']]`), không phải Series.
3. Tương quan **≠** nhân quả.
4. MSE/R² trên **train** luôn cải thiện khi tăng độ phức tạp mô hình → không dùng để chọn mô hình, phải dùng **test/cross-validation**.
5. `cross_val_score` → điểm số; `cross_val_predict` → giá trị dự đoán.
6. Ridge alpha=0 ≈ Linear Regression thường; alpha lớn quá → underfitting.
7. `kind='scatter'`/`'hexbin'` chỉ dùng được cho **DataFrame**, không dùng cho Series.
8. `plt.axis('equal')` bắt buộc với pie chart để không bị méo hình.
