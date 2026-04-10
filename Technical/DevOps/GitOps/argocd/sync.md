# Sync
- Sync phases and hooks define when resources are applied such as before or after the main sync operation.
- It makes the deployment of resources in order of sequence.
## Hooks

| Hook         | Description                                                                                                                                                                |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `PreSync`    | Executes prior to the application of the manifests.                                                                                                                        |
| `Sync`       | Executes after all `PreSync` hooks completed and were successful, at the same time as the application of the manifests.                                                    |
| `Skip`       | Indicates to Argo CD to skip the application of the manifest.                                                                                                              |
| `PostSync`   | Executes after all `Sync` hooks completed and were successful, a successful application, and all resources in a `Healthy` state.                                           |
| `SyncFail`   | Executes when the sync operation fails.                                                                                                                                    |
| `PreDelete`  | Executes before Application resources are deleted. Only runs when the entire Application is being deleted, not during normal sync operations (even with pruning enabled. ) |
| `PostDelete` | Executes after all Application resources are deleted.                                                                                                                      |
- To use the hook, add the `argocd.argoproj.io/hook` annotation into the resource.
### How it works?
1. Apply all the resources marked as PreSync hooks. If any of them fails the whole sync process will stop and will be marked as failed
2. Apply all the resources marked as Sync hooks. If any of them fails the whole sync process will be marked as failed. Hooks marked with SyncFail will also run
3. Apply all the resources marked as PostSync hooks. If any of them fails the whole sync process will be marked as failed.
![](/Image/Pasted%20image%2020260410074629.png)
### Cleanup
- Used with `argocd.argoproj.io/hook-delete-policy` annotation.

| Policy               | Description                                                                                                        |
| -------------------- | ------------------------------------------------------------------------------------------------------------------ |
| `HookSucceeded`      | The hook resource is deleted after the hook succeeded (e.g. Job/Workflow completed successfully).                  |
| `HookFailed`         | The hook resource is deleted after the hook failed.                                                                |
| `BeforeHookCreation` | Any existing hook resource is deleted before the new one is created. It is meant to be used with `/metadata/name`. |
## Waves
- Sync waves are used for ordering the resource is deployed in a hook.
- Hooks and resources are assigned to wave 0 by default. The wave can be negative, so you can create a wave that runs before all other resources.
- Use `ARGOCD_SYNC_WAVE_DELAY` to delay between each sync wave in order to give other controllers a chance to react to the spec change that was just applied and prevents Argo CD from assessing resource health too quickly (against the stale object), causing hooks to fire prematurely. The current delay between each sync wave is 2 seconds.
- Used with `argocd.argoproj.io/sync-wave` annotation.
![](/Image/Pasted%20image%2020260410080140.png)