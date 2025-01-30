<form asp-action="SaveTubeInput" method="post" >
	<div class="form-group">
		<label for="inputDateTime">Date an Time:</label>
		<input type="datetime-local" id="inputDateTime" name="CreatedOn" required class="form-control" />
	</div>
	<div class="form-group">
		<label for="od">OD:</label>
		<input type="text" id="od" name="OD" placeholder="OD" required class="form-control" />
	</div>
	<div class="form-group">
		<label for="thickness">Thickness:</label>
		<input type="text" id="thickness" name="Thickness" placeholder="Thickness" required class="form-control" />
	</div>
	<div class="form-group">
		<label for="grade">Grade:</label>
		<input type="text" id="grade" name="Grade" placeholder="Grade" required class="form-control" />
	</div>
	<div class="form-group">
		<label for="customer">Customer:</label>
		<input type="text" id="customer" name="Customer" placeholder="Customer" required class="form-control" />
	</div>
	<div class="form-group">
		<label for="MillNo">MillNo:</label>
		<input type="text" id="MillNo" name="MillNo" placeholder="MillNo" required class="form-control" />
	</div>
	<button type="submit" id="submitButton" class="btn btn-primary">Submit</button>
</form>
