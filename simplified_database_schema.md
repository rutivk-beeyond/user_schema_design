# TECH STACK
- Mobile: React Native (Expo)
- Web: Next.js + Tailwind
- Backend: Express.js (Node)
- DB: PostgreSQL
- ORM: Prisma
- Auth: JWT + Refresh Tokens
- OTP: Redis
- Realtime: Socket.IO
- Chat: Socket.IO + Redis
- Notifications: OneSignal

---

# USERS
- id (uuid, PK)
- name
- email (unique, nullable)
- phone (unique)
- avatar_url
- is_active (bool)
- isPhoneVerified (bool)
- isEmailVerified (bool)
- created_at, updated_at, deleted_at

# SESSIONS
- id (uuid, PK)
- user_id (FK)
- refresh_token (unique)
- ip_address
- expires_at
- created_at

# SOCIETIES
- id (uuid, PK)
- name, reg_number
- secretary, chairperson, gst_no
- address, city, state, pincode
- logo_url
- admin_id (FK users)
- subscriptionPlanId (FK)
- subscriptionPlanStartDate, subscriptionPlanEndDate
- is_active
- created_at, updated_at, deleted_at

# SUBSCRIPTION_PLANS
- id (uuid, PK)
- name
- pricing
- duration (Monthly | Quarterly | Half-Yearly | Yearly)
- is_active
- created_at, updated_at

# MODULES
- id (uuid, PK)
- name, description
- is_active
- created_at, updated_at

# SUBSCRIPTION_MODULE_CONFIGS
- id (uuid, PK)
- subscription_id (FK)
- module_id (FK)
- is_enabled
- created_at, updated_at

# WINGS
- id (uuid, PK)
- society_id (FK)
- name
- total_floors
- created_at, deleted_at

# UNITS
- id (uuid, PK)
- society_id, wing_id (FK)
- flat_number, floor, sqft
- type (BHK1–4, PENTHOUSE, SHOP, OFFICE)
- status (OCCUPIED, VACANT, RENOVATION)
- owner_name
- created_at, updated_at, deleted_at

# RESIDENTS
- id (uuid, PK)
- user_id, society_id, unit_id (FK)
- type (OWNER, TENANT, FAMILY)
- relation
- move_in, move_out
- created_at, updated_at, deleted_at

# GUARDS
- id (uuid, PK)
- user_id, society_id (FK)
- role (HEAD, SUPERVISOR, GUARD)
- documents[]
- is_active
- created_at, updated_at, deleted_at

# VEHICLES
- id (uuid, PK)
- society_id, unit_id, resident_id (FK)
- number, type
- parking_slot
- created_at, deleted_at

# VISITORS
- id (uuid, PK)
- society_id, unit_id, resident_id (FK)
- name, phone, photo_url
- purpose + note
- type (PREBOOKED, WALKIN, FREQUENT)
- status (PENDING → CHECKED_OUT)
- qr_token
- valid_from, valid_until
- approval_method (APP, OTP, QR, MANUAL)
- approved_by_guard_shift_id
- approved_at
- entry_time, exit_time
- created_at, updated_at

# GATES
- id (uuid, PK)
- society_id (FK)
- name
- type (ENTRY, EXIT, BOTH)
- is_active
- created_at, updated_at, deleted_at

# SHIFTS
- id (uuid, PK)
- society_id (FK)
- name
- start_time, end_time
- is_active
- created_at, updated_at

# GUARD_SHIFTS
- id (uuid, PK)
- guard_id, society_id, shift_id, gate_id (FK)
- start_time, end_time
- status (SCHEDULED, ACTIVE, DONE)
- assigned_by
- created_at, updated_at

# VISITOR_LOGS
- id (uuid, PK)
- society_id, visitor_id (FK)
- gate_id, guard_shift_id (FK)
- check_in, check_out
- vehicle_no
- entry_method (QR, OTP, MANUAL)
- status (IN, OUT)
- created_at

# GUARD_ATTENDANCE
- id (uuid, PK)
- guard_id, society_id (FK)
- guard_shift_id
- check_in, check_out
- created_at

# COMPLAINTS
- id (uuid, PK)
- society_id, resident_id (FK)
- category, title, description
- photos[]
- status (OPEN → CLOSED)
- priority (LOW, MEDIUM, HIGH)
- resolved_by, resolved_at
- remarks
- created_at, updated_at

# NOTICES
- id (uuid, PK)
- society_id, created_by (FK)
- title, body, category
- attachments[]
- onclick_link
- is_pinned
- published_at, expires_at
- created_at, updated_at, deleted_at

# AMENITIES
- id (uuid, PK)
- society_id (FK)
- name, description, photo
- capacity, location
- booking_fee, available_to
- open_time, close_time
- is_active
- created_at, deleted_at

# AMENITY_BOOKINGS
- id (uuid, PK)
- amenity_id, resident_id (FK)
- date, slot_start, slot_end
- status
- cancel_reason
- created_at, updated_at
- UNIQUE (amenity_id + date + slot_start)

# MAINTENANCE_BILLS
- id (uuid, PK)
- society_id, unit_id (FK)
- base (sqft/unit)
- month, year
- total_amount, due_date, penalty
- status
- invoice_url
- generated_at, paid_at
- created_at, updated_at

# PAYMENTS
- id (uuid, PK)
- bill_id (FK)
- resident_id, society_id, unit_id
- amount
- method_category (ONLINE/OFFLINE)
- method (UPI, CARD, CASH, etc.)
- transaction_id
- status
- receipt_no, receipt_url
- paid_at, created_at

# ACTIVITY_LOGS
- id
- society_id
- actor_id, actor_role, actor_name
- event_type, target_type, target_id
- metadata
- device_info
- timestamp (TTL 1 year)

# COMMUNITY (POSTS, COMMENTS, REACTIONS)
Posts:
- id, society_id, resident_id
- type, content, media[]
- is_pinned, is_anonymous
- status
- created_at, updated_at, deleted_at

Comments:
- id, post_id, resident_id
- parent_id
- content
- status
- created_at, updated_at

Reactions:
- id, resident_id
- target_type, target_id
- reaction
- UNIQUE (resident + target)

# POLLS
- poll, options, votes (1 vote/user)

# EVENTS
- id, post_id
- title, description, venue
- event_date, cover_photo
- RSVPs (GOING / NOT_GOING)

# PROPERTY
Listings:
- id, society_id, unit_id
- owner_resident_id
- type (RENT/SALE)
- title, description
- price, deposit, maintenance
- furnishing
- rooms, area, floor
- images[]
- status
- created_at, updated_at

Favorites / Inquiries supported

# CHAT
Conversations:
- id, listing_id
- buyer_id, owner_id
- last_message
- created_at, updated_at

Messages:
- id, conversation_id
- sender_id
- type (TEXT/IMAGE)
- content/media
- is_read
- created_at

# HELPERS
- id, society_id
- name, phone
- type (MAID, COOK, etc.)
- photo, documents[]
- is_verified, is_active
- created_at, updated_at

# HELPER_UNIT_MAP
- id
- helper_id, unit_id, resident_id
- work_type (FULL/PART)
- time_slot
- is_active
- created_at, updated_at
