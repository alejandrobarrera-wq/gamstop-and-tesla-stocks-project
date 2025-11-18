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
import matplotlib.pyplot as plt
import seaborn as sns
from io import StringIO

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

#Seleccionar la tabla correcta (primera es Tesla Revenue)
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

#EXTRAER INGRESOS REALES DE GAMESTOP (TABLA 2)
gme_revenue = tables[1]   # la segunda tabla es GameStop

# Renombrar columnas
gme_revenue.columns = ["Date", "Revenue"]

# Eliminar filas vacías o con guiones
gme_revenue.dropna(inplace=True)
gme_revenue = gme_revenue[gme_revenue["Revenue"] != "—"]

# Limpiar formato de Revenue
gme_revenue["Revenue"] = gme_revenue["Revenue"].replace(r"[\$,]", "", regex=True)

# Convertir a numérico
gme_revenue["Revenue"] = pd.to_numeric(gme_revenue["Revenue"])

# Convertir columna Date a datetime
gme_revenue["Date"] = pd.to_datetime(gme_revenue["Date"])

# Filtrar desde el año 2012
gme_revenue = gme_revenue[gme_revenue["Date"].dt.year >= 2012]

# Ordenar de menor a mayor por fecha
gme_revenue = gme_revenue.sort_values("Date")

# Reiniciar índice
gme_revenue.reset_index(drop=True, inplace=True)

print("\nGameStop Quarterly Revenue (desde 2012):")
print(gme_revenue.head())
print(gme_revenue.tail())
#Crear un objeto ticker para GameStop
gme = yf.Ticker("GME")

#muestra la informacion en formato json para ver si funciono
print(gme.info)

#Extraer la información histórica (máximo periodo disponible)
gme_data = gme.history(period="1y")

#Mostrar las primeras filas del DataFrame
print("GameStop stock data:")
print(gme_data.head())



#====================================================================


#--------- 5. Gráfica de ingresos anuales de Tesla ---
plt.figure(figsize=(12,5))
sns.barplot(data=tesla_revenue, x="Date", y="Revenue", palette="coolwarm")
plt.title("Tesla yearly Revenue")
plt.xlabel("Fecha")
plt.ylabel("Ingresos en millones (USD)")
plt.xticks(rotation=90)
plt.tight_layout()
plt.show()


#--------- 4. Gráfica de cierre de Tesla -----------
plt.figure(figsize=(12,5))
sns.lineplot(data=tesla_data, x="Date", y="Close")
plt.title("Tesla Closing Price por trimestre (Último año)")
plt.xlabel("Fecha")
plt.ylabel("Precio de cierre en trimestre (USD)")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()



# Convertir fecha a datetime
gme_revenue["Date"] = pd.to_datetime(gme_revenue["Date"])

# Filtrar desde 2012
gme_revenue = gme_revenue[gme_revenue["Date"] >= "2012-01-01"]

# Convertir la fecha a solo AÑO
gme_revenue["Date"] = gme_revenue["Date"].dt.year

# Ordenar
gme_revenue = gme_revenue.sort_values("Date")

# --------- Gráfica IGUAL a la de Tesla ----------
plt.figure(figsize=(12,5))
sns.barplot(data=gme_revenue, x="Date", y="Revenue", palette="coolwarm", errorbar=None)
plt.title("GameStop yearly Revenue (desde 2012)")
plt.xlabel("Fecha")
plt.ylabel("Ingresos (USD)")
plt.xticks(rotation=90)
plt.tight_layout()
plt.show()

#--------- 6. Gráfica de precios históricos GME --------
plt.figure(figsize=(12,5))
sns.lineplot(data=gme_data, x="Date", y="Close", color="purple")
plt.title("GameStop yearly Closing Price (Desde inicio)")
plt.xlabel("Fecha")
plt.ylabel("Precio de cierre por trimestre (USD)")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

