# APIs And SDKs
## APIs
- The APIs have a minimal implementation, which mean they have basic no-op (no operations) to ensure APIs work even if the SDKs are not installed.
### Characteristics
- Self-sufficient dependency:
	- Application can compile and run successfully
	- Avoid runtime errors
	- Application or 3rd libs can depend on the API without requiring the SDK
- No telemetry output by default: run as no-op mode when the SDK is not installed
- Minimal default implementation
