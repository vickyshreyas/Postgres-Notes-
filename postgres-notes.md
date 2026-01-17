4.Memory ARCHITECTURE
================================
->postmaster not only host based authentication and it will generate the procss id also (postgres process),and this main thing this is first process to start and stop posgresql services postmaster only 
->we have 6  components

1.Shared_buffers
  1.a wal_buffers
  1.b clog_buffers 
2.Work_mem
3.temp_buffers
4.Maintainance_work_mem 
5.Autovacuum_work_mem
6.Effective_Cache_size

1.Shared_buffers:(128MB)  least (128kb we cann assign with this we can start postgresql services)
========================

Ex:my system total ram:16GB(linux server ram 16gb)
->This is piece of memory used for postgresql internal operations we have 2(1. Internal 2.External)
*Internal Operations:like Heart,brain etc if not good server crash ->In general 
->Internal Operations:(Transaction logs generation tracking,checkpoints , data flushing etc called it as internal operations 
->This is dedicated for internal operations 
->Default value is 128MB
->Recommanded values 25% of total ram  
->minimum value we can assing 128KB (even audio file is more than 128kb )
->thats why we call postgresql is light waight software 
->We will configure this parametere in postgresql.conf file 
->This is static paramter (to reflect value we need to restrat services ) ->Restart means down time starting & stoping 
->postgresql will never cross the assigned limit 
->as per the above values , shared_buffers=4GB , remaining Ram will be 12GB 
->if we assign bigger shared_bfferes value it is goof for the server performace ,but we should thing about rrmaining ram as well ,if remaing ram not enough for other process there migt be chances for server crasg ]
->sometimes we will observe how the remaing ram utilization happening if it is alway idle(sitting idle) then we will increaase share_bfferes to 35% i.e  total ram i,e means 6GB to 16GB (with out knowing increase shared_buffers )
->until postgresql 9.6 these parameters are there ,now these two are diprecated they removed (internally postgres will take care ) when ever we assign shared_buffers it will assign values to wal_log bufffers some value to clog_buffers 
1a.  wal_log_buffers
2a. clog_buffers

1a.  wal_log_buffers:  
======================
->all the transaction coming to database ,initially it will consume this memory 
->This is peice of shared_buffers
->default values is =-1
1/32% of shared_buffers value(if sb is 32gb wal lo buffer is 1gb)

1b.Clog_buffers:
===================
track commited(completed transaction)  transaction information  it will track 
->Default value is = -1 that means 1/64% of shared_buffers 500 mb if sb is 32G

Note:postgresql default auto commit once done cant rollback but if our requirement is we need to commit or rollback  in that case we will run the query in BTET mode 
BEGIN;and end mode 
=========================================
example:
i will update emp table 
BEGIN;

update emp set no=5 where no=10;

COMMIT/ROLLBACK   ->now i will do my data validation with select query then  only i will decide if good i do commit and if anything wrong i will do rollback like that we need to plan whenever requeirement will come then we have to do like that 

=========================================================
2.Work_Mem:
============
->This is peace of remaing ram not ram beacuse shared buffer already allocated 
->It will be used for postgresql external operations (normal user operations ex; select,insert,update, truncate,etc user running queries those kind of operations we will called as external operations )
->this work_mem will be consumed only if ur query using hashing or sorting operation only internallu(any of our query using sorting or hashing while fetching the data or while inserting the data if it perform hasing or sorting then only work_mem using 

->whatg is hashing: every db hasing alogorithms will key resource it will genarete location of the data values 
ex:if emp table is there 
emp (no ,name )
     (1, ram)  it will geenarete (A1 ,B1) like that  who will generate this address location hasing algorithm will generate address locations based on that dispatcher will get the data 
->if we insert the ddata hasing alogorithm will genarate addresses and dispactcher will load the data (according to that it will thing i need to insert data in A1,B1 like that it will genarate and select,insert ,and update and delete every thing required address location (address location measns hashing algorithm is mandatory) 
->sorting will be performed only during select statemet 
->Default value is 4MB 
->it is dy dynameic parameter no need to restart 
->Shared_buffer ->Postgresql and oracle->SGA
->Work _mem-> postgresql and PGA ->oracle 
->4mb measns very small values this is operation specifc 
ex:if 100 operation happening at a time ,work_mem consumtion will be 100*4MB=400MB from remaining RAM(12GB)
->example work_mem will be depends upon max_connection_limit vakues 
->Max_connection : at a time max number of connections can connect to database 
default values :100 (staticc paramerter need restart requiered)
->so if we increse work_mem to bigger value query performnce is will good but we need to concider max_connection values (default 100) if 100 connection connected at a time and if 101 connection connect it will througj error 
->if 100 connections connected at a time for the faster performance if we set work_mem vakue to 1GB 100*1GB=100GB work_mem requeired from 12GB ram ,first 12 comnection will successfull 13 connecyion  onwards will get out of memory error 
->That is the reason always we will sset work_mekm value small 
->always plan max_connection*work_mem<75 % of remaing ram if ur remaing ram 12 gb then keep it 8Gb it should not go more than 8gb it should atleast 4gb free plan like that

3:Senarios :
 
->If one guy asking that work_mem is very small value my user is veryy critical then what ever connection comming with my user they should get mork_work mem 
->another guy asking what ever the connection coming to my database those are high priority those should complete very faster 
->another guy i dont want all above i will run one uery for that i need more work_mem like that different differnt requirements are there 
->Even though we are setting very small vakues work_mem still we can do work load priritization by setting work_mem values at below keveles  we can increse as requirement 
->Not only cross the 4mb every conection will use 4mb not only that one we can increses like below 

1.Scenario: set work_mem user level :
ex:kashif(user) requested that rambabau for every one u are giving 4mb work_mem but when ever kashif user running need to give 10MB work_mem what are the connection coming from kashid it should utilize 10MB 
2.Scenaria:set the work_mem db level
=->mahesh ask that we have 10 databases in postgresql sevre what ever the connection coming any database 4mb work_mem utilizing but my mahesh databases is very critical database what ever connection coming to mahesh db help to incresse work_mem we can set the work_mem db level 
3,.Scenario 3:Set work_mem sesssion level :
->one more guy came he is saying that i dont user level or db level i will run only one query i need more work_mem for only one query we can do session level also 
->dont worry that only have 4mb work_mem we can modify according requiredment 
1.set work_mem user level 
2.set work_mem db level 
3. set work_mem session level 

->we will configure this parameter in postresql.conf file all the paramenter in postgresql.conf exept hostbased authintication 
->work_mem for external operation 
->Default values :
->shared_bfferes ->128MB 
  minimum=12KB also 
  recomeneded=25% of total ranm 
->Default work_mem=4MB 
->default max_connection=100MB 

                                              3. TEMP_BUFFERS
											  
							==================================================================
->This is peace of remaing ram 12 GB only work_mem utilizing and tem_bufferes utilizing 
->It will be used if we create temporary tables 
-what is temprory Tables:
ex: if i create temp table and i loaded 100gb data if i came out from session and if i login to databaseagain we cant see the table(exit&login) temp tables means session specific with in the session appper if we create temp table it will not savein the disk it will save in the temp bufferes that is the reason 
->it will be used if we are working on multiple join conditios ,more temp result set will be genarated by that if our query generating more temp resultset  by that time also temp buffers utilize      
->some time if assigned work_mem not enogh for your query,then it will consume temp_buffers eeither while creating temporary tables while handling with multiple join conditions it will generate more temprerory if assigned work_mem not enogh for query that time also temp buffer will utilize 
->Default value =8MB ->for each session this value will be hold(each session)->for each session this value hold and ready if any temp tables/temp result set/temp files genarated 
->once session completed it will clear automatically 
ex:if temp table genarate 16MB  then it will show out of temp buffers ->it will through error like temp_buffer limit exeeded 
->this is dynamic 