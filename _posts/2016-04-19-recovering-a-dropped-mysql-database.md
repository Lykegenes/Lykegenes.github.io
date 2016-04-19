---
title:  "Recovering a dropped MySQL database"
date:   2016-04-19 15:00:00
description: How to recover a dropped MySQL database or table using InnoDB
---


This tutorial is largely based on [this StackOverflow answer](http://dba.stackexchange.com/a/72760/62007).

When a table or database is dropped, some data can remain, if the MySQL setting `innodb_file_per_table` was `OFF`. This data is in the `ibdata1` file that is usually located in the MySQL data directory, alongside the other databases.

So, how can we recover the data?

First, clone the tool named `Undrop for InnoDB` :
{% highlight bash %}
git clone https://github.com/chhabhaiya/undrop-for-innodb.git

// or my fork, as a mirror
// git clone https://github.com/Lykegenes/undrop-for-innodb.git
{% endhighlight %}

Now, build it :
```
// sudo apt-get install make gcc flex bison

cd undrop-for-innodb
make
```

Next step is to extract the InnoDB pages from the `ibdata1` file :
{% highlight bash %}
./stream_parser -f ../ibdata1
{% endhighlight %}
It will create the `pages-ibdata1` and the `dictionnary` directories, with the extracted data inside.

We have to analyze the data table by table, modify the `grep` to suit you needs...
{% highlight bash %}
./c_parser -4Df pages-ibdata1/FIL_PAGE_INDEX/0000000000000001.page -t dictionary/SYS_TABLES.sql | grep your-database/your-table
{% endhighlight %}
The first column after the table name will contain the `table ID`, 1248 in my case. Remember it.

Using that `table ID, we search for the `PRIMARY Index ID` (filter using grep) :
{% highlight bash %}
./c_parser -4Df pages-ibdata1/FIL_PAGE_INDEX/0000000000000003.page -t dictionary /SYS_INDEXES.sql | grep 1248
{% endhighlight %}
You might see multiple indexes. We need the PRIMARY one. (4904 in my case). Remember it.
It is the column BEFORE the index name ("PRIMARY").

Now, this is the important part, and you need to be careful :
When the database or table was dropped, the schema has been deleted. You need this schema to properly extract your data. All you need is the SQL CREATE command, like so :
{% highlight sql %}
CREATE TABLE `some-table` (
 `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
 `name` varchar(255) COLLATE utf8_unicode_ci NOT NULL,
 `created_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00',
 `updated_at` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00',
 PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=26 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
{% endhighlight %}
You could easily get it from a fresh install of the software, a development database, an older backup, or by trial and error. Most important is the name and type of the column (I think).

If you're doing this for multiple tables, I suggest creating two folders. `create` which will contain you SQL create scripts and `dumps` where your data will be dumped.

We now have everything we need to finally extract our data, using our Primary Index ID from before (4904) :
{% highlight bash %}
./c_parser -6f pages-ibdata1/FIL_PAGE_INDEX/0000000000004904.page -t create/your-table.sql > dumps/your-data.tsv 2> load_cmd.sql
{% endhighlight %}
Your data should now be in the `your-data.tsv` file, and it is worth it to have a look in `load_cmd.sql` for any errors.


Make sure to double check your data, there might be duplicates or missing data. Some columns may also not be casted properly in the right type, such as dates and currencies.

Good luck!