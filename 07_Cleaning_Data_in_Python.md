7. Cleaning Data in Python

- df.head() 顯示頭五列，df.tail() 顯示倒數五列，head(n) 和 tail(n) 可加參數 n 來指定要顯示幾列。
- df.shape 顯示有幾行幾列，df.columns 顯示全部欄位的名字，shape 和 columns 是屬性不是方法。
- df.info() 顯示 DataFrame 的資訊，df.describe() 顯示統計資訊。
- EDA = Exploratory data analysis

- 用 value_counts() 來做 frequency count
- 就計算相同結果在該欄位中出現的次數，將結果以遞減排列。
原本 missing value 和 NaN 不會被列入計算，若加入參數 dropna=False (預設是 dropna=True) 則會把 missing value 和 NaN 列入計算。
- 例如：df['Borough'].value_counts(dropna=False) 會計算 Borough 欄位的每個值出現的數目，用 dropna=False 會把 missing value 和 NaN 都計算。
- df['欄位名'] 也可以用 df.欄位名 但是後者只適用於欄位名沒有空格時。

- 用 bar 圖看不連續的資料，用 hist 圖看連續的資料。
df.欄位.plot('hist')
df.欄位.boxplot(column='哪一欄當y軸，通常是某種計數', by='依照哪一欄當x軸，通常是某種分類')
例如：df['Existing Zoning Sqft'].plot(kind='hist', rot=70, logx=True, logy=True) kind 指明畫 hist，rot=70 是說 x labels 轉 70 度， logx=True, logy=True 使用 log 座標。
例如：df.boxplot(column='initial_cost', by='Borough', rot=90) 照 by 指定的欄位裡面獨立的元素來當 x 軸，畫 column 指定欄位的 box plot
例如：df.plot(kind='scatter', x='initial_cost', y='total_est_fee', rot=70) 用 kind 指定畫 scatter plot，x 和 y 軸的值是用欄位名字帶入。

- 適合用來做報告的和適合用來做分析的表格不一定一樣
Tidy data 的原則:
columns represent separable variables
rows represent individual observations
observation units form tables

- 可以用 melt() 來將表格轉換成 tidy 的
Melting data is the process of turning columns of your data into rows of data.
pd.melt(frame=df, id_vars=['固定不變的欄位們'], value_vars=['要 melt 的欄位們，不指定的話就是除了 id_vars 以外的全部'], var_name='melt 後的新欄位名稱', value_name='melt 後的新欄位名稱') 如果沒有指定 var_name 和 value_name 那這兩個預設是 var_name='variable' 和 value_name='value'
- 例如：pd.melt(frame=df, id_vars='name', value_vars=['treatment a', 'treatment b'])
- 例如：airquality_melt = pd.melt(airquality, id_vars=['Month', 'Day'], var_name='measurement', value_name='reading')
- pivot() 和 melt() 的功能正好相反
有些欄位值重複會讓 pivot() 不知道要如何處理，此時可改用 pivot_table()
df.pivot(index=['固定不變的欄位'], columns='要樞紐轉換的欄位', values='樞紐轉換後要當成值的欄位')
pd.pivot_table(data=df, index=['固定不變的欄位'], columns='要樞紐轉換的欄位', values='樞紐轉換後要當成值的欄位', aggfunc=指定使用的 aggregate function)
- 也可以使用 df.pivot_table() 就不用指定 data=df 參數
- 例如：weather_tidy = weather.pivot(index='date', columns='element', values='value')
- 例如：weather2_tidy = weather.pivot_table(values='value', index='date', columns='element', aggfunc=np.mean)
- 例如：airquality_pivot = pd.pivot_table(data=airquality_melt, index=['Month', 'Day'], columns='measurement', values='reading')
- 例如：airquality_pivot = pd.pivot_table(data=airquality_dup, index=['Month', 'Day'], columns='measurement', values='reading', aggfunc=np.mean) 這邊指定當欄位有重複時用 np.mean
- pivot 後的表格得到的 index 是 hierarchical index (also known as a MultiIndex)
可以加上 .reset_index() 來變成一般的平坦化的 index

- 有時候一個欄位包含了兩種以上的資訊，可以利用 melt() 和 pivot() 把資訊分開來
例如：tb_melt.variable 同時包含了性別和年紀
tb_melt['gender'] = tb_melt.variable.str[0]  取出性別
tb_melt['age_group'] = tb_melt.variable.str[1:] 取出年紀
例如：ebola_melt['type_country'] 欄位包含了 type 和 country 的資訊
- 可以利用 Cases_Guinea.split('_'), which returns the list ['Cases', 'Guinea']
ebola_melt['str_split'] = ebola_melt['type_country'].str.split('_') 先得到列表
ebola_melt['type'] = ebola_melt['str_split'].str.get(0) 再從列表內取得 type
- ebola_melt['country'] = ebola_melt['str_split'].str.get(1) 再從列表內取得 country
- pd.concat([df1, df2, df3, ...], axis=0, ignore_index=True) 將 df1, df2, df3 等結合起來
- axis=0 是預設值，表示沿著欄的方向上下疊在一起，若用 axis=1 表示沿著列的方向左右疊在一起
- 疊起來的新 DataFrame 的 index 仍然和舊的一樣，因此要加入 ignore_index=True 來重設 index

- 用 pd.concat() 合併表格：
當有很多 DataFrame 要合併在一起時，可以用 glob 先取得一個要合併的檔案們的列表，然後再用迴圈把所有檔案讀成 DataFrame，最後再合併在一起
import glob
pattern='*.csv'
csv_files = glob.glob(pattern) csv_files 只是一個 list
- dfs = [] 建立一個空的列表，裡面要放要合併的 DataFrame
- for csv in csv_files:
    df = pd.read_csv(csv)
    dfs.append(df)
df_concat = pd.concat(dfs)

- 用 pd.merge() 合併表格：
pd.merge(left=放左邊的 DataFrame 名字, right=放右邊的 DataFrame 名字, left_on='照左邊 DataFrame 的哪個欄位合併', right_on='照右邊 DataFrame 的哪個欄位合併')
- 如果左右兩個 DataFrame 是依照相同欄位名稱來合併，可以使用 on='欄位名稱'，就不需要分別指定 left_on 和 right_on
- 合併欄位有一對一，一對多，多對ㄧ，多對多
- df.dtypes 查看 df 中各欄位的型態
- 可以用 astype() 來轉換欄位的型態
- 例如：df['treatment a'] = df['treatment a'].astype(str) 將 df['treatment a'] 欄位轉換成 str 型態
- 例如：tips.sex = tips.sex.astype('category') 把 sex 欄位轉換成 category 型態，用 category 型態可以減少記憶體使用量
- tips['tip'] = pd.to_numeric(tips['tip'], errors='coerce') 將 str 轉換成數值型態，用 errors='coerce' 才能轉換 NaN 而不出錯

- 用 regular expression 來比對
import re
# Compile the pattern: prog
prog = re.compile('\d{3}-\d{3}-\d{4}') \d 表示整數 {3} 表示三位數
- # See if the pattern matches
result = prog.match('123-456-7890')
print(bool(result))
例如：pattern2 = bool(re.match(pattern='\$\d*\.\d{2}', string='$123.45')) \$ 指錢字號，用 * 號就沒限制是幾位數，\. 是指小數點
例如：pattern3 = bool(re.match(pattern='[A-Z]\w*', string='Australia'))
- matches = re.findall('\d+', 'the recipe calls for 10 strawberries and 1 banana') 在後面的字串中找整數，用 \d+ 才能找到兩位數以上，而不會把 10 當成 1 和 0
- from numpy import NaN

- 用 apply() 對欄位做某些運算
- 例如：tips['sex_recode'] = tips.sex.apply(recode_sex) recode_sex() 是一個預先定義好的函數，放在 apply() 裡面時不用加括號
例如：tips['total_dollar_replace'] = tips['total_dollar'].apply(lambda x: x.replace('$', '')) 也可以用 lambda 函數
例如：tips['total_dollar_re'] = tips['total_dollar'].apply(lambda x: re.findall('\d+\.\d+', x)[0])
- df.drop_duplicates() 刪除重複的列資料
- df.dropna() 只要該列有 NaN 就丟掉該列，可以加參數 axis=0, how='any'
- df[ ['欄位1', '欄位2'] ].fillna(要取代 NaN 的東西)
- 例如：airquality['Ozone'] = airquality.fillna(oz_mean) 是把 NaN 用 mean 取代
- assert statement
assert 1 == 1 若 statement 是 True 則什麼都不會發生
assert 1 == 2 若 statement 是 False 則會傳回 Error
例如：assert google.Close.notnull().all() notnull() 會檢查 google['Close'] 是否有空值，加上 all() 表示要檢查整個欄位
- 例如：assert pd.notnull(ebola).all().all() 用 all().all() 是因為是對整個 DataFrame 做檢查
- 例如：assert (ebola >= 0).all().all()
- pd.notnull(df) 和 df.notnull() 等價
- pattern = '^[A-Za-z\.\s]*$'
# Create the Boolean vector: mask
mask = countries.str.contains(pattern)
# Invert the mask: mask_inverse
mask_inverse = ~mask
- df.groupby('欄位1')['欄位2'].計算()
例如：gapminder_agg = gapminder.groupby('year')['life_expectancy'].mean()