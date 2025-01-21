	   const DashErrorElementsFirstRow = document.querySelector('.DS');
		  const value1 =DashErrorElementsFirstRow.querySelector('td:nth-child(10)').getAttribute('data-value');

const errorvalue=value1.split(',').map(item=>item.trim());
console.log(errorvalue);
	errorvalue.forEach((value,index)=>{
		if(value==='')
		{
			console.log("Null  value found");
		}
		else
		{
			if(value[index]!=='' && index===0)
			{
				console.log(value[index]);
				document.querySelector('.power').style.backgroundColor='red';
			}
			if(value[index]!=='' && index===2)
			{
				console.log(value[index]);
				document.querySelector('.freq').style.backgroundColor='red';
			}
			if(value[index]!=='' && index===3)
			{
				console.log(value[index]);
				document.querySelector('.speed').style.backgroundColor='red';
			}
			if(value[index]!=='' && index===1)
			{
				console.log(value[index]);
				document.querySelector('.voltage').style.backgroundColor='red';
			}
		}
	});
