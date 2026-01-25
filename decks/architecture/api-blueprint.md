# 1. The API Handler



Import aws_lambda_powertools utilities

```python
# **`handlers/api/publisher_handler.py`**

from http import HTTPStatus

from aws_lambda_powertools import Logger, Tracer
from aws_lambda_powertools.event_handler import APIGatewayRestResolver, Response
from aws_lambda_powertools.utilities.typing import LambdaContext
```



Import Shared Middlewares and Env Vars
```python
# Shared Middlewares
from src.common.shared.middlewares.organization import (
    inject_organization_user_context,
    log_request_response,
    api_error_handler,
    get_cors_config)

# CORS_ALLOW_ORIGIN = os.getenv('CORS_ALLOW_ORIGIN', None) Force specific CORS_ALLOW_ORIGIN
from src.env_vars import LOG_EVENT, CORS_ALLOW_ORIGIN
```



Repository specific Dependency Injection, App Initialization and Middleware Factory
```python

# Repository specific middleware

from src.events_management.application.middlewares import inject_app_services

# Initialize Powertools
logger = Logger("events_manager_service")
tracer = Tracer("events_manager_service")

app = APIGatewayRestResolver(enable_validation=True, cors=get_cors_config(CORS_ALLOW_ORIGIN))

app.use(middlewares=[
    log_request_response, 
    inject_organization_user_context, 
    inject_app_services, 
    api_error_handler
])

```



API Endpoint uses the injected service(s) into the app context
```python
@app.post("/events")
def publish():
    event_data = app.current_event.json_body or {}
    events_manager_service = app.context.get('services').get('events_manager_service')

    response = events_manager_service.publish_something_happened(event_data)

    return Response(
        status_code=HTTPStatus.OK.value, content_type="application/json",
        body=response)
```



Lambda Handler Entry Point
```python
@logger.inject_lambda_context(log_event=LOG_EVENT)
def lambda_handler(event: dict, context: LambdaContext):
    """
    Lambda entry point — handles API Gateway REST requests.
    Middleware is automatically applied to all routes.
    """
    return app.resolve(event, context)
```

---

# 2. Service Dependency Injection Middleware



This layer performs **Dependency Injection** before the handler is even called. but with the Organization User Context already injected by a previous middleware.

```python
# middlewares.py`
from aws_lambda_powertools.event_handler import APIGatewayRestResolver, Response
from aws_lambda_powertools.event_handler.middlewares import NextMiddleware

## Import required ServiceFactories
from src.events_management.application.factories import ServiceFactory

def inject_app_services(app: APIGatewayRestResolver, next_middleware: NextMiddleware) -> Response:
    # 1. Grab identity context from previous middleware
    context = app.context.get('organization_user_context')

    # 2. Use Factory to create a ready-to-use service
    service = ServiceFactory.create_events_manager_service(context=context)

    # 3. Inject it into the app context for the handler
    app.append_context(services={'events_manager_service': service})

    # Repeat to inject all required services

    return next_middleware(app)

```



### Service Factory
Service Factory Initializes Services with Organization User Contextr, so organization setters won't be necesary.
```python
### `factories.py`

class ServiceFactory:
    
    @staticmethod
    def create_events_manager_service(context):
        # Initialize Infrastructure Clients
        event_bridge = EventBridge(
            event_bus_name=env_vars.EVENTS_BUS_NAME,
            client=boto3.client('events'),
            logger=logger
        )

        # Build and return the service with its dependencies
        return EventsManagerService(event_bridge, context)

```

---

# 3. The Application Service



Minimal example of an Application Service that uses the OrganizationUserContext object
```python
# Instead of using a generic dict 
# Use the OrganizationUserContext which is injected by the shared middleware.
# Database and Repository dependencies are ommited for simplicity.
    
from src.common.shared.infrastructure.events_bus.eventbrigde_bus import EventBridge
from src.common.shared.schemas.organization_user_context import OrganizationUserContext

class EventsManagerService:
    def __init__(self, event_bus: EventBridge, context: OrganizationUserContext):
        self.event_bus = event_bus
        self.context = context

    def publish_something_happened(self, event_data):
        # Add organization and username to event data
        event_data.update(self.get_user_info())

        # Publish the event
        self.event_bus.publish('BluePrint.events_management.publisher', 
                               'SomethingHasHappened', event_data)

        return True

    def get_context(self):
        return self.context
```

---

# 4. Engineering Team Action Items




1. Make sure you are implementing Service Dependency Injection with the Organization User Context already injected by a previous middleware.
```python
# middlewares.py`
from aws_lambda_powertools.event_handler import APIGatewayRestResolver, Response
from aws_lambda_powertools.event_handler.middlewares import NextMiddleware

## Import required ServiceFactories
from src.events_management.application.factories import ServiceFactory

def inject_app_services(app: APIGatewayRestResolver, next_middleware: NextMiddleware) -> Response:
    # 1. Grab identity context from previous middleware
    context = app.context.get('organization_user_context')

    # 2. Use Factory to create a ready-to-use service
    service = ServiceFactory.create_events_manager_service(context=context)

    # 3. Inject it into the app context for the handler
    app.append_context(services={'events_manager_service': service})

    # Repeat to inject all required services

    return next_middleware(app)
```



2. Makse sure your Application Service constructor implement context: OrganizationUserContext and event_bus: EventBridge
```python
    from src.common.shared.infrastructure.events_bus.eventbrigde_bus import EventBridge
    from src.common.shared.schemas.organization_user_context import OrganizationUserContext
    .
    # This Example is missing the database and repository dependencies 
    # They are not relevant for this Action Item (So they are ommited for simplicity)
    .
    def __init__(self, event_bus: EventBridge, context: OrganizationUserContext):
        self.event_bus = event_bus
        self.context = context
```