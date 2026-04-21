---
name: kubernetes-operator
description: > Use when this capability is needed.
metadata:
  author: stormingluke
---

## Controller-Runtime Patterns

### Reconciler Structure
- Implement `reconcile.Reconciler` interface
- Always accept and propagate `context.Context`
- Return `ctrl.Result{}` with appropriate requeue:
  - `Result{}` — success, no requeue
  - `Result{RequeueAfter: 30 * time.Second}` — delayed retry
  - `Result{Requeue: true}` — immediate retry
- Never return an error for expected/permanent failures — log and return `Result{}`
- Return errors only for transient failures that should be retried

### CRD Design
- Group: `<domain>.example.com/v1alpha1` → `v1beta1` → `v1`
- Status subresource always enabled
- Use status conditions following `metav1.Condition` pattern:
  - `Type`, `Status` (True/False/Unknown), `Reason`, `Message`, `LastTransitionTime`
- Printer columns for `kubectl get` output
- Validation via CEL expressions in CRD markers

### Finalizers
- Add finalizer on creation if external cleanup is needed
- Check `DeletionTimestamp` before reconciling
- Remove finalizer only after cleanup succeeds
- Pattern:
  ```go
  if obj.DeletionTimestamp != nil {
      if controllerutil.ContainsFinalizer(obj, finalizerName) {
          // cleanup external resources
          controllerutil.RemoveFinalizer(obj, finalizerName)
          return ctrl.Result{}, r.Update(ctx, obj)
      }
      return ctrl.Result{}, nil
  }
  ```

### Owner References
- Set owner references for all child resources
- Use `controllerutil.SetControllerReference()` for single-owner
- Use `controllerutil.SetOwnerReference()` for shared ownership
- Watch owned resources with `.Owns(&corev1.ConfigMap{})`

### RBAC
- Use kubebuilder RBAC markers: `//+kubebuilder:rbac:groups=...,resources=...,verbs=...`
- Principle of least privilege — only request what the controller needs
- Separate ClusterRole for cluster-scoped vs namespace-scoped resources

### Testing
- Unit tests: `fake.NewClientBuilder().WithScheme(s).WithObjects(objs...).Build()`
- Integration tests: `envtest.Environment` with real etcd + API server
- Test the full reconcile loop: create → reconcile → verify → update → reconcile → verify
- Test idempotency: running reconcile twice should produce the same result

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stormingluke) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
