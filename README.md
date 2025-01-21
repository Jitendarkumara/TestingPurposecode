	  const DashErrorElementsRow = document.querySelectorAll('.DS');
	 DashErrorElementsRow.forEach(el => {

	   const value =parseInt(el.querySelector('td:nth-child(10)').getAttribute('data-value'));

   const value1 =el.querySelector('td:nth-child(11)').getAttribute('data-value');
console.log(value1)
	
	if (value === 1) {
		  el.style.backgroundColor='red';
					}
	else
		{
		 //  el.style.backgroundColor='green';
		}
	 });
