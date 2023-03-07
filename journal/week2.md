# Week 2 — Distributed Tracing
## Honeycomb (Instrucciones en Honeycomb Docs)
- Exportamos variables de Honeycomb PERSONALIZADAS
 ```
export HONEYCOMB_API_KEY="64IcfWw0zoemi8jjL1NNyL"
export HONEYCOMB_SERVICE_NAME="Cruddur"
gp env HONEYCOMB_API_KEY="64IcfWw0zoemi8jjL1NNyL"
gp env HONEYCOMB_SERVICE_NAME="Cruddur"
```
- Añadimos las variables de entorno de Honeycomb al fichero docker-compose.yml del Backend
```
OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
OTEL_SERVICE_NAME: "${HONEYCOMB_SERVICE_NAME}"
```
- Añadimos al fichero de requisitos las librerías de Honeycomb
```
opentelemetry-api 
opentelemetry-sdk 
opentelemetry-exporter-otlp-proto-http 
opentelemetry-instrumentation-flask 
opentelemetry-instrumentation-requests
```
- Instalamos las librerías
```
pip install -r requirements.txt
```
- Añadimos al código de la App la parte de instrumentación / telemetría
```
from opentelemetry import trace
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
----------------------------------------------------------------
# Initialize tracing and an exporter that can send data to Honeycomb
provider = TracerProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)
trace.set_tracer_provider(provider)
tracer = trace.get_tracer(__name__)
--------------------------------------------------------------------
# Initialize automatic instrumentation with Flask
app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()
```
