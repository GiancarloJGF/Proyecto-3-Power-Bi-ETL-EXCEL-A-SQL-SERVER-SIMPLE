# Proyecto-3-Power-Bi-ETL-EXCEL-A-SQL-SERVER-SIMPLE


# Guía: Dashboard de Envíos en Power BI Desktop

## 1. Importar los datos
1. Abre **Power BI Desktop**.
2. **Inicio → Obtener datos → Excel**.
3. Selecciona el archivo `Datos_para_PowerBI.xlsx`.
4. Marca las dos tablas: **Envios** y **Calendario** → **Cargar** (no uses "Transformar", ya están limpias).

## 2. Crear la relación entre tablas
1. Ve a la vista **Modelo** (ícono de tablas conectadas, panel izquierdo).
2. Arrastra el campo **Fecha** de la tabla `Envios` hacia el campo **Fecha** de la tabla `Calendario`.
3. Confirma que la relación quede: `Calendario[Fecha]` (1) → `Envios[Fecha]` (muchos), activa y de dirección única.
4. En la tabla `Calendario`, click derecho → **Marcar como tabla de fechas** → selecciona la columna `Fecha`.
5. En el panel **Modelo**, ordena la columna `MesNombre` por `MesNum` (selecciona `MesNombre` → pestaña Herramientas de columna → Ordenar por columna → `MesNum`). Esto evita que los meses salgan en orden alfabético.

## 3. Medidas DAX a crear
Click derecho en la tabla `Envios` → **Nueva medida**, y pega cada una:

```DAX
Total Envios = COUNTROWS(Envios)

Costo Total = SUM(Envios[Costo_Envio])

Peso Total Kg = SUM(Envios[Peso_Kg])

Costo Promedio por Envio = DIVIDE([Costo Total], [Total Envios])

% Participacion Cliente =
DIVIDE([Total Envios], CALCULATE([Total Envios], ALL(Envios[Cliente])))

% Participacion Transportadora =
DIVIDE([Total Envios], CALCULATE([Total Envios], ALL(Envios[Transportadora])))
```

## 4. Construir los 3 reportes (cada uno en una página/pestaña nueva)

### Página 1 — Envíos y monto total por mes
- Inserta un **gráfico de columnas y líneas combinado**.
- Eje X: `Calendario[AñoMes]` (asegúrate que esté ordenado por fecha).
- Columnas: `[Total Envios]`.
- Línea (eje secundario): `[Costo Total]`.
- Agrega 3 **tarjetas (Card)** arriba con: `[Total Envios]`, `[Costo Total]`, `[Peso Total Kg]`.

<img width="1576" height="533" alt="image" src="https://github.com/user-attachments/assets/3a3af954-a71a-4264-91c1-d5aa0c7faa7b" />


### Página 2 — Envíos por empresa transportadora
- Inserta un **gráfico circular (pastel/dona)**: Leyenda = `Envios[Transportadora]`, Valores = `[Total Envios]`.
- Inserta un **gráfico de barras**: Eje = `Transportadora`, Valor = `[Costo Total]`.
- Agrega una **tabla**: `Transportadora`, `[Total Envios]`, `[% Participacion Transportadora]`, `[Costo Total]`, `[Peso Total Kg]`.

<img width="1779" height="506" alt="image" src="https://github.com/user-attachments/assets/89e8b738-a941-427f-9e46-3c2c9e0fa939" />


### Página 3 — Empresas con mayor tasa de envío
- Inserta un **gráfico de barras horizontales**: Eje = `Cliente`, Valor = `[Total Envios]`.
- En el panel **Filtros**, aplica un filtro de **Top N = 10** sobre `Cliente` por `[Total Envios]`.
- Agrega una **tabla**: `Cliente`, `[Total Envios]`, `[% Participacion Cliente]`, `[Costo Total]`.
- Opcional: agrega **barras de datos** (formato condicional) a la columna de envíos.

<img width="1474" height="556" alt="image" src="https://github.com/user-attachments/assets/82bba014-da9e-4f98-ba27-eefa1a78e960" />


## 5. Filtros recomendados (segmentadores) en cada página
Agrega un **Segmentador de datos (Slicer)** con: `Calendario[MesNombre]`, `Envios[Transportadora]`, `Envios[Tipo_Cliente]` y `Envios[Ciudad]`, para que el usuario pueda filtrar el dashboard interactivamente.

## 6. Notas de limpieza ya aplicadas en los datos
- 8 registros con año erróneo (28/04/2024) corregidos a 28/04/2026.
- 76 envíos sin número de guía recibieron un ID automático `AUTO-####`.
- Espacios en blanco sobrantes en nombres de clientes eliminados.

Con esto el dashboard queda 100% interactivo y se actualiza solo con **Actualizar datos** si cambias el Excel origen.
