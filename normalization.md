# Database Normalization Analysis - AirBnB Database

## Overview
This document analyzes the current database schema and applies normalization principles to ensure the database is in Third Normal Form (3NF).

## Current Schema Analysis

### 1. First Normal Form (1NF) Review
**Definition**: A table is in 1NF if all attributes contain atomic values and there are no repeating groups.

**Current Status**: ✅ **COMPLIANT**
- All attributes in our tables contain atomic values
- No repeating groups or multi-valued attributes
- Each cell contains a single value

### 2. Second Normal Form (2NF) Review
**Definition**: A table is in 2NF if it's in 1NF and all non-key attributes are fully functionally dependent on the primary key.

**Current Status**: ✅ **COMPLIANT**
- All tables use UUID primary keys
- No composite primary keys exist, so partial dependencies are not possible
- All non-key attributes depend on the entire primary key

### 3. Third Normal Form (3NF) Analysis
**Definition**: A table is in 3NF if it's in 2NF and has no transitive dependencies (non-key attributes don't depend on other non-key attributes).

**Issues Identified**:

#### 3.1 Property Table - Location Redundancy
**Issue**: The `location` field in the Property table may contain redundant location information.
- Properties in the same city/region will have repeated location strings
- This violates 3NF as location details could depend on a location identifier

**Solution**: Create a separate Location table to normalize location data.

#### 3.2 Booking Table - Potential Price Calculation Issue
**Issue**: `total_price` in Booking table might be calculated from other attributes.
- Could be derived from `pricepernight` (Property) × number of nights
- This creates a potential transitive dependency

**Analysis**: This is acceptable as `total_price` serves as a historical record and may include additional fees not captured in the base price.

## Normalized Schema Design

### New Location Table
```sql
Location
- location_id: Primary Key, UUID, Indexed
- city: VARCHAR, NOT NULL
- state_province: VARCHAR, NOT NULL
- country: VARCHAR, NOT NULL
- postal_code: VARCHAR, NULL
- latitude: DECIMAL(10,8), NULL
- longitude: DECIMAL(11,8), NULL
- created_at: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP
```

### Updated Property Table
```sql
Property (Updated)
- property_id: Primary Key, UUID, Indexed
- host_id: Foreign Key, references User(user_id)
- location_id: Foreign Key, references Location(location_id)
- name: VARCHAR, NOT NULL
- description: TEXT, NOT NULL
- pricepernight: DECIMAL, NOT NULL
- created_at: TIMESTAMP, DEFAULT CURRENT_TIMESTAMP
- updated_at: TIMESTAMP, ON UPDATE CURRENT_TIMESTAMP
```

## Normalization Steps Applied

### Step 1: Identify Transitive Dependencies
- Analyzed each table for non-key attributes depending on other non-key attributes
- Found location information in Property table could be normalized

### Step 2: Extract Location Information
- Created a separate Location table to store unique location data
- This eliminates redundancy for properties in the same location
- Provides better data integrity and consistency

### Step 3: Update Foreign Key Relationships
- Modified Property table to reference Location table
- Maintains referential integrity through foreign key constraints

### Step 4: Verify 3NF Compliance
After normalization:
- ✅ All tables are in 1NF (atomic values, no repeating groups)
- ✅ All tables are in 2NF (no partial dependencies)
- ✅ All tables are in 3NF (no transitive dependencies)

## Benefits of Normalization

### 1. Reduced Data Redundancy
- Location information is stored once per unique location
- Eliminates duplicate location strings across properties

### 2. Improved Data Integrity
- Location updates only need to be made in one place
- Consistent location formatting and validation

### 3. Better Query Performance
- Can efficiently query properties by location attributes
- Enables location-based indexing and searching

### 4. Enhanced Scalability
- Easy to add new location attributes without affecting existing data
- Supports future location-based features (geocoding, mapping, etc.)

## Updated Constraints and Indexing

### Location Table Constraints
- Non-null constraints on city, state_province, country
- Unique constraint on combination of city, state_province, country, postal_code
- Foreign key indexes for efficient joins

### Updated Property Table Constraints
- Foreign key constraint on location_id
- Index on location_id for efficient location-based queries

## Conclusion

The normalized schema achieves Third Normal Form (3NF) by:
1. Eliminating transitive dependencies through location normalization
2. Maintaining data integrity and consistency
3. Reducing storage redundancy
4. Improving query performance for location-based operations

The remaining tables (User, Booking, Payment, Review, Message) were already compliant with 3NF and require no changes.
