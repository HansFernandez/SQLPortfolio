#### Found an oppurtunity to create a database trigger. I've adjusted the facts of the case to document how it was resolved

Triggers are attached to tables/views to execute a code when an action takes place.

##### Users should be able to select (and apply) the ‘Customer’ filter on the ‘inventory’ page with respective results returning. 

#### Scenario
- Log in and navigate to 'Agents' section.
- Select the ‘Customer’ filter button and apply customer to filter by

#### Expected result
The users should only show ‘Customer’ selected.

#### Actual result
No results appear at all

#### Cause

Site uses select statement with Customer string against SQL. The field/column for customer is blank and therefore never returns records. This was meant to be filled in but was never iterated. Other tables contain the value.

#### Proposed Solution: 

SQL Trigger: After an insert into inventory and the customer value is blank have the trigger query other_table and use the value xyz to search against customer_id where iscustomer_key is Y. Grab the agency_id and then update inventory.agency with the value gathered. If agency_id is blank then end trigger and take no action.


create table Customers (
id INTEGER PRIMARY KEY,
first_name TEXT,
last_name varchar(20),
age integer
)


create trigger MyTrigger
on Customers
after insert
as
begin
  Print 'Hello World'
end
