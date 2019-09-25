***
**Пример как скачать данные из Physionet**
```bash
rdsamp -r ecgiddb/Person_01/rec_1 -p -v >Person_01.txt 
```


`ecgiddb` - имя конкретной базы данных из Physionet, у всех баз разное имя


`-p -v -r` - опции для комфортного отображения таблиц

*для работы этой команды нужна программа WFDB (Download here: https://archive.physionet.org/physiotools/wfdb-linux-quick-start.shtml)*
***
```python
import numpy as np
import pandas as pd
from scipy import interpolate
import matplotlib.pyplot as plt
```
***
**Открыть данные через pandas**
```python
df = pd.read_csv('file.txt', sep="\t" ,header =[0,1])
```
`header=[0,1]` - заголовки находятся в первых двух строках таблицы


***
**Выбрать первые 5 с для визуализации**
2 столбец содержит фильтрованный сигнал
```python
x = df.iloc[:2500, 0] 
y = df.iloc[:2500, 2]
```
***
**Визуализация 5с**
```python
ax=plt.figure(figsize=(90,70)).gca()
ax.tick_params(labelsize=100)
plt.plot(x, y,linewidth=12)
```
***
**Выбрать 20 с записи, чтобы перести с оригинальной частоты 500Гц в 256Гц как у Hexoskin и записать 256Гц данные в отдельный файл**
```python
x = df.iloc[:10001, 0] 
y = df.iloc[:10001, 2]
```
***
**для перевода из 500Гц в 256Гц используем интерполяцию сплайнами, для этого рассчитать интервал времени и создать новй набор точек**

```python
xnew = np.linspace(0, 20, num=5128, endpoint=True) 
tck = interpolate.splrep(x, y)
ynew = interpolate.splev(xnew, tck)
```
`xnew` - содержит новый набор точек времени


`tck`-содержит узлы интерполяции


`ynew` -содержит интерполированный сигнал

***
**Создание нового файла *.csv* c интерполированными данными (нужной частотой 256Гц)**
```python
data = {'time (seconds)': xnew,'(mV)':  ynew}
df=pd.DataFrame(data=data)
df.to_csv('file_256Hz.csv')
```


