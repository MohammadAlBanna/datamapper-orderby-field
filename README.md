# Order By Field in Datamapper ORM with Codeigniter Framework

Datamapper orm using order_by of codeigniter as an alias $this->db->order_by() , and  database class is not extendable, so you have to edit the core if you have other implementation.

# The Previous problem is:
The order_by function is used to sort any column ASC or DESC and has no implementation to sort by field using MySQL FIELD function.  

# How to use - Example
```
$sortIdsAs = array(50,10,60,12);
$articles = new Article();
$articles->where_in("id",$sortIdsAs); //it will return the objects ordered by ASC
$articles->order_by("id",$sortIdsAs,true); // it will return the objects ordered by sortIdsAs
$articles->get();
```

# Changes:
### Step One:
Open file DB_active_rec.php located in application/core/database/DB_active_rec.php
### Step Two:
Edit function order_by:
```
/** This function has been edited by: Mohammad AlBanna
	 * Web Address: www.mbanna.info
	 * Sets the ORDER BY value
	 *
	 * @param	string
	 * @param	string	direction: asc or desc
	 * @return	object
	 */
	public function order_by($orderby, $direction = '',$byField = false)
	{	if(is_array($direction) && $byField){
			$direction = "FIELD(".$orderby.",'".implode("' , '",$direction)."')";
		}
		elseif (strtolower($direction) == 'random')
		{
			$orderby = ''; // Random results want or don't need a field name
			$direction = $this->_random_keyword;
		}
		elseif (trim($direction) != '')
		{
			$direction = (in_array(strtoupper(trim($direction)), array('ASC', 'DESC'), TRUE)) ? ' '.$direction : ' ASC';
		}

		if (strpos($orderby, ',') !== FALSE)
		{
			$temp = array();
			foreach (explode(',', $orderby) as $part)
			{
				$part = trim($part);
				if ( ! in_array($part, $this->ar_aliased_tables))
				{
					$part = $this->_protect_identifiers(trim($part));
				}

				$temp[] = $part;
			}

			$orderby = implode(', ', $temp);
		}
		else if ($direction != $this->_random_keyword)
		{
			$orderby = $this->_protect_identifiers($orderby);
		}

		if($byField){
			$orderby_statement = $direction;

		}
		else{
			$orderby_statement = $orderby.$direction;
		}

		// var_dump($orderby_statement);
		// exit;
		$this->ar_orderby[] = $orderby_statement;
		if ($this->ar_caching === TRUE)
		{
			$this->ar_cache_orderby[] = $orderby_statement;
			$this->ar_cache_exists[] = 'orderby';
		}

		return $this;
	}
```
### Step Three:
Change function order_by that located inside datamapper.php library to:
```
/** This function has been edited by: Mohammad AlBanna
	 * Web Address: www.mbanna.info
	 * Order By
	 *
	 * Sets the ORDER BY portion of the query.
	 *
	 * @param	string $orderby Field to order by
	 * @param	string $direction One of 'ASC' or 'DESC'  Defaults to 'ASC'
	 * @return	DataMapper Returns self for method chaining.
	 */
	public function order_by($orderby, $direction = '',$byField = false)
	{
		$this->db->order_by($this->add_table_name($orderby), $direction,$byField);

		// For method chaining
		return $this;
	}
```
# What if you want to add CASE to order_by:
[Question in stackoverflow.com](https://stackoverflow.com/questions/11821503/does-codeigniter-datamapper-orm-support-a-mysql-case-statement-in-order-by-claus)

