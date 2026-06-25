---
name: add-preprocessor
description: Add a new Preprocessor that runs BEFORE CloudFormation deletion (e.g. deletion-protection check, Lambda VPC detachment). Use when adding an entry to the "Pre-deletion Processing" table. Not for force-deleting resources that cause DELETE_FAILED — use `add-operator` for those. Use when this capability is needed.
metadata:
  author: go-to-k
---

# Add a new Preprocessor

You are adding logic that runs **before** CloudFormation `DeleteStack` is called, to either validate that deletion is allowed or to mutate state to improve the deletion's success rate.

## Decide the phase first

`CompositePreprocessor` runs preprocessors in two phases. Pick the one that fits before writing any code:

- **Checker** — runs first, in parallel with other checkers. Errors are **fatal** and abort the whole deletion. All checker errors are collected before returning, so the user sees every problem at once. Use for validation that must pass (e.g. deletion-protection check).
- **Modifier** — runs after all checkers pass. Errors are **logged as warnings only** and do not stop deletion. Use for optimisations that improve deletion success but are not required (e.g. Lambda VPC detachment, which lets CloudFormation finish faster but is not strictly necessary).

If your logic must block deletion on failure → Checker. If it is best-effort cleanup → Modifier.

## Input

The user provides a name like `LambdaVPCDetacher` or `DeletionProtectionRemover`. Derive:

- File name: `internal/preprocessor/<snake_case>.go` (e.g. `lambda_vpc_detacher.go`)
- Factory function name: `new<Name>FromConfig` (lowercase first letter — package-internal)
- Phase: Checker or Modifier (decided above)

## Steps

1. **`internal/preprocessor/<name>.go`** — Implement `IPreprocessor`.
   - Add `var _ IPreprocessor = (*<Name>)(nil)` for compile-time check.
   - Reach AWS only through `pkg/client` interfaces (never the SDK directly).
   - Operations must be idempotent.
   - Concurrency: `errgroup` + `semaphore.NewWeighted(runtime.NumCPU())`.
   - Errors: client errors are returned as-is (already `ClientError`-wrapped). Only wrap locally generated errors (e.g. `ctx.Done()`, validation).
   - **Checker**: collect all errors, do not short-circuit. **Modifier**: errors propagate but the caller logs them as warnings.

2. **`internal/preprocessor/<name>_test.go`** — Tests use **gomock** against the `I<Service>` interfaces in `pkg/client/`.

3. **`pkg/client/`** — If the preprocessor needs an AWS service the project does not yet wrap:
   - If the service file exists, **extend** the interface and struct (do not create a new file).
   - If new, create `pkg/client/<service>.go` with `//go:generate mockgen` on **line 1**, then run `make mockgen`. Public methods wrap errors with `ClientError`. Tests use AWS SDK middleware mocks (see `s3_test.go`, `backup_test.go`), NOT gomock.

4. **`internal/preprocessor/factory.go`** — Wire it in:
   - Add `new<Name>FromConfig(config aws.Config[, forceMode bool])` that builds SDK clients with `operation.SDKRetryMaxAttempts` and `aws.RetryModeStandard`, wraps them via `pkg/client`, and returns the preprocessor.
   - In `NewRecursivePreprocessorFromConfig`, call the factory and append the result to **either** the `checkers` slice **or** the `modifiers` slice in the `NewCompositePreprocessor(...)` call. Pick the slice that matches the phase you chose.

5. **`internal/resourcetype/resourcetype.go`** — If the preprocessor reasons over a CloudFormation resource type that is not yet defined (e.g. a new deletion-protection target), add the constant to the appropriate group ("For Deletion Protection Check" or similar). It does **not** need to be appended to `ResourceTypes` (that slice is for force-delete operators).

6. **`go.mod` / `go.sum`** — `go get github.com/aws/aws-sdk-go-v2/service/<service>` for any new SDK service, then `go mod tidy`.

7. **`README.md`** — Add a row to the "Pre-deletion Processing" table.

8. **E2E test** — Prefer a dedicated `e2e/<name>/` directory (e.g. `e2e/preprocessor/`, `e2e/deletion_protection/`) over modifying `e2e/full/`. When creating a new directory, add `cdk/.gitignore` excluding `cdk.context.json` (CDK writes account-specific context there at deploy time). Add corresponding `testgen_<name>` and `e2e_<name>` Make targets if applicable. The maintainer runs E2E; the code must compile and synth.

9. **Verify locally**:
   - `make mockgen` (only if you added a new client method)
   - `make test`
   - `make lint`

## Conventions reminder

- Code comments: minimal, in English.
- Test naming: `Test[ReceiverType]_[MethodName]`.
- All public-facing text (README rows, error messages, PR/commit) is English.
- Checker errors are user-visible blockers — error messages should tell the user how to unblock (e.g. "remove TerminationProtection or pass `-f`").

---
> Source: [go-to-k/delstack](https://github.com/go-to-k/delstack) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
