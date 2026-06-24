---
name: django-rest
description: Creates REST APIs on Django with Django REST Framework. Use for Python web applications with powerful admin interface.
metadata:
  author: ssrjkk
---
# Django REST Framework

> REST API on Django with powerful admin panel and ORM.

## 🚀 Quick Start
```python
# serializers.py
from rest_framework import serializers
from .models import User

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email']

# views.py
from rest_framework import viewsets
class UserViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer
```

## 📋 When to Use
- ✅ Complex web applications on Python
- ✅ Need built-in admin interface
- ❌ Not for microservices (better use FastAPI)

## 🔧 Step-by-Step Instructions
1. Install: `pip install django djangorestframework`
2. Create project: `django-admin startproject myproject`
3. Add DRF and create API views
4. Run: `python manage.py runserver`

## 📦 Dependencies
```bash
pip install django djangorestframework
```

## 🧪 Examples
Input: `GET /api/users/` 
Output: JSON array of users

## 🔗 Resources
- [DRF Docs](https://www.django-rest-framework.org/)
- [Examples](./examples/)

## ✅ Validation
1. API responds correctly
2. Admin available at `/admin/`
3. ORM queries work without errors

---
> Source: [ssrjkk/claude-skills](https://github.com/ssrjkk/claude-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
