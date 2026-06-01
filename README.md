# ESP32 Transmissor - Monitoramento de Sensores via ESP-NOW

Firmware para ESP32 responsavel pela leitura de sensores ambientais e envio dos dados a um segundo ESP32 (receptor) utilizando o protocolo ESP-NOW.

---

## Funcionamento

A cada 2 segundos o dispositivo le todos os sensores, aciona os LEDs indicadores e transmite o pacote de dados ao receptor via ESP-NOW. Caso algum sensor apresente falha de leitura, o envio e suspenso ate que os dados sejam validos novamente.

---

## Hardware

| Componente | Pino | Dado coletado |
|---|---|---|
| HC-SR04 (ultrassonico) | TRIG: 26, ECHO: 27 | Nivel do tanque (%) |
| DHT22 | 23 | Temperatura (C) e Umidade (%) |
| LDR | 36 (analogico) | Luminosidade |
| PIR | 22 | Presenca detectada |
| LED Verde | 18 | Indicador de nivel normal (>= 20%) |
| LED Vermelho | 19 | Indicador de nivel baixo (< 20%) |

---

## Estrutura dos Arquivos

| Arquivo | Descricao |
|---|---|
| `espTransmissor.ino` | Setup, loop principal, definicao de pinos e struct de dados |
| `sensores.ino` | Leitura do HC-SR04 e do DHT22 |
| `atuadores.ino` | Controle dos LEDs indicadores |
| `rede.ino` | Configuracao do ESP-NOW e envio dos dados ao receptor |
| `outros.ino` | Calibracao do tanque e monitor serial |

---

## Configuracao

### MAC Address do Receptor

No arquivo `espTransmissor.ino`, substitua o MAC address pelo do seu ESP32 receptor:

```cpp
const uint8_t macReceptor[] = {0xB4, 0xBF, 0xE9, 0x32, 0xDF, 0xE4};
```

Para descobrir o MAC do receptor, carregue um sketch simples que imprime `WiFi.macAddress()` via Serial Monitor.

### Altura do Tanque

O valor padrao e 100 cm. Para calibrar com a altura real do seu tanque, chame `definirCapacidade()` no `setup()` com o tanque vazio. A funcao realiza 10 leituras e usa a media como referencia de altura maxima.

```cpp
void setup() {
  // ...
  definirCapacidade(); // chame com o tanque vazio
}
```

---

## Calculo do Nivel

O nivel e calculado pelo sensor HC-SR04 instalado na tampa do tanque apontando para baixo:

```
nivelTanque (%) = ((alturaTanque - distancia) * 100) / alturaTanque
```

O valor e limitado entre 0% e 100% pela funcao `constrain`.

---

## Dependencias

Instale as seguintes bibliotecas pelo Gerenciador de Bibliotecas da Arduino IDE:

- **DHT sensor library** (Adafruit)
- **Adafruit Unified Sensor**

As bibliotecas `WiFi.h` e `esp_now.h` ja fazem parte do pacote ESP32 para Arduino.

---

## Projeto Relacionado

Os dados transmitidos por este dispositivo sao recebidos e enviados ao banco de dados pelo repositorio [ESP32 Receptor](#).
