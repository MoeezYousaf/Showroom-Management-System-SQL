# Showroom-Management-System-SQL
This SQL script manages a vehicle showroom system. It includes table creation, data insertion, and queries for customers, vehicles, sales, and payments. It also defines views, procedures, functions, and triggers to handle operations like stock updates, sales transactions, and reporting, ensuring data integrity.

CREATE DATABASE ShowroomManagementDB;
USE ShowroomManagementDB;

------------------------------------------ Tables ----------------------------------------

-- Manager Table
CREATE TABLE Manager (
    Manager_id INT PRIMARY KEY,
    Manager_first_name VARCHAR(50) NOT NULL,
    Manager_last_name VARCHAR(50) NOT NULL,
    Manager_email VARCHAR(100) UNIQUE NOT NULL,
    Manager_phone VARCHAR(20) UNIQUE NOT NULL,
	Manager_Address VARCHAR(100) NOT NULL
);

-- Showroom Table
CREATE TABLE Showroom (
    Showroom_id INT PRIMARY KEY IDENTITY(1,1) ,
    Showroom_Name VARCHAR(50) NOT NULL,
    city VARCHAR(50) NOT NULL,
    Showroom_Address VARCHAR(50),
    Manager_id INT UNIQUE FOREIGN KEY REFERENCES Manager(Manager_id) ON DELETE CASCADE,
);

-- Customer Table
CREATE TABLE Customer (
    customer_ID INT PRIMARY KEY IDENTITY(1,1),
    customer_first_name VARCHAR(50) NOT NULL,
    customer_last_name VARCHAR(50) NOT NULL,
    customer_email VARCHAR(100) UNIQUE NOT NULL,
    customer_phone VARCHAR(20) UNIQUE NOT NULL,
	IsDeleted BIT DEFAULT 0
);

-- Address Table
CREATE TABLE Customer_Address (
    address_id INT PRIMARY KEY IDENTITY(1,1),
    customer_id INT UNIQUE FOREIGN KEY REFERENCES Customer(customer_ID) ON DELETE CASCADE,
    street VARCHAR(100) NOT NULL,
    city VARCHAR(50) NOT NULL,
    state VARCHAR(50) NOT NULL,
    country VARCHAR(50) DEFAULT 'Pakistan'
);

-- Payment Table
CREATE TABLE Payment (
    Payment_id INT PRIMARY KEY IDENTITY(1,1),
    Payment_Date DATETIME DEFAULT GETDATE() NOT NULL,
    Amount DECIMAL(10,2) CHECK (Amount > 0), 
    Method VARCHAR(20) NOT NULL,
    Payment_Status VARCHAR(20) DEFAULT 'Pending'
);

-- SalesPerson Table
CREATE TABLE SalesPerson (
    SalesPerson_id INT PRIMARY KEY,
    SalesPerson_first_name VARCHAR(50) NOT NULL,
    SalesPerson_last_name VARCHAR(50) NOT NULL,
    SalesPerson_email VARCHAR(100) UNIQUE NOT NULL,
    SalesPerson_phone VARCHAR(20) UNIQUE NOT NULL,
    Showroom_id INT FOREIGN KEY REFERENCES Showroom(Showroom_id) ON DELETE CASCADE
);

-- Vehicle_Type Table
CREATE TABLE Vehicle_Type (
    Vehicle_Type_id INT PRIMARY KEY,
    Vehicle_Type_Name VARCHAR(50) UNIQUE
);

-- Vehicle Table
CREATE TABLE Vehicle (
    vehicle_id INT PRIMARY KEY IDENTITY(1,1),
    Model VARCHAR(10) UNIQUE NOT NULL,
    Brand VARCHAR(50) NOT NULL,
    Model_year INT NOT NULL,
    Color VARCHAR(20) NOT NULL,
    Price DECIMAL(10,2) NOT NULL,
    Stock_Quantity INT NOT NULL,
    Vehicle_Type_id INT FOREIGN KEY REFERENCES Vehicle_Type(Vehicle_Type_id) ON DELETE CASCADE,
    Showroom_id INT FOREIGN KEY REFERENCES Showroom(Showroom_id) ON DELETE CASCADE
);

-- Sales Table
CREATE TABLE Sales (
    Sale_id INT PRIMARY KEY,
	customer_id INT FOREIGN KEY REFERENCES Customer(customer_ID) ON DELETE CASCADE,
    vehicle_id INT FOREIGN KEY REFERENCES Vehicle(vehicle_id) ON DELETE CASCADE,
    SalesPerson_id INT FOREIGN KEY REFERENCES SalesPerson(SalesPerson_id) ON DELETE NO ACTION,
    Sale_Date DATETIME DEFAULT GETDATE() NOT NULL,
    Payment_id INT UNIQUE FOREIGN KEY REFERENCES Payment(Payment_id) ON DELETE CASCADE,
    TotalAmount DECIMAL(10,2) CHECK (TotalAmount > 0)
);

-- DealerShip Table
CREATE TABLE DealerShip (
    DealerShip_id INT PRIMARY KEY,
	DealerShip_Name VARCHAR(50) NOT NULL,
	Phone VARCHAR(20) UNIQUE NOT NULL,
	Email VARCHAR(100) UNIQUE NOT NULL,
    DealerShip_Address VARCHAR(100)
);

-- VehicleSupply Table
CREATE TABLE VehicleSupply (
    VehicleSupply_id INT PRIMARY KEY,
    DealerShip_id INT FOREIGN KEY REFERENCES DealerShip(DealerShip_id) ON DELETE CASCADE,
    vehicle_id INT FOREIGN KEY REFERENCES Vehicle(vehicle_id) ON DELETE CASCADE,
    Supply_Date DATETIME DEFAULT GETDATE(),
    Quantity INT,
    Cost_Price DECIMAL(10,2) 
);

-- Service_centre Table
CREATE TABLE Service_centre (
    Service_centre_id INT PRIMARY KEY,
    Service_centre_Location VARCHAR(200),
    Service_centre_Contact VARCHAR(100)
);

-- Service Table
CREATE TABLE Taken_Service (
    Service_id INT PRIMARY KEY IDENTITY(1,1),
    vehicle_id INT FOREIGN KEY REFERENCES Vehicle(vehicle_id) ON DELETE CASCADE,
    customer_id INT FOREIGN KEY REFERENCES Customer(customer_ID) ON DELETE CASCADE,
    Service_centre_id INT FOREIGN KEY REFERENCES Service_centre(Service_centre_id) ON DELETE CASCADE,
    Service_Date DATETIME DEFAULT GETDATE(),
    Service_Description VARCHAR(200),
    Service_Cost DECIMAL(10,2) CHECK (Service_Cost >= 0)
);

------------------------------------------ Drop Table ----------------------------------------
/*
Drop Table Customer
Drop Table Customer_Address
Drop Table DealerShip
Drop Table Manager
Drop Table Payment
Drop Table Sales
Drop Table SalesPerson
Drop Table Service_centre
Drop Table Showroom
Drop Table Taken_Service
Drop Table Vehicle
Drop Table Vehicle_Type
Drop Table VehicleSupply
*/

------------------------------------------ Insertions ----------------------------------------
--Insert into Manager
INSERT INTO Manager (Manager_id, Manager_first_name, Manager_last_name, Manager_email, Manager_phone,Manager_Address)
VALUES (1, 'Hannan', 'Saleem', 'hannan991@gmail.com', '0349023444','Mughal Pura Road');

--Insert into Showroom
INSERT INTO Showroom (Showroom_Name, city, Showroom_Address, Manager_id)
VALUES ('XYZ Complex', 'Lahore', 'ABC', 1);

--Insert into Customer
INSERT INTO Customer (customer_first_name, customer_last_name, customer_email, customer_phone,IsDeleted)
VALUES ('Kashif', 'Majeed', 'kashif45@gmail.com', '03456789000',0);

--Insert into Customer_Address
INSERT INTO Customer_Address (customer_id, street, city, state)
VALUES (1, 'ABC', 'Lahore', 'Punjab');

--Insert into Payment
SET Identity_Insert Payment on --on
INSERT INTO Payment(Payment_id, Amount , Method ,Payment_Status)
VALUES (1, 10000, 'JAZZCASH','Pending');

INSERT INTO Payment(Payment_id, Amount , Method ,Payment_Status)
VALUES (2, 10000, 'JAZZCASH','Pending');

INSERT INTO Payment(Payment_id, Amount , Method ,Payment_Status)
VALUES (3, 10000, 'JAZZCASH','Pending');
SET Identity_Insert Payment off --off

--Insert into SalesPerson
INSERT INTO SalesPerson(SalesPerson_id, SalesPerson_first_name , SalesPerson_last_name ,SalesPerson_email,SalesPerson_phone,Showroom_id)
VALUES (1, 'Rafey','Hussain','RafeY@examplemail.com','02832932332',1);

--Insert into Vehicle_Type
INSERT INTO Vehicle_Type(Vehicle_Type_id,Vehicle_Type_Name)
VALUES (1, 'Coupe');

INSERT INTO Vehicle_Type(Vehicle_Type_id,Vehicle_Type_Name)
VALUES (2, 'SUV');

--Insert into Vehicle
SET Identity_Insert Vehicle on --on
INSERT INTO Vehicle(vehicle_id,Model,Brand,Model_year,Color,Price,Stock_Quantity,Vehicle_Type_id,Showroom_id)
VALUES (1,'CR-Z','Honda ',2013,'RED',3675000,1,1,1);

INSERT INTO Vehicle(vehicle_id,Model,Brand,Model_year,Color,Price,Stock_Quantity,Vehicle_Type_id,Showroom_id)
VALUES (2,'H6','Haval  ',2025,'Grey',9100000,10,2,1);
SET Identity_Insert Vehicle off --off

--Insert into Sales
INSERT INTO Sales(Sale_id,customer_id,vehicle_id,SalesPerson_id,Payment_id,TotalAmount)
VALUES (1,1,1,1,1,3675000);

--Insert into DealerShip
INSERT INTO DealerShip(DealerShip_id,DealerShip_Name,Phone,Email,DealerShip_Address)
VALUES (1,'Honda',903782983,'Honda@example.com','Lahore Pakistan');

--Insert into VehicleSupply
INSERT INTO VehicleSupply(VehicleSupply_id,DealerShip_id,vehicle_id,Quantity,Cost_Price)
VALUES (1,1,1,1,3675000);

--Insert into Service_centre
INSERT INTO Service_centre(Service_centre_id,Service_centre_Location,Service_centre_Contact)
VALUES (1,'Lahore','08372673');

--Insert into Taken_Service
INSERT INTO Taken_Service(vehicle_id,customer_id,Service_centre_id,Service_Description,Service_Cost)
VALUES (1,1,1,'Car Service',10000);

------------------------------------------ Update ----------------------------------------
update Vehicle_Type
set Vehicle_Type_Name = 'Coupe'
where Vehicle_Type_id = 1

update Vehicle
set Price = 7950000
where  vehicle_id = 3

------------------------------------------ Selection ----------------------------------------

Select * from Manager
Select * from Showroom
Select * from Customer
Select * from Customer_Address
Select * from Payment
Select * from SalesPerson
Select * from Vehicle_Type
Select * from Vehicle
Select * from Sales
Select * from DealerShip
Select * from VehicleSupply
Select * from Service_centre
Select * from Taken_Service

------------------------------------------ Views ----------------------------------------
go
CREATE VIEW ActiveSales AS
SELECT s.Sale_id, c.customer_first_name, v.Brand, v.Model, s.Sale_Date, s.TotalAmount
FROM Sales s
JOIN Customer c ON s.customer_id = c.customer_ID
JOIN Vehicle v ON s.vehicle_id = v.vehicle_id;
go

select * from ActiveSales

------------------------------------------ Indexes----------------------------------------

CREATE INDEX idx_brand ON Vehicle(Brand);
CREATE INDEX idx_city ON Showroom(city);
CREATE INDEX idx_Customer_City ON Customer(customer_first_name, customer_last_name);


------------------------------------------ Procedures ------------------------------------------

--Add New Customer
go
CREATE PROCEDURE AddCustomer
    @FirstName VARCHAR(50),
    @LastName VARCHAR(50),
    @Email VARCHAR(100),
    @Phone VARCHAR(20),
	@IsDeleted BIT
AS
BEGIN
    INSERT INTO Customer (customer_first_name, customer_last_name, customer_email, customer_phone,IsDeleted)
    VALUES (@FirstName, @LastName, @Email, @Phone,@IsDeleted);
END;


--Update Vehicle Stock Manually
go
CREATE PROCEDURE UpdateVehicleStock
    @VehicleId INT,
    @NewStock INT
AS
BEGIN
    UPDATE Vehicle
    SET Stock_Quantity = Stock_Quantity + @NewStock
    WHERE vehicle_id = @VehicleId;
END;


--Add New Payment
go
CREATE PROCEDURE AddPayment
    @Amount DECIMAL(10,2),
    @Method VARCHAR(20)
AS
BEGIN
    INSERT INTO Payment (Amount, Method)
    VALUES (@Amount, @Method);
END;

-- View All Vehicles by Showroom
go
CREATE PROCEDURE GetVehiclesByShowroom
    @ShowroomId INT
AS
BEGIN
    SELECT * FROM Vehicle
    WHERE Showroom_id = @ShowroomId;
END;

--Procedure to Get Sales by a Customer
go
CREATE PROCEDURE GetCustomerSales
    @CustomerId INT
AS
BEGIN
    SELECT s.Sale_id, v.Brand, v.Model, s.Sale_Date, s.TotalAmount
    FROM Sales s
    JOIN Vehicle v ON s.vehicle_id = v.vehicle_id
    WHERE s.customer_id = @CustomerId;
END;

--Procedure to Add New Vehicle
go
CREATE PROCEDURE AddVehicle
    @Model VARCHAR(10),
    @Brand VARCHAR(50),
    @ModelYear INT,
    @Color VARCHAR(20),
    @Price DECIMAL(10,2),
    @Stock INT,
    @VehicleTypeId INT,
    @ShowroomId INT
AS
BEGIN
    INSERT INTO Vehicle (Model, Brand, Model_year, Color, Price, Stock_Quantity, Vehicle_Type_id, Showroom_id)
    VALUES (@Model, @Brand, @ModelYear, @Color, @Price, @Stock, @VehicleTypeId, @ShowroomId);
END;


--Soft Delete Customer
go
CREATE PROCEDURE SoftDeleteCustomer
    @CustomerID INT
AS
BEGIN
    UPDATE Customer
    SET IsDeleted = 1
    WHERE customer_ID = @CustomerID;
END;

------------------------------------------ Transactions ------------------------------------------
--Selling a Vehicle Atomically
go
CREATE PROCEDURE SellVehicle
    @CustomerID INT,
    @VehicleID INT,
    @SalesPersonID INT,
    @Amount INT,
    @Method VARCHAR(20)
AS
BEGIN
    BEGIN TRANSACTION;
    BEGIN TRY
        DECLARE @SaleID INT = (SELECT ISNULL(MAX(Sale_id),0) + 1 FROM Sales);

        -- Insert Payment
        INSERT INTO Payment (Amount, Method, Payment_Status)
        VALUES (@Amount, @Method, 'Paid');

		DECLARE @PaymentID INT = (SELECT ISNULL(MAX(Payment_id),0) + 1 FROM Payment);

        -- Insert Sale
        INSERT INTO Sales (Sale_id, customer_id, vehicle_id, SalesPerson_id, Payment_id, TotalAmount)
        VALUES (@SaleID, @CustomerID, @VehicleID, @SalesPersonID, @PaymentID, @Amount);

        -- Decrease Vehicle Stock
        UPDATE Vehicle
        SET Stock_Quantity = Stock_Quantity - 1
        WHERE vehicle_id = @VehicleID;

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        THROW;
    END CATCH
END;

--Add Vehicle Supply (Dealership + Vehicle)
go
CREATE PROCEDURE AddVehicleSupply
    @DealerShipId INT,
    @VehicleId INT,
    @Quantity INT,
    @CostPrice DECIMAL(10,2)
AS
BEGIN
    BEGIN TRANSACTION;

    BEGIN TRY
        INSERT INTO VehicleSupply (VehicleSupply_id, DealerShip_id, vehicle_id, Quantity, Cost_Price)
        VALUES ((SELECT ISNULL(MAX(VehicleSupply_id),0)+1 FROM VehicleSupply), @DealerShipId, @VehicleId,@Quantity, @CostPrice);

        -- Update vehicle stock
        UPDATE Vehicle
        SET Stock_Quantity = Stock_Quantity + @Quantity
        WHERE vehicle_id = @VehicleId;

        COMMIT;
    END TRY
    BEGIN CATCH
        ROLLBACK;
        PRINT ERROR_MESSAGE();
    END CATCH;
END;

------------------------------------------ Functions ------------------------------------------
--Function to Calculate Discounted Price
go
CREATE FUNCTION GetDiscountedPrice
(
    @Price DECIMAL(10,2),
    @DiscountPercent DECIMAL(5,2)
)
RETURNS DECIMAL(10,2)
AS
BEGIN
    RETURN @Price - (@Price * @DiscountPercent / 100);
END;


--
go
CREATE FUNCTION GetFullCustomerName
(
    @CustomerId INT
)
RETURNS VARCHAR(101)
AS
BEGIN
    DECLARE @FullName VARCHAR(101);

    SELECT @FullName = customer_first_name + ' ' + customer_last_name
    FROM Customer
    WHERE customer_ID = @CustomerId;

    RETURN @FullName;
END;


-- Function to calculate total cost after tax (assuming 5% tax)
go
CREATE FUNCTION CalculateTotalCost(@Amount INT)
RETURNS INT
AS
BEGIN
    RETURN @Amount + (@Amount * 5 / 100)
END;
go

------------------------------------------ Triggers ------------------------------------------
/*
--Trigger to Update Vehicle Stock After Sale
go
CREATE TRIGGER trg_UpdateStockAfterSale
ON Sales
AFTER INSERT
AS
BEGIN
    UPDATE v
    SET v.Stock_Quantity = v.Stock_Quantity - 1
    FROM Vehicle v
    JOIN inserted i ON v.vehicle_id = i.vehicle_id;
END;

*/

--Trigger to Prevent Duplicate Emails in Customer Table (already enforced by UNIQUE but for demonstration)
go
CREATE TRIGGER trg_PreventDuplicateEmail
ON Customer
INSTEAD OF INSERT
AS
BEGIN
    IF EXISTS (
        SELECT 1 FROM Customer c
        JOIN inserted i ON c.customer_email = i.customer_email
    )
    BEGIN
        RAISERROR('Email already exists.', 16, 1);
        ROLLBACK;
    END
    ELSE
    BEGIN
        INSERT INTO Customer (customer_first_name, customer_last_name, customer_email, customer_phone)
        SELECT customer_first_name, customer_last_name, customer_email, customer_phone FROM inserted;
    END
END;

--Auto Payment Status Update
go
CREATE TRIGGER trg_UpdatePaymentStatus
ON Payment
AFTER INSERT
AS
BEGIN
    UPDATE Payment
    SET Payment_Status = 'Pending'
    WHERE Payment_Status IS NULL;
END;

------------------------------------------ Execution & Calling ----------------------------------------

exec AddCustomer 'Farooq','Afzal','Farooq@example.com','03456789011',0;

exec UpdateVehicleStock 1,1

exec AddPayment 9170000,'JAZZCASH'

exec GetVehiclesByShowroom 1

exec GetCustomerSales 1

exec AddVehicle 'Jolion','Haval',2025,'Red',795000,5,2,1

exec SoftDeleteCustomer 2

------------------------------------------ Transactions Execution & Calling ----------------------------------------
Select * from Vehicle
exec SellVehicle 2,2,1,910000,'JAZZCASH'
Select * from Vehicle
exec SellVehicle 2,1,1,4593302,'JAZZCASH'
Select * from Vehicle
exec AddVehicleSupply 1,3,5,950000

------------------------------------------ Functions Execution & Calling ----------------------------------------

select dbo.GetDiscountedPrice (950000,5)

select dbo.GetFullCustomerName (1)

select dbo.CalculateTotalCost (950000)

------------------------------------------ Selection ----------------------------------------
Select * from Manager
Select * from Showroom
Select * from Customer
Select * from Customer_Address
Select * from Payment
Select * from SalesPerson
Select * from Vehicle_Type
Select * from Vehicle
Select * from Sales
Select * from DealerShip
Select * from VehicleSupply
Select * from Service_centre
Select * from Taken_Service
