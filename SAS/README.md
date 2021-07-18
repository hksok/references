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
