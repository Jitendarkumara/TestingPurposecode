
@{
    Layout = null;
}

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Production Report</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            background-color: #d4e3d1;
            font-family: Arial, sans-serif;
        }

        .container-box {
            background-color: #2f6d30;
            padding: 20px;
            border-radius: 10px;
            color: white;
        }

        .table-section {
            background-color: #f0f78f;
            color: black;
            border-radius: 5px;
            overflow-x: auto;
        }

        .table th {
            background-color: #e3e6bc;
            text-align: center;
        }

        .table td {
            text-align: center;
        }

        .action-buttons button {
            margin-right: 10px;
        }

        .section-title {
            font-size: 24px;
            color: orange;
            margin-bottom: 20px;
        }

        .date-box {
            margin-bottom: 15px;
        }
        
        #tbl_table_container{
          border:1px solid;          
        }
            
         
        
    </style>
</head>
<body>

    <div class="container my-4">
        <div class="row">
            <div class="col-md-10">
                <div class="container-box">
                    <div class="section-title">Production</div>
                    <div class="date-box d-flex align-items-center">                      
                        <label for="tbFDatePick"><strong>Date:</strong>&nbsp;</label>
                        <input id="tbFDatePick" size="11" style="height:35px;">
                        <button class="btn btn-secondary ms-2">Refresh Balance</button>
                    </div>
                    <!-- Main Table -->
                    <div class="table-section mb-4">                        
                        <div>
                            <table class="table table-bordered" id="tbl_table_container"></table>
                        </div>  
                    </div>

                    <!-- Actual Breakup -->
                    <div class="table-section p-3 mb-4">
                        <h5 class="mb-3">ACTUAL BREAKUP</h5>
                        <table class="table table-bordered">
                            <thead>
                                <tr>
                                    <th></th>
                                    <th>LD1 Tons</th>
                                    <th>LD2 Tons</th>
                                    <th>LD3 Tons</th>
                                    <th>MRP Tons</th>
                                    <th>No of TP</th>
                                    <th>No of Slag Ladle</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr>
                                    <td>On Date</td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                </tr>
                                <tr>
                                    <td>To Date A - F</td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                </tr>
                                <tr>
                                    <td>To Date A - G</td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                </tr>
                                <tr>
                                    <td>To Date A - I</td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                    <td></td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                    <!-- Action Buttons -->
                    <div class="action-buttons">
                        <button class="btn btn-success">Save</button>
                        <button class="btn btn-secondary">Back</button>
                        <button class="btn btn-primary">Raw Mat Cons</button>
                        <button class="btn btn-info">Prod Details</button>
                        <button class="btn btn-warning">HM Distribution</button>
                        <button class="btn btn-danger">Delay Details</button>
                        <button class="btn btn-success">Calculate Now</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</body>
</html>
<script src="@Url.Content("~/bower_components/jquery/dist/jquery.min.js")"></script>
<script src="@Url.Content("~/bower_components/bootstrap-daterangepicker/bootstrap-daterangepicker-1.3.21.js")"></script>    
<script src="@Url.Content("~/bower_components/bootstrap-datepicker/dist/js/bootstrap-datepicker.min.js")"></script>
<script type="text/javascript">
    let lsSelectedFDate;
    $('document').ready(function () {
        debugger;
        lsSelectedFDate = '@DateTime.Today.AddDays(-1).ToString("dd-MM-yyyy", new System.Globalization.CultureInfo("en-GB"))';                
        $('#tbFDatePick').val(lsSelectedFDate);
        $('#tbFDatePick').datepicker({
            format: "dd-mm-yyyy",
            autoclose: true,
            todayHighlight: true,
            changeMonth: true,
            changeYear: true,            
            setDate: lsSelectedFDate,
        });
        $('#tbFDatePick').on('changeDate', function () {            
            let getFormattedDate = $('#tbFDatePick').datepicker('getFormattedDate');            
            $('#tbFDatePick').val(getFormattedDate);           
            lsSelectedFDate = $('#tbFDatePick').val();
            DisplayATOFProduction(lsSelectedFDate);
        });
        DisplayATOFProduction(lsSelectedFDate);
    });       
    function DisplayATOFProduction(lsSelectedFDate) {
        debugger;
        var lsURL = '@Url.Action("DisplayATOFProduction", "Blast_Furnace")';
        $.ajax({
                url: lsURL,
                method: "POST",
                dataType: "json",
                data: {
                    Fdate: lsSelectedFDate
                },
                success: function (response) {
                    $("#tbl_table_container").empty();
                    var obj = JSON.parse(response);
                    const table = $("<table>");
                    //const table = $("#tbl_table_container");
                   const thead = $("<thead>").appendTo(table);
                    const headerRow = $("<tr>").appendTo(thead);
                    headerRow.append($("<th>").text("Date Time"));
                    headerRow.append($("<th>").text("Furnace"));
                    headerRow.append($("<th>").text("Actual On Date"));
                    headerRow.append($("<th>").text("Actual To Date"));
                    headerRow.append($("<th>").text("Reported On Date"));
                    headerRow.append($("<th>").text("Reported To Date"));
                    headerRow.append($("<th>").text("Balance"));
                    const tbody = $("<tbody>").appendTo(table);
                    obj.forEach(function (item) {
                        const row = $("<tr>").appendTo(tbody);
                        row.append($("<td>").text(item.DATE_C));
                        row.append($("<td>").text(item.FUR_C));
                        row.append($("<td>").text(item.ACT_C));
                        row.append($("<td>"));
                        //row.append($("<td>").text(item.REP_C));
                        row.append($("<input type='text'>"));
                        row.append($("<td>"));
                        row.append($("<td>"));
                        row.append($("<td>").text(item.BAL_C));
                    });
                    table.addClass
                   $("#tbl_table_container").append(table);
                    return false;
                },
                error: function () {
                    return false;
                }
            }
       );
    }
</script>
