---
layout: post
title: Transferring CLOBs Across Linked Oracle Databases
author: Daniel
date: 2007-06-15 14:04:02
categories:
- [ Databases, Oracle ]
- [ Programming, SQL, PL/SQL ]
tags:
- algorithm
- clob
- data
- linked database
- oracle
summary: An algorithm to move CLOBs from one database to another
---

Linking databases in Oracle make it easy to share data, and can be useful for replication. However, there is a limitation in Oracle that prevents Character Large Objects (CLOBs) from coming across these links. The following technique uses stored procedures and a temporary table to pull CLOBs across a database link.

First, you'll need the temporary table, which will hold a sequence number, the primary key for the table where you'll want to reconstruct the CLOB, and some text. This table can reside in the source or destination database, but must be linked from the other one. For our purposes, it looks like this...

{% codeblock lang:sql %}
create table clob_xfer_area
(
  cxa_pk      number(12),
  cxa_number  number(12),
  cxa_text    varchar2(4000 byte)
);
alter table clob_xfer_area add
(
  constraint pk_cxa_id
    primary key (cxa_pk, cxa_number)
);
{% endcodeblock %}

Second, you'll need the procedure in the source database that breaks the CLOB apart and populates the temporary table.

{% codeblock lang:sql %}
set serveroutput on size 1000000
set lines 1000
set pages 0
set tab off
set feedback on
create or replace
procedure break_clobs_apart
is
  v_line_number   number(3);
  v_text_piece    varchar2(4000);
  v_total_length  number(12);
  cursor clob_cur is
    select twc_pk, twc_clob_field
    from   table_with_clob;
begin /* { */
  for clob_rec in clob_cur loop /* { */
    v_total_length := 1;
    v_line_number  := 0;
    while (v_total_length <=
           DBMS_LOB.GETLENGTH(clob_rec.twc_clob_field)) loop /* { */
      v_line_number := v_line_number + 1;
      v_text_piece := DBMS_LOB.SUBSTR(clob_rec.twc_clob_field,
        3999, v_total_length);
      v_total_length := v_total_length + 3999;
      insert into clob_xfer_area (
        cxa_pk,
        cxa_number,
        cxa_text
      )
        values (
          clob_rec.twc_pk, -- cxa_pk
          v_line_number,   -- cxa_number
          v_text_piece     -- cxa_text
        );
    end loop; /* } of while */
  end loop; /* } of clob_cur */
end; /* } of procedure break_clobs_apart */
{% endcodeblock %}

Third, you'll need a procedure in the destination database that puts the CLOB back together, and deletes the data from the temporary table.

{% codeblock lang:sql %}
set serveroutput on size 1000000
set lines 1000
set pages 0
set feedback on
set tab off
create or replace
procedure put_clobs_together
is
  v_new_clob   clob;
  cursor pk_cur is
    select distinct cxa_pk
    from   clob_xfer_area;
  cursor piece_cur(p_cxa_pk number) is
    select cxa_text
    from   clob_xfer_area
    where  cxa_pk = p_cxa_pk
    order by cxa_number;
begin /* { */
  for pk_rec in pk_cur loop /* { */
    DBMS_LOB.CREATETEMPORARY(v_new_clob, TRUE);
    DBMS_LOB.OPEN(v_new_clob, DBMS_LOB.LOB_READWRITE);
    for piece_rec in piece_cur(pk_rec.cxa_pk) loop /* { */
      DBMS_LOB.WRITEAPPEND(v_new_clob, LENGTH(piece_rec.cxa_text),
        piece_rec.cxa_text);
    end loop;  /* } of piece_cur */
    DBMS_LOB.CLOSE(v_new_clob);
    update dest_table_with_clob
      set  migrated_clob = v_new_clob
      where dtwc_pk = pk_rec.cxa_pk;
  end loop; /* } of pk_cur */
  delete from clob_xfer_area;
end; /* } of procedure put_clobs_together */
{% endcodeblock %}

Finally, you'll need a procedure that controls the whole thing. We'll assume that this procedure is loaded in the destination database, and the source database is linked with the name "source".

{% codeblock lang:sql %}
set lines 1000
set pages 0
set feedback on
set tab off
create or replace
procedure xfer_clobs
is
begin /* { */
  break_clobs_apart@source;
  put_clobs_together;
end; /* } */
{% endcodeblock %}

(This does not include a commit - the changes will not be persistent unless they are committed.)

Of course, these processes could (and, to be useful, likely would) be integrated into other procedures and scripts. But, this framework will successfully transfer CLOBs across linked databases in Oracle.
