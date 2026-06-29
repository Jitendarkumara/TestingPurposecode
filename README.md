The Level‑2 automation application for CRM (Cold Rolling Mill) lines serves as the process control and optimization layer between basic machine automation (Level‑1) and plant/business systems (Level‑3/ERP).


Purpose -
1) Enables automatic coil tracking and data handling

2) Supports recipe and setup management based on coil data

3) Maintains production data, history, and traceability

4) Improves process efficiency, consistency, and productivity

 

 

Application structure is mostly same for all the lines.

CCL
GP
SPM
ECL
RM
PL
HRS
 

Accordingly, modifications are made that are customized according to line sensors and process.

Level‑2 Automation Application – User Guide (Formatted)

1. 🔐 Login

Enter the username and password provided by the Level‑2 team.
Click on the LOGIN button.
The Version field shows the application version.
2. 📍 Tracking Page

After successful login, the user will be redirected to the Tracking Page.

This page displays:
Critical process parameters
Coil tracking information
2.1 Available Tabs

Multiple tabs are available on this page.
Clicking any tab navigates the user to the respective section.
3. ⚙️ Level‑2 Tab

Coils are automatically assigned to PORs after being saved from MES.

3.1 Planned Coil

Displays all planned coils scheduled for Level‑2.
3.2 Coil Scheduling Panel

SAVE → Saves coils to the respective location
CANCEL → Cancels assignment
REJECT REQUEST → Opens Coil Reject Request window
Select checkbox to mark coils for rejection
3.3 Coil Reject Request

Selected coil appears in the Coil ID textbox
Rejection types:
Temp → Temporary rejection (coil remains in planned list)
Per → Permanent rejection (sent to MES for planning change; visible to PPC)
4. 📋 PDI Tab (Process Data Input)

4.1 Coil Details

Displays list of coil IDs received from MES
4.2 PDI Details

Shows parameters grouped into:
Coil details
Quality attributes
Order attributes
4.3 Return Planning

Used to return coils to MES for:
Re-planning
Other reasons
5. 🎯 Setpoint Tab

5.1 Setpoint Output

Displays Level‑2 setpoint output for the selected coil.
6. 📊 PDO Tab (Production Data Output)

Displays production data of selected coil:
Coil details
Exit/SO attributes
Coil history
Mother & daughter coil
Start time, end time
Length and calculated weight
Shows coils produced in CCL based on coil end signal
6.1 MES Transfer

Click TRANSFER MES to send coil and process data to C3DIS for GRN
6.2 GRN Status (Left Panel)

Pending → Not highlighted in green
Completed → Highlighted in green
7. ⏱️ Delay Tab

Displays delays captured from Level‑2
Steps:

Enter:
Delay agency
Reason code
Remarks
Click UPDATE
Click TRANSFER to send delay data to MES
8. 📈 Report Tab

Displays details of all coils processed through Level‑2.
