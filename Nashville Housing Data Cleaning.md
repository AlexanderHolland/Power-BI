Nashville Housing-Data Cleaning
====================

![house 2](https://github.com/user-attachments/assets/4e89ea29-8d02-4d45-8f73-8af77f17482f)

### SQL Script Link:

[View SQL Script](https://github.com/YourUsername/YourRepository/blob/main/DataCleaning.sql)

This project focuses on cleaning and standardizing data in a SQL database. The following steps outline the process used to clean and prepare the data for analysis.

Objective
---------

To clean and standardize data in the NashvilleHousing database, including formatting dates, handling missing values, breaking out addresses into separate columns, and removing duplicates.

Steps Followed:
---------------

### 1\. Standardize Date Format:

    -- View existing SaleDate format
    SELECT SaleDate
    FROM dbo.NashvilleHousing
    
    -- Convert SaleDate to a standard date format
    SELECT CONVERT(DATE, SaleDate)
    FROM dbo.NashvilleHousing
    
    -- Update SaleDate column to standardized date format
    UPDATE dbo.NashvilleHousing
    SET SaleDate = CONVERT(DATE, SaleDate)
    
    -- Add a new column for standardized SaleDate
    ALTER TABLE dbo.NashvilleHousing
    ADD SaleDateConverted DATE
    
    -- Populate the new SaleDateConverted column
    UPDATE dbo.NashvilleHousing
    SET SaleDateConverted = CONVERT(DATE, SaleDate)
    
    -- Verify the changes
    SELECT SaleDate, SaleDateConverted
    FROM dbo.NashvilleHousing
    

### 2\. Populate Property Address Data:

    -- Find rows with missing PropertyAddress
    SELECT *
    FROM NashvilleHousing
    WHERE PropertyAddress IS NULL
    ORDER BY ParcelID
    
    -- Join table to fill missing PropertyAddress values
    SELECT a.ParcelID, a.PropertyAddress, b.ParcelID, b.PropertyAddress, ISNULL(a.PropertyAddress, b.PropertyAddress)
    FROM NashvilleHousing a
    INNER JOIN NashvilleHousing b
    ON a.ParcelID = b.ParcelID
    WHERE a.UniqueID <> b.UniqueID
    AND a.PropertyAddress IS NULL
    
    -- Update missing PropertyAddress values
    UPDATE a
    SET PropertyAddress = ISNULL(a.PropertyAddress, b.PropertyAddress)
    FROM NashvilleHousing a
    INNER JOIN NashvilleHousing b
    ON a.ParcelID = b.ParcelID
    WHERE a.UniqueID <> b.UniqueID
    AND a.PropertyAddress IS NULL
    

### 3\. Break Out Address into Individual Columns (Address, City, State):

    -- Extract Address component
    SELECT SUBSTRING(PropertyAddress, 1, CHARINDEX(',', PropertyAddress)-1) AS Address,
    SUBSTRING(PropertyAddress, CHARINDEX(',', PropertyAddress) + 1, LEN(PropertyAddress)) AS Address
    FROM NashvilleHousing
    
    -- Add new columns for Address, City, State
    ALTER TABLE NashvilleHousing
    ADD PropertySplitAddress NVARCHAR(255)
    
    -- Update new Address column
    UPDATE NashvilleHousing
    SET PropertySplitAddress = SUBSTRING(PropertyAddress, 1, CHARINDEX(',', PropertyAddress)-1)
    
    ALTER TABLE NashvilleHousing 
    ADD PropertySplitCity NVARCHAR(255)
    
    -- Update new City column
    UPDATE NashvilleHousing
    SET PropertySplitCity = SUBSTRING(PropertyAddress, CHARINDEX(',', PropertyAddress) + 1, LEN(PropertyAddress))
    
    -- Handle OwnerAddress similarly
    SELECT OwnerAddress
    FROM NashvilleHousing
    
    -- Split OwnerAddress into Address, City, State
    SELECT 
    PARSENAME(REPLACE(OwnerAddress,',','.'), 3) AS OwnerSplitAddress,
    PARSENAME(REPLACE(OwnerAddress,',','.'), 2) AS OwnerSplitCity,
    PARSENAME(REPLACE(OwnerAddress, ',','.'), 1) AS OwnerSplitState
    FROM NashvilleHousing
    
    -- Add and populate new OwnerAddress columns
    ALTER TABLE NashvilleHousing
    ADD OwnerSplitAddress NVARCHAR(255)
    
    UPDATE NashvilleHousing
    SET OwnerSplitAddress = PARSENAME(REPLACE(OwnerAddress,',','.'), 3)
    
    ALTER TABLE NashvilleHousing
    ADD OwnerSplitCity NVARCHAR(255)
    
    UPDATE NashvilleHousing
    SET OwnerSplitCity = PARSENAME(REPLACE(OwnerAddress,',','.'), 2)
    
    ALTER TABLE NashvilleHousing 
    ADD OwnerSplitState NVARCHAR(255)
    
    UPDATE NashvilleHousing
    SET OwnerSplitState = PARSENAME(REPLACE(OwnerAddress, ',','.'), 1)
    

### 4\. Change Y and N to Yes and No in "Sold as Vacant" Field:

    -- Check current values
    SELECT DISTINCT(SoldAsVacant), COUNT(SoldAsVacant)
    FROM NashvilleHousing
    GROUP BY SoldAsVacant
    
    -- Convert Y/N to Yes/No
    UPDATE NashvilleHousing
    SET SoldAsVacant = CASE 
    	WHEN SoldAsVacant = 0 THEN 'No'
    	WHEN SoldAsVacant = 1 THEN 'Yes'
    	END
    

### 5\. Remove Duplicates:

    -- Use CTE to identify duplicates
    WITH RowNumCTE AS(
    SELECT n.*,
    	ROW_NUMBER() OVER (
    	PARTITION BY ParcelID, 
    		PropertyAddress, 
    		SalePrice, 
    		SaleDate, 
    		LegalReference 
    		ORDER BY 
    		UniqueID) Row_Num
    FROM NashvilleHousing n
    )
    -- Delete duplicate rows
    DELETE
    FROM RowNumCTE
    WHERE Row_Num > 1
    

### 6\. Delete Unused Columns:

    -- Drop unused columns
    ALTER TABLE NashvilleHousing
    DROP COLUMN OwnerAddress, TaxDistrict, PropertyAddress, SaleDate
