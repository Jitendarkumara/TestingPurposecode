Created a copy of Gp2 application and changed the code according Gp1 application Required table.

In GP2 application Customer Name Auto-Population:
In manual PDO generation, the customer name was not being inserted automatically, so users had to input it manually during GR.
Now, the code has been updated to insert the customer name with PDO data, so it automatically populates in the GR window without manual input.
IN GP2 application-- PDI and POD Report Updates:
Modified both PDI and POD reports according to user requirements.
In GP2 application Added a option In GR section if Tension value will be less than 0 then the value of all tension will be converted into 0 and handeled exception. 
Leave
Leave

Leave
In CCL application In menually PDO generation section added validation in Duration EndTime can not be less than start Time`
Visited GP1 plant to explore the requirement of GP1 level2 application.
Worked in CCL Application to make front page of all section according to big screen 
Worked in CCL Application to make front page of PDI  section according to big screen 

In CCL application changed all page according big Screen
For GP2 Application added data in T_GL_SOFT_CYCLE and T_GL_HARD_CYCLE table 
In CCL application Added code to validate duration during PDO generation. 
In CCL application Added code to display RLNG data in level 2 application.
Added code to tranfer the RLNG data in MES database.

In CCL application PDO section was not refreshing after search the coil resolved it and also changed the code to refresh data on coil selection.
Added process parameter tag in T_config_tag table and T_Periodic table in database to send data in PDO table and MES table . 
IN CCL application Added code to add New process parameter tag value in PDO table .
IN CCL application Added code to add New process parameter tag value in MES  table changed the procedure to send data in MES table .
IN GP2 application modified the PDO generation procedure according to user Reqirement PDO generation.
--
".PLC Connectivity status using color code
-b.Screen color and control size change as per EPH comments
Site Visit
Understanding L2 architecture"
1.Sticker print
created a window form for  CCL application to dispay coil data in a dialoge .
created a window form for  CCL application to dispay coil data on print button .

written code to generate QR code for Width , Length , Thikness
worked on CCL application to generate coil data into QR code for scan using machin
Worked on code to change the degine on sticker and visted ccl plant
change QR resolution to scan the QR code exactly and testing done
Added unit into all value like thikness,width,Length etc and visted ccl plant for testing of sticker

Added date time with formate in CCL sticker and worked on tubemill application to fetch data into Advance page in detail view from OUTPUT table
worked on tubemill application into Advance page in detail view background-color change of row according to Error aslo Added   symbol for each graph according to its status.
Worked on tubemill application into Advance page in chart Added symbol for voltage and current indivisually and also added split delayed page in GP2 application 
Worked on GP2 sticker form and binded data with form in stikcer
worked on CCL application on splite delay page fetched data from databse using query and splite delayed data.Went to 

. Went to Power plant and created a view for IONDATA_LOG2 table also created an user to access data.
.Went to Power plant to check data and for IONDATA_LOG2 testing
.Worked on Tubmill application to fetch data in detail tab on show button click
.Worked on CCL Application to display sticker as auto popup after coil completion
Worked on TubeMill application to stop data fetching on shift changing

In Tube mill application error was coming  on Chart i added solution to resoved it.
In GP2 Application added Return Planning option  in PDI form so that user can Return PDI  and PDO hold request option.
worked to display coil running status in CCL application in Lavel 2 so that user can see running status of coil from level2 screen. 
In GP2 added a option for menually PDO creation so that user can create manually PDO .
Added a feature to auto Popup for sticker in GP application.

Added code to run recoiler design(animation) on every second according UNcoiler machine if the speed value will zero then recoiler design will stop.
In CCL application added code to run UNcoiler according to singnal data that which coil is running .
In CCL application in PDI form two Textbox to understand the person name and reason added Manually_by and menually_reason and created procedure to insert data into table.
In CCL aaplication worked to run Recoiler design shape according to real time machine status .
Visted Tubmill plant to check status of PIms server also visted CCL and GP plant to understand the GR issue in the aaplication and resolve the issue  and in CCL plant understand the other requirement of user.

Visted CCL plant to analysis the issue in CCL application in advance module PDO was not generating properly.
In EMS application created a HMI to display electricity consumtion data also displayed running status of all feeders.
Worked on coding part for EMS application to fetch data from sql database using ado.net .
In the EMS application, a graph has been created to display machine KWH data based on mill selection.
In the EMS application, code has been added to export an Excel sheet of machine data displayed on the screen, including alerts and running status.

Worked on the EMS application to display a graph on the page based on mill data and created a table in the PIMS database to receive data from the EMS database. Also, added a dropdown list in the EMS application to select data based on the mill.
Worked on the EMS application to display the current (KWH) consumption by the mill in a graph and added timestamp as the X-axis in the graph.
Worked on EMS application to display chart responsive and time in X-axis wrote new code to fetch data from Pims tabale and work on designing of chart.
In ems Ap X-axis on the chart added code to show date and time only below of KWH data shown in chart .
Visited plant also worked Ems application to show Dashboard as responsive.

In Tube mill application error was coming  on Chart i added solution to resoved it.
Holiday
In Ems application  Added a new dropdownlist for feeder on mill selection for selected mill so that user can see data in chart based on mill and feeder .
In Tube Application Added Indicator so that user can check that Mill and HF status that Plc is runing or not .
In CCL application Discussed or desined a window form to handle coil status so that user can change coil status .

In EMS application changed the code to fetch Actual value of MILL dynamically so that if New mill or feeder will be added in future Automatically it will reflect in the Home page also modified Sql query to get latest data .
In EMS application Added time indicator in home page so  that user can see last updated data and updated time also added timer so that data will refresh automatically in every two minutes.
In CCL application handled error during connection time out modified code that if connection will lose it will smoothly try again to connect the data base without showing any error.
In Spm apllicatipon in home changes screen Tracking Page left to right Alignment also added in  PDI module  two fields HR crown and hr wedge so that user can enter Crown and wedge data .
In Ems application Added date calender modified code to fetch data according to date and shift wise. so that user see data in chart , date wise and shift wise.

In Ems application changed to fetch Speed,Size,CustomerName from database and displayed in to HMI so that user can see  the same in screen of home page. 
In Ems application changed query in data base fetch size speed data in round digit in Home page also modified home page Added home and dashboard button in side menubaar.
In CCL Apllication Added code to generate txt file with date and time disconnection of the server so that user can see issue of database connection and time .
Went to CCL plant to install required SDK  PDI_App application in the DMZ server also Discussed avout message structure in Coil printing.
In CCL application created new page to update coil Id if the signle miss and coil missmatch in that case use can update coil ID accordingly 

In CCL application created a Support and maintainace page using this module user can update coil status accordingly if any signal mismatch .
Added code to update T_coil_Temp table and also created new table named T_CCL_UPDATE_STATUS table to keep  information of modified data 
In CCL application created a TABLE named t_ccl_update_status where coil data and updated statsu will be saved of user so that we can check detail of updated data and also added backend code to insert data.
In CCL application added a  PDO section from there user can see genreted PDO by perticuler mother batch(coil_ID) .

In EMS application added code to display each mill data in diffirent chart in same page . aslo designed new page to display dynamically feeders data in page on the click inside button in mill chart
Design a window form to generate CSV file to print message in the coil for CCL application 
In CCL application changed the code to fetch Four days Filtered data and Visited Plant .
In CCL asplication crteated a Message Structure page  and wrote code to to Generate CSV file with Required data to print sticker in COIL
In  GP2 application added code to display speed  of line and batch coil ID which is in Recoiler and shadle. 

In Tube Mill Application Changed code to fetch data from database according to datewise in previous version only 5 days data user could see but now after changing the code user can see previos all data.
Visited CCL plant to testing PDI application and discussed with user Regarding CCL application and also Visited GP2 plant and discussed with user facing problem .
In GP2 application changed code to resolve the issue (in PDO section data in genral form data was not updating on gridview selection change)  also visted CCL plant for testing of PDI application and GP2 plant visited to discuss the requirment of delay of PDO data.
In CCL database created a view for T_preodic_log table   required for L3 application . also In GP2 application checked delay section to flow of delay data tranfer to MES.
In CCL application in CCL section added code to fetch data of Detaly reason of coil data on agency selection

Visited CCL plant to testing PDI application and also worked on delay section of CCl application to fetch and display delay data of CCL 
In CCl application chaged the code to display 100 % accurate data of GR and PDO .
Solve the Gp2 application issue during Network connectivity issue
Visted GP1 Plant to develop level 2 application and discribe the Gp2 application to new developer.
Created a Report Section in CCL application in that section user can see PDO detail along with PDI detail

Visited GP1 Plant to understand the work structure and project Design for GP1 application also created database and Table to Gp1 Application.
In GP2 application Added a Manual  GR Report section in that section user can see Datewise Manual GR data .
In GP2 application Resolved an error that occurred during GR when the tension value was abnormal.
The database procedure was updated to set the tension value to 0 if it's less than 0..

Created a copy of Gp2 application and changed the code according Gp1 application Required table.

In GP2 application Customer Name Auto-Population:
In manual PDO generation, the customer name was not being inserted automatically, so users had to input it manually during GR.
Now, the code has been updated to insert the customer name with PDO data, so it automatically populates in the GR window without manual input.
IN GP2 application-- PDI and POD Report Updates:
Modified both PDI and POD reports according to user requirements.
In GP2 application Added a option In GR section if Tension value will be less than 0 then the value of all tension will be converted into 0 and handeled exception. 
Leave
Leave

Leave
In CCL application In menually PDO generation section added validation in Duration EndTime can not be less than start Time`
Visited GP1 plant to explore the requirement of GP1 level2 application.
Worked in CCL Application to make front page of all section according to big screen 
Worked in CCL Application to make front page of PDI  section according to big screen 

In CCL application changed all page according big Screen
For GP2 Application added data in T_GL_SOFT_CYCLE and T_GL_HARD_CYCLE table 
In CCL application Added code to validate duration during PDO generation. 
In CCL application Added code to display RLNG data in level 2 application.
Added code to tranfer the RLNG data in MES database.

In CCL application PDO section was not refreshing after search the coil resolved it and also changed the code to refresh data on coil selection.
Added process parameter tag in T_config_tag table and T_Periodic table in database to send data in PDO table and MES table . 
IN CCL application Added code to add New process parameter tag value in PDO table .
IN CCL application Added code to add New process parameter tag value in MES  table changed the procedure to send data in MES table .
IN GP2 application modified the PDO generation procedure according to user Reqirement PDO generation.

Change the transfer to MES procedure to send process parameter tag data in MES table.
IN GP2 application-- changed the PDO generation procedure to handle advance batch generation issue.
In CCL application Added Remark option in manual PDO generation so that we can check all the reason to manual PDO generation.
In CCl Application added code to validate start time and end time should not overlap to other coil start time.
In GP2 Appliaction chaged the procedure to handle advance PDO generation.

In CCL application added code to start Auto GR . also changed the code the handle the issue of extra batch generation .
In CCL application modified delay selection to display shift wise data.
In Delay section modified screen level of Splite delay and handled error due to date formate in client system
For GP1 application added TAG in Kepware to read data from PLC 
configure Kepware to read data 

Worked on GP1 application to insert periodic table tag data from kepware.
Worked on GP1 application to run the application according to the tag data reading from kepware also configure kepware according to PLC.
Worked on GP1 application to generate PDO also changed the procedure  
Worked on SPM application  to run the application according to SPM khopoli plant using backup file of previous application
Worked on SPM application  to save Kepware data in T_periodic_Value_log table  

In SPM application added new table T_plc_tag config and also added column name according to SPM tag 
Added code to Read Event and store data into data base from kepware
Added new PLC tag in to Kepware to read real time data from PLC
Changed the code to save data into  T_Periodic_value_log  table 
Leave

In GP1 application chaged the PDO generation procedure according to GP1 application 

in SPM Added new tag in PLC Tag config table and also aded in Pdo generation procedure . 
Worked on GP1 application to generate PDO and changed the column name in procedure acording to new PLC_Tag_config table.
Change the transfer to MES procedure to send process parameter tag data in MES table.
Worked on Gp1 UA application to run the application on the server and added DLL according to the appliaction made ready for GP1


In GP2 application In adde the code to display  REPORT total weight (GR DONE) also code added to display total count PDO genration data.
IN GP2 application on TRACKING page  coil no. With running POR was not displaying solved the issue and 
IN GP2 application added code to solve POR selection not showing in running . and solve the all runing part issue .
In GP2 application New data pup-op batch id wise into PDI or PDO which will help us to make confirmation as per SO. Following to be added into screen. 1.S.O number 2.Item number 3. Coating mass
In SPM application Added code to save signal data in T_event_Log table


Worked on SPM application to send data of PDO generation in L3 database .
Leave
Worked on SPM application to send set point to PLC according to PDI coil also Worked on GP1 application to send tranfer to MES data 
Worked on GP1 application to generate PDO also changed the procedure  and created New table for GP1 and added code to fectch coil number in llevel 2 page in GP1 application
Worked on SPM application  to write set point in PLC tag and added tag name in PLC tag config table 


Worked on SPM application  to save tag signal data in T_event_log table only conditional data that is true and changed to code to save only one tag data once
Visited GP1 plant to mke  Ready GP1 server system installed requiered DLL to run the GP1 application also changed the PDO genartion to genrate PDO according to new table (GP1_PDI
visited Gp1 plant to check signal issue in PLC and validated data and changed the scan rate in kepware .
In Spm application added code to insert start time for next coil batch generation of mother coil.
Changed the code to save data into  T_Periodic_value_log  table 


Visited Gp1 plant to install required Dll for UA application also visited SPM plant to discuss Set point for SPM application 
Visited GP1 plant to Install visual studio and updation .net
published GP1 application in client system also also published SPM application in client system 
Worked on GP2 application to handle weight issue added code to tolrence 10% in PDI weight in PDO generation code
Worked on SPM application and published in client system and tested application also worked on GP1 application changed the transfer to MES code to resolve during tranfer to MES due connection .
Worked on SPM application to add scrap weight with PDO weight also visited SPM plant to testing application with real time data.
Worked on GP1 application to display weight of PDO also worked on PDO generation procedure to calculate weight.
Worked on GP1 application to generated PDO data on recoiler signal and changed code to move all coil in BR3,BR8 on POR signal also visited GP1 plant to test GP1 application with real time data.
worked on GP1 application to send PDO data in MES table in level 3 also visited Gp1 plant to test application and for GR .
Visted Gp1 plant to testing GP1 plant to test application with real time data and signal .

Worked on Gp1 application to display actual weight in PDO screen and visited plant to test application 
visited SPM plant to test SPM application and modified code to generate baby coil as well as mother coil 
Visited GP1 and SPM plant to test both application changed the PDO generation logic due to signal issue 
Worked on CCL application to display Production report and display data according to coil ID
Visted GP2 plant to ready Entry and Exit system also added code to display TDC of PDO data.
Visited NCRM plant to ready system for pickling application and also tested all system to connect with PLC
Worked on SPM server installed Kepware and also installed required software and made ready server for application.
Worked on Pickling server  installed kepware and also installed required software and made ready server for application.
Added tag name in kepware and read data from plc also configure kepware with ABB
Made ready UA application for RM and pickling application  inserted data into T_periodic table for RM application connected database from one server to other PC and read PLC data using kepware
Added returning batch coil form in CCL and GP1 and GP2 application and added procedure to send data into MES database.
Changed the procedure to send delay data into MES databse in GP2 ,GP1 and CCL application
Made Ready UA application for  ECL server and also adde tag in kepware for ECL
Worked on RM1 and RM2 applications; prepared the server to run the RM1 application.

Configured the RM1 server, connected it with the RM1 PC, and installed the required software.
Worked on the GP1 application to ensure smooth and fast performance; also modified the procedure to handle NULL values of PDI weight.
Prepared the server for the Pickling application, installed the required software, and set up one PC for the HRS application.
Worked on the CCL pulpit server to prepare the Pickling PC and HRS2 PC;
installed the required software and created schemas for the Pickling and HRS2 databases.
worked on pickling application run UA application and installed required library and prepared application to run.
Worked on pickling PC prepaired the system to communicate with server
Worked on ECL application Created Procedure to insert data into PDO level2 table on PDO generation 
Worked on ECL application Created Procedure to insert data into PDO level2 table on PDO generation  and also worked on CCL application to display pop if delay data is pending to submit 
Worked on ECL application to generate PDO changed the code to genrate PDO also creeated a procedure to insert data into PDO_level2 table .
Woked Pickling application to design PDO page also wrote code to bind data from HMI .
Worked on pickling application to design master page for PDO screen

Worked on pickling application designed PDO screen and prepared Query to fetch data from T_pdo_level2 table and binded data to PDO screen
Prepared Procedure to generate PDO in ECL application also changed the code to call PDO generation procedure
Created Trigger for ECL application to generate setpoint after inserting new row in PDI table also worked in Pickling application to bind PDO data in PDO screen.
Worked on ECL application to show the coil on POR and Recoiler section also changed the procedure PDO generation logic also changed the POR assigning logic.
Worked on Pickling application to generate PDO added code to call procedure from UA application also worked on ECL application to changed calculated weight after PDO generation before GR.

Worked in pickling application prepared mater page for PDO screen also mapped data from PDO_level2 table to PDO page.
Worked on pickling application created procedure to generate PDO on EXIT shear signal.
On ECL application added logic to move coil ID from stage to other stage also added code to move coil POR to Recoiler after PDO generation .
worked on pickling application added code to move coil ID on every stage added code to assign coil ID on assign button also created trigger to insert in T_scheduling table after inserting data in PDI table.
Worked on ECL application created a trigger to insert in T_scheduling table after inserting in PDI_level2 table also created procedure to generate PDO for Pickling application.
Worked on RM application prepared HMI tracking page and mapped data from pages also mapped PDO data to PDO pdo page.

Worked on RM application to generate PDO added code to call the PDO generation procedure .
Worked on pickling application prepaired client PC to run the pickling application also changed the code to run application in server added new column in periodic value log.
Added code in ECL application to generate PDO same as mother coil_ID .
Worked on RM application on UA application code to communicate with kepware .
Worked on SPM application added code to generate PDO on exit shear .
Worked on SPM application created procedure in database to generate PDO also created a procedure to send data to L3.
Worked on Pickling application Prepaired system to run the UA application installed required software


worked Pickling application of WCRM  added code to display POR1 coil and POR2 coil after Start signal
worked Pickling application of WCRM added code to generate PDO also modified P_Coil_Assign procedure also added code to move coil POR to recoiler after PDO generation. 
Worked on HRS application to display PDI data also prepared a query to fetch data from table also changed the code in RM1 application to display PDO and PDI data .
Worked on HRS application to display PDO data added new design according to user also added code to fetch data from database. also worked in Pickling application to generate PDO of WCRM.
Worked on GP1 application added code to display as green color if  GR is DONE .


Worked on HRS application prepared UA application to run on the server .
worked on HRS application added code to display PDO data from table and added required tag in kepware.
worked on Gp1 and GP2 application to print sticker of PDO data after GR.
Visited Pickling and HRS plant to install system and application also changed PDO screen according to customer requirement.
Visited pickling mill to test the application for UAT testing also validate signal data also visited  HRS mill for application testing.

Worked on RM1 application added new tag in KEPWARE and also added in T_PLC_TAG_CONFIG table and also added  code to insert data into Periodic table
worked on RM5 application prepared UA application added TAG in T_PLC_TAG_config table.
Worked on ECL application display Delay details and created procedure to store delay data.


Installed New SPM client PC and prepared system to Run the SPM application also installed required Software.
installed new SPM client PC in NCRM and added SPM application also installed required software.
Visited NCRM pickling Line to Test application to installed system and application with Real Time data.
Visited WCRM Line to Test application with Real Time data  and visited Gp1 and Spm plant to check application status also tested SPM applicationn running process .
Visited Pickling Line in WCRM to Test the application with Real time signal and data.

Visited WCRM pickling mill to installation PC at exit point also went to electrical team for Event TAG.

"
Visted WCRM pickling mill to test application and given training to operator ."

Visited WCRM pickling mill to installation PC at exit point also went to electrical team for Event TAG.
Added code in CCL application to validate actual weight so that user cannot insert greater than PDI weight. also worked in ECL application in Delay section to split delay.  
visited ECL to TEST the application and train the user for GR and POR selection also explained whole function of ECLL application.
Worked on ECL application added code to send Delay data from level 2 to MES database also changed same in SPM application .
Added new tag in GP1 application also made a list tag name to get Tag address from electrical team also visited pickling to test the application and to give training to the user.


Worked on Pickling application changed procedure according to new signal also visited pickling Line.
Worked on GP2 application changed PDO generation procedure to correct Length. also visited ECL Line to give training of ECL application to the user . 
Visited ECL Line to Test application and to give training to the user also visited Pickling Line . 
Visited Pickling line to Test the application and improved application .
Worked on Pickling application added code to display PDO data .


Worked  on Gp1 application added new code to fetch data from Periodic_value_log table and made more smooth the application .
Visited Pickling line to Test the application and improved application due to Poor signal from PLC data.
visted HRS2 line to test the application also worked on delay screen SPM application.
Worked on SPM and ECL delay code to send delay data to the MES database.also worked on Transfer to MES procedure in HRS application.
Visited HRS2 line to test the application also worked on SPM and ECL application to send delay data Like agency and remark to the MES database


Worked on Pickling application to move coil saddle 1 to saddle 2 to POR also  visited Pickling to Test the application.
Visited Pickling Line to Test the application and test the signal and improved the application function.
Worked on RM1 application added delay page and fetched data from agency table and bonded with HMI. 
Worked on reverse Mill application added delay module also added delay module in ECL application.
Worked on HRS application added code to display  as heighlighted row which coil has been GR done.
Visited the HRS Line to test the application with live signals and provided training to the operator
Worked on the SPM application in the delay module, prepared a delay procedure named P_delay_register, and implemented the same in the ECL application.
Worked on the Pickling application in the delay module, prepared a delay procedure named P_delay_register, and implemented the same in the HRS application as well as other applications (NCRM).
Worked on the RM2 application and prepared the procedure P_delay_register to store delay data in the delay table. Also modified the PDO generation procedure to handle NULL values in output thickness
Worked on GP2 application Added New process parameter in PDO also added T_PLC_Config table .
Changed Transfer to MES procedure to send Extra process parameter with PDO data in GP2 application also woirked on PDI return module in Pickling application.
visited RM1 mill to install RM1 PC to run the application also installed required software and tested the application.
Worked on RM1 Entry page added POR and next coil selection option also worked on HRS and RM1 PDI return procedure to return planning module .
Worked on RM1 application added code to save data on  select POR data and saddle also modified procedure to move data from saddle to POR .
Visited Pickling Line to test the application with signal also given traning to the operator
Worked on GP2 application changed the PDO generation procedure to handle PDO generation issue to incorrect length.
Worked on Picking application changed the coil_update_tracking procedure to move coil from POR to recoiler on Uncoiler start signal.
Worked on Pickling application to remove Coil ID from POR on Coil finish signal also visited Pickling Line to Test the application.
Visited Pickling line to Test the application and also given training to the user.
Worked on dealy module in CCL and SPM application to splite Next delay adding 1 minute so that data can not coflict during MES tranfer due to duplicate data.


Visted Pickling Line to test the application and given training to the operator.
worked on CCL application coil sequence module , after sending PDI data from MES to Level2 user can Re-arrange coil ID according to replanning.
Visited HRS2 line to test application and given training to the operator. 
Worked on CCL application  on delay Split module to handle error during split due to wrong data. also worked in Gp1 application added tag name in T_plc_tag_config table to send set point.
Worked on GP1 application to write set point to PLC tag added code also added tag name in t_dep_config table.also visited all line to install printer with printer partner.

Worked on GP2 application changed the PDO generation procedure to handle PDO generation issue to incorrect length.
Worked on Picking application changed the coil_update_tracking procedure to move coil from POR to recoiler on Uncoiler start signal.
Worked on Gp1 application added code to unload coil from GP2 tracking page.
Worked on SPM and ECL application added code to display memory used by application and instance of application opened .
Visited Gp1 Line to install PC at exit and entry .
Created services for UA Application of CCL,GP1 Line.
visited Pickling ,RM1,RM1 to change all PC and installed required software
worked on CCL application added a procedure to Log or save all error in T_error_log Table to handling error.
Created services for UA Application of GP2 Line.
installed PC at pickling Line NCRM exit point.
worked on GP2 application added code to on delay module to disable Start and stop timing .
worked on GP1 application to correct delay booking on C shift.

worked on printer to print correctly as requirement of planning Teams also installed printer .."
worked on ECL application on sticker printing mapped all columns with required attribute on screen.


Worked on CCL application added code to Auto popup print sticker  also added same feature in GP2 and GP1 application.
Visited GP1 plant to install exit and entry monitor and PC. 
worked on HRS application on coil rejection module so that user can reject coil accordingly.
Worked on GP1 application added new procedure to write setpoint in PLC tag also added new TDC master data.
Worked on CCL application display  selected colums and made a report module so that user can see only importent column in that section.


worked on GP1 application to send setpoint to the PLC added new procedure to write setpoint .
Visited CCL to install printer at EXIT .
Worked on Gp1 application setup module added new Hard cycle and soft cycle master data in table also visited NCRM to install Printer.
visited NCRM to test sticker printing for all mill RM1,RM2,Rm5 . 
worked on SPM application to add elongation data on forth time so that user check all elongation on  different length.


Worked on GP1 application added new PLC tag to send setup data also changed procedure to save setup calculation data.
Created a trigger to save setup data in Table with TDC data.
worked on CCL application added code to generate auto sticker after PDO generation also installed printer on GP1 Line.
Worked on SPM application to add elongation data in PDO generation procedure to store process data level2 table.
visited NCRM to install new server (SPM,RM1,RM2,RM5)


Worked on GP1 application added new procedure to calculate setup Point.
Worked on SPM application added new procedure to insert elongation data into T_elongation_Temp and also added code into Pdo_insert_data procedure to insert data into table.

Worked on GP1 application added code  to send setpoint to dummy Tag for PLC
Visited all line to check printer and resolved the issue at GP2 and GP1 line.
Created a New HMI to get data from  API .
created new API for new application 
visited SPM to install new server at SPM ECR.


Created a New API to receive data from HMI .
Worked on GP1 application to Write setpoint to PLC tag .
Worked on RM1 application changed code to save data in T_periodic_value_log table and  created service
Worked on GP1 application chaged code to print sticker.
Vistited GP1 to check printer issue .


Visited SPM Ecr to configure Switch for New server.
Visited Tube MIll to understand the process.
Worked on gp1 application added new original PLC Tag to write setpoint.
worked on NCRM server compiled all procedure in new server.
Visited NCRM to install required library for react application.l


Worked on RLNG consumption application created an API to receive data from database.
Worked on RLNG consumption application created an HMI to display the RLNG consumption data .
Worked on RLNG consumption application created an HMI to display the RLNG consumption and host application IIS and tested the application also change Code to receive data from API.
Worked on RLNG application added code to display RLNG consumption data according to date filter.
Worked on the RLNG application, added code to fetch data based on GP1, GP2, and CCL, and also created a new API to fetch data from different databases as per requirements.
Worked on GP1 application added new TAG to write setpoint in PLC .
Worked on CCL application added new procedure to validate duplicate data before send data in MES database.
Created RLNG application more responsive and attractive .
Worked ON CCL application testing to Print sticker message on Coil.


Worked in RLNG project changed the code to display data according to date and chart wise.
Worked on GP2 application clear Two table data Periodic value log and periodic data log table and added indexing on date time column.
Worked on CCL application clear Two table data Periodic value log and periodic data log table and added indexing on date time column also changed the code to save insert T_periodic_data_log table and published new file.
worked on GP2 application changed the code to save insert T_periodic_data_log table and published new file.
Worked on Mill1 application to resolve printer issue also worked in return planning module lower case to upper case for  coilID.


Worked on the RLNG application, added code to fetch data based on GP1, GP2, and CCL, and also created a new API to fetch data from different databases as per requirements.
Worked on GP1 application added new TAG to write setpoint in PLC .
Worked on CCL application added new procedure to validate duplicate data before send data in MES database.
Created RLNG application more responsive and attractive .
Worked ON CCL application testing to Print sticker message on Coil.


worked on RLNG application moved application on SPM server and installed 
Visited CCL to investigate the coil weight issue using a weighing machine
worked on CCL application changed procedure to update coil weight accoirding to weighing machine after validate the weight of coilso that coil weight can not update twice.
visited CCL to testing printer function on coil  with level 2 application 
visited NCRM to install new server also added all new database for all application.
Visited NCRM to install required library for all application in new server. 


Installed NEW GP2 server on GP2 ECR.
Worked on GP1 application changed the procedure to write setup calculation data on PLC tag.
Visited Dormitory to make installed newly server
Worked on CCL application to update weight using weighing machine on capture weight signal.
Worked on SPM application made more user-friendly as fast for the user so that user can use it as fast.
Visited Pickling line to solve Printer issue at exit location.
Visited SPM to resolve sticker quality issue .
Worked on CCL aaplication to resolve the issue of multiple PDO transfer to MES database.
Visited pickling line to resolve application and printer issue  .
