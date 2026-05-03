---
name: database-architecture
description: This skill helps agents understand and work with the Supabase/PostgreSQL database structure for the SIMMER mosquito control management system. Use when this capability is needed.
metadata:
  author: thebigthing313
---

## Overview
This skill helps agents understand and work with the Supabase/PostgreSQL database structure for the SIMMER mosquito control management system. The database is a comprehensive, multi-tenant integrated mosquito surveillance and control management platform that handles:
- Adult mosquito surveillance (traps and collections)
- Larval surveillance (inspections and habitats)
- Insecticide applications (truck, handheld, aerial)
- Source reduction activities
- Public service requests and notifications
- Geographic information systems (GIS) with PostGIS
- Multi-tenant organization management with role-based access control

The database uses a modular schema organization with consistent patterns for audit trails, soft deletes, row-level security (RLS), and spatial data.

## Database Technology Stack
- **Database**: PostgreSQL (via Supabase)
- **Extensions**: PostGIS for spatial data (`extensions.geometry`)
- **Authentication**: Supabase Auth (auth schema with JWT)
- **Schemas**: Custom `simmer` schema (infrastructure) + `public` schema (application data)
- **Security**: Row Level Security (RLS) enabled on all tables
- **Data Types**: Custom ENUMs for constrained values (e.g., mosquito_sex, insecticide_type)

## Custom PostgreSQL Types (ENUMs)

The database uses several custom ENUM types for data consistency:

**General:**
- `unit_type`: 'weight', 'distance', 'area', 'volume', 'temperature', 'duration', 'count', 'speed'
- `unit_system`: 'si', 'imperial', 'us_customary'

**Adult Surveillance:**
- `mosquito_sex`: 'male', 'female'
- `mosquito_status`: 'damaged', 'unfed', 'bloodfed', 'gravid'

**Public Outreach:**
- `request_intake_type`: 'online', 'phone', 'walk-in', 'other'

**Insecticide Control:**
- `insecticide_type`: 'larvicide', 'adulticide', 'pupicide', 'other'

**Larval Surveillance:**
- `aerial_result_type`: 'recheck', 'fly', 'hand treat', 'no action'

## Schema Organization

All database schemas are located in `/supabase/schemas/` and organized by domain:

### Core Infrastructure (000 - simmer/)
- **000 - extensions.sql**: PostGIS and other extensions
- **100 - schemas.sql**: Schema definitions (simmer, public)
- **200 - soft-delete.sql**: Soft delete pattern with `simmer.deleted_data` table
- **300 - set-audit-fields.sql**: Automatic audit field management trigger

### Authentication & Authorization (100 - auth/)
The auth system is built on Supabase Auth with custom extensions:

- **000 - rls-helper-functions.sql**: RLS helper functions
  - `user_is_group_member(p_group_id)`: Check if current user belongs to group
  - `user_has_group_role(p_group_id, p_role_id)`: Check if user has sufficient role level
  - `user_owns_record(p_user_id)`: Check if record was created by current user
  
- **100 - roles.sql**: Role definitions (read-only reference table)
  - Defines role hierarchy by integer ID (lower = more permissions)
  - Common roles: 1=Super Admin, 2=Admin, 3=Manager, 4=Technician/Collector, 5=Viewer
  
- **200 - groups.sql**: Organization/group structure
  - Multi-tenant isolation: each organization has a group
  - Contains contact info, settings (JSONB), and audit fields
  - Read: group members | Update: group owners (role 1) only
  
- **300 - profiles.sql**: User profiles (1:1 with auth.users)
  - Links Supabase auth users to groups and roles
  - **Critical trigger**: `ids_to_app_metadata` syncs profile_id, group_id, role_id to JWT
  - **Security trigger**: `prevent_user_id_change` prevents changing user_id after creation
  - **Security trigger**: `prevent_self_role_escalation` prevents users from changing their own role
  
- **400 - prevent_role_escalation.sql**: Additional security to prevent self-privilege escalation

### General Domain (200 - general/)
Reference and configuration data shared across operations:

- **010 - units.sql**: Measurement units with conversion system
  - Unit types (weight, distance, area, volume, temperature, duration, count, speed)
  - Unit systems (SI, imperial, US customary)
  - Base units and conversion factors for unit conversion
  - Read-only: Public access for reference
  
- **020 - genera.sql** & **021 - species.sql**: Mosquito taxonomy
  - Genera: Genus names with abbreviations (e.g., "Aedes" → "Ae")
  - Species: Linked to genera, scientific names
  - Read-only: Public access for identification
  
- **030 - tag_groups.sql** & **031 - tags.sql**: Flexible tagging system
  - Tag groups: Categories for organizing tags (e.g., "Priority", "Status")
  - Tags: User-defined labels with colors, scoped to group
  - Constraint: Unique tag names within group + tag_group combination
  - Permissions: Admin (role 2) to manage tags
  
- **040 - contacts.sql**: Contact information management
  - Multiple phone/email/fax fields with validation
  - Check constraint: At least one contact method required
  - Organization and department tracking
  - Flexible metadata (JSONB)
  
- **060 - vehicles.sql**: Vehicle/equipment tracking
  - Unique vehicle names per group
  - Used for truck applications and aerial flights
  
- **070 - equipment.sql**: Equipment inventory
  - Serial number tracking
  - Unique constraint on name + serial number combination
  
- **080 - application_methods.sql**: Application technique reference
  - User-defined application methods (e.g., "ULV", "Thermal Fog")

### GIS & Spatial Data (300 - gis/)
PostGIS-based spatial features and geographic management:

- **000 - spatial_features.sql**: Core spatial feature table
  - Geometry storage: Point, Line, or Polygon in SRID 4326 (WGS84)
  - **Generated columns** (never insert these):
    - `lat`: Latitude from centroid
    - `lng`: Longitude from centroid
    - `geojson`: GeoJSON representation
  - Unique constraint: MD5 hash of geometry (prevents exact duplicates)
  - GIST spatial index for efficient spatial queries
  - Shared by many tables (traps, habitats, regions, etc.)
  
- **100 - addresses.sql**: Structured address management
  - `display_name`: Human-readable address
  - `address_fields`: JSONB for flexible address components
  - Links to spatial_features for geolocation
  - Permissions: Collectors can create, edit own; Managers edit all
  
- **105 - address_tags.sql**: Tag addresses with user-defined tags
  
- **200 - region_folders.sql**: Hierarchical region organization
  - Folders for grouping regions (e.g., "North District", "Commercial Areas")
  
- **210 - regions.sql**: Geographic regions/territories
  - Hierarchical: Can have parent regions (`parent_id`)
  - Self-referential constraint: id ≠ parent_id
  - `name_path`: Breadcrumb trail for hierarchical display
  - Links to spatial_features for boundary polygons
  - Optional region_folder for organization
  
- **300 - routes.sql**: Route planning and management
  - Named routes for organizing field work
  - Links to route_items (see 999 - general)
  
- **999 - get_or_create_spatial_features.sql**: Helper function
  - `get_or_create_spatial_feature(p_lat, p_lng, p_geojson)`: Returns feature UUID
  - Automatically deduplicates based on geometry hash
  - Snaps coordinates to 6 decimal places
  - Handles both lat/lng pairs and GeoJSON input
  - Forces 2D geometries (no Z/M dimensions)

### Adult Surveillance (400 - adult surveillance/)
Mosquito trap monitoring and adult mosquito collections:

- **100 - trap_types.sql**: Type definitions for traps
  - Group-specific trap types (e.g., "CDC Light Trap", "BG-Sentinel")
  - Optional shorthand for abbreviations
  - Admin-managed (role 2)
  
- **110 - trap_lures.sql**: Lure/attractant options
  - CO2, octenol, light, etc.
  - Manager-level permissions (role 3)
  
- **120 - traps.sql**: Physical trap locations
  - Links to: trap_type, spatial_features, addresses, groups
  - `is_active`: Trap currently in use
  - `is_permanent`: Permanent installation vs temporary
  - Optional trap_name and trap_code for identification
  - Manager-level permissions (role 3) for CRUD
  
- **121 - trap_tags.sql**: Tag traps with custom tags
  - Junction table: traps ↔ tags
  - Collector-level permissions (role 4)
  
- **200 - collections.sql**: Trap collection events
  - Records trap visits and specimen retrieval
  - `trap_nights`: Number of nights trap was active
  - Tracks who set the trap (`trap_set_by`, `trap_set_date`)
  - Tracks who collected (`collected_by`, `collection_date`)
  - `has_error`: Flag for problematic collections
  - Optional lure tracking
  - Collector-level permissions (role 4)
  
- **210 - landing_rates.sql**: Human landing rate observations
  - Direct observation of mosquitoes landing on observers
  - Tracks duration, temperature, wind speed with units
  - Observed count tracking
  - Time-stamped observations with start/stop times
  - Check constraints for valid time ranges and unit requirements
  - Links to spatial_features, addresses, profiles
  
- **300 - collection_results.sql**: Mosquito identification results
  - Links to collections OR landing_rates (check constraint: one or the other)
  - Species identification (links to species table)
  - Count, sex (male/female), status (damaged/unfed/bloodfed/gravid)
  - `identified_by`: Which profile performed identification
  - Check constraint: mosquito_count >= 0

### Larval Surveillance (500 - larval surveillance/)
Larval mosquito monitoring and breeding site management:

- **000 - densities.sql**: Larval density classifications
  - Named density ranges (e.g., "Low": 1-5, "Medium": 6-20)
  - `range_min` and `range_max` with validation check
  - Manager-level management (role 3)
  
- **100 - habitats.sql**: Breeding site locations
  - Permanent or temporary larval habitat sites
  - `is_active`: Currently monitored
  - `is_inaccessible`: Physical access restrictions
  - Optional habitat_name for permanent sites
  - Required description of habitat
  - Links to spatial_features and optional address
  - Collector permissions (role 4) for CRUD
  
- **150 - habitat_tags.sql**: Tag habitats with custom tags
  
- **200 - inspections.sql**: Larval habitat inspection records
  - Links to habitats (optional - can be ad-hoc inspection)
  - `is_wet`: Was water present?
  - If wet, requires larval data:
    - `larvae_count` and `dip_count` (for calculations)
    - OR `density_id` (reference to predefined density)
    - OR both null (no larvae found)
  - Instar stage tracking (1st, 2nd, 3rd, 4th instar, pupae, eggs) as booleans
  - `is_source_reduction`: Was source reduction performed?
    - If true, requires `source_reduction_notes`
  - Complex check constraints validate data completeness
  - Collector-level permissions (role 4)
  
- **210 - inspection_tags.sql**: Tag inspections
  
- **300 - aerial_sites.sql**: Aerial surveillance sites
  - Large-scale sites monitored by aerial survey
  - Active/inactive tracking
  - Admin-managed (role 2)
  
- **310 - aerial_inspections.sql**: Aerial site inspection records
  - Similar to ground inspections but for aerial sites
  - Result type enum: 'recheck', 'fly', 'hand treat', 'no action'
  - Larval density tracking (dips, larvae count, larvae per dip)
  - Check constraint: If wet, requires larval data
  
- **400 - samples.sql**: Laboratory samples from inspections
  - Links to inspection record
  - Optional display name for sample tracking
  - Allows detailed species identification in lab
  
- **410 - sample_results.sql**: Laboratory identification results
  - Links to samples and species
  - Tracks who identified and when
  - Larvae count per species
  - Unique constraint: One result per sample+species combination

### Public Outreach (600 - public outreach/)
Citizen interaction and notification management:

- **100 - service_requests.sql**: Public service requests
  - `display_name`: Integer sequence number for public display
  - `intake_type`: How request was received (online, phone, walk-in, other)
  - Links to: address, spatial_features, contact
  - `details`: Description of the request/complaint
  - `is_closed`: Request resolution status
  - Manager-level permissions (role 3)
  
- **110 - service_request_tags.sql**: Tag service requests
  
- **200 - notifications.sql**: Application notification registry
  - Citizens requesting notification before spray operations
  - `has_bees`: Indicates beekeepers
  - `is_no_spray`: Requests no spraying at location
  - `is_active`: Currently active notification request
  - Optional radius and unit for notification area
  - Contact preferences: email, fax, phone (at least one required)
  - Optional region_id for geographic filtering

### Insecticide Control (700 - insecticide control/)
Chemical control operations and tracking:

- **050 - insecticides.sql**: Insecticide product catalog
  - `trade_name`: Commercial product name
  - `active_ingredient`: Chemical active ingredient
  - `type`: larvicide, adulticide, pupicide, other
  - `registration_number`: EPA registration
  - `conversion_factor`: For unit conversions
  - `default_unit_id`: Standard unit for this product
  - Optional: `label_url`, `msds_url`, `shorthand`
  - Admin-managed (role 2)
  
- **051 - insecticide_batches.sql**: Batch/lot tracking
  - Links to insecticide product
  - Batch name/number for inventory tracking
  - `is_active`: Currently in use
  - Manager-level permissions (role 3)
  
- **100 - truck_applications.sql**: Vehicle-mounted spray applications
  - Spray operations from vehicle (ULV trucks, etc.)
  - Tracks start/end: time, odometer, temperature, wind speed
  - Links to vehicle, temperature/wind speed units
  - Optional feature_id for spray area geometry
  - Area description text field
  - Check constraints validate unit requirements
  - Collector-level permissions (role 4)
  
- **200 - handheld_applications.sql**: Handheld spray applications
  - Backpack sprayers, foggers, etc.
  - Similar to truck applications but without odometer
  - Links to address or spatial feature
  - Time and environmental condition tracking
  
- **300 - catch_basin_missions.sql**: Catch basin treatment programs
  - Specialized larvae control in storm drains
  - `basin_count`: Number of basins treated
  - Optional `sample_dry` and `sample_wet`: Sample counts
  - Check constraint: basin_count > 0
  
- **400 - scheduled_missions.sql**: Planned/scheduled application events
  
- **600 - flights.sql**: Aerial spray flights
  - Links to aircraft (vehicle) and pilot (profile)
  - Flight date tracking
  - Manager-level permissions (role 3)
  
- **610 - flights_aerial_sites.sql**: Junction table linking flights to aerial sites
  
- **620 - flight_batches.sql**: Insecticide batches used in flights
  - Junction table: flights ↔ insecticide_batches
  - Tracks which products/lots used in which flights
  
- **999 - applications.sql**: **Unified application records** (important!)
  - **Polymorphic parent table** for all application types
  - Links to ONE of: inspection, flight_aerial_site, catch_basin_mission, handheld_application, truck_application
  - Check constraint enforces exactly one source
  - Tracks: applicator, date, insecticide, batch, method, amount, units
  - Optional equipment tracking
  - This table provides a unified view across all application types
  - Used by `additional_personnel` table for crew tracking

### Source Reduction (800 - source reduction/)
Non-chemical control activities:

- **100 - source_reduction_types.sql**: Types of source reduction
  - User-defined types (e.g., "Tire Removal", "Container Elimination")
  - Admin-managed (role 2)
  
- **200 - source_reductions.sql**: Source reduction activity records
  - Links to source_reduction_type, technician, spatial_features
  - `sources_eliminated_count`: Number of sources removed
  - Date tracking
  - Optional notes and metadata
  - Collector-level permissions (role 4)

### General Supporting Tables (999 - general/)
Cross-cutting functionality:

- **100 - comments.sql**: **Polymorphic commenting system**
  - Comments can attach to: traps, collections, landing_rates, service_requests, contacts, aerial_sites, samples, notifications
  - Supports threaded comments via `parent_id` (comment replies)
  - `is_pinned`: Highlight important comments
  - `parent_type`: Generated column indicating what the comment is attached to
  - Permissions: All group members can comment; managers or creators can edit/delete
  
- **200 - additional_personnel.sql**: **Polymorphic crew tracking**
  - Links to ONE of: inspection, aerial_inspection, flight, application, source_reduction
  - Check constraint enforces exactly one parent reference
  - Tracks additional team members beyond primary technician
  - Used for multi-person field operations
  
- **310 - route_items.sql**: Items on a route
  - Links routes to habitats (or other entities)
  - `rank_string`: Text-based ordering (lexicographic sort using "C" collation)
  - `directions_to_next_item`: Turn-by-turn directions
  - Unique constraints: One rank per route, one habitat per route

## Standard Table Patterns

### 1. Audit Fields Pattern
Most tables include these standard audit fields:
```sql
created_at timestamptz not null default now(),
created_by uuid references public.profiles (user_id) on delete set null,
updated_at timestamptz not null default now(),
updated_by uuid references public.profiles (user_id) on delete set null
```

These are automatically managed by the `set_audit_fields()` trigger:
```sql
create trigger set_audit_fields
before insert or update on public.table_name
for each row
execute function public.set_audit_fields();
```

**Important notes:**
- Uses `auth.uid()` internally to get current user
- On INSERT: Sets all four fields
- On UPDATE: Only updates `updated_at` and `updated_by`
- Foreign key references `profiles(user_id)` NOT `profiles(id)`
- `on delete set null` preserves audit trail even if user deleted

### 2. Soft Delete Pattern
Instead of permanently deleting records, data is moved to `simmer.deleted_data`:
```sql
create trigger soft_delete_trigger
before delete on public.table_name
for each row
execute function simmer.soft_delete();
```

The deleted data is stored as:
- `original_table`: Source table name (text)
- `original_id`: Original record UUID
- `deleted_by`: User who deleted it (uuid)
- `deleted_at`: Deletion timestamp (timestamptz)
- `data`: Full record as JSONB (entire row preserved)

**Recovery process:**
1. Query `simmer.deleted_data` to find the record
2. Parse the JSONB to extract values
3. Re-insert into the original table if needed

### 3. Multi-Tenant Group Pattern
Most operational tables include:
```sql
group_id uuid not null references public.groups(id) on delete restrict
```

This enables multi-tenant isolation where organizations/groups only see their own data.

**Key characteristics:**
- `NOT NULL`: Every operational record belongs to a group
- `on delete restrict`: Cannot delete a group with data 
- Used in RLS policies for data isolation
- Propagated through relationships

**Exceptions (no group_id):**
- Reference tables (genera, species, units, roles)
- Infrastructure tables (spatial_features)
- Auth tables (users from Supabase Auth)

### 4. Row Level Security (RLS) Pattern
All tables have RLS enabled with standard policies based on role hierarchy.

#### Role Hierarchy (lower number = more permissions):
- **1 - Super Admin / Owner**: Full control of group settings
- **2 - Admin**: Manage reference data, vehicles, equipment
- **3 - Manager**: Manage operations, approve records, assign work
- **4 - Technician/Collector**: Create and edit own field data
- **5 - Viewer**: Read-only access to group data

#### Standard Policy Patterns:

**SELECT**: Based on group membership
```sql
create policy "select: own groups or group_id is null"
on public.table_name for select to authenticated
using (public.user_is_group_member(group_id));
```

**INSERT**: Based on role level
```sql
-- Most tables: Collectors (role 4) can insert
create policy "insert: own group collector"
on public.table_name for insert to authenticated
with check (public.user_has_group_role(group_id, 4));

-- Configuration tables: Admins (role 2) only
create policy "insert: own group admin"
on public.table_name for insert to authenticated
with check (public.user_has_group_role(group_id, 2));
```

**UPDATE**: Often hybrid - own records OR manager
```sql
-- Collectors can edit their own, managers can edit all
create policy "update: own if collector, all if manager"
on public.addresses for update to authenticated
using (
    (public.user_has_group_role(group_id, 4) AND created_by = auth.uid())
    OR public.user_has_group_role(group_id, 3)
)
with check (
    (public.user_has_group_role(group_id, 4) AND created_by = auth.uid())
    OR public.user_has_group_role(group_id, 3)
);
```

**DELETE**: Usually managers or record creators
```sql
create policy "delete: own group manager or own records"
on public.table_name for delete to authenticated
using (
    public.user_has_group_role(group_id, 3) 
    OR user_owns_record(created_by)
);
```

#### Reference Data RLS (read-only):
```sql
create policy "select: anyone" on public.genera for select to public using (true);
create policy "insert: none" on public.genera for insert to public with check (false);
create policy "update: none" on public.genera for update to public using (false);
create policy "delete: none" on public.genera for delete to public using (false);
```

### 5. Spatial Features Pattern
Tables with geographic data reference `public.spatial_features`:
```sql
feature_id uuid not null references public.spatial_features(id) on delete restrict
```

The `spatial_features` table stores:
- `geom`: PostGIS geometry (point, line, or polygon in SRID 4326)
- `lat`, `lng`: **Generated** centroid coordinates (DO NOT INSERT)
- `geojson`: **Generated** GeoJSON representation (DO NOT INSERT)
- `created_at`: Creation timestamp

**Important:**
- All geometries in SRID 4326 (WGS84 - standard lat/lng)
- Snapped to 6 decimal places (~0.1m precision)
- Forced to 2D (no Z or M dimensions)
- Unique constraint on MD5 hash of geometry bytes
- Use `get_or_create_spatial_feature()` function to avoid duplicates

### 6. Tagging Pattern
Many entities support flexible tagging through junction tables:
- `trap_tags` (traps)
- `address_tags` (addresses)
- `habitat_tags` (habitats)
- `inspection_tags` (inspections)
- `service_request_tags` (service requests)

**Junction table structure:**
```sql
create table public.entity_tags (
    id uuid primary key default gen_random_uuid(),
    group_id uuid not null references public.groups(id) on delete restrict,
    entity_id uuid not null references public.entities(id) on delete cascade,
    tag_id uuid not null references public.tags(id) on delete cascade,
    created_at timestamptz not null default now(),
    created_by uuid references public.profiles (user_id) on delete set null,
    updated_at timestamptz not null default now(),
    updated_by uuid references public.profiles (user_id) on delete set null,
    constraint unique_entity_tag unique (entity_id, tag_id)
);
```

**Key features:**
- `on delete cascade` on both entity and tag sides (cleanup on delete)
- Unique constraint prevents duplicate tags on same entity
- Includes full audit trail

### 7. Polymorphic Relationships Pattern
Some tables link to multiple possible parent tables.

**Pattern:** Multiple optional foreign keys with check constraint enforcing exactly one:

**Example - Comments:**
```sql
trap_id uuid references public.traps(id) on delete restrict,
collection_id uuid references public.collections(id) on delete restrict,
landing_rate_id uuid references public.landing_rates(id) on delete restrict,
-- ... more potential parents

constraint one_parent check (
    (trap_id is not null)::int +
    (collection_id is not null)::int +
    (landing_rate_id is not null)::int
    -- ... 
    = 1
)
```

**Tables using this pattern:**
- `comments`: Can attach to 8+ different entity types
- `additional_personnel`: Can track crew for 5 different activity types
- `applications`: Unified record for 5 different application source types
- `collection_results`: Can come from collections OR landing_rates

**Generated type column:**
```sql
parent_type text generated always as (
    case
        when trap_id is not null then 'trap'::text
        when collection_id is not null then 'collection'::text
        -- ...
        else 'unknown'::text
    end
) stored;
```

### 8. Metadata Pattern
Many tables include:
```sql
metadata jsonb
```
For flexible, schema-less additional data storage without migrations.

**Common uses:**
- Custom fields specific to one organization
- Integration data (external system IDs)
- Feature flags
- Temporary data during migrations

### 9. Check Constraints Pattern
Database enforces business rules through check constraints:

**Unit pairing** (if unit specified, value required):
```sql
constraint temperature_unit_check check (
    (temperature_unit_id is null AND temperature is null) 
    OR 
    (temperature_unit_id is not null AND temperature is not null)
)
```

**Time range validation:**
```sql
constraint valid_time_range check (
    start_time is null OR end_time is null OR end_time > start_time
)
```

**Count validation:**
```sql
constraint count_non_negative check (mosquito_count >= 0)
```

**Required text when boolean true:**
```sql
constraint source_reduction_notes_required check (
    is_source_reduction = false 
    OR 
    (source_reduction_notes is not null AND source_reduction_notes <> '')
)
```

**At least one required:**
```sql
constraint at_least_one_contact check (
    num_nonnulls(
        nullif(preferred_phone, ''), 
        nullif(email, ''), 
        nullif(fax, '')
    ) > 0
)
```

### 10. Unique Constraints Pattern
Preventing duplicate data at database level:

**Group-scoped uniqueness:**
```sql
constraint unique_vehicle_name unique (group_id, vehicle_name)
```

**Composite uniqueness:**
```sql
constraint unique_equipment_name_serial_number 
unique (group_id, equipment_name, serial_number)
```

**Junction table uniqueness:**
```sql
constraint unique_address_tag unique (address_id, tag_id)
```

### 11. JWT Metadata Sync Pattern
The `profiles` table syncs key data to JWT tokens for efficient authorization:

**Trigger function:** `simmer.ids_to_app_metadata()`
- Fires on INSERT, UPDATE, DELETE of profiles
- Updates `auth.users.raw_app_meta_data` with:
  - `profile_id`: UUID of profile record
  - `group_id`: User's organization
  - `role_id`: Integer role level
- Data becomes available in `auth.jwt()` for RLS policies
- Enables authorization without table joins

**Access in RLS:**
```sql
(auth.jwt() -> 'app_metadata' ->> 'group_id')::uuid
(auth.jwt() -> 'app_metadata' ->> 'role_id')::integer
```

## Common Field Conventions

- **Primary Keys**: Always `id uuid primary key default gen_random_uuid()`
  - UUIDs prevent ID collision in distributed systems
  - Auto-generated, never manually set
  
- **Foreign Keys**: Named `{table}_id` or `{description}_id`
  - Examples: `trap_type_id`, `collected_by`, `applicator_id`
  - Profile references: `{role}_by` (e.g., `collected_by`, `identified_by`)
  - Always include `on delete` and `on update` clauses
  
- **Timestamps**: Use `timestamptz` (timezone-aware)
  - Standard audit: `created_at`, `updated_at`
  - Activity dates: `{action}_date` (e.g., `collection_date`, `inspection_date`)
  - Time fields: `time` type (e.g., `start_time`, `end_time`)
  
- **Boolean Flags**: Descriptive prefixes
  - `is_*`: State flags (`is_active`, `is_wet`, `is_permanent`)
  - `has_*`: Presence indicators (`has_error`, `has_bees`, `has_pupae`)
  - `wants_*`: Preference indicators (`wants_email`, `wants_phone`)
  
- **Names/Codes**: Most entities have both
  - `{entity}_name`: Human-readable name (e.g., `trap_name`, `region_name`)
  - `{entity}_code`: Machine-readable code/identifier
  - `shorthand`: Abbreviations for UI display
  - `abbreviation`: Scientific/standard abbreviations
  
- **Descriptions**: Optional context fields
  - `description text`: Detailed information about reference data
  - `notes text`: Free-form field notes on operational data
  - `details text`: Longer-form descriptions
  
- **Display Names**: User-facing identifiers
  - `display_name`: Either text or integer for public display
  - Often used instead of exposing internal UUIDs
  
- **Measurements with Units**: Paired fields
  - `{measurement}`: Numeric value (e.g., `temperature`, `wind_speed`)
  - `{measurement}_unit_id`: Foreign key to units table
  - Check constraint enforces both or neither

- **Counts and Quantities**: Integer fields
  - `{item}_count`: Count of items (e.g., `mosquito_count`, `larvae_count`)
  - `amount_applied`: Quantity of insecticide
  - Usually have check constraints for valid ranges (>= 0, > 0)

- **Parent/Child Relationships**: Self-referential foreign keys
  - `parent_id`: Links to same table (e.g., regions, comments)
  - Check constraint: `id <> parent_id` (prevent self-reference)

## Working with Spatial Data

### Coordinate System
- **SRID 4326** (WGS84): Standard GPS latitude/longitude
- **Precision**: Snapped to 6 decimal places (~0.1 meter precision)
- **Dimensions**: 2D only (no Z altitude or M measure dimensions)

### Inserting Spatial Features

**Direct insertion (not recommended - use helper function instead):**
```sql
-- Point from lat/lng
INSERT INTO spatial_features (geom)
VALUES (ST_SetSRID(ST_MakePoint(longitude, latitude), 4326));

-- From GeoJSON
INSERT INTO spatial_features (geom)
VALUES (ST_SetSRID(ST_GeomFromGeoJSON('{"type":"Point","coordinates":[lng,lat]}'), 4326));

-- Polygon from GeoJSON
INSERT INTO spatial_features (geom)
VALUES (ST_SetSRID(ST_GeomFromGeoJSON('{"type":"Polygon","coordinates":[[[...]]]}'), 4326));
```

### Helper Function (Recommended)
Use `public.get_or_create_spatial_feature()` to automatically deduplicate and handle both input types:

```sql
-- From lat/lng
SELECT public.get_or_create_spatial_feature(
    p_lat := 37.7749,
    p_lng := -122.4194
); -- Returns UUID of feature

-- From GeoJSON
SELECT public.get_or_create_spatial_feature(
    p_geojson := '{"type":"Point","coordinates":[-122.4194,37.7749]}'::jsonb
); -- Returns UUID of feature

-- From GeoJSON Feature (extracts geometry)
SELECT public.get_or_create_spatial_feature(
    p_geojson := '{
        "type":"Feature",
        "geometry":{"type":"Point","coordinates":[-122.4194,37.7749]},
        "properties":{}
    }'::jsonb
); -- Returns UUID of feature
```

**Benefits:**
- Automatically snaps to 6 decimal places
- Deduplicates geometries (same location = same feature_id)
- Handles both lat/lng pairs and GeoJSON
- Forces 2D geometries
- Thread-safe with atomic insert

### Querying Spatial Data

**Find features within distance:**
```sql
SELECT sf.id, sf.lat, sf.lng
FROM spatial_features sf
WHERE ST_DWithin(
    sf.geom,
    ST_SetSRID(ST_MakePoint(-122.4194, 37.7749), 4326)::geography,
    1000  -- meters
);
```

**Find features within polygon:**
```sql
SELECT sf.id, sf.lat, sf.lng
FROM spatial_features sf
WHERE ST_Within(
    sf.geom,
    ST_SetSRID(ST_GeomFromGeoJSON(:polygon_geojson), 4326)
);
```

**Calculate distance between features:**
```sql
SELECT 
    ST_Distance(
        sf1.geom::geography,
        sf2.geom::geography
    ) as distance_meters
FROM spatial_features sf1, spatial_features sf2
WHERE sf1.id = :feature1_id AND sf2.id = :feature2_id;
```

**Get feature as GeoJSON (use generated column):**
```sql
SELECT geojson FROM spatial_features WHERE id = :feature_id;
-- Returns: {"type":"Point","coordinates":[-122.4194,37.7749]}
```

### Common Spatial Operations

**Buffer (create area around point/line):**
```sql
SELECT ST_Buffer(geom::geography, 100)::geometry  -- 100 meter buffer
FROM spatial_features
WHERE id = :feature_id;
```

**Centroid (center point of polygon):**
```sql
SELECT ST_Centroid(geom) FROM spatial_features WHERE id = :feature_id;
-- Note: lat/lng columns are already centroids (generated)
```

**Simplify polygon (reduce complexity):**
```sql
SELECT ST_Simplify(geom, 0.0001)  -- tolerance in degrees
FROM spatial_features
WHERE id = :feature_id;
```

### Important Spatial Notes
1. **Generated Columns**: Never INSERT or UPDATE `lat`, `lng`, or `geojson`
2. **GIST Index**: Spatial queries use GIST index on `geom` column
3. **Geography vs Geometry**: 
   - Use `::geography` for distance calculations (meters)
   - Use `geometry` for spatial relationships (within, intersects)
4. **Unique Constraint**: Based on MD5 hash, prevents exact duplicate geometries
5. **No Manual Editing**: Always use helper function or properly formed geometries

## Migration File Organization

Migrations are in `/supabase/migrations/` with timestamps:
- Format: `YYYYMMDDHHMMSS_description.sql`
- Applied in chronological order
- Should be idempotent when possible

## Key Relationships & Data Flow

### Core Entity Relationships

**Authentication Flow:**
```
auth.users (Supabase)
    ↓ 1:1
public.profiles (user_id)
    ↓ many:1
public.groups (group_id)
public.roles (role_id)
```

**Trap Surveillance Flow:**
```
public.traps
    → trap_types (type)
    → spatial_features (location)
    → addresses (optional address)
    ↓
public.collections (trap visit)
    → trap_lures (optional)
    → profiles (collected_by, trap_set_by)
    ↓
public.collection_results (identification)
    → species (what was found)
    → profiles (identified_by)
```

**Larval Surveillance Flow:**
```
public.habitats (breeding site)
    → spatial_features (location)
    → addresses (optional)
    ↓
public.inspections (site visit)
    → profiles (inspected_by)
    → densities (optional density ref)
    ↓
public.samples (lab analysis)
    ↓
public.sample_results (species ID)
    → species
    → profiles (identified_by)
```

**Insecticide Application Flow:**
```
public.insecticides (product catalog)
    ↓
public.insecticide_batches (lot tracking)
    ↓
[multiple application types]
    - truck_applications
    - handheld_applications  } → public.applications (unified)
    - catch_basin_missions
    - flights → flight_aerial_sites
    - inspections (with treatment)
    ↓
public.applications (unified record)
    → spatial_features (where)
    → equipment (what equipment)
    → application_methods (how)
```

**Spatial Hierarchy:**
```
public.spatial_features (shared geometry)
    ↑ referenced by
    - traps
    - habitats
    - addresses
    - regions
    - inspections
    - landing_rates
    - service_requests
    - aerial_sites
    - applications
    - source_reductions
```

**Route Planning:**
```
public.routes
    ↓
public.route_items
    → habitats (or other entities)
    → directions_to_next_item
```

**Tagging System:**
```
public.tag_groups (categories)
    ↓
public.tags (user-defined labels)
    ↓ many:many via junction tables
[multiple entities]
    - traps → trap_tags
    - addresses → address_tags
    - habitats → habitat_tags
    - inspections → inspection_tags
    - service_requests → service_request_tags
```

**Comments System:**
```
public.comments (polymorphic)
    → trap_id OR
    → collection_id OR
    → landing_rate_id OR
    → service_request_id OR
    → contact_id OR
    → aerial_site_id OR
    → sample_id OR
    → notification_id OR
    → parent_id (comment reply)
```

### Important Cascading Rules

**on delete restrict** (most common):
- Prevents deletion of referenced records
- Used for: groups, insecticides, trap_types, species, etc.
- Ensures data integrity - must delete children first

**on delete cascade**:
- Automatically deletes child records
- Used for junction tables: trap_tags, address_tags, etc.
- Used for tightly coupled data: samples when inspection deleted

**on delete set null**:
- Preserves record but nullifies foreign key
- Used for audit fields: created_by, updated_by
- Used for optional references: address_id on traps

### Foreign Key Reference Patterns

**Profile references:**
```sql
-- Reference profiles by user_id (not id)
created_by uuid references public.profiles(user_id) on delete set null
collected_by uuid references public.profiles(user_id) on delete set null
```

**Group references:**
```sql
-- Restrict deletion - protect data
group_id uuid not null references public.groups(id) on delete restrict
```

**Spatial references:**
```sql
-- Restrict deletion - geometry is shared
feature_id uuid not null references public.spatial_features(id) on delete restrict
```

**Optional references:**
```sql
-- Set null - preserve main record
address_id uuid references public.addresses(id) on delete set null
```

## When Working with the Database

### For Queries

1. **Always respect RLS**: Queries run as authenticated users with group context
   - Use authenticated connections, not service_role unless bypassing RLS intentionally
   - RLS policies automatically filter results based on JWT metadata
   
2. **Check group_id**: Most operational data is filtered by group membership
   ```sql
   -- RLS does this automatically, but for explicit checks:
   WHERE public.user_is_group_member(group_id)
   ```

3. **Use helper functions**: Leverage built-in authorization helpers
   - `public.user_is_group_member(group_id)`: Group membership check
   - `public.user_has_group_role(group_id, role_id)`: Role level check
   - `public.user_owns_record(user_id)`: Record ownership check
   
4. **Consider soft deletes**: Deleted data is in `simmer.deleted_data`, not main tables
   ```sql
   -- Find recently deleted records
   SELECT * FROM simmer.deleted_data
   WHERE original_table = 'traps'
   AND deleted_at > now() - interval '7 days'
   ORDER BY deleted_at DESC;
   ```

5. **Join patterns**: Be aware of reference vs operational data
   ```sql
   -- Good: Joining operational data (both have group_id)
   SELECT t.trap_name, c.collection_date
   FROM traps t
   JOIN collections c ON c.trap_id = t.id
   WHERE t.group_id = :group_id;
   
   -- Good: Joining reference data (no RLS on species)
   SELECT s.species_name, cr.mosquito_count
   FROM collection_results cr
   JOIN species s ON s.id = cr.species_id
   WHERE cr.group_id = :group_id;
   ```

6. **Spatial queries**: Use appropriate indexes and geography casts
   ```sql
   -- Use geography for distance (meters)
   WHERE ST_DWithin(geom::geography, :point::geography, :radius_m)
   
   -- Use geometry for spatial relationships
   WHERE ST_Within(geom, :polygon)
   ```

7. **Handle polymorphic tables**: Check the type column or use CASE
   ```sql
   -- Comments example
   SELECT 
       c.*,
       c.parent_type,
       CASE 
           WHEN c.trap_id IS NOT NULL THEN t.trap_name
           WHEN c.collection_id IS NOT NULL THEN col.collection_date::text
           -- ... other cases
       END as parent_display
   FROM comments c
   LEFT JOIN traps t ON c.trap_id = t.id
   LEFT JOIN collections col ON c.collection_id = col.id;
   ```

8. **Count queries**: Optimize with proper indexes
   ```sql
   -- Count collections per trap efficiently
   SELECT t.id, t.trap_name, COUNT(c.id) as collection_count
   FROM traps t
   LEFT JOIN collections c ON c.trap_id = t.id
   WHERE t.group_id = :group_id
   GROUP BY t.id, t.trap_name;
   ```

### For Inserts

1. **Use helper functions for spatial data**:
   ```sql
   -- Create feature first, get UUID
   SELECT public.get_or_create_spatial_feature(
       p_lat := 37.7749,
       p_lng := -122.4194
   ) INTO v_feature_id;
   
   -- Then use in insert
   INSERT INTO traps (group_id, trap_type_id, feature_id, ...)
   VALUES (:group_id, :type_id, v_feature_id, ...);
   ```

2. **Provide group_id**: Required for most operational tables
   - Get from JWT: `(auth.jwt() -> 'app_metadata' ->> 'group_id')::uuid`
   - Or from function parameter if explicit

3. **Don't set audit fields**: Let triggers handle them
   ```sql
   -- Bad:
   INSERT INTO traps (id, group_id, ..., created_at, created_by)
   VALUES (gen_random_uuid(), ..., now(), auth.uid());
   
   -- Good:
   INSERT INTO traps (group_id, trap_type_id, feature_id, ...)
   VALUES (:group_id, :type_id, :feature_id, ...);
   -- Trigger automatically sets created_at, created_by, updated_at, updated_by
   ```

4. **Check constraints**: Ensure data meets requirements
   - Counts >= 0 or > 0
   - Time ranges (end > start)
   - Paired fields (unit + value)
   - Required text when boolean is true

5. **Respect unique constraints**:
   - Check for existing records before insert
   - Use UPSERT (ON CONFLICT) when appropriate
   ```sql
   INSERT INTO trap_tags (group_id, trap_id, tag_id)
   VALUES (:group_id, :trap_id, :tag_id)
   ON CONFLICT (trap_id, tag_id) DO NOTHING;
   ```

### For Updates

1. **Never update audit fields**: Let triggers handle them
   ```sql
   -- Bad:
   UPDATE traps SET trap_name = :name, updated_at = now(), updated_by = auth.uid()
   
   -- Good:
   UPDATE traps SET trap_name = :name WHERE id = :id;
   -- Trigger automatically updates updated_at and updated_by
   ```

2. **Never update profile.user_id**: Trigger prevents it
   - user_id is immutable after creation
   - Links profile to auth.users permanently

3. **Check RLS policies**: Ensure user has permission
   - Updates may fail silently if RLS blocks them
   - Policies often require role check AND ownership check

4. **Don't update generated columns**: Database-managed
   - spatial_features: lat, lng, geojson
   - comments: parent_type
   - Any column marked `GENERATED ALWAYS`

### For Deletes

1. **Soft deletes happen automatically**: Trigger moves to deleted_data
   ```sql
   DELETE FROM traps WHERE id = :trap_id;
   -- Record moves to simmer.deleted_data, not permanently deleted
   ```

2. **Check cascade rules**: Understand what else will be deleted/nullified
   - Junction tables cascade (trap_tags deleted when trap deleted)
   - Audit fields set to null (created_by nullified on profile delete)
   - Restrict prevents deletion (can't delete group with data)

3. **Check RLS policies**: Ensure user has permission
   - Often requires manager role OR ownership
   ```sql
   -- Policy example:
   using (
       public.user_has_group_role(group_id, 3)  -- Managers
       OR user_owns_record(created_by)          -- Or record creator
   )
   ```

4. **Foreign key constraints**: May prevent deletion
   ```sql
   -- Can't delete group with traps:
   DELETE FROM groups WHERE id = :group_id;
   -- ERROR: update or delete on table "groups" violates foreign key constraint
   
   -- Must delete children first or use SET NULL/CASCADE
   ```

### For Schema Changes

1. **Create migration file**: Add to `/supabase/migrations/`
   - Format: `YYYYMMDDHHMMSS_description.sql`
   - Example: `20260216120000_add_trap_notes_field.sql`
   - Applied in chronological order

2. **Update schema file**: Modify corresponding file in `/supabase/schemas/`
   - Keep migration history AND schema definition in sync
   - Schema files = source of truth for current structure
   - Migrations = historical record of changes

3. **Follow patterns**: Use standard audit fields, RLS, soft delete
   ```sql
   -- Add new table with all standard patterns
   CREATE TABLE public.new_table (
       id uuid primary key default gen_random_uuid(),
       group_id uuid not null references public.groups(id) on delete restrict,
       -- domain fields here
       metadata jsonb,
       created_at timestamptz not null default now(),
       created_by uuid references public.profiles(user_id) on delete set null,
       updated_at timestamptz not null default now(),
       updated_by uuid references public.profiles(user_id) on delete set null
   );
   
   -- Add triggers
   CREATE TRIGGER set_audit_fields
   BEFORE INSERT OR UPDATE ON public.new_table
   FOR EACH ROW EXECUTE FUNCTION public.set_audit_fields();
   
   CREATE TRIGGER soft_delete_trigger
   BEFORE DELETE ON public.new_table
   FOR EACH ROW EXECUTE FUNCTION simmer.soft_delete();
   
   -- Enable RLS
   ALTER TABLE public.new_table ENABLE ROW LEVEL SECURITY;
   
   -- Add policies (customize based on needs)
   CREATE POLICY "select: own groups"
   ON public.new_table FOR SELECT TO authenticated
   USING (public.user_is_group_member(group_id));
   
   CREATE POLICY "insert: own group manager"
   ON public.new_table FOR INSERT TO authenticated
   WITH CHECK (public.user_has_group_role(group_id, 3));
   
   CREATE POLICY "update: own group manager"
   ON public.new_table FOR UPDATE TO authenticated
   USING (public.user_has_group_role(group_id, 3))
   WITH CHECK (public.user_has_group_role(group_id, 3));
   
   CREATE POLICY "delete: own group manager"
   ON public.new_table FOR DELETE TO authenticated
   USING (public.user_has_group_role(group_id, 3));
   ```

4. **Enable RLS**: Always enable row level security
   ```sql
   ALTER TABLE public.table_name ENABLE ROW LEVEL SECURITY;
   ```

5. **Add policies**: Define appropriate SELECT, INSERT, UPDATE, DELETE policies
   - Based on role level (1-5)
   - Consider ownership for updates/deletes
   - Reference data typically read-only

6. **Add indexes**: For frequently queried fields
   ```sql
   -- Foreign keys
   CREATE INDEX idx_traps_group_id ON public.traps(group_id);
   CREATE INDEX idx_traps_trap_type_id ON public.traps(trap_type_id);
   
   -- Dates (for range queries)
   CREATE INDEX idx_collections_collection_date ON public.collections(collection_date);
   
   -- Booleans (if highly selective)
   CREATE INDEX idx_traps_is_active ON public.traps(is_active) WHERE is_active = true;
   
   -- Spatial (GIST)
   CREATE INDEX idx_table_geom ON public.table_name USING GIST(geom);
   ```

7. **Test with RLS**: Ensure policies work as expected
   ```sql
   -- Set role to simulate user
   SET ROLE authenticated;
   SET request.jwt.claims = '{"app_metadata":{"group_id":"...","role_id":4}}';
   
   -- Test query
   SELECT * FROM traps;  -- Should only see own group's data
   ```

## Reference Data vs Operational Data

### Reference Data (READ-ONLY or ADMIN-ONLY)

**No group_id** - Shared across all groups:
- **genera, species**: Mosquito taxonomy (read-only by design)
- **units**: Measurement units (read-only by design)
- **roles**: Role definitions (read-only by design)
- **tag_groups**: Tag categories (read-only)
- **spatial_features**: Shared geometry (write-allowed but shared)

**Has group_id** - Group-specific configuration:
- **trap_types**: Trap type catalog (admin-managed, role 2)
- **trap_lures**: Lure options (manager-managed, role 3)
- **densities**: Density classifications (manager-managed, role 3)
- **insecticides**: Product catalog (admin-managed, role 2)
- **vehicles, equipment**: Asset inventory (admin-managed, role 2)
- **application_methods**: Method catalog (admin-managed, role 2)
- **source_reduction_types**: Activity catalog (admin-managed, role 2)
- **tags**: User-defined labels (admin-managed, role 2)
- **region_folders**: Region organization (admin-managed, role 2)

**RLS patterns for reference data:**
```sql
-- Public read-only (genera, species, units)
CREATE POLICY "select: anyone" ON public.genera FOR SELECT TO public USING (true);
CREATE POLICY "insert: none" ON public.genera FOR INSERT TO public WITH CHECK (false);
CREATE POLICY "update: none" ON public.genera FOR UPDATE TO public USING (false);
CREATE POLICY "delete: none" ON public.genera FOR DELETE TO public USING (false);

-- Group-scoped but admin-only (trap_types, insecticides)
CREATE POLICY "select: own groups" ON public.trap_types FOR SELECT TO authenticated
USING (public.user_is_group_member(group_id));

CREATE POLICY "insert: group admin" ON public.trap_types FOR INSERT TO authenticated
WITH CHECK (public.user_has_group_role(group_id, 2));

CREATE POLICY "update: group admin" ON public.trap_types FOR UPDATE TO authenticated
USING (public.user_has_group_role(group_id, 2))
WITH CHECK (public.user_has_group_role(group_id, 2));

CREATE POLICY "delete: group admin" ON public.trap_types FOR DELETE TO authenticated
USING (public.user_has_group_role(group_id, 2));
```

### Operational Data (COLLECTOR-LEVEL)

**Has group_id** - Day-to-day field operations:
- **traps**: Trap installations (manager-managed, role 3)
- **collections**: Trap collections (collector-managed, role 4)
- **collection_results**: Mosquito IDs (collector-managed, role 4)
- **landing_rates**: Landing observations (collector-managed, role 4)
- **habitats**: Breeding sites (collector-managed, role 4)
- **inspections**: Larval inspections (collector-managed, role 4)
- **aerial_inspections**: Aerial surveys (collector-managed, role 4)
- **samples, sample_results**: Lab analysis (collector-managed, role 4)
- **truck_applications, handheld_applications**: Spray ops (collector-managed, role 4)
- **applications**: Unified application records (collector-managed, role 4)
- **source_reductions**: Source reduction activities (collector-managed, role 4)
- **addresses**: Address records (collector can create/edit own, manager edits all)
- **service_requests**: Public requests (manager-managed, role 3)
- **notifications**: Notification registry (manager-managed, role 3)
- **routes, route_items**: Route planning (manager-managed, role 3)
- **contacts**: Contact information (manager-managed, role 3)

**Common operational data RLS:**
```sql
-- Collectors can create and edit their own records
CREATE POLICY "insert: own group collector"
ON public.collections FOR INSERT TO authenticated
WITH CHECK (public.user_has_group_role(group_id, 4));

CREATE POLICY "update: own group collector"
ON public.collections FOR UPDATE TO authenticated
USING (public.user_has_group_role(group_id, 4))
WITH CHECK (public.user_has_group_role(group_id, 4));

-- Managers can delete, or collectors can delete their own
CREATE POLICY "delete: own group manager or own records"
ON public.collections FOR DELETE TO authenticated
USING (
    public.user_has_group_role(group_id, 3) 
    OR user_owns_record(created_by)
);
```

### Mixed Permissions (addresses example)

Some tables have hybrid permissions where users can edit their own records but managers can edit all:

```sql
CREATE POLICY "update: own if collector, all if manager"
ON public.addresses FOR UPDATE TO authenticated
USING (
    (public.user_has_group_role(group_id, 4) AND created_by = auth.uid())
    OR public.user_has_group_role(group_id, 3)
)
WITH CHECK (
    (public.user_has_group_role(group_id, 4) AND created_by = auth.uid())
    OR public.user_has_group_role(group_id, 3)
);
```

## Common Query Patterns

### Get user's group context from JWT
```sql
SELECT 
  (auth.jwt() -> 'app_metadata' ->> 'group_id')::uuid as group_id,
  (auth.jwt() -> 'app_metadata' ->> 'role_id')::integer as role_id,
  (auth.jwt() -> 'app_metadata' ->> 'profile_id')::uuid as profile_id;
```

### Get user's profile with group and role details
```sql
SELECT 
    p.*,
    g.group_name,
    r.role_name
FROM profiles p
JOIN groups g ON g.id = p.group_id
JOIN roles r ON r.id = p.role_id
WHERE p.user_id = auth.uid();
```

### Trap surveillance summary
```sql
-- Get trap collection summary with latest collection
SELECT 
    t.id,
    t.trap_name,
    t.is_active,
    tt.trap_type_name,
    sf.lat,
    sf.lng,
    a.display_name as address,
    COUNT(c.id) as total_collections,
    MAX(c.collection_date) as last_collection_date,
    SUM(cr.mosquito_count) as total_mosquitoes
FROM traps t
JOIN trap_types tt ON tt.id = t.trap_type_id
JOIN spatial_features sf ON sf.id = t.feature_id
LEFT JOIN addresses a ON a.id = t.address_id
LEFT JOIN collections c ON c.trap_id = t.id
LEFT JOIN collection_results cr ON cr.collection_id = c.id
WHERE t.group_id = :group_id
GROUP BY t.id, t.trap_name, t.is_active, tt.trap_type_name, sf.lat, sf.lng, a.display_name;
```

### Species abundance from collections
```sql
-- Top species by mosquito count in date range
SELECT 
    g.genus_name,
    s.species_name,
    SUM(cr.mosquito_count) as total_count,
    COUNT(DISTINCT cr.collection_id) as collection_count,
    cr.sex,
    cr.status
FROM collection_results cr
JOIN species s ON s.id = cr.species_id
JOIN genera g ON g.id = s.genus_id
JOIN collections c ON c.id = cr.collection_id
WHERE cr.group_id = :group_id
  AND c.collection_date BETWEEN :start_date AND :end_date
GROUP BY g.genus_name, s.species_name, cr.sex, cr.status
ORDER BY total_count DESC
LIMIT 20;
```

### Larval inspections summary
```sql
-- Wet inspections with larvae in date range
SELECT 
    i.id,
    i.inspection_date,
    p.full_name as inspector,
    h.habitat_name,
    h.description as habitat_description,
    sf.lat,
    sf.lng,
    i.larvae_count,
    i.dip_count,
    CASE 
        WHEN i.larvae_count > 0 AND i.dip_count > 0 
        THEN ROUND((i.larvae_count::decimal / i.dip_count), 2)
        ELSE NULL
    END as larvae_per_dip,
    d.density_name,
    i.has_first_instar,
    i.has_fourth_instar,
    i.has_pupae,
    i.is_source_reduction,
    i.source_reduction_notes
FROM inspections i
JOIN profiles p ON p.user_id = i.inspected_by
JOIN spatial_features sf ON sf.id = i.feature_id
LEFT JOIN habitats h ON h.id = i.habitat_id
LEFT JOIN densities d ON d.id = i.density_id
WHERE i.group_id = :group_id
  AND i.is_wet = true
  AND i.inspection_date BETWEEN :start_date AND :end_date
ORDER BY i.inspection_date DESC;
```

### Insecticide application summary
```sql
-- All applications across types in date range
SELECT 
    a.id,
    a.application_date,
    p.full_name as applicator,
    i.trade_name as insecticide,
    ib.batch_name,
    a.amount_applied,
    u.abbreviation as unit,
    am.method_name,
    e.equipment_name,
    sf.lat,
    sf.lng,
    CASE 
        WHEN a.inspection_id IS NOT NULL THEN 'Larval Treatment'
        WHEN a.flight_aerial_site_id IS NOT NULL THEN 'Aerial Application'
        WHEN a.catch_basin_mission_id IS NOT NULL THEN 'Catch Basin'
        WHEN a.handheld_applications_id IS NOT NULL THEN 'Handheld'
        WHEN a.truck_applications IS NOT NULL THEN 'Truck'
        ELSE 'Unknown'
    END as application_type
FROM applications a
JOIN profiles p ON p.user_id = a.applicator_id
JOIN insecticides i ON i.id = a.insecticide_id
JOIN units u ON u.id = a.application_unit_id
LEFT JOIN insecticide_batches ib ON ib.id = a.batch_id
LEFT JOIN application_methods am ON am.id = a.application_method_id
LEFT JOIN equipment e ON e.id = a.equipment_id
LEFT JOIN spatial_features sf ON sf.id = a.feature_id
WHERE a.group_id = :group_id
  AND a.application_date BETWEEN :start_date AND :end_date
ORDER BY a.application_date DESC, a.created_at DESC;
```

### Query with spatial filter (traps near point)
```sql
-- Find traps within 1000m of a point
SELECT 
    t.id,
    t.trap_name,
    tt.trap_type_name,
    sf.lat,
    sf.lng,
    ROUND(
        ST_Distance(
            sf.geom::geography,
            ST_SetSRID(ST_MakePoint(:lng, :lat), 4326)::geography
        )
    ) as distance_meters
FROM traps t
JOIN trap_types tt ON tt.id = t.trap_type_id
JOIN spatial_features sf ON sf.id = t.feature_id
WHERE t.group_id = :group_id
  AND ST_DWithin(
      sf.geom::geography,
      ST_SetSRID(ST_MakePoint(:lng, :lat), 4326)::geography,
      1000  -- meters
  )
ORDER BY distance_meters;
```

### Query with polygon filter (inspections in region)
```sql
-- Find inspections within a specific region's boundaries
SELECT 
    i.id,
    i.inspection_date,
    p.full_name as inspector,
    sf.lat,
    sf.lng,
    i.is_wet,
    i.larvae_count
FROM inspections i
JOIN profiles p ON p.user_id = i.inspected_by
JOIN spatial_features sf ON sf.id = i.feature_id
JOIN regions r ON r.id = :region_id
JOIN spatial_features rsf ON rsf.id = r.feature_id
WHERE i.group_id = :group_id
  AND ST_Within(sf.geom, rsf.geom)
  AND i.inspection_date BETWEEN :start_date AND :end_date;
```

### Service requests with related comments
```sql
-- Service requests with comment count
SELECT 
    sr.id,
    sr.display_name as ticket_number,
    sr.request_date,
    sr.intake_type,
    sr.is_closed,
    a.display_name as address,
    c.contact_name,
    sr.details,
    COUNT(cmt.id) as comment_count,
    MAX(cmt.created_at) as last_comment_at
FROM service_requests sr
JOIN addresses a ON a.id = sr.address_id
JOIN contacts c ON c.id = sr.contact_id
LEFT JOIN comments cmt ON cmt.service_request_id = sr.id
WHERE sr.group_id = :group_id
GROUP BY sr.id, sr.display_name, sr.request_date, sr.intake_type, 
         sr.is_closed, a.display_name, c.contact_name, sr.details
ORDER BY sr.request_date DESC;
```

### Get deleted records for recovery
```sql
-- Find recently deleted traps
SELECT 
    dd.id,
    dd.deleted_at,
    p.full_name as deleted_by_name,
    dd.data->>'trap_name' as trap_name,
    dd.data->>'trap_code' as trap_code,
    (dd.data->>'created_at')::timestamptz as originally_created_at
FROM simmer.deleted_data dd
LEFT JOIN profiles p ON p.user_id = dd.deleted_by
WHERE dd.original_table = 'traps'
  AND (dd.data->>'group_id')::uuid = :group_id
ORDER BY dd.deleted_at DESC
LIMIT 50;
```

### Route with ordered items
```sql
-- Get route with items in order
SELECT 
    r.id as route_id,
    r.route_name,
    ri.rank_string,
    ri.directions_to_next_item,
    h.habitat_name,
    h.description,
    sf.lat,
    sf.lng,
    a.display_name as address
FROM routes r
JOIN route_items ri ON ri.route_id = r.id
LEFT JOIN habitats h ON h.id = ri.habitat_id
LEFT JOIN spatial_features sf ON sf.id = h.feature_id
LEFT JOIN addresses a ON a.id = h.address_id
WHERE r.id = :route_id
ORDER BY ri.rank_string COLLATE "C";  -- Lexicographic ordering
```

### Tagged entities query
```sql
-- Find all traps with a specific tag
SELECT 
    t.id,
    t.trap_name,
    tt.trap_type_name,
    sf.lat,
    sf.lng,
    tg.tag_name,
    tg.color
FROM traps t
JOIN trap_types tt ON tt.id = t.trap_type_id
JOIN spatial_features sf ON sf.id = t.feature_id
JOIN trap_tags ttags ON ttags.trap_id = t.id
JOIN tags tg ON tg.id = ttags.tag_id
WHERE t.group_id = :group_id
  AND tg.tag_name = :tag_name;
```

### Polymorphic additional personnel query
```sql
-- Get all team members for a specific inspection
SELECT 
    ap.id,
    p.full_name,
    p.user_id,
    CASE 
        WHEN ap.inspection_id IS NOT NULL THEN 'inspection'
        WHEN ap.aerial_inspection_id IS NOT NULL THEN 'aerial_inspection'
        WHEN ap.flight_id IS NOT NULL THEN 'flight'
        WHEN ap.application_id IS NOT NULL THEN 'application'
        WHEN ap.source_reduction_id IS NOT NULL THEN 'source_reduction'
    END as activity_type
FROM additional_personnel ap
JOIN profiles p ON p.user_id = ap.personnel_id
WHERE ap.inspection_id = :inspection_id;
```

## Important Notes & Best Practices

### Critical Constraints

1. **Schema Separation**: 
   - `simmer` schema: Infrastructure (deleted_data, utility functions)
   - `public` schema: Application data (all tables, RLS policies, triggers)
   - `auth` schema: Supabase managed (users tables)
   - `extensions` schema: PostGIS and other extensions

2. **Always use auth.uid()**: 
   - For getting current user ID in RLS policies and triggers
   - Returns UUID of authenticated user from JWT
   - Never hardcode user IDs

3. **Geometry SRID**: 
   - Always use 4326 (WGS84) for geographic coordinates
   - Standard GPS coordinate system (latitude/longitude)
   - Required for spatial_features table

4. **Generated Columns**: 
   - `spatial_features`: lat, lng, geojson (never INSERT or UPDATE these)
   - `comments`: parent_type (computed from foreign keys)
   - Database automatically computes and stores these

5. **Cascading Deletes**: 
   - Most foreign keys use `on delete set null` (audit fields, optional refs)
   - Some use `on delete restrict` (operational data, prevent orphans)
   - Junction tables use `on delete cascade` (clean up automatically)
   - Choose carefully based on data relationship

6. **Unique Constraints**: 
   - Spatial features: MD5 hash of geometry ensures no exact duplicates
   - Many tables: Group-scoped uniqueness (group_id + name)
   - Junction tables: Prevent duplicate relationships

7. **Indexes**: 
   - GIST indexes on all geometry columns for spatial queries
   - B-tree indexes on foreign keys (automatic with FK constraint)
   - Consider partial indexes for boolean flags
   - Consider indexes on date columns for range queries

8. **Profile References**:
   - Always reference `profiles(user_id)` NOT `profiles(id)`
   - `user_id` links to Supabase auth.users
   - `id` is internal primary key for profiles table

9. **Soft Deletes**:
   - All user data preserved in `simmer.deleted_data` as JSONB
   - Original table name, ID, and full record stored
   - Recovery possible by parsing JSONB and re-inserting
   - Consider retention policy for old deleted_data

10. **JWT Metadata Sync**:
    - Profile changes automatically sync to JWT (trigger)
    - group_id, role_id, profile_id available in auth.jwt()
    - Enables fast authorization without table joins
    - Powers all RLS policies

11. **Polymorphic Tables**:
    - Check constraints enforce exactly one parent reference
    - Use CASE statements in queries to handle multiple types
    - Generated type columns simplify querying
    - Consider performance impact of multiple LEFT JOINs

12. **Tag System**:
    - Tags scoped to tag_groups for organization
    - Tags scoped to groups (multi-tenant)
    - Junction tables enable many-to-many relationships
    - Unique constraint prevents duplicate tags on same entity

13. **Unit System**:
    - Supports unit conversion with conversion_factor
    - Units reference base_unit_id for conversion chains
    - Always pair unit fields with value fields
    - Check constraints enforce valid combinations

14. **Timestamps**:
    - Use `timestamptz` for all timestamps (includes timezone)
    - `date` for dates without time (collection_date, inspection_date)
    - `time` for time without date (start_time, end_time)
    - Never use timestamp without timezone

### Performance Considerations

1. **RLS Performance**:
   - RLS policies run on every query
   - Keep policies simple and indexed
   - JWT metadata access is fast (in-memory)
   - Consider materialized views for complex aggregations

2. **Spatial Queries**:
   - Use `::geography` for distance in meters
   - Use `geometry` for spatial relationships
   - GIST indexes are essential for performance
   - Consider simplifying complex polygons

3. **Large Result Sets**:
   - Use LIMIT and OFFSET for pagination
   - Index columns used in WHERE and ORDER BY
   - Consider cursor-based pagination for large tables

4. **Aggregations**:
   - Pre-aggregate in views for frequent queries
   - Use materialized views for expensive calculations
   - Refresh materialized views on schedule or trigger

5. **Soft Deletes**:
   - deleted_data table grows over time
   - Consider archiving old deleted records
   - May impact performance of simmer schema queries

### Security Considerations

1. **RLS is Mandatory**:
   - ALL tables must have RLS enabled
   - Missing policies = inaccessible data
   - Test policies with different roles

2. **Service Role Bypass**:
   - service_role connection bypasses RLS
   - Use only for admin operations and migrations
   - Never use in application code with user input

3. **Role Escalation Prevention**:
   - Triggers prevent users changing own role
   - Triggers prevent changing profile.user_id
   - Group owners can't delete groups with data

4. **Input Validation**:
   - Check constraints enforce data integrity
   - Foreign keys prevent invalid references
   - NOT NULL prevents missing critical data
   - Unique constraints prevent duplicates

5. **Audit Trail**:
   - created_by/updated_by track all changes
   - Soft deletes preserve who deleted data
   - Timestamps enable temporal queries

### Common Pitfalls to Avoid

1. **Don't manually set audit fields** - Let triggers handle them
2. **Don't bypass RLS** unless absolutely necessary (migrations only)
3. **Don't forget group_id** on operational tables
4. **Don't use timestamp** - Use timestamptz instead
5. **Don't INSERT generated columns** - Database computes these
6. **Don't use service_role** in application queries
7. **Don't forget to enable RLS** on new tables
8. **Don't skip check constraints** - Prevent invalid data
9. **Don't hardcode UUIDs** - Use gen_random_uuid()
10. **Don't delete reference data** - May have operational data dependencies

## Finding Information & Navigation

### Table Definitions
- **Location**: `/supabase/schemas/{category}/{number} - {name}.sql`
- **Categories**: 000 (simmer), 100 (auth), 200 (general), 300 (gis), 400 (adult), 500 (larval), 600 (outreach), 700 (insecticide), 800 (source reduction), 999 (general support)
- **Naming**: Numbers indicate load order, names are descriptive

### Relationships
- **Foreign key constraints**: In CREATE TABLE statements
- **Cascade rules**: `on delete` and `on update` clauses
- **Polymorphic links**: Multiple optional foreign keys with check constraints

### Permissions
- **RLS policies**: At bottom of each table's schema file
- **Policy naming**: Describes action and role requirement
- **Helper functions**: `/supabase/schemas/100 - auth/000 - rls-helper-functions.sql`

### Functions
- **Core functions**: `/supabase/schemas/000 - simmer/` (soft delete, audit)
- **Auth helpers**: `/supabase/schemas/100 - auth/000 - rls-helper-functions.sql`
- **Spatial helpers**: `/supabase/schemas/300 - gis/999 - get_or_create_spatial_features.sql`
- **Utility functions**: Files ending in `999 -` contain helper functions

### Migrations
- **Location**: `/supabase/migrations/`
- **Format**: `YYYYMMDDHHMMSS_description.sql`
- **Order**: Applied chronologically by timestamp
- **Schema files**: Updated to match migration changes

### Schema Organization
- **Load order**: Determined by folder and file number prefixes
- **Dependencies**: Lower numbers before higher numbers
- **Core infrastructure**: 000 - simmer
- **Auth and RLS**: 100 - auth (depends on simmer)
- **Reference data**: 200 - general (depends on auth)
- **Spatial**: 300 - gis (depends on general)
- **Domain tables**: 400-800 (depend on foundation)
- **Support tables**: 999 - general (depend on domain)

### Documentation
- **This file**: Comprehensive architecture overview
- **Schema files**: Source of truth for current structure
- **Migrations**: Historical record of all changes
- **Comments in SQL**: Column and constraint explanations

### Quick Reference by Use Case

**Need to add a trap?**
- Table: `public.traps`
- Requires: `group_id`, `trap_type_id`, `feature_id`
- Optional: `address_id`, `trap_name`, `trap_code`
- Permissions: Manager (role 3)
- Schema file: `/supabase/schemas/400 - adult surveillance/120 - traps.sql`

**Need to record a collection?**
- Table: `public.collections`
- Requires: `group_id`, `trap_id`, `collection_date`
- Optional: `trap_lure_id`, `collected_by`, `trap_set_date`, `trap_set_by`, `trap_nights`
- Permissions: Collector (role 4)
- Schema file: `/supabase/schemas/400 - adult surveillance/200 - collections.sql`

**Need to identify mosquitoes?**
- Table: `public.collection_results`
- Requires: `group_id`, (collection_id OR landing_rate_id), `species_id`, `mosquito_count`
- Optional: `identified_by`, `sex`, `status`
- Permissions: Collector (role 4)
- Schema file: `/supabase/schemas/400 - adult surveillance/300 - collection_results.sql`

**Need to inspect a habitat?**
- Table: `public.inspections`
- Requires: `group_id`, `feature_id`, `inspected_by`, `inspection_date`, `is_wet`
- If wet, requires: larval data (larvae_count + dip_count OR density_id)
- Optional: `habitat_id`, instar flags, source reduction fields
- Permissions: Collector (role 4)
- Schema file: `/supabase/schemas/500 - larval surveillance/200 - inspections.sql`

**Need to record insecticide application?**
1. Create application-specific record (truck_applications, handheld_applications, etc.)
2. Create unified `applications` record linking to it
- Tables: `public.applications` + type-specific table
- Requires: `group_id`, `applicator_id`, `application_date`, `insecticide_id`, `amount_applied`, `application_unit_id`
- Permissions: Collector (role 4)
- Schema files: `/supabase/schemas/700 - insecticide control/`

**Need to create a service request?**
- Table: `public.service_requests`
- Requires: `group_id`, `display_name`, `intake_type`, `request_date`, `address_id`, `feature_id`, `contact_id`, `details`
- Permissions: Manager (role 3)
- Schema file: `/supabase/schemas/600 - public outreach/100 - service_requests.sql`

**Need to check user permissions?**
- Functions: `user_is_group_member()`, `user_has_group_role()`, `user_owns_record()`
- JWT data: `auth.jwt() -> 'app_metadata'`
- Schema file: `/supabase/schemas/100 - auth/000 - rls-helper-functions.sql`

**Need to add comments?**
- Table: `public.comments`
- Requires: `group_id`, `comment_text`, ONE parent foreign key
- Optional: `parent_id` (for threaded replies), `is_pinned`
- Permissions: All group members can create; managers/creators can edit
- Schema file: `/supabase/schemas/999 - general/100 - comments.sql`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thebigthing313) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
