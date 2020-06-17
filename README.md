# utl-adding-row-of-summary-differences-to-final-table
Adding row of summary differences to final table   
    Adding row of summary differences to final table                                                                                         
                                                                                                                                             
    github                                                                                                                                   
    https://tinyurl.com/ycgf9k4k                                                                                                             
    https://github.com/rogerjdeangelis/utl-adding-row-of-summary-differences-to-final-table                                                  
                                                                                                                                             
    SAS Forum                                                                                                                                
    https://tinyurl.com/y945unl4                                                                                                             
    https://communities.sas.com/t5/SAS-Procedures/Adding-row-of-summary-differences-to-final-table/m-p/660194                                
                                                                                                                                             
    macros                                                                                                                                   
    https://tinyurl.co       m/y9nfugth                                                                                                      
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories                                               
                                                                                                                                             
    I suspect there eis an array solution, but I like to experiment with do_over?                                                            
    I also suspect I caould do this with one do_over?                                                                                        
                                                                                                                                             
    *_                   _                                                                                                                   
    (_)_ __  _ __  _   _| |_                                                                                                                 
    | | '_ \| '_ \| | | | __|                                                                                                                
    | | | | | |_) | |_| | |_                                                                                                                 
    |_|_| |_| .__/ \__,_|\__|                                                                                                                
            |_|                                                                                                                              
    ;                                                                                                                                        
                                                                                                                                             
    data have;                                                                                                                               
     input YEAR COHORT $ TOT IPS OPS SNF HHS HOS PRS DMS ;                                                                                   
    cards4;                                                                                                                                  
    2019 ALL 220383712 1318640 24958942 411863 1940778 6878 183047172 8699439                                                                
    2020 ALL 216412261 1493011 24395788 388176 2094616 8989 178342349 9689332                                                                
    ;;;;                                                                                                                                     
    run;quit;                                                                                                                                
                                                                                                                                             
    WORK.HAVE total obs=2                                                                                                                    
                                                                                                                                             
     YEAR    COHORT       TOT         IPS         OPS        SNF       HHS       HOS       PRS         DMS                                   
                                                                                                                                             
     2019     ALL      220383712    1318640    24958942    411863    1940778    6878    183047172    8699439                                 
     2020     ALL      216412261    1493011    24395788    388176    2094616    8989    178342349    9689332                                 
                                                                                                                                             
    *            _               _                                                                                                           
      ___  _   _| |_ _ __  _   _| |_                                                                                                         
     / _ \| | | | __| '_ \| | | | __|                                                                                                        
    | (_) | |_| | |_| |_) | |_| | |_                                                                                                         
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                                                        
                    |_|                                                                                                                      
    ;                                                                                                                                        
                                                                                                                                             
    WORK.WANT total obs=4                                                                                                                    
                                                                                                                                             
    YEAR    COHORT                 TOT           IPS             OPS           SNF           HHS      HOS                PRS           DMS   
                                                                                                                                             
    2019    ALL            220383712.0    1318640.00     24958942.00     411863.00    1940778.00    6878.00     183047172.00    8699439.00   
    2020    ALL            216412261.0    1493011.00     24395788.00     388176.00    2094616.00    8989.00     178342349.00    9689332.00   
       .    Difference            -1.8         13.22           -2.26         -5.75          7.93      30.69            -2.57         11.38   
                                                                                                                                             
    RULES                                                                                                                                    
    ======                 =============                                                                                                     
                           d_tot =216412261 - 220383712 = 3971451                                                                            
                           l_lag =220383712                                                                                                  
                                           d_tot/d_lag                                                                                       
                           -1.8 = round( 3971451/220383712 * 100, 0.01)                                                                      
                                                                                                                                             
    *          _       _   _                                                                                                                 
     ___  ___ | |_   _| |_(_) ___  _ __                                                                                                      
    / __|/ _ \| | | | | __| |/ _ \| '_ \                                                                                                     
    \__ \ (_) | | |_| | |_| | (_) | | | |                                                                                                    
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|                                                                                                    
                                                                                                                                             
    ;                                                                                                                                        
                                                                                                                                             
    proc datasets lib=work nolist;                                                                                                           
      delete want;                                                                                                                           
    run;quit;                                                                                                                                
                                                                                                                                             
    %array(vars,values=TOT IPS OPS SNF HHS HOS PRS DMS);                                                                                     
                                                                                                                                             
    %utlnopts;                                                                                                                               
                                                                                                                                             
    data want ;                                                                                                                              
                                                                                                                                             
       length year 3 cohort $ 10.;                                                                                                           
                                                                                                                                             
       set have;                                                                                                                             
       output;                                                                                                                               
                                                                                                                                             
       %do_over(vars,phrase=%str(                                                                                                            
           d? = dif(?);                                                                                                                      
           l? = lag(?);                                                                                                                      
       ));                                                                                                                                   
                                                                                                                                             
       if _n_ = 2 then do;                                                                                                                   
          year=.;                                                                                                                            
          cohort = "Difference";                                                                                                             
          %do_over(vars,phrase=%str(                                                                                                         
            ?= round( d?/l? *100, 0.01);                                                                                                     
            keep ?;                                                                                                                          
