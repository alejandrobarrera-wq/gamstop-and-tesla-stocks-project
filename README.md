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
tesla_revenue["Date"] = tesla_revenue["Date"].astype(int)

#Reiniciar índice
tesla_revenue.reset_index(drop=True, inplace=True)

#Mostrar resultados
print("\nTesla Quarterly Revenue:")
print(tesla_revenue.head())

print("\nÚltimas filas:")
print(tesla_revenue.tail())

#Gráfica de ingresos anuales de Tesla]
tesla_revenue_sorted = tesla_revenue.sort_values("Date")

plt.figure(figsize=(12,5))
sns.barplot(data=tesla_revenue, x="Date", y="Revenue", palette="coolwarm")
plt.title("Tesla yearly Revenue")
plt.xlabel("Fecha")
plt.ylabel("Ingresos en millones (USD)")
plt.xticks(rotation=90)
plt.tight_layout()
plt.show()


#Gráfica de cierre de Tesla
plt.figure(figsize=(12,5))
sns.lineplot(data=tesla_data, x="Date", y="Close")
plt.title("Tesla Closing Price por trimestre (Último año)")
plt.xlabel("Fecha")
plt.ylabel("Precio de cierre en trimestre (USD)")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()


gme_url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html"
gme_response = requests.get(gme_url)
gme_html_data = gme_response.text
print("GameStop HTML data successfully fetched.")
print(f"First 500 characters of HTML data: {gme_html_data[:500]}")

gme_soup = BeautifulSoup(gme_html_data, "html.parser")
print("GameStop HTML parsed with BeautifulSoup.")
print(f"\nPreview of parsed HTML (first 100 chars):\n{gme_soup.prettify()[:100]}")
#Extraer las tablas
gme_tables = pd.read_html(StringIO(str(gme_soup)))

#Identificar cuál tabla contiene Revenue
for i, table in enumerate(gme_tables):
    print(f"\nTabla {i}")
    print(table.head())
    print(table.columns)

#Suponiendo que la correcta es la tabla 1 (ajústalo después de imprimir)
gme_revenue = gme_tables[1].copy()

#Renombrar columnas asegurado
gme_revenue.columns = ["Date", "Revenue"]

#Limpiar con regex
gme_revenue["Revenue"] = gme_revenue["Revenue"]#revisar

gme_revenue.dropna(inplace=True)
gme_revenue = gme_revenue[gme_revenue["Revenue"] != "—"]
gme_revenue["Revenue"] = gme_revenue["Revenue"].str.replace(r'[$,]', '', regex=True)
gme_revenue["Revenue"] = pd.to_numeric(gme_revenue["Revenue"])
gme_revenue.reset_index(drop=True, inplace=True)

print("Cleaned GameStop Quarterly Revenue:")
print(gme_revenue.head())
print("\nData types after cleaning:")


print(gme_revenue.dtypes)
print(gme_revenue.head())
print("\nData types after cleaning:")
print(gme_revenue.dtypes)

#Gráfica de ingresos anuales de GameStop 

# Ordenar GameStop por fecha
gme_revenue["Date"] = pd.to_datetime(gme_revenue["Date"])
gme_revenue_sorted = gme_revenue.sort_values("Date")


# Gráfica ordenada
plt.figure(figsize=(12, 6))
sns.barplot(
    x='Date',
    y='Revenue',
    data=gme_revenue_sorted,
    palette='viridis',
    hue='Date',
    legend=False
)
plt.title('GameStop Quarterly Revenue')
plt.xlabel('Date')
plt.ylabel('Revenue (Millions of USD)')
plt.xticks(rotation=90)
plt.tight_layout()
plt.show()
