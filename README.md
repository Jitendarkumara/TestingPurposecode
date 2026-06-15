[15/06, 12:23 pm] Mahi Singh Napasoft: return (
  <div className="dashboard">

    <div className="header">
      <h1>RLNG Consumption Dashboard</h1>
      <p>Monitor RLNG Usage and Consumption</p>
    </div>

    <div className="filter-card">

      {xAxisType === "date" && (
        <>
          <div className="filter-group">
            <label>From Date</label>
            <input
              type="date"
              value={fromDate}
              onChange={(e) => setFromDate(e.target.value)}
            />
          </div>

          <div className="filter-group">
            <label>To Date</label>
            <input
              type="date"
              value={toDate}
              onChange={(e) => setToDate(e.target.value)}
            />
          </div>
        </>
      )}

      {xAxisType === "coilid" && (
        <div className="filter-group">
          <label>Select Date</label>
          <input
            type="date"
            value={coilDate}
            onChange={(e) => setCoilDate(e.target.value)}
          />
        </div>
      )}

      <div className="filter-group">
        <label>View Type</label>
        <select
          value={xAxisType}
          onChange={(e) => setXAxisType(e.target.value)}
        >
          <option value="date">Date Wise</option>
          <option value="coilid">Coil Wise</option>
        </select>
      </div>

      <div className="filter-group">
        <label>Chart Type</label>
        <select
          value={chartType}
          onChange={(e) => setChartType(e.target.value)}
        >
          <option value="bar">Bar Chart</option>
          <option value="line">Line Chart</option>
        </select>
      </div>

    </div>

    <div className="chart-card">

      {loading ? (
        <div className="loader">
          Loading Data...
        </div>
      ) : (
        <ResponsiveContainer width="100%" height={500}>

          {chartType === "bar" ? (

            <BarChart data={filteredData}>
              <CartesianGrid strokeDasharray="3 3" />
              <XAxis dataKey={xAxisType} />
              <YAxis />
              <Tooltip />
              <Legend />
              <Bar
                dataKey="consumption"
                fill="#1976d2"
                radius={[8, 8, 0, 0]}
              />
            </BarChart>

          ) : (

            <LineChart data={filteredData}>
              <CartesianGrid strokeDasharray="3 3" />
              <XAxis dataKey={xAxisType} />
              <YAxis />
              <Tooltip />
              <Legend />

              <Line
                type="monotone"
                dataKey="consumption"
                stroke="#1976d2"
                strokeWidth={3}
              />
            </LineChart>

          )}

        </ResponsiveContainer>
      )}

    </div>

  </div>
);
[15/06, 12:23 pm] Mahi Singh Napasoft: body {
  margin: 0;
  font-family: Segoe UI, sans-serif;
  background: #f4f6f9;
}

.dashboard {
  padding: 25px;
}

.header {
  background: linear-gradient(135deg, #1565c0, #42a5f5);
  color: white;
  padding: 25px;
  border-radius: 15px;
  margin-bottom: 20px;
  box-shadow: 0px 5px 15px rgba(0,0,0,0.15);
}

.header h1 {
  margin: 0;
  font-size: 28px;
}

.header p {
  margin-top: 8px;
}

.filter-card {
  background: white;
  border-radius: 15px;
  padding: 20px;
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  margin-bottom: 20px;
  box-shadow: 0px 4px 12px rgba(0,0,0,0.08);
}

.filter-group {
  display: flex;
  flex-direction: column;
  min-width: 180px;
}

.filter-group label {
  margin-bottom: 6px;
  font-weight: 600;
}

.filter-group input,
.filter-group select {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 8px;
}

.filter-group input:focus,
.filter-group select:focus {
  outline: none;
  border-color: #1976d2;
}

.chart-card {
  background: white;
  padding: 25px;
  border-radius: 15px;
  box-shadow: 0px 4px 12px rgba(0,0,0,0.08);
}

.loader {
  text-align: center;
  font-size: 20px;
  color: #1976d2;
  padding: 50px;
}

@media(max-width:768px) {
  .filter-card {
    flex-direction: column;
  }

  .filter-group {
    width: 100%;
  }
}