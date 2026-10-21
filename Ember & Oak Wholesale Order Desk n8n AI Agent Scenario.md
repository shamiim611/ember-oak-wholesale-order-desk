# Ember & Oak Wholesale Order Desk: n8n AI Agent Scenario

Oct 20, 2026 · @Namulema Shamiim

## Background

Ember & Oak is a small specialty coffee roaster with eight employees. Besides its own café, it sells roasted beans wholesale to about 40 local cafés, restaurants, and offices. Wholesale orders arrive by email to **orders@emberandoak.example**, and the owner, Dana, handles every one by hand.

She checks stock in a spreadsheet, replies with prices and availability, logs the order, and pings the roasting team if a batch needs roasting. This takes about two hours a day. Evening orders wait until morning, and twice last month she quoted a coffee that had already sold out.

Dana wants an AI agent to handle the routine work while she stays in control of anything unusual.

## Your task

Build an n8n workflow where an AI agent reads incoming wholesale order emails, uses **three tools** to check stock, log orders, and escalate problems, and then replies to the customer.

## The three tools

**Tool 1: Inventory Lookup.** A Google Sheet called `Inventory` that the agent reads to check availability, stock, and wholesale price.

| sku | coffee\_name | origin | roast | stock\_kg | price\_per\_kg ($) | status |
| --- | --- | --- | --- | --- | --- | --- |
| EO-101 | Morning Ember | Brazil | Medium | 42 | 18.50 | active |
| EO-102 | Highland Bloom | Ethiopia | Light | 8 | 26.00 | active |
| EO-103 | Midnight Oak | Sumatra | Dark | 0 | 21.00 | active |
| EO-104 | Cascade Decaf | Colombia | Medium | 15 | 22.50 | active |
| EO-105 | Holiday Reserve | Guatemala | Medium | 30 | 28.00 | seasonal |
| EO-106 | Red Canyon | Kenya | Light | 12 | 29.50 | discontinued |

**Tool 2: Order Logger.** A second sheet called `Orders` where the agent appends a row for each confirmed order. Columns: `order_id, timestamp, customer_email, customer_name, sku, quantity_kg, line_total, status, notes`.

**Tool 3: Escalate to Dana.** Sends Dana a message on Slack, Telegram, or email (your choice) when the agent can't or shouldn't handle something alone. It includes who the customer is, what they asked for, and why it was escalated.

## Business rules the agent must follow

1. Never confirm an order without checking stock first.
2. If the requested quantity exceeds stock, offer what's available and log the order with status `partial`, or ask the customer how they'd like to proceed.
3. Orders over **$500** are logged with status `pending_approval` and Dana is notified. The customer is told the order is being reviewed.
4. Discontinued coffees cannot be ordered. Suggest a similar coffee (same roast level or origin region).
5. Anything that isn't an order (a complaint, billing question, or custom blend request) goes straight to Dana. The customer gets a polite holding reply.
6. The agent never negotiates prices. Discount requests are escalated.
7. Every reply is signed "The Ember & Oak Team" and stays friendly and short.

## Test emails

Run all seven through your workflow. Each tests a different rule.

| Email | From | Message | Tests |
| --- | --- | --- | --- |
| A | Jamal, Sunrise Bagels | "Hi, can we get 10 kg of Morning Ember for this week? Thanks!" | Simple order |
| B | Priya, The Reading Room Café | "We'd like 12 kg of Highland Bloom and 5 kg of Cascade Decaf." | Partial stock |
| C | Marco, Hilltop Offices | "Please send 6 kg of Midnight Oak and 4 kg of Red Canyon." | Out of stock and discontinued |
| D | Lena, Grand Plaza Hotel | "We're stocking up for the holidays. 25 kg of Holiday Reserve, please." | Large order over $500 |
| E | Sam, Corner Deli | "Our last bag of Morning Ember tasted stale. Not happy about this." | Not an order (complaint) |
| F | Jamal, Sunrise Bagels | "If we order 30 kg a month, can you do a better price?" | Discount request |
| G | Ana, Bloom Florist & Coffee | "Can I get the usual?" | Vague request |

Email G is the tricky one. Think about what the agent should do when it lacks information, and whether memory could help.

## Intermediate requirements

These push the build beyond a basic agent workflow.

- [ ] **Trigger:** A Gmail trigger (or a webhook or form) that fires on new messages to the orders inbox.
- [ ] **Pre-processing:** Extract and clean sender name, email, and body with a Set or Code node, and filter out auto-replies and newsletters before the agent runs.
- [ ] **Memory:** Memory keyed to the customer's email, so Priya's follow-up ("Okay, send the 8 kg then") connects to her earlier message.
- [ ] **Structured output:** The agent returns JSON (for example `reply_text`, `action_taken`, `escalated: true/false`) that drives the next steps.
- [ ] **Branching:** An IF or Switch node on the agent's output decides whether the reply sends immediately or is saved as a draft for Dana.
- [ ] **Error handling:** If a Google Sheets call fails, no broken reply reaches the customer. An error path or error workflow alerts Dana.
- [ ] **System prompt:** A clear system prompt that encodes the business rules. Most of the agent's quality comes from this.

## Success criteria

The workflow is done when all seven test emails produce the right outcome: correct replies, correct rows in `Orders` with the right status, and Dana notified exactly when she should be, and not otherwise.

## Stretch goals (optional)

- [ ] A daily 5 p.m. scheduled workflow that summarizes the day's orders and sends Dana a report.
- [ ] Automatically reduce `stock_kg` in `Inventory` when an order is logged.
- [ ] A fourth tool that checks a `Customers` sheet so "the usual" in Email G can be resolved.
