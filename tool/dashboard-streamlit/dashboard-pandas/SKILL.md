---
name: dashboard-pandas
version: v2.0.0
author: book-skills
description: Pandas 數據處理技能，掌握數據讀取、清理、轉換和聚合，為數據看板提供高質量的數據源
---

# Dashboard Pandas

## 任務目標
- 本 Skill 用於：使用 Pandas 進行數據處理和準備
- 能力包含：數據讀取、數據清理、數據轉換、數據聚合
- 觸發條件：需要處理外部數據或準備看板數據源時

## 操作步驟

### 數據讀取
```python
import pandas as pd

# CSV 檔案
df = pd.read_csv('data.csv')

# Excel 檔案
df = pd.read_excel('data.xlsx', sheet_name='Sheet1')

# JSON
df = pd.read_json('data.json')

# SQL
from sqlalchemy import create_engine
engine = create_engine('sqlite:///data.db')
df = pd.read_sql('SELECT * FROM table', engine)

# URL
df = pd.read_csv('https://example.com/data.csv')
```

### 數據預覽
```python
# 基本資訊
df.head()           # 前5列
df.tail()           # 後5列
df.info()           # 資料型別與缺失值
df.describe()       # 統計描述
df.shape            # 列數與欄數
df.columns          # 欄位名稱列表
df.dtypes           # 各欄資料型別
```

### 數據選取
```python
# 欄位選取
df['name']              # 單欄
df[['name', 'age']]    # 多欄

# 列選取
df.iloc[0:10]          # 位置索引
df.loc[0:10]          # 標籤索引

# 條件篩選
df[df['age'] > 18]                    # 單一條件
df[(df['age'] > 18) & (df['city'] == 'Beijing')]  # 複合條件

# 查詢語法
df.query('age > 18 and city == "Beijing"')
```

### 數據清理
```python
# 缺失值處理
df.isnull().sum()           # 統計缺失值
df.dropna()                 # 刪除缺失列
df.fillna(0)                # 填補缺失值
df['col'].fillna(df['col'].mean())  # 用平均值填補

# 重複值處理
df.duplicated().sum()       # 統計重複值
df.drop_duplicates()       # 刪除重複值

# 型別轉換
df['date'] = pd.to_datetime(df['date'])
df['price'] = df['price'].astype(float)
```

### 數據轉換
```python
# 欄位重新命名
df.rename(columns={'old': 'new', 'col2': 'name2'})

# 新增/修改欄位
df['total'] = df['price'] * df['quantity']
df['year'] = df['date'].dt.year

# 刪除欄位
df.drop(columns=['col1', 'col2'])

# 排序
df.sort_values('price', ascending=False)
df.sort_index()

# 字串處理
df['name'].str.lower()
df['name'].str.strip()
df['code'].str.contains('ABC')
```

### 數據聚合
```python
# 分組統計
df.groupby('category')['price'].sum()
df.groupby('category').agg({'price': 'sum', 'quantity': 'mean'})

# 樞紐分析表
pd.pivot_table(df, values='sales', index='region', columns='quarter', aggfunc='sum')

# 交叉分析表
pd.crosstab(df['A'], df['B'])

# 時間序列重新取樣
df.set_index('date').resample('M')['sales'].sum()
```

### 數據合併
```python
# 合併
pd.merge(df1, df2, on='key', how='inner')

# 拼接
pd.concat([df1, df2], axis=0)   # 縱向
pd.concat([df1, df2], axis=1)   # 橫向

# 追加
df1.append(df2)
```

### 常用統計
```python
# 描述性統計
df['price'].count()
df['price'].mean()
df['price'].median()
df['price'].std()
df['price'].quantile([0.25, 0.5, 0.75])

# 累計計算
df['cumsum'] = df['value'].cumsum()
df['pct_change'] = df['value'].pct_change()

# 排名
df['rank'] = df['score'].rank(ascending=False)
```

### 數據匯出
```python
# CSV
df.to_csv('output.csv', index=False)

# Excel
df.to_excel('output.xlsx', index=False)

# JSON
df.to_json('output.json', orient='records')

# 剪貼簿
df.to_clipboard()
```

## 資源索引
- Pandas 文件：https://pandas.pydata.org/docs/
- 10分鐘入門：https://pandas.pydata.org/docs/user_guide/10min.html
- 數據清理：https://pandas.pydata.org/docs/user_guide/missing_data.html
- 分組聚合：https://pandas.pydata.org/docs/user_guide/groupby.html

## 注意事項
- 讀取大檔案時使用 chunksize 分批讀取
- 使用 query() 提高複雜篩選可讀性
- 避免在迴圈中修改 DataFrame，建議使用向量化操作
- 處理時間序列時先 set_index 再進行操作
