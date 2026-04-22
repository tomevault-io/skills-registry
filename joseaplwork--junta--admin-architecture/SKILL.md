---
name: admin-app-architecture
description: Feature-based architecture patterns, state management, and code organization for the Angular admin application. Use when this capability is needed.
metadata:
  author: joseaplwork
---

# Admin App Architecture

This skill defines the feature-based architecture and code organization patterns for the admin application.

## When to Use

- When creating new features or pages in the admin app
- When organizing code within a page or feature
- When deciding where to place shared vs. feature-specific code
- When implementing state communication between features

## Feature-Based Structure

Organize code by features within pages. Each page contains multiple features (create, list, update, delete).

### Directory Structure

```
src/
├── pages/
│   └── participant/                              # Participant management page
│       ├── create-feat/                          # Create participant feature
│       │   ├── components/                       # Presentational components
│       │   │   ├── create-participant-dialog.ts
│       │   │   └── create-participant-dialog.html
│       │   ├── interfaces/                       # Feature-specific interfaces
│       │   │   └── create-payload.ts
│       │   ├── services/                         # Feature business logic
│       │   │   └── create-participant.ts
│       │   ├── create-feat.html                  # Feature entry template
│       │   └── create-feat.ts                    # Feature entry component
│       ├── list-feat/                            # List participant feature
│       ├── update-feat/                          # Update participant feature
│       ├── delete-feat/                          # Delete participant feature
│       ├── participant-page-state.ts             # Shared state between features
│       ├── participant-page.html                 # Page layout template for features
│       └── participant-page.ts                   # Page component
└── shared/                                       # Reusable across admin app
    ├── components/                               # Shared UI components
    ├── interfaces/                               # Shared interfaces
    └── services/                                 # Shared services
```

## Feature Components

### Entry Point (`*-feat.ts`)

The feature entry component:

- Opens dialogs or navigates
- Passes callbacks to child components
- Handles success/error notifications
- Binds business logic to presentational components
- Sets up listeners for presentational components

### Example

```typescript
@Component({
  selector: 'app-create-feat',
  templateUrl: './create-feat.html',
})
export class CreateFeat {
  private readonly _dialog = inject(Dialog)
  private readonly _service = inject(CreateService)
  private readonly _state = inject(PageState)
  private readonly _snackbar = inject(Snackbar)

  openDialog(): void {
    this._dialog.open(CreateDialog, { handleSubmit: this._handleSubmit })
  }

  private _handleSubmit = async (payload: CreatePayload): Promise<void> => {
    try {
      const result = await this._service.create(payload)
      this._state.emitCreated(result)
      this._snackbar.success('Created successfully')
    } catch {
      this._snackbar.error('Creation failed')
    }
  }
}
```

### Dialog Components

- Receive data via `MAT_DIALOG_DATA`
- Handle form state and validation
- Call parent callbacks on submit
- Manage submitting state with try-finally

### Services

Services contain:

- HTTP calls and business logic
- Mapping between frontend and API data structures
- Return promises for async operations
- Logic outside presentation

## State Management

Use signal-based state services to communicate between features on the same page.

### Example

```typescript
@Injectable({ providedIn: 'root' })
export class ParticipantPageState {
  private readonly _participantCreated = new Subject<Participant>()
  readonly participantCreated$ = this._participantCreated.asObservable()

  emitParticipantCreated(participant: Participant): void {
    this._participantCreated.next(participant)
  }

  emitParticipantUpdated(participant: Participant): void {
    this._participantUpdated.next(participant)
  }
}
```

## Code Organization

### Shared Code (Across App)

Place code used across multiple pages or the entire app in `shared/`:

- `shared/components/` - Dialog, Snackbar components
- `shared/interfaces/` - Participant, Admin interfaces
- `shared/services/` - Config, Navigation, Session services

### Page-Level Shared Code

Place code reused between features within a single page at the page top level only when necessary:

- `components/` - Presentational components across page features
- `interfaces/` - Interfaces shared across page features
- `services/` - Services shared across page features

### Feature-Specific Code

Keep code specific to a single feature within that feature's folder:

- `components/` - Feature-specific presentational components
- `interfaces/` - Feature-specific interfaces
- `services/` - Feature-specific business logic

## Interface Naming

- Use consistent naming: `CreatePayload`, `UpdatePayload`
- Export type aliases for backward compatibility when needed
- Keep interfaces in feature-specific `interfaces/` folders
- Use descriptive names without repeating context (e.g., `UpdatePayload` not `ParticipantListUpdatePayload`)

## Instructions

1. **Create features**: Follow the `*-feat/` pattern with components, interfaces, services subfolders
2. **Organize code**: Place shared code in `shared/`, page-level code at page root, feature code in feature folder
3. **State management**: Use signal-based state services for feature communication
4. **Entry components**: Handle business logic, pass callbacks to dialogs
5. **Services**: Contain HTTP calls and data mapping, return promises
6. **Interfaces**: Use consistent naming, keep in appropriate location based on scope

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joseaplwork) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
