---
layout: post
title: Algorithm for One-to-Many Child Table Updates
author: Daniel
date: 2008-03-28 20:13:24
categories:
- Databases
- [ Programming, PHP ]
- [ Programming, SQL ]
tags:
- algorithm
- array
- nsx
- one-to-many
- php
- sql
summary: An easy way to sync changes in a one-to-many relationship
---

While working on the [Not So Extreme Makeover: Community Edition][nsx] site, I came up with an algorithm that simplifies anything else I've ever written to deal with this condition.  I'll set the scenario, explain the algorithm, share how I implemented it in PHP, and provide a modification if the scenario is a bit more complicated.

**Scenario** - You have two parent tables, and a child table with a many-to-one relationship with both parent tables, used to map entries in the two parent tables to each other.  For this example, we'll use these three tables...

{% codeblock lang:sql %}
create table volunteer (
    vol_id  integer  not null,
    vol_last_name  varchar(50)  not null,
    ...etc...
  primary key (vol_id)
);

create table r_volunteer_area (
    rva_id  integer  not null,
    rva_description  varchar(255)  not null,
  primary key (rva_id)
);

create table volunteer_area (
    va_volunteer_id  integer  not null,
    va_area_id  integer  not null,
  primary key (va_volunteer_id, va_area_id),
  foreign key (va_volunteer_id) references volunteer (vol_id),
  foreign key (va_area_id) references r_volunteer_area (rva_id)
);
{% endcodeblock %}

**Algorithm** - The three-step algorithm is as follows...

1. Create a comma-delimited string of IDs for the child table.
2. Delete the IDs from the child table that are not in the list.
3. Insert the IDs into the child table that are not there already.

**Implementation** - In PHP, if you have an array, it's easy to come up with comma-delimited list.  To get an array of values back in a post, define your fields with "[]" after the name...

{% codeblock lang:php %}
<input type="checkbox" name="area[]" id="chkArea1" value="1" />
<label for="chkArea1">Do Something</label><br />
<input type="checkbox" name="area[]" id="chkArea7" value="7" />
<label for="chkArea7">Do Something Else</label>
{% endcodeblock %}

Here's the PHP code, using [PHP Data Objects (PDO)][pdo] as the database interface, behind a helper class that creates the statement, appends the parameters, and executes it. _(The "quoting" escapes the statement to avoid potential SQL injection attacks - putting it in its own class would make the implementation here much cleaner.)_

{% codeblock lang:php %}
/**
 * STEP 1
 *    Create a comma-delimited list of IDs.
 */

// Quote will return the string as '2,3,4' - since we're using this
// as an IN clause of integers, we'll strip the quotes off.
$sAreas = $pdo->quote(join(",", $_POST["area"]));
$sAreas = substr($sAreas, 1, strlen($sAreas) - 1);

// Quote the volunteer ID.
$iVol = $pdo->quote($_POST["vol"], PDO::PARAM_INT);

/**
 * STEP 2
 *    Delete the IDs that are no longer in the list.
 */
$dbService->executeCommand(
    "DELETE FROM volunteer_area
    WHERE   va_volunteer_id = ?
        AND va_area_id NOT IN ($sAreas)",
    array($iVol);

/**
 * STEP 3
 *    Insert the IDs that are not yet in the list.
 */
$dbService->executeCommand(
    "INSERT INTO volunteer_area
        SELECT $iVol, rva_id
        FROM r_volunteer_area
        WHERE   rva_id IN ($sAreas)
            AND rva_id NOT IN
            (SELECT va_area_id
            FROM volunteer_area
            WHERE va_volunteer_id = ?)",
    array($iVol));
{% endcodeblock %}

**Modification** - Suppose that now you accepted comments along with each of the checkboxes, so a simple two-integer insert/delete is no longer sufficient.  You would still only need to break step 3 into two steps.

1. Get a list of IDs to update.
2. For each ID in the posted list
   1. If the ID exists in the update list, update it.
   2. Otherwise, insert it.

The implementation would then be able to use this list to make the decision without hitting the database every time.

{% codeblock lang:php %}
// Assume this returns an associative array of IDs.
$aUpdates = $dbService->performSelect(
    "SELECT va_area_id
    FROM volunteer_area
    WHERE   va_volunteer_id = ?
        AND va_area_id IN ($sAreas)",
    array($iVol));

foreach($_POST["area"] as $iArea) {
    if (in_array($iArea, $aUpdates)) {
        // Update the table
        ...etc...
    }
    else {
        // Insert into the table
        ...etc...
    }
}
{% endcodeblock %}

I think you'll agree that this is much better than spinning through a loop, doing a count on each ID to see if it exists, then either doing an update or an insert based on the count.  And, while the implementation here is PHP, it could easily be implemented in any language that supports arrays and database access.


[nsx]: //bitbadger.solutions/solutions/nsx "Not So Extreme Makeover: Community Edition &bull; Bit Badger Solutions"
[pdo]: //us.php.net/pdo "PHP Data Objects (PDO)"
