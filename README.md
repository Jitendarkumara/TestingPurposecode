  const DashErrorElementsRow = document.querySelectorAll('.DS');
 DashErrorElementsRow.forEach(el => {

const value =$(el).find('td:eq(9)').getAttribute('data-value');
		
if (value === 1) {
	  el.style.backgroundColor='red';
				}
else
	{
	   el.style.backgroundColor='green';
	}
 });
