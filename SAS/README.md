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

---

## filter rows

```sas
proc print data=sashelp.cars;
    var Make Model Type MSRP MPG_City MPG_Highway;
    where Type="SUV" and MSRP <= 30000;
run;
```

```sas
proc print data=pg1.storm_summary;
    *Add WHERE statement;
    where MaxWindMPH >= 156;
run;
```

```sas
proc print data=pg1.storm_summary;
    *Add WHERE statement;
    where Basin = "WP";
run;
```

```sas
proc print data=pg1.storm_summary;
    *Add WHERE statement;
    where Basin in ("SI", "NI");
run;
```

```sas
proc print data=pg1.storm_summary;
    *Add WHERE statement;
    where StartDate >= "01jan2020"d;
run;
```

```sas
proc print data=pg1.storm_summary;
    *Add WHERE statement;
    where Type="TS" and Hem_EW="W";
run;
```

```sas
proc print data=pg1.storm_summary;
    *Add WHERE statement;
    where MaxWindMPH>156 or 0<MinPressure<920;
run;
```

```sas
proc print data=pg1.storm_summary(obs=50);
	*where MinPressure is missing; /*same as MinPressure = .*/
	*where Type is not missing; /*same as Type ne " "*/
	*where MaxWindMPH between 150 and 155;
	*where Basin like "_I";
run;
```

---

## creating and using macro variables

%LET macro-variable=value;

```sas
%let CarType=Wagon;

proc print data=sashelp.cars;
    where Type="&CarType";
    var Type Make Model MSRP;
run;

proc means data=sashelp.cars;
    where Type="&CarType";
    var MSRP MPG_Highway;
run;

proc freq data=sashelp.cars;
    where Type="&CarType";
    tables Origin Make;
run;
```

```sas
%let WindSpeed=156;
%let BasinCode=NA;
%let Date=01Jan2000;

proc print data=pg1.storm_summary;
    where MaxWindMPH>=&WindSpeed and Basin="&BasinCode" and StartDate>="&Date"d;
    var Basin Name StartDate EndDate MaxWindMPH;
run;
```

---

## Formatting Columns

```sas
proc print data=pg1.class_birthdate;
    format Height Weight 3. Birthdate date9.;
run;
```

```sas
proc print data=pg1.storm_damage;
    format Date mmddyy10. Cost dollar16.;
run;
```

---

## Sort data

```sas
proc sort data=pg1.class_test2 out=test_sort;
    by Subject descending TestScore;
run;
```

---

## remove duplicates

```sas
proc sort data=pg1.class_test3
    out=test_clean
    nodupkey
    dupout=test_dups;
    by _all_;
run;
```

```sas
proc sort data=pg1.storm_detail out=storm_clean nodupkey dupout=storm_dups;
    by _all_;
run;

proc sort data=pg1.storm_detail out=min_pressure;
    where Pressure is not missing and Name is not missing;
    by descending Season Basin Name Pressure;
run;

proc sort data=min_pressure nodupkey;
    by descending Season Basin Name;
run;
```

---

## Using the DATA step to create a SAS data set

```sas
DATA output-table;
    SET input-table;
run;
```

```sas
data myclass;
    set sashelp.class;
run;
```

DATA Step Procesing

Compilation

- check syntax for errors
- identify column attributes
- establish new table metadata

Execution

- read and write data
- perform data manipulations, calculations, and so on

```sas
data myclass;
    set sashelp.class;
    where Age >= 15;
    *keep Name Age Height;
    *drop Sex Weight;
    format Height 4.1 Weight 3.
run;
```

```sas
data cars_new;
    set sashelp.cars;
    where Origin ne "USA";
    Profit = MSRP-Invoice;
    Source = "Non-US Cars";
    format Profit dollar10.;
    keep Make Model MSRP Invoice Profit Source;
run;
```

```sas
data tropical_storm;
    set pg1.storm_summary;
    drop Hem_EW Hem_NS Lat Lon;
    where Type="TS";
    *Add assignment and FORMAT statements;
    MaxWindKM=MaxWindMPH*1.60934;
    format MaxWindKM 3.;
    StormType="Tropical Storm";
run;
```

```sas
data storm_new;
    set pg1.storm_summary;
    drop Type Hem_EW Hem_NS MinPrssure Lat Lon;
    *Add assignment statements;
    Basin=upcase(basin);
    Name=propcase(Name);
    Hemisphere=cats(Hem_NS, Hem_EW);
    Ocean=substr(Basin, 2, 1);
run;
```

```sas
data storm_new;
    set pg1.storm_damage;
    drop Summary;
    YearsPassed=yrdif(Date, today(), "age");
    Anniversary=mdy(month(Date), day(Date), year(today()));
    format YearsPassed 4.1 Date Anniversary mmddyy10.;
run;
```

---

## Conditional Processing

```sas
data cars2;
    set sashelp.cars;
    if MSRP < 30000 then Cost_Group=1;
    if MSRP >= 30000then Cost_Group=2;
    keep Make Model Type MSRP Cost_Group;
run;
```

```sas
data storm_new;
    set pg1.storm_summary;
    keep Season Name Basin MinPressure PressureGroup;
    *Add IF-THEN statemenets;
    if MinPressure=. then PressureGroup=.;
    if MinPressure <= 920 then PressureGroup=1;
    if MinPressure > 920 then PressureGroup=0;
run;
```

```sas
data cars2;
    set sashelp.cars;
    length CarType $ 6;
    if MSRP < 60000 then CarType="Basic";
    else CarType="Luxury";
    keep Make Model MSRP CarType;
run;
```

```sas
data under40 over40;
    set sashelp.cars;
    keep Make Model msrp cost_group;
    if MSRP < 20000 then do;
        Cost_Group=1;
        output under40;
    end;
    else if MSRP < 40000 then do;
        Cost_Group=2;
        output under40;
    end;
    else do;
        Cost_Group=3;
        output over40;
    end;
run;
```

```sas
data indian atlantic pacific;
    set pg1.storm_summary;
    length Ocean $ 8;
    keep Basin Season Name MaxWindMPH Ocean;
    Basin=upcase(Basin);
    OceanCode=substr(Basin, 2, 1);
    if OceanCode="I" then do;
        Ocean="Indian";
        output indian;
    end;
    else if OceanCode="A" then do;
        Ocean="Atlantic";
        output atlantic;
    end;
    else do;
        Ocean="Pacific";
        output pacific;
    end;
run;
```

---

## Reporting

```sas
title1 "Class Report";
title2 "All Students";
footnote1 "Report Generated on 01SEP2018";
proc print data=pg1.class_birthdate;
run;
```

```sas
%let age=13;

title1 "Class Report";
title2 "Age=&age";
footnote1 "School Use Only";

proc print data=pg1.class_birthdate;
    where age=&age;
run;

title;
footnote;
```

```sas
proc means data=sashelp.cars;
    where type="Sedan";
    var Make Model MSRP MPG_Highway MPG_City;
    label MSRP="Manufacturer Suggested Retail Price" MPG_Highway="Highway Miles per Gallon";
run;
```

---

## Segmenting Reports

```sas
proc sort data=sashelp.cars;
    out=cars_sort;
    by Origin;
run;

proc freq data=cars_sort;
    by Origin;
    tables Type;
run;
```

## creating frequency reports and graphs

```sas
proc freq data=pg1.storm_final order=freq nlevels;
    tables BasinName StartDate /nocum;
    format StartDate monname.;
run;
```

```sas
ods graphics on;
odsnoproctitle;
title "Frequency Report for Basin and Storm Month";
proc freq data=pg1.storm_final order=freq nlevels;
    tables BasinName StartDate /nocum plots=freqplot(orient=horizontal scale=percent);
    format StartDate monname.;
    label BasinName="Basin" StartDate="Storm Month";
run;
title;
ods proctitle;
```

---

## creating two-way frequency reports

```sas
proc freq data=sashelp.heart;
    tables BP_Status*Chol_Status;
run;
```

```sas
proc freq data=pg1.storm_final;
    tables BasinName*StartDate / norow nocol nopercent;
    format StartDate monname.;
    label BasinName="Basin" StartDate="Storm Month";
run;
```

```sas
proc freq data=pg1.storm_final;
    tables BasinName*StartDate / crosslist;
    format StartDate monname.;
    label BasinName="Basin" StartDate="Storm Month";
run;
```

```sas
proc freq data=pg1.storm_final noprint;
    tables BasinName*StartDate / out=stormcounts;
    format StartDate monname.;
    label BasinName="Basin" StartDate="Storm Month";
run;
```

---

## creating a summary statistics report

```sas
proc means data=pg1.storm_final;
    var MaxWindMPH;
run;
```

```sas
proc means data=pg1.storm_final mean median min max maxdec=0;
    var MaxWindMPH;
run;
```

```sas
proc means data=pg1.storm_final mean median min max maxdec=0;
    var MaxWindMPH;
    class BasinName StormType;
    ways 1;
run;
```

```sas
proc means data=pg1.storm_final mean median min max maxdec=0;
    var MaxWindMPH;
    class BasinName StormType;
    ways 0 1 2;
run;
```

---

## Creating an output summary table

```sas
proc means data=sashelp.heart noprint;
    var Weight;
    class Chol_Status;
    ways 1;
    output out=heart_stats mean=AvgWeight;
run;
```

---

### call symput

- for example, to assign the character string `testing` to macro variable `new`, submit the following statement:

```sas
call symput('new', 'testing');
```

### intnx

The intnx function increments a date, time, or datetime value by intervals such as DAY, WEEK, QTR, and MINUTE, or a custom interval that you define. 

The increment is based on a starting date, time or datetime value, and on the number of time intervals that you specify.

The INTNX function returns the SAS date value for the beginning date, time, or datetime value of the interval that you specify in the start–from argument. (To convert the SAS date value to a calendar date, use any valid SAS date format, such as the DATE9. format.) The following example shows how to determine the date of the start of the week that is six weeks from the week of October 17, 2003.

```sas
x=intnx('week', '17oct03'd, 6);
put x date9.;
```

---

### writing output without creating a data set

In many cases, when you use a DATA step to write a report, you do not need to create an additional data set. When you use the `data _null_` statement, SAS processes the DATA step without writing observations to a data set. Using the `data _null_` statement can increase program efficiency considerably.

```sas
data _null_;
   length medalist $ 19;
   input year 1-4  medalist $ 6-24  medal $ 26-31 country $ 33-35 result 37-41; 
   put medalist country medal result year;
   datalines;
1984 Lingjuan Li         SILVER CHN 2559
1984 Jin-Ho Kim          BRONZE KOR 2555
1988 Soo-Nyung Kim       GOLD   KOR 2683
     Hee-Kyung Wang      SILVER KOR 2612
1988 Young-Sook Yun      BRONZE KOR 2593
1992 Youn-Jeong Cho      GOLD   KOR 113
1992 Soo-Nyung Kim       SILVER KOR 105
1992 Natalya Valeyeva    BRONZE URS     
1996 Kyung-Wook Kim      GOLD   KOR     
1996 Ying He             SILVER CHN     
1996 Olena Sadovnycha    BRONZE UKR     
2000 Mi-Jin Jun          GOLD   KOR 107 
2000 Nam-Soon Kim        SILVER KOR 106 
2000 Soo-Nyung Kim       BRONZE KOR 103 
;
run;
```

---

## Exporting Data

```sas
proc export data=sashelp.cars
    outfile="s:/workshop/output/cars.txt"
    dbms=tab replace;
run;
```

```sas
libname myxl xlsx "&outpath/cars.xlsx";

data myxl.asiacars;
    set sashelp.cars;
    where origin='Asia';
run;

libname myxl clear;
```

```sas

libname xlout xlsx "&outpath/southpacific.xlsx";

data South_Pacific;
    set pg1.storm_final;
    where Basin="SP";
run;

proc means data=pg1.storm_final noprint maxdec=1;
    where Basin="SP";
    var MaxWindKM;
    class Season;
    ways 1;
    output out=Season_Stats n=Count mean=AvgMaxWindKM max=StrongestWindKM;
run;

libname xlout clear;
```

---

## SAS Output Delivery System

sas procedure -> output objects -> ODS destinations

```sas
ods csvall file="&outpath/cars.csv";
proc print data=sashelp.cars noobs;
    var Make Model Type MSRP MPG_City MPG_Highway;
    format MSRP dollar8.;
run;
ods csvall close;
```

```sas
proc template;
    list styles;
run;
ods excel file="&outpath/wind.xlsx" style=sasdocprinter
          options(sheet_name='Wind Stats');
title "Wind Statistics by Basin";
ods noproctitle;
proc means data=pg1.storm_final min mean median max maxdec=0;
    class BasinName;
    var MaxWindMPH;
run;

ods excel options(sheet_name='Wind Distribution');
title "Distribution of Maximum Wind";
proc sgplot data=p1.storm_final;
    histogram MaxWindMPH;
    density MaxWindMPH;
run;
title;
ods proctitle;
ods excel close;
```

```sas
ods pdf file="&outpath/wind.pdf" startpage=no style=journal pdftoc=1;
ods noproctitle;

ods proclabel "Wind Statistics";
title "Wind Statistics By Basin";
proc means data=pg1.storm_final min mean median max maxdec=0;
    class BasinName;
    var MaxWindMPH;
run;

ods proclabel "Wind Distribution";
title "Distribution of Maximum Wind";
proc sgplot data=pg1.storm_final;
    histogram MaxWindMPH;
    density MaxWindMPH;
run;
title;

ods pdf close;
```