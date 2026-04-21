---
name: spring-thymeleaf-bootstrap
description: Server-side rendering with Thymeleaf templates and Bootstrap 5 CSS framework. Use for web applications with HTML views, forms, layouts, and fragments in Spring Boot. Use when this capability is needed.
metadata:
  author: gazolla
---

# Spring Thymeleaf + Bootstrap 5

Server-side rendered web applications with Thymeleaf and Bootstrap.

## Use this skill when

- Creating web pages with server-side rendering
- Building forms with validation
- Setting up page layouts and reusable fragments
- User mentions "Thymeleaf", "templates", "Bootstrap", or "HTML pages"
- Need a web UI without JavaScript frameworks

## Do not use this skill when

- User wants SPA (React, Vue, Angular)
- User needs REST API only (use spring-boot-core)
- User asks for a different CSS framework

## Dependencies

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>

<!-- Layout Dialect for template inheritance -->
<dependency>
    <groupId>nz.net.ultraq.thymeleaf</groupId>
    <artifactId>thymeleaf-layout-dialect</artifactId>
</dependency>
```

## Configuration

### application.yml

```yaml
spring:
  thymeleaf:
    cache: ${THYMELEAF_CACHE:true}
    prefix: classpath:/templates/
    suffix: .html
    mode: HTML
    encoding: UTF-8
```

### Dev profile (disable cache)

```yaml
# application-dev.yml
spring:
  thymeleaf:
    cache: false
```

## Template Structure

```
src/main/resources/templates/
    layout/
        base.html           # Main layout
        admin.html          # Admin layout (optional)
    fragments/
        header.html         # Navigation
        footer.html         # Footer
        alerts.html         # Flash messages
        pagination.html     # Pagination component
    users/
        list.html           # User list
        form.html           # Create/Edit form
        view.html           # Detail view
    error/
        404.html            # Not found
        500.html            # Server error
    index.html              # Home page
```

## Base Layout

### layout/base.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title layout:title-pattern="$CONTENT_TITLE - My App">My App</title>
    
    <!-- Bootstrap 5 CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" 
          rel="stylesheet">
    <!-- Bootstrap Icons -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.1/font/bootstrap-icons.css" 
          rel="stylesheet">
    <!-- Custom CSS -->
    <link th:href="@{/css/custom.css}" rel="stylesheet">
    
    <!-- Additional head content -->
    <th:block layout:fragment="head"></th:block>
</head>
<body>
    <!-- Navigation -->
    <div th:replace="~{fragments/header :: navbar}"></div>
    
    <!-- Main Content -->
    <main class="container py-4">
        <!-- Flash Messages -->
        <div th:replace="~{fragments/alerts :: alerts}"></div>
        
        <!-- Page Content -->
        <div layout:fragment="content"></div>
    </main>
    
    <!-- Footer -->
    <div th:replace="~{fragments/footer :: footer}"></div>
    
    <!-- Bootstrap 5 JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
    <!-- Custom JS -->
    <script th:src="@{/js/main.js}"></script>
    
    <!-- Additional scripts -->
    <th:block layout:fragment="scripts"></th:block>
</body>
</html>
```

## Fragments

### fragments/header.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<nav th:fragment="navbar" class="navbar navbar-expand-lg navbar-dark bg-dark">
    <div class="container">
        <a class="navbar-brand" th:href="@{/}">
            <i class="bi bi-box"></i> My App
        </a>
        
        <button class="navbar-toggler" type="button" 
                data-bs-toggle="collapse" data-bs-target="#navbarNav">
            <span class="navbar-toggler-icon"></span>
        </button>
        
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav me-auto">
                <li class="nav-item">
                    <a class="nav-link" th:href="@{/}" 
                       th:classappend="${#request.requestURI == '/'} ? 'active'">
                        Home
                    </a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" th:href="@{/users}"
                       th:classappend="${#strings.startsWith(#request.requestURI, '/users')} ? 'active'">
                        Users
                    </a>
                </li>
            </ul>
            
            <ul class="navbar-nav">
                <li class="nav-item" th:if="${#authorization.expression('!isAuthenticated()')}">
                    <a class="nav-link" th:href="@{/login}">Login</a>
                </li>
                <li class="nav-item dropdown" th:if="${#authorization.expression('isAuthenticated()')}">
                    <a class="nav-link dropdown-toggle" href="#" 
                       data-bs-toggle="dropdown" th:text="${#authentication.name}">
                        User
                    </a>
                    <ul class="dropdown-menu dropdown-menu-end">
                        <li><a class="dropdown-item" th:href="@{/profile}">Profile</a></li>
                        <li><hr class="dropdown-divider"></li>
                        <li>
                            <form th:action="@{/logout}" method="post">
                                <button type="submit" class="dropdown-item">Logout</button>
                            </form>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
</nav>
</body>
</html>
```

### fragments/alerts.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<div th:fragment="alerts">
    <!-- Success -->
    <div th:if="${successMessage}" class="alert alert-success alert-dismissible fade show">
        <i class="bi bi-check-circle me-2"></i>
        <span th:text="${successMessage}">Success</span>
        <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
    </div>
    
    <!-- Error -->
    <div th:if="${errorMessage}" class="alert alert-danger alert-dismissible fade show">
        <i class="bi bi-exclamation-triangle me-2"></i>
        <span th:text="${errorMessage}">Error</span>
        <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
    </div>
    
    <!-- Info -->
    <div th:if="${infoMessage}" class="alert alert-info alert-dismissible fade show">
        <i class="bi bi-info-circle me-2"></i>
        <span th:text="${infoMessage}">Info</span>
        <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
    </div>
</div>
</body>
</html>
```

### fragments/pagination.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<nav th:fragment="pagination(page, url)" th:if="${page.totalPages > 1}">
    <ul class="pagination justify-content-center">
        <!-- Previous -->
        <li class="page-item" th:classappend="${page.first} ? 'disabled'">
            <a class="page-link" th:href="@{${url}(page=${page.number - 1})}">
                <i class="bi bi-chevron-left"></i>
            </a>
        </li>
        
        <!-- Page numbers -->
        <li th:each="i : ${#numbers.sequence(0, page.totalPages - 1)}"
            class="page-item" th:classappend="${i == page.number} ? 'active'">
            <a class="page-link" th:href="@{${url}(page=${i})}" th:text="${i + 1}">1</a>
        </li>
        
        <!-- Next -->
        <li class="page-item" th:classappend="${page.last} ? 'disabled'">
            <a class="page-link" th:href="@{${url}(page=${page.number + 1})}">
                <i class="bi bi-chevron-right"></i>
            </a>
        </li>
    </ul>
    
    <p class="text-center text-muted small">
        Showing <span th:text="${page.numberOfElements}">0</span>
        of <span th:text="${page.totalElements}">0</span> items
    </p>
</nav>
</body>
</html>
```

## Page Templates

### users/list.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorate="~{layout/base}">
<head>
    <title>Users</title>
</head>
<body>
<div layout:fragment="content">
    <div class="d-flex justify-content-between align-items-center mb-4">
        <h1><i class="bi bi-people"></i> Users</h1>
        <a th:href="@{/users/new}" class="btn btn-primary">
            <i class="bi bi-plus-lg"></i> New User
        </a>
    </div>
    
    <!-- Search -->
    <form th:action="@{/users}" method="get" class="mb-4">
        <div class="input-group">
            <input type="text" name="search" class="form-control" 
                   placeholder="Search by name or email..."
                   th:value="${search}">
            <button type="submit" class="btn btn-outline-secondary">
                <i class="bi bi-search"></i>
            </button>
        </div>
    </form>
    
    <!-- Table -->
    <div class="table-responsive">
        <table class="table table-striped table-hover">
            <thead class="table-dark">
                <tr>
                    <th>Name</th>
                    <th>Email</th>
                    <th>Role</th>
                    <th>Status</th>
                    <th>Created</th>
                    <th width="150">Actions</th>
                </tr>
            </thead>
            <tbody>
                <tr th:each="user : ${users.content}">
                    <td th:text="${user.name}">John Doe</td>
                    <td th:text="${user.email}">john@example.com</td>
                    <td>
                        <span class="badge" 
                              th:classappend="${user.role.name() == 'ADMIN'} ? 'bg-danger' : 'bg-secondary'"
                              th:text="${user.role}">USER</span>
                    </td>
                    <td>
                        <span class="badge" 
                              th:classappend="${user.active} ? 'bg-success' : 'bg-warning'"
                              th:text="${user.active} ? 'Active' : 'Inactive'">Active</span>
                    </td>
                    <td th:text="${#temporals.format(user.createdAt, 'dd/MM/yyyy')}">01/01/2024</td>
                    <td>
                        <a th:href="@{/users/{id}(id=${user.id})}" 
                           class="btn btn-sm btn-outline-info" title="View">
                            <i class="bi bi-eye"></i>
                        </a>
                        <a th:href="@{/users/{id}/edit(id=${user.id})}" 
                           class="btn btn-sm btn-outline-primary" title="Edit">
                            <i class="bi bi-pencil"></i>
                        </a>
                        <button type="button" class="btn btn-sm btn-outline-danger" 
                                title="Delete"
                                data-bs-toggle="modal" 
                                th:data-bs-target="'#deleteModal' + ${user.id}">
                            <i class="bi bi-trash"></i>
                        </button>
                        
                        <!-- Delete Modal -->
                        <div class="modal fade" th:id="'deleteModal' + ${user.id}">
                            <div class="modal-dialog">
                                <div class="modal-content">
                                    <div class="modal-header">
                                        <h5 class="modal-title">Confirm Delete</h5>
                                        <button type="button" class="btn-close" 
                                                data-bs-dismiss="modal"></button>
                                    </div>
                                    <div class="modal-body">
                                        Delete user <strong th:text="${user.name}">Name</strong>?
                                    </div>
                                    <div class="modal-footer">
                                        <button type="button" class="btn btn-secondary" 
                                                data-bs-dismiss="modal">Cancel</button>
                                        <form th:action="@{/users/{id}/delete(id=${user.id})}" 
                                              method="post" class="d-inline">
                                            <button type="submit" class="btn btn-danger">Delete</button>
                                        </form>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </td>
                </tr>
                <tr th:if="${users.empty}">
                    <td colspan="6" class="text-center text-muted py-4">
                        <i class="bi bi-inbox fs-1 d-block mb-2"></i>
                        No users found
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    
    <!-- Pagination -->
    <div th:replace="~{fragments/pagination :: pagination(${users}, '/users')}"></div>
</div>
</body>
</html>
```

### users/form.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorate="~{layout/base}">
<head>
    <title th:text="${user.id} ? 'Edit User' : 'New User'">User Form</title>
</head>
<body>
<div layout:fragment="content">
    <h1>
        <i class="bi bi-person"></i>
        <span th:text="${user.id} ? 'Edit User' : 'New User'">User Form</span>
    </h1>
    
    <div class="row">
        <div class="col-md-8">
            <div class="card">
                <div class="card-body">
                    <form th:action="${user.id} ? @{/users/{id}(id=${user.id})} : @{/users}"
                          th:object="${user}" method="post" novalidate>
                        
                        <!-- Name -->
                        <div class="mb-3">
                            <label for="name" class="form-label">Name *</label>
                            <input type="text" id="name" th:field="*{name}"
                                   class="form-control"
                                   th:classappend="${#fields.hasErrors('name')} ? 'is-invalid'">
                            <div class="invalid-feedback" th:if="${#fields.hasErrors('name')}"
                                 th:errors="*{name}">Name error</div>
                        </div>
                        
                        <!-- Email -->
                        <div class="mb-3">
                            <label for="email" class="form-label">Email *</label>
                            <input type="email" id="email" th:field="*{email}"
                                   class="form-control"
                                   th:classappend="${#fields.hasErrors('email')} ? 'is-invalid'">
                            <div class="invalid-feedback" th:if="${#fields.hasErrors('email')}"
                                 th:errors="*{email}">Email error</div>
                        </div>
                        
                        <!-- Role -->
                        <div class="mb-3">
                            <label for="role" class="form-label">Role</label>
                            <select id="role" th:field="*{role}" class="form-select">
                                <option th:each="r : ${T(com.example.app.model.User.UserRole).values()}"
                                        th:value="${r}" th:text="${r}">ROLE</option>
                            </select>
                        </div>
                        
                        <!-- Active -->
                        <div class="mb-3 form-check">
                            <input type="checkbox" id="active" th:field="*{active}"
                                   class="form-check-input">
                            <label for="active" class="form-check-label">Active</label>
                        </div>
                        
                        <!-- Buttons -->
                        <div class="d-flex gap-2">
                            <button type="submit" class="btn btn-primary">
                                <i class="bi bi-check-lg"></i> Save
                            </button>
                            <a th:href="@{/users}" class="btn btn-secondary">
                                <i class="bi bi-x-lg"></i> Cancel
                            </a>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
</body>
</html>
```

## Web Controller

```java
package com.example.app.controller.web;

import com.example.app.dto.*;
import com.example.app.service.UserService;
import jakarta.validation.Valid;
import lombok.RequiredArgsConstructor;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.web.PageableDefault;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.servlet.mvc.support.RedirectAttributes;

import java.util.UUID;

@Controller
@RequestMapping("/users")
@RequiredArgsConstructor
public class UserWebController {

    private final UserService userService;

    @GetMapping
    public String list(
            @RequestParam(required = false) String search,
            @PageableDefault(size = 20, sort = "createdAt,desc") Pageable pageable,
            Model model) {
        Page<UserResponse> users = userService.findAll(search, pageable);
        model.addAttribute("users", users);
        model.addAttribute("search", search);
        return "users/list";
    }

    @GetMapping("/new")
    public String createForm(Model model) {
        model.addAttribute("user", new CreateUserRequest("", ""));
        return "users/form";
    }

    @PostMapping
    public String create(
            @Valid @ModelAttribute("user") CreateUserRequest request,
            BindingResult result,
            RedirectAttributes redirectAttributes) {
        if (result.hasErrors()) {
            return "users/form";
        }
        
        userService.create(request);
        redirectAttributes.addFlashAttribute("successMessage", "User created successfully!");
        return "redirect:/users";
    }

    @GetMapping("/{id}")
    public String view(@PathVariable UUID id, Model model) {
        model.addAttribute("user", userService.findById(id));
        return "users/view";
    }

    @GetMapping("/{id}/edit")
    public String editForm(@PathVariable UUID id, Model model) {
        model.addAttribute("user", userService.findById(id));
        return "users/form";
    }

    @PostMapping("/{id}")
    public String update(
            @PathVariable UUID id,
            @Valid @ModelAttribute("user") UpdateUserRequest request,
            BindingResult result,
            RedirectAttributes redirectAttributes) {
        if (result.hasErrors()) {
            return "users/form";
        }
        
        userService.update(id, request);
        redirectAttributes.addFlashAttribute("successMessage", "User updated successfully!");
        return "redirect:/users";
    }

    @PostMapping("/{id}/delete")
    public String delete(@PathVariable UUID id, RedirectAttributes redirectAttributes) {
        userService.delete(id);
        redirectAttributes.addFlashAttribute("successMessage", "User deleted successfully!");
        return "redirect:/users";
    }
}
```

## Thymeleaf Expression Cheatsheet

| Expression | Description | Example |
|------------|-------------|---------|
| `${...}` | Variable | `${user.name}` |
| `*{...}` | Selection (in th:object) | `*{name}` |
| `#{...}` | Message (i18n) | `#{welcome.title}` |
| `@{...}` | URL | `@{/users/{id}(id=${user.id})}` |
| `~{...}` | Fragment | `~{fragments/header :: navbar}` |

## Code Quality Checklist

- [ ] Using layout dialect for template inheritance
- [ ] Common elements extracted to fragments
- [ ] Forms have validation feedback
- [ ] Flash messages for user feedback
- [ ] Tables have empty state
- [ ] Delete actions have confirmation
- [ ] Pagination for lists
- [ ] Mobile-responsive design

## References

- See `references/thymeleaf-syntax.md` for complete syntax
- See `examples/` for more templates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gazolla) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
