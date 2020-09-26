# utl-inserting-code-into-proc-means-to-calculate-the-mean-plus-six-standar-deviation
Inserting code into proc means to calculate the mean plus six standar deviations
    Inserting code into proc means to calculate the mean plus six standar deviations.                                                  
                                                                                                                                       
    SAS-L: Example of views as output from SAS procs                                                                                   
                                                                                                                                       
    Calculating the mean plus 6 * the standard deviation directly using proc means.                                                    
                                                                                                                                       
    github                                                                                                                             
    https://tinyurl.com/y53onjvv                                                                                                       
    https://github.com/rogerjdeangelis/utl-inserting-code-into-proc-means-to-calculate-the-mean-plus-six-standar-deviation             
                                                                                                                                       
    SAS-L: https://listserv.uga.edu/cgi-bin/wa?A2=SAS-L;19b0bc5a.2009d                                                                 
                                                                                                                                       
    This post is based on a post  by                                                                                                   
    FriedEgg <00000a7c04fef931-dmarc-request@listserv.uga.edu>                                                                         
    Thanks FriedEgg                                                                                                                    
                                                                                                                                       
    I realize SQL can easily do this.                                                                                                  
    This is just a proof of concept,                                                                                                   
                                                                                                                                       
    I am inserting code into proc means using a SAS view.                                                                              
    Proc means will output one dataset with the new variable, 6 sigma.                                                                 
    Six sigma is the average student weight + 6 * times the standard deviation.                                                        
    Only one output dataset is created by proc means.                                                                                  
                                                                                                                                       
    SAS issues this warning                                                                                                            
    SAS WARNING: The definition of an output DATA step view is an experimental                                                         
    feature in this release and is not intended for use                                                                                
    in the development of production applications.                                                                                     
                                                                                                                                       
    SOAPBOX ON                                                                                                                         
      We really need shared storage with DOSUBL!!                                                                                      
    SOAPBOX OFF                                                                                                                        
                                                                                                                                       
    /*                   _                                                                                                             
    (_)_ __  _ __  _   _| |_ ___                                                                                                       
    | | `_ \| `_ \| | | | __/ __|                                                                                                      
    | | | | | |_) | |_| | |_\__ \                                                                                                      
    |_|_| |_| .__/ \__,_|\__|___/                                                                                                      
            |_|                                                                                                                        
    */                                                                                                                                 
                                                                                                                                       
    * A view is just code, so you need to create this view prior to running proc means;                                                
                                                                                                                                       
    1, SAS dataset class in sashelp library                                                                                            
       sashelp.class                                                                                                                   
                                                                                                                                       
    2. The view below which is just static code, executed on the                                                                       
       proc means output statement 'out=want_view'.                                                                                    
                                                                                                                                       
    data _null_ / view=want_view;                                                                                                      
                                                                                                                                       
      length                                                                                                                           
       wgtAvg                                                                                                                          
       wgtStd 8;                                                                                                                       
      ;                                                                                                                                
                                                                                                                                       
      set want_view;                                                                                                                   
                                                                                                                                       
      wgtAvg=wgtAvg + 6 *wgtStd;                                                                                                       
      call symputx('wgtAvg',wgtAvg);                                                                                                   
                                                                                                                                       
      rc=dosubl('                                                                                                                      
         data want;                                                                                                                    
            wgtAvgPlusSix=&wgtAvg;                                                                                                     
         run;quit;                                                                                                                     
      ');                                                                                                                              
                                                                                                                                       
    run;quit;                                                                                                                          
                                                                                                                                       
    /*           _               _                                                                                                     
      ___  _   _| |_ _ __  _   _| |_                                                                                                   
     / _ \| | | | __| `_ \| | | | __|                                                                                                  
    | (_) | |_| | |_| |_) | |_| | |_                                                                                                   
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                                                  
                    |_|                                                                                                                
    */                                                                                                                                 
                                                                                                                                       
    * Output id the want dataset created by the want view;                                                                             
    * It is the only output;                                                                                                           
                                                                                                                                       
    WORK.WANT total obs=1                                                                                                              
                                                                                                                                       
      WGTAVGPLUSSIX                                                                                                                    
                                                                                                                                       
         236.670                                                                                                                       
                                                                                                                                       
    /*         _       _   _                                                                                                           
     ___  ___ | |_   _| |_(_) ___  _ __                                                                                                
    / __|/ _ \| | | | | __| |/ _ \| `_ \                                                                                               
    \__ \ (_) | | |_| | |_| | (_) | | | |                                                                                              
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|                                                                                              
                                                                                                                                       
    */                                                                                                                                 
                                                                                                                                       
    proc datasets lib=work nolist;                                                                                                     
    delete want;                                                                                                                       
    run;quit;                                                                                                                          
                                                                                                                                       
    %symdel wgtAvg / nowarn;                                                                                                           
                                                                                                                                       
    proc means data=sashelp.class;                                                                                                     
    var weight;                                                                                                                        
    * The output is not a dataset, it is a view.;                                                                                      
    output  mean=wgtAvg std=wgtStd out=want_view(keep=wgtAvg wgtStd);                                                                  
    run;quit;                                                                                                                          
                                                                                                                                       
    /*                                                                                                                                 
    | | ___   __ _                                                                                                                     
    | |/ _ \ / _` |                                                                                                                    
    | | (_) | (_| |                                                                                                                    
    |_|\___/ \__, |                                                                                                                    
             |___/                                                                                                                     
    */                                                                                                                                 
                                                                                                                                       
    NOTE: Multiple concurrent threads will be used to summarize data.                                                                  
    SYMBOLGEN:  Macro variable WGTAVG resolves to 236.66991675                                                                         
    NOTE: The data set WORK.WANT has 1 observations and 1 variables.                                                                   
    NOTE: DATA statement used (Total process time):                                                                                    
          real time           0.01 seconds                                                                                             
          user cpu time       0.01 seconds                                                                                             
          system cpu time     0.00 seconds                                                                                             
          memory              9882.15k                                                                                                 
          OS Memory           27192.00k                                                                                                
          Timestamp           09/25/2020 11:12:14 PM                                                                                   
          Step Count                        180  Switch Count  0                                                                       
                                                                                                                                       
                                                                                                                                       
    NOTE: There were 19 observations read from the data set SASHELP.CLASS.                                                             
    NOTE: The data set WORK.WANT_VIEW has 1 observations and 2 variables.                                                              
    NOTE: View WORK.WANT_VIEW.VIEW used (Total process time):                                                                          
          real time           0.17 seconds                                                                                             
          user cpu time       0.04 seconds                                                                                             
          system cpu time     0.11 seconds                                                                                             
          memory              9882.15k                                                                                                 
          OS Memory           27192.00k                                                                                                
          Timestamp           09/25/2020 11:12:14 PM                                                                                   
          Step Count                        180  Switch Count  9                                                                       
                                                                                                                                       
    NOTE: PROCEDURE MEANS used (Total process time):                                                                                   
          real time           0.18 seconds                                                                                             
          user cpu time       0.04 seconds                                                                                             
          system cpu time     0.11 seconds                                                                                             
          memory              9882.15k                                                                                                 
          OS Memory           27192.00k                                                                                                
          Timestamp           09/25/2020 11:12:14 PM                                                                                   
          Step Count                        180  Switch Count  9                                                                       
                                                                                                                                       
    433 !     quit;                                                                                                                    
                                                                                                                                       
