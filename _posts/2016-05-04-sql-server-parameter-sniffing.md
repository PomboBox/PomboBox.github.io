---
layout: post
title:  "SQL Server Parameter Sniffing"
date:   2016-05-04 16:48:00 -0300
categories: sql-server sql database
tags: sql-server sql database
language: br
summary: summary
---
 
 acbdefghijklmnop


# Referências

## Referências

### Referências

#### Referências

##### Referências

###### Referências


{% highlight sql %}
DBCC DROPCLEANBUFFERS
DBCC FREEPROCCACHE
{% endhighlight %}

Setar opção ARITHABORT

{% highlight sql %}
SET ARITHABORT ON; 
SELECT ...
{% endhighlight %}


<div markdown="block" class="table-wrapper">

||Applications using ADO .Net, ODBC or OLE DB | SSMS, Query Analyzer | SQLCMD, OSQL, BCP, SQL Server Agent | ISQL, DB-Library|
|---                     |:---:|:---:|:---:|:---:|
|ANSI_NULL_DFLT_ON       | ON  | ON  | ON  | OFF |
|ANSI_NULLS              | ON  | ON  | ON  | OFF |
|ANSI_PADDING            | ON  | ON  | ON  | OFF |
|ANSI_WARNINGS           | ON  | ON  | ON  | OFF |
|CONACT_NULLS_YIELD_NULL | ON  | ON  | ON  | OFF |
|QUOTED_IDENTIFIER       | ON  | ON  | OFF | OFF |
|ARITHABORT              | OFF | ON  | OFF | OFF |
{: .table .table-striped}

<center markdown="block">
Fonte: [sommarskog.se](http://www.sommarskog.se/query-plan-mysteries.html)
</center>

</div>

A Non-Solution
Before I go into the real solutions, let me first point out that adding SET ARITHABORT ON to your procedure is not a solution. It will seem to work when you try it. But that is only because you recreated the procedure which forced a new compilation and then the next invocation sniffed the current set of parameters. SET ARITHABORT ON is only a placebo, and not even a good one. The problem will most likely come back. It will not even help you avoid the confusion with different performance in the application and SSMS, because the overall cache entry will still haveARITHABORT OFF as its plan attribute.


________________________________________

Executar recompilando a SP:
{% highlight c# %}
cmd.CommandType = CommandType.Text;
cmd.Text = "EXECUTE memdb_get_updated_customers @tstamp WITH RECOMPILE";
{% endhighlight %}


________________________________________

One solution to address this is to force recompilation every time with the RECOMPILE query hint:

{% highlight sql %}
CREATE PROCEDURE Exemplo_sp @p1 int, @p2 datetime
AS

[...]

OPTION (RECOMPILE)

{% endhighlight %}

With this hint, SQL Server will compile the query every time. Rather than using the query hint, you can tell SQL Server to recompile the entire procedure on each invocation:

{% highlight sql %}

CREATE PROCEDURE Exemplo_sp @p1 int, @p2 datetime
WITH RECOMPILE AS
[...]
{% endhighlight %}

For the example at hand, it has no importance which you use, since the procedure consists of a single statement. But for a long procedure with many statements of which only one is problematic, WITH RECOMPILE is clearly an inferior choice, due to the increase in compilation overhead. An interesting effect of WITH RECOMPILE, though, is that the plan is never put into the cache at all, whereas this happens when you use OPTION (RECOMPILE).


___

Query para verificar os parâmetros "sniffeds" (em cache):



#### Referências

<http://www.sommarskog.se/query-plan-mysteries.html>

<http://stackoverflow.com/questions/2248112/query-times-out-when-executed-from-web-but-super-fast-when-executed-from-ssms>

<http://sqlperformance.com/2013/08/t-sql-queries/parameter-sniffing-embedding-and-the-recompile-options>

<http://stackoverflow.com/questions/6585417/stored-procedure-slow-when-called-from-web-fast-from-management-studio#13823047>

<http://stackoverflow.com/questions/834124/ado-net-calling-t-sql-stored-procedure-causes-a-sqltimeoutexception#839055>
