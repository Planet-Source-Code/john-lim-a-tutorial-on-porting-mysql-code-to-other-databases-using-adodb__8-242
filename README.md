﻿<div align="center">

## A Tutorial on Porting MySQL code to other Databases using ADODB


</div>

### Description

In PHP every database is accessed slightly differently. To connect to MySQL, you would use mysql_connect(); when you decide to upgrade to Oracle or Microsoft SQL Server, you would use ocilogon() or mssql_connect() respectively. What is worse is that the parameters you use for the different connect functions are different also..

That is why a database wrapper library such as ADODB comes in handy when you need to ensure portability. It provides you with a common API to communicate with any supported database so you don't have to call things off.
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[John Lim](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/john-lim.md)
**Level**          |Intermediate
**User Rating**    |4.6 (23 globes from 5 users)
**Compatibility**  |PHP 4\.0
**Category**       |[Databases](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/databases__8-5.md)
**World**          |[PHP](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/php.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/john-lim-a-tutorial-on-porting-mysql-code-to-other-databases-using-adodb__8-242/archive/master.zip)





### Source Code

<pre> You say eether and I say eyether,
 You say neether and I say nyther;
 Eether, eyether, neether, nyther -
 Let's call the whole thing off !
<br>
 You like potato and I like po-tah-to,
 You like tomato and I like to-mah-to;
 Potato, po-tah-to, tomato, to-mah-to -
 Let's call the whole thing off !
</pre>
<p>I love this song, especially the version with Louis Armstrong and Ella singing
 duet. It is all about how hard it is for two people in love to be compatible
 with each other. It's about compromise and finding a common ground, and that's
 what this article is all about.
<p>PHP is all about creating dynamic web-sites with the least fuss and the most
 fun. To create these websites we need to use databases to retrieve login information,
 to splash dynamic news onto the web page and store forum postings. So let's
 say we were using the popular MySQL database for this. Your company has done
 such a fantastic job that the Web site is more popular than your wildest dreams.
 You find that MySQL cannot scale to handle the workload; time to switch databases.
<p> Unfortunately in PHP every database is accessed slightly differently. To connect
 to MySQL, you would use <i>mysql_connect()</i>; when you decide to upgrade to
 Oracle or Microsoft SQL Server, you would use <i>ocilogon() </i>or <i>mssql_connect()</i>
 respectively. What is worse is that the parameters you use for the different
 connect functions are different also.. One database says po-tato, the other
 database says pota-to. Oh-oh.
<h3>Let's NOT call the whole thing off</h3>
<p>A database wrapper library such as ADODB comes in handy when you need to ensure portability. It provides
 you with a common API to communicate with any supported database so you don't have to call things off.
<p>ADODB stands for Active Data Objects DataBase (sorry computer guys are sometimes
 not very original). ADODB currently supports MySQL, PostgreSQL, Oracle, Interbase,
 Microsoft SQL Server, Access, FoxPro, Sybase, ODBC and ADO. You can download
 ADODB from <a href=http://php.weblogs.com/adodb>http://php.weblogs.com/adodb</a>.
<h3>MySQL Example</h3>
<p>The most common database used with PHP is MySQL, so I guess you should be familiar
 with the following code. It connects to a MySQL server at <i>localhost</i>,
 database <i>mydb</i>, and executes an SQL select statement. The results are
 printed, one line per row.
<pre><font color="#666600">$db = <b>mysql_connect</b>("localhost", "root", "password");
<b>mysql_select_db</b>("mydb",$db);</font>
<font color="#660000">$result = <b>mysql_query</b>("SELECT * FROM employees",$db)</font><code><font color="#663300">;
if ($result === false) die("failed");</font></code>
<font color="#006666"><b>while</b> ($fields =<b> mysql_fetch_row</b>($result)) {
 <b>for</b> ($i=0, $max=sizeof($fields); $i < $max; $i++) {
 <b>print</b> $fields[$i].' ';
 }
 <b>print</b> "<br>\\n";
}</font>
</pre>
<p>The above code has been color-coded by section. The first section is the connection
 phase. The second is the execution of the SQL, and the last section is displaying
 the fields. The <i>while</i> loop scans the rows of the result, while the <i>for</i>
 loop scans the fields in one row.</p>
<p>Here is the equivalent code in ADODB</p>
<pre><b><font color="#666600"> include("adodb.inc.php");
 ADOLoadCode</font></b><font color="#666600">('mysql');
 $db = <b>NewADOConnection</b>();
 $db-><b>Connect</b>("localhost", "root", "password", "mydb");</font>
 <font color="#663300">$result = $db-><b>Execute</b>("SELECT * FROM employees");
 </font><font color="#663300"></font><code><font color="#663300">if ($result === false) die("failed")</font></code><code><font color="#663300">;</font></code>
 <font color="#006666"><b>while</b> (!$result->EOF) {
 <b>for</b> ($i=0, $max=$result-><b>FieldCount</b>(); $i < $max; $i++)
 <b>print</b> $result->fields[$i].' ';
 $result-><b>MoveNext</b>();
 <b>print</b> "<br>\\n";
 }</font> </pre>
<p></p>
<p>Now porting to Oracle is as simple as changing the second line to <code>ADOLoadCode('oracle')</code>.
 Let's walk through the code...</p>
<h3>Connecting to the Database</h3>
<p></p>
<pre><b><font color="#666600">include("adodb.inc.php");
</font></b><b><font color="#666600">ADOLoadCode</font></b><font color="#666600">('mysql');
$db = <b>NewADOConnection</b>();
$db-><b>Connect</b>("localhost", "root", "password", "mydb");</font></pre>
<p>The connection code is a bit more sophisticated than MySQL's because our needs
 are more sophisticated. In ADODB, we use an object-oriented approach to managing
 the complexity of handling multiple databases. We have different classes to
 handle different databases. If you aren't familiar with object-oriented programing,
 don't worry -- the complexity is all hidden away in the<code> NewADOConnection()</code>
 function.</p>
<p>To conserve memory, we only load the PHP code specific to the database you
 are connecting to. We do this by calling <code>ADOLoadCode(databasedriver)</code>.
 Legal database drivers include <i>mysql, mssql, oracle, oci8, postgres, sybase,
 vfp, access, ibase </i>and many others.</p>
<p>Then we create a new instance of the connection class by calling <code>NewADOConnection()</code>.
 Finally we connect to the database using <code>$db->Connect(). </code></p>
<h3>Executing the SQL</h3>
<p><code><font color="#663300">$result = $db-><b>Execute</b>("SELECT *
 FROM employees");<br>
 if ($result === false) die("failed")</font></code><code><font color="#663300">;</font></code>
 <br>
</p>
<p>Sending the SQL statement to the server is straight forward. Execute() will
 return a recordset object on successful execution. You should check $result
 as we do above.
<p>An issue that confuses beginners is the fact that we have two types of objects
 in ADODB, the connection object and the recordset object. When do we use each?
<p>The connection object ($db) is responsible for connecting to the database,
 formatting your SQL and querying the database server. The recordset object ($result)
 is responsible for retrieving the results and formatting the reply as text or
 as an array.
<p>The only thing I need to add is that ADODB provides several helper functions
 for making INSERT and UPDATE statements easier, which we will cover in the Advanced
 section.
<h3>Retrieving the Data<br>
</h3>
<pre><font color="#006666"><b>while</b> (!$result->EOF) {
 <b>for</b> ($i=0, $max=$result-><b>FieldCount</b>(); $i < $max; $i++)
 <b>print</b> $result->fields[$i].' ';
 $result-><b>MoveNext</b>();
 <b>print</b> "<br>\\n";
}</font></pre>
<p>The paradigm for getting the data is that it's like reading a file. For every
 line, we check first whether we have reached the end-of-file (EOF). While not
 end-of-file, loop through each field in the row. Then move to the next line
 (MoveNext) and repeat.
<p>The <code>$result->fields[]</code> array is generated by the PHP database
 extension. Some database extensions do not index the array by field name (unlike
 MySQL). To guarantee portability, use <code>$result->Fields($fieldname)</code>.
 Note that this is a function, not an array.
<h3>Other Useful Functions</h3>
<p><code>$recordset->Move($pos)</code> scrolls to that particular row. ADODB supports forward
 scrolling for all databases. Some databases will not support backwards scrolling.
 This is normally not a problem as you can always cache records to simulate backwards
 scrolling.
<p><code>$recordset->RecordCount()</code> returns the number of records accessed by the
 SQL statement. Some databases will return -1 because it is not supported.
<p><code>$recordset->GetArray()</code> returns the result as an array.
<p><code>rs2html($recordset)</code> is a function that is generates a HTML table based on the
 $recordset passed to it. An example with the relevant lines in bold:
<pre> include('adodb.inc.php');
 <b>include('tohtml.inc.php');</b> /* includes the rs2html function */
 ADOLoadCode('mysql');
 $conn = &ADONewConnection();
 $conn->PConnect('localhost','userid','password','database');
 $rs = $conn->Execute('select * from table');
 <b> rs2html($rs)</b>; /* recordset to html table */ </pre>
<p>There are many other helper functions that are listed in the documentation available at <a href="http://php.weblogs.com/adodb_manual">http://php.weblogs.com/adodb_manual</a>.
<h2>Advanced Material</h2>
<h3>Inserts and Updates </h3>
<p>Let's say you want to insert the following data into a database.
<p><b>ID</b> = 3<br>
 <b>TheDate</b>=mktime(0,0,0,8,31,2001) /* 31st August 2001 */<br>
 <b>Note</b>= sugar why don't we call it off
<p>When you move to another database, your insert might no longer work.</p>
<p>The first problem is that each database has a different default date format.
 MySQL expects YYYY-MM-DD format, while other databases have different defaults.
 ADODB has a function called DBDate() that addresses this issue by converting
 converting the date to the correct format.</p>
<p>The next problem is that the <b>don't</b> in the Note needs to be quoted. In
 MySQL, we use <b>don\\'t</b> but in some other databases (Sybase, Access, Microsoft
 SQL Server) we use <b>don''t. </b>The qstr() function addresses this issue.</p>
<p>So how do we use the functions? Like this:</p>
<pre>$sql = "INSERT INTO table (id, thedate,note) values ("
 . $<b>ID</b> . ','
 . $db->DBDate($<b>TheDate</b>) .','
 . $db->qstr($<b>Note</b>).")";
$db->Execute($sql);</pre>
<p>ADODB also supports <code>$connection->Affected_Rows()</code> (returns the
 number of rows affected by last update or delete) and <code>$recordset->Insert_ID()</code>
 (returns last autoincrement number generated by an insert statement). Be forewarned
 that not all databases support the two functions.<br>
</p>
<h3>MetaTypes</h3>
<p>You can find out more information about each of the fields (I use the words
 fields and columns interchangebly) you are selecting by calling the recordset
 method <code>FetchField($fieldoffset)</code>. This will return an object with
 3 properties: name, type and max_length.
<pre>For example:</pre>
<pre>$recordset = $conn->Execute("select adate from table");<br>$f0 = $recordset->FetchField(0);
</pre>
<p>Then <code>$f0->name</code> will hold <i>'adata'</i>, <code>$f0->type</code>
 will be set to '<i>date'</i>. If the max_length is unknown, it will be set to
 -1.
<p>One problem with handling different databases is that each database often calls
 the same type by a different name. For example a <i>timestamp</i> type is called
 <i>datetime</i> in one database and <i>time</i> in another. So ADODB has a special
 <code>MetaType($type, $max_length)</code> function that standardises the types
 to the following:
<p>C: character type<br>
 B: blob or long character type (eg. more than 255 bytes wide).<br>
 D: date<br>
 T: timestamp<br>
 L: logical (boolean)<br>
 N: numeric (float, double, integer)
<p>In the above date example,
<p><code>$recordset = $conn->Execute("select adate from table");<br>
 $f0 = $recordset->FetchField(0);<br>
 $type = $recordset->MetaType($f0->type, $f0->max_length);<br>
 print $type; /* should print 'D'</code> */
<p>
<p><b>PHP4 Session Handler Support</b>
<p>ADODB also supports PHP4 session handlers. You can store your session variables
 in a database for true scalability using ADODB. For further information, visit
 <a href="http://php.weblogs.com/adodb-sessions">http://php.weblogs.com/adodb-sessions</a>
<h3>Commercial Use Encouraged</h3>
<p>If you plan to write commercial PHP applications that you want to resell, you should consider ADODB. It has been released using the lesser GPL, which means you can legally include it in commercial applications, while keeping your code proprietary. Commercial use of ADODB is strongly encouraged! We are using it internally for this reason.
<h2>Conclusion</h2>
<p>As a thank you for finishing this article, here are the complete lyrics for
 <i>let's call the whole thing off</i>.<br>
 <br>
<pre>
 Refrain
<br>
 You say eether and I say eyether,
 You say neether and I say nyther;
 Eether, eyether, neether, nyther -
 Let's call the whole thing off !
<br>
 You like potato and I like po-tah-to,
 You like tomato and I like to-mah-to;
 Potato, po-tah-to, tomato, to-mah-to -
 Let's call the whole thing off !
<br>
But oh, if we call the whole thing off, then we must part.
And oh, if we ever part, then that might break my heart.
<br>
 So, if you like pajamas and I like pa-jah-mas,
 I'll wear pajamas and give up pa-jah-mas.
 For we know we
 Need each other, so we
 Better call the calling off off.
 Let's call the whole thing off !
<br>
 Second Refrain
<br>
 You say laughter and I say lawfter,
 You say after and I say awfter;
 Laughter, lawfter, after, awfter -
 Let's call the whole thing off !
<br>
 You like vanilla and I like vanella,
 You, sa's'parilla and I sa's'parella;
 Vanilla, vanella, choc'late, strawb'ry -
 Let's call the whole thing off !
<br>
But oh, if we call the whole thing off, then we must part.
And oh, if we ever part, then that might break my heart.
<br>
 So, if you go for oysters and I go for ersters,
 I'll order oysters and cancel the ersters.
 For we know we
 Need each other, so we
 Better call the calling off off.
 Let's call the whole thing off !
 </pre>
<p><font size=2>Song and lyrics by George and Ira Gershwin, introduced by Fred Astaire and Ginger Rogers
in the film "Shall We Dance?" </font>

