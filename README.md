### NYC-Air-Quality-Analysis.
Project Name: NYC Air Quality Analysis
Overview: This project is a self-analysis to monitor air quality trends in New York. This analysis was conducted to evaluate whether there is a relationship between exposure to air pollution (especially ozone) and an increase in asthma cases in various areas of New York City. I focus the analysis on the main pollutants such as PM2.5 and NO2 levels in various regions. The key data-cleaning (“scrubbing”) outcome, resolved a time-period mismatch between seasonal ozone data and multi-year asthma data by aggregating ozone (3-year average), filtering to relevant areas, and standardizing analysis at the UHF42 geographic level, producing a cleaned dataset ready for visualization. My analysis is framed using the OSEMN workflow (Obtain, Scrub, Explore, Model, Interpret), with highlights including exploratory grouping/summary checks, scatter-plot visualization for correlation, and a simple correlation-style analysis rather than causal inference. My Interpreted takeaway, a generally positive pattern may appear, but ozone alone doesn’t explain asthma variation; certain areas (example mentioned: East Harlem) show higher vulnerability, implying other social/environmental factors may drive outcomes.  
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

Here are the technical details of how I process NYC air quality data using Python (Pandas & Seaborn):

<details>
<summary><b>1. Load Data</b></summary>
Mengambil dataset mentah untuk diproses lebih lanjut.

Python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Memuat dataset
df = pd.read_csv('Air_Quality_and_Health_Impacts.csv')
</details>

<details>
<summary><b>2. Scrubbing (Pembersihan Data)</b></summary>
Memastikan data konsisten dengan memfilter wilayah tertentu dan menyamakan rentang waktu (Time Period).

Python
# Filter wilayah UHF42 saja
df_filtered = df[df['Geo Type Name'] == 'UHF42']

# Ambil data Asma periode 2015-2017
df_asthma = df_filtered[(df_filtered['Name'] == 'Asthma emergency departments visits due to Ozone') & 
                        (df_filtered['Time Period'] == '2015-2017')]

# Ambil data Ozon musiman dan hitung rata-ratanya agar sejajar dengan data Asma
ozone_years = ['Summer 2015', 'Summer 2016', 'Summer 2017']
df_ozone = df_filtered[(df_filtered['Name'] == 'Ozone (O3)') & (df_filtered['Time Period'].isin(ozone_years))]
df_ozone_avg = df_ozone.groupby('Geo Place Name')['Data Value'].mean().reset_index()
df_ozone_avg.columns = ['Geo Place Name', 'Ozone_Level']
</details>

<details>
<summary><b>3. Merge Data (Penyatuan Dataset)</b></summary>
Menyatukan data kualitas udara dan data kesehatan ke dalam satu tabel final.

Python
# Menyatukan data kesehatan dan kadar ozon berdasarkan nama wilayah
df_final = pd.merge(df_asthma[['Geo Place Name', 'Data Value']], 
                     df_ozone_avg, 
                     on='Geo Place Name')
df_final.columns = ['Wilayah', 'Kasus_Asma', 'Kadar_Ozon']
</details>

<details>
<summary><b>4. Exploratory Data Analysis (Visualisasi)</b></summary>
Membuat scatter plot untuk melihat korelasi antara Ozon dan Kasus Asma.

Python
plt.figure(figsize=(10, 6))
sns.scatterplot(data=df_final, x='Kadar_Ozon', y='Kasus_Asma')

# Menambahkan Label Informasi
plt.title('Hubungan Kadar Ozon vs Kasus Asma di NYC (2015-2017)')
plt.xlabel('Rata-rata Kadar Ozon (ppb)')
plt.ylabel('Kasus_Asma per 100,000 Penduduk')

plt.show()
</details>
