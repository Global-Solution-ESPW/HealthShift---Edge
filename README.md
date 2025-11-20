
# 🩺 Health Shift – Monitoramento Inteligente de Condições Ambientais (Edge Computing + IoT)

**Health Shift** é um sistema de monitoramento em tempo real voltado para ambientes da área da saúde, projetado para acompanhar variáveis ambientais críticas que impactam diretamente o conforto, a produtividade e o bem-estar de profissionais e pacientes.

A solução combina **ESP32 (Edge Computing)**, **sensores ambientais**, **protocolo MQTT**, **broker remoto** e **dashboard Node-RED local**, garantindo autonomia no nó de borda, envio eficiente de dados e visualização clara para tomada de decisão.

---

## 🚀 Tecnologias Utilizadas

- **ESP32** (processamento local na borda)
- **Wokwi IoT Simulation**
- **Sensores:**
  - DHT22 – temperatura e umidade
  - LDR (via módulo) – luminosidade
  - HC-SR04 – fluxo/presença (distância)
- **Atuadores:**
  - LED – indicação de estados
  - Buzzer – alerta sonoro crítico
- **Protocolo MQTT**
- **Broker público** (fornecido pelo professor)
- **Node-RED local** + Dashboard
- **JSON Telemetry Pipeline**

---

## 🏥 Objetivo da Solução

Ambientes da saúde exigem controle rigoroso das condições ambientais. Calor excessivo, iluminação inadequada ou grande fluxo de pessoas podem resultar em:

- Fadiga mais rápida  
- Queda de produtividade  
- Diminuição da segurança  
- Risco ao paciente  

Por isso, o **Health Shift**:

- Calcula o **Índice de Risco de Fadiga (IRF)** baseado nos sensores  
- Classifica o estado do ambiente em:
  - **OK**
  - **ATENÇÃO**
  - **ALTO RISCO**
- Aciona **LED** e **buzzer** localmente quando necessário  
- Envia telemetria e alertas para o dashboard via MQTT  

---

# ⚙️ Arquitetura da Solução

```

```
      ┌──────────────────────────────┐
      │          ESP32 (Edge)        │
      │  - Lê sensores               │
      │  - Calcula IRF               │
      │  - Aciona LED/Buzzer         │
      │  - Publica MQTT              │
      └───────────────┬──────────────┘
                      MQTT
                       |
                       v
            ┌─────────────────────┐
            │  Broker MQTT (FIAP) │
            │   IP: 44.223.43.74  │
            └───────┬─────────────┘
                    |
                    v
        ┌────────────────────────────┐
        │    Node-RED Local (http)   │
        │ - Consome telemetria       │
        │ - Exibe Dashboard / UI     │
        │ - Log de alertas críticos  │
        └───────────┬────────────────┘
                    |
                    v
           ┌────────────────┐
           │    Dashboard   │
           │  IRF / Status  │
           │  Gráficos      │
           │  Últimos alertas│
           └────────────────┘
```

```

---

# 📡 Publicação MQTT

**Broker:** `44.223.43.74`  
**Porta:** `1883`  
**Tópicos exclusivos do projeto:**

```

leandro/healthshift/telemetria
leandro/healthshift/alertas

````

### 📤 Exemplo de payload enviado pelo ESP32:

```json
{
  "estacao": "HOSP_A_ENF_01",
  "temp": 23.10,
  "umid": 59.5,
  "lux": 24,
  "dist": 65,
  "irf": 15,
  "status": "OK",
  "critico": "NAO"
}
````

---

# 🧠 Lógica do IRF (Índice de Risco de Fadiga)

Cada variável recebe um peso baseado em faixas ideais:

* **Temperatura** – conforto térmico
* **Umidade** – sensação térmica e ressecamento
* **Luminosidade** – impacto direto na fadiga ocular
* **Fluxo de pessoas** – impacto na carga emocional e sensorial

O IRF varia de **0 a 100**, onde:

* **0–40 → OK**
* **41–60 → ATENÇÃO**
* **61+ → ALTO RISCO**

---

# 🔔 Regras de ALERTA CRÍTICO (acionam Buzzer + MQTT Alertas)

O buzzer é disparado quando:

* Temperatura **alta** + Umidade **alta**
* Luz **muito forte** + Temp **alta**
* Luz **muito baixa** + Umidade **alta**
* Distância **< 50 cm** + Temp **alta**

Quando isso ocorre:

* LED acende forte
* Buzzer toca
* Payload é enviado para:

  ```
  leandro/healthshift/alertas
  ```

---

# 🖥 Dashboard Node-RED

Componentes do dashboard:

* **Gauge do IRF**
* **Gauge de temperatura**
* **Gauge de umidade**
* **Gauge de luminosidade**
* **Gauge de distância**
* **Texto com status atual**
* **Gráfico histórico do IRF**
* **Card com último alerta crítico**

Acessível em:

```
http://localhost:1880/ui
```

---

# ▶️ Como Executar o Projeto (Passo a Passo)

## 1️⃣ Wokwi (ESP32)

1. Abra o projeto no Wokwi
2. Verifique se o WIFI está correto:

   ```
   WIFI_SSID="Wokwi-GUEST"
   WIFI_PASSWORD=""
   ```
3. Rode o código do ESP32
4. Verifique no console:

   ```
   Conectado ao WiFi!
   Conectado ao MQTT!
   ```
5. Verifique telemetria sendo enviada

---

## 2️⃣ Node-RED (Local)

1. Instalar Node.js
2. Instalar Node-RED:

   ```bash
   npm install -g --unsafe-perm node-red
   ```
3. Iniciar:

   ```bash
   node-red
   ```
4. Acessar:

   ```
   http://localhost:1880
   ```
5. Instalar Dashboard:

   * Em NODE-RED vá em Manage Palette → Install → `node-red-dashboard`
     
6. Importar o fluxo
7. Ver dados chegando em tempo real

---

# 📁 Link do Wokwi

*(https://wokwi.com/projects/448013098375915521)*

---

# 🎬 Vídeo de Demonstração

*(https://youtu.be/nKyDCS5lqcM)*


# 🧩 Autor

**Leandro Simoneli da Silva**
RM : 566539
Engenharia de Software – FIAP
Projeto da disciplina **Edge Computing**
2025
