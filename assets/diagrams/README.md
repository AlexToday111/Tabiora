# Tabiora Diagram Assets

Each maintained diagram has one directory, one editable source, and one PNG with matching lowercase kebab-case names.

## Files

| View | Editable source | PNG |
| --- | --- | --- |
| Architecture map | [drawio](architecture-map/architecture-map.drawio) | [PNG](architecture-map/architecture-map.png) |
| Booking and reservation | [puml](booking-class-diagram/booking-class-diagram.puml) | [PNG](booking-class-diagram/booking-class-diagram.png) |
| Table reservation sequence | [puml](booking-sequence/booking-sequence.puml) | [PNG](booking-sequence/booking-sequence.png) |
| General domain classes | [puml](domain-class-diagram/domain-class-diagram.puml) | [PNG](domain-class-diagram/domain-class-diagram.png) |
| Ordering and payment classes | [puml](ordering-class-diagram/ordering-class-diagram.puml) | [PNG](ordering-class-diagram/ordering-class-diagram.png) |
| Payment outcomes | [puml](payment-sequence/payment-sequence.puml) | [PNG](payment-sequence/payment-sequence.png) |
| Pre-order and checkout exit | [puml](preorder-checkout-sequence/preorder-checkout-sequence.puml) | [PNG](preorder-checkout-sequence/preorder-checkout-sequence.png) |
| Staff operations classes | [puml](staff-class-diagram/staff-class-diagram.puml) | [PNG](staff-class-diagram/staff-class-diagram.png) |
| Customer use cases | [puml](use-case-customer/use-case-customer.puml) | [PNG](use-case-customer/use-case-customer.png) |
| General use cases | [puml](use-case-general/use-case-general.puml) | [PNG](use-case-general/use-case-general.png) |
| Manager use cases | [puml](use-case-manager/use-case-manager.puml) | [PNG](use-case-manager/use-case-manager.png) |
| Waiter use cases | [puml](use-case-waiter/use-case-waiter.puml) | [PNG](use-case-waiter/use-case-waiter.png) |
| Waiter-call sequence | [puml](waiter-call-sequence/waiter-call-sequence.puml) | [PNG](waiter-call-sequence/waiter-call-sequence.png) |

### State diagrams

Standalone editable views from Lab 8.

| State machine | Editable source | PNG |
| --- | --- | --- |
| Payment | [drawio](state-payment/state-payment.drawio) | [PNG](state-payment/state-payment.png) |
| PreOrder | [drawio](state-preorder/state-preorder.drawio) | [PNG](state-preorder/state-preorder.png) |
| WaiterCall | [drawio](state-waiter-call/state-waiter-call.drawio) | [PNG](state-waiter-call/state-waiter-call.png) |

## Rendering

- **draw.io.** Open the source and export PNG at 300% with a white background and a small border. Fit the export to the diagram content.
- **PlantUML.** Use PlantUML 1.2025.2. It saves the PNG next to the specified source. For use cases, check Graphviz with `java -jar $plantUmlJar -testdot`.

```powershell
$plantUmlJar = 'C:/path/to/plantuml-1.2025.2.jar'
$diagram = 'assets/diagrams/booking-sequence/booking-sequence.puml'
java -jar $plantUmlJar -charset UTF-8 -checkonly $diagram
java -jar $plantUmlJar -charset UTF-8 -tpng -Sdpi=180 $diagram
```

## Maintenance

Edit the source and regenerate its PNG. Inspect labels, connectors, and guards at document size. Update Markdown links when a path changes. Keep one source format per diagram.
