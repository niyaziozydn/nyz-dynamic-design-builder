# 🧩 nyz-dynamic-design-builder

`nyz-dynamic-design-builder` is a lightweight Django-compatible Python utility for exporting model data (including ForeignKey and ManyToMany fields) to Excel — no `pandas` required.

---

## 📦 Installation

```bash
pip install nyz-dynamic-design-builder
```

---

## 📝 Sample Model: Blog

Assume you have a `Blog` model like this:

```python
from django.db import models
from django.contrib.auth import get_user_model

User = get_user_model()

class Blog(models.Model):
    title = models.CharField(max_length=255)
    content = models.TextField()
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    tags = models.ManyToManyField("Tag", blank=True)
    created_at = models.DateTimeField(auto_now_add=True)

class Tag(models.Model):
    name = models.CharField(max_length=100)
```

---

## ⚙️ Usage

```python
from nyz_dynamic_design_builder.exporter import export_model_to_excel

export_model_to_excel(
    file_path="blog_export.xlsx",
    model=Blog,
    field_list=["title", "content", "author", "tags", "created_at"],
    search_fk_list=["text", "name", "username", "first_name", "last_name"],
    special_fk_values={"author": ["username", "first_name", "last_name", "is_active"]},
    ignore_m2m_fields=["id", "created_at", "updated_at"]
)
```

---

## 🔍 Parameters Explained

| Parameter            | Description                                                                 |
|----------------------|-----------------------------------------------------------------------------|
| `file_path`          | Path to save the exported Excel file.                                       |
| `model`              | Django model class to export from.                                          |
| `field_list`         | Fields to export from the model (includes FK and M2M fields as needed).     |
| `search_fk_list`     | For FK fields in `field_list`, this defines the inner field search priority (e.g. `text`, `name`). |
| `special_fk_values`  | If a field in `field_list` is also in `special_fk_values`, it will extract only the listed fields. |
| `ignore_m2m_fields`  | If a field in `field_list` is M2M, this list is used to ignore specific subfields. |

---

## 🧠 Logic

- If a ForeignKey field is included in `field_list`:
  - First it checks if the field is listed in `special_fk_values`. If so, it extracts only those subfields.
  - Otherwise, it loops through `search_fk_list` to find the best matching subfield.

- If a ManyToMany field is in `field_list`:
  - All of its fields are extracted except those listed in `ignore_m2m_fields`.

---

## 📤 Output Example

Excel file might include columns like:

- Title  
- Content  
- Author - Username  
- Author - First Name  
- Author - Last Name  
- Author - Is Active  
- Tags 1 - Name  
- Tags 2 - Name  
- Created At

---

## ✅ Perfect For

- Admin Excel exports  
- Lightweight dynamic field selection  
- Human-readable relationship data without pandas

---

## 🔒 Notes

- No need to modify your model.
- Works with any Django model, respects relationships.