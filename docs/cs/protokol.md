# Protokol a formát zpráv
Na této stránce najdete dokumentaci k protokolu, se kterým lze se Scoreboard Serverem komunikovat. Rozhraní je založeno na WebSocketech, které využívá zprávy ve formátu JSON. Scoreboard Server všechny zprávy přeposílá všem naslouchajícím klientům (broadcast).

## Formát zprávy
Zprávy jsou posílány ve vyměnném formátu JSON, který se serializovaný do textového řetězce. 

```typescript
interface Message {
    target: MessageTarget,
    data: unknown
}
```

## Cíle zprávy
|Cíl|Vysvětlení|Kapitola|
|:--|:--|:--|
|`CF_CONFIG`|Konfigurace parametrů časomíry (hrací čas, počet současně měřených trestů, ...)|[Konfigurace časomíry](#konfigurace-casomiry)|
|`CF_TIME`|Ovládání času|[Nastavení a správa hracího času](#nastaveni-a-sprava-hraciho-casu)|
|`CF_TIMEOUT`|Ovládání timeoutů|[Nastavení timeoutu](#nastaveni-timeoutu)|

## Konfigurace časomíry

### Nastavení hracího času
```typescript
interface ConfigMessage {
    target: "CF_CONFIG",
    data: {
        periods?: number;
        periodMinutes?: number | number[];
        overtimeEnable?: boolean;
        overtimeMinutes?: number;
        penaltyshotsEnable?: boolean;
        penaltyshotsSeries?: number;
        simutaneouslyRunningPenalties?: number;
        personalPenaltyEnable?: boolean;
        personalPenaltyShow?: boolean;
        penaltyShowAnnouncement?: boolean;
    }
}
```

|Konfigurační možnost|Vysvětlení|Výchozí hodnota|
|:---|:---|:---|
|`periods`|Nastavení počtu částí základního hracího času (třetiny, poloviny, ...).|3|
|`periodMinutes`|Počet minut na hrhací část. Je možné pomocí pole nastavit trvání jednotlivých hracích částí.|20|
|`overtimeEnable`|Zapnutí nebo vypnutí prodloužení.|`true`|
|`overtimeMinutes`|Počet minut na prodloužení.|`10`|
|`penaltyshotsEnable`|Zapnutí nebo vypnutí trestných střílení po prodloužení.|`true`|
|`penaltyshotsSeries`|Počet základních sérií trestných střílení po prodloužení.|`5`|
|`simutaneouslyRunningPenalties`|Počet vyloučení na trestnou lavici, které se měří družstvu současně.|`2`|
|`personalPenaltyEnable`|Zapnutí nebo vypnutí měření osobních trestů|`true`|
|`personalPenaltyShow`|Zapnutí nebo vypnutí ukazování osobních trestů na časomíře|`true`|
|`penaltyShowAnnouncement`|Zapnutí nebo vypnutí ukazování udělení trestu |`false`|


=== "3x15 minut hracího času"
    ```typescript
    interface TimeoutMessage {
        target: "CF_TIMEOUT",
        data: {
            team: "home" | "away",  //Který tým si bere timeout
            length?: number         //Počet sekund pro timeout. Výchozí hodnota je 30.
        }
    }
    ```
=== "3x10 minut a 5 minutové prodloužení "
    ```typescript
    interface TimeoutMessage {
        target: "CF_TIMEOUT",
        data: {
            team: "home" | "away",  //Který tým si bere timeout
            length?: number         //Počet sekund pro timeout. Výchozí hodnota je 30.
        }
    }
    ```
=== "2x14 minut"
    ```typescript
    interface TimeoutMessage {
        target: "CF_TIMEOUT",
        data: {
            team: "home" | "away",  //Který tým si bere timeout
            length?: number         //Počet sekund pro timeout. Výchozí hodnota je 30.
        }
    }
    ```


## Nastavení a správa hracího času
=== "Rozhraní"
    ```typescript
    interface TimeoutMessage {
        target: "CF_TIME",
        data: {
            running: boolean;
        }
    }
    ```

## Vyloučení


## Nastavení timeoutu
Timeout je oddechový čas, které si družstvo vyžádá. Na časomíře je spuštěn automaticky po přijetí zprávy o vyžádáném timeoutu.

=== "Rozhraní pro zprávu o timeoutu"
    ```typescript
    interface TimeoutMessage {
        target: "CF_TIMEOUT",
        data: {
            team: "home" | "away",  //Který tým si bere timeout
            length?: number         //Počet sekund pro timeout. Výchozí hodnota je 30.
        }
    }
    ```
=== "Spuštění timeoutu pro domácí" 
    ```typescript
    {
        target: "CF_TIMEOUT",
        data: {
            "team": "home"
        }
    }
    ```
=== "Spuštění timeoutu pro hosty v délce 45 sekund" 
    ```typescript
    {
        target: "CF_TIMEOUT",
        data: {
            "team": "home",
            "length" 45
        }
    }
    ```