# **Medidor de Qualidade do Sangue com Arduino**

## **Descrição**
Esse projeto propõe um **medidor de qualidade do sangue** baseado no **Arduino UNO**, utilizando sensores biomédicos para coletar informações como **nível de oxigenação (SpO2)**, 
**batimentos cardíacos** e **temperatura corporal**. O sistema poderá exibir os dados em um **display LCD** ou enviá-los via **Wi-Fi/Bluetooth** para um dispositivo móvel ou computador.

---

## **Índice**
1. [Introdução](#introdução)
2. [Requisitos](#requisitos)
3. [Configuração do Ambiente](#configuração-do-ambiente)
4. [Montagem do Circuito](#montagem-do-circuito)
5. [Programação](#programação)
6. [Teste e Validação](#teste-e-validação)
7. [Expansões e Melhorias](#expansões-e-melhorias)
8. [Referências](#referências)

---

## **Introdução**
O objetivo deste projeto é criar um **sistema portátil de monitoramento de qualidade do sangue**, útil para aplicações médicas ou esportivas. Ele pode medir:
- **Saturação de oxigênio (SpO2)**
- **Batimentos cardíacos (BPM)**
- **Temperatura corporal**

Esse monitor pode ser utilizado por **pacientes, atletas ou profissionais de saúde** para obter informações em tempo real sobre a circulação sanguínea.

---

## **Requisitos**

### **Hardware**
- **Placa:** Arduino UNO ou ESP32
- **Sensores:**
  - **MAX30102** (Sensor de oxigenação e batimentos cardíacos)
  - **MLX90614** (Sensor de temperatura sem contato)
- **Display LCD 16x2** ou **OLED 0.96"**
- **Módulo Bluetooth HC-05** (opcional, para comunicação sem fio)
- **Módulo Wi-Fi ESP8266** (opcional, para envio de dados à nuvem)
- **Resistores e cabos de ligação**
- **Bateria 9V (se for um sistema portátil)**

### **Software**
- **Linguagem:** C/C++ para Arduino
- **IDE:** Arduino IDE
- **Bibliotecas:**
  - **Wire.h** (para comunicação I2C)
  - **Adafruit_MLX90614.h** (para sensor de temperatura)
  - **Adafruit_MAX30102.h** (para sensor de oxigenação e BPM)
  - **LiquidCrystal_I2C.h** (para o display LCD)

---

## **Configuração do Ambiente**
### **Passo 1: Instalação do Software**
1. **Baixe a Arduino IDE** no link: [Arduino Software](https://www.arduino.cc/en/software).
2. **Instale as bibliotecas necessárias** na Arduino IDE:
   - Vá até **Gerenciar Bibliotecas** e procure por:
     - `Adafruit MAX30102`
     - `Adafruit MLX90614`
     - `LiquidCrystal I2C`

---

## **Montagem do Circuito**
### **Esquema de Ligações**
| Componente  | Pino no Arduino  | Observação  |
|-------------|-----------------|-------------|
| MAX30102    | SDA -> A4, SCL -> A5 | Comunicação I2C |
| MLX90614    | SDA -> A4, SCL -> A5 | Compartilha barramento I2C |
| LCD 16x2    | SDA -> A4, SCL -> A5 | Comunicação I2C |
| Bluetooth HC-05 | TX -> RX, RX -> TX | Comunicação serial |
| GND de todos os sensores | GND | Terra comum |
| 5V | Alimentação dos sensores | -

### **Diagrama**
A conexão segue a estrutura dos sensores I2C (MAX30102 e MLX90614) na mesma linha SDA/SCL e outros periféricos ligados corretamente.

---

## **Programação**
### **Passo 1: Configuração dos Sensores**
```cpp
#include <Wire.h>
#include <Adafruit_MLX90614.h>
#include <Adafruit_MAX30102.h>
#include <LiquidCrystal_I2C.h>

// Sensores
Adafruit_MLX90614 mlx = Adafruit_MLX90614();
Adafruit_MAX30102 pulseOx;

// Display LCD
LiquidCrystal_I2C lcd(0x27, 16, 2);

void setup() {
  Serial.begin(115200);

  // Inicializa os sensores
  if (!pulseOx.begin()) {
    Serial.println("Sensor MAX30102 não detectado!");
    while (1);
  }

  if (!mlx.begin()) {
    Serial.println("Sensor MLX90614 não detectado!");
    while (1);
  }

  // Inicializa o LCD
  lcd.begin();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print("Iniciando...");
}
```

---

### **Passo 2: Medindo Qualidade do Sangue**
```cpp
void loop() {
  float temperatura = mlx.readObjectTempC();
  int batimentos = pulseOx.getHeartRate();
  int spo2 = pulseOx.getSpO2();

  Serial.print("Temp: "); Serial.print(temperatura); Serial.println(" C");
  Serial.print("BPM: "); Serial.print(batimentos); Serial.println();
  Serial.print("SpO2: "); Serial.print(spo2); Serial.println("%");

  // Exibir no LCD
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("BPM: ");
  lcd.print(batimentos);
  lcd.setCursor(0, 1);
  lcd.print("SpO2: ");
  lcd.print(spo2);
  lcd.print("%");

  delay(1000);
}
```

---

## **Teste e Validação**
1. **Testando sensores**: Verifique se os sensores MAX30102 e MLX90614 estão capturando leituras corretas.
2. **Validando LCD**: Confirme se os valores aparecem corretamente na tela.
3. **Serial Monitor**: Abra o Monitor Serial da Arduino IDE e veja se os valores de temperatura, BPM e SpO2 estão sendo atualizados corretamente.

---

## **Expansões e Melhorias**
- **Conectar a um app móvel** via **Bluetooth**.
- **Enviar dados para um banco de dados** via **Wi-Fi e ESP8266**.
- **Adicionar um sensor de glicose** para medir diabetes.
- **Fazer um alerta sonoro** caso o nível de oxigenação esteja baixo.

---

## **Referências**
- [Arduino MAX30102](https://github.com/oxullo/Arduino-MAX30100)
- [Arduino MLX90614](https://github.com/adafruit/Adafruit-MLX90614-Library)
- [Tutorial LCD I2C](https://lastminuteengineers.com/arduino-lcd-tutorial/)
