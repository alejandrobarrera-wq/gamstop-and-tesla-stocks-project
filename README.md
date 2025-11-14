
#Instalar librerías necesarias
!pip install yfinance
!pip install bs4
!pip install nbformat
!pip install --upgrade plotly
!pip install lxml


#Importar librerías
import yfinance as yf
import pandas as pd
import requests
from bs4 import BeautifulSoup
from io import StringIO
import plotly.graph_objects as go
from plotly.subplots import make_subplots
import matplotlib.pyplot as plt
import seaborn as sns

#Descargar datos de Tesla
tesla = yf.Ticker("TSLA")
tesla_data = tesla.history(period="1y")
tesla_data.reset_index(inplace=True)
print("Tesla stock data:")
print(tesla_data.head())

#Descargar página HTML
url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm"
response = requests.get(url)
html_data = response.text

#Analizar con BeautifulSoup
soup = BeautifulSoup(html_data, "html.parser")
print("\nPreview del HTML:")
print(soup.prettify()[:100])

#Extraer tablas HTML con pandas y StringIO
tables = pd.read_html(StringIO(str(soup)))

#Seleccionar la tabla correcta (primera o segunda según el contenido)
tesla_revenue = tables[0]

#Renombrar columnas
tesla_revenue.columns = ["Date", "Revenue"]

#Eliminar filas vacías o con guiones
tesla_revenue.dropna(inplace=True)
tesla_revenue = tesla_revenue[tesla_revenue["Revenue"] != "—"]

#Limpiar formato de la columna Revenue
tesla_revenue["Revenue"] = tesla_revenue["Revenue"].replace(r"[\$,]", "", regex=True)

#Convertir Revenue a tipo numérico
tesla_revenue["Revenue"] = pd.to_numeric(tesla_revenue["Revenue"])

#Reiniciar índice
tesla_revenue.reset_index(drop=True, inplace=True)

#Mostrar resultados
print("\nTesla Quarterly Revenue:")
print(tesla_revenue.head())

print("\nÚltimas filas:")
print(tesla_revenue.tail())
#Crea un objeto ticker para guardar la ingo de gamestop
gme = yf.Ticker("GME")

#muestra la informacion en formato json para ver si funciono
print(gme.info)
#Extraer la información histórica (máximo periodo disponible)
gme_data = gme.history(period="max")

#Mostrar las primeras filas del DataFrame
print("GameStop stock data:")
print(gme_data.head())

plt.figure(figsize=(12,5))
sns.lineplot(data=tesla_data, x="Date", y="Close")
plt.title("Tesla Closing Price (Último año)")
plt.xlabel("Fecha")
plt.ylabel("Precio de cierre (USD)")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

#--------- 5. Gráfica de ingresos trimestrales Tesla ---
plt.figure(figsize=(12,5))
sns.barplot(data=tesla_revenue, x="Date", y="Revenue", palette="coolwarm")
plt.title("Tesla Quarterly Revenue")
plt.xlabel("Fecha")
plt.ylabel("Ingresos (USD)")
plt.xticks(rotation=90)
plt.tight_layout()
plt.show()

#--------- 6. Gráfica de precios históricos GME --------
plt.figure(figsize=(12,5))
sns.lineplot(data=gme_data, x="Date", y="Close", color="purple")
plt.title("GameStop Closing Price (Desde inicio)")
plt.xlabel("Fecha")
plt.ylabel("Precio de cierre (USD)")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
