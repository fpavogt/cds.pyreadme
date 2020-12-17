
# ReadMe Generator Python library  

The cdspyreadme library is a Python package dedicated for authors who want to submit data in VizieR or AAS.

The package builts ReadMe, standardized tables (in ASCII aligned format) or MRT tables from tables which
can be in different formats (CSV, votable, FITS, astropy table, MRT)

by G.Landais (CDS) 24 june 2016

## Requirements
The cdspyreadme library works with Python3 and requires :
- astropy
- numpy

## Install
python3 setup.py install --user

## Examples
```python
import cdspyreadme

tablemaker = cdspyreadme.CDSTablesMaker()

# add a table
table = tablemaker.addTable("file.csv", description="my CSV table")
# write table in CDS-ASCII aligned format (required)
tablemaker.writeCDSTables()

# Customize ReadMe output
tablemaker.title = "catalogue title"
tablemaker.author = 'G.Landais'
tablemaker.date = 2020
tablemaker.abstract = "This is my abstract..."
tablemaker.putRef("II/246", "2mass catalogue")
tablemaker.putRef("http://...", "external link")

# Print ReadMe
tablemaker.makeReadMe()

# Save ReadMe into a file
with open("ReadMe", "w") as fd:
    tablemaker.makeReadMe(out=fd)
```

#### add astropy table
```python
from astropy.table import Table
import cdspyreadme

astropy_table = Table([(1.4845, 1.4835, -1.234),
               (24.5,  18.2401, 23.426),
               ('HD100', 'HD101', None)],
              names=['ra', 'dec','name'])
tablemaker = cdspyreadme.CDSTablesMaker()
tablemaker.addTable(astropy_table, name="table1")

# add an other local table (in VOTable) 
table2 = Table.read("table.vot")
tablemaker.addTable(table2, name="table2")

tablemaker.writeCDSTables()
tablemaker.makeReadMe()
```

### use astropy Masked Column to remove values according criteria
```python
from astropy.table import Table, MaskedColumn
import cdspyreadme

tablemaker = cdspyreadme.CDSTablesMaker()
csv = Table.read("file.csv")
csv.columns[0] = MaskedColumn(csv.columns[0], mask=[(val>10) for val in csv.columns[0]])
tablemaker.addTable(csv, name="data.cds")

tablemaker.writeCDSTables()
tablemaker.makeReadMe()
```

### Sexagesimal columns
Flag sexagesimal columns in ReadMe.

The method transforms string columns (ie: ra_sexa, de_sexa) in columns RAh, Ram, RAs, DEsign, DEd, DEm, DEs.

```python
from astropy.table import Table
import cdspyreadme

tablemaker = cdspyreadme.CDSTablesMaker()
csv = Table.read("file.csv")
table = tablemaker.addTable(csv, name="data.cds")
ra = table.get_column("ra_sexa")
ra.setSexaRa()
de = table.get_column("dec_sexa")
de.setSexaDe()

tablemaker.writeCDSTables()
tablemaker.makeReadMe()
```

### add ASCII aligned table
```python
from astropy.table import Table
import cdspyreadme

tablemaker = cdspyreadme.CDSTablesMaker()
ascii = cdspyreadme.CDSAsciiTable("table.ascii", "table1", description="ascii table")
table = tablemaker.addTable(ascii)

tablemaker.writeCDSTables()
tablemaker.makeReadMe()
```

## MRT example
The following example builds MRT table from a CSV table 

```python
from astropy.table import Table
import cdspyreadme

tablemaker = cdspyreadme.CDSTablesMaker()
tablemaker.title = "catalogue title"
tablemaker.author = 'G.Landais'

csv = Table.read("file.csv")
# rename columns
colra = csv["ra"]
colra.name = "RAdeg"
colra.description="Right ascension"
colra.unit='deg'
...
table = tablemaker.addTable(ascii, name='table.mrt', description='csv file')
tablemaker.toMRT()
```

