# API Integrations Presentation

[![Cover](https://i.imgur.com/44ZVTYo.png)](https://i.imgur.com/44ZVTYo.png)

## 🔧 API Request Types Overview

| Type       | Purpose                   | Example Use Case              |
| ---------- | ------------------------- | ----------------------------- |
| **GET**    | Retrieve data             | Fetch user profile, task info |
| **POST**   | Create a new resource     | Create a new ticket or task   |
| **PUT**    | Update an entire resource | Update all fields of a task   |
| **PATCH**  | Update part of a resource | Update task status only       |
| **DELETE** | Remove a resource         | Delete a task or user         |

---

## 💬 Intercom API Examples

> Goal: Retrieve and update contact data by ID

### 🔹 GET Contact by ID

**cURL Example:**

```bash
curl --request GET \
  --url https://api.intercom.io/contacts/676fc2b9d1c63403acf97041 \
  --header 'Authorization: Bearer <access_token>' \
  --header 'Accept: application/json'
```

### 🔹 Update Contact Tier

**cURL Example:**

```bash
curl --request PUT \
  --url https://api.intercom.io/contacts/676fc2b9d1c63403acf97041 \
  --header 'Authorization: Bearer <access_token>' \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --data '{
    "custom_attributes": {
      "Tier": "Tier 4"
    }
  }'
```

---

## ✅ Asana API Examples

> Goal: Retrieve task or project information by ID using real values

### 🔹 Get Task by ID (Task ID: `1210916236149844`)

**cURL Example:**

```bash
curl --request GET \
  --url https://app.asana.com/api/1.0/tasks/1210916236149844 \
  --header 'Authorization: Bearer <personal_access_token>'
```

### 🔹 Get Project by ID (Project ID: `1207140969468447`)

**cURL Example:**

```bash
curl --request GET \
  --url https://app.asana.com/api/1.0/projects/1207140969468447 \
  --header 'Authorization: Bearer <personal_access_token>'
```

---

## 📊 Zignaly API Examples

> Goal: Access trader service and score info from Zignaly

### 🔹 Get Service Info

**cURL Example:**

```bash
curl --request GET \
  --url https://api.zignaly.com/services/648a6d2a4bd3382eea09432d
```

### 🔹 Get Score Info

**cURL Example:**

```bash
curl --request GET \
  --url https://api.zignaly.com/services/648a6d2a4bd3382eea09432d/score-info
```

---

## 🔄 Zapier Integrations

> Goal: Automate workflows between Asana, Intercom, and Slack with practical, real-world examples.

### 🔌 Common Zapier Use Cases:

| Trigger                           | Action                  | Description                                                                                         |
| --------------------------------- | ----------------------- | --------------------------------------------------------------------------------------------------- |
| New ticket in Intercom            | Create task in Asana    | Automatically create an Asana task for follow-up whenever a new Intercom ticket is created.         |
| Agent unresponsive in Intercom    | Send message in Slack   | Notify a Slack channel if an Intercom agent doesn't respond within a set time.                      |
| Emoji added to message in Slack   | Create task in Asana    | Use emoji reactions as lightweight triggers to spin off tasks in Asana.                             |
| New message added to Slack thread | Add note in Intercom    | Automatically log Slack thread replies as notes in the associated Intercom conversation.            |
| Task completed in Asana           | Send message in Slack   | Celebrate or notify completion of important tasks with an automatic Slack message.                  |
| Incident completed in Asana       | Update Intercom tracker | Once an incident task is marked completed, automatically update the linked Intercom tracker ticket. |

### ⚙️ Setup Steps:

1. Choose the **trigger app** and event
2. Choose the **action app** and desired action
3. Connect accounts using API keys or OAuth
4. Map input/output fields between services
5. Test & turn on your Zap
