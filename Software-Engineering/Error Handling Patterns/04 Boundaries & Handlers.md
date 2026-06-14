---
tags: [error-handling, error-boundaries, global-handler, domain-errors]
---

# 04 — Boundaries & Handlers

## Error Boundaries in Frontend

```jsx
class ErrorBoundary extends React.Component {
    constructor(props) {
        super(props);
        this.state = { hasError: false, error: null };
    }

    static getDerivedStateFromError(error) {
        return { hasError: true, error };
    }

    componentDidCatch(error, errorInfo) {
        logErrorToService(error, errorInfo);
    }

    render() {
        if (this.state.hasError) {
            return <FallbackUI error={this.state.error}
                      onRetry={() => this.setState({ hasError: false })} />;
        }
        return this.props.children;
    }
}
```

## Global Error Handlers

```python
# Flask
@app.errorhandler(404)
def not_found(e):
    return jsonify({"error": "Resource not found"}), 404

@app.errorhandler(500)
def server_error(e):
    logger.critical(f"Internal error: {e}")
    return jsonify({"error": "Internal server error"}), 500

# FastAPI
@app.exception_handler(RequestValidationError)
async def validation_handler(request, exc):
    return JSONResponse(
        status_code=422,
        content={"detail": exc.errors(), "body": exc.body},
    )
```

## Domain Errors vs Technical Errors

| Aspect | Domain Errors | Technical Errors |
|--------|--------------|------------------|
| **Meaning** | Business logic violations | System/infra failures |
| **Examples** | Insufficient funds, invalid email | Network timeout, disk full |
| **User message** | "Your balance is too low" | "Something went wrong" |
| **Should retry?** | No (will fail again) | Yes (may succeed later) |
| **Log level** | WARNING | ERROR / CRITICAL |
| **Exception type** | `InsufficientFundsError` | `ConnectionError` |

### Custom Exception Hierarchy

```python
class AppError(Exception): pass
class DomainError(AppError): pass
class TechnicalError(AppError): pass

class InsufficientFundsError(DomainError): pass
class InvalidEmailError(DomainError): pass
class PaymentFailedError(DomainError): pass

class DatabaseConnectionError(TechnicalError): pass
class ExternalServiceError(TechnicalError): pass
class RateLimitError(TechnicalError): pass
```

**Links**: [[Software-Engineering/Error Handling Patterns/03 Retries, Degradation & Logging]] | [[Software-Engineering/Error Handling Patterns/05 Null Safety, Defensive & Async]] | [[Software-Engineering/Error Handling Patterns/01 Strategy & Exceptions]]
**See also**: [[Clean Code Principles]], [[Testing Guide]]
