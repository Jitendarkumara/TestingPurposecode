 <div class="filter-container" style="margin-top:5px">
     <div class="row">
         <div class="col-2">
             <label for="millSelect">Select Mill:</label>
             <select id="millSelect" class="form-select"></select>
         </div>
         <div class="col-2">
             <label for="FeederSelect">Select Feeder:</label>
             <select id="FeederSelect" class="form-select"></select>
         </div>
         <!-- Date Filter -->
         <div class="col-2">
             <label for="dateFilter">Select Date:</label>
             <input type="date" id="dateFilter" class="form-control">
         </div>

         <!-- Shift Selection -->
         <div class="col-2">
             <label for="shiftSelect">Select Shift:</label>
             <select id="shiftSelect" class="form-select">
                 <option selected disabled>Select Shift</option>
                 <option value="D">All</option>
                 <option value="A">Shift A</option>
                 <option value="B">Shift B</option>
                 <option value="C">Shift C</option>
      
             </select>
         </div>

         <!-- Status Selection -->
         <div class="col-md-2">
             <label for="statusSelect">Select Status:</label>
             <select id="statusSelect" class="form-select">
                 <option selected disabled>Select Status</option>
                 <option value="2">All</option>
                 <option value="1">Running</option>
                 <option value="0">Stop</option>
             
             </select>
         </div>

         <!-- Home Button -->
         <div class="col-md-2">
             <button class="btn btn-custom" onclick="HomeLoad()">Home</button>
         </div>
     </div>
 </div>
