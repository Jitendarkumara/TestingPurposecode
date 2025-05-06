1. Customer Name Auto-Population:
In manual PDO generation, the customer name was not being inserted automatically, so users had to input it manually during GR.
Now, the code has been updated to insert the customer name with PDO data, so it automatically populates in the GR window without manual input.


2. PDI and POD Report Updates:
Modified both PDI and POD reports according to user requirements.


3. Confirmation Message Before GR:
Added a confirmation message prompt before generating the GR.


4. Date and Time in Level 2:
Displayed the scheduled PDI's date and time in the Level 2 section.


5. Auto Calculation of PDO Duration:
Automatically calculated the duration in minutes for the PDO based on the start and end times.


6. Print Sticker Popup Issue:
Resolved the issue where the print sticker popup was displaying repeatedly.


7. PDO Coil ID Case Handling:
Handled the scenario where users entered the coil ID in lowercase during PDO generation.
The coil ID is now converted to uppercase before inserting into the database.


8. Abnormal Tension Value Handling During GR:
Resolved an error that occurred during GR when the tension value was abnormal.
The database procedure was updated to set the tension value to 0 if it's less than 0.


9. Manual GR Report Window:
Added a new report window to display manual GR data, filtered by date.


10. Input Coil ID Lock During PDO Generation:
In some cases, users were changing the input coil ID (e.g., from K58562000 to k58562000).
This issue is now handled by disabling the coil ID textbox (setting Enabled = false) after input.