- Implement reliable publishing.
- When publisher confirms are enabled on a channel, messages the client publishes are confirmed asynchronously by the broker, meaning they have been taken care of on the server side.
# Strategy
## Publishing messages individually
- Use `WaitForConfirmsOrDie`.
- Draw back: **significantly slows down publishing**.
## Publishing messages in batches
- Use `WaitForConfirmsOrDie` and publish message in a batch.
- Draw back: we do not know exactly what went wrong in case of failure.
## Handling Publisher Confirms Asynchronously
- Wait for publisher confirm with async.