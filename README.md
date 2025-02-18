@model List<ProductionUnit>

<table class="table table-bordered">
    <thead>
        <tr>
            <th>Unit Name</th>
            <th>Run</th>
            <th>Stop</th>
            <th>Low</th>
            <th>High</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var unit in Model)
        {
            <tr>
                <td>@unit.Name</td>
                <td><span class="dot @(unit.IsRunning ? "green" : "red")"></span></td>
                <td><span class="dot @(!unit.IsRunning ? "green" : "red")"></span></td>
                <td><span class="dot @(!unit.IsHigh ? "green" : "red")"></span></td>
                <td><span class="dot @(unit.IsHigh ? "green" : "red")"></span></td>
            </tr>
        }
    </tbody>
</table>

<style>
    .dot {
        height: 15px;
        width: 15px;
        border-radius: 50%;
        display: inline-block;
    }
    .green { background-color: green; }
    .red { background-color: red; }
</style>