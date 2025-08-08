# Dgraph

## Dgraph Certificate

`dgraph cert` program creates and manages CA-signed certificate and private keys using a generated Dgraph Root CA.

1. Root CA certificate/key pair.
2. Node certificate/key pair - shared by the dgraph Alpha nodes and used for accepting TLS connection.
3. Client certificate/key pair - used by client to communicate with Dgraph Alpha server nodes.

## Define Directives and index

director.film: [uid] @reverse .
actor.film: [uid] @count .
genre: [uid] @reverse .
initial_release_date: dateTime @index(year) .
name: string @index(exact, term) @lang .
starring: [uid] .
performance.film: [uid] .
performance.character_note: string .
performance.character: [uid] .
performance.actor: [uid] .
performance.special_performance_type: [uid] .
type: [uid] .

type Person {
    name
    director.film
    actor.film
}

type Movie {
    name
    initial_release_date
    genre
    starring
}

type Genre {
    name
}

type Performance {
    performance.film
    performance.character
    performance.actor
}

## Query

### Function

#### Term matching

- allofterms(predicate,"value value")

- anyofterms(predicate,"value value")

```yaml
{
    me(func: eq(name@en, "steven spielberg"))@filter(has(director.film)){
        name@en
        director.film @filter(anyofterms(name@en,"war spies"))
        {
            name@en
        }
    }
}
```

- regular Expressions
  - schema types: string
  - index: trigram

- fuzzy matching : matches predicate values by calculating the `Levenshtein distance` to the string.
  - schema: string
  - index: trigram

- type: all nodes of specific type.

```yaml
{
    q(func: type(Animal)){
        uid
        name
    }
}
```

### Shortest Path

0x185a9

0xa9298

type Device{
    blade_host_id: Int
    is_it_switch: String
    name: String
    is_it_blade_host: String
    in_service: String
    building: String
    type: String
    device_id: Int! @id
    rack: String
    device_sub_type: String
    child_devices_name: String
    blade_device_connection: Device
    switches_connection: [Device]
    server_connection : Server
}

### JPW1 RIaaS Network

```mermaid
graph TD
    DC[DC/Internet]
    AD[AD-NET]
    A1(jpw1-4-i109-dss-1)
    A2(jpw1-4-j103-dss-2)
    A3(jpw1-i107-adcsw01)
    A4(jpw1-j105-adcsw02)

    B1(jpw1-4-i108-riaas-dbl-1001)
    B2(jpw1-4-j104-riaas-dbl-1002)
    BS3(jpw1-4-i108-riaas-sbl-1001)
    BS4(jpw1-4-j104-sbl-1002)

    C1(jpw1-4-i108-riaas-dsp-1001)
    C2(jpw1-4-j104-riaas-dsp-1002)
    CS3(jpw1-4-i108-sss-1001)
    CS4(jpw1-4-j104-sss-1002)

    D1(jpw1-4-k101-riaas-dhl-1001)
    D2(jpw1-4-k102-riaas-dhl-1002)
    D3(jpw1-4-k103-riaas-dhl-1003)
    DS1(jpw1-4-i108-riaas-ssp-1001)
    DS2(jpw1-4-j104-riaas-ssp-1002)
    DS3(jpw1-4-i108-ssl-1001)
    DS4(jpw1-4-j104-ssl-1002)

    E1("zaas401-...<br> ESXi (Max:12)")
    E2("zaas401-...<br> ESXi (Max:12)")
    E3("zaas401-...<br> ESXi (Max:12).....")
    ES1(jpw1-4-k101-riaas-shl-1001)
    ES2(jpw1-4-k102-riaas-shl-1002)
    ES3(jpw1-4-k102-riaas-shl-1003)
    

    DC --> A1
    DC --> A2
    AD -.- A3
    AD -.- A4
    A1 --> B1
    A1 --> B2
    A2 --> B1
    A2 --> B2
    A3 -.- B1
    A3 -.- B2
    A4 -.- B1
    A4 -.- B2
    A1 --> BS3
    A1 --> BS4
    A2 --> BS3
    A2 --> BS4

    B1 --> C1
    B1 --> C2
    B2 --> C1
    B2 --> C2
    BS3 --> CS3
    BS3 --> CS4
    BS4 --> CS3
    BS4 --> CS4

    C1 --> D1
    C1 --> D2
    C1 --> D3
    C2 --> D1
    C2 --> D2
    C2 --> D3
    CS3 --> DS1
    CS3 --> DS2
    CS4 --> DS1
    CS4 --> DS2
    CS3 --> DS3
    CS3 --> DS4
    CS4 --> DS3
    CS4 --> DS4

    D1 --> E1
    D1 --> E2
    D2 --> E1
    D2 --> E2
    D3 --> E3
    DS1 --> ES1
    DS1 --> ES2
    DS2 --> ES1
    DS2 --> ES2
    DS1 --> ES3
    DS2 --> ES3
    ES1 --> E1
    ES1 --> E2
    ES2 --> E1
    ES2 --> E2
    ES3 --> E3


```