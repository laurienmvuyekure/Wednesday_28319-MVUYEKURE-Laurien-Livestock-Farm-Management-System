Name: MVUYEKURE Laurien
Student ID: 28319
Project Name: Livestock Farm Management System(LFMS)
1. Project Idea
LFMS is a comprehensive, data-driven livestock management system designed to track and manage every stage of an animal's life cycle, from birth to death. The primary goal is to replace fragmented record-keeping methods such as paper logs, spreadsheets, and memory with a unified, centralized platform. This system will provide farmers with the tools to make informed decisions that enhance animal welfare, optimize operational efficiency, and improve profitability.
The core functionality of LFMS encompasses:
1. Lifecycle Tracking: Record every significant event, including birth, parentage, growth milestones, health treatments, breeding cycles, production (e.g., milk, eggs, wool), and eventual departure (sale or death).
2. Health Management: Maintain detailed health records for vaccinations, medications, disease diagnoses, and veterinary visits.
3. Inventory & Supply Management: Track inventory of feed, medications, and other supplies, linking consumption to specific animal groups or individuals for cost analysis.
2.  Oracle Database Schema
The system will be built upon a normalized relational schema in Oracle to ensure data integrity and efficient querying. Below are the core tables:
Core Animal & Lineage Tables:
1. Animals (animal_id (NUMBER, PRIMARY KEY), tag_id (VARCHAR2, UNIQUE), name, breed, sex, date_of_birth, sire_id, dam_id, status)
Lifecycle Event Tables:
1.Lifecycle_Events (event_id (NUMBER, PRIMARY KEY), animal_id (NUMBER, FOREIGN KEY REFERENCES Animals(animal_id)), event_type, event_date, details)
2. Health_Records (health_id(NUMBER, PRIMARY KEY, FOREIGN KEY REFERENCES Lifecycle_Events(event_id)), treatment_type, description, veterinarian)
3. Production_Records (production_id (NUMBER, PRIMARY KEY, FOREIGN KEY REFERENCES Lifecycle_Events(event_id)), product_type, quantity, unit)

Operational Tables:
1. Locations (location_id (NUMBER, PRIMARY KEY), location_name, capacity, description) 
2. Animal_Location (assignment_id (NUMBER, PRIMARY KEY), animal_id (NUMBER, FOREIGN KEY REFERENCES Animals(animal_id)), location_id (NUMBER, FOREIGN KEY REFERENCES Locations(location_id)), date_moved_in, date_moved_out)
3. Inventory (item_id (NUMBER, PRIMARY KEY), item_name, item_type, current_stock, reorder_level)

3. Innovation and Improvement

LFMS introduces several key innovations that move beyond simple digital record-keeping:
1.  Integrated Cost-Per-Animal Analysis: Unlike standard systems that track either animal data or financial data in isolation, LFMS seamlessly links the two. By correlating data from the Inventory, Health_Records, and Production_Records tables with individual animals, the system can automatically calculate the total cost of raising each animal (including feed, medical, and other expenses) and compare it against its lifetime production or sale value. This provides an unprecedented, granular view of profitability per animal, helping farmers cull non-performing stock and optimize their herd for maximum financial return.
2.  Dynamic Pasture and Resource Management: Using the Animal_Location tracking, the system provides a real-time view of pasture and pen utilization. It can generate alerts when a location is over-capacity (increasing disease and stress risk) or under-utilized. This allows for dynamic rotation schedules and optimal use of land and housing resources, promoting animal welfare and operational efficiency.

In summary, LFMS is not merely a database; it is an intelligent management partner. It innovates by leveraging integrated data to empowering farmers to improve animal welfare, increase operational efficiency, and maximize profitability throughout the entire livestock lifecycle.


 
Diagram Explanation
The BPMN diagram illustrates the integrated workflow between LFMS's three core modules, demonstrating how they function as interconnected components rather than isolated systems.
Lifecycle Tracking serves as the central timeline, recording all significant animal events from birth to disposition. This module feeds data into the other two systems and provides the chronological backbone for all farm operations.
Health Management operates as both a specialized module and integrated component, where veterinary treatments and medical events are recorded as lifecycle events while also triggering inventory adjustments for medications and supplies.
Inventory & Supply Management acts as the resource backbone, tracking consumption patterns linked directly to both lifecycle and health events. This enables precise cost allocation per animal and resource optimization.
The integration creates a closed-loop system where:
1.	Lifecycle events trigger inventory consumption
2.	Health treatments update both medical histories and stock levels
3.	Production outputs are correlated with resource inputs
4.	All modules share a unified data model for comprehensive analytics
This interconnected architecture transforms LFMS from a simple record-keeping system into an intelligent management platform that provides holistic visibility into animal welfare, operational efficiency, and financial performance throughout the entire livestock lifecycle.


LOGICAL MODEL STRUCTURE
 




PDB Creation
  

Oracle Enterprise 

CODE DOCUMENTATION

1.	LFMS Oracle Database Creation Script - Livestock Farm Management System

i.	Create sequence for primary keys
CREATE SEQUENCE animal_seq START WITH 1001 INCREMENT BY 1;
CREATE SEQUENCE event_seq START WITH 2001 INCREMENT BY 1;
CREATE SEQUENCE location_seq START WITH 3001 INCREMENT BY 1;
CREATE SEQUENCE assignment_seq START WITH 4001 INCREMENT BY 1;
CREATE SEQUENCE inventory_seq START WITH 5001 INCREMENT BY 1;

ii.	Create tables
CREATE TABLE Animals ( animal_id NUMBER PRIMARY KEY, tag_id VARCHAR2(50) UNIQUE NOT NULL, name VARCHAR2(100), breed VARCHAR2(100) NOT NULL, sex CHAR(1) CHECK (sex IN ('M', 'F')), date_of_birth DATE, sire_id NUMBER REFERENCES Animals(animal_id), dam_id NUMBER REFERENCES Animals(animal_id), status VARCHAR2(20) DEFAULT 'ACTIVE' CHECK (status IN ('ACTIVE', 'SOLD', 'DECEASED', 'CULLED')), created_date DATE DEFAULT SYSDATE);

CREATE TABLE Lifecycle_Events ( event_id NUMBER PRIMARY KEY, animal_id NUMBER NOT NULL REFERENCES Animals(animal_id), event_type VARCHAR2(50) NOT NULL CHECK (event_type IN ('BIRTH', 'WEANING', 'VACCINATION', 'TREATMENT', 'BREEDING', 'MILKING', 'EGG_PRODUCTION', 'SALE', 'DEATH', 'MOVEMENT')), event_date DATE NOT NULL,  details CLOB, recorded_by VARCHAR2(100) DEFAULT 'SYSTEM');

CREATE TABLE Health_Records (  health_id NUMBER PRIMARY KEY REFERENCES Lifecycle_Events(event_id),   treatment_type VARCHAR2(100) NOT NULL, description CLOB,  veterinarian VARCHAR2(100),   medication_used VARCHAR2(200), dosage VARCHAR2(100), cost NUMBER(10,2) );



CREATE TABLE Production_Records (   production_id NUMBER PRIMARY KEY REFERENCES Lifecycle_Events(event_id), product_type VARCHAR2(50) NOT NULL CHECK (product_type IN ('MILK', 'EGGS', 'WOOL', 'MEAT')), quantity NUMBER(10,2) NOT NULL, unit VARCHAR2(20) NOT NULL, quality_grade VARCHAR2(20), sale_price NUMBER(10,2));

CREATE TABLE Locations ( location_id NUMBER PRIMARY KEY,   location_name VARCHAR2(100) NOT NULL,  location_type VARCHAR2(50) CHECK (location_type IN ('PASTURE', 'BARN', 'PEN', 'NURSERY', 'QUARANTINE')), capacity NUMBER NOT NULL, description VARCHAR2(500), status VARCHAR2(20) DEFAULT 'ACTIVE' );

CREATE TABLE Animal_Location ( assignment_id NUMBER PRIMARY KEY, animal_id NUMBER NOT NULL REFERENCES Animals(animal_id), location_id NUMBER NOT NULL REFERENCES Locations(location_id), date_moved_in DATE NOT NULL, date_moved_out DATE, reason VARCHAR2(200) );

CREATE TABLE Inventory ( item_id NUMBER PRIMARY KEY, item_name VARCHAR2(100) NOT NULL,  item_type VARCHAR2(50) NOT NULL CHECK (item_type IN ('FEED', 'MEDICATION', 'VACCINE', 'SUPPLY', 'EQUIPMENT')),  current_stock NUMBER(10,2) NOT NULL, unit VARCHAR2(20) NOT NULL,   reorder_level NUMBER(10,2), unit_cost NUMBER(10,2), supplier VARCHAR2(100) );

CREATE TABLE Animal_Feed_Consumption (
    consumption_id NUMBER PRIMARY KEY,
    animal_id NUMBER REFERENCES Animals(animal_id),
    item_id NUMBER REFERENCES Inventory(item_id),
    consumption_date DATE NOT NULL,
    quantity NUMBER(10,2) NOT NULL,
    cost NUMBER(10,2)
);



2.	Insert sample data

i.	Insert Locations
INSERT INTO Locations VALUES (location_seq.NEXTVAL, 'North Pasture', 'PASTURE', 50, 'Main grazing area for cattle', 'ACTIVE');
INSERT INTO Locations VALUES (location_seq.NEXTVAL, 'Dairy Barn A', 'BARN', 30, 'Milking and housing for dairy cows', 'ACTIVE');
INSERT INTO Locations VALUES (location_seq.NEXTVAL, 'Chicken Coop 1', 'PEN', 200, 'Laying hens housing', 'ACTIVE');
INSERT INTO Locations VALUES (location_seq.NEXTVAL, 'Nursery Pen', 'NURSERY', 20, 'Young animal nursery', 'ACTIVE');
INSERT INTO Locations VALUES (location_seq.NEXTVAL, 'Quarantine Area', 'QUARANTINE', 10, 'Sick animal isolation', 'ACTIVE');

-- Insert Inventory
INSERT INTO Inventory VALUES (inventory_seq.NEXTVAL, 'Alfalfa Hay', 'FEED', 1000, 'kg', 200, 0.80, 'Local Feed Co');
INSERT INTO Inventory VALUES (inventory_seq.NEXTVAL, 'Corn Grain', 'FEED', 500, 'kg', 100, 1.20, 'Local Feed Co');
INSERT INTO Inventory VALUES (inventory_seq.NEXTVAL, 'Penicillin', 'MEDICATION', 50, 'bottles', 10, 25.00, 'Vet Supply Inc');
INSERT INTO Inventory VALUES (inventory_seq.NEXTVAL, 'Rabies Vaccine', 'VACCINE', 100, 'doses', 20, 8.50, 'Vet Supply Inc');
INSERT INTO Inventory VALUES (inventory_seq.NEXTVAL, 'Milking Machine Filters', 'SUPPLY', 200, 'units', 50, 2.50, 'Farm Equipment Ltd');

-- Insert Animals (starting with foundation animals without parents)
INSERT INTO Animals VALUES (animal_seq.NEXTVAL, 'BULL001', 'Thunder', 'Angus', 'M', TO_DATE('2020-03-15', 'YYYY-MM-DD'), NULL, NULL, 'ACTIVE', SYSDATE);
INSERT INTO Animals VALUES (animal_seq.NEXTVAL, 'COW001', 'Daisy', 'Holstein', 'F', TO_DATE('2019-05-20', 'YYYY-MM-DD'), NULL, NULL, 'ACTIVE', SYSDATE);
INSERT INTO Animals VALUES (animal_seq.NEXTVAL, 'COW002', 'Buttercup', 'Jersey', 'F', TO_DATE('2018-11-10', 'YYYY-MM-DD'), NULL, NULL, 'ACTIVE', SYSDATE);

-- Insert more animals with parent relationships
INSERT INTO Animals VALUES (animal_seq.NEXTVAL, 'CALF001', 'Spot', 'Holstein-Angus Cross', 'M', TO_DATE('2023-01-15', 'YYYY-MM-DD'), 1001, 1002, 'ACTIVE', SYSDATE);
INSERT INTO Animals VALUES (animal_seq.NEXTVAL, 'CALF002', 'Rosie', 'Jersey-Angus Cross', 'F', TO_DATE('2023-02-20', 'YYYY-MM-DD'), 1001, 1003, 'ACTIVE', SYSDATE);
INSERT INTO Animals VALUES (animal_seq.NEXTVAL, 'CHK001', 'Clucky', 'Rhode Island Red', 'F', TO_DATE('2022-08-10', 'YYYY-MM-DD'), NULL, NULL, 'ACTIVE', SYSDATE);

-- Insert Lifecycle Events
INSERT INTO Lifecycle_Events VALUES (event_seq.NEXTVAL, 1004, 'BIRTH', TO_DATE('2023-01-15', 'YYYY-MM-DD'), 'Healthy male calf born, weight: 45kg', 'FARMER001');
INSERT INTO Lifecycle_Events VALUES (event_seq.NEXTVAL, 1005, 'BIRTH', TO_DATE('2023-02-20', 'YYYY-MM-DD'), 'Healthy female calf born, weight: 38kg', 'FARMER001');
INSERT INTO Lifecycle_Events VALUES (event_seq.NEXTVAL, 1002, 'MILKING', TO_DATE('2023-11-01', 'YYYY-MM-DD'), 'Morning milking session', 'FARMER002');
INSERT INTO Lifecycle_Events VALUES (event_seq.NEXTVAL, 1003, 'MILKING', TO_DATE('2023-11-01', 'YYYY-MM-DD'), 'Morning milking session', 'FARMER002');

-- Insert Health Records
INSERT INTO Lifecycle_Events VALUES (event_seq.NEXTVAL, 1004, 'VACCINATION', TO_DATE('2023-01-20', 'YYYY-MM-DD'), 'Initial vaccination series', 'VET001');
INSERT INTO Health_Records VALUES (event_seq.CURRVAL, 'Vaccination', 'Rabies and Clostridium vaccination', 'Dr. Smith', 'Rabies Vaccine', '1 dose', 15.50);

INSERT INTO Lifecycle_Events VALUES (event_seq.NEXTVAL, 1002, 'TREATMENT', TO_DATE('2023-10-15', 'YYYY-MM-DD'), 'Mastitis treatment', 'VET001');
INSERT INTO Health_Records VALUES (event_seq.CURRVAL, 'Antibiotic Treatment', 'Mild mastitis in left rear quarter', 'Dr. Smith', 'Penicillin', '10ml twice daily for 5 days', 45.00);

-- Insert Production Records
INSERT INTO Lifecycle_Events VALUES (event_seq.NEXTVAL, 1002, 'MILKING', TO_DATE('2023-11-01', 'YYYY-MM-DD'), 'Daily milk production', 'FARMER002');
INSERT INTO Production_Records VALUES (event_seq.CURRVAL, 'MILK', 25.5, 'liters', 'Grade A', NULL);

INSERT INTO Lifecycle_Events VALUES (event_seq.NEXTVAL, 1003, 'MILKING', TO_DATE('2023-11-01', 'YYYY-MM-DD'), 'Daily milk production', 'FARMER002');
INSERT INTO Production_Records VALUES (event_seq.CURRVAL, 'MILK', 18.2, 'liters', 'Grade A', NULL);

INSERT INTO Lifecycle_Events VALUES (event_seq.NEXTVAL, 1006, 'EGG_PRODUCTION', TO_DATE('2023-11-01', 'YYYY-MM-DD'), 'Daily egg collection', 'FARMER003');
INSERT INTO Production_Records VALUES (event_seq.CURRVAL, 'EGGS', 12, 'units', 'Large', 3.60);

-- Insert Animal Locations
INSERT INTO Animal_Location VALUES (assignment_seq.NEXTVAL, 1001, 3001, TO_DATE('2023-01-01', 'YYYY-MM-DD'), NULL, 'Pasture rotation');
INSERT INTO Animal_Location VALUES (assignment_seq.NEXTVAL, 1002, 3002, TO_DATE('2023-01-01', 'YYYY-MM-DD'), NULL, 'Dairy barn assignment');
INSERT INTO Animal_Location VALUES (assignment_seq.NEXTVAL, 1003, 3002, TO_DATE('2023-01-01', 'YYYY-MM-DD'), NULL, 'Dairy barn assignment');
INSERT INTO Animal_Location VALUES (assignment_seq.NEXTVAL, 1004, 3004, TO_DATE('2023-01-15', 'YYYY-MM-DD'), NULL, 'Nursery for young calf');
INSERT INTO Animal_Location VALUES (assignment_seq.NEXTVAL, 1005, 3004, TO_DATE('2023-02-20', 'YYYY-MM-DD'), NULL, 'Nursery for young calf');
INSERT INTO Animal_Location VALUES (assignment_seq.NEXTVAL, 1006, 3003, TO_DATE('2023-01-01', 'YYYY-MM-DD'), NULL, 'Chicken coop assignment');

-- Insert Feed Consumption
INSERT INTO Animal_Feed_Consumption VALUES (assignment_seq.NEXTVAL, 1002, 5001, TO_DATE('2023-11-01', 'YYYY-MM-DD'), 15, 12.00);
INSERT INTO Animal_Feed_Consumption VALUES (assignment_seq.NEXTVAL, 1003, 5001, TO_DATE('2023-11-01', 'YYYY-MM-DD'), 12, 9.60);
INSERT INTO Animal_Feed_Consumption VALUES (assignment_seq.NEXTVAL, 1006, 5002, TO_DATE('2023-11-01', 'YYYY-MM-DD'), 2, 2.40);

COMMIT;

-- WINDOW FUNCTIONS
-- 1. Rank animals by milk production using window function
SELECT 
    a.animal_id,
    a.tag_id,
    a.name,
    pr.product_type,
    pr.quantity,
    RANK() OVER (PARTITION BY pr.product_type ORDER BY pr.quantity DESC) as production_rank,
    AVG(pr.quantity) OVER (PARTITION BY pr.product_type) as avg_production
FROM Production_Records pr
JOIN Lifecycle_Events le ON pr.production_id = le.event_id
JOIN Animals a ON le.animal_id = a.animal_id
WHERE pr.product_type = 'MILK'
AND le.event_date >= TRUNC(SYSDATE) - 30;

-- 2. Calculate running total of medical costs per animal
SELECT 
    a.animal_id,
    a.name,
    le.event_date,
    hr.treatment_type,
    hr.cost,
    SUM(hr.cost) OVER (
        PARTITION BY a.animal_id 
        ORDER BY le.event_date 
        ROWS UNBOUNDED PRECEDING
    ) as running_total_cost
FROM Health_Records hr
JOIN Lifecycle_Events le ON hr.health_id = le.event_id
JOIN Animals a ON le.animal_id = a.animal_id
ORDER BY a.animal_id, le.event_date;

-- TRIGGERS

-- Trigger 1: Auto-update inventory when health treatments are recorded
CREATE OR REPLACE TRIGGER trg_inventory_update_health
AFTER INSERT ON Health_Records
FOR EACH ROW
DECLARE
    v_item_id NUMBER;
    v_current_stock NUMBER;
BEGIN
    -- Find the inventory item for the medication
    SELECT item_id, current_stock INTO v_item_id, v_current_stock
    FROM Inventory 
    WHERE UPPER(item_name) LIKE '%' || UPPER(:NEW.medication_used) || '%'
    AND item_type IN ('MEDICATION', 'VACCINE')
    AND ROWNUM = 1;
    
    IF v_current_stock > 0 THEN
        UPDATE Inventory 
        SET current_stock = current_stock - 1
        WHERE item_id = v_item_id;
        
        -- Insert into application log (you would need to create this table)
        INSERT INTO application_log (log_id, log_message, log_date)
        VALUES (assignment_seq.NEXTVAL, 
                'Inventory updated for medication: ' || :NEW.medication_used || ' for animal treatment',
                SYSDATE);
    END IF;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        -- Log error but don't stop the operation
        INSERT INTO application_log (log_id, log_message, log_date, log_level)
        VALUES (assignment_seq.NEXTVAL, 
                'Warning: No inventory found for medication: ' || :NEW.medication_used,
                SYSDATE, 'WARNING');
    WHEN OTHERS THEN
        -- Log unexpected errors
        INSERT INTO application_log (log_id, log_message, log_date, log_level)
        VALUES (assignment_seq.NEXTVAL, 
                'Error in trg_inventory_update_health: ' || SQLERRM,
                SYSDATE, 'ERROR');
END;
/

-- Trigger 2: Prevent overcapacity in locations
CREATE OR REPLACE TRIGGER trg_check_location_capacity
BEFORE INSERT ON Animal_Location
FOR EACH ROW
DECLARE
    v_current_count NUMBER;
    v_capacity NUMBER;
BEGIN
    -- Get current animal count in the location
    SELECT COUNT(*) INTO v_current_count
    FROM Animal_Location
    WHERE location_id = :NEW.location_id
    AND date_moved_out IS NULL;
    
    -- Get location capacity
    SELECT capacity INTO v_capacity
    FROM Locations
    WHERE location_id = :NEW.location_id;
    
    IF v_current_count >= v_capacity THEN
        RAISE_APPLICATION_ERROR(-20001, 
            'Location ' || :NEW.location_id || ' is at full capacity. Current: ' || 
            v_current_count || ', Capacity: ' || v_capacity);
    END IF;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        RAISE_APPLICATION_ERROR(-20002, 'Location not found');
END;
/
-- CURSOR EXAMPLE

-- Procedure using cursor to generate animal health report
CREATE OR REPLACE PROCEDURE generate_animal_health_report IS
    CURSOR health_cursor IS
        SELECT 
            a.animal_id,
            a.tag_id,
            a.name,
            a.breed,
            COUNT(hr.health_id) as treatment_count,
            SUM(hr.cost) as total_health_cost
        FROM Animals a
        LEFT JOIN Lifecycle_Events le ON a.animal_id = le.animal_id
        LEFT JOIN Health_Records hr ON le.event_id = hr.health_id
        WHERE le.event_type IN ('VACCINATION', 'TREATMENT')
        AND le.event_date >= ADD_MONTHS(SYSDATE, -12)
        GROUP BY a.animal_id, a.tag_id, a.name, a.breed
        ORDER BY total_health_cost DESC NULLS LAST;
    
    v_animal_id Animals.animal_id%TYPE;
    v_tag_id Animals.tag_id%TYPE;
    v_name Animals.name%TYPE;
    v_breed Animals.breed%TYPE;
    v_treatment_count NUMBER;
    v_total_cost NUMBER;
BEGIN
    DBMS_OUTPUT.PUT_LINE('ANIMAL HEALTH REPORT - ' || TO_CHAR(SYSDATE, 'YYYY-MM-DD'));
    DBMS_OUTPUT.PUT_LINE('=========================================');
    
    OPEN health_cursor;
    LOOP
        FETCH health_cursor INTO v_animal_id, v_tag_id, v_name, v_breed, v_treatment_count, v_total_cost;
        EXIT WHEN health_cursor%NOTFOUND;
        
        DBMS_OUTPUT.PUT_LINE(
            'ID: ' || v_animal_id || 
            ' | Tag: ' || v_tag_id ||
            ' | Name: ' || NVL(v_name, 'Unknown') ||
            ' | Breed: ' || v_breed ||
            ' | Treatments: ' || v_treatment_count ||
            ' | Total Cost: $' || NVL(TO_CHAR(v_total_cost, '9990.99'), '0.00')
        );
    END LOOP;
    CLOSE health_cursor;
    
    DBMS_OUTPUT.PUT_LINE('=========================================');
    DBMS_OUTPUT.PUT_LINE('Report generated successfully.');
EXCEPTION
    WHEN OTHERS THEN
        IF health_cursor%ISOPEN THEN
            CLOSE health_cursor;
        END IF;
        DBMS_OUTPUT.PUT_LINE('Error generating health report: ' || SQLERRM);
END;
/
-- STORED PROCEDURES

-- Procedure 1: Calculate cost per animal analysis
CREATE OR REPLACE PROCEDURE calculate_animal_profitability (
    p_animal_id IN NUMBER,
    p_start_date IN DATE DEFAULT ADD_MONTHS(SYSDATE, -12),
    p_end_date IN DATE DEFAULT SYSDATE
) IS
    v_total_feed_cost NUMBER := 0;
    v_total_health_cost NUMBER := 0;
    v_total_production_value NUMBER := 0;
    v_net_profit NUMBER := 0;
    v_animal_name Animals.name%TYPE;
BEGIN
    -- Get animal name
    SELECT name INTO v_animal_name FROM Animals WHERE animal_id = p_animal_id;
    
    -- Calculate feed costs
    SELECT NVL(SUM(afc.cost), 0) INTO v_total_feed_cost
    FROM Animal_Feed_Consumption afc
    WHERE afc.animal_id = p_animal_id
    AND afc.consumption_date BETWEEN p_start_date AND p_end_date;
    
    -- Calculate health costs
    SELECT NVL(SUM(hr.cost), 0) INTO v_total_health_cost
    FROM Health_Records hr
    JOIN Lifecycle_Events le ON hr.health_id = le.event_id
    WHERE le.animal_id = p_animal_id
    AND le.event_date BETWEEN p_start_date AND p_end_date;
    
    -- Calculate production value
    SELECT NVL(SUM(pr.quantity * NVL(pr.sale_price, 
        CASE 
            WHEN pr.product_type = 'MILK' THEN 0.50  -- Default price per liter
            WHEN pr.product_type = 'EGGS' THEN 0.30  -- Default price per egg
            ELSE 1.00  -- Default for other products
        END)), 0) INTO v_total_production_value
    FROM Production_Records pr
    JOIN Lifecycle_Events le ON pr.production_id = le.event_id
    WHERE le.animal_id = p_animal_id
    AND le.event_date BETWEEN p_start_date AND p_end_date;
    
    -- Calculate net profit
    v_net_profit := v_total_production_value - (v_total_feed_cost + v_total_health_cost);
    
    -- Output results
    DBMS_OUTPUT.PUT_LINE('PROFITABILITY ANALYSIS FOR: ' || v_animal_name || ' (ID: ' || p_animal_id || ')');
    DBMS_OUTPUT.PUT_LINE('Period: ' || TO_CHAR(p_start_date, 'YYYY-MM-DD') || ' to ' || TO_CHAR(p_end_date, 'YYYY-MM-DD'));
    DBMS_OUTPUT.PUT_LINE('=========================================');
    DBMS_OUTPUT.PUT_LINE('Total Feed Cost: $' || TO_CHAR(v_total_feed_cost, '99990.99'));
    DBMS_OUTPUT.PUT_LINE('Total Health Cost: $' || TO_CHAR(v_total_health_cost, '99990.99'));
    DBMS_OUTPUT.PUT_LINE('Total Production Value: $' || TO_CHAR(v_total_production_value, '99990.99'));
    DBMS_OUTPUT.PUT_LINE('-----------------------------------------');
    DBMS_OUTPUT.PUT_LINE('NET PROFIT: $' || TO_CHAR(v_net_profit, '99990.99'));
    DBMS_OUTPUT.PUT_LINE('=========================================');
    
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        RAISE_APPLICATION_ERROR(-20003, 'Animal not found with ID: ' || p_animal_id);
    WHEN OTHERS THEN
        RAISE_APPLICATION_ERROR(-20004, 'Error calculating profitability: ' || SQLERRM);
END;
/

-- Procedure 2: Move animal to new location with validation
CREATE OR REPLACE PROCEDURE move_animal_location (
    p_animal_id IN NUMBER,
    p_new_location_id IN NUMBER,
    p_reason IN VARCHAR2 DEFAULT 'Rotation'
) IS
    v_current_assignment_id NUMBER;
    v_animal_exists NUMBER;
    v_location_exists NUMBER;
BEGIN
    -- Validate animal exists
    SELECT COUNT(*) INTO v_animal_exists FROM Animals WHERE animal_id = p_animal_id;
    IF v_animal_exists = 0 THEN
        RAISE_APPLICATION_ERROR(-20005, 'Animal not found with ID: ' || p_animal_id);
    END IF;
    
    -- Validate location exists
    SELECT COUNT(*) INTO v_location_exists FROM Locations WHERE location_id = p_new_location_id;
    IF v_location_exists = 0 THEN
        RAISE_APPLICATION_ERROR(-20006, 'Location not found with ID: ' || p_new_location_id);
    END IF;
    
    -- Close current active assignment
    UPDATE Animal_Location 
    SET date_moved_out = SYSDATE,
        reason = p_reason || ' (Auto-closed)'
    WHERE animal_id = p_animal_id 
    AND date_moved_out IS NULL;
    
    -- Create new assignment
    INSERT INTO Animal_Location (assignment_id, animal_id, location_id, date_moved_in, reason)
    VALUES (assignment_seq.NEXTVAL, p_animal_id, p_new_location_id, SYSDATE, p_reason);
    
    -- Record the movement event
    INSERT INTO Lifecycle_Events (event_id, animal_id, event_type, event_date, details)
    VALUES (event_seq.NEXTVAL, p_animal_id, 'MOVEMENT', SYSDATE, 
            'Moved to location ' || p_new_location_id || ' - ' || p_reason);
    
    COMMIT;
    
    DBMS_OUTPUT.PUT_LINE('Animal ' || p_animal_id || ' successfully moved to location ' || p_new_location_id);
    
EXCEPTION
    WHEN OTHERS THEN
        ROLLBACK;
        RAISE_APPLICATION_ERROR(-20007, 'Error moving animal: ' || SQLERRM);
END;
/
-- FUNCTIONS

-- Function 1: Calculate animal age in years/months
CREATE OR REPLACE FUNCTION get_animal_age(
    p_animal_id IN NUMBER
) RETURN VARCHAR2 IS
    v_date_of_birth DATE;
    v_years NUMBER;
    v_months NUMBER;
BEGIN
    SELECT date_of_birth INTO v_date_of_birth
    FROM Animals
    WHERE animal_id = p_animal_id;
    
    v_months := MONTHS_BETWEEN(SYSDATE, v_date_of_birth);
    v_years := TRUNC(v_months / 12);
    v_months := TRUNC(MOD(v_months, 12));
    
    RETURN v_years || ' years, ' || v_months || ' months';
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        RETURN 'Unknown';
    WHEN OTHERS THEN
        RETURN 'Error calculating age';
END;
/

-- Function 2: Get location utilization percentage
CREATE OR REPLACE FUNCTION get_location_utilization(
    p_location_id IN NUMBER
) RETURN NUMBER IS
    v_current_count NUMBER;
    v_capacity NUMBER;
    v_utilization NUMBER;
BEGIN
    -- Get current animal count
    SELECT COUNT(*) INTO v_current_count
    FROM Animal_Location
    WHERE location_id = p_location_id
    AND date_moved_out IS NULL;
    
    -- Get location capacity
    SELECT capacity INTO v_capacity
    FROM Locations
    WHERE location_id = p_location_id;
    
    IF v_capacity > 0 THEN
        v_utilization := (v_current_count / v_capacity) * 100;
    ELSE
        v_utilization := 0;
    END IF;
    
    RETURN ROUND(v_utilization, 2);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        RETURN -1;  -- Indicates location not found
    WHEN OTHERS THEN
        RETURN -2;  -- Indicates calculation error
END;
/

-- Function 3: Check if inventory needs reorder
CREATE OR REPLACE FUNCTION check_inventory_reorder
RETURN SYS_REFCURSOR IS
    v_cursor SYS_REFCURSOR;
BEGIN
    OPEN v_cursor FOR
    SELECT 
        item_id,
        item_name,
        item_type,
        current_stock,
        reorder_level,
        unit,
        CASE 
            WHEN current_stock <= reorder_level THEN 'REORDER NEEDED'
            WHEN current_stock <= reorder_level * 1.5 THEN 'LOW STOCK'
            ELSE 'SUFFICIENT'
        END as stock_status
    FROM Inventory
    WHERE reorder_level IS NOT NULL
    ORDER BY stock_status, item_type, item_name;
    
    RETURN v_cursor;
END;
/
-- EXCEPTION HANDLING DEMONSTRATION

-- Procedure with comprehensive exception handling
CREATE OR REPLACE PROCEDURE record_animal_production(
    p_animal_id IN NUMBER,
    p_product_type IN VARCHAR2,
    p_quantity IN NUMBER,
    p_unit IN VARCHAR2,
    p_quality_grade IN VARCHAR2 DEFAULT NULL,
    p_sale_price IN NUMBER DEFAULT NULL
) IS
    v_event_id NUMBER;
    v_animal_status Animals.status%TYPE;
    e_animal_not_found EXCEPTION;
    e_invalid_quantity EXCEPTION;
    e_animal_inactive EXCEPTION;
    PRAGMA EXCEPTION_INIT(e_animal_not_found, -20008);
    PRAGMA EXCEPTION_INIT(e_invalid_quantity, -20009);
    PRAGMA EXCEPTION_INIT(e_animal_inactive, -20010);
BEGIN
    -- Validate animal exists and is active
    BEGIN
        SELECT status INTO v_animal_status
        FROM Animals
        WHERE animal_id = p_animal_id;
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            RAISE e_animal_not_found;
    END;
    
    IF v_animal_status != 'ACTIVE' THEN
        RAISE e_animal_inactive;
    END IF;
    
    IF p_quantity <= 0 THEN
        RAISE e_invalid_quantity;
    END IF;
    
    -- Generate event ID
    SELECT event_seq.NEXTVAL INTO v_event_id FROM DUAL;
    
    -- Insert lifecycle event
    INSERT INTO Lifecycle_Events (event_id, animal_id, event_type, event_date, details)
    VALUES (v_event_id, p_animal_id, 
            CASE 
                WHEN p_product_type = 'MILK' THEN 'MILKING'
                WHEN p_product_type = 'EGGS' THEN 'EGG_PRODUCTION'
                ELSE 'PRODUCTION'
            END,
            SYSDATE, 
            'Recorded ' || p_quantity || ' ' || p_unit || ' of ' || p_product_type);
    
    -- Insert production record
    INSERT INTO Production_Records (production_id, product_type, quantity, unit, quality_grade, sale_price)
    VALUES (v_event_id, p_product_type, p_quantity, p_unit, p_quality_grade, p_sale_price);
    
    COMMIT;
    
    DBMS_OUTPUT.PUT_LINE('Production record added successfully for animal ' || p_animal_id);
    
EXCEPTION
    WHEN e_animal_not_found THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('Error: Animal not found with ID ' || p_animal_id);
        RAISE_APPLICATION_ERROR(-20008, 'Animal not found');
    WHEN e_animal_inactive THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('Error: Animal ' || p_animal_id || ' is not active. Current status: ' || v_animal_status);
        RAISE_APPLICATION_ERROR(-20010, 'Animal is not active');
    WHEN e_invalid_quantity THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('Error: Invalid quantity ' || p_quantity || '. Quantity must be positive.');
        RAISE_APPLICATION_ERROR(-20009, 'Invalid quantity');
    WHEN OTHERS THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('Unexpected error: ' || SQLERRM);
        RAISE_APPLICATION_ERROR(-20011, 'Unexpected error recording production');
END;
/
-- DEMONSTRATION AND TESTING

-- Create application log table for triggers
CREATE TABLE application_log (
    log_id NUMBER PRIMARY KEY,
    log_message VARCHAR2(1000),
    log_date DATE DEFAULT SYSDATE,
    log_level VARCHAR2(20) DEFAULT 'INFO'
);

-- Test the functions and procedures
BEGIN
    -- Test profitability calculation
    DBMS_OUTPUT.PUT_LINE('=== Testing Profitability Calculation ===');
    calculate_animal_profitability(1002, TO_DATE('2023-01-01', 'YYYY-MM-DD'), SYSDATE);
    
    DBMS_OUTPUT.PUT_LINE(CHR(10) || '=== Testing Animal Age Function ===');
    DBMS_OUTPUT.PUT_LINE('Animal 1002 age: ' || get_animal_age(1002));
    
    DBMS_OUTPUT.PUT_LINE(CHR(10) || '=== Testing Location Utilization ===');
    DBMS_OUTPUT.PUT_LINE('Location 3002 utilization: ' || get_location_utilization(3002) || '%');
    
    DBMS_OUTPUT.PUT_LINE(CHR(10) || '=== Testing Health Report ===');
    generate_animal_health_report;
    
    -- Test moving an animal
    DBMS_OUTPUT.PUT_LINE(CHR(10) || '=== Testing Animal Movement ===');
    move_animal_location(1004, 3001, 'Weaned and moved to pasture');
    
    -- Test production recording with exception handling
    DBMS_OUTPUT.PUT_LINE(CHR(10) || '=== Testing Production Recording ===');
    record_animal_production(1002, 'MILK', 22.5, 'liters', 'Grade A', NULL);
    
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Demo error: ' || SQLERRM);
END;
/

-- Example queries using window functions for advanced analytics
SELECT 
    location_id,
    location_name,
    current_count,
    capacity,
    utilization_percentage,
    RANK() OVER (ORDER BY utilization_percentage DESC) as utilization_rank
FROM (
    SELECT 
        l.location_id,
        l.location_name,
        l.capacity,
        COUNT(al.animal_id) as current_count,
        ROUND((COUNT(al.animal_id) / l.capacity) * 100, 2) as utilization_percentage
    FROM Locations l
    LEFT JOIN Animal_Location al ON l.location_id = al.location_id AND al.date_moved_out IS NULL
    GROUP BY l.location_id, l.location_name, l.capacity
);

-- Show animals with their production ranking
SELECT 
    a.animal_id,
    a.name,
    a.breed,
    SUM(pr.quantity) as total_production,
    RANK() OVER (ORDER BY SUM(pr.quantity) DESC) as production_rank,
    get_animal_age(a.animal_id) as age
FROM Animals a
JOIN Lifecycle_Events le ON a.animal_id = le.animal_id
JOIN Production_Records pr ON le.event_id = pr.production_id
WHERE pr.product_type = 'MILK'
AND le.event_date >= ADD_MONTHS(SYSDATE, -3)
GROUP BY a.animal_id, a.name, a.breed
ORDER BY production_rank;

COMMIT;

DBMS_OUTPUT.PUT_LINE('LFMS Database setup completed successfully!');
