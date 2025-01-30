data.forEach(item => {
    let dateObj = new Date(item.timestamp);

    // ✅ Manually format to 24-hour format (YYYY-MM-DD HH:mm:ss)
    let formattedTimestamp = dateObj.getFullYear() + "-" +
                             String(dateObj.getMonth() + 1).padStart(2, '0') + "-" +
                             String(dateObj.getDate()).padStart(2, '0') + " " +
                             String(dateObj.getHours()).padStart(2, '0') + ":" +
                             String(dateObj.getMinutes()).padStart(2, '0') + ":" +
                             String(dateObj.getSeconds()).padStart(2, '0');

    let row = `<tr class="DS">
                <td>${formattedTimestamp}</td>
                <td>${Math.round(item.voltage * 100) / 100}</td>
                <td>${Math.round(item.currents * 100) / 100}</td>
                <td>${Math.round(item.power * 100) / 100}</td>
                <td>${Math.round(item.frequency * 100) / 100}</td>
                <td>${Math.round(item.speed * 100) / 100}</td>
                <td>${item.od}</td>
                <td>${item.grade}</td>
                <td class="DS-error" data-value="${item.alarm}">${item.mill}</td>
                <td data-value="${item.erroneous_Parameter}">${item.erroneous_Parameter}</td>
              </tr>`;
    tableBody.innerHTML += row;
});