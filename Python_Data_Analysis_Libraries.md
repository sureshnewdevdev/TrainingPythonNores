# 🧠 Python Data Analysis Libraries: NumPy, Pandas & Matplotlib

---

## 🧩 1. NUMPY (Numerical Python)

### 🔹 Introduction
**NumPy** (Numerical Python) is the foundation of scientific computing in Python.  
It provides support for **multi-dimensional arrays**, **matrix operations**, and **mathematical computations**.

**Key Benefits:**
- Efficient numerical computations.
- Powerful `ndarray` object.
- Support for vectorized operations (no need for loops).
- Backbone of libraries like Pandas, SciPy, Scikit-learn.

---

### 🔹 Syntax
```python
import numpy as np
```

---

### 🔹 Description
NumPy provides the `ndarray` (N-dimensional array) data structure that supports fast element-wise operations.

---

### 🔹 Usage
| Task | Function |
|------|-----------|
| Create arrays | `np.array()` |
| Generate sequences | `np.arange()`, `np.linspace()` |
| Mathematical operations | `np.add()`, `np.dot()`, `np.mean()` |
| Random numbers | `np.random.rand()` |
| Reshape arrays | `reshape()`, `ravel()` |

---

### 🔹 Options & Examples

#### 1️⃣ Creating Arrays
```python
import numpy as np
arr1 = np.array([1, 2, 3, 4])
arr2 = np.array([[1, 2, 3], [4, 5, 6]])
print(arr1.ndim)  # 1D
print(arr2.ndim)  # 2D
```

#### 2️⃣ Generating Sequences
```python
np.arange(0, 10, 2)   # [0 2 4 6 8]
np.linspace(0, 1, 5)  # [0.  0.25 0.5 0.75 1.]
```

#### 3️⃣ Array Attributes
```python
arr = np.array([[1,2,3],[4,5,6]])
print(arr.shape)  # (2,3)
print(arr.size)   # 6
print(arr.dtype)  # int32
```

#### 4️⃣ Mathematical Operations
```python
a = np.array([1,2,3])
b = np.array([4,5,6])

print(a + b)
print(np.dot(a,b))
print(np.sqrt(a))
```

#### 5️⃣ Reshaping and Flattening
```python
arr = np.arange(12)
print(arr.reshape(3,4))
print(arr.ravel())
```

#### 6️⃣ Random Numbers
```python
np.random.rand(2,3)
np.random.randn(3,3)
np.random.randint(1,10,5)
```

#### 7️⃣ Aggregations
```python
arr = np.array([10, 20, 30, 40])
print(np.mean(arr))
print(np.median(arr))
print(np.std(arr))
```

---

## 🐼 2. PANDAS (Python Data Analysis Library)

### 🔹 Introduction
**Pandas** provides powerful, flexible, and easy-to-use data structures for data analysis and manipulation.

It builds upon NumPy and adds labeled data structures:
- `Series` (1D)
- `DataFrame` (2D like Excel tables)

---

### 🔹 Syntax
```python
import pandas as pd
```

---

### 🔹 Description
Pandas simplifies:
- Reading and writing data.
- Cleaning and transforming.
- Aggregating and analyzing data.
- Working with time series.

---

### 🔹 Usage
| Task | Function |
|------|-----------|
| Create Series | `pd.Series()` |
| Create DataFrame | `pd.DataFrame()` |
| Read CSV | `pd.read_csv()` |
| Select rows/columns | `df.loc[]`, `df.iloc[]` |
| Group data | `groupby()` |
| Handle missing data | `fillna()`, `dropna()` |
| Combine datasets | `merge()`, `concat()` |

---

### 🔹 Options & Examples

#### 1️⃣ Creating Series
```python
import pandas as pd
s = pd.Series([10, 20, 30, 40], index=['a','b','c','d'])
print(s)
print(s['b'])
```

#### 2️⃣ Creating DataFrame
```python
data = {
    'Name': ['Arjun','Meena','Karthik'],
    'Age': [22, 24, 20],
    'City': ['Chennai','Mumbai','Delhi']
}
df = pd.DataFrame(data)
print(df)
```

#### 3️⃣ Reading Data
```python
df = pd.read_csv('data.csv')
print(df.head())
print(df.info())
print(df.describe())
```

#### 4️⃣ Selecting Data
```python
print(df['Name'])
print(df[['Name','City']])
print(df.iloc[0])
print(df.loc[1, 'City'])
```

#### 5️⃣ Filtering Data
```python
print(df[df['Age'] > 21])
```

#### 6️⃣ Adding / Dropping Columns
```python
df['Salary'] = [25000, 30000, 20000]
df.drop('City', axis=1, inplace=True)
print(df)
```

#### 7️⃣ Handling Missing Values
```python
df.fillna(0, inplace=True)
df.dropna(inplace=True)
```

#### 8️⃣ Grouping & Aggregation
```python
print(df.groupby('City')['Salary'].mean())
```

#### 9️⃣ Merge and Join
```python
df1 = pd.DataFrame({'ID':[1,2,3], 'Name':['A','B','C']})
df2 = pd.DataFrame({'ID':[1,2,4], 'Marks':[90,80,70]})
merged = pd.merge(df1, df2, on='ID', how='inner')
print(merged)
```

#### 🔟 Sorting and Exporting
```python
df.sort_values('Age', ascending=False, inplace=True)
df.to_csv('output.csv', index=False)
```

---

## 📊 3. MATPLOTLIB (Data Visualization)

### 🔹 Introduction
**Matplotlib** is Python’s core library for 2D data visualization.  
It supports various plots such as line, bar, pie, scatter, and histograms.

---

### 🔹 Syntax
```python
import matplotlib.pyplot as plt
```

---

### 🔹 Description
- Used for static, interactive, and animated visualizations.
- Highly customizable charts.
- Works seamlessly with NumPy and Pandas.

---

### 🔹 Usage
| Plot Type | Function |
|------------|-----------|
| Line | `plot()` |
| Bar | `bar()` |
| Scatter | `scatter()` |
| Histogram | `hist()` |
| Pie | `pie()` |
| Labels/Titles | `xlabel()`, `ylabel()`, `title()` |
| Show Plot | `show()` |

---

### 🔹 Options & Examples

#### 1️⃣ Line Plot
```python
import matplotlib.pyplot as plt

x = [1,2,3,4,5]
y = [10,20,25,30,40]

plt.plot(x, y, color='blue', marker='o', linestyle='--')
plt.title("Sales Growth")
plt.xlabel("Month")
plt.ylabel("Sales")
plt.show()
```

#### 2️⃣ Bar Chart
```python
cities = ['Chennai','Delhi','Mumbai']
pop = [8, 12, 10]

plt.bar(cities, pop, color='orange')
plt.title("City Population")
plt.show()
```

#### 3️⃣ Scatter Plot
```python
x = [5,7,8,9,10]
y = [10,20,30,40,50]

plt.scatter(x, y, color='green')
plt.title("Age vs Income")
plt.xlabel("Age")
plt.ylabel("Income")
plt.show()
```

#### 4️⃣ Pie Chart
```python
labels = ['Python','Java','C++','Go']
sizes = [40,30,20,10]

plt.pie(sizes, labels=labels, autopct='%1.1f%%')
plt.title("Programming Language Popularity")
plt.show()
```

#### 5️⃣ Histogram
```python
import numpy as np
data = np.random.randn(1000)
plt.hist(data, bins=20, color='purple', alpha=0.7)
plt.title("Normal Distribution Histogram")
plt.show()
```

#### 6️⃣ Multiple Plots
```python
x = [1,2,3,4]
y1 = [10,20,30,40]
y2 = [5,15,25,35]

plt.plot(x, y1, label='Dataset 1')
plt.plot(x, y2, label='Dataset 2')
plt.legend()
plt.title("Comparison Chart")
plt.show()
```

---

## 🧾 Summary Table

| Library | Purpose | Core Object | Typical Use |
|----------|----------|-------------|--------------|
| **NumPy** | Numerical computation | `ndarray` | Math, statistics, arrays |
| **Pandas** | Data manipulation | `DataFrame`, `Series` | Cleaning, filtering, analysis |
| **Matplotlib** | Visualization | `pyplot` | Graphs & charts |

---

## 🧠 Real-World Example — Combine All Three

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# Step 1: Generate Data with NumPy
sales = np.random.randint(100, 500, size=6)
months = ['Jan','Feb','Mar','Apr','May','Jun']

# Step 2: Create DataFrame
df = pd.DataFrame({'Month': months, 'Sales': sales})
print(df)

# Step 3: Plot using Matplotlib
plt.bar(df['Month'], df['Sales'], color='teal')
plt.title('Monthly Sales Report')
plt.xlabel('Month')
plt.ylabel('Sales')
plt.show()
```

✅ **Output:** Bar chart showing monthly sales.

---

## 🎯 Key Takeaways
- **NumPy**: Backbone for numerical operations.  
- **Pandas**: Simplifies structured data handling.  
- **Matplotlib**: Visualizes insights from data.  

Together, these three libraries form the **core stack for Data Analysis in Python**.
