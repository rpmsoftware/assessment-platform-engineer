# Platform Engineer Assessment

### Objective 

Evaluate the candidate's proficiency in AWS CloudFormation, PowerShell and MSSQL through a series of tasks that simulate real-world scenarios.

## Task 1: AWS CloudFormation

### Objective

Create a CloudFormation template to deploy a secure web application infrastructure on Windows.

### Instructions

1. Create a CloudFormation template (WebServer.yaml) that includes the following resources:
    - EC2 instances running Windows Server.
    - An S3 bucket for storing application logs.
    - A security group on the EC2 instances that allows HTTPS (port 443) and RDP (port 3389) access.
    - An Application Load Balancer (ALB) to distribute traffic to the EC2 instance.
    - An AWS WAF (Web Application Firewall) to protect the ALB.
    - An ACM (AWS Certificate Manager) certificate for HTTPS.
2. The EC2 instance should have a user data script that installs and starts IIS (Internet Information Services) to serve a simple web page.
3. An EC2 instance that fails a status check should automatically be replaced.
4. The S3 bucket should have a policy that allows the EC2 instance to write logs to it.
5. Outside of the mandatory tasks, please list any concerns and recommendations you have for getting this solution ready for production.

### Deliverables

- WebServer.yaml file with the CloudFormation template.
- A brief README explaining how to deploy the template and verify the setup.

## Task 2: PowerShell

### Objective

Write a PowerShell script to manage EC2 instances created in [Task 1](#task-1-aws-cloudformation).

### Instructions

1.	Write a PowerShell script (Manage-EC2Instances.ps1) that performs the following actions:
    - Lists all EC2 instances in the environment created by the CloudFormation template.
    - Increase or decrease the number of running EC2 instances.

### Deliverables

- Manage-EC2Instances.ps1 script.
- A brief README explaining how to use the script.

## Task 3: MSSQL

### Objective
Write a T-SQL stored procedure that updates a unique column and handles errors.

### Instructions

Write a stored procedure that accepts a JSON array of objects and updates the DisplayOrder column with the data in the array. 

- The array should be formatted as follows: 
    ```
    [ 
        { 
            "VehiclePartID": 1, 
            "DisplayOrder": 2 
        }, 
        { 
            "VehiclePartID": 2, 
            "DisplayOrder": 1 
        } 
    ]
    ```
- The stored procedure must handle errors using return codes instead of exceptions.
- The stored procedure must handle cases where the JSON array contains only a few of the VehiclePartID/DisplayOrder combinations. 
- Use the setup data in [appendix A](#appendix-a).

### Deliverables

- usp_VehiclePart_UpdateDisplayOrder.sql script.

## Task 4: MSSQL

### Objective

Optimize a stored procedure.

### Instructions

Update usp_VehiclePart_ReadSearch with any optimisations you see fit. 
- usp_VehiclePart_ReadSearch uses data from [appendix A](#appendix-a).
- As part of the optimizations, indexes and constraints can be added to the source tables.
- Assume that the data provided is just a small sample of a much larger data set.

**usp_VehiclePart_ReadSearch**
```
/*
	Search for parts for a certain vehicle

	@param vehicleName Limit to this vehicle
	@param vehiclePartName Limit to this part. Null for all
	@param sku Limit to this SKU. Null for all
	@param isStockItem Limit to stock values. Null for all

	return 0
*/
CREATE PROCEDURE usp_VehiclePart_ReadSearch
	@vehicleName nvarchar(20),
	@vehiclePartName nvarchar(20) = NULL,
	@sku nvarchar(10) = NULL,
	@isStockItem bit = NULL
AS
	SELECT 
		v.VehicleName,
		vp.VehiclePartName,
		vp.Sku, 
		vp.IsStockItem
	FROM 
	Vehicle v 
	JOIN VehiclePart vp 
		ON v.VehicleID = vp.VehicleID
	WHERE 
	v.VehicleName = @vehicleName AND
	(vp.VehiclePartName LIKE '%' + @vehiclePartName + '%' OR @vehiclePartName IS NULL) AND
	(vp.Sku = @sku OR @sku IS NULL) AND
	(vp.IsStockItem = @isStockItem OR @isStockItem IS NULL)
```

### Deliverables

- The new procedure saved as usp_VehiclePart_ReadSearch.sql
- Document any changes with justifications.

## Appendix A

```
CREATE TABLE Vehicle (
	VehicleID int IDENTITY (1,1), 
	VehicleName nvarchar(20));

CREATE TABLE VehiclePart (
	VehiclePartID int IDENTITY (1,1), 
	VehicleID int, 
	VehiclePartName nvarchar(20),
	Sku nvarchar(10),
	IsStockItem bit DEFAULT 1 , 
	DisplayOrder int NOT NULL, 
    	UNIQUE (VehicleID, DisplayOrder)); 

SET IDENTITY_INSERT Vehicle ON;

INSERT INTO Vehicle (VehicleID, VehicleName)
SELECT * FROM 
(VALUES
	(1, 'Car'),
	(2, 'Truck'),
	(3, 'Motorbike'),
	(4, 'Quad')) AS r (VehicleID, VehicleName);

SET IDENTITY_INSERT Vehicle OFF;

INSERT INTO VehiclePart (VehicleID, VehiclePartName, Sku, IsStockItem, DisplayOrder)
SELECT * FROM 
(VALUES
	(1, 'Tire','WG10223',1, 1),
	(1, 'Wheel','QW6643',0, 2),
	(1, 'Nut','DL44304',1, 3),
	(2, 'Rack','CR0001',1, 1),
	(2, 'Tie down','VF21216',1, 2),
	(3, 'Bags','JH99401',0, 1),
	(3, 'Stand','PT20112',1, 2)
	) AS r (VehicleID, VehiclePartName, Sku, IsStockItem, DisplayOrder);
```


## Delivery

1. Create a new repository with your solution.
2. Once your solution is checked in, send it to your hiring contact.
3. Please deliver the solution within 7 days of receiving this assessment.
4. Git repository structure:
    ```
    /assessment-platform-engineer
    ├── README.md
    ├── task1
    │   ├── README.md
    │   ├── WebServer.yaml
    ├── task2
    │   ├── README.md
    │   ├── Manage-EC2Instances.ps1
    ├── task3
    │   ├── usp_VehiclePart_UpdateDisplayOrder.sql
    └── task4
        ├── README.md
        ├── usp_VehiclePart_ReadSearch.sql
        └── Any other changes 
    ```

