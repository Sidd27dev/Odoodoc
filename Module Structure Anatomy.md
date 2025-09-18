# Module Structure Anatomy

A professional guide to the anatomy of an Odoo module, covering every directory and file with examples and best practices.

---

### 📦 **Module Structure Overview**

```
your_module_name/
│
├── __manifest__.py          # Module descriptor (metadata, dependencies, etc.)
├── __init__.py              # Module package initializer
│
├── models/                  # Business logic (Python models)
│   └── __init__.py          # Imports model files
│   └── your_model.py        # Model definitions (e.g., models.Model)
│
├── controllers/             # Website or REST controllers
│   └── __init__.py
│   └── your_controller.py
│
├── wizards/                 # Transient models (wizards, popups)
│   └── __init__.py
│   └── your_wizard.py
│
├── views/                   # XML views (tree, form, kanban, menus, etc.)
│   └── your_model_views.xml
│   └── menu.xml
│
├── security/                # Access controls and security rules
│   └── ir.model.access.csv
│   └── security.xml
│
├── data/                    # Data files (defaults, demo data, sequences, etc.)
│   └── default_data.xml
│
└── static/                  # Static files (JS, CSS, images, etc.)
    └── src/
        └── js/
        └── css/
        └── img/
```

---

## 🔧 1. `__manifest__.py` — **Module Descriptor**

### ✅ Purpose:
Defines metadata about the module (name, version, category), dependencies, data files, and other configuration settings.

### 🧩 Example:
```python
{
    'name': 'Custom Sales Enhancer',
    'version': '1.0.0',
    'summary': 'Enhances sales order workflow',
    'description': """A custom module to add features to Sales Orders.""",
    'author': 'Your Name or Company',
    'website': 'https://yourcompany.com',
    'category': 'Sales',
    'depends': ['sale', 'stock'],
    'data': [
        'security/ir.model.access.csv',
        'views/sale_order_views.xml',
        'views/menu.xml',
        'data/default_settings.xml',
    ],
    'assets': {
        'web.assets_backend': [
            'custom_sales/static/src/js/custom.js',
        ]
    },
    'application': True,
    'installable': True,
    'auto_install': False,
    'license': 'LGPL-3',
}
```

### 🧠 Tips:
- The `depends` list **must** include any Odoo module your code relies on (like `base`, `sale`, `stock`, `mail`, etc.)
- `data`: Load order matters. Security files **first**.
- `application=True`: Makes it appear on the apps list in Odoo UI.

---

## 📂 2. `__init__.py` — **Python Package Initializer**

### ✅ Purpose:
Tells Python (and Odoo) which submodules to load when your module is initialized.

### 🧩 Example:
```python
from . import models
from . import controllers
from . import wizards
```
Each of these directories should also have their own `__init__.py`.

---

## 📁 3. `models/` — **Business Logic Layer**

### ✅ Purpose:
This is where all core models and logic reside (usually inherited from `models.Model`).

### 🧩 File: `models/__init__.py`
```python
from . import sale_order
```

### 🧩 File: `models/sale_order.py`
```python
from odoo import models, fields, api

class SaleOrder(models.Model):
    _inherit = 'sale.order'

    x_custom_note = fields.Text(string="Custom Note")

    @api.depends('order_line.price_total')
    def _compute_total_custom(self):
        for order in self:
            order.total_custom = sum(order.order_line.mapped('price_total'))
```

### 🧠 Tips:
- Use `_inherit` to extend existing models.
- Use `_name` to create a new model (e.g., `'my.module.model'`).
- Decorators:
  - `@api.model` — class-level, no recordset.
  - `@api.multi` — (pre-Odoo 13, deprecated).
  - `@api.depends()` — triggers recompute of computed fields.
  - `@api.onchange()` — reacts to UI changes.

---

## 📁 4. `controllers/` — **HTTP Layer (Frontend/API)**

### ✅ Purpose:
Defines custom HTTP routes for frontend pages, JSON-RPC endpoints, REST APIs, etc.

### 🧩 File: `controllers/__init__.py`
```python
from . import main
```

### 🧩 File: `controllers/main.py`
```python
from odoo import http
from odoo.http import request

class MyController(http.Controller):

    @http.route('/my_module/hello', type='http', auth='public', website=True)
    def hello_world(self, **kw):
        return "<h1>Hello Odoo World!</h1>"

    @http.route('/api/sale_order', type='json', auth='user')
    def get_sale_orders(self):
        orders = request.env['sale.order'].sudo().search([], limit=10)
        return [{'name': o.name, 'amount': o.amount_total} for o in orders]
```

### 🧠 Tips:
- Use `type='http'` for frontend routes (HTML).
- Use `type='json'` for APIs (returns JSON).
- `auth='public'` or `'user'` controls access.

---

## 📁 5. `wizards/` — **Transient Models for Dialogs & Popups**

### ✅ Purpose:
Temporary models used for guided interactions, like confirmation dialogs or assistants.

### 🧩 File: `wizards/__init__.py`
```python
from . import confirm_cancel_wizard
```

### 🧩 File: `wizards/confirm_cancel_wizard.py`
```python
from odoo import models, fields

class ConfirmCancel(models.TransientModel):
    _name = 'confirm.cancel.wizard'
    _description = 'Confirm Cancel Wizard'

    reason = fields.Text('Reason for Cancel')

    def action_confirm(self):
        active_id = self.env.context.get('active_id')
        sale_order = self.env['sale.order'].browse(active_id)
        sale_order.write({'state': 'cancel', 'cancel_reason': self.reason})
```

### 🧠 Notes:
- Always use `TransientModel` (auto-deletes).
- Called from buttons via `context`, `res_model`, etc.

---

## 📁 6. `views/` — **XML Definitions for UI**

### ✅ Purpose:
Defines the user interface: form views, tree views, kanban, menus, actions, etc.

### 🧩 Example: `views/sale_order_views.xml`
```xml
<odoo>
  <record id="view_order_form_custom" model="ir.ui.view">
    <field name="name">sale.order.form.custom</field>
    <field name="model">sale.order</field>
    <field name="inherit_id" ref="sale.view_order_form"/>
    <field name="arch" type="xml">
      <xpath expr="//field[@name='note']" position="after">
        <field name="x_custom_note"/>
      </xpath>
    </field>
  </record>
</odoo>
```

### 🧩 Example: `views/menu.xml`
```xml
<odoo>
  <menuitem id="menu_sales_custom" name="Custom Sales" parent="sales_team.menu_sales"/>
  <act_window id="action_custom_orders" name="Custom Orders"
              res_model="sale.order" view_mode="tree,form"
              menu_id="menu_sales_custom"/>
</odoo>
```

---

## 📁 7. `security/` — **Permissions & Record Rules**

### ✅ File: `ir.model.access.csv`

### 📄 Purpose:
Grants create, read, write, delete access to specific user groups.

### 🧩 Example:
```csv
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
access_sale_order_custom,sale.order.custom,model_sale_order,base.group_user,1,1,1,0
```

### ✅ File: `security.xml`
Used for defining **groups**, **rules**, etc.

### 🧩 Example:
```xml
<odoo>
  <record id="group_sales_manager_custom" model="res.groups">
    <field name="name">Sales Manager Custom</field>
  </record>
</odoo>
```

---

## 📁 8. `data/` — **Initial Data, Scheduled Actions, Sequences**

### ✅ Purpose:
Used to preload configuration data, scheduled actions, templates, etc.

### 🧩 Example: `data/sequence.xml`
```xml
<odoo>
  <record id="seq_custom_order" model="ir.sequence">
    <field name="name">Custom Order</field>
    <field name="code">custom.order</field>
    <field name="prefix">ORD/</field>
    <field name="padding">4</field>
    <field name="number_next">1</field>
  </record>
</odoo>
```

---

## 📁 9. `static/` — **Static Assets (JS, CSS, Images)**

Used mostly in web or website modules.

### 📂 Structure:
```
static/
└── src/
    ├── js/
    │   └── my_script.js
    ├── css/
    │   └── my_styles.css
    └── img/
        └── logo.png
```

These files are included via `assets` in `__manifest__.py`.

---
