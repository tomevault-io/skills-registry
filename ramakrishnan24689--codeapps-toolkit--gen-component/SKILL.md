---
name: gen-component
description: Generate React components for displaying and managing data. Creates list views, detail views, and forms using Fluent UI v9 with React Query hooks integration. Use when this capability is needed.
metadata:
  author: ramakrishnan24689
---

# Generate React Components

Generate Fluent UI v9 components for displaying and managing data with React Query hooks.

## What This Skill Does

1. Creates list view components (display multiple records)
2. Creates detail view components (display single record)
3. Creates form components (create/edit records)
4. Integrates with React Query hooks
5. Uses Fluent UI v9 components
6. Implements proper loading and error states

## Usage

```
/gen-component todos list
/gen-component todos detail
/gen-component todos form
```

## Component Patterns

### List View Component

```typescript
import { Spinner, MessageBar, Card } from '@fluentui/react-components';
import { useFeatures } from '../hooks/useFeatures';

export const FeatureList = () => {
  const { data: features, isLoading, error } = useFeatures();

  if (isLoading) return <Spinner label="Loading features..." />;
  if (error) return <MessageBar intent="error">Failed to load features</MessageBar>;
  if (!features?.length) return <MessageBar>No features found</MessageBar>;

  return (
    <div className="feature-list">
      {features.map(feature => (
        <Card key={feature.id} className="feature-card">
          <h3>{feature.name}</h3>
          <p>{feature.description}</p>
        </Card>
      ))}
    </div>
  );
};
```

### Detail View Component

```typescript
export const FeatureDetail = ({ id }: { id: string }) => {
  const { data: feature, isLoading, error } = useFeatureById(id);

  if (isLoading) return <Spinner />;
  if (error) return <MessageBar intent="error">Failed to load</MessageBar>;
  if (!feature) return <MessageBar>Feature not found</MessageBar>;

  return (
    <div className="feature-detail">
      <h2>{feature.name}</h2>
      <p>{feature.description}</p>
      {/* Additional fields */}
    </div>
  );
};
```

### Form Component

```typescript
export const FeatureForm = ({ id }: { id?: string }) => {
  const [formData, setFormData] = useState<CreateFeatureDto>({
    name: '',
    description: '',
  });

  const createFeature = useCreateFeature();
  const updateFeature = useUpdateFeature();

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    if (id) {
      await updateFeature.mutateAsync({ id, dto: formData });
    } else {
      await createFeature.mutateAsync(formData);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <Field label="Name">
        <Input
          value={formData.name}
          onChange={(e) => setFormData({ ...formData, name: e.target.value })}
        />
      </Field>
      <Button type="submit" appearance="primary">
        {id ? 'Update' : 'Create'}
      </Button>
    </form>
  );
};
```

## Output

```
✅ Component generated!

📁 Created: src/features/<feature>/components/<Feature><Type>.tsx

🎨 Component Type: <List|Detail|Form>

💡 Import in your app:
   import { <Feature><Type> } from '@/features/<feature>/components/<Feature><Type>';
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ramakrishnan24689) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
