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
# What if you want to add CASE to order_by:
[Question in stackoverflow.com](https://stackoverflow.com/questions/11821503/does-codeigniter-datamapper-orm-support-a-mysql-case-statement-in-order-by-claus)

