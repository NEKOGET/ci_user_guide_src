##################################
Database Quick Start: Example Code
##################################

ここでは、データベースクラスの使い方の
例を示します。詳細は、個別に各機能を
説明したページを見て下さい。

データベースクラスの初期化
========================

以下のコードは、 :doc:`configuration <configuration>` の
設定に従ってデータベースクラスを読み込んで初期化します。

	$this->load->database();

読込み後、データベースクラスはこれ以降の例に書いている様に使えます。

note: もし、全ページでデータベースへのアクセスが必用なら、自動でデータベースに接続する様にもできます。
詳細は :doc:`connecting <connecting>` を見て下さい。

Standard Query With Multiple Results (Object Version)
=====================================================

::

	$query = $this->db->query('SELECT name, title, email FROM my_table');
	
	foreach ($query->result() as $row)
	{
		echo $row->title;
		echo $row->name;
		echo $row->email;
	}
	
	echo 'Total Results: ' . $query->num_rows();

The above result() function returns an array of **objects**. Example:
$row->title

Standard Query With Multiple Results (Array Version)
====================================================

::

	$query = $this->db->query('SELECT name, title, email FROM my_table');
	
	foreach ($query->result_array() as $row)
	{
		echo $row['title'];
		echo $row['name'];
		echo $row['email'];
	}

The above result_array() function returns an array of standard array
indexes. Example: $row['title']

Standard Query With Single Result
=================================

::

	$query = $this->db->query('SELECT name FROM my_table LIMIT 1'); 
	$row = $query->row();
	echo $row->name;

The above row() function returns an **object**. Example: $row->name

Standard Query With Single Result (Array version)
=================================================

::

	$query = $this->db->query('SELECT name FROM my_table LIMIT 1');
	$row = $query->row_array();
	echo $row['name'];

The above row_array() function returns an **array**. Example:
$row['name']

Standard Insert
===============

::

	$sql = "INSERT INTO mytable (title, name) VALUES (".$this->db->escape($title).", ".$this->db->escape($name).")";
	$this->db->query($sql);
	echo $this->db->affected_rows();

Query Builder Query
===================

The :doc:`Query Builder Pattern <query_builder>` gives you a simplified
means of retrieving data::

	$query = $this->db->get('table_name');
	
	foreach ($query->result() as $row)
	{
		echo $row->title;
	}

The above get() function retrieves all the results from the supplied
table. The :doc:`Query Builder <query_builder>` class contains a full
compliment of functions for working with data.

Query Builder Insert
====================

::

	$data = array(
		'title' => $title,
		'name' => $name,
		'date' => $date
	);
	
	$this->db->insert('mytable', $data);  // Produces: INSERT INTO mytable (title, name, date) VALUES ('{$title}', '{$name}', '{$date}')

