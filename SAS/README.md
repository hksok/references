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