# utl-when-flagged-output-the-current-previous-and-future-observation
    If flagged output the current previous and future observation                                                               
                                                                                                                                
                                                                                                                                
       Corrections and simplifications and Marks full comment on end of message                                                 
       Keintz, Mark                                                                                                             
       mkeintz@wharton.upenn.edu                                                                                                
                                                                                                                                
                                                                                                                                
       * Marks simplest;                                                                                                        
       data want;                                                                                                               
          merge have                                                                                                            
                have (firstobs=2 keep=flag rename=(flag=lead_flag));                                                            
          if flag or lag(flag) or lead_flag;                                                                                    
       run;                                                                                                                     
                                                                                                                                
       * improvement of my code by Mark;                                                                                        
       data want;                                                                                                               
         set have end=eof;                                                                                                      
         if not eof then set have (firstobs=2 keep=flag rename=(flag=lead_flag));                                               
         else call missing(lead_flag);                                                                                          
         if flag or lag(flag) or lead_flag;                                                                                     
       run;                                                                                                                     
                                                                                                                                
       * see the by group solution by Mark below;                                                                               
                                                                                                                                
                                                                                                                                
       Four Solutions                                                                                                           
                                                                                                                                
           1. Two set statements (simplificarion of 2?)                                                                         
           2. Two set starments with a by                                                                                       
              Tom: https://stackoverflow.com/users/4965549/tom                                                                  
           3. Sql                                                                                                               
              Kiran: https://stackoverflow.com/users/4619477/kiran                                                              
                                                                                                                                
           4, Hash ( a more flexible and general solution)                                                                      
              Richard: https://stackoverflow.com/users/1249962/richard                                                          
                                                                                                                                
           5. Three additional improved solutions below                                                                         
                                                                                                                                
                                                                                                                                
    github                                                                                                                      
    https://tinyurl.com/yav5sx7a                                                                                                
    https://github.com/rogerjdeangelis/utl-when-flagged-output-the-current-previous-and-future-observation                      
                                                                                                                                
    StackOverflow                                                                                                               
    https://tinyurl.com/yah7ngjv                                                                                                
    https://stackoverflow.com/questions/53583768/in-sas-how-to-print-leading-and-lagging-observations                           
                                                                                                                                
    INPUT                                                                                                                       
                                                                                                                                
    WORK.HAVE total obs=15               | RULES                                                                                
                                         |                                                                                      
    Obs    ID     VAR1    VAR2    FLAG   | ID     VAR1    VAR2    FLAG                                                          
                                         |                                                                                      
      1    ID1      1      15       .    | ID1      1      15       .                                                           
      2    ID1      2      14       1    | ID1      2      14       1                                                           
      3    ID1      3      13       .    | ID1      3      13       .                                                           
                                                                                                                                
      4    ID1      4      12       .    | Not within +1 or -1 of a flag -> remove                                              
      5    ID2      5      11       .    | Not within +1 or -1 of a flag -> remove                                              
                                                                                                                                
      6    ID2      6      10       .    | ID2      6      10       .                                                           
      7    ID2      7       9       1    | ID2      7       9       1                                                           
      8    ID2      8       8       .    | ID2      8       8       .                                                           
                                                                                                                                
      9    ID2      9       7       .    |                                                                                      
                                                                                                                                
     10    ID3     10       6       .    | ID3     10       6       .                                                           
     11    ID3     11       5       1    | ID3     11       5       1                                                           
     12    ID3     12       4       .    | ID3     12       4       .                                                           
                                                                                                                                
     13    ID3     13       3       .    | ID3     13       3       .                                                           
     14    ID3     14       2       1    | ID3     14       2       1                                                           
     15    ID3     15       1       .      ID3     15       1       .                                                           
                                                                                                                                
                                                                                                                                
    EXAMPLE OUTPUT  (ony 12 observatios 2 above were dropped)                                                                   
    --------------                                                                                                              
                                                                                                                                
    WORK.WANT total obs=12                                                                                                      
                                                                                                                                
    Obs    ID     VAR1    VAR2    FLAG                                                                                          
                                                                                                                                
      1    ID1      1      15       .                                                                                           
      2    ID1      2      14       1                                                                                           
      3    ID1      3      13       .                                                                                           
      4    ID2      6      10       .                                                                                           
      5    ID2      7       9       1                                                                                           
      6    ID2      8       8       .                                                                                           
      7    ID3     10       6       .                                                                                           
      8    ID3     11       5       1                                                                                           
      9    ID3     12       4       .                                                                                           
     10    ID3     13       3       .                                                                                           
     11    ID3     14       2       1                                                                                           
     12    ID3     15       1       .                                                                                           
                                                                                                                                
                                                                                                                                
    PROCESS                                                                                                                     
    =======                                                                                                                     
                                                                                                                                
                                                                                                                                
    1. Two set statements (simplificarion of 2?)                                                                                
    --------------------------------------------                                                                                
                                                                                                                                
                                                                                                                                
    * improvement by Mark;                                                                                                      
    data want;                                                                                                                  
      set have end=eof;                                                                                                         
      if not eof then set have (firstobs=2 keep=flag rename=(flag=lead_flag));                                                  
      else call missing(lead_flag);                                                                                             
      if flag or lag(flag) or lead_flag;                                                                                        
    run;                                                                                                                        
                                                                                                                                
    /*                                                                                                                          
     WORK.WANT total obs=12                                                                                                     
                                                                                                                                
      ID     VAR1    VAR2    FLAG                                                                                               
                                                                                                                                
      ID1      1      15       .                                                                                                
      ID1      2      14       1                                                                                                
      ID1      3      13       .                                                                                                
                                                                                                                                
      ID2      6      10       .                                                                                                
      ID2      7       9       1                                                                                                
      ID2      8       8       .                                                                                                
                                                                                                                                
      ID3     10       6       .                                                                                                
      ID3     11       5       1                                                                                                
      ID3     12       4       .                                                                                                
                                                                                                                                
      ID3     13       3       .                                                                                                
      ID3     14       2       1                                                                                                
      ID3     15       1       .                                                                                                
    */                                                                                                                          
                                                                                                                                
                                                                                                                                
    2. Two set starments with a by                                                                                              
       Tom: https://stackoverflow.com/users/4965549/tom                                                                         
    ----------------------------------------------------                                                                        
                                                                                                                                
    data want ;                                                                                                                 
      set have end=eof;                                                                                                         
      by id ;                                                                                                                   
      if not eof then set have(keep=flag rename=(flag=lead_flag) firstobs=2);                                                   
      lag_flag=lag(flag);                                                                                                       
      if first.id then call missing(lag_flag);                                                                                  
      if last.id then call missing(lead_flag);                                                                                  
      if flag or lead_flag or lag_flag ;                                                                                        
      drop lead_flag lag_flag;                                                                                                  
    run;quit;                                                                                                                   
                                                                                                                                
    * same as above;                                                                                                            
                                                                                                                                
                                                                                                                                
    3. Sql                                                                                                                      
       Kiran: https://stackoverflow.com/users/4619477/kiran                                                                     
    --=-----------------------------------------------------                                                                    
                                                                                                                                
    proc sql;                                                                                                                   
      create                                                                                                                    
          table want as                                                                                                         
      select                                                                                                                    
          distinct a.*                                                                                                          
      from                                                                                                                      
         (select * from have) as a                                                                                              
      inner join                                                                                                                
         (select * from have where flag = 1 ) as b                                                                              
      on                                                                                                                        
         a.var1 = b.var1    or                                                                                                  
         a.var1 = b.var1-1  or                                                                                                  
         a.var1 = b.var1 +1                                                                                                     
    ;quit;                                                                                                                      
                                                                                                                                
                                                                                                                                
                                                                                                                                
    4, Hash                                                                                                                     
       Richard: https://stackoverflow.com/users/1249962/richard                                                                 
                                                                                                                                
                                                                                                                                
    data have;                                                                                                                  
      do id = 'A', 'B', 'C', 'D';                                                                                               
        do seq = 1 to 36;                                                                                                       
           rowid + 1;                                                                                                           
                                                                                                                                
           x = seq;                                                                                                             
           if id = 'D' then x = floor(12*ranuni(123));                                                                          
                                                                                                                                
           flag = mod(x,7);                                                                                                     
           output;                                                                                                              
        end;                                                                                                                    
      end;                                                                                                                      
    run;                                                                                                                        
                                                                                                                                
    /*                                                                                                                          
     WORK.HAVE total obs=144                                                                                                    
                                                                                                                                
      Obs    ID    SEQ    ROWID     X    FLAG                                                                                   
                                                                                                                                
        1    A       1       1      1      1                                                                                    
        2    A       2       2      2      2                                                                                    
        3    A       3       3      3      3                                                                                    
        4    A       4       4      4      4                                                                                    
      ....                                                                                                                      
      ....                                                                                                                      
       38    B       2      38      2      2                                                                                    
       39    B       3      39      3      3                                                                                    
       40    B       4      40      4      4                                                                                    
     */                                                                                                                         
                                                                                                                                
                                                                                                                                
    /*                                                                                                                          
    Within each group report each row meeting a fulcral condition as                                                            
    well as p rows before and q rows after                                                                                      
    p and q would correspond to the concepts of lag-size and lead-size.                                                         
    A hash object can track a within group sequence number for the rows                                                         
    that should be output. The double DOW technique is used to measure                                                          
    over the group (compute the seq number for output) and then                                                                 
    evaluate the measure for each item in the group.                                                                            
    */                                                                                                                          
                                                                                                                                
                                                                                                                                
    %let lagsize = 2;                                                                                                           
    %let leadsize = 3;                                                                                                          
                                                                                                                                
    data want;                                                                                                                  
      if _n_ = 1 then do;                                                                                                       
        declare hash marks();                                                                                                   
        retain __n .;                                                                                                           
        marks.defineKey('__n');                                                                                                 
        marks.defineData('__n');                                                                                                
        marks.defineDone();                                                                                                     
      end;                                                                                                                      
                                                                                                                                
      marks.clear();                                                                                                            
      do __n = 1 by 1 until(last.id);                                                                                           
        set have;                                                                                                               
        by id;                                                                                                                  
        if                                                                                                                      
          flag=0 /* fulcrum criteria checked for each row in group */                                                           
        then do;                                                                                                                
          * computation is "store sequence number that would fall in the lag lead 'about' space";                               
          _n_ = __n;                                                                                                            
          do __n = _n_ - &lagsize to _n_ + &leadsize;                                                                           
            marks.replace();                                                                                                    
          end;                                                                                                                  
          __n = _n_;                                                                                                            
        end;                                                                                                                    
      end;                                                                                                                      
                                                                                                                                
      do __n = 1 to __n;                                                                                                        
        set have;                                                                                                               
        if marks.check()=0 then output; * evaluate for each row in group;                                                       
      end;                                                                                                                      
    run;quit;                                                                                                                   
                                                                                                                                
                                                                                                                                
    The technique can be extended to the case of wanting different 'about'                                                      
    sizes based on different fulcral conditions.                                                                                
                                                                                                                                
                                                                                                                                
    Up to 40 obs WORK.WANT total obs=100                                                                                        
                                                                                                                                
    Obs    __N    ID    SEQ    ROWID     X    FLAG                                                                              
                                                                                                                                
      1      5    A       5       5      5      5                                                                               
      2      6    A       6       6      6      6                                                                               
      3      7    A       7       7      7      0                                                                               
    ...                                                                                                                         
    ...                                                                                                                         
     98     32    D      32     140      3      3                                                                               
     99     33    D      33     141      6      6                                                                               
    100     34    D      34     142      3      3                                                                               
                                                                                                                                
                                                                                                                                
    *                _               _       _                                                                                  
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _                                                                           
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |                                                                          
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |                                                                          
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|                                                                          
                                                                                                                                
    ;                                                                                                                           
                                                                                                                                
    data have;                                                                                                                  
     input ID$ VAR1 VAR2 Flag;                                                                                                  
    cards4;                                                                                                                     
    ID1 1 15 0                                                                                                                  
    ID1 2 14 1                                                                                                                  
    ID1 3 13 0                                                                                                                  
    ID1 4 12 0                                                                                                                  
    ID2 5 11 0                                                                                                                  
    ID2 6 10 0                                                                                                                  
    ID2 7 9 1                                                                                                                   
    ID2 8 8 0                                                                                                                   
    ID2 9 7 0                                                                                                                   
    ID3 10 6 0                                                                                                                  
    ID3 11 5 1                                                                                                                  
    ID3 12 4 0                                                                                                                  
    ID3 13 3 0                                                                                                                  
    ID3 14 2 1                                                                                                                  
    ID3 15 1 0                                                                                                                  
    ;;;;                                                                                                                        
    run;quit;                                                                                                                   
                                                                                                                                
                                                                                                                                
    *__  __            _                                                                                                        
    |  \/  | __ _ _ __| | __                                                                                                    
    | |\/| |/ _` | '__| |/ /                                                                                                    
    | |  | | (_| | |  |   <                                                                                                     
    |_|  |_|\__,_|_|  |_|\_\                                                                                                    
                                                                                                                                
    ;                                                                                                                           
                                                                                                                                
    Roger:                                                                                                                      
                                                                                                                                
    There’s a problem with                                                                                                      
                                                                                                                                
    data want;                                                                                                                  
      set have end=eof;                                                                                                         
      if not eof then set have (firstobs=2 keep=flag rename=(flag=lead_flag));                                                  
      if flag or lag(flag) or lead_flag;                                                                                        
    run;                                                                                                                        
                                                                                                                                
                                                                                                                                
    Variables from SET or MERGE are not automatically reset to missing at the next                                              
    iteration of the data step.  Instead they change values only when the same SET                                              
    or MERGE is executed in the next iteration (or some other SET/MERGE reading the                                             
    same vars appear later in the step).  So lead_flag, which is read in the IF …                                               
    THEN statement, is not reset to missing when EOF is true.  Although                                                         
    it isn’t a problem in this particular task, good practice would require                                                     
    explicate reset to missing:                                                                                                 
                                                                                                                                
    data want;                                                                                                                  
      set have end=eof;                                                                                                         
      if not eof then set have (firstobs=2 keep=flag rename=(flag=lead_flag));                                                  
      else call missing(lead_flag);                                                                                             
      if flag or lag(flag) or lead_flag;                                                                                        
    run;                                                                                                                        
                                                                                                                                
                                                                                                                                
    This is one of the reasons I like the MERGE statement for this type of look-ahead problem.                                  
    It resets variables to missing in the next iteration when the shorter                                                       
    of two merged data sets is exhausted.  No call missing required.  And no “if eof” test:                                     
                                                                                                                                
                                                                                                                                
    data want;                                                                                                                  
      merge have                                                                                                                
            have (firstobs=2 keep=flag rename=(flag=lead_flag));                                                                
      if flag or lag(flag) or lead_flag;                                                                                        
    run;                                                                                                                        
                                                                                                                                
    The usual response to this is  “what about BY groups”?  The BY statement attached to a                                      
    MERGE would mess up the alignment of observations, once the 2nd by-group is encountered.                                    
    The simplest answer is to attach the BY statement to a separate SET, and then use the                                       
    first.id and last.id dummies as needed:                                                                                     
                                                                                                                                
    data want;                                                                                                                  
      set have (keep=id);                                                                                                       
      by id;                                                                                                                    
      merge have                                                                                                                
            have (firstobs=2 keep=flag rename=(flag=_flag));                                                                    
      if flag or (lag(flag) and not first.id) or (lead_flag and not last.id);                                                   
    run;                                                                                                                        
                                                                                                                                
    You don’t really need the “keep=id” parameter but I use it to remind me of                                                  
    why the SET statement is there.  And because the SET and MERGE are synchronized,                                            
    they are unlikely to add to disk channel usage, since they can both                                                         
    find their respective data in the same disk cache.                                                                          
                                                                                                                                
    Regards,                                                                                                                    
                                                                                                                                
    Mark                                                                                                                        
                                                                                                                                
                                                                                                                                
                                                                                                                                
                                                                                                                                
