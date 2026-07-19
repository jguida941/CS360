# Mobile App Design: Inventory Management App

## App Goals

For the Mobile2App project I chose the **Inventory App**, which tracks items in a
warehouse. I picked it for two reasons that are honest to my background. First, I
worked as a warehouse associate, so I have lived the exact problems this app is meant
to solve. Second, most of the technical pieces the client is asking for are things I
have already built in other projects, so I can focus on doing them well on Android
instead of learning them for the first time.

The overarching purpose of the app is to give warehouse staff a fast, reliable way to
see what is in stock, change those numbers as items move, and get warned before
anything runs out. On the floor, the failure I saw most often was a simple one:
someone would go to pull an item and there would be zero of it, with no warning until
the moment it was needed. The client's requirements map directly onto that problem,
and each one becomes a concrete component:

- **A database with two tables (inventory items and user logins).** This is the backbone. I would implement it as a local SQLite database, storing users separately from items so credentials stay isolated from inventory data. I designed a very similar schema in my `contact-suite-spring-react` project, where I built a persistence layer with per-user data isolation and 17 database migrations, and in my CS-340 Grazioso Salvare dashboard, where `animal_shelter.py` is a reusable CRUD module over a database.
- **A login screen that also creates an account for first-time users.** A single screen checks the users table and, if the login does not exist, inserts a new one. I have already implemented authentication with per-user isolation using JWT in my contact suite, so "authenticate an existing user or register a new one" is familiar territory; on Android it becomes an Activity backed by the users table.
- **A grid that displays every item in the inventory.** This is the main screen, a `RecyclerView` with a grid layout reading rows out of the items table. My CS-340 dashboard did essentially this: it pulled records from a database and displayed them in an interactive, filterable table, so I have built the "grid of database records" pattern before.
- **Add and remove items, and increase or decrease a specific item's count.** These are standard create, delete, and update operations against the items table, exposed as buttons in the UI. My contact suite is built entirely around validated add, update, and delete with atomic updates and clear error messages, which is the same discipline an inventory app needs so counts never go corrupt.
- **Notify the user when an item's quantity reaches zero.** When an update drops a count to zero, the app raises an alert, using either a local notification or an SMS message with the user's permission. Threshold-triggered alerts are something I built into my Raspberry Pi smart thermostat, where sensor readings crossing a limit drove LED and status feedback, so "a value crosses a boundary, so fire a signal" is not new to me.

At a high level, the design is three screens over one data layer. The user opens to a
**login and registration screen**; on success they land on the **inventory grid**;
tapping an item opens an **edit view** for changing the count or removing it, with a
dedicated action for adding a new item. Underneath, a single SQLite database with an
items table and a users table is the source of truth, and the UI is always redrawn
from that data. Keeping the model separate from the screens is a pattern I am
reinforcing right now in this course's Lights Out work, where the game's state lives
in a model class and the Activity only reflects it. That separation is what makes the
zero-quantity alert trustworthy: the notification fires off the real stored count, not
off whatever happens to be on the screen.

## Competitive App Comparison

Two existing apps aim at similar goals but from different angles.

**Square (Square for Retail).** Square is a point-of-sale platform first, with
inventory tracking attached. It stores items in a catalog, decrements counts
automatically as sales are rung up, supports multiple staff logins, and can send
low-stock alerts. Its strength is that inventory is tied to real transactions, so
counts update without anyone thinking about it. Its weakness, for this scenario, is
that it is heavy: it assumes you are selling through Square's register and payment
ecosystem, which a plain warehouse does not need. My app is narrower on purpose. It
just tracks stock and warns on zero, without the retail and payment machinery.

**Sortly.** Sortly is a dedicated, mobile-first inventory app built for exactly the
kind of person who is walking a warehouse with a phone. It leans on photos, QR and
barcode labels, nested folders, and low-stock notifications, and it is designed to be
updated in the field rather than at a desk. It is much closer to my target use case
than Square is. Where I would differentiate is scope and simplicity: Sortly is a broad
commercial product with subscription tiers and cloud sync, while my app is a focused,
self-contained tool with a login, a grid, quantity controls, and a zero-count alert.
It is the smallest thing that solves the associate's actual problem. Studying both
shows me the two poles I am between: Square proves inventory is most reliable when it
is tied to the events that change it, and Sortly proves the interface has to be fast
and phone-first for someone who is not sitting at a computer.

## Target Users

Based on the warehouse scenario, I see three user types defined by what they are
trying to accomplish.

1. **The warehouse associate (floor user).** This is who I was. Their goal is speed: adjust a count the moment an item is picked or received, add a new item without friction, and not get blindsided by a zero. They engage with the app many times a day, in short bursts, often one-handed while doing something else. For them the increase and decrease controls and the add flow have to be immediate, and the grid has to be scannable at a glance.
2. **The inventory or warehouse manager.** Their goal is prevention. They live in the grid overview and in the zero-quantity alerts because their job is to keep the shelves from running dry. They engage less frequently than the associate but more deliberately, checking overall stock health and reacting to alerts. The notification feature is built primarily for them.
3. **The business owner or administrator.** Their goal is control and visibility. They care that the right people have logins, that the data is trustworthy, and that they can glance at stock status without doing the day-to-day entry. They are occasional users who value the account system and the reliability of the counts more than the moment-to-moment editing.

Across all three, the app fits into a workday, not leisure time. A user opens it
because a physical thing moved or a number needs checking, uses it for seconds to
minutes, and closes it. That is the lifestyle fit I am designing for: a quiet,
dependable tool that prevents the small failures that keep a warehouse from running
smoothly.
