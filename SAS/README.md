# SAS

- A SAS program consists of a sequence of steps.
- Each step in the program performs a specific task. 
- There are two kinds of steps in SAS programs: 
> - DATA steps
> - PROC or procedure steps
- A SAS program can contain any combination of DATA steps and PROC steps depending on the tasks you want to perform.

```sas
data myclass;
    set sashelp.class;
run;

proc print data=myclass;
run;
```

```sas
data myclass;
    set sashelp.class;
    heightcm=height*2.54;
run;

proc print data=myclass;
run;

proc means data=myclass;
    var age heightcm;
run;
```

- A DATA step generally reads data from an input source, processes it, and creates a SAS table.
- A DATA step might also filter rows, compute new columns, join tables, and perform other dat manipulations.

- A PROC or procedure step processes a SAS table in a specific predefined way.
- SAS has dozens of procedures that generate reports and graphs, managed data, or perform complex statistical analyses. 

---

## Comments

```sas
/* students under 13 yo */

data under13;
    set sashelp.class;
    where Age<13;
    * drop Height Weight;
run;
```

---

```sas
/* Program created by */
data mycars;
    set sashelp.cars;
    AvgMPG=mean(mpg_city, mpg_highway);
run;

title "Cars with average MPG Over 35";

proc print data=mycars;
    var make model type avgmpg;
    where AvgMPG > 35;
run;

title "Average MPG by Car Type";

proc means data=mycars mean min max maxdec=1;
    var avgmpg;
    class type;
run;
```

---

## SAS Table

- A SAS table is a structured data file that has defined columns and rows.
- SAS tables have the file extension.sas7bdat
- There are two parts to a SAS table;
> - a descriptor portion: contains the metadata or the properties of the table such as the name, the number of rows, and the date and time the table was created. The descriptor portion also includes column names and attributes.
> - a data portion: the data portion contains the data values stored in columns.
- In SAS, a table is also called a dataset, a column is also called a variable, and a row is also called an observation.
- In SAS, a column must have three attributes: 
> - name
> - type
> - length

```sas
proc contents data="filepath/class_birthdays.sas7bdat";
run;
```

---

## SAS libraries

- SAS libraries give you a way to specify the two required pieces of information
> - the location
> - the file type
- you can think of a library as a collection of data files that are the same type and in the same location.
- you can create a SAS library with the LIBNAME statement.
- The statement begins with the keyword LIBNAME, followed by a library reference, or libref. 
- The libref is the name of the library.
- The libref must be eight characters or less, must start with either a leter or underscore, and can include only letters, numbers, and underscores in the name.
- After the libref, you specify the engine.
- The engine is the name of a behind-the-scenes set of instructions for reading structured data.
- There is a different engine for each type of data SAS can read, including Base for SAS tables, Excel, Teradata, Hadoop, and many others.
- Engines enable SAS to read these structured data files directly, without having to do a separate, manual import into SAS.

```sas
libname mylib base "s:/workshop/data";

proc contents data=mylib.class;
run;
```

- By default, a library, or libref, that you define remains active until you delete it or end your SAS session. 
- The libref is simply a pointer or shortcut to your existing data, so although the libref might be deleted when SAS shuts down, your data is not deleted. 
- When you restart SAS, you simply submit the LIBNAME statement again and then you can access your data. 
- You'll notice that SAS programs often begin with one or more LIBNAME statements to connect to the various data sources thatare used in the code.

```sas
OPTIONS VALIDVARNAME=V7;
LIBNAME xlclass xlsx "s:/workshop/data/class.xlsx";
proc contents data=xlclass.class_birthdate;
run;
LIBNAME xlclass CLEAR;
```

---

### importing unstructured data

```sas
PROC IMPORT DATAFILE="path/filename" DBMS=filetype OUT=output-table;
RUN;
```

```sas
proc import datafile="/home/u59014060/EPG1V2/data/storm_damage.csv" dbms=csv
	out=storm_damage_import replace;
run;

proc contents data=storm_damage_import;
run;
```

XLSX engine
- reads directly from Excel file
- data is always current

PROC IMPORT
- creates copy of Excel file
- data must be reimported if it changes

```sas
proc import datafile="/home/u59014060/EPG1V2/data/eu_sport_trade.xlsx"
	dbms=XLSX
	out=EU_SPORT_TRADE REPLACE;
run;

proc contents data=EU_SPORT_TRADE;
run;
```

---

## Exploring Data with SAS

```sas
/* list first 10 rows */
proc print data=pg1.storm_summary (obs=10);
    var Season Name Basin MaxWindMPH MinPressure StartDate EndDate;
run;
```

```sas
/* calculate summary statistics */
proc means data=pg1.storm_summary;
    var MaxWindMPH MinPressure;
run;
```

```sas
/* examine extreme values */
proc univariate data=pg1.storm_summary;
    var MaxWindMPH MinPressure;
run;
```

```sas
/* list unique values and frequencies */
proc freq data=pg1.storm_summary;
    tables Basin Type Season;
run;
```
