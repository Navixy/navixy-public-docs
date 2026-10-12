---
title: Billing
description: The account's invoices, payments, and the service plans its devices are on.
---

# Billing

Billing covers what the account owes and how it pays: invoices raised against it, the providers it can pay through, recurring payments, and the history of both. It also covers the service plans that decide what each device is allowed to do.

An integration usually needs [Transaction](transaction.md) to read history and [Tariff (plan)](tariff/README.md) to find out what a device's plan permits. Raising and settling invoices is the rest.

## Operations in this section

<!-- endpoint-reference:start -->

#### Bill

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/bill/create`](bill.md#post-bill-create) | POST | Create bill |
| [`/bill/list`](bill.md#post-bill-list) | POST | List bills |

#### Payment system

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/payment_system/list`](payment_system.md#post-payment_system-list) | POST | List payment systems |
| [`/payment_system/estimate/get`](payment_system.md#post-payment_system-estimate-get) | POST | Get payment estimate |

#### Subscription

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/subscription/avangate/cancel`](subscription.md#post-subscription-avangate-cancel) | POST | Cancel subscription |
| [`/subscription/avangate/list`](subscription.md#post-subscription-avangate-list) | POST | List subscriptions |

#### Transaction

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/transaction/list`](transaction.md#post-transaction-list) | POST | List transactions |

#### Tariff (plan)

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tariff/list`](tariff/README.md#post-tariff-list) | POST | List plans |

#### Tracker tariff

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tariff/tracker/change`](tariff/tariff_tracker.md#post-tariff-tracker-change) | POST | Change tracker plan |
| [`/tariff/tracker/list`](tariff/tariff_tracker.md#post-tariff-tracker-list) | POST | List plans for tracker |

<!-- endpoint-reference:end -->
