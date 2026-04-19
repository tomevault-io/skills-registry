---
name: livewire-form-object
description: >- Use when this capability is needed.
metadata:
  author: naykel76
---

## When to use me

- When form logic needs to be reused across multiple components
- When forms are large and need better organization
- When the same form is used in different contexts (create vs edit)

## Initial Setup

```bash +code
php artisan livewire:form ModelFormObject
```

```php +code
namespace App\Livewire\Forms;

use App\Models\Model;
use Livewire\Attributes\Validate;
use Livewire\Form;
use Naykel\Gotime\Traits\Crudable;
use Naykel\Gotime\Traits\Formable;

class ModelFormObject extends Form
{
    use Crudable, Formable;

    public function init(Model $model): void
    {
        $this->editing = $model;
        $this->setFormProperties($this->editing);
    }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/naykel76) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
