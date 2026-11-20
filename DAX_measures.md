# DAX measures

All measures use the `Energy` table loaded from `data/electricity_data.csv`.

```dax
Total Generation (TWh) =
SUM ( Energy[electricity_generation] )

Coal Share % =
DIVIDE ( SUM ( Energy[coal_electricity] ), [Total Generation (TWh)] )

Wind + Solar Share % =
DIVIDE (
    SUM ( Energy[wind_electricity] ) + SUM ( Energy[solar_electricity] ),
    [Total Generation (TWh)]
)

kWh per Person =
DIVIDE ( SUM ( Energy[electricity_generation] ) * 1000000000, SUM ( Energy[population] ) )

Population (m) =
SUM ( Energy[population] ) / 1000000

Latest Year =
MAX ( Energy[year] )

Generation (latest) =
VAR MaxYear = [Latest Year]
RETURN
    CALCULATE ( [Total Generation (TWh)], Energy[year] = MaxYear )

Coal Share (latest) =
VAR MaxYear = [Latest Year]
RETURN
    CALCULATE ( [Coal Share %], Energy[year] = MaxYear )

Wind + Solar Share (latest) =
VAR MaxYear = [Latest Year]
RETURN
    CALCULATE ( [Wind + Solar Share %], Energy[year] = MaxYear )

kWh per Person (latest) =
VAR MaxYear = [Latest Year]
RETURN
    CALCULATE ( [kWh per Person], Energy[year] = MaxYear )

Coal Share Change since 2000 (pp) =
( [Coal Share (latest)] - CALCULATE ( [Coal Share %], Energy[year] = 2000 ) ) * 100

Highlight Colour =
IF ( SELECTEDVALUE ( Energy[country] ) = "South Africa", "#E69F00", "#999999" )
```

## Why the "latest" measures store the year in a variable first
A measure used directly inside a `CALCULATE` filter is re-evaluated for every year being tested, so `[Latest Year]` always matches the year being tested and the filter keeps all years. Saving it in a variable first fixes it to the latest year of the selected country, before the filter runs.

## Number formats
- Percentage, 1 decimal: `Coal Share %`, `Wind + Solar Share %`, `Coal Share (latest)`, `Wind + Solar Share (latest)`
- Decimal number, 0 decimals: `Total Generation (TWh)`, `Generation (latest)`
- Whole number with thousands separator: `kWh per Person`, `kWh per Person (latest)`
- Decimal number, 1 decimal: `Coal Share Change since 2000 (pp)`, `Population (m)`
