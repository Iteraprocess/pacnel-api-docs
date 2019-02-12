# pacnel-api-docs


## Descripción API

El API es de tipo `JSON-REST` la autenticación se realiza por medio de los HEADERS siguientes: `APPTOKEN` y `APPSECRET`.
La url en la que está montado el servicio es: `https://www.cfdimax.com`


## Recursos

### Clientes 
Para poder timbrar con un rfc tenemos que crear un cliente, de manera que todos los CFDIS emitidos por el cliente quedarán vinculados a él.

#### Crear Cliente
Para crear un cliente basta con mandar llamar una peticion POST con los datos nombre y rfc a través del payload codificados en JSON

`POST /clientes/create`  
```
  {
    nombre:String, 
    rfc:String
  }
```

### Certificados
Para poder timbrar es necesario también que los clientes tengan registrado un certificado, para esto es necesario una request POST a la URL que se enlista sustituyendo el RFC por el del cliente al que le pertenece el certificado (es necesario que el cliente con ese RFC exista previamente)

#### Crear Certificado
`POST /clientes/certificado/${rfc}`
```
  {
    keyB64:String, 
    cerB64:String,
    password : String
  }
```


### Paquetes
También tenemos que registrar un paquete al cliente, el paquete nos sirve para controlar la cantidad de timbres que un cliente puede efectuar. en el campo `timbres_totales` se añade la cantidad de timbres que le vamos a permitir al cliente

#### Crear Paquetes
`POST /clientes/crearPaquete/{$rfc}`

```
  {
    descripcion:String, 
    timbres_totales:Int
  }
```



## Timbrado 
 Para timbrar tenemos 2 endpoints :

- Timbrado Real : `POST /timbrar/json`
- Timbrado Prueba: `POST /timbrar/prueba`

Para ambos endpoints se manda el XML en formato JSON sin SELLAR de la siguiente manera:
```

    {
        "cfdi:Comprobante":{
            "@version":"3.2",
            "@serie":"AGOSTO",
            "@folio":"343",
            "@fecha":"2016-08-05T08:55:24",
            "@formaDePago":"Una sola exibici\u00f3n",
            "@condicionesDePago":"PALMAS - 311 Fecha de pago: 2016-08-03 Referencia: PAL311AGO2016 PAGO CUOTA DE MTTO AGOSTO 2016.",
            "@metodoDePago":"01",
            "@tipoDeComprobante":"ingreso",
            "@LugarExpedicion":"CIUDAD MADERO TAMAULIPAS, M\u00e9xico",
            "@subTotal":"550.000000",
            "@Moneda":"MN",
            "@total":"550.000000",
            "cfdi:Emisor":{
                "@rfc":"ACR140919F80",
                "@nombre":"ASOCIACION DE CONDOMINOS RESIDENCIAL DEL PARQUE",
                "cfdi:DomicilioFiscal":{
                    "@calle":"COTO PARQUE BUGAMBILIAS",
                    "@noExterior":"609",
                    "@colonia":"JARDIN 20 DE NOVIEMBRE",
                    "@municipio":"CIUDAD MADERO",
                    "@estado":"TAMAULIPAS",
                    "@pais":"M\u00e9xico",
                    "@codigoPostal":"89440"
                },
                "cfdi:ExpedidoEn":{
                    "@calle":"COTO PARQUE BUGAMBILIAS",
                    "@noExterior":"609",
                    "@colonia":"JARDIN 20 DE NOVIEMBRE",
                    "@municipio":"CIUDAD MADERO",
                    "@estado":"TAMAULIPAS",
                    "@pais":"M\u00e9xico",
                    "@codigoPostal":"89440"
                },
                "cfdi:RegimenFiscal":{
                    "@Regimen":"ASOCIACION CIVIL"
                }
            },
            "cfdi:Receptor":{
                "@rfc":"XAXX010101000",
                "cfdi:Domicilio":{
                    "@estado":"Tamaulipas",
                    "@pais":"M\u00e9xico",
                    "@codigoPostal":"89440"
                }
            },
            "cfdi:Conceptos":{
                "cfdi:Concepto":{
                    "@cantidad":"1.00",
                    "@unidad":"No aplica",
                    "@descripcion":"CUOTA DE MANTENIMIENTO AGOSTO 2016",
                    "@valorUnitario":"550.000000",
                    "@importe":"550.000000"
                }
            }
        }
    }

## Timbrado Directo (Certificados Sellados)

Para timbrar un certificado directo se utlizará el endpoint: `api.cfdimax.com/cfdi33/timbrado`
Al cual se le mandará una estructura JSON de la siguiente manera

    {
      "cfdi" : "(String completa del XML Certificado)"
    }

Por ahora este servicio es privado y no cuenta con Autenticación alguna, con el fin de acelerar las pruebas
```