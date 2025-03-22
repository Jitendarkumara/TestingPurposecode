 const response = await fetch(url);
 if (!response.ok) throw new Error(`HTTP error! Status: ${response.status}`);

 const data = await response.json();
 console.log("Fetched Data:", data);

 if (!data || data.length === 0) {
     console.warn("No data available");
          
     return;
 }

     <div id="chart-container" style="margin-top:3px">
        <canvas id="kwhChart"></canvas>
    </div>
