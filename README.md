# utl-driving-all-your-cores-at-I00-percent-utilization-parallel-processing
Driving all your cores to 100 percent utilization parallel processing 

    Driving all your cores to 100 percent utilization parallel processing                                     
                                                                                                              
    Grid computing on a power workstation                                                                     
                                                                                                              
    Dell T7400 2xXeon 3ghz 64gb ram dual 1tb raid 0 arrays 2x4x250gb(3bps max not six)                        
                                                                                                              
    ProbleM: I have 7 compute bound jobs, each takes 3.5 minutes(210 seconds) when run serially               
             Can I run the 7 jobs in parallel on my Dell T7400 8 core workstation                             
                                                                                                              
                                                                                                              
    Banchmarks                    Seconds                                                                     
                                                                                                              
      Single Job                    209   see below                                                           
      Seven Jobs in Parallel        207   see below                                                           
                                                                                                              
                                                                                                              
                                                                                                              
                                                                                                              
    INPUT (& parallel batch jobs)                                                                             
    =============================                                                                             
                                                                                                              
    The jobs do not have to be the same but they need to do little I/O.                                       
                                                                                                              
                                                                                                              
    * JOB1;                                                                                                   
    ------                                                                                                    
    data _null_;                                                                                              
      do rec=1 to 1e9;                                                                                        
         x=rec**(1/constant("PI"))*rec**(1/3)*+rec**(1/10);                                                   
      end;                                                                                                    
      stop;                                                                                                   
    run;                                                                                                      
                                                                                                              
    * JOB1;                                                                                                   
    ------                                                                                                    
    data _null_;                                                                                              
      do rec=1 to 1e9;                                                                                        
         x=rec**(1/constant("PI"))*rec**(1/3)*+rec**(1/10);                                                   
      end;                                                                                                    
      stop;                                                                                                   
    run;                                                                                                      
                                                                                                              
    ....                                                                                                      
                                                                                                              
    * Job 7;                                                                                                  
    --------                                                                                                  
    data _null_;                                                                                              
      do rec=1 to 1e9;                                                                                        
         x=rec**(1/constant("PI"))*rec**(1/3)*+rec**(1/10);                                                   
      end;                                                                                                    
      stop;                                                                                                   
    run;                                                                                                      
                                                                                                              
    If I run one job interactively it takes 3.5 minutes(209 seconds)                                          
                                                                                                              
                                                                                                              
    185   data _null_;                                                                                        
    186     do rec=1 to 1e9;                                                                                  
    187        x=rec**(1/constant("PI"))*rec**(1/3)*+rec**(1/10);                                             
    188     end;                                                                                              
    189     stop;                                                                                             
    190   run;                                                                                                
                                                                                                              
    NOTE: DATA statement used (Total process time):                                                           
          real time           3:29.31                                                                         
          cpu time            3:29.38                                                                         
                                                                                                              
    190 !     quit;                                                                                           
                                                                                                              
                                                                                                              
                                                                                                              
    EXAMPLE OUTPUT RUNNING SEVEN JOBS IN PARALLEL                                                             
    --------------------------------------------                                                              
                                                                                                              
    PS C:\Windows\System32> Get-WmiObject Win32_PerfFormattedData_PerfOS_Processor |                          
     Select Name, PercentProcessorTime                                                                        
                                                                                                              
    CPU          ProcessorUtilization                                                                         
    ----         --------------------                                                                         
    0                  100%                                                                                   
    1                   82&                                                                                   
    2                   94%                                                                                   
    3                   52% ** interactive session                                                            
    4                   88%                                                                                   
    5                   88&                                                                                   
    6                   94%                                                                                   
    7                  100%                                                                                   
                                                                                                              
    SystemUtilization   87%                                                                                   
                                                                                                              
    Clock Time (Elapsed) 207.148999929399 seconds                                                             
                                                                                                              
                                                                                                              
    PROCESS                                                                                                   
    =======                                                                                                   
                                                                                                              
    * save program;                                                                                           
    filename ft15f001 "c:/temp/calc.sas";                                                                     
    parmcards4;                                                                                               
    data _null_;                                                                                              
      do rec=1 to 1e9;                                                                                        
         x=rec**(1/constant("PI"))*rec**(1/3)*+rec**(1/10);                                                   
      end;                                                                                                    
      stop;                                                                                                   
    run;quit;                                                                                                 
    ;;;;                                                                                                      
    run;quit;                                                                                                 
    filename ft15f001 clear;                                                                                  
                                                                                                              
    %let tym=%sysfunc(time());                                                                                
                                                                                                              
    systask kill sys1 sys2 sys3 sys4 sys5 sys6 sys7;                                                          
    systask command "sas c:/temp/calc.sas -nosplash -rsasuser -log c:/temp/cacl1.log" taskname=sys1;          
    systask command "sas c:/temp/calc.sas -nosplash -rsasuser -log c:/temp/cacl2.log" taskname=sys2;          
    systask command "sas c:/temp/calc.sas -nosplash -rsasuser -log c:/temp/cacl3.log" taskname=sys3;          
    systask command "sas c:/temp/calc.sas -nosplash -rsasuser -log c:/temp/cacl4.log" taskname=sys4;          
    systask command "sas c:/temp/calc.sas -nosplash -rsasuser -log c:/temp/cacl5.log" taskname=sys5;          
    systask command "sas c:/temp/calc.sas -nosplash -rsasuser -log c:/temp/cacl6.log" taskname=sys6;          
    systask command "sas c:/temp/calc.sas -nosplash -rsasuser -log c:/temp/cacl7.log" taskname=sys7;          
                                                                                                              
    waitfor sys1 sys2 sys3 sys4 sys5 sys6 sys7;                                                               
    %put %sysevalf( %sysfunc(time()) - &tym);                                                                 
                                                                                                              
                                                                                                              
    *_                                                                                                        
    | | ___   __ _                                                                                            
    | |/ _ \ / _` |                                                                                           
    | | (_) | (_| |                                                                                           
    |_|\___/ \__, |                                                                                           
             |___/                                                                                            
    ;                                                                                                         
                                                                                                              
    172   run;quit;                                                                                           
    173   filename ft15f001 clear;                                                                            
    NOTE: Fileref FT15F001 has been deassigned.                                                               
    174   %let tym=%sysfunc(time());                                                                          
    NOTE: "sys1" is not an active task/transaction.                                                           
    175   systask kill sys1 sys2 sys3 sys4 sys5 sys6 sys7;                                                    
    176   systask command "sas c:/temp/calc.sas -nosplash -log c:/temp/cacl1.log" taskname=sys1;              
    177   systask command "sas c:/temp/calc.sas -nosplash -log c:/temp/cacl2.log" taskname=sys2;              
    178   systask command "sas c:/temp/calc.sas -nosplash -log c:/temp/cacl3.log" taskname=sys3;              
    179   systask command "sas c:/temp/calc.sas -nosplash -log c:/temp/cacl4.log" taskname=sys4;              
    180   systask command "sas c:/temp/calc.sas -nosplash -log c:/temp/cacl5.log" taskname=sys5;              
    181   systask command "sas c:/temp/calc.sas -nosplash -log c:/temp/cacl6.log" taskname=sys6;              
    182   systask command "sas c:/temp/calc.sas -nosplash -log c:/temp/cacl7.log" taskname=sys7;              
    183   waitfor sys1 sys2 sys3 sys4 sys5 sys6 sys7;                                                         
                                                                                                              
    NOTE: Task "sys1" produced no LOG/Output.                                                                 
    184   %put %sysevalf( %sysfunc(time()) - &tym);                                                           
                                                                                                              
    207.148999929399 seconds                                                                                  
                                                                                                              
                                                                                                              
    PS C:\Windows\System32> Get-WmiObject Win32_PerfFormattedData_PerfOS_Processor |                          
     Select Name, PercentProcessorTime                                                                        
                                                                                                              
    CPU          ProcessorUtilization                                                                         
    ----         --------------------                                                                         
    0                             100                                                                         
    1                              82                                                                         
    2                              94                                                                         
    3                              52  ** interactive session                                                 
    4                              88                                                                         
    5                              88                                                                         
    6                              94                                                                         
    7                             100                                                                         
                                                                                                              
    _Total                         87                                                                         
                                                                                                              
                                                                                                              
