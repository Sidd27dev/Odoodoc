
# ERP & Odoo Ecosystem

## What is ERP?

**ERP (Enterprise Resource Planning)** is a type of software used by organizations to manage and integrate the core processes of their business such as:

- Accounting
- Sales
- Inventory
- Human Resources
- Procurement
- Manufacturing

ERP systems centralize data, automate processes, and improve cross-departmental communication.

## Odoo in the ERP Landscape

**Odoo** is a powerful open-source ERP platform with a modular approach. It offers a wide range of business applications that are fully integrated:

- CRM
- Sales
- Inventory
- Accounting
- Project Management
- eCommerce
- And many more

### Why Odoo?

- Open-source & flexible
- Modular (install only what you need)
- Web-based with a modern UI
- Large community & enterprise support

---

# Odoo Editions: Community vs Enterprise

| Feature                  | Community Edition                        | Enterprise Edition                        |
|--------------------------|-------------------------------------------|-------------------------------------------|
| **License**             | LGPL v3 (Free & Open Source)              | Proprietary (Paid, subscription-based)    |
| **Features**            | Basic ERP functionalities                 | Advanced modules (Studio, VOIP, Helpdesk, etc.) |
| **Support**             | Community support                         | Official support from Odoo S.A.           |
| **Hosting**             | Self-hosted only                          | Odoo Online, Odoo.sh, or self-hosted      |
| **UI/UX Enhancements**  | Basic                                     | Enhanced with mobile/responsive views     |
| **Upgrades**            | Manual                                    | Handled by Odoo (with version upgrades)   |

### Use Case

- **Community**: Best for tech-savvy businesses or developers who want customization without licensing costs.
- **Enterprise**: Best for companies that need full features, official support, and cloud hosting.

---

# Odoo Architecture

Odoo’s architecture is modular, scalable, and web-based. It’s built primarily using the following technologies:

## Core Components

- **PostgreSQL**: Default and only supported database engine. Stores all data.
- **Python**: Main programming language used for business logic.
- **XML**: Used to define UI (views), workflows, and data structures.
- **ORM (Object-Relational Mapping)**: Abstracts direct DB access. Developers use Python objects instead of raw SQL.

## Structure Overview

- **Presentation Layer**: Web client (frontend) using JavaScript, QWeb templating.
- **Application Layer**: Python modules defining models, business logic.
- **Data Layer**: PostgreSQL database managed via ORM.

---

# MVC Pattern in Odoo

Odoo follows the **MVC (Model-View-Controller)** design pattern:

## 1. Model

- Defined in **Python**.
- Represents business logic and data structure.
- Interacts with the PostgreSQL database via ORM.

```python
class Product(models.Model):
    _name = 'product.product'
    name = fields.Char(string="Product Name")
    price = fields.Float(string="Price")
````

## 2. View

* Defined in **XML**.
* Manages how data is displayed (forms, lists, kanban, etc.).

```xml
<record id="view_product_form" model="ir.ui.view">
    <field name="model">product.product</field>
    <field name="arch" type="xml">
        <form string="Product">
            <field name="name"/>
            <field name="price"/>
        </form>
    </field>
</record>
```

## 3. Controller

* Defined in **Python**.
* Manages interaction between models and views (especially for website, portal, or REST API).

```python
from odoo import http

class MyController(http.Controller):
    @http.route('/hello', auth='public')
    def hello(self, **kw):
        return "Hello, world!"
```

---

```

---
