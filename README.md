const array = ["voltage", "power", "speed", "temperature"]; // Example array

array.forEach((value, index) => {
    switch (index) {
        case 0:
            console.log(`Index ${index}: Task for ${value}`);
            // Perform task for the first value
            break;
        case 1:
            console.log(`Index ${index}: Task for ${value}`);
            // Perform task for the second value
            break;
        case 2:
            console.log(`Index ${index}: Task for ${value}`);
            // Perform task for the third value
            break;
        case 3:
            console.log(`Index ${index}: Task for ${value}`);
            // Perform task for the fourth value
            break;
        default:
            console.log(`Index ${index}: No task for ${value}`);
    }
});