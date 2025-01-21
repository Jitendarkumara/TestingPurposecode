const DashErrorElementsFirstRow = document.querySelector('.DS'); 
const value1 = DashErrorElementsFirstRow.querySelector('td:nth-child(10)').getAttribute('data-value');

// Split the string by commas and trim any whitespace
const errorvalue = value1.split(',').map(item => item.trim());
console.log("Array values:", errorvalue);

// Iterate over the array to check each value
errorvalue.forEach((value, index) => {
    if (value === '') {
        console.log(`Index ${index}: Null or empty value found`);
        // You can add additional handling for empty or null values here
    } else {
        // Perform specific tasks based on the index
        switch (index) {
            case 0:
                console.log(`Index ${index}: Task for ${value}`);
                document.querySelector('.power').style.backgroundColor = 'red';  // Change background color for power
                break;
            case 1:
                console.log(`Index ${index}: Task for ${value}`);
                document.querySelector('.voltage').style.backgroundColor = 'red';  // Change background color for voltage
                break;
            case 2:
                console.log(`Index ${index}: Task for ${value}`);
                document.querySelector('.freq').style.backgroundColor = 'red';  // Change background color for frequency
                break;
            case 3:
                console.log(`Index ${index}: Task for ${value}`);
                document.querySelector('.speed').style.backgroundColor = 'red';  // Change background color for speed
                break;
            default:
                console.log(`Index ${index}: No specific task for ${value}`);
        }
    }
});