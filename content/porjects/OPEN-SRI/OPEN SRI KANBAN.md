---
kanban-plugin: board
---

## Backlog

## TODO

- [ ] **_RetentionXmlSerializer_**

  infrastructure/serializers
  - [ ] Análogo a InvoiceXmlSerializer
  - [ ] Para comprobantes de retención
  - [ ] Implementa XmlSerializer

## Bugs/Quick Fixes/High Priority

- [ ] **_SriAuthorizationService_**

  infrastructure/sri – consulta autorización
  - [x] Implementa SriGateway.checkAuth()
  - [x] Llama WS Autorización SOAP
  - [x] Retorna estado + clave autorización
  - [ ] Reintento configurable

## In Progress

## Done

**Complete**

- [x] **_Test integración E2E_**

  Requiere ambiente SRI Pruebas activo
  - [x] Enviar factura real a SRI Pruebas
  - [x] Consultar autorización
  - [x] Verificar XML autorizado
  - [x] Depende de todos los módulos anteriores

- [x] **_InvoiceValidationRule_**

  domain/validation
  - [x] Valida RUC del emisor
  - [x] Valida número de documento
  - [x] Valida que items no estén vacíos
  - [ ] Usa Specification pattern

- [x] **_CheckAuthorizationUseCase_**

  application/usecases
  - [x] Consulta estado de autorización por clave
  - [x] Retorna Autorizado/Devuelto/Pendiente
  - [x] Test con mock SriGateway

- [x] **_SendInvoiceUseCase_**

  application/usecases
  - [x] Orquesta: validar → serializar → firmar → enviar
  - [x] Usa puertos (no implementaciones)
  - [x] Retorna resultado con clave acceso
  - [x] Test con mocks de puertos

- [x] **_SriReceiptService_**

  infrastructure/sri – envío XML
  - [x] Implementa SriGateway.send()
  - [x] Llama WS Recepcion SOAP
  - [x] Parsea respuesta RECIBIDA/DEVUELTA
  - [ ] Propaga SriException en error

- [x] **_SOAP WS layer_**

  infrastructure/ws/client + request + response
  - [x] DTO RecepcionRequest/Response
  - [x] DTO AutorizacionRequest/Response
  - [x] Cliente SOAP (JAX-WS o Axis2)
  - [x] Mapeo de respuesta a dominio

- [x] - [x] Crear record Paymet
  - [x] Editar todos los models a 2 decimales
  - [x] Añadir un paso mas al builder y cambiar el orden del call
  - [x] Crear un enum para el currency
  - [x] Tambien editar en el domain
  - [x] Arreglar los test
  - [x] Crear nuevos test comparando los xml generados con los xsd del SRI
- [x] - [x] Acabar los TODOS
- [x] **_Excepciones compartidas_**

  shared/exceptions
  - [x] SriException
  - [x] XmlSerializationException
  - [x] CertificateException
  - [x] Jerarquía clara, sin dependencias externas

- [x] **_XadesSigner_**

  infrastructure/crypto/signing
  - [x] Firma XML con XAdES-BES
  - [x] Implementa DocumentSigner
  - [x] Usa CertificateLoader internamente
  - [x] Test: firma verificable

- [x] **_CertificateLoader_**

  infrastructure/crypto/certificates
  - [x] Carga .p12 desde bytes
  - [x] Expone PrivateKey + X509Certificate
  - [x] Test con certificado dummy
  - [x] Maneja alias y password

- [x] **_Puertos de aplicación_**

  application/ports – interfaces
  - [x] interface XmlSerializer
  - [x] interface DocumentSigner
  - [x] interface SriGateway
  - [x] Ninguna dependencia de infraestructura

- [x] **_Arreglar Step de Items en Invoice Builder_**
  - [x] Arreglar que reciba una lista de items
  - [ ] separar la logica de Totals a una clase externa
- [x] **_InvoiceXmlSerializer_**

  infrastructure/serializers
  - [x] Genera XML según esquema SRI
  - [x] Usa JAXB o builder manual
  - [x] Implementa puerto XmlSerializer
  - [x] Test: XML válido contra XSD del SRI

- [x] **_TotalTax Fix_**
  - [x] Eliminar el campo de tarifa del total de los impuestos
  - [x] Verificar que no se dañe el calculo de los totales
  - [ ] Hacer testing de este fallo en especifico para tener como prueba del error
- [x] **_SRIAcessKeyGenerator_**

  infrastructure/crypto
  - [x] Formato: fechaEmisión+tipoDoc+RUC+ambiente+serie+secuencial+codigoNumérico+tipoEmisión
  - [x] Módulo 11 para dígito verificador
  - [x] Cubrir con tests unitarios

- [x] **_OpenSRIClient + Builder_**

  api/client – entry point SDK
  - [x] OpenSRIClientBuilder fluido
  - [x] Campos: environment, certificate, password, alias, issuerProfile, timeout
  - [x] Valida configuración en build()
  - [x] Test: builder construye sin lanzar excepción (ya existe)

---

## Archive

- [x] Modelo de dominio Invoice

  entities, value objects, aggregate root
  - Invoice, InvoiceItem, Tax, Totals
  - Totals.from(items)
  - Tax.value() derivado
  - Inmutabilidad garantizada

- [x] InvoiceBuilder

  API fluida con pasos tipados
  - Step-builder (ItemStep → BuildStep)
  - Calcula Totals en build()
  - No acepta Totals manual
  - doneItems() obliga al menos 1 item

- [x] Value objects base

  Ruc, NationalId, IssueDate, DocumentNumber
  - Validaciones en constructor
  - Inmutables
  - Ruc (13 dígitos), NationalId (10 dígitos)

- [x] Tests de facturación

  InvoiceBuilderTest – 2 escenarios
  - Test IVA 15% un item
  - Test agrupación IVA+ICE
  - Agrupación por code+rateCode verificada

%% kanban:settings

```
{"kanban-plugin":"board","list-collapse":[false,false,false,false,false],"show-checkboxes":false}
```

%%
