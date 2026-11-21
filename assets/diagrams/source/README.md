# Diagram Sources

## Files

| View | Source | Rendering |
| --- | --- | --- |
| Architecture Map | [dineflow-architecture-map.drawio](dineflow-architecture-map.drawio) | [dineflow-architecture-map.svg](../dineflow-architecture-map.svg) |
| General use cases | [use-case-general.puml](use-case-general.puml) | [use-case-general.svg](../use-case-general.svg) |
| Customer use cases | [use-case-customer.puml](use-case-customer.puml) | [use-case-customer.svg](../use-case-customer.svg) |
| Waiter use cases | [use-case-waiter.puml](use-case-waiter.puml) | [use-case-waiter.svg](../use-case-waiter.svg) |
| Manager use cases | [use-case-manager.puml](use-case-manager.puml) | [use-case-manager.svg](../use-case-manager.svg) |
| General domain | [class-diagram.puml](class-diagram.puml) | [class-diagram.svg](../class-diagram.svg) |
| Booking/reservation | [booking-class-diagram.puml](booking-class-diagram.puml) | [booking-class-diagram.svg](../booking-class-diagram.svg) |
| Ordering/payment | [ordering-class-diagram.puml](ordering-class-diagram.puml) | [ordering-class-diagram.svg](../ordering-class-diagram.svg) |
| Staff/operations | [staff-class-diagram.puml](staff-class-diagram.puml) | [staff-class-diagram.svg](../staff-class-diagram.svg) |
| Table reservation | [sequence-diagram.puml](sequence-diagram.puml) | [sequence-diagram.svg](../sequence-diagram.svg) |
| Pre-order creation / checkout exit | [preorder-payment-sequence.puml](preorder-payment-sequence.puml) | [preorder-payment-sequence.svg](../preorder-payment-sequence.svg) |
| Payment outcomes | [payment-sequence.puml](payment-sequence.puml) | [payment-sequence.svg](../payment-sequence.svg) |
| Waiter call | [waiter-call-sequence.puml](waiter-call-sequence.puml) | [waiter-call-sequence.svg](../waiter-call-sequence.svg) |

The [architecture index](../../../docs/architecture/README.md) and [use cases](../../../docs/requirements/use-cases.md) explain the views. The [archived draw.io workspace](DineFlow-Architecture-Workspace.drawio) retains its original labels.

PNG exports share the following sources.

| PNG rendering | Maintained source |
| --- | --- |
| [restaurant-service-class-diagram.png](../restaurant-service-class-diagram.png) | [class-diagram.puml](class-diagram.puml) |
| [restaurant-service-use-case-diagram.png](../restaurant-service-use-case-diagram.png) | [use-case-general.puml](use-case-general.puml) |

## Rendering

For the Architecture Map, open the dedicated `.drawio` file in draw.io and use [File → Export As → SVG](https://www.drawio.com/docs/manual/export/export-to-svg/). Export the full page content in light appearance and check the labels, connectors, and legend in the SVG.

Use Java 21 and PlantUML 1.2025.2 from the repository root. Classes use Smetana, use cases use Graphviz 2.44.1, and sequences use PlantUML layout.

```powershell
$plantUmlJar = 'C:/path/to/plantuml-1.2025.2.jar'
java -jar $plantUmlJar -charset UTF-8 -checkonly 'assets/diagrams/source/*.puml'
java -jar $plantUmlJar -charset UTF-8 -tsvg -o .. 'assets/diagrams/source/*.puml'
```

Run `java -jar $plantUmlJar -testdot` before rendering use cases. PlantUML resolves `-o ..` relative to each source and writes the SVGs to `assets/diagrams/`. The JAR and Graphviz binary are external to the repository.

To refresh the two PNG formats, render the canonical files into a temporary preview directory and copy the results to the existing PNG filenames:

```powershell
$diagramPreviewDir = Join-Path $env:TEMP 'dineflow-diagram-preview'
New-Item -ItemType Directory -Path $diagramPreviewDir -Force | Out-Null
java -jar $plantUmlJar -charset UTF-8 -tpng -o $diagramPreviewDir assets/diagrams/source/class-diagram.puml assets/diagrams/source/use-case-general.puml
Copy-Item -LiteralPath (Join-Path $diagramPreviewDir 'class-diagram.png') -Destination assets/diagrams/restaurant-service-class-diagram.png -Force
Copy-Item -LiteralPath (Join-Path $diagramPreviewDir 'use-case-general.png') -Destination assets/diagrams/restaurant-service-use-case-diagram.png -Force
```

## Update workflow

1. Edit the source. Trace affected FR/NFR/AC and P-/D-IDs and preserve requirement priorities.
2. Validate syntax, regenerate the export, and inspect labels, connectors, multiplicities, and branch conditions.
3. Check prose, terminology, state labels, and local links together. Record any validation limits.
4. When requested, commit the source, export, and documentation together.

Use composition for owned elements such as pre-order items. Use include for required sub-behavior. Sequence loops show repetition. Retry limits and state definitions belong to the [proposed lifecycle model](../../../docs/requirements/functional-requirements.md#proposed-lifecycle-model) and [decision register](../../../docs/requirements/open-questions.md).
