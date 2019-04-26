# utl-sas-array-macro-fcmp-or-dosubl-take-your-choice
SAS array macro fcmp or dosubl take your choice

    SAS array macro fcmp or dosubl take your choice

    Summing the values of an array (dumb example)

      Three Solutions

         1. Array macro Bartosz Jablonski  yabwon@gmail.com  (at macro execution time worls for
            funtions not available in the macro language)

         2. DOSUBL (needs support from SAS, no external routine needed?)
         3. FCMP

    With some compiler enhancements DOSUB is a competitor to FCMP.

    More is Less (external routines not needed with DOSUBL for many problems)

    github
    https://tinyurl.com/y4qool77
    https://github.com/rogerjdeangelis/utl-sas-array-macro-fcmp-or-dosubl-take-your-choice

    macros
    https://tinyurl.com/y9nfugth
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories

    SAS FCMP example
    http://support.sas.com/kb/41/754.html

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    Bartosz Jablonski macro array
     %barray(ary [3] (1 2 3)


    DOSUBL and FCMP
     array ary[3] 8 (1,2,3);
    *           _
     _ __ _   _| | ___  ___
    | '__| | | | |/ _ \/ __|
    | |  | |_| | |  __/\__ \
    |_|   \__,_|_|\___||___/

    ;

     Sum the elements of an array.

     In this case

         sum  =  ary[1] + ary[2] +ary[3]

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;

    1. Array macro Bartosz Jablonski  yabwon@gmail.com

       Sum of array = 6


    2. DOSUBL (needs support from SAS, no external routine needed?)

       ARRAYSUM=6


    3. FCMP

       FCMP_ARRAYSUM=6

    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    ;

    *****************************************************
    1. Array macro Bartosz Jablonski  yabwon@gmail.com  *
    *****************************************************


        %barray(ary [3] (1 2 3)
        , function = sum(of ary[*])
        , macarray=Y)

        %put sum of array = %ary(1) ;



    ******************************************************************
    2. DOSUBL (needs support from SAS, no external routine needed?)  *
    ******************************************************************

       * it may be necessary that aey be a temporary array?;

       data want;
           array ary[3] 8 (1,2,3);
           call symputx('adrAns',put(addrlong(ary[1]),$hex16.));
           arraysum=0;
           call symputx('adrSum',put(addrlong(arraysum),$hex16.));
           rc=dosubl('
             data _null_;
                retain arysum 0;
                do _i_=1 to 3;
                   arysum  = arysum + input(peekclong (ptrlongadd ("&adrAns"x,(_i_-1)*8),8),rb8.);
                end;
                call  pokelong(arysum,"&adrSum"x,8);
             run;quit;
             ');
            put  arraysum = ;
       run;quit;


    ***********
    2. FCMP   *
    ***********


    options cmplib=work.functions;

    proc fcmp outlib=work.functions.samples;
    function sas_summation (b[*]) varargs;
       total = 0;
       do i = 1 to dim(b);
           total = total + b[i];
       end;
    return(total);
    endsub;
    run;
    quit;

    data _null_;
    array temp (3) _temporary_;
    array ary (*) x1-x3 (1,2,3);
    do i=1 to dim(temp);
      temp(i)=ary(i);
    end;
    fcmp_arraysum=sas_summation(temp);
    put fcmp_arraysum=;
    run;

