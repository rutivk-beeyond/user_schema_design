# Database Design

### Users

id — uuid, PK
name — varchar
email — varchar, nullable, unique
phone — varchar, unique, not null
avatar_url — varchar, nullable
role                string      – can remove
role_id	          uuid FK → roles.id
status              string
is_active — boolean, default true
invited_at          timestamp
joined_at           timestamp
last_active_at      timestamp
created_at — timestamp
updated_at — timestamp
deleted_at — timestamp, nullable
invited_by_user_id  uuid FK → users.id

### roles
id                string PK
label             string
is_super_admin    boolean
is_system_locked  boolean
sort_order        int

### permissions
id        uuid PK
key       string        -- e.g. "view_users"
module    string        -- e.g. "users"
label     string        -- UI label

###  role_permissions
id              uuid PK
role_id        string FK → roles.id
permission_id  uuid FK → permissions.id
granted        boolean

### user_permission_overrides
id              uuid PK
user_id         uuid FK → users.id
permission_id   uuid FK → permissions.id
granted         boolean
set_by_user_id  uuid FK → users.id
created_at      timestamp

### modules
id                     uuid PK
key                    string        -- e.g. "billing"
label                  string

### module_permission_requirements
id               uuid PK
module_id        uuid FK → modules.id
permission_id    uuid FK → permissions.id
requirement_type string   -- e.g. "ALL", "ANY"

### audit_log
id                uuid PK
actor_user_id     uuid FK → users.id
entity_type       string   -- "ROLE", "PERMISSION", "MODULE", etc.
entity_id         uuid     -- ID of affected record
action            string   -- "CREATE", "UPDATE", "DELETE", "ASSIGN_PERMISSION"
changes           jsonb    -- structured diff
metadata          jsonb    -- optional context
created_at        timestamp

<!-- 👉 Tracks:
Role changes
Permission updates
User actions -->


### Sessions

id — uuid, PK
user_id — uuid, FK → users
refresh_token — varchar, unique
ip_address — varchar, nullable
expires_at — timestamp
created_at — timestamp

### Societies

id — uuid, PK
name — varchar
reg_number — varchar, nullable
Secretary
Chairperson
GstINno
address — text
city — varchar
state — varchar
pincode — varchar
logo_url — varchar, nullable
admin_id — uuid, FK → users
is_active — boolean, default true
created_at — timestamp
updated_at — timestamp
deleted_at — timestamp, nullable

### Wings

id — uuid, PK
society_id — uuid, FK → societies
name — varchar (A Wing, Tower 1, Block B)
total_floors — int
created_at — timestamp
deleted_at — timestamp, nullable

### Units

id — uuid, PK
society_id — uuid, FK → societies
wing_id — uuid, FK → wings
Flatnumber — varchar (101, A-204)
floor — int
Square feet
type — enum (BHK_1, BHK_2, BHK_3, BHK_4, PENTHOUSE, SHOP, OFFICE)
status — enum (OCCUPIED, VACANT, UNDER_RENOVATION)
owner_name — varchar, nullable
created_at — timestamp
updated_at — timestamp
deleted_at — timestamp, nullable

### Residents

id — uuid, PK
user_id — uuid, FK → users
society_id — uuid, FK → societies
unit_id — uuid, FK → units
resident_type — enum (OWNER, TENANT, FAMILY_MEMBER)
relation — varchar, nullable (Wife, Son, Brother)
move_in_date — date, nullable
move_out_date — date, nullable
created_at — timestamp
updated_at — timestamp
deleted_at — timestamp, nullable

### Guards

id — uuid, PK
user_id — uuid, FK → users
society_id — uuid, FK → societies
role — enum (HEAD_GUARD, SECURITY_SUPERVISOR, GUARD)
documents — text array
is_active — boolean, default true
created_at — timestamp
updated_at — timestamp
deleted_at — timestamp, nullable

### Vehicles

id — uuid, PK
society_id — uuid, FK → societies
unit_id — uuid, FK → units
resident_id — uuid, nullable, FK → residents
number — varchar (MH02AB1234)
type — enum (CAR, BIKE, BICYCLE, OTHER)
parking_slot — varchar, nullable
created_at — timestamp
deleted_at — timestamp, nullable

### Visitors

id — uuid, PK
society_id — uuid, FK → societies
unit_id — uuid, FK → units
resident_id — uuid, FK → residents
name — varchar
phone — varchar, nullable
photo_url — varchar, nullable
purpose — enum (GUEST, DELIVERY, CAB, MAID, COOK, DRIVER, CONTRACTOR, OTHER)
purpose_note — varchar, nullable
visitor_type — enum (PREBOOKED, WALKIN, FREQUENT)
status — enum (PENDING, APPROVED, DENIED, CHECKED_IN, CHECKED_OUT, EXPIRED)
qr_token — varchar, nullable, unique
valid_from — timestamp, nullable
valid_until — timestamp, nullable
approval_method — enum, nullable (APP, OTP, QR, MANUAL)
approved_by_guard_shift_id — uuid, FK → guard_shifts, nullable -- NEW
approved_at — timestamp, nullable -- NEW
created_at — timestamp
updated_at — timestamp

### Gates

id — uuid, PK
society_id — uuid, FK → societies
name — varchar (Main Gate, Gate 2)
type — enum (ENTRY, EXIT, BOTH)
is_active — boolean, default true
created_at — timestamp
updated_at — timestamp
deleted_at — timestamp, nullable

### Shifts

id — uuid, PK
society_id — uuid, FK → societies
name — varchar (Morning, Evening, Night)
start_time — time
end_time — time
is_active — boolean, default true
created_at — timestamp
updated_at — timestamp

### Guard_Shifts

id — uuid, PK
guard_id — uuid, FK → guards
society_id — uuid, FK → societies
shift_id — uuid, FK → shifts
gate_id — uuid, FK → gates
start_time — timestamp
end_time — timestamp, nullable
status — enum (SCHEDULED, ACTIVE, COMPLETED, MISSED)
assigned_by — uuid, FK → users
created_at — timestamp
updated_at — timestamp

### Visitor_Logs

id — uuid, PK
society_id — uuid, FK → societies
visitor_id — uuid, FK → visitors
gate_id — uuid, FK → gates                         -- NEW
guard_shift_id — uuid, FK → guard_shifts           -- NEW (critical)
check_in — timestamp
check_out — timestamp, nullable
vehicle_no — varchar, nullable
entry_method — enum (QR, OTP, MANUAL)
status — enum (IN, OUT)
created_at — timestamp

### Guard_Attendance

id — uuid, PK
guard_id — uuid, FK → guards
society_id — uuid, FK → societies
check_in_time — timestamp
check_out_time — timestamp, nullable
guard_shift_id — uuid, FK → guard_shifts, nullable
created_at — timestamp

### Complaints

id — uuid, PK
society_id — uuid, FK → societies
resident_id — uuid, FK → residents
category — enum (WATER, ELECTRICITY, LIFT, SECURITY, CLEANLINESS, PARKING, NOISE, OTHER)
title — varchar
description — text
photo_urls — text array
status — enum (OPEN, IN_PROGRESS, RESOLVED, CLOSED)
priority — enum (LOW, MEDIUM, HIGH)
resolved_by — uuid, nullable, FK → users
resolved_at — timestamp, nullable
remarks — text, nullable
created_at — timestamp
updated_at — timestamp

### Notices

id — uuid, PK
society_id — uuid, FK → societies
created_by — uuid, FK → users
title — varchar
body — text
category — enum (GENERAL, MAINTENANCE, EVENT, EMERGENCY, RULE)
attachment_urls — text array
onclick_link
is_pinned — boolean, default false
published_at — timestamp
expires_at — timestamp, nullable
created_at — timestamp
updated_at — timestamp
deleted_at — timestamp, nullable

### Amenities

id — uuid, PK
society_id — uuid, FK → societies
name — varchar (Clubhouse, Gym, Swimming Pool)
description — text, nullable
photo_url — varchar, nullable
capacity — int, nullable
open_time — time
close_time — time
is_active — boolean, default true
created_at — timestamp
deleted_at — timestamp, nullable

### Amenity_bookings

id — uuid, PK
amenity_id — uuid, FK → amenities
resident_id — uuid, FK → residents
date — date
slot_start — time
slot_end — time
status — enum (CONFIRMED, CANCELLED, COMPLETED, NO_SHOW)
cancel_reason — text, nullable
created_at — timestamp
updated_at — timestamp
UNIQUE constraint on (amenity_id, date, slot_start)

### Maintenance_Bills

id — uuid, PK
society_id — uuid, FK → societies
unit_id — uuid, FK → units
payment_base -> enum[sqft, unit]
month — int (1–12)
year — int
total_amount — decimal
due_date — date
penalty
status — enum (UNPAID, PAID, OVERDUE, WAIVED)
generated_at — timestamp
paid_at — timestamp, nullable
created_at — timestamp
updated_at — timestamp
UNIQUE constraint on (society_id, unit_id, month, year)

### Payments

id — uuid, PK
bill_id — uuid, FK → bills, unique
resident_id — uuid, FK → residents
SocietyID 
UnitID
amount — decimal
method_category — enum (ONLINE, OFFLINE)
method — enum ( UPI,  CREDIT_CARD,  DEBIT_CARD,  NET_BANKING,  CASH, CHEQUE,  DD,  NEFT)
transaction_id — varchar, nullable
status — enum (SUCCESS, FAILED, PENDING, REFUNDED)
receipt_no — varchar, nullable
paid_at — timestamp
created_at — timestamp

### Activity_logs

id — uuid PK
society_id — uuid, indexed
actor_id — uuid
actor_role — string
actor_name — string
event_type — string, indexed
target_type — string
target_id — uuid
metadata — object, flexible per event
device_info — string, nullable
Type, data, metadata
timestamp — ISODate, TTL index 1 year

### community_posts

id — uuid, PK
society_id — uuid, FK → societies
resident_id — uuid, FK → residents
type — enum (TEXT, IMAGE, POLL, EVENT)
content — text, nullable
media_urls — text array, nullable
is_pinned — boolean, default false
is_anonymous — boolean, default false
status — enum (ACTIVE, REMOVED, REPORTED)
created_at — timestamp
updated_at — timestamp
deleted_at — timestamp, nullable

### community_comments

id — uuid, PK
post_id — uuid, FK → community_posts
resident_id — uuid, FK → residents
parent_id — uuid, nullable, FK → community_comments (for replies)
content — text
is_anonymous — boolean, default false
status — enum (ACTIVE, REMOVED, REPORTED)
created_at — timestamp
updated_at — timestamp
deleted_at — timestamp, nullable

### community_reactions

id — uuid, PK
resident_id — uuid, FK → residents
target_type — enum (POST, COMMENT)
target_id — uuid
reaction — enum (LIKE, LOVE, HAHA, SAD, ANGRY)
created_at — timestamp
UNIQUE constraint on (resident_id, target_type, target_id)

### community_polls

id — uuid, PK
post_id — uuid, FK → community_posts, unique
question — varchar
expires_at — timestamp, nullable
created_at — timestamp

### community_poll_options

id — uuid, PK
poll_id — uuid, FK → community_polls
option_text — varchar
created_at — timestamp

### community_poll_votes

id — uuid, PK
poll_id — uuid, FK → community_polls
option_id — uuid, FK → community_poll_options
resident_id — uuid, FK → residents
created_at — timestamp
UNIQUE constraint on (poll_id, resident_id) — one vote per resident per poll

### Events

id — uuid, PK
post_id — uuid, FK → community_posts, unique
title — varchar
description — text, nullable
venue — varchar, nullable
event_date — timestamp
cover_photo_url — varchar, nullable
created_at — timestamp

### Event_rsvps

id — uuid, PK
event_id — uuid, FK → community_events
resident_id — uuid, FK → residents
status — enum (GOING, NOT_GOING)
created_at — timestamp
UNIQUE constraint on (event_id, resident_id)

### Property_Listings

id — uuid, PK
society_id — uuid, FK → societies
unit_id — uuid, FK → units
owner_resident_id — uuid, FK → residents
listing_type — enum (RENT, SALE)
title — varchar
description — text
price — decimal
deposit — decimal, nullable
maintenance — decimal, nullable
available_from — date, nullable
furnishing — enum (UNFURNISHED, SEMI_FURNISHED, FULLY_FURNISHED)
bedrooms — int
bathrooms — int
balconies — int, nullable
area_sqft — int
floor — int
image_urls — text array
is_flagged — boolean, default false
status — enum (ACTIVE, RENTED, SOLD, EXPIRED, REMOVED)
is_active — boolean, default true
created_at — timestamp
updated_at — timestamp
deleted_at — timestamp, nullable

### Property_Favorites

 id — uuid, PK
 user_id — uuid, FK → users
 listing_id — uuid, FK → property_listings
 created_at — timestamp

### Property_Inquiries

id — uuid, PK
listing_id — uuid, FK → property_listings
buyer_user_id — uuid, FK → users
owner_user_id — uuid, FK → users
message — text, nullable
status — enum (PENDING, CONTACTED, CLOSED)
created_at — timestamp
 updated_at — timestamp

### Conversations

id — uuid, PK
listing_id — uuid, FK → property_listings
buyer_user_id — uuid, FK → users
owner_user_id — uuid, FK → users
last_message_id — uuid, nullable, FK → messages
last_message_at — timestamp, nullable
created_at — timestamp
updated_at — timestamp
UNIQUE constraint on (listing_id, buyer_user_id, owner_user_id)

### Messages

id — uuid, PK
conversation_id — uuid, FK → conversations
sender_id — uuid, FK → users
message_type — enum (TEXT, IMAGE)
content — text, nullable
media_url — varchar, nullable
is_read — boolean, default false
created_at — timestamp
updated_at — timestamp

### Message_Reads

id — uuid, PK
message_id — uuid, FK → messages
user_id — uuid, FK → users
read_at — timestamp
UNIQUE constraint on (message_id, user_id)

### User_Blocks

id — uuid, PK
blocker_user_id — uuid, FK → users
blocked_user_id — uuid, FK → users
reason — varchar, nullable
created_at — timestamp
updated_at — timestamp, nullable
deleted_at — timestamp, nullable

### Helpers

id — uuid, PK
society_id — uuid, FK → societies
name — varchar
phone — varchar, not null
helper_type — enum (MAID, COOK, DRIVER, CLEANER, ELECTRICIAN, PLUMBER, SECURITY, OTHER)
photo_url — varchar, nullable
documents — text array, nullable
is_verified — boolean, default false
is_active — boolean, default true
created_at — timestamp
updated_at — timestamp
deleted_at — timestamp, nullable

### Helper_Unit_Map

id — uuid, PK
helper_id — uuid, FK → helpers
unit_id — uuid, FK → units
resident_id — uuid, FK → residents
work_type — enum (FULL_TIME, PART_TIME)
time_slot — varchar, nullable
is_active — boolean, default true
created_at — timestamp
updated_at — timestamp







