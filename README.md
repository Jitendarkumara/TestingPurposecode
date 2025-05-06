In mamually PDO genration was not inserting Customer name so user had to input manually during GR
I added code to insert customer name with PDO data so that automatically populate customer name in the 
window no need to input .

PDI and POD Report has been modified according to user requirement.

Added a confirmation message before GR 

dispaled date and Time with scheduled PDI in Level 2 section .

Added auto calculation duration minute of the PDO from starting Time and End Time .

popup of print sticker was diplaying again and again solved it now.

Handeled PDO coil ID generation lower case if the user input lower case coil ID soleved as inserting data
after converting upper case.

Handeled error during GR if the Tension value will be abnormal to resolve it changed in database procedure
if the tension value will less than 0 it will send 0 value .

Added a report window to display date wise Manual GR report.

some case user was changing Input coil ID during PDO genration like coil ID was K58562000 after
changing k58562000 it has handled using disable false to the textbox .
