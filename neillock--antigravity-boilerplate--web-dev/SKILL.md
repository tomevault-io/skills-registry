---
name: web-development
description: Comprehensive standards and best practices for building modern web applications (React, Next.js, TypeScript, Tailwind). Use when this capability is needed.
metadata:
  author: neillock
---

# Web Development Skill

This skill defines the **mandatory** standards, conventions, and workflows for all web development tasks.

## 1. Core Philosophy & Constraints
- **Clean & Scalable**: Follow SOLID principles. Prefer functional/discriminative programming.
- **Type Safety**: strict TypeScript usage is non-negotiable.
- **Component-Driven**: Build small, focused, reusable components.
- **Local Environment**: **NO Local Docker**. Use native runtimes (`npm run dev`) locally. Dockerfiles are for remote production only.
- **Security First**: 
  - NO hardcoded secrets (`.env` only).
  - NO exposed APIs without Auth.
  - Scan for key leakage before commits.

## 2. Technology Stack
- **Frameworks**: React, Next.js (App Router).
- **Language**: TypeScript (Strict Mode).
- **Styling**: Tailwind CSS, Shadcn UI, Radix UI primitives.
- **State**: React Context (simple), Redux Toolkit (complex/global).
- **Validation**: Zod.
- **Forms**: React Hook Form.
- **Testing**: Jest, React Testing Library.

## 3. Naming Conventions
| Type | Format | Example |
|------|--------|---------|
| **Directories** | kebab-case | `components/auth-wizard/` |
| **Files** | kebab-case | `user-profile.tsx`, `api-utils.ts` |
| **Components** | PascalCase | `UserProfile`, `SubmitButton` |
| **Interfaces/Types** | PascalCase | `UserData`, `AuthResponse` |
| **Variables/Props** | camelCase | `isLoading`, `userData`, `handleClick` |
| **Constants/Env** | UPPER_CASE | `API_URL`, `MAX_RETRIES` |

**Specific Patterns**:
- Event Handlers: `handleClick`, `handleSubmit`
- Booleans: `isLoading`, `hasError`, `canSubmit`
- Hooks: `useAuth`, `useForm`
- Abbreviations: Avoid them. Exceptions: `err`, `req`, `res`, `props`, `ref`.

## 4. Code Style & Implementation
- **Formatting**: Tabs for indentation. Single quotes. No semicolons (unless needed). 80 char line limit.
- **Equality**: Always use strict `===`.
- **Control Flow**: Curly braces for multi-line `if`. Else on same line as closing brace.
- **Planning**: Write pseudocode before implementation. Document architecture.

## 5. React & Next.js Best Practices
- **Server Components**: Default to Server Components. Use `'use client'` only for interactivity (hooks, listeners).
- **Performance**: 
  - `useCallback`/`useMemo` for expensive ops.
  - `React.lazy` / Dynamic imports for code splitting.
  - `Next/Image` for images.
- **Key Props**: Never use index as key.
- **Hooks**: Extract logic to custom hooks. Clean up `useEffect`.

## 6. TypeScript Rules
- **Strict Mode**: Enabled.
- **Types vs Interfaces**: Prefer `interface` for objects (extensibility). Use `type` for unions/mappings.
- **Generics**: Use for flexible actions/utilities.
- **No `any`**: Use `unknown` or strict checks. Use Type Guards.

## 7. UI & Styling
- **Tailwind**: Utility-first. Mobile-first (responsive).
- **Shadcn UI**: Use for consistent base components.
- **Theming**: CSS variables for colors/spacing. Dark mode via Tailwind/CSS vars.
- **Accessibility (A11y)**:
  - Semantic HTML.
  - Keyboard navigation coverage.
  - ARIA attributes where semantic tags fail.
  - Color contrast compliance.

## 8. State Management (Redux Toolkit)
- **Organization**: Use `createSlice`. Normalize state (flat structure).
- **Selectors**: Encapsulate access.
- **Slices**: Feature-based separation (not monolithic).

## 9. Testing & Quality
- **Unit**: Jest + RTL. Test behavior, not implementation details.
- **Integration**: Focus on workflows.
- **TDD Requirement** (If active): Red -> Green -> Refactor.
- **Linting**: No `eslint-disable`. Fix errors immediately.

## 10. Production (EXPERIMENTAL) & AI
- **Environment**: 
  - **Review**: NEVER hardcode API URLs. Use `NEXT_PUBLIC_API_URL` etc.
  - **Config**: Maintain `.env.example`.
- **Database**: Plan for schema migrations. Don't break prod DB.
- **AI Models**: 
  - Priority: Gemini 3.0 > 2.5.
  - **Documentation**: You MUST create and maintain `AI_MODELS.md` in the project root.
  - **Required Content**:
    | Service/Feature | Model Name | File Location | Purpose | Est. Cost (per 1k tokens) |
    |-----------------|------------|---------------|---------|---------------------------|
    | Example: Chat   | gemini-3.0-pro | `server/services/chat.ts:45` | User conversation handling | $0.00025 input / $0.0005 output |
  - **Update Trigger**: Every time you add, change, or remove an AI model call, update this file.

## 11. API Documentation (Backend)
- **Requirement**: Every backend API MUST have Swagger/OpenAPI documentation.
- **Implementation**:
  - Use `swagger-jsdoc` + `swagger-ui-express` (Node/Express) or equivalent for your stack.
  - Document ALL routes with: method, path, description, request body schema, response schema, and auth requirements.
- **Security Documentation**: Each endpoint must specify:
  - Authentication method (JWT, API Key, OAuth, Public)
  - Required permissions/roles
  - Rate limiting (if applicable)
- **Location**: Serve docs at `/api/docs` or `/swagger`.
- **Maintenance**: Update Swagger docs on every API change.

## 12. Workflow Triggers
- **Backend Logic Detection**: If writing DB/Secret logic in a Client Component -> STOP. Suggest decoupling.
- **Cross-Stack Verification**: If editing Client AND Server in one go -> STOP. Ask for confirmation.

## 13. Link Integrity
- **No Dead Ends**: Every link (`<a>` tag or router navigation) MUST point to a functional destination. 404s are unacceptable.
- **Verification**: Always verify link targets during implementation and testing. For internal links, ensure the route exists. For external links, ensure the URL is valid.
- **Clarification**: If the destination of a link is unclear or not yet implemented, **ASK THE USER** for instructions before proceeding. Do not use placeholder links (e.g., `#`) in production-ready code without explicit approval.

## 14. Project Documentation (README.md)
- **Requirement**: Every project MUST have a `README.md` file in the root directory.
- **Essential Content**: The README must include a "Getting Started" or "Setup" section that explains:
  - **Prerequisites**: Required tools (e.g., Node.js version, npm/yarn).
  - **Environment Variables**: List of required variables (referencing `.env.example`).
  - **Installation**: Commands to install dependencies (e.g., `npm install`).
  - **Development**: Commands to run the app locally (e.g., `npm run dev`).
  - **Testing**: Commands to run tests (e.g., `npm test`).
  - **Architecture**: Brief overview of the project structure and key components.

## 15. Visual Verification & Rendering
- **Mandatory Verification**: Frontend work is NOT considered complete until it has been visually verified in a real browser environment.
- **Browser Tool**: Use the `browser` tool to navigate to the local development environment (`npm run dev`) and capture screenshots or videos of the rendered UI.
- **Checklist**:
  - **Rendering**: Ensure all components render without errors or layout shifts.
  - **Responsiveness**: Verify the UI across Mobile, Tablet, and Desktop breakpoints.
  - **Dark Mode**: If implemented, ensure styles are correct in dark mode.
  - **Interactivity**: Test hover states, clicks, and form submissions to ensure the UI responds as expected.
- **Fail Proof**: If the browser tool fails to load the page or rendering is broken, you MUST identify and fix the issue before asking for user review.

## 16. Interactive Content Discovery
- **Proactive Consultation**: When building multi-level or complex websites, do not assume the content or purpose of nested pages. 
- **Mandatory Questions**: As you build, proactively ask the user: *"Would you like a page that does [X]?"* or *"How should this nested section behave?"*
- **Final Design Check**: At the conclusion of the primary build-out, you MUST ask the user: *"Are there any other pages you think I should design?"* to ensure no requirements were missed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neillock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
