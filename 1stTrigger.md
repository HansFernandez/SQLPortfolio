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

SQL Trigger: After an insert into TableA and the customer_id value is blank have the trigger query TableB and use the ownerid to search against customer_id where iscustomer_key is Y. Grab the customer and then update TableA.customer_id with the value gathered. 



```
---- =============================================
---- Author:		Hans Fernandez
---- Create date: 07-11-2019
---- Description:	Website needs agency field filled in to display customer search results correctly. This trigger populates agency code when new record is inserted if blank
---- =============================================


CREATE TRIGGER tr_add_customerid ON Customers
AFTER INSERT
AS
BEGIN
	DECLARE @referenceid INT;
	DECLARE @OwnerId INT;
	DECLARE @AgencyCode VARCHAR(4);

	SELECT @OwnerId = owner_id, @AgencyCode = agency FROM inserted;

	IF(RTRIM(@AgencyCode) ='')
	BEGIN
		/****Get primary key of new record****/
		SELECT @referenceid = @@IDENTITY;

		/*** If the new agency code is blank, try to pull from TableB ***/
		SELECT top 1 @AgencyCode = agencyid 
		FROM TableB 
		WHERE TableB.rec_id = @OwnerId
		and TableB.iscustomer_key = 'Y'
		and TableB.agencyid != ' '

		/*** Update new record if Agency Code was retrieved ***/
		IF(RTRIM(@AgencyCode) !=''
		AND
			@AgencyCode is not null)
		BEGIN
			UPDATE Customers
			SET agency = @AgencyCode
			WHERE vehi_id = @referenceid

			--Print 'Agency Code Updated';
			--SELECT agency,* FROM Customers WHERE vehi_id = @referenceid;
		END
	END
END

GO
```
