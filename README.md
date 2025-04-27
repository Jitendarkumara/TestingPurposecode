@{
    Layout = null;
}

<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Furnace Delay List</title>
    <link href="https://cdn.datatables.net/1.13.6/css/jquery.dataTables.min.css" rel="stylesheet" />
</head>
<body>
    <div>
        <h2>Delay List</h2>
        <table id="employeeTable" class="display" style="width:100%">
            <thead>
                <tr>
                    <th>SHIFT</th>
                    <th>FURNACE</th>
                    <th>TYPE</th>
                    <th>DELAY</th>
                    <th>START TIME</th>
                    <th>END TIME</th>
                </tr>
            </thead>
        </table>
    </div>
</body>

<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script src="https://cdn.datatables.net/1.13.6/js/jquery.dataTables.min.js"></script>

<script>
    $(document).ready(function () {
        $('#employeeTable').DataTable({
            "ajax": {
                "url": "@Url.Action("Furnace_Delay", "Sinter_Plant")",
                "type": "GET",
                "datatype": "json"
            },
            "columns": [
                { "data": "SHIFT" },
                { "data": "FURNACE" },
                { "data": "TYPE" },
                { "data": "DELAY" },
                { "data": "START_TIME" },
                { "data": "END_TIME" }
            ],
            "responsive": true,
            "autoWidth": false
        });
    });
</script>
</html>