  const DashErrorElementsRow = document.querySelectorAll('.DS');
 DashErrorElementsRow.forEach(el => {

const value = $(this).find('td:eq(9)').text().trim();
		
if (value === 1) {
	  (this).style.color='red';
				}
else
	{
	   (this).style.color='green';
	}
 });
