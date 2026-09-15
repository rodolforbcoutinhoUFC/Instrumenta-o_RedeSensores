# 🌐 CPS-ThermalComfort: Rede de Sensores Ciber-Física para Conforto Térmico e Eficiência Exergética

Este repositório centraliza o desenvolvimento, a documentação e o histórico evolutivo do **Projeto Final de Instrumentação** do Curso Superior em Engenharia da Computação (Campus Quixadá). O projeto adota uma abordagem de longo prazo, integrando sistemas embarcados de baixo nível, Redes de Sensores Sem Fio (WSN), simulação termoenergética preditiva (Gêmeos Digitais) e inteligência artificial.

---

## ⏳ Linha do Tempo e Evolução do Projeto

O projeto é incremental e divide-se em dois grandes marcos históricos estruturantes:

### 🔹 Fase 1: Fundamentação e Infraestrutura de Campo (2025)
Focada no estabelecimento da WSN de baixo custo para coleta massiva de dados ambientais:
* **Hardware Usado:** Arquitetura inicial avaliada com microcontroladores STM32 (Blue Pill) mapeando temperatura/umidade (AHT10) e iluminância (LDR).
* **Comunicação:** Transmissão local via rádio nRF24L01 (2.4 GHz) para um nó concentrador.
* **Entregáveis:** Validação de campo baseada em 48 questionários subjetivos em salas de aula para mapeamento do Voto Médio Estimado (PMV) tradicional e modelos adaptativos (ASHRAE 55).

### 🔸 Fase 2: Gêmeos Digitais, Controle Preditivo e Otimização Exergética (2026 — Atual)
Evolução da arquitetura local para um Sistema Ciber-Físico (CPS) em malha fechada baseado na Segunda Lei da Termodinâmica (Mínima destruição exergética e Teorema de Gouy-Stodola):
* **Nós Sensores Bare-Metal:** Firmware STM32 restrito ao uso da interface CMSIS (proibido HAL/Arduino), focado em economia severa de energia via STOP Mode com acordar determinístico por RTC interno.
* **Gateway Multicore & RTOS:** Coordenador ESP32 rodando FreeRTOS com separação rígida de tarefas em cores independentes (Core 0 para rádio SPI de alta prioridade; Core 1 para pilha TCP/IP, MQTT e inferência Fuzzy).
* **Sincronização Indireta:** Mecanismo de carimbo de tempo (Unix Timestamp) trafegado via Auto-ACK do rádio a partir de servidores NTP globais.
* **Gêmeo Digital Co-Simulado:** Integração bidirecional em tempo real entre o banco temporal InfluxDB e o motor de simulação EnergyPlus v25.1.0/OpenStudio, fatiando o Laboratório de Computadores (Bloco 4) em 6 zonas térmicas virtuais acopladas por mistura de ar (*Air Mixing*).
* **Controle Inteligente Activo:** Algoritmo Preditivo (*Feed-forward*) que consome dados da nuvem para antecipar a inércia térmica (*thermal lag*) disparando comandos infravermelhos (KY-005 modulado em 38 kHz) em máquinas Split de 60.000 BTU/h.

---

## 📂 Organização do Repositório

O repositório está estruturado de forma a blindar o código histórico de falhas e garantir frentes isoladas de desenvolvimento para a turma atual:

* **`.github/`**: Templates estruturados para submissão de Issues e Pull Requests.
* **`docs/`**: Contém relatórios técnicos padronizados (IEEE/SBC) e os slides das apresentações da banca acadêmica.
* **`src/`**: Código-fonte de produção sob desenvolvimento estrito da turma atual:
  * `/hardware/`: Arquivos CAD e esquemáticos de placas de circuito impresso (PCB) customizadas.
  * `/firmware_stm32/`: Código C bare-metal CMSIS puro com Makefile associado.
  * `/firmware_esp32/`: Código voltado à gerência multicore do FreeRTOS.
  * `/analytics_twin/`: Scripts em Python para as 200 rodadas de simulação estocástica via Hipercubo Latino (LHS), Análise de Sensibilidade Global (GSA) e Regras Fuzzy de Zadeh.
* **`legacy/`**: Cópia integral e intocável dos artefatos funcionais recuperados do ano de 2025 para fins de consulta e engenharia reversa.
* **`REQUISITOS.md`**: Backlog técnico de tarefas e metas de integrabilidade do período letivo corrente.

---

## 🛠️ Matriz Tecnológica do Sistema

| Subgrupo Tecnológico | Componente Recomendado | Protocolo / Canal de Enlace | Padrão de Firmware / Governança |
| :--- | :--- | :--- | :--- |
| **Nó Sensor (MCU)** | STM32 Blue Pill (Cortex-M3) | SPI1 (Rádio) / I2C1 (Sensores) | Bare-Metal estrito via CMSIS (Sem HAL) |
| **Transdução** | AHT10 + LDR Resistivo | I2C1 (0x38) / ADC1 de 12 bits | Polling temporizado via SysTick núcleo |
| **Rádio Local** | Módulo nRF24L01 (2.4 GHz) | SPI de alta velocidade | Auto-ACK com payload de sincronização |
| **Gateway Central** | ESP32-WROOM-32 | SPI2 (Rádio) + Wi-Fi 802.11 | FreeRTOS com alocação Multicore e filas |
| **Persistência / Nuvem**| InfluxDB + Eclipse Mosquitto| MQTT sobre TCP/IP / API Flux | Banco Temporal como SSoT / Painel Grafana |
| **Atuação Ativa** | Diodo Emissor IR KY-005 | PWM Modulado em 38 kHz | Algoritmo Preditivo Feed-forward |

---

## ⚠️ Regras de Custódia de Dados e Governança de Código

1. **Proibição de Arquivos Locais:** Conforme a *Regra Metodológica de Custódia de Dados*, é estritamente vedada a transferência de matrizes ou tabelas de dados entre grupos por arquivos locais isolados (.csv, .xlsx). Toda comunicação de dados operacionais e simulados deve ser consumida e persistida obrigatoriamente através da API REST/Flux do **InfluxDB** central.
2. **Critério de Validação do Gêmeo Digital:** O modelo computacional predial calibrado pelo motor EnergyPlus deve respeitar um limite estatístico rígido, onde o Erro Quadrático Médio (RMSE) da temperatura simulada frente ao dado real de campo **não pode ultrapassar 0,381 °C**. Modelos fora dessa métrica são considerados inválidos.
3. **Fluxo de Trabalho Git:** A branch `main` é protegida. Nenhuma alteração pode ser injetada diretamente. O desenvolvimento é focado no envio de Pull Requests avaliados pelos líderes de cada uma das quatro frentes de pesquisa interdependentes (Conforto, Gêmeos Digitais, Atuação e Sensibilidade Fuzzy).
