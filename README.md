const array = ["voltage", "power", "speed", null]; // Example array with a null value

array.forEach((value, index) => {
    const element = document.querySelectorAll('.test')[index]; // Select the corresponding element by index
    
    if (value === null) {
        console.log(`Index ${index}: The value is null, skipping task.`);
        // If the value is null, maybe set a default color for the element
        if (element) {
            element.style.backgroundColor = 'gray'; // Change color to gray when the value is null
        }
    } else {
        // Check for each index and change the background color for each case
        switch (index) {
            case 0:
                console.log(`Index ${index}: Task for ${value}`);
                // Change color to red for "voltage"
                if (element) {
                    element.style.backgroundColor = 'red';
                }
                break;
            case 1:
                console.log(`Index ${index}: Task for ${value}`);
                // Change color to green for "power"
                if (element) {
                    element.style.backgroundColor = 'green';
                }
                break;
            case 2:
                console.log(`Index ${index}: Task for ${value}`);
                // Change color to blue for "speed"
                if (element) {
                    element.style.backgroundColor = 'blue';
                }
                break;
            case 3:
                console.log(`Index ${index}: Task for ${value}`);
                // For "null" value or other index, set the color to gray or skip task
                if (element) {
                    element.style.backgroundColor = 'gray';
                }
                break;
            default:
                console.log(`Index ${index}: No specific task for ${value}`);
                if (element) {
                    element.style.backgroundColor = 'purple'; // Default color
                }
        }
    }
});