# Formato de documentos de entrada

Esta guia describe el formato de los documentos que el app procesa desde archivos XML o JSON. La estructura se obtiene de los DTOs en [src/main/java/com/tamaysistemas/localfiles/xml](src/main/java/com/tamaysistemas/localfiles/xml) y ejemplos en [xmls/factura001-001-0000225.xml](xmls/factura001-001-0000225.xml) y [xmls/0004428.xml](xmls/0004428.xml).

## Formato general

- XML: el nodo raiz debe ser `DocumentoElectronico` o `Anulacion`.
- JSON: se usan los mismos nombres de campos que en XML.
- Campos vacios u omitidos se aceptan cuando el campo es opcional.
- Prefijos de archivo ignorados por el visor: `gen_` y `err_`.
- Para anulaciones por archivo se usan prefijos `anul_` o `anulacion_`.
- Una anulacion por archivo; el archivo original no se elimina automaticamente.

### Ejemplo minimo (XML)

```xml
<DocumentoElectronico>
  <TipoDocumento>1</TipoDocumento>
  <NumeroDeDocumentoCompleto>001-001-0000225</NumeroDeDocumentoCompleto>
  <FechaDocumento>2026-01-28 15:25:32</FechaDocumento>
  <TipoTransaccion>2</TipoTransaccion>
  <TipoImpuesto>5</TipoImpuesto>
  <Total>28575000.00</Total>
  <TipoOperacion>2</TipoOperacion>
  <IndicadorPresencial>1</IndicadorPresencial>
  <Condicion>1</Condicion>
  <NumeroTimbrado>18555686</NumeroTimbrado>
  <Detalles>
    <DetalleDocumento>
      <CodigoProducto>410</CodigoProducto>
      <Descripcion>BALANCEO</Descripcion>
      <TipoIva>1</TipoIva>
      <TasaIva>10</TasaIva>
      <Precio>75000.00</Precio>
      <Cantidad>6.00</Cantidad>
      <Descuento>0.00</Descuento>
      <Unidad>UNI</Unidad>
    </DetalleDocumento>
  </Detalles>
  <Cliente>
    <RazonSocial>HEPP S.A.</RazonSocial>
    <TipoContribuyente>1</TipoContribuyente>
    <Ruc>80011876</Ruc>
    <TipoDocumento>1</TipoDocumento>
    <Correo>contacto@empresa.com</Correo>
    <Direccion>Bomberos Voluntarios c/Ruta VI</Direccion>
    <CodigoDepartamento>8</CodigoDepartamento>
    <CodigoDistrito>97</CodigoDistrito>
    <CodigoCiudad>1915</CodigoCiudad>
  </Cliente>
</DocumentoElectronico>
```

## DocumentoElectronico

Nodo raiz: `DocumentoElectronico`

- `TipoDocumento` (numero, 1-8). 1=Factura, 4=Autofactura, 5=Nota de credito, 6=Nota de debito, 7=Remision.
- `NumeroTimbrado` (numero). En prueba puede usarse el RUC.
- `CodigoEstablecimiento` (numero) y `PuntoExpedicion` (numero). Se pueden omitir si ya vienen en `NumeroDeDocumentoCompleto`.
- `IdSistema` (texto, max 200). Opcional.
- `CodigoSeguridad` (texto). Opcional; el app puede generarlo.
- `NumeroDeDocumentoCompleto` (texto, 001-001-0000001).
- `FechaDocumento` (texto, `yyyy-MM-dd HH:mm:ss`).
- `FechaProceso` (texto, `yyyy-MM-dd HH:mm:ss`). Se completa automaticamente.
- `TipoTransaccion` (numero, 1-13).
- `TipoOperacion` (numero, 1-4). 1=B2B, 2=B2C, 3=B2G, 4=B2F.
- `TipoImpuesto` (numero, 1-5). 1=IVA, 2=ISC, 3=Renta, 4=Ninguno, 5=IVA-Renta.
- `Total` (numero, >0). Total recalculado para validar detalles.
- `DescuentoGlobalPorc` o `PorcentajeDescuentoGlobal` (numero, 0-100). Opcional.
- `IndicadorPresencial` (numero, 1,2,3,4,5,6,9).
- `Condicion` (numero, 1=Contado, 2=Credito).
- `Cliente` (nodo).
- `Detalles` (lista `DetalleDocumento`, al menos 1).
- `MetodosDePago` (lista `MetodoDePago`). Si se omite, se asume efectivo.
- `TipoCredito` (numero, 1-2). Solo si `Condicion=2`.
- `TiempoCredito` (texto). Solo si `Condicion=2`.
- `DiaVencimientoCuotas` (numero, 1-31). Solo si `Condicion=2`.
- `CantidadCuotas` (numero). Solo si `Condicion=2`.
- `MontoInicial` (numero). Opcional.
- `MotivoEmision` (numero, 1-8). Solo para nota de credito.
- `Cuotas` (lista `Cuota`).
- `Relacionados` (lista `DocumentoRelacionado`).
- `RemisionFields` (nodo). Solo si `TipoDocumento=7`.
- `CodigoMoneda` (texto, default `PYG`).
- `PrecioTasaCambio` (numero, default `1.0`).

## Cliente

Nodo: `Cliente`

- `CodigoCliente` (texto). Opcional.
- `Ruc` (texto). RUC sin DV; o documento si no contribuyente.
- `Dv` (texto). DV del RUC.
- `RazonSocial` (texto).
- `NombreFantasia` (texto). Opcional.
- `TipoContribuyente` o `TipoCliente` (numero, 0-2). 0=No contribuyente, 1=Persona fisica, 2=Persona juridica.
- `TipoDocumento` (numero, 1-9). Obligatorio si no contribuyente.
- `DescripcionTipoDocumento` (texto). Requerido si `TipoDocumento=9`.
- `NumeroLineaBaja` o `LineaBaja` (texto). Opcional.
- `NumeroCelular` o `Celular` (texto). Opcional.
- `Correo` o `Email` (texto). Opcional.
- `PaisOrigen` (texto, ISO 3166-1 alfa-3). Default `PRY`.
- `Direccion` (texto). Opcional.
- `NumeroCasa` (numero). Obligatorio si se informa `Direccion`.
- `CodigoDepartamento`, `CodigoDistrito`, `CodigoCiudad` (numero). Obligatorio si se informa `Direccion`.

## DetalleDocumento

Nodo: `DetalleDocumento`

- `CodigoProducto` o `Codigo` (texto). No debe repetirse dentro del documento.
- `Descripcion` (texto).
- `TipoIva` (numero, 1-4).
- `TasaIva` o `Tasa` (numero, 0, 5, 10).
- `Precio` (numero, >0).
- `Cantidad` (numero, >0).
- `Descuento` (numero, >=0).
- `Unidad` o `UnidadId` (texto). Ejemplo: `UNI`.
- `SystemId` (texto). Opcional.

## MetodoDePago

Nodo: `MetodoDePago`

- `TipoDePago` o `TipoPago` (numero). Valores permitidos: 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,99.
- `Total` (numero, >0).
- `TarjetaDenominacion` o `DenominacionTarjeta` (numero). 1,2,3,4,5,6,99. Solo para tarjetas.
- `DescripcionDenominacionTarjeta` (texto). Requerido si denominacion=99.
- `TarjetaFormaProcesamiento` o `TipoProcesamientoTarjeta` (numero). 1,2,9. Solo para tarjetas.
- `NumeroCheque` (texto). Solo si aplica.
- `BancoCheque` (texto). Solo si aplica.

## Cuota

Nodo: `Cuota`

- `Numero` (numero). Numero de cuota.
- `Vencimiento` (fecha, `yyyy-MM-dd`).
- `Monto` (numero, >0).

## DocumentoRelacionado

Nodo: `DocumentoRelacionado`

- `Tipo` (numero, 1-2). 1=Electronico (usar CDC), 2=Impreso (usar numero completo).
- `Numero` (texto). Formato `001-001-0000001` para impresos.

## Anulacion

Nodo raiz: `Anulacion`

- En JSON se acepta el wrapper:

```json
{
  "Anulacion": {
    "CDC": "0180...",
    "MotivoAnulacion": "Motivo de la anulacion"
  }
}
```

- `IdSistema` (texto). Opcional si se informa CDC o numero de documento.
- `CDC` (texto). Opcional si se informa IdSistema o numero de documento.
- `NumeroDocumentoCompleto` (texto). Opcional si se informa IdSistema o CDC.
- `TipoDocumento` (numero). Requerido si se usa numero de documento.
- `NumeroTimbrado` (numero). Requerido si se usa numero de documento.
- `CodigoEstablecimiento` (numero). Requerido si se usa numero de documento.
- `PuntoExpedicion` (numero). Requerido si se usa numero de documento.
- `NumeroDocumento` (numero). Requerido si se usa numero de documento.
- `MotivoAnulacion` (texto). Requerido.

Metodos de identificacion soportados:

- Por `CDC` (recomendado).
- Por componentes: `NumeroTimbrado`, `CodigoEstablecimiento`, `PuntoExpedicion`, `NumeroDocumento`.
- Por numero completo: `NumeroDocumentoCompleto`.

Respuesta (salida en la misma carpeta):

- Exito: `gen_*.xml` con `RespuestaAnulacion` y `Exito=true`.
- Error: `err_*.xml` con `RespuestaAnulacion` y `Exito=false`.

# Remisiones

Nodo: `RemisionFields` (solo cuando `TipoDocumento=7`)

- `MotivoEmision` (numero).
- `ResponsableEmision` (numero).
- `KilometrosEstimados` (numero).
- `FechaEmisionFacturaFutura` (fecha, `yyyy-MM-dd`).
- `TipoTransporte` (numero).
- `ModalidadTransporte` (numero).
- `ResponsableCosto` (numero).
- `CodigoCondicionNegociacion` (texto).
- `NumeroManifiesto` (texto).
- `NumeroDespachoImportacion` (texto).
- `FechaInicioTransporte` (fecha, `yyyy-MM-dd`).
- `FechaFinTransporte` (fecha, `yyyy-MM-dd`).
- `CodigoPaisDestino` (texto).
- `DireccionSalida` (texto).
- `NumeroSalida` (numero).
- `ComplementoSalida1` (texto).
- `ComplementoSalida2` (texto).
- `DepartamentoSalida` (numero).
- `DistritoSalida` (numero).
- `CiudadSalida` (numero).
- `TelefonoSalida` (texto).
- `DireccionEntrega` (texto).
- `NumeroEntrega` (numero).
- `ComplementoEntrega1` (texto).
- `ComplementoEntrega2` (texto).
- `DepartamentoEntrega` (numero).
- `DistritoEntrega` (numero).
- `CiudadEntrega` (numero).
- `TelefonoEntrega` (texto).
- `TipoVehiculo` (numero).
- `MarcaVehiculo` (texto).
- `TipoIdentificacionVehiculo` (numero).
- `NumeroIdentificacionVehiculo` (texto).
- `DatosAdicionalesVehiculo` (texto).
- `NumeroVuelo` (texto).
- `NaturalezaTransportista` (numero).
- `RazonSocialTransportista` (texto).
- `DocumentoTransportista` (texto).
- `DVTransportista` (texto).
- `TipoDocumentoTransportista` (texto).
- `NacionalidadTransportista` (texto).
- `DireccionTransportista` (texto).
- `DocumentoChoferTransportista` (texto).
- `NombreChoferTransportista` (texto).
- `DireccionChoferTransportista` (texto).
- `RazonSocialAgenteTransporte` (texto).
- `RucAgenteTransporte` (texto).
- `DVAgenteTransporte` (texto).
- `DireccionAgenteTransporte` (texto).

Opciones y codigos usados (segun omoguahe):

- `MotivoEmision`: 1=Traslado por venta, 2=Traslado por consignacion, 3=Exportacion, 4=Traslado por compra, 5=Importacion, 6=Traslado por devolucion, 7=Traslado entre locales de la empresa, 8=Traslado de bienes por transformacion, 9=Traslado de bienes por reparacion, 10=Traslado por emisor movil, 11=Exhibicion o demostracion, 12=Participacion en ferias, 13=Traslado de encomienda, 14=Decomiso, 99=Otro.
- `ResponsableEmision`: 1=Emisor de la factura, 2=Poseedor de la factura y bienes, 3=Empresa transportista, 4=Despachante de Aduanas, 5=Agente de transporte o intermediario.
- `TipoTransporte`: 1=Propio, 2=Tercero.
- `ModalidadTransporte`: 1=Terrestre, 2=Fluvial, 3=Aereo, 4=Multimodal.
- `ResponsableCosto`: 1=Emisor de la FE, 2=Receptor de la FE, 3=Tercero, 4=Agente intermediario del transporte, 5=Transporte propio.
- `CodigoCondicionNegociacion`: CFR, CIF, CIP, CPT, DAP, DAT, DDP, EXW, FAS, FCA, FOB.
- `TipoVehiculo`: mismos valores que `ModalidadTransporte`.
- `TipoIdentificacionVehiculo`: 1=Numero de identificacion del vehiculo, 2=Numero de matricula del vehiculo.
- `NaturalezaTransportista`: 1=Contribuyente, 2=No contribuyente.
- `TipoDocumentoTransportista`: (vacio/RUC), 1=Cedula de identidad, 2=Pasaporte, 3=Cedula extranjera, 4=Carnet de residencia.

Notas:

- Si `NaturalezaTransportista=1` (Contribuyente), se usa `DVTransportista`.
- En el backend, si `CodigoPaisDestino` es distinto de `PRY`, se copian los codigos de salida a los de entrega.
- Para remisiones no se validan impuestos ni precios en los detalles; se esperan al menos codigo, descripcion y cantidad.

## Validaciones (backend web)

Estas reglas se aplican cuando `tax_payer.should_perform_validations = true` en el backend (ver modelos Rails en [others/fse-facturador-externo-web/app/models](others/fse-facturador-externo-web/app/models)).

### DocumentoElectronico

- `document_type` en 1..8.
- `system_id` max 200.
- `transaction_type` en 1..13 (solo cuando corresponde).
- `tax_type` en 1..5.
- `global_discount_percentage` entre 0 y 100.
- `exchange_type` en 1..4.
- `presence_indicator` en {1,2,3,4,5,6,9}.
- `condition` en 1..2.
- `credit_type` en 1..2 (cuando `condition=2`).
- `credit_time` requerido (cuando `condition=2`).
- `installments_due_day` entre 1 y 31 (cuando `condition=2`).
- `emission_motive` en 1..8 (cuando aplica).
- `document_number` entero >0 y <= 9999999, max 7 digitos.
- `NumeroDeDocumentoCompleto` debe cumplir formato `001-001-0000001`.
- Debe tener al menos un detalle y al menos un metodo de pago.
- `document_number` es unico por `CodigoEstablecimiento`, `PuntoExpedicion` y timbrado.

### DetalleDocumento

- `code` requerido y unico dentro del documento.
- `description` requerido.
- `iva_type` en 1..4 (no se valida para remision).
- `tax_rate` en 0,5,10 (no se valida para remision).
- `price` > 0 (no se valida para remision).
- `quantity` > 0.
- `discount` >= 0.
- Si `iva_type` es 2 o 3, `tax_rate` debe ser 0; si es 1 o 4, `tax_rate` debe ser 5 o 10.

### MetodoDePago

- `payment_type` en {1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,99}.
- `total` > 0.
- Para tarjetas (`payment_type` 3 o 4):
  - `card_denomination` en {1,2,3,4,5,6,99}.
  - `card_type_of_processing` en {1,2,9}.

### DocumentoRelacionado

- `document_type` requerido y en 1..8.

## Ejemplos

### Factura (XML)

```xml
<DocumentoElectronico>
  <TipoDocumento>1</TipoDocumento>
  <NumeroDeDocumentoCompleto>001-001-0000100</NumeroDeDocumentoCompleto>
  <FechaDocumento>2026-02-13 10:15:00</FechaDocumento>
  <TipoTransaccion>1</TipoTransaccion>
  <TipoImpuesto>1</TipoImpuesto>
  <Total>110000.00</Total>
  <TipoOperacion>1</TipoOperacion>
  <IndicadorPresencial>1</IndicadorPresencial>
  <Condicion>1</Condicion>
  <NumeroTimbrado>80061256</NumeroTimbrado>
  <Detalles>
    <DetalleDocumento>
      <CodigoProducto>PROD-001</CodigoProducto>
      <Descripcion>Producto de prueba</Descripcion>
      <TipoIva>1</TipoIva>
      <TasaIva>10</TasaIva>
      <Precio>100000.00</Precio>
      <Cantidad>1.00</Cantidad>
      <Descuento>0.00</Descuento>
      <Unidad>UNI</Unidad>
    </DetalleDocumento>
  </Detalles>
  <MetodosDePago>
    <MetodoDePago>
      <TipoPago>1</TipoPago>
      <Total>110000.00</Total>
    </MetodoDePago>
  </MetodosDePago>
  <Cliente>
    <RazonSocial>Cliente Ejemplo S.A.</RazonSocial>
    <TipoContribuyente>2</TipoContribuyente>
    <Ruc>494829</Ruc>
    <Dv>7</Dv>
    <Correo>cliente@ejemplo.com</Correo>
    <Direccion>Av. Principal 123</Direccion>
    <NumeroCasa>123</NumeroCasa>
    <CodigoDepartamento>1</CodigoDepartamento>
    <CodigoDistrito>1</CodigoDistrito>
    <CodigoCiudad>1</CodigoCiudad>
  </Cliente>
</DocumentoElectronico>
```

### Nota de credito (XML)

```xml
<DocumentoElectronico>
  <TipoDocumento>5</TipoDocumento>
  <NumeroDeDocumentoCompleto>001-001-0000101</NumeroDeDocumentoCompleto>
  <FechaDocumento>2026-02-13 10:45:00</FechaDocumento>
  <TipoTransaccion>1</TipoTransaccion>
  <TipoImpuesto>1</TipoImpuesto>
  <Total>55000.00</Total>
  <TipoOperacion>1</TipoOperacion>
  <IndicadorPresencial>1</IndicadorPresencial>
  <Condicion>1</Condicion>
  <NumeroTimbrado>80061256</NumeroTimbrado>
  <MotivoEmision>2</MotivoEmision>
  <Relacionados>
    <DocumentoRelacionado>
      <Tipo>1</Tipo>
      <Numero>01800612566001001000010022026021310150000001</Numero>
    </DocumentoRelacionado>
  </Relacionados>
  <Detalles>
    <DetalleDocumento>
      <CodigoProducto>PROD-001</CodigoProducto>
      <Descripcion>Devolucion parcial</Descripcion>
      <TipoIva>1</TipoIva>
      <TasaIva>10</TasaIva>
      <Precio>50000.00</Precio>
      <Cantidad>1.00</Cantidad>
      <Descuento>0.00</Descuento>
      <Unidad>UNI</Unidad>
    </DetalleDocumento>
  </Detalles>
  <MetodosDePago>
    <MetodoDePago>
      <TipoPago>1</TipoPago>
      <Total>55000.00</Total>
    </MetodoDePago>
  </MetodosDePago>
  <Cliente>
    <RazonSocial>Cliente Ejemplo S.A.</RazonSocial>
    <TipoContribuyente>2</TipoContribuyente>
    <Ruc>494829</Ruc>
    <Dv>7</Dv>
  </Cliente>
</DocumentoElectronico>
```

### Remision (XML)

```xml
<DocumentoElectronico>
  <TipoDocumento>7</TipoDocumento>
  <NumeroDeDocumentoCompleto>001-001-0000200</NumeroDeDocumentoCompleto>
  <FechaDocumento>2026-02-13 11:30:00</FechaDocumento>
  <TipoTransaccion>1</TipoTransaccion>
  <TipoImpuesto>1</TipoImpuesto>
  <Total>1.00</Total>
  <TipoOperacion>1</TipoOperacion>
  <IndicadorPresencial>1</IndicadorPresencial>
  <Condicion>1</Condicion>
  <NumeroTimbrado>80061256</NumeroTimbrado>
  <Detalles>
    <DetalleDocumento>
      <CodigoProducto>INS-001</CodigoProducto>
      <Descripcion>Insumos internos</Descripcion>
      <TipoIva>1</TipoIva>
      <TasaIva>10</TasaIva>
      <Precio>1.00</Precio>
      <Cantidad>1.00</Cantidad>
      <Descuento>0.00</Descuento>
      <Unidad>UNI</Unidad>
    </DetalleDocumento>
  </Detalles>
  <Cliente>
    <RazonSocial>Sucursal Central</RazonSocial>
    <TipoContribuyente>2</TipoContribuyente>
    <Ruc>87654321</Ruc>
    <Dv>3</Dv>
    <Direccion>Ruta 1 Km 10</Direccion>
    <NumeroCasa>10</NumeroCasa>
    <CodigoDepartamento>1</CodigoDepartamento>
    <CodigoDistrito>1</CodigoDistrito>
    <CodigoCiudad>1</CodigoCiudad>
  </Cliente>
  <RemisionFields>
    <MotivoEmision>7</MotivoEmision>
    <ResponsableEmision>1</ResponsableEmision>
    <KilometrosEstimados>25</KilometrosEstimados>
    <TipoTransporte>1</TipoTransporte>
    <ModalidadTransporte>1</ModalidadTransporte>
    <ResponsableCosto>5</ResponsableCosto>
    <CodigoCondicionNegociacion>EXW</CodigoCondicionNegociacion>
    <FechaInicioTransporte>2026-02-13</FechaInicioTransporte>
    <CodigoPaisDestino>PRY</CodigoPaisDestino>
    <DireccionSalida>Deposito Central</DireccionSalida>
    <NumeroSalida>100</NumeroSalida>
    <DepartamentoSalida>1</DepartamentoSalida>
    <DistritoSalida>1</DistritoSalida>
    <CiudadSalida>1</CiudadSalida>
    <DireccionEntrega>Sucursal Central</DireccionEntrega>
    <NumeroEntrega>10</NumeroEntrega>
    <DepartamentoEntrega>1</DepartamentoEntrega>
    <DistritoEntrega>1</DistritoEntrega>
    <CiudadEntrega>1</CiudadEntrega>
    <TipoVehiculo>1</TipoVehiculo>
    <MarcaVehiculo>Toyota</MarcaVehiculo>
    <TipoIdentificacionVehiculo>2</TipoIdentificacionVehiculo>
    <NumeroIdentificacionVehiculo>ABC-123</NumeroIdentificacionVehiculo>
    <NaturalezaTransportista>1</NaturalezaTransportista>
    <RazonSocialTransportista>Transporte Propio</RazonSocialTransportista>
    <DocumentoTransportista>80061256</DocumentoTransportista>
    <DVTransportista>6</DVTransportista>
  </RemisionFields>
</DocumentoElectronico>
```

### Respuestas generadas (XML)

Las respuestas de generacion usan el nodo `RespuestaGenerada` con los campos `CDC`, `QR`, `XML` y `PDFUrl`.

```xml
<RespuestaGenerada>
  <CDC>01800612566001001000010022026021310150000001</CDC>
  <QR>https://ekuatia.set.gov.py/consultas-test/qr?nVersion=150&amp;Id=01800612566001001000010022026021310150000001</QR>
  <XML>&lt;rDE&gt;...&lt;/rDE&gt;</XML>
  <PDFUrl>https://efacturador.tamaysistemas.com/electronic_documents/12345.pdf</PDFUrl>
</RespuestaGenerada>
```

```xml
<RespuestaGenerada>
  <CDC>01800612566001001000020022026021311300000002</CDC>
  <QR>https://ekuatia.set.gov.py/consultas-test/qr?nVersion=150&amp;Id=01800612566001001000020022026021311300000002</QR>
  <XML>&lt;rDE&gt;...&lt;/rDE&gt;</XML>
  <PDFUrl>https://efacturador.tamaysistemas.com/electronic_documents/12345.pdf</PDFUrl>
</RespuestaGenerada>
```
