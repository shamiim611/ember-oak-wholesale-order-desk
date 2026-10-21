# Ember & Oak Wholesale Order Desk

An n8n chat assistant for Ember & Oak Coffee Roasters' wholesale customers. It answers greetings, checks coffee availability and prices, records eligible orders, and escalates requests that need Dana's review.

> **Workflow status:** The main [Ember & Oak workflow](https://salimshamiim.app.n8n.cloud/workflow/TRHaP2BycjfWos9P) is currently an unpublished n8n draft. The linked tool workflows are published.

## How it works

1. Customers send messages through an n8n chat trigger.
2. An AI Agent powered by **Google Gemini 2.5 Flash** interprets the request and follows the [agent system prompt](Wholesale%20Order%20Agent%20System%20Prompt.txt). Gemini 2.5 Flash replaced Gemini 3 Flash Preview after the preview model returned “Thought signature is not valid” errors during multi-tool calls.
3. Simple Memory retains the last 20 messages to support follow-up turns.
4. The agent uses the configured tools to look up inventory, log orders, and escalate requests.

## Tools

| Tool | Workflow | Purpose |
| --- | --- | --- |
| Inventory Lookup | `i2sLj1EkQTlDWTzR` | Looks up SKU, coffee name, origin, roast, stock in kg, price per kg, and status. Its [system prompt](Inventory%20Lookup%20Tool%20System%20Prompt.txt) defines the read-only lookup behavior. |
| Order Logger | [`Vr7wamffugQbLUn4`](https://salimshamiim.app.n8n.cloud/workflow/Vr7wamffugQbLUn4) | Appends one row per order line to `Orders.csv` in this repository and posts a new-order alert to Discord `#general`. |
| Escalate to Dana | [`dFdjTrhNYZitVxyE`](https://salimshamiim.app.n8n.cloud/workflow/dFdjTrhNYZitVxyE) | Posts an escalation to Discord `#dana` and sends an email to the configured escalation address. |

The [Order Logger](https://salimshamiim.app.n8n.cloud/workflow/Vr7wamffugQbLUn4) receives order ID, customer email and name, SKU, quantity, line total, status, and notes. The [Escalate to Dana workflow](https://salimshamiim.app.n8n.cloud/workflow/dFdjTrhNYZitVxyE) receives the category, customer and business details, request summary, escalation reason, order ID, and order total.

## Order handling rules

- A greeting by itself gets the standard [chat welcome message](Chat%20Welcome%20Message.txt); no tools are used.
- The agent checks inventory for each requested coffee before confirming or logging it. It does not negotiate or change prices.
- Discontinued coffees cannot be ordered. The agent may suggest a similar in-stock option, but only substitutes with customer agreement.
- Seasonal coffees are orderable only while inventory shows stock.
- For low or zero stock, the agent explains availability and waits for the customer's decision before logging a reduced quantity or alternative.
- Partial orders are logged with status `partial` and notes describing the requested and accepted quantities. Multi-coffee orders use one line per SKU and share an order ID.
- Orders totaling over **$500** are logged with status `pending_approval`, escalated to Dana, and described to the customer as awaiting review. Orders totaling exactly $500 do not trigger this rule.
- Complaints, billing questions, custom blend requests, discount requests, and tool or logging failures are escalated with a polite holding reply where appropriate.
- The agent does not claim that an order was logged or Dana was notified unless the corresponding tool succeeds.
- Customer replies are signed **The Ember & Oak Team**.

See the full [wholesale order policy](Wholesale%20Order%20Policy.md) for the detailed rules.

## Repository files

| File | Description |
| --- | --- |
| `Inventory.csv` | Sample inventory catalog used by the lookup tool. |
| `Orders.csv` | Order log populated by the Order Logger workflow. |
| `Customers.csv` | Customer records and usual-order information; currently contains headers only. |
| `Wholesale Order Agent System Prompt.txt` | Main agent instructions and structured response format. |
| `Inventory Lookup Tool System Prompt.txt` | Inventory tool instructions and JSON result format. |
| `Chat Welcome Message.txt` | Standard welcome text for greeting-only chat messages. |
| `Wholesale Order Policy.md` | Human-readable order handling policy. |
| `Ember & Oak Wholesale Order Desk n8n AI Agent Scenario.md` | Original scenario and acceptance requirements. |

## Data privacy

This repository is public. The Order Logger writes order records here, and those records can include customer names and email addresses. **Do not use real customer information with this public repository.** Before handling live orders, make the repository private or move order and customer data to an appropriately access-controlled storage system, and review the n8n workflows and integrations for data access and retention.

## Setup and operation

The workflow definitions run in n8n and are not exported in this repository. Configure the chat workflow, model credentials, memory, and three tool workflows in the n8n workspace using the prompts and CSV schemas here. The main workflow is still a draft; publish and test it in n8n before directing customer traffic to it.
