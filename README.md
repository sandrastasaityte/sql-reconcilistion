create database unreconciled;
use unreconciled;
CREATE TABLE GL (
    gl_ref SMALLINT UNSIGNED NOT NULL AUTO_INCREMENT,
    transaction_date DATE NOT NULL,
    line_description VARCHAR(255) DEFAULT NULL,
    accounted_dr DECIMAL(15, 2) NOT NULL,
    accounted_cr DECIMAL(15, 2) NOT NULL,
    total DECIMAL(15, 2) NOT NULL,
    event_date DATETIME NOT NULL,
    gl_je_name VARCHAR(255) DEFAULT NULL,
    header_description VARCHAR(255) DEFAULT NULL,
    PRIMARY KEY (gl_ref)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;


CREATE TABLE 1950_unreconciled (
    date DATE NOT NULL,
    amount DECIMAL(15, 2) NOT NULL,
    transaction_type VARCHAR(255) NOT NULL,
    source VARCHAR(255) NOT NULL,
    statement TEXT,
    additional_information TEXT,
    value_date DATE,
    account_servicer_reference VARCHAR(255),
    customer_reference VARCHAR(255),
    transaction_number VARCHAR(255),
    journal VARCHAR(255),
    journal_batch VARCHAR(255),
    journal_description TEXT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

DROP TABLE 1950_UNRECONCILED;

CREATE TABLE Bank (
    bank_ref VARCHAR(50) NOT NULL,
    booking_date  DATE NOT NULL,
    remitter_beneficiary VARCHAR(255) NOT NULL,
    value_date DATE NOT NULL,
    Reference VARCHAR(255),
    amount DECIMAL(15, 2) NOT NULL,
    status VARCHAR(100),
    transaction_type VARCHAR(100) NOT NULL,
    company VARCHAR(255),
    ap_comments TEXT,
    ops VARCHAR(50),
    type VARCHAR(100),
    PRIMARY KEY (bank_ref)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

SELECT * FROM 1950_UNRECONCILED;
select * from bank;
select * from gl;

SELECT
    u.transaction_number AS unreconciled_transaction,
    u.amount AS unreconciled_amount,
    u.value_date AS unreconciled_value_date,
    g.gl_ref AS gl_reference,
    g.amount AS gl_amount,
    g.value_date AS gl_value_date,
    b.bank_ref AS bank_reference,
    b.amount AS bank_amount,
    b.value_date AS bank_value_date
FROM
    1950_unreconciled u
LEFT JOIN
    GL g ON u.amount = g.amount AND u.value_date = g.value_date
LEFT JOIN
    Bank b ON u.amount = b.amount AND u.value_date = b.value_date
WHERE
    g.gl_ref IS NULL OR b.bank_ref IS NULL;

INSERT INTO Bank VALUES (5917, '2024-01-31', 'INTERNOVERFORING', '2024-01-31', 'i000233013nm8521', -44730215.85, 'matched', 'payment', 0, 0, 0, 0);

INSERT INTO 1950_Unreconciled VALUES ('2024-01-31', -44730215.85, 'Miscellaneous', 'statement', 'line 2 1-22', 'I000233013NM8521', '2024-01-31', '013103958582298', 'I000233013NM8521', 0, 0, 0, 0);

INSERT INTO 1950_Unreconciled VALUES ("2024-05-31", -44730215.85, 0, "JOURNAL", 0, 0, 0, 0, 0, "Auto transfer 1950-1920 2024", "CF Adjustment", "CF Spreadsheet A 30000001035", "auto transfer 1950-1920 2024");

INSERT INTO GL VALUES (1924, "1900-01-01", "auto transfer 1950-1920 2024", 0, 44730215.85, -44730215.85, "2024-01-01", "CF Adjustment", "CF");

SELECT
    1950_unreconciled.transaction_number AS unreconciled_transaction,
    1950_unreconciled.amount AS unreconciled_amount,
    1950_unreconciled.value_date AS unreconciled_value_date,
    1950_unreconciled.transaction_type AS unreconciled_transaction_type,
    gl.gl_ref AS gl_reference,
    gl.accounted_dr AS gl_debit,
    gl.accounted_cr AS gl_credit,
    gl.value_date AS gl_value_date,
    bank.bank_ref AS bank_reference,
    bank.amount AS bank_amount,
    bank.value_date AS bank_value_date
FROM
    1950_unreconciled
LEFT JOIN
    GL ON 1950_unreconciled.amount = gl.accounted_dr AND 1950_unreconciled.value_date = gl.value_date
LEFT JOIN
    Bank ON 1950_unreconciled.amount = bank.amount AND 1950_unreconciled.value_date = bank.value_date
WHERE
    gl.gl_ref IS NULL OR bank.bank_ref IS NULL;
    
    SELECT
    u.transaction_number AS unreconciled_transaction,
    u.amount AS unreconciled_amount,
    u.value_date AS unreconciled_value_date,
    u.transaction_type AS unreconciled_transaction_type,
    g.gl_ref AS gl_reference,
    g.accounted_dr AS gl_debit,
    g.accounted_cr AS gl_credit,
    g.value_date AS gl_value_date,
    b.bank_ref AS bank_reference,
    b.amount AS bank_amount,
    b.value_date AS bank_value_date
FROM
    1950_unreconciled u
LEFT JOIN
    GL g ON u.amount = g.accounted_dr AND u.value_date = g.value_date
LEFT JOIN
    Bank b ON u.amount = b.amount AND u.value_date = b.value_date
WHERE
    g.gl_ref IS NULL OR b.bank_ref IS NULL;

use unreconciled;
use 1950_unreconciled;
drop table 1950_unreconciled;
INSERT INTO 1950_Unreconciled VALUES ("2024-05-31", "-44730215.85", 0, "JOURNAL", 0, 0, 0, 0, 0, "Auto transfer 1950-1920 2024", "CF Adjustment", "CF Spreadsheet A 30000001035", "auto transfer 1950-1920 2024");

INSERT INTO 1950_Unreconciled 
VALUES (
    '2024-05-31',                   -- date (correct date format)
    -44730215.85,  					-- amount (numeric value)
    '',                       		-- transaction_type (string)
    'Journal',                       -- source (empty string)
    '',                              -- statement (empty string)
    '',                              -- additional_information (empty string)
    '2024-05-31',                    -- value_date (valid date, or you can use NULL if unknown)
    NULL,                            -- account_servicer_reference (NULL if no data)
    '',                              -- customer_reference (empty string)
    'Auto transfer 1950-1920 2024',  -- transaction_number (string)
    'CF Adjustment',                 -- journal (string)
    'CF Spreadsheet A 30000001035',  -- journal_batch (string)
    'auto transfer 1950-1920 2024'   -- journal_description (string)
);

LOAD DATA INFILE "C:\Users\Sandra Stasaityte\Downloads"
INTO TABLE 1950_Unreconciled
FIELDS TERMINATED BY ','  -- This specifies that the fields are separated by commas
ENCLOSED BY '"'           -- This specifies that the fields are enclosed by double quotes
LINES TERMINATED BY '\n'  -- This specifies that each new line is a new record
IGNORE 1 LINES            -- Ignore the first line if your CSV has a header row
(date, amount, transaction_type, source, statement, additional_information, value_date, account_servicer_reference, customer_reference, transaction_number, journal, journal_batch, journal_description);

LOAD DATA INFILE "C:\Users\Sandra Stasaityte\Downloads"
INTO TABLE GL
FIELDS TERMINATED BY ',' 
LINES TERMINATED BY '\n' 
IGNORE 1 LINES
(gl_ref, transaction_date, line_description, @accounted_dr, @accounted_cr, @total, event_date, gl_je_name, header_description)
SET accounted_dr = IF(@accounted_dr = '', NULL, @accounted_dr),
    accounted_cr = IF(@accounted_cr = '', NULL, @accounted_cr),
    total = IF(@total = '', NULL, @total);  -- Converts empty strings to NULL
    
LOAD DATA INFILE "C:\Users\Sandra Stasaityte\Downloads"
INTO TABLE Bank
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"'             -- Optional: if your CSV values are enclosed in double quotes
LINES TERMINATED BY '\n' 
IGNORE 1 LINES              -- Skips the first line (headers) if present
(bank_ref, booking_date, remitter_beneficiary, value_date, Reference, amount, status, transaction_type, company, ap_comments, ops, type);

CREATE TABLE Unreconciled_Items (
    unreconciled_ref INT NOT NULL AUTO_INCREMENT,
    date DATE NOT NULL,
    amount DECIMAL(15, 2) NOT NULL,
    reference VARCHAR(255),
    source VARCHAR(50),  -- 'Bank' or 'GL'
    PRIMARY KEY (unreconciled_ref)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

SELECT
    b.bank_ref,
    b.booking_date AS bank_date,
    b.amount AS bank_amount,
    g.gl_ref,
    g.transaction_date AS gl_date,
    g.total AS gl_amount,
    'Matched' AS reconciliation_status
FROM
    Bank b
INNER JOIN
    GL g ON b.booking_date = g.transaction_date
          AND b.amount = g.total
          AND b.reference = g.gl_je_name;  -- Adjust based on your matching criteria

SELECT
    b.bank_ref,
    b.booking_date,
    b.amount,
    b.reference,
    'Unmatched in Bank' AS reconciliation_status
FROM
    Bank b
LEFT JOIN
    GL g ON b.booking_date = g.transaction_date
          AND b.amount = g.total
          AND b.reference = g.gl_je_name
WHERE 
    g.gl_ref IS NULL;  -- GL entry not found


SELECT
    b.bank_ref,
    b.booking_date,
    b.amount,
    b.reference,
    'Unmatched in Bank' AS reconciliation_status
FROM
    Bank b
LEFT JOIN
    GL g ON b.booking_date = g.transaction_date
          AND b.amount = g.total
          AND b.reference = g.gl_je_name
WHERE 
    g.gl_ref IS NULL;  -- GL entry not found
    
SELECT 
    b.bank_ref, 
    b.booking_date, 
    b.amount, 
    b.reference
FROM 
    Bank b
LEFT JOIN 
    GL g 
ON 
    b.booking_date = g.transaction_date 
    AND b.amount = g.total 
    AND b.reference = g.gl_je_name
WHERE 
    g.gl_ref IS NULL;  -- No matching entry in the GL
    
SELECT 
    b.bank_ref, 
    b.booking_date, 
    b.amount, 
    b.reference
FROM 
    Bank b
LEFT JOIN 
    GL g 
ON 
    b.booking_date = g.transaction_date 
    AND b.amount = g.total 
    AND b.reference = g.gl_je_name
WHERE 
    g.gl_ref IS NULL;  -- No matching entry in the GL
    
SELECT 
    g.gl_ref, 
    g.transaction_date, 
    g.total, 
    g.gl_je_name
FROM 
    GL g
LEFT JOIN 
    Bank b 
ON 
    g.transaction_date = b.booking_date 
    AND g.total = b.amount 
    AND g.gl_je_name = b.reference
WHERE 
    b.bank_ref IS NULL;  -- No matching entry in the Bank
    
    
SELECT
    'MATCHED' AS reconciliation_status,
    b.bank_ref,
    b.booking_date,
    b.amount,
    g.gl_ref,
    g.transaction_date,
    g.total
FROM
    Bank b
INNER JOIN
    GL g ON b.booking_date = g.transaction_date AND b.amount = g.total AND b.reference = g.gl_je_name
UNION

SELECT
    'UNMATCHED IN BANK' AS reconciliation_status,
    b.bank_ref,
    b.booking_date,
    b.amount,
    NULL AS gl_ref,
    NULL AS transaction_date,
    NULL AS total
FROM
    Bank b
LEFT JOIN
    GL g ON b.booking_date = g.transaction_date AND b.amount = g.total AND b.reference = g.gl_je_name
WHERE
    g.gl_ref IS NULL
UNION

SELECT
    'UNMATCHED IN GL' AS reconciliation_status,
    NULL AS bank_ref,
    NULL AS booking_date,
    NULL AS amount,
    g.gl_ref,
    g.transaction_date,
    g.total
FROM
    GL g
LEFT JOIN
    Bank b ON g.transaction_date = b.booking_date AND g.total = b.amount AND g.gl_je_name = b.reference
WHERE
    b.bank_ref IS NULL;
    
SELECT
    b.bank_ref,
    b.booking_date AS bank_date,
    b.amount AS bank_amount,
    g.gl_ref,
    g.transaction_date AS gl_date,
    g.total AS gl_amount,
    'Matched' AS reconciliation_status
FROM
    Bank b
INNER JOIN
    GL g
ON 
    b.booking_date = g.transaction_date 
    AND b.amount = g.total
    AND b.reference = g.gl_je_name;  -- Adjust as per your matching criteria
    
SELECT
    b.bank_ref,
    b.booking_date,
    b.amount,
    b.reference,
    'Unmatched in Bank' AS reconciliation_status
FROM
    Bank b
LEFT JOIN
    GL g 
ON 
    b.booking_date = g.transaction_date 
    AND b.amount = g.total
    AND b.reference = g.gl_je_name
WHERE 
    g.gl_ref IS NULL;  -- No match found in GL
    
SELECT
    g.gl_ref,
    g.transaction_date,
    g.total,
    g.gl_je_name,
    'Unmatched in GL' AS reconciliation_status
FROM
    GL g
LEFT JOIN
    Bank b 
ON 
    g.transaction_date = b.booking_date 
    AND g.total = b.amount
    AND g.gl_je_name = b.reference
WHERE 
    b.bank_ref IS NULL;  -- No match found in Bank
    
    
SELECT
    u.unreconciled_ref,
    u.date,
    u.amount,
    'Still Unreconciled' AS reconciliation_status
FROM
    Unreconciled_Items u
LEFT JOIN
    Bank b ON u.date = b.booking_date AND u.amount = b.amount
LEFT JOIN
    GL g ON u.date = g.transaction_date AND u.amount = g.total
WHERE 
    b.bank_ref IS NULL 
    AND g.gl_ref IS NULL;  -- If the item is still not found in either Bank or GL
INSERT INTO Unreconciled_Items (date, amount, reference, source)
SELECT
    booking_date,
    amount,
    reference,
    'Bank' AS source
FROM
    Bank b
LEFT JOIN
    GL g ON b.booking_date = g.transaction_date AND b.amount = g.total AND b.reference = g.gl_je_name
WHERE
    g.gl_ref IS NULL;

INSERT INTO Unreconciled_Items (date, amount, reference, source)
SELECT
    transaction_date,
    total,
    gl_je_name,
    'GL' AS source
FROM
    GL g
LEFT JOIN
    Bank b ON g.transaction_date = b.booking_date AND g.total = b.amount AND g.gl_je_name = b.reference
WHERE
    b.bank_ref IS NULL;
    
SELECT
    b.bank_ref,
    b.booking_date AS bank_date,
    b.amount AS bank_amount,
    g.gl_ref,
    g.transaction_date AS gl_date,
    g.total AS gl_amount,
    'Matched' AS reconciliation_status
FROM
    Bank b
INNER JOIN
    GL g ON b.booking_date = g.transaction_date
          AND b.amount = g.total
          AND b.reference = g.gl_je_name;  -- Adjust based on your matching criteria
SELECT
    b.bank_ref,
    b.booking_date,
    b.amount,
    b.reference,
    'Unmatched in Bank' AS reconciliation_status
FROM
    Bank b
LEFT JOIN
    GL g ON b.booking_date = g.transaction_date
          AND b.amount = g.total
          AND b.reference = g.gl_je_name
WHERE 
    g.gl_ref IS NULL;  -- GL entry not found
    
SELECT
    g.gl_ref,
    g.transaction_date,
    g.total,
    g.gl_je_name,
    'Unmatched in GL' AS reconciliation_status
FROM
    GL g
LEFT JOIN
    Bank b ON g.transaction_date = b.booking_date
          AND g.total = b.amount
          AND g.gl_je_name = b.reference
WHERE 
    b.bank_ref IS NULL;  -- Bank entry not found
    
    
SELECT
    u.unreconciled_ref,
    u.date,
    u.amount,
    u.reference,
    u.source,
    'Still Unreconciled' AS reconciliation_status
FROM
    Unreconciled_Items u
LEFT JOIN
    Bank b ON u.date = b.booking_date AND u.amount = b.amount
LEFT JOIN
    GL g ON u.date = g.transaction_date AND u.amount = g.total
WHERE 
    b.bank_ref IS NULL 
    AND g.gl_ref IS NULL;  -- Still not matched in either Bank or GL
-- Insert unmatched Bank transactions into Unreconciled_Items


INSERT INTO Unreconciled_Items (date, amount, reference, source)
SELECT
    b.booking_date,
    b.amount,
    b.reference,
    'Bank' AS source
FROM
    Bank b
LEFT JOIN
    GL g ON b.booking_date = g.transaction_date AND b.amount = g.total AND b.reference = g.gl_je_name
WHERE
    g.gl_ref IS NULL;

-- Insert unmatched GL transactions into Unreconciled_Items



INSERT INTO Unreconciled_Items (date, amount, reference, source)
SELECT
    g.transaction_date,
    g.total,
    g.gl_je_name,
    'GL' AS source
FROM
    GL g
LEFT JOIN
    Bank b ON g.transaction_date = b.booking_date AND g.total = b.amount AND g.gl_je_name = b.reference
WHERE
    b.bank_ref IS NULL;
    
    
    
SELECT
    b.bank_ref,
    b.booking_date AS transaction_date,
    b.amount,
    'Matched' AS reconciliation_status
FROM
    Bank b
INNER JOIN
    GL g ON b.booking_date = g.transaction_date AND b.amount = g.total AND b.reference = g.gl_je_name
UNION
SELECT
    b.bank_ref,
    b.booking_date AS transaction_date,
    b.amount,
    'Unmatched in Bank' AS reconciliation_status
FROM
    Bank b
LEFT JOIN
    GL g ON b.booking_date = g.transaction_date AND b.amount = g.total AND b.reference = g.gl_je_name
WHERE
    g.gl_ref IS NULL
UNION
SELECT
    g.gl_ref,
    g.transaction_date,
    g.total,
    'Unmatched in GL' AS reconciliation_status
FROM
    GL g
LEFT JOIN
    Bank b ON g.transaction_date = b.booking_date AND g.total = b.amount AND g.gl_je_name = b.reference
WHERE
    b.bank_ref IS NULL;






