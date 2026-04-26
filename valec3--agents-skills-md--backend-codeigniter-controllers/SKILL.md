---
name: backend-codeigniter-controllers
description: Controller patterns, request handling, RESTful design. Use when this capability is needed.
metadata:
  author: valec3
---

# Backend CodeIgniter Controllers

## When to use this skill
- Creating API endpoints
- Handling HTTP requests
- RESTful controllers
- Resource controllers

## Workflow
- [ ] Extend ResourceController for REST
- [ ] Keep controllers thin
- [ ] Validate input
- [ ] Return appropriate responses
- [ ] Handle errors properly

## Instructions

### REST Controller
```php
<?php

namespace App\Controllers;

use CodeIgniter\RESTful\ResourceController;

class UserController extends ResourceController
{
    protected $modelName = 'App\Models\UserModel';
    protected $format = 'json';

    public function index()
    {
        return $this->respond($this->model->findAll());
    }

    public function show($id = null)
    {
        $user = $this->model->find($id);
        if (!$user) {
            return $this->failNotFound();
        }
        return $this->respond($user);
    }

    public function create()
    {
        $rules = ['email' => 'required|valid_email'];
        if (!$this->validate($rules)) {
            return $this->failValidationErrors($this->validator->getErrors());
        }

        $id = $this->model->insert($this->request->getJSON(true));
        return $this->respondCreated(['id' => $id]);
    }
}
```

## Resources
- Use ResourceController for REST
- Validate all input
- Return proper HTTP status

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
