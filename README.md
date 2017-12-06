# utl_number_of_rows_in_a_table_at_compile_time
    Number of rows in a table at compile time

    *
     _ __ ___   __ _  ___ _ __ ___
    | '_ ` _ \ / _` |/ __| '__/ _ \
    | | | | | | (_| | (__| | | (_) |
    |_| |_| |_|\__,_|\___|_|  \___/

    ;

    %macro utl_nobs(dsn)/des="number of non deleted rows in a table";

       %let dsid=%sysfunc(open(&dsn));
       %sysfunc(attrn(&dsid,nlobs))
       %let rc=%sysfunc(close(&dsid));

    %mend utl_nobs;

    %put *** sas help.class has %utl_nobs(sashelp.class) non deleted rows ***;

    *** sashelp.class has 19 non deleted rows ***

    *
     _ __   ___  _ __    _ __ ___   __ _  ___ _ __ ___
    | '_ \ / _ \| '_ \  | '_ ` _ \ / _` |/ __| '__/ _ \
    | | | | (_) | | | | | | | | | | (_| | (__| | | (_) |
    |_| |_|\___/|_| |_| |_| |_| |_|\__,_|\___|_|  \___/

    ;

    data one;
      input x;
    datalines;
    1
    2
    ;

    %let dsid=%sysfunc(open(one));
    %let num=%sysfunc(attrn(&dsid,nlobs));
    %let rc=%sysfunc(close(&dsid));

    %put table one has  &=num  rows;

    table one has  NUM=2  rows

    *    _       _            _
      __| | __ _| |_ __ _ ___| |_ ___ _ __
     / _` |/ _` | __/ _` / __| __/ _ \ '_ \
    | (_| | (_| | || (_| \__ \ ||  __/ |_) |
     \__,_|\__,_|\__\__,_|___/\__\___| .__/
                                     |_|
    ;

    Thes are very fast solutions because they only access meta data
    and do not scan or read data

    proc sql;
      select count(*) into :nobs trimmed from sashelp.class

    data nobs;
      if _n_ = 0 then set sashelp.class nobs=numobs;

      if numobs=>18  then put "more than 18 obs";

    run;quit;

    *____    _    ____
    / ___|  / \  / ___|
    \___ \ / _ \ \___ \
     ___) / ___ \ ___) |
    |____/_/   \_\____/

    ;

    http://support.sas.com/kb/24/671.html

    /* Create a test data set */
    data test;
       input a b c $ d $;
       datalines;
    1 2 A B
    3 4 C D
    ;
    run;

    %macro obsnvars(ds);
       %global dset nvars nobs;
       %let dset=&ds;

       /* Open data set passed as the macro parameter */
       %let dsid = %sysfunc(open(&dset));

       /* If the data set exists, get the number of observations */
       /* and variables and then close the data set */
       %if &dsid %then
       %do;
          %let nobs =%sysfunc(attrn(&dsid,nobs));
          %let nvars=%sysfunc(attrn(&dsid,nvars));
          %let rc = %sysfunc(close(&dsid));
       %end;

       /* Otherwise, write a message that the data set could not be opened */
       %else %put open for data set &dset failed - %sysfunc(sysmsg());
    %mend obsnvars;

    /* Execute the macro, passing TEST as the data set parameter */

    %obsnvars(test)

    %put &dset has &nvars variable(s) and &nobs observation(s).;


