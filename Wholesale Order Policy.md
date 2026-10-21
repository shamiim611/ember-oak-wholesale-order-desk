# Ember & Oak Wholesale Order Policy

## Purpose and scope

This policy governs how wholesale order requests are reviewed, recorded, and answered. It is intended for the order-handling agent and the team members who review escalations. The inventory file is the source of truth for current stock, coffee status, and wholesale prices. The customer file may help identify a customer's usual order, but does not replace an inventory check.

## Standard order handling

1. Identify the sender's email, name, business, requested coffees, and quantities. If essential order details are missing or unclear, ask the customer to clarify rather than guessing.
2. Look up every requested coffee in the inventory before confirming or logging an order. Check its stock, price, and status.
3. Calculate the order total using the listed wholesale price and requested quantity. Do not change or negotiate prices.
4. Apply the rules below for unavailable stock, discontinued products, and orders requiring approval.
5. Log only confirmed orders or partial quantities the customer has agreed to. Record each coffee as its own order line, using the same order ID for multiple lines in one order. Include relevant context in `notes`.
6. Send a short, friendly reply that accurately reflects what has been checked, logged, or referred for review. Sign every customer reply:

   **The Ember & Oak Team**

## Availability and product status

- **Enough stock:** Confirm the quantity only after checking inventory, then log the order.
- **Insufficient stock:** Tell the customer the available quantity and ask whether they would like that quantity or prefer another option. Do not log an unaccepted substitution or reduced quantity. Once the customer agrees, log the accepted quantity with status `partial` and note the requested and fulfilled quantities.
- **No stock:** Do not confirm availability. Explain that the coffee is currently unavailable and ask whether the customer wants to wait or choose an alternative. Do not log an unaccepted order.
- **Discontinued:** Do not accept an order for a discontinued coffee. Suggest an in-stock alternative with a similar roast level or origin region, and ask whether the customer would like it. Do not substitute without the customer's agreement.
- **Seasonal:** Treat seasonal coffees as orderable only when inventory shows stock. Do not imply they are a year-round offering.

## Large orders and approval

- If the total order value is **over $500**, log each confirmed line with status `pending_approval` and notify Dana with the customer and business details, requested items and quantities, calculated total, and reason for escalation.
- Tell the customer the order is being reviewed and is not yet approved. Do not promise fulfillment until Dana approves it.
- Orders totaling exactly $500 or less do not require approval under this threshold rule.

## Customer lookup and “the usual”

- Match a customer by sender email against `Customers.csv`; do not rely on name alone.
- If there is one clear usual order, verify each item is active and check current stock and price in the inventory before proceeding.
- If no customer match or usual order is recorded, or if the usual is ambiguous, ask the customer what they would like. Never invent a prior order or assume an unrecorded preference.
- If the usual is unavailable or its quantity exceeds stock, follow the availability rules above and get the customer's agreement before logging a partial quantity or alternative.
- Use a customer's reply in the current conversation to resolve an earlier clarification. Do not treat conversational context as a substitute for stock checks.

## Escalation

Send the request to Dana instead of handling it as a routine order when it is:

- A complaint, billing question, custom blend request, or another non-order inquiry.
- A request for a discount or other price negotiation.
- A confirmed order over $500 that requires approval.
- An issue the agent cannot resolve reliably, including unclear requests that remain unresolved.
- A workflow or inventory/order logging failure.

An escalation message should include the customer's name and email, business if known, what they asked for, and the reason for escalation. For complaints or other non-order inquiries, send the customer a polite holding reply that says the team will follow up; do not promise a resolution or log an order.

## Records and failures

- For each order line, record `order_id`, `timestamp`, `customer_email`, `customer_name`, `sku`, `quantity_kg`, `line_total`, `status`, and useful `notes` in `Orders.csv`.
- Use `partial` only for a customer-approved reduced quantity, and `pending_approval` for confirmed orders over $500 awaiting Dana's decision.
- Do not claim an order was logged if the order log operation failed. Do not send a success-shaped confirmation when an inventory or logging step fails. Alert Dana with the failure details and send no unverified fulfillment confirmation to the customer.
- Keep customer records accurate and append or update them only with information obtained from the customer or a verified order.
