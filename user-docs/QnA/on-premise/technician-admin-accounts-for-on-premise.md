---
title: Technician admin accounts for On-Premise
description: Create unlimited technician accounts via SQL INSERT. Change login, title, and password post-creation via Admin Panel. No additional charge
---

# Technician admin accounts for On-Premise

## Can I have technician admin accounts for On-Premise?

Yes. To create a new technician, execute the following SQL statement in the database:

```sql
INSERT INTO google.dealers
SET parent_id=1,
    title='Tech role',
    last_name='Tech role',
    details='Tech role',
    role='dealer_support',
    paas_type=4,
    reg_date=CURDATE(),
    login='desired_login',
    `password`='9f2c8a4a10364437a3fc58376e912f673936de3b', -- ChangeMe!
    email='';
```

Change `login` to anything (for example `tech1`).

Change `title`, `last_name`, and `details` so the account is unique.

Hash in password corresponds to `ChangeMe!` password. After the query is executed, log in to the new technician account in the Admin Panel and change this password (or tell the customer to do it).

## How many technician accounts can I have? Does Navixy charge for it?

The number of technician accounts is unlimited. Create as many as you need for your tasks.

Technician accounts are free of charge.

## Why can't I change the technician's password? I don't see that option.

Your platform is probably old and hasn't been updated in a long time. The password change option was introduced in 2023, and if your instance is older than that, you need to update it.
