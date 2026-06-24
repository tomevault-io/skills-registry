---
name: django-validators
description: Apply this skill when writing custom validators in Django. Covers model validators vs form validators, DRF validator patterns, raising ValidationError correctly, and when to use clean() vs clean_field(). Triggered by phrases like 'Django validator', 'clean method', 'ValidationError', 'custom validation', or when adding input validation. Use when this capability is needed.
metadata:
  author: upstate-web-co
---

## Trigger
You need to add input validation, file upload validation, or phone/email validation to a Django app.

## Summary (Human)
5-layer validation architecture where every layer delegates to the same core (`UniversalInputValidator` / `UniversalFileValidator`). Every method returns `{'is_valid': bool, 'errors': list}` — never raises directly. Each higher layer wraps the result into its own exception type.

## Procedure (Claude)

### Architecture: 5 Layers

```
Layer 5: Model Validators     ← ModelValidationMixin auto-validates on clean()
Layer 4: Serializer Validators ← DRF validate_<field>() calls core validators
Layer 3: File Validators       ← 6-point file validation (size, ext, MIME, filename, image, antivirus)
Layer 2: Input Validators      ← Core logic: email, phone, amount, password, username
Layer 1: Field Validators      ← Thin wrappers for Django model field validators=[...]
```

All layers delegate downward to Layer 2/3. Never duplicate validation logic between layers.

### Layer 1: Field Validators (Django model field level)

Thin wrappers that call `UniversalInputValidator` and raise Django's `ValidationError`:

```python
# core_validators/field_validators.py
from django.core.exceptions import ValidationError

def validate_email(value):
    result = UniversalInputValidator.validate_email(value)
    if not result['is_valid']:
        raise ValidationError(result['errors'][0])

def validate_kenyan_phone_number(value):
    if not value:
        return
    result = UniversalInputValidator.validate_phone(value, country='KE')
    if not result['is_valid']:
        raise ValidationError(result['errors'][0])

def validate_image(file, max_size_mb=5):
    from .file_validators import UniversalFileValidator
    result = UniversalFileValidator.validate_file(
        file, file_type='image',
        custom_config={'max_size_mb': max_size_mb}
    )
    if not result['is_valid']:
        raise ValidationError(result['errors'][0])
```

Usage in models:
```python
class Member(models.Model):
    email = models.EmailField(validators=[validate_email])
    phone_number = models.CharField(validators=[validate_kenyan_phone_number])
```

### Layer 2: Input Validators (Core logic)

Single source of truth. Every method returns a result dict — never raises:

```python
# core_validators/input_validators.py
class UniversalInputValidator:
    EMAIL_PATTERN = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'

    @staticmethod
    def validate_email(email, check_unique=False, model=None):
        errors = []
        if not email or not email.strip():
            errors.append("Email is required")
        elif not re.match(UniversalInputValidator.EMAIL_PATTERN, email.strip()):
            errors.append("Invalid email format")
        if check_unique and model:
            if model.objects.filter(email=email.strip().lower()).exists():
                errors.append("Unable to register with the provided details")
        return {
            'is_valid': len(errors) == 0,
            'errors': errors,
            'normalized_email': email.strip().lower() if email else None,
        }

    @staticmethod
    def validate_phone(phone_number, country='KE', check_unique=False, model=None):
        errors = []
        normalized = None
        try:
            parsed = phonenumbers.parse(phone_number, country)
            if phonenumbers.is_valid_number(parsed):
                normalized = phonenumbers.format_number(
                    parsed, phonenumbers.PhoneNumberFormat.E164
                )
            else:
                errors.append("Invalid phone number")
        except (phonenumbers.NumberParseException, ValueError):
            # Fallback to regex for country-specific formats
            patterns = UniversalInputValidator.PHONE_PATTERNS.get(country, {}).get('patterns', [])
            matched = False
            for pattern in patterns:
                if re.match(pattern, phone_number):
                    matched = True
                    cleaned = re.sub(r'\D', '', phone_number)
                    if country == 'KE':
                        if cleaned.startswith('0'):
                            normalized = '+254' + cleaned[1:]
                        elif cleaned.startswith('254'):
                            normalized = '+' + cleaned
                        else:
                            normalized = '+254' + cleaned
                    break
            if not matched:
                errors.append(f"Invalid {country} phone number format")
        return {
            'is_valid': len(errors) == 0,
            'errors': errors,
            'normalized_phone': normalized,
        }

    @staticmethod
    def validate_amount(amount, min_amount=0, max_amount=None, allow_negative=False):
        errors = []
        try:
            if isinstance(amount, str):
                amount = Decimal(amount)
            if amount < min_amount:
                errors.append(f"Amount must be at least {min_amount}")
            if not allow_negative and amount < 0:
                errors.append("Amount cannot be negative")
            if max_amount is not None and amount > max_amount:
                errors.append(f"Amount cannot exceed {max_amount}")
            return {
                'is_valid': len(errors) == 0,
                'errors': errors,
                'normalized_amount': amount,
            }
        except (InvalidOperation, TypeError, ValueError):
            return {'is_valid': False, 'errors': ["Invalid amount"]}
```

### Layer 3: File Validators (Upload security)

6-point validation: size, extension allowlist, MIME type, dangerous filename, image verification, anti-virus placeholder:

```python
# core_validators/file_validators.py
class UniversalFileValidator:
    VALIDATORS = {
        'image': {
            'extensions': ['.jpg', '.jpeg', '.png', '.gif', '.webp', '.bmp'],
            'mime_types': ['image/jpeg', 'image/png', 'image/gif', 'image/webp'],
            'max_size_mb': 10,
            'dimensions': (4000, 4000),
        },
        'profile_picture': {
            'extensions': ['.jpg', '.jpeg', '.png', '.gif', '.webp'],
            'mime_types': ['image/jpeg', 'image/png', 'image/gif', 'image/webp'],
            'max_size_mb': 5,
            'dimensions': (2000, 2000),
            'aspect_ratio': (1, 1),
        },
        'document': {
            'extensions': ['.pdf', '.doc', '.docx', '.txt', '.xls', '.xlsx', '.csv'],
            'mime_types': ['application/pdf', 'application/msword', ...],
            'max_size_mb': 20,
        },
    }

    @classmethod
    def validate_file(cls, file, file_type='document', custom_config=None):
        errors = []
        warnings = []
        config = cls.VALIDATORS.get(file_type, cls.VALIDATORS['document'])
        if custom_config:
            config.update(custom_config)

        # 1. File size
        if file.size > config['max_size_mb'] * 1024 * 1024:
            errors.append(f"File size exceeds {config['max_size_mb']}MB limit")

        # 2. Extension allowlist
        ext = os.path.splitext(file.name)[1].lower()
        if ext not in config['extensions']:
            errors.append(f"Invalid extension. Allowed: {', '.join(config['extensions'])}")

        # 3. MIME type check
        mime_type = mimetypes.guess_type(file.name)[0]
        if mime_type and mime_type not in config.get('mime_types', []):
            warnings.append(f"MIME type '{mime_type}' doesn't match extension")

        # 4. Dangerous filename detection
        if not cls._is_safe_filename(file.name):
            errors.append("File name contains dangerous characters")

        # 5. Image-specific (PIL verify + dimensions)
        if file_type in ('image', 'profile_picture'):
            errors.extend(cls._validate_image(file, config))

        # 6. Anti-virus placeholder (implement per deployment)

        return {
            'is_valid': len(errors) == 0,
            'errors': errors,
            'warnings': warnings,
            'file_name': file.name,
            'file_size': file.size,
            'file_extension': ext,
            'mime_type': mime_type,
        }

    @staticmethod
    def _is_safe_filename(filename):
        if '..' in filename or filename.startswith('/'):
            return False
        dangerous = ['.exe', '.bat', '.sh', '.js', '.php', '.py', '.sql',
                     '.dll', '.so', '.dylib', '.jar', '.war']
        return not any(p in filename.lower() for p in dangerous)
```

### Layer 4: Serializer Validators (DRF integration)

Serializer `validate_<field>` methods call core validators:

```python
# users/serializers.py
class UserRegistrationSerializer(serializers.ModelSerializer):
    def validate_email(self, value):
        value = value.strip().lower()
        result = UniversalInputValidator.validate_email(value)
        if not result['is_valid']:
            raise serializers.ValidationError(result['errors'][0])
        if CustomUser.objects.filter(email=value).exists():
            raise serializers.ValidationError(
                'Unable to register with the provided details.'
            )
        return value

    def validate_phone_number(self, value):
        if not value:
            return value
        result = UniversalInputValidator.validate_phone(str(value), country='KE')
        if not result['is_valid']:
            raise serializers.ValidationError(result['errors'][0])
        return value

class ProfilePictureSerializer(serializers.ModelSerializer):
    def validate_profile_picture(self, value):
        result = UniversalFileValidator.validate_file(value, file_type='profile_picture')
        if not result['is_valid']:
            raise serializers.ValidationError(result['errors'][0])
        return value
```

Composite serializer validation for multi-field forms:

```python
# core_validators/serializer_validators.py
class SerializerValidator:
    @staticmethod
    def validate_user_registration(data, existing_user=None):
        errors = {}
        email_result = UniversalInputValidator.validate_email(
            data.get('email'),
            check_unique=(existing_user is None),
        )
        if not email_result['is_valid']:
            errors['email'] = email_result['errors']
        phone = data.get('phone_number')
        if phone:
            phone_result = UniversalInputValidator.validate_phone(phone, country='KE')
            if not phone_result['is_valid']:
                errors['phone_number'] = phone_result['errors']
        return errors
```

### Layer 5: Model Validators (Auto-validation mixin)

```python
# core_validators/model_validators.py
from django.utils.deconstruct import deconstructible

@deconstructible
class PhoneNumberValidator:
    def __init__(self, country='KE'):
        self.country = country
    def __call__(self, value):
        if not value:
            return
        result = UniversalInputValidator.validate_phone(str(value), country=self.country)
        if not result['is_valid']:
            raise ValidationError(result['errors'][0])

@deconstructible
class UniversalFileFieldValidator:
    def __init__(self, file_type='document', max_size_mb=None):
        self.file_type = file_type
        self.max_size_mb = max_size_mb
    def __call__(self, value):
        if not value:
            return
        config = {'max_size_mb': self.max_size_mb} if self.max_size_mb else {}
        result = UniversalFileValidator.validate_file(
            value, file_type=self.file_type, custom_config=config
        )
        if not result['is_valid']:
            raise ValidationError(result['errors'][0])

class ModelValidationMixin:
    """Auto-validates fields by name convention on clean()."""
    def clean(self):
        super_clean = getattr(super(), 'clean', None)
        if super_clean:
            super_clean()
        self.validate_with_universal_validators()

    def validate_with_universal_validators(self):
        errors = {}
        handlers = {
            'email': lambda v: UniversalInputValidator.validate_email(v),
            'phone_number': lambda v: UniversalInputValidator.validate_phone(str(v), 'KE'),
            'first_name': lambda v: UniversalInputValidator.validate_name(v, 'First name'),
            'last_name': lambda v: UniversalInputValidator.validate_name(v, 'Last name'),
        }
        for field in self._meta.fields:
            value = getattr(self, field.name, None)
            if value is None and (field.null or field.blank):
                continue
            handler = handlers.get(field.name)
            if handler and value:
                result = handler(value)
                if not result['is_valid']:
                    errors[field.name] = result['errors']
        if errors:
            raise ValidationError(errors)
```

### Error Handling Middleware

Normalizes Django vs DRF validation errors into consistent JSON:

```python
class ValidationErrorMiddleware:
    def process_exception(self, request, exception):
        if isinstance(exception, (ValidationError, CoreValidationError)):
            return JsonResponse({
                'success': False,
                'errors': formatted_errors,
                'message': 'Validation failed',
            }, status=400)
```

## Anti-Patterns

| Anti-Pattern | Correct |
|---|---|
| Validate inline in views | Use core validators via serializer `validate_<field>()` |
| Raise exceptions in core validators | Return `{'is_valid': bool, 'errors': list}` — let caller decide exception type |
| Duplicate validation between serializer and model | Both layers delegate to same core validator |
| Non-`@deconstructible` validators on model fields | Always `@deconstructible` (migration-safe) |
| `mimetypes.guess_type()` without magic bytes | Add PIL `img.verify()` for images; log warning for non-image files |
| Inline file extension lists | Use `VALIDATORS` config dict with named file types |
| Generic error messages | Include field name and constraint: "Amount must be at least 100" |
| Skip phone normalization | Always return `normalized_phone` in E.164 format |

## Source
- MyChama: `chamagroup/core_validators/` (5 files: field, input, file, serializer, model validators)
- MyChama: `communications/validators.py` (domain-specific composites: email frequency, spam detection)

---
> Source: [upstate-web-co/uwc-django-skills](https://github.com/upstate-web-co/uwc-django-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
