Introduction
Tata steel Meramandali has a 0.12 MT/Year capacity Colour coating line (CCL)  at CRM Complex  premise .This CCL has two POR and single recoiler configuration. CCL line consists of two furnace one prime oven and one finish oven containing 4 heating zones each. Zero spangled galvanized coil from (GP2,GP3) is the feed for this line. This line is a continuous line so the head end and tail end of coils are stitched together with a punch hole at middle for the tracking at the time of stitching entry speed is made to 0 , then the coil goes through a vertical lopper (Entry Accumulator) to account for the line stop at entry stitching process, coil passes through different process stage first through the TLL for surface flatness, then goes through a degreasing section where the surface of coil is treated with alkali and electrolytes to remove any impurities and then goes through a Hot air dryer to Dry out the cleaning agents. In the further process coil passes through chemical coater and chemical dryer to add a layer or chemical and    coil enters the coater area for application of the prime coating on both surface (Top and Bottom). After prime coater coil enters the Prime oven subsequently enters the Finish top coater and before entering to the finish oven finish back coat is applied through finish back coater. Coating rolls are disengaged at the time stitch passing to prevent the applicator roll damage. After the finish oven the coil enters the exit vertical looper (Exit Accumulator) to account for the zero speed at the coil cut at the process exit. At exit crop shear is operated there to part coil into different parts single on 
Coil is recoiled in the recoiler and mandrel is collapsed to remove the coil from the recoiler and transferred through 
Coil transport Car. For tracking of stitch 4 WPD(Weld point Detection) are present in line at different location ( Briddle roll 2,Briddle roll 6,Stearing roll 6,Briddle roll 9).

 
1 
	
HMI 
	
Human machine interface 
2 
PDI 
Primary data Input 
PDO 
Primary data Output 
4 
CCL 
Color Coating Line 
5 
OPC 
Open Protocol Communication 
CRM 
Cold Rolling Mill 
7 
WPD 
Weld Point Detector 
8 
	
LEVEL-3 
Production planning & scheduling system 
9 
GWT 
Grade Width Thickness 
 Description of the Module
The level 2 system for colour coating line aims towards the regularity of coil scheduling, Remove manual intervention in many process different functionalities are 
Tracking page
PDI management
Coil Schedule management
Setpoint Management
PDO management
Delay management
Process data acquisition
Reports
Impact of the Module
The Importance of the Level 2 system is to provide optimized setpoint, gather the process data to make PDO, and send the process data to the higher Level system.
 
Process description
PDI Management
This process download the Primary data like Coil Id, Input Weight, thickness, width, Grade etc as per required data for business from MES to Level 2 Systsem
Setpoint Management
This Process creates setups according to GWT for the specific coil. From refence data and the historical data 
Coil Tracking
This process tracks the Coil from the Loading into POR till the coil exit from the Recoiler end through WPD signals.
PDO Management
This Process Creates single point data from the acquired process data and send the data to the MES for the auto GRN
Delay Management
This module captures the Line outage by the line speed for example when the Line speed becomes 0 the delay starts and after the line speed >0 delay end and send these delay data to MES for delay booking.
Process data acquisition
This module use the OPC for capturing the Data from the respective level 1 PLC and capture the data against the specific tag address and stores in the CCLDB data base.
6 months of periodic data are stored in the data base for further analysis.
 
