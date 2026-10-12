# ADR-006 — Geolocalização com `geography` + NetTopologySuite

- **Status:** Aceito
- **Data:** 12/10/2026

## Contexto
A funcionalidade central (RF001) é "unidades num raio de X km, ordenadas por distância", com resposta em até 500 ms (RNF001).

## Decisão
Armazenar a localização como `geography` (SRID 4326, WGS84) com **índice espacial**. Mapear com NetTopologySuite (`Point`) no EF Core. A distância é calculada no banco (`STDistance`, em metros).

O Value Object de domínio `GeoLocation` (latitude/longitude) **não** depende do NetTopologySuite. A conversão para `Point` acontece na Infrastructure.

## Alternativas
- **Colunas `Latitude`/`Longitude` decimais + fórmula de Haversine em SQL:** funciona, mas não usa índice espacial (varre a tabela inteira).
- **Calcular em memória:** inviável com 50 mil+ unidades.
- **Serviço externo (Google Places):** custo por requisição e dependência dos dados de terceiros.

## Consequências
- (+) Busca eficiente e precisa (considera a curvatura da Terra).
- (−) Armadilha conhecida: `new Point(x: longitude, y: latitude)`. Deve haver um teste cobrindo isso.
